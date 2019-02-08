---
title: Integrowanie usługi Azure Data Lake Storage Gen2 z innymi usługami platformy Azure | Dokumentacja firmy Microsoft
description: Zrozumienie, jak usługi Azure Data Lake Storage Gen2 integruje się z innymi usługami platformy Azure
documentationcenter: ''
services: storage
author: normesta
ms.component: data-lake-storage-gen2
ms.service: storage
ms.devlang: na
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: nitinme
ms.openlocfilehash: 7bc4889403e1d52cfa3b18792a554f0faf605132
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55885676"
---
# <a name="integrate-azure-data-lake-storage-gen2-with-other-azure-services"></a>Integracja usługi Azure Data Lake Storage Gen2 z innymi usługami platformy Azure

Za pomocą usług platformy Azure do pozyskiwania, analizowanie i wizualizowanie danych na koncie magazynu Azure Data Lake Storage Gen2. Wybierz usługi, które najlepiej pasują do tego zadania, które mają być osiągnięte.

## <a name="ingest-data-into-your-data-lake"></a>Pozyskiwanie danych do usługi data lake

Te usługi pomagają możesz wypełnić Twojej usługi data lake z danymi.

### <a name="azure-data-factory"></a>Azure Data Factory

Możesz użyć [usługi Azure Data Factory](https://azure.microsoft.com/services/data-factory/) pozyskiwać dane z tych źródeł:

* Tabele platformy Azure
* Baz danych SQL Azure
* Azure SQL DataWarehouse
* Azure Storage Blobs
* Lokalne bazy danych

Zobacz [przenoszenie danych do i z Data Lake Storage Gen2 przy użyciu usługi Data Factory](../../data-factory/connector-azure-data-lake-store.md).

## <a name="analyze-and-visualize-data-in-your-data-lake"></a>Analizuj i wizualizuj dane w Twojej usłudze data lake

Te usługi mogą używać Data Lake Storage Gen2 jako punktu końcowego magazynu.

### <a name="azure-hdinsight"></a>Azure HDInsight

Możesz aprowizować [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) klastra, który używa Data Lake Storage Gen2 jako zgodna z systemem HDFS magazynu. W tej wersji w przypadku klastrów usługi Hadoop i platformy Storm w Windows i Linux, można użyć Data Lake Storage Gen2 tylko jako dodatkowego magazynu. Takie klastry nadal korzystać z usługi Azure Storage (WASB) jako magazyn domyślny. Jednak w przypadku klastrów HBase w systemach Windows i Linux, używając Data Lake Storage Gen2 jako magazynu domyślnego i dodatkowego miejsca do magazynowania.

Zobacz [klastrów użycia usługi Azure Data Lake magazynu Gen2 za pomocą usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)

### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

Możesz użyć [Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-overview.md) do pracy z danymi Big Data w skali chmury. Je dynamicznie udostępnia zasoby i umożliwia analizowanie eksabajtów danych. Usługa Data Lake Analytics jest zoptymalizowana do użytku Data Lake Storage Gen2 jako źródła danych. 

Zobacz [Rozpoczynanie pracy z usługą Data Lake Analytics przy użyciu Data Lake Storage Gen2](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="copy-data-to-other-repositories"></a>Kopiowanie danych do innych repozytoriów

Korzystać z tych usług, aby skopiować dane z usługi data lake i umieszczenie ich w pozostałych repozytoriach.

### <a name="sql-data-warehouse"></a>SQL Data Warehouse

Program PolyBase umożliwia ładowanie danych z Data Lake Storage Gen2 do usługi SQL Data Warehouse. Aby uzyskać więcej informacji, zobacz [Gen2 — magazynu jeziora danych użycia z usługą SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="see-also"></a>Zobacz także

* [Omówienie usługi Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
* [Za pomocą usługi Azure Data Lake Storage Gen2 dla wymagających danych big Data](data-lake-storage-data-scenarios.md)
