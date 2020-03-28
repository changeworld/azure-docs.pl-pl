---
title: 'Samouczek: Wykrywanie anomalii podczas przesyłania strumieniowego danych przy użyciu usługi Azure Databricks'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsu API detektora anomalii i usługi Azure Databricks do monitorowania anomalii w danych.
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.openlocfilehash: e0df0773daf8f9be21ac70d8390013adfd93483a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78402672"
---
# <a name="tutorial-anomaly-detection-on-streaming-data-using-azure-databricks"></a>Samouczek: Wykrywanie anomalii podczas przesyłania strumieniowego danych przy użyciu usługi Azure Databricks

[Usługa Azure Databricks](https://azure.microsoft.com/services/databricks/) to szybka, łatwa i oparta na współpracy usługa analityczna oparta na platformie Apache Spark. Interfejs API detektora anomalii, część usługi Azure Cognitive Services, umożliwia monitorowanie danych szeregów czasowych. Ten samouczek służy do uruchamiania wykrywania anomalii na strumieniu danych w czasie zbliżonym do rzeczywistego przy użyciu usługi Azure Databricks. Będziesz pozyskiwać dane twitter przy użyciu usługi Azure Event Hubs i zaimportować je do usługi Azure Databricks przy użyciu łącznika usługi Spark Event Hubs. Następnie użyjesz interfejsu API do wykrywania anomalii na danych przesyłanych strumieniowo. 

Poniższa ilustracja przedstawia przepływ aplikacji:

![Usługa Azure Databricks z centrami zdarzeń i usługami Cognitive Services](../media/tutorials/databricks-cognitive-services-tutorial.png "Usługa Azure Databricks z centrami zdarzeń i usługami Cognitive Services")

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Tworzenie obszaru roboczego usługi Azure Databricks
> * Tworzenie klastra Spark w usłudze Azure Databricks
> * Tworzenie aplikacji usługi Twitter w celu uzyskania dostępu do danych strumienia
> * Tworzenie notesów w usłudze Azure Databricks
> * Dołączanie bibliotek usługi Event Hubs oraz interfejsu API usługi Twitter
> * Tworzenie zasobu detektora anomalii i pobieranie klucza dostępu
> * Wysyłanie tweetów do usługi Event Hubs
> * Odczytywanie tweetów z usługi Event Hubs
> * Uruchom wykrywanie anomalii na tweetach

> [!Note]
> * W tym samouczku przedstawiono podejście do implementowania architektury zalecanego [rozwiązania](https://azure.microsoft.com/solutions/architecture/anomaly-detector-process/) dla interfejsu API detektora anomalii.
> * Tego samouczka nie można ukończyć z bezpłatną wersję próbną interfejsu API detektora anomalii lub usługi Azure Databricks. 

Utwórz [subskrypcję platformy Azure,](https://azure.microsoft.com/free/) jeśli jej nie masz.

## <a name="prerequisites"></a>Wymagania wstępne

- [Obszar nazw usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) i centrum zdarzeń.

- [Ciąg połączenia,](../../../event-hubs/event-hubs-get-connection-string.md) aby uzyskać dostęp do obszaru nazw Centrum zdarzeń. Parametry połączenia powinny mieć format podobny do:

    `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`. 

- Nazwa zasad dostępu współdzielonego i klucz zasad dla centrów zdarzeń.

Zobacz Szybki start usługi Azure Event [Hubs,](../../../event-hubs/event-hubs-create.md) aby uzyskać informacje na temat tworzenia obszaru nazw i centrum zdarzeń.

## <a name="create-an-azure-databricks-workspace"></a>Tworzenie obszaru roboczego usługi Azure Databricks

W tej sekcji utworzysz obszar roboczy usługi Azure Databricks przy użyciu [portalu Azure.](https://portal.azure.com/)

1. W witrynie Azure portal wybierz pozycję **Utwórz zasób** > **Analytics** > **Azure Databricks**.

    ![Databricks w witrynie Azure portal](../media/tutorials/azure-databricks-on-portal.png "Databricks w witrynie Azure portal")

3. W obszarze **Usługa Azure Databricks** podaj następujące wartości, aby utworzyć obszar roboczy usługi Databricks:


    |Właściwość  |Opis  |
    |---------|---------|
    |**Nazwa obszaru roboczego**     | Podaj nazwę obszaru roboczego usługi Databricks.        |
    |**Subskrypcja**     | Z listy rozwijanej wybierz subskrypcję platformy Azure.        |
    |**Grupa zasobów**     | Określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej grupy. Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../../../azure-resource-manager/management/overview.md). |
    |**Lokalizacja**     | Wybierz **wschodnie stany USA 2** lub jeden z innych dostępnych regionów. Zobacz [usługi platformy Azure dostępne według regionu,](https://azure.microsoft.com/regions/services/) aby uzyskać dostępność regionu.        |
    |**Warstwa cenowa**     |  Wybierz warstwę **Standardowa** lub **Premium**. NIE **wybieraj opcji Próba**. Aby uzyskać więcej informacji o tych warstwach, zobacz [stronę usługi Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Wybierz **pozycję Utwórz**.

4. Tworzenie obszaru roboczego trwa kilka minut. 

## <a name="create-a-spark-cluster-in-databricks"></a>Tworzenie klastra Spark w usłudze Databricks

1. W witrynie Azure Portal przejdź do utworzonego obszaru roboczego usługi Databricks, a następnie wybierz pozycję **Uruchom obszar roboczy**.

2. Nastąpi przekierowanie do portalu usługi Azure Databricks. W portalu wybierz pozycję **Nowy klaster**.

    ![Databricks na platformie Azure](../media/tutorials/databricks-on-azure.png "Databricks na platformie Azure")

3. Na stronie **Nowy klaster** podaj wartości, aby utworzyć klaster.

    ![Tworzenie klastra Databricks Spark na platformie Azure](../media/tutorials/create-databricks-spark-cluster.png "Tworzenie klastra Databricks Spark na platformie Azure")

    Zaakceptuj pozostałe wartości domyślne poza następującymi:

   * Wprowadź nazwę klastra.
   * W tym artykule utwórz klaster z czasem wykonywania **5.2.** NIE wybieraj środowiska uruchomieniowego **5.3.**
   * Upewnij się, że zaznaczono pole wyboru **Zakończ po \_ \_ minutach braku aktywności.** Podaj czas trwania (w minutach), aby zakończyć klaster, jeśli klaster nie jest używany.

     Wybierz pozycję **Utwórz klaster**. 
4. Tworzenie klastra trwa kilka minut. Po uruchomieniu klastra możesz dołączyć do niego notesy i uruchamiać zadania Spark.

## <a name="create-a-twitter-application"></a>Tworzenie aplikacji usługi Twitter

Aby otrzymywać strumień tweetów, musisz utworzyć aplikację w usłudze Twitter. Wykonaj poniższe kroki, aby utworzyć aplikację usługi Twitter i zarejestrować wartości potrzebne do ukończenia tego samouczka.

1. W przeglądarce internetowej przejdź do strony [Twitter Application Management (Zarządzanie aplikacjami usługi Twitter)](https://apps.twitter.com/) i wybierz pozycję **Create New App (Utwórz nową aplikację)**.

    ![Tworzenie aplikacji Twitter](../media/tutorials/databricks-create-twitter-app.png "Tworzenie aplikacji Twitter")

2. Na stronie **Create an application (Tworzenie aplikacji)** podaj szczegóły nowej aplikacji, a następnie wybierz pozycję **Create your Twitter application (Utwórz aplikację usługi Twitter)**.

    ![Szczegóły aplikacji na Twitterze](../media/tutorials/databricks-provide-twitter-app-details.png "Szczegóły aplikacji na Twitterze")

3. Na stronie aplikacji wybierz kartę **Keys and Access Tokens (Klucze i tokeny dostępu)**, a następnie skopiuj wartości pól **Consumer Key (Klucz klienta)** i **Consumer Secret (Wpis tajny klienta)**. Zaznacz również pole **Create my access token (Utwórz mój token dostępu)**, aby wygenerować tokeny dostępu. Skopiuj wartości pól **Access Token (Token dostępu)** i **Access Token Secret (Klucz tajny tokenu dostępu)**.

    ![Szczegóły aplikacji na Twitterze](../media/tutorials/twitter-app-key-secret.png "Szczegóły aplikacji na Twitterze")

Zapisz wartości dotyczące aplikacji usługi Twitter. Będą one potrzebne w dalszej części tego samouczka.

## <a name="attach-libraries-to-spark-cluster"></a>Dołączanie bibliotek do klastra Spark

W tym samouczku tweety są wysyłane do usługi Event Hubs za pomocą interfejsów API usługi Twitter. Ponadto dane są odczytywane i zapisywane w usłudze Azure Event Hubs za pomocą [łącznika Event Hubs platformy Apache Spark](https://github.com/Azure/azure-event-hubs-spark). Aby korzystać z tych interfejsów API w ramach klastra, dodaj je jako biblioteki do usługi Azure Databricks, a następnie je skojarz z klastrem Spark. Poniższe instrukcje pokazują, jak dodać biblioteki do folderu **udostępnionego** w obszarze roboczym.

1. W obszarze roboczym usługi Azure Databricks wybierz pozycję **Obszar roboczy**, a następnie kliknij prawym przyciskiem myszy pozycję **Udostępnione**. Z menu kontekstowego wybierz polecenie **Utwórz** > **bibliotekę**.

   ![Okno dialogowe Dodawanie biblioteki](../media/tutorials/databricks-add-library-option.png "Okno dialogowe Dodawanie biblioteki")

2. Na stronie Nowa biblioteka wybierz pozycję **Maven** . **Maven** W przypadku **współrzędnych**wprowadź współrzędne pakietu, który chcesz dodać. Oto współrzędne Maven bibliotek używanych w tym samouczku:

   * Łącznik Event Hubs platformy Spark — `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * Interfejs API usługi Twitter — `org.twitter4j:twitter4j-core:4.0.7`

     ![Podaj współrzędne Mavena](../media/tutorials/databricks-eventhub-specify-maven-coordinate.png "Podaj współrzędne Mavena")

3. Wybierz **pozycję Utwórz**.

4. Wybierz folder, do którego dodano bibliotekę, a następnie wybierz nazwę biblioteki.

    ![Wybierz bibliotekę, aby dodać](../media/tutorials/select-library.png "Wybierz bibliotekę, aby dodać")

5. Jeśli na stronie biblioteki nie ma klastra, wybierz **pozycję Klastry** i uruchom utworzony klaster. Poczekaj, aż stan pokaże "Uruchomiony", a następnie wróć do strony biblioteki.
Na stronie biblioteki wybierz klaster, w którym chcesz korzystać z biblioteki, a następnie wybierz pozycję **Zainstaluj**. Po pomyślnym skojarzeniu biblioteki z klastrem stan natychmiast zmieni się na **Zainstalowany**.

    ![Instalowanie biblioteki w klastrze](../media/tutorials/databricks-library-attached.png "Instalowanie biblioteki w klastrze")

6. Powtórz te kroki dla pakietu Twitter: `twitter4j-core:4.0.7`.

## <a name="get-a-cognitive-services-access-key"></a>Pobieranie klucza dostępu usług Cognitive Services

W tym samouczku używasz [interfejsów API detektora anomalii usług Wirtualnych usług Azure](../overview.md) do uruchamiania wykrywania anomalii w strumieniu tweetów w czasie zbliżonym do rzeczywistego. Przed użyciem interfejsów API należy utworzyć zasób Wykrywania anomalii na platformie Azure i pobrać klucz dostępu, aby użyć interfejsów API detektora anomalii.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

2. Wybierz pozycję **+ Utwórz zasób**.

3. W obszarze Azure Marketplace wybierz **pozycję AI + uczenie** > maszynowe**Zobacz wszystkie** > **usługi Cognitive Services — więcej** > **wykrywaczy anomalii**. Możesz też użyć [tego łącza,](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) aby przejść bezpośrednio do okna dialogowego **Tworzenie.**

    ![Tworzenie zasobu detektora anomalii](../media/tutorials/databricks-cognitive-services-anomaly-detector.png "Tworzenie zasobu detektora anomalii")

4. W oknie dialogowym **Tworzenie** podaj następujące wartości:

    |Wartość |Opis  |
    |---------|---------|
    |Nazwa     | Nazwa zasobu Wykrywania anomalii.        |
    |Subskrypcja     | Subskrypcja platformy Azure, z nią skojarzony.        |
    |Lokalizacja     | Lokalizacja platformy Azure.        |
    |Warstwa cenowa     | Warstwa cenowa dla usługi. Aby uzyskać więcej informacji na temat cen detektora anomalii, zobacz [stronę cennika](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/).        |
    |Grupa zasobów     | Określ, czy chcesz utworzyć nową grupę zasobów, czy wybrać istniejącą grupę.        |


     Wybierz **pozycję Utwórz**.

5. Po utworzeniu zasobu na karcie **Przegląd** skopiuj i zapisz adres URL **punktu końcowego,** jak pokazano na zrzucie ekranu. Następnie wybierz pozycję **Pokaż klawisze dostępu**.

    ![Pokaż klucze dostępu](../media/tutorials/cognitive-services-get-access-keys.png "Pokaż klucze dostępu")

6. W obszarze **Klawisze**wybierz ikonę kopiowania względem klucza, którego chcesz użyć. Zapisz klucz dostępu.

    ![Kopiowanie kluczy dostępu](../media/tutorials/cognitive-services-copy-access-keys.png "Kopiowanie kluczy dostępu")

## <a name="create-notebooks-in-databricks"></a>Tworzenie notesów w usłudze Databricks

W tej sekcji w obszarze roboczym usługi Databricks zostaną utworzone dwa notesy o następujących nazwach:

- **SendTweetsToEventHub** — notes producenta służący do pobierania tweetów z usługi Twitter i przesyłania ich w strumieniu do usługi Event Hubs.
- **AnalyzeTweetsFromEventHub** — notes konsumenta, którego używasz do odczytywania tweetów z centrów zdarzeń i uruchamiania wykrywania anomalii.

1. W obszarze roboczym usługi Azure Databricks wybierz **obszar roboczy** z lewego okienka. Z listy rozwijanej **Obszar roboczy** wybierz pozycje **Utwórz** i **Notes**.

    ![Tworzenie notesu w umiań danych](../media/tutorials/databricks-create-notebook.png "Tworzenie notesu w umiań danych")

2. W oknie dialogowym **Tworzenie notesu** wprowadź **sendtweetsToEventHub** jako nazwę, wybierz **scala** jako język i wybierz utworzony wcześniej klaster Spark.

    ![Tworzenie notesu w umiań danych](../media/tutorials/databricks-notebook-details.png "Tworzenie notesu w umiań danych")

    Wybierz **pozycję Utwórz**.

3. Powtórz te kroki, aby utworzyć notes **AnalyzeTweetsFromEventHub**.

## <a name="send-tweets-to-event-hubs"></a>Wysyłanie tweetów do usługi Event Hubs

W notesie **SendTweetsToEventHub** wklej następujący kod i zastąp symbol zastępczy utworzonymi wcześniej wartościami przestrzeni nazw usługi Event Hubs i aplikacji usługi Twitter. Ten notes wyodrębnia czas tworzenia i liczbę "Lubię to" z tweetów za pomocą słowa kluczowego "Azure" i przesyła je strumieniowo jako zdarzenia do centrów zdarzeń w czasie rzeczywistym.

```scala
//
// Send Data to Eventhub
//

import scala.collection.JavaConverters._
import com.microsoft.azure.eventhubs._
import java.util.concurrent._
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import java.util.Date
import scala.util.control._
import twitter4j._
import twitter4j.TwitterFactory
import twitter4j.Twitter
import twitter4j.conf.ConfigurationBuilder

// Event Hub Config
val namespaceName = "[Placeholder: EventHub namespace]"
val eventHubName = "[Placeholder: EventHub name]"
val sasKeyName = "[Placeholder: EventHub access key name]"
val sasKey = "[Placeholder: EventHub access key key]"
val connStr = new ConnectionStringBuilder()
  .setNamespaceName(namespaceName)
  .setEventHubName(eventHubName)
  .setSasKeyName(sasKeyName)
  .setSasKey(sasKey)

// Connect to the Event Hub
val pool = Executors.newScheduledThreadPool(1)
val eventHubClient = EventHubClient.create(connStr.toString(), pool)

def sendEvent(message: String) = {
  val messageData = EventData.create(message.getBytes("UTF-8"))
  eventHubClient.get().send(messageData)
  System.out.println("Sent event: " + message + "\n")
}

case class MessageBody(var timestamp: Date, var favorite: Int)
val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").create()

val twitterConsumerKey = "[Placeholder: Twitter consumer key]"
val twitterConsumerSecret = "[Placeholder: Twitter consumer seceret]"
val twitterOauthAccessToken = "[Placeholder: Twitter oauth access token]"
val twitterOauthTokenSecret = "[Placeholder: Twitter oauth token secret]"

val cb = new ConfigurationBuilder()
cb.setDebugEnabled(true)
  .setOAuthConsumerKey(twitterConsumerKey)
  .setOAuthConsumerSecret(twitterConsumerSecret)
  .setOAuthAccessToken(twitterOauthAccessToken)
  .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

val twitterFactory = new TwitterFactory(cb.build())
val twitter = twitterFactory.getInstance()

// Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!

val query = new Query(" #Azure ")
query.setCount(100)
query.lang("en")

var finished = false
var maxStatusId = Long.MinValue
var preMaxStatusId = Long.MinValue
val innerLoop = new Breaks
while (!finished) {
  val result = twitter.search(query)
  val statuses = result.getTweets()
  var lowestStatusId = Long.MaxValue
  innerLoop.breakable {
    for (status <- statuses.asScala) {
      if (status.getId() <= preMaxStatusId) {
        preMaxStatusId = maxStatusId
        innerLoop.break
      }
      if(!status.isRetweet()) {
        sendEvent(gson.toJson(new MessageBody(status.getCreatedAt(), status.getFavoriteCount())))
      }
      lowestStatusId = Math.min(status.getId(), lowestStatusId)
      maxStatusId = Math.max(status.getId(), maxStatusId)
    }
  }
  
  if (lowestStatusId == Long.MaxValue) {
    preMaxStatusId = maxStatusId
  }
  Thread.sleep(10000)
  query.setMaxId(lowestStatusId - 1)
}

// Close connection to the Event Hub
eventHubClient.get().close()
pool.shutdown()
```

Aby uruchomić notes, naciśnij klawisze **SHIFT + ENTER**. Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu. Każde zdarzenie w danych wyjściowych jest kombinacją sygnatury czasowej i liczby "Like" po spożyciu do centrów zdarzeń.

    Sent event: {"timestamp":"2019-04-24T09:39:40.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:38:48.000Z","favorite":1}

    Sent event: {"timestamp":"2019-04-24T09:38:36.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:27.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:00.000Z","favorite":2}

    Sent event: {"timestamp":"2019-04-24T09:31:11.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:15.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:02.000Z","favorite":1}

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Odczytywanie tweetów z usługi Event Hubs

W **notesie AnalyzeTweetsFromEventHub** wklej następujący kod i zastąp symbol zastępczy wartościami zasobu Detektor anomalii, który został utworzony wcześniej. Ten notes umożliwia odczyt tweetów przesłanych strumieniowo do usługi Event Hubs przy użyciu notesu **SendTweetsToEventHub**.

Najpierw napisz klienta, aby wywołać detektor anomalii. 
```scala

//
// Anomaly Detection Client
//

import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp

import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection

case class Point(var timestamp: Timestamp, var value: Double)
case class Series(var series: Array[Point], var maxAnomalyRatio: Double, var sensitivity: Int, var granularity: String)
case class AnomalySingleResponse(var isAnomaly: Boolean, var isPositiveAnomaly: Boolean, var isNegativeAnomaly: Boolean, var period: Int, var expectedValue: Double, var upperMargin: Double, var lowerMargin: Double, var suggestedWindow: Int)
case class AnomalyBatchResponse(var expectedValues: Array[Double], var upperMargins: Array[Double], var lowerMargins: Array[Double], var isAnomaly: Array[Boolean], var isPositiveAnomaly: Array[Boolean], var isNegativeAnomaly: Array[Boolean], var period: Int)

object AnomalyDetector extends Serializable {

  // Cognitive Services API connection settings
  val subscriptionKey = "[Placeholder: Your Anomaly Detector resource access key]"
  val endpoint = "[Placeholder: Your Anomaly Detector resource endpoint]"
  val latestPointDetectionPath = "/anomalydetector/v1.0/timeseries/last/detect"
  val batchDetectionPath = "/anomalydetector/v1.0/timeseries/entire/detect";
  val latestPointDetectionUrl = new URL(endpoint + latestPointDetectionPath)
  val batchDetectionUrl = new URL(endpoint + batchDetectionPath)
  val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").setPrettyPrinting().create()

  def getConnection(path: URL): HttpsURLConnection = {
    val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
    connection.setRequestMethod("POST")
    connection.setRequestProperty("Content-Type", "text/json")
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey)
    connection.setDoOutput(true)
    return connection
  }

  // Handles the call to Cognitive Services API.
  def processUsingApi(request: String, path: URL): String = {
    println(request)
    val encoded_text = request.getBytes("UTF-8")
    val connection = getConnection(path)
    val wr = new DataOutputStream(connection.getOutputStream())
    wr.write(encoded_text, 0, encoded_text.length)
    wr.flush()
    wr.close()

    val response = new StringBuilder()
    val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
    var line = in.readLine()
    while (line != null) {
      response.append(line)
      line = in.readLine()
    }
    in.close()
    return response.toString()
  }

  // Calls the Latest Point Detection API.
  def detectLatestPoint(series: Series): Option[AnomalySingleResponse] = {
    try {
      println("Process Timestamp: " + series.series.apply(series.series.length-1).timestamp.toString + ", size: " + series.series.length)
      val response = processUsingApi(gson.toJson(series), latestPointDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalySingleResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        e.printStackTrace()
        return None
      }
    }
  }

  // Calls the Batch Detection API.
  def detectBatch(series: Series): Option[AnomalyBatchResponse] = {
    try {
      val response = processUsingApi(gson.toJson(series), batchDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalyBatchResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        return None
      }
    }
  }
}
```

Aby uruchomić notes, naciśnij klawisze **SHIFT + ENTER**. Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu.

    import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
    import java.net.URL
    import java.sql.Timestamp
    import com.google.gson.{Gson, GsonBuilder, JsonParser}
    import javax.net.ssl.HttpsURLConnection
    defined class Point
    defined class Series
    defined class AnomalySingleResponse
    defined class AnomalyBatchResponse
    defined object AnomalyDetector

Następnie przygotuj funkcję agregacji dla przyszłego użycia.
```scala
//
// User Defined Aggregation Function for Anomaly Detection
//

import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap

class AnomalyDetectorAggregationFunction extends UserDefinedAggregateFunction {
  override def inputSchema: StructType = new StructType().add("timestamp", TimestampType).add("value", FloatType)
  
  override def bufferSchema: StructType = new StructType().add("point", MapType(TimestampType, FloatType))
  
  override def dataType: DataType = BooleanType
  
  override def deterministic: Boolean = false
  
  override def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = Map()
  }
  
  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    buffer(0) = buffer.getAs[Map[java.sql.Timestamp, Float]](0) + (input.getTimestamp(0) -> input.getFloat(1))
  }
  
  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getAs[Map[java.sql.Timestamp, Float]](0) ++ buffer2.getAs[Map[java.sql.Timestamp, Float]](0)
  }
  
  override def evaluate(buffer: Row): Any = {
    val points = buffer.getAs[Map[java.sql.Timestamp, Float]](0)
    if (points.size > 12) {
      val sorted_points = ListMap(points.toSeq.sortBy(_._1.getTime):_*)
      var detect_points: List[Point] = List()
      sorted_points.keys.foreach {
        key => detect_points = detect_points :+ new Point(key, sorted_points(key))
      }
      
      
      // 0.25 is maxAnomalyRatio. It represents 25%, max anomaly ratio in a time series.
      // 95 is the sensitivity of the algorithms.
      // Check Anomaly detector API reference (https://aka.ms/anomaly-detector-rest-api-ref)
      
      val series: Series = new Series(detect_points.toArray, 0.25, 95, "hourly")
      val response: Option[AnomalySingleResponse] = AnomalyDetector.detectLatestPoint(series)
      if (!response.isEmpty) {
        return response.get.isAnomaly
      }
    }
    
    return None
  }
}

```

Aby uruchomić notes, naciśnij klawisze **SHIFT + ENTER**. Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu.

    import org.apache.spark.sql.Row
    import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
    import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
    import scala.collection.immutable.ListMap
    defined class AnomalyDetectorAggregationFunction

Następnie załaduj dane z Centrum zdarzeń w celu wykrycia anomalii. Zastąp symbol zastępczy wartościami dla usługi Azure Event Hubs utworzonymi wcześniej.

```scala
//
// Load Data from Eventhub
//

import org.apache.spark.eventhubs._
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

val connectionString = ConnectionStringBuilder("[Placeholder: EventHub namespace connection string]")
  .setEventHubName("[Placeholder: EventHub name]")
  .build

val customEventhubParameters =
  EventHubsConf(connectionString)
  .setConsumerGroup("$Default")
  .setMaxEventsPerTrigger(100)

val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

val messages =
  incomingStream
  .withColumn("enqueuedTime", $"enqueuedTime".cast(TimestampType))
  .withColumn("body", $"body".cast(StringType))
  .select("enqueuedTime", "body")

val bodySchema = new StructType().add("timestamp", TimestampType).add("favorite", IntegerType)

val msgStream = messages.select(from_json('body, bodySchema) as 'fields).select("fields.*")

msgStream.printSchema

display(msgStream)

```

Dane wyjściowe przypominają teraz poniższy obraz. Należy zauważyć, że data w tabeli może się różnić od daty w tym samouczku, ponieważ dane są w czasie rzeczywistym.
![Załaduj dane z Centrum zdarzeń](../media/tutorials/load-data-from-eventhub.png "Ładowanie danych z Centrum zdarzeń")

Dane z usługi Azure Event Hubs zostały przesłane strumieniowo do usługi Azure Databricks niemal w czasie rzeczywistym za pomocą łącznika usługi Event Hubs dla platformy Apache Spark. Aby uzyskać więcej informacji na temat sposobu korzystania z łącznika usługi Event Hubs dla platformy Spark, zobacz [dokumentację łącznika](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs).



## <a name="run-anomaly-detection-on-tweets"></a>Uruchom wykrywanie anomalii na tweetach

W tej sekcji można uruchomić wykrywanie anomalii na tweets odebrane przy użyciu interfejsu API detektora anomalii. Fragmenty kodu należy dodać do tego samego notesu **AnalyzeTweetsFromEventHub**.

Aby wykonać wykrywanie anomalii, najpierw należy agregować liczbę metryk według godziny.
```scala
//
// Aggregate Metric Count by Hour
//

// If you want to change granularity, change the groupBy window. 
val groupStream = msgStream.groupBy(window($"timestamp", "1 hour"))
  .agg(avg("favorite").alias("average"))
  .withColumn("groupTime", $"window.start")
  .select("groupTime", "average")

groupStream.printSchema

display(groupStream)
```
Dane wyjściowe przypominają teraz następujące fragmenty kodu.
```
groupTime                       average
2019-04-23T04:00:00.000+0000    24
2019-04-26T19:00:00.000+0000    47.888888888888886
2019-04-25T12:00:00.000+0000    32.25
2019-04-26T09:00:00.000+0000    63.4
...
...

```

Następnie pobierz zagregowany wynik wyjściowy do delta. Ponieważ wykrywanie anomalii wymaga dłuższego okna historii, używamy Delta do przechowywania danych historii dla punktu, który chcesz wykryć. Zastąp "[Nazwa tabeli Placeholder:]" kwalifikowaną nazwą tabeli Delta (na przykład "tweety"). Zastąp "[Placeholder: nazwa folderu dla punktów kontrolnych]" wartością ciągu, która jest unikatowa przy każdym uruchomieniu tego kodu (na przykład "etl-from-eventhub-20190605").
Aby dowiedzieć się więcej o Delta Lake na platformie Azure Databricks, zapoznaj się z [Przewodnikiem po jeziorze Delta](https://docs.azuredatabricks.net/delta/index.html)


```scala
//
// Output Aggregation Result to Delta
//

groupStream.writeStream
  .format("delta")
  .outputMode("complete")
  .option("checkpointLocation", "/delta/[Placeholder: table name]/_checkpoints/[Placeholder: folder name for checkpoints]")
  .table("[Placeholder: table name]")

```

Zastąp "[Nazwa tabeli zastępczej:]" tą samą nazwą tabeli Delta, którą wybrałeś powyżej.
```scala
//
// Show Aggregate Result
//

val twitterCount = spark.sql("SELECT COUNT(*) FROM [Placeholder: table name]")
twitterCount.show()

val twitterData = spark.sql("SELECT * FROM [Placeholder: table name] ORDER BY groupTime")
twitterData.show(200, false)

display(twitterData)
```
Wyjście jak poniżej: 
```
groupTime                       average
2019-04-08T01:00:00.000+0000    25.6
2019-04-08T02:00:00.000+0000    6857
2019-04-08T03:00:00.000+0000    71
2019-04-08T04:00:00.000+0000    55.111111111111114
2019-04-08T05:00:00.000+0000    2203.8
...
...

```

Teraz zagregowane dane szeregów czasowych są stale pozyskiwania do delta. Następnie można zaplanować zadanie godzinowe, aby wykryć anomalię najnowszego punktu. Zastąp "[Nazwa tabeli zastępczej:]" tą samą nazwą tabeli Delta, którą wybrałeś powyżej.

```scala
//
// Anomaly Detection
//

import java.time.Instant
import java.time.format.DateTimeFormatter
import java.time.ZoneOffset
import java.time.temporal.ChronoUnit

val detectData = spark.read.format("delta").table("[Placeholder: table name]")

// You could use Databricks to schedule an hourly job and always monitor the latest data point
// Or you could specify a const value here for testing purpose
// For example, val endTime = Instant.parse("2019-04-16T00:00:00Z")
val endTime = Instant.now()

// This is when your input of anomaly detection starts. It is hourly time series in this tutorial, so 72 means 72 hours ago from endTime.
val batchSize = 72
val startTime = endTime.minus(batchSize, ChronoUnit.HOURS)

val DATE_TIME_FORMATTER = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss").withZone(ZoneOffset.UTC);

val series = detectData.filter($"groupTime" <= DATE_TIME_FORMATTER.format(endTime))
  .filter($"groupTime" > DATE_TIME_FORMATTER.format(startTime))
  .sort($"groupTime")

series.createOrReplaceTempView("series")

//series.show()

// Register the function to access it
spark.udf.register("anomalydetect", new AnomalyDetectorAggregationFunction)

val adResult = spark.sql("SELECT '" + endTime.toString + "' as datetime, anomalydetect(groupTime, average) as anomaly FROM series")
adResult.show()
```
Wynik jak poniżej: 

```
+--------------------+-------+
|           timestamp|anomaly|
+--------------------+-------+
|2019-04-16T00:00:00Z|  false|
+--------------------+-------+
```

Gotowe. Korzystając z usługi Azure Databricks, pomyślnie przesyłano strumieniowo dane do usługi Azure Event Hubs, zużywano dane strumienia przy użyciu łącznika usługi Event Hubs, a następnie uruchamiasz wykrywanie anomalii na danych przesyłania strumieniowego w czasie zbliżonym do rzeczywistego.
Chociaż w tym samouczku ziarnistość jest co godzinę, zawsze można zmienić ziarnistość, aby spełnić twoje potrzeby. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu tego samouczka możesz zakończyć działanie klastra. Aby to zrobić, w obszarze roboczym usługi Azure Databricks wybierz **klastry** z lewego okienka. W przypadku klastra, który chcesz zakończyć, przesuń kursor nad wielokropek w kolumnie **Akcje,** a następnie wybierz ikonę **Zakończ,** a następnie wybierz pozycję **Potwierdź**.

![Zatrzymywać klaster Databricks](../media/tutorials/terminate-databricks-cluster.png "Zatrzymywać klaster Databricks")

Jeśli nie zakończysz ręcznie klastra, zostanie on automatycznie wstrzymany, pod warunkiem, że podczas tworzenia klastra zostanie zaznaczone pole wyboru **Zakończ po \_ \_ minutach braku aktywności.** W takim przypadku nieaktywny klaster zostanie automatycznie zatrzymany po określonym czasie.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono użycie usługi Azure Databricks w celu przesłania strumienia danych do usługi Azure Event Hubs oraz odczytania tego strumienia z usługi Event Hubs w czasie rzeczywistym. Przejdź do następnego samouczka, aby dowiedzieć się, jak wywołać interfejs API detektora anomalii i wizualizować anomalie przy użyciu pulpitu usługi Power BI. 

> [!div class="nextstepaction"]
>[Wykrywanie anomalii wsadowych za pomocą pulpitu usługi Power BI](batch-anomaly-detection-powerbi.md)
