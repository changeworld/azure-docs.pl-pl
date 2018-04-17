---
title: Uaktualnienia klastra usługi HDInsight do nowszej wersji-Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uaktualnić klaster usługi HDInsight do nowszej wersji.
services: hdinsight
documentationcenter: ''
author: bhanupr
manager: asadk
editor: bhanupr
ms.assetid: 60eb573c-e639-4815-9fc6-ea8b106d8dbc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: bhanupr
ms.openlocfilehash: d6cd0dd8b326f5340e5014bba4babc6dd945744d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>Uaktualnij do nowszej wersji klastra usługi HDInsight
Aby móc korzystać z najnowszych funkcji usługi HDInsight, zaleca się, że klastry usługi HDInsight można uaktualnić do najnowszej wersji. Postępuj zgodnie z poniższych wytycznych, aby uaktualnić Twoje HDInsight cluster wersji.

> [!NOTE]
> Klastry usługi HDInsight w wersji 3.2 lub 3.3 zbliża się Data wycofania. Aby uzyskać informacji na temat obsługiwanych wersji usługi hdinsight, zobacz [wersji składnika usługi HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).
>
>

## <a name="upgrade-tasks"></a>Zadania uaktualniania
Poniżej przedstawiono przepływ pracy do uaktualnienia klastra usługi HDInsight.

![Diagram przepływu pracy uaktualniania](./media/hdinsight-upgrade-cluster/upgrade-workflow.png)

1. Przeczytaj każdej sekcji tego dokumentu, aby zrozumieć zmiany, które mogą być wymagane w przypadku uaktualniania z klastrem usługi HDInsight.
2. Tworzenie klastra jako środowiska gwarancji testu/jakości. Aby uzyskać więcej informacji na temat tworzenia klastra, zobacz [informacje o sposobie tworzenia klastrów usługi HDInsight opartej na systemie Linux](hdinsight-hadoop-provision-linux-clusters.md)
3. Skopiuj zadania istniejące źródła danych i wychwytywanie do nowego środowiska. Zobacz [kopiowania danych do środowiska testowego](hdinsight-migrate-from-windows-to-linux.md#copy-data-to-the-test-environment) więcej szczegółów.
4. Należy przeprowadzić testy weryfikacji, aby upewnić się, że zadaniach działać zgodnie z oczekiwaniami w nowym klastrze.


Po upewnieniu się, że wszystko działa zgodnie z oczekiwaniami, należy zaplanować przestój do migracji. Podczas tego przestojów wykonaj następujące czynności:

1.  Utwórz kopię zapasową przejściowej danych przechowywanych lokalnie w węzłach klastra. Jeśli na przykład dane przechowywane bezpośrednio na węzła głównego.
2.  Usuwanie istniejącego klastra.
3.  Tworzenia klastra w tej samej podsieci sieci Wirtualnej z najnowszą (lub obsługiwanych) wersja usługi HDI przy użyciu tego samego magazynu danych domyślne używane poprzedniego klastra. Dzięki temu nowego klastra kontynuować pracę z istniejących danych produkcyjnych.
4.  Importuj wszystkie przejściowej dane kopii zapasowej.
5.  Uruchom zadania/Kontynuuj przetwarzanie przy użyciu nowego klastra.

## <a name="next-steps"></a>Następne kroki
* [Informacje o sposobie tworzenia klastrów usługi HDInsight opartej na systemie Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Połączenie do usługi HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Zarządzanie klastrem opartych na systemie Linux przy użyciu narzędzia Ambari](hdinsight-hadoop-manage-ambari.md)

