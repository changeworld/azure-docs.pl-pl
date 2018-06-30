---
title: Mapowanie schematu w przypadku działania kopiowania | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu działania kopiowania w fabryce danych Azure mapowania typów schematów i dane ze źródła danych do zbiornika danych podczas kopiowania danych.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: jingwang
ms.openlocfilehash: 16275ddc4d4ad85bdac54244ceeec568603fdfef
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112103"
---
# <a name="schema-mapping-in-copy-activity"></a>Mapowanie schematu w przypadku działania kopiowania
W tym artykule opisano, jak aktywność kopiowania fabryki danych Azure mapowania schematu i mapowanie typu danych ze źródła danych do zbiornika danych kiedy wykonać kopię danych.

## <a name="column-mapping"></a>Mapowanie kolumny

Domyślnie aktywności kopiowania **mapy źródła danych do zbiornika według nazwy kolumn**, chyba że [mapowanie kolumn jawne](#explicit-column-mapping) jest skonfigurowany. W szczególności działania kopiowania:

1. Odczytywanie danych z źródła i ustalić schematu źródła

    * Dla źródeł danych za pomocą wstępnie zdefiniowanych schematów w magazynie/format pliku danych, na przykład/pliki baz danych z metadanymi (Avro/ORC/Parquet/tekstu z nagłówkiem), schematu źródła są wyodrębniane z metadanych pliku lub wyniku zapytania.
    * Dla źródeł danych ze schematem elastyczne, na przykład Azure tabeli/rozwiązania Cosmos DB schematu źródła jest wywnioskowany na podstawie wyniku zapytania. Można go zmienić, zapewniając "structure" w zestawie danych.
    * Plik tekstowy bez nagłówka domyślne nazwy kolumn są generowane z wzorcem "Prop_0", "Prop_1"... Można go zmienić, zapewniając "structure" w zestawie danych.
    * Dynamics źródłowej musisz podać informacje o schemacie w sekcji "structure" zestawu danych.

2. Zastosowania mapowanie kolumn jawne, jeśli określony.

3. Zapisu danych do zbiornika

    * Dla magazynów danych ze schematem wstępnie zdefiniowane dane są zapisywane do kolumn o takiej samej nazwie.
    * Dla magazynów danych bez stałego schematu i formatów plików nazwy kolumny/metadane zostaną wygenerowane na podstawie schematu źródła.

### <a name="explicit-column-mapping"></a>Mapowanie kolumny jawne

Można określić **columnMappings** w **typeProperties** sekcji działanie kopiowania do mapowania kolumn jawnego. W tym scenariuszu sekcja "structure" jest wymagana dla wejścia i wyjścia zestawów danych. Obsługuje mapowania kolumn **mapowania wszystkie lub podzbiór kolumn w zestawie danych "structure" dla wszystkich kolumn w zestawie danych "structure" sink źródła**. Poniżej znajdują się błędy, które powodują powstanie wyjątku:

* Źródło danych magazynu zapytań, że wynik nie jest nazwa kolumny, które jest określone w sekcji "structure" zestawu danych wejściowych.
* Ujścia magazynu danych (Jeśli za pomocą wstępnie zdefiniowanych schematów) nie ma nazwy kolumny, które jest określone w sekcji "structure" zestawu danych wyjściowych.
* Mniejszą liczbę kolumn lub większą liczbę kolumn w "strukturze" sink zestawu danych niż określona w mapowaniu.
* Zduplikowane mapowania.

#### <a name="explicit-column-mapping-example"></a>Przykładowe mapowanie kolumny jawne

W tym przykładzie Tabela wejściowa ma strukturę i wskazuje tabelę w lokalnej bazie danych SQL.

```json
{
    "name": "SqlServerInput",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "SqlServerLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

W tym przykładzie tabela wyjściowa ma strukturę i wskazuje tabelę w bazie danych SQL Azure.

```json
{
    "name": "AzureSqlOutput",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

Następujące JSON definiuje działanie kopiowania w potoku. Kolumny źródłowej zamapowany na kolumny w ujściu (**columnMappings**) przy użyciu **translator** właściwości.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "SqlServerInput",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "AzureSqlOutput",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "SqlSource" },
        "sink": { "type": "SqlSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings": 
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

Przy użyciu składni `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` Aby określić mapowanie kolumny, jest nadal obsługiwane jako — jest.

**Przepływ mapowanie kolumn:**

![Przepływ mapowania kolumn](./media/copy-activity-schema-and-type-mapping/column-mapping-sample.png)

## <a name="data-type-mapping"></a>Mapowanie typu danych

Działanie kopiowania wykonuje typy źródeł do zbiornika typy mapowanie, uwzględniając następujące podejście krok 2:

1. Konwertowanie typów natywnych źródła na typy danych tymczasowych fabryki danych Azure
2. Konwertowanie typów danych tymczasowych fabryki danych Azure na typ ujścia natywnego

Mapowanie między natywny typ tymczasowe w sekcji "Mapowanie typu danych" w temacie każdy łącznik można znaleźć.

### <a name="supported-data-types"></a>Obsługiwane typy danych

Fabryka danych obsługuje następujące typy danych tymczasowych: można określić poniżej wartości podczas dostarczania informacji o typie w [struktury zestawu danych](concepts-datasets-linked-services.md#dataset-structure) konfiguracji:

* Byte[]
* Wartość logiczna
* Data/godzina
* Datetimeoffset
* Decimal
* podwójne
* Identyfikator GUID
* Int16
* Int32
* Int64
* Pojedyncze
* Ciąg
* Zakres czasu

### <a name="explicit-data-type-conversion"></a>Konwersja typu jawnego danych

Jeśli kopiowanie danych na dane przechowuje ze schematem stałej, na przykład serwer SQL/Oracle, gdy źródłowy i odbiorczy ma inny typ na tej samej kolumnie, konwersja typu jawnego powinny zostać zadeklarowane w źródłowej strony:

* Dla pliku źródłowego na przykład, CSV/Avro konwersji typu są zgłaszane za pośrednictwem struktury źródła z listy kolumn Pełna (po stronie Typ kolumny źródłowej nazwy i ujście po stronie)
* Dla relacyjnego źródła (na przykład SQL/Oracle) należy osiągnąć konwersja typu jawnego typu rzutowania w instrukcji zapytania.

## <a name="when-to-specify-dataset-structure"></a>Gdy do określenia zestawu danych "structure"

W poniższych scenariuszach "structure" w zestawie danych nie jest wymagana:

* Stosowanie [konwersja typu jawnego danych](#explicit-data-type-conversion) dla źródeł dla plików podczas kopiowania (wejściowy zestaw danych)
* Stosowanie [mapowanie kolumn jawne](#explicit-column-mapping) podczas kopiowania (zarówno wejściowy i wyjściowy zestaw danych)
* Kopiowanie z Dynamics 365 / CRM źródła (wejściowy zestaw danych)
* Kopiowanie do rozwiązania Cosmos bazy danych jako zagnieżdżony obiekt, gdy źródło nie jest pliki w formacie JSON (wyjściowy zestaw danych)

W poniższych scenariuszach, zalecane jest "structure" w zestawie danych:

* Kopiowanie z pliku tekstowego bez nagłówka (wejściowy zestaw danych). Można określić nazwy kolumn dopasowanie się na odpowiednie kolumny odbioru można zapisać zapewnienie mapowanie jawne kolumny pliku tekstowego.
* Kopiowanie danych przechowuje ze schematem elastyczne, na przykład bazy danych Azure tabeli/rozwiązania Cosmos (wejściowy zestaw danych), aby zagwarantować oczekiwanych danych (kolumny), które są kopiowane zamiast kopiowania let działania wnioskowania dotyczącego schematu oparte na najwyższym wiersze podczas każdego uruchamiania działania.


## <a name="next-steps"></a>Kolejne kroki
Zobacz inne artykuły działania kopiowania:

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Kopiuj działania odporność na uszkodzenia](copy-activity-fault-tolerance.md)
- [Wydajności działania kopiowania](copy-activity-performance.md)
