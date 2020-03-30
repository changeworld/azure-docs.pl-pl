---
title: IllegalArgumentException error for Apache Spark - Azure HDInsight
description: IllegalArgumentException for Apache Spark activity in Azure HDInsight for Azure Data Factory
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: df62dbd8db7d41eb11207c7741aed76cec0ac7a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894376"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Scenariusz: IllegalArgumentException for Apache Spark activity in Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas korzystania ze składników Platformy Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Podczas próby wykonania działania platformy Spark w potoku usługi Azure Data Factory zostanie wyświetlony następujący wyjątek:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Przyczyna

Zadanie platformy Spark zakończy się niepowodzeniem, jeśli plik jar aplikacji nie znajduje się w domyślnym/podstawowym magazynie klastra Platformy Spark.

Jest to znany problem z platformą open source Platformy Spark śledzony w tym błędzie: [Zadanie iskry kończy się niepowodzeniem, jeśli fs.defaultFS i jar aplikacji są różne adresy URL](https://issues.apache.org/jira/browse/SPARK-22587).

Ten problem został rozwiązany w spark 2.3.0.

## <a name="resolution"></a>Rozwiązanie

Upewnij się, że słoik aplikacji jest przechowywany w domyślnej/podstawowej pamięci masowej dla klastra HDInsight. W przypadku usługi Azure Data Factory upewnij się, że usługa połączona z usługą ADF jest wskazywalna na domyślny kontener HDInsight, a nie na kontener pomocniczy.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
