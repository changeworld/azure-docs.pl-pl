---
title: Format tekstu rozdzielanego w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: W tym temacie opisano sposób postępowania z format tekstu rozdzielanego w usłudze Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 4e365ef01b8c7a89d61efad25bd18943e7b2d1a4
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64877971"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Format tekstu rozdzielanego w usłudze Azure Data Factory

Postępuj zgodnie z tego artykułu, gdy użytkownik chce **przeanalizować rozdzielane pliki tekstowe lub zapisywać dane na format tekstu rozdzielanego**. 

Format tekstu rozdzielanego są obsługiwane następujące łączniki: [Amazon S3](connector-amazon-simple-storage-service.md), [obiektów Blob platformy Azure](connector-azure-blob-storage.md), [usługi Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [usługi Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [usługi Azure File Storage](connector-azure-file-storage.md), [System plików](connector-file-system.md), [FTP](connector-ftp.md), [usługi Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)i [ SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych rozdzielany tekst.

| Właściwość         | Opis                                                  | Wymagane |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Właściwość typu elementu dataset musi być równa **DelimitedText**. | Yes      |
| location         | Ustawienia lokalizacji plików. Każdy łącznik opartych na plikach ma swój własny typ lokalizacji i obsługiwane właściwości w obszarze `location`. **Szczegółowe informacje w artykule łącznika -> Sekcja właściwości zestawu danych**. | Yes      |
| columnDelimiter  | Znaki, używany do rozdzielania kolumn w pliku. Obecnie wielu znak ogranicznika jest obsługiwana tylko dla mapowania przepływu danych, ale nie działania kopiowania. <br>Wartość domyślna to **przecinkami `,`** , jeśli ogranicznikiem kolumny jest zdefiniowana jako ciąg pusty, co oznacza, że nie było ogranicznika, cały wiersz jest traktowana jako jedną kolumnę. | Nie       |
| rowDelimiter     | Pojedynczy znak lub "\r\n" używany do rozdzielania wierszy w pliku.<br>Wartość domyślna to dowolne z następujących wartości **przy odczycie: ["\r\n", "\r", "\n"]**, i **"\n" lub "\r\n" przy zapisie** dane mapowania przepływu i działanie kopiowania, odpowiednio. <br>Gdy `rowDelimiter` jest ustawiona na nie było ogranicznika (ciąg pusty) `columnDelimiter` musi być ustawiona jako nie było ogranicznika (ciąg pusty) jako, co oznacza traktowanie całą zawartość jako pojedynczą wartość. | Nie       |
| quoteChar        | Pojedynczy znak cudzysłowu wartości w kolumnie, jeśli zawiera on ogranicznik kolumny. <br>Wartość domyślna to **podwójne cudzysłowy** `"`. <br>Dla mapowania przepływu danych `quoteChar` nie może być pustym ciągiem. <br>Działanie kopiowania gdy `quoteChar` jest zdefiniowana jako ciąg pusty, oznacza to, istnieje nie znaków cudzysłowu, a wartość kolumny nie jest ujęty w cudzysłów, i `escapeChar` jest używany jako znak ucieczki ogranicznik kolumny, a nią samą. | Nie       |
| escapeChar       | Pojedynczy znak jako znak ucieczki dla cudzysłowów w cudzysłowie wartość.<br>Wartość domyślna to **ukośnik odwrotny `\`** . <br>Dla mapowania przepływu danych `escapeChar` nie może być pustym ciągiem. <br/>Działanie kopiowania gdy `escapeChar` jest zdefiniowana jako ciąg pusty, `quoteChar` musi być ustawiony jako ciąg pusty, a także, w którym to przypadku upewnij się, wszystkie wartości w kolumnie nie zawiera ograniczników. | Nie       |
| firstRowAsHeader | Określa, czy Traktuj/upewnij pierwszego wiersza jako wiersz nagłówka z nazwami kolumn.<br>Dozwolone wartości to **true** i **false** (ustawienie domyślne). | Nie       |
| nullValue        | Określa ciąg reprezentujący wartość null. <br>Wartość domyślna to **pusty ciąg**. | Nie       |
| encodingName     | Typ kodowania, używane do odczytu/zapisu plików testowych. <br>Dozwolone wartości to w następujący sposób: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", " IBM852 ","IBM855","IBM857","IBM860","IBM861","IBM863","IBM864","IBM865","IBM869","IBM870","IBM01140","IBM01141","IBM01142","IBM01143","IBM01144","IBM01145","IBM01146","IBM01147","IBM01148","IBM01149","ISO-2022-JP","ISO-2022-KR ","ISO-8859-1","ISO-8859-2","ISO-8859-3","ISO-8859-4","ISO-8859-5","ISO-8859-6","ISO-8859-7","ISO-8859-8","ISO-8859-9","ISO-8859-13","ISO-8859-15","WINDOWS-874","WINDOWS-1250","WINDOWS-1251","WINDOWS-1252","WINDOWS-1253"," WINDOWS 1254 ","WINDOWS-1255","WINDOWS-1256","WINDOWS-1257","WINDOWS-1258".<br>Należy pamiętać, że mapowanie przepływu danych nie obsługuje kodowania UTF-7. | Nie       |
| compressionCodec | Koder-dekoder kompresji używany do odczytu/zapisu plików tekstowych. <br>Dozwolone wartości to **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **snappy**, lub **lz4**. do użycia podczas zapisywania pliku. <br>Pamiętaj, obecnie działanie kopiowania nie obsługuje "snappy" i "lz4" mapowanie przepływu danych nie obsługuje "ZipDeflate". | Nie       |
| compressionLevel | Stopień kompresji. <br>Dozwolone wartości to **optymalna** lub **najszybciej**.<br>- **Najszybszy:** Kompresja operacja powinna być uznana tak szybko, jak to możliwe, nawet wtedy, gdy wynikowy plik nie jest optymalnie skompresowany.<br>- **Optymalne**: Operacja kompresji powinny zostać skompresowane optymalnie, nawet wtedy, gdy operacja trwa dłużej. Aby uzyskać więcej informacji, zobacz [poziom kompresji](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) tematu. | Nie       |

Poniżej przedstawiono przykład tekstu rozdzielanego dataset w usłudze Azure Blob Storage:

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez tekst rozdzielany źródła i ujścia.

### <a name="delimited-text-as-source"></a>Tekst rozdzielany jako źródło 

Następujące właściwości są obsługiwane w działaniu kopiowania ***\*źródła\**** sekcji.

| Właściwość       | Opis                                                  | Wymagane |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | Właściwość typu źródła działania kopiowania musi być równa **DelimitedTextSource**. | Yes      |
| formatSettings | Grupa właściwości. Zapoznaj się **tekst rozdzielany Odczytaj ustawienia** w poniższej tabeli. | Nie       |
| storeSettings  | Grupa właściwości o tym, jak można odczytać danych z magazynu danych. Każdy łącznik opartych na plikach ma swoje własne obsługiwane ustawienia odczytu w ramach `storeSettings`. **Szczegółowe informacje w artykule łącznika -> Sekcja właściwości działania kopiowania**. | Nie       |

Obsługiwane **tekst rozdzielany Odczytaj ustawienia** w obszarze `formatSettings`:

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Typ formatsettings — musi być równa **DelimitedTextReadSetting**. | Yes      |
| skipLineCount | Wskazuje liczbę **niepuste** wierszy do pominięcia podczas odczytywania danych z plików wejściowych. <br>Jeśli określono zarówno właściwość skipLineCount, jak i firstRowAsHeader, najpierw zostaną pominięte wiersze, a następnie zostaną odczytane informacje nagłówka z pliku wejściowego. | Nie       |

### <a name="delimited-text-as-sink"></a>Tekst rozdzielany jako ujście

Następujące właściwości są obsługiwane w działaniu kopiowania ***\*ujścia\**** sekcji.

| Właściwość       | Opis                                                  | Wymagane |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | Właściwość typu źródła działania kopiowania musi być równa **DelimitedTextSink**. | Yes      |
| formatSettings | Grupa właściwości. Zapoznaj się **tekst rozdzielany zapis ustawień** w poniższej tabeli. |          |
| storeSettings  | Grupa właściwości o tym, jak można zapisać danych do magazynu danych. Każdy łącznik opartych na plikach ma swoje własne ustawienia obsługiwanych zapisu w obszarze `storeSettings`. **Szczegółowe informacje w artykule łącznika -> Sekcja właściwości działania kopiowania**. | Nie       |

Obsługiwane **tekst rozdzielany zapis ustawień** w obszarze `formatSettings`:

| Właściwość      | Opis                                                  | Wymagane                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Typ formatsettings — musi być równa **DelimitedTextWriteSetting**. | Yes                                                   |
| fileExtension | Rozszerzenie pliku używany do nazywania plików wyjściowych, np. `.csv`, `.txt`. Musi być określony, gdy `fileName` nie została określona w danych wyjściowych DelimitedText zestawu danych. | Tak, jeśli nie określono nazwy pliku wyjściowego zestawu danych |

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości z przepływu danych

Dowiedz się, szczegółowe informacje z [źródła przekształcenia](data-flow-source.md) i [ujścia przekształcania](data-flow-sink.md) w mapowanie przepływu danych.

## <a name="next-steps"></a>Kolejne kroki

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)