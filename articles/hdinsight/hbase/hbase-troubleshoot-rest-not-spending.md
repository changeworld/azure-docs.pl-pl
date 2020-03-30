---
title: Apache HBase REST nie odpowiada na żądania w usłudze Azure HDInsight
description: Rozwiąż problem z apache HBase REST nie odpowiada na żądania w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 49b547829a369ea6df35e2f1c2f7d60458e41040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887176"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Scenariusz: Apache HBase REST nie odpowiada na żądania w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Usługa Apache HBase REST nie odpowiada na żądania w usłudze Azure HDInsight.

## <a name="cause"></a>Przyczyna

Możliwą przyczyną w tym miejscu może być Usługa Apache HBase REST jest przeciekanie gniazd, co jest szczególnie powszechne, gdy usługa działa przez długi czas (na przykład miesiące). W kole SDK klienta może zostać wyświetlony komunikat o błędzie podobny do:

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Rozwiązanie

Uruchom ponownie usługę HBase REST, używając poniższego polecenia po SSHing do hosta. Można również użyć akcji skryptu, aby ponownie uruchomić tę usługę we wszystkich węzłach procesu roboczego:

```bash
sudo service hdinsight-hbrest restart
```

To polecenie spowoduje zatrzymanie serwera regionu HBase na tym samym hoście. Serwer regionu HBase można uruchomić ręcznie za pośrednictwem programu Ambari lub umożliwić automatyczne ponowne uruchomienie funkcji Ambari w celu automatycznego odzyskania serwera regionu HBase.

Jeśli problem nadal występuje, można zainstalować następujący skrypt ograniczający zagrożenie jako zadanie CRON, które jest uruchamiane co 5 minut w każdym węźle procesu roboczego. Ten skrypt łagodzenia pinguje usługi REST i uruchamia ją ponownie w przypadku, gdy usługa REST nie odpowiada.

```bash
#!/bin/bash
nc localhost 8090 < /dev/null
if [ $? -ne 0 ]
    then
    echo "RESTServer is not responding. Restarting"
    sudo /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh restart rest
fi
```

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
