---
title: Rozwiązywanie problemów z platformy Spark w usłudze Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania na temat pracy z usługą Apache Spark i Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: a4dc7293c00097c7a5752e29bf7c9a203cbb31a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721151"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z platformy Apache Spark przy użyciu usługi Azure HDInsight

Dowiedz się więcej o najważniejszych problemach i ich rozwiązania, pracując z [platformy Apache Spark](https://spark.apache.org/) ładunków w [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Jak skonfigurować aplikację platformy Apache Spark przy użyciu Apache Ambari w klastrach?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

Wartości konfiguracji aparatu Spark można dostroić pomóc uniknąć wyjątek OutofMemoryError aplikacji platformy Apache Spark. Poniższe kroki pokazują domyślne wartości konfiguracji aparatu Spark w usłudze Azure HDInsight: 

1. Wybierz listy klastrów, **Spark2**.

    ![Wybierz klaster, z listy](./media/apache-troubleshoot-spark/update-config-1.png)

2. Wybierz **Configs** kartę.

    ![Wybierz kartę konfiguracje](./media/apache-troubleshoot-spark/update-config-2.png)

3. Na liście konfiguracji wybierz **Custom-spark2 — domyślne**.

    ![Wybierz niestandardowe — spark — ustawienia domyślne](./media/apache-troubleshoot-spark/update-config-3.png)

4. Znajdź ustawienie wartości, które należy dopasować, takich jak **spark.executor.memory**. W tym przypadku wartość **4608m** jest zbyt wysoka.

    ![Wybierz pole spark.executor.memory](./media/apache-troubleshoot-spark/update-config-4.png)

5. Ustaw wartość na ustawienie zalecane. Wartość **2048m** jest zalecane w przypadku tego ustawienia.

    ![Zmień wartość na 2048m](./media/apache-troubleshoot-spark/update-config-5.png)

6. Zapisz wartość, a następnie Zapisz konfigurację. Na pasku narzędzi wybierz **Zapisz**.

    ![Zapisz ustawienia i Konfiguracja](./media/apache-troubleshoot-spark/update-config-6a.png)

    Otrzymasz powiadomienie, jeśli wszystkie konfiguracje wymagające uwagi. Należy pamiętać, elementy, a następnie wybierz **kontynuować mimo to**. 

    ![Wybierz kontynuować mimo to](./media/apache-troubleshoot-spark/update-config-6b.png)

    Wpisz notatkę o zmiany konfiguracji, a następnie wybierz **Zapisz**.

    ![Wprowadź informację o zmianach, które wprowadziłeś](./media/apache-troubleshoot-spark/update-config-6c.png)

7. Przy każdym zapisaniu konfiguracji pojawia się monit o ponowne uruchomienie usługi. Wybierz **ponowne uruchomienie**.

    ![Uruchom ponownie](./media/apache-troubleshoot-spark/update-config-7a.png)

    Potwierdź ponowne uruchomienie.

    ![Wybieranie przycisku Potwierdź ponowne uruchomienie wszystkich](./media/apache-troubleshoot-spark/update-config-7b.png)

    Możesz przejrzeć procesów, które są uruchomione.

    ![Przegląd uruchomionego procesu](./media/apache-troubleshoot-spark/update-config-7c.png)

8. Można dodać konfiguracji. Na liście konfiguracji, wybierz **Custom-spark2 — ustawienia domyślne**, a następnie wybierz pozycję **Dodaj właściwość**.

    ![Wybierz opcję Dodaj właściwość](./media/apache-troubleshoot-spark/update-config-8.png)

9. Definiowania nowej właściwości. Można zdefiniować jedną właściwość, za pomocą okno dialogowe dotyczące konkretnych ustawień, takich jak typ danych. Alternatywnie można zdefiniować wiele właściwości, za pomocą jednej definicji dla każdego wiersza. 

    W tym przykładzie **spark.driver.memory** właściwość jest zdefiniowana z wartością **4g**.

    ![Zdefiniuj nową właściwość](./media/apache-troubleshoot-spark/update-config-9.png)

10. Zapisz konfigurację, a następnie uruchom ponownie usługę, zgodnie z opisem w kroku 6 i 7.

Te zmiany są całego klastra, ale mogą być zastąpione, gdy prześlesz zadanie platformy Apache Spark.

### <a name="additional-reading"></a>Materiały uzupełniające

[Przesyłanie zadań platformy Apache Spark w klastrach HDInsight](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Jak skonfigurować aplikację platformy Apache Spark za pomocą notesu Jupyter w klastrach?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Aby określić, które Spark konfiguracji należy ustawić i jakie wartości, zobacz, co powoduje, że wyjątek OutofMemoryError aplikacji platformy Apache Spark.

2. W pierwszej komórki notesu programu Jupyter po **%% skonfigurować** dyrektywy, określanie konfiguracji platformy Spark w prawidłowym formacie JSON. Ustaw rzeczywistymi wartościami:

    ![Dodaj konfigurację](./media/apache-troubleshoot-spark/add-configuration-cell.png)

### <a name="additional-reading"></a>Materiały uzupełniające

[Przesyłanie zadań platformy Apache Spark w klastrach HDInsight](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Jak skonfigurować aplikację platformy Apache Spark przy użyciu usługi Livy Apache w klastrach?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Aby określić, które Spark konfiguracji należy ustawić i jakie wartości, zobacz, co powoduje, że wyjątek OutofMemoryError aplikacji platformy Apache Spark. 

2. Przesyłanie aplikacji Spark usługi Livy za pomocą klienta REST, takich jak narzędzie cURL. Użyj polecenia podobnego do następującego. Ustaw rzeczywistymi wartościami:

    ```apache
    curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
    ```

### <a name="additional-reading"></a>Materiały uzupełniające

[Przesyłanie zadań platformy Apache Spark w klastrach HDInsight](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Jak skonfigurować aplikację za pomocą skryptu spark-submit Apache Spark w klastrach?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Aby określić, które Spark konfiguracji należy ustawić i jakie wartości, zobacz, co powoduje, że wyjątek OutofMemoryError aplikacji platformy Apache Spark.

2. Uruchamianie powłoki spark przy użyciu polecenia podobnego do następującego. Zmień rzeczywistej wartości konfiguracji zgodnie z potrzebami: 

    ```apache
    spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
    ```

### <a name="additional-reading"></a>Materiały uzupełniające

[Przesyłanie zadań platformy Apache Spark w klastrach HDInsight](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="what-causes-an-apache-spark-application-outofmemoryerror-exception"></a>Co powoduje, że wyjątek OutofMemoryError aplikacji platformy Apache Spark?

### <a name="detailed-description"></a>Szczegółowy opis

Aplikacja Spark nie powiedzie się z następującymi typami nieprzechwyconych wyjątków:

```apache
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439) 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

```apache
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main] 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

### <a name="probable-cause"></a>Prawdopodobna przyczyna

Najbardziej prawdopodobną przyczyną tego wyjątku to, że nie ma wystarczającej ilości pamięci sterty jest przydzielany do maszyny wirtualnej Java (JVMs). JVMs te funkcje na rynek jako funkcja wykonawcza lub sterowników w ramach aplikacji aparatu Spark. 

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Określ maksymalny rozmiar danych Spark obsługuje aplikacja. Istnieje możliwość odgadnięcia, na podstawie maksymalnego rozmiaru danych wejściowych, danych pośrednich, który jest wytwarzany przez przekształcania danych wejściowych i danych wyjściowych, które są generowane, gdy aplikacji jest dalsze przekształcenie danych pośrednich. Ten proces może zająć iteracyjne, jeśli nie możesz obejrzeć początkowe przewidywanie formalnych. 

2. Upewnij się, że klaster HDInsight, które zamierzasz używać ma wystarczającą ilość zasobów pamięci i rdzeni, aby pomieścić aplikację aparatu Spark. Można to ustalić przez wyświetlenie sekcji metryki klastra w Interfejsie użytkownika YARN dla wartości **pamięć używana** programu vs. **Łącznej ilości pamięci**, i **liczby używanych rdzeni wirtualnych** programu vs. **Łączna liczba rdzeni wirtualnych**.

3. Ustaw następujące konfiguracje Spark odpowiednie wartości, które nie powinna przekraczać 90% dostępnej pamięci i rdzeni. Wartość musi być również w ramach aplikacji aparatu Spark wymagania dotyczące pamięci: 

    ```apache
    spark.executor.instances (Example: 8 for 8 executor count) 
    spark.executor.memory (Example: 4g for 4 GB) 
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
    spark.executor.cores (Example: 2 for 2 cores per executor) 
    spark.driver.memory (Example: 8g for 8GB) 
    spark.driver.cores (Example: 4 for 4 cores)   
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
    ```

    Aby obliczyć całkowita pamięć używana przez wszystkie executors: 
    
    ```apache
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```
   Aby obliczyć całkowita pamięć używana przez sterownik:
    
    ```apache
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

### <a name="additional-reading"></a>Materiały uzupełniające

- [Omówienie zarządzania pamięci platformy Apache Spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [Debugowanie aplikacji platformy Apache Spark w klastrze usługi HDInsight](https://web.archive.org/web/20190112152909/ https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)


### <a name="see-also"></a>Zobacz też
[Rozwiązywanie problemów przy użyciu usługi Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
