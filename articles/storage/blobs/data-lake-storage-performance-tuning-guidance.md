---
title: Optymalizacja usługi Azure Data Lake Storage Gen2 pod kątem wydajności | Dokumenty firmy Microsoft
description: Wskazówki dotyczące dostrajania wydajności usługi Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: f1a16228b72d7e0f45048669ade94a0c78d9ac52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327941"
---
# <a name="optimize-azure-data-lake-storage-gen2-for-performance"></a>Optymalizacja usługi Azure Data Lake Storage Gen2 pod kątem wydajności

Usługa Azure Data Lake Storage Gen2 obsługuje wysoką przepływność dla intensywnej analizy we/wy i przenoszenia danych.  W umiar pamięci masowej usługi Data Lake Gen2 przy użyciu wszystkich dostępnych przepływności — ilość danych, które mogą być odczytywane lub zapisywane na sekundę — jest ważne, aby uzyskać najlepszą wydajność.  Osiąga się to poprzez wykonywanie jak największej liczby odczytów i zapisów równolegle, jak to możliwe.

![Wydajność pamięci masowej Data Lake Gen2](./media/data-lake-storage-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen2 można skalować, aby zapewnić niezbędną przepływność dla wszystkich scenariuszy analizy. Domyślnie konto Data Lake Storage Gen2 zapewnia automatycznie wystarczającą przepływność, aby zaspokoić potrzeby szerokiej kategorii przypadków użycia. W przypadkach, gdy klienci napotkają domyślny limit, konto Data Lake Storage Gen2 można skonfigurować w celu zapewnienia większej przepływności, kontaktując się z [pomocą techniczną platformy Azure.](https://azure.microsoft.com/support/faq/)

## <a name="data-ingestion"></a>Wprowadzanie danych

Podczas pozyskiwania danych z systemu źródłowego do usługi Data Lake Storage Gen2, należy wziąć pod uwagę, że sprzęt źródłowy, sprzęt sieci źródłowej i łączność sieciowa z data lake storage gen2 może być wąskim gardłem.  

![Wydajność pamięci masowej Data Lake Gen2](./media/data-lake-storage-performance-tuning-guidance/bottleneck.png)

Ważne jest, aby upewnić się, że te czynniki nie mają wpływu na przenoszenie danych.

### <a name="source-hardware"></a>Sprzęt źródłowy

Niezależnie od tego, czy używasz maszyn lokalnych, czy maszyn wirtualnych na platformie Azure, należy starannie wybrać odpowiedni sprzęt. W przypadku sprzętu z dysku źródłowego preferuj dyski SSD od dysków twardych i wybierz sprzęt dyskowy z szybszymi wrzecionami. W przypadku sprzętu sieci źródłowej użyj najszybszych kart sieciowych.  Na platformie Azure zaleca się maszyny wirtualne usługi Azure D14, które mają odpowiednio zaawansowany sprzęt dyskowy i sieciowy.

### <a name="network-connectivity-to-data-lake-storage-gen2"></a>Łączność sieciowa z pamięcią danych Data Lake Storage Gen2

Łączność sieciowa między danymi źródłowymi a programem Data Lake Storage Gen2 może czasami być wąskim gardłem. Gdy dane źródłowe są lokalne, należy rozważyć użycie dedykowanego łącza za pomocą [usługi Azure ExpressRoute.](https://azure.microsoft.com/services/expressroute/) Jeśli dane źródłowe znajdują się na platformie Azure, wydajność będzie najlepsza, gdy dane znajdują się w tym samym regionie platformy Azure, co konto Data Lake Storage Gen2.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Konfigurowanie narzędzi pozyskiwania danych w celu maksymalnej równoległości

Po usunięciu problemu z powyższymi wąskimi gardłami sprzętu źródłowego i łączności sieciowej można przystąpić do konfigurowania narzędzi pozyskiwania. W poniższej tabeli podsumowano kluczowe ustawienia kilku popularnych narzędzi pozyskiwania i przedstawiono szczegółowe artykuły dostrajania wydajności dla nich.  Aby dowiedzieć się więcej o tym, którego narzędzia użyć w scenariuszu, odwiedź ten [artykuł](data-lake-storage-data-scenarios.md).

| Narzędzie               | Ustawienia     | Więcej szczegółów                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| DistCp (distcp)            | -m (mapper)   | [Link](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies (Kopia równoległa)    | [Link](../../data-factory/copy-activity-performance.md)                          |
| Sqoop           | fs.azure.block.size, -m (maper)    |   [Link](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Struktura zestawu danych

Gdy dane są przechowywane w programie Data Lake Storage Gen2, rozmiar pliku, liczba plików i struktura folderów mają wpływ na wydajność.  W poniższej sekcji opisano najlepsze rozwiązania w tych obszarach.  

### <a name="file-size"></a>Rozmiar pliku

Zazwyczaj aparaty analityczne, takie jak HDInsight i Usługi Azure Data Lake Analytics mają obciążenie dla plików. Jeśli przechowujesz dane jak najwięcej małych plików, może to negatywnie wpłynąć na wydajność. Ogólnie rzecz biorąc, zorganizuj dane w większe pliki o większym rozmiarze, aby uzyskać lepszą wydajność (rozmiar od 256 MB do 100 GB). Niektóre aparaty i aplikacje mogą mieć problemy z wydajnym przetwarzaniem plików o rozmiarze większym niż 100 GB.

Czasami potoki danych mają ograniczoną kontrolę nad nieprzetworzonymi danymi, które mają wiele małych plików. Zaleca się, aby "gotowania" proces, który generuje większe pliki do użycia w dalszych aplikacji.

### <a name="organizing-time-series-data-in-folders"></a>Organizowanie danych szeregów czasowych w folderach

W przypadku obciążeń hive przycinanie partycji danych szeregów czasowych może pomóc niektórym kwerendom w odczytie tylko podzbioru danych, co zwiększa wydajność.    

Te potoki, które pozyskiwania danych szeregów czasowych, często umieszczają swoje pliki z bardzo ustrukturyzowanym nazewnictwem dla plików i folderów. Poniżej znajduje się bardzo częsty przykład, który widzimy dla danych, które są uporządkowane według daty:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Należy zauważyć, że informacje o datetime są wyświetlane zarówno jako foldery, jak i w nazwach plików.

W przypadku daty i godziny, poniżej znajduje się wspólny wzorzec

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Ponownie, wybór dokonany w organizacji folderów i plików powinien zoptymalizować pod kątem większych rozmiarów plików i rozsądnej liczby plików w każdym folderze.

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Optymalizacja zadań intensywnie korzystających z we/wy w przypadku obciążeń hadoop i spark w programie HDInsight

Praca należy do jednej z trzech następujących kategorii:

* **Procesor intensywnie.**  Te zadania mają długi czas obliczeń przy minimalnych czasach we/wy.  Przykłady obejmują uczenie maszynowe i zadania przetwarzania języka naturalnego.  
* **Pamięć intensywnie.**  Te zadania używają dużo pamięci.  Przykłady obejmują PageRank i zadania analizy w czasie rzeczywistym.  
* **Intensywnie we/wy.**  Te zadania spędzają większość czasu na wykonywaniu we/wy.  Typowym przykładem jest zadanie kopiowania, które wykonuje tylko operacje odczytu i zapisu.  Inne przykłady obejmują zadania przygotowania danych, które odczytują dużo danych, wykonuje transformację niektórych danych, a następnie zapisuje dane z powrotem do magazynu.  

Poniższe wskazówki mają zastosowanie tylko do zadań intensywnie korzystających z we/wy.

## <a name="general-considerations"></a>Zagadnienia ogólne

Zadanie, które odczytuje lub zapisuje aż 100 MB w jednej operacji, ale bufor o tym rozmiarze może naruszyć wydajność.
Aby zoptymalizować wydajność, spróbuj zachować rozmiar operacji we/wy między 4 MB a 16 MB.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Uwagi ogólne dotyczące klastra USŁUGI HDInsight

* **Wersje HDInsight.** Aby uzyskać najlepszą wydajność, użyj najnowszej wersji programu HDInsight.
* **Regionów.** Umieść konto Data Lake Storage Gen2 w tym samym regionie co klaster HDInsight.  

Klaster HDInsight składa się z dwóch węzłów głównego i niektórych węzłów procesu roboczego. Każdy węzeł procesu roboczego zawiera określoną liczbę rdzeni i pamięci, która jest określana przez typ maszyny Wirtualnej.  Podczas uruchamiania zadania YARN jest negocjatorem zasobów, który przydziela dostępną pamięć i rdzenie do tworzenia kontenerów.  Każdy kontener uruchamia zadania potrzebne do wykonania zadania.  Kontenery działają równolegle do szybkiego przetwarzania zadań. W związku z tym wydajność jest lepsza, uruchamiając jak najwięcej kontenerów równoległych, jak to możliwe.

Istnieją trzy warstwy w klastrze HDInsight, które można dostroić, aby zwiększyć liczbę kontenerów i używać wszystkich dostępnych przepływności.  

* **Warstwa fizyczna**
* **Warstwa przędzy**
* **Warstwa obciążenia**

### <a name="physical-layer"></a>Warstwa fizyczna

**Uruchom klaster z większą liczrą węzłów i/lub większymi maszynami wirtualnymi.**  Większy klaster umożliwi uruchomienie większej liczby kontenerów YARN, jak pokazano na poniższym rysunku.

![Wydajność pamięci masowej Data Lake Gen2](./media/data-lake-storage-performance-tuning-guidance/VM.png)

**Używaj maszyn wirtualnych z większą przepustowością sieci.**  Przepustowość sieci może być wąskim gardłem, jeśli przepustowość sieci jest mniejsza niż przepustowość usługi Data Lake Storage Gen2.  Różne maszyny wirtualne będą miały różne rozmiary przepustowości sieci.  Wybierz typ maszyny Wirtualnej, który ma największą możliwą przepustowość sieci.

### <a name="yarn-layer"></a>Warstwa przędzy

**Użyj mniejszych pojemników na przędzę.**  Zmniejsz rozmiar każdego kontenera YARN, aby utworzyć więcej kontenerów z taką samą ilością zasobów.

![Wydajność pamięci masowej Data Lake Gen2](./media/data-lake-storage-performance-tuning-guidance/small-containers.png)

W zależności od obciążenia zawsze będzie minimalny rozmiar kontenera YARN, który jest potrzebny. Jeśli wybierzesz zbyt mały kontener, zadania będą działać na problemy z braku pamięci. Zazwyczaj pojemniki YARN nie powinny być mniejsze niż 1 GB. Często można zobaczyć pojemniki 3GB YARN. W przypadku niektórych obciążeń mogą być potrzebne większe kontenery YARN.  

**Zwiększ rdzenie na pojemnik YARN.**  Zwiększ liczbę rdzeni przydzielonych do każdego kontenera, aby zwiększyć liczbę równoległych zadań uruchamianych w każdym kontenerze.  Działa to w przypadku aplikacji, takich jak Spark, które uruchamiają wiele zadań na kontener.  Dla aplikacji, takich jak Hive, które uruchamiają pojedynczy wątek w każdym kontenerze, lepiej jest mieć więcej kontenerów, a nie więcej rdzeni na kontener.

### <a name="workload-layer"></a>Warstwa obciążenia

**Użyj wszystkich dostępnych pojemników.**  Ustaw liczbę zadań, aby były równe lub większe niż liczba dostępnych kontenerów, tak aby wszystkie zasoby były wykorzystywane.

![Wydajność pamięci masowej Data Lake Gen2](./media/data-lake-storage-performance-tuning-guidance/use-containers.png)

**Nieudane zadania są kosztowne.** Jeśli każde zadanie ma dużą ilość danych do przetworzenia, a następnie niepowodzenie zadania powoduje kosztowne ponowienie próby.  Dlatego lepiej jest utworzyć więcej zadań, z których każdy przetwarza niewielką ilość danych.

Oprócz ogólnych wytycznych powyżej, każda aplikacja ma różne parametry dostępne do strojenia dla tej konkretnej aplikacji. W poniższej tabeli wymieniono niektóre parametry i łącza, aby rozpocząć dostrajanie wydajności dla każdej aplikacji.

| Obciążenie | Parametr do ustawiania zadań |
|----------|------------------------|
| [Usługa Spark w usłudze HDInsight](data-lake-storage-performance-tuning-spark.md) | <ul><li>Wykonawcy num</li><li>Wykonawca-pamięć</li><li>Rdzenie executor</li></ul> |
| [Gałąź na HDInsight](data-lake-storage-performance-tuning-hive.md) | <ul><li>hive.tez.container.size</li></ul> |
| [MapReduce na HDInsight](data-lake-storage-performance-tuning-mapreduce.md) | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.zmniejsza</li></ul> |
| [Usługa Storm w usłudze HDInsight](data-lake-storage-performance-tuning-storm.md)| <ul><li>Liczba procesów roboczych</li><li>Liczba wystąpień wykonawcy wylewki</li><li>Liczba wystąpień wykonawcy śrub </li><li>Liczba zadań wylewki</li><li>Liczba zadań śrubowych</li></ul>|

## <a name="see-also"></a>Zobacz też
* [Omówienie usługi Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
