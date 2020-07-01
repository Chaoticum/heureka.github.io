---
layout: post
title:  "Implementace Firebase Crashlytics a Analytics pro iOS s rozlišením debug a produkčního prostředí"
permalink: /integrace-crashlytics-v-ios/
date:   2018-09-07 11:43:01 +0100
author: Filip Melík
tags: [ios,crashlytics,firebase,analytics,debug]
categories: [blog, kod]
---

Když píšeme [Heureku pro iOS](https://apps.apple.com/cz/app/heureka-app/id436106975), rozlišujeme prostředí, ve kterém aplikace běží na vývojové (aka development, někdo tomu říká debug) a produkční pomocí proměnné v aplikaci, která je nastavovaná na základě build konfigurace [Debug/Release]. Podle toho upravujeme chování aplikace když vyvíjíme. Například tak, že používáme vývojové API namísto produkčního, zobrazujeme si napříč appkou různé pomocné informace, nebo tlačítka, která nám různě při vývoji usnadňují práci. To není nic revolučního a domnívám se, že je to běžná praxe.

## Problém

Pro sběr analytických dat používáme službu [Firebase Analytics](https://firebase.google.com/docs/analytics/) a pro snadnější a transparentnější sdílení crash reportů napříč týmem službu [Firebase Crashlytics](https://firebase.google.com/docs/crashlytics/). Integrace těchto služeb do projektu je poměrně přímočará pokud se držíme dokumentace. Na co je ale dokumentace krátká, to je případ, kdy nechceme, aby při vývoji byla odesílána analytická data do "produkční instance" Firebase Analytics respektive Crashlytics. Integrace do projektu probíhá tak, že si ve [Firebase console](https://console.firebase.google.com/) vytvoříte projekt, a v daném projektu se pak přidá již konkrétní aplikace pro danou platformu, v našem případě iOS. Při vytváření projektu se nás průvodce ptá mimo jiné na bundle ID, které pak používá k identifikaci aplikace ve Firebase. Tady ale vzniká problém, protože náš Xcode projekt nemá separátní bundle ID pro vývojovou verzi a pro produkční, ale rozlišujeme to pomocí proměnné, která identifikuje běhové prostředí jak jsem nastínil na začátku článku. **To ale způsobuje, že se do Firebase analytics resp. Crashlytics dostávají i data z vývoje**, která pak narušují celkové statistiky.

Jedno z řešení je vytvořit v Xcode nový target s jiným bundle ID, ale to přináší další problémy - je potřeba vytvořit v Apple developer portálu nové App ID a k němu spravovat zvlášť nové certifikáty a provisioning profily, zvlášť spravovat nové certifikáty pro push notifikace, zvlášť držet v synchronizaci build settings a Info.plist atd, což je poměrně dost práce. Proto jsme se rozhodli nedělat nové bundle ID, ale najít jiné řešení.

Jednoduché řešení tohoto problému je pro vývojové prostředí analytiku a crashe vůbec neposílat ve stylu

```
if environment == .production {
	Analytics.log("button_click")
}
```

ale my chceme i analytické eventy testovat předtím, než je pustíme do produkce, takže se při akceptaci validuje, jestli se posílají eventy se správným názvem a parametry a ověřuje se to pak v konzoli ve Firebase Analytics, takže nesledovat analytiku a crashe pro dev prostředí není řešením.

## Řešení

#### Analytics
Líný programátor se nejdříve mrkne, jestli podobný problém už někdo neřešil, což mě přivedlo na docela [zajímavé řešení na tomto blogu](https://medium.com/rocket-fuel/using-multiple-firebase-environments-in-ios-12b204cfa6c0). Ve zkratce jde o to, že se  vytvoří úplně nový Firebase projekt který bude sloužit pro data z debug prostředí a v tomto projektu vytvoříme novou aplikaci s identickým bundle ID. Vlastní rozlišení, kam se mají posílat analytická data, pak provedeme tak, že v Xcode projektu v `Build phases` přidáme `Run script phase` pro Firebase analytics, která vybere jaký konfigurační soubor (GoogleService-Info.plist) nakopírovat do archivu s projektem v závislosti na zvolené build konfiguraci [Debug/Release]. V kódu, který inicializuje Firebase SDK pak není nic potřeba měnit a stačí např. v `AppDelegate` zavolat dle dokumentace `FirebaseApp.configure()` a díky triku výše se použije správný konfigurační soubor a data se tak budou posílat do správného projektu = profit!

#### Crashlytics
Pro Crashlytics jsme zvolili víceméně stejný postup. Opět šlo o to vytvořit novou `Run script phase` pro Crashlytics, jejímž úkolem je v závislosti na build konfiguraci při kompilaci použít správný Crashlytics API key a build secret.

[![build phase](/assets/integrace-crashlytics-v-ios/build-phase.png)](/assets/integrace-crashlytics-v-ios/build-phase.png)

Je potřeba však dodržet dvě věci:

1) Tuto build fázi mít až úplně jako poslední v pořadí, jinak se Crashlytics správně nenainicializuje (je o tom zmínka i v implementační dokumentaci)

2) Provést správně inicializaci při spouštění aplikace (např. v `AppDelegate`) a místo toho, co je v dokumentaci (`Fabric.with([Crashlytics.self])`), udělat inicializaci ručně v závislosti na prostředí a předat podle toho odpovídající API klíč:

```
if services.environment == .production {
    Crashlytics.start(withAPIKey: productionApiKey)
} else {
    Crashlytics.start(withAPIKey: developmentApiKey)
}
```

Crashlytics je původně produkt [Fabricu](https://get.fabric.io/), který koupil Google, a teď jsou momentálně v mezidobí, kdy se snaží jejich funkcionalitu implementovat do Firebase. To s sebou přináší pár nepříjemností při implementaci. Než budeme pokračovat, je potřeba nejdříve vytvořit si účet ve Fabricu, pod který pak spadne jak produkční, tak i debug appka.

Xcode projekt teď nejspíše ale nepůjde vybuildit, protože je potřeba vytvořit ve Fabricu "aplikace", se kterými náš Xcode projekt spárujeme. Rozhodně nedoporučuji to dělat přes jejich webovou appku, protože obsahuje mnoho chyb a druhou aplikaci pro debug prostředí mi nešlo ani za boha přes web přidat. Nakonec jsem úplně náhodou objevil, že mají [desktop aplikaci](https://get.fabric.io/), se kterou se aplikaci ve Fabricu povede vytvořit a pomůže i s integrací do Xcode projektu. Když jsem to zkoušel přes jejich web appku, po kliknutí na "Add app" mě to přesměrovalo vždy na homepage, kde se nedá nic udělat a aplikaci to stejně nevytvoří.

## Nástrahy
Co se ukázalo jako největší problém, bylo po implementaci v kódu iOS aplikace prolinkování Fabric aplikací (které jsme vytvořili o kus výše pomocí jejich desktop appky) s aplikacemi ve Firebase konzoli. Po přihlášení do Firebase konzole, zvolení projektu a rozkliknutí vlevo v menu záložky Crashlytics to nabízí spárování Firebase s Fabricem. To platí ale jen v případě, že máme ve Fabricu vytvořené aplikace dle návodu výše. Pak je důležité spárovat správný Firebase projekt se správnou Fabric appkou, tj. debug projekt s debug Fabric appkou a totéž s produkční, aby nám data nekončila ve špatných instancích.

Druhá věc je, že když už vše máme implementováno a dojde ke crashi, data z iOS appky, která je vybuilděná s debug konfigurací, se ve Firebase po spárování s Fabric appkou propisují docela rychle, v řádu minut. Ale pokud máme appku vybuilděnou s Release konfigurací, trvalo mi vždy cca den, než se crash objevil ve Firebase. Možná je to náhoda, ale kolega, co dělá Heureku pro Android a implementoval Crashlytics také, má podobnou zkušenost i na Androidu.


## Good luck
Doufám, že tenhle text ušetří někomu pár hodin života, které mně Crashlytics vzaly!
