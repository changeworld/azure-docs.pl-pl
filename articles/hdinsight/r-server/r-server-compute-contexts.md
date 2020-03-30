---
title: Opcje kontekstu obliczeń dla usług ML w usłudze HDInsight — Azure
description: Dowiedz się więcej o różnych opcjach kontekstu obliczeniowego dostępnych dla użytkowników w usługach ML w programie HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: b67bd5b6310e1f8ce35dc14690757209ef62c9d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75660260"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>Opcje kontekstu obliczania usług ML w programie HDInsight

Usługi ML w usłudze Azure HDInsight sterują sposób wykonywania wywołań przez ustawienie kontekstu obliczeniowego. W tym artykule opisano opcje, które są dostępne, aby określić, czy i jak wykonanie jest równoległe między rdzeniami węzła krawędzi lub klastra HDInsight.

Węzeł brzegowy klastra zapewnia wygodne miejsce do łączenia się z klastrem i uruchamiania skryptów języka R. W węźle brzegowym można uruchamiać równoległe funkcje rozproszone RevoScaleR na rdzeniach serwera węzła brzegowego. Można również uruchomić je w węzłach klastra przy użyciu RevoScaleR's Hadoop Map Reduce lub Apache Spark obliczeń kontekstów.

## <a name="ml-services-on-azure-hdinsight"></a>Usługi ML w usłudze Azure HDInsight

[Usługi ML w usłudze Azure HDInsight](r-server-overview.md) zapewniają najnowsze możliwości analizy opartej na r. Można użyć danych, które są przechowywane w kontenerze Apache Hadoop HDFS na koncie magazynu [obiektów Blob platformy Azure,](../../storage/common/storage-introduction.md "Azure Blob Storage") w magazynie usługi Data Lake store lub lokalnym systemie plików systemu Linux. Ponieważ usługi ML services są oparte na środowisku R typu open source, tworzone aplikacje oparte na języku R mogą stosować dowolne z ponad 8000 pakietów języka R typu open source. Mogą również korzystać z procedur w [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), pakiet analizy dużych zbiorów danych firmy Microsoft, który jest dołączony do usług ML Services.  

## <a name="compute-contexts-for-an-edge-node"></a>Obliczania kontekstów dla węzła brzegowego

Ogólnie rzecz biorąc skrypt języka R, który jest uruchamiany w klastrze usług ML w węźle brzegowym, jest uruchamiany w interpreterze języka R w tym węźle. Wyjątki są te kroki, które wywołują RevoScaleR funkcji. Wywołania RevoScaleR są uruchamiane w środowisku obliczeniowym, które zależy od sposobu ustawiania kontekstu obliczeniowego RevoScaleR.  Po uruchomieniu skryptu Języka R z węzła krawędzi możliwe wartości kontekstu obliczeniowego są następujące:

- lokalny sekwencyjny (*lokalny*)
- lokalny równoległy (*localpar*)
- Zmniejsz mapę
- platforma Spark

Opcje *lokalne* i *localpar* różnią się tylko w jaki **sposób rxExec** wywołania są wykonywane. Oba wykonują inne wywołania funkcji rx w sposób równoległy we wszystkich dostępnych rdzeniach, chyba że określono inaczej `rxOptions(numCoresToUse=6)`za pomocą opcji RevoScaleR **numCoresToUse,** na przykład . Opcje wykonywania równoległego oferują optymalną wydajność.

W poniższej tabeli podsumowano różne opcje kontekstu obliczeniowego, aby ustawić sposób wykonywania wywołań:

| Kontekst obliczeniowy  | Jak ustawić                      | Kontekst wykonywania                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Lokalne sekwencyjne | rxSetComputeContext('local')    | Równoległe wykonywanie między rdzeniami serwera węzłów brzegowych, z wyjątkiem wywołań rxExec, które są wykonywane szeregowo |
| Równolegle lokalny   | rxSetComputeContext('localpar') | Równoległe wykonywanie między rdzeniami serwera węzłów brzegowych |
| platforma Spark            | RxSpark()                       | Równoległe wykonanie rozproszone za pośrednictwem platformy Spark w węzłach klastra HDI |
| Zmniejsz mapę       | RxHadoopMR()                    | Równoległe wykonanie rozproszone za pośrednictwem map Reduce w węzłach klastra HDI |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Wskazówki dotyczące podejmowania decyzji w kontekście obliczeń

Która z trzech opcji, które wybierzesz, które zapewniają równoległe wykonanie zależy od charakteru pracy analitycznej, rozmiaru i lokalizacji danych. Nie ma prostej formuły, która informuje, który kontekst obliczeniowy do użycia. Istnieją jednak pewne zasady przewodnie, które mogą pomóc ci dokonać właściwego wyboru, a przynajmniej pomóc zawęzić swoje wybory przed uruchomieniem testu porównawczego. Te zasady przewodnie obejmują:

- Lokalny system plików Linux jest szybszy niż HDFS.
- Powtarzane analizy są szybsze, jeśli dane są lokalne i jeśli są w XDF.
- Zaleca się przesyłanie strumieniowe niewielkich ilości danych ze źródła danych tekstowych. Jeśli ilość danych jest większa, przekonwertuj je na XDF przed analizą.
- Obciążenie związane z kopiowaniem lub przesyłaniem strumieniowo danych do węzła krawędzi w celu analizy staje się nie do opanowania dla bardzo dużych ilości danych.
- ApacheSpark jest szybszy niż Map Reduce do analizy w Hadoop.

Biorąc pod uwagę te zasady, w poniższych sekcjach przedstawiono pewne ogólne zasady przy wyborze kontekstu obliczeniowego.

### <a name="local"></a>Lokalny

- Jeśli ilość danych do analizy jest niewielka i nie wymaga ponownej analizy, przesyłaj je bezpośrednio do procedury analizy przy użyciu *lokalnego* lub *lokalnego.*
- Jeśli ilość danych do analizy jest mała lub średnia i wymaga ponownej analizy, skopiuj je do lokalnego systemu plików, zaimportuj do XDF i przeanalizuj za pośrednictwem *lokalnego* lub *lokalnego.*

### <a name="apache-spark"></a>Apache Spark

- Jeśli ilość danych do analizy jest duża, a następnie zaimportować go do Spark DataFrame przy użyciu **RxHiveData** lub **RxParquetData**, lub XDF w hdfs (chyba że magazyn jest problem) i analizować go przy użyciu kontekstu obliczeń Platformy Spark.

### <a name="apache-hadoop-map-reduce"></a>Apache Hadoop Mapa Zmniejszyć

- Użyj mapu Zmniejsz kontekst obliczeniowy tylko wtedy, gdy natkniesz się na problem nie do pokonania z kontekstem obliczeń platformy Spark, ponieważ jest on ogólnie wolniejszy.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Inline pomoc na rxSetComputeContext
Aby uzyskać więcej informacji i przykładów kontekstów obliczeniowych RevoScaleR, zobacz wbudowaną pomoc w R na rxSetComputeContext metody, na przykład:

    > ?rxSetComputeContext

Można również zapoznać się z [omówieniem przetwarzania rozproszonego](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing) w [dokumentacji serwera uczenia maszynowego](https://docs.microsoft.com/machine-learning-server/).

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się o opcjach, które są dostępne, aby określić, czy i jak wykonanie jest równoległe między rdzeniami węzła krawędzi lub klastra HDInsight. Aby dowiedzieć się więcej o korzystaniu z usług ML z klastrami HDInsight, zobacz następujące tematy:

- [Przegląd usług ML dla Apache Hadoop](r-server-overview.md)
- [Opcje usługi Azure Storage dla usług ML w usłudze HDInsight](r-server-storage.md)
