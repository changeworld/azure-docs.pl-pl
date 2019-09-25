---
title: 'Samouczek: Przetwarzanie danych z usługi Azure Event Hubs przy użyciu Apache Spark w usłudze HDInsight'
description: Samouczek — łączenie Apache Spark w usłudze Azure HDInsight z usługą Azure Event Hubs i przetwarzanie danych przesyłanych strumieniowo.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/24/2019
ms.openlocfilehash: be21b809272a132ee6e63582036c36ad5dcdf4ad
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266207"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-apache-spark-in-hdinsight"></a>Samouczek: Przetwarzanie tweetów przy użyciu usługi Azure Event Hubs i Apache Spark w usłudze HDInsight

W tym samouczku dowiesz się, jak utworzyć aplikację [Apache Spark](https://spark.apache.org/) streaming, aby wysyłać tweety do centrum zdarzeń platformy Azure, i utworzyć kolejną aplikację do odczytywania tweetów z centrum zdarzeń. Aby uzyskać szczegółowy opis usługi Spark streaming, zobacz [Apache Spark Streaming Overview](https://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). Usługa HDInsight zapewnia te same funkcje przesyłania strumieniowego do klastra Spark na platformie Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Wysyłanie komunikatów do usługi Azure Event Hub
> * Odczytywanie komunikatów z usługi Azure Event Hub

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Spark w usłudze HDInsight. Zobacz [Tworzenie klastra Apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

* Znajomość zagadnień dotyczących używania notesów Jupyter za pomocą platformy Spark w usłudze HDInsight. Aby uzyskać więcej informacji, zobacz [ładowanie danych i uruchamianie zapytań za pomocą Apache Spark w usłudze HDInsight](./apache-spark-load-data-run-query.md).

* Konto w usłudze [Twitter](https://twitter.com/i/flow/signup).

## <a name="create-a-twitter-application"></a>Tworzenie aplikacji usługi Twitter

Aby otrzymywać strumień tweetów, musisz utworzyć aplikację w usłudze Twitter. Postępuj zgodnie z instrukcjami, aby utworzyć aplikację usługi Twitter i zapisać wartości, które są potrzebne do ukończenia tego samouczka.

1. Przejdź do [zarządzania aplikacjami](https://apps.twitter.com/)w usłudze Twitter.

1. Wybierz pozycję **Utwórz nową aplikację**.

1. Podaj następujące wartości:

    |Właściwość |Value |
    |---|---|
    |Name|Podaj nazwę aplikacji. Wartość użyta w tym samouczku to **HDISparkStreamApp0423**. Ta nazwa musi być unikatową nazwą.|
    |Opis|Podaj krótki opis aplikacji. Wartość używana w tym samouczku to **prosta aplikacja przesyłania strumieniowego HDInsight Spark**.|
    |Witryna internetowa|Podaj witrynę sieci Web aplikacji. Nie musi być prawidłową witryną sieci Web.  Ta wartość jest `http://www.contoso.com`używana w tym samouczku.|
    |Adres URL wywołania zwrotnego|Możesz pozostawić to pole puste.|

1. Wybierz opcję **tak, znam i Akceptuję Umowę dewelopera w serwisie Twitter**, a następnie wybierz pozycję **Utwórz aplikację w usłudze Twitter**.

1. Wybierz kartę **klucze i tokeny dostępu** .

1. Na końcu strony wybierz pozycję **Utwórz mój token dostępu** .

1. Zapisz następujące wartości na stronie.  Te wartości są potrzebne w dalszej części tego samouczka:

    - **Klucz klienta (klucz interfejsu API)**    
    - **Wpis tajny klienta (klucz tajny interfejsu API)**  
    - **Token dostępu**
    - **Wpis tajny tokenu dostępu**   

## <a name="create-an-azure-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Azure Event Hubs

To centrum zdarzeń służy do przechowywania tweetów.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

2. Z menu po lewej stronie wybierz pozycję **wszystkie usługi**.  

3. W obszarze **Internet rzeczy**wybierz pozycję **Event Hubs**. 

    ![Przykład tworzenia centrum zdarzeń na potrzeby przesyłania strumieniowego Spark](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Przykład tworzenia centrum zdarzeń na potrzeby przesyłania strumieniowego Spark")

4. Wybierz pozycję **+ Dodaj**.

5. Wprowadź następujące wartości dla nowej przestrzeni nazw Event Hubs:

    |Właściwość |Value |
    |---|---|
    |Name|Wprowadź nazwę centrum zdarzeń.  Wartość użyta w tym samouczku to **myeventhubns20180403**.|
    |Warstwa cenowa|Wybierz opcję **Standardowa**.|
    |Subscription|Wybierz odpowiednią subskrypcję.|
    |Resource group|Wybierz istniejącą grupę zasobów z listy rozwijanej lub wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę zasobów.|
    |Location|Wybierz tę samą **lokalizację** co Klaster Apache Spark w usłudze HDInsight, aby zmniejszyć opóźnienia i koszty.|
    |Włącz funkcję autodostrajania (opcjonalnie) |Automatyczne rozszerzanie automatycznie skaluje liczbę jednostek przepływności przypisanych do przestrzeni nazw Event Hubs, gdy ruch przekracza pojemność przypisanych do niej jednostek przepływności.  |
    |Największa liczba jednostek przepływności, które są opcjonalne (opcjonalnie)|Ten suwak zostanie wyświetlony tylko w przypadku zaznaczenia pola wyboru **Włącz autorozdęcie**.  |

    ![Podaj nazwę centrum zdarzeń dla przykładu przesyłania strumieniowego Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Podaj nazwę centrum zdarzeń dla przykładu przesyłania strumieniowego Spark")

6. Wybierz pozycję **Utwórz** , aby utworzyć przestrzeń nazw.  Wdrożenie zostanie ukończone w ciągu kilku minut.

## <a name="create-an-azure-event-hub"></a>Tworzenie centrum zdarzeń platformy Azure
Utwórz centrum zdarzeń po wdrożeniu Event Hubs przestrzeni nazw.  Z poziomu portalu:

1. Z menu po lewej stronie wybierz pozycję **wszystkie usługi**.  

1. W obszarze **Internet rzeczy**wybierz pozycję **Event Hubs**.  

1. Wybierz z listy przestrzeń nazw Event Hubs.  

1. Na stronie **przestrzeń nazw Event Hubs** wybierz pozycję **+ centrum zdarzeń**.  
1. Wprowadź następujące wartości na stronie **Tworzenie centrum zdarzeń** :

    - **Nazwa**: Nadaj nazwę centrum zdarzeń. 
 
    - **Liczba partycji**: 10.  

    - **Przechowywanie komunikatów**: 1.   
   
      ![Podaj szczegóły centrum zdarzeń dla przykładu przesyłania strumieniowego Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Podaj szczegóły centrum zdarzeń dla przykładu przesyłania strumieniowego Spark")

1. Wybierz pozycję **Utwórz**.  Wdrożenie powinno zakończyć się w ciągu kilku sekund, a następnie nastąpi powrót do strony Event Hubs przestrzeni nazw.

1. W obszarze **Ustawienia**wybierz pozycję **zasady dostępu**współdzielonego.

1. Wybierz **RootManageSharedAccessKey**.
    
     ![Ustawianie zasad centrum zdarzeń dla przykładu przesyłania strumieniowego Spark](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Ustawianie zasad centrum zdarzeń dla przykładu przesyłania strumieniowego Spark")

1. Zapisz wartości **klucza podstawowego** i **parametrów połączenia — klucz podstawowy** do użycia w dalszej części samouczka.

     ![Wyświetl klucze zasad centrum zdarzeń dla przykładu przesyłania strumieniowego Spark](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Wyświetl klucze zasad centrum zdarzeń dla przykładu przesyłania strumieniowego Spark")


## <a name="send-tweets-to-the-event-hub"></a>Wyślij tweety do centrum zdarzeń

Utwórz Notes Jupyter i nadaj mu nazwę **SendTweetsToEventHub**. 

1. Uruchom następujący kod, aby dodać zewnętrzne biblioteki Apache Maven:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. Edytuj Poniższy kod, zastępując `<Event hub name>`, `<Event hub namespace connection string>`, `<CONSUMER KEY>` `<CONSUMER SECRET>`,, i`<TOKEN SECRET>` z odpowiednimi wartościami. `<ACCESS TOKEN>` Uruchom edytowany kod, aby wysłać tweety do centrum zdarzeń:

    ```scala
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

3. Otwórz centrum zdarzeń w Azure Portal.  Na stronie **Przegląd**są wyświetlane wykresy przedstawiające komunikaty wysyłane do centrum zdarzeń.

## <a name="read-tweets-from-the-event-hub"></a>Odczytywanie tweetów z centrum zdarzeń

Utwórz inny Notes Jupyter i nadaj mu nazwę **ReadTweetsFromEventHub**. 

1. Uruchom następujący kod, aby dodać zewnętrzną bibliotekę Apache Maven:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13"}}
    ```

2. Edytuj Poniższy kod, zastępując `<Event hub name>`go i `<Event hub namespace connection string>` odpowiednimi wartościami. Uruchom edytowany kod, aby przeczytać tweety z centrum zdarzeń:

    ```scala
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build
    
    val customEventhubParameters = EventHubsConf(connStr).setMaxEventsPerTrigger(5)
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

Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage lub Azure Data Lake Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Jeśli planujesz pracę w następnym samouczku natychmiast, możesz chcieć zachować klaster, w przeciwnym razie, a następnie usunąć klaster.

Otwórz klaster w witrynie Azure Portal, a następnie wybierz pozycję **Usuń**.

![Usuwanie klastra usługi HDInsight w witrynie Azure Portal](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Usuwanie klastra usługi HDInsight")

Dodatkowo możesz wybrać nazwę grupy zasobów, aby otworzyć stronę grupy zasobów, a następnie wybrać pozycję **Usuń grupę zasobów**. Usunięcie grupy zasobów powoduje usunięcie zarówno klastra Spark w usłudze HDInsight, jak i domyślnego konta magazynu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia Apache Spark aplikacji przesyłania strumieniowego w celu wysyłania tweetów do centrum zdarzeń platformy Azure i utworzenia innej aplikacji w celu odczytania tweetów z centrum zdarzeń.  Przejdź do następnego artykułu, aby zobaczyć, jak można utworzyć aplikację uczenia maszynowego.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji do uczenia maszynowego](./apache-spark-ipython-notebook-machine-learning.md)
