---
title: Zachowywanie metadanych i list ACL przy użyciu działania kopiowania w Azure Data Factory
description: Dowiedz się, jak zachować metadane i listy ACL podczas kopiowania przy użyciu działania kopiowania w Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: jingwang
ms.openlocfilehash: 056909f5fd5838e5ae50fb84bd3535029d862acf
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356274"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Zachowywanie metadanych i list ACL przy użyciu działania kopiowania w Azure Data Factory

W przypadku używania działania kopiowania Azure Data Factory do kopiowania danych ze źródła do ujścia w następujących scenariuszach można również zachować metadane i listy ACL.

## <a name="preserve-metadata"></a>Zachowaj metadane usługi Lake Migration

W przypadku migrowania danych z jednej usługi Data Lake do innej, w tym usługi [Amazon S3](connector-amazon-simple-storage-service.md), [obiektów blob platformy Azure](connector-azure-blob-storage.md)i [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)można zachować metadane plików wraz z danymi.

Działanie kopiowania obsługuje zachowywanie następujących atrybutów podczas kopiowania danych:

- **Wszystkie metadane określone przez klienta** 
- I pięć następujących **wbudowanych właściwości systemu magazynu danych**: `contentType`, `contentLanguage` (z wyjątkiem usługi Amazon S3), `contentEncoding`, `contentDisposition`, `cacheControl`.

W przypadku kopiowania plików jako-is z usługi Amazon S3/Azure Data Lake Storage Gen2/Azure BLOB do Azure Data Lake Storage Gen2/Azure Blob z formatem binarnym można znaleźć opcję **Zachowaj** na karcie **Ustawienia** > **działania kopiowania** dla tworzenia działań lub na stronie **Ustawienia** w narzędziu kopiowanie danych.

![Metadane zachowywania działania kopiowania](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

Oto przykład konfiguracji JSON działania kopiowania (zobacz `preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AmazonS3ReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "Attributes"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset Amazon S3/Azure Blob/ADLS Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset for Azure Blob/ADLS Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="preserve-acls"></a>Zachowaj listy ACL z Data Lake Storage Gen1 do Gen2

Podczas uaktualniania programu z Azure Data Lake Storage Gen1 do Gen2, można wybrać opcję zachowania list kontroli dostępu POSIX (ACL) wraz z plikami danych. Aby uzyskać więcej informacji na temat kontroli dostępu, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) i [Kontrola dostępu w programie Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

Działanie kopiowania obsługuje zachowywanie następujących typów list ACL podczas kopiowania danych. Można wybrać jeden lub więcej typów:

- **Lista ACL**: kopiowanie i zachowywanie list kontroli dostępu POSIX dla plików i katalogów. Kopiuje wszystkie istniejące listy ACL ze źródła do ujścia. 
- **Właściciel**: kopiowanie i zachowywanie użytkownika będącego właścicielem plików i katalogów. Wymagany jest dostęp administratora do Data Lake Storage Gen2 ujścia.
- **Grupa**: Skopiuj i Zachowaj grupę będącą właścicielem plików i katalogów. Dostęp przez administratora do Data Lake Storage Gen2 ujścia lub użytkownika będącego właścicielem (jeśli jest również członkiem grupy docelowej) jest wymagane.

Jeśli wybierzesz opcję kopiowania z folderu, Data Factory replikuje listy ACL dla danego folderu oraz plików i katalogów w tym folderze, jeśli `recursive` jest ustawiona na wartość true. W przypadku wybrania kopiowania z jednego pliku listy ACL w tym pliku zostaną skopiowane.

>[!NOTE]
>W przypadku korzystania z podajnika APD do zachowania list ACL z Data Lake Storage Gen1 do Gen2, istniejące listy ACL w Gen2's odpowiednich folderach/plikach zostaną nadpisywane.

>[!IMPORTANT]
>Po wybraniu opcji zachowania list kontroli dostępu należy się upewnić, że przyznano wystarczające uprawnienia, aby Data Factory działały na koncie ujścia Data Lake Storage Gen2. Na przykład Użyj uwierzytelniania klucza konta lub Przypisz rolę właściciela danych obiektu blob magazynu do nazwy głównej usługi lub tożsamości zarządzanej.

Podczas konfigurowania źródła jako Data Lake Storage Gen1 przy użyciu formatu binarnego lub opcji kopiowania binarnego i ujścia jako Data Lake Storage Gen2 za pomocą formatu binarnego lub opcji kopiowania binarnego można znaleźć opcję **Zachowaj** na stronie **Ustawienia** w narzędziu kopiowanie danych lub na karcie **Ustawienia** > **działania kopiowania** dla tworzenia działań.

![Data Lake Storage Gen1 zachować listy ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Oto przykład konfiguracji JSON działania kopiowania (zobacz `preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>Następne kroki

Zobacz inne artykuły dotyczące działania kopiowania:

- [Przegląd działania kopiowania](copy-activity-overview.md)
- [Wydajność działania kopiowania](copy-activity-performance.md)
