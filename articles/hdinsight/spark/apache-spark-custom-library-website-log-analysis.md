---
title: Analizowanie dzienników witryn sieci Web za pomocą biblioteki języka Python na platformie Spark - Azure
description: Ten notes pokazuje, jak analizować dane dzienników z platformy Spark w usłudze Azure HDInsight przy użyciu niestandardową biblioteką.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: hrasheed
ms.openlocfilehash: bef71f210e015dc10cd6f5c0c655d0d3beee3655
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124445"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-apache-spark-cluster-on-hdinsight"></a>Analizowanie dzienników witryn sieci Web przy użyciu klastra Apache Spark w HDInsight przy użyciu niestandardową biblioteką języka Python

Ten notes pokazuje, jak analizować dane dzienników z platformy Apache Spark w HDInsight przy użyciu niestandardową biblioteką. Niestandardową biblioteką używamy to biblioteka języka Python o nazwie **iislogparser.py**.

> [!TIP]  
> W tym samouczku jest również dostępny jako notesu programu Jupyter w klastrze Spark (Linux), który zostanie utworzony w HDInsight. Środowisko notesu umożliwia uruchamianie fragmenty kodu języka Python z samego notesu. Aby wykonać samouczek z poziomu Notes, utworzyć klaster platformy Spark, uruchamiania notesu programu Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), a następnie uruchomić Notes **analizowanie dzienników za pomocą platformy Spark za pomocą niestandardowych library.ipynb** w obszarze **PySpark**  folderu.
>
>

**Wymagania wstępne:**

Należy dysponować następującymi elementami:

* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Zapisz dane pierwotne jako RDD
W tej sekcji użyto [Jupyter](https://jupyter.org) notesu skojarzone z klastra Apache Spark w HDInsight do uruchomienia zadania, które przetworzyć Twojego przykładowe dane nieprzetworzone i zapisz go jako tabeli programu Hive. Dane przykładowe są plik CSV (hvac.csv) dostępne we wszystkich klastrach domyślnie.

Po zapisaniu danych jako tabelę programu Apache Hive, w następnej sekcji możemy połączy się w tabeli programu Hive przy użyciu narzędzi analizy Biznesowej, takich jak usługa Power BI i Tableau.

1. W witrynie [Azure Portal](https://portal.azure.com/) na tablicy startowej kliknij kafelek klastra Spark (jeśli został przypięty do tablicy startowej). Możesz także przejść do klastra, wybierając polecenia **Przeglądaj wszystko** > **Klastry usługi HDInsight**.   
2. W bloku klastra Spark kliknij pozycję **Pulpit nawigacyjny klastra**, a następnie opcję **Jupyter Notebook**. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia administratora klastra.

   > [!NOTE]
   > Można również przejść do aplikacji Jupyter Notebook dla klastra, otwierając następujący adres URL w przeglądarce. Zastąp ciąg **CLUSTERNAME** nazwą klastra:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Utwórz nowy notes. Kliknij opcję **New** (Nowy), a następnie kliknij pozycję **PySpark**.

    ![Tworzenie nowego notesu Jupyter](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "Tworzenie nowego notesu Jupyter")
4. Zostanie utworzony i otwarty nowy notes o nazwie Untitled.pynb. Kliknij nazwę notesu u góry, a następnie wprowadź przyjazną nazwę.

    ![Wprowadzanie nazwy notesu](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "Wprowadzanie nazwy notesu")
5. Ponieważ notes został utworzony z użyciem jądra PySpark, nie ma konieczności jawnego tworzenia kontekstów. Konteksty Spark i Hive zostaną automatycznie utworzone po uruchomieniu pierwszej komórki kodu. Aby zacząć od importowania typów, które są wymagane dla tego scenariusza. Wklej następujący fragment kodu do pustej komórki, a następnie naciśnij klawisze **SHIFT + ENTER**.

        from pyspark.sql import Row
        from pyspark.sql.types import *


1. Utwórz RDD przy użyciu przykładowych danych dziennika już dostępne w klastrze. Można uzyskać dostęp do danych domyślne konto magazynu skojarzone z klastrem pod adresem **\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log**.

        logs = sc.textFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')


1. Pobrać przykładowy dziennik równa upewnij się, że w poprzednim kroku zostało ukończone pomyślnie.

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

## <a name="analyze-log-data-using-a-custom-python-library"></a>Analizuj dane dzienników przy użyciu niestandardowej biblioteki języka Python
1. W powyższych danych wyjściowych pierwszych kilka wierszy zawierają informacje nagłówka, a każdy wiersz pozostałe pasuje do schematu, opisane w tym nagłówku. Analizowanie dzienników taki może być skomplikowane. Dlatego używamy niestandardową biblioteką języka Python (**iislogparser.py**) sprawia to, że analizy tych dzienników, które są znacznie łatwiejsze. Domyślnie ta biblioteka jest uwzględniane na potrzeby klastra platformy Spark na HDInsight przy **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**.

    Jednak nie jest w tej bibliotece `PYTHONPATH` dlatego firma Microsoft nie może używać przy użyciu instrukcji importu, takich jak `import iislogparser`. Aby użyć tej biblioteki, firma Microsoft rozpowszechnienie go do wszystkich węzłów procesu roboczego. Uruchom poniższy fragment kodu.

        sc.addPyFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')


1. `iislogparser` udostępnia funkcję `parse_log_line` zwracającego `None` czy wiersza dziennika wiersz nagłówka, a zwraca wystąpienie `LogLine` klasy, jeśli wykryje nieważną wiersza dziennika. Użyj `LogLine` klasy można wyodrębnić tylko wiersze dziennika z RDD:

        def parse_line(l):
            import iislogparser
            return iislogparser.parse_log_line(l)
        logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()
2. Pobrać kilka wyodrębnione wierszy dziennika do sprawdzenia, czy krok została ukończona pomyślnie.

       logLines.take(2)

   Dane wyjściowe będą podobne do następujących:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
        2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]
3. `LogLine` Klasy, z kolei ma niektóre przydatne metody, takie jak `is_error()`, która zwraca, czy wpis dziennika ma kod błędu. Służy do obliczenia liczba błędów w wyodrębnionych wierszy dziennika, a następnie zaloguj się do innego pliku wszystkie błędy.

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
4. Można również użyć **Matplotlib** do utworzenia wizualizacji danych. Na przykład jeśli chcesz ustalić przyczynę żądania, które są uruchamiane przez długi czas, można znaleźć plików, które zajmuje najwięcej czasu, aby obsługiwać średnio.
   Poniższy fragment kodu pobiera główne zasoby 25, które miały najwięcej czasu ma obsługiwać żądania.

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
5. Może także prezentować te informacje w formie wykresu. Pierwszym krokiem do utworzenia wykresu, Daj nam najpierw utworzyć tabelę tymczasową **AverageTime**. Tabela grupy dzienników przez czas, aby zobaczyć, jeśli wystąpiły jakiekolwiek skoki nietypowe opóźnienia w danym momencie.

       avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
       schema = StructType([StructField('Minutes', IntegerType(), True),
                            StructField('Time', FloatType(), True)])

       avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
       avgTimeTakenByMinuteDF.registerTempTable('AverageTime')
6. Następnie można uruchomić następujące zapytanie SQL w celu pobrania wszystkich rekordów **AverageTime** tabeli.

       %%sql -o averagetime
       SELECT * FROM AverageTime

   `%%sql` Magic następuje `-o averagetime` gwarantuje, że wyniki kwerendy jest trwały lokalnie na serwerze programu Jupyter (zazwyczaj węzła głównego klastra). Dane wyjściowe są utrwalane jako [Pandas](https://pandas.pydata.org/) ramka danych o określonej nazwie **averagetime**.

   Powinny pojawić się dane wyjściowe podobne do następujących:

   ![Dane wyjściowe zapytań SQL](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "wyników zapytania SQL")

   Aby uzyskać więcej informacji na temat `%%sql` magic, zobacz [parametry obsługiwane w %% magicznym wyrażeniem sql](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).
7. Teraz umożliwia Matplotlib, biblioteki, używany do budowy wizualizacji danych, aby utworzyć wykres. Ponieważ musi zostać utworzony wykres z utrwalonego lokalnie **averagetime** dataframe, fragment kodu musi zaczynać się od `%%local` magic. Zapewnia to, że kod jest uruchamiany lokalnie na serwerze programu Jupyter.

       %%local
       %matplotlib inline
       import matplotlib.pyplot as plt

       plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
       plt.xlabel('Time (min)')
       plt.ylabel('Average time taken for request (ms)')

   Powinny pojawić się dane wyjściowe podobne do następujących:

   ![Dane wyjściowe Matplotlib](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Matplotlib danych wyjściowych")
8. Po zakończeniu działania aplikacji należy ją zamknąć, aby zwolnić zasoby. W tym celu w menu **File** (Plik) w notesie kliknij polecenie **Close and Halt** (Zamknij i zatrzymaj). Spowoduje to zakończenie pracy i zamknięcie notesu.

## <a name="seealso"></a>Zobacz też
* [Omówienie: Platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze
* [Platforma Apache Spark przy użyciu Power BI: Interakcyjna analiza danych przy użyciu platformy Spark w HDInsight przy użyciu narzędzi do analizy Biznesowej](apache-spark-use-bi-tools.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Tworzenie i przesyłanie aplikacji Apache Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Zdalne debugowanie aplikacji platformy Apache Spark przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Apache Zeppelin na HDInsight klastra Apache Spark](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
