---
title: Usługa Azure HDInsight — przyspieszone zapisy dla oprogramowania Apache HBase
description: Zawiera omówienie funkcji przyspieszonego zapisu usługi Azure HDInsight, która używa dysków zarządzanych w warstwie Premium w celu zwiększenia wydajności dziennika zapisu z wyprzedzeniem Apache HBase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 7165bab96d037f6782bc9aa6767cadd9b35f058c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76764579"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Usługa Azure HDInsight — przyspieszone zapisy dla oprogramowania Apache HBase

Ten artykuł zawiera tło dotyczące funkcji **przyspieszonego zapisu** dla bazy danych Apache HBase w usłudze Azure HDInsight oraz sposobu, w jaki można jej skutecznie używać do zwiększania wydajności zapisu. **Przyspieszone zapisy** używa [platformy Azure premium dysków zarządzanych SSD](../../virtual-machines/linux/disks-types.md#premium-ssd) w celu zwiększenia wydajności Apache HBase Write Ahead Log (WAL). Aby dowiedzieć się więcej o Apache HBase, zobacz [Co to jest Apache HBase w hdinsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Omówienie architektury HBase

W bazie danych HBase **wiersz** składa się z jednej lub kilku **kolumn** i jest identyfikowany za pomocą **klucza wiersza**. Wiele wierszy tworzą **tabelę**. Kolumny zawierają **komórki,** które są sygnaturą czasową wartości w tej kolumnie. Kolumny są pogrupowane w **rodziny kolumn,** a wszystkie kolumny z rodziny kolumn są przechowywane razem w plikach magazynu o nazwie **HFiles**.

**Regiony** w bazie danych HBase są używane do równoważenia obciążenia przetwarzania danych. HBase najpierw przechowuje wiersze tabeli w jednym regionie. Wiersze są rozłożone w wielu regionach wraz ze wzrostem ilości danych w tabeli. **Serwery regionów** mogą obsługiwać żądania dla wielu regionów.

## <a name="write-ahead-log-for-apache-hbase"></a>Napisz dziennik z wyprzedzeniem dla Apache HBase

HBase najpierw zapisuje aktualizacje danych do typu dziennika zatwierdzania o nazwie Write Ahead Log (WAL). Po zapisaniu aktualizacji w WW jest zapisywana w pamięci **MemStore**. Gdy dane w pamięci osiągnie maksymalną pojemność, jest zapisywany na dysku jako **HFile**.

Jeśli **RegionServer** ulega awarii lub staje się niedostępny przed opróżnieniem MemStore, dziennik zapisu z wyprzedzeniem może służyć do odtwarzania aktualizacji. Bez WAL, jeśli **RegionServer** ulegnie awarii przed opróżnianie aktualizacji do **HFile**, wszystkie te aktualizacje zostaną utracone.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Funkcja przyspieszonego zapisu w usłudze Azure HDInsight dla bazy danych Apache HBase

Funkcja Przyspieszone zapisy rozwiązuje problem wyższych opóźnień zapisu spowodowanych używaniem dzienników zapisu z wyprzedzeniem, które znajdują się w magazynie w chmurze.  Funkcja Przyspieszone zapisy dla klastrów HDInsight Apache HBase dołącza dyski zarządzane dyskami premium SSD do każdego regionu (węzła procesu roboczego). Dzienniki zapisu z wyprzedzeniem są następnie zapisywane w systemie plików Hadoop (HDFS) zamontowanym na tych dyskach zarządzanych premium zamiast w chmurze.  Dyski zarządzane w wersji Premium używają dysków PÓŁPRZEWODNIKOWYCH (SSD) i oferują doskonałą wydajność we/wy z odpornością na uszkodzenia.  W przeciwieństwie do dysków niezarządzanych, jeśli jedna jednostka magazynu ulegnie ulegnie upadkowi, nie wpłynie to na inne jednostki magazynu w tym samym zestawie dostępności.  W rezultacie dyski zarządzane zapewniają niskie opóźnienia zapisu i lepszą odporność aplikacji. Aby dowiedzieć się więcej o dyskach zarządzanych przez platformę Azure, zobacz [Wprowadzenie do dysków zarządzanych platformy Azure](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Jak włączyć przyspieszone zapisy dla HBase w HDInsight

Aby utworzyć nowy klaster HBase z funkcją Przyspieszone zapisy, wykonaj czynności opisane w [funkcji Konfigurowanie klastrów w umiaśnieniu HDInsight,](../hdinsight-hadoop-provision-linux-clusters.md) aż do osiągnięcia **kroku 3, Magazyn**. W obszarze **Ustawienia metasklepu**zaznacz pole wyboru obok pozycji **Włącz przyspieszone zapisy HBase**. Następnie przejdź do pozostałych kroków tworzenia klastra.

![Włącz opcję przyspieszonego zapisu dla bazy HDInsight Apache HBase](./media/apache-hbase-accelerated-writes/azure-portal-cluster-storage-hbase.png)

## <a name="other-considerations"></a>Inne zagadnienia

Aby zachować trwałość danych, należy utworzyć klaster z co najmniej trzema węzłami procesu roboczego. Po utworzeniu nie można skalować klastra w dół do mniej niż trzech węzłów procesu roboczego.

Opróżnić lub wyłączyć tabele HBase przed usunięciem klastra, tak aby nie utracić danych dziennika zapisu z wyprzedzeniem.

```
flush 'mytable'
```

```
disable 'mytable'
```

Wykonaj podobne czynności podczas skalowania w dół klastra: opróżnij tabele i wyłącz tabele, aby zatrzymać przychodzące dane. Nie można skalować w dół klastra do mniej niż trzech węzłów.

Wykonaj następujące kroki zapewni pomyślne skalowanie w dół i uniknąć możliwości namenode przechodzi w trybie awaryjnym z powodu niedoreplikowanych lub plików tymczasowych.

Jeśli nazwa nie przejść do safemode po skalowaniu w dół, użyj poleceń hdfs do ponownej replikacji niedoreplikowanych bloków i uzyskać hdfs z trybu awaryjnego. Ta ponowna replikacja umożliwia pomyślne ponowne uruchomienie bazy danych HBase.

## <a name="next-steps"></a>Następne kroki

* Oficjalna dokumentacja Apache HBase dotycząca [funkcji Dziennika zapisu z wyprzedzeniem](https://hbase.apache.org/book.html#wal)
* Aby uaktualnić klaster HDInsight Apache HBase do używania przyspieszonych zapisów, zobacz [Migrowanie klastra Apache HBase do nowej wersji](apache-hbase-migrate-new-version.md).
