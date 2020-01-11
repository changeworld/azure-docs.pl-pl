---
title: Analiza na platformie HDInsight Spark przy użyciu PySpark, Scala - zespołu danych dla celów naukowych
description: Przykłady procesu nauka danych zespołu, który przeprowadzi Cię przez użycie PySpark i scala na Azure HDInsight Spark.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 91aac279a264d64ace5988d147c4caf8c52e9656
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864149"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>HDInsight Spark przewodniki dotyczące przetwarzania danych przy użyciu PySpark i środowiskiem Scala na platformie Azure

Te przewodniki Użyj PySpark i środowiskiem Scala w klastrze usługi Azure Spark do analizy predykcyjnej. One wykonaj kroki opisane w procesie nauki o danych zespołu. Omówienie procesu do nauki o danych zespołu, zobacz [danych dla celów naukowych](overview.md). Aby zapoznać się z omówieniem Spark on HDInsight, zobacz [wprowadzenie do platformy Spark w HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Przewodniki dotyczące przetwarzania dodatkowe dane, które są wykonywane w procesie nauki o danych zespołu są pogrupowane według **platformy** używane. Zobacz [przewodniki dotyczące wykonywania procesu do nauki o danych zespołu](walkthroughs.md) do podziału na pozycje z tych przykładów.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Przewidywanie napiwków przy użyciu PySpark na platformie Azure Spark

Za pomocą nowych danych o taksówkach w [usłudze Azure HDInsight wskazówki dotyczące korzystania z platformy Spark w ramach przewodnika dotyczącego usługi](spark-overview.md) , w której znajduje się Porada i zakres przewidywanych kwot. W tym przykładzie użyto zespołowego procesu nauki o danych w scenariuszu korzystającego z [Azure HDInsight Spark klastra](https://azure.microsoft.com/services/hdinsight/) do przechowywania, eksplorowania i opisywania danych inżynierów z publicznie dostępnego zestawu danych podróży i opłat za taksówkę z NYC. Ten temat zawiera informacje dotyczące klastrów usługi HDInsight Spark i Jupyter PySpark. Te notesy dowiesz się, jak eksplorować dane, a następnie tworzenie i korzystanie z modeli. Notes Eksplorowanie i modelowanie zaawansowanych danych pokazuje, jak do uwzględnienia krzyżowa Weryfikacja, parametrów sprawdzaniu, a model oceny.

### <a name="data-exploration-and-modeling-with-spark"></a>Eksplorowanie i modelowanie za pomocą platformy Spark danych 
Zapoznaj się z zestawu danych i tworzenie, wynik i ocenić maszyny modeli uczenia dzięki pracy za pośrednictwem [Tworzenie binarnego modeli klasyfikacji i regresji dla danych z zestawem narzędzi Biblioteka MLlib platformy Spark](spark-data-exploration-modeling.md) tematu.

### <a name="model-consumption"></a>Model użycia
Aby dowiedzieć się, jak na ocenianie modeli klasyfikacji i regresji, utworzone w tym temacie, zobacz [wynik i ocena modeli uczenia maszynowego utworzonych na platformie Spark](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Krzyżowa Weryfikacja i zaczynają hiperparametrycznego
Zobacz [zaawansowane Eksplorowanie i modelowanie za pomocą platformy Spark danych](spark-advanced-data-exploration-modeling.md) w sposób modeli może być uczony przy użyciu zaczynają krzyżowego sprawdzania poprawności i parametrów.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Przewidywanie napiwków na platformie Azure Spark przy użyciu języka Scala

W instruktażu [Scala z platformą Spark w systemie Azure](scala-walkthrough.md) jest przewidywane, czy Porada jest płatna, oraz jaki jest zakres kwot, które należy zapłacić. Widoczny jest sposób Scala na użytek nadzorowanych uczenie maszynowe zadania przy użyciu biblioteki (MLlib) i pakiety SparkML w klastrze usługi Azure HDInsight Spark uczenia maszynowego platformy Spark. Przeprowadzi Cię on zadania, które stanowią [danych dla celów naukowych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/): pozyskiwanie danych i eksploracji, wizualizacji, technicznego opracowywania funkcji, modelowania i użycie modelu. Modele utworzone obejmują regresji logistycznej liniowego i liniowa w, losowych lasów i gradientu wzmocnionego drzewa.


## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem procesu nauki o danych zespołowych, zobacz [Omówienie procesu nauka danych zespołu](overview.md).

Aby zapoznać się z omówieniem cyklu życia procesu nauki o danych zespołowych, zobacz [cykl życia procesu nauki o danych zespołowych](lifecycle.md). Ten cykl życia przedstawia kroki od początku do końca, które zwykle są wykonywane podczas wykonywania projektów. 

