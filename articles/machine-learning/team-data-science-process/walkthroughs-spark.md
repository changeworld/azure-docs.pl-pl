---
title: Analiza hdinsight spark z PySpark, Scala - Team Data Science Process
description: Przykłady procesu nauki o danych zespołu, które przechodzą przez korzystanie z PySpark i Scala na platformie Azure HDInsight Spark.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864149"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Wskazówki dotyczące nauki o danych platformy HDInsight Spark przy użyciu pyspark i scala na platformie Azure

Te wskazówki używają PySpark i Scala w klastrze platformy Azure Spark do analizy predykcyjnej. Wykonaj one kroki opisane w procesie nauki o danych zespołu. Aby zapoznać się z omówieniem procesu nauki o danych zespołu, zobacz [Proces nauki o danych](overview.md). Aby uzyskać omówienie spark on HDInsight, zobacz [Wprowadzenie do spark na HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Dodatkowe wskazówki do nauki o danych, które wykonują proces nauki o danych zespołu są pogrupowane według **platformy,** której używają. Zobacz [wskazówki wykonujące proces nauki o danych zespołu](walkthroughs.md) dla itemization tych przykładów.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Przewiduj porady dotyczące taksówek za pomocą pysparku w usłudze Azure Spark

Przy użyciu danych taksówki w Nowym [Jorku, Użyj spark na platformie Azure HDInsight](spark-overview.md) instruktaż przewiduje, czy wskazówka jest wypłacana i zakres oczekiwanych kwot. W tym przykładzie użyto proces nauki o danych zespołu w scenariuszu przy użyciu [klastra platformy Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) do przechowywania, eksplorowania i funkcji inżyniera danych z publicznie dostępnego nyc podróży taksówką i zestaw danych taryfy. W tym temacie przeglądowym użyto klastra platformy Spark usługi HDInsight i notesów PySpark jupyter. Te notesy pokazują, jak eksplorować dane, a następnie jak tworzyć i używać modeli. Zaawansowany notes eksploracji i modelowania danych pokazuje, jak uwzględnić krzyżowe sprawdzanie poprawności, tworzenie hiperparametrów i ocenę modelu.

### <a name="data-exploration-and-modeling-with-spark"></a>Eksploracja i modelowanie danych za pomocą platformy Spark 
Eksploruj zestaw danych i tworzenie, ocena i oceny modeli uczenia maszynowego, pracując za pośrednictwem [Tworzenie klasyfikacji binarnej i modeli regresji dla danych z Spark MLlib toolkit tematu.](spark-data-exploration-modeling.md)

### <a name="model-consumption"></a>Zużycie modelu
Aby dowiedzieć się, jak ocenić modele klasyfikacji i regresji utworzone w tym temacie, zobacz [Ocenianie i ocenianie modeli uczenia maszynowego utworzonych przez platformę Spark.](spark-model-consumption.md)

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Krzyżowa walidacja i zamiatanie hiperparametrowe
Zobacz [Zaawansowane eksploracji danych i modelowania z Spark](spark-advanced-data-exploration-modeling.md) na jak modele mogą być szkolone przy użyciu krzyżowego sprawdzania poprawności i hyper-parametrów zamiatanie.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Przewidywanie porad dotyczących taksówek przy użyciu aplikacji Scala na platformie Azure Spark

[Użyj Scala z spark na platformie Azure](scala-walkthrough.md) instruktaż przewiduje, czy wskazówka jest wypłacana i zakres kwot oczekuje się zapłaty. Pokazano w nim, jak używać scali do nadzorowanych zadań uczenia maszynowego za pomocą pakietów biblioteki uczenia maszynowego platformy Spark (MLlib) i sparkML w klastrze platformy Azure HDInsight Spark. Przeprowadzi Cię przez zadania, które tworzą [proces nauki o danych:](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)pozyskiwania i eksploracji danych, wizualizacji, inżynierii funkcji, modelowania i zużycia modelu. Zbudowane modele obejmują regresję logistyczną i liniową, losowe lasy i drzewa pobudzone gradientem.


## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem procesu nauki o danych zespołu, zobacz [omówienie procesu nauki o danych zespołu.](overview.md)

Aby zapoznać się z omówienia cyklu życia procesu nauki o danych zespołu, zobacz [Cykl życia procesu nauki o danych zespołu.](lifecycle.md) Ten cykl życia określa kroki, od początku do końca, które projekty zwykle wykonaj, gdy są wykonywane. 

