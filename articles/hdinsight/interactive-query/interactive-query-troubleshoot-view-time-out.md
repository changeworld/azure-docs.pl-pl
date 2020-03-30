---
title: Przekroczeniu czasu wyświetlania gałęzi apache z wyniku kwerendy — Usługa Azure HDInsight
description: Przekroczenie czasu wyświetlania gałęzi apache podczas pobierania wyniku kwerendy w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: f00f70e674ac0b83b737d6b2a4bf9d20400736fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672025"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>Scenariusz: Przekroczenie czasu wyświetlania gałęzi apache podczas pobierania wyniku kwerendy w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas korzystania ze składników zapytania interaktywnego w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Podczas uruchamiania niektórych zapytań z widoku Gałąź Apache może wystąpić następujący błąd:

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>Przyczyna

Wartość limitu czasu domyślnego widoku hive może nie być odpowiednia dla uruchomionej kwerendy. Określony okres czasu jest zbyt krótki dla widoku gałęzi, aby pobrać wynik kwerendy.

## <a name="resolution"></a>Rozwiązanie

Zwiększ limity czasu widoku gałęzi Apache Ambari, ustawiając następujące właściwości w pliku `/etc/ambari-server/conf/ambari.properties`.

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

Wartość `HIVE_VIEW_INSTANCE_NAME` jest dostępna na końcu adresu URL widoku gałęzi.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
