---
title: Problemy pulsu Apache Ambari w usłudze Azure HDInsight
description: Przegląd różnych powodów problemów pulsu Apache Ambari w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 09/11/2019
ms.openlocfilehash: 9f7d3fb5363a5eb2d79287bc9a9398bfdcbc4aec
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087821"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Problemy pulsu Apache Ambari w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="scenario-high-cpu-utilization"></a>Scenariusz: Wysokie wykorzystanie procesora CPU

### <a name="issue"></a>Problem

Agent Ambari ma duże użycie procesora CPU, które powoduje utratę alertów z interfejsu użytkownika Ambari, który dla niektórych węzłów zostaje utracony. Alert utracony pulsu jest zwykle przejściowy. 

### <a name="cause"></a>Przyczyna

Ze względu na różne błędy agenta Ambari, w rzadkich przypadkach, Agent Ambari — może mieć wysokie wykorzystanie procesora CPU (w pobliżu 100).

### <a name="resolution"></a>Rozwiązanie

1. Określ identyfikator procesu (PID) Ambari-Agent:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Następnie uruchom następujące polecenie, aby pokazać użycie procesora CPU:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Uruchom ponownie Ambari agenta, aby rozwiązać problem:

    ```bash
    service ambari-agent restart
    ```

1. Jeśli ponowne uruchomienie nie działa, należy skasować proces Ambari-Agent, a następnie uruchomić go:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Scenariusz: Agent Ambari nie został uruchomiony

### <a name="issue"></a>Problem

Nie uruchomiono agenta Ambari, który powoduje, że w przypadku niektórych węzłów zostanie utracony alert z interfejsu użytkownika Ambari.

### <a name="cause"></a>Przyczyna

Alerty są spowodowane przez agenta Ambari, który nie jest uruchomiony.

### <a name="resolution"></a>Rozwiązanie

1. Potwierdzenie stanu Ambari-Agent:

    ```bash
    service ambari-agent status
    ```

1. Potwierdź, że usługi kontrolera trybu failover są uruchomione:

    ```bash
    ps -ef | grep failover
    ```

    Jeśli usługi kontrolera trybu failover nie są uruchomione, prawdopodobnie z powodu problemu nie można zablokować uruchomienia kontrolera trybu failover z poziomu agenta usługi HDInsight. Sprawdź dziennik HDInsight-Agent z `/var/log/hdinsight-agent/hdinsight-agent.out` pliku.

---

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) za pomocą — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
