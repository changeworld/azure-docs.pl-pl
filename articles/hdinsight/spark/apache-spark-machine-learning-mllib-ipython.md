---
title: Maszyna przykład learning MLlib Spark w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak umożliwia tworzenie aplikacji learning maszyny, która analizuje zestawu danych za pomocą funkcji klasyfikacji za pośrednictwem Regresja logistyczna Spark MLlib.
keywords: Platforma Spark uczenia maszynowego, spark machine learning przykład
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c0fd4baa-946d-4e03-ad2c-a03491bd90c8
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: jgao
ms.openlocfilehash: 1fc89f2181a5b9fb6b6c5a26d974b016fa1926a6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361339"
---
# <a name="use-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Użyj Spark MLlib do tworzenia aplikacji uczenia maszynowego i analizować zestawu danych

Dowiedz się, jak używać platformy Spark [MLlib](https://spark.apache.org/mllib/) do tworzenia aplikacji w celu proste analizy predykcyjnej na Otwórz zestaw danych uczenia maszynowego. Z maszyny wbudowanych platforma Spark uczenia biblioteki, w tym przykładzie użyto *klasyfikacji* za pośrednictwem Regresja logistyczna. 

> [!TIP]
> W tym przykładzie jest również dostępny jako notesu Jupyter w klastrze Spark (Linux), które są tworzone w usłudze HDInsight. Środowisko notesu umożliwia uruchamianie fragmenty kodu języka Python z notesu samej siebie. Aby wykonać samouczek z poziomu Notes, tworzenie klastra Spark, a następnie uruchom notesu Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`). A następnie uruchomić notesu **Spark Machine Learning - analizy predykcyjnej na dane inspekcji żywności przy użyciu MLlib.ipynb** w obszarze **Python** folderu.
>
>

MLlib jest biblioteki Spark core, która udostępnia wiele narzędzi przydatne dla machine learning zadania, w tym narzędzia, które są odpowiednie dla:

* Klasyfikacja
* Regresja
* Klastrowanie
* Temat modelowania
* Rozkład wartości pojedynczej (SVD) i analizy głównych składników (PCA)
* Hipoteza testowania i obliczania statystyk próbki

## <a name="understand-classification-and-logistic-regression"></a>Zrozumienie klasyfikacji i Regresja logistyczna
*Klasyfikacja*, maszynę popularnych uczenia zadań, to proces polegający na sortowanie danych wejściowych w kategorie. To zadanie jest algorytm klasyfikacji, aby dowiedzieć się, jak można przypisać "etykiety", aby wprowadzić dane podane. Na przykład można traktować z algorytmu uczenia maszynowego, który akceptuje podstawowe informacje jako dane wejściowe i zasoby są podzielone na dwie kategorie: zasobów, które powinny sprzedaży i zasobów, które należy przechowywać.

Regresja logistyczna jest algorytmem, której użyjesz dla klasyfikacji. Regresja logistyczna firmy Spark interfejsu API jest przydatne w przypadku *klasyfikacji binarnej*, lub klasyfikacji danych wejściowych do jednej z dwóch grup. Aby uzyskać więcej informacji na temat logistyczna regresji, zobacz [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Podsumowując, tworzy proces Regresja logistyczna *logistyczna funkcja* można przewidzieć prawdopodobieństwo, że wektor wejściowy należy do jednej lub drugiej.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Przykład analizy predykcyjnej w danych kontroli żywności
W tym przykładzie używasz do wykonywania analizy predykcyjnej na dane inspekcji żywności Spark (**Food_Inspections1.csv**) które zostało zakupione w ramach [portalu danych Miasto Chicago](https://data.cityofchicago.org/). Ten zestaw danych zawiera informacje o kontroli ustanowienia żywności, które były przeprowadzane w Chicago, wraz z informacjami dotyczącymi każdego zakładu, naruszeń znaleziono (jeśli istnieje) i wyniki inspekcji. Plik danych CSV jest już dostępne w ramach konta magazynu skojarzone z klastra z **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

W poniższych krokach opracowywania modelu, aby zobaczyć, co jest potrzebne do powodzenie lub Niepowodzenie inspekcji żywności.

## <a name="create-a-spark-mllib-machine-learning-app"></a>Tworzenie aplikacji Spark MLlib machine learning

1. Utwórz notes Jupyter przy użyciu jądra PySpark. Aby uzyskać instrukcje, zobacz [Tworzenie notesu Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Importowania typów wymaganych dla tej aplikacji. Skopiuj i wklej następujący kod do pustej komórki, a następnie naciśnij klawisz **SHIRT + ENTER**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```
    Z powodu jądra PySpark nie ma potrzeby jawnego tworzenia kontekstów. Konteksty Spark i Hive są automatycznie tworzone po uruchomieniu pierwszej komórki kodu. 

## <a name="construct-the-input-dataframe"></a>Konstrukcja dataframe wejściowych

Ponieważ dane pierwotne w formacie CSV, można użyć obiektu context Spark może kopiować plik do pamięci jako tekst bez struktury, a następnie użyć języka Python CSV biblioteki można przeanalizować każdego wiersza danych.

1. Uruchom następujące wiersze do utworzenia odporność rozproszonych zestawu danych (RDD) przez importowanie i analizowanie danych wejściowych.

    ```PySpark
    def csvParse(s):
        import csv
        from StringIO import StringIO
        sio = StringIO(s)
        value = csv.reader(sio).next()
        sio.close()
        return value
    
    inspections = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                    .map(csvParse)
    ```

2. Uruchom następujący kod, aby pobrać jeden wiersz z RDD, co pozwoli podjąć wygląd schemat danych:

    ```PySpark
    inspections.take(1)
    ```

    Wynik jest:

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

    Dane wyjściowe daje wyobrażenie o schemat pliku wejściowego. Zawiera nazwę każda jednostka organizacyjna, typ ustanowienia, adres, dane inspekcji i lokalizacji, między innymi. 

3. Uruchom następujący kod, aby utworzyć dataframe (*df*) i tabeli tymczasowej (*CountResults*) z kilka kolumn, które są przydatne w przypadku analizy predykcyjnej. `sqlContext` Służy do wykonywania transformacji na danych strukturalnych. 

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])
    
    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    Są cztery kolumny zainteresowanie dataframe **identyfikator**, **nazwa**, **wyniki**, i **naruszeń**.

4. Uruchom następujący kod, aby pobrać małej przykładowej danych:

    ```PySpark
    df.show(5)
    ```

    Wynik jest:

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

Zacznijmy zorientować zawiera zestaw danych. 

1. Uruchom następujący kod, aby wyświetlić różne wartości w **wyniki** kolumny:

    ```PySpark
    df.select('results').distinct().show()
    ```

    Wynik jest:

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

2. Uruchom poniższy kod do wizualizacji dystrybucji tych wyników:

    ```PySpark
    %%sql -o countResultsdf
    SELECT results, COUNT(results) AS cnt FROM CountResults GROUP BY results
    ```

    `%%sql` Magic następuje `-o countResultsdf` gwarantuje, że wyniki kwerendy jest trwały lokalnie na serwerze Jupyter (zazwyczaj headnode klastra). Dane wyjściowe jest utrwalony jako [Pandas](http://pandas.pydata.org/) dataframe o określonej nazwie **countResultsdf**. Aby uzyskać więcej informacji na temat polecenia magicznego `%%sql` oraz innych poleceń magicznych dostępnych za pośrednictwem jądra PySpark, zobacz [Jądra dostępne dla notesu Jupyter w klastrze Spark w usłudze HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    Wynik jest:

    ![Dane wyjściowe kwerendy SQL](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "wyników zapytania SQL")


3. Można również użyć [Matplotlib](https://en.wikipedia.org/wiki/Matplotlib), biblioteki użyta do skonstruowania wizualizacji danych do utworzenia wykresu. Ponieważ powierzchni musi zostać utworzona z utrwalonego lokalnie **countResultsdf** dataframe, fragment kodu musi rozpoczynać się od `%%local` magic. Dzięki temu, że jest on uruchamiany lokalnie na serwerze Jupyter.

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

    Wynik jest:

    ![Wyjście Spark machine learning aplikacji - wykres kołowy z pięciu wyników inspekcji różne](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-1.png "maszyny Spark uczenia wyników w danych wyjściowych")

    Istnieje 5 różne wyniki, które mogą mieć inspekcji:

    - Biznesowe, które nie znajdują się
    - Niepowodzenie
    - Powodzenie
    - Przekazywanie z warunkami
    - Poza biznesowa

    Do przewidywania wyników kontroli żywności, konieczne jest opracowanie modelu oparte na naruszenia. Regresja logistyczna jest metoda klasyfikacji binarnej, warto grupować dane wynikowe w dwóch kategorii: **niepowodzenie** i **przekazać**:

    - Powodzenie
        - Powodzenie
        - Przekazywanie z warunkami
    - Niepowodzenie
        - Niepowodzenie
    - Odrzuć
        - Biznesowe, które nie znajdują się
        - Poza biznesowa

    Dane z innych wyników ("Nie znajduje się biznesowych" lub "Out of Business") nie są przydatne, i stanowią one niewielka wyników mimo to.

4. Uruchom następujący kod, aby przekonwertować istniejące dataframe (`df`) do nowej dataframe, gdzie każdej kontroli jest reprezentowany jako pary naruszeń etykiety. W tym przypadku etykiety `0.0` reprezentuje awarii etykiety `1.0` reprezentuje sukcesu i etykiety `-1.0` reprezentuje pewnych wyników oprócz tych dwóch. 

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

5. Uruchom następujący kod, aby wyświetlić jeden wiersz etykietami danych:

    ```PySpark
    labeledData.take(1)
    ```

    Wynik jest:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Tworzenie modelu Regresja logistyczna z dataframe wejściowych

Ostatnim zadaniem jest można przekonwertować danych oznaczonych do formatu, który może zostać przeanalizowana przez Regresja logistyczna. Dane wejściowe algorytm Regresja logistyczna musi być zestawem *pary wektor etykiety funkcji*, gdzie "wektor funkcji" jest wektorem liczb reprezentujący punkt wejściowy. Tak należy przekonwertować kolumny "naruszeń", która jest częściową strukturą i zawiera wiele komentarzy w niezależnych, do tablicy liczb rzeczywistych, które maszyna można łatwo zrozumieć.

Na jednym komputerze standardowe uczenia podejście do przetwarzania języka naturalnego jest przypisanie każdego wyrazu różne "index", a następnie przekazać wektora maszynie Algorytm uczenia w taki sposób, że każdy indeks wartość zawiera względne częstotliwości tego wyrazu w ciągu tekstowym.

MLlib zapewnia prosty sposób wykonania tej operacji. Po pierwsze "tokenizacji" każdego ciąg naruszeń, aby uzyskać poszczególnych wyrazów w każdym ciągu. Następnie należy użyć `HashingTF` Aby przekonwertować każdego zestawu tokenów wektor funkcji, które można następnie przekazać do algorytmu Regresja logistyczna do konstruowania modelu. Wszystkie kroki przeprowadzić przy użyciu "potoku".

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Ocena modelu przy użyciu innego elementu dataset

Można użyć modelu utworzonego wcześniej do *prognozowania* co wyników inspekcji nowych będzie, oparte na naruszenia, które zaobserwowano. Uczenia modelu w zestawie danych **Food_Inspections1.csv**. Drugi zestaw danych, można użyć **Food_Inspections2.csv**, do *oceny* siłę tego modelu do nowych danych. Drugi zestaw danych (**Food_Inspections2.csv**) znajduje się w domyślnego kontenera magazynu skojarzone z klastrem.

1. Uruchom następujący kod, aby utworzyć nowy dataframe, **predictionsDf** zawierający prognozy generowane przez model. Fragment kodu tworzy także tabeli tymczasowej o nazwie **prognoz** oparte na dataframe.

    ```PySpark
    testData = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    Powinny pojawić się dane wyjściowe podobne do następujących:

    ```
    # -----------------
    # THIS IS AN OUTPUT
    # -----------------

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

   Brak prognozowania pierwszego wpisu w testowego zestawu danych.
1. `model.transform()` Metoda stosuje przekształcenia do nowych danych z tego samego schematu i przyjeździe Prognozowanie klasyfikowania danych. Można wykonać statystykami proste uzyskanie zorientować się, jak dokładny zostały prognozy:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    Dane wyjściowe wygląda następująco:

    ```
    # -----------------
    # THIS IS AN OUTPUT
    # -----------------

    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    Korzystanie z platformy Spark Regresja logistyczna umożliwia precyzyjne modelu relacji między opisy naruszeń w języku angielskim i czy danej firmy spowoduje powodzenie lub Niepowodzenie inspekcji żywności.

## <a name="create-a-visual-representation-of-the-prediction"></a>Utwórz wizualną reprezentację Prognozowanie
Można teraz utworzyć końcowego wizualizacji ułatwiające przyczyny o wyniki tego testu.

1. Możesz uruchomić wyodrębnianie różnych prognoz i wyniki z **prognoz** tabeli tymczasowej utworzony wcześniej. Następujące kwerendy oddzielnych danych wyjściowych w formacie *true_positive*, *false_positive*, *true_negative*, i *false_negative*. W zapytaniach poniżej, możesz wyłączyć wizualizacji przy użyciu `-q` i zapisać dane wyjściowe (przy użyciu `-o`) jako dataframes, który można następnie użyć z `%%local` magic.

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

1. Na koniec użyj następującego fragmentu kodu można wygenerować za pomocą kreślenia **Matplotlib**.

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

    Powinny być widoczne następujące dane wyjściowe:

    ![Spark machine learning danych wyjściowych aplikacji - wartości procentowe wykresu kołowego inspekcji żywności nie powiodło się. ] (./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Maszyny Spark uczenia wyników w danych wyjściowych")

    Na tym wykresie "pozytywny" odwołuje się do kontroli żywności nie powiodło się, gdy negatywny wynik odwołuje się do kontroli przekazany.

## <a name="shut-down-the-notebook"></a>Zamknij notesu
Po zakończeniu działania aplikacji należy wyłączać aby zwolnić zasoby. W tym celu w menu **File** (Plik) w notesie kliknij polecenie **Close and Halt** (Zamknij i zatrzymaj). Spowoduje to zamknięcie i zamknięcie notesu.

## <a name="seealso"></a>Zobacz też
* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze
* [Platforma Spark i analiza biznesowa: interakcyjna analiza danych na platformie Spark w usłudze HDInsight z użyciem narzędzi do analizy biznesowej](apache-spark-use-bi-tools.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Analiza dzienników witryny sieci Web na platformie Spark w usłudze HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Spark przy użyciu programu Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Zdalne debugowanie aplikacji Spark przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Zeppelin w klastrze Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Spark w usłudze HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
