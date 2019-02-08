---
title: Scenariusze danych dotyczących usługi Azure Data Lake Storage Gen2 | Dokumentacja firmy Microsoft
description: Zapoznać się z różnymi scenariuszami i narzędzi przy użyciu danych, które można pozyskiwane, przetwarzane, pobrane i wizualizowane w Gen2 magazynu Data Lake z (wcześniej znane jako usługi Azure Data Lake Store)
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.openlocfilehash: 1c50a6e14955b2c31222ff1317aa99ad28866ec8
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864744"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Za pomocą usługi Azure Data Lake Storage Gen2 dla wymagających danych big Data

Przetwarzanie danych big Data są czterech etapach klucza:

* Wprowadzania dużych ilości danych w magazynie danych w czasie rzeczywistym lub w partiach
* Przetwarzanie danych
* Pobieranie danych
* Wizualizację danych

W tym artykule przyjrzymy się tych etapów w odniesieniu do usługi Azure Data Lake magazynu Gen2, aby zrozumieć opcje i narzędzi dostępnych w celu spełnienia konkretnych potrzeb obsługi danych big data.

## <a name="ingest-data-into-data-lake-storage-gen2"></a>Pozyskiwanie danych do Data Lake Storage Gen2
W tej sekcji opisano różne źródła danych i różne sposoby, w którym można pozyskać danych do konta Data Lake Storage Gen2.

![Pozyskiwanie danych do Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "pozyskiwania danych w Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Danych ad hoc
Ta pozycja reprezentuje mniejszych zestawów danych, które są używane do tworzenia prototypów aplikacji danych big data. Istnieją różne sposoby pozyskiwania danych ad hoc w zależności od źródła danych.

| Źródło danych | Za pomocą pozyskiwania |
| --- | --- |
| Komputer lokalny |<ul> <li>[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)</ul> |
| Azure Storage Blob |<ul> <li>[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)</li> <li>[Narzędzie AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[Uruchomione w klastrze HDInsight narzędzia DistCp](data-lake-storage-use-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Dane przesyłane strumieniowo
Reprezentuje dane, które mogą być generowane przez różnych źródeł, takich jak aplikacje, urządzenia, czujników itp. Te dane można pozyskać do Data Lake Storage Gen2 za pomocą różnych narzędzi. Te narzędzia będą zwykle przechwytywania i przetwarzania danych na podstawie zdarzeń, zdarzenia w czasie rzeczywistym i następnie zapisywania zdarzeń w partiach w Data Lake Storage Gen2 dzięki czemu mogą być dalej przetwarzane.

Poniżej przedstawiono narzędzia, których można użyć:

* [Usługa Azure HDInsight Storm](../../hdinsight/storm/apache-storm-write-data-lake-store.md) — możesz zapisać dane bezpośrednio do Data Lake Storage Gen2 z klastrem Storm.

### <a name="relational-data"></a>Dane relacyjne
Można również źródła danych z relacyjnej bazy danych. W okresie czasu relacyjne bazy danych zbierania bardzo duże ilości danych, co może zapewnić kluczowych wniosków, przetwarzanie za pomocą potoku danych big data. Aby przenieść dane do Data Lake Storage Gen2, można użyć następujących narzędzi.

* [Azure Data Factory](../../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dane dzienników serwera sieci Web (przy użyciu niestandardowych aplikacji przekazywania)
Ten typ zestawu danych zwrócono szczególną ponieważ analizy danych dziennika serwera sieci web jest typowy przypadek użycia dla aplikacji przetwarzających dane big Data i wymaga dużych woluminów, plików dziennika do przesłania do Data Lake Storage Gen2. Jedną z następujących narzędzi umożliwia pisanie własnego skryptu lub aplikacji do przekazania takich danych.

* [Azure Data Factory](../../data-factory/copy-activity-overview.md)

Do przekazywania danych dzienników serwera sieci web, a także do przekazywania innych typów danych (np. Dane społecznościowe tonacji) jest dobrym sposobem zapisu własnych niestandardowych skryptów/aplikacji, ponieważ zapewnia elastyczność obejmują przekazywanie składnik jako część danych większej aplikacji danych big data. W niektórych przypadkach ten kod może mieć postać skryptu lub narzędzie wiersza polecenia proste. W innych przypadkach kod może służyć do przetwarzania danych big Data Integrowanie aplikacji biznesowych lub rozwiązań.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dane skojarzone z klastrami usługi Azure HDInsight
Większość typów klastrów HDInsight (Hadoop, HBase, Storm) obsługują Data Lake Storage Gen2 jako repozytorium magazynów danych. Klastry HDInsight dostęp do danych z usługi Azure blob Storage (WASB). Lepszą wydajność możesz skopiować dane z WASB do konta Data Lake Storage Gen2 skojarzonego z klastrem. Aby skopiować dane, można użyć następujących narzędzi.

* [Apache DistCp](data-lake-storage-use-distcp.md)
* [Narzędzie AzCopy](../common/storage-use-azcopy-v10.md)
* [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dane przechowywane w lokalnych lub IaaS Hadoop klastrów
Duże ilości danych mogą być przechowywane w istniejących klastrów usługi Hadoop, lokalnie na komputerach przy użyciu systemu plików HDFS. Klastry usługi Hadoop może być we wdrożeniu w środowisku lokalnym lub może znajdować się w klastrze usługi IaaS na platformie Azure. Może to być wymagania dotyczące kopiowania tych danych do usługi Azure Data Lake Storage Gen2 jednorazowe podejście lub w sposób cykliczne. Istnieją różne opcje, których można to osiągnąć. Poniżej przedstawiono listę alternatywnych skojarzone wady i zalety.

| Podejście | Szczegóły | Zalety | Zagadnienia do rozważenia |
| --- | --- | --- | --- |
| Kopiowanie danych bezpośrednio z klastrami usługi Hadoop do usługi Azure Data Lake Storage Gen2 za pomocą usługi Azure Data Factory (ADF) |[ADF obsługuje system plików HDFS jako źródło danych](../../data-factory/connector-hdfs.md) |ADF zapewnia obsługę poza pole dla systemu plików HDFS oraz najwyższej klasy end-to-end, zarządzania i monitorowania |Wymaga bramy zarządzania danymi do wdrożenia lokalnie lub w modelu IaaS klastra |
| Kopiowanie danych z usługi Hadoop do usługi Azure Storage za pomocą narzędzia Distcp. Następnie skopiuj dane z usługi Azure Storage Data Lake Storage Gen2 przy użyciu odpowiedniego mechanizmu. |Możesz skopiować dane z usługi Azure Storage, Data Lake Storage Gen2 za pomocą: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[Narzędzie AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[Apache DistCp uruchamianie w klastrach HDInsight](data-lake-storage-use-distcp.md)</li></ul> |Można użyć narzędzi typu open source. |Wieloetapowy proces, który obejmuje wiele technologii |

### <a name="really-large-datasets"></a>Bardzo dużych zestawów danych
Do przekazywania zestawów danych, które zakresu w kilku terabajtów, za pomocą metod opisanych wyżej czasami może być powolne i kosztowne. W takich przypadkach można użyć poniższych opcji.

* **Przy użyciu usługi Azure ExpressRoute**. Usługa Azure ExpressRoute umożliwia tworzenie prywatnych połączeń między centrami danych platformy Azure a infrastrukturą lokalną. Zapewnia niezawodne opcja przesyłania dużych ilości danych. Aby uzyskać więcej informacji, zobacz [dokumentacji usługi Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-data-stored-in-data-lake-storage-gen2"></a>Przetwarzanie danych przechowywanych w Data Lake Storage Gen2
Gdy dane są dostępne w Data Lake Storage Gen2 analizy można uruchomić na tych danych przy użyciu aplikacji obsługiwanej danych big data. Obecnie można używać usługi Azure HDInsight i Azure Databricks uruchamiać zadania analizy danych na dane przechowywane w Data Lake Storage Gen2.

![Analizowanie danych w Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "analizy danych Data Lake Storage Gen2")

Aby uzyskać przykład, zobacz [użycia usługi Azure Data Lake magazynu Gen2 za pomocą usługi Azure HDInsight clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2).


## <a name="download-data-from-data-lake-storage-gen2"></a>Pobierz dane z Data Lake Storage Gen2
Można także pobrać lub przenieść dane z usługi Azure Data Lake Storage Gen2 dla scenariuszy, takich jak:

* Przenoszenie danych do innych repozytoriów w interfejsie przy użyciu istniejących potoków przetwarzania danych. Na przykład można przenieść dane z Data Lake Storage Gen2 do usługi Azure SQL Database lub lokalnego programu SQL Server.
* Pobierz dane na komputerze lokalnym do przetworzenia w środowiskach IDE podczas kompilowania aplikacji prototypów.

![Ruch wychodzący danych z Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "ruch wychodzący danych z Data Lake Storage Gen2")

W takich przypadkach można użyć dowolnej z następujących opcji:

* [Azure Data Factory](../../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-storage-use-distcp.md)

## <a name="visualize-data-in-data-lake-storage-gen2"></a>Wizualizowanie danych w Data Lake Storage Gen2
Kombinacja services umożliwia tworzenie wizualnej reprezentacji danych przechowywanych w Data Lake Storage Gen2.

![Wizualizowanie danych w Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/visualize-data.png "wizualizować dane w Data Lake Storage Gen2")

* Można uruchomić za pomocą [usługi Azure Data Factory, aby przenieść dane z Gen2 magazynu jezioro danych Azure SQL Data Warehouse](../../data-factory/copy-activity-overview.md)
* Po tym można [Integracja usługi Power BI z usługą Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) do tworzenia wizualnych reprezentacji danych.
