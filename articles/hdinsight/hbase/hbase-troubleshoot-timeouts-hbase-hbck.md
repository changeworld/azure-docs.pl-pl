---
title: Limity czasu za pomocą polecenia "HBase hbck" w usłudze Azure HDInsight
description: Problem z limitem czasu podczas usuwania przypisań regionów przy użyciu polecenia "HBase hbck"
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/01/2019
ms.openlocfilehash: 4f3f1c22fa1dc05a66a8b6bf0179903a44cef9b6
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737929"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Scenariusz: Limity czasu za pomocą polecenia "HBase hbck" w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Napotkaj limity czasu `hbase hbck` przy użyciu polecenia przy rozwiązywaniu przypisań regionów.

## <a name="cause"></a>Przyczyna

Potencjalną przyczyną może być kilka regionów w stanie "w przejściu" przez długi czas. Regiony te mogą być widoczne jako offline z poziomu interfejsu użytkownika Apache HBase Master. Ze względu na dużą liczbę regionów, które próbują przejść, HBase Master może przekroczyć limit czasu i nie będzie można przywrócić tych regionów z powrotem do stanu online.

## <a name="resolution"></a>Rozwiązanie

1. Zaloguj się do klastra usługi HDInsight HBase przy użyciu protokołu SSH.

1. Uruchom `hbase zkcli` polecenie, aby nawiązać połączenie z powłoką dozorcy.

1. Uruchom `rmr /hbase/regions-in-transition` polecenie `rmr /hbase-unsecure/regions-in-transition` lub.

1. Wyjdź z `hbase zkcli` powłoki przy użyciu `exit` polecenia.

1. Otwórz interfejs użytkownika Ambari i uruchom ponownie usługę Active HBase Master z poziomu usługi Ambari.

1. Aby upewnić się, że żaden region nie jest zablokowany HBase Master, należy go monitorować w sekcji "region w przejściu".

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) za pomocą — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
