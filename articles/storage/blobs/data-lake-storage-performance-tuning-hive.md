---
title: 'Dostrajanie wydajności: Hive, HDInsight & Azure Data Lake Storage Gen2 | Dokumenty firmy Microsoft'
description: Wskazówki dotyczące dostrajania gałęzi usługi Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 66042568cede364c16302fbd85751de4113bbe0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327583"
---
# <a name="tune-performance-hive-hdinsight--azure-data-lake-storage-gen2"></a>Dostrajanie wydajności: Gałąź, usługa HDInsight & usługa Azure Data Lake Storage Gen2

Ustawienia domyślne zostały ustawione w celu zapewnienia dobrej wydajności w wielu różnych przypadkach użycia.  W przypadku zapytań intensywnie korzystających z we/wy gałęzi można dostroić, aby uzyskać lepszą wydajność za pomocą usługi Azure Data Lake Storage Gen2.  

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto Data Lake Storage Gen2**. Aby uzyskać instrukcje dotyczące tworzenia jednego z nich, zobacz [Szybki start: tworzenie konta magazynu usługi Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md)
* **Klaster usługi Azure HDInsight** z dostępem do konta Data Lake Storage Gen2. Zobacz [Korzystanie z usługi Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **Uruchamianie gałęzi na hdinsight**.  Aby dowiedzieć się więcej o uruchamianiu zadań gałęzi w programie HDInsight, zobacz [Korzystanie z funkcji Gałąź w programie HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Wskazówki dotyczące dostrajania wydajności w programie Data Lake Storage Gen2**.  Aby zapoznać się z ogólnymi pojęciami dotyczącymi wydajności, zobacz [Wskazówki dotyczące dostrajania wydajności magazynu w ułowia](data-lake-storage-performance-tuning-guidance.md) w u źródła danych

## <a name="parameters"></a>Parametry

Oto najważniejsze ustawienia, aby dostroić się do poprawy wydajności data lake storage gen2:

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
Kluczem do poprawy wydajności przy użyciu usługi Data Lake Storage Gen2 jest zwiększenie współbieżności w miarę możliwości.  Tez automatycznie oblicza liczbę zadań, które powinny zostać utworzone, więc nie trzeba go ustawiać.   

## <a name="example-calculation"></a>Przykładowe obliczenie

Załóżmy, że masz klaster 8 węzłów D14.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="further-information-on-hive-tuning"></a>Więcej informacji na temat strojenia hive

Oto kilka blogów, które pomogą dostroić twoje zapytania hive:
* [Optymalizacja zapytań gałęzi dla hadoop w hdinsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Rozwiązywanie problemów z wydajnością zapytań gałęzi](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Zapalić mówić na optymalizację Hive na HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
