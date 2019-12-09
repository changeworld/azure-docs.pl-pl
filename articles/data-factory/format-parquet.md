---
title: Format Parquet w Azure Data Factory
description: W tym temacie opisano sposób postępowania z formatem Parquet w Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 72b001ada98ecd768cd39fea012a20f2ada466d2
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931270"
---
# <a name="parquet-format-in-azure-data-factory"></a>Format Parquet w Azure Data Factory

Postępuj zgodnie z tym artykułem, jeśli chcesz **analizować pliki Parquet lub zapisywać dane w formacie Parquet**. 

Format Parquet jest obsługiwany dla następujących łączników: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [system plików](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)i [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Parquet.

| Właściwość         | Opis                                                  | Wymagane |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Właściwość Type zestawu danych musi być ustawiona na wartość **Parquet**. | Tak      |
| location         | Ustawienia lokalizacji plików. Każdy Łącznik oparty na plikach ma własny typ lokalizacji i obsługiwane właściwości w obszarze `location`. **Zobacz szczegóły w sekcji łącznik — > Właściwości zestawu danych**. | Tak      |
| compressionCodec | Koder-dekoder kompresji do użycia podczas zapisywania w plikach Parquet. Podczas odczytywania z plików Parquet Data Factory automatycznie określać koder-dekoder kompresji na podstawie metadanych pliku.<br>Obsługiwane typy to "**none**", "**gzip**", "**przyciąganie**" (domyślnie) i "**LZO**". Działanie, które jest obecnie kopiowane, nie obsługuje LZO. | Nie       |

> [!NOTE]
> Biały znak w nazwie kolumny nie jest obsługiwany w przypadku plików Parquet.

Poniżej znajduje się przykład zestawu danych Parquet na platformie Azure Blob Storage:

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
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
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujścia Parquet.

### <a name="parquet-as-source"></a>Parquet jako źródło

Następujące właściwości są obsługiwane w sekcji działanie kopiowania ***\*źródło\**** .

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Właściwość Type źródła działania Copy musi być ustawiona na wartość **ParquetSource**. | Tak      |
| storeSettings | Grupa właściwości do odczytywania danych z magazynu danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia odczytu w obszarze `storeSettings`. **Zobacz szczegóły w artykule łącznik — > właściwości działania kopiowania**. | Nie       |

### <a name="parquet-as-sink"></a>Parquet jako ujścia

Następujące właściwości są obsługiwane w sekcji działanie kopiowania ***\*ujścia\**** .

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Właściwość Type źródła działania Copy musi być ustawiona na wartość **ParquetSink**. | Tak      |
| storeSettings | Grupa właściwości do zapisywania danych w magazynie danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia zapisu w obszarze `storeSettings`. **Zobacz szczegóły w artykule łącznik — > właściwości działania kopiowania**. | Nie       |

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Dowiedz się więcej o [przekształceniu źródłowym](data-flow-source.md) i [transformacji ujścia](data-flow-sink.md) w mapowaniu przepływu danych.

## <a name="data-type-support"></a>Obsługa typu danych

Parquet złożone typy danych nie są obecnie obsługiwane (na przykład mapy, listy, struktury).

## <a name="using-self-hosted-integration-runtime"></a>Korzystanie z Integration Runtime samoobsługowego

> [!IMPORTANT]
> W przypadku kopii obsługiwanej przez samodzielne Integration Runtime, np. między lokalnym i magazynem danych w chmurze, jeśli nie kopiujesz plików Parquet w taki **sposób**, musisz zainstalować **64-bitową JRE, Java Runtime Environment lub OPENJDK** na maszynie IR. Więcej informacji można znaleźć w poniższym akapicie.

W przypadku kopiowania uruchomionego na samoobsługowym środowisku IR przy użyciu serializacji/deserializacji pliku Parquet można zlokalizować środowisko uruchomieniowe języka Java, sprawdzając najpierw *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* rejestru dla środowiska JRE, jeśli nie zostanie on znaleziony, a następnie podczas drugiej kontroli zmiennej systemowej *`JAVA_HOME`* dla OpenJDK.

- **Aby użyć środowiska JRE**: 64-bitowy IR wymaga 64-bitowego środowiska JRE. Można je znaleźć w [tym miejscu](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Aby użyć OpenJDK**: jest obsługiwana od czasu IR w wersji 3,13. Spakuj plik JVM. dll ze wszystkimi innymi wymaganymi zestawami OpenJDK do samodzielnej maszyny IR, a następnie ustaw dla zmiennej środowiskowej systemowe JAVA_HOME odpowiednio.

> [!TIP]
> Jeśli skopiujesz dane do/z formatu Parquet przy użyciu samodzielnego Integration Runtime i błędu trafień mówiąc "Wystąpił błąd podczas wywoływania języka Java, komunikat: **Java. lang. OutOfMemoryError: przestrzeń sterty Java**", można dodać zmienną środowiskową `_JAVA_OPTIONS` na maszynie, która hostuje własne środowisko IR, aby dostosować rozmiar sterty minimalny/maksymalny dla JVM, a następnie ponownie uruchomić potok.

![Ustawianie rozmiaru sterty JVM na samoobsługowym środowisku IR](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Przykład: Ustaw zmienną `_JAVA_OPTIONS` przy użyciu `-Xms256m -Xmx16g`wartości. Flaga `Xms` określa początkową pulę alokacji pamięci dla wirtualna maszyna Java (JVM), podczas gdy `Xmx` określa maksymalną pulę alokacji pamięci. Oznacza to, że JVM zostanie uruchomione z `Xms` ilości pamięci i będzie można użyć maksymalnie `Xmx` ilości pamięci. Domyślnie funkcja ADF korzysta z minimalnej 64 MB i maksymalnej wartości 1G.

## <a name="next-steps"></a>Następne kroki

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
