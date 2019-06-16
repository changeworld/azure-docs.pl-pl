---
title: Wytyczne dotyczące dostosowywania usługi Azure Data Lake Storage Gen2 MapReduce wydajności | Dokumentacja firmy Microsoft
description: Wytyczne dotyczące dostosowywania usługi Azure Data Lake Storage Gen2 MapReduce wydajności
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 7d20f1b398c50a3b98ee862332338dbf3aaece59
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939378"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen2"></a>Wskazówki dotyczące technologii MapReduce, HDInsight i Azure Data Lake Storage Gen2 dostrajania wydajności

Omówienie czynników, które należy wziąć pod uwagę podczas dostrajaniu zadań Mapreduce. W tym artykule omówiono szereg wskazówki dotyczące dostrajania wydajności oprogramowania.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Storage Gen2**. Aby uzyskać instrukcje na temat jej tworzenia, zobacz [Szybki Start: Tworzenie konta magazynu Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).
* **Klaster usługi Azure HDInsight** dzięki dostępowi do konta Data Lake Storage Gen2. Zobacz [klastrów użycia usługi Azure Data Lake magazynu Gen2 za pomocą usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **Przy użyciu technologii MapReduce w HDInsight**.  Aby uzyskać więcej informacji, zobacz [używanie MapReduce na platformie Hadoop w HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Wytyczne dotyczące Data Lake Storage Gen2 dostrajania wydajności**.  Pojęcia związane z ogólnej wydajności, zobacz [Data Lake Storage Gen2 dostrajania wskazówki dotyczące wydajności](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Parametry

Podczas uruchamiania zadania MapReduce, poniżej przedstawiono parametry, które można skonfigurować w celu zwiększenia wydajności na Data Lake Storage Gen2:

* **Mapreduce.map.Memory.MB** — ilość pamięci do przydzielenia do każdego mapowania
* **Mapreduce.job.Maps** — liczba zadań mapy na zadanie
* **Mapreduce.Reduce.Memory.MB** — ilość pamięci do przydzielenia do każdego reduktor
* **Mapreduce.job.reduces** — liczba zadań Zmniejsz na zadanie

**Mapreduce.map.Memory / Mapreduce.reduce.memory** tę liczbę należy dostosować, zależnie od ilości pamięci jest niezbędny do mapy i/lub Zmniejsz zadania.  Wartości domyślne mapreduce.map.memory i mapreduce.reduce.memory można wyświetlić w Ambari za pomocą konfiguracji usługi Yarn.  W Ambari przejdź do usługi YARN, a następnie Wyświetl kartę konfiguracje.  Pojawi się pamięci usługi YARN.  

**Mapreduce.job.Maps / Mapreduce.job.reduces** określi maksymalną liczbę liczby maperów i reduktorów, które ma zostać utworzony.  Liczba podziałów określi, ile liczby maperów zostanie utworzone zadanie MapReduce.  W związku z tym może zostać mniejszej liczby maperów niż żądana, czy istnieją mniej dzieli dane niż liczba liczby maperów zażądano.       

## <a name="guidance"></a>Wskazówki

> [!NOTE]
> Wskazówki zawarte w tym dokumencie przyjęto założenie, że aplikacja jest tylko aplikacja działająca w klastrze.

**Krok 1. Określenie liczby zadań uruchamiania**

Domyślnie MapReduce użyje całego klastra dla zadania.  Używając mniej klastra przy użyciu mniejszej liczby maperów niż jest dostępnych kontenerów.        

**Krok 2. Ustaw mapreduce.map.memory/mapreduce.reduce.memory**

Rozmiar pamięci dla mapy i zredukować zadania będzie zależeć od określonego zadania.  Można zmniejszyć rozmiar pamięci, jeśli chcesz zwiększyć współbieżność.  Liczba równolegle wykonywanych zadań zależy od liczbę kontenerów.  Przez zmniejszenie ilości pamięci dla mapowania lub reduktor, można utworzyć większej liczbie kontenerów, umożliwiają one więcej liczby maperów i reduktorów można uruchamiać jednocześnie.  Za dużo zmniejsza ilość pamięci może spowodować niektóre procesy przekroczyć dostępną ilość pamięci.  Jeśli wystąpi błąd stosu podczas uruchamiania zadania, należy zwiększyć ilość pamięci na mapowania lub reduktor.  Należy rozważyć, czy dodanie większej liczbie kontenerów spowoduje dodanie dodatkowych obciążenie dla każdego kontenera dodatkowe, które potencjalnie mogą obniżyć wydajność.  Innym sposobem jest uzyskanie większej ilości pamięci za pomocą klastra, który ma większej ilości pamięci lub zwiększenie liczby węzłów w klastrze.  Większa ilość pamięci spowoduje włączenie większej liczbie kontenerów, które ma być używany, co oznacza, że uzyskać większą współbieżność.  

**Krok 3. Określania YARN całkowitej ilości pamięci**

Należy dostosować mapreduce.job.maps/mapreduce.job.reduces, należy rozważyć ilość całkowitej ilości pamięci usługi YARN dostępne do użycia.  Te informacje są dostępne w Ambari.  Przejdź do usługi YARN i wyświetlić kartę konfiguracje.  W tym oknie wyświetlane jest pamięci usługi YARN.  Należy pomnożyć pamięci usługi YARN z liczbą węzłów w klastrze, aby pobrać całkowitej ilości pamięci usługi YARN.

    Total YARN memory = nodes * YARN memory per node

Jeśli używasz klastra pusty pamięć może być całkowitej ilości pamięci usługi YARN dla klastra.  Jeśli inne aplikacje używają pamięci, można wybrać do użycia tylko część klastra, pamięci, zmniejszając liczbę liczby maperów i reduktorów do liczby kontenerów, do których chcesz użyć.  

**Krok 4. Oblicz liczbę kontenery usługi YARN**

Kontenery usługi YARN określają ilość współbieżności dostępny dla zadania.  Pobrać całkowity rozmiar pamięci usługi YARN i przez mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Krok 5. Ustaw mapreduce.job.maps/mapreduce.job.reduces**

Ustaw mapreduce.job.maps/mapreduce.job.reduces do co najmniej liczby dostępnych kontenerów.  Możesz eksperymentować jeszcze bardziej przez zwiększenie liczby maperów i reduktorów, aby zobaczyć, jeśli możesz uzyskać lepszą wydajność.  Należy pamiętać o tym, czy więcej liczby maperów będzie miała dodatkowe obciążenie, dlatego masz zbyt wiele liczby maperów może obniżyć wydajność.  

Planowanie procesora CPU i procesora CPU izolacji są domyślnie wyłączone, liczbę kontenery usługi YARN jest ograniczony przez pamięć.

## <a name="example-calculation"></a>Przykład obliczania

Załóżmy, że istnieje klastra składa się z 8 węzłów D14, i chcemy uruchomić zadanie intensywnie korzystających z operacji We/Wy.  Poniżej przedstawiono obliczenia, które należy wykonać:

**Krok 1. Określenie liczby zadań uruchamiania**

W tym przykładzie załóżmy, że nasze zadania jest tylko zadanie, które działa.  

**Krok 2. Ustaw mapreduce.map.memory/mapreduce.reduce.memory**

W tym przykładzie będziemy działają zadania intensywnie korzystających z operacji We/Wy i zdecydować, że 3GB pamięci dla zadań mapy okażą się wystarczające.

    mapreduce.map.memory = 3GB

**Krok 3. Określania YARN całkowitej ilości pamięci**

    Total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Krok 4. Oblicz # kontenery usługi YARN**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Krok 5. Ustaw mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>Przykłady do uruchomienia

Aby zademonstrować, jak MapReduce jest wykonywany na Data Lake Storage Gen2, poniżej przedstawiono przykładowy kod, który został uruchomiony w klastrze z następującymi ustawieniami:

* węzeł 16 D14v2
* Klastra Hadoop z systemem HDI 3.6

Począwszy od punktu poniżej przedstawiono niektóre przykładowe polecenia do uruchomienia MapReduce Teragen, Terasort i Teravalidate.  Można dostosować tych poleceń, w oparciu o zasoby.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate
