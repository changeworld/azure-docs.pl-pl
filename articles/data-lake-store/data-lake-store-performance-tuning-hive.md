---
title: Dostosowywanie wskazówki dotyczące wydajności Hive usługi Azure Data Lake Store | Dokumentacja firmy Microsoft
description: Dostosowywanie wskazówki dotyczące wydajności Hive usługi Azure Data Lake Store
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: c46eb1b2da62d70337e60066ed0706c3a4fdedcf
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-store"></a>Wskazówki dotyczące Hive w usłudze HDInsight i usługi Azure Data Lake Store dostrajania wydajności

Aby zapewnić dobrą wydajność w wielu innych przypadków użycia skonfigurowane ustawienia domyślne.  Dla operacji We/Wy znacznym zapytań Aby uzyskać lepszą wydajność ADLS można przedstawić Hive.  

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Store**. Aby uzyskać instrukcje na temat go utworzyć, zobacz [wprowadzenie do usługi Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Klaster HDInsight Azure** z dostępem do konta usługi Data Lake Store. Zobacz [tworzenia klastra usługi HDInsight z usługą Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Upewnij się, że włączenie pulpitu zdalnego dla klastra.
* **Uruchomiona Hive w usłudze HDInsight**.  Aby dowiedzieć się więcej na temat uruchamiania zadań Hive w usłudze HDInsight, zobacz () [używanie Hive w usłudze HDInsight]https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Wytyczne dotyczące ADLS dostrajania wydajności**.  Pojęcia dotyczące ogólnej wydajności, aby zapoznać [Data Lake magazynu dostrajanie wytyczne dotyczące wydajności](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parametry

Poniżej przedstawiono najważniejsze ustawienia, aby dostroić zwiększonej wydajności ADLS:

* **hive.tez.container.SIZE** — ilość pamięci użytej przez każdego zadania

* **rozmiar tez.GROUPING.min** — minimalny rozmiar każdego mapowania

* **rozmiar tez.GROUPING.max** — maksymalny rozmiar każdego mapowania

* **hive.EXEC.reducer.Bytes.per.reducer** — rozmiar każdego reduktor

**hive.tez.container.SIZE** — rozmiar kontenera Określa, ile pamięci dostępnej dla każdego zadania.  Jest to główny danych wejściowych do kontrolowania współbieżność w gałęzi.  

**rozmiar tez.GROUPING.min** — ten parametr umożliwia określenie minimalny rozmiar każdego mapowania.  Jeśli liczba mapowań, które wybierze Tez jest mniejsza niż wartość tego parametru, Tez użyje wartość ustawiona w tym miejscu.

**rozmiar tez.GROUPING.max** — parametr można ustawić maksymalny rozmiar każdego mapowania.  Jeśli liczba mapowań, które wybierze Tez jest większa niż wartość tego parametru, Tez użyje wartość ustawiona w tym miejscu.

**hive.EXEC.reducer.Bytes.per.reducer** — ten parametr określa rozmiar każdego reduktor.  Domyślnie każdy reduktor to 256MB.  

## <a name="guidance"></a>Wskazówki

**Ustaw hive.exec.reducer.bytes.per.reducer** — wartość domyślna działa dobrze, gdy jest nieskompresowanych danych.  Dane są kompresowane należy zmniejszyć rozmiar reduktor.  

**Ustaw hive.tez.container.size** — w każdym węźle jest określona przez yarn.nodemanager.resource.memory mb pamięci i powinien zostać poprawnie określony HDI klastra domyślnie.  Aby uzyskać dodatkowe informacje na temat ustawiania odpowiedniej ilości pamięci w YARN, zobacz [post](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

Intensywnych obciążeń We/Wy mogą korzystać z równoległości więcej przy zmniejszaniu rozmiaru kontenera aplikacji Tez. Dzięki temu użytkownik więcej kontenerów, co zwiększa współbieżności.  Jednak niektóre zapytania Hive wymagają znaczną ilość pamięci (np. MapJoin).  Jeśli zadanie nie ma wystarczającej ilości pamięci, wystąpi wyjątek braku pamięci w czasie wykonywania.  Jeśli zostanie wyświetlony poza wyjątkami pamięci, należy zwiększyć ilość pamięci.   

Współbieżnych liczba zadań uruchomionych lub równoległości zostaną ograniczone przez pamięć YARN.  Liczba kontenerów YARN wyznaczają liczbę równoczesnych zadań można uruchamiać.  Aby znaleźć pamięci YARN w każdym węźle, można przejść do narzędzia Ambari.  Przejdź do YARN, a następnie Wyświetl kartę Configs.  Pamięć YARN jest wyświetlany w tym oknie.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
W celu poprawy wydajności przy użyciu ADLS należy zwiększyć współbieżność możliwie.  Tez automatycznie oblicza liczbę zadań, które powinny być tworzone, dzięki czemu nie trzeba go ustawić.   

## <a name="example-calculation"></a>Przykład obliczeń

Załóżmy, że klaster D14 8 węzłów.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>Ograniczenia

**ADLS ograniczania przepustowości** 

UIf osiągnął limity przepustowości udostępniane przez ADLS, zacząć Zobacz niepowodzeń zadań. Może to być identyfikowany przez obserwowania błędy ograniczania przepustowości w dziennikach zadań.  Aby zmniejszyć równoległości, zwiększyć rozmiar kontenera aplikacji Tez.  Jeśli potrzebujesz więcej współbieżności dla zadania, skontaktuj się z nami.

Aby sprawdzić, jeśli użytkownik są pobierania ograniczane, musisz włączyć rejestrowania po stronie klienta debugowania. Oto, jak można to zrobić:

1. Umieść następującą właściwość w właściwości log4j w pliku konfiguracyjnym Hive. Można to zrobić z widoku Ambari: log4j.logger.com.microsoft.azure.datalake.store=DEBUG ponownie uruchomić usługę wszystkie węzły/konfiguracji zaczęły obowiązywać.

2. Jeśli możesz są pobierania ograniczane, zobaczysz kod błędu HTTP 429 w pliku dziennika gałęzi. Gałąź plik dziennika znajduje się w /tmp/&lt;użytkownika&gt;/hive.log

## <a name="further-information-on-hive-tuning"></a>Więcej informacji na temat dostrajania gałęzi

Poniżej przedstawiono kilka blogi, pomagających dostroić zapytań Hive:
* [Optymalizacja zapytań programu Hive dla platformy Hadoop w usłudze HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Rozwiązywanie problemów z wydajność zapytań Hive](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Rozmawiać z konferencji Ignite na optymalizowanie Hive w usłudze HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
