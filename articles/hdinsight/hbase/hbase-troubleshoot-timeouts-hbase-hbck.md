---
title: Limity czasu za pomocą polecenia "HBase hbck" w usłudze Azure HDInsight
description: Problem z limitem czasu podczas usuwania przypisań regionów przy użyciu polecenia "HBase hbck"
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/16/2019
ms.openlocfilehash: 941a710e4c3be3e93263bb63a60c3e0fbcfc4fc4
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69638396"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Scenariusz: Limity czasu za pomocą polecenia "HBase hbck" w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Napotkaj limity czasu `hbase hbck` przy użyciu polecenia przy rozwiązywaniu przypisań regionów.

## <a name="cause"></a>Przyczyna

Potencjalną przyczyną problemów z przekroczeniem limitu czasu `hbck` , gdy użycie polecenia może być w stanie "w przejściu" przez długi czas. Te regiony można zobaczyć jako offline w interfejsie użytkownika HBase Master. Ze względu na to, że duża liczba regionów próbuje przejść, HBase Master może przekroczyć limit czasu i nie można przywrócić tych regionów z powrotem do trybu online.

## <a name="resolution"></a>Rozwiązanie

1. Zaloguj się do klastra HBase usługi HDInsight przy użyciu protokołu SSH.

1. Uruchom `hbase zkcli` polecenie, aby nawiązać połączenie z powłoką Apache ZooKeeper.

1. Uruchom `rmr /hbase/regions-in-transition` polecenie `rmr /hbase-unsecure/regions-in-transition` lub.

1. Wyjdź z `hbase zkcli` powłoki przy użyciu `exit` polecenia.

1. W interfejsie użytkownika Apache Ambari Uruchom ponownie usługę Active HBase Master.

1. Uruchom polecenie `hbase hbck -fixAssignments`.

1. Aby upewnić się, że żaden region nie jest zablokowany HBase Master, należy go monitorować w sekcji "region w przejściu".

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

- Połącz się [@AzureSupport](https://twitter.com/azuresupport) z programem — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

- Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
