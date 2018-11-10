---
title: Wskazówki dotyczące platformy HDInsight Spark przy użyciu PySpark i środowiskiem Scala na platformie Azure | Dokumentacja firmy Microsoft
description: Przykłady procesie nauki o danych zespołu, które pomagają przy użyciu PySpark i środowiskiem Scala na usługi Azure HDInsight Spark do analizy predykcyjnej.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: deguhath
ms.openlocfilehash: 3d39e752e874b6b0c6fbdf678d6eddda0b0d9404
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51226552"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>HDInsight Spark przewodniki dotyczące przetwarzania danych przy użyciu PySpark i środowiskiem Scala na platformie Azure

Te przewodniki Użyj PySpark i środowiskiem Scala w klastrze usługi Azure Spark do analizy predykcyjnej. One wykonaj kroki opisane w procesie nauki o danych zespołu. Omówienie procesu do nauki o danych zespołu, zobacz [danych dla celów naukowych](overview.md). Aby zapoznać się z omówieniem Spark on HDInsight, zobacz [wprowadzenie do platformy Spark w HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Przewodniki dotyczące przetwarzania dodatkowe dane, które są wykonywane w procesie nauki o danych zespołu są pogrupowane według **platformy** używane. Zobacz [przewodniki dotyczące wykonywania procesu do nauki o danych zespołu](walkthroughs.md) do podziału na pozycje z tych przykładów.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Przewidywanie napiwków przy użyciu PySpark na platformie Azure Spark

[Korzystanie z platformy Spark w usłudze Azure HDInsight](spark-overview.md) wskazówki używane dane taksówek w Nowym Jorku w celu przewidywania, czy Porada zostało opłacone i zakres kwoty przewidywań zapłaty. Używa zespół danych dla celów naukowych w scenariuszu za pomocą [klastra usługi HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) do przechowywania, eksplorować, inżynier ds. danych z publicznie dostępnego podróży taksówek NYC funkcji i taryfy zestawu danych. Ten ogólny temat ustawia możesz przy użyciu klastra usługi HDInsight Spark i Jupyter PySpark notebooks używana w pozostałej części przewodnika. Te notesy dowiesz się, jak eksplorować dane, a następnie tworzenie i korzystanie z modeli. Notes Eksplorowanie i modelowanie zaawansowanych danych pokazuje, jak do uwzględnienia krzyżowa Weryfikacja, parametrów sprawdzaniu, a model oceny.

### <a name="data-exploration-and-modeling-with-spark"></a>Eksplorowanie i modelowanie za pomocą platformy Spark danych 
Zapoznaj się z zestawu danych i tworzenie, wynik i ocenić maszyny modeli uczenia dzięki pracy za pośrednictwem [Tworzenie binarnego modeli klasyfikacji i regresji dla danych z zestawem narzędzi Biblioteka MLlib platformy Spark](spark-data-exploration-modeling.md) tematu.

### <a name="model-consumption"></a>Model użycia
Aby dowiedzieć się, jak na ocenianie modeli klasyfikacji i regresji, utworzone w tym temacie, zobacz [wynik i ocena modeli uczenia maszynowego utworzonych na platformie Spark](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Krzyżowa Weryfikacja i zaczynają hiperparametrycznego
Zobacz [zaawansowane Eksplorowanie i modelowanie za pomocą platformy Spark danych](spark-advanced-data-exploration-modeling.md) w sposób modeli może być uczony przy użyciu zaczynają krzyżowego sprawdzania poprawności i parametrów.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Przewidywanie napiwków na platformie Azure Spark przy użyciu języka Scala

[Korzystanie z języka Scala za pomocą platformy Spark na platformie Azure](scala-walkthrough.md) wskazówki używane dane taksówek w Nowym Jorku w celu przewidywania, czy Porada zostało opłacone i zakres kwoty przewidywań zapłaty. Widoczny jest sposób Scala na użytek nadzorowanych uczenie maszynowe zadania przy użyciu biblioteki (MLlib) i pakiety SparkML w klastrze usługi Azure HDInsight Spark uczenia maszynowego platformy Spark. Przeprowadzi Cię on zadania, które stanowią [danych dla celów naukowych](https://aka.ms/datascienceprocess): pozyskiwanie danych i eksploracji, wizualizacji, technicznego opracowywania funkcji, modelowania i użycie modelu. Modele utworzone obejmują regresji logistycznej liniowego i liniowa w, losowych lasów i gradientu wzmocnionego drzewa.


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać omówienie najważniejsze składniki wchodzące w skład procesu do nauki o danych zespołu, zobacz [przegląd danych zespołu dla celów naukowych](overview.md).

Omówienie cyklu życia procesu do nauki o danych zespołu, używanej do organizowania projektów do nauki o danych, zobacz [cykl życia zespołowego danych naukowych](lifecycle.md). Cykl życia zawiera opis kroków, od początku do końca, że projekty zazwyczaj należy wykonać podczas są wykonywane. 

