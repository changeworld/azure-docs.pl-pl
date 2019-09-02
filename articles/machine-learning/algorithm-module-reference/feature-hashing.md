---
title: 'Mieszanie funkcji: Dokumentacja modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak cechowanie dane tekstowe przy użyciu modułu tworzenia skrótów funkcji w usłudze Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 09/01/2019
ms.openlocfilehash: 78d01cf071faed312773ebf12c75e7e6e5596e71
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210847"
---
# <a name="feature-hashing"></a>Mieszanie funkcji

W tym artykule opisano moduł Visual Interface (wersja zapoznawcza) dla usługi Azure Machine Learning.

Użyj modułu **wyznaczania wartości skrótu funkcji** , aby przekształcić strumień tekstu w języku angielskim do zestawu funkcji całkowitych. Następnie można przekazać tę funkcję skrótu do algorytmu uczenia maszynowego, aby szkolić model analizy tekstu.

Funkcje wyznaczania wartości skrótu funkcji dostępne w tym module bazują na strukturze nimbusml. Aby uzyskać więcej informacji, zobacz [Klasa NgramHash](https://docs.microsoft.com/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest).

## <a name="what-is-feature-hashing"></a>Co to jest mieszanie funkcji

Mieszanie funkcji działa przez konwersję unikatowych tokenów na liczby całkowite. Działa on na dokładnych ciągach dostarczanych jako dane wejściowe i nie wykonuje żadnej analizy językowej ani przetwarzania wstępnego. 

Na przykład należy wykonać zestaw prostych zdań takich jak te, po których następuje wynik tonacji. Załóżmy, że chcesz użyć tego tekstu do skompilowania modelu.

|USERTEXT|TONACJI|
|--------------|---------------|
|Uwielbiane tę książkę|3|
|Hated tę książkę|1|
|Ta książka była świetna|3|
|Lubię książki|2|

Wewnętrznie moduł **mieszania funkcji** tworzy słownik n-gramy. Na przykład lista wielogramów dla tego zestawu danych będzie wyglądać następująco:

|TERMIN (w gramach)|JAKĄ|
|------------|---------------|
|Ta książka|3|
|Uwielbiane|1|
|Hated|1|
|Kocham|1|

Można kontrolować rozmiar n-gramów przy użyciu właściwości **n-Grams** . W przypadku wybrania opcji rozgramy są również obliczane unigrams. Słownik zawiera również pojedyncze warunki, takie jak następujące:

|Termin (unigrams)|JAKĄ|
|------------|---------------|
|typu|3|
|I|3|
|stad|1|
|było|1|

Po skompilowaniu słownika moduł tworzenia **skrótów funkcji** konwertuje warunki słownika na wartości skrótu. Następnie oblicza, czy funkcja została użyta w każdym przypadku. Dla każdego wiersza danych tekstowych moduł wyprowadza zestaw kolumn, jedną kolumnę dla każdej funkcji skrótu.

Na przykład po utworzeniu skrótu kolumny funkcji mogą wyglądać następująco:

|Ocena|Funkcja mieszania 1|Funkcja mieszania 2|Funkcja mieszania 3|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Jeśli wartość w kolumnie to 0, wiersz nie zawiera funkcji skrótu.
* Jeśli wartość wynosi 1, wiersz zawiera funkcję.

Funkcja tworzenia skrótów umożliwia reprezentowanie dokumentów tekstowych o zmiennej długości jako wektorów funkcji liczbowych o równej długości, aby osiągnąć redukcję liczby wymiarów. Jeśli podjęto próbę użycia kolumny tekstu do szkolenia w takiej postaci, będzie ona traktowana jako kolumna funkcji kategorii z wieloma unikatowymi wartościami.

Wyniki liczbowe umożliwiają również korzystanie z typowych metod uczenia maszynowego, takich jak Klasyfikacja, klastrowanie i pobieranie informacji. Ponieważ operacje wyszukiwania mogą używać skrótów liczb całkowitych zamiast porównywania ciągów, pobieranie wag funkcji jest również znacznie szybsze.

## <a name="configure-feature-hashing"></a>Konfiguruj mieszanie funkcji

1.  Dodaj moduł **skrótu funkcji** do eksperymentu w interfejsie wizualnym.

1. Połącz zestaw danych zawierający tekst, który chcesz przeanalizować.

    > [!TIP]
    > Ponieważ funkcja tworzenia skrótów nie wykonuje operacji leksykalnych, takich jak rdzenie lub obcinanie, można czasami uzyskać lepsze wyniki, wykonując wstępne przetwarzanie tekstu przed zastosowaniem skrótu funkcji. 

1. Ustaw **kolumny docelowe** na kolumny tekstowe, które mają być konwertowane na funkcje zmieszane. 

    * Kolumny muszą być typu String.
    
    * Wybranie wielu kolumn tekstowych może mieć znaczący wpływ na wymiarowanie funkcji. Na przykład liczba kolumn dla wartości 10-bitowej jest równa 1024 dla pojedynczej kolumny do 2048 dla dwóch kolumn.

1. Użyj **Bitsize mieszania** , aby określić liczbę bitów do użycia podczas tworzenia tabeli skrótów.
    
    Domyślny rozmiar bitowy to 10. W przypadku wielu problemów ta wartość jest odpowiednia. Aby uniknąć kolizji w zależności od rozmiaru tekstu szkoleniowego n-gramów, można potrzebować więcej miejsca.
    
1. W przypadku **N-gramów**wpisz liczbę określającą maksymalną długość N-gramów, która ma zostać dodana do słownika szkoleniowego. N-gram jest sekwencją *n* wyrazów, traktowaną jako unikatowa jednostka.

    Na przykład, jeśli wprowadzisz 3, unigrams, i trigrams zostanie utworzony.

1. Uruchom eksperyment.

## <a name="results"></a>Wyniki

Po zakończeniu przetwarzania moduł wyprowadza przekształcony zestaw danych, w którym oryginalna kolumna tekstowa została przekonwertowana na wiele kolumn, z których każdy reprezentuje funkcję w tekście. W zależności od tego, jaki jest istotny słownik, zestaw danych może być duży:

|Nazwa kolumny 1|Typ kolumny 2|
|-------------------|-------------------|
|USERTEXT|Oryginalna kolumna danych|
|TONACJI|Oryginalna kolumna danych|
|USERTEXT — funkcja 1|Kolumna funkcji z mieszaniem|
|USERTEXT — funkcja 2|Kolumna funkcji z mieszaniem|
|USERTEXT — funkcja skrótu n|Kolumna funkcji z mieszaniem|
|USERTEXT — funkcja wyznaczania wartości skrótu 1024|Kolumna funkcji z mieszaniem|

Po utworzeniu przekształconego zestawu danych można go użyć jako danych wejściowych modułu uczenie modelu.
 
### <a name="best-practices"></a>Najlepsze praktyki

Poniższe najlepsze rozwiązania mogą pomóc w optymalnym wykorzystaniu modułu tworzenia **skrótów funkcji** :

* Dodaj **wstępnie przetworzony moduł tekstowy** przed użyciem **mieszania funkcji** do wstępnego przetworzenia tekstu wejściowego. 

* Aby usunąć kolumny tekstowe z zestawu danych wyjściowych, Dodaj moduł **SELECT Columns (Wybieranie kolumn** ). Po wygenerowaniu funkcji tworzenia skrótów nie są potrzebne kolumny tekstowe.
    
* Należy rozważyć użycie tych opcji wstępnego przetwarzania tekstu, aby uprościć wyniki i poprawić dokładność:

    * Dzielenie wyrazów
    * Zatrzymaj usuwanie wyrazów
    * Normalizacja przypadku
    * Usuwanie interpunkcji i znaków specjalnych
    * Uszkodzeniem  

Optymalny zestaw metod przetwarzania wstępnego, które mają być stosowane w poszczególnych rozwiązaniach, zależy od domeny, słownika i potrzeby biznesowe. Eksperymentuj z danymi, aby zobaczyć, które metody przetwarzania tekstu są najbardziej skuteczne.

## <a name="next-steps"></a>Następne kroki
            
Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 
