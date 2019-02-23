---
title: Mapowanie transformacji filtru przepływu danych w usłudze Azure Data Factory
description: Mapowanie transformacji filtru przepływu danych w usłudze Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 1930ca7406b6ef1b9fd20e1651bc031150dd952e
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729547"
---
# <a name="azure-data-factory-mapping-data-flow-filter-transformation"></a>Mapowanie transformacji filtru przepływu danych w usłudze Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Przekształcenia filtru zapewnia filtrowanie wierszy. Twórz wyrażenia, który definiuje warunek filtru. Kliknij w polu tekstowym, aby uruchomić Kreatora wyrażeń. Wewnątrz Konstruktor wyrażeń kompilacji do kontroli wiersze, które z bieżącym strumienia danych mogą przechodzić przez (filtr) dalej transformację wyrażenie filtru.

Czyli filtru w kolumnie loan_status:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Odfiltruj kolumnę roku, w wersji demonstracyjnej filmów:

```
year > 1980
```
