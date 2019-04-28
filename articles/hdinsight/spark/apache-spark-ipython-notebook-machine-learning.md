---
title: 'Samouczek: tworzenie aplikacji uczenia maszynowego platformy Apache Spark w usłudze Azure HDInsight'
description: Instrukcje krok po kroku dotyczące tworzenia aplikacji uczenia maszynowego platformy Apache Spark w klastrach HDInsight Spark przy użyciu notesu Jupyter.
ms.service: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 87efac96aa0120bfcc804f7a2a49a5ac3da1036b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124275"
---
# <a name="tutorial-build-an-apache-spark-machine-learning-application-in-hdinsight"></a>Samouczek: tworzenie aplikacji uczenia maszynowego platformy Apache Spark w usłudze HDInsight 

W tym samouczku dowiesz się, jak utworzyć aplikację uczenia maszynowego platformy [Apache Spark](https://spark.apache.org/) dla usługi Azure HDInsight za pomocą środowiska [Jupyter Notebook](https://jupyter.org/). 

[MLlib](https://spark.apache.org/docs/1.1.0/mllib-guide.html) to skalowalna biblioteka uczenia maszynowego platformy Spark, składająca się ze wspólnych narzędzi i algorytmów uczenia się, w tym klasyfikacji, regresji, klastrowania, filtrowania z wykorzystaniem współpracy, zmniejszania wymiarowości, a także źródłowych typów pierwotnych optymalizacji.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie aplikacji uczenia maszynowego platformy Apache Spark

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne:

Obowiązują następujące wymagania:

* Zapoznanie się z przewodnikiem [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="understand-the-data-set"></a>Opis zestawu danych

Aplikacja korzysta z przykładowych danych zawartych w pliku HVAC.csv, który jest domyślnie dostępny we wszystkich klastrach. Plik znajduje się w lokalizacji **\HdiSamples\HdiSamples\SensorSampleData\hvac**. Dane dotyczą temperatury docelowej i temperatury rzeczywistej niektórych budynków, wyposażonych w instalacje grzewczo-wentylacyjne (HVAC). Kolumna **System** zawiera identyfikatory systemów, a kolumna **SystemAge** — liczbę lat użytkowania instalacji grzewczo-wentylacyjnej w danym budynku. Na podstawie danych — temperatury docelowej oraz identyfikatora i liczby lat użytkowania systemu — można przewidzieć warunki temperaturowe (ciepło lub zimno) w budynku.

![Migawka danych używanych w przykładzie uczenia maszynowego platformy Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Migawka danych używanych w przykładzie uczenia maszynowego platformy Spark")

## <a name="develop-a-spark-machine-learning-application-using-spark-mllib"></a>Tworzenie aplikacji uczenia maszynowego platformy Spark przy użyciu biblioteki MLLib platformy Spark

W tej aplikacji [potok uczenia maszynowego](https://spark.apache.org/docs/2.2.0/ml-pipeline.html) platformy Spark jest używany do przeprowadzania klasyfikacji dokumentów. Potoki uczenia maszynowego udostępniają ujednolicony zestaw interfejsów API wysokiego poziomu, które bazują na ramkach danych i pomagają użytkownikom tworzyć oraz dostrajać praktyczne potoki uczenia maszynowego. W potoku dokument jest dzielony na wyrazy, które są przekształcane w wektor elementów liczbowych. Na końcu jest tworzony model predykcyjny przy użyciu etykiet i wektorów elementów. Wykonaj poniższe kroki, aby utworzyć aplikację.

1. Utwórz notes Jupyter przy użyciu jądra PySpark. Aby uzyskać instrukcje, zobacz [Tworzenie notesu Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).
2. Zaimportuj typy wymagane w tym scenariuszu. Wklej następujący fragment kodu do pustej komórki, a następnie naciśnij klawisze **SHIFT + ENTER**. 

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row

    import os
    import sys
    from pyspark.sql.types import *

    from pyspark.mllib.classification import LogisticRegressionWithSGD
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array
    ```
3. Załaduj dane (hvac.csv) i je przeanalizuj, a następnie je wykorzystaj do nauczenia modelu. 

    ```PySpark
    # Define a type called LabelDocument
    LabeledDocument = Row("BuildingID", "SystemInfo", "label")

    # Define a function that parses the raw CSV file and returns an object of type LabeledDocument
    def parseDocument(line):
        values = [str(x) for x in line.split(',')]
        if (values[3] > values[2]):
            hot = 1.0
        else:
            hot = 0.0        

        textValue = str(values[4]) + " " + str(values[5])

        return LabeledDocument((values[6]), textValue, hot)

    # Load the raw HVAC.csv file, parse it using the function
    data = sc.textFile("/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
    training = documents.toDF()
    ```

    Fragment kodu zawiera definicję funkcji, która porównuje temperaturę rzeczywistą z temperaturą docelową. Jeśli temperatura rzeczywista jest większa, przyjmuje się, że w budynku jest ciepło, co odpowiada wartości **1.0**. W przeciwnym razie w budynku jest zimno, co odpowiada wartości **0.0**. 

4. Skonfiguruj potok uczenia maszynowego platformy Spark, która składa się z trzech etapów: tokenizer, hashingTF i lr. 

    ```PySpark
    tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    ```

    Aby uzyskać więcej informacji o potoku i jego działaniu, zobacz <a href="https://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Apache Spark machine learning pipeline</a> (Potok uczenia maszynowego platformy Apache Spark).

5. Dopasuj potok do dokumentu szkoleniowego.
   
    ```PySpark
    model = pipeline.fit(training)
    ```

6. Zweryfikuj dokument szkoleniowy w celu sprawdzenia postępu w opracowywaniu aplikacji.
   
    ```PySpark
    training.show()
    ```
   
    Dane wyjściowe są podobne do następujących:

    ```
    +----------+----------+-----+
    |BuildingID|SystemInfo|label|
    +----------+----------+-----+
    |         4|     13 20|  0.0|
    |        17|      3 20|  0.0|
    |        18|     17 20|  1.0|
    |        15|      2 23|  0.0|
    |         3|      16 9|  1.0|
    |         4|     13 28|  0.0|
    |         2|     12 24|  0.0|
    |        16|     20 26|  1.0|
    |         9|      16 9|  1.0|
    |        12|       6 5|  0.0|
    |        15|     10 17|  1.0|
    |         7|      2 11|  0.0|
    |        15|      14 2|  1.0|
    |         6|       3 2|  0.0|
    |        20|     19 22|  0.0|
    |         8|     19 11|  0.0|
    |         6|      15 7|  0.0|
    |        13|      12 5|  0.0|
    |         4|      8 22|  0.0|
    |         7|      17 5|  0.0|
    +----------+----------+-----+
    ```

    Porównaj dane wyjściowe z nieprzetworzonymi danymi w pliku CSV. Na przykład pierwszy wiersz pliku CSV zawiera te dane:

    ![Migawka danych wyjściowych używanych w przykładzie uczenia maszynowego platformy Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "Migawka danych wyjściowych używanych w przykładzie uczenia maszynowego platformy Spark")

    Zwróć uwagę, że temperatura rzeczywista jest mniejsza niż temperatura docelowa, co świadczy o tym, że w budynku jest zimno. Z tego względu w szkoleniowych danych wyjściowych wartość w kolumnie **label** w pierwszym wierszu wynosi **0.0**, co oznacza, że w budynku nie jest ciepło.

7. Przygotuj zestaw danych do uruchomienia uczonego modelu. Aby to zrobić, przekaż identyfikator systemu i liczbę lat użytkowania instalacji (w szkoleniowych danych wyjściowych te informacje są zawarte w kolumnie **SystemInfo**). Model przewiduje, czy w budynku charakteryzowanym przez te dane będzie ciepło (wartość 1.0) czy chłodno (wartość 0.0).
   
    ```PySpark   
    # SystemInfo here is a combination of system ID followed by system age
    Document = Row("id", "SystemInfo")
    test = sc.parallelize([(1L, "20 25"),
                    (2L, "4 15"),
                    (3L, "16 9"),
                    (4L, "9 22"),
                    (5L, "17 10"),
                    (6L, "7 22")]) \
        .map(lambda x: Document(*x)).toDF() 
    ```
8. Na koniec wygenerujemy prognozy na podstawie danych testowych. 
   
    ```PySpark
    # Make predictions on test documents and print columns of interest
    prediction = model.transform(test)
    selected = prediction.select("SystemInfo", "prediction", "probability")
    for row in selected.collect():
        print row
    ```

    Dane wyjściowe są podobne do następujących:

    ```   
    Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
    Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
    Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
    Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
    Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
    Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
    ```
   
   W pierwszym wierszu prognozy widać, że w przypadku instalacji grzewczo-wentylacyjnej o identyfikatorze 20 i liczbie lat użytkowania równej 25 w budynku jest ciepło (**prediction=1.0**). Pierwsza wartość elementu DenseVector (0.49999) odpowiada prognozie 0.0, a druga wartość (0.5001) odpowiada prognozie 1.0. Chociaż w danych wyjściowych druga wartość jest tylko nieznacznie większa, model generuje wynik **prediction=1.0**.
10. Zamknij notes, aby zwolnić zasoby. W tym celu w menu **File** (Plik) w notesie wybierz pozycję **Close and Halt** (Zamknij i zatrzymaj). Ta akcja powoduje zatrzymanie i zamknięcie notesu.

## <a name="use-anaconda-scikit-learn-library-for-spark-machine-learning"></a>Uczenie maszynowe platformy Spark z użyciem biblioteki Anaconda scikit-learn
Klastry Apache Spark w usłudze HDInsight obejmują biblioteki Anaconda. Zawierają także bibliotekę **scikit-learn** do uczenia maszynowego. W tej bibliotece znajdują się różne zestawy danych, za pomocą których można utworzyć przykładowe aplikacje bezpośrednio w notesie Jupyter. Przykłady użycia biblioteki scikit-learn można znaleźć na stronie[https://scikit-learn.org/stable/auto_examples/index.html](https://scikit-learn.org/stable/auto_examples/index.html).

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Tworzenie aplikacji uczenia maszynowego platformy Apache Spark

Przejdź do następnego samouczka, aby dowiedzieć się, jak używać środowiska IntelliJ IDEA na potrzeby zadań Spark. 

> [!div class="nextstepaction"]
> [Utwórz aplikację Scala Maven za pomocą środowiska IntelliJ](./apache-spark-create-standalone-application.md)

