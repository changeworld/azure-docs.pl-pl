---
title: Wyodrębnianie, przekształcanie i ładowanie (ETL) na dużą skalę — Azure HDInsight
description: Dowiedz się, jak ETL jest używany w HDInsight przy użyciu technologii Apache Hadoop.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: a343caaa998505a1772096b058ec7ad300eec03c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64725698"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Wyodrębnianie, przekształcanie i ładowanie (ETL) na dużą skalę

Wyodrębniania, przekształcania i ładowania (ETL) to proces, za pomocą którego danych uzyskanych z różnych źródeł, zebranych w lokalizacji, czyszczenia i przetwarzane i ostatecznie załadowane do magazynu danych, z którego mogą być przeszukiwane. Starszych procesów ETL importowania danych, usunięcie go w miejscu, a następnie zapisać ją w aparacie relacyjnej bazie danych. HDInsight szerokiej gamy składników ekosystemu Apache Hadoop obsługuje wykonywanie ETL na dużą skalę. 

Użycie HDInsight w proces ETL można podsumować w tym potoku:

![Omówienie HDInsight ETL](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

Poniższe sekcje zapoznaj się z każdej fazy ETL i ich skojarzone składniki.

## <a name="orchestration"></a>Aranżacja

Organizowanie zakresów we wszystkich fazach potoku ETL. Zadania ETL w HDInsight często obejmują kilka różnych produktów, współpracując ze sobą.  Korzystanie z programu Hive, może wyczyścić część danych, podczas gdy Pig czyści pozostałej części.  Można użyć usługi Azure Data Factory do ładowania danych do usługi Azure SQL Database z usługi Azure Data Lake Store.

Orchestration jest wymagany do uruchomienia zadania odpowiednie w odpowiednim czasie.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie to system koordynacji przepływu pracy, który zarządza zadaniami na platformie Hadoop. Oozie jest uruchamiany w ramach klastra usługi HDInsight i jest zintegrowany ze stosem platformy Hadoop. Oozie obsługuje zadania platformy Hadoop, Apache Hadoop MapReduce, Apache Pig, Apache Hive i Apache Sqoop. Oozie może również służyć do planowania zadań, które są specyficzne dla systemu, np. programów Java lub skryptów powłoki.

Aby uzyskać więcej informacji, zobacz [Użyj Apache Oozie przy użyciu technologii Apache Hadoop, aby zdefiniować i uruchomić przepływ pracy na HDInsight](../hdinsight-use-oozie-linux-mac.md) Aby uzyskać szczegółowe informacje, w którym pokazano, jak za pomocą programu Oozie, można dostarczać potoku end-to-end, zobacz [Operacjonalizacja potoku danych](../hdinsight-operationalize-data-pipeline.md). 

### <a name="azure-data-factory"></a>Azure Data Factory

Usługa Azure Data Factory zapewnia możliwości aranżacji w postaci platformy jako usługi. Jest to usługa integracji danych w chmurze, która umożliwia tworzenie przepływów pracy opartych na danych w chmurze służące do organizowania i automatyzowania przenoszenia i przekształcania danych. 

Za pomocą usługi Azure Data Factory, możesz wykonywać następujące czynności:

1. Tworzenie i planowanie oparte na danych przepływy pracy (nazywane potokami), które pozyskiwać dane z różnych magazynów danych.
2. Przetwarzaj i przekształcaj dane za pomocą obliczeń usług, takich jak Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics, Azure Batch i Azure Machine Learning.
3. Publikować dane wyjściowe w magazynach danych, na przykład Azure SQL Data Warehouse, w celu użycia przez aplikacje analizy biznesowej.

Aby uzyskać więcej informacji na temat usługi Azure Data Factory, zobacz [dokumentacji](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Pozyskiwania, przechowywania plików i Magazyn wyników

Źródłowe pliki danych zazwyczaj są ładowane do lokalizacji w usłudze Azure Storage lub usługi Azure Data Lake Storage. Pliki można w dowolnym formacie, ale zazwyczaj są one plików prostych, takich jak woluminy CSV. 

### <a name="azure-storage"></a>Azure Storage 

[Usługa Azure Storage](https://azure.microsoft.com/services/storage/blobs/) ma [cele skalowalności określonych](../../storage/common/storage-scalability-targets.md).  Najbardziej analityczne węzłów usługi Azure Storage umożliwia skalowanie najlepiej zajmujące się dużo mniejsze pliki.  Usługa Azure Storage gwarantuje to ta sama wydajność niezależnie od tego, jak duże pliki (tak długo, jak są w ramach limitów) lub liczby plików.  Oznacza to, można przechowywać terabajty danych i nadal otrzymywać spójną wydajność, czy używasz podzbiór danych lub wszystkich danych.

Usługa Azure Storage ma kilka różnych typów obiektów blob.  *Uzupełnialnego obiektu blob* jest świetnym rozwiązaniem do przechowywania dzienników sieci web lub danych czujnika.  

Wiele obiektów blob mogą być rozproszone między wiele serwerów w celu skalowania w poziomie do nich dostęp, ale jeden obiekt blob może być obsługiwany tylko przez jeden serwer. Gdy obiektów blob może być logicznie pogrupowane w kontenerach obiektów blob, nie ma żadnych skutków partycjonowania w tej metodzie grupowania.

Usługa Azure Storage również ma warstwę interfejsu API WebHDFS dla magazynu obiektów blob.  Wszystkie usługi w HDInsight można uzyskać dostęp do plików w usłudze Azure Blob Storage, czyszczenie danych i przetwarzania danych, podobnie jak jak użyć tych usług plików System (HDFS, Hadoop Distributed).

Dane zwykle są pozyskiwane do usługi Azure Storage za pomocą programu PowerShell, zestawu SDK usługi Azure Storage lub narzędzia AZCopy.

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Usługa Azure Data Lake Storage (ADLS) jest zarządzany ogromne repozytorium do przechowywania danych analizy, która jest zgodna z systemem plików HDFS.  Azure Data Lake Store korzysta z paradygmatu projekt, który jest podobny do systemu plików HDFS i nieograniczona skalowalność pod względem łącznej pojemności i rozmiaru poszczególnych plików. Azure Data Lake Store jest bardzo dobra, podczas pracy z dużymi plikami, ponieważ dużych plików mogą być przechowywane w wielu węzłach.  Partycjonowanie danych w usłudze ADLS jest wykonywane w tle.  Usługa zapewnia ogromną przepływność do obsługi zadań analitycznych z tysiącami równorzędnych funkcji wykonawczych, które efektywnie odczytują i zapisują setki terabajtów danych.

Dane są zwykle pozyskiwane w usłudze ADLS za pomocą usługi Azure Data Factory, zestawy SDK usługi ADLS, AdlCopy usługi, narzędzia DistCp Apache lub Apache Sqoop.  Które z tych usług do użycia w dużej mierze zależy od danych.  Jeśli dane są obecnie w istniejącym klastrze usługi Hadoop, można użyć narzędzia DistCp Apache, usługi AdlCopy lub usługi Azure Data Factory.  Jeśli znajduje się w usłudze Azure Blob Storage, można użyć zestawu .NET SDK usługi Azure Data Lake Storage, programu Azure PowerShell lub usługi Azure Data Factory.

Azure Data Lake Store jest również zoptymalizowane pod kątem przyjmowanie zdarzeń za pomocą usługi Azure Event Hub lub Apache Storm.

#### <a name="considerations-for-both-storage-options"></a>Zagadnienia dotyczące obie opcje magazynowania

Do przekazywania zestawów danych w zakresie terabajtów, opóźnienie sieci może być poważny problem, szczególnie w przypadku, gdy dane pochodzą z lokalizacji lokalnej.  W takich przypadkach można użyć poniższych opcji:

* Azure ExpressRoute:  Usługa Azure ExpressRoute umożliwia tworzenie prywatnych połączeń między centrami danych platformy Azure a infrastrukturą lokalną. Połączenia te zapewniają niezawodne opcja przesyłania dużych ilości danych. Aby uzyskać więcej informacji, zobacz [dokumentacji usługi Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

* "Tryb offline" przekazywanie danych. Możesz użyć [usługa Azure Import/Export](../../storage/common/storage-import-export-service.md) do wysłania z stacje dysków twardych z danymi do centrum danych platformy Azure. Dane, najpierw jest przekazywany do obiektów blob usługi Azure Storage. Następnie można użyć [usługi Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md) lub [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) narzędzie do kopiowania danych z obiektów blob usługi Azure Storage do usługi Data Lake Storage.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Usługa Azure SQL DW jest doskonałym wyborem do przechowywania oraz zapewnienie dobrego administrowania przygotowane wyników na potrzeby przyszłej analizy.  Usługa Azure HDInsight może służyć do wykonywania tych usług dla usługi Azure SQL data Warehouse.

Usługa Azure SQL Data Warehouse (SQL data Warehouse) jest w magazynie relacyjnej bazy danych zoptymalizowane na potrzeby obciążeń analitycznych.  Azure SQL data Warehouse umożliwia skalowanie na podstawie na partycjonowane tabele.  Tabele można podzielić na partycje w wielu węzłach.  Węzły na platformie Azure SQL data Warehouse są zaznaczone w czasie tworzenia.  Mogły być skalowane w późniejszym czasie, ale jest aktywny proces, który może wymagać podjęcia przenoszenia danych. Zobacz [SQL Data Warehouse — Zarządzanie obliczenia](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md) Aby uzyskać więcej informacji.

### <a name="apache-hbase"></a>Apache HBase

Apache HBase to parach klucz wartość, dostępne w usłudze Azure HDInsight.  Apache HBase jest bazą danych NoSQL typu open source opartą na platformie Hadoop i wzorowaną na bazie danych Google BigTable. Baza danych HBase zapewnia wydajny dostęp losowy i wysoki poziom spójności w przypadku dużych ilości danych z częściową strukturą i bez struktury w bezschematowej bazie zorganizowanej według rodzin kolumn.

Dane są przechowywane w wierszach tabeli, a dane w obrębie wiersza są zgrupowane według rodziny kolumn. HBase jest bezschematową bazą danych, co oznacza, że ani kolumny, ani typy danych w nich przechowywanych nie muszą być zdefiniowane przed użyciem. Kod typu open source zapewnia skalowanie liniowe, umożliwiając obsługę petabajtów danych na tysiącach węzłów. Baza danych HBase może polegać na nadmiarowość danych, przetwarzanie wsadowe i inne funkcje, które są dostarczane przez aplikacje rozproszone w ekosystemie usługi Hadoop.   

Bazy danych HBase jest doskonałym miejscem docelowym dla danych czujników i dziennika do przyszłej analizy.

Skalowalność bazy danych HBase jest zależna od liczby węzłów w klastrze HDInsight.

### <a name="azure-sql-database-and-azure-database"></a>Azure SQL Database i Azure Database

System Azure udostępnia trzy różne relacyjnych baz danych jako platform-as-a-service (PAAS).

* [Usługa Azure SQL Database](../../sql-database/sql-database-technical-overview.md) to implementacja programu Microsoft SQL Server. Aby uzyskać więcej informacji o wydajności, zobacz [dostrajanie wydajności w usłudze Azure SQL Database](../../sql-database/sql-database-performance-guidance.md).
* [Usługa Azure Database for MySQL](../../mysql/overview.md) jest implementacją Oracle MySQL.
* [Usługa Azure Database for postgresql w warstwie](../../postgresql/quickstart-create-server-database-portal.md) jest implementacją PostgreSQL.

Te produkty skalowanie w górę, co oznacza, że są one skalowane, dodając więcej czasu Procesora i pamięci.  Możesz również użyć dysków w warstwie premium z produktami uzyskać lepszą wydajność operacji We/Wy.

## <a name="azure-analysis-services"></a>Azure Analysis Services 

Azure Analysis Services (AAS) to aparat przetwarzania danych używane w podejmowanie decyzji i analiza biznesowa, podając dane analityczne raportów biznesowych i aplikacje klienckie, takie jak Power BI, Excel, raporty usług Reporting Services oraz inne dane narzędzia do wizualizacji.

Moduły analizy można skalować przez zmianę warstwy dla każdego modułu indywidualnych.  Aby uzyskać więcej informacji, zobacz [cennik usługi Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Wyodrębnianie i ładowanie

Gdy istnieje danych na platformie Azure, można użyć wielu usług wyodrębnić i załadować je do innych produktów.  HDInsight obsługuje, Sqoop i Flume. 

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop to narzędzie przeznaczone do wydajnego przesyłania danych między źródłami danych ze strukturą, częściową strukturą i bez struktury. 

Sqoop używa MapReduce, aby importować i eksportować dane, w celu dostarczania operacji równoległej i odporności na uszkodzenia.

### <a name="apache-flume"></a>Apache Flume

Apache Flume to usługa rozproszonej, niezawodnej i dostępnej efektywnie zbieranie, agregowania i przenoszenie dużych ilości danych dziennika. Flume ma architektury prosty i elastyczny, oparty na tym strumieniu przepływów danych. Flume będzie niezawodne i odporne na uszkodzenia za pomocą mechanizmów niezawodności możliwą do dostosowania i wiele mechanizmów trybu failover i odzyskiwania. Flume korzysta z modelu prostego extensible danych, umożliwiający online aplikacji analitycznych.

Nie można użyć Apache Flume przy użyciu usługi Azure HDInsight.  Instalacja usługi Hadoop w środowisku lokalnym służy Flume do przesyłania danych do usługi Azure Storage Blobs lub usługi Azure Data Lake Storage.  Aby uzyskać więcej informacji, zobacz [przy użyciu Apache Flume z HDInsight](https://web.archive.org/web/20190217104751/ https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Transformacja

Po danych istnieje w wybranej lokalizacji, należy go wyczyścić, połączyć go lub przygotowania go do wzorca określonego użycia.  Hive, Pig i Spark SQL są wszystkie dobrych wyborów dla tego rodzaju pracy.  Są one wszystkie obsługiwane w HDInsight. 

## <a name="next-steps"></a>Kolejne kroki

* [Use Apache Pig z platformą Apache Hadoop w HDInsight](hdinsight-use-pig.md)
* [Za pomocą programu Apache Hive jako narzędzia ETL](apache-hadoop-using-apache-hive-as-an-etl-tool.md) 
* [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
