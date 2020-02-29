---
title: Mapowanie przekształcenia wyszukiwania przepływu danych
description: Przekształcenie przeszukiwania przepływu danych Azure Data Factory mapowania
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/26/2020
ms.openlocfilehash: 2216e1bf058eef486dbfefba24d52bdc6bdb232f
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164682"
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

Transformacja wyszukiwania jest implementowana jako lewe sprzężenie zewnętrzne. Jeśli masz wiele dopasowań z wyszukiwania, możesz chcieć zmniejszyć liczbę dopasowanych wierszy, wybierając pierwszy dopasowany wiersz, ostatnie dopasowanie lub dowolny losowy wiersz.

### <a name="option-1"></a>Opcja 1

![Wyszukiwanie pojedynczego wiersza](media/data-flow/singlerowlookup.png "Wyszukiwanie pojedynczego wiersza")

* Dopasuj wiele wierszy: pozostaw to pole puste, aby zwrócić pojedynczy wiersz dopasowania
* Dopasuj na: wybierz pierwsze, ostatnie lub dowolne dopasowanie
* Warunki sortowania: w przypadku wybrania opcji pierwszy lub ostatni moduł ADF wymaga, aby dane były uporządkowane w taki sposób, że logika jest za pierwsza i Ostatnia

> [!NOTE]
> Użyj pierwszej lub ostatniej opcji w selektorze pojedynczego wiersza, jeśli musisz określić wartość, która ma zostać przywrócona z odnośnika. Użycie wyszukiwania "any" lub wiele wierszy będzie szybsze.

### <a name="option-2"></a>Opcja 2

Można to również zrobić przy użyciu transformacji agregacji po wyszukiwaniu. W takim przypadku transformacja agregacji o nazwie ```PickFirst``` jest używana do wybrania pierwszej wartości z dopasowań wyszukiwania.

![Agregowanie wyszukiwania](media/data-flow/lookup333.png "Agregowanie wyszukiwania")

![Wyszukaj w pierwszej kolejności](media/data-flow/lookup444.png "Wyszukaj w pierwszej kolejności")

## <a name="optimizations"></a>Optymalizacje

W Data Factory przepływy danych są wykonywane w skalowanych środowiskach Spark. Jeśli zestaw danych może pasować do przestrzeni pamięci węzła procesu roboczego, możemy zoptymalizować wydajność wyszukiwania.

![Sprzężenie emisji](media/data-flow/broadcast.png "Sprzężenie emisji")

### <a name="broadcast-join"></a>Sprzężenie emisji

Wybierz pozycję sprzężenie emisji lewej i/lub prawej strony, aby zażądać podajnika APD w celu wypchnięcia całego zestawu danych z obu stron relacji wyszukiwania do pamięci. W przypadku mniejszych zestawów danych może to znacznie poprawić wydajność wyszukiwania.

### <a name="data-partitioning"></a>Partycjonowanie danych

Możesz również określić Partycjonowanie danych, wybierając pozycję "Ustaw partycjonowanie" na karcie Optymalizacja transformacji wyszukiwania, aby utworzyć zestawy danych, które mogą być lepiej dopasowane do pamięci na proces roboczy.

## <a name="next-steps"></a>Następne kroki

* Przekształcenia [sprzężeń](data-flow-join.md) i [istniejących](data-flow-exists.md) są podobne do zadań w MAPOWANIU przepływów danych ADF. Zapoznaj się z tymi przekształceniami dalej.
* Użyj [podziału warunkowego](data-flow-conditional-split.md) z ```isMatch()```, aby podzielić wiersze na pasujące i niezgodne wartości
