---
title: Użyj metadanych zewnętrzne magazyny - Azure HDInsight | Dokumentacja firmy Microsoft
description: Magazyny metadanych zewnętrznych za pomocą klastrów usługi HDInsight.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
tags: azure-portal
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: jgao
ms.openlocfilehash: 26f71b8efb7d31192ceecf03d9c0ed904196faf1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Użyj zewnętrznego metadanych magazynów w usłudze Azure HDInsight

Potrzeby magazynu metadanych Hive w usłudze HDInsight jest integralną część architektura platformy Hadoop. Potrzeby magazynu metadanych to repozytorium centralnej schematu, które mogą być używane przez innych narzędzi dostępu do danych big data, takich jak Spark, zapytania interakcyjne (LLAP), Presto lub Pig. HDInsight używa bazy danych SQL Azure jako potrzeby magazynu metadanych Hive.

![Architektura magazynu metadanych Hive HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Istnieją dwa sposoby, które można skonfigurować potrzeby magazynu metadanych dla klastrów usługi HDInsight:

* [Domyślne potrzeby magazynu metadanych](#default-metastore)
* [Niestandardowe potrzeby magazynu metadanych](#custom-metastore)

## <a name="default-metastore"></a>Domyślne potrzeby magazynu metadanych

Domyślnie HDInsight udostępnia potrzeby magazynu metadanych z każdym typem klastra. Zamiast tego można określić niestandardowe potrzeby magazynu metadanych. Domyślne potrzeby magazynu metadanych należy uwzględnić następujące kwestie:
- Bez dodatkowych kosztów. HDInsight udostępnia potrzeby magazynu metadanych z każdy typ klastra bez żadnych dodatkowych kosztów dla Ciebie.
- Każda domyślna potrzeby magazynu metadanych jest częścią cyklu życia klastra. Po usunięciu klastra również usunięte potrzeby magazynu metadanych i metadanych.
- Domyślne potrzeby magazynu metadanych nie mogą mieć inne klastry.
- Domyślne potrzeby magazynu metadanych używa podstawowych Azure bazą danych SQL, która ma ograniczenie (jednostka transakcji bazy danych) do 5 jednostek dtu w warstwie.
To domyślne potrzeby magazynu metadanych jest zwykle używany dla stosunkowo proste obciążeń, które nie wymagają wielu klastrów i nie wymagają metadanych zachowane poza cyklem życia klastra.


## <a name="custom-metastore"></a>Niestandardowe potrzeby magazynu metadanych

HDInsight obsługuje również niestandardowe magazyny, które są zalecane w przypadku klastrów produkcyjnych:
- Możesz określić własne bazy danych SQL Azure jako potrzeby magazynu metadanych.
- Cykl życia potrzeby magazynu metadanych nie jest powiązany cykl klastry, dlatego można tworzyć i usuwać klastry bez utraty metadanych. Metadane, takie jak schematy programu Hive pozostają, nawet po usunięcie i ponowne utworzenie klastra usługi HDInsight.
- Niestandardowe potrzeby magazynu metadanych umożliwia dołączanie wielu klastrów i typy klastrów do tego potrzeby magazynu metadanych. Na przykład pojedynczy potrzeby magazynu metadanych może być udostępniane przez klastry interakcyjne zapytania, Hive i Spark w usłudze HDInsight.
- Płaci się kosztów potrzeby magazynu metadanych (bazy danych SQL Azure), należy wybrać poziom wydajności.
- Potrzeby magazynu metadanych można zwiększać zgodnie z potrzebami.


![Przypadek użycia magazynu metadanych Hive HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

<!-- Image – Typical shared custom Metastore scenario in HDInsight (?) -->



### <a name="select-a-custom-metastore-during-cluster-creation"></a>Wybierz niestandardowe potrzeby magazynu metadanych podczas tworzenia klastra

Możesz wskazać klastra wcześniej utworzono bazę danych SQL Azure podczas tworzenia klastra lub po utworzeniu klastra można skonfigurować bazy danych SQL. Ta opcja jest określona z magazynem > Ustawienia potrzeby magazynu metadanych podczas tworzenia nowego Hadoop, Spark lub interakcyjne gałęzi klastra z portalu Azure.

![Portal Azure magazynu metadanych Hive HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

Można również dodać dodatkowe klastry na potrzeby magazynu metadanych niestandardowych z portalu Azure lub konfiguracje Ambari (Hive > Zaawansowane)

![Ambari magazynu metadanych Hive HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Najlepsze rozwiązania w zakresie potrzeby magazynu metadanych hive

Poniżej przedstawiono pewne ogólne HDInsight Hive potrzeby magazynu metadanych najlepsze rozwiązania:

- Za pomocą niestandardowych potrzeby magazynu metadanych Jeśli to możliwe, ponieważ ułatwi to zasoby obliczeniowe oddzielne (uruchomionych klastra) i metadanych (przechowywane w potrzeby magazynu metadanych).
- Rozpocznij od warstwy S2, co zapewnia jednostek dtu w warstwie 50 i 250 GB miejsca do magazynowania. Jeśli widzisz wąskiego gardła można skalować bazy danych.
- Upewnij się, że potrzeby magazynu metadanych utworzone dla jednego klastra usługi HDInsight w wersji nie jest udostępniony w różnych wersjach klastra usługi HDInsight. Różne wersje Hive używają różnych schematach. Na przykład nie można udostępnić potrzeby magazynu metadanych z klastrami zarówno Hive 1.2 i Hive 2.1.
- Utwórz kopię zapasową z niestandardowych potrzeby magazynu metadanych okresowo.
- Zachowaj Twoje potrzeby magazynu metadanych i klaster usługi HDInsight w tym samym regionie.
- Monitorowanie Twoje potrzeby magazynu metadanych wydajności i dostępności przy użyciu narzędzi monitorowania bazy danych SQL Azure, takich jak portalu Azure lub usługi Analiza dzienników Azure.

## <a name="oozie-metastore"></a>Potrzeby magazynu metadanych Oozie

Apache Oozie to system koordynacji przepływu pracy, który zarządza zadaniami na platformie Hadoop.  Oozie obsługuje zadania platformy Hadoop dla Apache MapReduce Pig, Hive i innych użytkowników.  Oozie przechowuje potrzeby magazynu metadanych szczegółowe informacje o aktualnych i zakończonych przepływów pracy. Aby zwiększyć wydajność, korzystając z Oozie, bazy danych SQL Azure służy jako niestandardowego potrzeby magazynu metadanych. Potrzeby magazynu metadanych oferuje również dostęp do danych zadania Oozie, po usunięciu klastra.

Aby uzyskać instrukcje dotyczące tworzenia potrzeby magazynu metadanych Oozie z bazy danych SQL Azure, zobacz [Oozie używany dla przepływów pracy](hdinsight-use-oozie-linux-mac.md).

## <a name="next-steps"></a>Kolejne kroki

- [Ustawianie klastrów w usłudze HDInsight Hadoop, Spark, Kafka i](./hdinsight-hadoop-provision-linux-clusters.md)
