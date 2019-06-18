---
title: Usługa Azure Data Lake Storage Gen1 wydajności, wytyczne dotyczące dostosowywania | Dokumentacja firmy Microsoft
description: Usługa Azure Data Lake Storage Gen1 wydajności, wytyczne dotyczące dostosowywania
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: cgronlun
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: stewu
ms.openlocfilehash: a8a50db5ece242bc00a28e66e21c863388950d6f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61437642"
---
# <a name="tuning-azure-data-lake-storage-gen1-for-performance"></a>Dostosowywanie usługi Azure Data Lake Storage Gen1 wydajności

Azure Data Lake Storage Gen1 obsługę operacji We/Wy o znacznym wykorzystaniu przenoszenia danych i analizy o wysokiej przepływności.  Data Lake Storage Gen1 przy użyciu wszystkich dostępnych przepływności — ilość danych, który może odczytać lub zapisywane na sekundę — jest ważne, aby uzyskać najlepszą wydajność.  To jest osiągane, wykonując dowolną liczbę operacji odczytu i zapisu w sposób równoległy, jak to możliwe.

![Data Lake Storage Gen1 wydajności](./media/data-lake-store-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen1 można skalować w celu zapewnienia niezbędnych przepływności dla wszystkich scenariuszy analizy. Domyślnie konta Data Lake Storage Gen1 zapewnia automatycznie wystarczającej przepływności, aby zaspokoić potrzeby szerokiej kategorii przypadków użycia. W przypadkach, w których klienci napotkania domyślny limit można skonfigurować konta Data Lake Storage Gen1 zapewnić wyższą przepływność, kontaktując się z pomocą techniczną firmy Microsoft.

## <a name="data-ingestion"></a>Wprowadzanie danych

Przetwarzanie danych z systemu źródłowego do Data Lake Storage Gen1, należy wziąć pod uwagę, że sprzętu źródłowego, sprzęt sieciowy źródła i łączność sieciową Data Lake Storage Gen1 mogą być wąskie gardło.  

![Data Lake Storage Gen1 wydajności](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

Należy upewnić się, że przenoszenie danych nie ma wpływu tych czynników.

### <a name="source-hardware"></a>Źródło sprzętu

Czy używasz maszyn wirtualnych lub maszyn lokalnych na platformie Azure, należy dokładnie wybrać odpowiedni sprzęt. Sprzęt dysku źródłowego Preferuj dyski SSD na dyskach HDD, a następnie wybierz sprzętu dysku z jednostkami szybciej. Dla źródła sprzęt sieciowy używać jak najszybciej kart sieciowych.  Na platformie Azure firma Microsoft zaleca D14 maszyn wirtualnych platformy Azure, które mają odpowiednio zaawansowane dysku i sprzęt sieciowy.

### <a name="network-connectivity-to-data-lake-storage-gen1"></a>Połączenie sieciowe z Gen1 Lake magazynu danych

Połączenie sieciowe między źródła danych i Data Lake Storage Gen1 czasami może być wąskie gardło. Gdy źródło danych działa lokalnie, należy rozważyć użycie dedykowanego Połącz z [usługi Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) . Jeśli źródło danych znajduje się na platformie Azure, wydajność będzie najlepiej, gdy dane znajdują się w tym samym regionie platformy Azure, co konto usługi Data Lake Storage Gen1.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Konfigurowanie narzędzi pozyskiwanie danych do maksymalnego przetwarzania równoległego

Gdy zostały rozwiązane sprzętu źródłowego i sieci połączenia gardła powyżej, można przystąpić do konfigurowania narzędzia do wprowadzania. Poniższa tabela zawiera podsumowanie ustawień klucza dla kilku popularnych pozyskiwania narzędzi i udostępnia szczegółowe Dostosowywanie artykuły ich wydajności.  Aby dowiedzieć się więcej na temat narzędzia do użycia dla danego scenariusza, odwiedź [artykułu](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios).

| Narzędzie               | Ustawienia     | Więcej szczegółów                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| PowerShell       | PerFileThreadCount, ConcurrentFileCount |  [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-powershell) |
| Narzędzia AdlCopy    | Usługa Azure Data Lake Analytics jednostki  |   [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob#performance-considerations-for-using-adlcopy)         |
| DistCp            | -m (mapowanie)   | [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-wasb-distcp#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies    | [Link](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | FS.Azure.Block.size, -m (mapowanie)    |   [Link](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Struktury zestawu danych

Gdy dane są przechowywane w Data Lake Storage Gen1, rozmiar pliku, liczbę plików i struktury folderów mają wpływ na wydajność.  W poniższej sekcji opisano najlepsze rozwiązania w tych obszarach.  

### <a name="file-size"></a>Rozmiar pliku

Zazwyczaj aparatów analizy, takich jak HDInsight i Azure Data Lake Analytics ma obciążenie każdego pliku.  Jeśli dane są przechowywane jako wielu małych plików, może to negatywnie wpłynąć na wydajność.  

Ogólnie rzecz biorąc organizowania danych w większe pliki o rozmiarze w celu zapewnienia lepszej wydajności.  Jako ogólną regułę można przyjąć Zorganizuj zestawy danych w plikach 256MB lub większy. W niektórych przypadkach, takich jak obrazy i danych binarnych nie jest możliwe do ich przetwarzania równoległego.  W takich przypadkach zalecane jest przechowywanie plików poszczególnych mniej niż 2GB.

Czasami potoków danych w ograniczonym zakresie wpływać nieprzetworzone dane, które zawierają wiele małych plików.  Zalecane jest "inspirować" procesu, który generuje większych plikach w celu używania na potrzeby transmisji.

### <a name="organizing-time-series-data-in-folders"></a>Organizowanie danych szeregów czasowych w folderach

W przypadku obciążeń programów Hive i ADLA oczyszczania partycji danych szeregów czasowych może pomóc odczytać tylko podzestaw danych, co zwiększa wydajność zapytań.    

Potoki, które pozyskiwania danych szeregów czasowych, często umieścić swoje pliki z bardzo ze strukturą nazewnictwa dla plików i folderów. Poniżej przedstawiono przykład bardzo często, których firma Microsoft, do których znajdują się dane, które są skonstruowane według daty:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Zauważ, że informacje daty/godziny są wyświetlane zarówno jako folderów, jak i w nazwie pliku.

Dla daty i godziny Oto typowy wzorzec

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Ponownie wybór za pomocą folderu i pliku organizacji powinien optymalizować generowany dla większych plików i uzasadnioną liczbę plików w każdym folderze.

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Optymalizowanie zadań intensywnie korzystających z we/wy dla obciążeń usługi Hadoop i Spark w HDInsight

Zadania można podzielić na następujące trzy kategorie:

* **Mocy procesora CPU.**  Te zadania mają obliczeń długie czasy minimalny czas operacji We/Wy.  Przykłady obejmują usługi machine learning i zadania przetwarzania języka naturalnego.  
* **O znacznym wykorzystaniu pamięci.**  Te zadania używać dużej ilości pamięci.  Przykłady obejmują PageRank i zadania analizy w czasie rzeczywistym.  
* **Znaczne wykorzystanie mocy operacji We/Wy.**  Te zadania spędzają większość czasu wykonywania operacji We/Wy.  Typowym przykładem jest zadanie kopiowania, które tylko operacji odczytu i zapisu.  Przykładami innych zadań przygotowywania danych, które odczytują dużą ilość danych, wykonuje pewne przekształcania danych i następnie zapisuje dane z powrotem do magazynu.  

Poniższe wskazówki ma zastosowanie tylko do zadań intensywnie korzystających z we/wy.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Zagadnienia ogólne dotyczące klastra usługi HDInsight

* **Wersje HDInsight.** Aby uzyskać najlepszą wydajność należy użyć najnowszej wersji usługi HDInsight.
* **Regiony.** W tym samym regionie co klaster HDInsight, należy umieścić konta Data Lake Storage Gen1.  

Klaster usługi HDInsight składa się z dwoma węzłami głównymi i niektóre węzły procesu roboczego. Każdy węzeł procesu roboczego zawiera określonej liczby rdzeni i pamięci, która jest określana przez typ maszyny Wirtualnej.  W przypadku uruchamiania zadania, YARN jest moduł negocjowania zasobu, który przydziela dostępnej pamięci i rdzeni do utworzenia kontenerów.  Każdy kontener uruchamia zadania wymagane do ukończenia zadania.  Kontenery są uruchamiane równolegle w celu szybkiego przetwarzania zadań. W związku z tym lepsza wydajność uruchamiając dowolną liczbę kontenerów równoległych, jak to możliwe.

Istnieją trzy warstwy w klastrze usługi HDInsight, który może być dostosowane do liczbę kontenerów i wykorzystać wszystkie dostępne przepływności.  

* **W warstwie fizycznej**
* **Warstwy usługi YARN**
* **Obciążenia warstwy**

### <a name="physical-layer"></a>W warstwie fizycznej

**Uruchom klaster przy użyciu więcej węzłów i/lub większy rozmiar maszyn wirtualnych.**  Większego klastra umożliwią uruchamianie większej liczbie kontenerów usługi YARN, jak pokazano na poniższej ilustracji.

![Data Lake Storage Gen1 wydajności](./media/data-lake-store-performance-tuning-guidance/VM.png)

**Maszyny wirtualne za pomocą większe obciążenie przepustowości sieci.**  Przepustowość sieci może być "wąskie gardło", jeśli występuje mniej przepustowości sieci niż Data Lake Storage Gen1 przepływności.  Różnych maszyn wirtualnych mają różne rozmiary przepustowości sieci.  Wybierz maszynę Wirtualną — typ, który ma największy przepustowości sieci.

### <a name="yarn-layer"></a>Warstwy usługi YARN

**Użyj mniejszej kontenery usługi YARN.**  Zmniejsz rozmiar każdego kontenera YARN, aby utworzyć większej liczbie kontenerów przy użyciu tej samej ilości zasobów.

![Data Lake Storage Gen1 wydajności](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

W zależności od obciążenia zawsze będą minimalny rozmiar kontenera YARN, które są potrzebne. W przypadku wybrania zbyt mały kontenera, Twoje zadania będą uruchamiane problemy braku pamięci. Zazwyczaj kontenery usługi YARN nie powinien być mniejszy niż 1GB. Jest to częściej można zobaczyć kontenery usługi YARN 3GB. Dla niektórych obciążeń może być konieczne większych kontenery usługi YARN.  

**Zwiększ rdzeni kontenera YARN.**  Zwiększenie liczby rdzeni przydzielonych do każdego kontenera, aby zwiększyć liczbę równoległych zadań podrzędnych, które są uruchamiane w każdym kontenerze.  Działa to w przypadku aplikacji, takich jak Spark, których uruchamianie wielu zadań na kontener.  Dla aplikacji, takich jak Hive uruchamianych jednym wątkiem w każdym kontenerze lepiej jest zapewnienie większej liczbie kontenerów, a nie rdzeni na kontener.

### <a name="workload-layer"></a>Obciążenia warstwy

**Użyj wszystkich dostępnych kontenerów.**  Ustaw liczbę zadań, aby być równa lub większa niż liczba dostępnych kontenerów, aby wszystkie zasoby są wykorzystywane.

![Data Lake Storage Gen1 wydajności](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**Zadania podrzędne zakończone niepowodzeniem są kosztowne.** Jeśli każde zadanie zawiera dużą ilość danych do przetwarzania, następnie niepowodzenia zadania powoduje kosztowne ponownych prób.  Dlatego zaleca się tworzenie większej liczby zadań, z których każdy przetwarza niewielką ilość danych.

Oprócz ogólnych wytycznych powyżej każda aplikacja ma różne parametry można dostrajanie dla tej konkretnej aplikacji. W poniższej tabeli przedstawiono niektóre z parametrów i połączeń, aby zacząć korzystać z wydajności automatycznego dostrajania dla każdej aplikacji.

| Obciążenie               | Parametr, aby ustawić zadania                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [Spark on HDInsight](data-lake-store-performance-tuning-spark.md)       | <ul><li>Liczba programów przetwarzających</li><li>Pamięć funkcji wykonawczej</li><li>Liczba rdzeni funkcji wykonawczej</li></ul> |
| [Hive on HDInsight](data-lake-store-performance-tuning-hive.md)    | <ul><li>hive.tez.container.size</li></ul>         |
| [MapReduce na HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduces</li></ul> |
| [System STORM w HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Liczba procesów roboczych</li><li>Liczba wystąpień funkcji wykonawczej spout</li><li>Liczba wystąpień funkcji wykonawczej elementu bolt </li><li>Liczba zadań spout</li><li>Liczba zadań elementu bolt</li></ul>|

## <a name="see-also"></a>Zobacz także
* [Omówienie usługi Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Rozpoczynanie pracy z usługą Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
