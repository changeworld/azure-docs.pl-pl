---
title: Wyodrębnianie, przekształcanie i ładowanie (ETL) w skali — Azure HDInsight
description: Dowiedz się, jak wyodrębnianie, przekształcanie i ładowanie jest używane w usłudze HDInsight z Apache Hadoop.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: ashishth
ms.openlocfilehash: ceafee2d3356d37e74039789c8243ace41c141b2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435791"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Wyodrębnianie, przekształcanie i ładowanie (ETL) na dużą skalę

Wyodrębnianie, przekształcanie i ładowanie (ETL) to proces polegający na tym, że dane są uzyskiwane z różnych źródeł, zbierane w standardowej lokalizacji, oczyszczone i przetworzone, a ostatecznie załadowane do magazynu danych, z którego mogą być wysyłane zapytania. Starsze procesy ETL importują dane, czyści je, a następnie przechowują w aparacie danych relacyjnych. Za pomocą usługi HDInsight szeroka gama składników ekosystemu Apache Hadoop obsługują użycie ETL na dużą skalę.

Użycie usługi HDInsight w procesie ETL może być podsumowane przez ten potok:

![Omówienie ETL usługi HDInsight w skali](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

W poniższych sekcjach opisano wszystkie fazy ETL i powiązane z nimi składniki.

## <a name="orchestration"></a>Aranżacja

Aranżacja obejmuje wszystkie fazy potoku ETL. Zadania ETL w usłudze HDInsight często obejmują kilka różnych produktów współpracujących ze sobą.  Możesz użyć programu Hive do oczyszczenia części danych, natomiast świnia czyści inną część.  Azure Data Factory można użyć do załadowania danych do Azure SQL Database z Azure Data Lake Store.

Aranżacja jest wymagana do uruchomienia odpowiedniego zadania w odpowiednim czasie.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie to system koordynacji przepływu pracy, który zarządza zadaniami usługi Hadoop. Oozie działa w ramach klastra usługi HDInsight i jest zintegrowany z stosem Hadoop. Usługa Oozie obsługuje zadania usługi Hadoop dla Apache Hadoop MapReduce, Apache chlewnej, Apache Hive i Apache Sqoop. Oozie może również służyć do planowania zadań specyficznych dla systemu, takich jak programy Java lub skrypty powłoki.

Aby uzyskać więcej informacji, zobacz [Używanie platformy Apache Oozie z usługą Apache Hadoop do definiowania i uruchamiania przepływu pracy w usłudze HDInsight](../hdinsight-use-oozie-linux-mac.md) dla głębokiego szczegółoweu pokazującego, jak używać Oozie do kierowania kompleksowego potoku, zobacz [operacjonalizować the Data potoku](../hdinsight-operationalize-data-pipeline.md).

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory zapewnia możliwości aranżacji w formie platformy jako usługi. Jest to oparta na chmurze usługa integracji danych, która umożliwia tworzenie przepływów pracy opartych na danych w chmurze na potrzeby organizowania i automatyzowania przenoszenia i przekształcania danych.

Za pomocą Azure Data Factory można:

1. Twórz i planuj przepływy pracy oparte na danych (nazywane potokami), które pozyskiwanie danych z różnych magazynów danych.
2. Przetwarzaj i Przekształcaj dane przy użyciu usług obliczeniowych, takich jak Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics, Azure Batch i Azure Machine Learning.
3. Publikować dane wyjściowe w magazynach danych, na przykład Azure SQL Data Warehouse, w celu użycia przez aplikacje analizy biznesowej.

Aby uzyskać więcej informacji na temat Azure Data Factory, zobacz [dokumentację](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Pozyskiwanie magazynu plików i magazynu wyników

Pliki danych źródłowych są zwykle ładowane do lokalizacji w usłudze Azure Storage lub Azure Data Lake Storage. Pliki mogą być w dowolnym formacie, ale zazwyczaj są plikami prostymi, takimi jak CSV.

### <a name="azure-storage"></a>Azure Storage

[Usługa Azure Storage](https://azure.microsoft.com/services/storage/blobs/) ma konkretne elementy docelowe skalowalności. Aby uzyskać więcej informacji, zobacz [elementy docelowe skalowalności i wydajności dla usługi BLOB Storage](../../storage/blobs/scalability-targets.md). W przypadku większości węzłów analitycznych usługa Azure Storage jest Najlepsza w przypadku pracy w wielu mniejszych plikach.  Usługa Azure Storage gwarantuje taką samą wydajność, niezależnie od liczby plików lub wielkości plików (o ile znajdują się w granicach limitów).  Oznacza to, że można przechowywać terabajty danych i nadal uzyskać spójną wydajność, niezależnie od tego, czy jest używany podzbiór danych, czy też wszystkie dane.

Usługa Azure Storage ma kilka różnych typów obiektów BLOB.  *Dołącz obiekt BLOB* jest doskonałym rozwiązaniem do przechowywania dzienników sieci Web lub danych czujników.  

Wiele obiektów BLOB może być dystrybuowanych na wielu serwerach w celu skalowania w poziomie dostępu do nich, ale jeden obiekt BLOB może być obsługiwany tylko przez jeden serwer. Obiekty blob można logicznie grupować w kontenerach obiektów blob, ale nie ma to wpływu na partycjonowanie z tego grupowania.

Usługa Azure Storage ma również warstwę interfejsu API WebHDFS dla magazynu obiektów BLOB.  Wszystkie usługi w usłudze HDInsight mogą uzyskiwać dostęp do plików w usłudze Azure Blob Storage na potrzeby czyszczenia i przetwarzania danych, podobnie jak w przypadku korzystania z systemu plików rozproszonych Hadoop (HDFS).

Dane są zwykle pozyskiwane w usłudze Azure Storage przy użyciu programu PowerShell, zestawu SDK usługi Azure Storage lub AZCopy.

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Azure Data Lake Storage (ADLS) to zarządzane repozytorium do skalowania dla danych analitycznych, które są zgodne z systemem plików HDFS.  ADLS używa modelu projektowania podobnego do systemu plików HDFS i oferuje nieograniczoną skalowalność pod względem całkowitej pojemności i rozmiaru poszczególnych plików. ADLS jest bardzo dobre podczas pracy z dużymi plikami, ponieważ duży plik można przechowywać w wielu węzłach.  Partycjonowanie danych w ADLS odbywa się w tle.  Usługa zapewnia ogromną przepływność do obsługi zadań analitycznych z tysiącami równorzędnych funkcji wykonawczych, które efektywnie odczytują i zapisują setki terabajtów danych.

Dane są zwykle wprowadzane do ADLS przy użyciu Azure Data Factory, ADLS SDK, AdlCopy Service, Apache pomocą distcp lub Apache Sqoop.  Które z tych usług są duże, zależy od tego, gdzie są dane.  Jeśli dane są obecnie w istniejącym klastrze usługi Hadoop, można użyć usługi Apache pomocą distcp, AdlCopy lub Azure Data Factory.  Jeśli znajduje się na platformie Azure Blob Storage, możesz użyć Azure Data Lake Storage .NET SDK, Azure PowerShell lub Azure Data Factory.

ADLS jest również zoptymalizowany pod kątem pozyskiwania zdarzeń przy użyciu usługi Azure Event Hub lub Apache Storm.

#### <a name="considerations-for-both-storage-options"></a>Zagadnienia dotyczące obu opcji magazynu

W przypadku przekazywania zestawów danych w zakresie terabajtów opóźnienie sieci może być poważnym problemem, szczególnie w przypadku, gdy dane pochodzą z lokalizacji lokalnej.  W takich przypadkach można użyć poniższych opcji:

* Azure ExpressRoute: usługa Azure ExpressRoute umożliwia tworzenie prywatnych połączeń między centrami danych platformy Azure a infrastrukturą lokalną. Te połączenia zapewniają niezawodną opcję przesyłania dużych ilości danych. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

* Przekazywanie danych w trybie offline. [Usługa Azure Import/Export](../../storage/common/storage-import-export-service.md) umożliwia wysyłanie dysków twardych z danymi do centrum danych platformy Azure. Dane są najpierw przekazywane do obiektów BLOB usługi Azure Storage. Następnie można użyć [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md) lub narzędzia [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) do kopiowania danych z obiektów BLOB usługi Azure Storage do Data Lake Storage.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Usługa Azure SQL DW to doskonały wybór do przechowywania oczyszczonych i przygotowanych wyników dla przyszłej analizy.  Usługa Azure HDInsight może służyć do wykonywania tych usług dla usługi Azure SQL DW.

Azure SQL Data Warehouse (SQL DW) to relacyjny magazyn baz danych zoptymalizowany pod kątem obciążeń analitycznych.  Usługa Azure SQL DW jest skalowana na podstawie partycjonowanych tabel.  Tabele mogą być partycjonowane w wielu węzłach.  W momencie tworzenia są wybierane węzły magazynu danych SQL Azure.  Mogą one być skalowane po fakcie, ale jest to aktywny proces, który może wymagać przeniesienia danych. Aby uzyskać więcej informacji, zobacz [SQL Data Warehouse-Manage COMPUTE](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md) .

### <a name="apache-hbase"></a>Apache HBase

Apache HBase jest magazynem klucz-wartość dostępnym w usłudze Azure HDInsight.  Apache HBase jest bazą danych NoSQL typu open source opartą na platformie Hadoop i wzorowaną na bazie danych Google BigTable. HBase zapewnia dostęp losowy i silną spójność w przypadku dużych ilości danych bez struktury i częściową strukturą w bazie danych bez schematu zorganizowanym według rodzin kolumn.

Dane są przechowywane w wierszach tabeli, a dane w obrębie wiersza są zgrupowane według rodziny kolumn. HBase jest bezschematową bazą danych, co oznacza, że ani kolumny, ani typy danych w nich przechowywanych nie muszą być zdefiniowane przed użyciem. Kod typu open source zapewnia skalowanie liniowe, umożliwiając obsługę petabajtów danych na tysiącach węzłów. HBase może polegać na nadmiarowości danych, przetwarzaniu wsadowym i innych funkcjach, które są dostarczane przez aplikacje rozproszone w ekosystemie usługi Hadoop.   

HBase to doskonałe miejsce docelowe dla danych czujników i dzienników do przyszłej analizy.

Skalowalność HBase zależy od liczby węzłów w klastrze usługi HDInsight.

### <a name="azure-sql-database-and-azure-database"></a>Azure SQL Database i Azure Database

Platforma Azure oferuje trzy różne relacyjne bazy danych jako platformę jako usługę (PAAS).

* [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) jest implementacją Microsoft SQL Server. Aby uzyskać więcej informacji na temat wydajności, zobacz [dostrajanie wydajności w Azure SQL Database](../../sql-database/sql-database-performance-guidance.md).
* [Azure Database for MySQL](../../mysql/overview.md) to implementacja programu Oracle MySQL.
* [Azure Database for PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) to implementacja PostgreSQL.

Te produkty można skalować w górę, co oznacza, że są skalowane przez dodanie większej liczby procesorów i pamięci.  Możesz również użyć dysków Premium z produktami w celu uzyskania lepszej wydajności operacji we/wy.

## <a name="azure-analysis-services"></a>Azure Analysis Services 

Azure Analysis Services (AAS) to analityczny aparat danych używany w ramach pomocy technicznej decyzyjnej i analizy biznesowej, zapewniający dane analityczne dla raportów i aplikacji klienckich, takich jak Power BI, Excel, raporty usług Reporting Services i inne dane narzędzia do wizualizacji.

Moduły analizy można skalować przez zmianę warstw dla poszczególnych modułów.  Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Wyodrębnij i Załaduj

Gdy dane istnieją na platformie Azure, możesz użyć wielu usług, aby wyodrębnić i załadować je do innych produktów.  Usługa HDInsight obsługuje Sqoop i Flume. 

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop to narzędzie służące do wydajnego transferu danych między strukturalnymi i częściowo strukturalnymi źródłami danych. 

Sqoop używa MapReduce do importowania i eksportowania danych, w celu zapewnienia równoległej operacji i odporności na uszkodzenia.

### <a name="apache-flume"></a>Apache Flume

Apache Flume to dystrybuowana, niezawodna i dostępna usługa służąca do wydajnego zbierania, agregowania i przemieszczania dużych ilości danych dzienników. Flume ma prostą i elastyczną architekturę opartą na przepływach danych przesyłanych strumieniowo. Flume jest niezawodna i odporna na uszkodzenia z mechanizmami niezawodności możliwość dostosowania i wieloma mechanizmami trybu failover i odzyskiwania. Flume korzysta z prostego rozszerzalnego modelu danych, który umożliwia aplikacji analitycznych online.

Nie można używać Apache Flume z usługą Azure HDInsight.  Lokalna instalacja usługi Hadoop może używać Flume do wysyłania danych do obiektów blob magazynu Azure lub Azure Data Lake Storage.  Aby uzyskać więcej informacji, zobacz [Korzystanie z platformy Apache Flume z usługą HDInsight](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Przekształcaj

Gdy dane istnieją w wybranej lokalizacji, należy je wyczyścić, połączyć lub przygotować do określonego wzorca użycia.  Usługi Hive, świnie i Spark SQL są dobrym wyborami dla tego rodzaju pracy.  Są one obsługiwane w usłudze HDInsight. 

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z usługi Apache świni z usługą Apache Hadoop w usłudze HDInsight](hdinsight-use-pig.md)
* [Używanie Apache Hive jako narzędzia ETL](apache-hadoop-using-apache-hive-as-an-etl-tool.md) 
* [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
