---
title: Architektura Apache Hadoop — Azure HDInsight
description: W tym artykule apache hadoop magazynu i przetwarzania w klastrach usługi Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/07/2020
ms.openlocfilehash: 3feacd94558ba275c81469827993aef106ae633c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162212"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>Architektura platformy Apache Hadoop w usłudze HDInsight

[Apache Hadoop](https://hadoop.apache.org/) zawiera dwa podstawowe składniki: [Apache Hadoop Distributed File System (HDFS),](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) który zapewnia pamięć masową, i [Apache Hadoop Yet Another Resource Negotiator (YARN),](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) który zapewnia przetwarzanie. Dzięki możliwości przechowywania i przetwarzania klaster staje się zdolny do uruchamiania programów [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) do wykonywania żądanego przetwarzania danych.

> [!NOTE]  
> Usługa HDFS zazwyczaj nie jest wdrażana w klastrze HDInsight w celu zapewnienia pamięci masowej. Zamiast tego warstwa interfejsu zgodna z systemem HDFS jest używana przez składniki Hadoop. Rzeczywista funkcja magazynu jest dostarczana przez usługę Azure Storage lub usługę Azure Data Lake Storage. Dla Hadoop MapReduce zadania wykonywane w klastrze HDInsight działają tak, jakby hdfs były obecne i dlatego nie wymagają żadnych zmian w celu obsługi ich potrzeb magazynu. W Hadoop na HDInsight pamięć masowa jest zlecana na zewnątrz, ale przetwarzanie YARN pozostaje podstawowym składnikiem. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-introduction.md).

W tym artykule przedstawiono YARN i jak koordynuje wykonywanie aplikacji na HDInsight.

## <a name="apache-hadoop-yarn-basics"></a>Apache Hadoop Podstawy przędzy

YARN zarządza i organizuje przetwarzanie danych w Hadoop. YARN ma dwie podstawowe usługi, które działają jako procesy w węzłach w klastrze:

* ResourceManager
* Menedżer węzłów

ResourceManager udziela zasobów obliczeniowych klastra do aplikacji, takich jak Zadania MapReduce. ResourceManager udziela tych zasobów jako kontenery, gdzie każdy kontener składa się z alokacji rdzeni procesora CPU i pamięci RAM. Jeśli połączysz wszystkie zasoby dostępne w klastrze, a następnie rozprowadzasz rdzenie i pamięć w blokach, każdy blok zasobów jest kontenerem. Każdy węzeł w klastrze ma pojemność dla określonej liczby kontenerów, w związku z tym klaster ma stały limit liczby dostępnych kontenerów. Przydział zasobów w kontenerze jest konfigurowalny.

Gdy aplikacja MapReduce działa w klastrze, ResourceManager udostępnia aplikacji kontenery, w których do wykonania. ResourceManager śledzi stan uruchomionych aplikacji, dostępne pojemności klastra i śledzi aplikacje, jak zakończyć i zwolnić swoje zasoby.

ResourceManager uruchamia również proces serwera sieci web, który udostępnia interfejs użytkownika sieci web do monitorowania stanu aplikacji.

Gdy użytkownik przesyła mapreduce aplikacji do uruchomienia w klastrze, aplikacja jest przesyłana do ResourceManager. Z kolei ResourceManager przydziela kontenera w dostępnych węzłach NodeManager. NodeManager węzłów są, gdzie aplikacja faktycznie wykonuje. Pierwszy kontener przydzielony uruchamia specjalną aplikację o nazwie ApplicationMaster. Ten ApplicationMaster jest odpowiedzialny za pozyskiwanie zasobów, w postaci kolejnych kontenerów, potrzebnych do uruchomienia złożonego wniosku. ApplicationMaster sprawdza etapy aplikacji, takie jak etap mapy i zmniejszyć etap i czynniki, ile danych musi być przetwarzane. ApplicationMaster następnie żąda *(negocjuje)* zasoby z ResourceManager w imieniu aplikacji. ResourceManager z kolei udziela zasobów z NodeManagers w klastrze do ApplicationMaster dla niego do użycia w wykonywaniu aplikacji.

NodeManagers uruchomić zadania, które tworzą aplikację, a następnie zgłosić ich postęp i stan z powrotem do ApplicationMaster. ApplicationMaster z kolei zgłasza stan aplikacji z powrotem do ResourceManager. ResourceManager zwraca wszelkie wyniki do klienta.

## <a name="yarn-on-hdinsight"></a>YARN na HDInsight

Wszystkie typy klastrów HDInsight wdrażają YARN. ResourceManager jest wdrażany w celu zapewnienia wysokiej dostępności z wystąpieniem podstawowym i pomocniczym, które działa odpowiednio w pierwszym i drugim węzłze głównym w klastrze. Tylko jedno wystąpienie ResourceManager jest aktywny w czasie. Wystąpienia NodeManager są uruchamiane przez dostępne węzły procesu roboczego w klastrze.

![Apache YARN na platformie Azure HDInsight](./media/hdinsight-hadoop-architecture/apache-yarn-on-hdinsight.png)

## <a name="soft-delete"></a>Usuwanie nietrwałe

Aby cofnąć usunięcie pliku z konta magazynu, zobacz:

### <a name="azure-storage"></a>Azure Storage

* [Soft delete for Azure Storage blobs](../storage/blobs/storage-blob-soft-delete.md) (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)
* [Cofanie usunia obiektu blob](https://docs.microsoft.com/rest/api/storageservices/undelete-blob)

### <a name="azure-data-lake-storage-gen-1"></a>Usługa Azure Data Lake Storage Gen 1

[Przywracanie-AzDataLakeStoreDeletedItem](https://docs.microsoft.com/powershell/module/az.datalakestore/restore-azdatalakestoredeleteditem)

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

[Znane problemy z usługą Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-known-issues.md)

## <a name="trash-purging"></a>Czyszczenie śmieci

Właściwość `fs.trash.interval` z **witryny hdfs** > **advanced core-site** powinna pozostać na wartości domyślnej, `0` ponieważ nie należy przechowywać żadnych danych w lokalnym systemie plików. Ta wartość nie ma wpływu na konta magazynu zdalnego(WASB, ADLS GEN1, ABFS)

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z technologii MapReduce na platformie Apache Hadoop w usłudze HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-introduction.md)
