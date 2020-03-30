---
title: Rozwiązywanie problemów z programem Apache Spark w usłudze Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące pracy z programami Apache Spark i Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: 80bca2dab1d07d9b99e75e283068bff99335fa18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271943"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z platformą Apache Spark za pomocą usługi Azure HDInsight

Dowiedz się więcej o najważniejszych problemach i ich rozwiązaniach podczas pracy z ładunkami Apache Spark w [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Jak skonfigurować aplikację platformy Apache Spark za pomocą usługi Apache Ambari w klastrach?

Wartości konfiguracji platformy Spark można dostroić, `OutofMemoryError` aby uniknąć wyjątku aplikacji Apache Spark. Następujące kroki pokazują domyślne wartości konfiguracji platformy Spark w usłudze Azure HDInsight:

1. Zaloguj się do Ambari `https://CLUSTERNAME.azurehdidnsight.net` przy użyciu poświadczeń klastra. Na ekranie początkowym zostanie wyświetlony pulpit nawigacyjny przeglądu. Istnieją niewielkie różnice kosmetyczne między HDInsight 3.6 i 4.0.

1. Przejdź do **Spark2** > **Configs**.

    ![Wybieranie karty Konfiguracje](./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png)

1. Na liście konfiguracji wybierz i rozwiń **ustawienia domyślne Niestandardowe iskrowe2**.

1. Poszukaj ustawienia wartości, które należy dostosować, takiego jak **spark.executor.memory**. W tym przypadku wartość **9728m** jest zbyt wysoka.

    ![Wybieranie niestandardowych ustawień domyślnych iskier](./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png)

1. Ustaw wartość na zalecane ustawienie. Dla tego ustawienia zalecana jest wartość **2048m.**

1. Zapisz wartość, a następnie zapisz konfigurację. Wybierz **pozycję Zapisz**.

    ![Zmień wartość na 2048m](./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png)

    Napisz notatkę dotyczącą zmian konfiguracji, a następnie wybierz pozycję **Zapisz**.

    ![Wprowadzanie notatki o wprowadzonych zmianach](./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png)

    Użytkownik jest powiadamiany, jeśli dowolne konfiguracje wymagają uwagi. Zanotuj elementy, a następnie wybierz opcję **Kontynuuj mimo .**

    ![Wybierz kontynuuj mimo to](./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png)

1. Za każdym razem, gdy konfiguracja jest zapisywana, zostanie wyświetlony monit o ponowne uruchomienie usługi. Wybierz pozycję **Uruchom ponownie**.

    ![Wybierz ponowne uruchomienie](./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png)

    Potwierdź ponowne uruchomienie.

    ![Wybierz pozycję Potwierdź ponownie wszystko](./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png)

    Można przejrzeć uruchomione procesy.

    ![Przeglądanie uruchomionych procesów](./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png)

1. Można dodawać konfiguracje. Na liście konfiguracji wybierz pozycję **Niestandardowe iskrzenie2-defaults**, a następnie wybierz pozycję **Dodaj właściwość**.

    ![Wybierz właściwość dodaj](./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png)

1. Zdefiniuj nową właściwość. Pojedynczą właściwość można zdefiniować za pomocą okna dialogowego dla określonych ustawień, takich jak typ danych. Można też zdefiniować wiele właściwości przy użyciu jednej definicji na wiersz.

    W tym przykładzie właściwość **spark.driver.memory** jest zdefiniowana o wartości **4g**.

    ![Definiowanie nowej właściwości](./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png)

1. Zapisz konfigurację, a następnie uruchom ponownie usługę zgodnie z opisem w krokach 6 i 7.

Te zmiany są w całym klastrze, ale mogą być zastąpione podczas przesyłania zadania Platformy Spark.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Jak skonfigurować aplikację platformy Apache Spark za pomocą notesu Jupyter w klastrach?

W pierwszej komórce notesu Jupyter po dyrektywie **%%configure** określ konfiguracje platformy Spark w prawidłowym formacie JSON. W razie potrzeby zmień rzeczywiste wartości:

![Dodawanie konfiguracji](./media/apache-troubleshoot-spark/add-configuration-cell.png)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Jak skonfigurować aplikację platformy Apache Spark za pomocą usługi Apache Livy w klastrach?

Prześlij aplikację Spark do Livy przy użyciu klienta REST, takiego jak cURL. Użyj polecenia podobnego do następującego. W razie potrzeby zmień rzeczywiste wartości:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Jak skonfigurować aplikację platformy Apache Spark za pomocą polecenia spark-submit w klastrach?

Uruchom powłokę iskrową za pomocą polecenia podobnego do następującego. W razie potrzeby zmień rzeczywistą wartość konfiguracji:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>Dodatkowa lektura

[Apache Spark zadania składania w klastrach HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* [Omówienie zarządzania pamięcią iskrzą](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Debugowanie aplikacji Spark w klastrach HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
