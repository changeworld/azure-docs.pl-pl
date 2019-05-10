---
title: Usługa Azure HDInsight Accelerated zapisy do bazy danych Apache HBase
description: Zawiera omówienie funkcji usługi Azure HDInsight Accelerated zapisuje, która używa dysków zarządzanych w warstwie premium, aby zwiększyć wydajność, Apache HBase zapisu wyprzedzeniem dziennika.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 219899c2e336f544ff6572589cc79f84f555490d
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233833"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Usługa Azure HDInsight Accelerated zapisy do bazy danych Apache HBase

Ten artykuł zawiera tła **Accelerated zapisuje** funkcję dla bazy danych Apache HBase w usłudze Azure HDInsight i jak ją można efektywnie używać aby zwiększyć wydajność zapisu. **Przyspieszona zapisów** używa [dyskach zarządzanych platformy Azure w warstwie premium SSD](../../virtual-machines/linux/disks-types.md#premium-ssd) poprawić wydajność programu Apache HBase zapisu wyprzedzeniem dziennika (WAL). Aby dowiedzieć się więcej na temat bazy danych Apache HBase, zobacz [co to jest Apache HBase w HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Omówienie architektury HBase

W bazie danych HBase **wiersz** składa się z co najmniej jeden **kolumn** i jest identyfikowany przez **klucz wiersza**. Wiele wierszy tworzą **tabeli**. W kolumnach **komórek**, które są wersje oznaczony sygnaturą czasową wartości w tej kolumnie. Kolumny są grupowane w **rodziny kolumn**, i wszystkich kolumn w rodzinie kolumny będą przechowywane w plikach magazynu o nazwie **HFiles**.

**Regiony** w bazie danych HBase są używane w celu zrównoważenia obciążenia przetwarzania danych. HBase przechowuje pierwsze wiersze tabeli w jednym regionie. Wiersze są dystrybuowane między wieloma regionami ilości danych w tej tabeli zwiększa. **Serwery regionów** mogły obsługiwać żądania dla wielu regionów.

## <a name="write-ahead-log-for-apache-hbase"></a>Dziennik zapisu z wyprzedzeniem dla bazy danych Apache HBase

Baza danych HBase najpierw zapisuje aktualizacje danych typu Dziennik zatwierdzania o nazwie zapisu wyprzedzeniem dziennika (WAL). Po aktualizacji są przechowywane w pliku WAL, są zapisywane w pamięci **magazynu pamięci**. Gdy dane w pamięci osiągnie maksymalną pojemność, zapisane na dysku jako **HFile**.

Jeśli **RegionServer** ulegnie awarii lub przestanie być dostępny przed wysłaniem magazynu pamięci dziennik zapisu wyprzedzeniem może służyć do odtworzenia aktualizacji. Bez pliku WAL Jeśli **RegionServer** ulega awarii przed opróżnianie aktualizacje **HFile**, wszystkie te aktualizacje zostaną utracone.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Przyspieszone funkcji zapisuje w usłudze Azure HDInsight dla bazy danych Apache HBase

Funkcja przyspieszonej zapisuje rozwiązuje problem z większych opóźnień zapisu w wyniku użycia zapisu dalej dzienniki, które znajdują się w magazynie w chmurze.  Klastry HDInsight Apache HBase funkcję przyspieszonej zapisuje dołącza premium SSD — managed disks do każdego RegionServer (węzeł procesu roboczego). Zapisu dalej dzienniki są następnie zapisywane do pliku System (HDFS, Hadoop) zainstalowanych na tych dysków warstwy premium — zamiast magazynu w chmurze.  Dyski zarządzane Premium Użyj Solid-State dyski SSD i oferują doskonałą wydajność operacji We/Wy przy użyciu odporność na uszkodzenia.  Inaczej niż w przypadku dysków niezarządzanych Jeśli jedna jednostka magazynu ulegnie uszkodzeniu, nie wpłynie to inne jednostki magazynu, w tym samym zestawie dostępności.  W wyniku dysków managed disks zapewnia małe opóźnienia zapisu i większą odporność aplikacji. Aby dowiedzieć się więcej na temat usługi Azure managed disks, zobacz [wprowadzenie do usługi Azure managed disks w](../../virtual-machines/windows/managed-disks-overview.md). 

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Jak włączyć Accelerated zapisuje do bazy danych HBase w HDInsight

Do utworzenia nowego klastra HBase z funkcją przyspieszonej zapisuje, wykonaj kroki opisane w [konfigurowanie klastrów w HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) aż **kroku 3, magazynu**. W obszarze **ustawienia magazynu metadanych**, kliknij pole wyboru obok pozycji **Włącz Accelerated zapisuje (wersja zapoznawcza)**. Następnie kontynuuj pozostałe kroki w celu utworzenia klastra.

![Włącz opcję jej jako przyspieszonej zapisu dla bazy danych Apache HDInsight HBase](./media/apache-hbase-accelerated-writes/accelerated-writes-cluster-creation.png)

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
* Aby uaktualnić do użycia, przyspieszonej zapisuje przez klaster HDInsight Apache HBase, zobacz [Migrowanie klastra Apache HBase do nowej wersji](apache-hbase-migrate-new-version.md).
