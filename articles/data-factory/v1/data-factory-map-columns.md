---
title: Mapowanie kolumny zestawu danych w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie mapowania kolumny źródłowe kolumny docelowej.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f1dd8c4c5e38547492887425bb5732d87d5f8071
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045888"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Mapowanie kolumny źródłowe zestawu danych do kolumny zestawu danych docelowego
> [!NOTE]
> Ten artykuł dotyczy wersji 1 fabryki danych. 

Mapowanie kolumny może służyć do określania, jak kolumny określone w "strukturę" mapy źródłowej tabeli kolumn określona w "structure" tabeli ujścia. **ColumnMapping** właściwość jest dostępna w **typeProperties** sekcji działanie kopiowania.

Mapowanie kolumny obsługuje następujące scenariusze:

* Wszystkie kolumny w strukturze zestawu danych źródła są zamapowane na wszystkie kolumny w strukturze zestawu danych obiektu sink.
* Podzbiór kolumn w strukturze zestawu danych źródła jest zamapowana na wszystkie kolumny w strukturze zestawu danych obiektu sink.

Poniżej znajdują się błędy, które powodują powstanie wyjątku:

* Mniejszą liczbę kolumn lub większą liczbę kolumn w "strukturze" tabeli ujścia niż określona w mapowaniu.
* Zduplikowane mapowania.
* Wynik kwerendy SQL nie ma nazwy kolumny, która jest określona w mapowaniu.

> [!NOTE]
> Poniższe przykłady są Azure SQL i obiektów Blob platformy Azure, ale są stosowane do dowolnego magazynem danych, który obsługuje prostokątne zestawów danych. Dostosuj zestawu danych i definicje połączonej usługi w przykładach, aby wskazywał danych w źródle danych.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Przykład 1 – kolumny mapowanie z bazy danych SQL Azure do obiektów blob platformy Azure
W tym przykładzie Tabela wejściowa ma strukturę i wskazuje tabeli SQL w bazie danych Azure SQL.

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

W tym przykładzie tabela wyjściowa ma strukturę i wskazuje obiekt blob w magazynie obiektów blob platformy Azure.

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

Następujące JSON definiuje działanie kopiowania w potoku. Kolumny źródłowej zamapowany na kolumny w ujściu (**columnMappings**) przy użyciu **Translator** właściwości.

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
**Przepływ mapowanie kolumn:**

![Przepływ mapowania kolumn](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Przykład 2 — z zapytania SQL z bazy danych SQL Azure obiektów blob platformy Azure w mapowaniu kolumn
W tym przykładzie zapytanie SQL służy do wyodrębniania danych z bazy danych SQL Azure zamiast określania po prostu nazwę tabeli i nazwy kolumn w sekcji "structure". 

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
W takim przypadku wyniki zapytania najpierw są mapowane na kolumny określone w "structure" źródła. Następnie kolumn ze źródła "structure" są mapowane na kolumny w sink "structure" z regułami określonymi w columnMappings.  Załóżmy, że zapytanie zwraca 5 kolumn, dwóch kolumn niż określone w "structure" źródła.

**Przepływ mapowania kolumn**

![Mapowanie kolumny przepływu-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Kolejne kroki
Samouczek dotyczący używania działania kopiowania, zobacz artykuł: 

- [Kopiowanie danych z magazynu obiektów Blob do bazy danych SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
