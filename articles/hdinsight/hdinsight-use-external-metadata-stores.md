---
title: Korzystanie z zewnętrznych magazynów metadanych — Azure HDInsight
description: Użyj zewnętrznych magazynów metadanych z klastrami usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/03/2020
ms.openlocfilehash: 0cadf3930008868fe223e6e1024a2d14d17d8131
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657121"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Korzystanie z zewnętrznych magazynów metadanych w usłudze Azure HDInsight

Usługa HDInsight umożliwia przejęcie kontroli nad danymi i metadanymi za pomocą zewnętrznych magazynów danych. Ta funkcja jest dostępna dla [apache hive metastore](#custom-metastore), [Apache Oozie metastore](#apache-oozie-metastore)i [Apache Ambari bazy danych](#custom-ambari-db).

Metastore Apache Hive w HDInsight jest istotną częścią architektury Apache Hadoop. Magazyn metasekonu jest centralnym repozytorium schematów. Metasklep jest używany przez inne narzędzia dostępu do dużych zbiorów danych, takie jak Apache Spark, Interactive Query (LLAP), Presto lub Apache Pig. Usługa HDInsight używa bazy danych SQL Azure jako magazynu meteoru.

![Architektura magazynu metadanych gałęzi HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Istnieją dwa sposoby konfigurowania magazynu metastore dla klastrów HDInsight:

* [Domyślny metasklep](#default-metastore)
* [Niestandardowy sklep metasklepu](#custom-metastore)

## <a name="default-metastore"></a>Domyślny metasklep

Domyślnie usługa HDInsight tworzy magazyn metasklepu przy każdym typie klastra. Zamiast tego można określić niestandardowy magazyn metasklepu. Domyślny metasklep zawiera następujące zagadnienia:

* Bez dodatkowych kosztów. Usługa HDInsight tworzy magazyn metasklepu z każdym typem klastra bez żadnych dodatkowych kosztów.

* Każdy domyślny magazyn metasja część cyklu życia klastra. Po usunięciu klastra odpowiedni magazyn meta i metadane są również usuwane.

* Nie można udostępnić domyślnego magazynu metasklepu innym klastrom.

* Domyślny magazyn metastore używa podstawowej usługi Azure SQL DB, która ma limit pięciu jednostek transakcji jednostek DTU (jednostki transakcji bazy danych).
Ten domyślny magazyn metasa przez zwykle jest używany dla stosunkowo prostych obciążeń. Obciążenia, które nie wymagają wielu klastrów i nie potrzebują metadanych zachowanych poza cyklem życia klastra.

## <a name="custom-metastore"></a>Niestandardowy sklep metasklepu

Usługa HDInsight obsługuje również niestandardowe metasklepy, które są zalecane dla klastrów produkcyjnych:

* Określ własną usługę Azure SQL Database jako magazyn meteoformowy.

* Cykl życia magazynu metastore nie jest powiązany z cyklem życia klastrów, dzięki czemu można tworzyć i usuwać klastry bez utraty metadanych. Metadane, takie jak schematy hive będą zachowywać się nawet po usunięciu i ponownym utworzeniu klastra HDInsight.

* Niestandardowy magazyn metasuger umożliwia dołączanie wielu klastrów i typów klastrów do tego magazynu meteada. Na przykład pojedynczy magazyn meta może być współużytkowane przez interaktywne kwerendy, hive i klastrów platformy Spark w programie HDInsight.

* Płacisz za koszt metasklepu (Azure SQL DB) zgodnie z poziomem wydajności, który wybierzesz.

* W razie potrzeby można skalować w górę magazynu metastore.

* Klaster i zewnętrzny magazyn meta musi być hostowany w tym samym regionie.

![Przypadek użycia magazynu metadanych hdinsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Tworzenie i konfiguralizy azure sql database dla niestandardowego magazynu metastore

Utwórz lub utwórz istniejącą usługę Azure SQL Database przed skonfigurowaniem niestandardowego magazynu mete hive dla klastra HDInsight.  Aby uzyskać więcej informacji, zobacz [Szybki start: Tworzenie pojedynczej bazy danych w bazie danych SQL usługi Azure](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal).

Skonfiguruj reguły zapory usługi Azure SQL Database, aby umożliwić dostęp do serwera usługom i zasobom platformy Azure. Włącz tę opcję w witrynie Azure portal, wybierając **pozycję Ustaw zaporę serwera**. Następnie wybierz **w obszarze** **Zezwalaj na usługi i zasoby platformy Azure, aby uzyskać dostęp do tego serwera** dla serwera lub bazy danych usługi Azure SQL Database. Aby uzyskać więcej informacji, zobacz [Tworzenie reguł zapory IP i zarządzanie nimi](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

![przycisk zapory serwera](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![zezwalaj na dostęp do usług platformy Azure](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Wybieranie niestandardowego magazynu metastore podczas tworzenia klastra

W dowolnym momencie można skierować klaster do wcześniej utworzonej usługi Azure SQL Database. W przypadku tworzenia klastra za pośrednictwem portalu opcja jest określona w **ustawieniach magazynu > metasklepu**.

![Portal Azure magazynu metadanych usługi HDInsight](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-guidelines"></a>Wskazówki dotyczące metastore hive

* W miarę możliwości należy użyć niestandardowego magazynu metastore, aby w każdym możliwym celu oddzielić zasoby obliczeniowe (uruchomiony klaster) i metadane (przechowywane w magazynie meteożynowym).

* Zacznij od warstwy S2, która zapewnia 50 DTU i 250 GB pamięci masowej. Jeśli widzisz wąskie gardło, można skalować bazę danych w górę.

* Jeśli zamierzasz wiele klastrów HDInsight, aby uzyskać dostęp do oddzielnych danych, użyj oddzielnej bazy danych dla magazynu metastore w każdym klastrze. Jeśli udostępniasz magazyn meta w wielu klastrach usługi HDInsight, oznacza to, że klastry używają tych samych metadanych i podstawowych plików danych użytkownika.

* Okresowo owak owak owak owacj ą niestandardowych metasklepów. Usługa Azure SQL Database generuje kopie zapasowe automatycznie, ale przedział czasu przechowywania kopii zapasowych jest różny. Aby uzyskać więcej informacji, zobacz [Dowiedz się więcej o automatycznych kopiach zapasowych bazy danych SQL](../sql-database/sql-database-automated-backups.md).

* Znajdź swój klaster metastore i HDInsight w tym samym regionie. Ta konfiguracja zapewni najwyższą wydajność i najniższe opłaty za wyjście sieci.

* Monitoruj swój metastore pod kątem wydajności i dostępności za pomocą narzędzi do monitorowania bazy danych SQL platformy Azure lub dzienników usługi Azure Monitor.

* Gdy nowa, wyższa wersja usługi Azure HDInsight jest tworzony względem istniejącej bazy danych niestandardowych metastore, system uaktualnia schemat metastore. Uaktualnienie jest nieodwracalne bez przywracania bazy danych z kopii zapasowej.

* Jeśli udostępniasz magazyn meta w wielu klastrach, upewnij się, że wszystkie klastry są tej samej wersji HDInsight. Różne wersje hive używają różnych schematów bazy danych magazynu metastore. Na przykład nie można udostępnić magazynu meta w klastrach hive 2.1 i Hive 3.1.

* W programie HDInsight 4.0 spark i hive używają niezależnych katalogów do uzyskiwania dostępu do tabel SparkSQL lub Hive. Tabela utworzona przez spark mieszka w katalogu Spark. Tabela utworzona przez Hive mieszka w katalogu Hive. To zachowanie różni się od hdinsight 3.6, gdzie Hive i Spark udostępnione wspólnego katalogu. Integracja hive i spark w programie HDInsight 4.0 zależy od łącznika magazynu hive (HWC). HWC działa jako pomost między Spark i Hive. [Dowiedz się więcej o łączniku magazynu hive](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

## <a name="apache-oozie-metastore"></a>Apache Oozie metastore

Apache Oozie to system koordynacji przepływu pracy, który zarządza zadaniami na platformie Hadoop. Oozie obsługuje zadania Hadoop dla Apache MapReduce, Pig, Hive i innych.  Oozie używa magazynu metastore do przechowywania szczegółów dotyczących przepływów pracy. Aby zwiększyć wydajność podczas korzystania z usługi Oozie, można użyć usługi Azure SQL Database jako niestandardowego magazynu metastore. Magazyn metastore zapewnia dostęp do danych zadania Oozie po usunięciu klastra.

Aby uzyskać instrukcje dotyczące tworzenia metasklepu Oozie za pomocą usługi Azure SQL Database, zobacz [Używanie apache Oozie dla przepływów pracy](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>Niestandardowa baza danych Ambari

Aby korzystać z własnej zewnętrznej bazy danych z Apache Ambari na HDInsight, zobacz [Niestandardowa baza danych Apache Ambari](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie klastrów w usłudze HDInsight przy użyciu technologii Apache Hadoop, Apache Spark, Apache Kafka i innych](./hdinsight-hadoop-provision-linux-clusters.md)
