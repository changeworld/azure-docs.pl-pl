---
title: 'Dostrajanie wydajności: MapReduce, HDInsight & Azure Data Lake Storage Gen2 | Dokumenty firmy Microsoft'
description: Wskazówki dotyczące dostrajania wydajności usługi Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: a3ea6858355d6cb921f629bf36134d96371f6244
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327929"
---
# <a name="tune-performance-mapreduce-hdinsight--azure-data-lake-storage-gen2"></a>Dostrajanie wydajności: MapReduce, HDInsight & Azure Data Lake Storage Gen2

Zrozumienie czynników, które należy wziąć pod uwagę podczas dostrojenia wydajności zadań Redukcji mapy. W tym artykule omówiono szereg wskazówek dotyczących dostrajania wydajności.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Storage Gen2**. Aby uzyskać instrukcje dotyczące tworzenia jednego z nich, zobacz [Szybki start: Tworzenie konta magazynu usługi Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).
* **Klaster usługi Azure HDInsight** z dostępem do konta Data Lake Storage Gen2. Zobacz [Korzystanie z usługi Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **Korzystanie MapReduce na HDInsight**.  Aby uzyskać więcej informacji, zobacz [Korzystanie z mapReduce w hadoop na HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Wskazówki dotyczące dostrajania wydajności w programie Data Lake Storage Gen2**.  Aby zapoznać się z ogólnymi pojęciami dotyczącymi wydajności, zobacz [Wskazówki dotyczące dostrajania wydajności magazynu w ułowia](data-lake-storage-performance-tuning-guidance.md) w u źródła danych

## <a name="parameters"></a>Parametry

Podczas uruchamiania zadań MapReduce, oto parametry, które można skonfigurować w celu zwiększenia wydajności w programie Data Lake Storage Gen2:

* **Mapreduce.map.memory.mb** — ilość pamięci do przydzielenia każdemu maperowi
* **Mapreduce.job.maps** — liczba zadań mapy na zadanie
* **Mapreduce.reduce.memory.mb** — ilość pamięci do przydzielenia każdemu reduktorowi
* **Mapreduce.job.reduces** — liczba zmniejszyć liczbę zadań na zadanie

**Mapreduce.map.memory / Mapreduce.reduce.memory** Liczba ta powinna być dostosowana w zależności od ilości pamięci potrzebnej do mapy i/lub zmniejszenia zadania.  Domyślne wartości mapreduce.map.memory i mapreduce.reduce.memory można przeglądać w Ambari za pośrednictwem konfiguracji przędzy.  W Ambari przejdź do YARN i wyświetl kartę Konfiguracje.  Zostanie wyświetlona pamięć YARN.  

**Mapreduce.job.maps / Mapreduce.job.reduces** Określi to maksymalną liczbę maperów lub reduktorów, które mają zostać utworzone.  Liczba podziałów określi, ile maperów zostanie utworzonych dla zadania MapReduce.  W związku z tym można uzyskać mniej maperów niż wymagane, jeśli istnieje mniej podziałów niż liczba mapperów wymagane.       

## <a name="guidance"></a>Wskazówki

> [!NOTE]
> Wskazówki w tym dokumencie przyjęto założenie, że aplikacja jest jedyną aplikacją działającą w klastrze.

**Krok 1: Określ liczbę uruchomionych zadań**

Domyślnie MapReduce użyje całego klastra dla zadania.  Można użyć mniej klastra przy użyciu mniej maperów niż istnieją dostępne kontenery.        

**Krok 2: Ustaw mapreduce.map.memory/mapreduce.reduce.memory**

Rozmiar pamięci dla mapy i zmniejszyć zadania będą zależeć od konkretnego zadania.  Można zmniejszyć rozmiar pamięci, jeśli chcesz zwiększyć współbieżność.  Liczba jednocześnie uruchomionych zadań zależy od liczby kontenerów.  Zmniejszając ilość pamięci na mapera lub reduktora, można utworzyć więcej kontenerów, które umożliwiają uruchamianie równocześnie większej liczby maperów lub reduktorów.  Zmniejszenie ilości pamięci zbyt dużo może spowodować, że niektóre procesy zabraknie pamięci.  Jeśli podczas uruchamiania zadania pojawia się błąd sterty, należy zwiększyć pamięć na mapera lub reduktora.  Należy wziąć pod uwagę, że dodanie większej liczby kontenerów spowoduje dodanie dodatkowych nakładów dla każdego dodatkowego kontenera, co może potencjalnie obniżyć wydajność.  Inną alternatywą jest uzyskanie większej ilości pamięci przy użyciu klastra, który ma większe ilości pamięci lub zwiększenie liczby węzłów w klastrze.  Więcej pamięci umożliwi więcej kontenerów do użycia, co oznacza większą współbieżność.  

**Krok 3: Określ całkowitą pamięć PRZĘDZY**

Aby dostroić mapreduce.job.maps/mapreduce.job.reduces, należy wziąć pod uwagę ilość całkowitej pamięci YARN dostępnej do użycia.  Informacje te są dostępne w ambari.  Przejdź do pozycji YARN i wyświetl kartę Konfiguracje.  Pamięć YARN jest wyświetlana w tym oknie.  Należy pomnożyć pamięć YARN z liczbą węzłów w klastrze, aby uzyskać całkowitą pamięć YARN.

    Total YARN memory = nodes * YARN memory per node

Jeśli używasz pustego klastra, pamięć może być całkowita pamięć YARN dla klastra.  Jeśli inne aplikacje używają pamięci, można wybrać użycie tylko części pamięci klastra, zmniejszając liczbę maperów lub reduktorów do liczby kontenerów, których chcesz użyć.  

**Krok 4: Oblicz liczbę pojemników YARN**

Kontenery PRZĘDZy dyktują ilość współbieżności dostępnej dla zadania.  Weź całkowitą pamięć YARN i podziel ją przez mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Krok 5: Ustaw mapreduce.job.maps/mapreduce.job.reduces**

Ustaw mapreduce.job.maps/mapreduce.job.reduces do co najmniej liczby dostępnych kontenerów.  Możesz eksperymentować dalej, zwiększając liczbę maperów i reduktorów, aby sprawdzić, czy uzyskasz lepszą wydajność.  Należy pamiętać, że więcej maperów będzie miało dodatkowe obciążenie, więc posiadanie zbyt wielu maperów może obniżyć wydajność.  

Planowanie procesora CPU i izolacja procesora CPU są domyślnie wyłączone, więc liczba kontenerów YARN jest ograniczona przez pamięć.

## <a name="example-calculation"></a>Przykładowe obliczenie

Załóżmy, że mamy klaster składający się z 8 węzłów D14 i chcemy uruchomić zadanie intensywnie we/wy.  Oto obliczenia, które powinieneś wykonać:

**Krok 1: Określ liczbę uruchomionych zadań**

W tym przykładzie załóżmy, że nasze zadanie jest jedynym zadaniem, które jest uruchomione.  

**Krok 2: Ustaw mapreduce.map.memory/mapreduce.reduce.memory**

W tym przykładzie uruchamiamy zadanie intensywnie we/wy i decydujemy, że 3 GB pamięci dla zadań mapy będzie wystarczające.

    mapreduce.map.memory = 3GB

**Krok 3: Określ całkowitą pamięć PRZĘDZY**

    Total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Krok 4: Oblicz numer pojemników YARN**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Krok 5: Ustaw mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>Przykłady do uruchomienia

Aby zademonstrować, jak MapReduce działa w u źródła danych Lake Storage Gen2, poniżej znajduje się przykładowy kod, który został uruchomiony w klastrze z następującymi ustawieniami:

* 16 węzeł D14v2
* Klaster Hadoop z hdi 3.6

Dla punktu wyjścia, oto kilka przykładowych poleceń do uruchamiania MapReduce Teragen, Terasort i Teravalidate.  Można dostosować te polecenia na podstawie zasobów.

**Teragen ( Teragen )**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input

**Okręg wyborczy Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output

**Teravalidate (terawalidate)**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate
