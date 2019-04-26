---
title: Integrowanie usługi Azure Data Lake Storage Gen1 z innymi usługami platformy Azure | Dokumentacja firmy Microsoft
description: Zrozumienie, jak usługi Azure Data Lake Storage Gen1 integruje się z innymi usługami platformy Azure
documentationcenter: ''
services: data-lake-store
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: e28863f9980d6403bef1f88de01b7a9b5271b444
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60197089"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Integrowanie usługi Azure Data Lake Storage Gen1 z innymi usługami platformy Azure
Azure Data Lake Storage Gen1 może służyć w połączeniu z innymi usługami platformy Azure w celu realizacji szerszego zakresu scenariuszy. Następujący artykuł zawiera listę usług, które Data Lake Storage Gen1 można zintegrować z.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Data Lake Storage Gen1 za pomocą usługi Azure HDInsight
Możesz aprowizować [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) klastra, który używa Data Lake Storage Gen1 jako zgodna z systemem HDFS magazynu. W tej wersji w przypadku klastrów usługi Hadoop i platformy Storm w Windows i Linux, można użyć Data Lake Storage Gen1 tylko jako dodatkowego magazynu. Takie klastry nadal korzystać z usługi Azure Storage (WASB) jako magazyn domyślny. Jednak w przypadku klastrów HBase w systemach Windows i Linux, możesz użyć Data Lake Storage Gen1 jako magazynu domyślnego i/lub dodatkowego miejsca do magazynowania.

Aby uzyskać instrukcje dotyczące sposobu inicjowania obsługi klastra HDInsight z Data Lake Storage Gen1 zobacz:

* [Aprowizowanie klastra usługi HDInsight przy użyciu Data Lake Storage Gen1 przy użyciu witryny Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Aprowizowanie klastra usługi HDInsight za pomocą programu Data Lake Storage Gen1 jako magazynem domyślnym przy użyciu programu Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Aprowizowanie klastra usługi HDInsight za pomocą programu Data Lake Storage Gen1 jako dodatkowego magazynu za pomocą programu Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Data Lake Storage Gen1 za pomocą usługi Azure Data Lake Analytics
[Usługa Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) umożliwia pracę z danymi Big Data w skali chmury. Dynamicznie udostępnia zasoby i umożliwia analizowanie terabajtów, a nawet eksabajtów danych, które mogą być przechowywane w liczbie obsługiwanych źródeł danych, jeden z nich jest Data Lake Storage Gen1. Usługa Data Lake Analytics jest specjalnie zoptymalizowana pod kątem pracy z Gen1 magazynu Data Lake — zapewnia najwyższy poziom wydajności, przepływności i przetwarzania równoległego na potrzeby obciążeń dużymi ilościami danych.

Aby uzyskać instrukcje dotyczące sposobu używania usługi Data Lake Analytics przy użyciu Data Lake Storage Gen1, zobacz [Rozpoczynanie pracy z usługą Data Lake Analytics przy użyciu Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Data Lake Storage Gen1 za pomocą usługi Azure Data Factory
Możesz użyć [usługi Azure Data Factory](https://azure.microsoft.com/services/data-factory/) pozyskiwać dane z tabel platformy Azure, usługi Azure SQL Database, Azure SQL DataWarehouse, obiektów blob usługi Azure Storage i lokalnych baz danych. Trwa jest "pełnoprawnym obywatelem" w ekosystemie platformy Azure, Azure Data Factory może służyć do organizowania pozyskiwania danych ze źródła tych Data Lake Storage Gen1.

Aby uzyskać instrukcje dotyczące sposobu używania usługi Azure Data Factory za pomocą programu Data Lake Storage Gen1, zobacz [przenoszenie danych do i z Data Lake Storage Gen1 przy użyciu usługi Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Kopiowanie danych z obiektów blob usługi Azure Storage do Data Lake Storage Gen1
Azure Data Lake Storage Gen1 udostępnia narzędzia wiersza polecenia narzędzia AdlCopy, która umożliwia kopiowanie danych z usługi Azure Blob Storage do konta Data Lake Storage Gen1. Aby uzyskać więcej informacji, zobacz [kopiowanie danych z obiektów blob usługi Azure Storage do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Kopiowanie danych między środowiskiem usługi Azure SQL Database i Data Lake Storage Gen1
Przy użyciu narzędzia Apache Sqoop do importowania i eksportowania danych między Azure SQL Database i Data Lake Storage Gen1. Aby uzyskać więcej informacji, zobacz [Gen1 programu Data Lake Storage i Azure SQL database przy użyciu narzędzia Sqoop kopiować dane między](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Data Lake Storage Gen1 za pomocą usługi Stream Analytics
Data Lake Storage Gen1 jako dane wyjściowe służy do przechowywania danych przesyłane strumieniowo przy użyciu usługi Azure Stream Analytics. Aby uzyskać więcej informacji, zobacz [Stream dane z rozszerzenia Azure Storage Blob do Data Lake Storage Gen1 przy użyciu usługi Azure Stream Analytics](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Data Lake Storage Gen1 za pomocą usługi Power BI
Usługa Power BI umożliwia importowanie danych z konta Data Lake Storage Gen1 do analizy i wizualizacji danych. Aby uzyskać więcej informacji, zobacz [analizy danych Data Lake Storage Gen1 przy użyciu usługi Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Za pomocą usług Data Lake Storage Gen1 wykazu danych
Dane z Data Lake Storage Gen1 możesz zarejestrować się do usługi Azure Data Catalog, aby stał się wykrywalny w całej organizacji danych. Aby uzyskać więcej informacji, zobacz [zarejestrować dane z Data Lake Storage Gen1 w usłudze Azure Data Catalog](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>Za pomocą usług Data Lake Storage Gen1 usług SQL Server Integration Services (SSIS)
Nawiązywanie połączeń z Data Lake Storage Gen1 pakietu SSIS, można użyć Menedżera połączeń Data Lake Storage Gen1 w SSIS. Aby uzyskać więcej informacji, zobacz [Użyj Data Lake Storage Gen1 przy użyciu funkcji SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-storage-gen1-with-sql-data-warehouse"></a>Za pomocą usług Data Lake Storage Gen1 usługa SQL Data Warehouse
Program PolyBase umożliwia ładowanie danych z Data Lake Storage Gen1 do usługi SQL Data Warehouse. Aby uzyskać więcej informacji, zobacz [Użyj Data Lake Storage Gen1 z usługą SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Data Lake Storage Gen1 za pomocą usługi Azure Event Hubs
Za pomocą usługi Azure Data Lake Storage Gen1 archiwum i przechwytywania danych odebranych przez usługi Azure Event Hubs. Aby uzyskać więcej informacji, zobacz [Użyj Data Lake Storage Gen1 za pomocą usługi Azure Event Hubs](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Zobacz także
* [Omówienie usługi Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Rozpoczynanie pracy z usługą Data Lake Storage Gen1 przy użyciu portalu](data-lake-store-get-started-portal.md)
* [Rozpoczynanie pracy z usługą Data Lake Storage Gen1 przy użyciu programu PowerShell](data-lake-store-get-started-powershell.md)  

