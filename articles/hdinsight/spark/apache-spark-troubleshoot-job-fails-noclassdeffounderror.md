---
title: NoClassDefFoundError — platforma Iskrowa Apache z danymi platformy Apache Kafka w usłudze Azure HDInsight
description: Zadanie przesyłania strumieniowego Platformy Apache Spark, które odczytuje dane z klastra Platformy Kafek Apache, kończy się niepowodzeniem za pomocą narzędzia NoClassDefFoundError w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 4659274110add96613ca88560edfb459b20a99cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894347"
---
# <a name="apache-spark-streaming-job-that-reads-apache-kafka-data-fails-with-noclassdeffounderror-in-hdinsight"></a>Zadanie przesyłania strumieniowego Apache Spark, które odczytuje dane Apache Kafka, kończy się niepowodzeniem dzięki noclassdeffounderror w hdinsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas korzystania ze składników Platformy Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Klaster Platformy Spark apache uruchamia zadanie przesyłania strumieniowego platformy Spark, które odczytuje dane z klastra Platformy Apache. Zadanie przesyłania strumieniowego platformy Spark kończy się niepowodzeniem, jeśli kompresja strumienia platformy Kafka jest włączona. W takim przypadku aplikacja Yarn do przesyłania strumieniowego spark application_1525986016285_0193 nie powiodła się z powodu błędu:

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany przez `spark-streaming-kafka` określenie wersji pliku jar, która jest inna niż wersja klastra platformy Kafka, który jest uruchomiony.

Na przykład jeśli korzystasz z klastra platformy Kafka w wersji 0.10.1, następujące polecenie spowoduje błąd:

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>Rozwiązanie

Użyj polecenia Spark-submit `–packages` z opcją i upewnij się, że wersja pliku jar iskrowego przesyłania strumieniowego kafka jest taka sama jak wersja uruchomionego klastra platformy Kafka.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
