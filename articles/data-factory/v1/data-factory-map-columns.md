---
title: Mapowanie kolumny zestawu danych w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zamapować kolumny źródłowe kolumny docelowe.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1b009ac2ca42e9804b88989b55b2e73524732550
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60238150"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Mapowanie kolumny zestawu danych źródła do docelowego zestawu danych kolumn
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. 

Mapowanie kolumn może służyć do określenia, jak kolumny określone w "Struktura" Mapowanie tabel źródłowych do kolumn określonych w "strukturę" w tabeli ujścia. **ColumnMapping** właściwość jest dostępna w **typeProperties** części działania kopiowania.

Mapowanie kolumny obsługuje następujące scenariusze:

* Wszystkie kolumny w strukturze zestawu danych źródłowych są mapowane do wszystkich kolumn w strukturze zestawu danych ujścia.
* Podzbiór kolumn w strukturze zestawu danych źródła jest zamapowana na wszystkie kolumny w strukturze zestawu danych ujścia.

Dostępne są następujące warunki błędów, które powoduje wyjątek:

* Mniejszą liczbę kolumn lub większą liczbę kolumn "strukturę" tabeli ujścia niż określony w mapowaniu.
* Zduplikowane mapowania.
* Wynik zapytania SQL nie ma nazwę kolumny, który jest określony w mapowaniu.

> [!NOTE]
> Poniższe przykłady są przeznaczone dla usługi Azure SQL i obiektów Blob platformy Azure, ale mają zastosowanie do wszelkich magazyn danych, który obsługuje prostokątnych zestawów danych. Dostosuj zestaw danych i definicji usługi połączonej w przykładach, aby wskazać dane w źródle danych.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Przykład 1 — kolumny mapowanie z bazy danych SQL Azure, do obiektów blob platformy Azure
W tym przykładzie Tabela wejściowa ma strukturę i wskazuje tabelę SQL w bazie danych Azure SQL.

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

W tym przykładzie tabela danych wyjściowych ma strukturę i wskazuje obiekt blob w usłudze Azure blob storage.

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

Następujący kod JSON definiuje działania kopiowania w potoku. Kolumny ze źródła mapowane na kolumny w ujściu (**columnMappings**) przy użyciu **Translator** właściwości.

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
**Przepływ mapowanie kolumny:**

![Przepływ mapowanie kolumn](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Przykład 2 — kolumny mapowanie z zapytania SQL z bazy danych SQL Azure do obiektów blob platformy Azure
W tym przykładzie zapytanie SQL jest używany do wyodrębniania danych z bazy danych SQL Azure zamiast określania po prostu nazwę tabeli i nazwy kolumn w sekcji "strukturę". 

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
W tym przypadku wyniki zapytania najpierw są mapowane na kolumny określone w "strukturę" źródła. Następnie kolumny ze źródła "strukturę" są mapowane na kolumny w ujściu "strukturę" za pomocą reguł określonych w columnMappings.  Załóżmy, że zapytanie zwraca 5 kolumn, dwóch kolumn niż określone w "strukturę" źródła.

**Przepływ mapowanie kolumn**

![Mapowanie kolumny flow-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Kolejne kroki
Samouczek dotyczący za pomocą działania kopiowania, zobacz artykuł: 

- [Kopiowanie danych z magazynu obiektów Blob do bazy danych SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
