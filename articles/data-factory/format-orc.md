---
title: Format ORC w Azure Data Factory
description: W tym temacie opisano sposób postępowania z formatem ORC w Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 5f83e9bbcdfffdd9b19786012295ff5643116551
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927370"
---
# <a name="orc-format-in-azure-data-factory"></a>Format ORC w Azure Data Factory

Postępuj zgodnie z tym artykułem, jeśli chcesz **analizować pliki ORC lub zapisywać dane w formacie Orc**. 

Format ORC jest obsługiwany dla następujących łączników: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [system plików](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)i [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych ORC.

| Właściwość         | Opis                                                  | Wymagane |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Właściwość Type zestawu danych musi być ustawiona na wartość **Orc**. | Tak      |
| location         | Ustawienia lokalizacji plików. Każdy Łącznik oparty na plikach ma własny typ lokalizacji i obsługiwane właściwości w obszarze `location`. **Zobacz szczegóły w sekcji łącznik — > Właściwości zestawu danych**. | Tak      |

Poniżej znajduje się przykład zestawu danych ORC na platformie Azure Blob Storage:

```json
{
    "name": "ORCDataset",
    "properties": {
        "type": "Orc",
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
            }
        }
    }
}
```

Pamiętaj o następujących kwestiach:

* Złożone typy danych nie są obsługiwane (struktura, mapa, lista, Unia).
* Biały znak w nazwie kolumny nie jest obsługiwany.
* Plik ORC ma trzy [opcje związane z kompresją](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB, SNAPPY. Usługa Data Factory obsługuje odczyt danych z pliku ORC w dowolnym z tych skompresowanych formatów. Do odczytywania danych używa kodera-dekodera kompresji z metadanych. Podczas zapisywania w pliku ORC usługa Data Factory wybiera natomiast opcję ZLIB, która jest domyślna dla formatu ORC. Obecnie nie ma możliwości zastąpienia tego zachowania.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujścia ORC.

### <a name="orc-as-source"></a>ORC jako źródło

Następujące właściwości są obsługiwane w sekcji działanie kopiowania ***\*źródło\**** .

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Właściwość Type źródła działania Copy musi być ustawiona na wartość **OrcSource**. | Tak      |
| storeSettings | Grupa właściwości do odczytywania danych z magazynu danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia odczytu w obszarze `storeSettings`. **Zobacz szczegóły w artykule łącznik — > właściwości działania kopiowania**. | Nie       |

### <a name="orc-as-sink"></a>ORC jako ujścia

Następujące właściwości są obsługiwane w sekcji działanie kopiowania ***\*ujścia\**** .

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Właściwość Type źródła działania Copy musi być ustawiona na wartość **OrcSink**. | Tak      |
| storeSettings | Grupa właściwości do zapisywania danych w magazynie danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia zapisu w obszarze `storeSettings`. **Zobacz szczegóły w artykule łącznik — > właściwości działania kopiowania**. | Nie       |

## <a name="using-self-hosted-integration-runtime"></a>Korzystanie z Integration Runtime samoobsługowego

> [!IMPORTANT]
> W przypadku kopii obsługiwanej przez samodzielne Integration Runtime, np. między lokalnym i magazynem danych w chmurze, jeśli nie kopiujesz plików ORC w taki **sposób**, musisz zainstalować **64-bitową JRE, Java Runtime Environment lub OPENJDK** na maszynie IR. Więcej informacji można znaleźć w poniższym akapicie.

W przypadku kopiowania uruchomionego na samoobsługowym środowisku IR przy użyciu serializacji/deserializacji pliku ORC można zlokalizować środowisko uruchomieniowe języka Java, sprawdzając najpierw *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* rejestru dla środowiska JRE, jeśli nie zostanie on znaleziony, a następnie podczas drugiej kontroli zmiennej systemowej *`JAVA_HOME`* dla OpenJDK.

- **Aby użyć środowiska JRE**: 64-bitowy IR wymaga 64-bitowego środowiska JRE. Można je znaleźć w [tym miejscu](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Aby użyć OpenJDK**: jest obsługiwana od czasu IR w wersji 3,13. Spakuj plik JVM. dll ze wszystkimi innymi wymaganymi zestawami OpenJDK do samodzielnej maszyny IR, a następnie ustaw dla zmiennej środowiskowej systemowe JAVA_HOME odpowiednio.

> [!TIP]
> Jeśli skopiujesz dane do/z formatu ORC przy użyciu samodzielnego Integration Runtime i błędu trafień mówiąc "Wystąpił błąd podczas wywoływania języka Java, komunikat: **Java. lang. OutOfMemoryError: przestrzeń sterty Java**", można dodać zmienną środowiskową `_JAVA_OPTIONS` na maszynie, która hostuje własne środowisko IR, aby dostosować rozmiar sterty minimalny/maksymalny dla JVM, a następnie ponownie uruchomić potok.

![Ustawianie rozmiaru sterty JVM na samoobsługowym środowisku IR](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Przykład: Ustaw zmienną `_JAVA_OPTIONS` przy użyciu `-Xms256m -Xmx16g`wartości. Flaga `Xms` określa początkową pulę alokacji pamięci dla wirtualna maszyna Java (JVM), podczas gdy `Xmx` określa maksymalną pulę alokacji pamięci. Oznacza to, że JVM zostanie uruchomione z `Xms` ilości pamięci i będzie można użyć maksymalnie `Xmx` ilości pamięci. Domyślnie funkcja ADF korzysta z minimalnej 64 MB i maksymalnej wartości 1G.

## <a name="next-steps"></a>Następne kroki

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
