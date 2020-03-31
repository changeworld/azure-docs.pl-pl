---
title: Skalowalna nauka o danych za pomocą usługi Azure Data Lake — proces nauki o danych zespołowych
description: Jak używać usługi Azure Data Lake do eksploracji danych i zadań klasyfikacji binarnej w zestawie danych.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9409f14b20684afa1a39d45e663ff316f405cc97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717912"
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Skalowalna nauka o danych za pomocą usługi Azure Data Lake: kompleksowa przewodnik
W tym przewodniku pokazano, jak używać usługi Azure Data Lake do eksploracji danych i zadań klasyfikacji binarnej na próbce usługi taksówki NYC i zestawu danych taryfy, aby przewidzieć, czy wskazówka jest płatna przez taryfę. Przeprowadzi Cię przez kroki [procesu nauki o danych zespołu](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), end-to-end, od pozyskiwania danych do szkolenia modelu, a następnie do wdrożenia usługi sieci web, która publikuje model.

## <a name="technologies"></a>Technologie

Technologie te są używane w tym instruktażu.
* Azure Data Lake Analytics
* U-SQL i Visual Studio
* Python
* Azure Machine Learning
* Scripts


### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
Usługa [Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) ma wszystkie funkcje wymagane do ułatwienia analitykom danych przechowywania danych o dowolnym rozmiarze, kształcie i szybkości oraz przeprowadzania przetwarzania danych, zaawansowanej analizy i modelowania uczenia maszynowego z wysoką skalowalnością w opłacalny sposób.   Płacisz na podstawie za pracę, tylko wtedy, gdy dane są faktycznie przetwarzane. Usługa Azure Data Lake Analytics zawiera U-SQL, język, który łączy deklaratywny charakter SQL z ekspresyjną mocą języka C#, aby zapewnić skalowalne możliwości rozproszonych zapytań. Umożliwia przetwarzanie danych nieustrukturyzowanych przez zastosowanie schematu na odczyt, wstawianie logiki niestandardowej i funkcji zdefiniowanych przez użytkownika (UDF) i zawiera rozszerzalność, aby włączyć szczegółową kontrolę nad tym, jak wykonać na dużą skalę. Aby dowiedzieć się więcej na temat filozofii projektowania u-SQL, zobacz [Wpis w blogu programu Visual Studio.](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)

Usługa Data Lake Analytics jest również kluczową częścią pakietu Cortana Analytics Suite i współpracuje z usługą Azure SQL Data Warehouse, Power BI i Data Factory. Ta kombinacja zapewnia pełną chmurę dużych zbiorów danych i zaawansowaną platformę analityczną.

Ten przewodnik rozpoczyna się od opisu sposobu instalowania wymagań wstępnych i zasobów, które są potrzebne do wykonywania zadań procesu nauki o danych. Następnie przedstawiono kroki przetwarzania danych przy użyciu języka U-SQL i konkluduje, pokazując, jak używać języka Python i hive z usługi Azure Machine Learning Studio (klasyczny) do tworzenia i wdrażania modeli predykcyjnych.

### <a name="u-sql-and-visual-studio"></a>U-SQL i Visual Studio
W tym instruktażu zaleca się używanie programu Visual Studio do edytowania skryptów U-SQL w celu przetworzenia zestawu danych. Skrypty U-SQL są opisane w tym miejscu i podane w oddzielnym pliku. Proces obejmuje pozyskiwania, eksplorowania i próbkowania danych. Pokazano również, jak uruchomić zadanie skryptowe U-SQL z witryny Azure portal. Tabele gałęzi są tworzone dla danych w skojarzonym klastrze HDInsight, aby ułatwić tworzenie i wdrażanie modelu klasyfikacji binarnej w usłudze Azure Machine Learning Studio.

### <a name="python"></a>Python
Ten instruktaż zawiera również sekcję, która pokazuje, jak skompilować i wdrożyć model predykcyjny przy użyciu języka Python za pomocą usługi Azure Machine Learning Studio. Zapewnia notes Jupyter ze skryptami Języka Python dla kroków w tym procesie. Notes zawiera kod dla niektórych dodatkowych czynności inżynierskich funkcji i konstrukcji modeli, takich jak wieloklasowe modelowanie klasyfikacji i regresji oprócz modelu klasyfikacji binarnej opisanego w tym miejscu. Zadaniem regresji jest przewidywanie ilości końcówki na podstawie innych funkcji porad.

### <a name="azure-machine-learning"></a>Azure Machine Learning 
Usługa Azure Machine Learning Studio (klasyczna) służy do tworzenia i wdrażania modeli predykcyjnych przy użyciu dwóch metod: najpierw za pomocą skryptów języka Python, a następnie z tabelami hive w klastrze HDInsight (Hadoop).

### <a name="scripts"></a>Scripts
Tylko główne kroki są opisane w tym instruktażu. Możesz pobrać pełny **skrypt U-SQL** i **notebook Jupyter** z [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tych tematów należy mieć następujące właściwości:

* Subskrypcja platformy Azure. Jeśli jeszcze go nie masz, zobacz [Pobierz bezpłatną wersję próbną platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Zalecane] Visual Studio 2013 lub nowszych. Jeśli nie masz jeszcze jednej z tych wersji zainstalowanych, możesz pobrać bezpłatną wersję społecznościową ze [społeczności programu Visual Studio.](https://www.visualstudio.com/vs/community/)

> [!NOTE]
> Zamiast programu Visual Studio można również użyć witryny Azure portal do przesyłania zapytań usługi Azure Data Lake. Instrukcje dotyczące sposobu wykonywania tego problemu zarówno w programie Visual Studio, jak i w portalu w sekcji **"Przetwarzanie danych z u-SQL"** jest dostępne.
>
>


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Przygotowywanie środowiska nauki o danych dla usługi Azure Data Lake
Aby przygotować środowisko nauki o danych dla tego przewodnika, utwórz następujące zasoby:

* Usługa Azure Data Lake Storage (ADLS)
* Usługa Azure Data Lake Analytics (ADLA)
* Konto magazynu obiektów Blob platformy Azure
* Konto usługi Azure Machine Learning Studio (klasyczne)
* Narzędzia usługi Azure Data Lake dla programu Visual Studio (zalecane)

W tej sekcji przedstawiono instrukcje dotyczące tworzenia każdego z tych zasobów. Jeśli zdecydujesz się użyć tabel hive z usługi Azure Machine Learning, zamiast Python, do tworzenia modelu, należy również aprowizować klastra HDInsight (Hadoop). Niniejsza alternatywna procedura opisana w sekcji wariant 2.


> [!NOTE]
> **Usługa Azure Data Lake Store** można utworzyć oddzielnie lub podczas tworzenia usługi Azure Data Lake **Analytics** jako magazynu domyślnego. Instrukcje są odwoływane do tworzenia każdego z tych zasobów oddzielnie, ale konto magazynu usługi Data Lake nie muszą być tworzone oddzielnie.
>
>

### <a name="create-an-azure-data-lake-storage"></a>Tworzenie magazynu usługi Azure Data Lake


Utwórz usługę ADLS z [witryny Azure portal](https://portal.azure.com). Aby uzyskać szczegółowe informacje, zobacz [Tworzenie klastra USŁUGI HDInsight w magazynie usługi Data Lake store przy użyciu witryny Azure portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Należy skonfigurować tożsamość usługi AAD klastra w bloku **DataSource** w bloku **konfiguracji opcjonalnej** opisane tam.

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Tworzenie konta usługi Azure Data Lake Analytics
Utwórz konto ADLA z [witryny Azure Portal](https://portal.azure.com). Aby uzyskać szczegółowe informacje, zobacz [Samouczek: wprowadzenie do usługi Azure Data Lake Analytics przy użyciu witryny Azure portal.](../../data-lake-analytics/data-lake-analytics-get-started-portal.md)

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Tworzenie konta magazynu obiektów Blob platformy Azure
Utwórz konto magazynu obiektów Blob platformy Azure z [witryny Azure portal](https://portal.azure.com). Aby uzyskać szczegółowe informacje, zobacz sekcję Tworzenie konta magazynu w obszarze [Informacje o kontach usługi Azure Storage](../../storage/common/storage-create-storage-account.md).

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-classic-account"></a>Konfigurowanie konta usługi Azure Machine Learning Studio (klasycznego)
Zarejestruj się/w usłudze Azure Machine Learning Studio (klasyczny) ze strony [studio usługi Azure Machine Learning.](https://azure.microsoft.com/services/machine-learning/) Kliknij przycisk **Rozpocznij teraz,** a następnie wybierz "Wolny obszar roboczy" lub "Standardowy obszar roboczy". Teraz możesz utworzyć eksperymenty w studiu usługi Azure Machine Learning.

### <a name="install-azure-data-lake-tools-recommended"></a>Instalowanie narzędzi usługi Azure Data Lake [Zalecane]
Zainstaluj narzędzia usługi Azure Data Lake Tools dla swojej wersji programu Visual Studio z [usługi Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

Po zakończeniu instalacji otwórz program Visual Studio. U góry powinna zostać wyświetlna karta Usługa Data Lake. Zasoby platformy Azure powinny pojawić się w lewym panelu po zalogowaniu się do konta platformy Azure.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>Zestaw danych NYC Taxi Trips
Używany w tym miejscu zestaw danych jest publicznie dostępnym zbiorem danych — [zestawem danych NYC Taxi Trips.](https://www.andresmh.com/nyctaxitrips/) Dane NYC Taxi Trip składają się z około 20 GB skompresowanych plików CSV (~ 48 GB nieskompresowanych), rejestrując ponad 173 miliony pojedynczych podróży i opłaty zapłacone za każdą podróż. Każdy rekord podróży zawiera miejsca odbioru i nadsyłania, anonimowy numer licencji hack (kierowcy) i numer medalionu (unikalny identyfikator taksówki). Dane obejmują wszystkie podróże w 2013 r. i są dostępne w następujących dwóch zestawach danych dla każdego miesiąca:

Csv "trip_data" zawiera szczegóły podróży, takie jak liczba pasażerów, punkty odbioru i wysiadania, czas trwania podróży i długość podróży. Oto kilka przykładowych rekordów:

       medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868



"trip_fare" CSV zawiera szczegóły taryfy zapłaconej za każdą podróż, takie jak rodzaj płatności, kwota taryfy, dopłaty i podatki, napiwki i opłaty drogowe oraz całkowita kwota zapłacona. Oto kilka przykładowych rekordów:

       medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Ten jedyny w\_swym\_rodzaju klucz wobec przyłączyć się podróż dane\_i podróż\_taryfa jest składający się z ten kolejne trzy pola: medalion, siekać upoważnić i okazja okazja. Nieprzetworzone pliki CSV są dostępne z obiektu blob usługi Azure Storage. Skrypt U-SQL dla tego sprzężenia znajduje się w sekcji [Dołącz do tabel podróży i taryf.](#join)

## <a name="process-data-with-u-sql"></a>Przetwarzanie danych za pomocą języka U-SQL
Zadania przetwarzania danych zilustrowane w tej sekcji obejmują pozyskiwania, sprawdzanie jakości, eksplorowanie i próbkowanie danych. Pokazano również, jak dołączyć do tabel podróży i taryf. W ostatniej sekcji przedstawiono uruchamianie zadania skryptowego U-SQL z witryny Azure portal. Oto linki do każdej podsekcji:

* [Pozyskiwania danych: odczyt w danych z publicznego obiektu blob](#ingest)
* [Kontrole jakości danych](#quality)
* [Eksploracja danych](#explore)
* [Dołącz do stołów podróży i taryf](#join)
* [Pobieranie próbek danych](#sample)
* [Uruchamianie zadań U-SQL](#run)

Skrypty U-SQL są opisane w tym miejscu i podane w oddzielnym pliku. Możesz pobrać pełne **skrypty U-SQL** z [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Aby wykonać U-SQL, Otwórz program Visual Studio, kliknij przycisk **Plik --> Nowy --> Project**, wybierz nazwę **U-SQL Project**, nazwę i zapisz go w folderze.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> Istnieje możliwość użycia usługi Azure Portal do wykonywania U-SQL zamiast programu Visual Studio. Możesz przejść do zasobu usługi Azure Data Lake Analytics w portalu i przesyłać zapytania bezpośrednio, jak pokazano na poniższym rysunku:
>
>

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="data-ingestion-read-in-data-from-public-blob"></a><a name="ingest"></a>Pobędzenie danych: odczyt danych z publicznego obiektu blob

Lokalizacja danych w obiekcie blob platformy Azure odwołuje się jako **wasb://container\_\_konto magazynu obiektów\@\_\_blob nazwy name.blob.core.windows.net/blob_name** i mogą być wyodrębniane za pomocą **extractors.Csv()**. Zastąp własną nazwę kontenera i nazwę\_\@konta\_magazynu\_w następujących skryptach dla nazwy konta magazynu\_obiektów blob nazwy kontenera w adresie wasb. Ponieważ nazwy plików są w tym samym formacie, możliwe jest użycie **danych\_\_\{\*\}podróży .csv** do odczytu we wszystkich 12 plikach podróży.

    ///Read in Trip data
    @trip0 =
        EXTRACT
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string
    // This is reading 12 trip data from blob
    FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
    USING Extractors.Csv();

Ponieważ w pierwszym wierszu znajdują się nagłówki, należy usunąć nagłówki i zmienić typy kolumn na odpowiednie. Przetwarzane dane można zapisać w usłudze Azure Data Lake Storage za pomocą **pliku swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ lub konta magazynu obiektów Blob platformy Azure przy użyciu **pliku wasb://container_name\@blob_storage_account_name.blob.core.windows.net/blob_name**.

    // change data types
    @trip =
        SELECT
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        DateTime.Parse(pickup_datetime) AS pickup_datetime,
        DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
        Int32.Parse(passenger_count) AS passenger_count,
        Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
        Double.Parse(trip_distance) AS trip_distance,
        (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
        (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
        (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
        (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
    FROM @trip0
    WHERE medallion != "medallion";

    ////output data to ADL
    OUTPUT @trip
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_trip.csv"
    USING Outputters.Csv();

    ////Output data to blob
    OUTPUT @trip
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
    USING Outputters.Csv();

Podobnie można odczytać w zestawach danych taryfy. Kliknij prawym przyciskiem myszy usługę Azure Data Lake Storage, możesz przeglądać dane w **witrynie Azure portal — > Eksploratora danych** lub **Eksploratora plików** w programie Visual Studio.

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="data-quality-checks"></a><a name="quality"></a>Kontrole jakości danych
Po odczytywanie tabel taryf i taryf, kontrole jakości danych można przeprowadzić w następujący sposób. Wynikowe pliki CSV mogą być dane wyjściowe do magazynu obiektów Blob platformy Azure lub usługi Azure Data Lake Storage.

Znajdź liczbę medalionów i unikalną liczbę medalionów:

    ///check the number of medallions and unique number of medallions
    @trip2 =
        SELECT
        medallion,
        vendor_id,
        pickup_datetime.Month AS pickup_month
        FROM @trip;

    @ex_1 =
        SELECT
        pickup_month,
        COUNT(medallion) AS cnt_medallion,
        COUNT(DISTINCT(medallion)) AS unique_medallion
        FROM @trip2
        GROUP BY pickup_month;
        OUTPUT @ex_1
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
    USING Outputters.Csv();

Znajdź te medaliony, które miały ponad 100 podróży:

    ///find those medallions that had more than 100 trips
    @ex_2 =
        SELECT medallion,
               COUNT(medallion) AS cnt_medallion
        FROM @trip2
        //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
        GROUP BY medallion
        HAVING COUNT(medallion) > 100;
        OUTPUT @ex_2
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
    USING Outputters.Csv();

Znajdź te nieprawidłowe rekordy pod względem pickup_longitude:

    ///find those invalid records in terms of pickup_longitude
    @ex_3 =
        SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
        FROM @trip
        WHERE
        pickup_longitude <- 90 OR pickup_longitude > 90;
        OUTPUT @ex_3
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
    USING Outputters.Csv();

Znajdź brakujące wartości dla niektórych zmiennych:

    //check missing values
    @res =
        SELECT *,
               (medallion == null? 1 : 0) AS missing_medallion
        FROM @trip;

    @trip_summary6 =
        SELECT
            vendor_id,
        SUM(missing_medallion) AS medallion_empty,
        COUNT(medallion) AS medallion_total,
        COUNT(DISTINCT(medallion)) AS medallion_total_unique
        FROM @res
        GROUP BY vendor_id;
    OUTPUT @trip_summary6
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
    USING Outputters.Csv();



### <a name="data-exploration"></a><a name="explore"></a>Eksploracja danych
Wykonaj eksplorację danych za pomocą następujących skryptów, aby lepiej zrozumieć dane.

Znajdź rozkład przechylonych i nienapiętych wycieczek:

    ///tipped vs. not tipped distribution
    @tip_or_not =
        SELECT *,
               (tip_amount > 0 ? 1: 0) AS tipped
        FROM @fare;

    @ex_4 =
        SELECT tipped,
               COUNT(*) AS tip_freq
        FROM @tip_or_not
        GROUP BY tipped;
        OUTPUT @ex_4
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
    USING Outputters.Csv();

Znajdź rozkład kwoty końcówki z wartościami granicznymi: 0, 5, 10 i 20 dolarów.

    //tip class/range distribution
    @tip_class =
        SELECT *,
               (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
        FROM @fare;
    @ex_5 =
        SELECT tip_class,
               COUNT(*) AS tip_freq
        FROM @tip_class
        GROUP BY tip_class;
        OUTPUT @ex_5
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
    USING Outputters.Csv();

Znajdź podstawowe statystyki odległości podróży:

    // find basic statistics for trip_distance
    @trip_summary4 =
        SELECT
            vendor_id,
            COUNT(*) AS cnt_row,
            MIN(trip_distance) AS min_trip_distance,
            MAX(trip_distance) AS max_trip_distance,
            AVG(trip_distance) AS avg_trip_distance
        FROM @trip
        GROUP BY vendor_id;
    OUTPUT @trip_summary4
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
    USING Outputters.Csv();

Znajdź percentyle odległości podróży:

    // find percentiles of trip_distance
    @trip_summary3 =
        SELECT DISTINCT vendor_id AS vendor,
                        PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
        FROM @trip;
       // group by vendor_id;
    OUTPUT @trip_summary3
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
    USING Outputters.Csv();


### <a name="join-trip-and-fare-tables"></a><a name="join"></a>Dołącz do stołów podróży i taryf
Do stołów wycieczkowych i taryfowych można dołączyć medalionem, hack_license i pickup_time.

    //join trip and fare table

    @model_data_full =
    SELECT t.*,
    f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
    (f.tip_amount > 0 ? 1: 0) AS tipped,
    (f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @trip AS t JOIN  @fare AS f
    ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
    WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

    //// output to blob
    OUTPUT @model_data_full
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
    USING Outputters.Csv();

    ////output data to ADL
    OUTPUT @model_data_full
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_7_full_data.csv"
    USING Outputters.Csv();


Dla każdego poziomu liczby pasażerów obliczyć liczbę rekordów, średnią kwotę napiwku, wariancję kwoty napiwku, procent przechylonych podróży.

    // contingency table
    @trip_summary8 =
        SELECT passenger_count,
               COUNT(*) AS cnt,
               AVG(tip_amount) AS avg_tip_amount,
               VAR(tip_amount) AS var_tip_amount,
               SUM(tipped) AS cnt_tipped,
               (float)SUM(tipped)/COUNT(*) AS pct_tipped
        FROM @model_data_full
        GROUP BY passenger_count;
        OUTPUT @trip_summary8
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
    USING Outputters.Csv();


### <a name="data-sampling"></a><a name="sample"></a>Pobieranie próbek danych
Najpierw losowo wybierz 0,1% danych z połączonej tabeli:

    //random select 1/1000 data for modeling purpose
    @addrownumberres_randomsample =
    SELECT *,
            ROW_NUMBER() OVER() AS rownum
    FROM @model_data_full;

    @model_data_random_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_randomsample
    WHERE rownum % 1000 == 0;

    OUTPUT @model_data_random_sample_1_1000
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
    USING Outputters.Csv();

Następnie wykonaj próbkowanie stratified przez zmienną binarną tip_class:

    //stratified random select 1/1000 data for modeling purpose
    @addrownumberres_stratifiedsample =
    SELECT *,
            ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
    FROM @model_data_full;

    @model_data_stratified_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_stratifiedsample
    WHERE rownum % 1000 == 0;
    //// output to blob
    OUTPUT @model_data_stratified_sample_1_1000
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv();
    ////output data to ADL
    OUTPUT @model_data_stratified_sample_1_1000
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv();


### <a name="run-u-sql-jobs"></a><a name="run"></a>Uruchamianie zadań U-SQL
Po edycji skryptów U-SQL można przesłać je do serwera przy użyciu konta usługi Azure Data Lake Analytics. Kliknij **pozycję Data Lake**, Submit **Job**, wybierz konto **Analytics**, wybierz **pozycję Równoległość**i kliknij przycisk **Prześlij.**

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

Po pomyślnym wykonaniu zadania stan zadania jest wyświetlany w programie Visual Studio do monitorowania. Po zakończeniu zadania można nawet odtworzyć proces wykonywania zadania i dowiedzieć się o krokach wąskich gardeł, aby poprawić wydajność pracy. Można również przejść do witryny Azure portal, aby sprawdzić stan zadań U-SQL.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Teraz możesz sprawdzić pliki wyjściowe w magazynie obiektów Blob platformy Azure lub w witrynie Azure portal. Użyj stratified przykładowe dane dla naszego modelowania w następnym kroku.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Tworzenie i wdrażanie modeli w usłudze Azure Machine Learning
Dostępne są dwie opcje ściągania danych do usługi Azure Machine Learning w celu

* W pierwszej opcji należy użyć przykładowych danych, które zostały zapisane w usłudze Azure Blob (w kroku **próbkowania danych** powyżej) i używać języka Python do tworzenia i wdrażania modeli z usługi Azure Machine Learning.
* W drugiej opcji kwerendy danych w usłudze Azure Data Lake bezpośrednio przy użyciu zapytania hive. Ta opcja wymaga utworzenia nowego klastra HDInsight lub użycia istniejącego klastra HDInsight, w którym tabele hive wskazują dane NY Taxi w usłudze Azure Data Lake Storage.  Obie te opcje zostały omówione w poniższych sekcjach.

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Opcja 1: Tworzenie i wdrażanie modeli uczenia maszynowego za pomocą języka Python
Aby tworzyć i wdrażać modele uczenia maszynowego przy użyciu języka Python, utwórz notes Jupyter na komputerze lokalnym lub w usłudze Azure Machine Learning Studio. Notes Jupyter dostępny w [usłudze GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) zawiera pełny kod do eksplorowania, wizualizowania danych, inżynierii funkcji, modelowania i wdrażania. W tym artykule tylko modelowanie i wdrażanie są objęte.

### <a name="import-python-libraries"></a>Importowanie bibliotek języka Python
Aby uruchomić przykładowy notes Jupyter lub plik skryptu Pythona, potrzebne są następujące pakiety Pythona. Jeśli używasz usługi Azure Machine Learning Notebook, te pakiety zostały wstępnie zainstalowane.

    import pandas as pd
    from pandas import Series, DataFrame
    import numpy as np
    import matplotlib.pyplot as plt
    from time import time
    import pyodbc
    import os
    from azure.storage.blob import BlobService
    import tables
    import time
    import zipfile
    import random
    import sklearn
    from sklearn.linear_model import LogisticRegression
    from sklearn.cross_validation import train_test_split
    from sklearn import metrics
    from __future__ import division
    from sklearn import linear_model
    from azureml import services


### <a name="read-in-the-data-from-blob"></a>Odczytywanie danych z obiektu blob
* Ciąg połączenia

        CONTAINERNAME = 'test1'
        STORAGEACCOUNTNAME = 'XXXXXXXXX'
        STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
        BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
        blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
* Czytaj jako tekst

        t1 = time.time()
        data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
        t2 = time.time()
        print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))

  ![17](./media/data-lake-walkthrough/17-python_readin_csv.PNG)
* Dodawanie nazw kolumn i oddzielnych kolumn

        colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
        'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
        df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
* Zmienianie niektórych kolumn na numeryczne

        cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
        ,'tipped','tip_class','rownum']
        for col in cols_2_float:
            df1[col] = df1[col].astype(float)

### <a name="build-machine-learning-models"></a>Tworzenie modeli uczenia maszynowego
W tym miejscu można utworzyć model klasyfikacji binarnej, aby przewidzieć, czy podróż jest przechylony, czy nie. W notesie Jupyter można znaleźć inne dwa modele: klasyfikacja wieloklasowa i modele regresji.

* Najpierw musisz utworzyć fikcyjne zmienne, które mogą być używane w modelach scikit-learn

        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
* Tworzenie ramki danych do modelowania

        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])

        X = data.iloc[:,1:]
        Y = data.tipped
* Szkolenia i testy 60-40 split

        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
* Regresja logistyczna w zestawie szkoleniowym

        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)

       ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)
* Zestaw danych testowania wyników

        Y_test_pred = logit_fit.predict(X_test)
* Obliczanie danych ewaluacyjnych

        fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
        print fpr_train, tpr_train, thresholds_train

        fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred)
        print fpr_test, tpr_test, thresholds_test

        #AUC
        print metrics.auc(fpr_train,tpr_train)
        print metrics.auc(fpr_test,tpr_test)

        #Confusion Matrix
        print metrics.confusion_matrix(Y_train,Y_train_pred)
        print metrics.confusion_matrix(Y_test,Y_test_pred)

       ![c2](./media/data-lake-walkthrough/c2-py-logit-evaluation.PNG)

### <a name="build-web-service-api-and-consume-it-in-python"></a>Tworzenie interfejsu API usługi sieci Web i zużywanie go w języku Python
Chcesz operacjonalizacji modelu uczenia maszynowego po jego zbudowaniu. Binarny model logistyczny jest używany w tym miejscu jako przykład. Upewnij się, że wersja scikit-learn na komputerze lokalnym jest 0.15.1 (Usługa Azure Machine Learning Studio jest już co najmniej w tej wersji).

* Znajdź poświadczenia obszaru roboczego z ustawień usługi Azure Machine Learning Studio (klasyczne). W usłudze Azure Machine Learning Studio kliknij pozycję**Tokeny autoryzacji****nazw** -->  **ustawień** --> .

    ![c3](./media/data-lake-walkthrough/c3-workspace-id.PNG)

        workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
        auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

* Tworzenie usługi sieci Web

        @services.publish(workspaceid, auth_token)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(int) #0, or 1
        def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
            return logit_fit.predict(inputArray)
* Potrzymyuj poświadczenia usługi sieci Web

        url = predictNYCTAXI.service.url
        api_key =  predictNYCTAXI.service.api_key

        print url
        print api_key

        @services.service(url, api_key)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(float)
        def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            pass
* Wywołanie interfejsu API usługi sieci Web. Zazwyczaj poczekaj 5-10 sekund po poprzednim kroku.

        NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)

       ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>Opcja 2: Tworzenie i wdrażanie modeli bezpośrednio w usłudze Azure Machine Learning
Usługa Azure Machine Learning Studio (klasyczna) może odczytywać dane bezpośrednio z usługi Azure Data Lake Storage, a następnie być używana do tworzenia i wdrażania modeli. Takie podejście używa tabeli gałęzi, która wskazuje w usłudze Azure Data Lake Storage. Oddzielny klaster usługi Azure HDInsight musi być aprowidywu dla tabeli hive. 

### <a name="create-an-hdinsight-linux-cluster"></a>Tworzenie klastra systemu Linux usługi HDInsight
Utwórz klaster HDInsight (Linux) z [witryny Azure portal](https://portal.azure.com). Aby uzyskać szczegółowe informacje, zobacz **tworzenie klastra HDInsight z dostępem do usługi Azure Data Lake Storage** w sekcji Tworzenie [klastra USŁUGI HDInsight z magazynem Usługi Data Lake przy użyciu witryny Azure portal.](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Tworzenie tabeli gałęzi w umiaśnie HDInsight
Teraz można utworzyć tabele hive do użycia w usłudze Azure Machine Learning Studio (klasyczny) w klastrze HDInsight przy użyciu danych przechowywanych w usłudze Azure Data Lake Storage w poprzednim kroku. Przejdź do utworzonego klastra HDInsight. Kliknij pozycję**Właściwości** -->  **ustawień** --> **klastra AAD Identity** --> **ADLS Access**, upewnij się, że twoje konto usługi Azure Data Lake Storage jest dodawane do listy z prawami do odczytu, zapisu i wykonywania.

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Następnie kliknij **pozycję Pulpit nawigacyjny** obok przycisku **Ustawienia** i pojawi się okno. Kliknij **pozycję Widok gałęzi** w prawym górnym rogu strony, a w **edytorze zapytań**powinien zostać wyświetlony edytor zapytań .

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Wklej w poniższych skryptach gałęzi, aby utworzyć tabelę. Lokalizacja źródła danych znajduje się w odwołaniu usługi Azure Data Lake Storage w ten sposób: **adl://data_lake_store_name.azuredatalakestore.net:443/folder_name/file_name**.

    CREATE EXTERNAL TABLE nyc_stratified_sample
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string,
      payment_type string,
      fare_amount string,
      surcharge string,
      mta_tax string,
      tolls_amount string,
      total_amount string,
      tip_amount string,
      tipped string,
      tip_class string,
      rownum string
      )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    LOCATION 'adl://data_lake_storage_name.azuredatalakestore.net:443/nyctaxi_folder/demo_ex_9_stratified_1_1000_copy.csv';


Po zakończeniu kwerendy powinny zostać wyświetlene następujące wyniki:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Tworzenie i wdrażanie modeli w usłudze Azure Machine Learning Studio
Teraz możesz przystąpić do tworzenia i wdrażania modelu, który przewiduje, czy wskazówka jest płatna za pomocą usługi Azure Machine Learning. Stratyfikowane dane próbki są gotowe do użycia w tym problemie klasyfikacji binarnej (wskazówka lub nie). Modele predykcyjne przy użyciu klasyfikacji wieloklasowej (tip_class) i regresji (tip_amount) można również skompilować i wdrożyć za pomocą usługi Azure Machine Learning Studio, ale w tym miejscu pokazano tylko sposób obsługi sprawy przy użyciu modelu klasyfikacji binarnej.

1. Pobierz dane do usługi Azure Machine Learning Studio (klasyczne) przy użyciu modułu **Importuj dane,** dostępnego w sekcji **Dane wejściowe i wyjściowe.** Aby uzyskać więcej informacji, zobacz stronę odwołania do [modułu importu](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) danych.
2. Wybierz **pozycję Zapytanie gałęzi** jako źródło **danych** w panelu **Właściwości.**
3. Wklej następujący skrypt gałęzi w edytorze **zapytań bazy danych hive**

        select * from nyc_stratified_sample;
4. Wprowadź identyfikator URI klastra HDInsight (ten identyfikator URI można znaleźć w witrynie Azure portal), poświadczenia Usługi Hadoop, lokalizacja danych wyjściowych i nazwa konta usługi Azure Storage/klucz/kontener.

   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)

Przykład eksperymentu binarnego odczytu danych z tabeli Hive jest pokazany na poniższym rysunku:

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

Po utworzeniu eksperymentu kliknij pozycję **Skonfiguruj** --> usługę sieci**Web Predictive Web Service**

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Uruchom automatycznie utworzony eksperyment oceniania po jego zakończeniu kliknij pozycję **Wdrażanie usługi sieci Web**

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

Pulpit nawigacyjny usługi sieci web wyświetla się wkrótce:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Podsumowanie
Po wykonaniu tego przewodnika utworzono środowisko do nauki o danych do tworzenia skalowalnych kompleksowych rozwiązań w usłudze Azure Data Lake. To środowisko zostało użyte do analizy dużego zestawu danych publicznych, przechodząc przez kroki kanoniczne procesu nauki o danych, od pozyskiwania danych przez szkolenie modelu, a następnie do wdrożenia modelu jako usługi sieci web. U-SQL został użyty do przetwarzania, eksplorowania i próbkowania danych. Python i Hive były używane z usługą Azure Machine Learning Studio (klasyczna) do tworzenia i wdrażania modeli predykcyjnych.

## <a name="whats-next"></a>Co dalej?
Ścieżka szkoleniowa dla [procesu nauki o danych zespołu (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) zawiera łącza do tematów opisujących każdy krok w procesie zaawansowanej analizy. Istnieje seria instruktaży wyszczególnionych na stronie wskazówki dotyczące [procesu nauki o danych zespołu,](walkthroughs.md) które pokazują, jak korzystać z zasobów i usług w różnych scenariuszach analizy predykcyjnej:

* [Proces nauki o danych zespołu w akcji: przy użyciu magazynu danych SQL](sqldw-walkthrough.md)
* [Proces nauki o danych zespołu w akcji: korzystanie z klastrów HDInsight Hadoop](hive-walkthrough.md)
* [Proces nauki o danych zespołu: korzystanie z programu SQL Server](sql-walkthrough.md)
* [Omówienie procesu nauki o danych przy użyciu platformy Spark w usłudze Azure HDInsight](spark-overview.md)
