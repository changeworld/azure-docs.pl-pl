---
title: Usługi platformy Azure obsługujące usługę Azure Data Lake Storage Gen2 | Dokumenty firmy Microsoft
description: Dowiedz się, które usługi platformy Azure integrują się z usługą Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: edfcb1d0bfc87c84620b13ed26ec681fef32f1e3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878327"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Usługi platformy Azure obsługujące usługę Azure Data Lake Storage Gen2

Usługi platformy Azure umożliwiają pozyskiwania danych, przeprowadzania analiz i tworzenia reprezentacji wizualnych. Ten artykuł zawiera listę obsługiwanych usług platformy Azure, ujawnia ich poziom pomocy technicznej i zawiera łącza do artykułów, które ułatwiają korzystanie z tych usług za pomocą usługi Azure Data Lake Storage Gen2.

## <a name="supported-azure-services"></a>Obsługiwane usługi platformy Azure

W tej tabeli wymieniono usługi platformy Azure, których można używać z usługą Azure Data Lake Storage Gen2. Elementy, które pojawiają się w tych tabelach będą się zmieniać wraz z czasem, gdy obsługa nadal się rozwija.

> [!NOTE]
> Poziom pomocy technicznej odnosi się tylko do sposobu, w jaki usługa jest obsługiwana za pomocą usługi Data Lake Storage Gen 2.

|Usługa platformy Azure |Poziom wsparcia |Pokrewne artykuły: |
|---------------|-------------------|---|
|Azure Data Factory|Ogólnie dostępne|[Ładowanie danych do usługi Azure Data Lake Storage Gen2 za pomocą usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Ogólnie dostępne|[Używanie z usługą Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Szybki start: analizowanie danych w usłudze Azure Data Lake Storage Gen2 przy użyciu usługi Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Samouczek: wyodrębnianie, przekształcanie i ładowanie danych przy użyciu usługi Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Samouczek: dostęp do danych magazynu usługi Data Lake Gen2 za pomocą usługi Azure Databricks przy użyciu platformy Spark](data-lake-storage-use-databricks-spark.md)|
|Centrum zdarzeń Azure|Ogólnie dostępne|[Przechwytywanie zdarzeń za pośrednictwem usługi Azure Event Hubs w usłudze Azure Blob Storage lub usłudze Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Event Grid|Ogólnie dostępne|[Samouczek: Zaimplementowanie wzorca przechwytywania jeziora danych w celu zaktualizowania tabeli Databricks Delta](data-lake-storage-events.md)|
|Azure Logic Apps|Ogólnie dostępne|[Omówienie — co to jest usługa Azure Logic Apps?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|Ogólnie dostępne|[Uzyskiwanie dostępu do danych w usługach magazynu platformy Azure](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Usługa Azure Stream Analytics|Ogólnie dostępne|[Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Wyjście do usługi Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box|Ogólnie dostępne|[Używanie usługi Azure Data Box do migrowania danych z lokalnego magazynu HDFS do usługi Azure Storage](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |Ogólnie dostępne|[Korzystanie z usługi Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Korzystanie z interfejsu wiersza interfejsu interfejsu twardego HDFS z pamięcią masową Data Lake Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Samouczek: wyodrębnianie, przekształcanie i ładowanie danych przy użyciu gałęzi apache w usłudze Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|Usługa IoT Hub |Ogólnie dostępne|[Wysyłanie wiadomości z urządzenia do chmury za pomocą usługi IoT Hub do wysyłania wiadomości z urządzenia do chmury do różnych punktów końcowych](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|Ogólnie dostępne|[Analizowanie danych w usłudze Data Lake Storage Gen2 przy użyciu usługi Power BI](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|SQL Data Warehouse|Ogólnie dostępne|[Używanie z usługą Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|Usługi SQL Server Integration Services (SSIS)|Ogólnie dostępne|[Menedżer połączeń usługi Azure Storage](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Azure Cognitive Search|Wersja zapoznawcza|[Indeksowanie i przeszukiwanie dokumentów usługi Azure Data Lake Storage Gen2 (wersja zapoznawcza)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure Data Explorer|Wersja zapoznawcza|[Zapytanie o dane w usłudze Azure Data Lake przy użyciu Eksploratora danych platformy Azure](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)|
|Azure Content Delivery Network|Jeszcze nie jest obsługiwana|[Indeksowanie i przeszukiwanie dokumentów usługi Azure Data Lake Storage Gen2 (wersja zapoznawcza)](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>Zobacz też

- [Znane problemy z usługą Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Funkcje magazynu obiektów Blob dostępne w usłudze Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Platformy open source obsługujące usługę Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Dostęp do wielu protokołów w usłudze Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)