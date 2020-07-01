---
layout: post
title: "Metriky, metriky, metriky"
permalink: /metriky-metriky-metriky/
date: 2019-05-29 11:00:00 +0200
author: David Jetelina
tags: [metriky, devops, prometheus, grafana]
categories: [blog]
imageUrl: /assets/metriky-metriky-metriky/parovani_1d.png
---

Metriky jsou jednou z nejdůležitějších částí práce vývojových týmu. Pokud si to nemyslíte, čtěte dál a dozvíte se proč.
Pokud si to myslíte, tak snad tento článek dočtete už jen proto, že víte, že to s nimi není tak jednoduché a je nějaká
šance, že se třeba něco přiučíte :)

Co vůbec myslím pod slovem metriky? Na první dobrou si asi většina lidí představí graf s rychlostí odpovědí
nějaké služby nebo webovky - to je určitě velice dobrý základ, ale metriky nejsou jenom o milisekundách odpovědí
a počtech chyb v kódu. Pokud jen slepě neplníte úkoly tak, jak na vás padají z vyšších míst, dost možná vás
budou zajímat i nějaké produktové nebo businessové metriky - je dobré si umět finanční výsledky firmy rozdrobit
na menší části a vidět, jak si vedou.

## Okénko do metrik automatického párování

Konkrétněji - před měsícem jsem na našem blogu popisoval něco málo o tom, [jak jsme technicky vymýšleli nové
párování]({{ site.baseurl }}/jak-fialovi-vymysleli-dasenku/). Rád bych se trochu vrátil v čase a popsal,
co tomu předcházelo. Věděli jsme, že nové párování je důležité, ale pouze tak nějak pocitově. Než se dostaneme
k tomu, že nové párování pustíme v produkci, budeme chtít vědět, že je lepší než současné řešení. Což se
jednoduše řekne, ale abychom si definovali co to „lepší“ znamená, vyjeli jsme mimo kancelář a sedli si nad to.
Strávili jsme den nad otázkou „Jak můžeme hmatatelně měřit kvalitu párování nabídek?“.

Vymysleli jsme osm metrik, které párování pokrývají. Shrnul bych je do kategorií: kvalita katalogu,
manuální práce (tudíž rychlost odbavení) a chybovost automatického párování. Kapacity týmu ovšem nejsou
nafukovací a kromě metrik trávíme čas i na jiných věcech, takže zbýval ještě krok prioritizace. Zhruba
jsme odhadli náročnost implementace měření, srovnali, co se nám jak vyplatí, a rozhodli se implementovat dvě z nich,
abychom měli data, jak si vlastně vede naše aktuální řešní a co musíme pokořit.

Jako první nastoupil počet neschválených produktů - což jsou nabídky, ke kterým jsme nenašli shodu, takže
někdo musí manuálně rozhodnout, co s nimi. Na výrobu jednoduchá metrika, vybrat z databáze celkový počet produktů
a počet neschválených, a ty někam poslat (technické okénko bude následovat). Díky této metrice po zhruba měsíci
měření víme, že tyto procenta pomalu ale jistě rostou, že na Slovensku máme takových produktů víc a že napříč
Českem i Slovenskem jde dnes o nějakých 14 milionů produktů - takže obrovský prostor pro zlepšení.

Druhá metrika je procento automaticky napárovaných nabídek, která je v kombinaci s chybovostí asi to nejdůležitější.
Trochu se nám sekla ve výrobě, ale když jsme ji konečně získali - překvapila nás. Za poslední týden jsme
automaticky napárovali 80,1 % nabídek, což není vůbec malé číslo. Trochu ale klame v tom, že když obchod svou nabídku
upraví, tak nám do tohoto procesu jde znovu, takže si s ní jednoduše poradíme. Když se ale podíváme na absolutní čísla
namísto procent, za týden to je přes půl milionu nabídek, které jsme nezvládli automaticky, takže prostor
pro zlepšení je opět vidět.

Ta nejzajímavější část práce přijde, až budeme mít MVP nového řešení a budeme moct srovnávat dvě věci vedle sebe,
do té doby budeme dál sbírat data a zjišťovat, s čím naše nové řešení vlastně zápasí.

## Jak měříme, jak sbíráme, jak vykreslujeme

V Heurece už dlouhou dobu využíváme jako uložiště pro metriky [Graphite](https://graphiteapp.org/),
kam odesíláme naměřené hodnoty za pomocí StatsD a vykreslujeme je na [Grafaně](https://grafana.com/). Nedávno jsme
se ale rozhodli pro změnu a přecházíme z Graphite+StatsD na [Prometheus](https://prometheus.io/). Hlavním důvodem
jsou labely Promethea, které nám umožní vykreslovat jednu metriku vícero způsoby za pomocí labelů, a
[AlertManager](https://prometheus.io/docs/alerting/latest/alertmanager/). 

Vykreslení vícero způsoby znamená, že jednoduchou metriku `api_response_time_seconds` (jak dlouho trvá naši službě
odbavit požadavek) si můžeme vygrafovat nejen podle konkrétních endpointů, ale i podle jejich běžících instancí,
podle služeb, které se nás ptají, podle toho jestli jde o Českou nebo Slovenskou Heureku atd. Nemusíme mít tyto
grafy předem připravené a sledovat je na televizích, ale pokud Prometheus tyto data má, můžeme se ho za běhu
ptát podle aktuální potřeby. U produktových metrik párování si tedy ukládáme v labelech například kategorie. Ale
tak to být vlastně ani nemuselo.

Jak jsme v průběhu přechodu zjistili, ne všechno se hodí strkat do Promethea. Pokud to s labely přeženete,
nebude se mu to úplně líbit a při vykreslování se to nebude líbit ani vašemu počítači. Každá hodnota labelu vytváří
novou sérii dat v čase, a pokud jich jsou tisíce, operace nad nimi jsou náročné a velice nepraktické.
Jako alternativu v tomto případě máme [ElasticSearch](https://www.elastic.co/), kam se dají posílat strukturované
logy z aplikace a Grafana se umí dotazovat i Elasticu. Pro zkoumání detailnějších věcí je tento systém vhodnější.
Každý systém byl přeci jen navržen pro rozdílné potřeby. Elastic je dražší co se potřebného výkonu týče, ale
nezhroutí se pod obrovským objemem dat.

Během vytváření našich dvou produktových metrik jsme se museli potýkat s více věcmi, na které jsme nebyli zvyklí.
StatsD naměřené hodnoty pushuje do Graphite, Prometheus naopak chodí po službách a vyzvedává si od nich naměřené
hodnoty sám. Aktuální párovací proces je cronjob a jak je o cronjobech známo, nejsou úplně uzpůsobené tomu,
že v pravidelných intervalech odbavují HTTP požadavky. Od toho autoři Promethea přišli s
[Pushgateway](https://prometheus.io/docs/practices/pushing/), ovšem není to jediné možné řešení. Pushgateway má několik
svých much, které nebudu úplně rozebírat - jen zmíním, že jsme zvažovali jiné řešení. Je možnost vytvořit si vlastní
server, do kterého job může pushovat metriky a vystavuje je - výhodou je, že ho máte plně pod kontrolou. Nemusí se
posílat kompletně celé metriky, můžou se k tomu využít jiné protokoly, může se postupně inkrementovat metriky i napříč
více běhy, job nepotřebuje speciální knihovnu… Ale pro naše potřeby jsme usoudili, že si s originálem vystačíme.

### Měření

…není nijak složité, ale v rychlosti:

#### Metrika pro počet neschválených produktů

První verzi této metriky jsme přidali k jedné z našich služeb, kdy pokaždé, když byla dotázána na metriky, tak
v rychlosti skočila do databáze, přečetla si počty, které má vystavit, a vystavila je. Rychle jsme ale přišli na to,
že výkonově to není dostatečně rychlé. Takže jsme oddělili čtení z databáze do samotného jobu,
a data sdílíme s web serverem přes Redis.

#### Metrika pro počet automaticky napárovaných nabídek

Jak jsem zmiňoval výš, jde o cronjob. Velice starý cronjob, psaný v PHP, součást našeho monolitu. Pro odesílání
jsme si v Kubernetes rozběhnuli jednu instanci Pushgatewaye, na příslušná místa v kódu přidali inkrementy
jednoduchých `Counter` metrik, přidali jim labely podle kategorie a každých x produktů odešleme.

### Vykreslování

…PromQL vyžaduje pár zkušeností.

U počtu neschválených produktů nejde o nic složitého. Napřed jsme vykreslili metriku bez žádných funkcí a i tak
nám stačila. V rámci zkrášlení jsme ale deduplikovali data, protože serverů které příslušnou metriku vystavují je víc
najednou. Takže z `products_all_count` jsme udělali `avg(products_all_count) by (language)` a vedle přikreslili druhý
graf s dotazem `avg(products_not_approved_count) by (language) / avg(products_all_count) by (language)`. Oboje jsme
vykreslili na klasickém grafu v čase a bylo hotovo.

Nad metrikou pro automatické párování proběhlo iterací víc. Při první iteraci jsme v Grafaně zaškrtli špatnou
jednotku pro procenta a pohybovali jsme se mezi 0.7%-1%, což působilo skvěle, protože jsme si mysleli, že je
extrémně hodně prostoru kam růst a že to staré řešení pokoříme levou zadní… Po dni nám to začalo připadat divné
a opravili jsme to. A zjistili, že ta úspěšnost je podezřele velká. Co hůř, hodně skákala. Takže jsme pár dní
počkali, než se nahromadí data a zkusili jsme čísla sčítat za delší interval.

![Čísla sečtené za jeden den](/assets/metriky-metriky-metriky/parovani_1d.png)

Jak je vidět na obrázku, pokud sečteme čísla za jeden den, tak nám i tak skáčou (timeshift je posun, jako kdybychom
na sten stejný graf koukali včera a předevčírem). Přidali jsme proto na dashboard přepínátko, za jak dlouho čísla
sčítat (zatím data za měsíc nejsou, ale až budou, budeme moct jednoduše přepnout). Výsledná query těchto grafů
je `sum(increase(catalogue_matching_offers_matched[$aggr])) / sum(increase(catalogue_matching_offers_processed[$aggr]))`.
Sčítáme navýšení příslušných metrik za agregační interval a vzájemně je mezi sebou dělíme, abychom zjistili procenta.

Pro zjištění přesných čísel pak stačí odmazat dělení a zobrazit na grafu každou z metrik samostatně. Poslední věc,
kterou jsme v tomto případě potřebovali, bylo čísla pochopit. Pokud máme v grafu mezi 12:00 a 12:05 hodnoty v milionech,
co to znamená? Nebo ještě lépe položená otázka - pokud nám Grafana nabízí v legendě součet hodnot, dá se podle něj
orientovat? Když takto teď vypisuji tyto otázky, je to poměrně jasné, ale chvíli jsem na ty čísla koukal a říkal
si, že si vlastně vůbec nejsem jistý. Takže - když v pětiminutovém okénku mám číslo v milionech a hodnoty mám
zagregované za 24 hodin, znamená to, že mezi 12:00 a 12:05 bylo navýšení za 24 hodin zpátky tolik milionů. 12:05-12:10
už tedy nejsou další miliony, ale opět součet navýšení za 24 hodin od té chvíle zpět. Takže součet všech těchto
hodnot v grafu je úplně k ničemu a není dobré se jím nechat zmást tak, jak se to povedlo mně.

## Závěrem

O tom, že by vývojář neměl svou práci ukončit tím, že předá úkol někomu nahoře, je napsáno spoustu, říká se tomu DevOps.
Krom toho, že je to pro firmu a komunikaci ve firmě přínosné, to taky má ten vedlejší efekt, že je práce mnohem
víc naplňující. Neustále se ptáme, proč věci děláme, čeho chceme dosáhnout a jaké to bude mít dopady.
A přesně o tom jsou dle mého názoru metriky - přesto, že přijít na ty správné trvá, tak stojí za to je mít.
