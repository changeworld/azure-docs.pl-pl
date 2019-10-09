---
title: Przekształcenie przeszukiwania przepływu danych Azure Data Factory mapowania
description: Przekształcenie przeszukiwania przepływu danych Azure Data Factory mapowania
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: ef72b7aed12afd1cee47b11bc7584d1e53bf2af5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029347"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Przekształcenie przeszukiwania przepływu danych Azure Data Factory mapowania



Użyj wyszukiwania, aby dodać dane referencyjne z innego źródła do przepływu danych. Transformacja wyszukiwania wymaga zdefiniowanego źródła wskazującego tabelę referencyjną i dopasowuje się do pól kluczy.

(media/data-flow/lookup1.png "Wyszukiwanie") ![transformacji wyszukiwania]

Wybierz pola klucza, które chcesz dopasować między polami strumienia przychodzącego a polami ze źródła odwołania. Najpierw należy utworzyć nowe źródło na kanwie projektu przepływu danych do użycia jako po prawej stronie dla wyszukiwania.

Gdy zostaną znalezione dopasowania, wyniki i kolumny ze źródła odwołania zostaną dodane do przepływu danych. Możesz wybrać, które pola zainteresowania mają być uwzględnione w zlewie na końcu przepływu danych.

## <a name="match--no-match"></a>Dopasowanie/brak dopasowania

Po przekształceniu wyszukiwania można użyć kolejnych przekształceń, aby sprawdzić wyniki każdego wiersza dopasowania przy użyciu funkcji wyrażenia `isMatch()`, aby dokonać dalszych wyborów w logice w zależności od tego, czy wyszukiwanie spowodowało dopasowanie wierszy, czy nie.

## <a name="optimizations"></a>Optymalizacje

W Data Factory przepływy danych są wykonywane w skalowanych środowiskach Spark. Jeśli zestaw danych może pasować do przestrzeni pamięci węzła procesu roboczego, możemy zoptymalizować wydajność wyszukiwania.

(media/data-flow/broadcast.png "Sprzężenie emisji") ![sprzężenia]emisji

### <a name="broadcast-join"></a>Sprzężenie emisji

Wybierz pozycję sprzężenie emisji lewej i/lub prawej strony, aby zażądać podajnika APD w celu wypchnięcia całego zestawu danych z obu stron relacji wyszukiwania do pamięci.

### <a name="data-partitioning"></a>Partycjonowanie danych

Możesz również określić Partycjonowanie danych, wybierając pozycję "Ustaw partycjonowanie" na karcie Optymalizacja transformacji wyszukiwania, aby utworzyć zestawy danych, które mogą być lepiej dopasowane do pamięci na proces roboczy.

## <a name="next-steps"></a>Następne kroki

Przekształcenia [sprzężeń](data-flow-join.md) i [istniejących](data-flow-exists.md) są podobne do zadań w MAPOWANIU przepływów danych ADF. Zapoznaj się z tymi przekształceniami dalej.
