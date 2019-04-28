---
title: Mapowanie transformacji filtru przepływu danych w usłudze Azure Data Factory
description: Mapowanie transformacji filtru przepływu danych w usłudze Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: d1751c47ad4507260d9f8d6ea44fcb32ed0e7338
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347433"
---
# <a name="azure-data-factoryfilter-transformation"></a>Przekształcenie FactoryFilter danych na platformie Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Przekształcenia filtru zapewnia filtrowanie wierszy. Twórz wyrażenia, który definiuje warunek filtru. Kliknij w polu tekstowym, aby uruchomić Kreatora wyrażeń. Wewnątrz Konstruktor wyrażeń kompilacji do kontroli wiersze, które z bieżącym strumienia danych mogą przechodzić przez (filtr) dalej transformację wyrażenie filtru. Pomyśl o przekształcenie filtru klauzuli WHERE instrukcji języka SQL.

## <a name="filter-on-loanstatus-column"></a>Filtruj według kolumny loan_status:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Odfiltruj kolumnę roku, w wersji demonstracyjnej filmów:

```
year > 1980
```

## <a name="next-steps"></a>Kolejne kroki

Kolumna filtrowania przekształcenia, spróbuj [wybierz transformacji](data-flow-select.md)
