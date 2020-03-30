---
title: Analiza usługi Azure HDInsight Hadoop przy użyciu procesu analizy danych zespołu
description: Przykłady procesu nauki o danych zespołu, które przechodzą przez korzystanie z hive na platformie Azure HDInsight Hadoop do analizy predykcyjnej.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864166"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>Wskazówki dotyczące nauki o danych usługi HDInsight Hadoop przy użyciu gałęzi na platformie Azure 

Te wskazówki używają hive z klastra HDInsight Hadoop do analizy predykcyjnej. Wykonaj one kroki opisane w procesie nauki o danych zespołu. Aby zapoznać się z omówieniem procesu nauki o danych zespołu, zobacz [Proces nauki o danych](overview.md). Aby zapoznać się z wprowadzeniem do usługi Azure HDInsight, [stosu technologii Hadoop i klastrów Hadoop.](../../hdinsight/hadoop/apache-hadoop-introduction.md)

Dodatkowe wskazówki do nauki o danych, które wykonują proces nauki o danych zespołu są pogrupowane według **platformy,** której używają. Zobacz [wskazówki wykonujące proces nauki o danych zespołu](walkthroughs.md) dla itemization tych przykładów.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Przewiduj porady dotyczące taksówek za pomocą hive z HDInsight Hadoop

[Przewodnik Użyj klastrów HDInsight Hadoop](hive-walkthrough.md) używa danych z nowojorskich taksówek do przewidywania: 

- Czy napiwek jest płatny 
- Rozkład kwot napiwków

Scenariusz jest implementowany przy użyciu gałęzi z [klastrem Usługi Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/). Dowiesz się, jak przechowywać, eksplorować i udostępniać dane inżyniera z publicznie dostępnego zestawu danych dotyczących taksówek i taryf w Nowym Jorku. Za pomocą usługi Azure Machine Learning można również tworzyć i wdrażać modele.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Przewidywanie kliknięć anonsu za pomocą gałęzi za pomocą funkcji HDInsight Hadoop

[Użyj platformy Azure HDInsight Klastry Hadoop na 1 TB zestawu danych](hive-criteo-walkthrough.md) instruktażowy używa publicznie dostępne [criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) kliknij zestaw danych, aby przewidzieć, czy wskazówka jest wypłacana i oczekiwane kwoty. Scenariusz jest implementowany przy użyciu gałęzi z [klastra Usługi Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) do przechowywania, eksplorowania, inżynierii funkcji i w dół przykładowych danych. Używa usługi Azure Machine Learning do tworzenia, uczenia i oceniania modelu klasyfikacji binarnej, który przewiduje, czy użytkownik kliknie anons. W przewodniku kończy się pokazano, jak opublikować jeden z tych modeli jako usługi sieci Web.


## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem kluczowych składników, które składają się na proces nauki o danych zespołu, zobacz [omówienie procesu nauki o danych zespołu.](overview.md)

Aby zapoznać się z omówień cyklu życia procesu nauki o danych zespołu, którego można użyć do struktury projektów nauki o danych, zobacz [cykl życia procesu nauki o danych zespołu.](lifecycle.md) Cykl życia określa kroki, od początku do końca, które projekty zwykle wykonaj, gdy są one wykonywane. 

