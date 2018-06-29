---
title: Opcje kontekst dla usługi uczenia Maszynowego w usłudze HDInsight - Azure obliczania | Dokumentacja firmy Microsoft
description: Informacje na temat opcji kontekstu obliczeń różne dostępne dla użytkowników z usługi uczenia Maszynowego w usłudze HDInsight
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 0deb0b1c-4094-459b-94fc-ec9b774c1f8a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: 57480cef48182a56b315d7d6932883c485f5a7c8
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050112"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>Obliczenia bazy danych opcji kontekstu dla usługi uczenia Maszynowego w usłudze HDInsight

Usługi uczenia Maszynowego w usłudze Azure HDInsight kontroluje sposób wywołania są wykonywane przez ustawienie kontekstu obliczeń. W tym artykule opisano opcje, które są dostępne określić, czy i jak wykonanie jest zarządzana z przetwarzaniem na rdzeni węzła krawędzi lub klastra usługi HDInsight.

Węzeł krawędzi klastra umożliwia wygodne Połącz się z klastrem i uruchamiania skryptów R. Z węzłem krawędzi istnieje możliwość uruchomionych zrównoleglone funkcje rozproszonej RevoScaleR na rdzeni serwera węzła krawędzi. Można również uruchomić je w węzłach klastra przy użyciu jego RevoScaleR Hadoop mapy zmniejszyć lub Spark obliczeniowe kontekstach.

## <a name="ml-services-on-azure-hdinsight"></a>Usługi uczenia Maszynowego w usłudze Azure HDInsight
[Usługi uczenia Maszynowego w usłudze Azure HDInsight](r-server-overview.md) zawiera najnowsze funkcje analizy na podstawie R. Można użyć danych przechowywanych w kontenerze systemu plików HDFS w Twojej [obiektów Blob platformy Azure](../../storage/common/storage-introduction.md "magazynu obiektów Blob Azure") konta magazynu, Data Lake store lub lokalny system plików w systemie Linux. Ponieważ usługi uczenia Maszynowego jest zbudowany na typu open source R, można zastosować R aplikacji tworzonych żadnych pakietów typu open source R 8000 +. Można też procedury w [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), pakietu analizy danych big data firmy Microsoft, dołączonej usługi uczenia Maszynowego.  

## <a name="compute-contexts-for-an-edge-node"></a>Obliczenia bazy danych kontekstów dla węzła krawędzi
Ogólnie rzecz biorąc skrypt języka R, uruchamiany w klastrze usługi uczenia Maszynowego w węźle krawędzi jest uruchamiany w ramach interpreter języka R w tym węźle. Wyjątki są te kroki, które wywołują funkcję RevoScaleR. Wywołania RevoScaleR uruchomione w środowisku obliczeniowe, które określają sposób ustawiania kontekstu obliczeń RevoScaleR.  Po uruchomieniu skryptu języka R z węzłem krawędzi, możliwe wartości kontekstu obliczeń to:

- lokalny sekwencyjnych (*lokalnego*)
- równoległe lokalnego (*localpar*)
- Zmniejsz mapy
- platforma Spark

*Lokalnego* i *localpar* opcje różnią się tylko w sposób **rxExec** wywołania są wykonywane. Oba wykonać inne wywołania funkcji odbierania w sposób równoległy między wszystkie dostępne rdzenie chyba że określono inaczej, za pomocą RevoScaleR **numCoresToUse** opcji, na przykład `rxOptions(numCoresToUse=6)`. Opcje przetwarzania równoległego oferują optymalną wydajność.

W poniższej tabeli przedstawiono różne opcje kontekstu obliczeń ustalenie sposobu wykonywania wywołań:

| Kontekst obliczeniowy  | Jak ustawić                      | Kontekst wykonywania                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Lokalny sekwencyjne | rxSetComputeContext('local')    | Wykonanie zrównoleglone między rdzenie serwera węzła krawędzi, z wyjątkiem rxExec wywołań, które są wykonywane szeregowo |
| Równoległe lokalnego   | rxSetComputeContext('localpar') | Wykonanie zrównoleglone między rdzenie serwera granicznego węzła |
| platforma Spark            | RxSpark()                       | Zrównoleglone rozproszonego wykonywania za pośrednictwem platformy Spark w węzłach klastra HDI |
| Zmniejsz mapy       | RxHadoopMR()                    | Zrównoleglone rozproszonego wykonywania przy użyciu mapy zmniejszyć w węzłach klastra HDI |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Wytyczne dotyczące podejmowania decyzji o w kontekście obliczeń

Z trzech opcji wybierzesz umożliwiających wykonanie zrównoleglone zależnej od charakteru pracy analytics, rozmiar i lokalizację danych. Nie ma żadnych prostej formuły, informujący o którym obliczeniowe kontekst do użycia. Istnieją jednak niektóre wytyczne, które mogą pomóc Ci wybrać odpowiednie opcje lub co najmniej pomóc zawęzić wybór przed uruchomieniem testu porównawczego. Obejmują one wytyczne:

- Lokalny system plików systemu Linux jest szybsza niż system plików HDFS.
- Jeśli dane są lokalne, a jeśli XDF, szybsze są powtarzane analizy.
- Zaleca się do przesyłania strumieniowego niewielkich ilości danych ze źródła danych tekstowych. W przypadku większych ilości danych, przekonwertować go na XDF przed analizą.
- Koszty kopiowania lub strumieniowe przesyłanie danych z węzłem krawędzi do analizy staje się bezproblemowego zarządzania dla bardzo dużych ilości danych.
- Platforma Spark jest szybsza niż mapa zmniejszyć do analizy w Hadoop.

Podana tych zasad, poniższe sekcje zapewniają pewne ogólne reguły przyjąć wybierania kontekstu obliczeń.

### <a name="local"></a>Lokalna
* Jeśli ilość danych w celu przeanalizowania jest mała i nie wymaga oczekiwanego, następnie strumienia go bezpośrednio do analizy rutynowych użyciu *lokalnego* lub *localpar*.
* Jeśli ilość danych w celu przeanalizowania jest małych i średnich i wymaga oczekiwanego, następnie skopiować go do lokalnego systemu plików, zaimportuj go do XDF i analizować go za pomocą *lokalnego* lub *localpar*.

### <a name="hadoop-spark"></a>Hadoop, Spark
* W przypadku dużych ilości danych do analizy, następnie zaimportować go do Spark DataFrame przy użyciu **RxHiveData** lub **RxParquetData**, lub XDF w systemie plików HDFS (chyba, że magazyn jest problemu) i przeanalizuj go przy użyciu obliczeniowych Spark kontekst.

### <a name="hadoop-map-reduce"></a>Zmniejsz mapy usługi Hadoop
* Tylko wtedy, gdy wystąpią porównania problem z kontekstem obliczeń Spark, ponieważ jest ono zazwyczaj wolniej, należy używać kontekstu obliczeń zmniejszyć mapy.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Wbudowany pomoc na temat rxSetComputeContext
Dodatkowe informacje i przykłady RevoScaleR kontekstów obliczeń dla wbudowanego pomoc w R w metodzie rxSetComputeContext, na przykład:

    > ?rxSetComputeContext

Można także odwoływać się do [rozproszonego przetwarzania danych omówienie](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing) w [dokumentacji Machine Learning Server](https://docs.microsoft.com/machine-learning-server/).

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono o opcjach, które są dostępne określić, czy i jak wykonanie jest zarządzana z przetwarzaniem na rdzeni węzła krawędzi lub klastra usługi HDInsight. Aby dowiedzieć się więcej na temat sposobu korzystania z usługi uczenie Maszynowe z klastrami usługi HDInsight, zobacz następujące tematy:

* [Omówienie usług ML dla platformy Hadoop](r-server-overview.md)
* [Wprowadzenie do uczenia Maszynowego usługi Hadoop](r-server-get-started.md)
* [Azure opcje magazynu dla usługi uczenia Maszynowego w usłudze HDInsight](r-server-storage.md)

