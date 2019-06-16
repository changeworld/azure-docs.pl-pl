---
title: Machine learning przykład z Biblioteka MLlib platformy Spark na HDInsight — Azure
description: Dowiedz się, jak utworzyć aplikację learning maszyny, która analizuje zestawu danych za pomocą funkcji klasyfikacji, za pomocą regresji logistycznej przy użyciu MLlib platformy Spark.
keywords: Platforma Spark uczenia maszynowego, spark machine learning przykład
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: hrasheed
ms.openlocfilehash: 31755dcc247ea3be5fb38249afd98dc72dcbc544
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64717112"
---
# <a name="use-apache-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Biblioteka MLlib platformy Spark Apache umożliwia tworzenie aplikacji uczenia maszynowego i analizować zestaw danych

Dowiedz się, jak używać platformy Apache Spark [MLlib](https://spark.apache.org/mllib/) do tworzenia aplikacji w celu proste analizy predykcyjnej na zestaw open uczenia maszynowego. Z platforma Spark wbudowanych dotyczącym uczenia maszynowego bibliotek w tym przykładzie użyto *klasyfikacji* za pomocą regresji logistycznej. 

Biblioteka MLlib jest podstawowej biblioteki platformy Spark, który udostępnia wiele narzędzi przydatne dla zadania uczenia maszynowego, w tym narzędzia, które są odpowiednie na potrzeby:

* Klasyfikacja
* Regresji
* Klastrowanie
* Modelowanie tematu
* Rozkład wartości pojedynczej (SVD) i analizy głównych składników (UPW)
* Hipotezę, testowania i obliczania statystyk próbki

## <a name="understand-classification-and-logistic-regression"></a>Omówienie funkcji klasyfikacji i regresji logistycznej
*Klasyfikacja*, popularne usługi machine learning zadania polega na sortowanie danych wejściowych na kategorie. To zadanie to algorytm klasyfikacji, aby dowiedzieć się, jak można przypisać "labels" wprowadzanie danych przez Ciebie. Na przykład, można traktować z algorytmu uczenia maszynowego, która przyjmuje informacje o akcjach jako dane wejściowe i zasoby są podzielone na dwie kategorie: zasobów, które powinny być sprzedaży i zasobów, które należy zachować.

Regresja logistyczna jest algorytm, której użyjesz dla klasyfikacji. Platforma Spark regresji logistycznej interfejsu API jest przydatne w przypadku *klasyfikacji binarnej*, lub klasyfikowania danych wejściowych do jednej z dwóch grup. Aby uzyskać więcej informacji na temat regresji logistycznej, zobacz [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Podsumowując, tworzy proces regresji logistycznej *logistycznej funkcja* można przewidzieć prawdopodobieństwo, że wektor wejściowy należy do jednej lub drugiej.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Przykład analizy predykcyjnej na danych inspekcji żywności
W tym przykładzie użyj platformy Spark do wykonania niektórych analizy predykcyjnej na danych inspekcji żywności (**Food_Inspections1.csv**) które zostało zakupione w ramach [portal data Miasto Chicago](https://data.cityofchicago.org/). Ten zestaw danych zawiera informacje o kontroli ustanowienia żywności, które zostały przeprowadzone w Chicago, w tym informacje o każde przedsiębiorstwo, naruszenia znaleziono (jeśli istnieje) oraz wyniki inspekcji. Jest już dostępne na koncie magazynu skojarzonym z klastrem w pliku danych CSV **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

W poniższych krokach utworzysz model, aby zobaczyć, co jest potrzebne do powodzenia lub niepowodzenia kontroli żywności.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Utwórz aplikację Biblioteka MLlib platformy Spark Apache machine learning

1. Utwórz notes Jupyter przy użyciu jądra PySpark. Aby uzyskać instrukcje, zobacz [Tworzenie notesu Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Zaimportuj typy wymagane dla tej aplikacji. Skopiuj i wklej następujący kod do pustej komórki, a następnie naciśnij **SHIFT + ENTER**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```
    Ze względu na jądra PySpark nie ma potrzeby jawnego tworzenia kontekstów. Konteksty Spark i Hive są automatycznie tworzone po uruchomieniu pierwszej komórki kodu. 

## <a name="construct-the-input-dataframe"></a>Konstrukcja ramki danych wejściowych

Ponieważ dane pierwotne w formacie CSV, można użyć kontekstu aparatu Spark może kopiować plik do pamięci jako tekst bez struktury i następnie użyć biblioteki CSV języka Python do analizowania każdego wiersza danych.

1. Uruchom następujące wiersze do tworzenia odpornych rozproszonych zestawu danych (RDD), importowanie i analizowanie danych wejściowych.

    ```PySpark
    def csvParse(s):
        import csv
        from StringIO import StringIO
        sio = StringIO(s)
        value = csv.reader(sio).next()
        sio.close()
        return value
    
    inspections = sc.textFile('/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                    .map(csvParse)
    ```

2. Uruchom następujący kod, aby pobrać jeden wiersz z RDD, możesz więc wyglądu schemat danych:

    ```PySpark
    inspections.take(1)
    ```

    Dane wyjściowe to:

    ```
    [['413707',
        'LUNA PARK INC',
        'LUNA PARK  DAY CARE',
        '2049789',
        "Children's Services Facility",
        'Risk 1 (High)',
        '3250 W FOSTER AVE ',
        'CHICAGO',
        'IL',
        '60625',
        '09/21/2010',
        'License-Task Force',
        'Fail',
        '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
        '41.97583445690982',
        '-87.7107455232781',
        '(41.97583445690982, -87.7107455232781)']]
    ```

    Dane wyjściowe zawierają pomysł schemat pliku wejściowego. Zawiera nazwę każdego zakładu, typ zakładu, adres, dane inspekcji i lokalizacji, między innymi. 

3. Uruchom poniższy kod, aby utworzyć ramkę danych (*df*) i tabelę tymczasową (*CountResults*) przy użyciu kilka kolumn, które są przydatne do analizy predykcyjnej. `sqlContext` Umożliwia wykonywanie przekształceń na danych strukturalnych. 

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])
    
    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    Są cztery kolumny zainteresowanie ramki danych **identyfikator**, **nazwa**, **wyniki**, i **naruszenia**.

4. Uruchom poniższy kod, aby uzyskać małą próbkę danych:

    ```PySpark
    df.show(5)
    ```

    Dane wyjściowe to:

    ```
    +------+--------------------+-------+--------------------+
    |    id|                name|results|          violations|
    +------+--------------------+-------+--------------------+
    |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
    |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
    |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
    |413708|BENCHMARK HOSPITA...|   Pass|                    |
    |413722|           JJ BURGER|   Pass|                    |
    +------+--------------------+-------+--------------------+
    ```

## <a name="understand-the-data"></a>Zrozumieć dane

Zacznijmy od wolumenu zawiera zestaw danych. 

1. Uruchom poniższy kod, aby wyświetlić różne wartości w **wyniki** kolumny:

    ```PySpark
    df.select('results').distinct().show()
    ```

    Dane wyjściowe to:

    ```
    +--------------------+
    |             results|
    +--------------------+
    |                Fail|
    |Business Not Located|
    |                Pass|
    |  Pass w/ Conditions|
    |     Out of Business|
    +--------------------+
    ```

2. Uruchom następujący kod, aby wizualizować dystrybucji następujące wyniki:

    ```PySpark
    %%sql -o countResultsdf
    SELECT COUNT(results) AS cnt, results FROM CountResults GROUP BY results
    ```

    `%%sql` Magic następuje `-o countResultsdf` gwarantuje, że wyniki kwerendy jest trwały lokalnie na serwerze programu Jupyter (zazwyczaj węzła głównego klastra). Dane wyjściowe są utrwalane jako [Pandas](https://pandas.pydata.org/) ramka danych o określonej nazwie **countResultsdf**. Aby uzyskać więcej informacji na temat `%%sql` magic i innych poleceń magicznych dostępnych za pośrednictwem jądra PySpark, zobacz [jądra dostępne dla notesu Jupyter w klastrze z klastrami Apache Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    Dane wyjściowe to:

    ![Dane wyjściowe zapytań SQL](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "wyników zapytania SQL")


3. Można również użyć [Matplotlib](https://en.wikipedia.org/wiki/Matplotlib), biblioteki, używany do budowy wizualizacji danych, aby utworzyć wykres. Ponieważ musi zostać utworzony wykres z utrwalonego lokalnie **countResultsdf** dataframe, fragment kodu musi zaczynać się od `%%local` magic. Zapewnia to, że kod jest uruchamiany lokalnie na serwerze programu Jupyter.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = countResultsdf['results']
    sizes = countResultsdf['cnt']
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Dane wyjściowe to:

    ![Wyjście aplikacji uczenia maszynowego platformy Spark - wykres kołowy z pięciu wyników inspekcji distinct](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-1.png "platformy Spark usługi machine learning wynik w danych wyjściowych")

    Przewidywanie wyników inspekcji żywności, konieczne jest opracowanie modelu, w oparciu o naruszenia. Ponieważ regresji logistycznej jest metoda klasyfikacji binarnej, warto grupować dane wynikowe w dwie kategorie: **Niepowodzenie** i **przekazać**:

   - — Dostęp próbny
       - — Dostęp próbny
       - Przekaż z warunkami
   - Niepowodzenie
       - Niepowodzenie
   - Odrzuć
       - Firmy, które nie znajdują się
       - Działalność

     Dane z innych wyników ("Nie znajduje się biznesowe" lub "Out of Business") nie są przydatne i stanowią bardzo małego procentu wyniki mimo to.

4. Uruchom poniższy kod, który można przekonwertować istniejących dataframe (`df`) do nowych elementów dataframe, gdzie każdej kontroli jest reprezentowany jako pary naruszenia etykiety. W tym przypadku etykiety `0.0` reprezentuje awarię etykiety `1.0` reprezentuje sukcesu i etykiety `-1.0` reprezentuje niektórych wyników, oprócz tych dwóch. 

    ```PySpark
    def labelForResults(s):
        if s == 'Fail':
            return 0.0
        elif s == 'Pass w/ Conditions' or s == 'Pass':
            return 1.0
        else:
            return -1.0
    label = UserDefinedFunction(labelForResults, DoubleType())
    labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')
    ```

5. Uruchom poniższy kod, aby wyświetlić jeden wiersz danych oznaczonych:

    ```PySpark
    labeledData.take(1)
    ```

    Dane wyjściowe to:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Tworzenie modelu regresji logistycznej na podstawie danych wejściowych ramkę danych

Ostatnim zadaniem jest przeprowadzenie konwersji danych oznaczonych do formatu, które mogą być analizowane przez regresji logistycznej. Dane wejściowe do algorytmu regresji logistycznej musi być zestaw *pary wektor funkcja etykiet*, gdzie "wektor funkcji" jest wektorem liczb reprezentujący punkt wejściowy. Tak musisz przekonwertować kolumny "naruszeń", która jest częściową strukturą i zawiera liczbę komentarzy w dowolny tekst, na tablicę liczb rzeczywistych, które maszyna może łatwo zrozumieć.

Na jednym komputerze standardowa uczenia podejście do przetwarzania języka naturalnego jest przypisywanie poszczególnych unikatowych wyrazów "index", a następnie przekazać wektor maszyną algorytmu uczenia w taki sposób, że wartość każdy indeks zawiera względną częstotliwość tego wyrazu w ciągu tekstowym.

Biblioteka MLlib zapewnia łatwy sposób wykonania tej operacji. Po pierwsze "tokenizację" każdego ciągu naruszenia, aby uzyskać poszczególnych wyrazów w każdym ciągu. Następnie należy użyć `HashingTF` do konwersji każdy zbiór tokenów do wektora funkcji, które mogą być następnie przekazywany do algorytmu regresji logistycznej w celu utworzenia modelu. Przeprowadź wszystkie te kroki w kolejności przy użyciu "pipeline".

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Ocena modelu przy użyciu innego zestawu danych

Możesz skorzystać z modelu utworzonego wcześniej do *przewidzieć* co ma być wyników inspekcji nowy, oparty na podstawie naruszeń, które zostały zaobserwowane. Skonfigurowanych pod kątem tego modelu w zestawie danych **Food_Inspections1.csv**. Drugi zestaw danych, można użyć **Food_Inspections2.csv**, *oceny* siła tego modelu na nowych danych. Ten drugi zestaw danych (**Food_Inspections2.csv**) jest w domyślnym kontenerze magazynu skojarzonego z klastrem.

1. Uruchom poniższy kod, aby utworzyć nowy ramkę danych **predictionsDf** zawierający prognoz wygenerowanych przez model. Fragment kodu tworzy również tabeli tymczasowej o nazwie **prognozy** oparte na ramki danych.

    ```PySpark
    testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    Powinny pojawić się dane wyjściowe podobne do następujących:

    ```
    ['id',
        'name',
        'results',
        'violations',
        'words',
        'features',
        'rawPrediction',
        'probability',
        'prediction']
    ```

1. Spójrz na jeden z prognozy. Uruchom ten fragment kodu:

    ```PySpark
    predictionsDf.take(1)
    ```

   Brak prognozy dotyczące pierwszej pozycji w zestawie danych testowych.
1. `model.transform()` Metoda stosuje przekształcenia do żadnych nowych danych z tego samego schematu i pojawić się w prognozie klasyfikowania danych. Możesz wykonać statystykami prosty, aby zorientować się o tym, jak dokładny było prognozy są tym:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    Dane wyjściowe wyglądają następująco:

    ```
    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    Za pomocą regresji logistycznej przy użyciu platformy Spark umożliwia dokładne model relacji między opisy naruszeń w języku angielskim i tego, czy dany firm będzie zakończone powodzeniem lub niepowodzeniem kontroli żywności.

## <a name="create-a-visual-representation-of-the-prediction"></a>Tworzenie wizualnej reprezentacji prognozowania
Teraz można utworzyć wizualizację końcowego ułatwiające przyczyny o wyniki tego testu.

1. Rozpocznij dzięki możliwości wyodrębniania różnych prognozy i wyniki z **prognozy** utworzonej wcześniej tabeli tymczasowej. Następujące zapytania oddzielnych danych wyjściowych w formacie *true_positive*, *false_positive*, *true_negative*, i *false_negative*. W poniższych zapytań możesz wyłączyć wizualizacji przy użyciu `-q` i również zapisywać dane wyjściowe (przy użyciu `-o`) jako elementy dataframe, które mogą być następnie używane z `%%local` magic.

    ```PySpark
    %%sql -q -o true_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

    ```PySpark
    %%sql -q -o true_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

1. Na koniec użyj następującego fragmentu kodu, aby wygenerować wykres, za pomocą **Matplotlib**.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = ['True positive', 'False positive', 'True negative', 'False negative']
    sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Powinny zostać wyświetlone następujące dane wyjściowe:

    ![Platforma Spark usługi machine learning dane wyjściowe aplikacji — wykres kołowy procenty inspekcji żywności nie powiodło się. ](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Platformy Spark usługi machine learning wynik w danych wyjściowych")

    Na tym wykresie "pozytywny" odnosi się do kontroli żywności nie powiodło się, gdy wynik ujemny oznacza sukces inspekcji.

## <a name="shut-down-the-notebook"></a>Zamknij Notes
Po zakończeniu działania aplikacji, należy zamknąć notes, aby zwolnić zasoby. W tym celu w menu **File** (Plik) w notesie wybierz pozycję **Close and Halt** (Zamknij i zatrzymaj). Spowoduje to zamknięcie i zamknięcie notesu.

## <a name="seealso"></a>Zobacz też
* [Omówienie: Platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze
* [Platforma Apache Spark przy użyciu Power BI: Interakcyjna analiza danych przy użyciu platformy Spark w HDInsight przy użyciu narzędzi do analizy Biznesowej](apache-spark-use-bi-tools.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Analiza dziennika witryny sieci Web przy użyciu platformy Apache Spark w HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Zdalne debugowanie aplikacji platformy Apache Spark przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Apache Zeppelin na HDInsight klastra Apache Spark](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
