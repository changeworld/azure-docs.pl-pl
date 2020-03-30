---
title: Dostrajanie wydajności usługi Azure Data Lake Storage Gen1 — MapReduce
description: Wskazówki dotyczące dostrajania wydajności usługi Azure Data Lake Storage Gen1
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: a645049665bc1d51efa94a879b9d2e4e5529282f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904587"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Wskazówki dotyczące dostrajania wydajności mapReduce w usłudze HDInsight i usłudze Azure Data Lake Storage Gen1

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Storage Gen1**. Aby uzyskać instrukcje dotyczące tworzenia jednego z nich, zobacz Wprowadzenie do [usługi Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Klaster usługi Azure HDInsight** z dostępem do konta Usługi Data Lake Storage Gen1. Zobacz [Tworzenie klastra HDInsight z pamięcią Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Upewnij się, że włączysz pulpit zdalny dla klastra.
* **Korzystanie MapReduce na HDInsight**. Aby uzyskać więcej informacji, zobacz [Korzystanie z mapReduce w hadoop na HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Przejrzyj wskazówki dotyczące dostrajania wydajności dla usługi Data Lake Storage Gen1**. Aby zapoznać się z ogólnymi pojęciami dotyczącymi wydajności, zobacz [Wskazówki dotyczące dostrajania wydajności magazynu danych Lake Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parametry

Podczas uruchamiania zadań MapReduce, oto najważniejsze parametry, które można skonfigurować w celu zwiększenia wydajności w programie Data Lake Storage Gen1:

|Parametr      | Opis  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  Ilość pamięci do przydzielenia do każdego mapera.  |
|`Mapreduce.job.maps`     |  Liczba zadań mapy na zadanie.  |
|`Mapreduce.reduce.memory.mb`     |  Ilość pamięci do przydzielenia do każdego reduktora.  |
|`Mapreduce.job.reduces`    |   Liczba zmniejszyć zadania na zadanie.  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>Mapreduce.map.memory / Mapreduce.reduce.memory

Dostosuj tę liczbę do liczby w zależności od ilości pamięci potrzebnej do mapy i/lub zmniejszenia zadania. Wartości domyślne `mapreduce.map.memory` i `mapreduce.reduce.memory` w Ambari można wyświetlić za pomocą konfiguracji Przędza. W Ambari przejdź do YARN i wyświetl kartę **Konfiguracje.** Zostanie wyświetlona pamięć YARN.

### <a name="mapreducejobmaps--mapreducejobreduces"></a>Mapreduce.job.maps / Mapreduce.job.reduces

Określa maksymalną liczbę maperów lub reduktorów do utworzenia. Liczba podziałów określa, ile maperów są tworzone dla zadania MapReduce. W związku z tym można uzyskać mniej maperów niż wymagane, jeśli istnieje mniej podziałów niż liczba mapperów wymagane.

## <a name="guidance"></a>Wskazówki

### <a name="step-1-determine-number-of-jobs-running"></a>Krok 1: Określ liczbę uruchomionych zadań

Domyślnie MapReduce użyje całego klastra dla zadania. Można użyć mniej klastra przy użyciu mniej maperów niż są dostępne kontenery. Wskazówki w tym dokumencie przyjęto założenie, że aplikacja jest jedyną aplikacją działającą w klastrze.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Krok 2: Ustaw mapreduce.map.memory/mapreduce.reduce.memory

Rozmiar pamięci dla mapy i zmniejszyć zadania będą zależeć od konkretnego zadania. Można zmniejszyć rozmiar pamięci, jeśli chcesz zwiększyć współbieżność. Liczba jednocześnie uruchomionych zadań zależy od liczby kontenerów. Zmniejszając ilość pamięci na mapera lub reduktora, można utworzyć więcej kontenerów, które umożliwiają uruchamianie równocześnie większej liczby maperów lub reduktorów. Zmniejszenie ilości pamięci zbyt dużo może spowodować, że niektóre procesy zabraknie pamięci. Jeśli podczas uruchamiania zadania pojawia się błąd sterty, zwiększ ilość pamięci na mapera lub reduktora. Należy wziąć pod uwagę, że dodanie większej liczby kontenerów dodaje dodatkowe obciążenie dla każdego dodatkowego kontenera, co może potencjalnie obniżyć wydajność. Inną alternatywą jest uzyskanie większej ilości pamięci przy użyciu klastra, który ma większe ilości pamięci lub zwiększenie liczby węzłów w klastrze. Więcej pamięci umożliwi więcej kontenerów do użycia, co oznacza większą współbieżność.

### <a name="step-3-determine-total-yarn-memory"></a>Krok 3: Określenie całkowitej pamięci PRZĘDZy

Aby dostroić mapreduce.job.maps/mapreduce.job.reduces, należy wziąć pod uwagę ilość całkowitej pamięci YARN dostępne do użycia. Informacje te są dostępne w ambari. Przejdź do pozycji YARN i wyświetl kartę **Konfiguracje.** Pamięć YARN jest wyświetlana w tym oknie. Pomnóż pamięć YARN z liczbą węzłów w klastrze, aby uzyskać całkowitą pamięć YARN.

`Total YARN memory = nodes * YARN memory per node`

Jeśli używasz pustego klastra, pamięć może być całkowita pamięć YARN dla klastra. Jeśli inne aplikacje używają pamięci, można wybrać użycie tylko części pamięci klastra, zmniejszając liczbę maperów lub reduktorów do liczby kontenerów, których chcesz użyć.

### <a name="step-4-calculate-number-of-yarn-containers"></a>Krok 4: Oblicz liczbę pojemników YARN

Kontenery PRZĘDZy dyktują ilość współbieżności dostępnej dla zadania. Weź całkowitą pamięć YARN i podziel ją przez mapreduce.map.memory.

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Krok 5: Ustaw mapreduce.job.maps/mapreduce.job.reduces

Ustaw mapreduce.job.maps/mapreduce.job.reduces do co najmniej liczby dostępnych kontenerów. Możesz eksperymentować dalej, zwiększając liczbę maperów i reduktorów, aby sprawdzić, czy uzyskasz lepszą wydajność. Należy pamiętać, że więcej maperów będzie miało dodatkowe obciążenie, więc posiadanie zbyt wielu maperów może obniżyć wydajność.

Planowanie procesora CPU i izolacja procesora CPU są domyślnie wyłączone, więc liczba kontenerów YARN jest ograniczona przez pamięć.

## <a name="example-calculation"></a>Przykładowe obliczenie

Załóżmy, że masz obecnie klaster składający się z 8 węzłów D14 i chcesz uruchomić zadanie intensywnie we/wy. Oto obliczenia, które powinieneś wykonać:

### <a name="step-1-determine-number-of-jobs-running"></a>Krok 1: Określ liczbę uruchomionych zadań

W naszym przykładzie zakładamy, że nasza praca jest jedyną działającą.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Krok 2: Ustaw mapreduce.map.memory/mapreduce.reduce.memory

W naszym przykładzie uruchamiasz zadanie intensywnie we/wy i decydujesz, że 3 GB pamięci dla zadań mapy jest wystarczające.

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>Krok 3: Określenie całkowitej pamięci PRZĘDZy

`total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB`

### <a name="step-4-calculate--of-yarn-containers"></a>Krok 4: Oblicz numer pojemników YARN

`# of YARN containers = 768 GB of available memory / 3 GB of memory = 256`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Krok 5: Ustaw mapreduce.job.maps/mapreduce.job.reduces

`mapreduce.map.jobs = 256`

## <a name="limitations"></a>Ograniczenia

**Ograniczanie przepustowości pamięci masowej w jeziorze danych Gen1**

Jako usługa wielodostępna usługa Data Lake Storage Gen1 ustawia limity przepustowości na poziomie konta. Jeśli trafisz te limity, zaczniesz widzieć błędy zadań. Można to zidentyfikować, obserwując błędy ograniczania przepustowości w dziennikach zadań. Jeśli potrzebujesz większej przepustowości do swojej pracy, skontaktuj się z nami.

Aby sprawdzić, czy są coraz ograniczone, należy włączyć rejestrowanie debugowania po stronie klienta. Oto jak możesz to zrobić:

1. Umieść następującą właściwość we właściwościach log4j w ambari > YARN > Config > Advanced yarn-log4j: log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Uruchom ponownie wszystkie węzły/usługę, aby config został uruchomiony.

3. Jeśli zostaniesz ograniczony, zobaczysz kod błędu HTTP 429 w pliku dziennika YARN. Plik dziennika YARN znajduje się&lt;w&gt;/tmp/ user /yarn.log

## <a name="examples-to-run"></a>Przykłady do uruchomienia

Aby zademonstrować, jak MapReduce działa w u źródła danych Lake Storage Gen1, poniżej przedstawiono przykładowy kod, który został uruchomiony w klastrze z następującymi ustawieniami:

* 16 węzeł D14v2
* Klaster Hadoop z hdi 3.6

Dla punktu wyjścia, oto kilka przykładowych poleceń do uruchamiania MapReduce Teragen, Terasort i Teravalidate. Można dostosować te polecenia na podstawie zasobów.

### <a name="teragen"></a>Teragen ( Teragen )

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input
```

### <a name="terasort"></a>Okręg wyborczy Terasort

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output
```

### <a name="teravalidate"></a>Teravalidate (terawalidate)

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
```
