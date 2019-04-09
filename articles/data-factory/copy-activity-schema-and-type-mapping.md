---
title: Mapowanie schematu w działaniu kopiowania | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu działania kopiowania w usłudze Azure Data Factory mapowania schematów i typy danych ze źródła danych do ujścia danych podczas kopiowania danych.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 99798b35419ec9574c99aaba42803fbeeb1555f1
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59267127"
---
# <a name="schema-mapping-in-copy-activity"></a>Mapowanie schematu w działaniu kopiowania
W tym artykule opisano, jakie działania kopiowania w usłudze Azure Data Factory mapowanie schematu i mapowanie typu danych ze źródła danych do ujścia danych podczas wykonywania kopii danych.

## <a name="column-mapping"></a>Mapowanie kolumn

Mapowanie kolumny ma zastosowanie, gdy kopiowanie danych między danych tabelarycznych w kształcie. Działanie kopiowania domyślnie **mapowania danych źródła do ujścia przy użyciu nazw kolumn**, chyba że [mapowania kolumn jawne](#explicit-column-mapping) jest skonfigurowany. W szczególności działania kopiowania:

1. Odczytywać dane ze źródła i określić schematu źródłowego

    * W przypadku źródeł danych przy użyciu wstępnie zdefiniowanego schematu w magazynie/format pliku danych, na przykład/pliki baz danych za pomocą metadanych (Avro/ORC/Parquet/tekstu z nagłówkiem), schematu źródła są wyodrębniane z metadanych pliku lub wynik zapytania.
    * W przypadku źródeł danych z elastycznym schematem, na przykład Azure tabeli/Cosmos DB schematu źródła jest wnioskowany z wyniku zapytania. Można go zmienić, konfigurując "strukturę" w zestawie danych.
    * Plik tekstowy bez nagłówka domyślne nazwy kolumn są generowane przy użyciu wzorca "Prop_0", "Prop_1"... Można go zmienić, konfigurując "strukturę" w zestawie danych.
    * Dla źródła Dynamics musisz podać informacje o schemacie w sekcji "strukturę" zestawu danych.

2. Mają zastosowanie mapowania kolumn jawne, jeśli określony.

3. Zapisywanie danych do ujścia

    * Dla magazynów danych za pomocą wstępnie zdefiniowany schemat dane są zapisywane do kolumn o takiej samej nazwie.
    * Magazyny danych bez stałego schematu i formatów plików nazwy/metadanych kolumn zostanie wygenerowany na podstawie schematu źródła.

### <a name="explicit-column-mapping"></a>Jawne mapowanie kolumn

Można określić **columnMappings** w **typeProperties** sekcji działaniem kopiowania, aby wykonać mapowania kolumn jawnego. W tym scenariuszu sekcji "strukturę" jest wymagana dla wejściowe i wyjściowe zestawy danych. Obsługuje mapowanie kolumny **mapowania wszystkich lub podzestawu kolumn w zestawie danych źródłowych "strukturę" do wszystkich kolumn w zestawie danych ujścia "strukturę"**. Dostępne są następujące warunki błędów, które powoduje wyjątek:

* Zapytanie, że wynik nie jest nazwa kolumny, które jest określone w sekcji "strukturę" wejściowy zestaw danych magazynu danych źródłowych.
* Magazyn danych ujścia (Jeśli za pomocą wstępnie zdefiniowany schemat) nie ma nazwę kolumny, który jest określony w sekcji "strukturę" wyjściowego zestawu danych.
* Mniejszą liczbę kolumn lub większą liczbę kolumn "Struktura" zestaw danych ujścia niż określony w mapowaniu.
* Zduplikowane mapowania.

#### <a name="explicit-column-mapping-example"></a>Jawne Przykładowe mapowanie kolumn

W tym przykładzie Tabela wejściowa ma strukturę i wskazuje do tabeli w bazie danych SQL w środowisku lokalnym.

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

W tym przykładzie tabela danych wyjściowych ma strukturę i wskazuje do tabeli w bazie danych SQL Azure.

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

Następujący kod JSON definiuje działania kopiowania w potoku. Kolumny ze źródła mapowane na kolumny w ujściu (**columnMappings**) przy użyciu **translator** właściwości.

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

Jeśli używasz składni `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` do określenia mapowania kolumn, nadal możliwe jest jako-to.

**Przepływ mapowania kolumn:**

![Przepływ mapowanie kolumn](./media/copy-activity-schema-and-type-mapping/column-mapping-sample.png)

## <a name="schema-mapping"></a>mapowanie schematu

Mapowanie schematu ma zastosowanie, gdy kopiowanie danych między hierarchiczne ukształtowane dane i tabelarycznym ukształtowane dane, np. kopiowanie danych z bazy danych MongoDB REST do pliku tekstowego i kopiowanie danych z programu SQL API usługi Azure Cosmos DB dla bazy danych MongoDB. Następujące właściwości są obsługiwane w działaniu kopiowania `translator` sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type translator aktywności kopiowania musi być równa: **TabularTranslator** | Yes |
| schemaMapping | Kolekcji pary klucz wartość, która reprezentuje relację mapowania **po stronie źródła do ujścia po stronie**.<br/>- **Klucz:** reprezentuje źródło. Aby uzyskać **tabelaryczne źródła**, określ nazwę kolumny, zgodnie z definicją w strukturze zestawu danych; w przypadku **źródła w hierarchiczny**, określa wyrażenie ścieżki JSON dla każdego pola wyodrębnić i mapy.<br/>- **Wartość:** reprezentuje ujścia. Dla **tabelarycznych ujścia**, określ nazwę kolumny, zgodnie z definicją w strukturze zestawu danych; w przypadku **hierarchiczne ujścia**, określa wyrażenie ścieżki JSON dla każdego pola wyodrębnić i mapy. <br/> W przypadku danych hierarchicznych, dla pola w obiekcie głównym ścieżkę JSON zaczyna się od głównego $; w przypadku pól wewnątrz tablicy wybranej przez `collectionReference` właściwości JSON ścieżka zaczyna się od elementu tablicy.  | Yes |
| collectionReference | Jeśli chcesz wykonać iterację i ekstrakcję danych z obiektów **wewnątrz pola tablicy** przy użyciu tego samego wzorca i przekonwertować każdego wiersza dla każdego obiektu, określ ścieżkę JSON tej tablicy cross-zastosować. Ta właściwość jest obsługiwana tylko wtedy, gdy źródło danych hierarchicznych. | Nie |

**Przykład: kopiowanie danych z bazy danych MongoDB do bazy danych SQL:**

Na przykład, jeśli masz bazy danych MongoDB dokumentów o następującej zawartości: 

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

i chcesz skopiować do tabeli usługi Azure SQL w następującym formacie, spłaszczając dane wewnątrz tablicy *(order_pd i order_price)* i cross join ze wspólnymi informacjami głównymi *(liczba, Data i miasta)* :

| orderNumber | orderDate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Skonfiguruj reguły mapowania schematu jako następujący przykładowy kod JSON aktywności kopiowania:

```json
{
    "name": "CopyFromMongoDBToSqlAzure",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "SqlSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "orderNumber": "$.number", 
                "orderDate": "$.date", 
                "order_pd": "prod", 
                "order_price": "price",
                "city": " $.city[0].name"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>Mapowanie typu danych

Działanie kopiowania wykonuje operację typów źródła do ujścia typy mapowania przy użyciu następujących podejść krok 2:

1. Konwertowanie na typy danych tymczasowych usługi Azure Data Factory typy natywne źródła
2. Konwertuj z typów danych tymczasowych usługi Azure Data Factory na typ ujścia natywne

Można znaleźć mapowania między typem natywnym typowi przejściowym w sekcji "Mapowanie typu danych" w każdym temacie łącznika.

### <a name="supported-data-types"></a>Obsługiwane typy danych

Usługa Data Factory obsługuje następujące typy danych tymczasowych: Możesz określić poniższe wartości podczas konfigurowania informacji o typie w [struktury zestawu danych](concepts-datasets-linked-services.md#dataset-structure) konfiguracji:

* Byte[]
* Wartość logiczna
* Data/godzina
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Single
* String
* Zakres czasu

### <a name="explicit-data-type-conversion"></a>Konwersja typu jawnego danych

Jeśli kopiowanie danych na dane przechowuje stały schemat, na przykład SQL Server/Oracle, gdy źródła i ujścia ma inny typ w tej samej kolumnie jawną konwersję typu powinien być zadeklarowany jako po stronie źródła:

* Dla pliku źródłowego na przykład, CSV/Avro konwersji typów są zgłaszane za pośrednictwem struktury źródła z listy kolumn pełny (po stronie kolumny nazwy i ujścia po stronie Typ źródła)
* Dla relacyjnego źródła (na przykład SQL/Oracle) należy osiągnąć konwersji typu rzutowania jawnego typu w instrukcji zapytania.

## <a name="when-to-specify-dataset-structure"></a>Kiedy należy określić strukturę"zestawu danych"

W poniższych scenariuszach "strukturę" w zestawie danych jest wymagane:

* Stosowanie [konwersja typu jawnego danych](#explicit-data-type-conversion) dla plikowych źródeł podczas kopiowania (wejściowy zestaw danych)
* Stosowanie [mapowania kolumn jawne](#explicit-column-mapping) podczas kopiowania (zarówno wejściowy i wyjściowy zestaw danych)
* Kopiowanie ze źródła Dynamics 365/CRM (wejściowy zestaw danych)
* Kopiowanie do usługi Cosmos DB jako obiekt zagnieżdżony, gdy źródłem nie są pliki JSON (wyjściowy zestaw danych)

W poniższych scenariuszach sugerowana jest "strukturę" w zestawie danych:

* Kopiowanie z pliku tekstowego, bez nagłówka (wejściowy zestaw danych). Można określić nazwy kolumn do pliku tekstowego, dopasowanie się do odpowiednich kolumn ujścia, można zapisać mapowania kolumn jawne Konfigurowanie.
* Kopiowanie danych są przechowywane z elastycznym schematem, na przykład Azure tabeli/Cosmos DB (wejściowy zestaw danych), aby zagwarantować oczekiwanych danych (kolumny), które są kopiowane zamiast kopiowania pozwalają działania wnioskowania dotyczącego schematu oparte na górnym wierszy podczas każdego uruchomienia działania.


## <a name="next-steps"></a>Kolejne kroki
Zobacz inne artykuły dotyczące działania kopiowania:

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Kopiuj działania odporności na uszkodzenia](copy-activity-fault-tolerance.md)
- [Wydajności działania kopiowania](copy-activity-performance.md)
