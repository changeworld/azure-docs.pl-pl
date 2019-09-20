---
title: Analizowanie dzienników witryn sieci Web przy użyciu bibliotek języka Python w platformie Spark — Azure
description: Ten Notes pokazuje, jak analizować dane dzienników przy użyciu biblioteki niestandardowej z platformą Spark w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.openlocfilehash: 6d23e8cfa8d20169d2b63723138b60dafb1069de
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146995"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-apache-spark-cluster-on-hdinsight"></a>Analizowanie dzienników witryn sieci Web przy użyciu niestandardowej biblioteki języka Python z klastrem Apache Spark w usłudze HDInsight

Ten Notes pokazuje, jak analizować dane dzienników przy użyciu biblioteki niestandardowej z Apache Spark w usłudze HDInsight. Używana biblioteka niestandardowa to biblioteka języka Python o nazwie **iislogparser.py**.

> [!TIP]  
> Ten artykuł jest również dostępny jako Notes Jupyter w klastrze Spark (Linux) tworzonym w usłudze HDInsight. Środowisko notesu umożliwia uruchamianie fragmentów kodu w języku Python z poziomu notesu. Aby wykonać artykuł z poziomu notesu, Utwórz klaster Spark, uruchom Notes Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), a następnie uruchom Notes **Analizuj dzienniki z użyciem platformy Spark przy użyciu biblioteki niestandardowej. ipynb** w folderze **PySpark** .

**Wymagania wstępne:**

Wymagane są następujące elementy:

* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Zapisz dane pierwotne jako RDD
W tej sekcji używamy notesu [Jupyter](https://jupyter.org) skojarzonego z klastrem Apache Spark w usłudze HDInsight w celu uruchamiania zadań przetwarzających pierwotne dane przykładowe i zapisując je jako tabelę programu Hive. Przykładowe dane to plik CSV (HVAC. csv) domyślnie dostępny dla wszystkich klastrów.

Po zapisaniu danych jako tabeli Apache Hive w następnej sekcji będziemy łączyć się z tabelą programu Hive przy użyciu narzędzi analizy biznesowej, takich jak Power BI i Tableau.

1. W witrynie [Azure Portal](https://portal.azure.com/) na tablicy startowej kliknij kafelek klastra Spark (jeśli został przypięty do tablicy startowej). Możesz także przejść do klastra, wybierając polecenia **Przeglądaj wszystko** > **Klastry usługi HDInsight**.

2. W bloku klastra Spark kliknij pozycję **Pulpit nawigacyjny klastra**, a następnie opcję **Jupyter Notebook**. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia administratora klastra.

   > [!NOTE]
   > Można również przejść do aplikacji Jupyter Notebook dla klastra, otwierając następujący adres URL w przeglądarce. Zastąp ciąg **CLUSTERNAME** nazwą klastra:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Utwórz nowy notes. Kliknij opcję **New** (Nowy), a następnie kliknij pozycję **PySpark**.

    ![Tworzenie nowego notesu Apache Jupyter](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "Tworzenie nowego notesu Jupyter")

4. Zostanie utworzony i otwarty nowy notes o nazwie Untitled.pynb. Kliknij nazwę notesu u góry, a następnie wprowadź przyjazną nazwę.

    ![Wprowadzanie nazwy notesu](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "Wprowadzanie nazwy notesu")
5. Ponieważ notes został utworzony z użyciem jądra PySpark, nie ma konieczności jawnego tworzenia kontekstów. Konteksty Spark i Hive zostaną automatycznie utworzone po uruchomieniu pierwszej komórki kodu. Możesz rozpocząć od zaimportowania typów, które są wymagane dla tego scenariusza. Wklej następujący fragment kodu do pustej komórki, a następnie naciśnij klawisze **SHIFT + ENTER**.

        from pyspark.sql import Row
        from pyspark.sql.types import *

6. Utwórz RDD za pomocą przykładowych danych dziennika, które są już dostępne w klastrze. Możesz uzyskać dostęp do danych na domyślnym koncie magazynu skojarzonym z klastrem w **\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log**.

        logs = sc.textFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')

7. Pobierz przykładowy zestaw dzienników, aby sprawdzić, czy poprzedni krok został ukończony pomyślnie.

        logs.take(5)

    Powinny pojawić się dane wyjściowe podobne do następujących:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [u'#Software: Microsoft Internet Information Services 8.0',
         u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']

## <a name="analyze-log-data-using-a-custom-python-library"></a>Analizowanie danych dzienników przy użyciu niestandardowej biblioteki języka Python

1. W danych wyjściowych powyżej pierwsze dwa wiersze zawierają informacje nagłówka, a każdy pozostały wiersz jest zgodny ze schematem opisanym w tym nagłówku. Analizowanie takich dzienników może być skomplikowane. Dlatego używamy niestandardowej biblioteki języka Python (**iislogparser.py**), która znacznie ułatwia analizowanie takich dzienników. Domyślnie ta biblioteka jest dołączona do klastra Spark w usłudze HDInsight pod adresem **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**.

    Jednak ta biblioteka nie jest w `PYTHONPATH` tej bibliotece, dlatego nie można jej używać przy użyciu instrukcji import, takiej jak. `import iislogparser` Aby można było korzystać z tej biblioteki, musimy ją rozpowszechnić do wszystkich węzłów procesu roboczego. Uruchom Poniższy fragment kodu.

        sc.addPyFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')

1. `iislogparser`zapewnia funkcję `parse_log_line` , która zwraca `None` , jeśli wiersz dziennika jest wierszem nagłówka i `LogLine` zwraca wystąpienie klasy, jeśli napotka wiersz dziennika. Użyj klasy `LogLine` , aby wyodrębnić tylko wiersze dziennika z RDD:

        def parse_line(l):
            import iislogparser
            return iislogparser.parse_log_line(l)
        logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()

1. Pobierz kilka wyodrębnionych wierszy dziennika, aby sprawdzić, czy krok zakończył się pomyślnie.

       logLines.take(2)

   Dane wyjściowe będą podobne do następujących:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
        2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]

1. Klasa z kolei ma kilka przydatnych metod, takich jak `is_error()`, które zwracają, czy wpis dziennika ma kod błędu. `LogLine` Użyj tego polecenia, aby obliczyć liczbę błędów w wyodrębnionych wierszach dziennika, a następnie Rejestruj wszystkie błędy w innym pliku.

       errors = logLines.filter(lambda p: p.is_error())
       numLines = logLines.count()
       numErrors = errors.count()
       print 'There are', numErrors, 'errors and', numLines, 'log entries'
       errors.map(lambda p: str(p)).saveAsTextFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')

   Powinny pojawić się dane wyjściowe podobne do następujących:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       There are 30 errors and 646 log entries
1. Możesz również użyć **matplotlib** do konstruowania wizualizacji danych. Na przykład jeśli chcesz odizolować przyczynę żądań, które są uruchamiane przez długi czas, możesz chcieć znaleźć pliki, które najprawdopodobniej przekroczą średnią.
   Poniższy fragment kodu pobiera 25 najpopularniejszych zasobów, które miały najwięcej czasu na przekazanie żądania.

       def avgTimeTakenByKey(rdd):
           return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                   lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                   lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                     .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

       avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])

   Powinny pojawić się dane wyjściowe podobne do następujących:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

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

1. Te informacje można również przedstawić w formie kreślenia. Jako pierwszy krok tworzenia wykresu, pozwól nam najpierw utworzyć tymczasową tabelę tabeli **AverageTime**. W tabeli są pogrupowane dzienniki według czasu, aby sprawdzić, czy w danym momencie wystąpił nietypowe opóźnienia.

       avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
       schema = StructType([StructField('Minutes', IntegerType(), True),
                            StructField('Time', FloatType(), True)])

       avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
       avgTimeTakenByMinuteDF.registerTempTable('AverageTime')

1. Następnie można uruchomić następujące zapytanie SQL, aby pobrać wszystkie rekordy z tabeli **AverageTime** .

       %%sql -o averagetime
       SELECT * FROM AverageTime

   Magiczna`-o averagetime` Metoda polega na tym, że dane wyjściowe zapytania są utrwalane lokalnie na serwerze Jupyter (zazwyczaj węzła głównego klastra). `%%sql` Dane wyjściowe są utrwalane jako ramka [dataPandas](https://pandas.pydata.org/) o określonej nazwie **averagetime**.

   Powinny pojawić się dane wyjściowe podobne do następujących:

   ![dane wyjściowe usługi HDInsight Jupyter SQL qyery](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "Dane wyjściowe zapytania SQL")

   Aby uzyskać więcej informacji na `%%sql` temat Magic, zobacz [Parametry obsługiwane za pomocą Magic%% SQL](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

1. Teraz możesz użyć matplotlib, biblioteki używanej do konstruowania wizualizacji danych, aby utworzyć wykres. Ponieważ wykres musi być tworzony na podstawie lokalnie utrwalonej ramki Dataframe **averagetime** , fragment kodu musi rozpoczynać `%%local` się od Magic. Dzięki temu kod jest uruchamiany lokalnie na serwerze Jupyter.

       %%local
       %matplotlib inline
       import matplotlib.pyplot as plt

       plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
       plt.xlabel('Time (min)')
       plt.ylabel('Average time taken for request (ms)')

   Powinny pojawić się dane wyjściowe podobne do następujących:

   ![Wykres analizy dzienników sieci Web platformy Apache Spark](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Matplotlib dane wyjściowe")

1. Po zakończeniu działania aplikacji należy ją zamknąć, aby zwolnić zasoby. W tym celu w menu **File** (Plik) w notesie kliknij polecenie **Close and Halt** (Zamknij i zatrzymaj). Spowoduje to zakończenie pracy i zamknięcie notesu.

## <a name="seealso"></a>Zobacz też
* [Podsumowanie Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze
* [Apache Spark z usługą BI: Przeprowadzanie interaktywnej analizy danych przy użyciu platformy Spark w usłudze HDInsight przy użyciu narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z Machine Learning: Korzystanie z platformy Spark w usłudze HDInsight do analizowania temperatury kompilacji przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z Machine Learning: Korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Użyj wtyczki Tools plugin for IntelliJ pomysł, aby utworzyć i przesłać Apache Spark aplikacje Scala](apache-spark-intellij-tool-plugin.md)
* [Użyj wtyczki narzędzi HDInsight do IntelliJ pomysł, aby debugować aplikacje Apache Spark zdalnie](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Apache Zeppelin z klastrem Apache Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzaj zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
