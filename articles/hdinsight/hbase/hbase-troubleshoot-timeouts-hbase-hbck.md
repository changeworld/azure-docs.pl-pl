---
title: Przekraczanie limitów czasu polecenia „hbase hbck” w usłudze Azure HDInsight
description: Problem z limitem czasu z poleceniem "hbase hbck" podczas ustawiania przypisań regionów
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 5604b42e1611830f3aaea9ae180cdb8142ab0942
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887193"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Scenariusz: Limity czasu z poleceniem 'hbase hbck' w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Napotkaj `hbase hbck` limity czasu z poleceniem podczas naprawiania przypisań regionu.

## <a name="cause"></a>Przyczyna

Potencjalną przyczyną problemów z limitem `hbck` czasu podczas korzystania z polecenia może być to, że kilka regionów jest w stanie "w okresie przejściowym" przez długi czas. Te regiony można zobaczyć w trybie offline w interfejsie użytkownika wzorca bazy danych. Ponieważ duża liczba regionów próbuje przejść, HBase Master może limit czasu i nie może przenieść tych regionów z powrotem do trybu online.

## <a name="resolution"></a>Rozwiązanie

1. Zaloguj się do klastra HDInsight HBase przy użyciu funkcji SSH.

1. Uruchom `hbase zkcli` polecenie, aby połączyć się z powłoką Apache ZooKeeper.

1. Uruchom `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` lub polecenie.

1. Wyjdź `hbase zkcli` `exit` z powłoki za pomocą polecenia.

1. Z interfejsu apache Ambari Uruchom ponownie usługę Active HBase Master.

1. Uruchom polecenie `hbase hbck -fixAssignments`.

1. Monitoruj interfejs użytkownika wzorca bazy danych HBase "region w okresie przejściowym", aby upewnić się, że żaden region nie utknie.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

- Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

- Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
