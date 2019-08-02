---
title: Przetwarzanie Apache Spark zadań przesyłania strumieniowego trwa dłużej niż zwykle w przypadku przetwarzania w usłudze Azure HDInsight
description: Przetwarzanie Apache Spark zadań przesyłania strumieniowego trwa dłużej niż zwykle w przypadku przetwarzania w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 7c7baaaa5fd08a102b3b55c38fb2c4bf892480c5
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679434"
---
# <a name="scenario-apache-spark-streaming-jobs-take-longer-than-usual-to-process-in-azure-hdinsight"></a>Scenariusz: Przetwarzanie Apache Spark zadań przesyłania strumieniowego trwa dłużej niż zwykle w przypadku przetwarzania w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania składników Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Należy zauważyć, że niektóre zadania przesyłania strumieniowego Apache Spark są powolne lub trwają dłużej niż zwykle. W przypadku aplikacji do przesyłania strumieniowego Spark każda partia komunikatów odpowiada jednemu zadaniu przesłanemu do platformy Spark. Jeśli wykonanie zadania zwykle trwa X s, może upłynąć od czasu do 2-3 minut.

## <a name="cause"></a>Przyczyna

Jedną z możliwych przyczyn jest to, że Apache Kafka producent zajmie więcej niż 2 minuty, aby zakończyć zapisywanie do klastra Kafka. W celu dalszej debugowania problemu Kafka można dodać do kodu, który używa producenta Kafka do wysyłania komunikatów i skorelować z dziennikami z klastra Kafka.

Inną możliwą przyczyną jest to, że częste operacje odczytu i zapisu w WASB mogą spowodować zwłokę kolejnych mikropartii. Implementacja `Filesystem.listStatus` WASB jest bardzo niska z powodu `O(n!)` algorytmu usuwania duplikatów. Zużywa zbyt dużo pamięci z powodu dodatkowej konwersji z `BlobListItem` `FileStatus`do `FileMetadata` . Na przykład algorytm zajmie ponad 30 minut, aby wyświetlić listę plików 700 000. Dlatego jeśli `ListBlobs` jest wywoływana agresywnie przez SparkSQL każdej mikropartii, spowoduje to, że kolejne mikropartie opóźnią się w związku z tym, co będzie miało duże opóźnienia w planowaniu. [Ta poprawka](https://issues.apache.org/jira/browse/HADOOP-15547) rozwiązuje ten problem, ale jeśli nie ma go w Twoim środowisku, `ListBlobs` wystąpi duże opóźnienie. Nawet jeśli pliki są usuwane co godzinę, lista w zapleczu musi przekroczyć wszystkie wiersze (włącznie z usuniętych), ponieważ proces odzyskiwania pamięci nie został jeszcze ukończony. Podczas gdy poprawka może rozwiązać jakiś problem, problem z wyrzucaniem elementów bezużytecznych może nadal powodować opóźnienie w przetwarzaniu partii.

## <a name="resolution"></a>Rozwiązanie

Zastosuj poprawkę [HADOOP-15547](https://issues.apache.org/jira/browse/HADOOP-15547) . Jeśli nie jest to możliwe, można użyć systemu HDFS jako lokalizacji punktu kontroli. Ustaw `checkpointDirectory` na wartość podobną `hdfs://mycluster/checkpoint`do:.

## <a name="next-steps"></a>Kolejne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) za pomocą — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
