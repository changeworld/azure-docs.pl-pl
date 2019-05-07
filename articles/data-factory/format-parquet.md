---
title: Format parquet w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: W tym temacie opisano sposób postępowania z formatu Parquet w usłudze Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 360b794f0d8ba9c145a92f015f264eb624fbb0f1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65144876"
---
# <a name="parquet-format-in-azure-data-factory"></a>Format parquet w usłudze Azure Data Factory

Postępuj zgodnie z tego artykułu, gdy użytkownik chce **analizować pliki Parquet lub zapisywać dane w formacie Parquet**. 

Parquet format jest obsługiwany w przypadku następujące łączniki: [Amazon S3](connector-amazon-simple-storage-service.md), [obiektów Blob platformy Azure](connector-azure-blob-storage.md), [usługi Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [usługi Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [usługi Azure File Storage](connector-azure-file-storage.md), [System plików](connector-file-system.md), [FTP](connector-ftp.md), [usługi Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)i [ SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Parquet.

| Właściwość         | Opis                                                  | Wymagane |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Właściwość typu elementu dataset musi być równa **Parquet**. | Yes      |
| location         | Ustawienia lokalizacji plików. Każdy łącznik opartych na plikach ma swój własny typ lokalizacji i obsługiwane właściwości w obszarze `location`. **Szczegółowe informacje w artykule łącznika -> Sekcja właściwości zestawu danych**. | Yes      |
| compressionCodec | Koder-dekoder kompresji do użycia podczas zapisywania plików Parquet. Podczas odczytu z plików Parquet, Data Factory automatycznie określić kodera-dekodera kompresji, na podstawie metadanych pliku.<br>Obsługiwane typy to "**Brak**","**gzip**","**snappy**" (ustawienie domyślne) i "**lzo**". Uwaga: obecnie działanie kopiowania nie obsługuje LZO. | Nie       |

> [!NOTE]
> Biały znak w nazwie kolumny nie jest obsługiwane dla plików Parquet.

Poniżej przedstawiono przykład Parquet dataset w usłudze Azure Blob Storage:

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

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez Parquet źródła i ujścia.

### <a name="parquet-as-source"></a>Parquet jako źródło

Następujące właściwości są obsługiwane w działaniu kopiowania ***\*źródła\**** sekcji.

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Właściwość typu źródła działania kopiowania musi być równa **ParquetSource**. | Yes      |
| storeSettings | Grupa właściwości o tym, jak można odczytać danych z magazynu danych. Każdy łącznik opartych na plikach ma swoje własne obsługiwane ustawienia odczytu w ramach `storeSettings`. **Szczegółowe informacje w artykule łącznika -> Sekcja właściwości działania kopiowania**. | Nie       |

### <a name="parquet-as-sink"></a>Parquet jako ujście

Następujące właściwości są obsługiwane w działaniu kopiowania ***\*ujścia\**** sekcji.

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Właściwość typu źródła działania kopiowania musi być równa **ParquetSink**. | Yes      |
| storeSettings | Grupa właściwości o tym, jak można zapisać danych do magazynu danych. Każdy łącznik opartych na plikach ma swoje własne ustawienia obsługiwanych zapisu w obszarze `storeSettings`. **Szczegółowe informacje w artykule łącznika -> Sekcja właściwości działania kopiowania**. | Nie       |

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości z przepływu danych

Dowiedz się, szczegółowe informacje z [źródła przekształcenia](data-flow-source.md) i [ujścia przekształcania](data-flow-sink.md) w mapowanie przepływu danych.

## <a name="data-type-support"></a>Obsługa typu danych

Parquet, do których złożone typy danych nie są obecnie obsługiwane (mapa, lista, struktura).

## <a name="using-self-hosted-integration-runtime"></a>Przy użyciu własnego środowiska Integration Runtime

> [!IMPORTANT]
> Dla kopiowania upoważniony przez własne środowisko IR np. między lokalizacją lokalną i chmurą magazyny danych, jeśli nie kopiujesz plików Parquet **jako — jest**, musisz zainstalować **64-bitowego środowiska JRE 8 (Java Runtime Environment) lub OpenJDK** na swojej maszynie Podczerwieni. Zapoznaj się z bardziej szczegółowymi informacjami.

Związanym z kopiowaniem działających w własne środowisko IR za pomocą pliku Parquet serializacji/deserializacji, ADF lokalizuje środowisko wykonawcze języka Java po pierwsze, sprawdzając w rejestrze *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* dla środowiska JRE, jeśli nie znaleziono, po drugie sprawdzanie zmiennej systemowej *`JAVA_HOME`* dla OpenJDK.

- **Aby użyć środowiska JRE**: Środowisko IR 64-bitowego wymaga 64-bitowego środowiska JRE. Znajdziesz go z [tutaj](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Aby użyć OpenJDK**: jest ona obsługiwana od środowiska IR wersji 3.13. Pakiet jvm.dll z pozostałych wymagane w związku z tym zestawy OpenJDK własne środowisko IR maszyny i zestaw systemowej zmiennej środowiskowej JAVA_HOME.

> [!TIP]
> Po skopiowaniu danych do/z Parquet formatowanie przy użyciu środowiskiem Integration Runtime i trafień informacją o tym błędzie "Wystąpił błąd podczas wywoływania kodu java, komunikat: **miejsca na stercie java.lang.OutOfMemoryError:Java**", można dodać zmienną środowiskową `_JAVA_OPTIONS` na komputerze hostującym IR produktem, aby dopasować rozmiar sterty minimalnej/maksymalnej dla maszyny JVM przeznaczonych dla takich kopiowania następnie ponowne uruchamianie potoku.

![Ustaw rozmiar sterty maszyny JVM na własne środowisko IR](C:/AzureContent/azure-docs-pr/articles/data-factory/media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Przykład: Ustaw zmienną `_JAVA_OPTIONS` wartością `-Xms256m -Xmx16g`. Flaga `Xms` Określa pulę alokacji pamięci początkowej dla języka Java maszyny wirtualnej (JVM), podczas gdy `Xmx` określa maksymalny rozmiar pamięci puli alokacji. Oznacza to, że maszyny JVM zostanie uruchomiona przy użyciu `Xms` ilość pamięci i będzie można użyć maksymalnie `Xmx` ilość pamięci. Domyślnie usługi ADF Użyj minimum 64MB i maksymalnej liczby 1G.

## <a name="next-steps"></a>Kolejne kroki

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)