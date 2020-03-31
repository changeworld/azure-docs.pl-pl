---
title: Usługa Azure Data Lake Storage Gen1 — dostrajanie wydajności
description: W tym artykule opisano sposób dostrajania usługi Azure Data Lake Storage Gen1 pod kątem wydajności.
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: stewu
ms.openlocfilehash: 2521700e0f07691541ee6cbbf085a8be72f08129
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904625"
---
# <a name="tune-azure-data-lake-storage-gen1-for-performance"></a>Dostrajanie usługi Azure Data Lake Storage Gen1 w celu uzyskania wydajności

Data Lake Storage Gen1 obsługuje wysoką przepływność dla intensywnej analizy we/wy i przenoszenia danych. W umiar pamięci masowej usługi Data Lake Gen1 przy użyciu wszystkich dostępnych przepływności — ilość danych, które mogą być odczytywane lub zapisywane na sekundę — jest ważne, aby uzyskać najlepszą wydajność. Osiąga się to poprzez wykonywanie jak największej liczby odczytów i zapisów równolegle, jak to możliwe.

![Wydajność pamięci masowej Data Lake Gen1](./media/data-lake-store-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen1 można skalować, aby zapewnić niezbędną przepływność dla wszystkich scenariuszy analizy. Domyślnie konto Data Lake Storage Gen1 zapewnia automatycznie wystarczającą przepływność, aby zaspokoić potrzeby szerokiej kategorii przypadków użycia. W przypadkach, gdy klienci napotkają domyślny limit, konto Data Lake Storage Gen1 można skonfigurować w celu zapewnienia większej przepływności przez skontaktowanie się z pomocą techniczną firmy Microsoft.

## <a name="data-ingestion"></a>Wprowadzanie danych

Podczas pozyskiwania danych z systemu źródłowego do usługi Data Lake Storage Gen1, należy wziąć pod uwagę, że sprzęt źródłowy, sprzęt sieci źródłowej i łączność sieciowa z pamięcią masową Data Lake Gen1 może być wąskim gardłem.

![Wydajność pamięci masowej Data Lake Gen1](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

Ważne jest, aby upewnić się, że te czynniki nie mają wpływu na przenoszenie danych.

### <a name="source-hardware"></a>Sprzęt źródłowy

Niezależnie od tego, czy używasz maszyn lokalnych, czy maszyn wirtualnych na platformie Azure, należy starannie wybrać odpowiedni sprzęt. W przypadku sprzętu z dysku źródłowego preferuj dyski SSD od dysków twardych i wybierz sprzęt dyskowy z szybszymi wrzecionami. W przypadku sprzętu sieci źródłowej użyj najszybszych kart sieciowych. Na platformie Azure zaleca się maszyny wirtualne usługi Azure D14, które mają odpowiednio zaawansowany sprzęt dyskowy i sieciowy.

### <a name="network-connectivity-to-data-lake-storage-gen1"></a>Łączność sieciowa z pamięcią danych Lake Storage Gen1

Łączność sieciowa między danymi źródłowymi a pamięcią Gen1 usługi Data Lake Storage Gen1 może czasami być wąskim gardłem. Gdy dane źródłowe są lokalne, należy rozważyć użycie dedykowanego łącza za pomocą [usługi Azure ExpressRoute.](https://azure.microsoft.com/services/expressroute/) Jeśli dane źródłowe znajdują się na platformie Azure, wydajność będzie najlepsza, gdy dane znajdują się w tym samym regionie platformy Azure, co konto Data Lake Storage Gen1.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Konfigurowanie narzędzi pozyskiwania danych w celu maksymalnej równoległości

Po zajście w sieć wąskich gardeł sprzętem źródłowym i łącznością sieciową można przystąpić do konfigurowania narzędzi pozyskiwania. W poniższej tabeli podsumowano kluczowe ustawienia kilku popularnych narzędzi pozyskiwania i przedstawiono szczegółowe artykuły dostrajania wydajności dla nich. Aby dowiedzieć się więcej o tym, którego narzędzia użyć w scenariuszu, odwiedź ten [artykuł](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios).

| Narzędzie          | Ustawienia | Więcej szczegółów                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| PowerShell       | PerFileThreadCount, ConcurrentFileCount | [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-powershell) |
| AdlCopy (Polski)    | Jednostki usługi Azure Data Lake Analytics | [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob#performance-considerations-for-using-adlcopy)         |
| DistCp (distcp)            | -m (mapper) | [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-wasb-distcp#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies (Kopia równoległa) | [Link](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | fs.azure.block.size, -m (maper) | [Link](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Struktura zestawu danych

Gdy dane są przechowywane w urozwoju danych Lake Storage Gen1, rozmiar pliku, liczba plików i struktura folderów wpływają na wydajność. W poniższej sekcji opisano najlepsze rozwiązania w tych obszarach.

### <a name="file-size"></a>Rozmiar pliku

Zazwyczaj aparaty analityczne, takie jak HDInsight i Usługi Azure Data Lake Analytics mają obciążenie dla plików. Jeśli przechowujesz dane jak najwięcej małych plików, może to negatywnie wpłynąć na wydajność.

Ogólnie rzecz biorąc, zorganizuj dane w pliki o większym rozmiarze, aby uzyskać lepszą wydajność. Z reguły organizuj zestawy danych w plikach o rozmiarze 256 MB lub większym. W niektórych przypadkach, takich jak obrazy i dane binarne, nie jest możliwe do przetwarzania równolegle. W takich przypadkach zaleca się przechowywanie pojedynczych plików poniżej 2 GB.

Czasami potoki danych mają ograniczoną kontrolę nad nieprzetworzonymi danymi, które mają wiele małych plików. Zaleca się, aby "gotowania" proces, który generuje większe pliki do użycia w dalszych aplikacji.

### <a name="organize-time-series-data-in-folders"></a>Organizowanie danych szeregów czasowych w folderach

W przypadku obciążeń hive i ADLA przycinanie partycji danych szeregów czasowych może pomóc niektórym kwerendom w odczytie tylko podzbioru danych, co zwiększa wydajność.

Te potoki, które pozyskiwania danych szeregów czasowych, często umieszczają swoje pliki ze strukturą nazewnictwa dla plików i folderów. Poniżej przedstawiono typowy przykład, który widzimy dla danych, które są uporządkowane według daty:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Należy zauważyć, że informacje o datetime są wyświetlane zarówno jako foldery, jak i w nazwach plików.

W przypadku daty i godziny, poniżej znajduje się wspólny wzorzec

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Ponownie, wybór dokonany w organizacji folderów i plików powinien zoptymalizować pod kątem większych rozmiarów plików i rozsądnej liczby plików w każdym folderze.

## <a name="optimize-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Optymalizacja zadań intensywnie korzystających z we/wy w obciążeniach hadoop i spark w programie HDInsight

Praca należy do jednej z trzech następujących kategorii:

* **Procesor intensywnie.** Te zadania mają długi czas obliczeń przy minimalnych czasach we/wy. Przykłady obejmują uczenie maszynowe i zadania przetwarzania języka naturalnego.
* **Pamięć intensywnie.** Te zadania używają dużo pamięci. Przykłady obejmują PageRank i zadania analizy w czasie rzeczywistym.
* **Intensywnie we/wy.** Te zadania spędzają większość czasu na wykonywaniu we/wy. Typowym przykładem jest zadanie kopiowania, które wykonuje tylko operacje odczytu i zapisu. Inne przykłady obejmują zadania przygotowania danych, które odczytują wiele danych, wykonuje transformację niektórych danych, a następnie zapisuje dane z powrotem do magazynu.

Poniższe wskazówki mają zastosowanie tylko do zadań intensywnie korzystających z we/wy.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Uwagi ogólne dotyczące klastra USŁUGI HDInsight

* **Wersje HDInsight.** Aby uzyskać najlepszą wydajność, użyj najnowszej wersji programu HDInsight.
* **Regionów.** Umieść konto Data Lake Storage Gen1 w tym samym regionie co klaster HDInsight.

Klaster HDInsight składa się z dwóch węzłów głównego i niektórych węzłów procesu roboczego. Każdy węzeł procesu roboczego zawiera określoną liczbę rdzeni i pamięci, która jest określana przez typ maszyny Wirtualnej. Podczas uruchamiania zadania YARN jest negocjatorem zasobów, który przydziela dostępną pamięć i rdzenie do tworzenia kontenerów. Każdy kontener uruchamia zadania potrzebne do wykonania zadania. Kontenery działają równolegle do szybkiego przetwarzania zadań. W związku z tym wydajność jest lepsza, uruchamiając jak najwięcej kontenerów równoległych, jak to możliwe.

Istnieją trzy warstwy w klastrze HDInsight, które można dostroić, aby zwiększyć liczbę kontenerów i używać wszystkich dostępnych przepływności.

* **Warstwa fizyczna**
* **Warstwa przędzy**
* **Warstwa obciążenia**

### <a name="physical-layer"></a>Warstwa fizyczna

**Uruchom klaster z większą liczrą węzłów i/lub większymi maszynami wirtualnymi.** Większy klaster umożliwi uruchomienie większej liczby kontenerów YARN, jak pokazano na poniższym rysunku.

![Wydajność pamięci masowej Data Lake Gen1](./media/data-lake-store-performance-tuning-guidance/VM.png)

**Używaj maszyn wirtualnych z większą przepustowością sieci.** Przepustowość sieci może być wąskim gardłem, jeśli przepustowość sieci jest mniejsza niż przepustowość usługi Data Lake Storage Gen1. Różne maszyny wirtualne będą miały różne rozmiary przepustowości sieci. Wybierz typ maszyny Wirtualnej, który ma największą możliwą przepustowość sieci.

### <a name="yarn-layer"></a>Warstwa przędzy

**Użyj mniejszych pojemników na przędzę.** Zmniejsz rozmiar każdego kontenera YARN, aby utworzyć więcej kontenerów z taką samą ilością zasobów.

![Wydajność pamięci masowej Data Lake Gen1](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

W zależności od obciążenia zawsze będzie minimalny rozmiar kontenera YARN, który jest potrzebny. Jeśli wybierzesz zbyt mały kontener, zadania będą działać na problemy z braku pamięci. Zazwyczaj pojemniki YARN nie powinny być mniejsze niż 1 GB. Często można zobaczyć kontenery 3 GB YARN. W przypadku niektórych obciążeń mogą być potrzebne większe kontenery YARN.

**Zwiększ rdzenie na pojemnik YARN.** Zwiększ liczbę rdzeni przydzielonych do każdego kontenera, aby zwiększyć liczbę równoległych zadań uruchamianych w każdym kontenerze. Działa to w przypadku aplikacji, takich jak Spark, które uruchamiają wiele zadań na kontener. W przypadku aplikacji, takich jak Hive, które uruchamiają pojedynczy wątek w każdym kontenerze, lepiej mieć więcej kontenerów, a nie więcej rdzeni na kontener.

### <a name="workload-layer"></a>Warstwa obciążenia

**Użyj wszystkich dostępnych pojemników.** Ustaw liczbę zadań, aby była równa lub większa niż liczba dostępnych kontenerów, tak aby wszystkie zasoby były używane.

![Wydajność pamięci masowej Data Lake Gen1](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**Nieudane zadania są kosztowne.** Jeśli każde zadanie ma dużą ilość danych do przetworzenia, a następnie niepowodzenie zadania powoduje kosztowne ponowienie próby. Dlatego lepiej jest utworzyć więcej zadań, z których każdy przetwarza niewielką ilość danych.

Oprócz ogólnych wytycznych powyżej, każda aplikacja ma różne parametry dostępne do strojenia dla tej konkretnej aplikacji. W poniższej tabeli wymieniono niektóre parametry i łącza, aby rozpocząć dostrajanie wydajności dla każdej aplikacji.

| Obciążenie               | Parametr do ustawiania zadań                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [Usługa Spark w usłudze HDInsight](data-lake-store-performance-tuning-spark.md)  | <ul><li>Wykonawcy num</li><li>Wykonawca-pamięć</li><li>Rdzenie executor</li></ul> |
| [Gałąź na HDInsight](data-lake-store-performance-tuning-hive.md)    | <ul><li>hive.tez.container.size</li></ul>         |
| [MapReduce na HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.zmniejsza</li></ul> |
| [Usługa Storm w usłudze HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Liczba procesów roboczych</li><li>Liczba wystąpień wykonawcy wylewki</li><li>Liczba wystąpień wykonawcy śrub </li><li>Liczba zadań wylewki</li><li>Liczba zadań śrubowych</li></ul>|

## <a name="see-also"></a>Zobacz też

* [Omówienie usługi Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Rozpoczynanie pracy z usługą Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
