---
title: Uaktualnianie klastra usługi HDInsight do nowszej wersji — Azure
description: Poznaj wskazówki dotyczące uaktualniania klastra usługi Azure HDInsight do nowszej wersji.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: 808e592a01bca2066b7d3d37a5fdfa697d739d4b
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122452"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>Uaktualnianie klastra usługi HDInsight do nowszej wersji

Aby skorzystać z najnowszych funkcji usługi HDInsight, zalecamy uaktualnienie klastrów usługi HDInsight do najnowszej wersji. Postępuj zgodnie z poniższymi wskazówkami, aby uaktualnić wersje klastra usługi HDInsight.

> [!NOTE]  
> Aby uzyskać informacje dotyczące obsługiwanych wersji usługi HDInsight, zobacz [wersje składników usługi HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="upgrade-tasks"></a>Zadania uaktualniania

Przepływ pracy uaktualniania klastra usługi HDInsight jest następujący:.
![Diagram przepływu pracy uaktualniania usługi HDInsight](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. Przeczytaj każdą z sekcji tego dokumentu, aby poznać zmiany, które mogą być wymagane podczas uaktualniania klastra usługi HDInsight.
2. Utwórz klaster jako środowisko testu/jakości. Aby uzyskać więcej informacji na temat tworzenia klastra, zobacz [informacje na temat tworzenia klastrów usługi HDInsight opartych na systemie Linux](hdinsight-hadoop-provision-linux-clusters.md)
3. Skopiuj istniejące zadania, źródła danych i ujścia do nowego środowiska.
4. Wykonaj testy weryfikacyjne, aby upewnić się, że zadania działają zgodnie z oczekiwaniami w nowym klastrze.

Po zweryfikowaniu, że wszystko działa zgodnie z oczekiwaniami, Zaplanuj przestój dla migracji. W trakcie tego przestoju wykonaj następujące czynności:

1.  Wykonaj kopię zapasową wszystkich danych przejściowych przechowywanych lokalnie w węzłach klastra. Na przykład, jeśli masz dane przechowywane bezpośrednio w węźle głównym.
2.  Usuń istniejący klaster.
3.  Utwórz klaster w tej samej podsieci wirtualnej z najnowszą (lub obsługiwaną) wersją HDI przy użyciu tego samego domyślnego magazynu danych, który został użyty przez poprzedni klaster. Dzięki temu nowy klaster będzie kontynuował pracę nad istniejącymi danymi produkcyjnymi.
4.  Zaimportuj wszystkie dane przejściowe, których kopię zapasową utworzono.
5.  Uruchom zadania/Kontynuuj przetwarzanie przy użyciu nowego klastra.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak tworzyć klastry usługi HDInsight oparte na systemie Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Łączenie się z usługą HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Zarządzanie klastrem opartym na systemie Linux przy użyciu usługi Apache Ambari](hdinsight-hadoop-manage-ambari.md)
