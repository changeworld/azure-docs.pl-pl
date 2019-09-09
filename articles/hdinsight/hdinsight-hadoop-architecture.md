---
title: Architektura Apache Hadoop — Azure HDInsight
description: Opisuje Apache Hadoop magazynowanie i przetwarzanie w klastrach usługi Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/27/2019
ms.openlocfilehash: d41d671cf773bdab20c3f105c7d1abb6c7bde840
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810238"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>Architektura platformy Apache Hadoop w usłudze HDInsight

[Apache Hadoop](https://hadoop.apache.org/) obejmuje dwa podstawowe składniki: [Apache HADOOP rozproszony system plików (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) , który udostępnia magazyn, i [Apache Hadoop jeszcze inne WYnegocjowane zasoby (przędza)](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) , które zapewniają przetwarzanie. Dzięki możliwościom magazynowania i przetwarzania klaster może uruchamiać programy [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) do wykonywania żądanych operacji przetwarzania danych.

> [!NOTE]  
> System plików HDFS nie jest zazwyczaj wdrażany w klastrze usługi HDInsight w celu zapewnienia magazynu. Zamiast tego warstwa interfejsu zgodna z systemem plików HDFS jest używana przez składniki usługi Hadoop. Rzeczywista możliwość magazynowania jest zapewniana przez usługę Azure Storage lub Azure Data Lake Storage. W przypadku usługi Hadoop zadania MapReduce wykonywane w klastrze usługi HDInsight są uruchamiane tak, jakby były obecne w systemie plików HDFS i dlatego nie wymagają żadnych zmian do obsługi ich potrzeb dotyczących magazynu. W usłudze Hadoop w usłudze HDInsight magazyn jest źródłem, ale przetwarzanie PRZĘDZenia pozostaje składnikiem podstawowym. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-introduction.md).

W tym artykule omówiono PRZĘDZę i sposób koordynowania wykonywania aplikacji w usłudze HDInsight.

## <a name="apache-hadoop-yarn-basics"></a>Podstawy Apache Hadoop PRZĘDZy 

PRZĘDZa zarządza i organizuje przetwarzanie danych w usłudze Hadoop. PRZĘDZa ma dwie podstawowe usługi, które są uruchamiane jako procesy na węzłach w klastrze: 

* ResourceManager 
* Węzełmanager

Program ResourceManager przydaje zasoby obliczeniowe klastra do aplikacji, takich jak MapReduce jobs. Obiekt ResourceManager przypisuje te zasoby jako kontenery, gdzie każdy kontener składa się z przydziału rdzeni procesora CPU i pamięci RAM. W przypadku połączenia wszystkich zasobów dostępnych w klastrze, a następnie rozdystrybuowania rdzeni i pamięci w blokach, każdy blok zasobów jest kontenerem. Każdy węzeł w klastrze ma pojemność określoną liczbę kontenerów, w związku z czym klaster ma stały limit liczby dostępnych kontenerów. Przydział zasobów w kontenerze można skonfigurować. 

Gdy aplikacja MapReduce jest uruchamiana w klastrze, obiekt ResourceManager zapewnia aplikację kontenerów, które mają zostać wykonane. Program ResourceManager śledzi stan uruchomionych aplikacji, dostępnej pojemności klastra i śledzi aplikacje w miarę ich kończenia i zwalniają zasoby. 

Obiekt ResourceManager również uruchamia proces serwera sieci Web, który udostępnia interfejs użytkownika sieci Web do monitorowania stanu aplikacji.

Gdy użytkownik przesyła aplikację MapReduce do uruchamiania w klastrze, aplikacja zostanie przesłana do elementu ResourceManager. Z kolei obiekt ResourceManager przydziela kontener w dostępnych węzłach Nodemanager. Węzły Nodemanager to miejsce, w którym aplikacja jest faktycznie wykonywana. Pierwszy przydzielony kontener uruchamia specjalną aplikację o nazwie ApplicationMaster. Ten ApplicationMaster jest odpowiedzialny za pozyskiwanie zasobów w formie kolejnych kontenerów, co jest konieczne do uruchomienia przesłanej aplikacji. ApplicationMaster bada etapy aplikacji, na przykład etap mapy i zmniejsza etap, oraz czynniki, w których należy przetworzyć dane. ApplicationMaster następnie żąda (*negocjuje*) zasobów z obiektu ResourceManager w imieniu aplikacji. Program ResourceManager z kolei przydaje zasoby z NodeManagers w klastrze do ApplicationMaster, aby można było użyć ich do wykonywania aplikacji. 

NodeManagers uruchamia zadania wchodzące w skład aplikacji, a następnie raportuje postęp i stan z powrotem do ApplicationMaster. ApplicationMaster z kolei raportuje stan aplikacji z powrotem do obiektu ResourceManager. Datasourcemanager zwraca wyniki do klienta.

## <a name="yarn-on-hdinsight"></a>PRZĘDZa w usłudze HDInsight

Wszystkie typy klastrów usługi HDInsight wdrażają PRZĘDZę. Program ResourceManager został wdrożony w celu zapewnienia wysokiej dostępności z wystąpieniem podstawowym i pomocniczym, które działa odpowiednio do pierwszego i drugiego węzła głównego w klastrze. Tylko jedno wystąpienie obiektu ResourceManager jest aktywne w danym momencie. Wystąpienia Nodemanager działają w ramach dostępnych węzłów procesu roboczego w klastrze.

![PRZĘDZa w usłudze HDInsight](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z technologii MapReduce na platformie Apache Hadoop w usłudze HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-introduction.md)
