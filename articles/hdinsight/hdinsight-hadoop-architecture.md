---
title: Architektura technologii Apache Hadoop — Azure HDInsight
description: W tym artykule opisano Apache Hadoop, przechowywania i przetwarzania w klastrach HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: edf7655c20f1b3bad5ce4d337861bd2ff3e029db
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097188"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>Architektura platformy Apache Hadoop w usłudze HDInsight

[Apache Hadoop](https://hadoop.apache.org/) obejmuje dwa składniki podstawowe: [Apache Hadoop Distributed pliku System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) zapewniający magazynu i [Apache Hadoop jeszcze inny zasób moduł negocjowania (YARN)](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) , Umożliwia przetwarzanie. Z usługi storage i możliwości przetwarzania klastra staje się stanie działać [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) programów do wykonywania żądanego przetwarzania danych.

> [!NOTE]  
> System plików HDFS nie są zwykle wdrażane w ramach klastra HDInsight w celu zapewnienia magazynu. Zamiast tego warstwy interfejsu zgodnego systemem plików HDFS jest używany przez składniki usługi Hadoop. Możliwości rzeczywisty magazyn są dostarczane przez usługę Azure Storage lub Azure Data Lake Storage. Dla platformy Hadoop zadań MapReduce, wykonywanie w klastrze HDInsight Uruchom tak, jakby systemu plików HDFS znajdowały się i dlatego nie wymagają żadnych zmian do obsługi ich potrzeb dotyczących magazynowania. Na platformie Hadoop, HDInsight magazyn jest obsługiwany zewnętrznie, ale przetwarzanie YARN pozostaje podstawowym składnikiem. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-introduction.md).

W tym artykule przedstawiono YARN i jak go służy do koordynowania wykonywanie aplikacji w HDInsight.

## <a name="apache-hadoop-yarn-basics"></a>Podstawy systemu Apache Hadoop YARN 

YARN kontroluje oraz służy do aranżacji przetwarzania danych na platformie Hadoop. YARN ma dwa podstawowe usługi są uruchamiane jako procesy w węzłach w klastrze: 

* ResourceManager 
* NodeManager

ResourceManager przyznaje klaster zasobów obliczeniowych do aplikacji, takich jak zadań MapReduce. ResourceManager przydziela te zasoby jako kontenery, w którym każdy kontener składa się z przydział rdzeni Procesora i pamięci RAM. Jeśli połączeniu wszystkich zasobów dostępnych w klastrze i następnie przesyła rdzeni i pamięci w blokach, każdy blok zasobów jest kontenerem. Każdy węzeł w klastrze ma pojemność określoną liczbę kontenerów, w związku z tym klaster ma stały limit liczby dostępnych kontenerów. Przydział zasobów w kontenerze jest konfigurowany. 

Po uruchomieniu aplikacji MapReduce w klastrze, ResourceManager zapewnia aplikacji kontenerów, w której chcesz wykonać. ResourceManager umożliwia śledzenie stanu uruchamiania aplikacji, pojemność klastra dostępny i śledzi aplikacji w miarę ich ukańczania i zwolnić swoje zasoby. 

ResourceManager również uruchamia proces serwera sieci web, która zapewnia interfejs użytkownika sieci web, aby monitorować stan aplikacji.

Gdy użytkownik przesyła aplikacji MapReduce, aby uruchomić w klastrze, aplikacja jest przesyłany do usługi Menedżera zasobów. Z kolei ResourceManager przydziela kontenerze na dostępne węzły NodeManager. Węzły NodeManager są na tym, gdzie aplikacja faktycznie wykonuje. Pierwszy kontener przydzielone uruchamia specjalnej aplikacji o nazwie ApplicationMaster. Ta ApplicationMaster jest odpowiedzialny za pobieranie zasobów w postaci kolejnych kontenery, potrzebne do uruchomienia przesłanej aplikacji. ApplicationMaster sprawdza etapy aplikacji, na przykład na etapie mapy i zmniejszyć etapów i czynniki jak dużo danych wymaga przetworzenia. Następnie żąda ApplicationMaster (*negocjuje*) zasobów z Menedżera zasobów w imieniu aplikacji. Menedżera zasobów z kolei przydziela zasoby z NodeManagers w klastrze ApplicationMaster go do użycia podczas wykonywania aplikacji. 

NodeManagers wykonania zadań, które tworzą aplikację, następnie sprawozdania ich postęp i stan ApplicationMaster. ApplicationMaster z kolei informuje o stanie aplikacji do Menedżera zasobów. ResourceManager zwraca wyniki do klienta.

## <a name="yarn-on-hdinsight"></a>YARN na HDInsight

Wszystkie typy klastrów HDInsight wdrożenie usługi YARN. Menedżera zasobów jest wdrażany w celu zapewnienia wysokiej dostępności przy użyciu wystąpienia podstawowego i pomocniczego, który jest uruchamiany w pierwszy i drugi węzłów głównych w klastrze odpowiednio. Tylko jedno wystąpienie ResourceManager jest aktywny w danym momencie. Uruchamianie wystąpienia NodeManager dostępnych procesów roboczych w różnych węzłach klastra.

![YARN na HDInsight](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="next-steps"></a>Kolejne kroki

* [Korzystanie z technologii MapReduce na platformie Apache Hadoop w HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-introduction.md)
