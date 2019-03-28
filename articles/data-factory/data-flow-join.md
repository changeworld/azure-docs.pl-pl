---
title: Usługi Azure Data Factory danych przepływ sprzężenia transformacji
description: Usługi Azure Data Factory danych przepływ sprzężenia transformacji
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 18f713198ef9aa45cb72a6718c0f7b086c019258
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540044"
---
# <a name="mapping-data-flow-join-transformation"></a>Przekształcenie sprzężenie przepływu danych mapowania

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Sprzężenia umożliwia łączenie danych z dwiema tabelami w przepływ danych. Kliknij przycisk na przekształcenie, które będą po lewej stronie relacji i Dodaj przekształcenie sprzężenie z przybornika. Wewnątrz przekształcenie sprzężenie wybierze inny strumień danych z przepływu danych, aby mieć prawo relacji.

![Dołącz do przekształcania](media/data-flow/join.png "Join")

## <a name="join-types"></a>Dołącz do typów

Wybierając typ sprzężenia jest wymagana dla transformacji sprzężenia.

### <a name="inner-join"></a>Sprzężenie wewnętrzne

Sprzężenie wewnętrzne będzie przekazywał tylko wiersze spełniające warunki kolumny z obu tabel.

### <a name="left-outer"></a>Lewe zewnętrzne

Wszystkie wiersze ze strumienia po lewej stronie, nie spełniają warunek sprzężenia są przekazywane, a kolumn wyjściowych z innej tabeli są ustawione na wartość NULL, oprócz wszystkich wierszy zwróconych przez sprzężenia wewnętrznego.

### <a name="right-outer"></a>Prawe zewnętrzne

Wszystkie wiersze z prawego strumienia, które nie spełniają warunek sprzężenia są przekazywane, a kolumny danych wyjściowych, które odpowiadają drugiej tabeli są ustawione na wartość NULL, oprócz wszystkich wierszy zwróconych przez sprzężenia wewnętrznego.

### <a name="full-outer"></a>Klauzule Full Outer

Pełne zewnętrzne tworzy wszystkie kolumny i wiersze z obu stron o wartości NULL dla kolumny, które są nieobecne w drugiej tabeli.

### <a name="cross-join"></a>Sprzężenie krzyżowe

Określ iloczyn wektorowy dwóch strumieni z wyrażeniem. Możesz użyć tego, aby utworzyć niestandardowe sprzężenia.

## <a name="specify-join-conditions"></a>Określ warunki sprzężenia

Warunek Left Join jest ze strumienia danych połączonych z lewej strony sprzężenia usługi. Warunek Right Join jest podłączony do sieci sprzężenia w dolnej części, która będzie bezpośrednich łącznika do innego strumienia lub odwołanie do innej usługi stream drugi strumienia danych.

Jest wymagane wprowadzenie co najmniej 1 warunki sprzężenia (1.n=czynniki). Mogą to być pola, które są albo wywoływane bezpośrednio, wybrania z menu rozwijanego lub wyrażenia.

## <a name="join-performance-optimizations"></a>Dołącz do optymalizacji wydajności

W odróżnieniu od sprzężenia scalania w narzędzia, takie jak SSIS sprzężenia w przepływ danych ADF operacja nie jest obowiązkowe scalania sprzężenia. W związku z kluczami sprzężenia nie trzeba najpierw posortowane. Operacja Join odbędzie się Spark za pomocą opartego na operacji join optymalne platformie Spark usługi Databricks: Sprzężenia emisji / po stronie mapy:

![Dołącz do przekształcania zoptymalizować](media/data-flow/joinoptimize.png "Dołącz do optymalizacji")

Jeśli zestaw danych można dopasować do pamięci węzła procesu roboczego usługi Databricks, firma Microsoft można zoptymalizować wydajność sprzężenia. Można również określić, partycjonowanie danych w operacji dołączania do tworzenia zestawów danych, który może lepiej mieści się w pamięci dla procesu roboczego.

## <a name="self-join"></a>Samosprzężenie

Samosprzężenie warunków w przepływ danych ADF można osiągnąć za pomocą Wybierz przekształcenie do aliasu istniejącego strumienia. Najpierw należy utworzyć "Nową gałąź" ze strumienia, a następnie dodaj Select alias całego oryginalnego strumienia.

![Samosprzężenie](media/data-flow/selfjoin.png "samosprzężenie")

Na powyższym diagramie Wybierz przekształcenie znajduje się na górze. Wszystkie wykonywanie operacji jest aliasowanie oryginalnego strumienia do "OrigSourceBatting". W wyróżnionych transformacji sprzężenia poniżej widać, że używamy tego strumienia wybierz aliasu jako sprzężenia po prawej stronie, może odwoływać się do tego samego klucza w lewej i prawej strony sprzężenia wewnętrznego.

## <a name="composite-and-custom-keys"></a>Klucze złożone i niestandardowe

Klucze złożone i niestandardowych można tworzyć na bieżąco wewnątrz przekształcania sprzężenia. Dodawanie wierszy i kolumn sprzężenia dodatkowe za pomocą znak plus (+) obok każdego wiersza w relacji. Albo obliczyć nową wartość klucza w Konstruktorze wyrażeń wartości sprzężenia na bieżąco.

## <a name="next-steps"></a>Kolejne kroki

Po dołączeniu danych, możesz następnie [tworzenie nowych kolumn](data-flow-derived-column.md) i [ujście danych do docelowego magazynu danych](data-flow-sink.md).
