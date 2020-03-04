---
title: Korzystanie z zewnętrznych magazynów metadanych — Azure HDInsight
description: Użyj zewnętrznych magazynów metadanych z klastrami usługi Azure HDInsight i najlepszymi rozwiązaniami.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: edb2d256d3e5d98c52dbdff1162e0e030ebe2be3
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251052"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Korzystanie z zewnętrznych magazynów metadanych w usłudze Azure HDInsight

Usługa HDInsight umożliwia przejęcie kontroli nad danymi i metadanymi przez wdrożenie kluczowych rozwiązań do przesyłania metadanych oraz baz danych zarządzania do zewnętrznych magazynów danych. Ta funkcja jest obecnie dostępna dla [Apache Hive metadanych](#custom-metastore), usługi [Apache Oozie](#apache-oozie-metastore) i [bazy danych Apache Ambari](#custom-ambari-db).

Magazyn metadanych Apache Hive w usłudze HDInsight jest istotną częścią architektury Apache Hadoop. Magazyn metadanych to centralne repozytorium schematów, które może być używane przez inne narzędzia dostępu do danych Big Data, takie jak Apache Spark, Interactive Query (LLAP), Presto lub Apache świni. Usługa HDInsight używa Azure SQL Database jako magazyn metadanych Hive.

![Architektura magazynu metadanych usługi HDInsight Hive](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Istnieją dwa sposoby konfigurowania magazynu metadanych dla klastrów usługi HDInsight:

* [Domyślny magazyn metadanych](#default-metastore)
* [Niestandardowy magazyn metadanych](#custom-metastore)

## <a name="default-metastore"></a>Domyślny magazyn metadanych

Domyślnie Usługa HDInsight tworzy magazyn metadanych dla każdego typu klastra. Zamiast tego można określić niestandardowy magazyn metadanych. Domyślny magazyn metadanych obejmuje następujące zagadnienia:

* Brak dodatkowych kosztów. Usługa HDInsight tworzy magazyn metadanych z każdym typem klastra bez dodatkowych kosztów.

* Każdy domyślny magazyn metadanych jest częścią cyklu życia klastra. Po usunięciu klastra są również usuwane odpowiednie magazyny i metadane.

* Nie można udostępnić domyślnego magazynu metadanych w innych klastrach.

* Domyślny magazyn metadanych jest używany w podstawowej bazie danych SQL Azure, która ma pięć jednostek DTU (jednostka transakcji bazy danych).
Ten domyślny magazyn metadanych jest zwykle używany dla relatywnie prostych obciążeń, które nie wymagają wielu klastrów i nie wymagają metadanych przechowywanych poza cyklem życia klastra.

## <a name="custom-metastore"></a>Niestandardowy magazyn metadanych

Usługa HDInsight obsługuje również niestandardowe magazyny metadanych, które są zalecane w przypadku klastrów produkcyjnych:

* Jako magazyn metadanych należy określić własne Azure SQL Database.

* Cykl życia magazynu metadanych nie jest powiązany z cyklem życia klastrów, dzięki czemu można tworzyć i usuwać klastry bez utraty metadanych. Metadane, takie jak schematy Hive, będą zachowywane nawet po usunięciu i ponownym utworzeniu klastra usługi HDInsight.

* Niestandardowy magazyn metadanych umożliwia dołączenie wielu klastrów i typów klastrów do tego magazynu. Na przykład pojedynczy magazyn metadanych może być współużytkowany przez zapytania interaktywne, Hive i klastry Spark w usłudze HDInsight.

* Płacisz za koszt magazynu metadanych (Azure SQL DB) zgodnie z wybranym poziomem wydajności.

* W razie konieczności można skalować w górę magazyn metadanych.

* Klaster i zewnętrzny magazyn metadanych muszą być hostowane w tym samym regionie.

![Przypadek użycia magazynu metadanych Hive usługi HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Tworzenie i konfiguracja Azure SQL Database dla niestandardowego magazynu metadanych

Przed skonfigurowaniem niestandardowego magazyn metadanych Hive klastra usługi HDInsight należy utworzyć lub mieć istniejące Azure SQL Database.  Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie pojedynczej bazy danych w usłudze Azure SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal).

Aby upewnić się, że klaster usługi HDInsight może uzyskać dostęp do połączonej Azure SQL Database, skonfiguruj Azure SQL Database reguły zapory, aby umożliwić usługom i zasobom platformy Azure dostęp do serwera.

Tę opcję można włączyć w Azure Portal, klikając pozycję **Ustaw zaporę serwera**, **a następnie klikając** pozycję **Zezwól usługom i zasobom platformy Azure na dostęp do tego serwera** dla Azure SQL Database serwera lub bazy danych. Aby uzyskać więcej informacji, zobacz [Tworzenie reguł zapory IP i zarządzanie nimi](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

![przycisk ustawiania zapory serwera](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![Zezwalaj na dostęp do usług platformy Azure](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Wybierz niestandardowy magazyn metadanych podczas tworzenia klastra

Możesz wskazać klaster do wcześniej utworzonego Azure SQL Database podczas tworzenia klastra lub można skonfigurować SQL Database po utworzeniu klastra. Ta opcja jest określana za pomocą **ustawień magazynu > Storage** podczas tworzenia nowego klastra usługi Hadoop, Spark lub Interactive w programie Azure Portal.

![Magazyn metadanych Hive usługi HDInsight Azure Portal](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-best-practices"></a>magazyn metadanych Hive najlepszych praktyk

Oto kilka ogólnych magazyn metadanych Hive najlepszych rozwiązań dotyczących usługi HDInsight:

* W miarę możliwości używaj niestandardowego magazynu metadanych, aby pomóc w oddzieleniu zasobów obliczeniowych (uruchomiony klaster) i metadanych (przechowywanych w magazynie Metadata).

* Zacznij od warstwy S2, która zapewnia 50 jednostek DTU i 250 GB miejsca w magazynie. Jeśli widzisz wąskie gardło, możesz skalować bazę danych w górę.

* Jeśli zamierzasz korzystać z wielu klastrów usługi HDInsight, aby uzyskać dostęp do oddzielnych danych, użyj oddzielnej bazy danych dla magazynu metadanych w każdym klastrze. W przypadku udostępniania magazynu metadanych w wielu klastrach usługi HDInsight oznacza to, że w klastrach są używane te same metadane i bazowe pliki danych użytkownika.

* Okresowo twórz kopię zapasową niestandardowego magazynu metadanych. Azure SQL Database automatycznie generuje kopie zapasowe, ale przedział czasu przechowywania kopii zapasowych jest różny. Aby uzyskać więcej informacji, zobacz informacje [o automatycznym SQL Database kopii zapasowych](../sql-database/sql-database-automated-backups.md).

* Znajdź magazyn metadanych i klaster usługi HDInsight w tym samym regionie, aby uzyskać najwyższą wydajność i niskie opłaty za ruch wychodzący w sieci.

* Monitoruj magazyn metadanych pod kątem wydajności i dostępności przy użyciu narzędzi do monitorowania Azure SQL Database, takich jak dzienniki Azure Portal lub Azure Monitor.

* Gdy nowa, wyższa wersja usługi Azure HDInsight jest tworzona na podstawie istniejącej niestandardowej bazy danych magazynu metadanych, system uaktualnia schemat magazynu metadanych, który jest nieodwracalny bez przywracania bazy danych z kopii zapasowej.

* W przypadku udostępniania magazynu metadanych w wielu klastrach upewnij się, że wszystkie klastry są w tej samej wersji usługi HDInsight. Różne wersje programu Hive używają różnych schematów bazy danych magazynu metadanych. Nie można na przykład udostępnić magazynu metadanych w ramach klastrów Hive 2,1 i Hive 3,1

* W usłudze HDInsight 4,0, Spark i Hive używają niezależnych wykazów do uzyskiwania dostępu do tabel SparkSQL lub Hive. Tabela utworzona przez platformę Spark znajduje się w katalogu Spark. Tabela utworzona w usłudze Hive znajduje się w katalogu Hive. Różni się to od usługi HDInsight 3,6, w której znajduje się gałąź Hive i wspólny katalog usługi Spark. Integracja Hive i Spark w usłudze HDInsight 4,0 opiera się na łączniku magazynu Hive (obsługiwane). OBSŁUGIWANE działa jako Most między platformami Spark i Hive. [Dowiedz się więcej o łączniku magazynu Hive](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

## <a name="apache-oozie-metastore"></a>Magazyn metadanych Apache Oozie

Apache Oozie to system koordynacji przepływu pracy, który zarządza zadaniami usługi Hadoop.  Usługa Oozie obsługuje zadania Hadoop dla platformy Apache MapReduce, trzody chlewnej, Hive i innych.  Oozie używa magazynu metadanych do przechowywania szczegółowych informacji o bieżących i zakończonych przepływach pracy. Aby zwiększyć wydajność podczas korzystania z programu Oozie, można użyć Azure SQL Database jako niestandardowego magazynu metadanych. Magazyn metadanych może także zapewnić dostęp do danych zadania Oozie po usunięciu klastra.

Aby uzyskać instrukcje dotyczące tworzenia magazynu metadanych Oozie z Azure SQL Database, zobacz [Korzystanie z platformy Apache Oozie dla przepływów pracy](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>Niestandardowa baza danych Ambari

Aby użyć własnej zewnętrznej bazy danych z oprogramowaniem Apache Ambari w usłudze HDInsight, zobacz temat [Custom dataAmbari Database](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie klastrów w usłudze HDInsight przy użyciu technologii Apache Hadoop, Apache Spark, Apache Kafka i innych](./hdinsight-hadoop-provision-linux-clusters.md)
