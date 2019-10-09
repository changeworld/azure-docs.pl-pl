---
title: Przekształcenie filtru przepływu danych Azure Data Factory mapowania
description: Przekształcenie filtru przepływu danych Azure Data Factory mapowania
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: dd29753ff14d16081a46eebbc2ea02d94e5985f1
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029358"
---
# <a name="azure-data-factory-filter-transformation"></a>Przekształcanie filtrów usługi Azure Data Factory



Przekształcenia filtru zapewniają filtrowanie wierszy. Utwórz wyrażenie definiujące warunek filtru. Kliknij w polu tekstowym, aby uruchomić Konstruktora wyrażeń. Wewnątrz konstruktora wyrażeń Utwórz wyrażenie filtru, aby określić, które wiersze z bieżącego strumienia danych mogą być przekazywane (filtrowanie) do następnej transformacji. Przekształcenie filtru należy traktować jako klauzulę WHERE instrukcji SQL.

## <a name="filter-on-loan_status-column"></a>Filtruj według kolumny loan_status:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Filtrowanie w kolumnie Year w pokazie filmów:

```
year > 1980
```

## <a name="next-steps"></a>Następne kroki

Wypróbuj transformację filtrowania kolumn, [przekształcenie SELECT](data-flow-select.md)
