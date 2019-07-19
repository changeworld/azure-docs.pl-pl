---
title: Azure Data Factory mapowanie wzorców kolumn przepływu danych
description: Tworzenie uogólnionych wzorców transformacji danych przy użyciu Azure Data Factory wzorców kolumn w mapowaniu przepływów danych
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: d24988dfd5cbaf20e92c5afbbc39dc0c78e3ef6a
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314856"
---
# <a name="azure-data-factory-mapping-data-flows-column-patterns"></a>Wzorce kolumn przepływów danych mapowania usługi Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Niektóre przekształcenia przepływu danych Azure Data Factory obsługują koncepcję "wzorców kolumn", dzięki czemu można tworzyć kolumny szablonów na podstawie wzorców zamiast zakodowanych nazw kolumn. Za pomocą tej funkcji w Konstruktorze wyrażeń można definiować wzorce w celu dopasowania do kolumn na potrzeby transformacji zamiast konieczności dokładnej, konkretnych nazw pól. Wzorce są przydatne, jeśli przychodzące pola źródłowe zmieniają się często, szczególnie w przypadku zmiany kolumn w plikach tekstowych lub bazach danych NoSQL. Ten stan jest czasami określany jako "dryfowanie schematu".

![wzorce kolumn](media/data-flow/columnpattern2.png "Wzorce kolumn")

Wzorce kolumn są przydatne do obsługi zarówno scenariuszy dryfowania schematu, jak i ogólnych scenariuszy. Jest to dobre w przypadku warunków, w których nie można w pełni znać nazw kolumn. Można dopasować wzorzec do nazwy kolumny i typu danych kolumny i utworzyć wyrażenie do przekształcenia, które wykona tę operację względem dowolnego pola w strumieniu danych, który jest zgodny ze `name`  &  `type` wzorcem.

Dodając wyrażenie do przekształcenia, które akceptuje wzorce, wybierz pozycję "Dodaj wzorzec kolumny". Wzorce kolumn umożliwiają wzorce dopasowywania do kolumn dryfu schematu.

Podczas kompilowania wzorców kolumn szablonu Użyj `$$` w wyrażeniu, aby reprezentować odwołanie do każdego dopasowanego pola ze strumienia danych wejściowych.

Jeśli zdecydujesz się użyć jednej z funkcji wyrażenia regularnego konstruktora wyrażeń, możesz następnie użyć $1, $2, $3... Aby odwołać się do podwzorców pasujących do wyrażenia regularnego.

Przykładowy scenariusz wzorca kolumny korzysta z SUM z serią pól przychodzących. Obliczenia SUM zagregowanych są w transformacji agregowanej. Następnie można użyć SUM dla każdego dopasowania typów pól, które pasują do wartości "Integer", a następnie użyć $ $, aby odwołać się do każdego dopasowania w wyrażeniu.

## <a name="match-columns"></a>Dopasuj kolumny
![typy wzorców kolumn](media/data-flow/pattern2.png "Typy wzorców")

Aby kompilować wzorce na podstawie kolumn, można dopasować nazwę kolumny, typ, strumień lub pozycję i użyć dowolnej kombinacji z funkcjami wyrażeń i wyrażeniami regularnymi.

![położenie kolumny](media/data-flow/position.png "Położenie kolumny")

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [języku wyrażeń](http://aka.ms/dataflowexpressions) przepływu danych mapowania ADF na potrzeby transformacji danych
