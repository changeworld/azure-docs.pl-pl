---
title: Integracja usługi Azure Data Lake Storage Gen1 z innymi usługami platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak usługa Azure Data Lake Storage Gen1 integruje się z innymi usługami platformy Azure
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
ms.openlocfilehash: 43024b63a355646f607adbb6623cc6c349374ea8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535599"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Integracja usługi Azure Data Lake Storage Gen1 z innymi usługami platformy Azure
Usługa Azure Data Lake Storage Gen1 może być używana w połączeniu z innymi usługami platformy Azure, aby włączyć szerszy zakres scenariuszy. W poniższym artykule wymieniono usługi, z którymi można zintegrować program Data Lake Storage Gen1.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Korzystanie z usługi Data Lake Storage Gen1 w usłudze Azure HDInsight
Można aprowizować klaster [usługi Azure HDInsight,](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) który używa magazynu usługi Data Lake Gen1 jako magazynu zgodnego z usługą HDFS. W tej wersji dla klastrów Hadoop i Storm w systemach Windows i Linux można używać usługi Data Lake Storage Gen1 tylko jako dodatkowego magazynu. Takie klastry nadal używają usługi Azure Storage (WASB) jako magazynu domyślnego. Jednak w przypadku klastrów HBase w systemach Windows i Linux można użyć usługi Data Lake Storage Gen1 jako magazynu domyślnego lub dodatkowego magazynu lub obu tych obrażeń.

Aby uzyskać instrukcje dotyczące sposobu aprowizowania klastra HDInsight przy pomocą usługi Data Lake Storage Gen1, zobacz:

* [Aprowizuj klaster usługi HDInsight przy użyciu usługi Data Lake Storage Gen1 przy użyciu witryny Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Aprowizuj klaster usługi HDInsight przy użyciu magazynu usługi Data Lake Storage Gen1 jako magazynu domyślnego przy użyciu programu Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Aprowizuj klaster usługi HDInsight za pomocą usługi Data Lake Storage Gen1 jako dodatkowego magazynu przy użyciu programu Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Korzystanie z usługi Data Lake Storage Gen1 w usłudze Azure Data Lake Analytics
[Usługa Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) umożliwia pracę z big data w skali chmury. Dynamicznie aporuje zasoby i umożliwia analizowanie terabajtów lub nawet eksabajtów danych, które mogą być przechowywane w wielu obsługiwanych źródłach danych, z których jednym jest Data Lake Storage Gen1. Usługa Data Lake Analytics jest specjalnie zoptymalizowana do pracy z usługą Data Lake Storage Gen1 — zapewnia najwyższy poziom wydajności, przepływności i równoległości dla obciążeń dużych zbiorów danych.

Aby uzyskać instrukcje dotyczące korzystania z usługi Data Lake Analytics z funkcją Data Lake Storage Gen1, zobacz Wprowadzenie do [usługi Data Lake Analytics przy użyciu usługi Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Korzystanie z usługi Data Lake Storage Gen1 w usłudze Azure Data Factory
[Usługa Azure Data Factory](https://azure.microsoft.com/services/data-factory/) umożliwia pozyskiwania danych z tabel platformy Azure, bazy danych SQL Azure, usługi Azure SQL DataWarehouse, obiektów blob usługi Azure Storage i lokalnych baz danych. Będąc obywatelem pierwszej klasy w ekosystemie platformy Azure, usługa Azure Data Factory może służyć do organizowania pozyskiwania danych z tego źródła do usługi Data Lake Storage Gen1.

Aby uzyskać instrukcje dotyczące korzystania z usługi Azure Data Factory z usługą Data Lake Storage Gen1, zobacz [Przenoszenie danych do i z usługi Data Lake Storage Gen1 przy użyciu usługi Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Kopiowanie danych z obiektów blob usługi Azure Storage do usługi Data Lake Storage Gen1
Usługa Azure Data Lake Storage Gen1 udostępnia narzędzie wiersza polecenia AdlCopy, które umożliwia kopiowanie danych z usługi Azure Blob Storage na konto Usługi Data Lake Storage Gen1. Aby uzyskać więcej informacji, zobacz [Kopiowanie danych z obiektów blob usługi Azure Storage do usługi Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Kopiowanie danych między usługą Azure SQL Database i data lake storage Gen1
Apache Sqoop służy do importowania i eksportowania danych między usługą Azure SQL Database i Data Lake Storage Gen1. Aby uzyskać więcej informacji, zobacz [Kopiowanie danych między bazą danych Data Lake Storage Gen1 i bazą danych SQL platformy Azure przy użyciu funkcji Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Korzystanie z usługi Data Lake Storage Gen1 w usłudze Stream Analytics
Można użyć Data Lake Storage Gen1 jako jeden z wyjść do przechowywania danych przesyłanych strumieniowo przy użyciu usługi Azure Stream Analytics. Aby uzyskać więcej informacji, zobacz [Przesyłanie strumieniowe danych z obiektu blob usługi Azure Storage do usługi Data Lake Storage Gen1 przy użyciu usługi Azure Stream Analytics.](data-lake-store-stream-analytics.md)

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Korzystanie z usługi Data Lake Storage Gen1 w usłudze Power BI
Za pomocą usługi Power BI można importować dane z konta Data Lake Storage Gen1 w celu analizowania i wizualizowania danych. Aby uzyskać więcej informacji, zobacz [Analizowanie danych w programie Data Lake Storage Gen1 przy użyciu usługi Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Korzystanie z usługi Data Lake Storage Gen1 z wykazem danych
Można zarejestrować dane z usługi Data Lake Storage Gen1 w usłudze Azure Data Catalog, aby dane były wykrywalne w całej organizacji. Aby uzyskać więcej informacji, zobacz [Rejestrowanie danych z usługi Data Lake Storage Gen1 w usłudze Azure Data Catalog](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>Korzystanie z usługi Data Lake Storage Gen1 z usługami integracji programu SQL Server (SSIS)
Za pomocą menedżera połączeń Data Lake Storage Gen1 w SSIS można połączyć pakiet SSIS z usługą Data Lake Storage Gen1. Aby uzyskać więcej informacji, zobacz [Korzystanie z usługi Data Lake Storage Gen1 z SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-storage-gen1-with-sql-data-warehouse"></a>Korzystanie z usługi Data Lake Storage Gen1 z magazynem danych SQL
Za pomocą polybase można załadować dane z usługi Data Lake Storage Gen1 do magazynu danych SQL. Aby uzyskać więcej informacji, zobacz [Korzystanie z magazynu danych Lake Gen1 z magazynem danych SQL](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Korzystanie z usługi Data Lake Storage Gen1 w usłudze Azure Event Hubs
Za pomocą usługi Azure Data Lake Storage Gen1 można archiwizować i przechwytywać dane odebrane przez usługi Azure Event Hubs. Aby uzyskać więcej informacji, zobacz [Korzystanie z usługi Data Lake Storage Gen1 w usłudze Azure Event Hubs](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Zobacz też
* [Omówienie usługi Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Wprowadzenie do usługi Data Lake Storage Gen1 przy użyciu portalu](data-lake-store-get-started-portal.md)
* [Wprowadzenie do usługi Data Lake Storage Gen1 przy użyciu programu PowerShell](data-lake-store-get-started-powershell.md)  

