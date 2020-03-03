---
layout: post
title: "Discovery & mobile first, aneb jak připravujeme novou responzivní Heureku"
permalink: /jak-pripravujeme-novou-responzivni-heureku/
date: 2020-03-03 10:26:00 +0200
author: Martin Humpál
tags: productdiscovery frontend měnímeheureku
categories: [blog, design]
imageUrl: /assets/mobile-first/1-mvp-mlp.png
---

Produktový tým Heureky je složen z odborníků ve svém oboru a redesign neděláme formou „pojďme to udělat hezké“, ale každá připravovaná část podléhá BI analýze, uživatelskému testování, návrhu nového UI a tvorbě prototypů, které pak zkoumáme na uživatelských testech. Zároveň u každé feature zkoumáme, jak je využívaná a konverzní. Následně ji pak ladíme nebo mažeme, abychom se mohli soustředit na hlavní business. 

## #1 Discovery

**Historicky máme dvě verze Heureky: Desktopovou a mobilní**

Uživatelům na mobilech chyběly informace z desktopu a celkový vzhled stránek a prožitek neodpovídal roku 2020. Dlouhodobě nám ani nedávalo smysl starat se o obě verze, proto jsme se rozhodli pro kompletní přepsání do responzivní podoby, a to pěkně po částech a agilně. Není to však jen o přepisu frontendu. My zároveň přepisujeme i celý monolitický kód původní Heureky do jednotlivých mikroservices.

Mobilní Heureka je pro nás ideální playground, kde můžeme začít s klíčovými částmi, které nám v responzivní verzi chybí. Jedná se o homepage, vyhledávání, kategorii a produktový detail. Zatím máme za sebou úspěšný přechod na responzivní verze v [uživatelském účtu](https://ucet.heureka.cz/) a na [detailu obchodu](https://obchody.heureka.cz/notino-cz/recenze/overene).

#dyckyDISCOVERYfirst

{% include image.html
      img="/assets/mobile-first/3-testing.png"
      title="Uživatelské testování" %}

## #3 MVP vs MLP
V produktovém týmu se často rozhodujeme, co pro nové části Heureky konkrétně znamená MVP (minimum viable product) a co MLP (minimum lovable product).

MVP verzi testujme, abychom si ověřili hlavně její výkon, ale cílem každé produktové oblasti je MLP. V každém projektu si definujeme zvlášť, co je testovatelná verze a co je verze, kterou můžeme pustit do světa mezi lidi. Občas nám pozitivní výsledky ukáže už MVP, s čímž se ale nespokojíme – naším cílem je doručit uživatelům MLP.

#dyckyMLP

{% include image.html
      img="/assets/mobile-first/1-mvp-mlp.png"
      title="MVP vs. MLP" %}


## #4 Styleguide
Vytvořili jsme knihovnu grafických elementů – Heureka styleguide, která nám pomáhá udržet jednotný vizuální styl napříč Heurekou. Do styleguide jsme umístili tlačítka, ikony či labely a postupně do ní vkládáme složitější komponenty jako jsou produktové karty, shopnabídky a jejich interakce.

Největší výhodou společné styleguide je, že pokud někdo z jiných týmů/vývojářů potřebuje do svého produktu přidat komponentu nebo element (např. tlačítko), stačí použít správnou CSS třídu s HTML kódem a je hotovo.

Naší styleguide najdete na adrese [https://heureka.cz/ui/](https://heureka.cz/ui/). 

#dyckySTYLEGUIDE

{% include image.html
      img="/assets/mobile-first/2-heureka-styleguide.png"
      title="Heureka Styleguide" 
      width="550px"
      %}

## #5 AB testy

První test na IP kanceláří, následně postupný náběh trafficu

První testy děláme na produkci pod IP adresou našich kanceláří, abychom se připravili na všechny možné krizové scénáře, kterým se chceme vyhnout.

Další kroky slouží k otestování výkonu a stability aplikace, kterou spouštíme na část našich uživatelů (většinou na 5-10 % trafficu m.heureky). Tento proces opakujeme a postupně navyšujeme zastoupení uživatelů.

Pro tyto testy používáme vlastní nástroj, který je součástí naší knihovny nazvané “Hanoi”, o které napíšeme nějaký z dalších článků.

Výkon je otestovaný, jedeme dál

Po otestování stability nové responzivní části (např. Detailu produktu) přichází na řadu uživatelské (kvantitativní) AB testy, k čemuž nejčastěji používáme nástroj Google Optimize. 

Získáváme tak postupně feedback, jak si vedou jednotlivé verze a ladíme to k úplné dokonalosti. Také se zaměřujeme na optimalizaci konverzí, více v článku Workshop o CRO s Ondrou Ilinčevem. Často je to dlouhý a pečlivý proces.

#dyckyTESTUJEME

## #7 Zpětná vazba na novou verzi
Během testování nové verze zároveň sbíráme zpětnou vazbu nejen z čísel konverzí, chyb, ale hlavně z dotazníků, heatmap a rozhovorů s uživateli.

#dyckyFEEDBACK

{% include image.html
      img="/assets/mobile-first/8-heatmap.png"
      title="Heatmapa uživatelských iterakcí na produktovém detailu na mobilu" 
      width="300px"
      %}

## #8 Nasazení nové responzivní části na 100 % mobilní Heureky
V současné době máme na m.heurece většinu částí nových a plně responzivních. Mobile first!

Poslední částí, která na m.heurece responzivní není, je nákupní proces v Heureka Košíku, na kterém už v rámci projektu Heureka Group #MěnímeHeureku pracují kolegové v Maďarsku.

#dyckyMOBILEfirst

## #9 Přípravy na Desktop
V současné chvíli se připravujeme na první testy nové responzivní verze na desktopové Heurece. Postupovat budeme stejným způsobem, jako jsme to dělali na m.heurece. Musíme také doladit logiku pro práci s jinými adresami a přesměrováním na části, které v nové Heurece zatím nemáme.

Současně s prvním testováním připravujeme jednotlivé featury, co nám zbývají pro kompletní desktopovou verzi např. porovnávání produktů. 

#dyckyDESKTOP

## #10 První AB testy na Desktopu
Během prvních testů na Desktopu budeme sledovat to samé, co jsme dělali na mobilní Heurece, tedy stabilitu, výkon a hlavně konverze. Všechno bude pod dozorem dotazníků a heatmap. Postupně budeme ladit výkon a přidávat featury, které nám chybí k dokončení MLP.

**Ochutnávka nové Heureky:**
{% include image.html
      img="/assets/mobile-first/7-homepage.png"
      title="Nová responzivní homepage" 
      width="280px"
      %}

{% include image.html
      img="/assets/mobile-first/6-search.png"
      title="Nové vyhledávání" 
      width="280px"
      %}

{% include image.html
      img="/assets/mobile-first/5-category.png"
      title="Nová responzivní kategorie" 
      width="280px"
      %}

{% include image.html
      img="/assets/mobile-first/4-product-detail.png"
      title="Nový responzivní produktový detail" 
      width="280px"
      %}


## Co nás čeká dál?

Musíme doladit výkon nových částí, jako je homepage Heureky, vyhledávání, kategorie a produktový detail. Pro další kroky máme roadmapu featur, které nás čekají a postupně je budeme odbavovat dle priorit a kapacit. Našim cílem je mít Heureku kompletně responzivní žačátkem roku 2021.

Dále budeme ladit rychlost načítání stránek tak, aby Heureka patřila mezi špičku v rychlosti načítání stránek. Můžete se těšit i na již zmiňované nové porovnání produktů.

--

**Na závěr pro vás máme malý tip, pokud chcete vidět do budoucnosti Heureky:**

Běžte na Heureku z počítače a v patičce se přepněte na mobilní verzi. Zobrazí se vám tak responzivní verze Heureky, tak jak by v budoucnu mohla vypadat na desktopu. Není ještě kompletní a navíc se obsah může trochu měnit na základě našeho AB testování.
