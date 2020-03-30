---
title: Lokalny system plików HDFS zablokowany w trybie awaryjnym w klastrze usługi Azure HDInsight
description: Rozwiązywanie problemów z lokalnym interfejsem API apache zablokowanym w trybie awaryjnym w klastrze Apache w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: 4d19a05129970b26ca1af20263fbfe93a0053c7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894190"
---
# <a name="scenario-local-hdfs-stuck-in-safe-mode-on-azure-hdinsight-cluster"></a>Scenariusz: Lokalny system plików HDFS zablokowany w trybie awaryjnym w klastrze usługi Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Lokalny apache Hadoop Distributed File System (HDFS) utknął w trybie awaryjnym w klastrze HDInsight. Zostanie wyświetlony komunikat o błędzie podobny w następujący sposób:

```output
hdiuser@spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
...
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

## <a name="cause"></a>Przyczyna

Klaster HDInsight został przeskalowany do bardzo niewielu węzłów poniżej lub liczba węzłów jest zbliżona do współczynnika replikacji hdfs.

## <a name="resolution"></a>Rozwiązanie

1. Raport o stanie usługi HDFS w klastrze HDInsight za pomocą następującego polecenia:

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
    ```

1. Sprawdź integralność usługi HDFS w klastrze HDInsight za pomocą następującego polecenia:

    ```bash
    hdiuser@spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
    ```

1. Jeśli zostanie ustalona, że nie brakuje, uszkodzony lub w obszarze zreplikowanych bloków lub te bloki mogą być ignorowane uruchom następujące polecenie, aby wyjąć węzeł nazwy z trybu awaryjnego:

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
    ```

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
