---
title: Problemy z pulsem usługi Apache Ambari w usłudze Azure HDInsight
description: Przegląd różnych przyczyn problemów zbiciem serca Apache Ambari w usłudze Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: ab88f65d535be2aef5f0b26fa1171c03276466e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057077"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Problemy z pulsem usługi Apache Ambari w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="scenario-high-cpu-utilization"></a>Scenariusz: Wysokie wykorzystanie procesora

### <a name="issue"></a>Problem

Agent Ambari ma wysokie wykorzystanie procesora CPU, co powoduje alerty z interfejsu użytkownika Ambari, że dla niektórych węzłów pulsu agenta Ambari jest tracona. Bicie serca utracone alert jest zwykle przemijające.

### <a name="cause"></a>Przyczyna

Ze względu na różne błędy ambari-agent, w rzadkich przypadkach, twój ambari-agent może mieć wysokie (blisko 100) procent wykorzystania procesora.

### <a name="resolution"></a>Rozwiązanie

1. Identyfikowanie identyfikatora procesu (pid) ambari-agenta:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Następnie uruchom następujące polecenie, aby pokazać użycie procesora CPU:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Uruchom ponownie ambari-agent, aby złagodzić problem:

    ```bash
    service ambari-agent restart
    ```

1. Jeśli ponowne uruchomienie nie zadziała, zabić proces ambari-agent, a następnie uruchom go:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Scenariusz: Agent Ambari nie został uruchomiony

### <a name="issue"></a>Problem

Agent Ambari nie rozpoczął, co powoduje alerty z interfejsu użytkownika Ambari, że dla niektórych węzłów pulsu agenta Ambari jest tracona.

### <a name="cause"></a>Przyczyna

Alerty są spowodowane przez agenta Ambari nie działa.

### <a name="resolution"></a>Rozwiązanie

1. Potwierdź status ambari-agenta:

    ```bash
    service ambari-agent status
    ```

1. Sprawdź, czy usługi kontrolera trybu failover są uruchomione:

    ```bash
    ps -ef | grep failover
    ```

    Jeśli usługi kontrolera trybu failover nie są uruchomione, prawdopodobnie z powodu problemu uniemożliwić hdinsight-agent z uruchamiania kontrolera trybu failover. Sprawdź dziennik hdinsight-agent z `/var/log/hdinsight-agent/hdinsight-agent.out` pliku.

## <a name="scenario-heartbeat-lost-for-ambari"></a>Scenariusz: Bicie serca utracone dla Ambari

### <a name="issue"></a>Problem

Ambari bicie serca agent został utracony.

### <a name="cause"></a>Przyczyna

Dzienniki oms powodują wysokie wykorzystanie procesora CPU.

### <a name="resolution"></a>Rozwiązanie

* Wyłącz rejestrowanie usługi Azure Monitor przy użyciu polecenia cmdlet programu PowerShell [disable-AzHDInsightMonitoring.](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring)
* Usuwanie `mdsd.warn` pliku dziennika

---

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
