---
title: Wzorce kolumn w Azure Data Factory mapowania przepływów danych
description: Tworzenie uogólnionych wzorców transformacji danych przy użyciu Azure Data Factory wzorców kolumn w mapowaniu przepływów danych
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: a95bbb726f8c391270d3f60ed769d9475004b1e4
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388022"
---
# <a name="mapping-data-flows-column-patterns"></a>Mapowanie wzorców kolumn przepływów danych



Niektóre przekształcenia przepływu danych Azure Data Factory obsługują koncepcję "wzorców kolumn", dzięki czemu można tworzyć kolumny szablonów na podstawie wzorców zamiast zakodowanych nazw kolumn. Za pomocą tej funkcji w Konstruktorze wyrażeń można definiować wzorce w celu dopasowania do kolumn na potrzeby transformacji zamiast konieczności dokładnej, konkretnych nazw pól. Wzorce są przydatne, jeśli przychodzące pola źródłowe zmieniają się często, szczególnie w przypadku zmiany kolumn w plikach tekstowych lub bazach danych NoSQL. Ten stan jest czasami określany jako "dryfowanie schematu".

Ta "elastyczna obsługa schematu" znajduje się obecnie w kolumnie pochodnej i przekształceniach agregacji, a także w transformacjach wybranych i ujścia jako "mapowanie oparte na regułach".

![wzorce kolumn](media/data-flow/columnpattern2.png "Wzorce kolumn")

## <a name="column-patterns"></a>Wzorce kolumn
Wzorce kolumn są przydatne do obsługi zarówno scenariuszy dryfowania schematu, jak i ogólnych scenariuszy. Jest to dobre w przypadku warunków, w których nie można w pełni znać nazw kolumn. Można dopasować wzorzec do nazwy kolumny i typu danych kolumny i utworzyć wyrażenie do przekształcenia, które wykona tę operację względem dowolnego pola w strumieniu danych, które pasuje do `name` @ no__t-2 wzorców.

Dodając wyrażenie do przekształcenia, które akceptuje wzorce, wybierz pozycję "Dodaj wzorzec kolumny". Wzorce kolumn umożliwiają wzorce dopasowywania do kolumn dryfu schematu.

Podczas kompilowania wzorców kolumn szablonu Użyj `$$` w wyrażeniu, aby reprezentować odwołanie do każdego dopasowanego pola ze strumienia danych wejściowych.

Jeśli zdecydujesz się użyć jednej z funkcji wyrażenia regularnego konstruktora wyrażeń, możesz następnie użyć $1, $2, $3... Aby odwołać się do podwzorców pasujących do wyrażenia regularnego.

Przykładowy scenariusz wzorca kolumny korzysta z SUM z serią pól przychodzących. Obliczenia SUM zagregowanych są w transformacji agregowanej. Następnie można użyć SUM dla każdego dopasowania typów pól, które pasują do wartości "Integer", a następnie użyć $ $, aby odwołać się do każdego dopasowania w wyrażeniu.

## <a name="match-columns"></a>Dopasuj kolumny
![typy wzorców kolumn](media/data-flow/pattern2.png "Typy wzorców")

Aby kompilować wzorce na podstawie kolumn, można dopasować nazwę kolumny, typ, strumień lub pozycję i użyć dowolnej kombinacji z funkcjami wyrażeń i wyrażeniami regularnymi.

![położenie kolumny](media/data-flow/position.png "Położenie kolumny")

## <a name="rule-based-mapping"></a>Mapowanie oparte na regułach
Podczas mapowania kolumn w źródle i wybierania przekształceń będzie dostępna opcja wyboru "stałe mapowanie" lub "mapowanie oparte na regułach". Gdy znasz schemat danych i oczekujesz określonych kolumn ze źródłowego zestawu danych, który zawsze pasuje do określonych nazw statycznych, możesz użyć stałego mapowania. Ale podczas pracy z elastycznymi schematami Użyj mapowania opartego na regułach. Można utworzyć dopasowanie wzorca przy użyciu reguł opisanych powyżej.

![Mapowanie oparte na regułach](media/data-flow/rule2.png "Mapowanie oparte na regułach")

Kompiluj reguły przy użyciu Konstruktora wyrażeń. Wyrażenia zwróci wartość logiczną, aby dopasować kolumny (true) lub wykluczyć kolumny (false).

## <a name="pattern-matching-special-columns"></a>Kolumny specjalne pasujące do wzorca

* `$$` będzie tłumaczyć na nazwę każdego dopasowania w czasie projektowania w trybie debugowania i po wykonaniu w czasie wykonywania
* `name` reprezentuje nazwę każdej kolumny przychodzącej
* `type` reprezentuje typ danych każdej kolumny przychodzącej
* `stream` reprezentuje nazwę skojarzoną z każdym strumieniem lub transformację w przepływie
* `position` jest pozycją porządkową kolumn w przepływie danych

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [języku wyrażeń](https://aka.ms/dataflowexpressions) przepływu danych mapowania ADF na potrzeby transformacji danych
* Używanie wzorców kolumn w [transformację ujścia](data-flow-sink.md) i [Wybieranie transformacji](data-flow-select.md) z mapowaniem opartym na regułach
