---
title: Format binarny w Azure Data Factory
description: W tym temacie opisano sposób postępowania z formatem binarnym w Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: 82f7c380c66dc6b42f4ca5c67c13524428c78221
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73674812"
---
# <a name="binary-format-in-azure-data-factory"></a>Format binarny w Azure Data Factory

Format binarny jest obsługiwany dla następujących łączników: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [system plików](connector-file-system.md), [FTP](connector-ftp.md), [Google Magazyn w chmurze](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)i [SFTP](connector-sftp.md).

Można użyć binarnego zestawu danych w działaniach [kopiowania](copy-activity-overview.md), [działania GetMetadata](control-flow-get-metadata-activity.md)lub [Usuń działanie](delete-activity.md). W przypadku korzystania z binarnego zestawu danych ADF nie analizuje zawartości pliku, ale traktuje ją jako-is. 

>[!NOTE]
>W przypadku korzystania z binarnego zestawu danych w działaniu kopiowania można kopiować tylko z binarnego zestawu danych do binarnego zestawu danych.

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez binarny zestaw danych.

| Właściwość         | Opis                                                  | Wymagany |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Właściwość Type zestawu danych musi być ustawiona na wartość **binarną**. | Tak      |
| location         | Ustawienia lokalizacji plików. Każdy Łącznik oparty na plikach ma własny typ lokalizacji i obsługiwane właściwości w obszarze `location`. **Zobacz szczegóły w sekcji łącznik — > Właściwości zestawu danych**. | Tak      |
| skompresowane | Grupa właściwości do konfigurowania kompresji plików. Skonfiguruj tę sekcję, jeśli chcesz przeprowadzić kompresję/dekompresowanie podczas wykonywania działania. | Nie |
| type | Koder-dekoder kompresji używany do odczytu/zapisu plików binarnych. <br>Dozwolone wartości to **bzip2**, **gzip**, **Wklęśnięcie**, **ZipDeflate**. do użycia podczas zapisywania pliku. | Nie       |
| poziomie | Współczynnik kompresji. Zastosuj, gdy zestaw danych jest używany w ujścia działania kopiowania.<br>Dozwolone wartości to **optymalne** lub **najszybszy**.<br>- **najszybciej:** operacja kompresji powinna zakończyć się tak szybko, jak to możliwe, nawet jeśli plik nie jest optymalnie kompresowany.<br>- **optymalnie**: operacja kompresji powinna być optymalnie skompresowana, nawet jeśli operacja trwa dłużej. Aby uzyskać więcej informacji, zobacz temat [poziom kompresji](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | Nie       |

Poniżej znajduje się przykładowy binarny zestaw danych na platformie Azure Blob Storage:

```json
{
    "name": "BinaryDataset",
    "properties": {
        "type": "Binary",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło danych binarnych i ujścia.

>[!NOTE]
>W przypadku korzystania z binarnego zestawu danych w działaniu kopiowania można kopiować tylko z binarnego zestawu danych do binarnego zestawu danych.

### <a name="binary-as-source"></a>Plik binarny jako źródło

Następujące właściwości są obsługiwane w sekcji działanie kopiowania ***\*źródło\**** .

| Właściwość      | Opis                                                  | Wymagany |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Właściwość Type źródła działania Copy musi być ustawiona na wartość **BinarySource**. | Tak      |
| storeSettings | Grupa właściwości do odczytywania danych z magazynu danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia odczytu w obszarze `storeSettings`. **Zobacz szczegóły w artykule łącznik — > właściwości działania kopiowania**. | Nie       |

### <a name="binary-as-sink"></a>Dane binarne jako ujścia

Następujące właściwości są obsługiwane w sekcji działanie kopiowania ***\*ujścia\**** .

| Właściwość      | Opis                                                  | Wymagany |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Właściwość Type źródła działania Copy musi być ustawiona na wartość **BinarySink**. | Tak      |
| storeSettings | Grupa właściwości do zapisywania danych w magazynie danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia zapisu w obszarze `storeSettings`. **Zobacz szczegóły w artykule łącznik — > właściwości działania kopiowania**. | Nie       |

## <a name="next-steps"></a>Następne kroki

- [Przegląd działania kopiowania](copy-activity-overview.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Usuń działanie](delete-activity.md)
