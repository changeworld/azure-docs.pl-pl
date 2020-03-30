---
title: 'Tekst przetwarzania wstępnego: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wyczyścić i uprościć tekst za pomocą modułu Tekst przetwarzania wstępnego w usłudze Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: 6e4d4c8f798418e090caeba091dec33c71f0458f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477497"
---
# <a name="preprocess-text"></a>Wstępne przetwarzanie tekstu

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Moduł **Tekst przetwarzania wstępnego** służy do czyszczenia i upraszczania tekstu. Obsługuje te typowe operacje przetwarzania tekstu:

* Usuwanie słów stop-
* Używanie wyrażeń regularnych do wyszukiwania i zastępowania określonych ciągów docelowych
* Lemmatization, która konwertuje wiele powiązanych słów na jedną formę kanoniczną
* Normalizacja przypadków
* Usuwanie niektórych klas znaków, takich jak liczby, znaki specjalne i sekwencje powtarzających się znaków, takich jak "aaaa"
* Identyfikacja i usuwanie wiadomości e-mail i adresów URL

Moduł **Tekst przetwarzania wstępnego** obsługuje obecnie tylko język angielski.

## <a name="configure-text-preprocessing"></a>Konfigurowanie przetwarzania wstępnego tekstu  

1.  Dodaj moduł **tekst przetwarzania wstępnego** do potoku w usłudze Azure Machine Learning. Ten moduł można znaleźć w obszarze **Analiza tekstu**.

1. Połącz zestaw danych zawierający co najmniej jedną kolumnę zawierającą tekst.

1. Wybierz język z listy rozwijanej **Język.**

1. **Kolumna tekst do czyszczenia**: Zaznacz kolumnę, którą chcesz wstępnie przetworzyć.

1. **Usuń słowa stop:** Zaznacz tę opcję, jeśli chcesz zastosować wstępnie zdefiniowaną listę stopword do kolumny tekstowej. 

    Listy stopword są zależne od języka i konfigurowalne.

1. **Lemmatization**: Wybierz tę opcję, jeśli chcesz, aby wyrazy były reprezentowane w formie kanonicznej. Ta opcja jest przydatna do zmniejszenia liczby unikatowych wystąpień podobnych tokenów tekstowych.

    Proces lemmatyzacji jest w dużym stopniu zależny od języka..

1. **Wykrywanie zdań:** Wybierz tę opcję, jeśli chcesz, aby moduł wstawił znacznik granicy zdania podczas przeprowadzania analizy.

    Ten moduł używa serii trzech `|||` znaków potoku do reprezentowania terminatora zdania.

1. Wykonywanie opcjonalnych operacji znajdowania i zamieniania przy użyciu wyrażeń regularnych.

    * **Niestandardowe wyrażenie regularne:** zdefiniuj poszukiwany tekst.
    * **Niestandardowy ciąg zastępczy:** Zdefiniuj pojedynczą wartość zastępczą.

1. **Normalizuj wielką literę na małe :** Wybierz tę opcję, jeśli chcesz przekonwertować wielkie litery ASCII na ich małe litery.

    Jeśli znaki nie są znormalizowane, to samo słowo pisane i pisane prawymi literami są traktowane jako dwa różne słowa.

1. Z przetworzonego tekstu wyjściowego można również usunąć następujące typy znaków lub sekwencji znaków:

    * **Usuń liczby**: Wybierz tę opcję, aby usunąć wszystkie znaki liczbowe dla określonego języka. Numery identyfikacyjne są zależne od domeny i są zależne od języka. Jeśli znaki liczbowe są integralną częścią znanego wyrazu, liczba może nie zostać usunięta.
    
    * **Usuń znaki specjalne:** Użyj tej opcji, aby usunąć wszelkie niealnumericzne znaki specjalne.
    
    * **Usuń zduplikowane znaki**: Wybierz tę opcję, aby usunąć dodatkowe znaki w sekwencjach powtarzanych przez więcej niż dwa razy. Na przykład sekwencja taka jak "aaaaa" zostanie zredukowana do "aa".
    
    * **Usuń adresy e-mail**: Wybierz tę `<string>@<string>`opcję, aby usunąć dowolną sekwencję formatu .  
    * **Usuń adresy URL**: Wybierz tę opcję, aby usunąć `http`dowolną `https` `ftp`sekwencję zawierającą następujące prefiksy adresu URL: , ,`www`
    
1. **Rozwiń skurcze czasowników**: Ta opcja ma zastosowanie tylko do języków, które używają skurczów czasowników; obecnie tylko w języku angielskim. 

    Na przykład, wybierając tę opcję, można zastąpić wyrażenie *"nie pozostanie tam"* *na "nie pozostanie tam"*.

1. **Znormalizuj ukośniki do ukośników:** Wybierz tę opcję, aby zamapować wszystkie wystąpienia `\\` na `/`.

1. **Podziel tokeny na znaki specjalne:** Wybierz tę opcję, jeśli `&` `-`chcesz podzielić wyrazy na znaki, takie jak , i tak dalej. Ta opcja może również zmniejszyć liczbę znaków specjalnych, gdy powtarza się więcej niż dwa razy. 

    Na przykład ciąg `MS---WORD` zostanie podzielony na trzy `MS`tokeny, , `-`i `WORD`.

1. Prześlij potok.

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 