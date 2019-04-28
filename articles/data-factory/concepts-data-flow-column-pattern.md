---
title: Mapowanie wzorców kolumny przepływu danych w usłudze Azure Data Factory
description: Azure danych fabryki mapowanie przepływu kolumny wzorców danych są używane do tworzenia wzorców uogólnionego szablonów do przekształcania pola do przepływu danych, bez względu na odpowiednie metadane schematu
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 53d3300ea11a86c34909ba6ce0fd6c8c0c38b4b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269729"
---
# <a name="azure-data-factory-mapping-data-flow-concepts"></a>Mapowanie przepływu pojęcia związane z danymi w usłudze Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Kilka przekształcenia przepływ danych fabryki danych platformy Azure obsługuje pomysł wzorców"kolumny", w którym można utworzyć kolumny szablonu na podstawie wzorców zamiast nazwy ustalonych kolumn. Wzorce, aby dopasować kolumny dla transformacji zamiast zadania xact, nazwy określonego pola, można użyć tej funkcji w ramach Konstruktor wyrażeń. Wzorce są przydatne, gdy przychodzący pola źródłowego zmieniają się często, szczególnie w przypadku zmiany kolumny w plikach tekstowych lub bazy danych NoSQL. To czasami nazywa się "Schematu dryfu".

![wzorce kolumny](media/data-flow/columnpattern2.png "wzorców kolumny")

Wzorce kolumny są przydatne do obsługi zarówno schematu kilka scenariuszy, a także Ogólne scenariusze. To rozwiązanie dobre dla warunków gdzie nie jest możliwe w pełni znać nazwę każdej kolumny. Można wzorca dopasowania nazwy kolumny i typ danych kolumny i utworzyć wyrażenie dla transformacji, która umożliwia wykonanie tej operacji względem dowolnego pola w strumień danych, który jest zgodny z `name`  &  `type` wzorców.

Podczas dodawania wyrażenia do transformacji, który akceptuje wzorców, wybierz pozycję "Dodaj kolumnę wzorzec". Wzorce kolumny pozwala wzorce dopasowania schematu kilka kolumn.

Podczas kompilowania wzorców kolumny szablonów, należy użyć `$$` w wyrażeniu do reprezentowania odwołań do każdego pola dopasowane ze strumienia danych wejściowych.

Jeśli zdecydujesz się użyć jednej z funkcji wyrażenia regularnego Konstruktor wyrażeń, następnie później służy $1, 2 USD, $3... odwołania subpatterns dopasowane z z wyrażeniem regularnym.

Przykładem scenariusza wzorzec kolumny używa SUM z serią przychodzących pola. Łączny obliczenia SUM w agregacji są transformacji. Można następnie Suma na każdego dopasowania z typów pól tej dopasowania "integer" i następnie użyj $$, aby odwoływać się do każdego dopasowania w wyrażeniu.
