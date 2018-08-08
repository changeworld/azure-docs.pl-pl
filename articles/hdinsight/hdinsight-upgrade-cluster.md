---
title: Uaktualnianie klastra HDInsight do nowszej wersji-Azure
description: Dowiedz się, jak klaster HDInsight uaktualnienia do nowszej wersji.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: cab2c7aabf23ba8636f46a92c8d864b1c9b74120
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39594849"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>Uaktualnianie klastra HDInsight do nowszej wersji
Aby móc korzystać z najnowszych funkcji HDInsight, zaleca się, że klastry HDInsight można uaktualnić do najnowszej wersji. Postępuj zgodnie z poniższych wskazówek, aby uaktualnić usługi HDInsight cluster wersji.

> [!NOTE]
> Aby uzyskać informacje na temat obsługiwanych wersji HDInsight, zobacz [wersje składników HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).
>
>

## <a name="upgrade-tasks"></a>Zadania aktualizacji
Przepływ pracy, aby uaktualnić klaster HDInsight to w następujący sposób.

![Diagram przepływu pracy uaktualniania](./media/hdinsight-upgrade-cluster/upgrade-workflow.png)

1. Przeczytaj sekcję tego dokumentu, aby zrozumieć zmiany, które mogą być wymagane w przypadku uaktualniania klastra usługi HDInsight.
2. Tworzenie klastra jako środowisko assurance jakość/testu. Aby uzyskać więcej informacji na temat tworzenia klastra, zobacz [Dowiedz się, jak tworzyć klastry HDInsight opartych na systemie Linux](hdinsight-hadoop-provision-linux-clusters.md)
3. Kopiowanie istniejących zadań, danych źródła i ujścia, do nowego środowiska. Zobacz [kopiowania danych do środowiska testowego](hdinsight-migrate-from-windows-to-linux.md#copy-data-to-the-test-environment) Aby uzyskać więcej informacji.
4. Należy przeprowadzić testy sprawdzania poprawności, aby upewnić się, że Twoje zadania działają zgodnie z oczekiwaniami w nowym klastrze.


Po upewnieniu się, że wszystko działa zgodnie z oczekiwaniami, należy zaplanować przestój w związku z migracją. Podczas tej awarii wykonaj następujące czynności:

1.  Utwórz kopię zapasową wszelkich danych przejściowych przechowywane lokalnie w węzłach klastra. Na przykład, jeśli masz dane zapisane bezpośrednio na węzła głównego.
2.  Usuń istniejący klaster.
3.  Tworzenie klastra w tej samej podsieci sieci Wirtualnej przy użyciu najnowszych (lub obsługiwanych) wersja usługi HDI przy użyciu tego samego magazynu danych domyślne używane poprzedniego klastra. Dzięki temu nowego klastra kontynuować pracę z istniejących danych produkcyjnych.
4.  Importowanie danych przejściowych, których kopię zapasową.
5.  Rozpoczęcie zadania/kontynuować przetwarzanie za pomocą nowego klastra.

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się, jak tworzyć klastry HDInsight opartych na systemie Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Nawiązać połączenie z HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Zarządzanie klastrem opartych na systemie Linux przy użyciu narzędzia Ambari](hdinsight-hadoop-manage-ambari.md)

