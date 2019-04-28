---
title: 'Samouczek: Przesyłanie strumieniowe ze strukturą na platformie Apache Spark za pomocą platformy Apache Kafka — Azure HDInsight'
description: Dowiedz się, jak wymieniać dane z platformą Apache Kafka za pomocą przesyłania strumieniowego platformy Apache Spark. Ten samouczek obejmuje przesyłanie strumieniowe danych z platformy Spark w usłudze HDInsight za pomocą notesu Jupyter.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seodec18
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 388ce607cf75a12705c9a32fe19086dbf9f15e71
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096151"
---
# <a name="tutorial-use-apache-spark-structured-streaming-with-apache-kafka-on-hdinsight"></a>Samouczek: Używanie strumieni ze strukturą platformy Apache Spark z platformą Kafka w usłudze HDInsight

W tym samouczku przedstawiono sposób użycia [przesyłania strumieniowego platformy Apache Spark](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) do odczytywania i zapisywania danych przy użyciu platformy [Apache Kafka](https://kafka.apache.org/) w usłudze Azure HDInsight.

Przesyłanie strumieniowe ze strukturą platformy Spark korzysta z aparatu przetwarzania strumienia opartego na module Spark SQL. Aparat ten umożliwia wyrażanie obliczeń strumieniowych tak samo jak obliczeń wsadowych na danych statycznych.  

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przesyłanie strumieniowe ze strukturą na platformie Kafka
> * Tworzenie klastrów Kafka i Spark
> * Przekazywanie notesu do platformy Spark
> * Używanie notesu
> * Oczyszczanie zasobów

Po zakończeniu pracy z tym dokumentem pamiętaj o usunięciu tych klastrów, aby uniknąć naliczania opłat.

## <a name="prerequisites"></a>Wymagania wstępne

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

| Batch | Przesyłanie strumieniowe |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

Operacja przesyłania strumieniowego używa również wywołania `awaitTermination(30000)`, które zatrzymuje strumień po 30000 ms. 

Aby używać przesyłania strumieniowego ze strukturą na platformie Kafka, projekt musi mieć zdefiniowaną zależność od pakietu `org.apache.spark : spark-sql-kafka-0-10_2.11`. Wersja tego pakietu powinna być zgodna z wersją platformy Spark w usłudze HDInsight. W przypadku platformy Spark 2.2.0 (dostępnej w usłudze HDInsight 3.6) informacje o zależnościach dla różnych typów projektu możesz znaleźć na stronie [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar).

W przypadku notesu Jupyter udostępnionego z tym samouczkiem następująca komórka ładuje zależność tego pakietu:

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
    
    Szablon usługi Azure Resource Manager znajduje się tutaj: **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**.

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

## <a name="upload-the-notebook"></a>Przekazywanie notesu

Aby przekazać notes z projektu na platformę Spark w klastrze usługi HDInsight, wykonaj następujące kroki:

1. Pobierz projekt ze strony [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming).

1. W przeglądarce internetowej połącz się z notesem Jupyter w klastrze Spark. Zastąp element `CLUSTERNAME` w poniższym adresie URL nazwą klastra __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Po wyświetleniu monitu wprowadź nazwę użytkownika klastra (administratora) i hasło użyte podczas tworzenia klastra.

2. Za pomocą przycisku __Przekaż__ wyświetlanego w prawej górnej części strony przekaż plik __spark-structured-streaming-kafka.ipynb__ do klastra. Wybierz pozycję __Otwórz__, aby rozpocząć przekazywanie.

    ![Wybieranie i przekazywanie notesu za pomocą przycisku przekazywania](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-button.png)

    ![Wybieranie pliku KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/select-notebook.png)

3. Znajdź pozycję __spark-structured-streaming-kafka.ipynb__ na liście notesów, a następnie wybierz przycisk __Przekaż__ widoczny obok tej pozycji.

    ![Aby przekazać notes, użyj przycisku przekazywania dla pozycji KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-notebook.png)


## <a name="use-the-notebook"></a>Używanie notesu

Po przekazaniu plików wybierz pozycję __spark-structured-streaming-kafka.ipynb__, aby otworzyć notes. Aby dowiedzieć się, jak używać przesyłania strumieniowego ze strukturą w systemie Spark z platformą Kafka w usłudze HDInsight, postępuj zgodnie z instrukcjami zawartymi w notesie.

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
