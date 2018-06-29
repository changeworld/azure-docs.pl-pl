---
title: Analizowanie w interfejsie API językową analiza wyborczy | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu wyborczy analizy, znanej także jako "frazę analizy struktury" identyfikuje fraz w tekście.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 03/21/2016
ms.author: lesun
ms.openlocfilehash: bff5e587621e1278c260d555aec280a0f4c7c8a1
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082176"
---
# <a name="constituency-parsing"></a>Podczas analizowania wyborczy

Celem wyborczy analizy (znanej także jako "Struktura fazy podczas analizowania") jest ustalenie fraz w tekście.
Może to być przydatne podczas wyodrębniania informacji z pliku tekstowego.
Klienci mogą też chcieć można znaleźć nazwy funkcji ani fraz klucza z dużą treści tekstu i Modyfikatory i akcje otaczającego każdego takie wyrażenie.

## <a name="phrases"></a>Wyrażenia

Aby linguist *frazy* jest więcej niż tylko sekwencję słów.
Jako wyrażenie grupy wyrazów ma grupuje odtwarzania konkretnej roli w zdaniu.
Tej grupy słów mogą być przeniesione razem lub zastąpione jako całość i zdanie powinna pozostać płynne i gramatyczne.

Należy wziąć pod uwagę zdanie

> Chcę znaleźć nowy samochód hybrydowe z funkcji Bluetooth.

To zdanie zawiera wyrażenie rzeczownik: "nowy hybrydowego samochód z Bluetooth".
Jak było wiadomo, że jest to wyrażenie?
Firma Microsoft (nieco poetically) edycji zdanie, przenosząc tej frazy całego do przodu:

> Nowy samochód hybrydowe z Bluetooth wyszukiwanie.

Lub firma Microsoft może zastąpić tej frazy frazę podobnych funkcji i znaczenie, takich jak "ozdobne nowego samochodu":

> Chcę znaleźć ozdobne nowego samochodu.

Jeśli zamiast tego wybraniu różnych podzbiór wyrazy, zadania te zastąpienia doprowadziłoby do zdań dziwne lub nie można go odczytać.
Należy rozważyć, co się stanie, możemy przenoszenia "odnaleźć nowy" do przodu:

> Znajdź nowy interesujące samochodów hybrydowe z funkcji Bluetooth.

Wyniki jest bardzo trudne przeczytane i zrozumiane.

Celem Analizator jest znajdowanie wszystkich tych zdań.
Interesujące jest, że w języku naturalnym fraz zwykle można zagnieździć w sobie nawzajem.
Fizyczne reprezentacja tych wyrażeń jest drzewo, takie jak następujące:

![Drzewo](./Images/tree.png)

W tym drzewie gałęzie oznaczone jako "Potoki" są fraz rzeczownik.
Istnieje kilka takich wyrażeń: *I*, *nowy samochód hybrydowego*, *Bluetooth*, i *nowy samochód hybrydowe z Bluetooth*.

## <a name="phrase-types"></a>Typy frazy

| Etykieta | Opis | Przykład |
|-------|-------------|---------|
|ADJP   | Wyrażenie przymiotników | "Aby niegrzeczny" |
|ADVP   | Parametr frazy | "Wyczyść za pośrednictwem" |
|CONJP  | Wyrażenie połączeniu | "jak i" |
|FRAG   | Fragment używane dla danych wejściowych niekompletne lub fragmentary | "Zdecydowanie zalecane..." |
|INTJ   | interjection | "Doskonale" |
|LST    | Znacznik listy, w tym znaki interpunkcyjne | "#4)" |
|KCA    | Nie A składników, służy do określania zakresu frazę z systemem innym niż składnika |  "i dużą" w "możesz zadań i postępowania w przypadku dobrej" |
|POTOKI | Rzeczownik frazy | "pancake tasty ziemniaka" |
|NX | Używane w niektórych złożonych serwera NPs do oznaczania nagłówek| |
|PZ | Wyrażenie przyimkowych| "w puli" |
|PRN    | Ta| "(tzw.)" |
|PRT    | Cząstka| "out" w "zgranych out" |
|QP | Ilość frazy (tj., złożonych miary/kwota) w obrębie frazę rzeczownik| "wokół $75" |
|KOD PRZYCZYNY REJESTRACJI    | Zmniejszenie klauzuli względny.| "nadal nierozpoznane" w "wiele problemów nierozwiązanych nadal" |
|S  | Zdania lub klauzuli. | "To zdanie."
|SBAR   | Klauzula podrzędny, często wprowadzane przez subordinating połączeniu | "jako po lewej" w "Oglądany jak I w lewo."|
|SBARQ  | Zapytania bezpośredniego wprowadzone przez co słowo lub - wyrażenie | "Co były punkt?". |
|SPISU OPROGRAMOWANIA   | Odwrócony zdanie deklaratywne | "Nie zostały one pamiętać." (Zwróć uwagę, jak normalne podmiotu "one" został przeniesiony do po zlecenie "zostały") |
|SQ | Odwrócony tak/nie pytanie lub klauzuli pytanie ki main | "Znalazły się samochód?" |
|UCP    | W odróżnieniu od skoordynowanego frazy| "mała i z usterkami" (należy zwrócić uwagę, jak conjoined przymiotnik i frazy preposition z "i")|
|VP | Wyrażenie zlecenie | "Wystąpił drzewami" |
|WHADJP | Ki przymiotnik frazy | "jak niepewne" |
|WHADVP | Parametr ki frazy| "kiedy" |
|WHNP   | Wyrażenie ki rzeczownik.| "który ziemniaczanej", "ile zup"|
|WHPP   | Ki prepositional frazy| "w kraju, w którym"|
|X  | Nieznany, niepewne lub unbracketable.| pierwszy "" w "... zup" |


## <a name="specification"></a>Specyfikacja

Tutaj drzewa, użyj wyrażenia S z [Penn Treebank](https://catalog.ldc.upenn.edu/ldc99t42).
