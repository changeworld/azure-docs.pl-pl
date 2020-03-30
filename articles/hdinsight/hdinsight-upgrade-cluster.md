---
title: Migrowanie klastra do nowszej wersji
titleSuffix: Azure HDInsight
description: Poznaj wskazówki dotyczące migracji klastra usługi Azure HDInsight do nowszej wersji.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/31/2020
ms.openlocfilehash: f7198aeff5e9ef6d37e29c2336dc38e4eec0dda1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023977"
---
# <a name="migrate-hdinsight-cluster-to-a-newer-version"></a>Migrowanie klastra USŁUGI HDInsight do nowszej wersji

Aby korzystać z najnowszych funkcji HDInsight, zaleca się regularne migrowanie klastrów HDInsight do najnowszej wersji. Usługa HDInsight nie obsługuje uaktualnień w miejscu, w których istniejący klaster jest uaktualniany do nowszej wersji składnika. Należy utworzyć nowy klaster z żądaną wersją składnika i platformy, a następnie przeprowadzić migrację aplikacji, aby użyć nowego klastra. Postępuj zgodnie z poniższymi wskazówkami, aby przeprowadzić migrację wersji klastra HDInsight.

> [!NOTE]  
> Aby uzyskać informacje na temat obsługiwanych wersji programu HDInsight, zobacz [wersje składników HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="migration-tasks"></a>Zadania migracji

Przepływ pracy w celu uaktualnienia klastra HDInsight jest następujący.
![Diagram przepływu pracy uaktualnienia systemu HDInsight](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. Przeczytaj każdą sekcję tego dokumentu, aby zrozumieć zmiany, które mogą być wymagane podczas uaktualniania klastra HDInsight.
2. Utwórz klaster jako środowisko testowania/zapewniania jakości. Aby uzyskać więcej informacji na temat tworzenia klastra, zobacz [Dowiedz się, jak tworzyć klastry HDInsight oparte na systemie Linux](hdinsight-hadoop-provision-linux-clusters.md)
3. Kopiowanie istniejących zadań, źródeł danych i pochłaniacze do nowego środowiska.
4. Wykonaj testy sprawdzania poprawności, aby upewnić się, że zadania działają zgodnie z oczekiwaniami w nowym klastrze.

Po sprawdzeniu, że wszystko działa zgodnie z oczekiwaniami, zaplanuj przestoje dla migracji. Podczas tego przestoju wykonaj następujące czynności:

1. Śmiuj wszystkie dane przejściowe przechowywane lokalnie w węzłach klastra. Na przykład, jeśli masz dane przechowywane bezpośrednio w węźle głównym.
1. [Usuń istniejący klaster](./hdinsight-delete-cluster.md).
1. Utwórz klaster w tej samej podsieci wirtualnej z najnowszą (lub obsługiwaną) wersją HDI przy użyciu tego samego domyślnego magazynu danych, którego używał poprzedni klaster. Dzięki temu nowy klaster, aby kontynuować pracę z istniejącymi danymi produkcyjnymi.
1. Zaimportuj wszystkie dane przejściowe, których kopię zapasową wykonasz.
1. Rozpocznij zadania/kontynuuj przetwarzanie przy użyciu nowego klastra.

## <a name="workload-specific-guidance"></a>Wskazówki dotyczące obciążenia pracą

Następujące dokumenty zawierają wskazówki dotyczące migracji określonych obciążeń:

* [Migrowanie bazy HBase](./hbase/apache-hbase-migrate-new-version.md)
* [Migrowanie kafki](./kafka/migrate-versions.md)
* [Migrowanie kwerendy hive/interaktywnej](./interactive-query/apache-hive-migrate-workloads.md)

## <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania bazy danych, zobacz [Odzyskiwanie bazy danych SQL platformy Azure przy użyciu automatycznych kopii zapasowych bazy danych.](../sql-database/sql-database-recovery-using-backups.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak tworzyć klastry HDInsight oparte na systemie Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Łączenie się z usługą HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Zarządzanie klastrem opartym na systemie Linux przy użyciu Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Informacje o wersji programu HDInsight](./hdinsight-version-release.md)
