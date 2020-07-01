---
layout: post
title: "Je dobré vědět, která mikroslužba pracuje s nejcitlivějšími daty"
permalink: /ktera-mikrosluzba-pracuje-s-nejcitlivejsimi-daty/
date: 2019-10-24 9:35:00 +0200
author: Pavel Škoda
tags: backend web security
categories: [blog, kod, bezpecnost]
imageUrl: /assets/security-group/security.jpg

---

V Heurece smýšlíme hodně produktově a naše týmy jsou postavené kolem jednotlivých [produktových oblastí](/jak-jsme-si-rozdelili-produktove-oblasti-do-tymu/). Abychom se ale nehonili jen za vylepšováním nákupního procesu a leštěním „favorite“ tlačítka, zavedli jsme po vzoru [Spotify modelu](/inspirovali-jsme-se-u-spotify/) koncept tzv. **technických skupin**, nebo chcete-li guild.

Guildy jsou odpovědné za technický rozvoj celého našeho stacku.‌ Každý vývojář má možnost být součástí jedné nebo více guild.

Máme tedy například:
* **Frontend guildu**, kde se řeší, jak by měla vypadat naše frontendová codebase (například [progressive enhancement](/princip-postupneho-vylepseni/).
* **Operations guildu**, která má pod palcem naši infrastrukturu (třeba [metriky](/metriky-metriky-metriky/)).
* **Data guildu** která pro nás zkoumá možnosti různých databází.
* **QA guildu** kde se hodně řeší testování a kvalita našich produkčních aplikací.
* **Security guildu** jejímž hlavním tématem je zabezpečení našich služeb.

Pojďme si nyní říct, čím se zabývala **Security guilda** první kvartál své existence.

![Je dobré vědět, která mikroslužba pracuje s nejcitlivějšími daty](/assets/security-group/security.jpg)

## Naivní zabezpečení mikroslužeb

Když začínáte s mikroslužbami, většinou je všechny zavřete do jedné společné [virtuální sítě](https://cs.wikipedia.org/wiki/VLAN) a&nbsp;autorizaci requestů necháte na vnějších službách.‌ To jsou ty, které vystavují nějaké API‌ do zbytku internetu. Ostatní služby uvnitř sítě se pak spoléhají na to, že ony „vnější“ služby odfiltrovaly cokoliv škodlivého a nezkoumají autenticitu ani autorizaci žádného požadavku.

Odsunout takhle veškerou odpovědnost na okrajové služby sice ušetří vývojářům nějaký ten kód, ale má i své nevýhody. Tou největší je, že pokud někdo překoná onu vnější vrstvu, dostane přístup k celé vnitřní infrastruktuře. A protože riziko, že se to podaří není nulové, začali jsme přemýšlet jak přidat další vrstvu zabezpečení.

## Více úrovní zabezpečení

Klasický přístup je navrhnout nějaký interní standard zabezpečení, který poté implementujete do všech služeb.‌ V praxi však máme některé služby, kde by implementace takové věci nepřinesla žádné benefity a naopak služby, které by si zasloužily zabezpečit mnohem více než zbytek stacku.

Například služba, která poskytuje pouze hodnocení (počty hvězdiček a recenze) produktů, což jsou veřejně dostupné údaje, asi nepotřebuje zabetonovat tak důkladně, jako služba, která spravuje účty uživatelů.

Rozhodli jsme se, že **služby klasifikujeme** podle toho, jak jsou pro nás **z hlediska zabezpečení citlivé.**

## Metoda klasifikace

Jak ale rozhodnout která služba je z hlediska zabezpečení citlivější a která méně?‌

Myšlenka je taková, že citlivé jsou pro nás především ty služby, které pracují s neveřejnými daty nebo osobními údaji.‌ Můžeme tedy klasifikovat data, se kterými služby pracují a citlivost dané služby bude rovna nejcitlivějším datům, se kterými pracuje.

Potřebovali jsme už tedy jen metodu klasifikace citlivosti dat a když jsme se rozhlédli po internetu, narazili jsme na [OWASP Risk Rating Methodology](https://owasp.org/www-community/OWASP_Risk_Rating_Methodology).

Vytáhli jsme si tedy entity a tabulky ze všech našich databází a začali je jednotlivě klasifikovat. Po vzoru již zmíněné [OWASP Risk Rating Methodology](https://owasp.org/www-community/OWASP_Risk_Rating_Methodology) jsme si určili sedm pro nás důležitých kritérií. Protože v jednom člověku by se všechna naše data nedala v rozumném čase ohodnotit, nastavili jsme si základní kotvy, o které se můžeme opřít:

### 1) confidentiality - citlivost dat s ohledem na GDPR
* 0 – veřejná data, id, ...
* 5 – interní, ale ne osobní
* 9 – uživatelská data, adresy, ...

### 2) integrity - poškození dat
* 0 – o žádná data nepřijdeme
* 5 – přijdeme o data, která už nedokážeme znovu získat
* 9 – úplná ztráta dat

### 3) availability - kolik služeb díky tomu bude mít výpadek
* 0 – žádná
* 5 – nějaké naše interní procesy
* 9 – úplný výpadek služeb

### 4) accountability - dokážeme útočníky vysledovat
* 0 – víme, co přesně se děje, kdykoliv s daty někdo nakládá
* 5 – víme o nakládání s daty, ale nevíme přesně se kterými
* 9 – nezjistíme ani v reálném čase, že se něco takového děje

### 5) financial - kolik nás to bude stát peněz
* 0 – žádné
* 5 – citelné poškození, např. pokuta kvůli porušení GDPR
* 9 – krach firmy

### 6) reputation - ztráta reputace
* 0 – nikomu to nebude vadit
* 5 – poškození dobrého jména firmy, které nás bude stát část zákazníků
* 9 – ztráta reputace vedoucí k hromadnému odchodu partnerů a zákazníků

### 7) privacy - kolik osobních informací lze identifikovat
* 3 – jednoho člověka
* 7 – tisíce lidí
* 9 – miliony lidí

## K čemu to celé vedlo?

Není velkým překvapením, že se nám jako nejcitlivější ukázaly entity jako „Uživatel“ a „Platba“, ale mezi nejcitlivější se dostaly i některé entity, o kterých by nás dříve ani nenapadlo přemýšlet jako o potencionálně citlivých.

U každé služby víme, kam a jaké má přístupy. Například pokud má nějaká služba přístup do naší „velké“ monolitické databáze, má přístup pouze k některým tabulkám a my jsme schopni pomocí **privileges** přesně určit ke kterým. Proto jsme mohli promítnout citlivost dat do jednotlivých služeb a určit tak citlivost samotných služeb.

Služby, které jen agregují data z jiných služeb, považujeme za tak citlivé, jako nejcitlivější ze služeb, na které jsou napojené.

Výsledkem našeho snažení je tedy **prioritizovaný seznam**, díky kterému víme kde začít s posilováním zabezpečení.

Nejhůře na tom jsou samozřejmě ty služby, které potřebují nejvíce zdrojů.
V Heurece máme jednu deprecated, ale stále užívanou službu, která má přístup téměř všude. Nejlepší způsob, jak zajistit, aby byla služba naprosto bezpečná, je **smazat ji**. Čeká nás teď vymyslet plán, jak tuto službu poslat do věčných lovišť. Musíme tedy vymyslet čím ji nahradit.
