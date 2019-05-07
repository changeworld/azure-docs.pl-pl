---
title: Obsługiwane formaty plików w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: W tym temacie opisano formaty plików i kody kompresji, które są obsługiwane przez łączników opartych na plikach w usłudze Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 6af5c8cdad00802052f08be844f77d36420da3c9
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142369"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Obsługiwane formaty plików i kompresji koderów-dekoderów w usłudze Azure Data Factory

*W tym artykule mają zastosowanie następujące łączniki: [Amazon S3](connector-amazon-simple-storage-service.md), [obiektów Blob platformy Azure](connector-azure-blob-storage.md), [usługi Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [usługi Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [usługi Azure File Storage](connector-azure-file-storage.md), [System plików](connector-file-system.md), [FTP](connector-ftp.md), [usługi Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)i [ SFTP](connector-sftp.md).*

Jeśli chcesz **skopiuj pliki — jest** między opartych na plikach magazynów (kopia binarna), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. Jeśli chcesz **analizować lub generowanie plików w określonym formacie**, Azure Data Factory obsługuje następujące typy formatów plików:

* [Format tekstu](#text-format)
* [JSON format](#json-format)
* [Parquet format](#parquet-format)
* [ORC format](#orc-format)
* [Avro format](#avro-format)

> [!TIP]
> Dowiedz się, jak działanie kopiowania mapuje dane źródła do ujścia z [mapowanie schematu w działaniu kopiowania](copy-activity-schema-and-type-mapping.md), w tym jak metadanych jest określone w oparciu o ustawienia formatu pliku i porady w tym, kiedy należy określić [dataset `structure` ](concepts-datasets-linked-services.md#dataset-structure-or-schema) sekcji.

## <a name="text-format"></a>Format tekstu

>[!NOTE]
>Data Factory wprowadzono nowe rozdzielany datset format tekstu, zobacz [format tekstu rozdzielanego](format-delimited-text.md) artykułu zawierającego szczegóły. Następujące konfiguracje dla zestawu danych magazynu danych oparte na plikach jest nadal obsługiwany jako — dotyczy compabitility z poprzednimi wersjami. Zaleca się używać nowego modelu, w przyszłości.

Jeśli chcesz odczytać z pliku tekstowego lub zapisać do pliku tekstowego, ustaw `type` właściwość `format` części zestawu danych na **TextFormat**. Ponadto możesz określić następujące **opcjonalne** właściwości w sekcji `format`. Aby uzyskać informacje na temat sposobu konfigurowania, zobacz sekcję [Przykład formatu TextFormat](#textformat-example).

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| columnDelimiter |Znak używany do rozdzielania kolumn w pliku. Aby wziąć pod uwagę do użycia rzadkich znak niedrukowalny, który nie istnieje w danych. Na przykład określić "\u0001", który reprezentuje Start z nagłówkiem (raportu o kondycji). |Dozwolony jest tylko jeden znak. Wartość **domyślna** to **przecinek (,)**. <br/><br/>Aby użyć znaku Unicode, zobacz [znaków Unicode](https://en.wikipedia.org/wiki/List_of_Unicode_characters) można uzyskać odpowiedni kod. |Nie |
| rowDelimiter |Znak używany do rozdzielania wierszy w pliku. |Dozwolony jest tylko jeden znak. Wartością **domyślną** jest dowolna z następujących wartości przy odczycie: **[„\r\n”, „\r”, „\n”]** oraz wartość **„\r\n”** przy zapisie. |Nie |
| escapeChar |Znak specjalny służący do zmiany interpretacji ogranicznika kolumny w zawartości pliku wejściowego. <br/><br/>W przypadku tabeli nie można określić zarówno właściwości escapeChar, jak i quoteChar. |Dozwolony jest tylko jeden znak. Brak wartości domyślnej. <br/><br/>Przykład: Jeśli masz przecinkiem (', '), jak ogranicznik kolumny, ale ma mieć znak przecinka występował w tekście (przykład: "Hello, world"), można zdefiniować "$" jako znak ucieczki i użyć ciągu "Witaj$, world" w źródle. |Nie |
| quoteChar |Znak używany do umieszczania wartości ciągu w cudzysłowie. Ograniczniki kolumny i wiersza umieszczone w cudzysłowie są traktowane jako część wartości ciągu. Ta właściwość ma zastosowanie zarówno do wejściowych, jak i wyjściowych zestawów danych.<br/><br/>W przypadku tabeli nie można określić zarówno właściwości escapeChar, jak i quoteChar. |Dozwolony jest tylko jeden znak. Brak wartości domyślnej. <br/><br/>Na przykład jeśli ogranicznikiem kolumny jest przecinek (,), ale chcesz, aby znak przecinka występował w tekście (przykład: <Witaj, świecie>), możesz zdefiniować cudzysłów (") jako znak cudzysłowu i użyć ciągu "Witaj, świecie" w źródle. |Nie |
| nullValue |Co najmniej jeden znak służący do reprezentowania wartości null. |Co najmniej jeden znak. Wartości **domyślne** to **„\N” i „NULL”** przy odczycie oraz **„\N”** przy zapisie. |Nie |
| encodingName |Określa nazwę kodowania. |Prawidłowa nazwa kodowania. Zobacz [właściwość Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Przykład: windows-1250 lub shift_jis. Wartość **domyślna** to **UTF-8**. |Nie |
| firstRowAsHeader |Określa, czy pierwszy wiersz ma być traktowany jako nagłówek. W przypadku zestawu danych wejściowych usługa Data Factory odczytuje pierwszy wiersz jako nagłówek. W przypadku zestawu danych wyjściowych usługa Data Factory zapisuje pierwszy wiersz jako nagłówek. <br/><br/>Aby uzyskać przykładowe scenariusze, zobacz sekcję [Scenariusze użycia właściwości `firstRowAsHeader` oraz `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |True<br/><b>False (domyślnie)</b> |Nie |
| skipLineCount |Wskazuje liczbę **niepuste** wierszy do pominięcia podczas odczytywania danych z plików wejściowych. Jeśli określono zarówno właściwość skipLineCount, jak i firstRowAsHeader, najpierw zostaną pominięte wiersze, a następnie zostaną odczytane informacje nagłówka z pliku wejściowego. <br/><br/>Aby uzyskać przykładowe scenariusze, zobacz sekcję [Scenariusze użycia właściwości `firstRowAsHeader` oraz `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |Liczba całkowita |Nie |
| treatEmptyAsNull |Określa, czy ciąg pusty lub o wartości null ma być traktowany jako wartość null podczas odczytu danych z pliku wejściowego. |**True (domyślnie)**<br/>False |Nie |

### <a name="textformat-example"></a>Przykład formatu TextFormat

W poniższej definicji JSON dla zestawu danych podano części właściwości opcjonalnych.

```json
"typeProperties":
{
    "folderPath": "mycontainer/myfolder",
    "fileName": "myblobname",
    "format":
    {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";",
        "quoteChar": "\"",
        "NullValue": "NaN",
        "firstRowAsHeader": true,
        "skipLineCount": 0,
        "treatEmptyAsNull": true
    }
},
```

Aby użyć właściwości `escapeChar` zamiast `quoteChar`, zastąp wiersz z właściwością `quoteChar` następującą właściwością escapeChar:

```json
"escapeChar": "$",
```

### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Scenariusze użycia właściwości firstRowAsHeader oraz skipLineCount

* Kopiujesz dane ze źródła innego niż plik do pliku tekstowego i chcesz dodać wiersz nagłówka zawierający metadane schematu (na przykład: SQL schema). Ustaw właściwość `firstRowAsHeader` na wartość true w zestawie danych wyjściowych dla tego scenariusza.
* Kopiujesz dane z pliku tekstowego zawierającego wiersz nagłówka do ujścia innego niż plik i chcesz pominąć ten wiersz. Ustaw właściwość `firstRowAsHeader` na wartość true w zestawie danych wejściowych.
* Kopiujesz dane z pliku tekstowego i chcesz pominąć kilka początkowych wierszy, które nie zawierają żadnych danych bądź informacji nagłówka. Określ właściwość `skipLineCount`, aby wskazać liczbę wierszy do pominięcia. Jeśli pozostała część pliku zawiera wiersz nagłówka, możesz również określić właściwość `firstRowAsHeader`. Jeśli określono zarówno właściwość `skipLineCount`, jak i `firstRowAsHeader`, najpierw zostaną pominięte wiersze, a następnie zostaną odczytane informacje nagłówka z pliku wejściowego

## <a name="json-format"></a>JSON format

Do **importu/eksportu plik w formacie JSON jako — importowanie / z usługi Azure Cosmos DB**, zawiera sekcja dokumentów JSON Import/export w [przenoszenie danych do i z usługi Azure Cosmos DB](connector-azure-cosmos-db.md) artykułu.

Jeśli chcesz analizować pliki JSON lub zapisywać dane w formacie JSON, ustaw `type` właściwość `format` sekcji **JsonFormat**. Ponadto możesz określić następujące **opcjonalne** właściwości w sekcji `format`. Aby uzyskać informacje na temat sposobu konfigurowania, zobacz sekcję [Przykład formatu JsonFormat](#jsonformat-example).

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| filePattern |Wskazuje wzorzec danych przechowywanych w każdym pliku JSON. Dozwolone wartości to: **setOfObjects** i **arrayOfObjects**. Wartością **domyślną** jest **setOfObjects**. Aby uzyskać szczegółowe informacje o tych wzorcach, zobacz sekcję [Wzorce plików JSON](#json-file-patterns). |Nie |
| jsonNodeReference | Jeśli chcesz wykonać iterację i ekstrakcję danych z obiektów wewnątrz pola tablicy o tym samym wzorcu, określ ścieżkę JSON tej tablicy. Ta właściwość jest obsługiwana tylko podczas kopiowania danych **z** pliki w formacie JSON. | Nie |
| jsonPathDefinition | Określa wyrażenie ścieżki JSON dla każdego mapowania kolumny z niestandardową nazwą kolumny (musi zaczynać się małą literą). Ta właściwość jest obsługiwana tylko podczas kopiowania danych **z** pliki w formacie JSON i wyodrębnianie danych z obiektu lub tablicy. <br/><br/> W przypadku pól obiektu głównego na początku użyj elementu głównego $. W przypadku pól wewnątrz tablicy wybranej przez właściwość `jsonNodeReference` najpierw podaj element tablicy. Aby uzyskać informacje na temat sposobu konfigurowania, zobacz sekcję [Przykład formatu JsonFormat](#jsonformat-example). | Nie |
| encodingName |Określa nazwę kodowania. Aby uzyskać listę prawidłowych nazw kodowania zobacz: [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) właściwości. Na przykład: windows-1250 lub shift_jis. **Domyślne** wartość to: **UTF-8**. |Nie |
| nestingSeparator |Znak używany do rozdzielania poziomów zagnieżdżenia. Wartość domyślna to „.” (kropka). |Nie |

>[!NOTE]
>W przypadku krzyżowe stosowanie danych w tablicy na wiele wierszy (przypadek 1 -> przykład 2 w [przykłady JsonFormat](#jsonformat-example)) rozwinąć pojedynczą tablicę przy użyciu właściwości można wybrać tylko `jsonNodeReference`.

### <a name="json-file-patterns"></a>Wzorce plików JSON

Działanie kopiowania może przeanalizować poniższe wzorce plików JSON:

- **Typ I: setOfObjects**

    Każdy plik zawiera pojedynczy obiekt lub wiele obiektów rozdzielonych wierszami bądź połączonych. W przypadku wybrania tej opcji w zestawie danych wyjściowych działanie kopiowania tworzy pojedynczy plik JSON z każdym obiektem w osobnym wierszu (rozdzielanie wierszami).

    * **przykład kodu JSON z pojedynczym obiektem**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **przykład kodu JSON z obiektami rozdzielonymi wierszami**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **przykład kodu JSON z obiektami połączonymi**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **Typ II: arrayOfObjects**

    Każdy plik zawiera tablicę obiektów.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

### <a name="jsonformat-example"></a>Przykład formatu JsonFormat

**Przypadek 1: Kopiowanie danych z plików JSON**

**Przykład 1. Wyodrębnianie danych z obiektu i tablicy**

W tym przykładzie oczekiwany jest jeden główny obiekt JSON mapowany na pojedynczy rekord w wyniku tabelarycznym. Jeśli masz plik JSON z następującą zawartością:

```json
{
    "id": "ed0e4960-d9c5-11e6-85dc-d7996816aad3",
    "context": {
        "device": {
            "type": "PC"
        },
        "custom": {
            "dimensions": [
                {
                    "TargetResourceType": "Microsoft.Compute/virtualMachines"
                },
                {
                    "ResourceManagementProcessRunId": "827f8aaa-ab72-437c-ba48-d8917a7336a3"
                },
                {
                    "OccurrenceTime": "1/13/2017 11:24:37 AM"
                }
            ]
        }
    }
}
```

i chcesz skopiować ją do tabeli usługi Azure SQL w następującym formacie przez wyodrębnienie danych z obiektu i tabeli:

| ID | deviceType | targetResourceType | resourceManagementProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | PC | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 1/13/2017 11:24:37 AM |

Zestaw danych wejściowych typu **JsonFormat** jest zdefiniowany następująco: (częściowa definicja zawierająca tylko stosowne fragmenty). Więcej szczegółów:

- Sekcja `structure` definiuje niestandardowe nazwy kolumn i odpowiedni typ danych podczas konwersji na dane tabelaryczne. Ta sekcja jest **opcjonalna**, o ile nie trzeba wykonać mapowania kolumn. Aby uzyskać więcej informacji, zobacz [mapowanie kolumny zestawu danych źródła do docelowego zestawu danych kolumn](copy-activity-schema-and-type-mapping.md).
- Właściwość `jsonPathDefinition` określa ścieżkę JSON dla każdej kolumny, wskazując, skąd mają zostać wyodrębnione dane. Aby skopiować dane z tablicy, możesz użyć `array[x].property` do wyodrębnienia wartości danej właściwości z `xth` obiektu, lub użyć `array[*].property` do odnalezienia wartości z dowolnych obiektów zawierających taką właściwość.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "deviceType",
            "type": "String"
        },
        {
            "name": "targetResourceType",
            "type": "String"
        },
        {
            "name": "resourceManagementProcessRunId",
            "type": "String"
        },
        {
            "name": "occurrenceTime",
            "type": "DateTime"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonPathDefinition": {"id": "$.id", "deviceType": "$.context.device.type", "targetResourceType": "$.context.custom.dimensions[0].TargetResourceType", "resourceManagementProcessRunId": "$.context.custom.dimensions[1].ResourceManagementProcessRunId", "occurrenceTime": " $.context.custom.dimensions[2].OccurrenceTime"}
        }
    }
}
```

**Przykład 2. Krzyżowe stosowanie tego samego wzorca z tabeli do wielu obiektów**

W tym przykładzie oczekiwane jest przetransformowanie jednego głównego obiektu JSON na wiele rekordów w wyniku tabelarycznym. Jeśli masz plik JSON z następującą zawartością:

```json
{
    "ordernumber": "01",
    "orderdate": "20170122",
    "orderlines": [
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
    "city": [ { "sanmateo": "No 1" } ]
}
```

i chcesz ją skopiować do tabeli Azure SQL w następującym formacie, spłaszczając dane wewnątrz tablicy i łącząc je krzyżowo ze wspólnymi informacjami głównymi:

| `ordernumber` | `orderdate` | `order_pd` | `order_price` | `city` |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P2 | 13 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P3 | 231 | `[{"sanmateo":"No 1"}]` |


Zestaw danych wejściowych typu **JsonFormat** jest zdefiniowany następująco: (częściowa definicja zawierająca tylko stosowne fragmenty). Więcej szczegółów:

- Sekcja `structure` definiuje niestandardowe nazwy kolumn i odpowiedni typ danych podczas konwersji na dane tabelaryczne. Ta sekcja jest **opcjonalna**, o ile nie trzeba wykonać mapowania kolumn. Aby uzyskać więcej informacji, zobacz [mapowanie kolumny zestawu danych źródła do docelowego zestawu danych kolumn](copy-activity-schema-and-type-mapping.md).
- `jsonNodeReference` Wskazuje, iteracja i ekstrakcja danych z obiektów o tym samym wzorcem, w obszarze **tablicy** `orderlines`.
- Właściwość `jsonPathDefinition` określa ścieżkę JSON dla każdej kolumny, wskazując, skąd mają zostać wyodrębnione dane. W tym przykładzie `ordernumber`, `orderdate`, i `city` są w obiekcie głównym przy użyciu formatu JSON ścieżki począwszy od `$.`, podczas gdy `order_pd` i `order_price` są definiowane przy użyciu ścieżki pochodzącej od elementu tablicy bez `$.` .

```json
"properties": {
    "structure": [
        {
            "name": "ordernumber",
            "type": "String"
        },
        {
            "name": "orderdate",
            "type": "String"
        },
        {
            "name": "order_pd",
            "type": "String"
        },
        {
            "name": "order_price",
            "type": "Int64"
        },
        {
            "name": "city",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonNodeReference": "$.orderlines",
            "jsonPathDefinition": {"ordernumber": "$.ordernumber", "orderdate": "$.orderdate", "order_pd": "prod", "order_price": "price", "city": " $.city"}
        }
    }
}
```

**Pamiętaj o następujących kwestiach:**

* Jeśli sekcja `structure` i właściwość `jsonPathDefinition` nie są zdefiniowane w zestawie danych usługi Data Factory, działanie kopiowania wykrywa schemat z pierwszego obiektu i spłaszcza cały obiekt.
* Jeśli dane wejściowe JSON zawierają tablicę, działanie kopiowania domyślnie konwertuje całą wartość tablicy na ciąg. Możesz wyodrębnić z niej dane przy użyciu właściwości `jsonNodeReference` i/lub `jsonPathDefinition` bądź ją pominąć, nie określając jej we właściwości `jsonPathDefinition`.
* Jeśli na tym samym poziomie występują zduplikowane nazwy, działanie kopiowania wybierze ostatnią z nich.
* W przypadku nazw właściwości wielkość liter ma znaczenie. Dwie właściwości o takiej samej nazwie, ale zapisanej przy użyciu różnej wielkości liter, są traktowane jako dwie osobne właściwości.

**Przypadek 2: Zapisywanie danych w pliku JSON**

Jeśli masz poniższej tabeli w bazie danych SQL:

| ID | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

i dla każdego rekordu będziesz zapisywać w obiekcie JSON w następującym formacie:

```json
{
    "id": "1",
    "order": {
        "date": "20170119",
        "price": 2000,
        "customer": "David"
    }
}
```

Zestaw danych wyjściowych typu **JsonFormat** jest zdefiniowany następująco: (częściowa definicja zawierająca tylko stosowne fragmenty). W szczególności `structure` sekcji zdefiniowano nazwy właściwości niestandardowe w pliku docelowym `nestingSeparator` (wartość domyślna to ".") są używane do identyfikowania warstwy zagnieżdżenia nazwę. Ta sekcja jest **opcjonalna**, o ile nie chcesz zmieniać nazwy właściwości na podstawie porównania z nazwą kolumny źródłowej ani zagnieżdżać właściwości.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "order.date",
            "type": "String"
        },
        {
            "name": "order.price",
            "type": "Int64"
        },
        {
            "name": "order.customer",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat"
        }
    }
}
```

## <a name="parquet-format"></a>Format Parquet

>[!NOTE]
>Data Factory wprowadzono nowe datset format Parquet, zobacz [formatu Parquet](format-parquet.md) artykułu zawierającego szczegóły. Następujące konfiguracje dla zestawu danych magazynu danych oparte na plikach jest nadal obsługiwany jako — dotyczy compabitility z poprzednimi wersjami. Zaleca się używać nowego modelu, w przyszłości.

Jeśli chcesz analizować pliki Parquet lub zapisywać dane w formacie Parquet, ustaw właściwość `format` `type` na wartość **ParquetFormat**. Nie musisz określać żadnych właściwości w sekcji Format należącej do sekcji typeProperties. Przykład:

```json
"format":
{
    "type": "ParquetFormat"
}
```

Pamiętaj o następujących kwestiach:

* Złożone typy danych są nie są obsługiwane (mapa, lista).
* Biały znak w nazwie kolumny nie jest obsługiwane.
* Plik parquet ma następujące opcje związane z kompresją: NONE, SNAPPY, GZIP oraz LZO. Odczytywanie danych z pliku Parquet w dowolnej z tych skompresowanych formatów, z wyjątkiem LZO — używa kodera-dekodera kompresji w metadanych do odczytywania danych obsługuje fabryki danych. Podczas zapisywania w pliku Parquet usługa Data Factory wybiera natomiast opcję SNAPPY, która jest domyślna dla formatu Parquet. Obecnie nie ma możliwości zastąpienia tego zachowania.

> [!IMPORTANT]
> Dla kopiowania upoważniony przez własne środowisko IR np. między lokalizacją lokalną i chmurą magazyny danych, jeśli nie kopiujesz plików Parquet **jako — jest**, musisz zainstalować **64-bitowego środowiska JRE 8 (Java Runtime Environment) lub OpenJDK** na swojej maszynie Podczerwieni. Zapoznaj się z bardziej szczegółowymi informacjami.

Związanym z kopiowaniem działających w własne środowisko IR za pomocą pliku Parquet serializacji/deserializacji, ADF lokalizuje środowisko wykonawcze języka Java po pierwsze, sprawdzając w rejestrze *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* dla środowiska JRE, jeśli nie znaleziono, po drugie sprawdzanie zmiennej systemowej *`JAVA_HOME`* dla OpenJDK.

- **Aby użyć środowiska JRE**: Środowisko IR 64-bitowego wymaga 64-bitowego środowiska JRE. Znajdziesz go z [tutaj](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Aby użyć OpenJDK**: jest ona obsługiwana od środowiska IR wersji 3.13. Pakiet jvm.dll z pozostałych wymagane w związku z tym zestawy OpenJDK własne środowisko IR maszyny i zestaw systemowej zmiennej środowiskowej JAVA_HOME.

>[!TIP]
>Po skopiowaniu danych do/z Parquet formatowanie przy użyciu środowiskiem Integration Runtime i trafień informacją o tym błędzie "Wystąpił błąd podczas wywoływania kodu java, komunikat: **miejsca na stercie java.lang.OutOfMemoryError:Java**", można dodać zmienną środowiskową `_JAVA_OPTIONS` na komputerze hostującym IR produktem, aby dopasować rozmiar sterty minimalnej/maksymalnej dla maszyny JVM przeznaczonych dla takich kopiowania następnie ponowne uruchamianie potoku.

![Ustaw rozmiar sterty maszyny JVM na własne środowisko IR](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Przykład: Ustaw zmienną `_JAVA_OPTIONS` wartością `-Xms256m -Xmx16g`. Flaga `Xms` Określa pulę alokacji pamięci początkowej dla języka Java maszyny wirtualnej (JVM), podczas gdy `Xmx` określa maksymalny rozmiar pamięci puli alokacji. Oznacza to, że maszyny JVM zostanie uruchomiona przy użyciu `Xms` ilość pamięci i będzie można użyć maksymalnie `Xmx` ilość pamięci. Domyślnie usługi ADF Użyj minimum 64MB i maksymalnej liczby 1G.

### <a name="data-type-mapping-for-parquet-files"></a>Mapowanie plików Parquet — typ danych

| Typ danych tymczasowych fabryki danych | Typ pierwotny parquet | Parquet oryginalny typ (deserializacji) | Parquet oryginalny typ (serializować) |
|:--- |:--- |:--- |:--- |
| Wartość logiczna | Wartość logiczna | ND | ND |
| Sbyte — | Int32 | Int8 | Int8 |
| Bajtów | Int32 | UInt8 | Int16 |
| Int16 | Int32 | Int16 | Int16 |
| UInt16 | Int32 | UInt16 | Int32 |
| Int32 | Int32 | Int32 | Int32 |
| UInt32 | Int64 | UInt32 | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 | Int64/dane binarne | UInt64 | Dziesiętna |
| Pojedyncze | float | ND | ND |
| Podwójne | Podwójne | ND | ND |
| Dziesiętna | Binarny | Dziesiętna | Dziesiętna |
| Ciąg | Binarny | Utf8 | Utf8 |
| DateTime | Int96 | ND | ND |
| Przedział czasu | Int96 | ND | ND |
| DateTimeOffset | Int96 | ND | ND |
| ByteArray | Binarny | ND | ND |
| Identyfikator GUID | Binarny | Utf8 | Utf8 |
| Char | Binarny | Utf8 | Utf8 |
| CharArray | Nieobsługiwane | ND | ND |

## <a name="orc-format"></a>ORC format

Jeśli chcesz analizować pliki ORC lub zapisywać dane w formacie ORC, ustaw właściwość `format` `type` na wartość **OrcFormat**. Nie musisz określać żadnych właściwości w sekcji Format należącej do sekcji typeProperties. Przykład:

```json
"format":
{
    "type": "OrcFormat"
}
```

Pamiętaj o następujących kwestiach:

* Złożone typy danych są nie są obsługiwane (struktura, mapa, lista, Unii).
* Biały znak w nazwie kolumny nie jest obsługiwane.
* Plik ORC ma trzy [opcje związane z kompresją](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB, SNAPPY. Usługa Data Factory obsługuje odczyt danych z pliku ORC w dowolnym z tych skompresowanych formatów. Do odczytywania danych używa kodera-dekodera kompresji z metadanych. Podczas zapisywania w pliku ORC usługa Data Factory wybiera natomiast opcję ZLIB, która jest domyślna dla formatu ORC. Obecnie nie ma możliwości zastąpienia tego zachowania.

> [!IMPORTANT]
> Dla kopiowania upoważniony przez własne środowisko IR np. między lokalizacją lokalną i chmurą magazyny danych, jeśli nie kopiujesz plików ORC **jako — jest**, musisz zainstalować **64-bitowego środowiska JRE 8 (Java Runtime Environment) lub bibliotekę OpenJDK**  na swojej maszynie Podczerwieni. Zapoznaj się z bardziej szczegółowymi informacjami.

Związanym z kopiowaniem działających w własne środowisko IR za pomocą pliku ORC serializacji/deserializacji, ADF lokalizuje środowisko wykonawcze języka Java po pierwsze, sprawdzając w rejestrze *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* dla środowiska JRE, jeśli nie znaleziono, po drugie sprawdzanie zmiennej systemowej *`JAVA_HOME`* dla OpenJDK.

- **Aby użyć środowiska JRE**: Środowisko IR 64-bitowego wymaga 64-bitowego środowiska JRE. Znajdziesz go z [tutaj](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Aby użyć OpenJDK**: jest ona obsługiwana od środowiska IR wersji 3.13. Pakiet jvm.dll z pozostałych wymagane w związku z tym zestawy OpenJDK własne środowisko IR maszyny i zestaw systemowej zmiennej środowiskowej JAVA_HOME.

### <a name="data-type-mapping-for-orc-files"></a>Mapowanie plików ORC — typ danych

| Typ danych tymczasowych fabryki danych | Typy ORC |
|:--- |:--- |
| Wartość logiczna | Wartość logiczna |
| Sbyte — | Bajtów |
| Bajtów | Krótkie |
| Int16 | Krótkie |
| UInt16 | Int |
| Int32 | Int |
| UInt32 | Długie |
| Int64 | Długie |
| UInt64 | Ciąg |
| Pojedyncze | float |
| Podwójne | Podwójne |
| Dziesiętna | Dziesiętna |
| Ciąg | Ciąg |
| DateTime | Znacznik czasu |
| DateTimeOffset | Znacznik czasu |
| Przedział czasu | Znacznik czasu |
| ByteArray | Binarny |
| Identyfikator GUID | Ciąg |
| Char | Char(1) |

## <a name="avro-format"></a>AVRO format

Jeśli chcesz analizować pliki Avro lub zapisywać dane w formacie Avro, ustaw właściwość `format` `type` na wartość **AvroFormat**. Nie musisz określać żadnych właściwości w sekcji Format należącej do sekcji typeProperties. Przykład:

```json
"format":
{
    "type": "AvroFormat",
}
```

Aby użyć formatu Avro w tabeli programu Hive, możesz zapoznać się z [samouczkiem oprogramowania Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Pamiętaj o następujących kwestiach:

* [Złożone typy danych](https://avro.apache.org/docs/current/spec.html#schema_complex) nie są obsługiwane (rekordy, wyliczenia, tablice, mapy, unie i stała).

## <a name="compression-support"></a>Obsługa kompresji

Usługa Azure Data Factory obsługuje Kompresuj/dekompresji danych podczas kopiowania. Po określeniu `compression` właściwości w wejściowego zestawu danych, działanie kopiowania odczytu skompresowane dane ze źródła i dekompresowanie; i po określeniu właściwość w zestawie danych wyjściowych działania kopiowania kompresowanie, a następnie zapisać danych do ujścia. Poniżej przedstawiono kilka przykładowych scenariuszy:

* GZIP odczytu skompresowane dane z obiektu blob platformy Azure zdekompresować go i zapisać dane wynikowe do usługi Azure SQL database. Zdefiniuj wejściowy zestaw danych obiektów Blob platformy Azure za pomocą `compression` `type` właściwość jako GZIP.
* Odczytywanie danych z pliku tekstowego z systemu plików w środowisku lokalnym, skompresować je w formacie GZip i zapisać skompresowane dane obiektu blob platformy Azure. Zdefiniuj wyjściowego zestawu danych obiektów Blob platformy Azure za pomocą `compression` `type` właściwość jako GZip.
* Odczytaj plik zip z serwera FTP Dekompresuj ją, aby pobrać pliki wewnątrz i te pliki znajdą się w usłudze Azure Data Lake Store. Zdefiniuj wejściowy zestaw danych FTP za pomocą `compression` `type` właściwość jako ZipDeflate.
* Odczytywanie danych z kompresji GZIP obiektu blob platformy Azure, zdekompresować, skompresować je przy użyciu BZIP2 i zapisać dane wynikowe do usługi Azure blob. Zdefiniuj wejściowy zestaw danych obiektów Blob platformy Azure za pomocą `compression` `type` GZIP i wyjściowy zestaw danych z ustawioną `compression` `type` równa BZIP2.

Aby określić kompresji dla zestawu danych, użyj **kompresji** właściwość w zestawie danych JSON, jak w poniższym przykładzie:

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "fileName": "pagecounts.csv.gz",
            "folderPath": "compression/file/",
            "format": {
                "type": "TextFormat"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

**Kompresji** sekcji ma dwie właściwości:

* **Typ:** kodera-dekodera kompresji, który może być **GZIP**, **Deflate**, **BZIP2**, lub **ZipDeflate**.
* **Poziom:** kompresji, który może być **optymalna** lub **najszybciej**.

  * **Najszybszy:** Kompresja operacja powinna być uznana tak szybko, jak to możliwe, nawet wtedy, gdy wynikowy plik nie jest optymalnie skompresowany.
  * **Optymalne**: Operacja kompresji powinny zostać skompresowane optymalnie, nawet wtedy, gdy operacja trwa dłużej.

    Aby uzyskać więcej informacji, zobacz [poziom kompresji](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) tematu.

> [!NOTE]
> Ustawienia kompresji nie są obsługiwane dla danych w **AvroFormat**, **OrcFormat**, lub **ParquetFormat**. Podczas odczytywania plików w tych formatach, Data Factory wykrywa i używa kodera-dekodera kompresji w metadanych. Podczas zapisywania plików w tych formatach, Data Factory wybiera natomiast opcję domyślną kodera-dekodera kompresji dla tego formatu. Na przykład ZLIB OrcFormat i SNAPPY dla ParquetFormat.

## <a name="unsupported-file-types-and-compression-formats"></a>Nieobsługiwane typy plików i kompresji

Funkcje rozszerzalności usługi Azure Data Factory umożliwia przekształcanie plików, które nie są obsługiwane.
Dwie opcje obejmują usługi Azure Functions i niestandardowe zadania za pomocą usługi Azure Batch.

Możesz zobaczyć przykładu korzystającego z funkcji platformy Azure, aby [Wyodrębnij zawartość pliku tar](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction). Aby uzyskać więcej informacji, zobacz [działania usługi Azure Functions](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity).

Można także utworzyć tej funkcjonalności za pomocą działania niestandardowego dotnet. Więcej informacji jest dostępnych [tutaj](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity)

## <a name="next-steps"></a>Kolejne kroki

Zobacz następujące artykuły dotyczące magazynów opartych na plikach danych obsługiwanych przez usługę Azure Data Factory:

- [Łącznik usługi Azure Blob Storage](connector-azure-blob-storage.md)
- [Łącznik usługi Azure Data Lake Store](connector-azure-data-lake-store.md)
- [Łącznik Amazon S3](connector-amazon-simple-storage-service.md)
- [Łącznik systemu plików](connector-file-system.md)
- [Łącznik FTP](connector-ftp.md)
- [Łącznik SFTP](connector-sftp.md)
- [Łącznik systemu plików HDFS](connector-hdfs.md)
- [Łącznik protokołu HTTP](connector-http.md)
