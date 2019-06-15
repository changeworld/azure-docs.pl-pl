---
title: Analizowanie fraz - interfejsu API analizy językowej
titlesuffix: Azure Cognitive Services
description: Więcej informacji na temat sposobu grupowa, znany także jako "frazę analizy struktury" identyfikuje fraz w tekście.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 7611f5f16111b5d8b0d2d293750f658125e50837
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60535450"
---
# <a name="constituency-parsing"></a>Analiza grupowa

> [!IMPORTANT]
> Wersja zapoznawcza analizy językowej została wycofana 9 sierpnia 2018 r. W celu przetwarzania i analizy tekstu zalecamy korzystanie z [modułów analizy tekstu w usłudze Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics).

Celem grupowa (znany także jako "Struktura fazy analizowania") jest określenie wyrażenia w tekście.
Może to być przydatne, gdy trwa wyodrębnianie informacji z pliku tekstowego.
Klienci powinni się znaleźć nazwy funkcji lub frazy kluczowe z dużych treści pewnego tekstu i zapoznaj się z modyfikatorów i działań związanych z każdą frazę takie.

## <a name="phrases"></a>Zwroty

Aby przejrzał *frazy* jest więcej niż tylko sekwencji wyrazów.
Być frazy, grupy wyrazów ma łączą się do odtwarzania określoną rolę w zdaniu.
Tej grupy słów można przenieść razem lub zastąpić jako całości, a zdanie powinna pozostać fluent i gramatyki.

Należy wziąć pod uwagę zdania

> Chcę, aby znaleźć nowe samochodów hybrydowych przy użyciu połączenia Bluetooth.

To pole następujące zdanie zawiera frazę rzeczownik: "nowy hybrydowy samochód z funkcją Bluetooth".
Jak gdy wiemy, że jest to wyrażenie?
Firma Microsoft (nieco poetically) edycji zdania, przenosząc tego cała fraza do przodu:

> Nowy samochód hybrydowe z funkcją Bluetooth, czy ma zostać odnaleziona.

Lub firma Microsoft może zastąpić tej frazy frazy podobnych funkcji i znaczenia, na przykład "ozdobnych nowego samochodu":

> Chcę znaleźć ozdobnych nowego samochodu.

Jeśli zamiast tego będziemy pobrane inny podzbiór słów, zadania te zastąpienia doprowadziłoby do zdań dziwne lub nie można go odczytać.
Weź pod uwagę na to, co się stanie, gdy przejdziemy "znaleźć nową" do przodu:

> Znajdź nowe, chcesz samochodów hybrydowych przy użyciu połączenia Bluetooth.

Wyniki jest bardzo trudne do odczytania i zrozumienia.

Celem Analizator jest aby znaleźć wszystkie takie fraz.
Co ciekawe w języku naturalnym, zwrotów zwykle być zagnieżdżone wewnątrz siebie nawzajem.
Fizyczne reprezentacja tych wyrażeń jest drzewa, takie jak następujące:

![drzewo](./Images/tree.png)

W tego drzewa gałęzie oznaczone jako "Nazwane" są rzeczownik fraz.
Istnieje kilka takich fraz: *Czy mogę*, *nowy samochód hybrydowego*, *Bluetooth*, i *nowych modeli samochodów hybrydowych przy użyciu połączenia Bluetooth*.

## <a name="phrase-types"></a>Typy frazy

| Label | Opis | Przykład |
|-------|-------------|---------|
|ADJP   | Fraza przymiotników | "Dlatego prosta" |
|ADVP   | Parametr frazy | "clear za pośrednictwem" |
|CONJP  | Fraza połączeniu | "oraz" |
|FRAG   | Fragment, używany dla danych wejściowych niekompletne lub fragmentary | "Zdecydowanie zaleca się..." |
|INTJ   | interjection | "Hura" |
|LST    | Znacznik listy, w tym znaki interpunkcyjne | "#4)" |
|NAC    | Nie składowych, używany do wskazania zakresu frazy bez składnika |  "i uzyskać dobrą ofertę" w "możesz wszystko i towary postępowania" |
|NP | Rzeczownik frazy | "pancake tasty ziemniaczanej" |
|NX | Używany w ramach niektórych złożonych serwera NPs do oznaczania nagłówek| |
|PP | Fraza przyimkowych| "w puli" |
|PRN    | Nawiasach| "(tzw.)" |
|PRT    | Cząstka| "limit" w "zgranych out" |
|QP | Ilość frazy (czyli złożone miary/kwota) w obrębie frazy rzeczownik| "around $75" |
|RRC    | Zmniejszenie względem klauzuli.| "nadal nieokreślony" w "wszechstronne nadal nierozpoznanych" |
|S  | Zdania lub klauzuli. | "Jest zdania".
|SBAR   | Klauzula podrzędny, często wynikające z subordinating połączeniu | "jako I lewej" w "I rozejrzeliśmy jak mogę left."|
|SBARQ  | Zapytania bezpośrednie wprowadzone przez pytania "Wh" słowo lub — wyrażenie | "Jaki był punkt?" |
|SINV   | Odwrócony zdania deklaratywne | "W żadnym momencie cynk one." (Zwróć uwagę, jak normalne podmiotu "one" został przeniesiony do po czasownik "były") |
|SQ | Odwrócony tak/nie pytanie lub klauzuli main pytanie pytania "Wh" | "Znalazły się samochodu?" |
|UCP    | W odróżnieniu od skoordynowanego frazy| "małe, a z usterkami" (należy pamiętać, jak conjoined przymiotnikiem i frazy preposition z "i")|
|VP | Fraza zlecenia | "ran w czasie" |
|WHADJP | Fraza przymiotnik pytania "Wh" | "jak przykrość" |
|WHADVP | Parametr pytania "Wh" frazy| "po" |
|WHNP   | Pytania "Wh" rzeczownik frazy| "które ziemniak", "ile od początku"|
|WHPP   | Fraza prepositional pytania "Wh"| "w od kraju"|
|X  | Nieznany, niepewne lub unbracketable.| pierwszy "" w "... od początku" |


## <a name="specification"></a>Specyfikacja

Drzewa w tym miejscu użyć wyrażeń S od [Treebank partnerów](https://catalog.ldc.upenn.edu/LDC99T42).
