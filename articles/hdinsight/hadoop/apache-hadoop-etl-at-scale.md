---
title: Wyodrębniania, transformacji i ładowania (ETL) na dużą skalę - Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ETL jest używana w usłudze HDInsight z usługą Hadoop.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 2f34233a68bec506d9f69426d779aee8e493d759
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31402067"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Wyodrębniania, przekształcania i ładowania (ETL) na dużą skalę

Wyodrębniania, przekształcania i ładowania (ETL) to proces za pomocą którego danych jest uzyskane z różnych źródeł, zebrane w standardowej lokalizacji, czyszczenia i przetwarzane i ostatecznie załadowane do magazynu danych, z którego mogą być przeszukiwane. Starsze procesów ETL importowania danych, usunięcie go w miejscu i przechowuj go w aparacie relacyjnej bazie danych. Z usługą HDInsight różnych składników ekosystemu Hadoop obsługuje wykonywanie ETL na dużą skalę. 

Korzystanie z usługi HDInsight w proces ETL można podsumować w ramach tego potoku:

![Omówienie usługi HDInsight ETL](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

Poniższe sekcje Poznaj poszczególnych faz ETL oraz ich powiązane składniki.

## <a name="orchestration"></a>Aranżacja

Orchestration zapisanych na wszystkich etapach procesu ETL. Zadania ETL w usłudze HDInsight często obejmują kilka różnych produktów, pracy w połączeniu ze sobą.  Można na przykład gałęzi do czyszczenia części danych, podczas gdy Pig czyści pozostałej części.  Aby załadować dane do bazy danych SQL Azure z usługi Azure Data Lake Store można użyć usługi fabryka danych Azure.

Orchestration jest potrzebne do uruchomienia tego zadania właściwe w odpowiednim czasie.

### <a name="oozie"></a>Oozie

Apache Oozie to system koordynacji przepływu pracy, który zarządza zadaniami na platformie Hadoop. Oozie jest uruchamiany w ramach klastra usługi HDInsight i zintegrowany ze stosem platformy Hadoop. Oozie obsługuje zadania platformy Hadoop dla Apache MapReduce, Apache Pig, Apache Hive i Apache Sqoop. Oozie mogą służyć do planowania zadań, które są specyficzne dla systemu, np. programów Java lub skryptów powłoki.

Aby uzyskać więcej informacji, zobacz [Oozie korzystać z platformy Hadoop do definiowania i uruchomić przepływ pracy w usłudze HDInsight](../hdinsight-use-oozie-linux-mac.md) dla nowości przedstawiająca sposób używania Oozie do dysków na trasie potoku, zobacz [Operacjonalizacji potoku danych](../hdinsight-operationalize-data-pipeline.md). 

### <a name="azure-data-factory"></a>Azure Data Factory

Fabryka danych Azure oferuje możliwości aranżacji w formie platforma jako usługa. Jest usługa integracji danych opartych na chmurze, która służy do tworzenia przepływów pracy z danymi w chmurze na potrzeby organizowanie i automatyzowanie przenoszenia danych i przekształcania danych. 

Przy użyciu fabryki danych Azure, możesz:

1. Tworzenie i planowanie oparte na danych przepływów pracy (nazywanym potoki) pozyskiwania danych z różnych magazynów.
2. Proces i przekształcania danych przy użyciu obliczeniowego usług, takich jak Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics, partii zadań Azure i usługi Azure Machine Learning.
3. Publikować dane wyjściowe w magazynach danych, na przykład Azure SQL Data Warehouse, w celu użycia przez aplikacje analizy biznesowej.

Aby uzyskać więcej informacji dotyczących fabryki danych Azure, zobacz [dokumentacji](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Pozyskiwania przechowywania plików i magazynu wyników

Źródłowe pliki danych zazwyczaj są ładowane do lokalizacji magazynu Azure lub usługi Azure Data Lake Store. Pliki można w dowolnym formacie, ale zazwyczaj są plików prostych, takich jak woluminy CSV. 

### <a name="azure-storage"></a>Azure Storage 

[Usługa Azure Storage](https://azure.microsoft.com/services/storage/blobs/) ma [wartości docelowe skalowalności określonych](../../storage/common/storage-scalability-targets.md).  Najbardziej analityczne węzłów usługi Azure Storage skaluje najlepiej podczas pracy nad dużo mniejsze pliki.  Usługa Azure Storage gwarantuje wydajności, niezależnie od tego, jak duże pliki (tak długo, jak są w granicach sieci) lub liczby plików.  Oznacza to, można przechowywać terabajtów danych i nadal otrzymywać wydajność, czy używasz podzbiór danych lub wszystkich danych.

Magazyn Azure ma kilka różnych typów obiektów blob.  *Dołącz blob* to doskonałe rozwiązanie do przechowywania danych czujnika lub dzienniki sieci web.  

Wiele obiektów blob mogą być rozproszone na wielu serwerach do skalowania w poziomie do nich dostęp, ale pojedynczego obiektu blob mogą być przekazywane tylko przez jeden serwer. Obiekty BLOB można grupować logicznie w kontenerów obiektów blob, jednak niektóre powodują nie partycjonowania efekty z grupy.

Magazyn Azure ma również warstwę interfejsu API WebHDFS dla magazynu obiektów blob.  Wszystkie usługi w usłudze HDInsight można uzyskać dostępu do plików w magazynie obiektów Blob Azure czyszczenie danych i przetwarzania danych, podobnie jak jak użyć tych usług systemu plików rozproszonych Hadoop (HDFS).

Danych jest zwykle pozyskanych w magazynie Azure przy użyciu programu PowerShell, zestawu SDK usługi Magazyn Azure albo AZCopy.

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

Azure Data Lake — magazyn (ADLS) jest zarządzany, repozytorium informatycznych dane analityczne, która jest zgodna z systemem plików HDFS.  ADLS używa modelu projektu, który jest podobny do systemu plików HDFS i nieograniczone skalowalność w postaci liczby całkowitej pojemności i rozmiaru poszczególnych plików. ADLS jest bardzo dobre podczas pracy z dużymi plikami, ponieważ dużych plików mogą być przechowywane w wielu węzłach.  Partycjonowanie danych w ADLS odbywa się w tle.  Usługa zapewnia ogromną przepływność do obsługi zadań analitycznych z tysiącami równorzędnych funkcji wykonawczych, które efektywnie odczytują i zapisują setki terabajtów danych.

Danych jest zwykle pozyskanych w ADLS przy użyciu fabryki danych Azure, SDK ADLS, usługi AdlCopy, narzędzia DistCp Apache lub Apache Sqoop.  Które z tych usług do użycia w dużej mierze zależy od w przypadku danych.  Jeśli dane są obecnie istniejącego klastra usługi Hadoop, można użyć narzędzia DistCp Apache, usługi AdlCopy lub fabryki danych Azure.  Jeśli w magazynie obiektów Blob Azure, można użyć zestawu SDK usługi Azure Data Lake Store .NET, programu Azure PowerShell lub fabryki danych Azure.

ADLS również jest zoptymalizowana pod kątem wprowadzanie zdarzeń przy użyciu Centrum zdarzeń platformy Azure lub systemu Apache Storm.

#### <a name="considerations-for-both-storage-options"></a>Zagadnienia dotyczące obie opcje magazynu

Do przekazywania zestawów danych w zakresie terabajtów, opóźnienie sieci może być poważny problem, zwłaszcza w przypadku, gdy dane pochodzą z lokalizacji lokalnego.  W takich przypadkach można użyć poniższych opcji:

* Usługa Azure ExpressRoute: Azure ExpressRoute umożliwia tworzenie prywatnych połączeń między centrach danych platformy Azure i infrastruktury lokalnej. Połączenia te zapewniają niezawodne opcja transferowania dużych ilości danych. Aby uzyskać więcej informacji, zobacz [dokumentacja usługi Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

* "Offline" przekazywanie danych. Można użyć [usługi Import/Eksport Azure](../../storage/common/storage-import-export-service.md) na potrzeby wysłania dysków twardych z danymi umieszczanymi w centrum danych Azure. Dane najpierw zostało załadowane na obiektach blob magazynu Azure. Następnie można użyć [fabryki danych Azure](../../data-factory/v1/data-factory-azure-datalake-connector.md) lub [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) narzędzie można skopiować danych z obiektów blob magazynu Azure do usługi Data Lake Store.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL magazynu danych jest doskonałym wyborem do przechowywania oczyszczony i przygotowane wyniki analiz w przyszłości.  Usługa Azure HDInsight mogą posłużyć do wykonania tych usług dla magazynu danych SQL Azure.

Azure SQL Data Warehouse (SQL magazynu danych) jest zoptymalizowana pod kątem obciążeń analizy dużych magazynem relacyjnej bazy danych.  Azure SQL DW skaluje oparte na partycjonowane tabele.  Mogą być partycjonowane tabele w wielu węzłach.  Węzły magazynu danych SQL platformy Azure są zaznaczone w momencie tworzenia obiektu.  Mogą być skalowane po fakcie, ale jest to aktywny proces, który może wymagać podjęcia przenoszenia danych. Zobacz [SQL Data Warehouse — Zarządzanie obliczeniowe](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md) Aby uzyskać więcej informacji.

### <a name="hbase"></a>HBase

Bazy danych Apache HBase jest dostępna w usłudze Azure HDInsight magazyn kluczy i wartości.  Apache HBase jest bazą danych NoSQL typu open source opartą na platformie Hadoop i wzorowaną na bazie danych Google BigTable. Baza danych HBase zapewnia wydajność dostęp losowy i wysoki poziom spójności w przypadku dużych ilości danych częściową strukturą i bez struktury w bezschematowej bazie zorganizowanej według rodzin kolumn.

Dane są przechowywane w wierszach tabeli, a dane w obrębie wiersza są zgrupowane według rodziny kolumn. HBase jest bezschematową bazą danych, co oznacza, że ani kolumny, ani typy danych w nich przechowywanych nie muszą być zdefiniowane przed użyciem. Kod typu open source zapewnia skalowanie liniowe, umożliwiając obsługę petabajtów danych na tysiącach węzłów. Baza danych HBase może wykorzystywać nadmiarowość danych, przetwarzanie wsadowe i inne funkcje, które są dostarczane przez aplikacje rozproszone w ekosystemie Hadoop.   

HBase jest doskonałym miejscem docelowym danych czujników i dziennika dla przyszłych analizy.

Skalowalność HBase jest zależna od liczby węzłów w klastrze usługi HDInsight.

### <a name="azure-sql-database-and-azure-database"></a>Baza danych Azure SQL i bazy danych platformy Azure

System Azure oferuje trzy różne relacyjnych baz danych jako platforma jako — usługa (PAAS).

* [Baza danych SQL Azure](../../sql-database/sql-database-technical-overview.md) jest implementacją programu Microsoft SQL Server. Aby uzyskać więcej informacji o wydajności, zobacz [dostrajania wydajności w bazie danych SQL Azure](../../sql-database/sql-database-performance-guidance.md).
* [Bazy danych platformy Azure dla programu MySQL](../../mysql/overview.md) jest implementacją Oracle, MySQL.
* [Bazy danych platformy Azure dla PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) jest implementacją PostgreSQL.

Te produkty skalowanie w górę, co oznacza, że są skalowane, dodając więcej czasu Procesora i pamięci.  Można także używać dysków premium produktów w celu poprawy wydajności operacji We/Wy.

## <a name="azure-analysis-services"></a>Azure Analysis Services 

Azure Analysis Services (AAS) to aparat danych analitycznych używanych w podejmowanie decyzji i analiz biznesowych, dostarczając danych analitycznych biznesowe i aplikacje klienckie, takie jak usługi Power BI, Excel, raporty usług Reporting Services i innych danych narzędzia wizualizacji.

Moduły analizy można skalować, zmieniając warstw dla każdego modułu indywidualnych.  Aby uzyskać więcej informacji, zobacz [cennik usługi Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Wyodrębnij i obciążenia

Gdy istnieje dane na platformie Azure, można użyć wielu usług wyodrębnić i załaduj go do innych produktów.  HDInsight obsługuje Sqoop i Flume. 

### <a name="sqoop"></a>Sqoop

Apache Sqoop to narzędzie przeznaczone dla wydajnie transferu danych między źródłami danych strukturalnych częściową strukturą i bez struktury. 

Sqoop używa MapReduce, aby importować i eksportować dane, w celu zapewnienia działania równoległe i odporność na uszkodzenia.

### <a name="flume"></a>Flume

Apache Flume to usługa rozproszonej, niezawodny i dostępne wydajnie zbierania, agregację i przenoszenia dużych ilości danych dziennika. Flume ma architektura prosty i elastyczny, oparty na przesyłanie strumieniowe przepływów danych. Flume jest niezawodny i odpornych z mechanizmów dostosowywalne niezawodność i wiele mechanizmów trybu failover i odzyskiwania. Flume używa modelu proste extensible danych, który umożliwia stosowanie analityczne online.

Apache Flume nie można używać z usługą Azure HDInsight.  Instalacja Hadoop lokalnego umożliwia Flume wysyłania danych do obiektów blob magazynu Azure lub usługi Azure Data Lake Store.  Aby uzyskać więcej informacji, zobacz [przy użyciu Flume Apache z usługą HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Przekształcenia

Po danych istnieje w wybranej lokalizacji, należy go wyczyścić, łączyć lub przygotowania go do użycia określonego wzorca.  Hive, Pig i Spark SQL powinno się wszystkie dla tego rodzaju pracy.  Są one wszystkie obsługiwane w usłudze HDInsight. 

## <a name="next-steps"></a>Kolejne kroki

* [Korzystanie z języka Pig z usługą Hadoop w usłudze HDInsight](hdinsight-use-pig.md)
* [Przy użyciu Apache Hive jako narzędzie ETL](apache-hadoop-using-apache-hive-as-an-etl-tool.md) 
