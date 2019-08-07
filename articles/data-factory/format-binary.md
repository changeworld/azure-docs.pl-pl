---
title: Format binarny w Azure Data Factory | Microsoft Docs
description: W tym temacie opisano sposób postępowania z formatem binarnym w Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: a3b3e4e873497b3b0f726d815a798919ace4f931
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68842545"
---
# <a name="binary-format-in-azure-data-factory"></a>Format binarny w Azure Data Factory

Format binarny jest obsługiwany dla następujących łączników: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [system plików](connector-file-system.md), [FTP](connector-ftp.md), [Magazyn Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)i [SFTP](connector-sftp.md).

Można użyć binarnego zestawu danych w działaniach [kopiowania](copy-activity-overview.md), [działania GetMetadata](control-flow-get-metadata-activity.md)lub [Usuń działanie](delete-activity.md). W przypadku korzystania z binarnego zestawu danych ADF nie analizuje zawartości pliku, ale traktuje ją jako-is. 

>[!NOTE]
>W przypadku korzystania z binarnego zestawu danych w działaniu kopiowania można kopiować tylko z binarnego zestawu danych do binarnego zestawu danych.

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez binarny zestaw danych.

| Właściwość         | Opis                                                  | Wymagane |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Właściwość Type zestawu danych musi być ustawiona na wartość **binarną**. | Yes      |
| location         | Ustawienia lokalizacji plików. Każdy Łącznik oparty na plikach ma własny typ lokalizacji i obsługiwane właściwości w sekcji `location`. **Zobacz szczegóły w sekcji łącznik — > Właściwości zestawu danych**. | Yes      |
| compression | Grupa właściwości do konfigurowania kompresji plików. Skonfiguruj tę sekcję, jeśli chcesz przeprowadzić kompresję/dekompresowanie podczas wykonywania działania. | Nie |
| — typ | Koder-dekoder kompresji używany do odczytu/zapisu plików binarnych. <br>Dozwolone wartości to **bzip2**, **gzip**, **Wklęśnięcie**, **ZipDeflate**. do użycia podczas zapisywania pliku. | Nie       |
| level | Współczynnik kompresji. Zastosuj, gdy zestaw danych jest używany w ujścia działania kopiowania.<br>Dozwolone wartości to **optymalne** lub **najszybszy**.<br>- **Najlepszy** Operacja kompresji powinna zostać ukończona tak szybko, jak to możliwe, nawet jeśli plik nie jest optymalnie kompresowany.<br>- **Optymalny**: Operacja kompresji powinna być optymalnie skompresowana, nawet jeśli ukończenie operacji trwa dłużej. Aby uzyskać więcej informacji, zobacz [poziom kompresji](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) tematu. | Nie       |

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

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło danych binarnych i ujścia.

>[!NOTE]
>W przypadku korzystania z binarnego zestawu danych w działaniu kopiowania można kopiować tylko z binarnego zestawu danych do binarnego zestawu danych.

### <a name="binary-as-source"></a>Plik binarny jako źródło

W sekcji ***\*źródło\**** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| — typ          | Właściwość Type źródła działania Copy musi być ustawiona na wartość **BinarySource**. | Tak      |
| storeSettings | Grupa właściwości do odczytywania danych z magazynu danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia odczytu w obszarze `storeSettings`. **Zobacz szczegóły w artykule łącznik — > właściwości działania kopiowania**. | Nie       |

### <a name="binary-as-sink"></a>Dane binarne jako ujścia

W sekcji ***\*ujścia\**** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Właściwość Type źródła działania Copy musi być ustawiona na wartość **BinarySink**. | Tak      |
| storeSettings | Grupa właściwości do zapisywania danych w magazynie danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia zapisu w obszarze `storeSettings`. **Zobacz szczegóły w artykule łącznik — > właściwości działania kopiowania**. | Nie       |

## <a name="next-steps"></a>Następne kroki

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Usuń działanie](delete-activity.md)
