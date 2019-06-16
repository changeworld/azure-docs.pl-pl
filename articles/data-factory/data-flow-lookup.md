---
title: Mapowanie transformacji odnośnika przepływu danych w usłudze Azure Data Factory
description: Mapowanie transformacji odnośnika przepływu danych w usłudze Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 197f5ba9d6921f4a9921b7074b9e05162d3e37b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64868121"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Mapowanie transformacji odnośnika przepływu danych w usłudze Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Użyj wyszukiwania, aby dodać dane referencyjne z innego źródła, aby przepływ danych. Przekształcenie wyszukiwania wymaga określonego źródła, wskazuje odwołanie do tabeli, która odpowiada na pola klucza.

![Przekształcanie wyszukiwania](media/data-flow/lookup1.png "wyszukiwania")

Zaznacz pola klucza, które mają być zgodne na przychodzące pola strumienia i pola ze źródła odniesienia. Należy najpierw utworzono nowe źródło na kanwę projektu przepływu danych, aby użyć po prawej stronie do wyszukiwania.

W przypadku znalezienia dopasowania wynikowy wiersze i kolumny źródłowej odwołania zostaną dodane do przepływu danych. Możesz wybrać pola, które interesujące, który chcesz uwzględnić w obiektu Sink na końcu przepływu danych.

## <a name="match--no-match"></a>Zgodne / Brak dopasowania

Po przekształceniu usługi wyszukiwania można użyć kolejnych przekształceń można sprawdzić wyniki każdego wiersza dopasowania za pomocą funkcji wyrażenia `isMatch()` może wprowadzać dalszych opcji w logice w oparciu o czy wyszukiwanie spowodowało dopasowanie wiersza lub nie.

## <a name="optimizations"></a>Optymalizacje

W usłudze Data Factory wykonania przepływu danych w środowiskach platformy Spark skalowanych w poziomie. Jeśli zestaw danych można dopasować do obszaru pamięci na węzeł procesu roboczego, firma Microsoft można zoptymalizować wydajność wyszukiwania.

![Emisja sprzężenia](media/data-flow/broadcast.png "emisji, sprzężenia")

### <a name="broadcast-join"></a>Sprzężenia emisji

Wybierz w lewo i/lub po prawej stronie emisji sprzężenie do żądania usługi ADF, aby wypchnąć cały zestaw danych z żadnej stronie relacji odnośników do pamięci.

### <a name="data-partitioning"></a>Partycjonowanie danych

Można również określić, partycjonowanie danych, wybierając pozycję "Ustaw partycjonowania" na karcie Optymalizacja transformacji wyszukiwania do tworzenia zestawów danych, który może lepiej mieści się w pamięci dla procesu roboczego.

## <a name="next-steps"></a>Kolejne kroki

[Dołącz do](data-flow-join.md) i [Exists](data-flow-exists.md) przekształcenia wykonywania podobnych zadań w ADF mapowanie przepływu danych. Przyjrzyj się te przekształcenia dalej.
