---
title: BindException — adres już używany w usłudze Azure HDInsight
description: BindException — adres już używany w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 80f984643d6d8be88b381881c6fc1cb1cb5f1815
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887346"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Scenariusz: BindException — adres już używany w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Nie można ukończyć operacji ponownego uruchamiania na serwerze regionu Apache HBase. Z `region-server.log` katalogu `/var/log/hbase` w węzłach procesu roboczego, w których uruchamianie serwera regionu kończy się niepowodzeniem, może zostać wyświetlony komunikat o błędzie podobny w następujący sposób:

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Przyczyna

Ponowne uruchamianie serwerów regionu Apache HBase podczas intensywnej aktywności obciążenia. Poniżej znajduje się to, co dzieje się za kulisami, gdy użytkownik inicjuje operację ponownego uruchamiania na serwerze regionu HBase z interfejsu apache Ambari:

1. Agent Ambari wysyła żądanie zatrzymania do serwera regionu.

1. Agent Ambari czeka 30 sekund na serwer regionu, aby zamknąć bezpiecznie

1. Jeśli aplikacja nadal łączy się z serwerem regionu, serwer nie zostanie natychmiast zamknięty. 30-sekundowy limit czasu wygasa przed zamknięciem.

1. Po 30 sekundach agent Ambari wysyła`kill -9`polecenie force-kill ( ) do serwera regionu.

1. Z powodu tego nagłego zamknięcia, chociaż proces serwera regionu zostaje zabity, port związany z `AddressBindException`procesem może nie zostać zwolniony, co ostatecznie prowadzi do .

## <a name="resolution"></a>Rozwiązanie

Zmniejsz obciążenie serwerów regionu HBase przed zainicjowaniem ponownego uruchomienia. Ponadto, to dobry pomysł, aby najpierw opróżnić wszystkie tabele. Aby uzyskać odwołanie dotyczące opróżniania tabel, zobacz [HDInsight HBase: Jak poprawić czas ponownego uruchamiania klastra Apache HBase przez opróżnianie tabel](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Alternatywnie spróbuj ręcznie ponownie uruchomić serwery regionu w węzłach procesu roboczego za pomocą następujących poleceń:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
