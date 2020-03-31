---
title: Scenariusze danych obejmujące pamięć masową Data Lake Gen1 | Dokumenty firmy Microsoft
description: Poznaj różne scenariusze i narzędzia, za pomocą których dane mogą być pozyskiwania, przetwarzania, pobierania i wizualizowane w Data Lake Storage Gen1 (wcześniej znany jako Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: a8f9b66bf9a301888f2371fb1c58a4845c2232b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536160"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>Korzystanie z usługi Azure Data Lake Storage Gen1 dla wymagań dotyczących dużych zbiorów danych

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Istnieją cztery kluczowe etapy przetwarzania dużych zbiorów danych:

* Połknąć duże ilości danych do magazynu danych w czasie rzeczywistym lub w partiach
* Przetwarzanie danych
* Pobieranie danych
* Wizualizacja danych

W tym artykule przyjrzymy się tym etapom w odniesieniu do usługi Azure Data Lake Storage Gen1, aby poznać opcje i narzędzia dostępne do zaspokojenia potrzeb dużych zbiorów danych.

## <a name="ingest-data-into-data-lake-storage-gen1"></a>Połknąć dane do usługi Data Lake Storage Gen1
W tej sekcji przedstawiono różne źródła danych i różne sposoby pozyskiwania tych danych na konto Usługi Data Lake Storage Gen1.

![Połknąć dane do usługi Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/ingest-data.png "Połknąć dane do usługi Data Lake Storage Gen1")

### <a name="ad-hoc-data"></a>Dane ad hoc
Reprezentuje to mniejsze zestawy danych, które są używane do tworzenia prototypów aplikacji dużych zbiorów danych. Istnieją różne sposoby pozyskiwania danych ad hoc w zależności od źródła danych.

| Źródło danych | Połknieć go za pomocą |
| --- | --- |
| Komputer lokalny |<ul> <li>[Azure Portal](data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Interfejs wiersza polecenia platformy Azure](data-lake-store-get-started-cli-2.0.md)</li> <li>[Korzystanie z narzędzi usługi Data Lake tools dla programu Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Azure Storage Blob |<ul> <li>[Fabryka danych platformy Azure](../data-factory/connector-azure-data-lake-store.md)</li> <li>[Narzędzie AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp uruchomiony w klastrze USŁUGI HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Przesyłane strumieniowo dane
Reprezentuje to dane, które mogą być generowane przez różne źródła, takie jak aplikacje, urządzenia, czujniki itp. Te dane mogą być pochłonięte do usługi Data Lake Storage Gen1 za pomocą różnych narzędzi. Te narzędzia zwykle przechwytują i przetwarzają dane na podstawie zdarzenia po zdarzeniu w czasie rzeczywistym, a następnie zapisują zdarzenia partiami w udziale usługi Data Lake Storage Gen1, aby mogły być dalej przetwarzane.

Oto narzędzia, których można użyć:

* [Usługa Azure Stream Analytics](../stream-analytics/stream-analytics-data-lake-output.md) — zdarzenia przynajmowane w centrach zdarzeń można zapisywać w usłudze Azure Data Lake Storage Gen1 przy użyciu danych wyjściowych usługi Azure Data Lake Storage Gen1.
* [Usługa Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md) — można zapisywać dane bezpośrednio w usłudze Data Lake Storage Gen1 z klastra Storm.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) — można odbierać zdarzenia z Centrów zdarzeń, a następnie zapisywać je w udziale danych Lake Storage Gen1 przy użyciu [pliku SDK magazynu usługi Data Lake Gen1 .NET SDK.](data-lake-store-get-started-net-sdk.md)

### <a name="relational-data"></a>Dane relacyjne
Można również pozyskiwać dane z relacyjnych baz danych. W danym okresie relacyjne bazy danych zbierają ogromne ilości danych, które mogą dostarczyć kluczowych informacji, jeśli są przetwarzane za pośrednictwem potoku dużych zbiorów danych. Aby przenieść takie dane do usługi Data Lake Storage Gen1, można użyć następujących narzędzi.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Fabryka danych platformy Azure](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dane dziennika serwera sieci Web (przekazywanie przy użyciu aplikacji niestandardowych)
Ten typ zestawu danych jest specjalnie wywoływany, ponieważ analiza danych dziennika serwera sieci web jest typowym przypadkiem użycia dla aplikacji dużych zbiorów danych i wymaga przekazywania dużych ilości plików dziennika do usługi Data Lake Storage Gen1. Do przesyłania takich danych można użyć dowolnego z następujących narzędzi do pisania własnych skryptów lub aplikacji.

* [Interfejs wiersza polecenia platformy Azure](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Zestaw SDK usługi Azure Data Lake Storage Gen1](data-lake-store-get-started-net-sdk.md)
* [Fabryka danych platformy Azure](../data-factory/copy-activity-overview.md)

W przypadku przesyłania danych dziennika serwera www, a także przesyłania innych rodzajów danych (np. danych dotyczących nastrojów społecznościowych), dobrym podejściem jest pisanie własnych niestandardowych skryptów/aplikacji, ponieważ zapewnia elastyczność w dołączaniu komponentu przesyłania danych jako części większej aplikacji do przesyłania dużych zbiorów danych. W niektórych przypadkach kod ten może mieć postać skryptu lub prostego narzędzia wiersza polecenia. W innych przypadkach kod może służyć do integracji przetwarzania dużych zbiorów danych do aplikacji biznesowej lub rozwiązania.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dane skojarzone z klastrami usługi Azure HDInsight
Większość typów klastrów HDInsight (Hadoop, HBase, Storm) obsługuje pamięć masową Data Lake Gen1 jako repozytorium magazynu danych. Klastry usługi HDInsight uzyskują dostęp do danych z obiektów blob usługi Azure Storage (WASB). Aby uzyskać lepszą wydajność, można skopiować dane z wasb do konta Usługi Data Lake Storage Gen1 skojarzone z klastrem. Do skopiowania danych można użyć następujących narzędzi.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [Usługa AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
* [Fabryka danych platformy Azure](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dane przechowywane w lokalnych lub iaaS klastrach Hadoop
Duże ilości danych mogą być przechowywane w istniejących klastrach Hadoop, lokalnie na komputerach korzystających z usługi HDFS. Klastry Hadoop mogą być we wdrożeniu lokalnym lub mogą znajdować się w klastrze IaaS na platformie Azure. Może istnieć wymagania dotyczące kopiowania takich danych do usługi Azure Data Lake Storage Gen1 w celu jednorazowego podejścia lub w sposób cykliczny. Istnieją różne opcje, których można użyć, aby to osiągnąć. Poniżej znajduje się lista alternatyw i związanych z nimi kompromisów.

| Podejście | Szczegóły | Zalety | Zagadnienia do rozważenia |
| --- | --- | --- | --- |
| Kopiowanie danych bezpośrednio z klastrów usługi Hadoop do usługi Azure Data Lake Storage Gen1 za pomocą usługi Azure Data Factory (ADF) |[ADF obsługuje system PLIKÓW HDFS jako źródło danych](../data-factory/connector-hdfs.md) |ADF zapewnia gotowe wsparcie dla systemu plików HDFS oraz kompleksowe zarządzanie i monitorowanie |Wymaga wdrożenia bramy zarządzania danymi lokalnie lub w klastrze IaaS |
| Eksportuj dane z Hadoop jako pliki. Następnie skopiuj pliki do usługi Azure Data Lake Storage Gen1 przy użyciu odpowiedniego mechanizmu. |Pliki można kopiować do usługi Azure Data Lake Storage Gen1 przy użyciu: <ul><li>[Usługa Azure PowerShell dla systemu operacyjnego Windows](data-lake-store-get-started-powershell.md)</li><li>[Interfejs wiersza polecenia platformy Azure](data-lake-store-get-started-cli-2.0.md)</li><li>Niestandardowa aplikacja przy użyciu dowolnego SDK data lake storage gen1</li></ul> |Szybkie rozpoczęcie pracy. Może wykonywać niestandardowe przesyłanie |Wieloetapowy proces, który obejmuje wiele technologii. Zarządzanie i monitorowanie z czasem staną się wyzwaniem, biorąc pod uwagę dostosowany charakter narzędzi |
| Użyj Distcp, aby skopiować dane z usługi Hadoop do usługi Azure Storage. Następnie skopiuj dane z usługi Azure Storage do usługi Data Lake Storage Gen1 przy użyciu odpowiedniego mechanizmu. |Dane z usługi Azure Storage do usługi Data Lake Storage Gen1 można kopiować przy użyciu: <ul><li>[Fabryka danych platformy Azure](../data-factory/copy-activity-overview.md)</li><li>[Narzędzie AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Apache DistCp uruchomiony w klastrach HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Można użyć narzędzi open source. |Wieloetapowy proces, który obejmuje wiele technologii |

### <a name="really-large-datasets"></a>Naprawdę duże zestawy danych
W przypadku przekazywania zestawów danych, które mają zasięg kilku terabajtów, przy użyciu metod opisanych powyżej może być czasami powolne i kosztowne. W takich przypadkach możesz skorzystać z poniższych opcji.

* **Korzystanie z usługi Azure ExpressRoute**. Usługa Azure ExpressRoute umożliwia tworzenie prywatnych połączeń między centrami danych platformy Azure a infrastrukturą w twojej siedzibie. Zapewnia to niezawodną opcję przesyłania dużych ilości danych. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure ExpressRoute](../expressroute/expressroute-introduction.md).
* **"Offline" przesyłanie danych**. Jeśli korzystanie z usługi Azure ExpressRoute nie jest możliwe z jakiegokolwiek powodu, można użyć [usługi Azure Import/Export](../storage/common/storage-import-export-service.md) do wysyłki dysków twardych z danymi do centrum danych platformy Azure. Twoje dane są najpierw przekazywane do obiektów blob usługi Azure Storage. Następnie można użyć [usługi Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) lub narzędzia [AdlCopy](data-lake-store-copy-data-azure-storage-blob.md) do kopiowania danych z obiektów blob usługi Azure Storage do usługi Data Lake Storage Gen1.

  > [!NOTE]
  > Podczas korzystania z usługi Import/Export rozmiary plików na dyskach wysyłanych do centrum danych platformy Azure nie powinny być większe niż 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-storage-gen1"></a>Przetwarzanie danych przechowywanych w pamięci masowej usługi Data Lake Gen1
Gdy dane są dostępne w data lake storage gen1 można uruchomić analizę tych danych przy użyciu obsługiwanych aplikacji big data. Obecnie można użyć usługi Azure HDInsight i usługi Azure Data Lake Analytics do uruchamiania zadań analizy danych na danych przechowywanych w usłudze Data Lake Storage Gen1.

![Analizowanie danych w umiar pamięci masowej usługi Data Lake Gen1](./media/data-lake-store-data-scenarios/analyze-data.png "Analizowanie danych w umiar pamięci masowej usługi Data Lake Gen1")

Możesz spojrzeć na poniższe przykłady.

* [Tworzenie klastra USŁUGI HDInsight z pamięcią masową Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Korzystanie z usługi Azure Data Lake Analytics z usługą Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-storage-gen1"></a>Pobieranie danych z usługi Data Lake Storage Gen1
Możesz również pobrać lub przenieść dane z usługi Azure Data Lake Storage Gen1 dla scenariuszy, takich jak:

* Przenoszenie danych do innych repozytoriów w celu interfejsu z istniejącymi potokami przetwarzania danych. Na przykład można przenieść dane z usługi Data Lake Storage Gen1 do usługi Azure SQL Database lub lokalnego programu SQL Server.
* Pobieranie danych na komputer lokalny w celu przetwarzania w środowiskach IDE podczas tworzenia prototypów aplikacji.

![Dane wychodzące z usługi Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/egress-data.png "Dane wychodzące z usługi Data Lake Storage Gen1")

W takich przypadkach można użyć dowolnej z następujących opcji:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Fabryka danych platformy Azure](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Można również użyć następujących metod do pisania własnego skryptu/aplikacji, aby pobrać dane z usługi Data Lake Storage Gen1.

* [Interfejs wiersza polecenia platformy Azure](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Zestaw SDK usługi Azure Data Lake Storage Gen1](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-storage-gen1"></a>Wizualizuj dane w programie Data Lake Storage Gen1
Można użyć kombinacji usług do tworzenia wizualnych reprezentacji danych przechowywanych w umia dla usługi Data Lake Storage Gen1.

![Wizualizuj dane w programie Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/visualize-data.png "Wizualizuj dane w programie Data Lake Storage Gen1")

* Można rozpocząć od użycia [usługi Azure Data Factory do przenoszenia danych z usługi Data Lake Storage Gen1 do usługi Azure SQL Data Warehouse](../data-factory/copy-activity-overview.md)
* Następnie można [zintegrować usługę Power BI z usługą Azure SQL Data Warehouse,](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) aby utworzyć wizualną reprezentację danych.
