---
title: Wywołaj procedurę składowaną z działania kopiowania fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wywołać procedurę przechowywaną w bazie danych SQL Azure lub programu SQL Server z działania kopiowania fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e75573513f107977e1d5fe62fbae89cb4439e0e9
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048955"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Wywołanie procedury składowanej z działania kopiowania w fabryce danych Azure
> [!NOTE]
> Ten artykuł dotyczy wersji 1 fabryki danych. Jeśli używasz bieżącą wersję usługi fabryka danych, zobacz [Przekształcanie danych za pomocą działania procedury składowanej w fabryce danych](../transform-data-using-stored-procedure.md).


Podczas kopiowania danych do [programu SQL Server](data-factory-sqlserver-connector.md) lub [bazy danych SQL Azure](data-factory-azure-sql-connector.md), można skonfigurować **SqlSink** w przypadku działania kopiowania, aby wywołać procedurę składowaną. Może zajść potrzeba użycia zapisana procedura do wykonania żadnych dodatkowych czynności (Scalanie kolumn wyszukiwania wartości i wstawia go do wielu tabel, itp.) jest wymagana przed wstawieniem danych w tabeli docelowej. Ta funkcja korzysta z [zwracającej tabelę parametrów](https://msdn.microsoft.com/library/bb675163.aspx). 

Poniższy przykład przedstawia sposób wywołania procedury przechowywanej w bazie danych programu SQL Server z potoku fabryki danych (działanie kopiowania):  

## <a name="output-dataset-json"></a>Wyjściowy zestaw danych JSON
W danych wyjściowych JSON, ustaw **typu** do: **SqlServerTable**. Ustaw ją na **AzureSqlTable** do korzystania z bazy danych Azure SQL. Wartość **tableName** właściwości musi odpowiadać nazwie pierwszego parametru procedury składowanej.  

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

## <a name="sqlsink-section-in-copy-activity-json"></a>Sekcja SqlSink w przypadku działania kopiowania JSON
Zdefiniuj **SqlSink** sekcji w przypadku działania kopiowania JSON w następujący sposób. Aby wywołać procedurę składowaną podczas wstawiania do odbioru/docelowej bazy danych, określ wartości dla obu **SqlWriterStoredProcedureName** i **SqlWriterTableType** właściwości. Aby uzyskać opis tych właściwości, zobacz [SqlSink części tego artykułu łącznika programu SQL Server](data-factory-sqlserver-connector.md#sqlsink).

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
W bazie danych, należy zdefiniować procedury składowanej z taką samą nazwę jak **SqlWriterStoredProcedureName**. Procedura składowana obsługuje dane wejściowe z magazynu danych źródła i wstawia dane do tabeli w docelowej bazie danych. Nazwa pierwszego parametru procedury składowanej musi odpowiadać tableName zdefiniowany w zestawie danych JSON (Marketing).

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
W bazie danych, określ typ tabeli z taką samą nazwę jak **SqlWriterTableType**. Schemat tabeli musi być zgodny schemat zestawu danych wejściowych.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj następujące łącznika artykuły, które pełną przykłady JSON: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
