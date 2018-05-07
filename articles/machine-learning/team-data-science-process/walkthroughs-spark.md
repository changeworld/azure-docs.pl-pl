---
title: Instruktaże Spark w usłudze HDInsight przy użyciu PySpark i Scala na platformie Azure | Dokumentacja firmy Microsoft
description: Przykłady zespołu procesu analizy danych, które opisano PySpark i Scala na Azure HDInsight Spark analizy predykcyjnej.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: deguhath
ms.openlocfilehash: 06498f55b38be98441ddceb8f1667c89d257f8fd
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>HDInsight Spark nauki wskazówki dotyczące danych przy użyciu PySpark i Scala na platformie Azure

Te wskazówki Użyj PySpark i Scala w klastrze Azure Spark analizy predykcyjnej. One wykonaj kroki opisane w procesie nauki danych zespołu. Omówienie procesu nauki danych zespołu, zobacz [proces nauki danych](overview.md). Omówienie Spark w usłudze HDInsight, zobacz [wprowadzenie do platformy Spark w usłudze HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Wskazówki dotyczące nauki dodatkowych danych, których wykonanie procesu analizy danych zespołu są pogrupowane według **platformy** jak używane. Zobacz [wskazówki dotyczące wykonywania procesu nauki danych zespołu](walkthroughs.md) do podziału na pozycje tych przykładów.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Przewidywanie taksówki porady na Azure Spark przy użyciu PySpark

[Korzystanie z platformy Spark w usłudze Azure HDInsight](spark-overview.md) wskazówki korzysta z nowego Jorku taksówkach danych do prognozowania czy otrzymuje porady i zakres kwoty oczekiwano mają zostać zapłacone. Używa procesu nauki danych zespołu w scenariuszu przy użyciu [klastra Spark w usłudze HDInsight Azure](https://azure.microsoft.com/services/hdinsight/) do przechowywania, Eksploruj, funkcję odtwarzania dane z dostępnych publicznie podróży taksówki NYC i taryfy zestawu danych. Ten temat ustawia można się z klastra Spark w usłudze HDInsight i Jupyter PySpark notesów używane w pozostałej części przewodnika. Te notesy przedstawiają sposób eksplorować dane, a następnie tworzenie i korzystanie z modeli. Zaawansowane danych notesu eksploracji i modelowanie pokazuje, jak do uwzględnienia krzyżowego sprawdzania poprawności, funkcji hyper parametr profilach, a model oceny.

### <a name="data-exploration-and-modeling-with-spark"></a>Eksploracja danych i modelowanie z Spark 
Eksploruj zestawu danych i tworzenie, wynik i ocenić maszyny modeli uczenia przez pracy nad [Tworzenie binarnego klasyfikacji i regresji modeli danych narzędzi Spark MLlib](spark-data-exploration-modeling.md) tematu.

### <a name="model-consumption"></a>Użycie modelu
Aby dowiedzieć się, jak wynik modele klasyfikacji i regresji, utworzone w tym temacie, zobacz [wynik i ocena modele uczenia wbudowane Spark maszyny](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Krzyżowe sprawdzanie poprawności i kominów hyperparameter
Zobacz [zaawansowane Eksploracja danych i modelowania z platformy Spark](spark-advanced-data-exploration-modeling.md) na jak można modeli uczenia przy użyciu kominów krzyżowego sprawdzania poprawności i parametru funkcji hyper.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Przewidywanie taksówki porady na Azure Spark przy użyciu języka Scala

[Scala użycia z łącznikiem Spark on Azure](scala-walkthrough.md) wskazówki korzysta z nowego Jorku taksówkach danych do prognozowania czy otrzymuje porady i zakres kwoty oczekiwano mają zostać zapłacone. Widoczny jest sposób użycia Scala maszyny nadzorowanego uczenia zadania z maszyną Spark uczenia biblioteki (MLlib) i pakiety SparkML w klastrze Spark w usłudze HDInsight Azure. Przeprowadza użytkownika przez zadania, które stanowią [proces nauki danych](http://aka.ms/datascienceprocess): wprowadzanie danych i eksploracja, wizualizacji engineering funkcji, modelowania i zużycia modelu. Modele wbudowane obejmują Regresja logistyczna i liniowych, losowe lasów i gradientu boosted drzewa.


## <a name="next-steps"></a>Kolejne kroki

Omówienie najważniejsze składniki wchodzące w skład procesu nauki danych zespołu, zobacz [Omówienie procesu nauki danych zespołu](overview.md).

Omówienie cykl zespołu w procesie nauki danych służy do struktury projektów analizy danych, zobacz [cyklu życia procesu nauki danych zespołu](lifecycle.md). Cykl życia omówiono kroki, od początku do końca, że projekty zwykle należy wykonać podczas są wykonywane. 

