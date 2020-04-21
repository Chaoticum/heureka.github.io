---
layout: post
title: "Příběhy webmasterů: Ruský spam, subdomain hijack, nástroje hromadného ničení a SEO pro Jekyll"
permalink: /pribehy-webmasteru-rusky-spam-subdomain-hijack/
date: 2020-02-05 9:04:00 +0200
author: Zdeněk Nešpor
tags: seo hack security web
categories: [blog, bezpecnost, seo]
imageUrl: /assets/pribehy-webmasteru-rusky-spam-subdomain-hijack/duplicates-removal.jpg
---

Běžná revize webu se občas může docela nepříjemně protáhnout. Zvlášť když zjistíte, že vám ruský hacker ukradl subdoménu blogu hostovaného na Githubu. Příběh jsme se rozhodli zveřejnit i s celým postupem opravy. Návodů, jak někomu podobným způsobem uškodit a zneužít bezpečnostní díry, totiž existuje nepřeberné množství. Oproti tomu informací k prevenci a odstranění podobných problémů jsme našli přesně nulový počet.

## Rutinní kontrola

Většinu času a pozornosti jako webmasteři investujeme do hlavních webů Heureky. Microsites zpravidla nemají potenciál vygenerovat příliš mnoho problémů, takže je revidujeme spíše pomocí rychlých rutinních kontrol. Jedna taková podzimní patnáctiminutová kontrola našeho heurekadevs.cz blogu se lehce zvrhla. Protáhla se na pár dní a několik hodin čistého času.

Obvyklým krokem revize webu je pomocí **operátoru site zběžně** zjistit stav zaindexovanosti stránek a stavu SERPu. Z crawlu webu víme, že bychom měli očekávat něco kolem 40-50 výsledků. Google však výsledků zobrazuje přes 6000. Po chvíli klikání v SERPu nacházíme záplavu ruskojazyčných snippetů ze subdomény `eeghiethi.heurekadevs.cz`. 

![Ruský spam v SERPu](/assets/pribehy-webmasteru-rusky-spam-subdomain-hijack/russian-serp.jpg "Ruský spam v SERPu")

Zcela určitě víme, že subdoménu eeghiethi nikdo nezaložil záměrně. Existují dvě potenciální vysvětlení. **První hypotéza**. Je to nějaký bordel, který Google ještě nezahodil po předchozím majiteli domény. Hypotéza rychle padá. Heurekadevs.cz je relativně nová doména a jsme první majitelé. **Druhá hypotéza**. Někdo nám hacknul web, kterému jsme za SEO nevěnovali zrovna moc pozornosti. Což se potvrzuje jako správná možnost. 

## Jak na hackery

Celkem zjevně se jedná o ukázkový případ únosu subdomény (z aj. subdomain hijack či také subdomain takeover). Problém dál dokumentujeme a zkoumáme, abychom mohli do firmy předat co nejvíce relevantních informací. 

Obsah webu na eeghiethi.heurekadevs.cz ukrýval e-mailovou adresu `Mirurokov@gmail.com`. E-mail byla stopa k doméně `mirurokov.ru`, která se vyskytuje na několika méně významných blacklistech. Všechno se nakonec ukazuje být slepými uličkami, které neposkytují žádné užitečné informace.

Vygooglit frázi _„subdomain hijack github“_ je ovšem trefa do černého. Návodů jak udělat takover subdomény přes Github totiž existují desítky. Například článek [A Guide To Subdomain Takeovers](https://www.hackerone.com/blog/Guide-Subdomain-Takeovers) poskytuje poměrně detailní postup. A také obsahuje důležité vodítko pro odhalení příčiny zranitelnosti a možnou opravu. 

## Proklaté DNS

Chyba byla v našich DNS záznamech. Celá doména byla přes wildcard A record `*.heurekadevs.cz` nasměrovaná na servery Githubu. Prostě chvilka nepozornosti, když blog vznikal. Tento záznam umožnil, že si kdokoliv mohl přes Github založit pod naší doménou libovolnou subdoménu.

Stačilo jen zrušit wildcard záznam a na Github nasměrovat pouze hlavní doménu, aby byl celý problém vyřešen a spamová subdoména byla přirozenou cestou blokována. Opravu DNS jsme udělali až jako úplně poslední krok procesu. Jak jsme postup rozfázovali, je detailněji popsané v následující části textu.

## Jak si rychle zkontrolovat zranitelnost

Pokud na Githubu používáte vlastní doménu, tak si do adresního řádku zadejte URL ve tvaru `nejakablbost.mojedomena.cz`. Pokud uvidíte stejnou 404 chybu, jako je na obrázku níže, tak existuje velice reálné riziko subdomain hijacku. Jedná se o velice běžný problém a je škoda, že na potenciální rizika Github nijak neupozorňuje v [návodu ke směrování DNS na jejich servery](https://help.github.com/en/github/working-with-github-pages/managing-a-custom-domain-for-your-github-pages-site).

![Chyba 404 jako signál bezpečnostní díry pro subdomain hijack](/assets/pribehy-webmasteru-rusky-spam-subdomain-hijack/github-vulnerability.jpg "Chyba 404 jako signál bezpečnostní díry pro subdomain hijack")

## Fázování postupu a úprav

**1)** V první řadě musí proběhnout důkladnější analýza problému, jeho dokumentace, formulace hypotéz a vymyšlení první iterace optimálního postupu řešení. Reálně to zabere jen chvilku a dobře uchované informace mohou v budoucnu ušetřit obrovské množství času spousty lidí.

**2)** Po shromáždění dostatku materiálů a dat je potřeba zjistit, kdo z kolegů je za danou oblast zodpovědný a předat jim všechny relevantní informace, abychom mohli začít zabíjet nalezený spam společným úsilím.

**3)** Prostřednictvím DNS TXT záznamu verifikujeme Domain Level property v nástroji Google Search Console (GSC) pro heurekadevs.cz. Ověření domény nám dá plnou kontrolu nad celým webem a možnost zakládat libovolné URL Level properties. Dle libosti pak můžeme se stránkami nakládat i bez zapojení kohokoliv dalšího. 

**4)** Zakládáme v Search Console URL Level property pro http://eeghiethi.heurekadevs.cz. Díky tomu můžeme využít nástroj URL Removal. Ten umožňuje nechat z výsledků vyhledávání dočasně odstranit nějakou URL. Nebo taky rovnou celou doménu či subdoménu, což je přesně možnost, kterou využíváme. Během pár minut spam kompletně mizí z výsledků vyhledávání. Více o tomto nástroji se ještě dočtete v následující části. 

**5)** Opravujeme A NAME DNS záznamy, jak již bylo uvedeno v předchozí části článku. Proč je tento krok až poslední a nepředchází krokům 3 a 4? Nebyl by to žádný problém. Chtěli jsme jen v nově založené Search Console nasbírat pro zajímavost alespoň nějaká data, dokud unesená subdoména ještě fungovala.

**6)** Spam je pryč. Důsledek i jeho příčina. A k žádnému dalšímu subdomain hijacku přes Github nemůže dojít. 

![Moderní umění I: Death by DNS](/assets/pribehy-webmasteru-rusky-spam-subdomain-hijack/death-by-dns.jpg "Death by DNS")

## URL Removal Tool

Nástroj hromadného ničení. Bez jakékoliv nadsázky. Pomocí URL Removal Toolu je možné vymazat z Google SERPu jakýkoliv web, byť jen dočasně na 3-6 měsíců. Triliony adres na dvě kliknutí zmizí v propadlišti internetu. 

Nástroj je dostupný na adrese [https://www.google.com/webmasters/tools/url-removal](https://www.google.com/webmasters/tools/url-removal). Po kliknutí na tlačítko _„Dočasně skrýt“_ se objeví textové pole. Pokud pole není vyplněno, tak při pokračování do dalšího kroku je možné nechat z výsledků hledání vyřadit celou doménu libovolné úrovně. Toto nepatří mezi běžně známé informace a funkce. V době psaní článku se jedná již pouze o legacy záležitost a pozůstatek staré verze Google Search Console. Provoz nástroje bude ukončen 27. února 2020. 

![Legacy verze GSC nástroje URL Removal. Funkční do 27. 2. 2020](/assets/pribehy-webmasteru-rusky-spam-subdomain-hijack/url-removal_v2.jpg "Legacy verze GSC nástroje URL Removal. Funkční do 27. 2. 2020")

Náhradu předchozího nástroje naleznete nově přímo v Google Search Console pod záložkou Removals. Jedná se o výrazně vylepšenou verzi, která umožňuje efektivnější správu odstraněných adres. Oficiální informace o této novince případně naleznete ve článku [New Removals report in Search Console](https://webmasters.googleblog.com/2020/01/new-removals-report-in-search-console.html) na oficiálním Google Webmasters blogu. Na podzim 2019 jsme toto ještě k dispozici neměli. 

Důležitá poznámka. Tento i předchozí nástroj je v GSC dostupný jen a pouze pro majitele (Owner) nebo adminy s plným oprávněním (Full Permission). Vždy si proto dávejte pozor komu, krom přístupů k běžným datovým reportům, udělujete i možnost smazat celý váš web z vyhledávání. 

![Nová verze GSC nástroje URL Removal. Funkční od 27. 1. 2020](/assets/pribehy-webmasteru-rusky-spam-subdomain-hijack/gsc-removals.jpg "Nová verze GSC nástroje URL Removal. Funkční od 27. 1. 2020")

Ještě existuje obdobný, tak trochu skrytý a hůře dostupný nástroj na adrese [https://www.google.com/webmasters/tools/removals](https://www.google.com/webmasters/tools/removals). Slouží primárně k odstranění jednotlivých zastaralých a nefunkčních URL z vyhledávání.

![Nástroj pro odstranění zastaralého obsahu z vyhledávání](/assets/pribehy-webmasteru-rusky-spam-subdomain-hijack/removals.jpg "Nástroj pro odstranění zastaralého obsahu z vyhledávání")

## Jekyll a SEO

Při práci na blogu jsme využili několik nástrojů. Desktopový crawler Screaming Frog pro sběr dat a validaci změn. Google Search Console pro získání kontroly nad webem a provedení deindexace. 

Nakonec přišel na řadu ještě Github a [Jekyll](https://jekyllrb.com/) (jednoduchý generátor malých blogů a statických webů), protože jsme chtěli rovnou opravit i jiné nalezené chyby - HTTPS, mixed content, základní redirecty, interní linky, canonicaly, vyčistit duplicity a podobně. 

![Render webového grafu blogu před úpravami. Zelené jsou stránky na HTTPS. Modré drobné anomálie - redirectované URL, CSS a JS soubory. Červené jsou nepřesměrované duplicity stránek na HTTP.](/assets/pribehy-webmasteru-rusky-spam-subdomain-hijack/duplicates-removal.jpg "Render webového grafu blogu před úpravami. Zelené jsou stránky na HTTPS. Modré drobné anomálie - redirectované URL, CSS a JS soubory. Červené jsou nepřesměrované duplicity stránek na HTTP.")

Na první pohled nepůsobil systém jako úplně SEO friendly. Ale proniknout do světa Jekyllu, [Liquid Filters](https://jekyllrb.com/docs/liquid/filters/) a připravit k nasazení opravy všech chyb bylo nakonec otázkou cca 3 hodin. Žádná velká věda. Můžete se přesvědčit sami. Blog má veřejně dostupný kód, viz [https://github.com/heureka/heureka.github.io](https://github.com/heureka/heureka.github.io). 

Klíčové pro nás je, že musíme velmi detailně znát možnosti běžně dostupných nástrojů a umět je ovládat. Nutně si musíme udržovat na dobré úrovni znalosti základů kolem DNS, různých CMS, kódování, programování, zabezpečení webů a tak dále. Jako webmasteři díky tomu můžeme odhalit spoustu problémů, sami je z části i vyřešit a ušetřit cenný čas vývojářů.  

Na gifu níže se můžete podívat na animaci, jak se změnil webový graf heurekadevs.cz po odstranění duplicit. Pro tvorbu animace byl využit specializovaný software Gephi s využitím algoritmu Force Atlas 2. Barevné kódování je stejné jako u obrázku výše. 

![Animace vývoje webového grafu a algoritmu Force Atlas 2](/assets/pribehy-webmasteru-rusky-spam-subdomain-hijack/animation-gif.gif "Animace vývoje webového grafu a algoritmu Force Atlas 2")

## Závěrem

Uběhlo mnoho týdnů od odstranění eeghiethi spamu z vyhledávání. Google stále zobrazuje u dotazů se site operátorem chybné číslo počtu výsledků z dané domény. To se pravděpodobně změní až ve chvíli, kdy vyprší blokace subdomény a Google zjistí, že již nefunguje. Sami jsme na to docela zvědaví. 

![Screenshot byl pořízen 29. ledna 2020](/assets/pribehy-webmasteru-rusky-spam-subdomain-hijack/site-operator.jpg "Screenshot byl pořízen 29. ledna 2020")

Pokud používáte Github pro hostování nějakého webu, tak si dejte pozor, jak pracujete s DNS. Pro web si založte Google Search Console a naučte se ten nástroj používat. A dejte si pozor komu dáváte v GSC plná oprávnění. Opravujte rychle, efektivně, v logické souslednosti a jděte primárně po příčině problému. 

Jako webmasteři v Heurece se musíme umět chopit práce a nespoléhat se jen na to, že vývojáři budou mít zrovna prostor opravovat každou drobnost. Úspěch jsme slavili tak minutu. Víc času nebylo. Za ty dva dny, co jsme řešili devel blog, nám v jiné GSC property vyskočilo přes 100.000 chyb ve strukturovaných datech. Ale to už zase na jiné povídání.

Na tomto místě je ještě dobré poznamenat, že k bezpečnosti v Heurece přistupujeme velice poctivě, důsledně a bez kompromisů. Tento drobný bezvýznamný úlet s Githubem publikujeme především jako úsměvný příběh. Krom toho, fail stories mohou pomoci začátečníkům a méně zkušeným uživatelům, kteří řeší podobný problém.

## Představujeme náš SEO tým 

Dočetli jste první článek od Heureky se SEO tematikou. Další budou následovat. Dovolíme si proto této příležitosti využít k představení našeho SEO týmu.

* **Luděk Pulicar** - Head of SEO & Webmaster. Zaštiťuje SEO v celé Heureka Group.
* **Zdeněk Nešpor** - Crawl master & SEO specialist. Stará se o pohodlí botů na webech Heureky.

![SEO tým Heureky](/assets/pribehy-webmasteru-rusky-spam-subdomain-hijack/zdenek-a-ludek.jpg "SEO tým Heureky")
