---
title: Scenariusze danych dotyczących usługi Data Lake Store | Dokumentacja firmy Microsoft
description: Zapoznanie się z różnych scenariuszy i narzędzi przy użyciu danych można pozyskanych, przetwarzane pobrane i wizualizowane w Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 37409a71-a563-4bb7-bc46-2cbd426a2ece
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 86b6d05538171fcf0e259e2761316529deb4fb37
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625325"
---
# <a name="using-azure-data-lake-store-for-big-data-requirements"></a>Za pomocą usługi Azure Data Lake Store wymagania danych big data
Istnieją cztery etapy klucza podczas przetwarzania danych big:

* Wprowadzania dużych ilości danych do magazynu danych, w czasie rzeczywistym lub w partiach
* Przetwarzanie danych
* Pobieranie danych
* Wizualizacja danych

W tym artykule opisano te etapy względem Azure Data Lake Store, aby zrozumieć opcje i dostępne narzędzia do własnych potrzeb danych big data.

## <a name="ingest-data-into-data-lake-store"></a>Pozyskiwania danych w usłudze Data Lake Store
W tej sekcji opisano różne źródła danych i różne sposoby, w którym można pozyskanych danych, do konta usługi Data Lake Store.

![Pozyskiwania danych w usłudze Data Lake Store](./media/data-lake-store-data-scenarios/ingest-data.png "pozyskiwania danych w usłudze Data Lake Store")

### <a name="ad-hoc-data"></a>Ad hoc danych
Ta pozycja reprezentuje mniejszych zestawów danych, które są używane do tworzenia prototypów aplikacji danych big data. Istnieją różne sposoby wprowadzania danych ad hoc w zależności od źródła danych.

| Źródło danych | Za pomocą pozyskiwania |
| --- | --- |
| Komputer lokalny |<ul> <li>[Azure Portal](/data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure CLI wieloplatformowych 2.0](data-lake-store-get-started-cli-2.0.md)</li> <li>[Przy użyciu narzędzi Data Lake Tools dla programu Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Azure Storage Blob |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[Narzędzie AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Narzędzia DistCp uruchomiona w klastrze usługi HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Strumienia danych
Reprezentuje dane, które mogą być generowane przez różnych źródeł, takich jak aplikacje, urządzeń, czujników, itp. Te dane mogą pozyskanych do usługi Data Lake Store za pomocą różnych narzędzi. Te narzędzia zazwyczaj będzie przechwytywania i przetwarzania danych na podstawie zdarzeń przez zdarzenie w czasie rzeczywistym, a następnie wpisz zdarzeń w partiach do usługi Data Lake Store, aby mogą być dalej przetwarzane.

Poniżej przedstawiono narzędzia, których można użyć:

* [Usługa Azure Stream Analytics](../stream-analytics/stream-analytics-data-lake-output.md) -zdarzeń pozyskanych w usłudze Event Hubs można zapisać usługi Azure Data Lake za pomocą usługi Azure Data Lake Store danych wyjściowych.
* [Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md) — możesz zapisać dane bezpośrednio do usługi Data Lake Store z klastra Storm.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) — można odbieranie zdarzeń z usługi Event Hubs i zapisać go do usługi Data Lake Store za pomocą [zestawu SDK .NET Data Lake Store](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Dane relacyjne
Może również pobierać dane z relacyjnej bazy danych. W danym okresie czasu relacyjnych baz danych, Zbierz dużych ilości danych, które zapewniają wgląd klucza przetwarzanie przez potok danych big data. Aby przenieść tych danych do usługi Data Lake Store, można użyć następujących narzędzi.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dane dziennika serwera sieci Web (przy użyciu niestandardowych aplikacji przekazywania)
Tego typu dataset jest zwrócono szczególną uwagę, ponieważ analizy danych dziennika serwera sieci web jest przypadek użycia typowe dla danych big data aplikacji i wymaga dużych woluminów, plików dziennika do przekazania do usługi Data Lake Store. Żadnego z następujących narzędzi umożliwia pisanie własnych skryptów lub aplikacji na przekazywanie tych danych.

* [Azure CLI wieloplatformowych 2.0](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Zestaw .NET SDK usługi Azure Data Lake Store](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

Do przekazywania danych dziennika serwera sieci web, a także do przekazywania innych typów danych (np. społecznościowych opinie) jest sposobem zapisać własnych skryptów niestandardowych/aplikacji, ponieważ zapewnia elastyczność do dołączenia danych przekazywania większych aplikacji danych big data w ramach składnika. W niektórych przypadkach ten kod może mieć postać skryptu lub narzędzia wiersza polecenia proste. W innych przypadkach kod może być używany do przetwarzania danych big integracji aplikacji biznesowej lub rozwiązania.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dane skojarzone z klastrami Azure HDInsight
Większość typów klastra usługi HDInsight (Hadoop, HBase, Storm) obsługuje usługi Data Lake Store jako repozytorium magazynu danych. Klastry HDInsight dostęp do danych z usługi Azure blob Storage (WASB). W celu poprawy wydajności można skopiować danych z WASB do konta usługi Data Lake Store skojarzony z klastrem. Aby skopiować dane, można użyć następujących narzędzi.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [Usługa AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dane przechowywane w lokalnych lub IaaS Hadoop klastrów
Duże ilości danych może być przechowywany w istniejących klastrów platformy Hadoop, lokalnie na komputerach przy użyciu systemu plików HDFS. Klastry Hadoop może być we wdrożeniu lokalnymi lub może być w klastrze IaaS na platformie Azure. Może to być wymagania, aby skopiować tych danych do usługi Azure Data Lake Store podejścia jednorazowe lub w sposób cykliczny. Istnieją różne opcje, których można to osiągnąć. Poniżej znajduje się lista alternatyw i skojarzone kompromis.

| Podejście | Szczegóły | Zalety | Zagadnienia do rozważenia |
| --- | --- | --- | --- |
| Kopiuj dane bezpośrednio z klastrów platformy Hadoop do usługi Azure Data Lake Store za pomocą fabryki danych Azure (ADF) |[ADF obsługuje system plików HDFS jako źródła danych](../data-factory/connector-hdfs.md) |ADF obsługuje system plików HDFS i pierwszej klasy end-to-end zarządzania i monitorowania poza pole |Wymaga brama zarządzania danymi jest wdrożony na lokalnym lub w IaaS klastra |
| Eksportowanie danych z usługi Hadoop jako plików. Następnie skopiuj pliki do usługi Azure Data Lake Store przy użyciu mechanizmu odpowiednie. |Możesz skopiować pliki przy użyciu usługi Azure Data Lake Store: <ul><li>[Program Azure PowerShell dla systemu operacyjnego Windows](data-lake-store-get-started-powershell.md)</li><li>[Azure CLI wieloplatformowych 2.0 OS z systemem innym niż Windows](data-lake-store-get-started-cli-2.0.md)</li><li>Niestandardowych aplikacji przy użyciu zestawu SDK wszelkich Data Lake Store</li></ul> |Szybko rozpocząć pracę. Możliwość przekazywania dostosowane |Proces wieloetapowych, która obejmuje wiele technologii. Zarządzanie i monitorowanie wzrośnie stanowić nie lada wyzwanie wraz z upływem czasu charakter niestandardowych narzędzi |
| Aby skopiować dane z usługi Hadoop do usługi Azure Storage za pomocą narzędzia Distcp. Następnie skopiuj dane z usługi Magazyn Azure Data Lake Store przy użyciu mechanizmu odpowiednie. |Można skopiować danych z magazynu Azure do usługi Data Lake Store za pomocą: <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[Narzędzie AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Narzędzia DistCp Apache uruchamianych w klastrach usługi HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Można użyć narzędzia open source. |Proces wieloetapowych, który obejmuje wiele technologii |

### <a name="really-large-datasets"></a>Naprawdę dużych zestawów danych
Do przekazywania zestawów danych w wielu terabajtów, za pomocą metod opisanych wyżej może być czasem powolne i kosztowne. W takich przypadkach można użyć poniższych opcji.

* **Za pomocą usługi Azure ExpressRoute**. Usługa Azure ExpressRoute umożliwia tworzenie prywatnych połączeń między centrach danych platformy Azure i infrastruktury lokalnie. To zapewnia niezawodne opcję transferowania dużych ilości danych. Aby uzyskać więcej informacji, zobacz [dokumentacja usługi Azure ExpressRoute](../expressroute/expressroute-introduction.md).
* **"Offline" przekazywanie danych**. Jeśli przy użyciu usługi Azure ExpressRoute nie jest jakiegokolwiek powodu możliwe, można użyć [usługi Import/Eksport Azure](../storage/common/storage-import-export-service.md) na potrzeby wysłania dysków twardych z danymi umieszczanymi w centrum danych Azure. Dane najpierw zostało załadowane na obiektach blob magazynu Azure. Następnie można użyć [fabryki danych Azure](../data-factory/connector-azure-data-lake-store.md) lub [AdlCopy narzędzia](data-lake-store-copy-data-azure-storage-blob.md) można skopiować danych z obiektów blob magazynu Azure do usługi Data Lake Store.

  > [!NOTE]
  > Podczas używania usługi Import/Eksport rozmiary plików na dyskach, które są dostarczane do centrum danych Azure nie powinna być większa niż 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-store"></a>Przetwarzaj dane przechowywane w usłudze Data Lake Store
Gdy dane są dostępne w usłudze Data Lake Store można uruchomić analizy danych za pomocą aplikacji obsługiwanych danych big data. Do uruchomienia zadań analizy danych na dane przechowywane w usłudze Data Lake Store można obecnie używać Azure HDInsight i usługą Azure Data Lake Analytics.

![Analizowanie danych w usłudze Data Lake Store](./media/data-lake-store-data-scenarios/analyze-data.png "analizowanie danych w usłudze Data Lake Store")

Można przyjrzeć się następujące przykłady.

* [Tworzenie klastra usługi HDInsight z usługą Data Lake Store jako magazyn](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Korzystanie z usługi Azure Data Lake Analytics z usługą Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-store"></a>Pobierz dane z usługi Data Lake Store
Można również pobrać lub przenieść dane z usługi Azure Data Lake Store w scenariuszach, takich jak:

* Przenoszenie danych do innych repozytoria do interfejsu z Twoje istniejące potoków przetwarzania danych. Na przykład można przenieść dane z usługi Data Lake Store do bazy danych SQL Azure lub lokalnego programu SQL Server.
* Pobierz dane na komputerze lokalnym do przetwarzania w środowisku IDE podczas kompilowania aplikacji prototypów.

![Wyjściowych danych z usługi Data Lake Store](./media/data-lake-store-data-scenarios/egress-data.png "wyjściowych danych z usługi Data Lake Store")

W takich przypadkach można użyć dowolnego z następujących opcji:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Następujące metody umożliwia również napisać własny skrypt/aplikację w celu pobierania danych z usługi Data Lake Store.

* [Azure CLI wieloplatformowych 2.0](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Zestaw .NET SDK usługi Azure Data Lake Store](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-store"></a>Wizualizuj dane w usłudze Data Lake Store
Kombinacja usługi służy do tworzenia wizualnej reprezentacji danych przechowywanych w usłudze Data Lake Store.

![Wizualizuj dane w usłudze Data Lake Store](./media/data-lake-store-data-scenarios/visualize-data.png "wizualizacji danych w usłudze Data Lake Store")

* Można uruchomić przy użyciu [fabryki danych Azure, aby przenieść dane z usługi Data Lake Store do usługi Azure SQL Data Warehouse](../data-factory/copy-activity-overview.md)
* Następnie można [integracji usługi Power BI z usługą Magazyn danych SQL Azure](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) utworzyć wizualną reprezentację danych.
