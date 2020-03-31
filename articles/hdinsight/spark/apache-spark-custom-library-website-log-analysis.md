---
title: Analizowanie dzienników witryn sieci Web za pomocą bibliotek języka Python w platformie Spark — Azure
description: W tym notesie pokazano, jak analizować dane dziennika przy użyciu biblioteki niestandardowej za pomocą platformy Spark na platformie Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: c6bf26d8f3a73db6ee69b2aa0de73872911893bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552716"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-apache-spark-cluster-on-hdinsight"></a>Analizowanie dzienników witryn internetowych przy użyciu niestandardowej biblioteki języka Python z klastrem Apache Spark w programie HDInsight

Ten notes pokazuje, jak analizować dane dziennika przy użyciu biblioteki niestandardowej z Apache Spark na HDInsight. Biblioteka niestandardowa, której używamy, to biblioteka Pythona o nazwie **iislogparser.py**.

## <a name="prerequisites"></a>Wymagania wstępne

Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Zapisywanie nieprzetworzonych danych jako RDD

W tej sekcji używamy notesu [Jupyter](https://jupyter.org) skojarzonego z klastrem Apache Spark w ugasić w udziale HDInsight do uruchamiania zadań przetwarzających nieprzetworzone przykładowe dane i zapisywania ich jako tabeli gałęzi. Przykładowe dane to plik csv (hvac.csv) dostępny domyślnie we wszystkich klastrach.

Po zapisaniu danych jako tabeli gałęzi Apache w następnej sekcji połączymy się z tabelą Gałąź za pomocą narzędzi analizy biznesowej, takich jak Power BI i Tableau.

1. W przeglądarce internetowej `https://CLUSTERNAME.azurehdinsight.net/jupyter`przejdź `CLUSTERNAME` do miejsca , gdzie jest nazwa klastra.

1. Utwórz nowy notes. Wybierz **pozycję Nowy**, a następnie pozycję **PySpark**.

    ![Tworzenie nowego notesu Apache Jupyter](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "Tworzenie nowego notesu Jupyter")

1. Zostanie utworzony i otwarty nowy notes o nazwie Untitled.pynb. Wybierz nazwę notesu u góry i wprowadź przyjazną nazwę.

    ![Wprowadzanie nazwy notesu](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "Wprowadzanie nazwy notesu")

1. Ponieważ notes został utworzony przy użyciu jądra PySpark, nie trzeba tworzyć żadnych kontekstów jawnie. Konteksty Spark i Hive zostaną automatycznie utworzone po uruchomieniu pierwszej komórki kodu. Można rozpocząć od zaimportowania typów, które są wymagane dla tego scenariusza. Wklej następujący fragment kodu w pustej komórce, a następnie naciśnij **klawisze Shift + Enter**.

    ```pyspark
    from pyspark.sql import Row
    from pyspark.sql.types import *
    ```

1. Utwórz rdd przy użyciu przykładowych danych dziennika już dostępnych w klastrze. Dostęp do danych na domyślnym koncie magazynu `\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log`skojarzonym z klastrem można uzyskać pod adresem . Wykonaj następujący kod:

    ```pyspark
    logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')
    ```

1. Pobierz zestaw dziennika przykładu, aby sprawdzić, czy poprzedni krok został ukończony pomyślnie.

    ```pyspark
    logs.take(5)
    ```

    Powinien zostać wyświetlony wynik podobny do następującego tekstu:

    ```output
    [u'#Software: Microsoft Internet Information Services 8.0',
    u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']
    ```

## <a name="analyze-log-data-using-a-custom-python-library"></a>Analizowanie danych dziennika przy użyciu niestandardowej biblioteki języka Python

1. W danych wyjściowych powyżej pierwsze wiersze para zawierają informacje nagłówka i każdy pozostały wiersz pasuje do schematu opisanego w tym nagłówku. Analizowanie takich dzienników może być skomplikowane. Używamy niestandardowej biblioteki Pythona (**iislogparser.py),** która znacznie ułatwia analizowanie takich dzienników. Domyślnie ta biblioteka jest dołączona do klastra `/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py`Platformy Spark w programie HDInsight w programie .

    Jednak ta biblioteka nie `PYTHONPATH` znajduje się w, więc nie możemy `import iislogparser`jej używać za pomocą instrukcji importu, takiej jak . Aby użyć tej biblioteki, musimy rozpowszechniać ją do wszystkich węzłów procesu roboczego. Uruchom poniższy fragment kodu.

    ```pyspark
    sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')
    ```

1. `iislogparser`udostępnia funkcję, `parse_log_line` `None` która zwraca, jeśli wiersz dziennika jest wierszem `LogLine` nagłówka i zwraca wystąpienie klasy, jeśli napotka wiersz dziennika. Użyj `LogLine` klasy, aby wyodrębnić tylko wiersze dziennika z RDD:

    ```pyspark
    def parse_line(l):
        import iislogparser
        return iislogparser.parse_log_line(l)
    logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()
    ```

1. Pobierz kilka wyodrębnionych wierszy dziennika, aby sprawdzić, czy krok został zakończony pomyślnie.

    ```pyspark
    logLines.take(2)
    ```

   Dane wyjściowe powinny być podobne do następującego tekstu:

    ```output
    [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
    2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]
    ```

1. Klasa `LogLine` z kolei ma kilka przydatnych `is_error()`metod, takich jak , który zwraca, czy wpis dziennika ma kod błędu. Ta klasa służy do obliczania liczby błędów w wyodrębnionych wierszach dziennika, a następnie rejestrowania wszystkich błędów w innym pliku.

    ```pyspark
    errors = logLines.filter(lambda p: p.is_error())
    numLines = logLines.count()
    numErrors = errors.count()
    print 'There are', numErrors, 'errors and', numLines, 'log entries'
    errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')
    ```

    Dane wyjściowe `There are 30 errors and 646 log entries`powinny być os.

1. Można również użyć **Matplotlib** do skonstruowania wizualizacji danych. Na przykład jeśli chcesz wyizolować przyczynę żądań, które są uruchamiane przez długi czas, możesz chcieć znaleźć pliki, które zajmują najwięcej czasu, aby służyć średnio. Fragment kodu poniżej pobiera 25 najlepszych zasobów, które miały najwięcej czasu, aby służyć żądanie.

    ```pyspark
    def avgTimeTakenByKey(rdd):
        return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                    .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

    avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])
    ```

   Powinien zostać wyświetlony wynik podobny do następującego tekstu:

    ```output
    [(u'/blogposts/mvc4/step13.png', 197.5),
    (u'/blogposts/mvc2/step10.jpg', 179.5),
    (u'/blogposts/extractusercontrol/step5.png', 170.0),
    (u'/blogposts/mvc4/step8.png', 159.0),
    (u'/blogposts/mvcrouting/step22.jpg', 155.0),
    (u'/blogposts/mvcrouting/step3.jpg', 152.0),
    (u'/blogposts/linqsproc1/step16.jpg', 138.75),
    (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
    (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
    (u'/blogposts/nested/step2.jpg', 126.0),
    (u'/blogposts/adminpack/step1.png', 124.0),
    (u'/BlogPosts/datalistpaging/step2.png', 118.0),
    (u'/blogposts/mvc4/step35.png', 117.0),
    (u'/blogposts/mvcrouting/step2.jpg', 116.5),
    (u'/blogposts/aboutme/basketball.jpg', 109.0),
    (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
    (u'/blogposts/mvc4/step12.png', 106.0),
    (u'/blogposts/linq8/step0.jpg', 105.5),
    (u'/blogposts/mvc2/step18.jpg', 104.0),
    (u'/blogposts/mvc2/step11.jpg', 104.0),
    (u'/blogposts/mvcrouting/step1.jpg', 104.0),
    (u'/blogposts/extractusercontrol/step1.png', 103.0),
    (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
    (u'/blogposts/mvcrouting/step21.jpg', 101.0),
    (u'/blogposts/mvc4/step1.png', 98.0)]
    ```

1. Informacje te można również przedstawić w formie wykresu. Jako pierwszy krok do utworzenia wykresu, najpierw utwórzmy tabelę tymczasową **AverageTime**. Tabela grupuje dzienniki według czasu, aby sprawdzić, czy w danym momencie wystąpiły nietypowe skoki opóźnienia.

    ```pyspark
    avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
    schema = StructType([StructField('Minutes', IntegerType(), True),
                        StructField('Time', FloatType(), True)])

    avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
    avgTimeTakenByMinuteDF.registerTempTable('AverageTime')
    ```

1. Następnie można uruchomić następującą kwerendę SQL, aby uzyskać wszystkie rekordy w tabeli **AverageTime.**

    ```pyspark
    %%sql -o averagetime
    SELECT * FROM AverageTime
    ```

   Magia, `%%sql` `-o averagetime` po której następuje zapewnia, że dane wyjściowe kwerendy jest zachowywane lokalnie na serwerze Jupyter (zazwyczaj headnode klastra). Dane wyjściowe są zachowywane jako [pandas](https://pandas.pydata.org/) dataframe z określoną nazwą **averagetime**.

   Powinien zostać wyświetlony dane wyjściowe, takie jak następujący obraz:

   ![hdinsight jupyter sql query output hdinsight jupyter sql query output hdinsight jupyter sql query output hdins](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "Dane wyjściowe kwerendy SQL")

   Aby uzyskać więcej `%%sql` informacji na temat magii, zobacz [Parametry obsługiwane za pomocą programu %%sql magic](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

1. Do utworzenia wykresu można teraz użyć matplotlib, biblioteki używanej do konstruowania wizualizacji danych. Ponieważ wykres musi być utworzony z lokalnie utrwalonej ramki danych **averagetime,** fragment kodu musi zaczynać się od `%%local` magii. Gwarantuje to, że kod jest uruchamiany lokalnie na serwerze Jupyter.

    ```pyspark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
    plt.xlabel('Time (min)')
    plt.ylabel('Average time taken for request (ms)')
    ```

   Powinien zostać wyświetlony dane wyjściowe, takie jak następujący obraz:

   ![wykres analizy dziennika sieci web apache spark](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Wyjście matplotlib")

1. Po zakończeniu uruchamiania aplikacji należy zamknąć notes, aby zwolnić zasoby. W tym celu w menu **File** (Plik) w notesie wybierz pozycję **Close and Halt** (Zamknij i zatrzymaj). Ta akcja spowoduje zamknięcie i zamknięcie notesu.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami:

* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)