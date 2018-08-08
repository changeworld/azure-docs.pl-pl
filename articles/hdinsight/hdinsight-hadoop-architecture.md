---
title: Architektura usługi Hadoop — Azure HDInsight
description: W tym artykule opisano Hadoop przechowywania i przetwarzania w klastrach HDInsight.
services: hdinsight
author: ashishthaps
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: 754f4538f7c2a8de6286198094b38d40c466a15f
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39599476"
---
# <a name="hadoop-architecture-in-hdinsight"></a>Architektura usługi Hadoop w usłudze HDInsight

Hadoop obejmuje dwa podstawowe składniki, wysoka gęstość File System (HDFS), która oferuje magazyn i jeszcze inny zasób moduł negocjowania (YARN), która oferuje przetwarzanie. Dzięki możliwości przechowywania i przetwarzania klastra staje się możliwe uruchamianie programów MapReduce do wykonywania żądanego przetwarzania danych.

> [!NOTE]
> System plików HDFS nie są zwykle wdrażane w ramach klastra HDInsight w celu zapewnienia magazynu. Zamiast tego warstwy interfejsu zgodnego systemem plików HDFS jest używany przez składniki usługi Hadoop. Możliwości rzeczywisty magazyn są dostarczane przez usługę Azure Storage lub Azure Data Lake Store. Dla platformy Hadoop zadań MapReduce, wykonywanie w klastrze HDInsight Uruchom tak, jakby systemu plików HDFS znajdowały się i dlatego nie wymagają żadnych zmian do obsługi ich potrzeb dotyczących magazynowania. Na platformie Hadoop, HDInsight magazyn jest obsługiwany zewnętrznie, ale przetwarzanie YARN pozostaje podstawowym składnikiem. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-introduction.md).

W tym artykule przedstawiono YARN i jak go służy do koordynowania wykonywanie aplikacji w HDInsight.

## <a name="yarn-basics"></a>Podstawy usługi YARN 

YARN kontroluje oraz służy do aranżacji przetwarzania danych na platformie Hadoop. YARN ma dwa podstawowe usługi są uruchamiane jako procesy w węzłach w klastrze: 

* ResourceManager 
* NodeManager

ResourceManager przyznaje klaster zasobów obliczeniowych do aplikacji, takich jak zadań MapReduce. ResourceManager przydziela te zasoby jako kontenery, w którym każdy kontener składa się z przydział rdzeni Procesora i pamięci RAM. Jeśli połączeniu wszystkich zasobów dostępnych w klastrze, a następnie przesyła je w blokach po danej liczby rdzeni i pamięci, każdy blok zasobów jest kontenerem. Każdy węzeł w klastrze ma pojemność określoną liczbę kontenerów, a w związku z tym klaster ma stały limit liczby dostępnych kontenerów. Przydział zasobów w kontenerze jest konfigurowany. 

Po uruchomieniu aplikacji MapReduce w klastrze, ResourceManager zapewnia aplikacji kontenerów, w której chcesz wykonać. ResourceManager umożliwia śledzenie stanu uruchamiania aplikacji, pojemność klastra dostępny i śledzi aplikacji w miarę ich ukańczania i zwolnić swoje zasoby. 

ResourceManager również uruchamia proces serwera sieci web, która zapewnia interfejs użytkownika sieci web, które służy do monitorowania stanu aplikacji. 

Gdy użytkownik przesyła aplikacji MapReduce, aby uruchomić w klastrze, aplikacja jest przesyłany do usługi Menedżera zasobów. Z kolei ResourceManager przydziela kontenerze na dostępne węzły NodeManager. Węzły NodeManager są na tym, gdzie aplikacja faktycznie wykonuje. Pierwszy kontener przydzielone uruchamia specjalnej aplikacji o nazwie ApplicationMaster. Ta ApplicationMaster jest odpowiedzialny za pobieranie zasobów w postaci kolejnych kontenery, potrzebne do uruchomienia przesłanej aplikacji. ApplicationMaster sprawdza etapów aplikacji (Mapa etap i zmniejszyć etapu) i uwzględnia ilości danych ma zostać przetworzony. Następnie żąda ApplicationMaster (*negocjuje*) zasobów z Menedżera zasobów w imieniu aplikacji. Menedżera zasobów z kolei przydziela zasoby z NodeManagers w klastrze ApplicationMaster go do użycia podczas wykonywania aplikacji. 

NodeManagers wykonania zadań, które tworzą aplikację, następnie sprawozdania ich postęp i stan ApplicationMaster. ApplicationMaster z kolei informuje o stanie aplikacji do Menedżera zasobów. ResourceManager zwraca wyniki do klienta.

## <a name="yarn-on-hdinsight"></a>YARN na HDInsight

Wszystkie typy klastrów HDInsight wdrożenie usługi YARN. Menedżera zasobów jest wdrażany wysokiej dostępności przy użyciu wystąpienia podstawowego i pomocniczego, uruchamiania na pierwszym i drugim węzłów głównych w klastrze odpowiednio. Tylko jedno wystąpienie ResourceManager jest aktywny w danym momencie. Uruchamianie wystąpienia NodeManager dostępnych procesów roboczych w różnych węzłach klastra.

![YARN na HDInsight](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="next-steps"></a>Kolejne kroki

* [Korzystanie z technologii MapReduce usługi Hadoop w HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-introduction.md)
