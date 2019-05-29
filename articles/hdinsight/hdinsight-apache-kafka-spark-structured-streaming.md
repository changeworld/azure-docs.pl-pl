---
title: 'Samouczek: Przesyłanie strumieniowe ze strukturą na platformie Apache Spark za pomocą platformy Apache Kafka — Azure HDInsight'
description: Dowiedz się, jak wymieniać dane z platformą Apache Kafka za pomocą przesyłania strumieniowego platformy Apache Spark. Ten samouczek obejmuje przesyłanie strumieniowe danych z platformy Spark w usłudze HDInsight za pomocą notesu Jupyter.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seodec18
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: hrasheed
ms.openlocfilehash: 51f84234ac35be5f60d1aaa5dac661ad9ce5e0c2
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257903"
---
# <a name="tutorial-use-apache-spark-structured-streaming-with-apache-kafka-on-hdinsight"></a>Samouczek: Używanie strumieni ze strukturą platformy Apache Spark z platformą Kafka w usłudze HDInsight

W tym samouczku przedstawiono sposób użycia [przesyłania strumieniowego platformy Apache Spark](https://spark.apache.org/docs/latest/structured-streaming-programming-guide) do odczytywania i zapisywania danych przy użyciu platformy [Apache Kafka](https://kafka.apache.org/) w usłudze Azure HDInsight.

Przesyłanie strumieniowe ze strukturą platformy Spark to aparat przetwarzania strumieniowego oparta na module Spark SQL. Aparat ten umożliwia wyrażanie obliczeń strumieniowych tak samo jak obliczeń wsadowych na danych statycznych.  

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Szablon usługi Azure Resource Manager do tworzenia klastrów
> * Korzystanie z platformy Spark Structured Streaming with Kafka

Po zakończeniu pracy z tym dokumentem pamiętaj o usunięciu tych klastrów, aby uniknąć naliczania opłat.

## <a name="prerequisites"></a>Wymagania wstępne

* jq, wiersza polecenia procesora w formacie JSON.  Zobacz [ https://stedolan.github.io/jq/ ](https://stedolan.github.io/jq/).

* Znajomość zagadnień dotyczących używania [notesów Jupyter](https://jupyter.org/) za pomocą platformy Spark w usłudze HDInsight. Aby uzyskać więcej informacji, zobacz dokument [Ładowanie danych i uruchamianie zapytań za pomocą platformy Apache Spark w usłudze HDInsight](spark/apache-spark-load-data-run-query.md).

* Znajomość języka programowania [Scala](https://www.scala-lang.org/). Kod używany w tym samouczku jest napisany w języku Scala.

* Znajomość zagadnień dotyczących tworzenia tematów platformy Kafka. Aby uzyskać więcej informacji, zobacz dokument [Przewodnik Szybki start dla platformy Apache Kafka w usłudze HDInsight](kafka/apache-kafka-get-started.md).

> [!IMPORTANT]  
> Kroki przedstawione w tym dokumencie wymagają grupy zasobów platformy Azure, która zawiera zarówno platformę Spark w usłudze HDInsight, jak i platformę Kafka w klastrze usługi HDInsight. Oba klastry znajdują się w usłudze Azure Virtual Network, dzięki czemu klaster Spark może komunikować się bezpośrednio z klastrem Kafka.
> 
> Dla Twojej wygody w tym dokumencie umieszczono link do szablonu, który umożliwia utworzenie wszystkich wymaganych zasobów platformy Azure. 
>
> Aby uzyskać więcej informacji na temat używania usługi HDInsight w sieci wirtualnej, zobacz dokument [Rozszerzanie usługi HDInsight przy użyciu sieci wirtualnej](hdinsight-extend-hadoop-virtual-network.md).

## <a name="structured-streaming-with-apache-kafka"></a>Przesyłanie strumieniowe ze strukturą na platformie Apache Kafka

Przesyłanie strumieniowe ze strukturą platformy Spark to aparat przetwarzania strumieni oparty na aparacie SQL platformy Spark. Podczas korzystania z przesyłania strumieniowego ze strukturą możesz pisać zapytania przesyłania strumieniowego w taki sam sposób, jak zapytania wsadowe.

Poniższe fragmenty kodu demonstrują odczytywanie z platformy Kafka i zapisywanie do pliku. Pierwszy z nich to operacja wsadowa, a drugi to operacja przesyłania strumieniowego:

```scala
// Read a batch from Kafka
val kafkaDF = spark.read.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data and write to file
kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .write
                .format("parquet")
                .option("path","/example/batchtripdata")
                .option("checkpointLocation", "/batchcheckpoint")
                .save()
```

```scala
// Stream from Kafka
val kafkaStreamDF = spark.readStream.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data from the stream and write to file
kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .writeStream
                .format("parquet")
                .option("path","/example/streamingtripdata")
                .option("checkpointLocation", "/streamcheckpoint")
                .start.awaitTermination(30000)
```

W obu fragmentach dane są odczytywane z platformy Kafka i zapisywane do pliku. Różnice między przykładami:

| Wsadowe | Przesyłanie strumieniowe |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

Używa również procesu przesyłania strumieniowego `awaitTermination(30000)`, który zatrzymuje strumień po 30 000 ms. 

Aby używać przesyłania strumieniowego ze strukturą na platformie Kafka, projekt musi mieć zdefiniowaną zależność od pakietu `org.apache.spark : spark-sql-kafka-0-10_2.11`. Wersja tego pakietu powinna być zgodna z wersją platformy Spark w usłudze HDInsight. W przypadku platformy Spark 2.2.0 (dostępnej w usłudze HDInsight 3.6) informacje o zależnościach dla różnych typów projektu możesz znaleźć na stronie [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar).

Dla notesu Jupyter, używany w tym samouczku następującej komórki ładuje tej zależności pakietu:

```
%%configure -f
{
    "conf": {
        "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
        "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
    }
}
```

## <a name="create-the-clusters"></a>Tworzenie klastrów

Platforma Apache Kafka w usłudze HDInsight nie zapewnia dostępu do brokerów Kafka za pośrednictwem publicznego Internetu. Wszystkie elementy, które używają platformy Kafka, muszą znajdować się w tej samej sieci wirtualnej platformy Azure. W tym samouczku zarówno klaster Kafka, jak i klaster Spark znajdują się w tej samej sieci wirtualnej platformy Azure. 

Na poniższym diagramie przedstawiono przepływ komunikacji między platformami Spark i Kafka:

![Diagram przedstawiający klastry Spark i Kafka w sieci wirtualnej platformy Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]  
> Komunikacja usługi Kafka jest ograniczona do sieci wirtualnej. Inne usługi w klastrze, takie jak SSH i Ambari, są dostępne przez Internet. Aby uzyskać więcej informacji o publicznych portach dostępnych z usługą HDInsight, zobacz [Ports and URIs used by HDInsight (Porty i identyfikatory URI używane przez usługę HDInsight)](hdinsight-hadoop-port-settings-for-services.md).

Aby utworzyć usługę Azure Virtual Network, a następnie utworzyć w niej klastry Kafka i Spark, wykonaj następujące kroki:

1. Kliknij poniższy przycisk, aby zalogować się do platformy Azure i otworzyć szablon w witrynie Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Szablon usługi Azure Resource Manager znajduje się tutaj: **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json** .

    Ten szablon umożliwia utworzenie następujących zasobów:

   * Klaster Kafka w usłudze HDInsight 3.6.
   * Klaster Spark 2.2.0 w usłudze HDInsight 3.6.
   * Sieć wirtualna platformy Azure zawierająca klastry usługi HDInsight.

     > [!IMPORTANT]  
     > Używany w tym samouczku notes do przesyłania strumieniowego ze strukturą wymaga platformy Spark 2.2.0 w usłudze HDInsight 3.6. W przypadku użycia starszej wersji platformy Spark w usłudze HDInsight podczas korzystania z notesu wystąpią błędy.

2. Wypełnij pola w sekcji **Dostosowany szablon**, korzystając z poniższych informacji:

    | Ustawienie | Wartość |
    | --- | --- |
    | Subskrypcja | Twoja subskrypcja platformy Azure |
    | Grupa zasobów | Grupa zasobów zawierająca zasoby. |
    | Lokalizacja | Region świadczenia usługi Azure, w którym są tworzone zasoby. |
    | Nazwa klastra Spark | Nazwa klastra Spark. Pierwszych sześć znaków musi być innych niż nazwa klastra Kafka. |
    | Nazwa klastra Kafka | Nazwa klastra Kafka. Pierwszych sześć znaków musi być innych niż nazwa klastra Spark. |
    | Nazwa użytkownika logowania klastra | Nazwa użytkownika będącego administratorem klastrów. |
    | Hasło logowania klastra | Hasło użytkownika będącego administratorem klastrów. |
    | Nazwa użytkownika SSH | Użytkownik SSH tworzony na potrzeby obsługi klastrów. |
    | Hasło SSH | Hasło użytkownika SSH. |
   
    ![Zrzut ekranu przedstawiający dostosowany szablon](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

3. Przeczytaj **Warunki i postanowienia**, a następnie wybierz pozycję **Wyrażam zgodę na powyższe warunki i postanowienia**

4. Na koniec zaznacz opcję **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz pozycję **Kup**. 

> [!NOTE]  
> Tworzenie klastrów może potrwać do 20 minut.

## <a name="use-spark-structured-streaming"></a>Przesyłanie strumieniowe ze strukturą korzystanie z platformy Spark

W tym przykładzie pokazano, jak używać przesyłania strumieniowego ze strukturą platformy Spark z platformą Kafka w HDInsight. Danych używa w podróży taksówek, który jest dostarczany przez New York City.  Zestaw danych używany przez ten notes pochodzi z [danych podróży taksówek zielony 2016](https://data.cityofnewyork.us/Transportation/2016-Green-Taxi-Trip-Data/hvrh-b6nb).

1. Zbieranie informacji o hoście. Korzystanie z programu curl i [jq](https://stedolan.github.io/jq/) polecenia poniżej, aby uzyskać usługi Kafka dozorcy i brokera informacje o hostach. Polecenia te zostały zaprojektowane dla wiersza polecenia Windows, niewielkie różnice, które będą potrzebne w innych środowiskach. Zastąp `KafkaCluster` nazwą Twojego klastra platformy Kafka i `KafkaPassword` z hasło logowania klastra. Ponadto Zastąp `C:\HDI\jq-win64.exe` przy użyciu rzeczywistej ścieżce do instalacji jq. Wprowadź polecenia w wierszu polecenia Windows i Zapisz dane wyjściowe do użycia w kolejnych krokach.

    ```cmd
    set CLUSTERNAME=KafkaCluster
    set PASSWORD=KafkaPassword
    
    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")"
    
    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/KAFKA/components/KAFKA_BROKER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")"
    ```

2. W przeglądarce internetowej połącz się z notesem Jupyter w klastrze Spark. Zastąp element `CLUSTERNAME` w poniższym adresie URL nazwą klastra __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Po wyświetleniu monitu wprowadź nazwę użytkownika klastra (administratora) i hasło użyte podczas tworzenia klastra.

3. Wybierz **nowy > Spark** do utworzenia notesu.

4. Ładowanie pakietów używane przez Notes, wprowadzając następujące informacje w komórce w notesie. Uruchom polecenie przy użyciu **CTRL + ENTER**.

    ```
    %%configure -f
    {
        "conf": {
            "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
            "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
        }
    }
    ```

5. Utworzenie tematu platformy Kafka. Edytuj poniższe polecenie, zastępując `YOUR_ZOOKEEPER_HOSTS` dzięki dozorcy hostować informacji wyodrębnianych w pierwszym kroku. Wprowadź polecenie edytowanych notesu programu Jupyter, aby utworzyć `tripdata` tematu.

    ```scala
    %%bash
    export KafkaZookeepers="YOUR_ZOOKEEPER_HOSTS"

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic tripdata --zookeeper $KafkaZookeepers
    ```

6. Odczytywanie danych podróży taksówek. Wprowadź polecenie, w następnej komórki do pobierania danych podróży taksówek w Nowym Jorku. Dane są ładowane do elementów dataframe, a następnie ramki danych są wyświetlane jako dane wyjściowe komórki.

    ```scala
    import spark.implicits._

    // Load the data from the New York City Taxi data REST API for 2016 Green Taxi Trip Data
    val url="https://data.cityofnewyork.us/resource/pqfs-mqru.json"
    val result = scala.io.Source.fromURL(url).mkString
    
    // Create a dataframe from the JSON data
    val taxiDF = spark.read.json(Seq(result).toDS)
    
    // Display the dataframe containing trip data
    taxiDF.show()
    ```

7. Ustaw informacje o hostach dla brokera platformy Kafka. Zastąp `YOUR_KAFKA_BROKER_HOSTS` o informacje o hostach brokera, które zostały wyodrębnione w kroku 1.  Wprowadź polecenie edytowanych w następnej komórki notesu programu Jupyter.

    ```scala
    // The Kafka broker hosts and topic used to write to Kafka
    val kafkaBrokers="YOUR_KAFKA_BROKER_HOSTS"
    val kafkaTopic="tripdata"
    
    println("Finished setting Kafka broker and topic configuration.")
    ```

8. Wysłać dane do platformy Kafka. W poniższym poleceniu `vendorid` pole jest używane jako wartości klucza dla wiadomości platformy Kafka. Klucz jest używany przez platformę Kafka podczas partycjonowania danych. Wszystkie pola są przechowywane w komunikacie Kafka jako wartość ciągu JSON. Wprowadź następujące polecenie w programie Jupyter, aby zapisać dane do platformy Kafka za pomocą zapytań usługi batch.

    ```scala
    // Select the vendorid as the key and save the JSON string as the value.
    val query = taxiDF.selectExpr("CAST(vendorid AS STRING) as key", "to_JSON(struct(*)) AS value").write.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("topic", kafkaTopic).save()

    println("Data sent to Kafka")
    ```

9. Zadeklaruj schematu. Następujące polecenie pokazuje, jak używać schematu podczas odczytywania danych JSON z platformy kafka. Wprowadź polecenie w swojej następnej komórki Jupyter.

    ```scala
    // Import bits useed for declaring schemas and working with JSON data
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Define a schema for the data
    val schema = (new StructType).add("dropoff_latitude", StringType).add("dropoff_longitude", StringType).add("extra", StringType).add("fare_amount", StringType).add("improvement_surcharge", StringType).add("lpep_dropoff_datetime", StringType).add("lpep_pickup_datetime", StringType).add("mta_tax", StringType).add("passenger_count", StringType).add("payment_type", StringType).add("pickup_latitude", StringType).add("pickup_longitude", StringType).add("ratecodeid", StringType).add("store_and_fwd_flag", StringType).add("tip_amount", StringType).add("tolls_amount", StringType).add("total_amount", StringType).add("trip_distance", StringType).add("trip_type", StringType).add("vendorid", StringType)
    // Reproduced here for readability
    //val schema = (new StructType)
    //   .add("dropoff_latitude", StringType)
    //   .add("dropoff_longitude", StringType)
    //   .add("extra", StringType)
    //   .add("fare_amount", StringType)
    //   .add("improvement_surcharge", StringType)
    //   .add("lpep_dropoff_datetime", StringType)
    //   .add("lpep_pickup_datetime", StringType)
    //   .add("mta_tax", StringType)
    //   .add("passenger_count", StringType)
    //   .add("payment_type", StringType)
    //   .add("pickup_latitude", StringType)
    //   .add("pickup_longitude", StringType)
    //   .add("ratecodeid", StringType)
    //   .add("store_and_fwd_flag", StringType)
    //   .add("tip_amount", StringType)
    //   .add("tolls_amount", StringType)
    //   .add("total_amount", StringType)
    //   .add("trip_distance", StringType)
    //   .add("trip_type", StringType)
    //   .add("vendorid", StringType)
    
    println("Schema declared")
    ```

10. Wybierz dane, a następnie uruchomić strumienia. Następujące polecenie pokazuje, jak pobierać dane z usługi kafka za pomocą zapytań wsadowych i następnie zapisać wyniki do systemu plików HDFS w klastrze Spark. W tym przykładzie `select` pobiera komunikat (wartość pola) z usługi Kafka i dotyczy schematu. Dane są następnie zapisywane do systemu plików HDFS (WASB lub ADL) w formacie parquet. Wprowadź polecenie w swojej następnej komórki Jupyter.

    ```scala
    // Read a batch from Kafka
    val kafkaDF = spark.read.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()
    
    // Select data and write to file
    val query = kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip").write.format("parquet").option("path","/example/batchtripdata").option("checkpointLocation", "/batchcheckpoint").save()
    
    println("Wrote data to file")
    ```

11. Aby sprawdzić, czy pliki zostały utworzone przez wprowadzenie polecenia w swojej następnej komórki Jupyter. Wyświetla listę plików w `/example/batchtripdata` katalogu.

    ```scala
    %%bash
    hdfs dfs -ls /example/batchtripdata
    ```

12. Poprzedni przykład używane zapytanie usługi batch, następujące polecenie pokazuje, jak zrobić to samo za pomocą zapytania przesyłania strumieniowego. Wprowadź polecenie w swojej następnej komórki Jupyter.

    ```scala
    // Stream from Kafka
    val kafkaStreamDF = spark.readStream.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()
    
    // Select data from the stream and write to file
    kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip").writeStream.format("parquet").option("path","/example/streamingtripdata").option("checkpointLocation", "/streamcheckpoint").start.awaitTermination(30000)
    println("Wrote data to file")
    ```

13. Uruchom następujące komórki, aby sprawdzić, czy pliki zostały napisane przez zapytanie przesyłania strumieniowego.

    ```scala
    %%bash
    hdfs dfs -ls /example/streamingtripdata
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby wyczyścić zasoby utworzone w tym samouczku, możesz usunąć grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego klastra usługi HDInsight i wszystkich innych zasobów skojarzonych z tą grupą zasobów.

Aby usunąć grupę zasobów za pomocą witryny Azure Portal:

1. W witrynie Azure Portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję __Grupy zasobów__, aby wyświetlić listę grup zasobów.
2. Znajdź grupę zasobów do usunięcia, a następnie kliknij prawym przyciskiem myszy przycisk __Więcej (...)__ po prawej stronie listy.
3. Wybierz pozycję __Usuń grupę zasobów__ i potwierdź.

> [!WARNING]  
> Naliczanie opłat rozpoczyna się w momencie utworzenia klastra usługi HDInsight i kończy się wraz z jego usunięciem. Opłaty są naliczane za minutę, więc jeśli klaster nie jest używany, należy go usunąć.
> 
> Usunięcie platformy Kafka w klastrze usługi HDInsight powoduje usunięcie wszystkich danych przechowywanych na platformie Kafka.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób użycia [przesyłania strumieniowego ze strukturą platformy Apache Spark](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) do zapisywania danych na platformie [Apache Kafka](https://kafka.apache.org/) w usłudze HDInsight i odczytywania z niej danych. Korzystając z poniższego linku, możesz dowiedzieć się, jak używać systemu [Apache Storm](https://storm.apache.org/) z platformą Kafka.

> [!div class="nextstepaction"]
> [Używanie systemu Apache Storm z platformą Apache Kafka](hdinsight-apache-storm-with-kafka.md)
