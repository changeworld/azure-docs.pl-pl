---
title: Przekształcanie danych za pomocą działania procedury składowanej w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Opis sposobu używania działania dotyczącego procedury składowanej programu SQL Server, aby wywołać procedurę składowaną w usługi Azure SQL Database/Data Warehouse z potoku usługi fabryka danych.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/27/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 806654b7586895b62b014a49b8b3a00fb18f008f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764411"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Przekształcanie danych za pomocą działania procedury składowanej programu SQL Server w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-stored-proc-activity.md)
> * [Bieżąca wersja](transform-data-using-stored-procedure.md)

Użyj działania przekształcania danych w usłudze Data Factory [potoku](concepts-pipelines-activities.md) do przekształcania i Przetwarzaj danych pierwotnych w prognozy i szczegółowych informacji. Działania dotyczącego procedury składowanej jest jednym z działania przekształcania, które obsługuje usługi Data Factory. W tym artykule opiera się na [przekształcania danych](transform-data.md) artykułu, który przedstawia ogólny przegląd działań przekształcania obsługiwanych w usłudze Data Factory i przekształcania danych.

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zapoznaj się z artykułem [wprowadzenie do usługi Azure Data Factory](introduction.md) i wykonać instrukcje z samouczka: [Samouczek: Przekształcanie danych](tutorial-transform-data-spark-powershell.md) przed przeczytaniem tego artykułu. 

Działania dotyczącego procedury składowanej umożliwia wywoływanie procedury składowanej w jednym z następujących magazynów danych w przedsiębiorstwie lub na maszynie wirtualnej platformy Azure (VM): 

- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server Database.  Jeśli używasz programu SQL Server, zainstaluj własne środowisko integration runtime na tym samym komputerze, który jest hostem bazy danych lub na osobnym komputerze, który ma dostęp do bazy danych. Własne środowisko integration runtime jest składnikiem, który nawiązuje połączenie danych źródła w lokalnych/na maszynie Wirtualnej platformy Azure z usługami w chmurze w sposób bezpieczny i zarządzane. Zobacz [może być samodzielnie hostowane środowisko IR](create-self-hosted-integration-runtime.md) artykuł, aby uzyskać szczegółowe informacje.

> [!IMPORTANT]
> Podczas kopiowania danych do usługi Azure SQL Database lub SQL Server, można skonfigurować **SqlSink** w działaniu kopiowania, aby wywołać procedurę składowaną przy użyciu **sqlWriterStoredProcedureName** właściwości. Aby uzyskać szczegółowe informacje o właściwości zobacz następujące artykuły łącznika: [Usługa Azure SQL Database](connector-azure-sql-database.md), [programu SQL Server](connector-sql-server.md). Wywoływanie procedury składowanej podczas kopiowania danych do usługi Azure SQL Data Warehouse za pomocą działania kopiowania nie jest obsługiwane. Jednak działanie procedury składowanej umożliwia wywoływanie procedury przechowywanej w usłudze SQL Data Warehouse. 
>
> Podczas kopiowania danych z usługi Azure SQL Database lub SQL Server lub usługi Azure SQL Data Warehouse, możesz skonfigurować **SqlSource** w działaniu kopiowania, aby wywołać procedurę przechowywaną, aby odczytać danych ze źródłowej bazy danych za pomocą  **sqlReaderStoredProcedureName** właściwości. Aby uzyskać więcej informacji zobacz następujące artykuły łącznika: [Usługa Azure SQL Database](connector-azure-sql-database.md), [programu SQL Server](connector-sql-server.md), [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Szczegóły składni
Oto formatu JSON do definiowania działania dotyczącego procedury składowanej:

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

W poniższej tabeli opisano te właściwości kodu JSON:

| Właściwość                  | Opis                              | Wymagane |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | Nazwa działania                     | Yes      |
| description               | Tekst opisujący przeznaczenie działania | Nie       |
| type                      | Dla działania dotyczącego procedury składowanej jest typ działania **SqlServerStoredProcedure** | Yes      |
| linkedServiceName         | Odwołanie do **usługi Azure SQL Database** lub **Azure SQL Data Warehouse** lub **programu SQL Server** zarejestrowany jako połączonej usługi w usłudze Data Factory. Aby dowiedzieć się więcej na temat tej połączonej usługi, zobacz [usługi połączone usługi Compute](compute-linked-services.md) artykułu. | Yes      |
| storedProcedureName       | Określ nazwę procedury składowanej do wywołania. | Yes      |
| storedProcedureParameters | Określ wartości dla parametrów procedury składowanej. Użyj `"param1": { "value": "param1Value","type":"param1Type" }` do przekazywania wartości parametrów i ich typu, obsługiwane przez źródło danych. Jeśli musisz przekazać wartości null dla parametru, użyj `"param1": { "value": null }` (wszystkie małe litery). | Nie       |

## <a name="error-info"></a>Informacje o błędzie

Jeśli procedura składowana nie powiedzie się, zwraca szczegóły błędu nie można przechwycić informacje o błędzie bezpośrednio w danych wyjściowych działania. Jednak fabryka danych pompy wszystkie jego działania, uruchom zdarzeń do usługi Azure Monitor. Wśród zdarzenia, Data Factory pompy do usługi Azure Monitor umieszcza ono szczegóły błędu istnieje. Można na przykład zdefiniować wiadomości e-mail dla alertów z tych zdarzeń. Aby uzyskać więcej informacji, zobacz [alertów i monitorowania fabryki danych przy użyciu usługi Azure Monitor](monitor-using-azure-monitor.md).

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły, które wyjaśniają, jak przekształcać dane w inny sposób: 

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie technologii hive](transform-data-using-hadoop-hive.md)
* [Działania technologii pig](transform-data-using-hadoop-pig.md)
* [Działania technologii MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działania przesyłania strumieniowego usługi Hadoop](transform-data-using-hadoop-streaming.md)
* [Działania platformy Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie wykonywania Bach Machine Learning](transform-data-using-machine-learning.md)
* [Działania procedur składowanych](transform-data-using-stored-procedure.md)
