---
title: Nie można odczytać dziennika usługi Apache Yarn w usłudze Azure HDInsight
description: Rozwiązywanie problemów z instrukcjami i możliwymi rozwiązaniami problemów podczas interakcji z klastrami usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776198"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Scenariusz: Nie można odczytać dziennika przędzy Apache w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Dzienniki Przędza Apache znalezione z konta pamięci masowej nie są czytelne dla człowieka. Analizator plików nie działa i generuje następujący komunikat o błędzie:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Przyczyna

Dziennik Przędza Apache jest agregowany do `IndexFile` formatu, który nie jest obsługiwany przez analizator plików.

## <a name="resolution"></a>Rozwiązanie

1. W przeglądarce internetowej `https://CLUSTERNAME.azurehdinsight.net`przejdź `CLUSTERNAME` do miejsca , gdzie jest nazwa klastra.

1. Z interfejsu użytkownika Ambari przejdź do witryny **YARN** > **Configs** > **Advanced** > **YARN Advanced yarn-site**.

1. Dla magazynu WASB: Domyślną wartością `yarn.log-aggregation.file-formats` jest `IndexedFormat,TFile`. Zmień wartość `TFile`na .

1. W przypadku magazynu ADLS: `yarn.nodemanager.log-aggregation.compression-type` `gz`wartością domyślną jest . Zmień wartość `none`na .

1. Zapisz zmianę i uruchom ponownie wszystkie usługi, których dotyczy problem.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
