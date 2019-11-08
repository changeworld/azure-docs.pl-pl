---
title: Integracja Azure Data Lake Storage z usługami platformy Azure | Microsoft Docs
description: Dowiedz się, które usługi platformy Azure integrują się z Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.openlocfilehash: c7223274417ef4c911c32acbcde1511682d6d9e0
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796067"
---
# <a name="integrate-azure-data-lake-storage-with-azure-services"></a>Integracja Azure Data Lake Storage z usługami platformy Azure

Usług platformy Azure można używać do pozyskiwania danych, wykonywania analiz i tworzenia wizualizacji wizualnych. Ten artykuł zawiera listę obsługiwanych usług platformy Azure i zawiera linki do artykułów, które ułatwiają korzystanie z tych usług w programie Azure Data Lake Storage Gen2.

## <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Usługi platformy Azure, które obsługują Azure Data Lake Storage Gen2

W poniższej tabeli wymieniono usługi platformy Azure, które obsługują Azure Data Lake Storage Gen2.

| Usługa platformy Azure |  Pokrewne artykuły |
|---------------|-------------------|
|Azure Data Factory | [Załaduj dane do Azure Data Lake Storage Gen2 z Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks | [Używanie z Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Szybki Start: analizowanie danych w Azure Data Lake Storage Gen2 przy użyciu Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Samouczek: dostęp Data Lake Storage Gen2 danych za pomocą Azure Databricks przy użyciu platformy Spark](data-lake-storage-use-databricks-spark.md) |
|Przechwytywanie Event Hubs platformy Azure| [Przechwyć zdarzenia za pomocą usługi Azure Event Hubs na platformie Azure Blob Storage lub Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Logic Apps | [Przegląd — co to jest Azure Logic Apps?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|[Dostęp do danych w usługach Azure Storage](https://docs.microsoft.com/azure/machine-learning/service/how-to-access-data)|
|Azure Cognitive Search | [Indeksowanie i Wyszukiwanie Azure Data Lake Storage Gen2 dokumentów (wersja zapoznawcza)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure Stream Analytics| [Szybki Start: Tworzenie zadania Stream Analytics przy użyciu Azure Portal](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Ruch wychodzący do Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2) |
|Data Box|  [Używanie Azure Data Box do migrowania danych z lokalnego magazynu systemu plików HDFS do usługi Azure Storage](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight | [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Korzystanie z interfejsu wiersza polecenia systemu plików HDFS z Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu Apache Hive w usłudze Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md) |
|Usługa IoT Hub | [Używanie routingu komunikatów IoT Hub do wysyłania komunikatów z urządzenia do chmury do różnych punktów końcowych](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|  [Analizowanie danych w Data Lake Storage Gen2 przy użyciu Power BI](data-lake-storage-use-power-bi.md) |
|SQL Data Warehouse | [Używanie z Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|Usługi SQL Server Integration Services (SSIS) | [Menedżer połączeń usługi Azure Storage](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o narzędziach, których można użyć do przetwarzania danych w usłudze Data Lake. Zobacz [używanie Azure Data Lake Storage Gen2, aby uzyskać wymagania dotyczące danych Big Data](data-lake-storage-data-scenarios.md).

- Dowiedz się więcej o Data Lake Storage Gen2 i jak rozpocząć pracę z nim. Zobacz [wprowadzenie do Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
