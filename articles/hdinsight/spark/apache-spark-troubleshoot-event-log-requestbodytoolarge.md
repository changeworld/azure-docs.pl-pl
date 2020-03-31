---
title: Błąd RequestBodyTooLarge z aplikacji Apache Spark — Usługa Azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge pojawia się w dzienniku aplikacji do przesyłania strumieniowego Apache Spark w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 777d06670238a7625d190c92f78a55cd4794d226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894395"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem... RequestBodyTooLarge" pojawiają się w dzienniku aplikacji do przesyłania strumieniowego Apache Spark w hdinsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas korzystania ze składników Platformy Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Błąd: `NativeAzureFileSystem ... RequestBodyTooLarge` pojawia się w dzienniku sterowników aplikacji do przesyłania strumieniowego Apache Spark.

## <a name="cause"></a>Przyczyna

Plik dziennika zdarzeń Spark prawdopodobnie uderza w limit długości pliku dla WASB.

W spark 2.3 każda aplikacja Spark generuje jeden plik dziennika zdarzeń Platformy Spark. Plik dziennika zdarzeń platformy Spark dla aplikacji do przesyłania strumieniowego platformy Spark nadal rośnie, gdy aplikacja jest uruchomiona. Dziś plik na WASB ma limit bloków 50000, a domyślny rozmiar bloku to 4 MB. Tak więc w konfiguracji domyślnej maksymalny rozmiar pliku wynosi 195 GB. Jednak usługa Azure Storage zwiększyła maksymalny rozmiar bloku do 100 MB, co skutecznie spowodowało, że limit pojedynczego pliku został do 4,75 TB. Aby uzyskać więcej informacji, zobacz [Skalowalność i cele wydajności dla magazynu obiektów Blob](../../storage/blobs/scalability-targets.md).

## <a name="resolution"></a>Rozwiązanie

Istnieją trzy rozwiązania dla tego błędu:

* Zwiększ rozmiar bloku do 100 MB. W interfejsie użytkownika Ambari zmodyfikuj właściwość konfiguracj `fs.azure.write.request.size` HDFS (lub utwórz ją w `Custom core-site` sekcji). Ustaw właściwość na większą wartość, na przykład: 33554432. Zapisz zaktualizowaną konfigurację i uruchom ponownie składniki, których dotyczy problem.

* Okresowo zatrzymywać i ponownie przesyłać zadanie przesyłania strumieniowego w iskrze.

* Użyj hdfs do przechowywania dzienników zdarzeń Spark. Korzystanie z usługi HDFS do magazynowania może spowodować utratę danych zdarzeń platformy Spark podczas skalowania klastra lub uaktualnień platformy Azure.

    1. Wprowadzanie `spark.eventlog.dir` zmian `spark.history.fs.logDirectory` w interfejsie użytkownika Ambari i za ich pośrednictwem:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Tworzenie katalogów w plików HDFS:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Uruchom ponownie wszystkie usługi, których dotyczy problem, za pośrednictwem interfejsu użytkownika Ambari.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
