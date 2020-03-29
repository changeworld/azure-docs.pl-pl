---
title: Wywoływanie procedury składowanej z działania kopiowania usługi Azure Data Factory
description: Dowiedz się, jak wywołać procedurę składowaną w usłudze Azure SQL Database lub SQL Server z działania kopiowania usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d05c2b03a0c498144f37c9b6205053120a596b09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74924101"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Wywoływanie procedury składowanej z działania kopiowania w usłudze Azure Data Factory
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [przekształcanie danych przy użyciu działania procedury składowanej w usłudze Data Factory](../transform-data-using-stored-procedure.md).


Podczas kopiowania danych do [programu SQL Server](data-factory-sqlserver-connector.md) lub usługi Azure SQL [Database](data-factory-azure-sql-connector.md)można skonfigurować **działanie SqlSink** w kopiowaniu w celu wywołania procedury składowanej. Procedura składowana umożliwia wykonanie dodatkowego przetwarzania (scalanie kolumn, wyszukiwanie wartości, wstawianie do wielu tabel itp.) jest wymagane przed wstawieniem danych do tabeli docelowej. Ta funkcja korzysta z [parametrów wycenionych w tabeli](https://msdn.microsoft.com/library/bb675163.aspx). 

W poniższym przykładzie pokazano, jak wywołać procedurę składowaną w bazie danych programu SQL Server z potoku usługi Data Factory (działanie kopiowania):  

## <a name="output-dataset-json"></a>Wyjściowy zestaw danych JSON
W wyjściowym zestawie danych JSON ustaw **typ:** **SqlServerTable**. Ustaw go na **AzureSqlTable** do użycia z bazą danych SQL platformy Azure. Wartość **właściwości tableName** musi być zgodna z nazwą pierwszego parametru procedury składowanej.  

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
Zdefiniuj sekcję **SqlSink** w działaniu kopiowania JSON w następujący sposób. Aby wywołać procedurę składowaną podczas wstawiania danych do bazy danych ujścia/miejsca docelowego, należy określić wartości dla właściwości **SqlWriterStoredProcedureName** i **SqlWriterTableType.** Aby uzyskać opisy tych właściwości, zobacz [Sekcję SqlSink w artykule łącznika programu SQL Server](data-factory-sqlserver-connector.md#sqlsink).

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
W bazie danych zdefiniuj procedurę składowaną o tej samej nazwie co **SqlWriterStoredProcedureName**. Procedura składowana obsługuje dane wejściowe z magazynu danych źródłowych i wstawia dane do tabeli w docelowej bazie danych. Nazwa pierwszego parametru procedury składowanej musi być zgodna z nazwą tabeli zdefiniowaną w zestawie danych JSON (Marketing).

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
W bazie danych zdefiniuj typ tabeli o takiej samej nazwie jak **SqlWriterTableType**. Schemat typu tabeli musi być zgodny ze schematem wejściowego zestawu danych.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>Następne kroki
Przejrzyj następujące artykuły łącznika, które dla kompletnych przykładów JSON: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
