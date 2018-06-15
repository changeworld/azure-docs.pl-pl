---
title: Analizowanie dzienników szczegółowe informacje o aplikacji z platformy Spark - Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyeksportować dzienniki szczegółowe informacje o aplikacji do magazynu obiektów blob, a następnie analizować dzienniki z platformy Spark w usłudze HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: 883beae6-9839-45b5-94f7-7eb0f4534ad5
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: larryfr
ms.openlocfilehash: 31068376e20b240a440432319e65f4e479163ee0
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33939604"
---
# <a name="analyze-application-insights-telemetry-logs-with-spark-on-hdinsight"></a>Analizowanie dzienników telemetrii usługi Application Insights z platformy Spark w usłudze HDInsight

Dowiedz się, jak używać platformy Spark w usłudze HDInsight do analizy danych telemetrycznych szczegółowe informacje o aplikacji.

[Visual Studio Application Insights](../../application-insights/app-insights-overview.md) jest usługa analizy, który monitoruje aplikacje sieci web. Dane telemetryczne generowane przez usługę Application Insights mogą być eksportowane do usługi Azure Storage. Po zaimportowaniu danych w usłudze Azure Storage, HDInsight można analizować.

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikacja, która jest skonfigurowana do używania usługi Application Insights.

* Znajomość tworzenia klastra usługi HDInsight opartej na systemie Linux. Aby uzyskać więcej informacji, zobacz [utworzyć Spark w usłudze HDInsight](apache-spark-jupyter-spark-sql.md).

  > [!IMPORTANT]
  > Kroki opisane w tym dokumencie wymagają klastra usługi HDInsight, który używa systemu Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* Przeglądarka sieci web.

Następujące zasoby używane w programie tworzenia i testowania w tym dokumencie:

* Dane telemetrii usługi Application Insights został wygenerowany za pomocą [aplikacji sieci web Node.js skonfigurowane do korzystania z usługi Application Insights](../../application-insights/app-insights-nodejs.md).

* Platforma Spark opartych na systemie Linux w klastrze usługi HDInsight w wersji 3.5 był używany do analizowania danych.

## <a name="architecture-and-planning"></a>Planowanie i architektura

Na poniższym diagramie przedstawiono architekturę usługi, w tym przykładzie:

![Diagram przedstawiający dane przepływające z usługi Application Insights do magazynu obiektów blob, a następnie przetwarzanych przez Spark w usłudze HDInsight](./media/apache-spark-analyze-application-insight-logs/appinsightshdinsight.png)

### <a name="azure-storage"></a>Azure Storage

Aby stale wyeksportować informacje o telemetrii do obiektów blob można skonfigurować usługi Application Insights. HDInsight można odczytać danych przechowywanych w obiektach blob. Istnieją jednak niektóre wymagania, które należy wykonać:

* **Lokalizacja**: Jeśli konto magazynu i HDInsight znajdują się w różnych lokalizacjach, może zwiększyć czas oczekiwania. Zwiększa także koszt, jako wyjście opłaty są stosowane do przechodzenia między regionami danych.

    > [!WARNING]
    > Używanie konta magazynu w innej lokalizacji niż HDInsight nie jest obsługiwane.

* **Typ obiektu blob**: HDInsight obsługuje tylko blokowe obiekty BLOB. Informacje na temat technologii aplikacji domyślnie używa blokowych obiektów blob, więc powinny działać domyślnie z usługą HDInsight.

Aby uzyskać informacje na dodawanie magazynu do istniejącego klastra, zobacz [dodać dodatkowe konta magazynu](../hdinsight-hadoop-add-storage.md) dokumentu.

### <a name="data-schema"></a>Schemat danych

Usługa Application Insights udostępnia [wyeksportować modelu danych](../../application-insights/app-insights-export-data-model.md) informacje o formacie danych telemetrii wyeksportowane do obiektów blob. Kroki opisane w tym dokumencie używania programu Spark SQL do pracy z danymi. Spark SQL może automatycznie generować schematu dla struktury danych JSON, które są zarejestrowane przez usługę Application Insights.

## <a name="export-telemetry-data"></a>Eksportowanie danych telemetrii

Postępuj zgodnie z instrukcjami [skonfigurować eksportu ciągłego](../../application-insights/app-insights-export-telemetry.md) do konfigurowania programu Application Insights, aby wyeksportować informacje o telemetrii do obiektu blob magazynu Azure.

## <a name="configure-hdinsight-to-access-the-data"></a>Konfigurowanie usługi HDInsight w celu uzyskania dostępu do danych

W przypadku tworzenia klastra usługi HDInsight, należy dodać konta magazynu podczas tworzenia klastra.

Aby dodać konto magazynu Azure do istniejącego klastra, użyj informacji w [dodać dodatkowe konta magazynu](../hdinsight-hadoop-add-storage.md) dokumentu.

## <a name="analyze-the-data-pyspark"></a>Analizowanie danych: PySpark

1. Z [portalu Azure](https://portal.azure.com), wybierz użytkownika Spark w klastrze usługi HDInsight. Z **szybkie linki** zaznacz **pulpitów nawigacyjnych klastrów**, a następnie wybierz **notesu Jupyter** z sekcji Dashboard__ klastra.

    ![Pulpitów nawigacyjnych klastrów](./media/apache-spark-analyze-application-insight-logs/clusterdashboards.png)

2. W prawym górnym rogu strony Jupyter wybierz **nowy**, a następnie **PySpark**. Otwiera nową kartę przeglądarki zawierające notesu Jupyter na podstawie języka Python.

3. W pierwszym polu (nazywane **komórki**) na stronie wprowadź następujący tekst:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Ten kod konfiguruje Spark rekursywnie dostępu struktura katalogów dla danych wejściowych. Telemetrię usługi Application Insights jest rejestrowany struktury katalogów, podobnie jak `/{telemetry type}/YYYY-MM-DD/{##}/`.

4. Użyj **SHIFT + ENTER** do uruchomienia kodu. Po lewej stronie komórki "\*" pojawia się w nawiasie, aby wskazać, że kodu w tej komórce jest wykonywana. Po zakończeniu instalacji "\*" zmiany na numer, a dane wyjściowe podobne do następującego tekstu jest wyświetlana poniżej komórki:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. Nowe komórki jest tworzony poniżej pierwsza z nich. Wprowadź następujący tekst w komórce nowe. Zastąp `CONTAINER` i `STORAGEACCOUNT` z nazwą konta magazynu platformy Azure i nazwa kontenera obiektów blob, który zawiera dane usługi Application Insights.

   ```python
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Użyj **SHIFT + ENTER** do wykonania tej komórki. Zostanie wyświetlony wynik jest podobny do następującego tekstu:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Ścieżka wasb, zwracana jest lokalizację danych telemetrii usługi Application Insights. Zmień `hdfs dfs -ls` wiersz w komórce, aby użyć ścieżki wasb zwrócony, a następnie użyj **SHIFT + ENTER** ponownie uruchomić komórki. Teraz, wyniki powinien być wyświetlany katalogi, które zawierają dane telemetryczne.

   > [!NOTE]
   > W pozostałej części kroki opisane w tej sekcji `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` użyto katalogu. Struktury katalogów mogą się różnić.

6. W następnej komórki, wprowadź poniższy kod: Zastąp `WASB_PATH` ze ścieżką z poprzedniego kroku.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Ten kod tworzy dataframe z wyeksportowanego przez proces eksportu ciągłego pliki w formacie JSON. Użyj **SHIFT + ENTER** do uruchomienia tej komórki.
7. W następnej komórki wprowadź i uruchom następujące polecenie, aby wyświetlić schemat Spark utworzony dla pliki w formacie JSON:

   ```python
   jsonData.printSchema()
   ```

    Schemat dla każdego typu danych telemetrycznych różni się. Poniższy przykład jest schemat, generowany dla żądań sieci web (dane przechowywane w `Requests` podkatalogu):

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
8. Aby zarejestrować dataframe jako tabeli tymczasowej, a następnie uruchom zapytania na danych, skorzystaj z następujących:

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    To zapytanie zwraca informacje Miasto top rekordów 20, gdzie context.location.city nie jest zerowa.

   > [!NOTE]
   > Struktura kontekstu znajduje się w wszystkie dane telemetryczne zarejestrowane przez usługę Application Insights. Element miasta może być pusta w dziennikach. Użycie schematu w celu identyfikowania inne elementy, które można wykonać zapytanie zawierające dane dla dzienników.

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

1. Z [portalu Azure](https://portal.azure.com), wybierz użytkownika Spark w klastrze usługi HDInsight. Z **szybkie linki** zaznacz **pulpitów nawigacyjnych klastrów**, a następnie wybierz **notesu Jupyter** z sekcji Dashboard__ klastra.

    ![Pulpitów nawigacyjnych klastrów](./media/apache-spark-analyze-application-insight-logs/clusterdashboards.png)
2. W prawym górnym rogu strony Jupyter wybierz **nowy**, a następnie **Scala**. Pojawi się na nowej karcie przeglądarki zawierające notesu Jupyter opartych na języku Scala.
3. W pierwszym polu (nazywane **komórki**) na stronie wprowadź następujący tekst:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Ten kod konfiguruje Spark rekursywnie dostępu struktura katalogów dla danych wejściowych. Telemetrię usługi Application Insights jest rejestrowany struktury katalogów, podobnie jak `/{telemetry type}/YYYY-MM-DD/{##}/`.

4. Użyj **SHIFT + ENTER** do uruchomienia kodu. Po lewej stronie komórki "\*" pojawia się w nawiasie, aby wskazać, że kodu w tej komórce jest wykonywana. Po zakończeniu instalacji "\*" zmiany na numer, a dane wyjściowe podobne do następującego tekstu jest wyświetlana poniżej komórki:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. Nowe komórki jest tworzony poniżej pierwsza z nich. Wprowadź następujący tekst w komórce nowe. Zastąp `CONTAINER` i `STORAGEACCOUNT` z nazwą konta magazynu platformy Azure i nazwa kontenera obiektów blob, który zawiera usługi Application Insights dzienniki.

   ```scala
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Użyj **SHIFT + ENTER** do wykonania tej komórki. Zostanie wyświetlony wynik jest podobny do następującego tekstu:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Ścieżka wasb, zwracana jest lokalizację danych telemetrii usługi Application Insights. Zmień `hdfs dfs -ls` wiersz w komórce, aby użyć ścieżki wasb zwrócony, a następnie użyj **SHIFT + ENTER** ponownie uruchomić komórki. Teraz, wyniki powinien być wyświetlany katalogi, które zawierają dane telemetryczne.

   > [!NOTE]
   > W pozostałej części kroki opisane w tej sekcji `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` użyto katalogu. Ten katalog nie istnieje, chyba że jest dane telemetryczne dla aplikacji sieci web.

6. W następnej komórki, wprowadź poniższy kod: Zastąp `WASB\_PATH` ze ścieżką z poprzedniego kroku.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Ten kod tworzy dataframe z wyeksportowanego przez proces eksportu ciągłego pliki w formacie JSON. Użyj **SHIFT + ENTER** do uruchomienia tej komórki.

7. W następnej komórki wprowadź i uruchom następujące polecenie, aby wyświetlić schemat Spark utworzony dla pliki w formacie JSON:

   ```scala
   jsonData.printSchema
   ```

    Schemat dla każdego typu danych telemetrycznych różni się. Poniższy przykład jest schemat, generowany dla żądań sieci web (dane przechowywane w `Requests` podkatalogu):

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

8. Aby zarejestrować dataframe jako tabeli tymczasowej, a następnie uruchom zapytania na danych, skorzystaj z następujących:

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city is not null limit 10").show()
   ```

    To zapytanie zwraca informacje Miasto top rekordów 20, gdzie context.location.city nie jest zerowa.

   > [!NOTE]
   > Struktura kontekstu znajduje się w wszystkie dane telemetryczne zarejestrowane przez usługę Application Insights. Element miasta może być pusta w dziennikach. Użycie schematu w celu identyfikowania inne elementy, które można wykonać zapytanie zawierające dane dla dzienników.
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

Więcej przykładów dotyczących platformie Spark do pracy z danymi i usług Azure można znaleźć w następujących dokumentach:

* [Platforma Spark i analiza biznesowa: interakcyjna analiza danych na platformie Spark w usłudze HDInsight z użyciem narzędzi do analizy biznesowej](apache-spark-use-bi-tools.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web na platformie Spark w usłudze HDInsight](apache-spark-custom-library-website-log-analysis.md)

Aby uzyskać informacje na temat tworzenia i uruchamiania aplikacji Spark można znaleźć w następujących dokumentach:

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Spark przy użyciu programu Livy](apache-spark-livy-rest-interface.md)
