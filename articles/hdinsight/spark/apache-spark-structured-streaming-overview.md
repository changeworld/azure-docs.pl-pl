---
title: Platforma Spark strukturę przesyłania strumieniowego w usłudze Azure HDInsight | Dokumentacja firmy Microsoft
description: Jak używać aplikacji strukturalnych przesyłania strumieniowego Spark w klastrach HDInsight Spark.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: maxluk
ms.openlocfilehash: f3eccfb63b127f3cbb8f9d95ec2fa802861d8c9d
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164683"
---
# <a name="overview-of-spark-structured-streaming"></a>Omówienie strukturę przesyłania strumieniowego Spark

Strukturalne przesyłania strumieniowego Spark umożliwia wdrożenie skalowalne, wysokiej przepustowości, odpornej na uszkodzenia aplikacje do przetwarzania strumieni danych. Strukturalne przesyłania strumieniowego oparto na aparat Spark SQL i poprawia konstrukcje z ramek danych Spark SQL i zestawów danych, więc można napisać strumieniowe przesyłanie zapytań w taki sam sposób jak zapisać partii zapytań.  

Strukturalne przesyłania strumieniowego aplikacji Uruchom w klastrach HDInsight Spark i połącz się strumieniowe przesyłanie danych z Kafka, TCP gniazda (na potrzeby debugowania), usługi Azure Storage i Azure Data Lake Store. Ostatnie dwie opcje, które korzysta z usług magazynu zewnętrznego, umożliwiają oczekiwał na nowe pliki dodane do magazynu i przetwarzanie ich zawartość, tak jakby były przesyłane strumieniowo. 

Strukturalne Streaming tworzy zapytania długotrwałe, podczas którego operacji należy zastosować do danych wejściowych, takich jak zaznaczenia, projekcji, agregacji, okien i przyłączanie do przesyłania strumieniowego DataFrame z odwołaniem DataFrames. Następnie output wyniki do pliku magazynu (obiektach blob magazynu Azure lub usługi Data Lake Store) lub dowolnym magazynem danych przy użyciu kodu niestandardowego (np. bazy danych SQL lub usługi Power BI). Strukturalne przesyłania strumieniowego są także dane wyjściowe do konsoli dla debugowania lokalnego, a tabelą w pamięci pozwala zobaczyć dane generowane dla debugowania w usłudze HDInsight. 

![Strumień przetwarzania za pomocą usługi HDInsight i strukturę przesyłania strumieniowego Spark ](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png)

> [!NOTE]
> Strukturalne przesyłania strumieniowego Spark zastępuje Spark przesyłania strumieniowego (DStreams). Idąc dalej, strukturalnych przesyłania strumieniowego zostanie wyświetlony ulepszeń i konserwacji, podczas gdy DStreams będą tylko w trybie konserwacji. Strukturalne przesyłania strumieniowego nie jest obecnie jako funkcja zakończenia jako DStreams źródeł i wychwytywanie obsługuje fabrycznej, więc oceny wymagań, aby wybrać odpowiednie Spark opcji przetwarzania strumienia. 

## <a name="streams-as-tables"></a>Strumienie jako tabele

Strukturalne przesyłania strumieniowego Spark reprezentuje strumienia danych jako tabelę, która jest niepowiązany szczegółowo, oznacza to, tabeli nadal będzie bardziej powszechne po odebraniu nowych danych. To *tabeli wejściowej* stale jest przetwarzany przez zapytanie długotrwałe i wyniki wysyłane do *tabeli wyników*:

![Struktura przesyłania strumieniowego koncepcji](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png)

W strukturze przesyłania strumieniowego dane dociera do systemu i jest natychmiast pozyskanych w tabeli wejściowej. Możesz zapisać zapytań (przy użyciu DataFrame i interfejsów API zestawu danych), które wykonują operacje względem tej tabeli wejściowej. Wynik kwerendy daje innej tabeli *tabeli wyników*. W tabeli wyników zawiera wyniki zapytania, z którego rysowania danych dla zewnętrznych magazynu danych, takie relacyjnej bazy danych. Czas przetworzenia danych z tabeli wejściowej jest kontrolowany przez *interwał wyzwalacza*. Domyślnie interwał wyzwalacza wynosi zero, więc strukturalnych przesyłania strumieniowego podejmuje próbę przetwarzania danych, natychmiast po dostarczeniu. W praktyce oznacza to, że jak odbywa się strukturalnych przesyłania strumieniowego przetwarzania uruchomienie poprzedniego zapytania, rozpoczyna się inny przetwarzania uruchomienia żadnych nowych danych. Wyzwalacza w odstępach czasu, można skonfigurować tak, aby dane przesyłane strumieniowo są przetwarzane w partiach oparte na czasie. 

Przetworzono danych w wynikach tabele mogą zawierać tylko dane, które jest nowy od czasu ostatniego czasu zapytania (*tryb dołączania*), lub tabeli może być całkowicie odświeżyć za każdym razem, gdy nie nowe dane, dlatego tabela zawiera wszystkie danych wyjściowych od chwili rozpoczęcia przesyłania strumieniowego kwerendy (*tryb pełny*).

### <a name="append-mode"></a>Tryb dołączania

W trybie append, tylko wiersze dodanych do tabeli wyników od momentu ostatniego uruchomienia kwerendy znajdują się w tabeli wyników i zapisywane w pamięci zewnętrznej. Na przykład najprostszym zapytania tylko kopiuje wszystkie dane z tabeli wejściowej tabeli wyników niezmieniony. Zawsze, gdy upłynie interwał wyzwalacza, nowe dane są przetwarzane i wiersze reprezentujący te nowe dane są wyświetlane w tabeli wyników. 

Rozważmy scenariusz, w którym są przetwarzania danych telemetrycznych z czujnikami temperatury, takich jak termostacie. Załóżmy, że jest to pierwszy wyzwalacz przetwarzane jednego zdarzenia w czasie 00:01 dla urządzenia 1 z odczyt temperatury 95 stopni. W to pierwszy wyzwalacz zapytania tylko wiersz mający czas 00:01 pojawia się w tabeli wyników. W czasie 00:02 po odebraniu inne zdarzenie tylko nowy wiersz jest wiersz mający czas 00:02 i dlatego zawierałoby aby tylko jeden wiersz w tabeli wyników.

![Tryb dołączania strukturalnych przesyłania strumieniowego](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png)

Po przy użyciu tryb dołączania, zapytanie będzie można stosowania projekcje (Wybieranie kolumn, które go dba o), filtrowania (tylko wiersze spełniające określone warunki dotyczące pobrania) lub sprzęgania (rozbudować danych przy użyciu danych z tabeli odnośników statyczne). Dołącz sprawia, że tryb ułatwia push odpowiednich nowych danych wskazuje magazynu zewnętrznego.

### <a name="complete-mode"></a>Tryb pełny

Rozważmy scenariusz, w tym samym, teraz w trybie pełnej. W trybie pełnej Tabela całej wyjściowa są odświeżane na każdy wyzwalacz tak w tabeli znajdują się dane, nie tylko z najnowszych wyzwalacz uruchomienia, ale z wszystkie elementy. Tryb pełny można użyć do skopiowania danych niezmieniony z tabeli wejściowej tabeli wyników. Przy każdym uruchomieniu wyzwalane wraz z poprzednich wierszy pojawią się nowe wiersze wynik. Dane wyjściowe tabeli wyników zakończą się przechowywania wszystkie dane zebrane od momentu rozpoczęcia zapytania i po pewnym czasie należy uruchomić za mało pamięci. Tryb pełny jest przeznaczony do użytku z zapytania agregujące podsumowujące przychodzących danych w określony sposób, i itd. każdy wyzwalacza w tabeli wyników został zaktualizowany o nowe podsumowanie. 

Przykładowa wykonanej do tej pory Brak pięć sekund, przez które dane już przetworzone, a czas przetwarzania danych dla szóstego drugiego. Tabela wejściowa ma zdarzeń 00:01 czas i czas 00:03. Celem tego przykładowe zapytanie jest umożliwiają średnia temperatura urządzenia co pięć sekund. Wykonanie tego zapytania stosuje wartość zagregowaną, która przyjmuje wszystkie wartości, które wchodzą w każdym okna 5 sekund, oblicza średnią temperaturę i tworzy wiersz średnia temperatura tego interwału. Na końcu pierwszego okna 5 sekund, istnieją dwie spójne kolekcje: (00:01, 1, 95) i (00:03, 1, 98). Tak dla okna 00:00-00:05 agregacji tworzy spójną kolekcję z średnia temperatura 96.5 stopni. W następnym oknie 5 sekund istnieje tylko jeden punkt danych w czasie 00:06, tak aby wynikowe średnia temperatura 98 stopni. W czasie 00:10, w trybie pełnej, w tabeli wyników ma wiersze dla obu windows 00:00-00:05 i 00-00:05:10, ponieważ zapytanie Wyświetla wszystkie zagregowane wiersze, nie tylko te nowe. W związku z tym tabeli wyników w dalszym ciągu rosnąć w miarę dodawania nowych okien.    

![Struktura przesyłania strumieniowego tryb pełny](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png)

Nie wszystkie zapytania w trybie pełnej spowoduje, że tabela rośnie bez granic.  Należy rozważyć w poprzednim przykładzie, że zamiast uśrednianie temperatury przez przedział czasu, zamiast tego średnio się według identyfikatora urządzenia. W tabeli wyników zawiera stałą liczbę wierszy (po jednym dla każdego urządzenia) z średnia temperatura urządzenia we wszystkich punktów danych odebranych za pomocą tego urządzenia. Nowe temperatury są odbierane, tabeli wyników jest aktualizowane tak, aby średnie w tabeli są zawsze aktualne. 

## <a name="components-of-a-spark-structured-streaming-application"></a>Składniki aplikacji Spark strukturalnych przesyłania strumieniowego

Prosty przykład kwerendy można podsumować odczyty temperatury przez hour-long systemu windows. W takim przypadku dane są przechowywane w plikach JSON w usłudze Azure Storage (dołączone jako domyślny magazyn dla klastra usługi HDInsight):

    {"time":1469501107,"temp":"95"}
    {"time":1469501147,"temp":"95"}
    {"time":1469501202,"temp":"95"}
    {"time":1469501219,"temp":"95"}
    {"time":1469501225,"temp":"95"}

Te pliki w formacie JSON są przechowywane w `temps` podfolder poniżej kontenera klastra usługi HDInsight. 

### <a name="define-the-input-source"></a>Zdefiniuj źródło danych wejściowych

Skonfiguruj DataFrame, opisujący źródła danych i wszelkich ustawień wymaganych przez to źródło. W tym przykładzie pobiera z pliku JSON w usłudze Azure Storage i zastosuje schematu do nich w czasie odczytu.

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    //Cluster-local path to the folder containing the JSON files
    val inputPath = "/temps/" 

    //Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
    val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

    //Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
    val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath) 

#### <a name="apply-the-query"></a>Stosuje zapytanie

Następnie Zastosuj kwerendę, która zawiera żądanej operacji przed DataFrame przesyłania strumieniowego. W takim przypadku agregacji grup wszystkie wiersze w 1 godziny w systemie windows, a następnie oblicza minimalną, średniego i maksymalnego temperatur w danym przedziale 1-godzinnym.

    val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))

### <a name="define-the-output-sink"></a>Zdefiniuj ujście danych wyjściowych

Następnie określ miejsce docelowe dla wierszy, które są dodawane do tabeli wyników w każdym interwale wyzwalacza. W tym przykładzie danych wyjściowych tylko wszystkie wiersze do tabeli w pamięci `temps` można później wykonać zapytanie z SparkSQL. Tryb pełny wyjścia zapewnia, że wszystkie wiersze, dla wszystkich okien są dane wyjściowe zawsze.

    val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete") 

### <a name="start-the-query"></a>Uruchom kwerendę

Uruchom zapytanie przesyłania strumieniowego i uruchomić do momentu otrzymania sygnału zakończenia. 

    val query = streamingOutDF.start()  

### <a name="view-the-results"></a>Wyświetlenie wyników

Po uruchomieniu zapytania w tym samym SparkSession, można uruchomić kwerendę SparkSQL `temps` tabeli przechowywania wyników zapytania. 

    select * from temps

To zapytanie zwraca wyniki podobne do następujących:


| okno |  min(temp) | AVG(temp) | MAX(temp) |
| --- | --- | --- | --- |
|{u'start ": u" 2016-07-26T02:00:00.000Z ", u'end"... |    95 |    95.231579 | 99 |
|{u'start ": u" 2016-07-26T03:00:00.000Z ", u'end"...  |95 |   96.023048 | 99 |
|{u'start ": u" 2016-07-26T04:00:00.000Z ", u'end"...  |95 |   96.797133 | 99 |
|{u'start ": u" 2016-07-26T05:00:00.000Z ", u'end"...  |95 |   96.984639 | 99 |
|{u'start ": u" 2016-07-26T06:00:00.000Z ", u'end"...  |95 |   97.014749 | 99 |
|{u'start ": u" 2016-07-26T07:00:00.000Z ", u'end"...  |95 |   96.980971 | 99 |
|{u'start ": u" 2016-07-26T08:00:00.000Z ", u'end"...  |95 |   96.965997 | 99 |  

Szczegółowe informacje dotyczące interfejsu API platformy Spark strukturalnych strumienia, wraz z danych wejściowych źródeł, działań i dane wyjściowe wychwytywanie ją obsługuje, zobacz [Spark strukturalnych przesyłania strumieniowego przewodnik programowania w języku](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html).

## <a name="checkpointing-and-write-ahead-logs"></a>Tworzenie punktów kontrolnych i dzienniki zapisu z wyprzedzeniem

Aby zapewnić odporność i odporność na uszkodzenia, przesyłanie strumieniowe strukturalnych opiera się na *tworzenie punktów kontrolnych* aby upewnić się, że strumień można kontynuować przetwarzania nieprzerwaną, nawet w przypadku awarii węzła. W usłudze HDInsight Spark tworzy punkty kontrolne do magazynu trwałego magazynu Azure lub usługi Data Lake Store. Te punkty kontrolne przechowywać informacje o postępie dotyczące przesyłania strumieniowego zapytania. Ponadto strukturalnych przesyłania strumieniowego używa *zapisu z wyprzedzeniem dziennika* (odnowy). ODNOWY przechwytuje pozyskiwane dane, które zostały odebrane, ale jeszcze nie przetworzonych przez zapytanie. Jeśli wystąpi błąd i przetwarzania ponownego uruchamiania z sieci, zdarzeń odebranych ze źródła nie zostaną utracone.

## <a name="deploying-spark-streaming-applications"></a>Wdrażanie aplikacji Spark przesyłania strumieniowego

Zazwyczaj tworzenie aplikacji Spark przesyłania strumieniowego lokalnie do pliku JAR, a następnie wdrożyć Spark w usłudze HDInsight, kopiując plik JAR do magazynu domyślnego powiązany z klastrem usługi HDInsight. Aplikację można uruchomić z interfejsów API REST LIVY, które są dostępne z klastrem przy użyciu operacji POST. Treść wpisu zawiera dokument JSON, który określa ścieżkę do Twojej JAR nazwę klasy, którego metoda główna definiuje i uruchamia przesyłania strumieniowego aplikacji oraz opcjonalnie wymagań dotyczących zasobów, zadania (na przykład liczba modułów, pamięci i rdzenie) , a wszystkie ustawienia konfiguracji kodu aplikacji wymaga.

![Wdrażanie aplikacji Spark przesyłania strumieniowego](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Można również sprawdzić stan wszystkich aplikacji z żądanie GET względem punktu końcowego programu LIVY. Na koniec może prowadzić działającej aplikacji, wysyłając żądanie usunięcia, względem punktu końcowego programu LIVY. Aby uzyskać więcej informacji na temat interfejsu API programu LIVY, zobacz [zdalnego zadań z programu LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie klastra Apache Spark w usłudze HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Platforma Spark strukturę przewodnik programowania w języku przesyłania strumieniowego](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Uruchamianie zadań Spark zdalnie za pomocą programu LIVY](apache-spark-livy-rest-interface.md)
