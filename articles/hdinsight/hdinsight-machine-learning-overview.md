---
title: Omówienie uczenia maszynowego — usługa Azure HDInsight
description: Omówienie opcji uczenia maszynowego dużych zbiorów danych dla klastrów w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 6c7f50e4ba3803b5cb929b75f5ad1127f7f063d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931683"
---
# <a name="machine-learning-on-hdinsight"></a>Uczenie maszynowe w programie HDInsight

Usługa HDInsight umożliwia uczenie maszynowe z dużymi zbiorami danych, zapewniając możliwość uzyskania cennych informacji z dużych ilości (petabajtów, a nawet eksabajtów) ustrukturyzowanych, nieustrukturyzowanych i szybko poruszających się danych. Istnieje kilka opcji uczenia maszynowego w programie HDInsight: SparkML i Apache Spark MLlib, R, Apache Hive i Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML i MLlib

[HDInsight Spark](spark/apache-spark-overview.md) to hostowana na platformie Azure oferta [Apache Spark](https://spark.apache.org/), ujednoliconej, otwartej, równoległej struktury przetwarzania danych obsługującej przetwarzanie w pamięci w celu zwiększenia analizy dużych zbiorów danych. Silnik przetwarzania Spark został stworzony z myślą o szybkości, łatwości obsługi i zaawansowanej analizie. Rozproszone możliwości obliczeniowe firmy Spark w pamięci sprawiają, że jest to dobry wybór dla algorytmów iteracyjnych używanych w uczeniu maszynowym i obliczeniach wykresów. Istnieją dwie skalowalne biblioteki uczenia maszynowego, które zapewniają możliwości modelowania algorytmicznego do tego rozproszonego środowiska: MLlib i SparkML. MLlib zawiera oryginalny interfejs API zbudowany na podstawie RDD. SparkML to nowszy pakiet, który zapewnia interfejs API wyższego poziomu zbudowany na podstawie dataframe do konstruowania potoków ml. SparkML nie obsługuje jeszcze wszystkich funkcji MLlib, ale zastępuje MLlib jako standardową bibliotekę uczenia maszynowego spark.

Biblioteka uczenia maszynowego firmy Microsoft dla platformy Apache Spark to [MMLSpark](https://github.com/Azure/mmlspark). Ta biblioteka została zaprojektowana w celu zwiększenia produktywności analityków danych w programie Spark, zwiększenia szybkości eksperymentów i wykorzystania najnowocześniejszych technik uczenia maszynowego, w tym uczenia głębokiego, w bardzo dużych zestawach danych. MMLSpark udostępnia warstwę na górze interfejsów API niskiego poziomu platformy SparkML podczas tworzenia skalowalnych modeli uczenia maszynowego, takich jak indeksowanie ciągów, wymuszanie danych do układu oczekiwanego przez algorytmy uczenia maszynowego i składanie wektorów funkcji. Biblioteka MMLSpark upraszcza te i inne typowe zadania dotyczące tworzenia modeli w pyspark.

## <a name="r"></a>R

[R](https://www.r-project.org/) jest obecnie najpopularniejszym statystycznym językiem programowania na świecie. Jest to narzędzie do wizualizacji danych typu open source, które ma ponad 2,5 miliona użytkowników i rozwija się. Dzięki dobrze prosperującej bazie użytkowników i ponad 8000 pakietów, R jest prawdopodobnym wyborem dla wielu firm, które potrzebują uczenia maszynowego. Można utworzyć klaster HDInsight z usługami ML gotowy do użycia z masywnymi zestawami danych i modelami. Ta funkcja zapewnia analityków danych i statystyków ze znanym interfejsem języka R, który można skalować na żądanie za pośrednictwem usługi HDInsight, bez konieczności konfigurowania i konserwacji klastra.

![Szkolenie do przewidywania z serwerem R](./media/hdinsight-machine-learning-overview/training-for-prediction.png)

Węzeł brzegowy klastra zapewnia wygodne miejsce do łączenia się z klastrem i uruchamiania skryptów języka R.  Skrypty języka R można również uruchamiać w węzłach klastra przy użyciu kontekstów obliczeniowych Hadoop Map Reduce lub Spark firmy ScaleR.

Dzięki usługom ML w programie HDInsight z programem Spark można zrównować szkolenie między węzłami klastra przy użyciu kontekstu obliczeniowego platformy Spark. Skrypty języka R można uruchamiać bezpośrednio w węźle brzegowym, używając wszystkich dostępnych rdzeni równolegle, w razie potrzeby. Alternatywnie można uruchomić kod z węzła brzegowego, aby rozpocząć przetwarzanie, które jest dystrybuowane we wszystkich węzłach w klastrze. Usługi ML w programie HDInsight with Spark umożliwiają również równoległość funkcji z pakietów R typu open source, w razie potrzeby.

## <a name="azure-machine-learning-and-apache-hive"></a>Usługa Azure Machine Learning i gałąź Apache

Usługa Azure Machine Learning udostępnia narzędzia do modelowania analizy predykcyjnej oraz w pełni zarządzaną usługę, której można użyć do wdrażania modeli predykcyjnych jako gotowych do użycia usług sieci web. Usługa Azure Machine Learning to kompletne rozwiązanie do analizy predykcyjnej w chmurze, którego można używać do tworzenia, testowania, operacjonalizacji i zarządzania modelami predykcyjnymi. Wybierz z dużej biblioteki algorytmów, użyj studio oparte na sieci Web do tworzenia modeli i łatwo wdrożyć model jako usługę sieci web.

![Omówienie uczenia maszynowego platformy Microsoft Azure](./media/hdinsight-machine-learning-overview/azure-machine-learning.png)

Tworzenie funkcji dla danych w klastrze programu HDInsight Hadoop przy użyciu [zapytań hive](../machine-learning/team-data-science-process/create-features-hive.md). *Inżynieria funkcji* próbuje zwiększyć predykcyjną moc algorytmów uczenia się, tworząc funkcje z nieprzetworzonych danych, które ułatwiają proces uczenia się. Można uruchamiać zapytania HiveQL z usługi Azure Machine Learning Studio (klasyczne) i uzyskać dostęp do danych przetwarzanych w obszarze hive i przechowywanych w magazynie obiektów blob przy użyciu [modułu Importuj dane.](../machine-learning/studio/import-data.md)

## <a name="microsoft-cognitive-toolkit"></a>Zestaw narzędzi usług Microsoft Cognitive

[Głębokie uczenie się](https://www.microsoft.com/en-us/research/group/dltc/) jest gałęzią uczenia maszynowego, która wykorzystuje sieci neuronowe, zainspirowany procesami biologicznymi ludzkiego mózgu. Wielu badaczy postrzega głębokie uczenie jako obiecujące podejście do wzmacniania sztucznej inteligencji. Przykładami uczenia głębokiego są tłumacze języka mówionego, systemy rozpoznawania obrazów i rozumowanie maszynowe.

Aby pomóc w rozwijaniu własnej pracy w zakresie głębokiego uczenia się, firma Microsoft opracowała bezpłatny, łatwy w użyciu zestaw [narzędzi Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)typu open source. Ten zestaw narzędzi jest używany przez szeroką gamę produktów firmy Microsoft, przez firmy na całym świecie, które muszą wdrażać głębokie uczenie na dużą skalę, oraz przez studentów zainteresowanych najnowszymi algorytmami i technikami.

## <a name="see-also"></a>Zobacz też

### <a name="scenarios"></a>Scenariusze

* [Apache Spark z uczeniem maszynowym: użyj platformy Spark w hdinsight do analizowania temperatury budynku przy użyciu danych HVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z uczeniem maszynowym: użyj iskry w hdinsight, aby przewidzieć wyniki inspekcji żywności](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Generowanie rekomendacji filmowych za pomocą Apache Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Ule Apache i uczenie maszynowe platformy Azure](../machine-learning/team-data-science-process/create-features-hive.md)
* [Apache Hive i azure machine learning end-to-end](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Uczenie maszynowe z Apache Spark na HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Zasoby głębokiego uczenia się

* [Korzystanie z modelu uczenia głębokiego microsoft cognitive toolkit z klastrem Azure HDInsight Spark](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Struktury uczenia głębokiego i interfejsu użytkownika w dziedzinie nauki o danych na maszynie wirtualnej do nauki o danych (DSVM)](../machine-learning/data-science-virtual-machine/dsvm-tools-deep-learning-frameworks.md)
