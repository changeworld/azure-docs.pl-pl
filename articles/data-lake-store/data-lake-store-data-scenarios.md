---
title: Scenariusze danych obejmujące Data Lake Storage Gen1 | Dokumentacja firmy Microsoft
description: Zapoznać się z różnymi scenariuszami i narzędzi przy użyciu danych, które można pozyskiwane, przetwarzane, pobrane i wizualizowane w Data Lake Storage Gen1, (wcześniej znane jako usługi Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: 19743dcd2866b8fc7b6ad1fdf387b134f7b3ca50
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2018
ms.locfileid: "43783080"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>Za pomocą usługi Azure Data Lake Storage Gen1 dla wymagających danych big Data

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Przetwarzanie danych big Data są czterech etapach klucza:

* Wprowadzania dużych ilości danych w magazynie danych w czasie rzeczywistym lub w partiach
* Przetwarzanie danych
* Pobieranie danych
* Wizualizację danych

W tym artykule przyjrzymy się tych etapów w odniesieniu do usługi Azure Data Lake Store, aby zrozumieć opcje i narzędzi dostępnych w celu spełnienia konkretnych potrzeb obsługi danych big data.

## <a name="ingest-data-into-data-lake-store"></a>Pozyskiwanie danych do programu Data Lake Store
W tej sekcji opisano różne źródła danych i różne sposoby, w którym można pozyskać danych do konta Data Lake Store.

![Pozyskiwanie danych do programu Data Lake Store](./media/data-lake-store-data-scenarios/ingest-data.png "pozyskiwania danych w Data Lake Store")

### <a name="ad-hoc-data"></a>Danych ad hoc
Ta pozycja reprezentuje mniejszych zestawów danych, które są używane do tworzenia prototypów aplikacji danych big data. Istnieją różne sposoby pozyskiwania danych ad hoc w zależności od źródła danych.

| Źródło danych | Za pomocą pozyskiwania |
| --- | --- |
| Komputer lokalny |<ul> <li>[Azure Portal](data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Dla wielu platform wiersza polecenia platformy Azure 2.0](data-lake-store-get-started-cli-2.0.md)</li> <li>[Przy użyciu narzędzi Data Lake Tools for Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Azure Storage Blob |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[Narzędzia AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Uruchomione w klastrze HDInsight narzędzia DistCp](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Dane przesyłane strumieniowo
Reprezentuje dane, które mogą być generowane przez różnych źródeł, takich jak aplikacje, urządzenia, czujników itp. Te dane można pozyskać do Data Lake Store za pomocą różnych narzędzi. Te narzędzia będą zwykle przechwytywania i przetwarzania danych na podstawie zdarzeń, zdarzenia w czasie rzeczywistym i następnie zapisywania zdarzeń w partiach w Data Lake Store dzięki czemu mogą być dalej przetwarzane.

Poniżej przedstawiono narzędzia, których można użyć:

* [Usługa Azure Stream Analytics](../stream-analytics/stream-analytics-data-lake-output.md) -zdarzeń pozyskanych do usługi Event Hubs można zapisać w usłudze Azure Data Lake przy użyciu danych wyjściowych usługi Azure Data Lake Store.
* [Usługa Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md) — możesz zapisać dane bezpośrednio do programu Data Lake Store z klastrem Storm.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) — może odbierać zdarzenia z usługi Event Hubs, a następnie zapisać go do programu Data Lake Store za pomocą [zestawu .NET SDK usługi Data Lake Store](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Dane relacyjne
Można również źródła danych z relacyjnej bazy danych. W okresie czasu relacyjne bazy danych zbierania bardzo duże ilości danych, co może zapewnić kluczowych wniosków, przetwarzanie za pomocą potoku danych big data. Aby przenieść dane do Data Lake Store, można użyć następujących narzędzi.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dane dzienników serwera sieci Web (przy użyciu niestandardowych aplikacji przekazywania)
Ten typ zestawu danych zwrócono szczególną ponieważ analizy danych dziennika serwera sieci web jest typowy przypadek użycia dla aplikacji przetwarzających dane big Data i wymaga dużych woluminów, plików dziennika do przesłania do Data Lake Store. Jedną z następujących narzędzi umożliwia pisanie własnego skryptu lub aplikacji do przekazania takich danych.

* [Dla wielu platform wiersza polecenia platformy Azure 2.0](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Zestaw SDK platformy .NET usługi Azure Data Lake Store](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

Do przekazywania danych dzienników serwera sieci web, a także do przekazywania innych typów danych (np. Dane społecznościowe tonacji) jest dobrym sposobem zapisu własnych niestandardowych skryptów/aplikacji, ponieważ zapewnia elastyczność obejmują przekazywanie składnik jako część danych większej aplikacji danych big data. W niektórych przypadkach ten kod może mieć postać skryptu lub narzędzie wiersza polecenia proste. W innych przypadkach kod może służyć do przetwarzania danych big Data Integrowanie aplikacji biznesowych lub rozwiązań.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dane skojarzone z klastrami usługi Azure HDInsight
Większość typów klastrów HDInsight (Hadoop, HBase, Storm) obsługują Data Lake Store jako repozytorium magazynów danych. Klastry HDInsight dostęp do danych z usługi Azure blob Storage (WASB). Lepszą wydajność możesz skopiować dane z WASB do konta Data Lake Store, skojarzonego z klastrem. Aby skopiować dane, można użyć następujących narzędzi.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [Usługa narzędzia AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dane przechowywane w lokalnych lub IaaS Hadoop klastrów
Duże ilości danych mogą być przechowywane w istniejących klastrów usługi Hadoop, lokalnie na komputerach przy użyciu systemu plików HDFS. Klastry usługi Hadoop może być we wdrożeniu w środowisku lokalnym lub może znajdować się w klastrze usługi IaaS na platformie Azure. Może to być wymagania, aby skopiować tych danych do usługi Azure Data Lake Store, jednorazowe podejście lub w sposób cykliczne. Istnieją różne opcje, których można to osiągnąć. Poniżej przedstawiono listę alternatywnych skojarzone wady i zalety.

| Podejście | Szczegóły | Zalety | Zagadnienia do rozważenia |
| --- | --- | --- | --- |
| Kopiowanie danych bezpośrednio z klastrami usługi Hadoop do usługi Azure Data Lake Store za pomocą usługi Azure Data Factory (ADF) |[ADF obsługuje system plików HDFS jako źródło danych](../data-factory/connector-hdfs.md) |ADF zapewnia obsługę poza pole dla systemu plików HDFS oraz najwyższej klasy end-to-end, zarządzania i monitorowania |Wymaga bramy zarządzania danymi do wdrożenia lokalnie lub w modelu IaaS klastra |
| Eksportowanie danych z usługi Hadoop, co pliki. Następnie skopiuj pliki do usługi Azure Data Lake Store, przy użyciu odpowiedniego mechanizmu. |Można kopiować pliki do usługi Azure Data Lake Store za pomocą: <ul><li>[Program Azure PowerShell dla systemu operacyjnego Windows](data-lake-store-get-started-powershell.md)</li><li>[Dla wielu platform Azure CLI 2.0 OS innych niż Windows](data-lake-store-get-started-cli-2.0.md)</li><li>Niestandardowej aplikacji przy użyciu dowolnego zestawu SDK usługi Data Lake Store</li></ul> |Szybkie rozpoczęcie pracy. Można wykonać przekazywanie niestandardowe |Wieloetapowy proces, który obejmuje wiele technologii. Zarządzanie i monitorowanie zostanie powiększona i stanowić wyzwanie, wraz z upływem czasu, ze względu na charakter dostosowanych narzędzi |
| Kopiowanie danych z usługi Hadoop do usługi Azure Storage za pomocą narzędzia Distcp. Następnie skopiuj dane z usługi Azure Storage Data Lake Store przy użyciu odpowiedniego mechanizmu. |Możesz skopiować dane z usługi Azure Storage, Data Lake Store za pomocą: <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[Narzędzia AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Apache DistCp uruchamianie w klastrach HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Można użyć narzędzi typu open source. |Wieloetapowy proces, który obejmuje wiele technologii |

### <a name="really-large-datasets"></a>Bardzo dużych zestawów danych
Do przekazywania zestawów danych, które zakresu w kilku terabajtów, za pomocą metod opisanych wyżej czasami może być powolne i kosztowne. W takich przypadkach można użyć poniższych opcji.

* **Przy użyciu usługi Azure ExpressRoute**. Usługa Azure ExpressRoute umożliwia tworzenie prywatnych połączeń między centrami danych platformy Azure a infrastrukturą lokalną. Zapewnia niezawodne opcja przesyłania dużych ilości danych. Aby uzyskać więcej informacji, zobacz [dokumentacji usługi Azure ExpressRoute](../expressroute/expressroute-introduction.md).
* **"Tryb offline" przekazywanie danych**. Jeśli przy użyciu usługi Azure ExpressRoute nie zostanie z jakiegokolwiek powodu jest to możliwe, możesz użyć [usługa Azure Import/Export](../storage/common/storage-import-export-service.md) do wysłania z stacje dysków twardych z danymi do centrum danych platformy Azure. Dane, najpierw jest przekazywany do obiektów blob usługi Azure Storage. Następnie można użyć [usługi Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) lub [narzędzia AdlCopy](data-lake-store-copy-data-azure-storage-blob.md) do skopiowania danych z obiektów blob usługi Azure Storage do Data Lake Store.

  > [!NOTE]
  > Podczas korzystania z usługi Import/Export rozmiary plików na dyskach, które są dostarczane do centrum danych platformy Azure nie powinna być dłuższa niż 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-store"></a>Przetwarzanie danych przechowywanych w Data Lake Store
Gdy dane są dostępne w Data Lake Store możesz uruchomić analizę na tych danych przy użyciu aplikacji obsługiwanej danych big data. Obecnie można używać usługi Azure HDInsight i Azure Data Lake Analytics uruchamiać zadania analizy danych na dane przechowywane w Data Lake Store.

![Analizowanie danych w Data Lake Store](./media/data-lake-store-data-scenarios/analyze-data.png "analizy danych Data Lake Store")

Możesz przejrzeć w poniższych przykładach.

* [Utwórz klaster usługi HDInsight za pomocą programu Data Lake Store jako magazynu](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Korzystanie z usługi Azure Data Lake Analytics z usługą Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-store"></a>Pobieranie danych z programu Data Lake Store
Można także pobrać lub przenieść dane z usługi Azure Data Lake Store w scenariuszach takich jak:

* Przenoszenie danych do innych repozytoriów w interfejsie przy użyciu istniejących potoków przetwarzania danych. Na przykład można przenieść dane z Data Lake Store do usługi Azure SQL Database lub lokalnego programu SQL Server.
* Pobierz dane na komputerze lokalnym do przetworzenia w środowiskach IDE podczas kompilowania aplikacji prototypów.

![Ruch wychodzący danych z programu Data Lake Store](./media/data-lake-store-data-scenarios/egress-data.png "ruch wychodzący danych z programu Data Lake Store")

W takich przypadkach można użyć dowolnej z następujących opcji:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Można również użyć następujących metod napisać własny skrypt/aplikację do pobierania danych z programu Data Lake Store.

* [Dla wielu platform wiersza polecenia platformy Azure 2.0](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Zestaw SDK platformy .NET usługi Azure Data Lake Store](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-store"></a>Wizualizowanie danych w Data Lake Store
Za pomocą różnych usług do tworzenia wizualnych reprezentacji danych przechowywanych w Data Lake Store.

![Wizualizowanie danych w Data Lake Store](./media/data-lake-store-data-scenarios/visualize-data.png "wizualizować dane w Data Lake Store")

* Można uruchomić za pomocą [usługi Azure Data Factory, aby przenieść dane z Data Lake Store do usługi Azure SQL Data Warehouse](../data-factory/copy-activity-overview.md)
* Po tym można [Integracja usługi Power BI z usługą Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) do tworzenia wizualnych reprezentacji danych.
