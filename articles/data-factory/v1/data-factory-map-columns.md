---
title: Mapowanie kolumn zestawu danych w fabryce danych platformy Azure
description: Dowiedz się, jak mapować kolumny źródłowe do kolumn docelowych.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6eb7012e28319ee6cc86de5ee56090743d681068
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74923866"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Mapowanie kolumn źródłowego zestawu danych do docelowych kolumn zestawu danych
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. 

Mapowanie kolumn może służyć do określenia sposobu określania kolumn określonych w "strukturze" mapy tabeli źródłowej do kolumn określonych w "strukturze" tabeli ujścia. **Właściwość columnMapping** jest dostępna w sekcji **typeProperties** działania Kopiowanie.

Mapowanie kolumn obsługuje następujące scenariusze:

* Wszystkie kolumny w strukturze źródłowego zestawu danych są mapowane na wszystkie kolumny w strukturze zestawu danych ujścia.
* Podzbiór kolumn w strukturze źródłowego zestawu danych jest mapowany na wszystkie kolumny w strukturze zestawu danych ujścia.

Poniżej przedstawiono warunki błędu, które skutkują wyjątkiem:

* Mniej kolumn lub więcej kolumn w "strukturze" tabeli ujścia niż określono w mapowaniu.
* Mapowanie duplikatów.
* Wynik kwerendy SQL nie ma nazwy kolumny, która jest określona w mapowaniu.

> [!NOTE]
> Poniższe przykłady są dla usługi Azure SQL i Azure Blob, ale mają zastosowanie do dowolnego magazynu danych, który obsługuje prostokątne zestawy danych. Dostosuj definicje zestawu danych i połączonych usług w przykładach, aby wskazać dane w odpowiednim źródle danych.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Przykład 1 — mapowanie kolumn z usługi Azure SQL do obiektu blob platformy Azure
W tym przykładzie tabela wprowadzania ma strukturę i wskazuje na tabelę SQL w bazie danych SQL platformy Azure.

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

W tym przykładzie tabela danych wyjściowych ma strukturę i wskazuje na obiekt blob w magazynie obiektów blob platformy Azure.

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Następujący JSON definiuje działanie kopiowania w potoku. Kolumny ze źródła mapowane do kolumn w zlewie **(columnMappings)** przy użyciu **Translator** właściwości.

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**Przepływ mapowania kolumn:**

![Przepływ mapowania kolumn](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Przykład 2 — mapowanie kolumn z kwerendą SQL z usługi Azure SQL do obiektu blob platformy Azure
W tym przykładzie kwerenda SQL jest używana do wyodrębniania danych z usługi Azure SQL zamiast po prostu określania nazwy tabeli i nazw kolumn w sekcji "struktura". 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
W takim przypadku wyniki kwerendy są najpierw mapowane na kolumny określone w "strukturze" źródła. Następnie kolumny ze źródła "struktura" są mapowane do kolumn w ujściu "struktura" z regułami określonymi w kolumnieMapowanie.  Załóżmy, że kwerenda zwraca 5 kolumn, dwie kolumny więcej niż określone w "struktury" źródła.

**Przepływ mapowania kolumn**

![Przepływ mapowania kolumn-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Następne kroki
Zobacz artykuł, aby zapoznać się z samouczkiem na temat używania działania kopiowania: 

- [Kopiowanie danych z magazynu obiektów blob do bazy danych SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
