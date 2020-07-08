---
layout: post
title: "Co dělat na Java konferenci, když vás Java nezajímá"
permalink: /devoxx-pl-2019/
date: 2019-07-22 12:34:00 +0200
author: Honza Haering
tags: [konference, devoxx]
categories: [blog, meetupy_a_konference]
imageUrl: /assets/devoxx-pl-2019/ICE_Krakov.jpg
---

V Krakově na [Devoxx Poland 2019](http://devoxx.pl/) jsem si potvrdil, že když se konference o Javě udělá dobře, naprosto se na ní neztratí ani člověk, který o Javě neví o moc víc, než že dostala jméno podle JavaScriptu.
Dobří a zkušení řečníci (jejichž nejčastějším doporučením je "well, it depends") zkrátka dokáží téma podat natolik s nadhledem, že si svoje odnese každý.
Právě přednášky s větší mírou abstrakce si vybírám nejraději – jednak v nich bývá hodně přesahů a informací "mezi řádky", jednak je větší šance, že se trefí do problémů, které řešíme aktuálně i my v Heurece. Což se mi letos víc než potvrdilo.

![ICE Krakov](/assets/devoxx-pl-2019/ICE_Krakov.jpg)

## Agilní ve větším měřítku

V Heurece jsme nedávno prošli důležitou změnou struktury vývojových týmů. Rozdělili jsme se důsledně podle jednotlivých produktových oblastí (vertikálně) a upustili od dělení podle technologie (horizontálně).
Na toto téma jsme si loni udělali jednodenní workshop, kde jsme Heureku rozkrájeli na jednotlivé produktové oblasti.
Proto mě zaujala přednáška od Juliena Lavigne, který představil zajímavé [řešení problému](https://medium.com/@julien.lavigne/continuous-reteaming-and-self-selection-cbe0df69a9a7) s malou flexibilitou takových produktových týmů, které nedokáží z hlediska své kapacity reagovat na rychle se měnící byznys priority.
Zavedli si zkrátka hodně podobné workshopy, jako jsme měli my, pravidelně každého čtvrt roku.
Workshop za účasti vývojářů, produkťáků, ale také stakeholderů má minimum pevně daných pravidel, jako třeba že všechna naplánovaná práce musí být na konci rozdělena, nebo že v každém týmu se musí změnit alespoň jeden člen, naopak alespoň jeden musí zůstat.
Na konci dne po několika iteracích mají nové týmy rozebrané oblasti a může se začít s tvorbou [OKR](https://en.wikipedia.org/wiki/OKR).
Po několika realizovaných kvartálech jsou dle Julienových slov s výsledky spokojeni, ale samozřejmě se potýkají i s problémy, jako je hůř definované vlastnictví projektů
nebo určitá daň za častější přepínání kontextu.
Bude hodně zajímavé sledovat, kam se to vyvine.

## Mikro s rozumem

Výtečný přednášející [Nathaniel Schutta](http://www.ntschutta.io/) ve své úvaze [Responsible Microservices](https://tanzu.vmware.com/content/blog/should-that-be-a-microservice-keep-these-six-factors-in-mind)
pěkně shrnul nebezpečí bezhlavého přepisu veškerého legacy kódu do mikroslužeb. Ty, jakkoliv mohou být dobrým řešením, přináší vyšší úroveň komplexity, která navíc nemusí být vyvážena benefity.
Uvedl proto 6 faktorů, které mohou sloužit jako vodítko, zda má smysl danou část separovat do mikroslužby. Jedním z méně zjevných kritérií je např. četnost změn – čím častěji se daná část systému mění,
tím větší smysl dává mít její kód odděleně.
Dalšími poměrně zjevnými faktory je odlišná škálovatelnost či možnost volby optimální technologie.
Myslím, že i když jsme se v Heurece vydali jednoznačně směrem k architektuře mikroslužeb, není vůbec na škodu se neustále ptát, zda a proč konkrétní doménu oddělit a jestli by se jí nakonec lépe nedařilo jako součásti většího celku.

## Shipping code like a Keptn

Andreas Grabner z Dynatrace [představil](https://www.slideshare.net/grabnerandi/shipping-code-like-a-keptn-continuous-delivery-automated-operations-on-k8s) rozrůstající se opensource projekt [Keptn](https://keptn.sh/), který
si dává za cíl vyřešit narůstající složitost systémů pro nasazení a provozování jednotlivých částí systému.
Byl primárně vyvíjen pro [Kubernetes](https://kubernetes.io/), ale postupně bude podporovat i další kontejnerové orchestrátory jako [Openshift](https://www.openshift.com/).
Jádrem je event-driven operátor, který umožňuje díky deklarativnímu přístupu odpojit aplikace od implementačních detailů vašeho CI/CD systému. Kromě fáze `Continous deployment` se zaměřuje také na
fázi následnou, tj. `Automated operations`. V praxi tedy jednak dokáže pohlídat, že se aplikace, které neprojdou testovací fázi, nedostanou ze staging na produkci. Kromě toho je ale také napojen i na produkční
monitoring (pro nás potěšující, že je podporován [Prometheus](https://prometheus.io/), který jsme si v HeurekaDevs vybrali jako svůj nastupující monitorovací systém) a dokáže automaticky sjednat nápravu, pokud se metriky dostanou do červených čísel. Například vrátit do produkce předchozí release.


## Pohled do zrcadla

Co mě opravdu pobavilo, bylo zjištění, že přednáška [How to break an 18 yo monolith](https://blog.andi95.de/2019/07/devoxxpl-my-talks-and-slides/) od Andrease Bräu, se týká monolitu
[idealo.de](https://www.idealo.de/), tedy německého klonu Heureky. V tamním vývoji se roky potýkali s podobnými problémy, co jsme řešili i my – odsekávání z monolitu po částech bylo pomalé a nekonečné.
Až přišla loni v létě pro vývojáře spásná zpráva: Od druhé poloviny roku 2019 končí podpora hlavní databáze. Rázem byla motivace i páka na stakeholdery. Zkrátka to museli stihnout za rok.
Vydali se cestou [Self-contained systémů](https://scs-architecture.org/), tedy autonomních aplikací, vlastněných jedním týmem. Všude, kde to je možné, se komunikuje asynchronně, synchronní API volání se nepoužívají vůbec.
Hlavní technologie pro výměnu zpráv mezi SCS je [Apache Kafka](https://kafka.apache.org/). Podle Andreasových slov za rok s nožem na krku rozsekání starého monolitu víceméně stihli, klobouk dolů.
Zajímavé, ale vlastně ne moc překvapivé, bylo zjištění, že produktově si svůj systém rozdělili na jednotlivé oblasti stejně, jako jsme to udělali my.

## Jak se krájí na frontendu

David Leitner se v přednášce [Micro Frontends – a Strive for Fully Verticalized](https://speakerdeck.com/duffleit/microfrontends-f5b07c7f-392b-4e73-b788-2806ba7341d3) zabýval tématem, které také hýbe Heurekou – aplikací principu mikroslužeb na frontend, neboli mikrofrontendy.
V zásadě shrnul 3 existující možnosti, jak integrovat několik SPA aplikací do jedné stránky:
- build time integration ([monorepo](https://medium.com/@brockreece/from-monolith-to-monorepo-19d78ffe9175))
- server side integration – to je cesta po které jsme se vydali my s naší vlastní integrační proxy, ale existují i opensource řešení jako [Mosaic](https://www.mosaic9.org/)
- runtime integration – neboli poskládání všeho v prohlížeči. Jedním ze zmiňovaných nástrojů je [singl-spa](https://single-spa.js.org/). Jako prerekvizitu pro tento způsob integrace webu David zmínil metodologii [Immutable Web Apps](https://immutablewebapps.org/), což je v podstatě rozšíření myšlenek [Twelve-Factor App](https://12factor.net/) do světa frontendu.

Na konci ovšem neopomněl zmínit to, co mimochodem zaznívalo na konferenci velmi často – mikrofrontendy jsou distribuované systémy, ve kterých platí víc než kde jinde Murphyho zákony.
Řeč proto zakončil kacířskou a lehce ironickou otázkou, jestli raději nechceme zkusit cestu monolitu.

## Místo

Co samotný Krakov? Jestli jste tam ještě nebyli, musíte tam! Je to krásné, historické a přitom velkorysé a nenucené město, s hospodou, barem nebo kavárnou doslova v každém druhém domě.
Židovská diskotéka na dvoře synagogy pak byla příjemnou třešničkou na dortu.

![Jewish disco party](/assets/devoxx-pl-2019/Jewish_disco.jpg)
