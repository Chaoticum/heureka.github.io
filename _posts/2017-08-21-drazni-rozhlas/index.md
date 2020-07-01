---
layout: post
title:  "Kancelářský drážní rozhlas z buildserveru"
permalink: /drazni-rozhlas/
date:   2017-08-21 11:38:01 +0100
author: Filip Melík
tags: fun
categories: [blog, kod, legrace]
---

V libereckém kanclu máme build server pro naše mobilní aplikace ([iOS][1] a [Android][2]), který má na starost jejich kontinuální integraci a dělá nightly buildy z vývojové branche pro produkťáka. Konkrétně je to Mac mini late 2014 (3GHz core i7).

Tak to má zřejmě nakonfigurované kde kdo, ale to je nuda. Takže jsme si v kanclu řekli, že bychom to mohli trochu oživit!

OSX má v sobě zabudovaný poměrně dobrý zvukový syntetizér, čehož se dá využít ke spoustě nepravostem.

## Jak na to #1

Jeden ze způsobů jak syntetizér bez námahy vyzkoušet je zapnout si terminál a použít příkaz `say`.

`say --voice=Zuzana "mňau, bum bác"`

Je možné, že zrovna tenhle hlas nebudete mít v systému předinstalován, ale hlasy se dají kdyžtak [snadno doinstalovat][6]

Příkaz say toho ale umí mnohem víc, kdyžtak zavolejte panu manovi, který toho o příkazech ví ze všech nejvíce:

`man say`

## Jak na to #2

Druhý způsob jak syntetizér použít, je spíchnout jednoduchou macOS appku, která použije třídu [NSSpeechSynthesiser][3]. V dokumentaci je všechno co člověk potřebuje vědět, je to konfigurace asi na 3 řádky.

## Co s tím?

Protože máme rádi katastrofické zprávy, spíchli jsme jednoduchou appku, která čte RSS idnes.cz, kanál "zprávy" (pro otrlé je pak kanál "ona", modří už vědí...) a přečte se vždy jen nadpis článku. Kdyby se četl celý článek, trvalo by to 100 let a lidi by to obtěžovalo, ale tím, že se přečte jen nadpis, tak to nechává spoustu prostoru pro představivost, co že se to vlastně stalo. Můj favorit za poslední dobu je třeba "Zadržený rváč bušil na služebně hlavou do zdi, pak kousl policistu do nohy".

Přidali jsme tento [skvělý program][4] na našem build serveru do crontabu, připojili k němu repráky a jako výsledek je, že si můžeme v oddělení vývoje užít 3x denně čerstvé zprávy o tom kdo zemřel, kdo koho okradl a kdo neumyl nádobí.

No a v ten moment řiká Jirka: "Hele, co tam dát takovou tu znělku co hrajou Český dráhy v drážním rozhlase"?

![challenge accepted](/assets/drazni-rozhlas/challenge-accepted-kid.jpg)

Trvalo to asi 5 minut a posunulo to celej rozhlas na úplně novej level!

Protože to slavilo v kanclu úspěch, Víťa napsal [jednoduchej bash skript][5], který nám náhodně vybere kam máme jít na oběd, já jsem upravil předčítač tak, aby pokud se spustí bez argumentů, tak četl RSS a pokud se mu předá jako argument text, tak ho to přečte. No a pak se to samozřejmě přidalo na do crontabu na čas kdy chodíme na oběd.

## Co s tim dál?
Další možné nápady na vylepšení jsou například takové, že by to mohlo běžet jako server a propojit to se Slackem. Takže by se vyrobil ve Slacku vlastní příkaz, např. `/rozhlas "bla"`, ten by to poslal přes HTTP volání do zmíněného serveru a bylo by to. Samozřejmě by bylo ale třeba udržet za pomoci biče, aby se to nezneužívalo.

No a odtud je už jen krůček například na hook při deployi.  

## Demo

<iframe width="560" height="315" src="https://www.youtube.com/embed/K-8ofgzcuOY" frameborder="0" allowfullscreen></iframe>

## Shrnutí
Takováhle kravina vznikla asi za 2 hodiny a pozvedla hladinu humoru v kanceláři. Ne kvůli zprávám co to předčítá, ale proto, že je to prostě zábavný.



[1]:https://apps.apple.com/cz/app/heureka-app/id436106975

[2]:https://play.google.com/store/apps/details?id=cz.ursimon.heureka.client.android&hl=cs

[3]:https://developer.apple.com/documentation/appkit/nsspeechsynthesizer
[4]:https://github.com/filipmelik/news-speaker
[5]:https://github.com/filipmelik/lunch-suggestor
[6]:https://osxdaily.com/2011/07/25/how-to-add-new-voices-to-mac-os-x/
