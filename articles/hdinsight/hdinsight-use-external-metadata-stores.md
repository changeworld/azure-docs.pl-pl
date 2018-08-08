---
title: Użyj zewnętrznych magazynów metadanych — Azure HDInsight
description: Użyj zewnętrznych magazynów metadanych z klastrami HDInsight.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.author: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: fb1401578237a92a6f164ec98e8dbcdbbb88be38
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39595402"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Użyj zewnętrznych magazynów metadanych w usłudze Azure HDInsight

Magazyn metadanych Hive w HDInsight jest integralną część Architektura usługi Hadoop. Metadanych jest repozytorium centralnym schematu, które mogą być używane przez innych narzędzi dostępu do danych big data, takich jak Spark, Interactive Query (LLAP), Presto lub Pig. HDInsight używa usługi Azure SQL Database jako magazyn metadanych Hive.

![Architektura Store metadanych programu Hive HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Istnieją dwa sposoby metadanych można skonfigurować dla klastrów usługi HDInsight:

* [Domyślny magazyn metadanych](#default-metastore)
* [Niestandardowy Magazyn metadanych](#custom-metastore)

## <a name="default-metastore"></a>Domyślny magazyn metadanych

Domyślnie HDInsight aprowizuje metadanych z każdego typu klastra. Zamiast tego możesz określić niestandardowy Magazyn metadanych. Domyślny magazyn metadanych należy uwzględnić następujące kwestie:
- Bez dodatkowych kosztów. HDInsight aprowizuje metadanych z każdego typu klastra bez żadnych dodatkowych kosztów.
- Każdy domyślny magazyn metadanych jest częścią cyklu życia klastra. Podczas usuwania klastra również usunięte magazynu metadanych i metadanych.
- Domyślny magazyn metadanych nie mogą mieć inne klastry.
- Domyślny magazyn metadanych używa podstawowe bazy danych SQL Azure, która z limitem 5 jednostek DTU (jednostka transakcji bazy danych).
Ten domyślny magazyn metadanych jest zwykle używane dla stosunkowo proste obciążeń, które nie wymagają wielu klastrów i nie wymagają metadanych zachowane poza cyklu życia klastra.


## <a name="custom-metastore"></a>Niestandardowy Magazyn metadanych

HDInsight obsługuje również niestandardowe magazyny metadanych, które są zalecane w przypadku klastrów produkcyjnych:
- Można określić swoją własną bazę danych SQL Azure jako magazynu metadanych.
- Cykl życia magazynu metadanych nie jest związany z klastrów cykl życia, dzięki czemu można tworzyć i usuwać klastry bez utraty metadanych. Metadane, takie jak schematy usługi Hive zostanie utrzymany, nawet po użytkownik usunięcie i ponowne utworzenie klastra HDInsight.
- Niestandardowy Magazyn metadanych umożliwia dołączanie wielu klastrów i typy klastrów do tego magazynu metadanych. Na przykład pojedynczy Magazyn metadanych mogą być współużytkowane między klastrami interakcyjnych zapytań Hive i platformy Spark w HDInsight.
- Płacisz za koszt magazynu metadanych (bazy danych SQL Azure) zgodnie z poziomem wydajności, które wybierzesz.
- Magazyn metadanych można zwiększać zgodnie z potrzebami.


![Przypadek użycia Store metadanych programu Hive HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

<!-- Image – Typical shared custom Metastore scenario in HDInsight (?) -->



### <a name="select-a-custom-metastore-during-cluster-creation"></a>Wybierz niestandardowy Magazyn metadanych podczas tworzenia klastra

Klaster może wskazywać wcześniej utworzoną bazę danych SQL Azure, podczas tworzenia klastra lub po utworzeniu klastra można skonfigurować bazy danych SQL. Ta opcja jest określona za pomocą magazynu > Ustawienia magazynu metadanych podczas tworzenia nowej usługi Hadoop, Spark lub interakcyjną technologią Hive klastra z witryny Azure portal.

![Portal Azure Store metadanych programu Hive HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

Można również dodać dodatkowe klastry na niestandardowy Magazyn metadanych z witryny Azure portal lub konfiguracje Ambari (Hive > Zaawansowane)

![Ambari Store metadanych programu Hive HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Najlepsze rozwiązania magazynu metadanych hive

Poniżej przedstawiono pewne ogólne HDInsight Hive magazynu metadanych najlepsze rozwiązania:

- Niestandardowy Magazyn metadanych, jeśli to możliwe, należy użyć, ponieważ ułatwi to zasoby obliczeniowe oddzielne (uruchomionego klastra) i metadanych (przechowywanego w Magazyn metadanych).
- Zacznij od warstwy S2, co zapewnia 50 jednostek DTU i 250 GB miejsca do magazynowania. Jeśli widzisz "wąskie gardło", można zwiększać bazy danych.
- Upewnij się, że Magazyn metadanych, zaprojektowane dla jednej wersji klastra HDInsight nie jest udostępniany w różnych wersjach klastra HDInsight. Różne wersje programu Hive używają różnych schematów. Na przykład nie można udostępniać metadanych klastry Hive 1.2 i Hive 2.1.
- Utwórz kopię zapasową swojej niestandardowy Magazyn metadanych okresowo.
- Zachowuj magazynu metadanych i klaster HDInsight, w tym samym regionie.
- Monitorowanie usługi magazynu metadanych dla wydajności i dostępności przy użyciu narzędzi monitorowania bazy danych SQL platformy Azure, takich jak witryny Azure portal lub usługi Azure Log Analytics.

## <a name="oozie-metastore"></a>Magazyn metadanych programu Oozie

Apache Oozie to system koordynacji przepływu pracy, który zarządza zadaniami na platformie Hadoop.  Oozie obsługuje zadania platformy Hadoop dla Apache MapReduce, Pig, Hive i innych.  Oozie używa metadanych do przechowywania szczegółowe informacje na temat aktualnych i zakończonych przepływów pracy. Aby zwiększyć wydajność, korzystając z technologii Oozie, można użyć usługi Azure SQL Database jako niestandardowy Magazyn metadanych. Magazyn metadanych oferuje również dostęp do danych zadania programu Oozie, po usunięciu klastra.

Aby uzyskać instrukcje dotyczące tworzenia Magazyn metadanych programu Oozie z usługą Azure SQL Database, zobacz [Oozie użycia dla przepływów pracy](hdinsight-use-oozie-linux-mac.md).

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie klastrów w HDInsight przy użyciu usługi Hadoop, Spark, Kafka i więcej](./hdinsight-hadoop-provision-linux-clusters.md)
