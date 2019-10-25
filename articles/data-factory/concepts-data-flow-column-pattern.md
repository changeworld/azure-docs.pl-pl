---
title: Wzorce kolumn w przepływie danych mapowania Azure Data Factory
description: Tworzenie uogólnionych wzorców transformacji danych przy użyciu wzorców kolumn w Azure Data Factory mapowania przepływów danych
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: 0c9a3c2ef05f4a11933ca7fc81c7c0f87a612293
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72789932"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Używanie wzorców kolumn w mapowaniu przepływu danych

Niektóre przekształcenia przepływu danych mapowania umożliwiają odwoływanie się do kolumn szablonu na podstawie wzorców zamiast zakodowanych nazw kolumn. Takie dopasowanie jest znane jako *wzorce kolumn*. Możesz definiować wzorce, aby dopasować kolumny na podstawie nazwy, typu danych, strumienia lub pozycji, zamiast wymagać dokładnej nazwy pól. Istnieją dwa scenariusze, w których są przydatne wzorce kolumn:

* Jeśli przychodzące pola źródłowe często zmieniają się, na przykład w przypadku zmiany kolumn w plikach tekstowych lub bazach danych NoSQL. Ten scenariusz jest nazywany [dryfem schematu](concepts-data-flow-schema-drift.md).
* Jeśli chcesz wykonać wspólną operację na dużej grupie kolumn. Na przykład, chcemy rzutować każdą kolumnę, która ma wartość "Total" w nazwie kolumny na podwójną.

Wzorce kolumn są obecnie dostępne w transformacje kolumn pochodnych, agregacji, wyboru i ujścia.

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Wzorce kolumn w kolumnie pochodnej i agregowanie

Aby dodać wzorzec kolumny w kolumnie pochodnej lub karcie agregowania transformacji agregowanej, kliknij ikonę znaku plus z prawej strony istniejącej kolumny. Wybierz pozycję **Dodaj wzorzec kolumny**. 

![wzorce kolumn](media/data-flow/columnpattern.png "Wzorce kolumn")

Użyj [konstruktora wyrażeń](concepts-data-flow-expression-builder.md) , aby wprowadzić warunek dopasowania. Utwórz wyrażenie logiczne, które pasuje do kolumn na podstawie `name`, `type`, `stream`i `position` kolumny. Wzorzec będzie miał wpływ na wszystkie kolumny, przenoszące lub zdefiniowane, gdzie warunek zwraca wartość true.

Dwa pola wyrażenia poniżej warunku Match określają nowe nazwy i wartości kolumn, których dotyczy problem. Użyj `$$`, aby odwołać się do istniejącej wartości dopasowanego pola. Pole wyrażenia Left definiuje nazwę i prawo pole wyrażenia definiuje wartość.

![wzorce kolumn](media/data-flow/columnpattern2.png "Wzorce kolumn")

Powyższy wzorzec kolumny jest zgodny z każdą kolumną typu Double i tworzy jedną kolumnę agregującą dla dopasowania. Nazwa nowej kolumny jest dopasowaną nazwą kolumny połączonej z "_Total". Wartość nowej kolumny to zaokrąglona, zagregowana suma istniejącej wartości podwójnej precyzji.

Aby sprawdzić, czy odpowiedni warunek jest poprawny, można sprawdzić poprawność schematu wyjściowego zdefiniowanych kolumn na karcie **Inspekcja** lub uzyskać migawkę danych na karcie **Podgląd danych** . 

![wzorce kolumn](media/data-flow/columnpattern3.png "Wzorce kolumn")

## <a name="rule-based-mapping-in-select-and-sink"></a>Mapowanie oparte na regułach w SELECT i ujścia

Podczas mapowania kolumn w źródle i wybierania transformacji można dodać stałe mapowania lub mapowania oparte na regułach. Jeśli znasz schemat danych i oczekujesz, że określone kolumny ze źródłowego zestawu danych są zawsze zgodne z określonymi nazwami statycznymi, użyj stałego mapowania. Jeśli pracujesz z elastycznymi schematami, użyj mapowania opartego na regułach, aby utworzyć dopasowanie do wzorca na podstawie `name`, `type`, `stream`i `position` kolumn. Można mieć dowolną kombinację stałych i mapowań opartych na regułach. 

Aby dodać mapowanie oparte na regułach, kliknij przycisk **Dodaj mapowanie** i wybierz **Mapowanie oparte na regułach**.

![Mapowanie oparte na regułach](media/data-flow/rule2.png "Mapowanie oparte na regułach")

W polu wyrażenia po lewej stronie wprowadź warunek dopasowania wartości logicznej. W polu wyrażenie po prawej stronie Określ, do czego zostanie zamapowana pasująca kolumna. Użyj `$$`, aby odwołać się do istniejącej nazwy dopasowanego pola.

Jeśli klikniesz ikonę z dolnym cudzysłowem, możesz określić warunek mapowania wyrażenia regularnego.

Kliknij ikonę okularów obok mapowania opartego na regułach, aby wyświetlić zdefiniowane kolumny i ich mapowania.

![Mapowanie oparte na regułach](media/data-flow/rule1.png "Mapowanie oparte na regułach")

W powyższym przykładzie tworzone są dwa mapowania oparte na regułach. Pierwsze przyjmuje wszystkie kolumny o nazwie "Movie" i mapuje je na istniejące wartości. Druga reguła używa wyrażenia regularnego do dopasowania wszystkich kolumn, które zaczynają się od "Movie" i mapują je do kolumny "movieId".

Jeśli reguła skutkuje wieloma identycznymi mapowaniami, Włącz **pomijanie zduplikowanych danych wejściowych** lub **Pomiń zduplikowane dane wyjściowe** , aby zapobiec duplikowaniu.

## <a name="pattern-matching-expression-values"></a>Wartości wyrażeń dopasowania do wzorca.

* `$$` przetłumaczy na nazwę lub wartość każdego dopasowania w czasie wykonywania
* `name` reprezentuje nazwę każdej kolumny przychodzącej
* `type` reprezentuje typ danych każdej kolumny przychodzącej
* `stream` reprezentuje nazwę skojarzoną z każdym strumieniem lub transformację w przepływie
* `position` jest pozycją porządkową kolumn w przepływie danych

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [języku wyrażeń](data-flow-expression-functions.md) przepływu danych mapowania dla transformacji danych
* Używanie wzorców kolumn w [transformację ujścia](data-flow-sink.md) i [Wybieranie transformacji](data-flow-select.md) z mapowaniem opartym na regułach
