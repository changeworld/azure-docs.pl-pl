---
title: Uaktualnianie klastra usługi HDInsight do nowszej wersji — Azure
description: Poznaj wskazówki dotyczące uaktualniania klastra usługi Azure HDInsight do nowszej wersji.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 1a1d4a71786ebb1e68f59084086b3256a1c1ea40
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951160"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>Uaktualnianie klastra usługi HDInsight do nowszej wersji

Aby skorzystać z najnowszych funkcji usługi HDInsight, zalecamy uaktualnienie klastrów usługi HDInsight do najnowszej wersji. Postępuj zgodnie z poniższymi wskazówkami, aby uaktualnić wersje klastra usługi HDInsight.

> [!NOTE]  
> Aby uzyskać informacje dotyczące obsługiwanych wersji usługi HDInsight, zobacz [wersje składników usługi HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="upgrade-tasks"></a>Zadania uaktualniania

Przepływ pracy uaktualniania klastra usługi HDInsight jest następujący:.
Diagram przepływu pracy uaktualniania ![HDInsight](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. Przeczytaj każdą z sekcji tego dokumentu, aby poznać zmiany, które mogą być wymagane podczas uaktualniania klastra usługi HDInsight.
2. Utwórz klaster jako środowisko testu/jakości. Aby uzyskać więcej informacji na temat tworzenia klastra, zobacz [informacje na temat tworzenia klastrów usługi HDInsight opartych na systemie Linux](hdinsight-hadoop-provision-linux-clusters.md)
3. Skopiuj istniejące zadania, źródła danych i ujścia do nowego środowiska.
4. Wykonaj testy weryfikacyjne, aby upewnić się, że zadania działają zgodnie z oczekiwaniami w nowym klastrze.

Po zweryfikowaniu, że wszystko działa zgodnie z oczekiwaniami, Zaplanuj przestój dla migracji. W trakcie tego przestoju wykonaj następujące czynności:

1. Wykonaj kopię zapasową wszystkich danych przejściowych przechowywanych lokalnie w węzłach klastra. Na przykład, jeśli masz dane przechowywane bezpośrednio w węźle głównym.
1. [Usuń istniejący klaster](./hdinsight-delete-cluster.md).
1. Utwórz klaster w tej samej podsieci wirtualnej z najnowszą (lub obsługiwaną) wersją HDI przy użyciu tego samego domyślnego magazynu danych, który został użyty przez poprzedni klaster. Dzięki temu nowy klaster będzie kontynuował pracę nad istniejącymi danymi produkcyjnymi.
1. Zaimportuj wszystkie dane przejściowe, których kopię zapasową utworzono.
1. Uruchom zadania/Kontynuuj przetwarzanie przy użyciu nowego klastra.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak tworzyć klastry usługi HDInsight oparte na systemie Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Łączenie się z usługą HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Zarządzanie klastrem opartym na systemie Linux przy użyciu usługi Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Informacje o wersji usługi HDInsight](./hdinsight-version-release.md)
