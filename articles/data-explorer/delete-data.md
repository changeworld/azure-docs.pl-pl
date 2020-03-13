---
title: Usuwanie danych z usługi Azure Eksplorator danych
description: W tym artykule opisano usuwanie scenariuszy w usłudze Azure Eksplorator danych, w tym przeczyszczanie, usuwanie zakresów i usuwanie danych przechowywanych na podstawie przechowywania.
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 681cfd71d2666630b192935d66ba32eaf16c92de
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204617"
---
# <a name="delete-data-from-azure-data-explorer"></a>Usuwanie danych z usługi Azure Eksplorator danych

Usługa Azure Eksplorator danych obsługuje różne scenariusze usuwania opisane w tym artykule. 

## <a name="delete-data-using-the-retention-policy"></a>Usuwanie danych przy użyciu zasad przechowywania

Usługa Azure Eksplorator danych automatycznie usuwa dane na podstawie [zasad przechowywania](/azure/kusto/management/retentionpolicy). Ta metoda to najbardziej wydajny i nieefektywny sposób usuwania danych. Ustaw zasady przechowywania na poziomie bazy danych lub tabeli.

Rozważ użycie bazy danych lub tabeli, która jest ustawiona na 90 dni przechowywania. Jeśli potrzebne są tylko 60 dni, Usuń starsze dane w następujący sposób:

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>Usuń dane poprzez upuszczenie zakresów

[Zakres (Data fragmentu)](/azure/kusto/management/extents-overview) to wewnętrzna struktura, w której są przechowywane dane. Każdy zakres może zawierać do milionów rekordów. Zakresy można usuwać pojedynczo lub jako grupę przy użyciu [poleceń usuwania zakresów](/azure/kusto/management/extents-commands#drop-extents). 

### <a name="examples"></a>Przykłady

Można usunąć wszystkie wiersze w tabeli lub tylko określony zakres.

* Usuń wszystkie wiersze w tabeli:

    ```kusto
    .drop extents from TestTable
    ```

* Usuń określony zakres:

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>Usuwanie pojedynczych wierszy przy użyciu przeczyszczania

[Przeczyszczanie danych](/azure/kusto/management/data-purge) może służyć do usuwania pojedynczych wierszy. Usuwanie nie jest natychmiastowe i wymaga znaczących zasobów systemowych. W związku z tym jest zalecane tylko w przypadku scenariuszy zgodności.  

