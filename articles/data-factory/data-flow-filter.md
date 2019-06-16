---
title: Mapowanie transformacji filtru przepływu danych w usłudze Azure Data Factory
description: Mapowanie transformacji filtru przepływu danych w usłudze Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: e0b41850c149ff7095333cf77b780dec1f03b882
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66234412"
---
# <a name="azure-data-factory-filter-transformation"></a>Usługi Azure data factory Filtr przekształcania

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
