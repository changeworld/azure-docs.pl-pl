---
title: Wskazówki dotyczące dostrajania wydajności hive usługi Azure Data Lake Storage Gene | Dokumenty firmy Microsoft
description: Wskazówki dotyczące dostrajania wydajności gałęzi usługi Azure Data Lake Storage Gene
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61437280"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Wskazówki dotyczące dostrajania wydajności dla gałęzi w usłudze HDInsight i usłudze Azure Data Lake Storage Gen1

Ustawienia domyślne zostały ustawione w celu zapewnienia dobrej wydajności w wielu różnych przypadkach użycia.  W przypadku zapytań intensywnie korzystających z we/wy gałęzi można dostroić, aby uzyskać lepszą wydajność za pomocą usługi Azure Data Lake Storage Gen1.  

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto Data Lake Storage Gen1**. Aby uzyskać instrukcje dotyczące tworzenia jednego z nich, zobacz Wprowadzenie do [usługi Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Klaster usługi Azure HDInsight** z dostępem do konta Usługi Data Lake Storage Gen1. Zobacz [Tworzenie klastra HDInsight z pamięcią Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Upewnij się, że włączysz pulpit zdalny dla klastra.
* **Uruchamianie gałęzi na hdinsight**.  Aby dowiedzieć się więcej o uruchamianiu zadań gałęzi w programie HDInsight, zobacz [Korzystanie z funkcji Gałąź w programie HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Wskazówki dotyczące dostrajania wydajności w programie Data Lake Storage Gen1**.  Aby zapoznać się z ogólnymi pojęciami dotyczącymi wydajności, zobacz [Wskazówki dotyczące dostrajania wydajności magazynu danych Lake Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parametry

Oto najważniejsze ustawienia, aby dostroić się do poprawy wydajności pamięci masowej w ułowiowej:

* **hive.tez.container.size** – ilość pamięci używanej przez każde zadanie

* **tez.grouping.min-size** – minimalny rozmiar każdego mapera

* **tez.grouping.max-size** – maksymalny rozmiar każdego mapera

* **hive.exec.reduktor.bytes.per.reducer** – rozmiar każdego reduktora

**hive.tez.container.size** — rozmiar kontenera określa, ile pamięci jest dostępne dla każdego zadania.  Jest to główne dane wejściowe do kontrolowania współbieżności w gałęzi.  

**tez.grouping.min-size** — ten parametr umożliwia ustawienie minimalnego rozmiaru każdego mapera.  Jeśli liczba maperów, które wybierze Tez jest mniejsza niż wartość tego parametru, Tez użyje wartości ustawionej w tym miejscu.

**tez.grouping.max-size** — parametr umożliwia ustawienie maksymalnego rozmiaru każdego mapera.  Jeśli liczba maperów, które wybierze Tez jest większa niż wartość tego parametru, Tez użyje wartości ustawionej w tym miejscu.

**hive.exec.reducer.bytes.per.reducer** — ten parametr ustawia rozmiar każdego reduktora.  Domyślnie każdy reduktor ma 256 MB.  

## <a name="guidance"></a>Wskazówki

**Ustaw hive.exec.reduktor.bytes.per.reducer** — wartość domyślna działa dobrze, gdy dane są nieskompresowane.  W przypadku danych, które są skompresowane, należy zmniejszyć rozmiar reduktora.  

**Ustaw hive.tez.container.size** — w każdym węźle pamięć jest określona przez yarn.nodemanager.resource.memory-mb i powinna być domyślnie poprawnie ustawiona w klastrze HDI.  Aby uzyskać dodatkowe informacje na temat ustawiania odpowiedniej pamięci w YARN, zobacz ten [post](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

Obciążenia intensywnie korzystające z operacji we/wy mogą korzystać z większej równoległości, zmniejszając rozmiar kontenera Tez. Daje to użytkownikowi więcej kontenerów, co zwiększa współbieżność.  Jednak niektóre zapytania hive wymagają znacznej ilości pamięci (np. MapJoin).  Jeśli zadanie nie ma wystarczającej ilości pamięci, otrzymasz wyjątek braku pamięci podczas wykonywania.  Jeśli otrzymasz wyjątki poza pamięcią, należy zwiększyć pamięć.   

Równoczesna liczba uruchomionych lub równoległości będzie ograniczona całkowitą pamięcią YARN.  Liczba kontenerów YARN będzie dyktować, ile równoczesnych zadań można uruchomić.  Aby znaleźć pamięć YARN na węzeł, można przejść do Ambari.  Przejdź do pozycji YARN i wyświetl kartę Konfiguracje.  Pamięć YARN jest wyświetlana w tym oknie.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
Kluczem do poprawy wydajności przy użyciu usługi Data Lake Storage Gen1 jest zwiększenie współbieżności w miarę możliwości.  Tez automatycznie oblicza liczbę zadań, które powinny zostać utworzone, więc nie trzeba go ustawiać.   

## <a name="example-calculation"></a>Przykładowe obliczenie

Załóżmy, że masz klaster 8 węzłów D14.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>Ograniczenia

**Ograniczanie przepustowości pamięci masowej w jeziorze danych Gen1** 

Jeśli osiągniesz limity przepustowości zapewniane przez program Data Lake Storage Gen1, zaczniesz widzieć błędy zadań. Można to zidentyfikować, obserwując błędy ograniczania przepustowości w dziennikach zadań.  Równoległość można zmniejszyć, zwiększając rozmiar kontenera Tez.  Jeśli potrzebujesz więcej współbieżności do swojej pracy, skontaktuj się z nami.

Aby sprawdzić, czy są coraz ograniczone, należy włączyć rejestrowanie debugowania po stronie klienta. Oto jak możesz to zrobić:

1. Umieść następującą właściwość we właściwościach log4j w uli config. Można to zrobić za pomocą widoku Ambari: log4j.logger.com.microsoft.azure.datalake.store=DEBUG Uruchom ponownie wszystkie węzły/usługę, aby config został uruchomiony.

2. Jeśli zostaniesz ograniczony, zobaczysz kod błędu HTTP 429 w pliku dziennika gałęzi. Plik dziennika gałęzi znajduje się w&lt;&gt;pliku /tmp/ user /hive.log

## <a name="further-information-on-hive-tuning"></a>Więcej informacji na temat strojenia hive

Oto kilka blogów, które pomogą dostroić twoje zapytania hive:
* [Optymalizacja zapytań gałęzi dla hadoop w hdinsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Rozwiązywanie problemów z wydajnością zapytań gałęzi](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Zapalić mówić na optymalizację Hive na HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
