---
layout: post
title: "Nové Dárky na Heurece: Jak jsme nedělali to, co uživatelé chtějí, a díky tomu zdvojnásobili tržby"
permalink: /nove-darky-na-heurece/
date: 2020-02-24 13:19:00 +0200
author: Romana Trusínová a Michaela Koucká
tags: productdiscovery frontend
categories: [blog, design]
imageUrl: /assets/nove-darky-na-heurece/darky-vyzkum.jpg
---

Na začátku roku 2019 jsme si řekli, že chceme pro nadcházející Vánoce zlepšit dárkového rádce na Heurece, který tady už roky byl. Jeho výsledky byly ale špatné – málo lidí klikalo na nabízené dárky a téměř nikdo nepokračoval v nákupním procesu. Navíc byl v zastaralém designu, nebyl přizpůsobený pro prohlížení na mobilních zařízeních a obsahoval velké množství filtrů, které šly kombinovat v nesmyslné výsledky (např. Dárek pro psa k Valentýnu).

Nový dárkovač měl být venku před vánoční sezónou 2019, a proto jsme už začátkem března začali s fází discovery. Výzkum s uživateli hrál důležitou roli v celém projektu. Chtěli jsme vytvořit dárkového rádce, kterého budou lidé opravdu spokojeně používat.

## Fáze 1. Discovery: Potřebuje vůbec někdo dárkového rádce?
 
Hned zjara jsme začali s první fází výzkumu, explorační. Cílem první fáze bylo skvěle pochopit, jak lidé dnes dárky vybírají a jaké jsou jejich potřeby při shánění inspirace. Udělali jsme nejdřív 12 hodinových rozhovorů s uživateli. Povídali jsme si hodně zeširoka a taky zkoušeli hledat dárky na spoustě dárkových rádců, včetně toho starého na Heurece. Zásadní hypotézy z kvality jsme si následně ověřovali v kvantitativním dotazníku na pěti stech lidech. Tam jsme si ověřili třeba to, že 92 % lidí hledá inspiraci na dárky online, ale téměř nikdo nemá žádného oblíbeného dárkového rádce. Prostor by tu tedy byl.

Rozhovory s lidmi nás v některých směrech šokovaly. Třeba v tom, že uživatelům se náš starý (a námi interně celkem nenáviděný) dárkový rádce na Heurece líbil. 😊 Při uživatelském testování většina říkala, jak je to pěkný web, jsou na něm zajímavé dárky, možná by jich tam jen chtěli víc. Cesta k nákupu vybraného dárku byla taky hladká, žádný problém. Z dat jsme ale věděli, že tady lidé reálně čas netráví a na nabízené produkty neklikají, natož aby je kupovali. Tak v čem byl problém?

{% include image.html
            img="/assets/nove-darky-na-heurece/darky-old.png"
            title="Staré Dárky na Heurece"
            width="600px" %}

### Začínáme chápat souvislosti

Problému jsme porozuměli ne díky odpovědím uživatelů na přímé otázky, ale díky pozorování a vnímání celého kontextu hledání inspirace na dárky. Začali jsme chápat ty důležité věci:

**Prvním zásadním poznatkem bylo, že lidi při hledání inspirace na dárky primárně nakupovat vůbec nechtějí.** Cesta k nákupu dárku byla sice na naší staré stránce hladká, ale dost zbytečná, protože to není to, co lidé v danou chvíli reálně udělají. Vybírání dárků nefunguje tak, že uvidím jeden produkt, a ten koupím. Funguje to tak, že uvidím jeden produkt, z toho mě napadne, že to je zajímavý směr a začnu hledat další podobné. A šmejdím. A konzultuji. A odcházím. A vracím se. A hledám další. A pak… možná… někdy… někde… koupím.

**Druhé zajímavé zjištění se týkalo filtrů.** Na různých dárkovačích můžete filtrovat v nejroztodivnějších kombinacích. Dárky vybíráte pro lidi podle pohlaví, věku, vztahu, příležitosti, koníčků, life stylu, vkusu, ceny, pomalu i barvy vlasů. Všechny ty filtry lidé používají. A nikdo vám neřekne, že pro něj nejsou užitečné. Když se ale díváte pozorně, zjistíte, že divoké filtry uživatelé nepoužívají proto, že je potřebují, ale prostě proto, že je vidí. A doufají, že díky nim najdou ten „poklad“. Ale ve skutečnosti jim filtry většinou jen neprakticky osekávají nabídku a zároveň vyvolávají extrémní **fear of missing out** – čili nutí lidi zkoušet další a další kombinace, protože co když ten „poklad“ je právě pod dalším filtrem. Zážitek z použití služby složité filtry rozhodně nezlepšují. 

## Čas na klíčová rozhodnutí

Vhledů z pozorování uživatelů bylo hodně, ale dva výše popsané (nechuť rychle nakupovat a negativní efekt široké škály filtrů), nám pomohly udělat tři velká rozhodnutí.

1. Protože inspiraci lidé potřebují širokou, nebudeme na službě nabízet jen konkrétní dárky, ale typy dárků, k nim tři alternativy a pak je ještě vždy pošleme do patřičné kategorie na Heurece, kde si budou moct dál šmejdit a vybírat z desítek až stovek podobných produktů.

1. Filtry budou co nejjednodušší – pohlaví, věk ve třech kategoriích a cena. Není třeba víc. Nakonec přibyl ještě filtr pro příležitost, protože to vyplynulo z analýzy klíčových slov.

1. A protože víme, že nakupování není to primární, co lidé při hledání inspirace na dárky dělají, zahodili jsme tohle KPI. Přimět lidi na dárkovači nakoupit je prostě příliš ambiciózní cíl. Řekli jsme si, že dárkový rádce bude inspirovat a pomáhat uživatelům dostat dobrý nápad, takže nejde o konverze, ale o engagement. A taky o posilování vnímání Heureky jako nákupního rádce (jaké bylo překvápko, když nakonec i ty tržby vzrostly o 87 %).

## Fáze 2. Design a testování konceptů
Následovala fáze designování nového dárkového rádce. Nejprve jsme se pustili do prototypů – ty pro nás připravovala agentura Blueghost, s kterou dlouhodobě spolupracujeme na webových projektech. Prototypy jsme připravili tak, aby zohlednily všechny poznatky, které jsme získali uživatelským testováním.

**Prototyp desktop:**

{% include image.html
            img="/assets/nove-darky-na-heurece/darky-prototyp.png"
            title="Prototyp Dárků na Heurece"
            width="550px" %}

**Prototyp mobil:**

{% include image.html
            img="/assets/nove-darky-na-heurece/darky-prototyp-m.png"
            title="Prototyp Dárků na Heurece mobil"
            width="300px" %}

S designem opět pomohli uživatelé. Na základě testování prototypů jsme odladili věci jako stále ještě velkou nabídku filtrů na úvodní stránce a chybějící samostatný filtr na děti, protože ho tam lidé očekávali a naopak nečekali panenky a plyšáky pod filtrem „žena“. Nový rádce se nám už rýsoval. Náš dvorní designer Filip Čapský připravil finální vzhled a grafiku a mohlo se jít do výroby.

## Fáze 3. Vývoj, testování použitelnosti a iterace

Vývoj nového dárkového rádce jsme mohli řešit buď interně, kde bychom museli upozadit jiné plány, nebo druhou možností – externím vývojem – jehož kapacity jsou prakticky neomezené. Rozhodování nám ulehčila nová technologie HANOI, kterou vyvinuli naši šikovní vývojáři minulý rok. Tato technologie umožňuje “zasazení” jakéhokoliv obsahu do stránek a vzhledu Heureky, což jsme v tomto případě přesně potřebovali. Nechtěli jsme, aby dárkovač byl jen další z desítek microsites, které máme. Chtěli jsme, aby byl opravdovou součástí Heureky, což se díky naší technologii podařilo. Hlavičku a patičku jsme proto použili z Heureky a externí agentura dopracovala obsah dárkovače napojený na redakční systém, abychom tipy na dárky mohli pravidelně obměňovat a přidávat.

V další fázi přišla obrovská práce pro content tým – najít ty nejzajímavější dárky a najít jich dost! Součástí zadání byly vstupy z výzkumu, hlavně z prvního kvantitativního dotazníku, kde jsme se mimo jiné ptali, v jakých cenových relacích lidé dárky kupují nebo pro koho je nejtěžší dárek vybrat. Věděli jsme tak, na jaké typy dárků se zaměřit.

Před spuštěním nás čekalo ještě jedno kolo uživatelských testů nad téměř hotovým produktem. Tentokrát už šlo téměř čistě o testování použitelnosti a obsahu. Stačilo 5 respondentů, kteří nám pomohli vychytat chyby v ovládání, nepřesné kotvy nebo vysledovat, co lidem nejvíc ježí chlupy z hlediska obsahu a nabízených dárků. Největším oříškem bylo vyřešit dárky pro teenagery tak, aby byly dohledatelné, ale přitom nenarušovaly tipy na dárky pro menší děti, kde vychytávky pro skoro dospělého působily jako chyba.

## Fáze 4. Spuštění a marketing

Byl začátek listopadu a nový dárkovač šel ven. Včetně mohutné kampaně, která zase vycházela ze zjištění ještě z první fáze výzkumu: že totiž (skoro) všichni potřebují inspiraci na dárky, ale (skoro) nikdo nehledá dárkové rádce. Můžete mít nejlepšího dárkového rádce na světě, ale většinu lidí nenapadne ho hledat a mine ho. Stejně jako v offline světě se inspirace na dárky hledá většinou bloumáním po nákupáku, v online světě lidé pro inspiraci vyrazí do oblíbeného eshopu. Proto bylo jasné, že lidem rádce musíme přinést až pod nos, aniž by se na něj ptali.

Kampaň proběhla na displayových plochách na všech velkých českých webech, na Facebooku a Instagramu a poslali jsme info o novém rádci i na naši velmi širokou e-mailovou databázi. Zároveň jsme se domluvili s jednotkami influencerů, aby rádce na svých kanálech ukázali a zpropagovali.

{% include image.html
            img="/assets/nove-darky-na-heurece/darky-marketing.gif"
            title="Marketing Dárků na Heurece"
            width="600px" %}

## Výsledky

S výsledky celého projektu jsme nad míru spokojeni. Už po prvních pár měsících provozu vidíme, že pomáháme uživatelům vybrat dárky lépe než v předchozích letech. Dokazují to subjektivní odpovědi uživatelů v dotazníčcích na rychlou zpětnou vazbu, které jsme si spouštěli na starém i novém dárkovači, a taky naše naměřené výsledky.

### Nový dárkovač má o 15 % méně zobrazení stránek

Sakra pokles? Ano, ale pozitivní. Celou strukturu dárkovače jsme znatelně zjednodušili, snížili jsme množství filtrů, které uživatele dost rozptylovaly. Tím jsme docílili lepšího výkonu na stránce. Průměrná doba strávená na stránkách dárkovače vzrostla o 12 %. A i při nižším množství zobrazení stránek máme více konverzí.

**Proklikovost z dárkovače do detailu produktu vzrostla o 153 %**

Největší slabinou starého dárkovače byla transformace z fáze SEE do fáze THINK (viz [STDC framework](https://www.kaushik.net/avinash/see-think-do-care-win-content-marketing-measurement/)). Málo uživatelů proklikávalo tipy na dárky, aby zjistili více informací, což byl pro nás hlavní ukazatel zaujetí. Ten se nám podařil novým designem i vylepšeným obsahem zvýšit dva a půlkrát! A téměř dvojnásobně vzrostly i následné prokliky z Heureky do eshopů (o 95 %) i celkové tržby (o výše zmíněných 87 %). 

Sečteno a podtrženo. Novému dárkovači se daří zaujmout pozornost uživatelů, vzbuzuje větší zvědavost k prozkoumání námi doporučených tipů na dárky a pomohli jsme více lidem najít inspiraci. A to nás moc těší.

Zároveň si ale nemyslíme, že je produkt dokonalý a úplně dokončený. Už teď je v plánu nové uživatelské testování s cílem dalšího vylepšování.

{% include image.html
            img="/assets/nove-darky-na-heurece/darky-vyzkum.jpg"
            title="Výzkum Dárků na Heurece" %}

## 6 klíčových faktorů úspěšného projektu

1. S pozorováním a dotazováním uživatelů se začalo opravdu na začátku, než se udělalo jediné rozhodnutí nebo čárka na papíře. V této fázi je totiž uživatelský výzkum nejefektivnější a ušetří nejvíc času i peněz v budoucnu.

1. Projekt měl celou dobu na starosti jeden produkťák, který byl osobně přítomný i u téměř všech uživatelských rozhovorů a testů. Informace se nikde „cestou neztrácely“ a postupovali jsme velmi konzistentně.

1. Využili jsme externí vývoj a netříštili jsme focus našim vývojářům, kteří se mohli věnovat vylepšování zásadních částí Heureky.

1. Rozvrhli jsme harmonogram dárkovače tak, abychom ho stihli dokončit před vánoční sezónou, kdy je využíván nejvíce.

1. Vytvořili jsme cross-functional tým z různých oddělení, v kterém měl každý na starost svoji část – od UX a výzkumu, externího vývoje, content oddělení, až po SEO, analytiku a marketingové oddělení. A to celé zastřešené produktovým oddělením.

1. V neposlední řadě vděčíme za úspěch [OKR metodice](https://rework.withgoogle.com/guides/set-goals-with-okrs/steps/introduction/), kterou jsme loni zavedli a posunuli na celofiremní úroveň. Jedním z cílů bylo posilovat Heureku jako nákupního rádce. Do tohoto cíle nám vytvoření nového dárkovače krásně zapadlo, díky čemuž se projektu mohl intenzivně věnovat každý člen týmu.

Na nové dárky se můžete podívat na [darky.heureka.cz](https://darky.heureka.cz/).

{% include image.html
            img="/assets/nove-darky-na-heurece/darky-new.png"
            title="Nové dárky na Heurece" %}