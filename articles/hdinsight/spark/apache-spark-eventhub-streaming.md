---
title: 'Samouczek: Przetwarzania danych z usługi Azure Event Hubs z platformy Apache Spark w usłudze Azure HDInsight | Dokumentacja firmy Microsoft'
description: Apache Spark w usłudze Azure HDInsight nawiązać połączenia z usługi Azure Event Hubs i przetwarzać dane przesyłane strumieniowo.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: 9b59f5d58234aaf8f8385f722d6659548e066933
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-spark-in-hdinsight"></a>Samouczek: Proces tweetów przy użyciu usługi Azure Event Hubs i Spark w usłudze HDInsight

Z tego samouczka dowiesz się tworzenie Apache Spark przesyłania strumieniowego aplikacji wysyłanie tweetów do Centrum zdarzeń platformy Azure i utworzyć inną aplikację do odczytu tweetów z Centrum zdarzeń. Aby uzyskać szczegółowy opis przesyłania strumieniowego Spark, zobacz [Apache Spark przesyłania strumieniowego omówienie](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). Klastra Spark w usłudze Azure HDInsight oferuje te same funkcje przesyłania strumieniowego.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Wysyłanie komunikatów do Centrum zdarzeń platformy Azure
> * Odczytywanie wiadomości z Centrum zdarzeń platformy Azure

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

* **Ukończenie tego artykułu [samouczek: ładowanie danych i uruchamianie zapytań w klastrze Apache Spark w usłudze Azure HDInsight](./apache-spark-load-data-run-query.md)**.

## <a name="create-a-twitter-application"></a>Tworzenie aplikacji usługi Twitter

Aby otrzymywać strumień tweetów, musisz utworzyć aplikację w usłudze Twitter. Wykonaj instrukcje tworzenia aplikacji Twitter i zanotuj wartości potrzebnych do ukończenia tego samouczka.

1. Przejdź do [zarządzania aplikacjami w usłudze Twitter](https://apps.twitter.com/).
2. Wybierz **Utwórz nową aplikację**.
3. Podaj następujące wartości:

    - Nazwa: Podaj nazwę aplikacji. Wartość używana w tym samouczku jest **HDISparkStreamApp0423**. Ta nazwa musi być unikatowa.
    - Opis: Podaj krótki opis aplikacji. Wartość używana w tym samouczku jest **proste HDInsight Spark przesyłania strumieniowego aplikacji**.
    - Witryna sieci Web: Podaj aplikacji witryny sieci Web. Nie musi być prawidłową witryną sieci Web.  Wartość używana w tym samouczku jest **http://www.contoso.com**.
    - Adres URL wywołania zwrotnego: można pole pozostanie puste.

4. Wybierz **tak, znam i Akceptuję umowę Developer Twitter**, a następnie wybierz **tworzenie aplikacji Twitter**.
5. Wybierz **kluczy i tokenów dostępu** kartę.
6. Wybierz **Utwórz moje tokenu dostępu** na końcu strony.
7. Zanotuj następujące wartości ze strony.  Te wartości są potrzebne w dalszej części tego samouczka:

    - **Klucz klienta (klucz interfejsu API)**    
    - **Klucz tajny klienta (klucz tajny interfejsu API)**  
    - **Token dostępu**
    - **Klucz tajny tokenu dostępu**   

## <a name="create-an-azure-event-hub"></a>Tworzenie centrum zdarzeń usługi Azure Event Hub

To Centrum zdarzeń służy do przechowywania tweetów.

1. Zaloguj się do [Portalu Azure](https://ms.portal.azure.com).
2. Wybierz **Utwórz zasób** w lewym górnym rogu ekranu.
3. Wybierz **Internetu rzeczy**, a następnie wybierz pozycję **usługi Event Hubs**.

    ![Tworzenie Centrum zdarzeń do przesyłania strumieniowego przykład Spark](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "tworzenia Centrum zdarzeń do przesyłania strumieniowego przykład Spark")
4. Wprowadź następujące wartości dla nowej przestrzeni nazw Centrum zdarzeń:

    - **Nazwa**: Wprowadź nazwę Centrum zdarzeń.  Wartość używana w tym samouczku jest **myeventhubns20180403**.
    - **Cena warstwy**: Wybierz **standardowe**.
    - **Grupa zasobów**: masz opcję, aby utworzyć nowy lub wybierz grupę zasobów klastra Spark. 
    - **Lokalizacja**: Wybierz taki sam **lokalizacji** jako klastra Apache Spark w usłudze HDInsight w celu zmniejszenia opóźnienia i koszty.

    ![Podaj nazwę Centrum zdarzeń przesyłania strumieniowego przykład Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Podaj nazwę Centrum zdarzeń przesyłania strumieniowego przykład Spark")
5. Wybierz **Utwórz** można utworzyć przestrzeni nazw.

6. Otwieranie obszaru nazw Centrum zdarzeń z poniższymi instrukcjami:

    1. Wybierz z portalu, **wszystkie usługi**.
    2. W polu filtru wpisz **usługi event hubs**.
    3. Kliknij dwukrotnie przestrzeni nazw, który został utworzony.
    4. Wybierz **+ Centrum zdarzeń**.

6. Na liście przestrzeni nazw usługi Event Hubs wybierz nowo utworzonej przestrzeni nazw.      
5. Wybierz **usługi Event Hubs**, a następnie wybierz **+ Centrum zdarzeń** do utworzenia nowego Centrum zdarzeń.
  

6. Wprowadź następujące wartości:

    - Nazwa: Nadaj nazwę Centrum zdarzeń.
    - Liczba partycji: 10
    - Komunikat przechowywania: 1. 
   
    ![Podaj szczegóły zdarzenia koncentratora do przesyłania strumieniowego przykład Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "podanie szczegółów Centrum zdarzeń przesyłania strumieniowego przykład Spark")

7. Wybierz pozycję **Utwórz**.
8. Wybierz **zasady dostępu współużytkowanego** dla przestrzeni nazw (Uwaga nie jest zasady dostępu udostępnionego Centrum zdarzeń), a następnie wybierz **RootManageSharedAccessKey**.
    
     ![Ustawianie zasad Centrum zdarzeń dla przesyłania strumieniowego przykład Spark](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "ustawić Centrum zdarzeń zasady dotyczące przesyłania strumieniowego przykład Spark")

9. Zapisywanie wartości **klucza podstawowego** i **klucz podstawowy ciąg połączenia** do użycia później w samouczku.

     ![Wyświetlanie kluczy zasad Centrum zdarzeń do przesyłania strumieniowego przykład Spark](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "zasad Centrum zdarzeń widoku kluczy, na przykład przesyłania strumieniowego Spark")


## <a name="send-tweets-to-the-event-hub"></a>Wysyłanie tweetów do Centrum zdarzeń

Należy utworzyć notesu Jupyter i nadaj mu nazwę **SendTweetsToEventHub**. 

1. Uruchom następujący kod, aby dodać zewnętrznej biblioteki Maven:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. Uruchom poniższy kod do wysyłania tweetów do Centrum zdarzeń:

    ```
    import java.util._
    import scala.collection.JavaConverters._
    import java.util.concurrent._
    
    import org.apache.spark._
    import org.apache.spark.streaming._
    import org.apache.spark.eventhubs.ConnectionStringBuilder

    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    import com.microsoft.azure.eventhubs._
    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)
    
    def sendEvent(message: String) = {
          val messageData = EventData.create(message.getBytes("UTF-8"))
          eventHubClient.get().send(messageData)
          println("Sent event: " + message + "\n")
    }
    
    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter application configurations
    // Replace values below with yours   
    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"
    
    val cb = new ConfigurationBuilder()
    cb.setDebugEnabled(true).setOAuthConsumerKey(twitterConsumerKey).setOAuthConsumerSecret(twitterConsumerSecret).setOAuthAccessToken(twitterOauthAccessToken).setOAuthAccessTokenSecret(twitterOauthTokenSecret)
    
    val twitterFactory = new TwitterFactory(cb.build())
    val twitter = twitterFactory.getInstance()

    // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!
    
    val query = new Query(" #Azure ")
    query.setCount(100)
    query.lang("en")
    var finished = false
    while (!finished) {
      val result = twitter.search(query)
      val statuses = result.getTweets()
      var lowestStatusId = Long.MaxValue
      for (status <- statuses.asScala) {
        if(!status.isRetweet()){
          sendEvent(status.getText())
        }
        lowestStatusId = Math.min(status.getId(), lowestStatusId)
        Thread.sleep(2000)
      }
      query.setMaxId(lowestStatusId - 1)
    }
    
    // Closing connection to the Event Hub
    eventHubClient.get().close()
    ```

3. Otwórz Centrum zdarzeń w portalu Azure.  Na **omówienie**, zostanie wyświetlona niektóre Wykresy pokazujący wiadomości wysyłane do Centrum zdarzeń.

## <a name="read-tweets-from-the-event-hub"></a>Tweetów odczytu z Centrum zdarzeń

Należy utworzyć inny notesu Jupyter i nadaj mu nazwę **ReadTweetsFromEventHub**. 

1. Uruchom następujący kod, aby dodać biblioteki zewnętrznej Maven:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0"}}
    ```
2. Uruchom następujący kod, aby odczytać tweetów z Centrum zdarzeń:

    ```
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    val customEventhubParameters = EventHubsConf(connectionString).setMaxEventsPerTrigger(5)
    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()
    //incomingStream.printSchema    
    
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so you cast it to string to see the actual content of the message
    val messages = incomingStream.withColumn("Offset", $"offset".cast(LongType)).withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType)).withColumn("Timestamp", $"enqueuedTime".cast(LongType)).withColumn("Body", $"body".cast(StringType)).select("Offset", "Time (readable)", "Timestamp", "Body")
    
    messages.printSchema
    
    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Dzięki usłudze HDInsight dane są przechowywane w magazynie Azure lub usługi Azure Data Lake Store, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane. Jeśli planujesz do pracy w następnym samouczku natychmiast, można zachować klastra.

Otwórz klaster w portalu Azure, a następnie wybierz **usunąć**.

![Usuwanie klastra usługi HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "HDInsight Usuwanie klastra")

Można również wybrać nazwę grupy zasobów, aby otworzyć stronę grupy zasobów, a następnie wybierz **Usuń grupę zasobów**. Usunięcie grupy zasobów, możesz usunąć klaster Spark w usłudze HDInsight i domyślne konto magazynu.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Przeczytaj komunikat z Centrum zdarzeń.
Przejdź do następnego artykuł, aby znaleźć można utworzyć aplikacji uczenia maszynowego. 

> [!div class="nextstepaction"]
> [Tworzenie aplikacji uczenia maszynowego](./apache-spark-ipython-notebook-machine-learning.md)


