---
layout: post
title: "Rozkaz (ne)zněl jasně: Budeme Heureku provozovat v Cloudu, nebo si postavíme vlastní on-premise řešení přes několik datacenter?"
permalink: /cloud-nebo-on-premise/
date: 2019-12-02 8:40:00 +0200
author: Vít Bartůněk
tags: architektura OnePlatform MěnímeHeureku
categories: [blog]
imageUrl: /assets/cloud-nebo-on-premise/thumb.png
excerpt_separator: <!--more-->
---

Kdepak rozkaz, ale cíl zní jasně. Spojit tři srovnávače fungující v 9 evropských zemích na jednu platformu. Vyvíjet jeden produkt pro všechny. Jenže jak k tomu dojdeme?

Náš šéf má jednu skvělou vlastnost. Dokáže se ptát a naslouchat, má zkrátka zájem o názory druhých. Máme tedy svobodu vybrat si na jakém projektu a s kým budeme spolupracovat a jaké nástroje/technologie k tomu vybereme. To je výhodné pro obě strany. Díky tomuto přístupu jsme motivovaní dojít k cíli, win-win.

Ale zpátky k technologiím, je to dnes opravdu o tom, rozhodnout se mezi public cloudem, nebo provozem ve vlastním datacentru?

<!--more-->

## Mezitím na domácím hřišti

Uvedu to celé do kontextu. Poslední rok a půl jsme strávili [přepisováním Heureky z monolitu do mikroservis](/menimeheureku-chystame-mezinarodni-oneplatform/), vznikly [autonomní týmy](/jak-jsme-si-rozdelili-produktove-oblasti-do-tymu/) zodpovědné za konkrétní produktovou oblast.

Splácíme technologický dluh a nejsme u konce. Polovina frontendu stále běží z monolitu a hlavně nám tam stále straší obrovský data-lake (pár TB dat v mysql), na které jsou mikroservisy napojené.

Jasně, jde to dle plánu, nejprve zatemovat byznys logiku v mikroservisách a následně přeskládat data na menší hromady. Výměna dat mezi mikroservisama je ten největší „challenge“. Chceme tvořit robustní služby, které ustojí kdejaké klopýtnutí.

A tohle nám zatím moc nejde, stále máme mikrofrontendy příliš fragilní. Zpomalení některých backendů teď vyvolá rachot, jako když hodíte sifonovou láhev do vany.

Keep it simple. Někde stačí synchronní komunikace – api cally mezi službama. Naopak někde je potřeba mít kvůli rychlosti agregovaná data blízko frontendů, datové fronty a obecně CQRS architektura. Zkrátka, je to cesta. Velmi podobná cesta jako pospojovat datové služby napříč několika datacentry v Evropě. Tedy cvičíme na novou platformu nasazenou napříč několika clustery.

## Deployment přes několik regionů

![]( /assets/cloud-nebo-on-premise/thumb.png)

V Heurece už nějaký čas provozujeme privátní Kubernetes cluster hostovaný ve dvou lokalitách. Říká vám něco Kubernetes cluster federation? Jedná se o konzistentní deploy do několika clusterů naráz s možností zahrnout specifika služeb pro jednotlivé trhy.

Komponenty frontendu i backendu, ať už je to pohled na detail produktu, stránka s výpisem kategorií, vyhledávací okno nebo služba obsluhující katalog, jsou nasazovány naráz do všech Kubernetes clusterů.

S využitím geoip dostane klient vždy nejbližší endpoint load-balanceru, kam se připojí pro nejrychlejší odezvu. Jestli budou běžet jednotlivé Kubernetes clustery pospojované do federace provozované u nás, v public cloudu, nebo to bude mix obou variant, nakonec není možná to, co je potřeba striktně rozhodnout.

Je to o celkových nákladech na provoz a možnosti využít jednotlivé služby, které cloud nabízí. Pro malý byznys, který potřebuje rychle škálovat, jde o rozhodnutí zřejmé. Psát si řešení na míru, kde musím vlastnit všechen HW na pokrytí největší špičky, který bude většinu času ležet ladem, nedává smysl. U velkého molochu jako je 9 srovnávačů, které mají navíc v podstatě kontinuální provoz, je úspora mimo public cloud obrovská.

## Pohled na on-premise

Kubernetes provozujeme na našich serverech umístěných ve dvou pražských datacentrech. Mezi nimi jsou duplikované optické propoje. Pro zjednodušení se pak obě serverovny tváří jako jedna větší síť rozdělená do jednotlivých VLAN. Cesta k L3 síti nás teprve čeká.

Jednotlivé k8s nody nám běží přímo na fyzických mašinách. Bereme stroje od Supermicra, historicky SuperBlady a nyní BigTwiny. Z pohledu virtualizace zvažujeme přidat jednu vrstvu (KVM), spravovanou třeba OpenStackem, mezi fyzické stroje a k8s nody. Mohlo by to vyřešit problémy se správou procesů, paměti (pinning na NUMA nody) a přidat větší flexibilitu.

Údržba Kubernetes z pohledu týmu, co se u nás stará o infrastrukturu, představuje udržování dostatečné kapacity clusteru, management oprávnění, provozování síťového storage pro pody (CEPH), rozvoj controllerů/operátorů pro poskytování specifických zdrojů, např. clustery pro MySQL (Percona), monitoring (Prometheus), messaging (RabbitMQ, NATS).

## Změna nabízené vrstvy vývojářům od PAAS k IAAS nám rozvázala ruce

Desítky nově vzniklých mikroservis by bylo obtížné na klasických kontejnerech udržovat a držet tempo s potřebami vývoje. Každý tým má k dispozici několik namespaců, generický pro svůj tým a jednotlivé pro [produktové oblasti](/jak-jsme-si-rozdelili-produktove-oblasti-do-tymu/), které rozvíjí. Příkladem může být ElasticSearch pro vyhledávání, který má přes 50 podů. Deploy dalších 8 ES nodů je pak otázkou několika minut.

Manifesty služeb definujeme přes helm, s kterým měli vývojáři porodní bolesti, tvorba šablon v něm totiž není moc přehledná. U některých služeb, kde jsme chtěli striktně spárovat runtime s repositářema, máme nasazený Flux a sealed secrets. Debugování při chybě deploymentu je tím však o trochu složitější.

## Těšíme se na to, co ještě potřebujeme dotáhnout

Chystáme se nasadit Istio/Envoy jako řešení zejména pro telemetrie mezi servisama a distribuovaný tracing. Snadno a rychle, tak půjdou dohledávat situace, když se začnou ucpávat trubky. Navíc teď se rate-limiting a circuit breaking implementuje v některých mikroservisách od nuly. Ideální by bylo mít toto pomocí mesh sítí vyřešené napříč celým clusterem.

Mesh síť nám pomůže i v rámci dalších vylepšení s deploymentem. Aktuálně probíhají aktualizace na nové verze služby pomocí RollingUpdate. To má omezené možnosti rychle se vrátit na předchozí verzi v případě nečekaného chování v produkci. Pro downgrade služby je potřeba udělat zcela nový deployment s původní verzí manifestu (neprovádíme živý switch na původně běžící instance). Image uvedený v původním manifestu však už nemusí být dostupný v repozitáři a downgrade tím selže. V kombinaci Istio + Spinnaker se dá prostřednictvím chytrých Canary release výrazně redukovat množství nečekaných chyb v produkci a proces deploymentu plně zautomatizovat.

**Vydání nové verze do produkce pak proběhne ve zkratce následovně:**
* Deploy nové verze služby.
* Deploy duplikátu staré verze služby.
* Na obě verze pošlu malé procento provozu (5 %).
* Vyhodnotím rychlosti odezev, chybových stavů z telemetrie těchto dvou služeb.
* Pokud je vše v pořádku, v několika iteracích zvyšuji množství provozu na novou instanci.

Testovací provoz je potřeba omezit na konkrétní výběr uživatelů na základě obsahu requestu, který mám k dispozici. Chceme zaručit, že uživatel nedostává náhodně starou a novou službu napříč požadavky v čase.

## Kam vypustíme smečku?

Z předchozích zkušeností nám dává smysl stavět služby odolné a robustní s ohledem na výměnu a zpracovávání dat. Výhod tohoto řešení chceme využít i při designování infrastruktury.

Zprovozníme tedy hybridní řešení s využitím současných datacenter a public cloudu i za cenu toho, že budeme tvořit jednu vrstvu navíc, kterou je potřeba spravovat v porovnání s provozem služeb v rámci jedné lokality. Služby, které běží kontinuálně a jsou náročné na HW, budeme provozovat v on-premise cloudu pro ušetření nákladů. Public cloud pak pro vykrytí výkonu ve špičkách a využití technologií, které nemáme zatím dobře orchestrované.

Pořád otázkou zůstává jaký jazyk či abstrakci zvolit pro definici zdrojů takové infrastruktury. Víme, že variant je dnes již více než dost. Jen se musíme pro jednu rozhodnout.
