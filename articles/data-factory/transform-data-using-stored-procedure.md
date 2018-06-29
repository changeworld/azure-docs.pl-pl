---
title: Przekształcanie danych za pomocą działania procedury składowanej w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak używać programu SQL Server działania dotyczącego procedury składowanej do wywołania procedury przechowywanej w magazyn danych/bazy danych SQL Azure z potoku fabryki danych.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: e8e0f8352404892ea8af6a0fa176c336dd2c1659
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054028"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Przekształcanie danych za pomocą działania procedury składowanej SQL Server w fabryce danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [W wersji 1](v1/data-factory-stored-proc-activity.md)
> * [Bieżąca wersja](transform-data-using-stored-procedure.md)

Użyj działania przekształcania danych w fabryce danych [potoku](concepts-pipelines-activities.md) do transformacji i przetwarzać dane pierwotne służące do przewidywania i szczegółowych informacji. Działania dotyczącego procedury składowanej jest jednym z działania przekształcania, które obsługuje fabryki danych. W tym artykule opiera się na [przekształcania danych](transform-data.md) artykułu, który przedstawia ogólny przegląd transformacji danych i działań przekształcania obsługiwanych w fabryce danych.

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi fabryka danych Azure, zapoznaj się z artykułem [wprowadzenie do fabryki danych Azure](introduction.md) i wykonaj samouczka: [samouczek: przekształcania danych](tutorial-transform-data-spark-powershell.md) przed przeczytaniem tego artykułu. 

Działania dotyczącego procedury składowanej umożliwia wywołanie procedury składowanej w jednym z następujących magazynów danych w przedsiębiorstwie lub na maszynie wirtualnej platformy Azure (VM): 

- Azure SQL Database
- Azure SQL Data Warehouse
- Baza danych programu SQL Server.  Jeśli używasz programu SQL Server, należy zainstalować siebie integrację środowiska uruchomieniowego na tym samym komputerze, który jest hostem bazy danych lub na osobnym komputerze, który ma dostęp do bazy danych. Hostowanie Samoobsługowe integrację środowiska uruchomieniowego jest składnik, który nawiązuje połączenie danych źródeł na lokalnym/na maszynie Wirtualnej platformy Azure z usługami w chmurze w sposób bezpieczny i zarządzanie nimi. Zobacz [środowiska uruchomieniowego integracji hosta samodzielnego](create-self-hosted-integration-runtime.md) artykułu, aby uzyskać szczegółowe informacje.

> [!IMPORTANT]
> Podczas kopiowania danych do usługi Azure SQL Database lub SQL Server, można skonfigurować **SqlSink** w przypadku działania kopiowania, aby wywołać procedurę składowaną przy użyciu **sqlWriterStoredProcedureName** właściwości. Aby uzyskać więcej informacji dotyczących właściwości, zobacz następujące artykuły łącznika: [bazy danych SQL Azure](connector-azure-sql-database.md), [programu SQL Server](connector-sql-server.md). Wywoływanie procedury składowanej podczas kopiowania danych do usługi Azure SQL Data Warehouse przy użyciu aktywność kopiowania nie jest obsługiwane. Jednak działania procedury składowanej służy do wywołania procedury przechowywanej w usłudze SQL Data Warehouse. 
>
> Podczas kopiowania danych z bazy danych SQL Azure lub programu SQL Server lub magazyn danych SQL Azure, możesz skonfigurować **SqlSource** w przypadku działania kopiowania, aby wywołać procedurę składowaną można odczytać danych ze źródłowej bazy danych przy użyciu  **sqlReaderStoredProcedureName** właściwości. Aby uzyskać więcej informacji, zobacz następujące artykuły łącznika: [bazy danych SQL Azure](connector-azure-sql-database.md), [programu SQL Server](connector-sql-server.md), [magazyn danych SQL Azure](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Szczegóły składni
Oto formatu JSON określających działania dotyczącego procedury składowanej:

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
        "storedProcedureName": "sp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

W poniższej tabeli opisano te właściwości JSON:

| Właściwość                  | Opis                              | Wymagane |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | Nazwa działania                     | Yes      |
| description               | Tekst opisujący działanie służy do | Nie       |
| type                      | Dla działania dotyczącego procedury składowanej, jest typu działania **SqlServerStoredProcedure** | Yes      |
| linkedServiceName         | Odwołanie do **bazy danych SQL Azure** lub **magazyn danych SQL Azure** lub **programu SQL Server** zarejestrowany jako połączonej usługi z fabryki danych. Aby dowiedzieć się więcej na temat tej połączonej usługi, zobacz [obliczeniowe połączonych usług](compute-linked-services.md) artykułu. | Yes      |
| storedProcedureName       | Określ nazwę procedury składowanej do wywołania. | Yes      |
| storedProcedureParameters | Określ wartości dla parametrów procedury składowanej. Użyj `"param1": { "value": "param1Value","type":"param1Type" }` do przekazania wartości parametrów i typem obsługiwane przez źródło danych. Jeśli trzeba przekazać wartości null dla parametru, użyj `"param1": { "value": null }` (małe litery). | Nie       |

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły, które opisują sposób przekształcania danych w inny sposób: 

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie gałęzi](transform-data-using-hadoop-hive.md)
* [Działanie pig](transform-data-using-hadoop-pig.md)
* [Działania MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działaniu przesyłania strumieniowego usługi Hadoop](transform-data-using-hadoop-streaming.md)
* [Działanie Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie wykonywania Bach uczenia maszynowego](transform-data-using-machine-learning.md)
* [Działania procedury składowanej](transform-data-using-stored-procedure.md)
