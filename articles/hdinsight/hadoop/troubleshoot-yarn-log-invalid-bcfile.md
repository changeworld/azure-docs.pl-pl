---
title: Nie można odczytać dziennika usługi Apache przędzy w usłudze Azure HDInsight
description: Kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas pracy z klastrami usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776198"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Scenariusz: nie można odczytać dziennika usługi Apache przędz w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Dzienniki przędzy usługi Apache, które znajdują się na koncie magazynu, nie można odczytać przez człowieka. Analizator plików nie działa i generuje następujący komunikat o błędzie:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Przyczyna

Dziennik przędzy Apache jest agregowany do formatu `IndexFile`, który nie jest obsługiwany przez parser plików.

## <a name="resolution"></a>Rozdzielczość

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net`, gdzie `CLUSTERNAME` jest nazwą klastra.

1. W interfejsie użytkownika Ambari przejdź do lokalizacji ** > konfiguracje** > **zaawansowanej** > **zaawansowanej przędzy**.

1. W przypadku magazynu WASB: wartość domyślna dla `yarn.log-aggregation.file-formats` jest `IndexedFormat,TFile`. Zmień wartość na `TFile`.

1. W przypadku magazynu ADLS: wartość domyślna dla `yarn.nodemanager.log-aggregation.compression-type` jest `gz`. Zmień wartość na `none`.

1. Zapisz zmiany i uruchom ponownie wszystkie usługi, których to dotyczy.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego Microsoft Azure konta, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
