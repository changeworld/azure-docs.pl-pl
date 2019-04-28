---
title: Wytyczne dotyczące dostosowywania usługi Azure Data Lake Storage Gen1 MapReduce wydajności | Dokumentacja firmy Microsoft
description: Wytyczne dotyczące dostosowywania usługi Azure Data Lake Storage Gen1 MapReduce wydajności
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
ms.openlocfilehash: b9e5d034db4711384d2ac8a1083da5c93ea11900
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437246"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Wskazówki dotyczące technologii MapReduce, HDInsight i Azure Data Lake Storage Gen1 dostrajania wydajności

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Storage Gen1**. Aby uzyskać instrukcje na temat jej tworzenia, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Klaster usługi Azure HDInsight** dzięki dostępowi do konta Data Lake Storage Gen1. Zobacz [Tworzenie klastra HDInsight z usługą Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Upewnij się, że włączenie pulpitu zdalnego dla klastra.
* **Przy użyciu technologii MapReduce w HDInsight**.  Aby uzyskać więcej informacji, zobacz [używanie MapReduce na platformie Hadoop w HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Wytyczne dotyczące Data Lake Storage Gen1 dostrajania wydajności**.  Pojęcia związane z ogólnej wydajności, zobacz [Data Lake Storage Gen1 dostrajania wskazówki dotyczące wydajności](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parametry

Podczas uruchamiania zadania MapReduce, poniżej przedstawiono najważniejsze parametry, które można skonfigurować w celu zwiększenia wydajności na Data Lake Storage Gen1:

* **Mapreduce.map.Memory.MB** — ilość pamięci do przydzielenia do każdego mapowania
* **Mapreduce.job.Maps** — liczba zadań mapy na zadanie
* **Mapreduce.Reduce.Memory.MB** — ilość pamięci do przydzielenia do każdego reduktor
* **Mapreduce.job.reduces** — liczba zadań Zmniejsz na zadanie

**Mapreduce.map.Memory / Mapreduce.reduce.memory** tę liczbę należy dostosować, zależnie od ilości pamięci jest niezbędny do mapy i/lub Zmniejsz zadania.  Wartości domyślne mapreduce.map.memory i mapreduce.reduce.memory można wyświetlić w Ambari za pomocą konfiguracji usługi Yarn.  W Ambari przejdź do usługi YARN, a następnie Wyświetl kartę konfiguracje.  Pojawi się pamięci usługi YARN.  

**Mapreduce.job.Maps / Mapreduce.job.reduces** określi maksymalną liczbę liczby maperów i reduktorów, które ma zostać utworzony.  Liczba podziałów określi, ile liczby maperów zostanie utworzone zadanie MapReduce.  W związku z tym może zostać mniejszej liczby maperów niż żądana, czy istnieją mniej dzieli dane niż liczba liczby maperów zażądano.       

## <a name="guidance"></a>Wskazówki

**Krok 1. Określenie liczby zadań uruchamianych** — domyślnie MapReduce użyje całego klastra dla zadania.  Używając mniej klastra przy użyciu mniejszej liczby maperów niż jest dostępnych kontenerów.  Wskazówki zawarte w tym dokumencie przyjęto założenie, że aplikacja jest tylko aplikacja działająca w klastrze.      

**Krok 2. Ustaw mapreduce.map.memory/mapreduce.reduce.memory** — rozmiar pamięci dla mapy i zredukować zadania będzie zależeć od określonego zadania.  Można zmniejszyć rozmiar pamięci, jeśli chcesz zwiększyć współbieżność.  Liczba równolegle wykonywanych zadań zależy od liczbę kontenerów.  Przez zmniejszenie ilości pamięci dla mapowania lub reduktor, można utworzyć większej liczbie kontenerów, umożliwiają one więcej liczby maperów i reduktorów można uruchamiać jednocześnie.  Za dużo zmniejsza ilość pamięci może spowodować niektóre procesy przekroczyć dostępną ilość pamięci.  Jeśli wystąpi błąd stosu podczas uruchamiania zadania, należy zwiększyć ilość pamięci na mapowania lub reduktor.  Należy rozważyć, czy dodanie większej liczbie kontenerów spowoduje dodanie dodatkowych obciążenie dla każdego kontenera dodatkowe, które potencjalnie mogą obniżyć wydajność.  Innym sposobem jest uzyskanie większej ilości pamięci za pomocą klastra, który ma większej ilości pamięci lub zwiększenie liczby węzłów w klastrze.  Większa ilość pamięci spowoduje włączenie większej liczbie kontenerów, które ma być używany, co oznacza, że uzyskać większą współbieżność.  

**Krok 3. Określania YARN całkowitej ilości pamięci** — można dostrajanie mapreduce.job.maps/mapreduce.job.reduces, należy wziąć pod uwagę ilość całkowitej ilości pamięci usługi YARN dostępne do użycia.  Te informacje są dostępne w Ambari.  Przejdź do usługi YARN i wyświetlić kartę konfiguracje.  W tym oknie wyświetlane jest pamięci usługi YARN.  Należy pomnożyć pamięci usługi YARN z liczbą węzłów w klastrze, aby pobrać całkowitej ilości pamięci usługi YARN.

    Total YARN memory = nodes * YARN memory per node
Jeśli używasz klastra pusty pamięć może być całkowitej ilości pamięci usługi YARN dla klastra.  Jeśli inne aplikacje używają pamięci, można wybrać do użycia tylko część klastra, pamięci, zmniejszając liczbę liczby maperów i reduktorów do liczby kontenerów, do których chcesz użyć.  

**Krok 4. Oblicz liczbę kontenery usługi YARN** — kontenery usługi YARN dyktowanie ilość współbieżności dostępny dla zadania.  Pobrać całkowity rozmiar pamięci usługi YARN i przez mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Krok 5. Ustaw mapreduce.job.maps/mapreduce.job.reduces** równa mapreduce.job.maps/mapreduce.job.reduces co najmniej liczby dostępnych kontenerów.  Możesz eksperymentować jeszcze bardziej przez zwiększenie liczby maperów i reduktorów, aby zobaczyć, jeśli możesz uzyskać lepszą wydajność.  Należy pamiętać o tym, czy więcej liczby maperów będzie miała dodatkowe obciążenie, dlatego masz zbyt wiele liczby maperów może obniżyć wydajność.  

Planowanie procesora CPU i procesora CPU izolacji są domyślnie wyłączone, liczbę kontenery usługi YARN jest ograniczony przez pamięć.

## <a name="example-calculation"></a>Przykład obliczania

Załóżmy, że masz już klaster składa się z 8 węzłów D14 i chcesz uruchomić zadanie intensywnie korzystających z operacji We/Wy.  Poniżej przedstawiono obliczenia, które należy wykonać:

**Krok 1. Określenie liczby zadań uruchamianych** — w tym przykładzie przyjęto założenie, że nasze zadania jest uruchomione tylko jedno.  

**Krok 2. Ustaw mapreduce.map.memory/mapreduce.reduce.memory** — w tym przykładzie są uruchomione zadania intensywnie korzystających z operacji We/Wy i zdecydować, że 3 GB pamięci dla zadań mapy okażą się wystarczające.

    mapreduce.map.memory = 3GB
**Krok 3. Określania YARN całkowitej ilości pamięci**

    total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Krok 4. Oblicz # kontenery usługi YARN**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Krok 5. Ustaw mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="limitations"></a>Ograniczenia

**Data Lake Storage Gen1 ograniczania**

Jako usługa dla wielu dzierżawców Data Lake Storage Gen1 Ustawia limity przepustowości na poziomie konta.  W razie osiągnięcia limitów rozpocznie się niepowodzeń zadań. To może wskazywać obserwowania błędy ograniczania w dziennikach zadania.  Jeśli potrzebujesz większej przepustowości dla zadania, skontaktuj się z nami.   

Aby sprawdzić, jeśli użytkownik jest ograniczany, musisz włączyć debugowanie rejestrowania po stronie klienta. Poniżej przedstawiono, jak można to zrobić:

1. Umieść następującą właściwość we właściwościach log4j w Ambari > YARN > Konfiguracja > Zaawansowane yarn log4j: log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Uruchom ponownie wszystkie węzły/usługi dla konfiguracji zostały wprowadzone.

3. Jeśli użytkownik jest ograniczany, zobaczysz kod błędu HTTP 429 w pliku dziennika usługi YARN. Plik dziennika usługi YARN znajduje się w /tmp/&lt;użytkownika&gt;/yarn.log

## <a name="examples-to-run"></a>Przykłady do uruchomienia

Aby zademonstrować, jak MapReduce jest wykonywany na Data Lake Storage Gen1, poniżej przedstawiono przykładowy kod, który został uruchomiony w klastrze z następującymi ustawieniami:

* węzeł 16 D14v2
* Klastra Hadoop z systemem HDI 3.6

Począwszy od punktu poniżej przedstawiono niektóre przykładowe polecenia do uruchomienia MapReduce Teragen, Terasort i Teravalidate.  Można dostosować tych poleceń, w oparciu o zasoby.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
