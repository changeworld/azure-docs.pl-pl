---
title: Serwer Apache ZooKeeper nie może utworzyć kworum w usłudze Azure HDInsight
description: Serwer Apache ZooKeeper nie może utworzyć kworum w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 4e46efaf17ae9bad5df6f1f61f401d3e6de58a85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250236"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Serwer Apache ZooKeeper nie może utworzyć kworum w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Serwer Apache ZooKeeper jest w złej kondycji, objawy mogą obejmować: oba menedżery zasobów/węzły nazw `zkFailoverController` są w trybie gotowości, proste operacje HDFS nie działają, jest zatrzymywane i nie można ich uruchomić, zadania Yarn/Spark/Livy nie działają z powodu błędów Zookeeper. Demony LLAP mogą również nie zostać uruchomiony na platformie Secure Spark lub Interactive Hive klastrów. Może zostać wyświetlony komunikat o błędzie podobny do:

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

W dzienniku serwera Zookeeper na dowolnym hoście Zookeeper w /var/log/zookeeper/zookeeper-zookeeper-server-\*.out może również pojawić się następujący błąd:

```
2020-02-12 00:31:52,513 - ERROR [CommitProcessor:1:NIOServerCnxn@178] - Unexpected Exception:
java.nio.channels.CancelledKeyException
```

## <a name="cause"></a>Przyczyna

Gdy ilość plików migawek jest duża lub pliki migawki są uszkodzone, serwer ZooKeeper nie może utworzyć kworum, co powoduje, że usługi związane z ZooKeeper w złej kondycji. Serwer ZooKeeper nie usunie starych plików migawek ze swojego katalogu danych, zamiast tego jest to okresowe zadanie, które mają być wykonywane przez użytkowników w celu utrzymania kondycji ZooKeeper. Aby uzyskać więcej informacji, zobacz [Zalety i ograniczenia ZooKeeper](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations).

## <a name="resolution"></a>Rozwiązanie

Sprawdź katalog danych `/hadoop/zookeeper/version-2` ZooKeeper i `/hadoop/hdinsight-zookeeper/version-2` dowiedzieć się, czy rozmiar pliku migawek jest duży. Jeśli istnieją duże migawki, należy wykonać następujące czynności:

1. Tworzenie kopii zapasowych migawek w `/hadoop/zookeeper/version-2` i `/hadoop/hdinsight-zookeeper/version-2`.

1. Wyczyść `/hadoop/zookeeper/version-2` migawki w i `/hadoop/hdinsight-zookeeper/version-2`.

1. Uruchom ponownie wszystkie serwery ZooKeeper z interfejsu apache Ambari.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

- Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

- Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
