---
title: Mapowanie wzorców kolumny przepływu danych w usłudze Azure Data Factory
description: Dowiedz się, jak używać wzorców kolumny fabryki danych Azure w mapowanie przepływu danych w celu utworzenia wzorców uogólnionego szablonów do przekształcania pola do przepływu danych, bez względu na odpowiednie metadane schematu
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 08cdaafe00b7dc586ea75f6ff03fdb89107edee9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66430756"
---
# <a name="azure-data-factory-mapping-data-flows-column-patterns"></a>Usługi Azure data factory mapowanie danych przepływów wzorców kolumny

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Kilka przekształcenia przepływ danych fabryki danych platformy Azure obsługuje pomysł wzorców"kolumny", w którym można utworzyć kolumny szablonu na podstawie wzorców zamiast nazwy ustalonych kolumn. Można użyć tej funkcji w ramach Konstruktor wyrażeń wzorce, aby dopasować kolumny dla transformacji zamiast nazwy dokładnie tak samo, określonych pól. Wzorce są przydatne w przypadku przychodzących pola źródłowego zmieniają się często, szczególnie w przypadku zmiany kolumny w plikach tekstowych lub bazy danych NoSQL. Ten stan jest czasami określane jako "Schematu dryfu".

![wzorce kolumny](media/data-flow/columnpattern2.png "wzorców kolumny")

Wzorce kolumny są przydatne do obsługi zarówno schematu kilka scenariuszy, a także Ogólne scenariusze. To rozwiązanie dobre dla warunków gdzie nie jest możliwe w pełni znać nazwę każdej kolumny. Można wzorca dopasowania nazwy kolumny i typ danych kolumny i utworzyć wyrażenie dla transformacji, która umożliwia wykonanie tej operacji względem dowolnego pola w strumień danych, który jest zgodny z `name`  &  `type` wzorców.

Podczas dodawania wyrażenia do transformacji, który akceptuje wzorców, wybierz pozycję "Dodaj kolumnę wzorzec". Wzorce kolumny pozwala wzorce dopasowania schematu kilka kolumn.

Podczas kompilowania wzorców kolumny szablonów, należy użyć `$$` w wyrażeniu do reprezentowania odwołań do każdego pola dopasowane ze strumienia danych wejściowych.

Jeśli zdecydujesz się użyć jednej z funkcji wyrażenia regularnego Konstruktor wyrażeń, następnie później można $1, 2 USD $3... można odwoływać się do podrzędnego wzorców zgodny z z wyrażeniem regularnym.

Przykładem scenariusza wzorzec kolumny używa SUM z serią przychodzących pola. Łączny obliczenia SUM w agregacji są transformacji. Następnie można suma każdego dopasowania typy pól, które odpowiada "integer", a następnie użyj $$ można odwoływać się do każdego dopasowania w wyrażeniu.
