---
title: Odwołanie do modułu mieszania funkcji
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu hashing funkcji w usłudze Azure Machine Learning do featurize danych tekstowych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 7178417a5c20afe5b1ed02bc526ec174704962df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456254"
---
# <a name="feature-hashing-module-reference"></a>Odwołanie do modułu mieszania funkcji

W tym artykule opisano moduł uwzględniony w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Użyj modułu Hashing funkcji, aby przekształcić strumień tekstu w języku angielskim w zestaw funkcji całkowitych. Następnie można przekazać ten zestaw funkcji mieszania do algorytmu uczenia maszynowego, aby wyszkolić model analizy tekstu.

Funkcja mieszania funkcji dostępna w tym module opiera się na ramie nimbusml. Aby uzyskać więcej informacji, zobacz [NgramHash klasy](https://docs.microsoft.com/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest).

## <a name="what-is-feature-hashing"></a>Co to jest haszowanie funkcji?

Funkcja mieszania działa konwertując unikatowe tokeny na liczby całkowite. Działa na dokładne ciągi, które można podać jako dane wejściowe i nie wykonuje żadnej analizy językowej lub przetwarzania wstępnego. 

Na przykład weź zestaw prostych zdań, takich jak te, a następnie wynik tonacji. Załóżmy, że chcesz użyć tego tekstu do utworzenia modelu.

|Tekst użytkownika|Opinia|
|--------------|---------------|
|Kochałem tę książkę|3|
|Nienawidziłem tej książki|1|
|Ta książka była świetna|3|
|Kocham książki|2|

Wewnętrznie moduł hashing funkcji tworzy słownik n-gramów. Na przykład lista bigramów dla tego zestawu danych będzie wyglądać mniej więcej tak:

|Termin (bigrams)|Częstotliwość|
|------------|---------------|
|Ta książka|3|
|Kochałem|1|
|Nienawidziłem|1|
|Kocham|1|

Rozmiar n-gramów można kontrolować za pomocą właściwości **N-grams.** Jeśli wybierzesz bigrams, unigramy są również obliczane. Słownik będzie również zawierać pojedyncze terminy, takie jak te:

|Termin (unigramy)|Częstotliwość|
|------------|---------------|
|książka|3|
|I|3|
|książki|1|
|Został|1|

Po zbudowaniu słownika moduł Hashing funkcji konwertuje terminy słownika na wartości skrótu. Następnie oblicza, czy funkcja została użyta w każdym przypadku. Dla każdego wiersza danych tekstowych moduł wyprowadza zestaw kolumn, jedną kolumnę dla każdej operacji mieszania.

Na przykład po mieszaniu kolumny funkcji mogą wyglądać mniej więcej tak:

|Klasyfikacja|Funkcja mieszania 1|Funkcja mieszania 2|Funkcja mieszania 3|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Jeśli wartość w kolumnie wynosi 0, wiersz nie zawiera funkcji mieszania.
* Jeśli wartość wynosi 1, wiersz zawiera operację.

Mieszanie operacji umożliwia reprezentowanie dokumentów tekstowych o zmiennej długości jako wektorów operacji liczbowych o równej długości w celu zmniejszenia wymiarowości. Jeśli próbowano użyć kolumny tekstowej do szkolenia, jak jest, będzie traktowany jako kolumna funkcji kategorii z wielu różnych wartości.

Dane liczbowe umożliwiają również korzystanie ze typowych metod uczenia maszynowego, w tym klasyfikacji, klastrowania i pobierania informacji. Ponieważ operacje odnośników można używać skrótów liczb całkowitych, a nie porównań ciągów, uzyskanie wagi operacji jest również znacznie szybsze.

## <a name="configure-the-feature-hashing-module"></a>Konfigurowanie modułu Hashing funkcji

1.  Dodaj moduł hashing funkcji do potoku w projektancie.

1. Połącz zestaw danych zawierający tekst, który chcesz analizować.

    > [!TIP]
    > Ponieważ mieszanie operacji nie wykonuje operacji leksykalne, takich jak wynikające lub obcinania, czasami można uzyskać lepsze wyniki, wstępnie przetwarzając tekst przed zastosowaniem mieszania operacji. 

1. Ustaw **kolumny docelowe** na kolumny tekstowe, które chcesz przekonwertować na obiekty skrótowe. Należy pamiętać, że:

    * Kolumny muszą być typem danych ciągu.
    
    * Wybranie wielu kolumn tekstowych może mieć znaczący wpływ na wymiarowość operacji. Na przykład liczba kolumn dla 10-bitowego skrótu idzie z 1024 dla pojedynczej kolumny do 2048 dla dwóch kolumn.

1. Użyj **opcji bitsize mieszania,** aby określić liczbę bitów, które mają być używane podczas tworzenia tabeli mieszania.
    
    Domyślny rozmiar bitu to 10. W przypadku wielu problemów wartość ta jest odpowiednia. Możesz potrzebować więcej miejsca, aby uniknąć kolizji, w zależności od rozmiaru słownictwa n-gramów w tekście szkolenia.
    
1. W przypadku **N-gramów**wprowadź liczbę definiuj maksymalną długość n-gramów, które mają być dodane do słownika treningowego. N-gram jest sekwencją *n* słów, traktowaną jako unikalna jednostka.

    Na przykład, jeśli wprowadzisz 3, zostaną utworzone unigramy, bigramy i trygramy.

1. Prześlij potok.

## <a name="results"></a>Wyniki

Po zakończeniu przetwarzania moduł wyprowadza przekształcony zestaw danych, w którym oryginalna kolumna tekstowa została przekonwertowana na wiele kolumn. Każda kolumna reprezentuje operację w tekście. W zależności od tego, jak istotny jest słownik, wynikowy zestaw danych może być duży:

|Nazwa kolumny 1|Kolumna typu 2|
|-------------------|-------------------|
|TEKST UŻYTKOWNIKA|Oryginalna kolumna danych|
|Sentyment|Oryginalna kolumna danych|
|USERTEXT - Funkcja mieszania 1|Kolumna operacji skrótu|
|USERTEXT - Funkcja mieszania 2|Kolumna operacji skrótu|
|USERTEXT - Funkcja mieszania n|Kolumna operacji skrótu|
|USERTEXT - Funkcja mieszania 1024|Kolumna operacji skrótu|

Po utworzeniu przekształconego zestawu danych można go użyć jako danych wejściowych do modułu Model pociągu.
 
## <a name="best-practices"></a>Najlepsze rozwiązania

Poniższe najlepsze rozwiązania mogą pomóc w pełni wykorzystać w pełni moduł hashing funkcji:

* Dodaj moduł tekstu przetwarzania wstępnego przed użyciem funkcji Hashing do wstępnego przetwarzania tekstu wejściowego. 

* Dodaj moduł Wybierz kolumny po module Hashing funkcji, aby usunąć kolumny tekstowe z wyjściowego zestawu danych. Kolumny tekstowe nie są potrzebne po wygenerowaniu funkcji mieszania.
    
* Rozważ użycie tych opcji wstępnego przetwarzania tekstu, aby uprościć wyniki i poprawić dokładność:

    * Podział wyrazów
    * Zatrzymywanie usuwania wyrazów
    * Normalizacja przypadków
    * Usuwanie znaków interpunkcyjnych i specjalnych
    * Wynikające  

Optymalny zestaw metod przetwarzania wstępnego do zastosowania w dowolnym rozwiązaniu zależy od domeny, słownictwa i potrzeb biznesowych. potoku z danymi, aby zobaczyć, które metody przetwarzania tekstu są najbardziej skuteczne.

## <a name="next-steps"></a>Następne kroki
            
Zobacz [zestaw modułów dostępnych w](module-reference.md) usłudze Azure Machine Learning 
