---
title: Problemy z serwerami regionów w usłudze Azure HDInsight
description: Problemy z serwerami regionów w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/07/2019
ms.openlocfilehash: e75f2fdd0530b92e8c8405b74c2a364ff9e9e28e
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935434"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Problemy z serwerami regionów w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="scenario-unassigned-regions"></a>Scenariusz: Nieprzypisane regiony

### <a name="issue"></a>Problem

Po uruchomieniu `hbase hbck` polecenia zostanie wyświetlony komunikat o błędzie podobny do:

```
multiple regions being unassigned or holes in the chain of regions
```

Z poziomu interfejsu użytkownika Apache HBase Master można zauważyć, że liczba regionów jest niezrównoważona na wszystkich serwerach regionów.

### <a name="cause"></a>Przyczyna

Dziury mogą być wynikiem regionów trybu offline.

### <a name="resolution"></a>Rozwiązanie

Napraw przydziały. Wykonaj poniższe kroki, aby przywrócić stan Normalny nieprzypisanych regionów:

1. Zaloguj się do klastra usługi HDInsight HBase przy użyciu protokołu SSH.

1. Uruchom `hbase zkcli` polecenie, aby nawiązać połączenie z powłoką dozorcy.

1. Uruchom `rmr /hbase/regions-in-transition` polecenie `rmr /hbase-unsecure/regions-in-transition` lub.

1. Zamknij powłokę dozorcy za `exit` pomocą polecenia.

1. Otwórz interfejs użytkownika Ambari i uruchom ponownie usługę Active HBase Master z poziomu usługi Ambari.

1. Uruchom `hbase hbck` ponownie polecenie (bez żadnych dalszych opcji). Sprawdź dane wyjściowe i upewnij się, że wszystkie regiony są przypisane.

---

## <a name="scenario-dead-region-servers"></a>Scenariusz: Uszkodzone serwery regionów

### <a name="issue"></a>Problem

Nie można uruchomić serwerów regionów.

### <a name="cause"></a>Przyczyna

Dzielenie wielu katalogów WAL.

1. Pobierz listę bieżących Wals: `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`.

1. `wals.out` Sprawdź plik. W przypadku zbyt wielu katalogów dzielenia (począwszy od znaku *-dzielenia) serwer regionu prawdopodobnie kończy się niepowodzeniem z powodu tych katalogów.

### <a name="resolution"></a>Rozwiązanie

1. Zatrzymaj HBase z portalu Ambari.

1. Wykonaj `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` , aby uzyskać świeżą listę WALs.

1. Przenieś katalogi *-dzielenie do folderu tymczasowego, `splitWAL`a następnie usuń katalogi *-dzielenie.

1. Wykonaj `hbase zkcli` polecenie, aby nawiązać połączenie z powłoką dozorcy.

1. Wykonaj `rmr /hbase-unsecure/splitWAL`.

1. Uruchom ponownie usługę HBase.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) z programem — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
