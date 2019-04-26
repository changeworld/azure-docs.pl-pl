---
title: Użyj zewnętrznych magazynów metadanych — Azure HDInsight
description: Użyj zewnętrznych magazynów metadanych z klastrami HDInsight.
services: hdinsight
documentationcenter: ''
author: jasonwhowell
manager: cgronlun
tags: azure-portal
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 09/14/2018
ms.author: v-yiso
ms.date: 04/01/2019
ms.openlocfilehash: 3daa71c91d1e49a497a979b9b5b89df1fcb9418c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60486446"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Użyj zewnętrznych magazynów metadanych w usłudze Azure HDInsight

Apache Hive magazynu metadanych w HDInsight jest integralną część z architekturą platformy Apache Hadoop. Metadanych jest repozytorium centralnym schematu, które mogą być używane przez innych narzędzi dostępu do danych big data, takich jak Apache Spark, Interactive Query (LLAP), Presto lub Apache Pig. HDInsight używa usługi Azure SQL Database jako magazyn metadanych Hive.

![Architektura Store metadanych programu Hive HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Istnieją dwa sposoby metadanych można skonfigurować dla klastrów usługi HDInsight:

* [Domyślny magazyn metadanych](#default-metastore)
* [Niestandardowy Magazyn metadanych](#custom-metastore)

## <a name="default-metastore"></a>Domyślny magazyn metadanych

Domyślnie HDInsight tworzy metadanych z każdego typu klastra. Zamiast tego możesz określić niestandardowy Magazyn metadanych. Domyślny magazyn metadanych należy uwzględnić następujące kwestie:
- Bez dodatkowych kosztów. HDInsight tworzy metadanych z każdego typu klastra bez żadnych dodatkowych kosztów.
- Każdy domyślny magazyn metadanych jest częścią cyklu życia klastra. Po usunięciu klastra odpowiedniego magazynu metadanych i metadane również zostaną usunięte.
- Domyślny magazyn metadanych nie mogą mieć inne klastry.
- Domyślny magazyn metadanych używa podstawowe bazy danych SQL Azure, która ma ograniczenie do pięciu (jednostka transakcji bazy danych) jednostek DTU.
Ten domyślny magazyn metadanych jest zwykle używane dla stosunkowo proste obciążeń, które nie wymagają wielu klastrów i nie wymagają metadanych zachowane poza cyklu życia klastra.


## <a name="custom-metastore"></a>Niestandardowy Magazyn metadanych

HDInsight obsługuje również niestandardowe magazyny metadanych, które są zalecane w przypadku klastrów produkcyjnych:
- Można określić swoją własną bazę danych SQL Azure jako magazynu metadanych.
- Cykl życia magazynu metadanych nie jest związany z klastrów cykl życia, dzięki czemu można tworzyć i usuwać klastry bez utraty metadanych. Metadane, takie jak schematy usługi Hive zostanie utrzymany, nawet po użytkownik usunięcie i ponowne utworzenie klastra HDInsight.
- Niestandardowy Magazyn metadanych umożliwia dołączanie wielu klastrów i typy klastrów do tego magazynu metadanych. Na przykład pojedynczy Magazyn metadanych mogą być współużytkowane między klastrami interakcyjnych zapytań Hive i platformy Spark w HDInsight.
- Płacisz za koszt magazynu metadanych (bazy danych SQL Azure) zgodnie z poziomem wydajności, które wybierzesz.
- Magazyn metadanych można zwiększać zgodnie z potrzebami.

![Przypadek użycia Store metadanych programu Hive HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)


### <a name="select-a-custom-metastore-during-cluster-creation"></a>Wybierz niestandardowy Magazyn metadanych podczas tworzenia klastra

Klaster może wskazywać wcześniej utworzoną bazę danych SQL Azure, podczas tworzenia klastra lub po utworzeniu klastra można skonfigurować bazy danych SQL. Ta opcja jest określona za pomocą magazynu > Ustawienia magazynu metadanych podczas tworzenia nowej usługi Hadoop, Spark lub interakcyjną technologią Hive klastra z witryny Azure portal.

![Portal Azure Store metadanych programu Hive HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

Można również dodać dodatkowe klastry na niestandardowy Magazyn metadanych z witryny Azure portal lub konfiguracje Ambari (Hive > Zaawansowane)

![Ambari Store metadanych programu Hive HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Najlepsze rozwiązania magazynu metadanych hive

Poniżej przedstawiono pewne ogólne HDInsight Hive magazynu metadanych najlepsze rozwiązania:

- Użyj niestandardowy Magazyn metadanych, jeśli to możliwe, ułatwiające zasoby obliczeniowe oddzielne (uruchomionego klastra) i metadanych (przechowywanego w Magazyn metadanych).
- Zacznij od warstwy S2, co zapewnia 50 jednostek DTU i 250 GB miejsca do magazynowania. Jeśli widzisz "wąskie gardło", można zwiększać bazy danych.
- Jeśli jest planowane wielu klastrów HDInsight dostęp do oddzielnych danych na użytek oddzielnej bazy danych magazynu metadanych w każdym klastrze. Jeśli udostępniasz metadanych dla wielu klastrów HDInsight, oznacza to, że klastry używają tych samych metadanych i plików danych w usłudze podstawowej użytkownika.
- Utwórz kopię zapasową swojej niestandardowy Magazyn metadanych okresowo. Usługa Azure SQL Database automatycznie generuje kopii zapasowych, ale różni się w przedziale czasu przechowywania kopii zapasowych. Aby uzyskać więcej informacji, zobacz [Dowiedz się więcej o automatycznych kopii zapasowych bazy danych SQL](../sql-database/sql-database-automated-backups.md).
- Znajdź Twojego magazynu metadanych i klaster HDInsight, w tym samym regionie, najwyższej wydajności i opłaty za ruch wychodzący z sieci najniższy.
- Monitorowanie usługi magazynu metadanych dla wydajności i dostępności przy użyciu narzędzi monitorowania bazy danych SQL platformy Azure, takich jak witryny Azure portal lub dzienniki usługi Azure Monitor.
- Po utworzeniu nowego wyższej wersji usługi Azure HDInsight względem istniejącej bazy danych niestandardowy Magazyn metadanych systemu uaktualnia schematu magazynu metadanych, jest nieodwracalne bez przywracania bazy danych z kopii zapasowej.
- Jeśli udostępniasz metadanych w wielu klastrach, upewnij się, że wszystkie klastry mają taką samą wersję HDInsight. Różne wersje programu Hive, użyj innego magazynu metadanych schematy bazy danych. Na przykład nie można udostępniać metadanych w gałęzi rejestru 1.2 i Hive 2.1 klastrach numerów wersji. 

##  <a name="apache-oozie-metastore"></a>Magazyn metadanych programu Oozie Apache

Apache Oozie to system koordynacji przepływu pracy, który zarządza zadaniami na platformie Hadoop.  Oozie obsługuje zadania platformy Hadoop dla Apache MapReduce, Pig, Hive i innych.  Oozie używa metadanych do przechowywania szczegółowe informacje na temat aktualnych i zakończonych przepływów pracy. Aby zwiększyć wydajność, korzystając z technologii Oozie, można użyć usługi Azure SQL Database jako niestandardowy Magazyn metadanych. Magazyn metadanych oferuje również dostęp do danych zadania programu Oozie, po usunięciu klastra.

Aby uzyskać instrukcje dotyczące tworzenia Magazyn metadanych programu Oozie z usługą Azure SQL Database, zobacz [Użyj Apache Oozie w przypadku przepływów pracy](hdinsight-use-oozie-linux-mac.md).

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie klastrów w HDInsight przy użyciu technologii Apache Hadoop, Apache Spark, Apache Kafka i więcej](./hdinsight-hadoop-provision-linux-clusters.md)
