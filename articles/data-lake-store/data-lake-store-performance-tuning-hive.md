---
title: Usługa Azure Data Lake Storage Gen1 wydajności technologii Hive wytyczne dotyczące dostosowywania | Dokumentacja firmy Microsoft
description: Usługa Azure Data Lake Storage Gen1 wydajności technologii Hive wytyczne dotyczące dostosowywania
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
ms.openlocfilehash: 433c6b7d70cea9406b67d65e23cc357939cb5aa0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61437280"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Wskazówki dotyczące technologii Hive HDInsight i Azure Data Lake Storage Gen1 dostrajania wydajności

Domyślne ustawienia zostały ustawione, aby zapewnić dobrą wydajność w wielu różnych przypadków użycia.  Dla zapytań intensywnie korzystających z operacji We/Wy gałąź może być dostosowane do osiągnąć wyższą wydajność przy użyciu usługi Azure Data Lake Storage Gen1.  

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konta Data Lake Storage Gen1**. Aby uzyskać instrukcje na temat jej tworzenia, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Klaster usługi Azure HDInsight** dzięki dostępowi do konta Data Lake Storage Gen1. Zobacz [Tworzenie klastra HDInsight z usługą Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Upewnij się, że włączenie pulpitu zdalnego dla klastra.
* **Systemem Hive HDInsight**.  Aby dowiedzieć się więcej o uruchamianiu zadań Hive na HDInsight, zobacz [korzystanie z programu Hive na HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Wytyczne dotyczące Data Lake Storage Gen1 dostrajania wydajności**.  Pojęcia związane z ogólnej wydajności, zobacz [Data Lake Storage Gen1 dostrajania wskazówki dotyczące wydajności](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parametry

Poniżej przedstawiono najważniejsze ustawienia, należy dostosować w celu zwiększenia wydajności Data Lake Storage Gen1:

* **hive.tez.container.SIZE** — ilość pamięci używana przez każdego zadania

* **rozmiar tez.GROUPING.min** — minimalny rozmiar każdego mapowania

* **rozmiar tez.GROUPING.max** — maksymalny rozmiar każdego mapowania

* **hive.EXEC.reducer.Bytes.per.reducer** — rozmiar każdego reduktor

**hive.tez.container.SIZE** — rozmiar kontenera określa ilość pamięci dostępnej dla każdego zadania.  Jest to główny dane wejściowe na potrzeby kontroli współbieżności w gałęzi.  

**rozmiar tez.GROUPING.min** — ten parametr można ustawić minimalny rozmiar każdego mapowania.  Jeśli liczba liczby maperów, które wybierze Tez jest mniejsza niż wartość tego parametru, Tez użyje wartość ustawiona w tym miejscu.

**rozmiar tez.GROUPING.max** — parametr można ustawić maksymalny rozmiar każdego mapowania.  Jeśli liczba liczby maperów, które wybierze Tez jest większy niż wartość tego parametru, Tez użyje wartość ustawiona w tym miejscu.

**hive.EXEC.reducer.Bytes.per.reducer** — ten parametr określa rozmiar każdego reduktor.  Domyślnie każdy reduktor to 256MB.  

## <a name="guidance"></a>Wskazówki

**Ustaw hive.exec.reducer.bytes.per.reducer** — wartość domyślna działa dobrze, gdy jest bez kompresji danych.  Dla danych, które jest skompresowany należy zmniejszyć rozmiar reduktor.  

**Ustaw hive.tez.container.size** — w każdym węźle jest określona przez yarn.nodemanager.resource.memory mb pamięci i należy poprawnie ustawić na klaster usługi HDI domyślnie.  Aby uzyskać dodatkowe informacje na temat ustawiania odpowiedniej ilości pamięci w YARN, zobacz [wpis](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

Obciążeń z intensywnym wykorzystaniem we/wy może korzystać z równoległości więcej przez zmniejszenie rozmiaru kontenera w aplikacji Tez. Dzięki temu użytkownik większej liczbie kontenerów, co zwiększa współbieżności.  Jednak niektóre zapytania programu Hive wymagać znacznej ilości pamięci (np. MapJoin).  Jeśli zadanie nie ma wystarczającej ilości pamięci, wystąpi wyjątek braku pamięci podczas wykonywania.  Jeśli pojawi się poza wyjątki pamięci, należy zwiększyć ilość pamięci.   

Numer równoczesnych zadań uruchomionych lub równoległości będzie ograniczone przez całkowitej ilości pamięci usługi YARN.  Liczba kontenerów YARN określają, jak wiele współbieżnych zadań można uruchamiać.  Aby znaleźć pamięci usługi YARN w każdym węźle, możesz przejść do narzędzia Ambari.  Przejdź do usługi YARN i wyświetlić kartę konfiguracje.  W tym oknie wyświetlane jest pamięci usługi YARN.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
Klawisz w celu poprawy wydajności przy użyciu usług Data Lake Storage Gen1 jest zwiększyć współbieżność, o ile to możliwe.  Tez automatycznie oblicza liczbę zadań, które mają zostać utworzone, więc nie trzeba ustawić go.   

## <a name="example-calculation"></a>Przykład obliczania

Załóżmy, że masz klaster D14 8 node.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>Ograniczenia

**Data Lake Storage Gen1 ograniczania** 

Jeśli napotkasz limity przepustowości, dostarczone przez Data Lake Storage Gen1, rozpocząć się niepowodzeń zadań. Może to być identyfikowany przez monitorowanie błędów ograniczania dostępności w dziennikach zadania.  Aby zmniejszyć równoległości, zwiększenie rozmiaru kontenera w aplikacji Tez.  Jeśli potrzebujesz uzyskać większą współbieżność dla zadania, skontaktuj się z nami.

Aby sprawdzić, jeśli użytkownik jest ograniczany, musisz włączyć debugowanie rejestrowania po stronie klienta. Poniżej przedstawiono, jak można to zrobić:

1. Umieść następującą właściwość we właściwościach log4j w konfiguracji programu Hive. Można to zrobić w widoku Ambari: log4j.logger.com.microsoft.azure.datalake.store=DEBUG ponownie uruchomić usługę wszystkie węzły/konfiguracji zostały wprowadzone.

2. Jeśli użytkownik jest ograniczany, zobaczysz kod błędu HTTP 429 w pliku dziennika programu hive. Plik dziennika programu hive jest /tmp/&lt;użytkownika&gt;/hive.log

## <a name="further-information-on-hive-tuning"></a>Dodatkowe informacje o dostosowywaniu programu Hive

Poniżej przedstawiono kilka blogi, pomagających dostrajanie zapytań Hive:
* [Optymalizowanie zapytań technologii Hive dla usługi Hadoop w HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Rozwiązywanie problemów z wydajnością zapytań Hive](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Rozmowa na konferencji Ignite na optymalizowanie gałęzi na HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
