---
title: Problemy z serwerami regionów w usłudze Azure HDInsight
description: Problemy z serwerami regionów w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 85aeafb2c4461b50d399e40d9abff2ac04b677c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272762"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Problemy z serwerami regionów w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="scenario-unassigned-regions"></a>Scenariusz: Nieprzypisane regiony

### <a name="issue"></a>Problem

Podczas `hbase hbck` uruchamiania polecenia pojawia się komunikat o błędzie podobny do:

```
multiple regions being unassigned or holes in the chain of regions
```

W interfejsie użytkownika interfejsu apache HBase Master można zobaczyć liczbę regionów, które są niezrównoważone na wszystkich serwerach regionu. Następnie można uruchomić `hbase hbck` polecenie, aby zobaczyć otwory w łańcuchu regionu.

### <a name="cause"></a>Przyczyna

Otwory mogą być wynikiem regionów offline.

### <a name="resolution"></a>Rozwiązanie

Napraw przypisania. Wykonaj poniższe czynności, aby przywrócić stan normalny nieprzydzielone regiony:

1. Zaloguj się do klastra HDInsight HBase przy użyciu funkcji SSH.

1. Uruchom `hbase zkcli` polecenie, aby połączyć się z powłoką ZooKeeper.

1. Uruchom `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` lub polecenie.

1. Wyjdź z `exit` powłoki zookeeper za pomocą polecenia.

1. Otwórz interfejs apache Ambari, a następnie uruchom ponownie usługę Active HBase Master.

1. Uruchom `hbase hbck` polecenie ponownie (bez żadnych dalszych opcji). Sprawdź dane wyjściowe i upewnij się, że wszystkie regiony są przypisane.

---

## <a name="scenario-dead-region-servers"></a>Scenariusz: Serwery regionów martwych

### <a name="issue"></a>Problem

Nie można uruchomić serwerów regionu.

### <a name="cause"></a>Przyczyna

Wiele katalogów WAL podziału.

1. Pobierz listę bieżących WALs: `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`.

1. Sprawdź `wals.out` plik. Jeśli istnieje zbyt wiele katalogów podziału (począwszy od *-dzielenie), serwer regionu prawdopodobnie kończy się niepowodzeniem z powodu tych katalogów.

### <a name="resolution"></a>Rozwiązanie

1. Zatrzymaj HBase z portalu Ambari.

1. Wykonaj, `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` aby uzyskać nową listę WALs.

1. Przenieś katalogi *-split do folderu `splitWAL`tymczasowego i usuń katalogi *-splitting.

1. Wykonaj `hbase zkcli` polecenie, aby połączyć się z powłoką zookeeper.

1. Wykonaj `rmr /hbase-unsecure/splitWAL`plik .

1. Uruchom ponownie usługę HBase.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
