---
title: Mapowanie wzorców kolumny przepływu danych w usłudze Azure Data Factory
description: Dowiedz się, jak używać wzorców kolumny fabryki danych Azure w mapowanie przepływu danych w celu utworzenia wzorców uogólnionego szablonów do przekształcania pola do przepływu danych, bez względu na odpowiednie metadane schematu
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 5131329f6675bc86374f5a5c081e0aaa7d36c0fe
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155237"
---
# <a name="azure-data-factory-mapping-data-flows-column-patterns"></a>Usługi Azure data factory mapowanie danych przepływów wzorców kolumny

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Kilka przekształcenia przepływ danych fabryki danych platformy Azure obsługuje pomysł wzorców"kolumny", w którym można utworzyć kolumny szablonu na podstawie wzorców zamiast nazwy ustalonych kolumn. Wzorce, aby dopasować kolumny dla transformacji zamiast zadania xact, nazwy określonego pola, można użyć tej funkcji w ramach Konstruktor wyrażeń. Wzorce są przydatne w przypadku przychodzących pola źródłowego zmieniają się często, szczególnie w przypadku zmiany kolumny w plikach tekstowych lub bazy danych NoSQL. Ten stan jest czasami określane jako "Schematu dryfu".

![wzorce kolumny](media/data-flow/columnpattern2.png "wzorców kolumny")

Wzorce kolumny są przydatne do obsługi zarówno schematu kilka scenariuszy, a także Ogólne scenariusze. To rozwiązanie dobre dla warunków gdzie nie jest możliwe w pełni znać nazwę każdej kolumny. Można wzorca dopasowania nazwy kolumny i typ danych kolumny i utworzyć wyrażenie dla transformacji, która umożliwia wykonanie tej operacji względem dowolnego pola w strumień danych, który jest zgodny z `name`  &  `type` wzorców.

Podczas dodawania wyrażenia do transformacji, który akceptuje wzorców, wybierz pozycję "Dodaj kolumnę wzorzec". Wzorce kolumny pozwala wzorce dopasowania schematu kilka kolumn.

Podczas kompilowania wzorców kolumny szablonów, należy użyć `$$` w wyrażeniu do reprezentowania odwołań do każdego pola dopasowane ze strumienia danych wejściowych.

Jeśli zdecydujesz się użyć jednej z funkcji wyrażenia regularnego Konstruktor wyrażeń, następnie później służy $1, 2 USD, $3... odwołania subpatterns dopasowane z z wyrażeniem regularnym.

Przykładem scenariusza wzorzec kolumny używa SUM z serią przychodzących pola. Łączny obliczenia SUM w agregacji są transformacji. Można następnie Suma na każdego dopasowania z typów pól tej dopasowania "integer" i następnie użyj $$, aby odwoływać się do każdego dopasowania w wyrażeniu.
