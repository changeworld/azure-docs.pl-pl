---
title: Lokalny system plików HDFS zablokowany w trybie awaryjnym w klastrze usługi Azure HDInsight
description: Lokalny system plików HDFS zablokowany w trybie awaryjnym w klastrze usługi Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/02/2019
ms.openlocfilehash: 2baef0d8c23a8da13c3640d85159dc17b3acc80f
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68817162"
---
# <a name="scenario-local-hdfs-stuck-in-safe-mode-on-azure-hdinsight-cluster"></a>Scenariusz: Lokalny system plików HDFS zablokowany w trybie awaryjnym w klastrze usługi Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Lokalny system plików HDFS zablokowany w trybie awaryjnym w klastrze usługi Azure HDInsight. Zostanie wyświetlony komunikat o błędzie podobny do poniższego:

```
hdiuser@hn0-spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
...
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

## <a name="cause"></a>Przyczyna

Klaster usługi HDInsight został przeskalowany w dół do bardzo kilku węzłów poniżej lub w pobliżu współczynnika replikacji systemu plików HDFS.

## <a name="resolution"></a>Rozwiązanie

1. Zgłoś stan systemu plików HDFS w klastrze usługi HDInsight przy użyciu następującego polecenia:

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
    ```

1. Sprawdź integralność systemu plików HDFS w klastrze usługi HDInsight przy użyciu następującego polecenia:

    ```bash
    hdiuser@hn0-spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
    ```

1. W przypadku określenia braku, uszkodzenia lub w replikowanych blokach lub można zignorować te bloki, uruchom następujące polecenie, aby przełączyć węzeł nazwy z trybu awaryjnego:

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
    ```

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) za pomocą — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
