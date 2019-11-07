---
title: Format JSON w Azure Data Factory
description: W tym temacie opisano sposób postępowania z formatem JSON w Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: b520575202165a3f879b17969d9ceea71eb71006
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73674787"
---
# <a name="json-format-in-azure-data-factory"></a>Format JSON w Azure Data Factory

Postępuj zgodnie z tym artykułem, jeśli chcesz **przeanalizować pliki JSON lub zapisać dane w formacie JSON**. 

Format JSON jest obsługiwany dla następujących łączników: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [system plików](connector-file-system.md), [FTP](connector-ftp.md), [Google Magazyn w chmurze](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)i [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych JSON.

| Właściwość         | Opis                                                  | Wymagany |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Właściwość Type zestawu danych musi być ustawiona na wartość **JSON**. | Tak      |
| location         | Ustawienia lokalizacji plików. Każdy Łącznik oparty na plikach ma własny typ lokalizacji i obsługiwane właściwości w obszarze `location`. **Zobacz szczegóły w sekcji łącznik — > Właściwości zestawu danych**. | Tak      |
| encodingName     | Typ kodowania używany do odczytu/zapisu plików testowych. <br>Dozwolone wartości są następujące: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", " IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149" , "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "WINDOWS-8859-15", "WINDOWS-874", "WINDOWS-1250", " WINDOWS-1252 "," WINDOWS-1253 "," WINDOWS-1254 "," WINDOWS-1255 "," WINDOWS-1256 "," WINDOWS-1257 "," WINDOWS-1258 ".| Nie       |
| compressionCodec | Koder-dekoder kompresji używany do odczytu/zapisu plików tekstowych. <br>Dozwolone wartości to **bzip2**, **gzip**, **Wklęśnięcie**, **ZipDeflate**, **przyciąganie**lub **lz4**. do użycia podczas zapisywania pliku. <br>Działanie kopiowania obecnie nie obsługuje "przyciągania" & "lz4". | Nie       |
| compressionLevel | Współczynnik kompresji. <br>Dozwolone wartości to **optymalne** lub **najszybszy**.<br>- **najszybciej:** operacja kompresji powinna zakończyć się tak szybko, jak to możliwe, nawet jeśli plik nie jest optymalnie kompresowany.<br>- **optymalnie**: operacja kompresji powinna być optymalnie skompresowana, nawet jeśli operacja trwa dłużej. Aby uzyskać więcej informacji, zobacz temat [poziom kompresji](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | Nie       |

Poniżej znajduje się przykład zestawu danych JSON w usłudze Azure Blob Storage:

```json
{
    "name": "JSONDataset",
    "properties": {
        "type": "Json",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujścia danych JSON.

### <a name="json-as-source"></a>KOD JSON jako źródło

Następujące właściwości są obsługiwane w sekcji działanie kopiowania ***\*źródło\**** .

| Właściwość      | Opis                                                  | Wymagany |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Właściwość Type źródła działania Copy musi być ustawiona na wartość **JSONSource**. | Tak      |
| storeSettings | Grupa właściwości do odczytywania danych z magazynu danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia odczytu w obszarze `storeSettings`. **Zobacz szczegóły w artykule łącznik — > właściwości działania kopiowania**. | Nie       |

### <a name="json-as-sink"></a>JSON jako ujścia

Następujące właściwości są obsługiwane w sekcji działanie kopiowania ***\*ujścia\**** .

| Właściwość      | Opis                                                  | Wymagany |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Właściwość Type źródła działania Copy musi być ustawiona na wartość **JSONSink**. | Tak      |
| formatSettings | Grupa właściwości. Zapoznaj się z tabelą **ustawień zapisu JSON** poniżej. | Nie       |
| storeSettings | Grupa właściwości do zapisywania danych w magazynie danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia zapisu w obszarze `storeSettings`. **Zobacz szczegóły w artykule łącznik — > właściwości działania kopiowania**. | Nie       |

Obsługiwane **Ustawienia zapisu** w formacie JSON w `formatSettings`:

| Właściwość      | Opis                                                  | Wymagany                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Typ formatSettings musi być ustawiony na **JsonWriteSetting**. | Tak                                                   |
| filePattern |Wskazuje wzorzec danych przechowywanych w każdym pliku JSON. Dozwolone wartości to: **setOfObjects** i **arrayOfObjects**. Wartością **domyślną** jest **setOfObjects**. Aby uzyskać szczegółowe informacje o tych wzorcach, zobacz sekcję [Wzorce plików JSON](#json-file-patterns). |Nie |

### <a name="json-file-patterns"></a>Wzorce plików JSON

Działanie kopiowania może automatycznie wykrywać i analizować następujące wzorce plików JSON. 

- **Typ I: setOfObjects**

    Każdy plik zawiera pojedynczy obiekt lub wiele obiektów rozdzielonych wierszami bądź połączonych. 
    Po wybraniu tej opcji w obiekcie ujścia działania kopiowania działanie kopiowania tworzy pojedynczy plik JSON z każdym obiektem w wierszu (rozdzielonym wierszem).

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

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Dowiedz się więcej o [przekształceniu źródłowym](data-flow-source.md) i [transformacji ujścia](data-flow-sink.md) w mapowaniu przepływu danych.

## <a name="next-steps"></a>Następne kroki

- [Przegląd działania kopiowania](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
