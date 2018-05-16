---
title: Tworzenie potoku uczenia maszynowego Apache Spark - Azure HDInsight | Dokumentacja firmy Microsoft
description: Biblioteka uczenia maszynowego Apache Spark służy do tworzenia potoki danych.
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
ms.date: 01/19/2018
ms.author: maxluk
ms.openlocfilehash: c3ff29404858a768737536e7d31c3c6858eea7d2
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="create-a-spark-machine-learning-pipeline"></a>Tworzenie potoku uczenia maszynowego platformy Spark

Apache Spark skalowalne machine learning biblioteki (MLlib) zapewnia funkcje modelowania w środowisku rozproszonym. Pakiet Spark [ `spark.ml` ](http://spark.apache.org/docs/latest/ml-pipeline.html) to zestaw interfejsów API wysokiego poziomu, oparty na DataFrames. Te interfejsy API ułatwiają tworzenie i dostrajania praktyczne potoki uczenia maszynowego.  *Platforma Spark jest usługa machine learning* odwołuje się do tego na podstawie MLlib DataFrame interfejsu API, nie starsze na podstawie RDD potoku interfejsu API.

Potok (ML) uczenia maszynowego jest pełny przepływ pracy łączenie maszyny wiele algorytmów uczenia razem. Może istnieć wiele czynności wymagane do przetwarzania i z danych wymagających sekwencji algorytmów. Potoki zdefiniuj etapów i kolejność procesu uczenia maszynowego. W MLlib etapy potoku są reprezentowane przez określonej kolejności PipelineStages, gdzie transformatora, jak i narzędzia do szacowania wykonać zadania.

Transformator jest algorytmem, który przekształca DataFrame jednego do drugiego za pomocą `transform()` metody. Na przykład transformatora funkcji można odczytać z jedną kolumną zawierającą DataFrame mapowanie go do innej kolumny i output DataFrame nowe kolumny zamapowanej dołączone do niego.

Narzędzia do szacowania to Abstrakcja algorytmów uczenia i jest odpowiedzialny za dopasowanie lub szkolenia w zestawie danych wygenerowało transformatora. Narzędzia do szacowania implementuje metodę o nazwie `fit()`, który akceptuje DataFrame i tworzy DataFrame, czyli transformatora.

Każde wystąpienie bezstanowych transformatora lub narzędzia do szacowania ma własny unikatowy identyfikator, który jest używany podczas określania parametrów. Zarówno za pośrednictwem jednolitego interfejsu API do określenia tych parametrów.

## <a name="pipeline-example"></a>Przykład potoku

Aby zademonstrować praktyczne wykorzystanie ML potoku, w tym przykładzie użyto przykładowej `HVAC.csv` pliku danych, który jest wstępnie załadowane na domyślny magazyn dla klastra usługi HDInsight, magazynu Azure lub usługi Data Lake Store. Aby wyświetlić zawartość pliku, przejdź do `/HdiSamples/HdiSamples/SensorSampleData/hvac` katalogu. `HVAC.csv` zawiera zestaw razy z docelowym i rzeczywistego temperatury HVAC (*grzejników, wentylatorów i klimatyzacja*) systemów w różnych budynkach. Celem jest do nauczenia modelu danych i tworzenia prognoz temperatury dla danego tworzenia.

Następujący kod:

1. Definiuje `LabeledDocument`, której są przechowywane `BuildingID`, `SystemInfo` (systemowego identyfikator i wiek), a `label` (1.0, jeśli kompilacja jest zbyt gorących 0.0, w przeciwnym razie).
2. Tworzy funkcję niestandardowy analizator składni `parseDocument` który przyjmuje wiersz danych i określa, czy kompilacja "gorących" porównując temperatury docelowych do rzeczywistego temperatury.
3. Podczas wyodrębniania źródło danych ma zastosowanie analizatora.
4. Tworzy danych szkoleniowych.

```python
# The data structure (column meanings) of the data array:
# 0 Date
# 1 Time
# 2 TargetTemp
# 3 ActualTemp
# 4 System
# 5 SystemAge
# 6 BuildingID

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
data = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
training = documents.toDF()
```

Ten przykład potoku ma trzy etapy: `Tokenizer` i `HashingTF` (zarówno transformatory) i `Logistic Regression` (narzędzia do szacowania).  Wyodrębnionego i przeanalizowane dane w `training` DataFrame przechodzi przez potok podczas `pipeline.fit(training)` jest wywoływana.

1. Pierwszy etap `Tokenizer`, dzieli `SystemInfo` kolumny wejściowej (składające się z wartości identyfikatora i wiek system) do `words` kolumny wyjściowej. Nowy `words` do DataFrame zostanie dodana kolumna. 
2. Drugi etap `HashingTF`, konwertuje nowe `words` kolumnę w funkcji wektory. Nowy `features` do DataFrame zostanie dodana kolumna. Tych dwóch pierwszych etapach są transformatory. 
3. Trzeci etap `LogisticRegression`, to narzędzia do szacowania i dlatego wywołuje potok `LogisticRegression.fit()` metodę, aby utworzyć `LogisticRegressionModel`. 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

Aby zobaczyć nowe `words` i `features` kolumnami dodanymi za `Tokenizer` i `HashingTF` transformatory i przykładowe `LogisticRegression` narzędzia do szacowania, uruchom `PipelineModel.transform()` metody w oryginalnym DataFrame. W kodzie produkcyjnym następnym krokiem powinno być przekazywane w teście DataFrame do sprawdzania poprawności szkolenia.

```python
peek = model.transform(training)
peek.show()

# Outputs the following:
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|BuildingID|SystemInfo|label|   words|            features|       rawPrediction|         probability|prediction|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|         4|     13 20|  0.0|[13, 20]|(262144,[250802,2...|[0.11943986671420...|[0.52982451901740...|       0.0|
|        17|      3 20|  0.0| [3, 20]|(262144,[89074,25...|[0.17511205617446...|[0.54366648775222...|       0.0|
|        18|     17 20|  1.0|[17, 20]|(262144,[64358,25...|[0.14620993833623...|[0.53648750722548...|       0.0|
|        15|      2 23|  0.0| [2, 23]|(262144,[31351,21...|[-0.0361327091023...|[0.49096780538523...|       1.0|
|         3|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|         4|     13 28|  0.0|[13, 28]|(262144,[69821,25...|[0.14630166986618...|[0.53651031790592...|       0.0|
|         2|     12 24|  0.0|[12, 24]|(262144,[187043,2...|[-0.0509556393066...|[0.48726384581522...|       1.0|
|        16|     20 26|  1.0|[20, 26]|(262144,[128319,2...|[0.33829638728900...|[0.58377663577684...|       0.0|
|         9|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|        12|       6 5|  0.0|  [6, 5]|(262144,[18659,89...|[0.07513008136562...|[0.51877369045183...|       0.0|
|        15|     10 17|  1.0|[10, 17]|(262144,[64358,25...|[-0.0291988646553...|[0.49270080242078...|       1.0|
|         7|      2 11|  0.0| [2, 11]|(262144,[212053,2...|[0.03678030020834...|[0.50919403860812...|       0.0|
|        15|      14 2|  1.0| [14, 2]|(262144,[109681,2...|[0.06216423725633...|[0.51553605651806...|       0.0|
|         6|       3 2|  0.0|  [3, 2]|(262144,[89074,21...|[0.00565582077537...|[0.50141395142468...|       0.0|
|        20|     19 22|  0.0|[19, 22]|(262144,[139093,2...|[-0.0769288695989...|[0.48077726176073...|       1.0|
|         8|     19 11|  0.0|[19, 11]|(262144,[139093,2...|[0.04988910033929...|[0.51246968885151...|       0.0|
|         6|      15 7|  0.0| [15, 7]|(262144,[77099,20...|[0.14854929135994...|[0.53706918109610...|       0.0|
|        13|      12 5|  0.0| [12, 5]|(262144,[89689,25...|[-0.0519932532562...|[0.48700461408785...|       1.0|
|         4|      8 22|  0.0| [8, 22]|(262144,[98962,21...|[-0.0120753606650...|[0.49698119651572...|       1.0|
|         7|      17 5|  0.0| [17, 5]|(262144,[64358,89...|[-0.0721054054871...|[0.48198145477106...|       1.0|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+

only showing top 20 rows
```

`model` Obiektu można teraz używać do tworzenia prognoz. Aby uzyskać pełny przykład tego machine learning aplikacji i instrukcje krok po kroku dotyczące uruchamiania go, zobacz [kompilacji Apache Spark machine learning aplikacji w usłudze Azure HDInsight](apache-spark-ipython-notebook-machine-learning.md).

## <a name="see-also"></a>Zobacz także

* [Nauki danych przy użyciu języka Scala i Spark na platformie Azure](../../machine-learning/team-data-science-process/scala-walkthrough.md)
