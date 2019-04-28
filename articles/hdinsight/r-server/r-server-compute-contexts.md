---
title: COMPUTE context options obliczeniowego usługi ML w HDInsight — Azure
description: Dowiedz się więcej o opcji kontekstu obliczeniowej dostępne dla użytkowników z usługami uczenia Maszynowego na HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 9dac7aa19e428c964bd10c3ef62df949393e8d1f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124643"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>COMPUTE context options obliczeniowego usługi ML w HDInsight

Usługi ML w usłudze Azure HDInsight kontroluje sposób wywołania są wykonywane przez ustawienie kontekstu obliczeniowego. W tym artykule opisano opcje, które są dostępne określić, czy i w jaki sposób wykonywania jest zrównoleglona na rdzeni węzła krawędzi lub klastra HDInsight.

Węzeł krawędzi klastra zapewnia wygodne miejsce do łączenia z klastrem i do uruchamiania skryptów języka R. Z węzłem krawędzi istnieje możliwość uruchamiania równoległego rozproszonego funkcje kolekcję funkcji RevoScaleR między rdzeniami serwera węzła krawędzi. Można również uruchomić je w węzłach klastra za pomocą jego RevoScaleR Hadoop Mapreduce lub konteksty wystąpień obliczeniowych platformy Apache Spark.

## <a name="ml-services-on-azure-hdinsight"></a>Usługi ML w usłudze Azure HDInsight
[Usługi ML w usłudze Azure HDInsight](r-server-overview.md) zapewnia analizę opartą na R najnowszych funkcji. Można użyć, dane są przechowywane w kontenerze usługi Apache Hadoop HDFS w Twojej [obiektów Blob platformy Azure](../../storage/common/storage-introduction.md "usługi Azure Blob storage") konta magazynu, usługa Data Lake store lub lokalny system plików w systemie Linux. Ponieważ usługi ML jest oparta na R typu open source, tworzone aplikacje na podstawie języka R można zastosować żadnych pakietów języka R typu open source 8000 +. Można też procedur w [kolekcję funkcji RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), pakiet analizy danych big data firmy Microsoft, który jest dołączony do usługi ML.  

## <a name="compute-contexts-for-an-edge-node"></a>Konteksty dla węzła krawędzi wystąpień obliczeniowych
Ogólnie rzecz biorąc skrypt języka R, która działa w klastrze usługi ML w węźle brzegowym jest uruchamiany w ramach interpreter języka R w tym węźle. Wyjątki są te kroki, które wywołują funkcję kolekcję funkcji RevoScaleR. Wywołania kolekcję funkcji RevoScaleR uruchomione w środowiska obliczeniowego, który jest określany przez ustawiania kontekstu obliczeniowego kolekcję funkcji RevoScaleR.  Po uruchomieniu skryptu języka R z węzłem krawędzi jest możliwe wartości kontekstu obliczeniowego:

- lokalny sekwencyjnego (*lokalnego*)
- równoległe lokalnego (*localpar*)
- Mapreduce
- platforma Spark

*Lokalnego* i *localpar* opcje różnią się tylko w sposób **rxExec** wywołania są wykonywane. Oba wykonać inne wywołania funkcji odbierania w sposób równoległy we wszystkich dostępnych rdzeni chyba że określono inaczej, przy użyciu kolekcję funkcji RevoScaleR **numCoresToUse** opcji, na przykład `rxOptions(numCoresToUse=6)`. Opcje przetwarzania równoległego oferują optymalną wydajność.

W poniższej tabeli przedstawiono różne opcje kontekstu obliczeniowego można ustawić, jak są wykonywane wywołania:

| Kontekst obliczeniowy  | Jak ustawić                      | Kontekst wykonywania                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Lokalny sekwencyjne | rxSetComputeContext('local')    | Wykonywanie równoległego między rdzeniami serwera węzła krawędzi, z wyjątkiem rxExec wywołania, które są wykonywane szeregowo |
| Lokalne równoległych   | rxSetComputeContext('localpar') | Wykonywanie równoległego między rdzeniami serwer węzłów brzegowych |
| platforma Spark            | RxSpark()                       | Zrównoleglona rozproszonego wykonywania za pośrednictwem platformy Spark w węzłach klastra usługi HDI |
| Mapreduce       | RxHadoopMR()                    | Równoległego rozproszonego wykonywania przy użyciu Map Reduce w węzłach klastra usługi HDI |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Wskazówki dotyczące ustawiania kontekstu obliczeniowego

Które z trzech opcji możesz wybrać, które zapewniają, równoległego wykonywania zależy od charakteru pracy analizy, rozmiar i lokalizację danych. Nie ma żadnych prostej formuły, informujące o którym obliczenia kontekst do użycia. Istnieją jednak niektóre wytyczne, które mogą pomóc Ci dokonanie dobrego wyboru lub co najmniej pomagają zawęzić wybór przed uruchomieniem testów porównawczych. W tym wytyczne:

- Lokalny system plików w systemie Linux jest szybsze niż system plików HDFS.
- Powtarzane analizy są realizowane szybciej, jeśli dane są lokalne, a jeśli XDF.
- Zaleca się, aby przesyłać strumieniowo małe ilości danych ze źródła danych tekstu. W przypadku większych ilości danych, przekonwertować go na XDF przed analizą.
- Obciążenie kopiowania lub danych z węzłem krawędzi dla analizy przesyłania strumieniowego staje się bezproblemowego zarządzania dla bardzo dużych ilości danych.
- ApacheSpark jest szybsza niż Map Reduce analizy na platformie Hadoop.

Poniższe sekcje, biorąc pod uwagę te zasady, oferują pewne ogólne reguły akceptacji służąca do wybierania kontekstu obliczeniowego.

### <a name="local"></a>Lokalna
* Jeśli ilość danych w celu przeanalizowania jest mały i nie wymaga oczekiwanego, następnie prześlij go strumieniowo bezpośrednio do procedur analizy przy użyciu *lokalnego* lub *localpar*.
* Jeśli ilość danych w celu przeanalizowania jest małych i średnich i wymaga powtarzanych analizy, następnie skopiuj go do lokalnego systemu plików, zaimportuj go do XDF i analizować go za pomocą *lokalnego* lub *localpar*.

### <a name="apache-spark"></a>Apache Spark
* W przypadku dużych ilości danych do analizy, zaimportuj go do elementów DataFrame aparatu Spark za pomocą **RxHiveData** lub **RxParquetData**, lub XDF w systemie plików HDFS (chyba że magazyn jest problem) i Analizuj je przy użyciu obliczeniowego platformy Spark kontekst.

### <a name="apache-hadoop-map-reduce"></a>Apache Hadoop Map Reduce
* Kontekst obliczeniowy Map Reduce należy używać tylko wtedy, gdy wystąpią niemożliwy do pokonania problem z kontekstu obliczeniowego aparatu Spark, ponieważ jest zazwyczaj mniejsza.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Wbudowanej pomocy na rxSetComputeContext
Aby uzyskać więcej informacji i przykładów konteksty obliczeniowe kolekcję funkcji RevoScaleR Zobacz wbudowanej pomocy w języku R w metodzie rxSetComputeContext, na przykład:

    > ?rxSetComputeContext

Może również dotyczyć [omówienie obliczeń rozproszonych](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing) w [dokumentacja usługi Machine Learning Server](https://docs.microsoft.com/machine-learning-server/).

## <a name="next-steps"></a>Kolejne kroki
W tym artykule opisano opcje, które są dostępne określić, czy i w jaki sposób wykonywania jest zrównoleglona na rdzeni węzła krawędzi lub klastra HDInsight. Aby dowiedzieć się więcej o sposobie używania usługi uczenie Maszynowe przy użyciu klastrów HDInsight, zobacz następujące tematy:

* [Omówienie usług uczenia Maszynowego dla usługi Apache Hadoop](r-server-overview.md)
* [Rozpocznij pracę z usługami uczenia Maszynowego dla usługi Apache Hadoop](r-server-get-started.md)
* [Opcje usługi Azure Storage dla usługi ML w HDInsight](r-server-storage.md)

