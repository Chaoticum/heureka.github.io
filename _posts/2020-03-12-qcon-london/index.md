---
layout: post
title: "QCon London 2020"
permalink: /qcon-london-2020/
date: 2020-03-16 10:21:10 +0200
author: Oldřich Taufer
tags: [konference, qcon, architecture]
categories: [blog, meetupy_a_konference]
imageUrl: /assets/qcon-london-2020/qcon.jpg

---

Co se vám vybaví, když se řekne slovo Anglie? Za mě je to určitě Rowan Etkinson, Červený trpaslík, spousta nádherných historických památek a mraky přechodů, na kterých vás potenciálně srazí auto, protože jste se stále nenaučili, na které straně se správně jezdí. O to větší překvapení pro mě bylo, že letos se přímo v Londýně konala konference QCon.

Neznáte ho? Nevadí. Je to jedna z nejprestiznějších konferencí pořádaná pod záštitou InfoQ. Tentokrát se jim do 3 dnů podařilo narvat více než 140 speakerů, kteří se snažili předat informace o nových technologiích a návrzích systémů. Rozhodl jsem se vybrat pár zajímavých i vám a třeba vás nalákat na další Qcon, který se bude konat v Německu (pokud to koronavir dovolí).

![QCon](/assets/qcon-london-2020/qcon.jpg)

## Cestovatelský fakt
Pokud si myslíte, že cesta z Liberce do Prahy už v neděli ve 3 ráno vám ušetří nějaký čas, tak se šeredně pletete. Budete mít perfektně zkontrolovanou povinnou výbavu, budete si opravdu jistí, že jste se alkoholu nenapili na D10, Pražském okruhu ani na Evropské. Čas cesty v noci je tak díky kvalitním kontrolám naší policie srovnatelný s jízdou ve špičce.

## The Internet of Things Might Have Less Internet Than We Thought?

![Alasdair Allan](/assets/qcon-london-2020/allan.jpg)

Vzpomínáte si kauzu "locationgate" (kauza, kdy Apple musel objasnit veřejnosti, proč zaznamenává data o poloze majitelů svých zařízení)? Znáte projekt hackster.io? Sledovali jste objevení zatím nejdále vzdáleného objektu? Se všemi těmito údálostmi se pojí jedno jméno, a to jméno přednášejícího Alsdaira Allana.

Tento vědec, hacker a žurnalista měl hned úvodní přednášku celého QConu. Rozpovídal se převážně o nebezpečí věcí uložených v cloudu a následné ztrátě soukromí. Nastínil myšlenku, jestli s čím dál výkonnějšími a menšími zařízeními není na čase opět začít využívat edge computing místo cloud computingu. Jedním ze super věciček, na kterých ukazoval důležitost soukromí, byl pro mě neznámý Bracelet of silence. Najděte si ho, je to pecka!

## Monolith Decomposition Patterns

![Sam Newman](/assets/qcon-london-2020/newman.jpg)

Není to tak dávno, co jsme se v Heurece (konečně) rozhodli, že monolit je třeba rozbít a udělat z něj mikrolit. Jedním z nejzvučnějších jmen poslední doby ohledně microservice je samozřejmě Sam Newman. Není divu, že mu v konferenčním středisku vyhradili jednu místnost a v druhé byl promítán na plátně. Během hodiny se snažil vysvětlit jakými způsoby rozbít monolit do micro service tak, abychom při tom stále mohli vydávat nové featury.

Základní myšlenkou je přepisovat postupně: pokud děláte big rewrite -> přijde big bang.

**Jedním ze způsobů jak na to:**
1. identifikujte funkcionalitu v monolitu (ucelenou business logiku)
1. v monolitu vytvořte abstraction point, přes který bude proudit veškerá komunikace
1. vytvořte oddělenou MS a přepište logiku (přepis je lepší, ale nevadí ani copy paste)
1. přepněte komunikace z abstraction point na MS
1. ukliďte starý bordel.

Nejhorší stav je, když vám běží komunikace přes MS a existující implementace v monolitu zároveň.

Za mě to byla jedna z nejlepších přednášek z celého QConu a dala mi na přepis monolitu úplně nový pohled. Doporučuji všem, kdo se na podobný přepis chystáte, zhlédněte nějaké přednášky od Sama.

## Kafka: a Modern Distributed System

![Tim Berglund](/assets/qcon-london-2020/berglund.jpg)

V rámci návrhu nového systému pro propojení 9 srovnávačů, o kterém už dříve [psali kluci](/prvni-kroky-sdileneho-katalogu-jsme-planovali-v-madarsku/), jsme se v Heurece rozhodli použít pro komunikaci Kafku. Proto jsem si nemohl nechat ujít přednášku od Tima Berglunda, který se snažil popsat, jak to celé funguje.

Hned v úvodu vysvětlil, že pokud nepochopíte, jak Kafka pracuje s daty, nikdy neuděláte správný návrh aplikace. Snažil se nám natlouct do hlavy základy, jak fungují controllery, jaká je role zookeepera, co jsou to to topicy, produceři, konzumenti, jakým způsobem se dotazují na data, jak funguje replikace, atd.

Musím říct, že i když většinu těchto věcí dohledáte lehce na internetu, tak takhle poutavě jsem to od nikoho ještě neslyšel. Na závěr celou komunikaci vysvětlil na pár vybraných dobrovolnících z publika, kteří představovali Kafka systém. Bylo to fakt super a pokud budete mít možnost někde zajít na jeho přednášku, určitě to udělejte.

## Cestovatelský fakt 2
Do žádných veřejných míst vás nepustí bez toho, aniž by vám předtím vydezinfikovali ruce. V dnešní době je to asi dobře, ale že vás na konci přechodu zastaví policista, který rozdává dezinfekci, jsem opravdu nečekal.

## Technical Leadership Through the Underground Railroad

![Anjuan Simmons](/assets/qcon-london-2020/simmons.jpg)

Osobně úplně nesnáším všemožné knížky se spoustou "osvědčených" tipů o tom, jak být dobrý rodič, jak mít správně uspořádané myšlenky, jak správně pokládat nohy při běhání... Podobný názor má Anjuan na to, jak být dobrým leaderem. Přitom stačí zahodit knížky a poučit se z historie. Jedním takovým dobrým leaderem, o kterém nám vyprávěl, byl i John Parker v 19. století. Je strašně zajímavé, jak lehce můžete najít asociace mezi vedením distribuovaných týmů a 200 let starou událostí, jako Underground railroad.

## Lessons From DAZN: Scaling Your Project with Micro-Frontends

![Luca Mezzalira](/assets/qcon-london-2020/mezzalira.jpg)

Narvaný sál k prasknutí značí, že je buď výborný přednášející nebo téma, které aktuálně trápí hodně lidí. V tomto případě to byla kombinace obojího. Micro frontendy očividně začaly lákat čím dál víc firem. Tahle přednáška měla skoro všechno. Vysvětlení, kde se mikrofrontendy hodí (single-page aplikace a monolit nejsou deprecated), výhody a nevýhody horizontálního a vertikální rozdělení, možné způsoby komunikace mezi nimi a samozřejmě popis implementačních problémů, které se jim přihodily (například problémy s abstrakcí, kterou měli postavenou a kterou museli zrušit). Upřímně to bylo takové množství informací, že jsem jich dokázal vstřebat sotva polovinu. Tuhle přednášku si musím ještě párkrát pustit.

## Tiny Go: Small Is Going Big

Už při studiu na fakultě Mechatroniky mě strašně bavilo programovat různé roboty. Říkal jsem si, že tahle přednáška by mohla být super a taky to tak bylo. Ron Evans si k sobě přizval i svého syna a ukazoval nám, jak TinyGo může fungovat i na 8bitových procesorech. A co to vlastně je? TinyGo je kompilátor pro Go, napsaný v Go (jo, zní to směšně, ale je to tak). Samozřejmě nechyběly ani ukázky blikajících mikroprocesorů či dronu, který pomocí kamery rozpoznává, jestli vidí člověka nebo ne (já jsem se mu jako člověk nezdál, téměř všichni ostatní ano).

## Cestovatelský fakt 3

Pokud si myslíte, že jste v pořádku dorazili domů a že se vám už nic nemůže stát, dejte si pozor. Hlava si rychle zvykne na to, že na přechodu se máte dívat na druhou stranu a pokud vás nezajelo auto v Anglii, dost pravděpodobně se to stane doma.

## Závěrem

Celý QCon byl opravdu nabitý. Přednášky začínaly v 9 ráno a končily v 6 večer. Nezažil jsem žádnou, která by byla vyloženě špatná. Nechci se tu rozepisovat o všech, které jsem navštívil (to by bylo pomalu na knížku), ale až budou dostupná videa, tak určitě doporučuji zkouknout ty v oboru, který vás zajímá (leadership, security, devops, architecture, ..).

Jak už to tak bývá, některé přednášky, co bych rád viděl, se mi kryly, takže mě to čeká také. Pokud budete mít možnost, tak další konferenci určitě doporučuju navštívit. Nejde tak ani o přednášky, ale o následnou komunikaci s lidmi, kteří opravdu vědí, co dělají. Nápad, kdy přes obědy máte na jednotlivých stolcích cedulky s tématem, o kterém se chcete bavit, bych zavedl 365 dní v roce a všude.