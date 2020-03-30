---
title: Błąd interfejsu użytkownika Apache Ambari 502 w usłudze Azure HDInsight
description: Błąd interfejsu użytkownika Apache Ambari 502 podczas próby uzyskania dostępu do klastra usługi Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 2b17c2488e47148e8845433f9c7613e1127fbffa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895753"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>Scenariusz: Błąd interfejsu użytkownika apache Ambari 502 w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Podczas próby uzyskania dostępu do interfejsu użytkownika Apache Ambari dla klastra HDInsight zostanie wyświetlony komunikat podobny do: "502 - Serwer sieci Web otrzymał nieprawidłową odpowiedź, działając jako brama lub serwer proxy".

## <a name="cause"></a>Przyczyna

Ogólnie rzecz biorąc, kod stanu HTTP 502 oznacza, że serwer Ambari nie działa poprawnie na aktywnym pliku headnode. Istnieje kilka możliwych przyczyn.

## <a name="resolution"></a>Rozwiązanie

W większości przypadków, aby rozwiązać problem, można ponownie uruchomić aktywny headnode. Możesz też wybrać większy rozmiar maszyny Wirtualnej dla swojego headnode.

### <a name="ambari-server-failed-to-start"></a>Nie można uruchomić serwera Ambari

Możesz sprawdzić dzienniki ambari-server, aby dowiedzieć się, dlaczego serwer Ambari nie mógł się uruchomić. Częstym powodem jest błąd sprawdzania spójności bazy danych. Można to znaleźć w tym `/var/log/ambari-server/ambari-server-check-database.log`pliku dziennika: .

Jeśli wprowadzono jakiekolwiek zmiany w węźle klastra, cofnij je. Zawsze używaj interfejsu użytkownika Ambari do modyfikowania wszelkich konfiguracji związanych z usługą Hadoop/Spark.

### <a name="ambari-server-taking-100-cpu-utilization"></a>Serwer Ambari biorąc 100% wykorzystanie procesora

W rzadkich sytuacjach widzieliśmy, że proces serwera ambari ma prawie 100% wykorzystania procesora stale. Jako ograniczenie, można ssh do aktywnego headnode i zabić proces serwera Ambari i uruchomić go ponownie.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Serwer Ambari zabity przez oom-killer

W niektórych scenariuszach, twój headnode zabraknie pamięci, a Linux oom-killer zaczyna wybierać procesy zabić. Tę sytuację można zweryfikować, przeszukując identyfikator procesu AmbariServer, którego nie należy znaleźć. Następnie spójrz `/var/log/syslog`na swoje , i poszukaj czegoś takiego:

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

Następnie określ, które procesy biorą wspomnienia i spróbuj dalej główną przyczynę.

### <a name="other-issues-with-ambari-server"></a>Inne problemy z serwerem Ambari

Rzadko serwer Ambari nie może obsłużyć żądania przychodzącego, można znaleźć więcej informacji, patrząc na dzienniki ambari-server dla każdego błędu. Jednym z takich przypadków jest błąd taki jak ten:

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
