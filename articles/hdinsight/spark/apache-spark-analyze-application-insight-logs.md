---
title: Analizowanie dzienników usługi Application Insight za pomocą platformy Spark — usługa Azure HDInsight
description: Dowiedz się, jak wyeksportować dzienniki usługi Application Insight do magazynu obiektów blob, a następnie analizować dzienniki za pomocą aplikacji Spark on HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 6fd7682f56fbe446904a4acdb39e78525f2523a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435241"
---
# <a name="analyze-application-insights-telemetry-logs-with-apache-spark-on-hdinsight"></a>Analizowanie dzienników telemetrycznych usługi Application Insights za pomocą aplikacji Apache Spark w programie HDInsight

Dowiedz się, jak używać [aplikacji Apache Spark](https://spark.apache.org/) w programie HDInsight do analizowania danych telemetrycznych usługi Application Insight.

[Visual Studio Application Insights](../../azure-monitor/app/app-insights-overview.md) to usługa analityczna, która monitoruje aplikacje sieci web. Dane telemetryczne generowane przez usługę Application Insights można wyeksportować do usługi Azure Storage. Gdy dane są w usłudze Azure Storage, hdinsight może służyć do ich analizowania.

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikacja skonfigurowana do używania usługi Application Insights.

* Znajomość tworzenia klastra HDInsight opartego na systemie Linux. Aby uzyskać więcej informacji, zobacz [Tworzenie platformy Spark apache w programie HDInsight](apache-spark-jupyter-spark-sql.md).

* Przeglądarka internetowa.

Do opracowania i przetestowania tego dokumentu wykorzystano następujące zasoby:

* Dane telemetryczne usługi Application Insights zostały wygenerowane przy użyciu [aplikacji sieci Web Node.js skonfigurowaną do korzystania z usługi Application Insights](../../azure-monitor/app/nodejs.md).

* Do analizy danych użyto platformy Spark opartej na systemie Linux w wersji 3.5 klastra HDInsight.

## <a name="architecture-and-planning"></a>Architektura i planowanie

Na poniższym diagramie przedstawiono architekturę usługi w tym przykładzie:

![Dane przepływające z usługi Application Insights do magazynu obiektów blob, a następnie spark](./media/apache-spark-analyze-application-insight-logs/application-insights.png)

### <a name="azure-storage"></a>Azure Storage

Usługa Application Insights można skonfigurować do ciągłego eksportowania informacji telemetrycznych do obiektów blob. Usługa HDInsight może następnie odczytywać dane przechowywane w obiektach blob. Istnieją jednak pewne wymagania, które należy wykonać:

* **Lokalizacja:** Jeśli konto magazynu i HDInsight znajdują się w różnych lokalizacjach, może to zwiększyć opóźnienie. Zwiększa również koszty, ponieważ opłaty za wyjście są stosowane do danych przemieszczających się między regionami.

    > [!WARNING]  
    > Korzystanie z konta magazynu w innej lokalizacji niż HDInsight nie jest obsługiwane.

* **Typ obiektu blob:** HDInsight obsługuje tylko blokowe obiekty blob. Usługa Application Insights domyślnie używa bloków obiektów blob, więc powinna działać domyślnie z programem HDInsight.

Aby uzyskać informacje na temat dodawania magazynu do istniejącego klastra, zobacz dokument [Dodawanie dodatkowych kont magazynu.](../hdinsight-hadoop-add-storage.md)

### <a name="data-schema"></a>Schemat danych

Usługa Application Insights udostępnia informacje o [modelu danych eksportu](../../azure-monitor/app/export-data-model.md) dla formatu danych telemetrycznych eksportowanych do obiektów blob. Kroki w tym dokumencie używać programu Spark SQL do pracy z danymi. Program Spark SQL może automatycznie wygenerować schemat dla struktury danych JSON zarejestrowanej przez usługę Application Insights.

## <a name="export-telemetry-data"></a>Eksportowanie danych telemetrycznych

Wykonaj kroki opisane w [temacie Konfigurowanie eksportu ciągłego,](../../azure-monitor/app/export-telemetry.md) aby skonfigurować usługę Application Insights do eksportowania informacji telemetrycznych do obiektu blob usługi Azure Storage.

## <a name="configure-hdinsight-to-access-the-data"></a>Konfigurowanie usługi HDInsight w celu uzyskania dostępu do danych

Jeśli tworzysz klaster HDInsight, dodaj konto magazynu podczas tworzenia klastra.

Aby dodać konto usługi Azure Storage do istniejącego klastra, użyj informacji w dokumencie [Dodaj dodatkowe konta magazynu.](../hdinsight-hadoop-add-storage.md)

## <a name="analyze-the-data-pyspark"></a>Analizowanie danych: PySpark

1. Z przeglądarki internetowej przejdź `https://CLUSTERNAME.azurehdinsight.net/jupyter` do miejsca, w którym nazwa klastra jest nazwą klastra.

2. W prawym górnym rogu strony Jupyter wybierz pozycję **Nowy**, a następnie **pozycję PySpark**. Zostanie otwarta nowa karta przeglądarki zawierająca notes Jupyter oparty na pythonie.

3. W pierwszym polu (nazywanym **komórką)** na stronie wprowadź następujący tekst:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Ten kod konfiguruje program Spark w celu cyklicznego dostępu do struktury katalogów danych wejściowych. Dane telemetryczne usługi Application Insights są rejestrowane `/{telemetry type}/YYYY-MM-DD/{##}/`w strukturze katalogów podobnej do .

4. Użyj **klawiszy SHIFT+ENTER,** aby uruchomić kod. Po lewej stronie komórki pojawi\*się ' ' pomiędzy nawiasami, aby wskazać, że kod w tej komórce jest wykonywany. Po jej zakończeniu '\*' zmienia się na liczbę, a wyjście podobne do następującego tekstu jest wyświetlane pod komórką:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...

5. Nowa komórka jest tworzona poniżej pierwszej. Wprowadź następujący tekst w nowej komórce. Zamień `CONTAINER` i `STORAGEACCOUNT` nazwę kontenera usługi Azure Storage i kontenera obiektów blob, który zawiera dane usługi Application Insights.

   ```python
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Aby wykonać tę komórkę, użyj **klawiszy SHIFT+ENTER.** Wynik jest podobny do następującego tekstu:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Zwrócona ścieżka wasbs jest lokalizacją danych telemetrycznych usługi Application Insights. Zmień `hdfs dfs -ls` wiersz w komórce, aby użyć zwróconej ścieżki wasbs, a następnie użyj **klawiszy SHIFT+ENTER,** aby ponownie uruchomić komórkę. Tym razem wyniki powinny wyświetlać katalogi zawierające dane telemetryczne.

   > [!NOTE]  
   > W pozostałych krokach w tej `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` sekcji użyto katalogu. Struktura katalogów może być inna.

6. W następnej komórce wprowadź `WASB_PATH` następujący kod: Zamień ścieżkę z poprzedniego kroku.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Ten kod tworzy ramkę danych z plików JSON eksportowanych przez proces ciągłego eksportowania. Aby uruchomić tę komórkę, użyj **klawiszy SHIFT+ENTER.**
7. W następnej komórce wprowadź i uruchom następujące elementy, aby wyświetlić schemat utworzony przez program Spark dla plików JSON:

   ```python
   jsonData.printSchema()
   ```

    Schemat dla każdego typu danych telemetrycznych jest inny. Poniższy przykład jest schemat, który jest generowany dla `Requests` żądań sieci web (dane przechowywane w podkatalogu):

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

8. Użyj następujących czynności, aby zarejestrować ramkę danych jako tabelę tymczasową i uruchomić kwerendę względem danych:

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    Ta kwerenda zwraca informacje o mieście dla 20 najlepszych rekordów, w których context.location.city nie ma wartości null.

   > [!NOTE]  
   > Struktura kontekstu jest obecna we wszystkich danych telemetrycznych zarejestrowanych przez usługa Application Insights. Element miasta może nie być wypełniane w dziennikach. Schemat służy do identyfikowania innych elementów, które można zbadać, które mogą zawierać dane dla dzienników.

    Ta kwerenda zwraca informacje podobne do następującego tekstu:

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

1. Z przeglądarki internetowej przejdź `https://CLUSTERNAME.azurehdinsight.net/jupyter` do miejsca, w którym nazwa klastra jest nazwą klastra.

2. W prawym górnym rogu strony Jupyter wybierz pozycję **Nowy**, a następnie **pozycję Scala**. Pojawi się nowa karta przeglądarki zawierająca notes Jupyter oparty na scalii.

3. W pierwszym polu (nazywanym **komórką)** na stronie wprowadź następujący tekst:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Ten kod konfiguruje program Spark w celu cyklicznego dostępu do struktury katalogów danych wejściowych. Dane telemetryczne usługi Application Insights są `/{telemetry type}/YYYY-MM-DD/{##}/`rejestrowane w strukturze katalogów podobnej do .

4. Użyj **klawiszy SHIFT+ENTER,** aby uruchomić kod. Po lewej stronie komórki pojawi\*się ' ' pomiędzy nawiasami, aby wskazać, że kod w tej komórce jest wykonywany. Po jej zakończeniu '\*' zmienia się na liczbę, a wyjście podobne do następującego tekstu jest wyświetlane pod komórką:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...

5. Nowa komórka jest tworzona poniżej pierwszej. Wprowadź następujący tekst w nowej komórce. Zamień `CONTAINER` i `STORAGEACCOUNT` nazwę konta usługi Azure Storage i nazwę kontenera obiektów blob, który zawiera dzienniki usługi Application Insights.

   ```scala
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Aby wykonać tę komórkę, użyj **klawiszy SHIFT+ENTER.** Wynik jest podobny do następującego tekstu:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Zwrócona ścieżka wasbs jest lokalizacją danych telemetrycznych usługi Application Insights. Zmień `hdfs dfs -ls` wiersz w komórce, aby użyć zwróconej ścieżki wasbs, a następnie użyj **klawiszy SHIFT+ENTER,** aby ponownie uruchomić komórkę. Tym razem wyniki powinny wyświetlać katalogi zawierające dane telemetryczne.

   > [!NOTE]  
   > W pozostałych krokach w tej `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` sekcji użyto katalogu. Ten katalog może nie istnieć, chyba że dane telemetryczne są dla aplikacji sieci web.

6. W następnej komórce wprowadź `WASB\_PATH` następujący kod: Zamień ścieżkę z poprzedniego kroku.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Ten kod tworzy ramkę danych z plików JSON eksportowanych przez proces ciągłego eksportowania. Aby uruchomić tę komórkę, użyj **klawiszy SHIFT+ENTER.**

7. W następnej komórce wprowadź i uruchom następujące elementy, aby wyświetlić schemat utworzony przez program Spark dla plików JSON:

   ```scala
   jsonData.printSchema
   ```

    Schemat dla każdego typu danych telemetrycznych jest inny. Poniższy przykład jest schemat, który jest generowany dla `Requests` żądań sieci web (dane przechowywane w podkatalogu):

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

8. Użyj następujących czynności, aby zarejestrować ramkę danych jako tabelę tymczasową i uruchomić kwerendę względem danych:

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city isn't null limit 10").show()
   ```

    Ta kwerenda zwraca informacje o mieście dla 20 najlepszych rekordów, w których context.location.city nie ma wartości null.

   > [!NOTE]  
   > Struktura kontekstu jest obecna we wszystkich danych telemetrycznych zarejestrowanych przez usługa Application Insights. Element miasta może nie być wypełniane w dziennikach. Schemat służy do identyfikowania innych elementów, które można zbadać, które mogą zawierać dane dla dzienników.

    Ta kwerenda zwraca informacje podobne do następującego tekstu:

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów korzystania z platformy Apache Spark do pracy z danymi i usługami na platformie Azure, zobacz następujące dokumenty:

* [Apache Spark z analizą biznesową: wykonywanie interaktywnej analizy danych przy użyciu funkcji Spark w funkcji HDInsight za pomocą narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z uczeniem maszynowym: użyj platformy Spark w hdinsight do analizowania temperatury budynku przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z uczeniem maszynowym: użyj iskry w hdinsight, aby przewidzieć wyniki inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dziennika strony internetowej przy użyciu platformy Apache Spark w hdinsight](apache-spark-custom-library-website-log-analysis.md)

Aby uzyskać informacje na temat tworzenia i uruchamiania aplikacji Platformy Spark, zobacz następujące dokumenty:

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Uruchamianie zadań zdalnie w klastrze Apache Spark przy użyciu livy](apache-spark-livy-rest-interface.md)
