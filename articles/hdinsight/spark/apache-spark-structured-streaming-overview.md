---
title: Platforma Spark jest przesyłanie strumieniowe ze strukturą w usłudze Azure HDInsight
description: Jak korzystać z aplikacji przesyłanie strumieniowe ze strukturą platformy Spark w klastrach HDInsight Spark.
author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: maxluk
ms.openlocfilehash: 0e9d87e5b344b7091a2a0cf41d6f7fa3484dfcf3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64711328"
---
# <a name="overview-of-apache-spark-structured-streaming"></a>Przegląd platformy Apache Spark Structured Streaming

[Platforma Apache Spark](https://spark.apache.org/) przesyłanie strumieniowe ze strukturą, umożliwia wdrożenie skalowalne o wysokiej przepływności, odpornej na uszkodzenia aplikacji do przetwarzania strumieni danych. Przesyłanie strumieniowe ze strukturą oparto na aparatu Spark SQL i zwiększa się po konstrukcji z ramek danych SQL platformy Spark i zestawy danych, dzięki czemu można napisać, przesyłanie strumieniowe zapytania w taki sam sposób napisać wsadowe.  

Aplikacje do przesyłania strumieniowego Structured działały w klastrach HDInsight Spark i połącz się dane przesyłane strumieniowo z [platformy Apache Kafka](https://kafka.apache.org/), TCP gniazda (na potrzeby debugowania), usługi Azure Storage lub usługi Azure Data Lake Storage. Ostatnie dwie opcje, które zależą od usługi zewnętrznej usługi storage umożliwiają wyszukiwać nowe pliki dodane do magazynu i przetwarzanie ich zawartość, tak, jakby były przesyłane strumieniowo. 

Przesyłanie strumieniowe ze strukturą tworzy zapytanie długotrwałych, podczas którego operacje stosowane do danych wejściowych, takich jak zaznaczenia, projekcji, agregacji, obsługi okien i dołączenie do przesyłania strumieniowego ramkę danych za pomocą odwołania elementy Dataframe. Następnie wyjściowe wyniki do file storage (Azure Storage blob lub magazynu usługi Data Lake) lub dowolnym magazynem danych za pomocą kodu niestandardowego (np. bazy danych SQL lub usługi Power BI). Przesyłanie strumieniowe ze strukturą także dane wyjściowe do konsoli debugowania lokalnie, a także do tabeli w pamięci, dzięki czemu możesz zobaczyć dane wygenerowane na potrzeby debugowania w HDInsight. 

![Przesyłanie strumieniowe ze strukturą Stream przetwarzania za pomocą HDInsight i platformy Spark](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png)

> [!NOTE]  
> Przesyłanie strumieniowe ze strukturą platformy Spark zastępuje Spark Streaming (DStreams). Idąc dalej, przesyłanie strumieniowe ze strukturą otrzyma ulepszeń i konserwacji, podczas gdy DStreams będą w trybie konserwacji tylko. Przesyłanie strumieniowe ze strukturą nie jest obecnie jako funkcja uzupełniania jako DStreams dla źródeł i wychwytywanie, że obsługuje ona gotowych, więc ocena wymagań, aby wybrać odpowiednie Spark opcji przetwarzania strumienia. 

## <a name="streams-as-tables"></a>Strumienie jako tabele

Przesyłanie strumieniowe ze strukturą platformy Spark przedstawia strumień danych jako tabelę, która nie jest powiązana szczegółowo, oznacza to, że tabela stale rośnie, nadejściu nowych danych. To *tabeli wejściowej* stale jest przetwarzany przez zapytanie długotrwałych i wyniki wysyłane do *tabeli wyjściowej*:

![Przesyłanie strumieniowe koncepcji ze strukturą](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png)

W przesyłanie strumieniowe ze strukturą danych dociera do systemu i od razu są zbierane w tabeli wejściowej. Możesz pisać zapytania (przy użyciu ramkę danych i interfejsy API zestawu danych), które wykonują operacje względem tej tabeli wejściowej. Wynik kwerendy daje innej tabeli *tabeli wyników*. Tabela wyników zawiera wyniki zapytania, z którego rysowania danych dla zewnętrznego magazynu danych, takie relacyjnej bazy danych. Chronometraż podczas przetwarzania danych z tabeli wejściowej jest kontrolowane przez *interwał wyzwalacza*. Domyślnie interwał wyzwalacza wynosi zero, więc przesyłanie strumieniowe ze strukturą podejmuje próbę przetwarzania danych, jak najszybciej po ich odebraniu. W praktyce oznacza to, że zaraz po zakończeniu przesyłania strumieniowego ze strukturą przetwarzania uruchomienia z poprzedniej kwerendy, rozpoczyna się innym przetwarzania uruchamiać żadnych nowych danych. Wyzwalacz, aby można było uruchamiane z interwałem można skonfigurować tak, aby dane przesyłane strumieniowo są przetwarzane w partiach oparte na czasie. 

Przetworzono danych w wynikach tabel mogą zawierać tylko dane, które jest nowy od czasu ostatniego czasu kwerendy (*trybie append*), lub tabeli może być całkowicie odświeżona, za każdym razem, gdy ma nowych danych, więc tabela obejmuje wszystkie dane wyjściowe od chwili rozpoczęcia przesyłania strumieniowego zapytania (*w trybie*).

### <a name="append-mode"></a>Tryb dołączania

W trybie append, tylko wiersze dodane do tabeli wyników, od momentu ostatniego uruchomienia zapytania są obecne w tabeli wyników i zapisywane do magazynu zewnętrznego. Na przykład najprostszym zapytania po prostu kopiuje wszystkie dane z tabeli wejściowej do tabeli wyników niezmieniony. Każdorazowo, gdy on upłynie interwał wyzwalacza, nowe dane zostaną przetworzone i wiersze reprezentujący te nowe dane są wyświetlane w tabeli wyników. 

Rozważmy scenariusz, w których skuteczność przetwarzania danych telemetrycznych z czujników temperatury, takich jak termostat. Załóżmy, że jest to pierwszy wyzwalacz przetwarzane jedno zdarzenie w czasie 00:01 dla urządzenia 1 z odczyt temperatury, 95 stopni. W to pierwszy wyzwalacz zapytanie tylko wiersz mający godziny 00:01 pojawia się w tabeli wyników. W czasie 00:02 po nadejściu inne zdarzenie tylko nowy wiersz to wiersz mający godziny 00:02, a więc zawiera tylko ten jeden wiersz w tabeli wyników.

![Przesyłanie strumieniowe ze strukturą trybie Append](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png)

Podczas przy użyciu trybie append, zapytania będzie się stosowanie projekcji (Wybieranie kolumn, które go dba o), filtrowania (pobrania tylko wiersze spełniające określone warunki) lub dołączania (rozszerzając danych przy użyciu danych z tabeli odnośników statyczne). Dołącz sprawia, że tryb wykazuje ułatwiają wypychania odpowiednie nowe dane do magazynu zewnętrznego.

### <a name="complete-mode"></a>W trybie

Rozważmy scenariusz, w tym samym, tym razem stosując w trybie. W trybie wszystkie dane wyjściowe tabela zostanie odświeżona w każdym wyzwalacza, więc tabela zawiera dane, nie tylko z ostatnich uruchomienie wyzwalacza, ale ze wszystkich przebiegów. Można użyć w trybie do kopiowania danych niezmieniony z tabeli wejściowej tabeli wyników. W każdym przebiegu wyzwalane nowe wiersze wynikowe są wyświetlane wraz z poprzednich wierszy. Dane wyjściowe tabeli wyników zakończy się przechowywanie wszystkich danych zebranych od czasu rozpoczęcia zapytania i po pewnym czasie należy uruchomić za mało pamięci. W trybie jest przeznaczona do użytku z zapytania zagregowane, które podsumowują dane przychodzące w jakiś sposób, a itd. każdy wyzwalacz tabeli wyników jest aktualizowana o nowe podsumowanie. 

Założono do tej pory, istnieje pięć sekund, przez które już przetworzone dane, nadszedł czas na przetwarzanie danych dla szóstego drugiego. Tabela wejściowa ma zdarzenia 00:01 czasu i godziny 00:03. Celem tego przykładowe zapytanie jest zapewnienie średnia temperatura urządzenie co pięć sekund. Wykonanie tego zapytania dotyczy agregacją, która pobiera wszystkie wartości, które mieszczą się w każdym przedziale 5-sekundowego, oblicza średnią temperaturę i tworzy wiersz średnia temperatura w tym okresie. Na końcu pierwsze okno 5-sekundowego istnieją dwie spójne kolekcje: (00:01, 1, 95) i (00:03, 1, 98). Dla okna 00:00-00:05 agregacji tworzy spójną kolekcję z średnia temperatura 96.5 stopni. W następnym oknie 5-sekundowego istnieje tylko jeden punkt danych w czasie 00:06, dzięki czemu wynikowy średnia temperatura jest 98 stopni. Godziny 00:10, przy użyciu trybu pełną tabeli wyników ma wiersze dla obu windows 00:00-00:05 i 00:05-00:10, ponieważ zapytanie Wyświetla wszystkie wiersze zagregowane, nie tylko nowe licencje. W związku z tym tabeli wyników nadal rosnąć w miarę dodawania nowych oknach.    

![Przesyłanie strumieniowe w trybie ze strukturą](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png)

Nie wszystkie kwerendy za pomocą w trybie spowoduje, że tabeli można rozwijać bez granic.  Należy rozważyć w poprzednim przykładzie, że zamiast średniej temperatury, przedział czasu, zamiast tego uśredniane się według identyfikatora urządzenia. W tabeli wyników zawiera stałą liczbę wierszy (po jednej na urządzenie) z średnia temperatura urządzenia we wszystkich punktów danych odebranych z tego urządzenia. Jak otrzymuje nowe temperatury, tabeli wyników jest aktualizowana tak, aby średnie w tabeli są zawsze aktualne. 

## <a name="components-of-a-spark-structured-streaming-application"></a>Składniki aplikacji Spark Structured Streaming

Prosty przykład kwerendy można podsumować odczyty temperatury przez hour-long systemu windows. W tym przypadku dane są przechowywane w plikach w formacie JSON w usłudze Azure Storage (dołączone jako magazynem domyślnym dla klastra HDInsight):

    {"time":1469501107,"temp":"95"}
    {"time":1469501147,"temp":"95"}
    {"time":1469501202,"temp":"95"}
    {"time":1469501219,"temp":"95"}
    {"time":1469501225,"temp":"95"}

Te pliki JSON są przechowywane w `temps` podfolder poniżej kontenera w klastrze HDInsight. 

### <a name="define-the-input-source"></a>Zdefiniuj źródło danych wejściowych

Najpierw należy skonfigurować ramkę opisujący źródła danych i wszystkie ustawienia wymagane przez to źródło danych. W tym przykładzie pobiera z plików JSON w usłudze Azure Storage i dotyczy schemat je w czasie odczytu.

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    //Cluster-local path to the folder containing the JSON files
    val inputPath = "/temps/" 

    //Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
    val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

    //Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
    val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath) 

#### <a name="apply-the-query"></a>Stosuje zapytanie

Następnie Zastosuj kwerendę, która zawiera żądanej operacji względem ramki danych przesyłania strumieniowego. W tym przypadku agregacji grup wszystkich wierszy do 1 godziny w systemie windows, a następnie oblicza minimalna, średnia i maksymalna temperatura w tym 1-godzinnego przedziału czasu.

    val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))

### <a name="define-the-output-sink"></a>Zdefiniuj ujścia danych wyjściowych

Następnie zdefiniuj lokalizację docelową dla wierszy, które są dodawane do tabeli wyników w każdym interwale wyzwalacza. W tym przykładzie po prostu wyświetla wszystkie wiersze do tabeli w pamięci `temps` można później tworzyć zapytania za pomocą SparkSQL. Tryb pełne dane wyjściowe zapewnia, że wszystkie wiersze, dla wszystkich okien są dane wyjściowe każdym razem.

    val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete") 

### <a name="start-the-query"></a>Uruchom zapytanie

Uruchom zapytanie przesyłania strumieniowego i uruchom do momentu otrzymania sygnału zakończenia. 

    val query = streamingOutDF.start()  

### <a name="view-the-results"></a>Wyświetlanie wyników

Po uruchomieniu zapytania w tym samym SparkSession, można uruchomić zapytanie do SparkSQL `temps` tabeli, w którym są przechowywane wyniki zapytania. 

    select * from temps

To zapytanie daje wyniki podobne do następujących:


| Okno |  min(temp) | AVG(temp) | MAX(temp) |
| --- | --- | --- | --- |
|{u'start ": u" 2016-07-26T02:00:00.000Z ", u'end"... |    95 |    95.231579 | 99 |
|{u'start ": u" 2016-07-26T03:00:00.000Z ", u'end"...  |95 |   96.023048 | 99 |
|{u'start ": u" 2016-07-26T04:00:00.000Z ", u'end"...  |95 |   96.797133 | 99 |
|{u'start ": u" 2016-07-26T05:00:00.000Z ", u'end"...  |95 |   96.984639 | 99 |
|{u'start ": u" 2016-07-26T06:00:00.000Z ", u'end"...  |95 |   97.014749 | 99 |
|{u'start ": u" 2016-07-26T07:00:00.000Z ", u'end"...  |95 |   96.980971 | 99 |
|{u'start ": u" 2016-07-26T08:00:00.000Z ", u'end"...  |95 |   96.965997 | 99 |  

Szczegółowe informacje dotyczące interfejsu API platformy Spark ze strukturą Stream, wraz z danych wejściowych źródeł, operacji i dane wyjściowe wychwytywanie ją obsługuje, zobacz [Apache Spark Structured Streaming Programming Guide](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html).

## <a name="checkpointing-and-write-ahead-logs"></a>Tworzenie punktów kontrolnych i zapisu z wyprzedzeniem dzienników

Aby zapewnić odporność i odporności na uszkodzenia, przesyłanie strumieniowe ze strukturą opiera się na *tworzenie punktów kontrolnych* aby upewnić się, że strumień przetwarzania można bezproblemowo kontynuują, nawet w przypadku awarii węzła. W HDInsight Spark tworzy punkty kontrolne do trwałego magazynu usługi Azure Storage lub magazynu usługi Data Lake. Te punkty kontrolne są przechowywane informacje o postępie dotyczące transmisji strumieniowej zapytania. Ponadto, przesyłanie strumieniowe ze strukturą używa *dziennik zapisu z wyprzedzeniem* (WAL). WAL przechwytuje pozyskiwanych danych, które zostały odebrane, ale nie zostały jeszcze przetworzone przez zapytanie. Jeśli wystąpi błąd, przetwarzanie jest ponownie z WAL otrzymanych z źródła zdarzeń nie zostaną utracone.

## <a name="deploying-spark-streaming-applications"></a>Wdrażanie aplikacji do przesyłania strumieniowego platformy Spark

Zazwyczaj tworzenie aplikacji Spark Streaming lokalnie do pliku JAR, a następnie wdrożyć go na platformę Spark w HDInsight przez skopiowanie pliku JAR do domyślnego magazynu dołączone do klastra usługi HDInsight. Można uruchomić aplikacji za pomocą [Apache, usługi Livy](https://livy.incubator.apache.org/) interfejsów API REST dostępne z klastra przy użyciu operacji POST. Treść wpisu zawiera dokument JSON, który zawiera ścieżkę do pliku JAR, nazwa klasy, którego metoda główna definiuje i przesyłanie aplikacji, i opcjonalnie wymagań dotyczących zasobów zadania (np. liczba executors, pamięci i rdzeni) , a wszystkie ustawienia konfiguracji kodu aplikacji wymaga.

![Wdrażanie aplikacji usługi przesyłania strumieniowego platformy Spark](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Można również sprawdzić stan wszystkich aplikacji, za pomocą żądanie GET względem punktu końcowego usługi LIVY. Na koniec możesz zakończyć uruchomionej aplikacji, wysyłając żądanie DELETE względem punktu końcowego usługi LIVY. Szczegółowe informacje na temat interfejsu API usługi LIVY, [zdalnej obsługi zadań przy użyciu usługi LIVY Apache](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie klastra platformy Apache Spark w usłudze HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Przewodnik programowania w przesyłania strumieniowego ze strukturą platformy Apache Spark](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Uruchamiać zadania Apache Spark zdalnie za pomocą usługi LIVY Apache](apache-spark-livy-rest-interface.md)
