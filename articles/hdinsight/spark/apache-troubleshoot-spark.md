---
title: Rozwiązywanie problemów Apache Spark w usłudze Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania na temat pracy z usługą Apache Spark i Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: 80bca2dab1d07d9b99e75e283068bff99335fa18
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271943"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z platformy Apache Spark przy użyciu usługi Azure HDInsight

Poznaj najważniejsze problemy i ich rozwiązania podczas pracy z Apache Spark ładunkiami w programie [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Jak skonfigurować aplikację platformy Apache Spark przy użyciu Apache Ambari w klastrach?

Wartości konfiguracji platformy Spark można dostrajać w celu uniknięcia wyjątku `OutofMemoryError` aplikacji Apache Spark. Poniższe kroki pokazują domyślne wartości konfiguracji platformy Spark w usłudze Azure HDInsight:

1. Zaloguj się do Ambari w `https://CLUSTERNAME.azurehdidnsight.net` z poświadczeniami klastra. Na ekranie początkowym zostanie wyświetlony pulpit nawigacyjny przegląd. Istnieją niewielkie różnice między usługami HDInsight 3,6 i 4,0.

1. Przejdź do **Spark2** > **configs**.

    ![Wybierz kartę konfiguracje](./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png)

1. Na liście konfiguracji wybierz i rozwiń pozycję **Custom-spark2-Defaults**.

1. Wyszukaj ustawienie wartości, które należy dostosować, takie jak **Spark. wykonawca. Memory**. W tym przypadku wartość **9728m** jest zbyt wysoka.

    ![Wybierz niestandardowe — spark — ustawienia domyślne](./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png)

1. Ustaw wartość na ustawienie zalecane. Wartość **2048m** jest zalecana dla tego ustawienia.

1. Zapisz wartość, a następnie Zapisz konfigurację. Wybierz pozycję **Zapisz**.

    ![Zmień wartość na 2048m](./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png)

    Napisz uwagi dotyczące zmian konfiguracji, a następnie wybierz pozycję **Zapisz**.

    ![Wprowadź informację o zmianach, które wprowadziłeś](./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png)

    Otrzymasz powiadomienie, jeśli wszystkie konfiguracje wymagające uwagi. Zanotuj elementy, a następnie wybierz pozycję **kontynuować mimo wszystko**.

    ![Wybierz kontynuować mimo to](./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png)

1. Przy każdym zapisaniu konfiguracji pojawia się monit o ponowne uruchomienie usługi. Wybierz pozycję **Uruchom ponownie**.

    ![Uruchom ponownie](./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png)

    Potwierdź ponowne uruchomienie.

    ![Wybieranie przycisku Potwierdź ponowne uruchomienie wszystkich](./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png)

    Możesz przejrzeć procesów, które są uruchomione.

    ![Przegląd uruchomionego procesu](./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png)

1. Można dodać konfiguracji. Na liście konfiguracji wybierz pozycję **Custom-spark2-Defaults**, a następnie wybierz pozycję **Dodaj właściwość**.

    ![Wybierz opcję Dodaj właściwość](./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png)

1. Definiowania nowej właściwości. Można zdefiniować jedną właściwość, za pomocą okno dialogowe dotyczące konkretnych ustawień, takich jak typ danych. Alternatywnie można zdefiniować wiele właściwości, za pomocą jednej definicji dla każdego wiersza.

    W tym przykładzie właściwość **Spark. Driver. Memory** jest zdefiniowana z wartością **4G**.

    ![Zdefiniuj nową właściwość](./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png)

1. Zapisz konfigurację, a następnie uruchom ponownie usługę, zgodnie z opisem w kroku 6 i 7.

Te zmiany są całego klastra, ale mogą być zastąpione, gdy prześlesz zadanie platformy Apache Spark.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Jak skonfigurować aplikację platformy Apache Spark za pomocą notesu Jupyter w klastrach?

W pierwszej komórce notesu Jupyter po określeniu w dyrektywie **%% Configure Skonfiguruj** konfigurację platformy Spark w prawidłowym formacie JSON. Ustaw rzeczywistymi wartościami:

![Dodaj konfigurację](./media/apache-troubleshoot-spark/add-configuration-cell.png)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Jak skonfigurować aplikację platformy Apache Spark przy użyciu usługi Livy Apache w klastrach?

Przesyłanie aplikacji Spark usługi Livy za pomocą klienta REST, takich jak narzędzie cURL. Użyj polecenia podobnego do następującego. Ustaw rzeczywistymi wartościami:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Jak skonfigurować aplikację za pomocą skryptu spark-submit Apache Spark w klastrach?

Uruchamianie powłoki spark przy użyciu polecenia podobnego do następującego. Zmień rzeczywistej wartości konfiguracji zgodnie z potrzebami:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>Materiały uzupełniające

[Apache Spark przesyłanie zadania w klastrach usługi HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* [Zarządzanie pamięcią Spark — Omówienie](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Debugowanie aplikacji Spark w klastrach usługi HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego Microsoft Azure konta, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
