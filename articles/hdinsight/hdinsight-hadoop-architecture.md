---
title: Architektura usługi Hadoop - Azure HDInsight | Dokumentacja firmy Microsoft
description: Opisuje Hadoop przechowywania i przetwarzania w klastrach usługi HDInsight.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: 5ec5f1f24d3bf953115bfa5023faf81df132f510
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31397457"
---
# <a name="hadoop-architecture-in-hdinsight"></a>Architektura usługi Hadoop w usłudze HDInsight

Hadoop obejmuje dwa podstawowe składniki, wysokiej gęstości pliku System (HDFS), która umożliwia przechowywanie i jeszcze inny zasób moduł negocjowania (YARN) zapewniający przetwarzania. O możliwości przechowywania i przetwarzania klastra staje się możliwe uruchamianie programów MapReduce, aby wykonać żądaną przetwarzania danych.

> [!NOTE]
> System plików HDFS nie są zwykle wdrażane w ramach klastra usługi HDInsight w celu zapewnienia magazynu. Zamiast tego warstwy interfejsu zgodnego systemem plików HDFS jest używany przez składniki platformy Hadoop. Możliwość przechowywania są udostępniane przez usługi Azure Storage lub usługi Azure Data Lake Store. Dla platformy Hadoop zadań MapReduce wykonywania w klastrze usługi HDInsight Uruchom tak, jakby był obecny system plików HDFS i dlatego nie wymagają żadnych zmian do obsługi ich wymagania dotyczące magazynu. W Hadoop w usłudze HDInsight jest zewnętrzna Usługa magazynu, ale przetwarzanie YARN pozostaje podstawowym składnikiem. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-introduction.md).

W tym artykule przedstawiono YARN i sposobu jego koordynuje wykonywania aplikacji w usłudze HDInsight.

## <a name="yarn-basics"></a>Podstawowe informacje o YARN 

YARN kontroluje oraz organizuje przetwarzania danych w Hadoop. YARN ma dwa podstawowe usługi są uruchamiane jako procesy na węzłach w klastrze: 

* ResourceManager 
* NodeManager

ResourceManager przydziela zasoby obliczeniowe klastra aplikacji, takich jak zadań MapReduce. ResourceManager przydziela te zasoby jak kontenery, gdy każdego kontenera składa się z alokacji rdzeni Procesora i pamięci RAM. Jeśli połączeniu wszystkie zasoby dostępne w klastrze i następnie rozproszonych je w blokach danej liczby rdzeni i ilości pamięci, każdy blok zasobów jest kontenerem. Każdy węzeł w klastrze ma pojemność określoną liczbę kontenerów, a w związku z tym klaster ma stały limit na liczbę kontenerów dostępne. Konfiguruje się przydziału zasobów w kontenerze. 

Po uruchomieniu aplikacji MapReduce w klastrze, ResourceManager zapewnia aplikacji kontenerów, w którym ma być wykonane. ResourceManager śledzi stan uruchomionych aplikacji, pojemność dostępna klastra i śledzi aplikacji jako zakończenie i zwolnić swoje zasoby. 

ResourceManager działa także procesu serwera sieci web, która zapewnia interfejs użytkownika sieci web, który służy do monitorowania stanu aplikacji. 

Gdy użytkownik prześle aplikacji MapReduce, aby uruchomić w klastrze, aplikacja jest przesyłany do ResourceManager. Z kolei ResourceManager przydziela kontenera na dostępne węzły NodeManager. Węzły NodeManager to, gdzie faktycznie wykonuje aplikacji. Pierwszy kontenera przydzielone uruchamia specjalnej aplikacji o nazwie ApplicationMaster. Ta ApplicationMaster jest odpowiedzialny za uzyskiwanie zasobów w formie kolejnych kontenerów, potrzebne do uruchomienia aplikacji zostało przesłane. ApplicationMaster sprawdza etapy aplikacji (mapy przemieszczanie i zmniejszyć etap) i uwzględnia ilość danych ma zostać przetworzony. Następnie żąda ApplicationMaster (*negocjuje*) zasoby z ResourceManager w imieniu aplikacji. ResourceManager z kolei przyznaje zasobów z NodeManagers w klastrze do ApplicationMaster go do użycia podczas wykonywania aplikacji. 

NodeManagers Uruchom zadania, które składają się na aplikacji, następnie ich postęp i stan wysyłać raporty do ApplicationMaster. ApplicationMaster z kolei informuje o stanie aplikacji do ResourceManager. ResourceManager zwraca wyniki do klienta.

## <a name="yarn-on-hdinsight"></a>YARN w usłudze HDInsight

Wszystkie typy klastrów usługi HDInsight wdrożyć YARN. ResourceManager jest wdrożony na potrzeby wysokiej dostępności przy użyciu wystąpienia podstawowych i pomocniczych uruchamiania na pierwszej i drugiej węzłów głównych w klastrze odpowiednio. Tylko jedno wystąpienie ResourceManager jest aktywny w czasie. Wystąpienia NodeManager uruchamiane w węzłach dostępnych procesu roboczego w klastrze.

![YARN w usłudze HDInsight](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="next-steps"></a>Kolejne kroki

* [Korzystać z usługi MapReduce w Hadoop w usłudze HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-introduction.md)
