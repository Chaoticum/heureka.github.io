---
layout: post
title: "Full Stack Fest 2019"
permalink: /full-stack-fest-2019/
date: 2019-10-21 12:00:00 +0200
author: David Šanda
tags: konference frontend backend web
categories: [blog, meetupy_a_konference]
imageUrl: /assets/2019-10-16-full-stack-fest-2019/intro.png
---
Frontendová i backendová témata na jednom místě! GraphQL, WebAssembly, JAMStack&nbsp;&&nbsp;Serverless, HTTP/3, automatizované testování nebo P2P webu – to je konference Full Stack Fest.

![intro](/assets/2019-10-16-full-stack-fest-2019/intro.png)

Full Stack Fest není pro HeurekaDevs žádnou novinkou. Účastníme se jí pravidelně každé září, ať už ve větším nebo v menším zastoupení. Letos se konference výjimečně nekonala v Barceloně, ale v sousedním městečku Sitges a byla “pouze” třídenní.


## A jaké přednášky stojí za zmínku?


### Applied Accessibility: Practical Tips for Building More Accessible Front-Ends
> by Sara Soueidan

Sara Soueidan ve své přednášce mluvila o důležitosti přístupného uživatelského rozhraní, neboli čitelnosti obsahu čtečkami pro nevidmé. Dle reakcí v sále si s touto problematikou moc vývojářů hlavu však neláme. Jelikož se jednalo hlavně o praktickou přednášku, Sara ukázala několik tipů, ukázek a rad pro zlepšení přístupnosti obsahu, základních věcí jako je využívání sémantičnosti/sebe popisnosti HTML elementů, tedy např. pro nadpis `<h{1, 2, 3,...}>`, pro odstavce `<p>` element nebo v HTML5 představené elementy `<section>`, `<nav>`, `<header>`, `<button>` atd., přes ukázku toho, jak udělat přístupný SVG graf, až po správně přístupné nastylování custom checkboxu.


### We're gunna program like it's 1999
> by Lee Byron

![Lee Byron](/assets/2019-10-16-full-stack-fest-2019/lee_byron.png)

Přednáška od Lee Byrona zrekapitulovala vývoj webu za posledních 20 let, od fascinujících možností, které nám dal 56kb modem, přes PHP a Flash, až po React a GraphQL. Na příkladech poukázal na fakt, že v důsledku se spousta věcí za tuto dobu zase až tak moc nezměnila a že aktuálně se vývoj webu snaží přiblížit jednoduchosti jakou jsme tu měli před 20 lety.


### The Future of Web Animation
> by Sarah Drasner

Sarah mluvila o tom, jak jsou důležité animace v kontextu webových stránek či aplikací a jakým směrem by se mohly vyvíjet do budoucna. Ukázala i několik příkladů jak animovat webový obsah, aby vypadal více “nativně”, podobně jako obsah na mobilních zařízeních. Ukázala také, jak je možné využít AR nebo VR pro prezentaci či pro výuku.


### HTTP/3 - HTTP OVER QUIC IS THE NEXT GENERATION
> by Daniel Stenberg

![HTTP3](/assets/2019-10-16-full-stack-fest-2019/http3.png)

Daniel, tvůrce nástroje curl, který najdeme prakticky všude, nastínil jak bude vypadat příští (třetí) generace HTTP. Spoiler alert: UDP + QUIC. Zmínil problémy, které vyvstávají z implementace stávajících protokolů, co a jak se snažili různé verze řešit. A jaké problémy by měla řešit nová verze HTTP.


### Spreading the JAM throughout your CI workflow
> by Bria Douglas

Přednáška ukazující, že většina příkladů lze vysvětlit na baseballu. Ano, na baseballu. Brian ukázal jaké výhody přináší využití JAMstacku. Staticky vygenerované stránky mohou být hostované kdekoli a například s využitím GitHubu, který poskytuje několik procesů, jak je automaticky publikovat. Následovaly názorné ukázky GitHub Actions (což je obdoba pipelines jaké nabízí Gitlab, která je aktuálně ve veřejné betě), Zeit Now, Netlify a dalších.


### CSS Houdini Today
> by Una Kravets

Una předvedla skvělou ukázku možností, které přináší Houdini a jak může změnit vzhled webových stránek. Houdini není zatím moc podporovaný prohlížeči, ale podpora se postupně zlepšuje. V Chrome je možné již nyní zapnout jeho podporu skrze příznak chrome://flags/#enable-experimental-web-platform-features. Jeho povolení vám dovolí skoro doslova programovat v CSS. Pro příklady nahlédni zde [extra-css](https://extra-css.netlify.app/).


### Standardizing JavaScript Decorators in TC39
> Daniel Ehrenberg

Daniel, který je členem TC39, vysvětloval, jak funguje proces schvalování v TC39. Aktuálně je zodpovědný za 3 návrhy ve stádiu Stage 3: BigInt, Class Public Instance Fields & Private Instance Fields a Private instance methods and accessors a za 1 návrh ve Stage 2: Decorators. Poslední ze zmiňovaných je už teď možné vidět napřiklad v ember, Angular nebo MobX. To jejich schválení dělá daleko náročnějším, jelikož každý na ně má trochu odlišný názor a je na TC39 (obsahující jak zástupce Ember.js, tak Angularu), aby přišli s řešením, které nerozbije internet.


### Závěrem
Konference stála za účast a určitě doporučuji zhlédnout alespoň [online](https://conferences.codegram.com/conferences/fsf2019) některé přednášky, témata byla zajímavá a s dobrými speakery. Určitě doporučuji v budoucnu navštívit a třeba se tam i potkáme.
