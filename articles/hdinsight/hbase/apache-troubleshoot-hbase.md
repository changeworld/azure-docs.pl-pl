---
title: Rozwiązywanie problemów z HBase za pomocą usługi Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące pracy z usługą HBase i usługą Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 595acdc46aca76a86038acebdb9a7581c51e3688
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69573943"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z programem Apache HBase przy użyciu usługi Azure HDInsight

Poznaj najważniejsze problemy i ich rozwiązania podczas pracy z ładunkiem HBase w usłudze Apache Ambari.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Jak mogę uruchomić raporty poleceń hbck z wieloma nieprzypisanymi regionami?

Typowym komunikatem o błędzie, który może zostać wyświetlony po `hbase hbck` uruchomieniu polecenia, jest "wiele regionów, które są nieprzypisane lub otwory w łańcuchu regionów".

W interfejsie użytkownika HBase Master można zobaczyć liczbę regionów, które są niezrównoważone na wszystkich serwerach regionów. Następnie można uruchomić `hbase hbck` polecenie, aby zobaczyć otwory w łańcuchu regionów.

Otwory mogą być powodowane przez regiony w trybie offline, więc najpierw należy usunąć przypisania. 

Aby przywrócić nieprzypisane regiony z powrotem do normalnego stanu, wykonaj następujące czynności:

1. Zaloguj się do klastra HBase usługi HDInsight przy użyciu protokołu SSH.
2. Aby nawiązać połączenie z powłoką Apache ZooKeeper, `hbase zkcli` Uruchom polecenie.
3. `rmr /hbase/regions-in-transition` Uruchom polecenie`rmr /hbase-unsecure/regions-in-transition` lub polecenie.
4. Aby wyjść z `hbase zkcli` powłoki, `exit` Użyj polecenia.
5. Otwórz interfejs użytkownika Apache Ambari, a następnie uruchom ponownie usługę Active HBase Master.
6. Uruchom ponownie `hbase hbck` polecenie (bez żadnych opcji). Sprawdź dane wyjściowe tego polecenia, aby upewnić się, że wszystkie regiony są przypisane.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Jak mogę rozwiązać problemy z limitem czasu podczas korzystania z poleceń hbck dla przypisań regionów?

### <a name="issue"></a>Problem

Potencjalną przyczyną problemów z przekroczeniem limitu czasu `hbck` , gdy użycie polecenia może być w stanie "w przejściu" przez długi czas. Te regiony można zobaczyć jako offline w interfejsie użytkownika HBase Master. Ze względu na to, że duża liczba regionów próbuje przejść, HBase Master może przekroczyć limitu czasu i nie może przywrócić tych regionów z powrotem do trybu online.

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Zaloguj się do klastra HBase usługi HDInsight przy użyciu protokołu SSH.
2. Aby nawiązać połączenie z powłoką Apache ZooKeeper, `hbase zkcli` Uruchom polecenie.
3. `rmr /hbase/regions-in-transition` Uruchom polecenie`rmr /hbase-unsecure/regions-in-transition` lub.
4. Aby wyjść `hbase zkcli` z powłoki, `exit` Użyj polecenia.
5. W interfejsie użytkownika Ambari Uruchom ponownie usługę Active HBase Master.
6. Ponownie uruchom `hbase hbck -fixAssignments` polecenie.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) z programem — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
