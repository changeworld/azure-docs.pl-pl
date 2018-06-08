---
title: HDInsight Hadoop nauki wskazówki dotyczące danych na platformie Azure przy użyciu Hive | Dokumentacja firmy Microsoft
description: Przykłady procesu nauki danych zespołu, które opisano przy użyciu Hive w usłudze Azure HDInsight Hadoop do analizy predykcyjnej.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
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
ms.openlocfilehash: 8b7eead12b6546ad86f6ff0aa48f4754c00a4734
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838759"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>Hive HDInsight Hadoop nauki wskazówki dotyczące danych przy użyciu na platformie Azure 

Te wskazówki korzystanie z programu Hive z klastrem usługi HDInsight Hadoop do analizy predykcyjnej. One wykonaj kroki opisane w procesie nauki danych zespołu. Omówienie procesu nauki danych zespołu, zobacz [proces nauki danych](overview.md). Aby obejrzeć wprowadzenie do usługi Azure HDInsight, zobacz [wprowadzenie do usługi Azure HDInsight, stos technologiczny Hadoop i klastrów platformy Hadoop](../../hdinsight/hadoop/apache-hadoop-introduction.md).

Wskazówki dotyczące nauki dodatkowych danych, których wykonanie procesu analizy danych zespołu są pogrupowane według **platformy** jak używane. Zobacz [wskazówki dotyczące wykonywania procesu nauki danych zespołu](walkthroughs.md) do podziału na pozycje tych przykładów.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Przewidywanie porady taksówki przy użyciu Hive z HDInsight Hadoop

[Klastrów HDInsight Hadoop użyj](hive-walkthrough.md) wskazówki korzysta z nowego Jorku taksówkach danych do prognozowania: 

- Określa, czy ma być stosowany porady 
- Rozkład liczby Porada

Scenariusz jest implementowane przy użyciu programu Hive z [klastra usługi Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/). Możesz dowiedzieć się, jak przechowywać, zapoznaj się i dane funkcji odtwarzania z dostępnych publicznie NYC taksówki podróży i taryfy zestawu danych. Uczenie maszynowe Azure można również użyć do tworzenia i wdrażania modeli.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Przewidywanie kliknięć anonsu przy użyciu Hive z HDInsight Hadoop

[Użyj Azure Hadoop w usłudze Hdinsight w zestawie danych 1 TB](hive-criteo-walkthrough.md) wskazówki używa publicznie dostępnych [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) kliknij zestaw danych do przewidzenia, czy ma być stosowany porady i zakres kwoty oczekiwano. Scenariusz jest implementowane przy użyciu programu Hive z [klastra usługi Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) do przechowywania, Eksploruj funkcję odtwarzania i w dół przykładowych danych. Uczenie maszynowe Azure używa do kompilacji, szkolenia i score model klasyfikacji binarnej przewidywania, czy użytkownik kliknie anonsu. Zawiera wskazówki przedstawiający sposób publikowania jednego z tych modeli jako usługę sieci Web.


## <a name="next-steps"></a>Kolejne kroki

Omówienie najważniejsze składniki wchodzące w skład procesu nauki danych zespołu, zobacz [Omówienie procesu nauki danych zespołu](overview.md).

Omówienie cykl zespołu w procesie nauki danych służy do struktury projektów analizy danych, zobacz [cyklu życia procesu nauki danych zespołu](lifecycle.md). Cykl życia omówiono kroki, od początku do końca, że projekty zwykle należy wykonać podczas są wykonywane. 

