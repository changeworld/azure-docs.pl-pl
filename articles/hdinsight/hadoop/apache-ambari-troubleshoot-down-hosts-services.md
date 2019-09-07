---
title: Interfejs użytkownika Apache Ambari pokazuje hosty i usługi w usłudze Azure HDInsight
description: Rozwiązywanie problemów z interfejsem użytkownika Apache Ambari podczas wyświetlania hostów i usług w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/02/2019
ms.openlocfilehash: bc65387252fd425256684474f38ec5d783bb45e7
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734849"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Scenariusz: Interfejs użytkownika Apache Ambari pokazuje hosty i usługi w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Interfejs użytkownika Apache Ambari jest dostępny, ale w interfejsie użytkownika są widoczne niemal wszystkie usługi, a wszystkie hosty przedstawiające utratę pulsu.

## <a name="cause"></a>Przyczyna

W większości scenariuszy jest to problem z serwerem Ambari nieuruchomionym na aktywnym węzła głównego. Sprawdź, który węzła głównego jest aktywnym węzła głównego i upewnij się, że Ambari-serwer działa w jednym z nich. Nie uruchamiaj ręcznie programu Ambari-Server, zezwól usłudze kontrolera trybu failover na uruchomienie usługi Ambari-Server w prawym węzła głównego. Aby wymusić przejście w tryb failover, uruchom ponownie aktywny węzła głównego.

Problemy dotyczące sieci mogą również powodować ten problem. W każdym węźle klastra sprawdź, czy można wykonać polecenie `headnodehost`ping. Istnieje rzadki przypadek, w którym żaden węzeł klastra nie może połączyć `headnodehost`się z:

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Rozwiązanie

Zwykle ponowny rozruch aktywnego węzła głównego będzie wyeliminować ten problem. Jeśli nie, skontaktuj się z zespołem pomocy technicznej usługi HDInsight.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) za pomocą — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
