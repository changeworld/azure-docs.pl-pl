---
title: Apache Ambari UI pokazuje hosty i usługi w usłudze Azure HDInsight
description: Rozwiązywanie problemów z interfejsem użytkownika Apache Ambari, gdy pokazuje hosty i usługi w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 5340b1c7a6510595376789bc5777e6fb6f07dd4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895636"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Scenariusz: Apache Ambari UI pokazuje w dół hostów i usług w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Apache Ambari UI jest dostępny, ale interfejs użytkownika pokazuje, że prawie wszystkie usługi są w dół, wszystkie hosty pokazujące bicie serca utracone.

## <a name="cause"></a>Przyczyna

W większości scenariuszy jest to problem z serwerem Ambari nie działa na aktywnym headnode. Sprawdź, który headnode jest aktywnym headnode i upewnij się, że twój ambari-serwer działa po prawej stronie. Nie uruchamiaj ręcznie ambari-server, niech usługa kontrolera trybu failover będzie odpowiedzialna za uruchomienie ambari-server na prawym headnode. Uruchom ponownie aktywny plik główny, aby wymusić tryb failover.

Przyczyną tego problemu mogą być również problemy z siecią. Z każdego węzła klastra sprawdź, czy można pingować `headnodehost`. Występuje rzadka sytuacja, w której `headnodehost`żaden węzeł klastra nie może się połączyć z:

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Rozwiązanie

Zwykle ponowne uruchomienie aktywnego headnode złagodzi ten problem. Jeśli nie, skontaktuj się z zespołem pomocy technicznej HDInsight.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
