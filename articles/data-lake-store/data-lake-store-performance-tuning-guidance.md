---
title: Dostosowywanie wskazówki dotyczące wydajności usługi Azure Data Lake Store | Dokumentacja firmy Microsoft
description: Dostosowywanie wskazówki dotyczące wydajności usługi Azure Data Lake Store
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: cgronlun
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/30/2017
ms.author: stewu
ms.openlocfilehash: aa803e823eb3096ea785f1f912293cae82c24b8d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="tuning-azure-data-lake-store-for-performance"></a>Dostrajanie wydajności usługi Azure Data Lake Store

Data Lake Store obsługuje wysokiej przepustowości dla analizy intensywne operacje We/Wy i przenoszenia danych.  W usłudze Azure Data Lake Store przy użyciu wszystkich dostępnych przepływności — ilość danych, które mogły być odczytywane i zapisywane na sekundę — ważne jest, aby uzyskać najlepszą wydajność.  To jest osiągane, wykonując dowolną liczbę operacji odczytu i zapisu równoległe, jak to możliwe.

![Wydajność usługi Data Lake Store](./media/data-lake-store-performance-tuning-guidance/throughput.png)

Azure Data Lake Store można skalować, podaj przepływności niezbędne dla wszystkich scenariuszy analizy. Domyślnie konto usługi Azure Data Lake Store zapewnia automatycznie za mało przepływności na potrzeby szerokiej kategorii przypadków użycia. W przypadkach, gdy klienci funkcjonowaniem domyślny limit można skonfigurować na koncie ADLS zapewnienie więcej przepustowości, kontaktując się z pomocą techniczną firmy Microsoft.

## <a name="data-ingestion"></a>Wprowadzanie danych

Podczas pobierania danych z systemu źródłowego do ADLS, należy wziąć pod uwagę, że sprzętu źródłowego, sprzęt sieciowy źródła i łączność sieciową ADLS może być wąskie gardło.  

![Wydajność usługi Data Lake Store](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

Jest ważne upewnić się, że ruch danych nie ma wpływu na te czynniki.

### <a name="source-hardware"></a>Źródło sprzętu

Czy korzystasz z maszyny lokalnej lub maszyn wirtualnych na platformie Azure, należy wybrać uważnie odpowiedni sprzęt. Sprzętu z dysku źródłowego wolą dysków SSD dyski twarde i wybierz sprzętu dysku z jednostkami szybsze. Źródła sprzętu sieciowego należy używać jak najszybsze karty sieciowe.  Na platformie Azure firma Microsoft zaleca D14 maszynach wirtualnych platformy Azure, którym odpowiedniego zaawansowanym dysku i sprzęt sieciowy.

### <a name="network-connectivity-to-azure-data-lake-store"></a>Połączenie sieciowe z usługi Azure Data Lake Store

Połączenie sieciowe między źródła danych i usługi Azure Data Lake store może być czasem wąskie gardło. Jeśli źródło danych działa lokalnie, należy rozważyć użycie dedykowanego łącza z [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) . Jeśli źródło danych jest na platformie Azure, wydajność będzie najlepiej, gdy dane są tego samego regionu Azure Data Lake Store.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Konfigurowanie narzędzia wprowadzanie danych do maksymalnego paralelizacja

Po usunąć sprzętu źródła i wąskich gardeł łączności powyżej sieci, możesz przystąpić do konfigurowania narzędziami wprowadzanie. Poniższa tabela zawiera podsumowanie ustawień klucza dla kilku popularnych wprowadzanie narzędzi oraz zapewnia szczegółowe wydajność dostrajanie artykułów dla nich.  Aby dowiedzieć się więcej na temat które narzędzie do użycia na potrzeby danego scenariusza, odwiedź [artykułu](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios).

| Narzędzie               | Ustawienia     | Więcej informacji                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| PowerShell       | PerFileThreadCount, ConcurrentFileCount |  [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-powershell#performance-guidance-while-using-powershell) |
| AdlCopy    | Usługa Azure Data Lake Analytics jednostki  |   [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob#performance-considerations-for-using-adlcopy)         |
| Narzędzia DistCp            | -m (mapowanie)   | [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-wasb-distcp#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies    | [Link](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | FS.Azure.Block.size, -m (mapowanie)    |   [Link](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Struktury zestawu danych

Gdy dane są przechowywane w usłudze Data Lake Store, rozmiar pliku, liczbę plików i struktury folderów będzie mieć wpływ na wydajność.  W poniższej sekcji opisano najlepsze rozwiązania w następujących obszarach.  

### <a name="file-size"></a>Rozmiar pliku

Aparaty analizy, takie jak usługi HDInsight i usługą Azure Data Lake Analytics ma zwykle, obciążenie dla plików.  Jeśli dane są przechowywane jako wiele małych plików, może to negatywnie wpłynąć na wydajność.  

Ogólnie rzecz biorąc organizowania danych w większe pliki o rozmiarze w celu poprawy wydajności.  Jako zasadą organizowanie zestawów danych w plikach 256MB lub większy. W niektórych przypadkach, takich jak obrazy i danych binarnych nie jest możliwe do ich przetworzenia równolegle.  W takich przypadkach zaleca się zachowanie poszczególnych plików mniej niż 2GB.

Czasami potoki danych mają ograniczoną kontrolę nad nieprzetworzone dane, które zawiera wiele małych plików.  Zalecane jest "gotowania" procesu, który generuje większe pliki służące do podrzędnych aplikacji.  

### <a name="organizing-time-series-data-in-folders"></a>Organizowanie danych szeregów czasowych w folderach

W przypadku obciążeń Hive i ADLA partycji oczyszczania danych szeregu czasowego może pomóc niektórych kwerend odczytać tylko podzestaw danych, co zwiększa wydajność.    

Potoki, które pozyskiwania danych szeregu czasowego, często umieść ich pliki z bardzo strukturalnych nazewnictwa plików i folderów. Poniżej znajdują się bardzo typowym przykładem, który widzimy dla danych, które mają strukturę według daty:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Należy zauważyć, że informacje daty i godziny są wyświetlane folderów oraz w nazwie pliku.

Daty i godziny Oto wspólnego wzorca

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Ponownie wybór za pomocą folderu i pliku organizacji powinien optymalizować większych plików i uzasadnione liczba plików w każdym folderze.

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Optymalizacja znacznym zadania we/wy na obciążenie Hadoop i Spark w usłudze HDInsight

Zadania można podzielić na następujące trzy kategorie:

* **Procesora CPU.**  Te zadania mają obliczeń długie czasy wraz z minimalnym czasem we/wy.  Przykładami uczenie maszynowe i przetwarzania zadań języka naturalnego.  
* **Pamięci.**  Te zadania korzystają z dużej ilości pamięci.  Przykładami PageRank i zadania usługi analiza w czasie rzeczywistym.  
* **Operacji We/Wy.**  Te zadania spędzają większość czasu wykonywania operacji We/Wy.  Typowym przykładem jest zadanie kopiowania, które tylko do odczytu i zapisu.  Przykładami inne zadania przygotowywania danych, które dużą ilość danych do odczytu, przeprowadza niektórych transformacji danych, a następnie ponownie zapisuje dane do magazynu.  

Poniższe wskazówki ma zastosowanie tylko do zadań intensywne operacje We/Wy.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Ogólne zagadnienia dotyczące klastra usługi HDInsight

* **Wersje usługi HDInsight.** Aby uzyskać najlepszą wydajność należy używać najnowszej wersji usługi HDInsight.
* **Regiony.** Umieść usługi Data Lake Store, w tym samym regionie co klaster usługi HDInsight.  

Klaster usługi HDInsight składa się z dwóch węzłów głównych i niektóre węzły procesów roboczych. Każdego węzła procesu roboczego zawiera określonej liczby rdzeni i ilości pamięci, który jest określany przez typu maszyny Wirtualnej.  Podczas wykonywania zadania, YARN jest moduł negocjowania zasobów, przydzielanej dostępnej pamięci i rdzeni do utworzenia kontenerów.  Każdy kontener uruchamia zadania wymagane do ukończenia zadania.  Równolegle kontenerów do szybkiego przetwarzania zadań. W związku z tym lepsza wydajność, uruchamiając dowolną liczbę kontenerów równoległych, jak to możliwe.

Istnieją trzy warstwy w ramach klastra usługi HDInsight, który można przedstawić aby zwiększyć liczbę kontenerów i używać wszystkich dostępnych przepływności.  

* **W warstwie fizycznej**
* **YARN warstwy**
* **Obciążenie warstwy**

### <a name="physical-layer"></a>W warstwie fizycznej

**Uruchomienie klastra z więcej węzłów i/lub większy rozmiar maszyn wirtualnych.**  Większego klastra umożliwi uruchomienie więcej kontenerów YARN, jak pokazano na rysunku poniżej.

![Wydajność usługi Data Lake Store](./media/data-lake-store-performance-tuning-guidance/VM.png)

**Użyj maszyn wirtualnych z większej przepustowości sieci.**  Przepustowość sieci może być wąskie gardło, jeśli istnieje mniej przepustowości sieci niż przepływności usługi Data Lake Store.  Różnych maszyn wirtualnych będą mieć różne rozmiary przepustowości sieci.  Wybierz maszyny Wirtualnej — typ, który ma największy przepustowości sieci.

### <a name="yarn-layer"></a>YARN warstwy

**Używanie mniejszych kontenerów YARN.**  Zmniejsz rozmiar każdego kontenera YARN, aby utworzyć więcej kontenerów za pomocą tego samego ilość zasobów.

![Wydajność usługi Data Lake Store](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

W zależności od obciążenia będą zawsze miały minimalny rozmiar kontenera YARN, który jest wymagany. W przypadku wybrania za mały kontener Twoje zadania będą uruchamiane w problemów braku pamięci. Zazwyczaj kontenerów YARN nie powinien być mniejszy niż 1GB. Jest to częściej można zobaczyć kontenery YARN 3GB. Dla niektórych zadań może być konieczne większe kontenery YARN.  

**Zwiększ rdzeni w jednym YARN kontenera.**  Zwiększ liczbę rdzeni przydzielone do każdego kontenera, aby zwiększyć liczbę zadań uruchamianych w każdego kontenera.  Działa to w przypadku aplikacji, takich jak Spark, których uruchamianie wielu zadań na kontenera.  Dla aplikacji, takich jak Hive uruchamianych jednym wątku w poszczególnych kontenerach lepiej jest kontenery więcej niż większej liczby rdzeni na kontenera.   

### <a name="workload-layer"></a>Obciążenie warstwy

**Użyj wszystkich dostępnych kontenerów.**  Ustaw liczbę zadań, jakie mają być równa lub większa niż liczba dostępnych kontenerów, tak aby wszystkie zasoby są wykorzystywane.

![Wydajność usługi Data Lake Store](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**Zadania zakończone niepowodzeniem są kosztowne.** Jeśli każde zadanie ma dużą ilość danych do przetwarzania, następnie niepowodzenia zadania powoduje kosztowne ponów próbę.  W związku z tym warto utworzyć więcej zadań, z których każdy przetwarza niewielką ilość danych.

Oprócz ogólne wytyczne powyżej każda aplikacja ma różnych parametry dostępne do dopasowywania dla tej konkretnej aplikacji. W poniższej tabeli przedstawiono niektóre z tych parametrów i łącza, aby zacząć korzystać z wydajności dostrajania dla każdej aplikacji.

| Obciążenie               | Aby ustawić zadania                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [Platforma Spark w HDInisight](data-lake-store-performance-tuning-spark.md)       | <ul><li>Liczba modułów</li><li>Moduł wykonujący pamięci</li><li>Moduł wykonujący rdzeni</li></ul> |
| [Hive w usłudze HDInsight](data-lake-store-performance-tuning-hive.md)    | <ul><li>hive.tez.container.size</li></ul>         |
| [MapReduce w usłudze HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>mapreduce.map.Memory</li><li>Mapreduce.job.Maps</li><li>mapreduce.Reduce.Memory</li><li>Mapreduce.job.reduces</li></ul> |
| [STORM w usłudze HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Liczba procesów roboczych</li><li>Liczba wystąpień Moduł wykonujący spout</li><li>Liczba wystąpień Moduł wykonujący bolt </li><li>Liczba zadań spout</li><li>Liczba zadań bolt</li></ul>|

## <a name="see-also"></a>Zobacz także
* [Omówienie usługi Azure Data Lake Store](data-lake-store-overview.md)
* [Rozpoczynanie pracy z usługą Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
