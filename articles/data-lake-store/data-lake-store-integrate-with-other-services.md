---
title: Integracja z innymi usługami Azure Data Lake Store | Dokumentacja firmy Microsoft
description: Zrozumienie, jak Data Lake Store integruje się z innymi usługami platformy Azure
documentationcenter: ''
services: data-lake-store
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 3fd1b03d2ce0b814f453ae4d87a136f28479662d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624101"
---
# <a name="integrating-data-lake-store-with-other-azure-services"></a>Integrating Data Lake Store with other Azure Services (Integracja usługi Data Lake Store z innymi usługami platformy Azure)
Azure Data Lake Store można w połączeniu z innymi usługami Azure realizacji szerszego zakresu scenariuszy. Artykuł zawiera listę usług, które można zintegrować z usługą Data Lake Store.

## <a name="use-data-lake-store-with-azure-hdinsight"></a>Użyj Data Lake Store z usługą Azure HDInsight
Umożliwia obsługę [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) klastra, który używa usługi Data Lake Store jako zgodne z systemem plików HDFS magazynu. W tej wersji dla klastrów platformy Hadoop i Storm w systemach Windows i Linux, można użyć usługi Data Lake Store tylko jako dodatkowego magazynu. Takie klastry nadal używać usługi Azure Storage (WASB) jako magazyn domyślny. Jednak w przypadku klastrów HBase w systemach Windows i Linux, można użyć usługi Data Lake Store jako domyślnego magazynu, i/lub dodatkowego miejsca do magazynowania.

Aby uzyskać instrukcje dotyczące sposobu udostępniania klastra usługi HDInsight z usługą Data Lake Store zobacz:

* [Udostępnić klastra usługi HDInsight przy użyciu portalu Azure usługi Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Udostępnianie klastra usługi HDInsight z usługą Data Lake Store jako domyślny magazyn przy użyciu programu Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Udostępnianie klastra usługi HDInsight z usługą Data Lake Store jako dodatkowego magazynu za pomocą programu Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-store-with-azure-data-lake-analytics"></a>Użyj Data Lake Store z usługi Azure Data Lake Analytics
[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) umożliwia pracę z danymi Big Data w skali chmury. Dynamicznie udostępnia zasoby i umożliwia terabajtów, a nawet eksabajtów danych, które mogą być przechowywane w wielu obsługiwanych źródeł danych, jednego z nich usługi Data Lake Store. Data Lake Analytics jest specjalnie zoptymalizowana pod kątem pracy z usługi Azure Data Lake Store - zapewnia najwyższy poziom wydajności, przepływności i przetwarzania równoległego na potrzeby obciążeń danych big data.

Aby uzyskać instrukcje dotyczące sposobu używania usługi Data Lake Analytics z usługą Data Lake Store, zobacz [wprowadzenie do usługi Data Lake Analytics przy użyciu usługi Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-store-with-azure-data-factory"></a>Użyj Data Lake — magazyn z fabryką danych Azure
Można użyć [fabryki danych Azure](https://azure.microsoft.com/services/data-factory/) do przyjmowania danych z tabel Azure, baza danych SQL Azure, Magazyn danych SQL Azure, obiektach blob magazynu Azure i lokalnych baz danych. Pierwszej klasie obywateli w ekosystemie usługi Azure, fabryki danych Azure może być używane do organizowania wprowadzanie danych z tych źródła do usługi Azure Data Lake Store.

Aby uzyskać instrukcje dotyczące sposobu używania fabryki danych Azure z usługą Data Lake Store, zobacz [przenoszenie danych do i z usługi Data Lake Store przy użyciu fabryki danych](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-store"></a>Kopiowanie danych z obiektów blob magazynu Azure do usługi Data Lake Store
Azure Data Lake Store udostępnia narzędzie wiersza polecenia, AdlCopy, która umożliwia kopiowanie danych z magazynu obiektów Blob Azure do konta usługi Data Lake Store. Aby uzyskać więcej informacji, zobacz [skopiowanie danych z obiektów blob magazynu Azure do usługi Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-store"></a>Kopiowanie danych między bazą danych SQL Azure i usługi Data Lake Store
Apache Sqoop umożliwia importowanie i eksportowanie danych między bazą danych SQL Azure i usługi Data Lake Store. Aby uzyskać więcej informacji, zobacz [kopiowanie danych między Data Lake Store i bazy danych Azure SQL przy użyciu Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-store-with-stream-analytics"></a>Użyj Data Lake Store z usługi analiza strumienia
Data Lake Store wśród dane wyjściowe służy do przechowywania danych strumieniowego przy użyciu usługi Azure Stream Analytics. Aby uzyskać więcej informacji, zobacz [strumienia danych z obiektu Blob magazynu Azure do usługi Data Lake Store za pomocą usługi Azure Stream Analytics](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-store-with-power-bi"></a>Użyj Data Lake Store przy użyciu usługi Power BI
Usługa Power BI służy do importowania danych z konta usługi Data Lake Store do analizowania i wizualizacji danych. Aby uzyskać więcej informacji, zobacz [analizowanie danych w usłudze Data Lake Store za pomocą usługi Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-store-with-data-catalog"></a>Użyj Data Lake Store z wykazu danych
Dane z usługi Data Lake Store można zarejestrować do usługi Azure Data Catalog, aby były one wykrywalny w całej organizacji. Aby uzyskać więcej informacji, zobacz [zarejestrować dane z usługi Data Lake Store w usłudze Azure Data Catalog](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-store-with-sql-server-integration-services-ssis"></a>Użyj Data Lake Store z usług SQL Server Integration Services (SSIS)
Menedżer połączeń usługi Azure Data Lake Store w SSIS umożliwia pakietów SSIS Uzyskuj dostęp do usługi Azure Data Lake Store. Aby uzyskać więcej informacji, zobacz [Użyj Data Lake Store z SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-store-with-sql-data-warehouse"></a>Użyj Data Lake Store z usługą Magazyn danych SQL
Program PolyBase umożliwia ładowanie danych z usługi Azure Data Lake Store do usługi SQL Data Warehouse. Aby uzyskać więcej informacji, zobacz [Użyj Data Lake Store z usługą Magazyn danych SQL](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-store-with-azure-event-hubs"></a>Użyj Data Lake Store przy użyciu usługi Azure Event Hubs
Azure Data Lake Store można użyć do archiwum i przechwytywania danych odebranych przez usługi Azure Event Hubs. Aby uzyskać więcej informacji, zobacz [Użyj Data Lake Store z usługą Azure Event Hubs](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Zobacz także
* [Omówienie usługi Azure Data Lake Store](data-lake-store-overview.md)
* [Wprowadzenie do usługi Data Lake Store za pomocą portalu](data-lake-store-get-started-portal.md)
* [Wprowadzenie do usługi Data Lake Store za pomocą programu PowerShell](data-lake-store-get-started-powershell.md)  

