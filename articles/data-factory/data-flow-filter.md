---
title: Przekształcenie filtru przepływu danych Azure Data Factory mapowania
description: Przekształcenie filtru przepływu danych Azure Data Factory mapowania
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 2afe079c346a15ec212664ce022ac5e2926b12d4
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387808"
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
