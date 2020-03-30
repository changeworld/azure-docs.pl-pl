---
title: Wyodrębnianie, przekształcanie i ładowanie (ETL) w skali — Usługa Azure HDInsight
description: Dowiedz się, jak wyodrębnić, przekształcić i załadować jest używany w HDInsight z Apache Hadoop.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: f4be3343f090c4d31ccb85eba8e99f22a3b1fcae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529479"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Wyodrębnianie, przekształcanie i ładowanie (ETL) na dużą skalę

Wyodrębnianie, przekształcanie i ładowanie (ETL) to proces, w którym dane są pobierane z różnych źródeł, zbierane w standardowej lokalizacji, czyszczone i przetwarzane, a ostatecznie ładowane do magazynu danych, z którego można je zbadać. Starszy ETL przetwarza dane importu, czyścić go w miejscu, a następnie przechowywać go w relacyjnym aparatie danych. Dzięki HDInsight, szeroka gama składników ekosystemu Apache Hadoop obsługuje wykonywanie ETL na dużą skalę.

Użycie hdinsight w procesie ETL można podsumować za pomocą tego potoku:

![HDInsight ETL w przeglądzie skali](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

W poniższych sekcjach przedstawiono każdą z faz ETL i skojarzonych z nimi składników.

## <a name="orchestration"></a>Aranżacji

Aranżacja obejmuje wszystkie fazy potoku ETL. Etl miejsc pracy w HDInsight często obejmują kilka różnych produktów pracujących w połączeniu ze sobą.  Możesz użyć Hive do czyszczenia części danych, podczas gdy Pig czyści inną część.  Za pomocą usługi Azure Data Factory można załadować dane do bazy danych SQL usługi Azure z usługi Azure Data Lake Store.

Aranżacja jest potrzebna do uruchomienia odpowiedniego zadania w odpowiednim czasie.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie to system koordynacji przepływu pracy, który zarządza zadaniami na platformie Hadoop. Oozie działa w klastrze HDInsight i jest zintegrowany ze stosem Hadoop. Oozie obsługuje zadania Hadoop dla Apache Hadoop MapReduce, Apache Pig, Apache Hive i Apache Sqoop. Oozie może również służyć do planowania zadań, które są specyficzne dla systemu, takich jak programy Java lub skrypty powłoki.

Aby uzyskać więcej informacji, zobacz [Używanie apache Oozie z Apache Hadoop do definiowania i uruchamiania przepływu pracy w programie HDInsight](../hdinsight-use-oozie-linux-mac.md) Aby uzyskać szczegółowe informacje pokazujące, jak używać Oozie do kierowania potokiem end-to-end, zobacz [Operationalize potoku danych](../hdinsight-operationalize-data-pipeline.md).

### <a name="azure-data-factory"></a>Azure Data Factory

Usługa Azure Data Factory udostępnia możliwości aranżacji w postaci platformy jako usługi. Jest to usługa integracji danych oparta na chmurze, która umożliwia tworzenie opartych na danych przepływów pracy w chmurze w celu organizowania i automatyzacji przenoszenia danych i przekształcania danych.

Korzystając z usługi Azure Data Factory, można:

1. Tworzenie i planowanie przepływów pracy opartych na danych (nazywanych potokami), które pozyskiwania danych z różnych magazynów danych.
2. Przetwarzaj i przekształcaj dane przy użyciu usług obliczeniowych, takich jak Usługi Azure HDInsight Hadoop, Spark, Usługa Azure Data Lake Analytics, usługa Azure Batch i azure machine learning.
3. Publikować dane wyjściowe w magazynach danych, na przykład Azure SQL Data Warehouse, w celu użycia przez aplikacje analizy biznesowej.

Aby uzyskać więcej informacji na temat usługi Azure Data Factory, zobacz [dokumentację](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Połknąć przechowywanie plików i wyniki

Źródłowe pliki danych są zazwyczaj ładowane do lokalizacji w usłudze Azure Storage lub usłudze Azure Data Lake Storage. Pliki mogą być w dowolnym formacie, ale zazwyczaj są to pliki płaskie, takie jak csv.

### <a name="azure-storage"></a>Azure Storage

[Usługa Azure Storage](https://azure.microsoft.com/services/storage/blobs/) ma określone cele skalowalności. Aby uzyskać więcej informacji, zobacz [Skalowalność i cele wydajności dla magazynu obiektów Blob](../../storage/blobs/scalability-targets.md). W przypadku większości węzłów analitycznych usługa Azure Storage jest najlepiej skalowana w przypadku wielu mniejszych plików.  Usługa Azure Storage gwarantuje taką samą wydajność, niezależnie od liczby plików i jak duże pliki (tak długo, jak jesteś w granicach).  Oznacza to, że można przechowywać terabajty danych i nadal uzyskać stałą wydajność, niezależnie od tego, czy używasz podzbioru danych, czy wszystkich danych.

Usługa Azure Storage ma kilka różnych typów obiektów blob.  Dołączanie *obiektu blob* jest doskonałym rozwiązaniem do przechowywania dzienników internetowych lub danych czujnika.  

Wiele obiektów blob może być dystrybuowanych na wielu serwerach, aby skalować w poziomie dostęp do nich, ale pojedynczy obiekt blob może być obsługiwany tylko przez jeden serwer. Podczas gdy obiekty BLOB mogą być logicznie zgrupowane w kontenerach obiektów blob, nie ma żadnych implikacji partycjonowania z tego grupowania.

Usługa Azure Storage ma również warstwę interfejsu API usługi WebHDFS dla magazynu obiektów blob.  Wszystkie usługi w hdinsight można uzyskać dostęp do plików w usłudze Azure Blob Storage do czyszczenia danych i przetwarzania danych, podobnie jak te usługi będą korzystać z Systemu Rozproszonych Plików (HDFS) usługi Hadoop.

Dane są zazwyczaj przybędzone do usługi Azure Storage przy użyciu programu PowerShell, zestawu SDK usługi Azure Storage lub AZCopy.

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Usługa Azure Data Lake Storage (ADLS) to zarządzane repozytorium danych hiperskalowych dla danych analitycznych, które jest zgodne z systemem PLIKÓW HDFS.  ADLS używa paradygmatu projektowego, który jest podobny do HDFS i oferuje nieograniczoną skalowalność pod względem całkowitej pojemności i rozmiaru poszczególnych plików. ADLS jest bardzo dobry podczas pracy z dużymi plikami, ponieważ duży plik może być przechowywany w wielu węzłach.  Partycjonowanie danych w ADLS odbywa się za kulisami.  Otrzymujesz ogromną przepływność do uruchamiania zadań analitycznych z tysiącami równoczesnych wykonawców, które skutecznie odczytują i zapisują setki terabajtów danych.

Dane są zazwyczaj pozyskiwania do usługi ADLS przy użyciu usługi Azure Data Factory, ADLS SDKs, AdlCopy Service, Apache DistCp lub Apache Sqoop.  Który z tych usług do wykorzystania w dużej mierze zależy od tego, gdzie są dane.  Jeśli dane są obecnie w istniejącym klastrze Hadoop, można użyć Apache DistCp, AdlCopy Service lub Azure Data Factory.  Jeśli jest w usłudze Azure Blob Storage, można użyć usługi Azure Data Lake Storage .NET SDK, Azure PowerShell lub Azure Data Factory.

Usługa ADLS jest również zoptymalizowana pod kątem pozyskiwania zdarzeń przy użyciu usługi Azure Event Hub lub Apache Storm.

#### <a name="considerations-for-both-storage-options"></a>Zagadnienia dotyczące obu opcji przechowywania

W przypadku przekazywania zestawów danych w zakresie terabajtów opóźnienie sieci może stanowić poważny problem, szczególnie jeśli dane pochodzą z lokalizacji lokalnej.  W takich przypadkach możesz skorzystać z poniższych opcji:

* Usługa Azure ExpressRoute: Usługa Azure ExpressRoute umożliwia tworzenie prywatnych połączeń między centrami danych platformy Azure a infrastrukturą lokalną. Połączenia te zapewniają niezawodną opcję przesyłania dużych ilości danych. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

* przesyłanie danych w trybie offline. [Usługi importu/eksportu platformy Azure](../../storage/common/storage-import-export-service.md) można użyć do wysłania dysków twardych z danymi do centrum danych platformy Azure. Twoje dane są najpierw przekazywane do obiektów blob usługi Azure Storage. Następnie można użyć [usługi Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md) lub narzędzia [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) do kopiowania danych z obiektów blob usługi Azure Storage do usługi Data Lake Storage.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Usługa Azure SQL DW to doskonały wybór do przechowywania oczyszczonych i przygotowanych wyników do przyszłych analiz.  Usługa Azure HDInsight może służyć do wykonywania tych usług dla usługi Azure SQL DW.

Usługa Azure SQL Data Warehouse (SQL DW) to relacyjny magazyn baz danych zoptymalizowany pod kątem obciążeń analitycznych.  Program Azure SQL DW skaluje się na podstawie tabel podzielonych na partycje.  Tabele mogą być podzielone na partycje między wieloma węzłami.  Węzły SQL DW platformy Azure są wybierane w momencie tworzenia.  Mogą skalować po fakcie, ale jest to aktywny proces, który może wymagać przenoszenia danych. Aby uzyskać więcej informacji, zobacz [MAGAZYN DANYCH SQL — Zarządzanie obliczeniami](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).

### <a name="apache-hbase"></a>Apache HBase

Apache HBase to magazyn klucz-wartość dostępny w usłudze Azure HDInsight.  Apache HBase jest bazą danych NoSQL typu open source opartą na platformie Hadoop i wzorowaną na bazie danych Google BigTable. HBase zapewnia wydajny dostęp losowy i silną spójność dla dużych ilości danych nieustrukturyzowanych i częściowo ustrukturyzowanych w bazie danych bez schematu uporządkowanej według rodzin kolumn.

Dane są przechowywane w wierszach tabeli, a dane w obrębie wiersza są zgrupowane według rodziny kolumn. HBase jest bezschematową bazą danych, co oznacza, że ani kolumny, ani typy danych w nich przechowywanych nie muszą być zdefiniowane przed użyciem. Kod typu open source zapewnia skalowanie liniowe, umożliwiając obsługę petabajtów danych na tysiącach węzłów. Usługa HBase może polegać na nadmiarowości danych, przetwarzaniu wsadowym i innych funkcjach dostarczanych przez aplikacje rozproszone w ekosystemie Hadoop.

HBase jest doskonałym miejscem dla danych czujników i dzienników do przyszłej analizy.

Skalowalność bazy danych HBase zależy od liczby węzłów w klastrze HDInsight.

### <a name="azure-sql-database-and-azure-database"></a>Baza danych SQL platformy Azure i baza danych platformy Azure

Platforma Azure oferuje trzy różne relacyjne bazy danych jako platform-as-a-service (PAAS).

* [Usługa Azure SQL Database](../../sql-database/sql-database-technical-overview.md) jest implementacją programu Microsoft SQL Server. Aby uzyskać więcej informacji na temat wydajności, zobacz [Dostrajanie wydajności w usłudze Azure SQL Database](../../sql-database/sql-database-performance-guidance.md).
* [Usługa Azure Database for MySQL](../../mysql/overview.md) to implementacja programu Oracle MySQL.
* [Usługa Azure Database for PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) jest implementacją postgreSql.

Te produkty są skalowane w górę, co oznacza, że są skalowane przez dodanie większej ilości procesora i pamięci.  Można również użyć dysków premium z produktami, aby uzyskać lepszą wydajność we/wy.

## <a name="azure-analysis-services"></a>Azure Analysis Services

Usługa Azure Analysis Services (AAS) to analityczny aparat danych używany w obsłudze decyzji i analizie biznesowej, dostarczający dane analityczne dla raportów biznesowych i aplikacji klienckich, takich jak Power BI, Excel, Reporting Services reports i inne dane narzędzi do wizualizacji.

Moduły analizy można skalować, zmieniając warstwy dla każdego modułu.  Aby uzyskać więcej informacji, zobacz [Ceny usług Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Wyodrębnij i załaduj

Gdy dane istnieją na platformie Azure, można użyć wielu usług, aby wyodrębnić i załadować go do innych produktów.  HDInsight obsługuje Sqoop i Flume.

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop to narzędzie przeznaczone do efektywnego przesyłania danych między ustrukturyzowanymi, częściowo ustrukturyzowanymi i nieustrukturyzowanymi źródłami danych.

Sqoop używa MapReduce do importowania i eksportowania danych, aby zapewnić równoległą obsługę i odporność na uszkodzenia.

### <a name="apache-flume"></a>Apache Flume

Apache Flume to rozproszona, niezawodna i dostępna usługa do efektywnego zbierania, agregowania i przenoszenia dużych ilości danych dziennika. Flume ma prostą i elastyczną architekturę opartą na strumieniowaniu danych. Flume jest wytrzymały i odporny na uszkodzenia dzięki przestrajalnym mechanizmom niezawodności oraz wielu mechanizmom pracy awaryjnej i odzyskiwania. Flume używa prostego rozszerzalnego modelu danych, który umożliwia zastosowanie analityczne online.

Apache Flume nie może być używany z usługą Azure HDInsight.  Lokalna instalacja usługi Hadoop może używać firmy Flume do wysyłania danych do obiektów blob usługi Azure Storage lub usługi Azure Data Lake Storage.  Aby uzyskać więcej informacji, zobacz [Korzystanie z programu Apache Flume z hdinsight](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Przekształcanie

Gdy dane istnieją w wybranej lokalizacji, należy je wyczyścić, połączyć lub przygotować do określonego wzorca użycia.  Hive, Pig i Spark SQL są dobrym wyborem dla tego rodzaju pracy.  Wszystkie są obsługiwane w programie HDInsight.

## <a name="next-steps"></a>Następne kroki

* [Używanie ula Apache jako narzędzia ETL](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [Korzystanie z usługi Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Przenoszenie danych z bazy danych SQL platformy Azure do tabeli gałęzi Apache](./apache-hadoop-use-sqoop-mac-linux.md)
