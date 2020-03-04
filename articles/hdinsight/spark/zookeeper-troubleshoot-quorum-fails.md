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
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250236"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Serwer Apache ZooKeeper nie może utworzyć kworum w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Serwer Apache ZooKeeper jest w złej kondycji, objawy mogą obejmować: Menedżer zasobów/węzły nazw są w trybie wstrzymania, proste operacje systemu plików HDFS nie działają, `zkFailoverController` jest zatrzymana i nie można go uruchomić, zadania przędzy/Spark/usługi Livy kończą się niepowodzeniem z powodu błędów dozorcy. Nie można uruchomić demonów LLAP w przypadku bezpiecznych klastrów Hive lub interaktywnych. Może zostać wyświetlony komunikat o błędzie podobny do:

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

W dzienniku serwera dozorcy na dowolnym hoście dozorcy w/var/log/Zookeeper/Zookeeper-Zookeeper-Server-\*. out może zostać wyświetlony następujący błąd:

```
2020-02-12 00:31:52,513 - ERROR [CommitProcessor:1:NIOServerCnxn@178] - Unexpected Exception:
java.nio.channels.CancelledKeyException
```

## <a name="cause"></a>Przyczyna

Gdy ilość plików migawek jest duża lub pliki migawek są uszkodzone, serwer dozorcy nie będzie mógł utworzyć kworum, co spowoduje złej kondycji usługi dozorcy. Serwer dozorcy nie usunie starych plików migawek z jego katalogu danych, ale jest to zadanie okresowe wykonywane przez użytkowników w celu utrzymania healthiness dozorcy. Aby uzyskać więcej informacji, zobacz [dozorcy siły i ograniczenia](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations).

## <a name="resolution"></a>Rozwiązanie

Sprawdź katalog danych dozorcy `/hadoop/zookeeper/version-2` i `/hadoop/hdinsight-zookeeper/version-2`, aby dowiedzieć się, czy rozmiar pliku migawek jest duży. Jeśli istnieją duże migawki, wykonaj następujące czynności:

1. Wykonaj kopię zapasową migawek w `/hadoop/zookeeper/version-2` i `/hadoop/hdinsight-zookeeper/version-2`.

1. Oczyść migawki w `/hadoop/zookeeper/version-2` i `/hadoop/hdinsight-zookeeper/version-2`.

1. Uruchom ponownie wszystkie serwery dozorcy z poziomu interfejsu użytkownika Apache Ambari.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

- Połącz się z [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego Microsoft Azure konta, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

- Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
