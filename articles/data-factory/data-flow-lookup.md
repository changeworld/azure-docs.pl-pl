---
title: Przekształcenie przeszukiwania przepływu danych Azure Data Factory mapowania
description: Przekształcenie przeszukiwania przepływu danych Azure Data Factory mapowania
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/03/2019
ms.openlocfilehash: 25d8588f8e2c968dc2516938263aaa7d6ddcff13
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387871"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Przekształcenie przeszukiwania przepływu danych Azure Data Factory mapowania

Użyj wyszukiwania, aby dodać dane referencyjne z innego źródła do przepływu danych. Transformacja wyszukiwania wymaga zdefiniowanego źródła wskazującego tabelę referencyjną i dopasowuje się do pól kluczy.

![Transformacja wyszukiwania](media/data-flow/lookup1.png "Wyszukiwanie")

Wybierz pola klucza, które chcesz dopasować między polami strumienia przychodzącego a polami ze źródła odwołania. Najpierw należy utworzyć nowe źródło na kanwie projektu przepływu danych do użycia jako po prawej stronie dla wyszukiwania.

Gdy zostaną znalezione dopasowania, wyniki i kolumny ze źródła odwołania zostaną dodane do przepływu danych. Możesz wybrać, które pola zainteresowania mają być uwzględnione w zlewie na końcu przepływu danych. Alternatywnie możesz użyć przekształcenia SELECT po wyszukiwaniu, aby oczyścić listę pól, aby zachować tylko pola z obu strumieni, które chcesz zachować.

Transformacja wyszukiwania wykonuje odpowiednik lewego sprzężenia zewnętrznego. W związku z tym zobaczysz wszystkie wiersze z lewej strony Połącz z dopasowaniami po prawej stronie. Jeśli masz wiele pasujących wartości w odnośniku lub jeśli chcesz dostosować wyrażenie wyszukiwania, zaleca się przełączenie na transformację sprzężenia i użycie sprzężenia krzyżowego. Pozwoli to uniknąć ewentualnych błędów produktu kartezjańskiego podczas wykonywania.

## <a name="match--no-match"></a>Dopasowanie/brak dopasowania

Po przekształceniu wyszukiwania można użyć kolejnych przekształceń, aby sprawdzić wyniki każdego wiersza dopasowania przy użyciu funkcji wyrażenia `isMatch()`, aby dokonać dalszych wyborów w logice w zależności od tego, czy wyszukiwanie spowodowało dopasowanie wierszy, czy nie.

## <a name="optimizations"></a>Optymalizacje

W Data Factory przepływy danych są wykonywane w skalowanych środowiskach Spark. Jeśli zestaw danych może pasować do przestrzeni pamięci węzła procesu roboczego, możemy zoptymalizować wydajność wyszukiwania.

![Sprzężenie emisji](media/data-flow/broadcast.png "Sprzężenie emisji")

### <a name="broadcast-join"></a>Sprzężenie emisji

Wybierz pozycję sprzężenie emisji lewej i/lub prawej strony, aby zażądać podajnika APD w celu wypchnięcia całego zestawu danych z obu stron relacji wyszukiwania do pamięci. W przypadku mniejszych zestawów danych może to znacznie poprawić wydajność wyszukiwania.

### <a name="data-partitioning"></a>Partycjonowanie danych

Możesz również określić Partycjonowanie danych, wybierając pozycję "Ustaw partycjonowanie" na karcie Optymalizacja transformacji wyszukiwania, aby utworzyć zestawy danych, które mogą być lepiej dopasowane do pamięci na proces roboczy.

## <a name="next-steps"></a>Następne kroki

Przekształcenia [sprzężeń](data-flow-join.md) i [istniejących](data-flow-exists.md) są podobne do zadań w MAPOWANIU przepływów danych ADF. Zapoznaj się z tymi przekształceniami dalej.
