---
layout: post
title: "Jak spam robot prostřednictvím Heureky zaplnil e-mailovými notifikacemi schránky našich uživatelů – Postmortem tak trochu jinak"
permalink: /spam-robot-zaplnil-schranky-zivatelu-postmortem-tak-trochu-jinak/
date: 2020-04-20 14:00:10 +0200
author: Jiří Vích
tags: [security, postmortem]
categories: [blog, bezpecnost, kod]
imageUrl: /assets/spammer/fronta2.png

---

V únoru začal na Heurece neznámý spammer hromadně přidávat recenze na produkty z formuláře na produktovém detailu. Jednalo se o text s odkazem na falešnou stránku, kde bylo plno reklam. Největší problém způsobily e-mailové notifikace, které uživatelům ohlašují novou recenzi na jimi sledované produkty. Bylo jich tolik, že zahltily e-mailové schránky našich uživatelů, e-mailové fronty na serverech a dokonce to došlo tak daleko, že nás poskytovatelé e-mailových služeb začali rate-limitovat, což způsobilo nemalé komplikace.

Jako nejrychlejší možnost ochrany jsme spammerovi zablokovali rozsahy IP adres a mazali jsme jím vytvořené uživatelské účty včetně obsahu. Spammer byl však vytrvalý, stále dokola hledal cesty, jak naše rychlé opravy obejít a v určitých intervalech se vracel. Museli jsme dokonce dočasně vypnout přidávání produktových recenzí z webu.

Mezitím spammer našel další místo na Heurece, kterým jsou naše Poradny. Diskuse u produktů, kam si zákazníci chodí pro radu a obchody, značky a uživatelé jim radí. I zde byly na nové příspěvky navěšené e-mailové notifikace, kterých tak začalo chodit ještě více.

E-mailové fronty se začaly nekontrolovatelně plnit a jejich zaplnění a postupný rate-limit ze stran e-mailových poskytovatelů způsobili, že se začaly na Heurece zpožďovat jiné důležité e-maily, jako například Souhrn objednávky nákupu v našem Heureka košíku, či jiné zprávy třeba o aktivacích.

Urychleně jsme začali pracovat na opravě kontrolního mechanismu, abychom mohli vypnuté přidávání recenzí opětovně spustit. Vyčistili jsme e-mailové fronty, začali nasazovat ReCaptchu a společně s PR a Support oddělením začali připravovat komunikaci směrem k vyspamovaným zákazníkům.

Kde byl největší problém a jak jsme se z toho poučili? Jak šel sled událostí minutu po minutě a může se podobný útok opakovat? Čtěte dál!

## Jak šel čas?
* 6.2.2020 09:00 - Spammer přišel na Heureku poprvé a obhlíží si terén.
* 18.2.2020 18:00 (úterý) - Spammer začíná vkládat recenze na Pneumatiky. Vždy zkouší 3x jeden produkt, přidá kolem 30 recenzí.
* 19.2. 2020 (středa) - Spam běží skoro celý den, nejvíce večer. Naše oddělení, které se stará o content, o tom ví, ale netuší velikost problému. Jednotky spam recenzí jsou totiž zvyklí mazat na denní bázi.
* 20.2.2020 (čtvrtek) - Product Owner oblasti přidávání recenzí při pravidelné kontrole dat naráží na neobvyklý nárůst recenzí přidaných z webu. Začínáme zjišťovat rozsah škod. 
* čtvrtek dopoledne - Kontrolujeme počty a **zjišťujeme příčinu**. Objevujeme, že jde o masivní spam.
* čtvrtek 12:56 - Narychlo mažeme uživatele a blokujeme IP adresu, ze které spamuje.
* čtvrtek 13:30 - Mažeme vytvořené uživatele z celého rozsahu (přes 700 účtů) včetně recenzí a postupně blokujeme rozsahy. Dáváme vědět oddělení Contentu, že o tom víme. Tato oprava dočasně zabrala, od 15:00 je klid.
* pátek 21:15 - Spammer je opět v akci s jiným rozsahem a spamuje až do 03:00.
* sobota 08:30 - Zjišťujeme, že spammer byl zase aktivní, blokujeme další rozsahy.
* sobota 18:00 - Spammer změnil rozsahy a je zase aktivní do 03:00.
* neděle dopoledne - Spammer je opět aktivní.
* neděle 16:03 - Oddělení Supportu kontaktuje #vyvoj kanál ve Slacku s problémem: **Nechodí e-maily z Heureka košíku**, které mají zákazníkům potvrzovat objednávku. Support začal uvědomovat tazatele, že máme problémy a mají vyčkávat.
* neděle 18:14 - Tým, který má na starost objednávky v Košíku, vše kontroluje a nenašel chybu v odesílání těchto zpráv.
* neděle 18:27 - Support vydává pro tazatele stanovisko, že e-maily přijdou, ale bohužel se zpožděním.
* pondělí 3:23 - Tým naší Infrastruktury se ozývá, že jsme se **dostali na maximální kvótu odeslaných e-mailů na Gmail** a Google nám blokuje další odesílané e-maily. Odbavování fronty se tak v některých případech zcela zastavilo.
* pondělí 10:41 - E-mailová fronta je prázdná, vše důležité se se zpožděním odeslalo.
* pondělí 10:55 - Gmail nás již neblokuje. Stále však trvá blokace od Seznam e-mailu, kterou jsme též dostali. Musíme čekat.
* pondělí 12:52 - Tým infrastruktury připravuje přehledné boardy v Kibaně, abychom lépe viděli problémové e-maily od Spamera a mohli rychleji zasahovat.
* pondělí odpoledne - Zasedá krizový štáb. Product Owner Recenzí rozhoduje, že **dočasně zakážeme přidávat recenze z webu** a pustíme se do opravy kontrolních mechanismů.
* pondělí 14:30 - Dočasně vypínáme přidávání produktových recenzí z webu. Je klid.
* pondělí 14:45 - Začínáme pracovat na nových pravidlech přidávání recenzí z webu.
* pondělí až úterý - Spammer začal spamovat sekci Poradna, chodí tak další notifikace uživatelům.
* úterý 6:50 - Product Ownerovi uživatelských recenzí přichází zpráva z Content oddělení, že spammer začal spamovat Poradnu.
* úterý 7:30 - Blokujeme další IP adresu.
* úterý 8:52 - Zakládáme kanál na slacku #spam-recenze-poradna, přidáváme do něho zúčastněné lidi z Heureky – tým, který řeší Heureka košík, tým, který řeší e-mailovou frontu, Poradnu, konkrétní lidi ze Support oddělení, Contentu, a PR oddělení
* úterý 9:30 - Dočasně vypínáme možnost přidávání příspěvků do Poradny.
* úterý 10:00 - Jako nejrychlejší řešení se nám jeví zapnout reCAPTCHU i pro přihlášené uživatele (pro nepřihlášené uživatele tam reCAPTCHa už byla) a přidat CSRF ochranu.
* úterý 14:23 - Na Facebooku se začíná o problému mluvit. Připravujeme komunikaci.
* úterý 15:30 - Připravili jsme Merge Request s úpravou. Pro lepší kontrolu deploy plánujeme až na další den ráno.
* středa 09:15 - **Dáváme ven úpravu (reCAPTCHA + CSRF)** a přidáváme logování metrik.
Spamer se neozývá.
* středa - Tým infrastruktury celý den maže e-maily z front na SMTP serveru. Smazali jsme všechny, kde k uživatelům směřovalo více než 1000 e-mailů. Řešíme odkud se fronty stále plní, protože naše hlavní služba na odesílání e-mailů je již dávno prázdná.
* středa 17:15 - Tým uživ. recenzí nachází MailQueue – **starou knihovnu, která v monolitu řeší odesílání e-mailů** a maže 3 miliony zpráv. Je klid.
* 2.3.2020 (pondělí) - Ráno Content posílá info, že nelze přidávat odpovědi na otázky v Poradně (problém s reCAPTCHA). Objevujeme chybu v JS v nasazení reCAPTCHy a opravujeme ji. Vše funguje, spammer zažehnán.

{% include image.html
      img="/assets/spammer/fronta1.png"
      title="Takto vypadala vlna e-mailů v naší centrální službě Mailservice" %}

{% include image.html
      img="/assets/spammer/fronta2.png" %}

## Business dopadů je hned několik

Díky ucpaným frontám jsme uživatelům neodesílali důležité e-maily včas. Hlavním problémem byly hlavně e-maily o Shrnutí objednávek z našeho košíku, dotazníky z programu Ověřeno zákazník nebo i informace o docházejícím kreditu e-shopů.

U několika e-mailových služeb jsme dosáhli na rate-limit, což zvětšilo prodlevu v odesílání celé fronty. Také jsme si teoreticky u poskytovatelů e-mailových schránek mohli poškodit reputaci – to je ale pouze spekulace.

Dalším dopadem bylo, že jsme zahltili schránky našich uživatelů, zákazníků a e-shopů. V některých případech se jednalo až o tisíce e-mailů.

Uživatelé Heureky v době vypnutí vkládání nových příspěvků nemohli psát recenze na produkty z webu a příspěvky do Poraden. Také nám po nasazení reCaptchy vznikla drobná chyba, kterou jsme přehlédli a nějakou dobu nešly přidávat odpovědi v Poradně. Přišli jsme tak o několik příspěvků.

A v neposlední řadě nás práce ohledně spammera zdržela od naplánované práce v našich sprintech.

{% include image.html
      img="/assets/spammer/facebook.png"
      title="Takto vypadaly notifikační e-maily a přeplněné schránky"
      width="300px" %}

## Nestalo by se to, kdyby… Co bylo příčinou?

Jakmile bylo po všem, všichni zúčastnění jsme si společně sedli a celý problém podrobně rozebrali.

Za předchozí roky v monolitu Heureky vzniklo spoustu přešlapů, které si neseme dodnes. V současné době celou starou Heureku přepisujeme [do responzivního designu](/jak-pripravujeme-novou-responzivni-heureku) a spolu s tím se věnujeme i přepisu funkčních celků [do mikroslužeb](/menimeheureku-chystame-mezinarodni-oneplatform/), kde všechny tyto přešlapy opravujeme nebo řešíme lépe. Není bohužel v našich silách všechno vyřešit najednou. Jde o postupný a dlouhotrvající proces a není tak divu, že občas tvrdě narazíme, jako nyní.

**Hlavním problémem** byly nezabezpečené formuláře ve starém monolitu, neměli jsme CSRF ochranu a captchu jsme měli jen pro nepřihlášené uživatele. Spammer si mohl vytvořit stovky účtu z jedné IP adresy, i zde chybí sofistikovaná ochrana. Rovněž byl problém, že jsme na přidávané příspěvky neměli žádné metriky nebo alerty, které by nás včas upozornily na nekalou aktivitu. V rámci oprav a úprav jsme se také hůře orientovali ve starém monolitickém kódu. 

## Stanovili jsme si konkrétní kroky, co musíme upravit

Všechna místa, kde odesíláme e-maily v monolitu starou metodou, napojíme na naši jednotnou e-mailovou službu Mailservice, abychom měli plný přehled o tom, co odesíláme.

Vytvoříme samostatné e-mailové fronty pro kritické služby, jako jsou notifikace o nákupu v košíku, docházejícím kreditu apod. tak, aby nebyly ovlivněny zaplněnou frontou notifikacemi o novém příspěvku. Vyřešíme priority a důležitosti jednotlivých e-mailů.

Vytvoříme kvalitní metriky a alertingy na rychlost přidávání příspěvků, abychom spammera odhalili rychle a automaticky. Přidáme i lepší metriky na úroveň SMTP serveru a uděláme alerty na úrovni naší jednotné služby na odesílání e-mailů.

Odhalíme v celém kódu Heureky další kritická místa, zejména tam, kde probíhají vstupy od uživatelů, aby se podobná akce nemohla opakovat.

Uděláme další úrovně zabezpečení všech formulářů.

## A co na závěr?

Chybami se učíme a díky nim jsme lepší. Když se na to díváme zpětně, vypadá to jako školácká chyba. Spoustu věcí bychom dnes udělali jinak. Ale na to bychom bez tohoto incidentu nejspíš nepřišli.

Vyzkoušeli jsme si krizovou komunikaci napříč týmy a odděleními a do budoucna zapracujeme na reakční době. Dost možná nám v tom pomohou nové metriky.

**Neopomíjejte podobné detaily.** Definujte si kroky ke zlepšení předem, inspirujte se námi. Ne vždy se v rámci přepisu kódu do mikroslužeb vyplatí zanevřít na údržbu starého kódu pod vidinou, že bzdy bude nový.

Jak byste to řešili vy? Stalo se vám něco podobného? Dejte nám o tom vědět na našem Twitteru.
