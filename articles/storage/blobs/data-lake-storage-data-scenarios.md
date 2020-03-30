---
title: Scenariusze danych obejmujące usługę Azure Data Lake Storage Gen2 | Dokumenty firmy Microsoft
description: Poznaj różne scenariusze i narzędzia, za pomocą których dane mogą być pozyskiwania, przetwarzania, pobierania i wizualizowane w Data Lake Storage Gen2 (wcześniej znany jako Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b0ebe6cb505fa2a145dd3cbb94398912f2933a4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77369716"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Korzystanie z usługi Azure Data Lake Storage Gen2 dla wymagań dotyczących dużych zbiorów danych

Istnieją cztery kluczowe etapy przetwarzania dużych zbiorów danych:

> [!div class="checklist"]
> * Połknąć duże ilości danych do magazynu danych w czasie rzeczywistym lub w partiach
> * Przetwarzanie danych
> * Pobieranie danych
> * Wizualizacja danych

W tym artykule przedstawiono opcje i narzędzia dla każdej fazy przetwarzania.

Aby uzyskać pełną listę usług platformy Azure, których można używać z usługą Azure Data Lake Storage Gen2, zobacz [Integrowanie usługi Azure Data Lake Storage z usługami platformy Azure](data-lake-storage-integrate-with-azure-services.md)

## <a name="ingest-the-data-into-data-lake-storage-gen2"></a>Połknąć dane do Data Lake Storage Gen2

W tej sekcji przedstawiono różne źródła danych i różne sposoby pozyskiwania tych danych na konto Data Lake Storage Gen2.

![Połknąć dane do usługi Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Połknąć dane do usługi Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Dane ad hoc

Reprezentuje to mniejsze zestawy danych, które są używane do tworzenia prototypów aplikacji dużych zbiorów danych. Istnieją różne sposoby pozyskiwania danych ad hoc w zależności od źródła danych. 

Oto lista narzędzi, których można użyć do pozyskiwania danych ad hoc.

| Źródło danych | Połknieć go za pomocą |
| --- | --- |
| Komputer lokalny |[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)<br><br>[Interfejs wiersza polecenia platformy Azure](data-lake-storage-directory-file-acl-cli.md)<br><br>[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Narzędzie AzCopy](../common/storage-use-azcopy-v10.md)|
| Azure Storage Blob |[Fabryka danych platformy Azure](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Narzędzie AzCopy](../common/storage-use-azcopy-v10.md)<br><br>[DistCp uruchomiony w klastrze USŁUGI HDInsight](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Przesyłane strumieniowo dane

Reprezentuje to dane, które mogą być generowane przez różne źródła, takie jak aplikacje, urządzenia, czujniki itp. Te dane mogą być pochłonięte do usługi Data Lake Storage Gen2 za pomocą różnych narzędzi. Te narzędzia zwykle przechwytują i przetwarzają dane na podstawie zdarzenia po zdarzeniu w czasie rzeczywistym, a następnie zapisują zdarzenia partiami w udziale usługi Data Lake Storage Gen2, aby mogły być dalej przetwarzane.

Oto lista narzędzi, których można użyć do pozyskiwania przesyłanych strumieniowo danych.

|Narzędzie | Wskazówki |
|---|--|
|Azure Stream Analytics|[Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Wyjście do usługi Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Burza usługi Azure HDInsight | [Napisz do Apache Hadoop HDFS z Apache Storm na HDInsight](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>Dane relacyjne

Można również pozyskiwać dane z relacyjnych baz danych. W danym okresie relacyjne bazy danych zbierają ogromne ilości danych, które mogą dostarczyć kluczowych informacji, jeśli są przetwarzane za pośrednictwem potoku dużych zbiorów danych. Aby przenieść takie dane do usługi Data Lake Storage Gen2, można użyć następujących narzędzi.

Oto lista narzędzi, których można użyć do pozyskiwania danych relacyjnych.

|Narzędzie | Wskazówki |
|---|--|
|Azure Data Factory | [Działanie kopiowania w usłudze Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dane dziennika serwera sieci Web (przekazywanie przy użyciu aplikacji niestandardowych)

Ten typ zestawu danych jest specjalnie wywoływany, ponieważ analiza danych dziennika serwera sieci web jest typowym przypadkiem użycia dla aplikacji dużych zbiorów danych i wymaga przekazywania dużych ilości plików dziennika do usługi Data Lake Storage Gen2. Do przesyłania takich danych można użyć dowolnego z następujących narzędzi do pisania własnych skryptów lub aplikacji.

Oto lista narzędzi, których można używać do pozyskiwania danych dziennika serwera sieci Web.

|Narzędzie | Wskazówki |
|---|--|
|Azure Data Factory | [Działanie kopiowania w usłudze Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |
|Interfejs wiersza polecenia platformy Azure|[Interfejs wiersza polecenia platformy Azure](data-lake-storage-directory-file-acl-cli.md)|
|Azure PowerShell|[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)|

W przypadku przesyłania danych dziennika serwera www, a także przesyłania innych rodzajów danych (np. danych dotyczących nastrojów społecznościowych), dobrym podejściem jest pisanie własnych niestandardowych skryptów/aplikacji, ponieważ zapewnia elastyczność w dołączaniu komponentu przesyłania danych jako części większej aplikacji do przesyłania dużych zbiorów danych. W niektórych przypadkach kod ten może mieć postać skryptu lub prostego narzędzia wiersza polecenia. W innych przypadkach kod może służyć do integracji przetwarzania dużych zbiorów danych do aplikacji biznesowej lub rozwiązania.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dane skojarzone z klastrami usługi Azure HDInsight

Większość typów klastrów HDInsight (Hadoop, HBase, Storm) obsługuje pamięć masową Data Lake Storage Gen2 jako repozytorium magazynu danych. Klastry usługi HDInsight uzyskują dostęp do danych z obiektów blob usługi Azure Storage (WASB). Aby uzyskać lepszą wydajność, można skopiować dane z wasb do konta Data Lake Storage Gen2 skojarzone z klastrem. Do skopiowania danych można użyć następujących narzędzi.

Oto lista narzędzi, których można użyć do pozyskiwania danych skojarzonych z klastrami HDInsight.

|Narzędzie | Wskazówki |
|---|--|
|Apache DistCp | [Kopiowanie danych między obiektami blob usługi Azure Storage i usługi Azure Data Lake Storage Gen2 za pomocą protokołu DistCp](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|Narzędzie AzCopy | [Przesyłanie danych za pomocą](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [Kopiowanie danych do lub z usługi Azure Data Lake Storage Gen2 przy użyciu usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dane przechowywane w lokalnych lub iaaS klastrach Hadoop

Duże ilości danych mogą być przechowywane w istniejących klastrach Hadoop, lokalnie na komputerach korzystających z usługi HDFS. Klastry Hadoop mogą być we wdrożeniu lokalnym lub mogą znajdować się w klastrze IaaS na platformie Azure. Może istnieć wymagania dotyczące kopiowania takich danych do usługi Azure Data Lake Storage Gen2 w celu jednorazowego podejścia lub w sposób cykliczny. Istnieją różne opcje, których można użyć, aby to osiągnąć. Poniżej znajduje się lista alternatyw i związanych z nimi kompromisów.

| Podejście | Szczegóły | Zalety | Zagadnienia do rozważenia |
| --- | --- | --- | --- |
| Kopiowanie danych bezpośrednio z klastrów usługi Hadoop do usługi Azure Data Lake Storage Gen2 za pomocą usługi Azure Data Factory (ADF) |[ADF obsługuje system PLIKÓW HDFS jako źródło danych](../../data-factory/connector-hdfs.md) |ADF zapewnia gotowe wsparcie dla systemu plików HDFS oraz kompleksowe zarządzanie i monitorowanie |Wymaga wdrożenia bramy zarządzania danymi lokalnie lub w klastrze IaaS |
| Użyj Distcp, aby skopiować dane z usługi Hadoop do usługi Azure Storage. Następnie skopiuj dane z usługi Azure Storage do Data Lake Storage Gen2 przy użyciu odpowiedniego mechanizmu. |Dane z usługi Azure Storage do usługi Data Lake Storage Gen2 można kopiować przy użyciu: <ul><li>[Fabryka danych platformy Azure](../../data-factory/copy-activity-overview.md)</li><li>[Narzędzie AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[Apache DistCp uruchomiony w klastrach HDInsight](data-lake-storage-use-distcp.md)</li></ul> |Można użyć narzędzi open source. |Wieloetapowy proces, który obejmuje wiele technologii |

### <a name="really-large-datasets"></a>Naprawdę duże zestawy danych

W przypadku przekazywania zestawów danych, które mają zasięg kilku terabajtów, przy użyciu metod opisanych powyżej może być czasami powolne i kosztowne. W takich przypadkach można użyć usługi Azure ExpressRoute.  

Usługa Azure ExpressRoute umożliwia tworzenie prywatnych połączeń między centrami danych platformy Azure a infrastrukturą w twojej siedzibie. Zapewnia to niezawodną opcję przesyłania dużych ilości danych. Aby dowiedzieć się więcej, zobacz [dokumentację usługi Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-the-data"></a>Przetwarzanie danych

Gdy dane są dostępne w Data Lake Storage Gen2 można uruchomić analizę tych danych przy użyciu obsługiwanych aplikacji big data. 

![Analizowanie danych w umiar pamięci masowej usługi Data Lake Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "Analizowanie danych w umiar pamięci masowej usługi Data Lake Gen2")

Oto lista narzędzi, których można użyć do uruchamiania zadań analizy danych na danych przechowywanych w umiań przechowywania danych.

|Narzędzie | Wskazówki |
|---|--|
|Azure HDInsight | [Korzystanie z usługi Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Usługa Azure Data Lake Storage 2. generacji](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Szybki start: analizowanie danych w usłudze Azure Data Lake Storage Gen2 przy użyciu usługi Azure Databricks](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Samouczek: wyodrębnianie, przekształcanie i ładowanie danych przy użyciu usługi Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Wizualizacja danych

Za pomocą łącznika usługi Power BI można tworzyć wizualne reprezentacje danych przechowywanych w programie Data Lake Storage Gen2. Zobacz [Analizowanie danych w usłudze Azure Data Lake Storage Gen2 przy użyciu usługi Power BI](https://docs.microsoft.com/power-query/connectors/datalakestorage).

## <a name="download-the-data"></a>Pobierz dane

Możesz również pobrać lub przenieść dane z usługi Azure Data Lake Storage Gen2 w scenariuszach takich jak:

* Przenoszenie danych do innych repozytoriów w celu interfejsu z istniejącymi potokami przetwarzania danych. Na przykład można przenieść dane z usługi Data Lake Storage Gen2 do usługi Azure SQL Database lub lokalnego programu SQL Server.

* Pobieranie danych na komputer lokalny w celu przetwarzania w środowiskach IDE podczas tworzenia prototypów aplikacji.

![Dane wychodzące z usługi Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Dane wychodzące z usługi Data Lake Storage Gen2")

Oto lista narzędzi, których można używać do pobierania danych z usługi Data Lake Storage Gen2.

|Narzędzie | Wskazówki |
|---|--|
|Azure Data Factory | [Działanie kopiowania w usłudze Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCp | [Kopiowanie danych między obiektami blob usługi Azure Storage i usługi Azure Data Lake Storage Gen2 za pomocą protokołu DistCp](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|Eksplorator usługi Azure Storage|[Używanie Eksploratora usługi Azure Storage do zarządzania katalogami, plikami i listami AL w usłudze Azure Data Lake Storage Gen2](data-lake-storage-explorer.md)|
|Narzędzie AzCopy|[Przesyłanie danych za pomocą pamięci masowej AzCopy i Blob](../common/storage-use-azcopy-blobs.md)|
