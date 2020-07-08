---
layout: post
title: "Mikrofrontendy pod lupou"
permalink: /mikrofrontendy/
date: 2020-05-18 10:21:00 +0200
author: Adam Havel
tags: frontend web architektura microservices
categories: [blog, kod]
imageUrl: /assets/mikrofrontendy/thumb.jpg
---

Mikroslužby způsobily ve světě softwaru velký rozruch. Jedna firma za druhou opouští monolitickou architekturu a dělí produkt na menší celky, i za cenu zvýšené komplexity. Mikrofrontendy s tímto trendem úzce souvisí, v praxi však ještě nejsou zcela běžné. My jsme nicméně před nějakou dobou dospěli do bodu, kdy dávalo smysl mikrofrontendy použít. V článku si proto ukážeme, k jakému řešení jsme dospěli.

I když rozdrobíme logickou a datovou část aplikace na mnoho kousků, je běžné, že si prezentační vrstva — a je jedno zdali jde o statický generátor, *SPA* aplikaci nebo staré (dobré?) PHP — zachová podobu monolitu. Jeho úkolem je poskládat související data dohromady a zobrazit lidem ucelený produkt. Filozofii mikroslužeb však můžeme aplikovat i na této úrovni. Stručně řečeno, podobně jako mikroslužby dělí back-endovou část aplikace na malé a nezávislé celky, potýkají se mikrofrontendy s tím samým na straně front-endu.

V prvé řadě mohou různé produktové sekce obsluhovat rozdílné koncové aplikace, dává-li to v daném případě smysl. To se hodí, pokud tyto sekce spravují rozdílné týmy, které si nechtějí šahat pod ruce a naopak si přejí mít pod kontrolou veškerý kód. Stejně tak mohou mít týmy různé preference ohledně technologií — jeden upřednostňuje Python, jiný JavaScript. Dobrým důvodem pro různé přístupy může být i samotný produkt. Pro různé sekce se totiž často hodí jiná technologie. V našem případě je úvodní mobilní stránka čistě statická (není důvod, aby nebyla), zatímco [mobilní kategorii](https://m.heureka.cz/mobilni-telefony/) pohání React, který lépe uspokojí interaktivní charakter této sekce.

V dělení nicméně můžeme pokračovat i na úrovni samotných sekcí a jednotlivých stránek. My jsme k tomuto bodu dospěli během (stále probíhající) transformace našeho monolitu na mikroslužby a tvorby nové responzivní verze, která postupně nahrazuje stávající na sobě nezávislé verze pro mobil a počítač. Tento proces trvá už nějakou dobu a ještě notnou chvíli trvat bude. Proto jsme museli vymyslet, jak přechod provést ne najednou, ale v postupných krocích.

Prvními vlaštovkami tohoto přístupu je hlavička a patička na každé stránce. Ať už člověk navštíví [starou kategorii](https://mobilni-telefony.heureka.cz/), kterou stále obsluhuje PHP monolit, nebo tu [novou](https://m.heureka.cz/mobilni-telefony/), responzivní (zatím dostupnou jen na mobilu), kterou pohání Node.js a React, zobrazí se mu ta stejná a tatáž hlavička a patička. Za první stojí statický generátor Hugo, druhá je napsaná v Pythonu. A přestože jde o mišmaš technologií, uživatel samozřejmě dostane a vidí jen výsledný HTML dokument.

## Volba technologie

Takový přístup zjevně vyžaduje víc, než jen promyšlenou organizaci kódu. Je třeba vyřešit, jakou technologii zvolit pro složení výsledného HTML dokumentu. Přístupů je několik, ale lze je rozdělit na dva hlavní: skládání na straně klienta nebo na straně serveru. V prvním případě nám může posloužit JavaScript nebo starý známý `iframe`. Nicméně, jak jsem [popisoval dříve](/princip-postupneho-vylepseni/), v Heurece ctíme požadavek, aby web z velké části fungoval i bez JavaScriptu, což tuto možnost okamžitě vylučuje. A ač je součástí HTML, trpí `iframe` příliš mnoha nedostatky, abychom si troufli jej použít.

Zvolili jsme proto variantu skládání na serveru, kterou na svém webu využívá třeba i Ikea. Prvně nás napadlo vyzkoušet speciální jazyk ESI (Edge Side Includes), který je implementován například v proxy serveru Varnish]. Brzy jsme však narazili na řadu problémů, které tento přístup bohužel vylučují. Hlavní z nich spočívá v omezení, kdy požadavky na jednotlivé služby (mikrofrontendy) neprobíhají zároveň, ale postupně. A jelikož jsme schůdné řešení potřebovali najít rychle a žádné jiné možnosti se nenabízely, rozhodli jsme se vydat sice zábavnou, ale o to zrádnější cestou implementace vlastního proxy.

Nazvali jsme ji Hanoi. Jméno neodkazuje na vietnamské město, ale na matematický hlavolam Hanojské věže. Zároveň dodržuje naší tradici více či méně nesmyslných zkratek interních nástrojů začínajících na „h”, v tomto případě ***H**eureka **A**synchro**no**us **I**nterface*. Server jsme postavili na Node.js. Ten se pro tyto účely nabízí, protože je JavaScript
od základu uzpůsobený pro asynchronní a paralelní operace.

### Hanoi v kostce

Základním kamenem Hanoi je konfigurace, jenž udává, z jakých služeb jsou postaveny jednotlivá URL. Nazýváme ji `pages`. Má podobu JSON objektu, jehož klíči jsou regulární výrazy, pomocí kterých se rozhodne, jaké nastavení použít pro aktuální adresu. Jednoduchým příkladem je scénář pro uživatelský účet:

```
'^ucet\\.heureka\\.(cz|sk)': {
        template: 'generic-header-footer',
        name: 'userAccount',
    services: [
        {
            name: 'header',
            priority: 2,
            timeout: 5000
        },
        {
            name: 'user-account',
            templateSection: 'service',
            priority: 3
        },
        {
            name: 'footer',
            priority: 1,
            timeout: 3000
        }
    ]
}
```

`template` označuje název souboru s šablonou, které se v tomto případě použije. Tato šablona — která je napsaná s pomocí technologie [Marko](/princip-postupneho-vylepseni/) — je velmi jednoduchá:

```
import Layout from './layout.marko';

<${Layout}>
    <@body>
        <service data=data.services['header'] />
        <service data=data.services['service'] />
        <service data=data.services['footer'] />
    </@body>
</>
```

Jednotlivé služby jsou zde poskládané pod sebou, jedna za druhou. V jiných případech ale nic nebrání tomu vytvořit za použití stylů složitější kompozice.

Výrazy `header`, `service` a `footer` v šabloně samozřejmě odkazují na stejné hodnoty v klíčích `name` — respektive `templateSection` — v konfiguraci uživatelského účtu v `pages`. Představují jednotlivé služby (mikrofrontendy), z kterých se tato sekce skládá a jejich názvy jsou buď staticky nebo dynamicky přiřazeny ke konkrétním interním IP adresám, díky čemuž Hanoi ví, kam posílat požadavky.

V ukázce z konfigurace najdeme ještě klíče `priority` a `timeout`. První říká, která služba je pro dané URL tou nejdůležitější (vyšší číslo vyhrává), což, jak si ukážeme, je důležité z několika důvodů. Klíč `timeout` udává čas, který je Hanoi ochotna čekat na odpověď od služby. V tomto případě na hlavičku čeká nejvýše pět vteřin a pokud včas neodpoví, Hanoi neváhá a pošle zpátky alespoň zbytek stránky. Když `timeout` chybí, znamená to, že jde o kritickou službu, bez které nedává smysl stránku vykreslovat.

### Ďábel je skryt v detailu

Na první pohled je princip Hanoi jednoduchý. Od klienta dostane požadavek na konkrétní URL, podívá se, z čeho se sekce skládá a požadavek přepošle na jednotlivé služby. Z jejich odpovědí pomocí šablony poskládá HTML dokument a ten pošle klientovi zpátky. Praxe je nicméně o trochu složitější.

Nejprve je třeba rozhodnout, jak budou mikrofrontendy komunikovat s Hanoi. My v první iteraci experimentovali s formátem JSON, kdy služby vracely HTML a potřebná metadata v rámci jednoho velkého objektu s různými klíči. Je to sice jednodušší pro zpracování, ale vzniká pevná vazba mezi Hanoi a službami, které už nejsou nezávislé a pro svůj chod Hanoi v podstatě potřebují. To se ukázalo jako problematické při jejich vývoji, proto jsme se vydali cestou zcela samostatných mikrofrontendů. Ty vracejí čisté HTML, dají se tady spustit, vyvíjet a zobrazit v běžném prohlížeči. HTML a HTTP hlavičky tak představují “API”, s kterým Hanoi musí umět pracovat.

Správný HTML dokument ostatně obsahuje nejen obsah v podobě elementu `body`, ale právě i metadata, které patří do elementu `head`. Typicky jde o název dokumentu (`title`), jeho popis (`meta` elementy), styly (`link rel="stylesheet"`), skripty (`script`) a všemožné vazby (`link rel="canonical"` ap.). Každá služba má vlastní metadata a u naprosté většiny z nich je nutné, aby se objevily i ve výsledném HTML u klienta. Hanoi proto z každé odpovědi vezme element `head` a vytvoří z něj objektovou DOM reprezentaci. (Ani ve snu nás nenapadlo vytahat z dokumentu potřebné informace [za pomoci regulárních výrazů](https://stackoverflow.com/questions/1732348/regex-match-open-tags-except-xhtml-self-contained-tags?page=1&tab=votes#answer-1732454).) Tyto objekty pak poskládá dohromady v jeden velký strom. Případné duplicity se odstraní a pokud dojde ke kolizím (třeba u `title` nebo `meta name="description"`), vyhraje důležitější služba (zde se konečně projeví `priority` z konfigurace `pages`). Finální strom se pak převede zpátky do čistého HTML. S `body` je práce o mnoho méně, jeho obsah se totiž nezpracovává, jen se vloží do místa určeného šablonou (například `<service data=data.services['header'] />`).

Podobným způsobem se pracuje s HTTP hlavičkami, které obsahují další metadata a především *cookies*. Každá služba může vracet důležitou informaci či data, konečná HTTP odpověď se tedy vytvoří slepením odpovědí od jednotlivých služeb. Pro duplicity a kolize platí stejný princip, jaký byl popsán dříve.

Je však třeba ošetřit i několik speciálních případů. Co dělat, když některá služba vrátí chybu (HTTP status začínající čtyřkou nebo pětkou)? Pokud jde o jinou než hlavní službu (s nejvyšší prioritou), Hanoi ji jednoduše nevykreslí. V opačném případě zobrazí uživateli místo hlavní služby obecnou šablonu s informací o chybě. Tyto chybové šablony také sídlí v Hanoi, jednotlivé mikrofrontendy je tedy nemusí řešit, stačí když pošlou vhodný HTTP status. Pokud ovšem jakákoliv služba vyvolá přesměrování (což poznáme podle HTTP statusu začínajícího trojkou a HTTP hlavičky `Location`), dojde k přesměrování okamžitě, Hanoi na nic nečeká, ani nic nevykresluje.

Poslední výjimkou je *AJAX* požadavek. Ten typicky poznáme podle HTTP hlavičky `X-Requested-With` s hodnotou `XMLHttpRequest`. Odpovědí na takový požadavek nebývá HTML, ale JSON. Hanoi se zeptá všech služeb a pokud ji přijde rozumná odpověď, pošle ji klientovi, nedochází tedy k žádnému skládání. Častější však je, že tento požadavek na straně klienta rovnou směřujeme na konkrétní službu (třeba hlavičku), což Hanoi naznačíme tím, že v požadavku pošleme `query` hodnotu `hanoi-service` s názvem služby (`header`).

Jelikož je každá služba samostatná jednotka, vrací v rámci HTML odpovědi i vlastní CSS soubor (například https://im9.cz/ms/useraccount/css/critical.33344bc2a4.css), který se vzhledem k výše popsanému skládání `head` elementu dostane i ke klientovi. V Heurece navíc používáme [knihovnu stylů](https://www.heureka.cz/ui/), která obsahuje základní pravidla a komponenty, jež jsou sdílené napříč mikrofrontendy. Tyto styly sice používají všechny služby, ale díky odstranění duplicit zůstane ve výsledné HTML hlavičce jen jeden element `link`.

Aby se tyto různé styly nemohly v rámci jedné stránky vzájemně ovlivnit, zavedli jsme *scoping* na úrovni služeb. Pokud CSS píšeme určitým způsobem — jak jsem popisoval v [předchozím článku](/css-v-heurece/) — nemělo by sice ke kolizím docházet, ale ne všechny služby, které přes Hanoi provozujeme (především naše monolitická aplikace) tyto principy dodržují, obezřetnost je tedy na místě. Proto má každá služba na svém `body` elementu HTML třídu typu `scope-header` a všechny deklarace v jejím CSS souboru používají *selektory* jako například `.scope-header .c-search { … }` (*scope* k selektorům nepřidáváme ručně, ale automaticky v rámci *build* procesu stylů). Pokud chce služba využívat základní knihovny stylů, musí na `body` přidat i třídu `scope-essentials-v1.51.0`. Jak je vidno, knihovna je oproti ostatním stylům verzovaná nejen v URL (například [https://www.heureka.cz/ui/1.51.0/css/essentials.css](https://www.heureka.cz/ui/1.51.0/css/essentials.css)), ale i v *selektorech*. To umožňuje, aby na jedné stránce koexistovaly mikrofrontendy používající různé verze této knihovny. Nejde o ideální stav, neboť pak dochází ke stažení obou verzí stylů, ale v případě aktualizace knihovny (kterou je potřeba propsat do všech mikrofrontendů) je taková situace v podstatě nevyhnutelná, byť jen krátce trvající.

## Rizika a příležitosti

Přidat mezi uživatele a produkt další kritickou vrstvu, která může selhat a kterou je třeba spravovat, má svou cenu i v podobě zvýšené komplexity a zátěže serverů. Proto se snažíme naplno využít možností, které Hanoi nabízí nad rámec svého původního účelu.

### Front-endové optimalizace

Jelikož je Hanoi zodpovědná za vykreslení finálního HTML dokumentu, představují šablony jednotné místo, kde lze vylepšovat načítání kritických souborů jako jsou třeba styly. Týmy spravující jednotlivé mikrofrontendy pak tento aspekt vývoje front-endových služeb nemusí řešit a mohou jej v klidu přenechat Hanoi. Z předchozího popisu například vychází, že většina stránek obsahuje značné množství stylů: v nejjednodušším případě jeden od hlavičky, druhý od patičky, další od hlavní služby a poslední v podobě knihovny společných stylů. To býval velký problém, protože styly patří mezi takzvané blokující zdroje, bez kterých se dokument nevykreslí. Protokol HTTP/2 však nevýhody tohoto přístupu řeší a umožňuje nám ponechat styly jako samostatné soubory, což je lepší i z pohledu *cache*.

I přesto používáme další vylepšení, které načtení stránky zrychlí. Hanoi si udržuje vlastní *cache* stylů, které služby používají, a pokud uživatel poprvé navštíví sekci na Heurece (má tedy prázdnou *cache*), nahradí v dokumentu odkazované styly (element `link`) variantou s obsahem stylů vypsaným v elementu `style`. Klient tak nemusí čekat na stažení souborů a stránka se vykreslí rychleji. Po načtení dokumentu se všechny styly pomocí JavaScriptu stáhnou v pozadí, aby došlo k jejich uložení v *cache* na straně prohlížeče. Hanoi navíc v odpovědi pošle *cookie*, jejímž obsahem je seznam *hashů* jednotlivých souborů. Při dalších načtení stránky tak ví, jakými styly uživatel disponuje a buď zopakuje předchozí postup nebo styly ponechá v původní podobě odkazů.

Třešničkou na dortu je použití direktiv typu `preload`, které prohlížeči umožňují stáhnout potřebné soubory ještě předtím, než na ně narazí sám, například při zpracování stylů. V našem případě tak klienta upozorníme na fonty a *SVG sprite* obsahující definice ikonek:

```
<link rel="preload" href="https://im9.cz/ui/font/source-sans-variable.woff2" as="font" type="font/woff2" crossorigin />
<link rel="preload" href="https://im9.cz/ui/img/icons.svg" as="fetch" type="svg+xml" crossorigin />
```

### AB testování

S pomocí Hanoi děláme i jednoduché AB testy na úrovni služby. Používáme k tomu už několikrát zmíněnou konfiguraci `pages`:

```
'^m\\.heureka\\.cz\\/?': {
    template: 'generic-header-footer',
    name: 'm-category-cz',
    services: [
        {
            name: 'header',
            priority: 2,
            timeout: 5000
        },
        {
            name: 'category',
            templateSection: 'service',
            priority: 3,
            abTest: {
                id: 'case-category',
                treatmentPopulationRange: [0, 19],
                control: {
                    name: 'm-heureka_cz'
                },
                treatment: {
                    name: 'case-category-cz'
                }
            }
        },
        {
            name: 'footer',
            priority: 1,
            timeout: 5000
        }
    ]
}
```

Přítomnost klíče `abTest` zařídí, že je uživateli při první návštěvě dané URL přiřazeno skrze *cookie* číslo, které ho výpočtem umístí v intervalu 0-99. Po srovnáním s intervalem v klíči `treatmentPopulationRange` Hanoi vykreslí buď variantu `control`, nebo `treatment`. Právě tímto způsobem monolitickou Heureku kus po kusu — nebo spíše test za testem — nahrazujeme novou responzivní verzí.

### Metadata

Mechanismus skládání hlaviček jednotlivých služeb se hodí nejen  k tomu, abychom do finálního dokumentu dostali všechny styly. Různé služby mají k dispozici různá data a pro potřeby analytiky je nutné, abychom z nich vytvořili jeden celek. Pro přenos těchto informací proto používáme právě `meta` elementy. Hlavička stránky pak může vypadat třeba následovně:

```
<meta name="sentry:environment" content="production">
<meta name="gam:header-banner-enabled" content="false">
<meta name="gam:area" type="string" content="elektronika/foto">
<meta name="gtm:page:path" type="string" content="/foto/">
<meta name="gtm:page:type" type="object" content="[&quot;category&quot;,&quot;crossroad&quot;]">
<meta name="gtm:page:category:id" type="object" content="[659,660]">
<meta name="gtm:page:category:name" type="object" content="[&quot;Elektronika&quot;,&quot;Foto&quot;]">
<meta name="gtm:page:category:type" type="string" content="category">
```

Jak je vidno, *namespace* v `name` určuje oblast zájmu: `gtm` obsahuje data pro *Google Tag Manager*, `gam` se týká *Google Ad Manageru*, zatímco `sentry` nepřekvapivě odkazuje na známý nástroj pro detekci a zápis chyb. Hanoi vkládá několik jednoduchých skriptů, jejichž úkolem je tyto data zpracovat a předat do správných míst. Některé informace ovšem slouží k přímé komunikaci mezi jednotlivými mikrofrontendy. Element `gam:header-banner-enabled` o hodnotě `false` například značí, že si nějaká služba nepřeje, aby hlavička vykreslila reklamu nad obsahem. Ta si zprávu pomocí JavaScriptu přečte a zařídí se podle ní.

### White-label

Další zajímavý způsob využití souvisí s takzvanými *white-label* produkty. Jedná se o formu spolupráce, kdy externí dodavatel vyrobí (a většinou i dále spravuje) produkt, který se tváří a působí jako by vznikl interně. V našem případě se jedná o web [Dárky](https://darky.heureka.cz/). Přestože neběží na našich serverech, můžeme jej díky Hanoi nabízet pod naší doménou, se stejnou hlavičkou a patičkou jako kdekoliv jinde. Stále však musíme počítat s tím, že se jedná o třetí stranu. Vzhledem k tomu, že stránka běží pod stejnou doménou jako zbytek Heureky, prohlížeč automaticky pošle (a Hanoi přepošle) všechny *cookie*, včetně v nich obsažených *session*. To je velké bezpečnostní riziko. V konfiguraci `pages` proto můžeme omezit, které konkrétní *cookie* k službě pustíme:

```
{
    name: 'darky',
    templateSection: 'service',
    allowedCookies: ['bg_sid', 'bg_PHPSESSID'],
    priority: 3
}
```

## Za hranice

Největší výzvu nicméně představuje rozšíření Hanoi za hranice země do celé naší [mezinárodní skupiny](/menimeheureku-chystame-mezinarodni-oneplatform/). Aby tento proces proběhl úspěšně a my se vyvarovali katastrofických scenářů, rozhodli jsme se před touto cestou Hanoi zocelit o typovou kontrolu v podobě TypeScriptu a očistit ji o kód, jež ji svazuje s naší infrastrukturou a technologiemi. Odměnou, jak doufáme, nám bude *open-source* projekt, který budeme moct bez rozpaků vystavit na našem [GitHubu](https://github.com/heureka).
