---
title: 'Wybieranie kolumn w zestawie danych: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak Użyj Wybieranie kolumn w module zestawu danych w usłudze Azure Machine Learning, aby wybrać podzbiór kolumn do użycia w niższego rzędu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: e7407f62bd3401411d56076b298bd8cd134ece62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028098"
---
# <a name="select-columns-in-dataset-module"></a>Wybierz kolumny w module zestawu danych

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Użycie tego modułu, aby wybrać podzbiór kolumn do użycia w niższego rzędu. Moduł fizycznie kolumny nie są usuwane z zestawu danych źródłowych; w zamian tworzy podzestawie kolumn, podobnie jak w bazie danych *widoku* lub *projekcji*.

Ten moduł jest przydatne, gdy chcesz ograniczyć kolumn dostępnych dla podrzędnego operacji lub jeśli chcesz zmniejszyć rozmiar zestawu przez usunięcie niepotrzebnych kolumn.

Kolumn w zestawie danych są dane wyjściowe w tej samej kolejności, jak oryginalne dane, nawet jeśli zostały określone w innej kolejności.

## <a name="how-to-use"></a>Jak stosować

Ten moduł nie ma parametrów. Selektor kolumn aby wybrać kolumny do dołączania lub wykluczania.

### <a name="choose-columns-by-name"></a>Wybierz kolumny, według nazwy

Istnieje wiele opcji w module dotyczące wybierania kolumn według nazwy: 

+ Filtrowanie i wyszukiwania

    Kliknij przycisk **według nazwy** opcji.

    Jeśli nawiązano połączenie zestawu danych, który jest już wypełnione, powinna zostać wyświetlona lista dostępnych kolumn. Pojawia się żadnych kolumn, może być konieczne uruchomienie nadrzędnego modułów, aby wyświetlić listę kolumn.

    Aby filtrować listę, wpisz w polu wyszukiwania. Na przykład w przypadku wpisania litery `w` w polu wyszukiwania, lista jest filtrowana, aby wyświetlić nazwy kolumn, które zawierają litery `w`.

    Wybierz kolumny, a następnie kliknij przycisk strzałki w prawo, aby przenieść wybrane kolumny na liście w okienku po prawej stronie.

    + Aby zaznaczyć ciągłego zakresu nazw kolumn, naciśnij **Shift + kliknięcie**.
    + Aby dodać poszczególne kolumny do zaznaczenia, naciśnij klawisze **Ctrl + kliknięcie**.

    Kliknij przycisk znacznika wyboru, aby zapisać i zamknąć.

+ Użyj nazwy w połączeniu z innymi regułami

    Kliknij przycisk **przy użyciu reguł** opcji.
    
    Wybierz regułę, takich jak pokazywanie kolumn na określony typ danych.

    Następnie kliknij poszczególne kolumny typu przy użyciu nazwy, aby dodać je do listy wyboru.

+ Wpisz lub Wklej listę rozdzielonych przecinkami nazw kolumn

    Jeśli zestaw danych jest szeroka, może być łatwiejszy w obsłudze indeksów lub indywidualnie wygenerowany listy nazwy zamiast wybrania kolumny. Zakładając, że lista została przygotowana z wyprzedzeniem:

    1. Kliknij przycisk **przy użyciu reguł** opcji. 
    2. Wybierz **żadnych kolumn**, wybierz opcję **Include**, a następnie kliknij wewnątrz pola tekstowego z czerwonym wykrzyknikiem. 
    3. Wklej lub wpisz listę rozdzielonych przecinkami nazw kolumn wcześniej zweryfikowane. Nie można zapisać modułu Jeśli dowolna kolumna ma nieprawidłową nazwę, więc można wcześniej Sprawdź nazwy.
    
    Ta metoda umożliwia również określenia listy kolumn za pomocą wartości indeksu. 

### <a name="choose-by-type"></a>Wybierz według typu

Jeśli używasz **przy użyciu reguł** opcji z opcjami kolumny można stosować wiele warunków. Na przykład może być konieczne uzyskać tylko funkcja kolumny zawierającą dane typu liczbowego.

**Rozpocząć z** opcja określa punkt początkowy i jest istotne dla zrozumienia wyniki. 

+ Jeśli wybierzesz **wszystkie kolumny** opcji wszystkie kolumny są dodawane do listy. Następnie należy użyć **wykluczyć** opcję *Usuń* kolumn, które spełniają określone warunki. 

    Na przykład może rozpoczynać się wszystkie kolumny, a następnie usuń kolumny, według nazwy lub typu.

+ Jeśli wybierzesz **kolumny nr** opcji pusty rozpoczyna się lista kolumn. Następnie możesz określić warunki, które *Dodaj* kolumn do listy. 

    Jeśli zastosujesz wiele reguł, każdy warunek jest **dodatku**. Załóżmy na przykład uruchomić nie ma kolumn, a następnie dodaj zasadę, aby pobrać wszystkie kolumny liczbowe. W zestawie danych cenę samochodu, która powoduje 16 kolumn. Następnie kliknij przycisk **+** Zaloguj się dodać nowy warunek, a następnie wybierz **wszystkich funkcji**. Wynikowy zestaw danych zawiera wszystkie kolumny liczbowe, a także wszystkie kolumny funkcji, w tym niektóre kolumny funkcję ciągu.

### <a name="choose-by-column-index"></a>Wybierz, indeks kolumny

Indeks kolumny kolejność kolumn w ramach oryginalnego zestawu danych.

+ Kolumny są numerowane kolejno rozpoczyna się od 1.  
+ Aby uzyskać kilka kolumn, należy użyć łącznika. 
+ Nieograniczony specyfikacje `1-` lub `-3` nie są dozwolone.
+ Zduplikowany indeks wartości (lub nazwy kolumn) są niedozwolone i może spowodować wystąpienie błędu.

Na przykład przy założeniu, że zestaw danych zawiera co najmniej osiem kolumn, możesz można wkleić w dowolnej z poniższych przykładów, aby zwrócić wiele kolumn nieciągłych: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

ostatnim przykładzie nie powoduje błąd; jednak zwraca pojedyncze wystąpienie kolumny `4`.



### <a name="change-order-of-columns"></a>Zmienianie kolejności kolumn

Opcja **zezwala na istnienie duplikatów i zachować kolejność kolumn w zaznaczeniu** rozpoczyna się od pustej listy, a następnie dodaje kolumn, które określają według nazwy lub indeksu. W przeciwieństwie do innych opcji, które zawsze zwracać kolumn w ich "kolejność naturalna", ta opcja danych wyjściowych o nazwie kolumny w kolejności lub ich listę. 

Można na przykład w zestawie danych z kolumnami Col1, Col2, Col3 i Kol4 odwrócić kolejność kolumn i Opuść kolumna 2, określając jedną z następujących list:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 