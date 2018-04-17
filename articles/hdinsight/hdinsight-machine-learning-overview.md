---
title: Przegląd Machine learning - Azure HDInsight | Dokumentacja firmy Microsoft
description: Opisuje opcje w usłudze HDInsight uczenia maszynowego.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: nitinme
ms.openlocfilehash: b80c49b67c7cca835ae08346561984e168e3df4e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="machine-learning-on-hdinsight"></a>Machine learning w usłudze HDInsight

HDInsight umożliwia uczenie maszynowe z danych big data, umożliwiając uzyskać cenne informacje z dużymi ilościami (petabajtów, a nawet eksabajtów) strukturalnych, bez struktury i szybko danych. Dostępne są opcje kilka uczenia maszynowego w usłudze HDInsight: SparkML oraz MLlib, R, Hive i kognitywnych zestaw narzędzi firmy Microsoft.

## <a name="sparkml-and-mllib"></a>SparkML oraz MLlib

[HDInsight Spark](spark/apache-spark-overview.md) jest oferowany hostowanymi na platformie Azure z [Spark](http://spark.apache.org/), ujednoliconego typu open source, obsługujący przetwarzanie w pamięci w celu zwiększania analizy danych big data framework równoległego przetwarzania danych. Aparat przetwarzania Spark zaprojektowano pod kątem szybkości, łatwości użycia i zaawansowanych możliwości analitycznych. Możliwości rozproszone obliczenia w pamięci platforma Spark stanowić dobry wybór w przypadku algorytmów iteracyjnych w machine learning i obliczeniach na grafach. Istnieją dwa biblioteki learning skalowalne maszyny zapewniające możliwości algorytmicznego modelowania w tym środowisku rozproszonym: MLlib i SparkML. MLlib zawiera oryginalnego interfejsu API, rozszerzający RDDs. SparkML jest nowsza pakietu, który udostępnia interfejs API wyższego poziomu, rozszerzający DataFrames do konstruowania potoki ML. SparkML jeszcze nie obsługuje wszystkich funkcji MLlib, ale zastępuje MLlib jako maszyna standardowa platforma Spark uczenia biblioteki.

Biblioteka Microsoft Machine Learning platformy Apache Spark jest [MMLSpark](https://github.com/Azure/mmlspark). Ta biblioteka jest przeznaczona do analityków danych bardziej wydajnej pracy w Spark, zwiększenia szybkości eksperymenty, a wykorzystanie technik uczenia maszynowego najnowocześniejsze, w tym uczenie głębokie, na bardzo dużych zestawów danych. MMLSpark zapewnia warstwę ponad niskiego poziomu interfejsów API firmy SparkML podczas kompilowania skalowalne modeli uczenia Maszynowego, na przykład ciągi indeksowania coercing — dane w układzie oczekiwany przez komputer, algorytmów uczenia i składania wektory funkcji. Biblioteka MMLSpark upraszcza tych i innych typowych zadań do tworzenia modeli w PySpark.

## <a name="r"></a>R

[R](https://www.r-project.org/) jest obecnie najpopularniejsze statystyczne język programowania na świecie. To narzędzie wizualizacji danych typu open source społeczności użytkowników ponad 2,5 mln i powiększania. Jego podstawowy użytkownik thriving i ponad 8000 załadowanie współtworzonej pakietów R to prawdopodobnie wybór dla wielu firm, które potrzebują uczenia maszynowego. Z serwerem R gotowe do użycia z duże zestawy danych i modeli, można utworzyć klastra usługi HDInsight. Ta funkcja udostępnia chi z interfejsem R znanych można skalować i analityków danych na żądanie za pośrednictwem usługi HDInsight, bez potrzeby Konfiguracja klastra i konserwacji.

![Szkolenie odpowiednie do prognozowania z serwerem R](./media/hdinsight-machine-learning-overview/r-training.png)

Węzeł krawędzi klastra umożliwia wygodne Połącz się z klastrem i uruchamiania skryptów R.  Masz również opcję, aby uruchamiać skrypty języka R w węzłach klastra przy użyciu jego ScaleR Hadoop mapy zmniejszyć lub Spark obliczeniowe kontekstach.

Z serwerem R w usłudze HDInsight z Spark można parallelize szkolenia w węzłach klastra przy użyciu kontekstu obliczeń platformy Spark. Można uruchamiać skrypty R bezpośrednio w węźle krawędzi przy użyciu wszystkie dostępne rdzenie równolegle, zgodnie z potrzebami. Alternatywnie można uruchomić kod z węzłem krawędzi, aby rozpocząć poza przetwarzania, która jest dystrybuowana do wszystkich węzłów w klastrze. R Server w usłudze HDInsight z Spark umożliwia również parallelizing funkcji z pakietów typu open source R, w razie potrzeby.

## <a name="azure-machine-learning-and-hive"></a>Uczenie maszynowe Azure i Hive

Usługa Azure Machine Learning zapewnia narzędzia do analizy predykcyjnej modelu, a także pełni zarządzaną usługę służącą do wdrażania modeli predykcyjnych jako gotowe do użycia usług sieci web. Usługa Azure Machine Learning to rozwiązanie ukończenia analizy predykcyjnej w chmurze, która służy do tworzenia, testowania, operacjonalizacji i zarządzania modeli predykcyjnych. Wybierz od dużej biblioteki algorytmów, użycia studia opartych na sieci web w celu utworzenia modeli i łatwy sposób wdrażania modelu w postaci usługi sieci web.

![Tworzenie, zaawansowane analizy dostępna dla platformy Hadoop za pomocą programu Microsoft Azure Machine Learning](./media/hdinsight-machine-learning-overview/hadoop-azure-ml.png)

Tworzenie funkcji, aby dane w usłudze HDInsight Hadoop dla klastra przy użyciu [zapytań Hive](../machine-learning/team-data-science-process/create-features-hive.md). *Inżynieria* próbuje zwiększyć możliwości predykcyjnej algorytmów uczenia przez tworzenie funkcji z nieprzetworzone dane, które ułatwiają proces uczenia. Uruchamianie zapytań HiveQL z uczenie Maszynowe Azure i uzyskać dostęp do danych przetwarzane w gałęzi rejestru i przechowywane w magazynie obiektów blob za pomocą [modułu importu danych](../machine-learning/studio/import-data.md).

## <a name="microsoft-cognitive-toolkit"></a>Zestaw narzędzi usług Microsoft Cognitive

[Głębokie learning](https://www.microsoft.com/en-us/research/group/dltc/) jest gałęzią uczenia maszynowego, korzystającą z sieci neuronowe przez procesom z inteligencji człowieka. Wielu pracowników naukowo-badawczych Zobacz głębokie learning jako obietnicy podejście wzmocnienia sztucznego analizy. Przykładem głębokie learning są tłumaczy język rozmowy, systemy rozpoznawania obrazu i rozsądkiem maszyny.

Aby poprawić pracę w uczeniu głębokie, firma Microsoft opracowała bezpłatne, łatwy w użyciu, open source [kognitywnych zestaw narzędzi firmy Microsoft](https://www.microsoft.com/en-us/cognitive-toolkit/). Ten zestaw narzędzi jest używany przez cały szereg produktów firmy Microsoft, firm na świecie potrzebuje do wdrożenia głębokiego uczenie na dużą skalę i studentów zainteresowana najnowsze algorytmy i techniki. 

## <a name="see-also"></a>Zobacz także

### <a name="scenarios"></a>Scenariusze

* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizy temperatury w budynku z użyciem danych HVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników kontroli żywności](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Generowanie zaleceń filmu przy Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Hive i uczenie maszynowe Azure](../machine-learning/team-data-science-process/create-features-hive.md)
* [Hive i usługi Azure Machine Learning end-to-end](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Machine learning z platformy Spark w usłudze HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Głębokie szkoleniowe

* [Głębokie learning toolkit z Spark](https://blogs.technet.microsoft.com/machinelearning/2017/04/25/using-microsofts-deep-learning-toolkit-with-spark-on-azure-hdinsight-clusters/)
* [Klasyfikacja embarrassingly równoległych obrazu z kognitywnych toolkit + Tensorflow na Spark](https://blogs.technet.microsoft.com/machinelearning/2017/04/12/embarrassingly-parallel-image-classification-using-cognitive-toolkit-tensorflow-on-azure-hdinsight-spark/)
