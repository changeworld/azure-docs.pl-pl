---
title: Mapowanie przekształcenia wyszukiwania przepływu danych
description: Przekształcenie przeszukiwania przepływu danych Azure Data Factory mapowania
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/03/2019
ms.openlocfilehash: 5cc54c95759ba1490f498305f05cc49a4411686d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930331"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Przekształcenie przeszukiwania przepływu danych Azure Data Factory mapowania

Użyj wyszukiwania, aby dodać dane referencyjne z innego źródła do przepływu danych. Transformacja wyszukiwania wymaga zdefiniowanego źródła wskazującego tabelę referencyjną i dopasowuje się do pól kluczy.

![Transformacja wyszukiwania](media/data-flow/lookup1.png "Wyszukiwanie")

Wybierz pola klucza, które chcesz dopasować między polami strumienia przychodzącego a polami ze źródła odwołania. Najpierw należy utworzyć nowe źródło na kanwie projektu przepływu danych do użycia jako po prawej stronie dla wyszukiwania.

Gdy zostaną znalezione dopasowania, wyniki i kolumny ze źródła odwołania zostaną dodane do przepływu danych. Możesz wybrać, które pola zainteresowania mają być uwzględnione w zlewie na końcu przepływu danych. Alternatywnie możesz użyć przekształcenia SELECT po wyszukiwaniu, aby oczyścić listę pól, aby zachować tylko pola z obu strumieni, które chcesz zachować.

Transformacja wyszukiwania wykonuje odpowiednik lewego sprzężenia zewnętrznego. W związku z tym zobaczysz wszystkie wiersze z lewej strony Połącz z dopasowaniami po prawej stronie. Jeśli masz wiele pasujących wartości w odnośniku lub jeśli chcesz dostosować wyrażenie wyszukiwania, zaleca się przełączenie na transformację sprzężenia i użycie sprzężenia krzyżowego. Pozwoli to uniknąć ewentualnych błędów produktu kartezjańskiego podczas wykonywania.

## <a name="match--no-match"></a>Dopasowanie/brak dopasowania

Po przekształceniu wyszukiwania można użyć kolejnych przekształceń, aby sprawdzić wyniki każdego z wierszy dopasowania przy użyciu funkcji wyrażenia `isMatch()`, aby dokonać dalszych wyborów w logice w zależności od tego, czy wyszukiwanie spowodowało dopasowanie wierszy, czy nie.

![Wzorzec wyszukiwania](media/data-flow/lookup111.png "Wzorzec wyszukiwania")

Po użyciu transformacji wyszukiwania można dodać podział przekształcenia warunkowego do funkcji ```isMatch()```. W powyższym przykładzie pasujące wiersze przechodzą przez górny strumień i niepasujące wiersze w strumieniu ```NoMatch```.

## <a name="first-or-last-value"></a>Pierwsza lub Ostatnia wartość

Jeśli masz wiele dopasowań z wyszukiwania, możesz chcieć zmniejszyć liczbę dopasowanych wierszy, wybierając pierwsze lub ostatnie dopasowanie. Można to zrobić przy użyciu transformacji agregacji po wyszukiwaniu.

W takim przypadku transformacja agregacji o nazwie ```PickFirst``` jest używana do wybrania pierwszej wartości z dopasowań wyszukiwania.

![Agregowanie wyszukiwania](media/data-flow/lookup333.png "Agregowanie wyszukiwania")

![Wyszukaj w pierwszej kolejności](media/data-flow/lookup444.png "Wyszukaj w pierwszej kolejności")

## <a name="optimizations"></a>Optymalizacje

W Data Factory przepływy danych są wykonywane w skalowanych środowiskach Spark. Jeśli zestaw danych może pasować do przestrzeni pamięci węzła procesu roboczego, możemy zoptymalizować wydajność wyszukiwania.

![Sprzężenie emisji](media/data-flow/broadcast.png "Dołączanie do emisji")

### <a name="broadcast-join"></a>Sprzężenie emisji

Wybierz pozycję sprzężenie emisji lewej i/lub prawej strony, aby zażądać podajnika APD w celu wypchnięcia całego zestawu danych z obu stron relacji wyszukiwania do pamięci. W przypadku mniejszych zestawów danych może to znacznie poprawić wydajność wyszukiwania.

### <a name="data-partitioning"></a>Partycjonowanie danych

Możesz również określić Partycjonowanie danych, wybierając pozycję "Ustaw partycjonowanie" na karcie Optymalizacja transformacji wyszukiwania, aby utworzyć zestawy danych, które mogą być lepiej dopasowane do pamięci na proces roboczy.

## <a name="next-steps"></a>Następne kroki

* Przekształcenia [sprzężeń](data-flow-join.md) i [istniejących](data-flow-exists.md) są podobne do zadań w MAPOWANIU przepływów danych ADF. Zapoznaj się z tymi przekształceniami dalej.
* Użyj [podziału warunkowego](data-flow-conditional-split.md) z ```isMatch()```, aby podzielić wiersze na pasujące i niezgodne wartości
