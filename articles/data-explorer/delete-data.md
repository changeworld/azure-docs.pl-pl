---
title: Usuwanie danych z Eksploratora danych platformy Azure
description: W tym artykule opisano usuwanie scenariuszy w Eksploratorze danych platformy Azure, w tym przeczyszczanie, upuszczanie zakresów i usuwania na podstawie przechowywania.
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: dd0f8740d148a7817bcfe2fbad591ceeb1610d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501414"
---
# <a name="delete-data-from-azure-data-explorer"></a>Usuwanie danych z Eksploratora danych platformy Azure

Usługa Azure Data Explorer obsługuje różne scenariusze usuwania opisane w tym artykule. 

## <a name="delete-data-using-the-retention-policy"></a>Usuwanie danych przy użyciu zasad przechowywania

Usługa Azure Data Explorer automatycznie usuwa dane na podstawie [zasad przechowywania](/azure/kusto/management/retentionpolicy). Ta metoda jest najbardziej wydajnym i bezproblemowym sposobem usuwania danych. Ustaw zasady przechowywania na poziomie bazy danych lub tabeli.

Należy wziąć pod uwagę bazy danych lub tabeli, która jest ustawiona na 90 dni przechowywania. Jeśli potrzebne są tylko 60 dni danych, usuń starsze dane w następujący sposób:

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>Usuwanie danych przez upuszczanie zakresów

[Extent (fragment danych)](/azure/kusto/management/extents-overview) jest wewnętrzną strukturą, w której przechowywane są dane. Każdy zakres może pomieścić do milionów rekordów. Zakresy można usuwać pojedynczo lub jako grupa za pomocą [poleceń zasięgu upuszczania](/azure/kusto/management/extents-commands#drop-extents). 

### <a name="examples"></a>Przykłady

Można usunąć wszystkie wiersze w tabeli lub tylko w określonym zakresie.

* Usuń wszystkie wiersze w tabeli:

    ```kusto
    .drop extents from TestTable
    ```

* Usuń określony zakres:

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>Usuwanie pojedynczych wierszy przy użyciu przeczyszczać

[Przeczyszczanie danych](/azure/kusto/concepts/data-purge) może służyć do usuwania wierszy osób. Usunięcie nie jest natychmiastowe i wymaga znacznych zasobów systemowych. W związku z tym jest zalecane tylko dla scenariuszy zgodności.  

