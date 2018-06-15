---
title: Rozwiązywanie problemów z Spark przy użyciu usługi Azure HDInsight | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania na temat pracy z Apache Spark i usłudze Azure HDInsight.
keywords: Usługa Azure HDInsight, Spark, często zadawane pytania, rozwiązywanie problemów z przewodnika, typowe problemy, konfiguracji aplikacji, Ambari
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: ''
editor: ''
ms.assetid: 25D89586-DE5B-4268-B5D5-CC2CE12207ED
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: arijitt
ms.openlocfilehash: 15fe5e6d2acdb8d782342b21f5db81443c44843d
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164537"
---
# <a name="troubleshoot-spark-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z Spark przy użyciu usługi Azure HDInsight

Dowiedz się więcej o Najważniejsze problemy i rozwiązania ich podczas pracy z ładunków Apache Spark w Apache Ambari.

## <a name="how-do-i-configure-a-spark-application-by-using-ambari-on-clusters"></a>Jak skonfigurować aplikacji Spark przy użyciu Ambari w klastrach?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

Wartości konfiguracji do wykonania tej procedury wcześniej zostały ustawione w usłudze HDInsight. Aby określić, które Spark konfiguracje trzeba ustawić i jakie wartości, zobacz [co powoduje Spark wyjątek OutofMemoryError aplikacji](#what-causes-a-spark-application-outofmemoryerror-exception). 

1. Na liście klastrów, wybierz **Spark2**.

    ![Wybierz klaster z listy](./media/apache-troubleshoot-spark/update-config-1.png)

2. Wybierz **Configs** kartę.

    ![Wybierz kartę Configs](./media/apache-troubleshoot-spark/update-config-2.png)

3. Na liście konfiguracji, wybierz **niestandardowe spark2-domyślne**.

    ![Wybierz niestandardowe spark — ustawienia domyślne](./media/apache-troubleshoot-spark/update-config-3.png)

4. Wyszukaj ustawienie wartości, które należy dopasować, takich jak **spark.executor.memory**. W takim przypadku wartość **4608m** jest zbyt duża.

    ![Wybierz pole spark.executor.memory](./media/apache-troubleshoot-spark/update-config-4.png)

5. Wartość to ustawienie zalecane. Wartość **2048m** jest zalecane dla tego ustawienia.

    ![Zmień wartość na 2048m](./media/apache-troubleshoot-spark/update-config-5.png)

6. Zapisz wartość, a następnie zapisać konfigurację. Na pasku narzędzi wybierz **zapisać**.

    ![Zapisz ustawienia i Konfiguracja](./media/apache-troubleshoot-spark/update-config-6a.png)

    Użytkownik jest powiadamiany, jeśli wszystkie konfiguracje wymagają uwagi. Należy pamiętać, elementy, a następnie wybierz **kontynuować mimo to**. 

    ![Wybierz kontynuować mimo to](./media/apache-troubleshoot-spark/update-config-6b.png)

    Wpisz notatkę o zmiany konfiguracji, a następnie wybierz **zapisać**.

    ![Wprowadź notatkę dotyczącą wprowadzone zmiany](./media/apache-troubleshoot-spark/update-config-6c.png)

7. Zawsze, gdy konfiguracja jest zapisywana, zostanie wyświetlony monit ponownie uruchom usługę. Wybierz **ponownego uruchomienia**.

    ![Uruchom ponownie](./media/apache-troubleshoot-spark/update-config-7a.png)

    Upewnij się, ponowne uruchomienie.

    ![Wybierz upewnij się, uruchom ponownie wszystkie](./media/apache-troubleshoot-spark/update-config-7b.png)

    Możesz przejrzeć procesów, które są uruchomione.

    ![Przejrzyj uruchomione procesy](./media/apache-troubleshoot-spark/update-config-7c.png)

8. Można dodać konfiguracje. Na liście konfiguracji, wybierz **niestandardowe spark2-ustawienia domyślne**, a następnie wybierz **Dodaj właściwość**.

    ![Wybierz opcję Dodaj właściwość](./media/apache-troubleshoot-spark/update-config-8.png)

9. Zdefiniuj nową właściwość. Za pomocą okna dialogowego dotyczące konkretnych ustawień, takich jak typ danych można zdefiniować jednej właściwości. Alternatywnie można zdefiniować wiele właściwości, za pomocą jednej definicji w każdym wierszu. 

    W tym przykładzie **spark.driver.memory** zdefiniowano właściwość z wartością **4g**.

    ![Zdefiniuj nową właściwość](./media/apache-troubleshoot-spark/update-config-9.png)

10. Zapisz konfigurację, a następnie uruchom ponownie usługę, zgodnie z opisem w kroku 6 i 7.

Te zmiany są całego klastra, ale może zostać zastąpiona po przesłaniu zadania Spark.

### <a name="additional-reading"></a>Dodatkowe materiały

[Przesyłanie zadań Spark na klastrów](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Jak skonfigurować aplikacji Spark przy użyciu notesu Jupyter w klastrze?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Aby określić, które Spark konfiguracje trzeba ustawić i jakie wartości, zobacz [co powoduje Spark wyjątek OutofMemoryError aplikacji](#what-causes-a-spark-application-outofmemoryerror-exception).

2. W pierwszej komórki notesu Jupyter po **%% skonfigurować** dyrektywy, określ konfiguracje Spark prawidłowy format JSON. Ustaw rzeczywistymi wartościami:

    ![Dodaj konfigurację](./media/apache-troubleshoot-spark/add-configuration-cell.png)

### <a name="additional-reading"></a>Dodatkowe materiały

[Przesyłanie zadań Spark na klastrów](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-livy-on-clusters"></a>Jak skonfigurować aplikacji Spark przy użyciu programu Livy w klastrach?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Aby określić, które Spark konfiguracje trzeba ustawić i jakie wartości, zobacz [co powoduje Spark wyjątek OutofMemoryError aplikacji](#what-causes-a-spark-application-outofmemoryerror-exception). 

2. Przesyłanie aplikacji Spark Livy przy użyciu klienta REST, takich jak cURL. Polecenie podobne do następującego. Ustaw rzeczywistymi wartościami:

    ```apache
    curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
    ```

### <a name="additional-reading"></a>Dodatkowe materiały

[Przesyłanie zadań Spark na klastrów](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-spark-submit-on-clusters"></a>Jak skonfigurować Spark, przesłać spark aplikacji przy użyciu w klastrach?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Aby określić, które Spark konfiguracje trzeba ustawić i jakie wartości, zobacz [co powoduje Spark wyjątek OutofMemoryError aplikacji](#what-causes-a-spark-application-outofmemoryerror-exception).

2. Uruchom powłokę spark przy użyciu polecenia podobny do następującego. Ustaw wartość rzeczywistą konfiguracje: 

    ```apache
    spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
    ```

### <a name="additional-reading"></a>Dodatkowe materiały

[Przesyłanie zadań Spark na klastrów](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="what-causes-a-spark-application-outofmemoryerror-exception"></a>Dlaczego Spark wyjątek OutofMemoryError aplikacji?

### <a name="detailed-description"></a>Szczegółowy opis

Aplikacji Spark kończy się niepowodzeniem z następujących typów nieprzechwyconych wyjątków:

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

Najbardziej prawdopodobną przyczyną tego wyjątku jest, że nie ma wystarczającej ilości pamięci sterty jest przydzielona do maszyny wirtualnej Java (JVMs). Te JVMs będą uruchamiane jako modułów lub sterowniki jako część aplikacji Spark. 

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Maksymalny rozmiar danych Spark dojść do aplikacji. Możesz wprowadzić wynik, na podstawie maksymalnego rozmiaru danych wejściowych, pośrednich danych, który jest generowany przez przekształcania danych wejściowych i danych wyjściowych, który jest generowany, gdy aplikacja jest dalsze przekształcanie pośrednich danych. Ten proces może zająć iteracyjną, jeśli nie możesz początkowej formalnego argumentu. 

2. Upewnij się, że klaster usługi HDInsight, który ma być używana ma wystarczające zasoby pamięci i rdzeni, aby zmieścił się w aplikacji Spark. Można to określić, wyświetlając sekcji metryki klastra w interfejsie użytkownika YARN dla wartości **pamięć używana** vs. **Suma pamięci**, i **VCores używane** vs. **Suma VCores**.

3. Ustaw następujące konfiguracje Spark na odpowiednie wartości, które nie może przekraczać 90% dostępnej pamięci i rdzeni. Wartości powinny być dobrze w wymagania dotyczące pamięci aplikacji Spark: 

    ```apache
    spark.executor.instances (Example: 8 for 8 executor count) 
    spark.executor.memory (Example: 4g for 4 GB) 
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
    spark.executor.cores (Example: 2 for 2 cores per executor) 
    spark.driver.memory (Example: 8g for 8GB) 
    spark.driver.cores (Example: 4 for 4 cores)   
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
    ```

    Aby Oblicz łączna ilość pamięci używana przez wszystkich modułów: 
    
    ```apache
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```
   Aby Oblicz suma pamięci używanej przez sterownik:
    
    ```apache
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

### <a name="additional-reading"></a>Dodatkowe materiały

- [Omówienie zarządzania pamięci w iskrowym](http://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [Debugowanie aplikacji Spark na pomoc techniczna](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)


### <a name="see-also"></a>Zobacz też
[Rozwiązywanie problemów przy użyciu usługi Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)

