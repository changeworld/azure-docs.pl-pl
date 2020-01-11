---
title: Analiza w usłudze Azure HDInsight Hadoop przy użyciu technologii Hive - zespołu danych dla celów naukowych
description: Przykłady procesie nauki o danych zespołu, które pomagają przy użyciu programu Hive w usłudze Azure HDInsight Hadoop do analizy predykcyjnej.
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
ms.openlocfilehash: f72ea6ed5f0eec076d181ef56c99c4f1308a7741
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864166"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>HDInsight Hadoop przewodniki dotyczące przetwarzania danych za pomocą programu Hive na platformie Azure 

Te przewodniki korzystanie z programu Hive przy użyciu klastra usługi HDInsight Hadoop do analizy predykcyjnej. One wykonaj kroki opisane w procesie nauki o danych zespołu. Omówienie procesu do nauki o danych zespołu, zobacz [danych dla celów naukowych](overview.md). Wprowadzenie do usługi Azure HDInsight, zobacz [wprowadzenie do usługi Azure HDInsight, stosu technologii Hadoop i klastrów Hadoop](../../hdinsight/hadoop/apache-hadoop-introduction.md).

Przewodniki dotyczące przetwarzania dodatkowe dane, które są wykonywane w procesie nauki o danych zespołu są pogrupowane według **platformy** używane. Zobacz [przewodniki dotyczące wykonywania procesu do nauki o danych zespołu](walkthroughs.md) do podziału na pozycje z tych przykładów.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Przewidywanie napiwków przy użyciu technologii Hive z usługą HDInsight Hadoop

[Klastrów HDInsight Hadoop użyj](hive-walkthrough.md) instruktażu wykorzystano dane taksówek w Nowym Jorku do prognozowania: 

- Czy zostało opłacone Porada 
- Dystrybucja kwoty Porada

Scenariusz jest implementowany przy użyciu programu Hive za pomocą [klastra Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/). Dowiesz się, jak przechowywać, eksplorować i danych inżynier ds. funkcji z publicznie dostępnego NYC taksówki podróży i klasie zestawu danych. Usługa Azure Machine Learning możesz także użyć do tworzenia i wdrażania modeli.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Przewidywanie kliknięć anonsu przy użyciu technologii Hive z usługą HDInsight Hadoop

W przypadku [klastrów użycia Azure HDInsight Hadoop na 1 TB zestawu danych](hive-criteo-walkthrough.md) są używane publicznie dostępne [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) kliknij zestaw danych, aby przewidzieć, czy Porada jest płatna i oczekiwane kwoty. Scenariusz jest implementowany przy użyciu programu Hive za pomocą [klastra Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) do przechowywania, zapoznaj się z, inżynier ds. funkcji i w dół przykładowych danych. Tworzenie, szkolenie i score model klasyfikacji binarnej przewidywania, czy użytkownik kliknie anonsu używa usługi Azure Machine Learning. Zawiera przewodnik pokazujący sposób publikowania jednego z tych modeli jako usług sieci Web.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać omówienie najważniejsze składniki wchodzące w skład procesu do nauki o danych zespołu, zobacz [przegląd danych zespołu dla celów naukowych](overview.md).

Omówienie cyklu życia procesu do nauki o danych zespołu, używanej do organizowania projektów do nauki o danych, zobacz [cykl życia zespołowego danych naukowych](lifecycle.md). Cykl życia zawiera opis kroków, od początku do końca, że projekty zazwyczaj należy wykonać podczas są wykonywane. 

