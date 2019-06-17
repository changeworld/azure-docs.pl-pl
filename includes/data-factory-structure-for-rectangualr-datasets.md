---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 1ab404b838af65dcb75395dfeee1ca0553e497a1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66122523"
---
## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Określanie definicji struktury dla prostokątnych zestawów danych
Sekcja struktury w zestawach danych JSON jest **opcjonalne** sekcji prostokątny tabel (wiersze i kolumny) i zawiera kolekcję kolumn dla tabeli. W sekcji struktury użyje albo udostępnianie informacji o typie dla konwersji typu lub sposób mapowania kolumn. W poniższych sekcjach opisano te funkcje szczegółowo. 

Każda kolumna zawiera następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| name |Nazwa kolumny. |Tak |
| type |Typ danych kolumny. Zobacz więcej szczegółów typu konwersje sekcji poniżej dotyczącą tego, możesz określić informacje o typie |Nie |
| culture |Kultura ma być używany, gdy typ jest określony, a typ architektury .NET, Datetime i Datetimeoffset oparte na platformie .NET. Wartość domyślna to "en-us". |Nie |
| format |Ciąg formatu, które ma być używany, gdy typ jest określony, a .NET typu Datetime i Datetimeoffset. |Nie |

Poniższy przykład pokazuje sekcji strukturze JSON dla tabeli, która ma trzy kolumny userid, nazwę oraz lastlogindate.

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

Użyj następujących wytycznych dla umieszczania "strukturę" informacji i co należy uwzględnić w **struktury** sekcji.

* **W przypadku źródeł danych ze strukturą** czy Magazyn informacji schematu i typu danych wraz z danymi, sama (źródeł, takich jak SQL Server, Oracle, tabela platformy Azure itp.), należy określić w sekcji "strukturę" tylko wtedy, gdy chcesz wykonać mapowania kolumn z określonego źródła kolumny do określonych kolumn w ujścia i ich nazwy nie są takie same (szczegółowe informacje w poniższej sekcji mapowania kolumn). 
  
    Jak wspomniano powyżej, informacje o typie jest opcjonalny w sekcji "strukturę". W przypadku strukturalnych źródeł informacji o typie jest już dostępny w ramach definicji zestawu danych w magazynie danych, dlatego nie należy używać informacji o typie przy dodawaniu w sekcji "strukturę".
* **Dla schematu do źródeł danych odczytu (w szczególności obiektów blob platformy Azure)** istnieje możliwość przechowywania danych bez przechowywania żadnych informacji schematu lub typu z danymi. Dla tych typów źródeł danych powinien zawierać "strukturę" w następujących przypadkach 2:
  * Chcesz wykonać mapowania kolumn.
  * Gdy zestaw danych jest źródłem w działaniu kopiowania, możesz podać informacje o typie w "strukturę" i usługi data factory użyje informacji o tym typie do konwersji na typy natywne ujścia. Zobacz [przenoszenie danych do i z usługi Azure Blob](../articles/data-factory/v1/data-factory-azure-blob-connector.md) artykuł, aby uzyskać więcej informacji.

### <a name="supported-net-based-types"></a>Obsługiwane. Typy sieci
Usługi Data factory obsługuje następujące zgodny ze specyfikacją zgodne .NET na podstawie typu wartości udostępnienie informacji o typie w "structure" dla schematu na dane odczytu źródeł, takich jak usługi Azure blob.

* Int16
* Int32 
* Int64
* Single
* Double
* Decimal
* Byte[]
* Bool
* String 
* Guid
* Datetime
* Datetimeoffset
* Zakres czasu 

Dla typu Datetime i Datetimeoffset również Opcjonalnie możesz określić ciąg "kultury" & "format" ułatwiają analizowanie niestandardowego ciągu daty/godziny. Zobacz przykład poniżej konwersji typu.

