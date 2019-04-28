---
title: Wywołaj procedurę składowaną z działania kopiowania w fabryce danych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wywołać procedurę składowaną w usłudze Azure SQL Database lub SQL Server z działania kopiowania w usłudze Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 35e9347039a7b9939ab4d2719f9738429dec168c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60824256"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Wywołaj procedurę składowaną z działaniem kopiowania w usłudze Azure Data Factory
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [przekształcania danych za pomocą działania procedury składowanej w usłudze Data Factory](../transform-data-using-stored-procedure.md).


Podczas kopiowania danych do [programu SQL Server](data-factory-sqlserver-connector.md) lub [usługi Azure SQL Database](data-factory-azure-sql-connector.md), można skonfigurować **SqlSink** w działaniu kopiowania, aby wywołać procedurę składowaną. Możesz chcieć użyć przechowywaną procedurę, aby wykonywać żadnego dodatkowego przetwarzania (Scalanie kolumn wyszukiwania wartości, wstawiania do wielu tabel, itp.) jest wymagana przed wstawieniem danych w tabeli docelowej. Ta funkcja korzysta z zalet [parametry Table-Valued](https://msdn.microsoft.com/library/bb675163.aspx). 

Poniższy przykład pokazuje jak wywołać procedurę składowaną w bazie danych programu SQL Server z potoku usługi fabryka danych (działanie kopiowania):  

## <a name="output-dataset-json"></a>Wyjściowy zestaw danych JSON
W przypadku wyjściowy zestaw danych JSON, ustaw **typu** do: **SqlServerTable**. Ustaw ją na **AzureSqlTable** za pomocą usługi Azure SQL database. Wartość **tableName** właściwość musi być zgodna z nazwą pierwszy parametr procedury składowanej.  

```json
{
  "name": "SqlOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlLinkedService",
    "typeProperties": {
      "tableName": "Marketing"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

## <a name="sqlsink-section-in-copy-activity-json"></a>Sekcja SqlSink w działaniu kopiowania JSON
Zdefiniuj **SqlSink** sekcji w działaniu kopiowania JSON w następujący sposób. Aby wywołać procedurę składowaną podczas wstawiania danych do bazy danych będących ujściem/docelowym, należy określić wartości dla obu **SqlWriterStoredProcedureName** i **SqlWriterTableType** właściwości. Opisy tych właściwości można znaleźć [SqlSink sekcji tego artykułu łącznik programu SQL Server](data-factory-sqlserver-connector.md#sqlsink).

```json
"sink":
{
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
    "storedProcedureParameters":
            {
                "stringData": 
                {
                    "value": "str1"     
                }
            }
}
```

## <a name="stored-procedure-definition"></a>Definicja procedury składowanej 
W bazie danych, zdefiniuj procedurę składowaną z taką samą nazwę jak **SqlWriterStoredProcedureName**. Procedura składowana obsługuje danych wejściowych z magazynu danych źródłowych i wstawia dane do tabeli w docelowej bazie danych. Nazwa pierwszego parametru procedury składowanej musi odpowiadać tableName zdefiniowane w zestawie danych JSON (Marketing).

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```

## <a name="table-type-definition"></a>Definicja typu tabeli
W bazie danych, zdefiniuj typ tabeli z taką samą nazwę jak **SqlWriterTableType**. Schematu typu tabeli muszą być zgodne ze schematem wejściowego zestawu danych.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj poniższe łącznika artykuły, aby uzyskać kompletny przykład JSON: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
