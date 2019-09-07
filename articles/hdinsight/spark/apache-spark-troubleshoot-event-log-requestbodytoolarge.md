---
title: RequestBodyTooLarge w dziennikach Apache Spark dla aplikacji przesyłania strumieniowego w usłudze Azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge pojawia się w dzienniku dla aplikacji przesyłania strumieniowego Apache Spark w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 92e9e41d5829e10bc0130d209249301d78f1b5d3
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736329"
---
# <a name="scenario-nativeazurefilesystem--requestbodytoolarge-appears-in-log-for-apache-spark-streaming-app-in-azure-hdinsight"></a>Scenariusz: "NativeAzureFileSystem ... RequestBodyTooLarge "pojawia się w dzienniku dla aplikacji przesyłania strumieniowego Apache Spark w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania składników Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Błąd: `NativeAzureFileSystem ... RequestBodyTooLarge` pojawia się w dzienniku sterowników dla aplikacji Apache Spark Streaming.

## <a name="cause"></a>Przyczyna

Plik dziennika zdarzeń Spark prawdopodobnie zostanie osiągnięty w limicie długości pliku dla WASB.

W platformie Spark 2,3 Każda aplikacja Spark generuje jeden plik dziennika zdarzeń platformy Spark. Plik dziennika zdarzeń Spark dla aplikacji do przesyłania strumieniowego Spark nadal rośnie, gdy aplikacja jest uruchomiona. Dzisiaj plik w WASB ma limit bloków 50000, a domyślny rozmiar bloku to 4 MB. W konfiguracji domyślnej maksymalny rozmiar pliku to 195 GB. Jednak usługa Azure Storage zwiększyła maksymalny rozmiar bloku do 100 MB, co skutecznie przekroczy limit jednego pliku do 4,75 TB. Aby uzyskać więcej informacji, zobacz [cele dotyczące skalowalności i wydajności usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

## <a name="resolution"></a>Rozwiązanie

Dostępne są trzy rozwiązania tego błędu:

* Zwiększ rozmiar bloku do 100 MB. W interfejsie użytkownika Ambari Zmodyfikuj właściwość `fs.azure.write.request.size` konfiguracji systemu plików HDFS (lub utwórz ją w `Custom core-site` sekcji). Ustaw właściwość na większą wartość, na przykład: 33554432. Zapisz zaktualizowaną konfigurację i ponownie uruchom składniki.

* Okresowo zatrzymuje i ponownie przesyła zadanie przetwarzania strumieniowego platformy Spark.

* Przechowywanie dzienników zdarzeń platformy Spark przy użyciu systemu plików HDFS. Korzystanie z systemu plików HDFS dla magazynu może spowodować utratę danych zdarzeń platformy Spark podczas skalowania klastra lub uaktualnień platformy Azure.

    1. Wprowadź zmiany do `spark.eventlog.dir` i `spark.history.fs.logDirectory` za pośrednictwem interfejsu użytkownika Ambari:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Tworzenie katalogów w systemie plików HDFS:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Uruchom ponownie wszystkie usługi, których to dotyczy, za pomocą interfejsu użytkownika Ambari.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) za pomocą — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
