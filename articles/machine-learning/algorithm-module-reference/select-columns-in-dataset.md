---
title: 'Wybieranie kolumn w zestawie danych: Odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu Wybierz kolumny w module Zestaw danych w usłudze Azure Machine Learning, aby wybrać podzbiór kolumn do użycia w dalszych operacjach.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 714148815fa4ff543ecbf86ab21e8d51fe775543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153778"
---
# <a name="select-columns-in-dataset-module"></a>Wybieranie kolumn w module zestawu danych

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do wybierania podzbioru kolumn do użycia w operacjach podrzędnych. Moduł nie usuwa fizycznie kolumn z źródłowego zestawu danych; Zamiast tego tworzy podzbiór kolumn, podobnie jak *widok* bazy danych lub *projekcja*.

Ten moduł jest przydatny, gdy trzeba ograniczyć kolumny dostępne dla operacji podrzędnych lub jeśli chcesz zmniejszyć rozmiar zestawu danych, usuwając niepotrzebne kolumny.

Kolumny w zestawie danych są dane wyjściowe w tej samej kolejności, co w oryginalnych danych, nawet jeśli określisz je w innej kolejności.

## <a name="how-to-use"></a>Jak stosować

Ten moduł nie ma parametrów. Selektor kolumny służy do wybierania kolumn do uwzględnienia lub wykluczenia.

### <a name="choose-columns-by-name"></a>Wybieranie kolumn według nazwy

W module dostępnych jest wiele opcji wyboru kolumn według nazwy: 

+ Filtrowanie i wyszukiwanie

    Kliknij opcję **WEDŁUG NAZWY.**

    Jeśli masz połączony zestaw danych, który jest już wypełniony, powinna zostać wyświetlona lista dostępnych kolumn. Jeśli nie są wyświetlane żadne kolumny, może być konieczne uruchomienie modułów nadrzędnych w celu wyświetlenia listy kolumn.

    Aby filtrować listę, wpisz pole wyszukiwania. Na przykład po wpisaniu `w` litery w polu wyszukiwania lista zostanie odfiltrowana, `w`aby wyświetlić nazwy kolumn zawierające literę .

    Zaznacz kolumny i kliknij przycisk strzałki w prawo, aby przenieść zaznaczone kolumny do listy w okienku po prawej stronie.

    + Aby wybrać ciągły zakres nazw kolumn, naciśnij **klawisze Shift + Click**.
    + Aby dodać poszczególne kolumny do zaznaczenia, naciśnij **klawisze Ctrl + Click**.

    Kliknij przycisk znacznika wyboru, aby zapisać i zamknąć.

+ Używanie nazw w połączeniu z innymi regułami

    Kliknij opcję **Z REGUŁAMI.**
    
    Wybierz regułę, na przykład przedstawiając kolumny określonego typu danych.

    Następnie kliknij poszczególne kolumny tego typu według nazwy, aby dodać je do listy wyboru.

+ Wpisywać lub wklejać oddzieloną przecinkami listę nazw kolumn

    Jeśli zestaw danych jest szeroki, może być łatwiejsze użycie indeksów lub wygenerowanych list nazw, zamiast wybierać kolumny indywidualnie. Zakładając, że lista została przygotowana z wyprzedzeniem:

    1. Kliknij opcję **Z REGUŁAMI.** 
    2. Wybierz **pozycję Brak kolumn**, wybierz pozycję **Uwzględnij**, a następnie kliknij wewnątrz pola tekstowego czerwony wykrzyknik. 
    3. Wklej lub wpisz listę wcześniej zweryfikowanych nazw kolumn oddzielonych przecinkami. Nie można zapisać modułu, jeśli dowolna kolumna ma nieprawidłową nazwę, dlatego należy wcześniej sprawdzić nazwy.
    
    Za pomocą tej metody można również określić listę kolumn przy użyciu ich wartości indeksu. 

### <a name="choose-by-type"></a>Wybierz według typu

Jeśli używasz opcji **Z regułami,** możesz zastosować wiele warunków do wyboru kolumn. Na przykład może być konieczne uzyskanie tylko kolumn elementów o typie danych liczbowych.

Opcja **BEGIN WITH** określa punkt początkowy i jest ważna dla zrozumienia wyników. 

+ Jeśli wybierzesz opcję **WSZYSTKIE KOLUMNY,** wszystkie kolumny zostaną dodane do listy. Następnie należy użyć opcji **Wyklucz,** aby *usunąć* kolumny spełniające określone warunki. 

    Na przykład można rozpocząć od wszystkich kolumn, a następnie usunąć kolumny według nazwy lub według typu.

+ Jeśli wybierzesz opcję **BRAK KOLUMN,** lista kolumn zacznie być pusta. Następnie należy określić *warunki,* aby dodać kolumny do listy. 

    Jeśli zastosujesz wiele reguł, każdy warunek jest **addytywny**. Załóżmy na przykład, że zaczynasz od braku kolumn, a następnie dodaj regułę, aby uzyskać wszystkie kolumny liczbowe. W zestawie danych o cenach samochodów powoduje to 16 kolumn. Następnie kliknij **+** znak, aby dodać nowy warunek, a następnie wybierz pozycję **Uwzględnij wszystkie operacje**. Wynikowy zestaw danych zawiera wszystkie kolumny liczbowe oraz wszystkie kolumny obiektowe, w tym niektóre kolumny operacji ciągu.

### <a name="choose-by-column-index"></a>Wybieranie według indeksu kolumn

Indeks kolumn odnosi się do kolejności kolumny w oryginalnym zestawie danych.

+ Kolumny są numerowane sekwencyjnie od 1.  
+ Aby uzyskać zakres kolumn, użyj łącznika. 
+ Specyfikacje otwarte, takie `1-` `-3` jak lub nie są dozwolone.
+ Zduplikowane wartości indeksu (lub nazwy kolumn) są niedozwolone i mogą spowodować błąd.

Na przykład przy założeniu, że zestaw danych ma co najmniej osiem kolumn, można wkleić w dowolnym z następujących przykładów, aby zwrócić wiele nieobsłanych kolumn: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

ostateczny przykład nie powoduje błędu; jednak zwraca pojedyncze wystąpienie `4`kolumny .



### <a name="change-order-of-columns"></a>Zmienianie kolejności kolumn

Opcja **Zezwalaj na duplikaty i zachowywanie kolejności kolumn w zaznaczeniu** zaczyna się od pustej listy i dodaje kolumny określone według nazwy lub indeksu. W przeciwieństwie do innych opcji, które zawsze zwracają kolumny w ich "kolejności naturalnej", ta opcja wyprowadza kolumny w kolejności, w jakiej nazwy lub listy. 

Na przykład w zestawie danych z kolumnami Col1, Col2, Col3 i Col4 można odwrócić kolejność kolumn i pominąć kolumnę 2, określając jedną z następujących list:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 