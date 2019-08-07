---
title: Usługa Apache HBase nie odpowiada na żądania w usłudze Azure HDInsight
description: Rozwiązywanie problemu z HBase usługi HDInsight nie odpowiada na żądania
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/01/2019
ms.openlocfilehash: 7219f66457e47bba34e750ec74810b8d2edee36e
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68817110"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Scenariusz: Usługa Apache HBase nie odpowiada na żądania w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Usługa Apache HBase REST nie odpowiada na żądania w usłudze Azure HDInsight.

## <a name="cause"></a>Przyczyna

Przyczyną może być to, że w tym przypadku usługa Apache HBase REST jest przyczyną przecieków gniazd, które są szczególnie powszechne, gdy usługa była uruchomiona przez długi czas (na przykład miesiące). W zestawie SDK klienta może zostać wyświetlony komunikat o błędzie podobny do:

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Rozwiązanie

Uruchom ponownie HBase REST przy użyciu poniższego polecenia po SSHing do hosta. Możesz również użyć akcji skryptu, aby ponownie uruchomić tę usługę na wszystkich węzłach procesu roboczego:

```bash
sudo service hdinsight-hbrest restart
```

To polecenie spowoduje zatrzymanie serwera regionu HBase na tym samym hoście. Można ręcznie uruchomić serwer regionu HBase za pomocą Ambari lub pozwolić Ambari funkcji automatycznego ponownego uruchamiania, aby przywrócić automatyczne odzyskiwanie serwera regionu HBase.

Jeśli problem nadal występuje, można zainstalować następujący skrypt zaradczy jako zadanie firmy CRONUS, które jest uruchamiane co 5 minut na każdym węźle procesu roboczego. Ten skrypt zaradczy wysyła polecenie ping do usługi REST i uruchamia ją ponownie na wypadek, gdyby usługa REST nie odpowiadała.

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

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) za pomocą — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
