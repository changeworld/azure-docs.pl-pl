---
title: Analizowanie dzienników usługi Application Insight przy użyciu platformy Spark — Azure HDInsight
description: Dowiedz się, jak wyeksportować dzienniki Application Insights do magazynu obiektów blob, a następnie analizowanie dzienników za pomocą platformy Spark w HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/09/2018
ms.openlocfilehash: 45cbb26eb74dd78bf46a6b148a32a7762bb31fbc
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696877"
---
# <a name="analyze-application-insights-telemetry-logs-with-apache-spark-on-hdinsight"></a>Analizowanie dzienników telemetrii usługi Application Insights przy użyciu platformy Apache Spark w HDInsight

Dowiedz się, jak używać [platformy Apache Spark](https://spark.apache.org/) na HDInsight do analizowania danych telemetrycznych Application Insights.

[Visual Studio Application Insights](../../azure-monitor/app/app-insights-overview.md) to usługa analizy, które monitoruje aplikacje sieci web. Dane telemetryczne generowane przez usługę Application Insights można wyeksportować do usługi Azure Storage. Gdy dane znajdują się w usłudze Azure Storage, HDInsight może służyć do analizowania go.

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikacja, która jest skonfigurowana do używania usługi Application Insights.

* Znajomość tworzenia klastra HDInsight opartych na systemie Linux. Aby uzyskać więcej informacji, zobacz [tworzenie platformy Apache Spark w HDInsight](apache-spark-jupyter-spark-sql.md).

  > [!IMPORTANT]  
  > Procedura przedstawiona w tym dokumencie wymaga klastra usługi HDInsight używającego systemu Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* Przeglądarka sieci web.

Następujące zasoby zostały użyte w zakresie opracowywania i testowania w tym dokumencie:

* Dane telemetryczne Insights aplikacji został wygenerowany za pomocą [aplikacji sieci web Node.js skonfigurowane do korzystania z usługi Application Insights](../../azure-monitor/app/nodejs.md).

* Platforma Spark opartych na systemie Linux w klastrze HDInsight w wersji 3.5 był używany do analizowania danych.

## <a name="architecture-and-planning"></a>Planowanie i architektura

Na poniższym diagramie przedstawiono architekturę usługi w tym przykładzie:

![Diagram przedstawiający danych napływających z usługi Application Insights do usługi blob storage, a następnie przetwarzany przez rozwiązanie Spark w HDInsight](./media/apache-spark-analyze-application-insight-logs/appinsightshdinsight.png)

### <a name="azure-storage"></a>Azure Storage

Usługa Application Insights można skonfigurować do ciągły Eksport informacje telemetryczne do obiektów blob. HDInsight odczytywać dane przechowywane w obiektach blob. Istnieją jednak pewne wymagania, które należy wykonać:

* **Lokalizacja**: Jeśli konto magazynu i HDInsight znajdują się w różnych lokalizacjach, może to zwiększyć opóźnienie. Zwiększa także koszt, jak ruch wychodzący, opłaty są stosowane do przenoszenia między regionami danych.

    > [!WARNING]  
    > Za pomocą konta magazynu w innej lokalizacji niż HDInsight nie jest obsługiwana.

* **Obiekt blob typu**: Usługa HDInsight obsługuje tylko blokowe obiekty blob. Application Insights wartością domyślną jest używanie blokowych obiektów blob, więc powinny działać domyślnie HDInsight.

Aby uzyskać informacje na temat dodawania magazynu do istniejącego klastra, zobacz [dodawanie kolejnych kont magazynu](../hdinsight-hadoop-add-storage.md) dokumentu.

### <a name="data-schema"></a>Schemat danych

Usługa Application Insights zapewnia [eksportowanie modelu danych](../../azure-monitor/app/export-data-model.md) informacje dotyczące formatu danych telemetrycznych wyeksportowane do obiektów blob. Kroki opisane w tym dokumencie używania programu Spark SQL do pracy z danymi. Rozwiązanie Spark SQL może automatycznie generować schemat strukturę danych JSON, które są rejestrowane przez usługę Application Insights.

## <a name="export-telemetry-data"></a>Eksportowanie danych telemetrycznych

Postępuj zgodnie z instrukcjami w [skonfiguruj Eksport ciągły](../../azure-monitor/app/export-telemetry.md) do konfigurowania usługi Application Insights, aby wyeksportować informacje o telemetrii do usługi Azure storage blob.

## <a name="configure-hdinsight-to-access-the-data"></a>Konfigurowanie HDInsight dostępu do danych

W przypadku tworzenia klastra usługi HDInsight, należy dodać konto magazynu podczas tworzenia klastra.

Aby dodać konto usługi Azure Storage do istniejącego klastra, skorzystaj z informacji w [dodać dodatkowe konta magazynu](../hdinsight-hadoop-add-storage.md) dokumentu.

## <a name="analyze-the-data-pyspark"></a>Analizowanie danych: PySpark

1. Z [witryny Azure portal](https://portal.azure.com), wybierz platformy Spark w klastrze HDInsight. Z **szybkich łączy** zaznacz **pulpity nawigacyjne klastra**, a następnie wybierz pozycję **notesu programu Jupyter** z sekcji Dashboard__ klastra.

    ![Pulpity nawigacyjne klastra](./media/apache-spark-analyze-application-insight-logs/clusterdashboards.png)

2. W prawym górnym rogu strony Jupyter wybierz **New**, a następnie **PySpark**. Zostanie otwarta nowa karta przeglądarki zawierająca notesu programu Jupyter oparta na środowisku Python.

3. W pierwszym polu (o nazwie **komórki**) na stronie wprowadź następujący tekst:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Ten kod konfiguruje Spark rekursywnie dostęp do struktury katalogu dla danych wejściowych. Telemetria usługi Application Insights jest rejestrowany wpis podobny do struktury katalogów `/{telemetry type}/YYYY-MM-DD/{##}/`.

4. Użyj **SHIFT + ENTER** do uruchomienia kodu. Po lewej stronie komórki "\*" pojawia się między nawiasami, aby wskazać, czy kod w tej komórce jest wykonywana. Po zakończeniu "\*" zmiany numeru i dane wyjściowe podobne do następującego tekstu jest wyświetlana poniżej komórki:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. Nową komórkę jest tworzony poniżej pierwszy z nich. Wprowadź następujący tekst w nowej komórce. Zastąp `CONTAINER` i `STORAGEACCOUNT` z nazwą konta usługi Azure storage oraz nazwa kontenera obiektów blob, który zawiera dane usługi Application Insights.

   ```python
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Użyj **SHIFT + ENTER** do wykonania tej komórki. Zostanie wyświetlony wynik podobny do następującego tekstu:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Ścieżka wasb, zwracany jest lokalizacja danych telemetrycznych usługi Application Insights. Zmiana `hdfs dfs -ls` wiersz w komórce, użyj ścieżki wasb zwrócony, a następnie użyj **SHIFT + ENTER** ponownie uruchomić komórkę. Tym razem wyniki powinien być wyświetlany katalogi, które zawierają dane telemetryczne.

   > [!NOTE]  
   > W pozostałej części kroki opisane w tej sekcji `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` użyto katalogu. Struktury katalogów mogą się różnić.

6. W następnej komórki wprowadź następujący kod: Zastąp `WASB_PATH` przy użyciu ścieżki z poprzedniego kroku.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Ten kod tworzy ramkę danych z plików JSON wyeksportowany przez proces eksportu ciągłego. Użyj **SHIFT + ENTER** do uruchomienia tej komórki.
7. W następnej komórki wprowadź, a następnie uruchom następujące polecenie, aby wyświetlić schemat, który Spark utworzony dla plików JSON:

   ```python
   jsonData.printSchema()
   ```

    Schemat dla każdego typu danych telemetrycznych jest inny. Poniższy przykład jest schematu, który jest generowany dla żądania sieci web (dane przechowywane w `Requests` podkatalogu):

        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)
8. Zarejestruj ramki danych jako tabeli tymczasowej, a następnie uruchamiania zapytania względem danych należy wykonać następujące kroki:

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    Ta kwerenda zwraca Miasto dotycząca pierwszych 20 rekordów, w której context.location.city nie ma wartości null.

   > [!NOTE]  
   > Strukturę context jest obecny w wszystkie dane telemetryczne zarejestrowane przez usługę Application Insights. Element Miasto może być pusta w dziennikach. Użycie schematu w celu identyfikowania inne elementy, które można tworzyć zapytania, które mogą zawierać dane dla dzienników.

    To zapytanie zwraca informacje podobne do następującego tekstu:

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="analyze-the-data-scala"></a>Analizowanie danych: Scala

1. Z [witryny Azure portal](https://portal.azure.com), wybierz platformy Spark w klastrze HDInsight. Z **szybkich łączy** zaznacz **pulpity nawigacyjne klastra**, a następnie wybierz pozycję **notesu programu Jupyter** z sekcji Dashboard__ klastra.

    ![Pulpity nawigacyjne klastra](./media/apache-spark-analyze-application-insight-logs/clusterdashboards.png)
2. W prawym górnym rogu strony Jupyter wybierz **New**, a następnie **Scala**. Zostanie wyświetlona nowa karta przeglądarki zawierająca notesu programu Jupyter na podstawie Scala.
3. W pierwszym polu (o nazwie **komórki**) na stronie wprowadź następujący tekst:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Ten kod konfiguruje Spark rekursywnie dostęp do struktury katalogu dla danych wejściowych. Telemetria usługi Application Insights jest rejestrowany wpis podobny do struktury katalogów `/{telemetry type}/YYYY-MM-DD/{##}/`.

4. Użyj **SHIFT + ENTER** do uruchomienia kodu. Po lewej stronie komórki "\*" pojawia się między nawiasami, aby wskazać, czy kod w tej komórce jest wykonywana. Po zakończeniu "\*" zmiany numeru i dane wyjściowe podobne do następującego tekstu jest wyświetlana poniżej komórki:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. Nową komórkę jest tworzony poniżej pierwszy z nich. Wprowadź następujący tekst w nowej komórce. Zastąp `CONTAINER` i `STORAGEACCOUNT` z nazwą konta usługi Azure storage oraz nazwa kontenera obiektów blob, który zawiera usługi Application Insights dzienniki.

   ```scala
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Użyj **SHIFT + ENTER** do wykonania tej komórki. Zostanie wyświetlony wynik podobny do następującego tekstu:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Ścieżka wasb, zwracany jest lokalizacja danych telemetrycznych usługi Application Insights. Zmiana `hdfs dfs -ls` wiersz w komórce, użyj ścieżki wasb zwrócony, a następnie użyj **SHIFT + ENTER** ponownie uruchomić komórkę. Tym razem wyniki powinien być wyświetlany katalogi, które zawierają dane telemetryczne.

   > [!NOTE]  
   > W pozostałej części kroki opisane w tej sekcji `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` użyto katalogu. Ten katalog nie może istnieć, chyba że Twoje dane telemetrii dla aplikacji sieci web.

6. W następnej komórki wprowadź następujący kod: Zastąp `WASB\_PATH` przy użyciu ścieżki z poprzedniego kroku.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Ten kod tworzy ramkę danych z plików JSON wyeksportowany przez proces eksportu ciągłego. Użyj **SHIFT + ENTER** do uruchomienia tej komórki.

7. W następnej komórki wprowadź, a następnie uruchom następujące polecenie, aby wyświetlić schemat, który Spark utworzony dla plików JSON:

   ```scala
   jsonData.printSchema
   ```

    Schemat dla każdego typu danych telemetrycznych jest inny. Poniższy przykład jest schematu, który jest generowany dla żądania sieci web (dane przechowywane w `Requests` podkatalogu):

        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)

8. Zarejestruj ramki danych jako tabeli tymczasowej, a następnie uruchamiania zapytania względem danych należy wykonać następujące kroki:

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city is not null limit 10").show()
   ```

    Ta kwerenda zwraca Miasto dotycząca pierwszych 20 rekordów, w której context.location.city nie ma wartości null.

   > [!NOTE]  
   > Strukturę context jest obecny w wszystkie dane telemetryczne zarejestrowane przez usługę Application Insights. Element Miasto może być pusta w dziennikach. Użycie schematu w celu identyfikowania inne elementy, które można tworzyć zapytania, które mogą zawierać dane dla dzienników.
   >
   >

    To zapytanie zwraca informacje podobne do następującego tekstu:

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej przykładów korzystania z platformy Apache Spark do pracy z danymi i usługami na platformie Azure zobacz następujące dokumenty:

* [Platforma Apache Spark przy użyciu Power BI: Interakcyjna analiza danych przy użyciu platformy Spark w HDInsight przy użyciu narzędzi do analizy Biznesowej](apache-spark-use-bi-tools.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dziennika witryny sieci Web przy użyciu platformy Apache Spark w HDInsight](apache-spark-custom-library-website-log-analysis.md)

Aby uzyskać informacje na temat tworzenia i uruchamiania aplikacji Spark zobacz następujące dokumenty:

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze usługi Apache Spark przy użyciu programu Livy](apache-spark-livy-rest-interface.md)
