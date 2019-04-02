---
title: Usługa Azure HDInsight rozszerzone zapisuje dla bazy danych Apache HBase (wersja zapoznawcza)
description: Zawiera omówienie funkcji usługi Azure HDInsight rozszerzone zapisuje, która używa dysków zarządzanych w warstwie premium, aby zwiększyć wydajność, Apache HBase zapisu wyprzedzeniem dziennika.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 3/27/2019
ms.openlocfilehash: b0c71d0f101ea262b6ce56c845ef9f375a7de85e
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804211"
---
# <a name="azure-hdinsight-enhanced-writes-for-apache-hbase-preview"></a>Usługa Azure HDInsight rozszerzone zapisuje dla bazy danych Apache HBase (wersja zapoznawcza)

Ten artykuł zawiera tła **rozszerzone zapisuje** funkcję dla bazy danych Apache HBase w usłudze Azure HDInsight i jak ją można efektywnie używać aby zwiększyć wydajność zapisu. **Ulepszone zapisów** używa [dyskach zarządzanych platformy Azure w warstwie premium SSD](../../virtual-machines/linux/disks-types.md#premium-ssd) poprawić wydajność programu Apache HBase zapisu wyprzedzeniem dziennika (WAL). Aby dowiedzieć się więcej na temat bazy danych Apache HBase, zobacz [co to jest Apache HBase w HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Omówienie architektury HBase

W bazie danych HBase **wiersz** składa się z co najmniej jeden **kolumn** i jest identyfikowany przez **klucz wiersza**. Wiele wierszy tworzą **tabeli**. W kolumnach **komórek**, które są wersje oznaczony sygnaturą czasową wartości w tej kolumnie. Kolumny są grupowane w **rodziny kolumn**, i wszystkich kolumn w rodzinie kolumny będą przechowywane w plikach magazynu o nazwie **HFiles**.

**Regiony** w bazie danych HBase są używane w celu zrównoważenia obciążenia przetwarzania danych. HBase pierwsze wiersze tabeli są przechowywane w jednym regionie, a następnie się wiersze w wielu regionach, jak ilość danych w tej tabeli zwiększa. **Serwery regionów** mogły obsługiwać żądania dla wielu regionów.

## <a name="write-ahead-log-for-apache-hbase"></a>Dziennik zapisu z wyprzedzeniem dla bazy danych Apache HBase

Aktualizacje danych w bazie danych HBase najpierw są zapisywane w rodzaju dziennik zatwierdzania o nazwie zapisu wyprzedzeniem dziennika (WAL). Po aktualizacji są przechowywane w pliku WAL, są zapisywane w pamięci **magazynu pamięci**. Gdy dane w pamięci osiągnie maksymalną pojemność, zapisane na dysku jako **HFile**.

Jeśli **RegionServer** ulegnie awarii lub przestanie być dostępny przed wysłaniem magazynu pamięci dziennik zapisu wyprzedzeniem może służyć do odtworzenia aktualizacji. Bez pliku WAL Jeśli **RegionServer** uległ awarii przed opróżnianie aktualizacje **HFile**, wszystkie te aktualizacje utratę.

## <a name="enhanced-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Rozszerzone funkcji zapisuje w usłudze Azure HDInsight dla bazy danych Apache HBase

Ulepszone zapisuje funkcja rozwiązuje problem z większych opóźnień zapisu w wyniku użycia zapisu dalej dzienniki, które znajdują się w magazynie w chmurze.  Funkcja rozszerzone zapisuje w przypadku klastrów HDInsight Apache HBase dołącza premium SSD dysków zarządzanych do każdego RegionServer (węzeł procesu roboczego). Zapisu dalej dzienniki są następnie zapisywane do pliku System (HDFS, Hadoop) zainstalowanego na tych dyskach zarządzanych w warstwie premium, zamiast magazynu w chmurze.  Dyski zarządzane w warstwie Premium, użyj Solid-State dyski SSD i oferują doskonałą wydajność operacji We/Wy przy użyciu odporność na uszkodzenia.  Inaczej niż w przypadku dysków niezarządzanych Jeśli jedna jednostka magazynu ulegnie uszkodzeniu, nie wpłynie to inne jednostki magazynu, w tym samym zestawie dostępności.  W rezultacie dyski zarządzane zapewniają małe opóźnienia zapisu i większą odporność aplikacji. Aby dowiedzieć się więcej o platformie Azure usługi managed disks, zobacz [wprowadzenie do usługi Azure managed disks w](../../virtual-machines/windows/managed-disks-overview.md). 

## <a name="how-to-enable-enhanced-writes-for-hbase-in-hdinsight"></a>Jak włączyć zwiększonych zapisuje do bazy danych HBase w HDInsight

Do utworzenia nowego klastra HBase przy użyciu funkcji rozszerzone zapisuje, wykonaj kroki opisane w [konfigurowanie klastrów w HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) aż **kroku 3, magazynu**. W obszarze **ustawienia magazynu metadanych**, kliknij pole wyboru obok pozycji **Włącz rozszerzone zapisuje (wersja zapoznawcza)**. Następnie kontynuuj pozostałe kroki w celu utworzenia klastra.

![Włącz opcję rozszerzone zapisu dla bazy danych Apache HDInsight HBase](./media/apache-hbase-enhanced-writes/enhanced-writes-cluster-creation.jpg)

## <a name="other-considerations"></a>Inne zagadnienia

Aby zachować trwałość danych, należy utworzyć klaster z co najmniej trzy węzły procesu roboczego. Po utworzeniu nie można skalować w dół klastra do mniej niż trzy węzły procesu roboczego. 

Flush lub wyłączenie tabel HBase przed usunięciem klastra, tak, aby nie utracić dane zapisu dziennika wyprzedzeniem.

```
flush 'mytable'
```

```
disable 'mytable'
```

## <a name="next-steps"></a>Kolejne kroki

* Oficjalna dokumentacja bazy danych Apache HBase [zapisu wyprzedzeniem dzienników funkcji](https://hbase.apache.org/book.html#wal)
* Aby uaktualnić do użycia, rozszerzone zapisuje przez klaster HDInsight Apache HBase, zobacz [Migrowanie klastra Apache HBase do nowej wersji](apache-hbase-migrate-new-version.md).
