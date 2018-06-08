---
title: 'Skalowalna nauki danych z usługi Azure Data Lake: wskazówki end-to-end | Dokumentacja firmy Microsoft'
description: Jak używać usługi Azure Data Lake do wykonywania zadań danych binarnego i eksploracja klasyfikacji w zestawie danych.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 91a8207f-1e57-4570-b7fc-7c5fa858ffeb
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 62ca89ffe7507c2dc0a0f1a86750fb2bb996a5bd
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836974"
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Skalowalna nauki danych z usługi Azure Data Lake: wskazówki end-to-end
Ten przewodnik przedstawia sposób użycia usługi Azure Data Lake zadania klasyfikacji binarnej na próbkę podróży taksówki NYC i eksploracja danych i taryfy zestawu danych do prognozowania, czy etykietki otrzymuje klasie. Przeprowadzi Cię przez kroki [proces nauki danych zespołu](http://aka.ms/datascienceprocess)end-to- end, od pozyskiwania danych do uczenia modelu, a następnie do wdrożenia usługi sieci web, która publikuje modelu.

### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
[Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) ma wszystkie możliwości wymagane, aby ułatwić analityków danych do przechowywania danych, rozmiar, kształt i szybkości oraz przeprowadzenia przetwarzania danych, zaawansowane analizy i modelowania z wysokim uczenia maszynowego skalowalność w ekonomiczny sposób.   Opłaty są naliczane za poszczególne zadania wykonywane tylko wtedy, gdy dane są rzeczywiście przetwarzane. Azure Data Lake Analytics obejmuje U-SQL, językiem miesza deklaratywnego charakteru SQL Dzięki wszechstronnym możliwościom języka C# w celu zapewnienia skalowalności rozproszonych możliwości zapytania. Umożliwia przetwarzanie danych bez struktury przechowywanych przez zastosowanie schematu na odczyt, wstawianie niestandardowej logiki i funkcje zdefiniowane przez użytkownika (UDF) i obejmuje rozszerzalności, aby włączyć szczegółowe kontroluje sposób wykonywania na dużą skalę. Aby dowiedzieć się więcej na temat zasady projektowania klas za U-SQL, zobacz [programu Visual Studio w blogu](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Usługa Data Lake Analytics to także kluczowa część pakietu Cortana Analytics współdziałająca z usługami Azure SQL Data Warehouse, Power BI i Data Factory. Zapewnia dane big data pełnej chmury i platformy zaawansowana analityka.

W tym przewodniku rozpoczyna się od opisujące sposób instalowania warunki wstępne i zasobów, które są niezbędne do ukończenia zadania procesu analizy danych. A następnie go opisano kroki przetwarzania danych przy użyciu języka U-SQL i nie zawiera przez przedstawiająca sposób używania Python i Hive Azure Machine Learning Studio do tworzenia i wdrażania modeli predykcyjnych. 

### <a name="u-sql-and-visual-studio"></a>U-SQL i programu Visual Studio
W tym przewodniku zaleca przy użyciu programu Visual Studio do edycji skryptów U-SQL do przetworzenia zestawu danych. Skryptów U-SQL są opisane w tym miejscu i dostępne w oddzielnym pliku. Proces obejmuje wprowadzania, badać i pobierania próbek danych. Widoczny jest również sposób uruchamiania zadania inicjowanych przez skrypty U-SQL z portalu Azure. Tabele programu hive są tworzone dla danych w skojarzone klastra usługi HDInsight w celu ułatwienia tworzenia i wdrażania modelu klasyfikacji binarnej w usłudze Azure Machine Learning Studio.  

### <a name="python"></a>Python
Ten przewodnik zawiera również sekcja, która przedstawia sposób tworzenia i wdrażania modelu predykcyjnego przy użyciu języka Python w usłudze Azure Machine Learning Studio. Zapewnia notesu Jupyter ze skryptami języka Python do kroków w tym procesie. Notesu zawiera kod dla niektórych dodatkowych funkcji engineering kroki i modele konstrukcja takie jak wieloklasowej klasyfikacji i regresji modelowania oprócz model klasyfikacji binarnej opisana w tym temacie. Zadanie regresji jest do prognozowania ilość Porada na podstawie innych funkcji poradę. 

### <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning Studio jest używany do tworzenia i wdrażania modeli predykcyjnych. Jest to realizowane przy użyciu dwóch metod: najpierw ze skryptami języka Python, a następnie tabele programu Hive w klastrze usługi HDInsight (Hadoop).

### <a name="scripts"></a>Scripts
Główne kroki zostały opisane w tym przewodniku. Możesz pobrać pełny **skrypt U-SQL** i **notesu Jupyter** z [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tych tematów należy dysponować następującymi elementami:

* Subskrypcja platformy Azure. Jeśli nie masz już jeden, zobacz [Azure Pobierz bezpłatną wersję próbną](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Zalecane] Program Visual Studio 2013 lub nowszy. Jeśli nie już masz jeden z tych wersji, możesz pobrać bezpłatną wersję społeczności z [Visual Studio Community](https://www.visualstudio.com/vs/community/).

> [!NOTE]
> Zamiast Visual Studio możesz również użyć portalu Azure do przesyłania zapytań usługi Azure Data Lake. Instrukcje dotyczące sposobu wykonania tak zarówno z programem Visual Studio i w portalu w sekcji **przetwarzania danych w języku U-SQL**. 
> 
> 


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Przygotowanie środowiska nauki danych dla usługi Azure Data Lake
Aby przygotować środowisko analizy danych w ramach tego przewodnika, utworzenie następujących zasobów:

* Azure Data Lake — magazyn (ADLS) 
* Usługi Azure Data Lake Analytics (ADLA)
* Konto magazynu obiektów Blob platformy Azure
* Konto usługi Azure Machine Learning Studio
* Usługi Azure Data Lake Tools dla programu Visual Studio (zalecane)

Ta sekcja zawiera instrukcje dotyczące sposobu tworzenia tych zasobów. Jeśli użytkownik chce tabele programu Hive za pomocą usługi Azure Machine Learning, zamiast języka Python, do tworzenia modelu, należy udostępnić klastra usługi HDInsight (Hadoop). Ta procedura alternatywne w opisany w sekcji opcja 2.


> [!NOTE]
> **Azure Data Lake Store** można tworzyć zarówno oddzielnie lub podczas tworzenia **Azure Data Lake Analytics** jako domyślnego magazynu. Instrukcje odwołuje się do tworzenia tych zasobów oddzielnie, ale konto usługi Data Lake magazynu muszą nie można utworzyć oddzielnie.
>
> 

### <a name="create-an-azure-data-lake-store"></a>Tworzenie usługi Azure Data Lake Store


Utwórz ADLS z [portalu Azure](http://portal.azure.com). Aby uzyskać więcej informacji, zobacz [tworzenia klastra usługi HDInsight z Data Lake Store za pomocą portalu Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Pamiętaj skonfigurować tożsamość usługi AAD klastra w **źródła danych** bloku **konfiguracji opcjonalnej** bloku opisane istnieje. 

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Tworzenie konta usługi Azure Data Lake Analytics
Tworzenie konta usługi ADLA z [portalu Azure](http://portal.azure.com). Aby uzyskać więcej informacji, zobacz [samouczek: wprowadzenie do usługi Azure Data Lake Analytics przy użyciu portalu Azure](../../data-lake-analytics/data-lake-analytics-get-started-portal.md). 

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Tworzenie konta magazynu obiektów Blob platformy Azure
Tworzenie konta magazynu obiektów Blob platformy Azure z [portalu Azure](http://portal.azure.com). Aby uzyskać więcej informacji, zobacz Tworzenie sekcji konta magazynu w [kont magazynu Azure o](../../storage/common/storage-create-storage-account.md).

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-account"></a>Konfigurowanie konta usługi Azure Machine Learning Studio
Zaloguj się w górę/w usłudze Azure Machine Learning Studio z [usługi Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) strony. Polecenie **Rozpocznij teraz** przycisk, a następnie wybierz pozycję "Wolnego obszaru roboczego" lub "Standardowe obszaru roboczego". Teraz używany jest gotowy do tworzenie eksperymentów w Studio uczenia Maszynowego Azure.  

### <a name="install-azure-data-lake-tools-recommended"></a>Zainstaluj usługi Azure Data Lake Tools [zalecane]
Zainstaluj usługi Azure Data Lake Tools dla używanej wersji programu Visual Studio z [Azure Data Lake Tools dla programu Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

Po pomyślnym zainstalowaniu otwarcia programu Visual Studio. Data Lake karcie menu u góry powinna zostać wyświetlona. Po zalogowaniu do konta platformy Azure zasobów platformy Azure powinny być wyświetlane w lewym panelu.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>Zestaw danych rund taksówki NYC
Zestaw danych używany w tym miejscu jest publicznie dostępnych zestawu danych-- [dataset rund taksówki NYC](http://www.andresmh.com/nyctaxitrips/). Dane podróży taksówki NYC składa się z około 20 GB skompresowanego plików CSV (~ 48 GB nieskompresowane), rejestrowanie ponad milion 173 poszczególnych podróży i opłaty płatnej dla każdej podróży. Każdy rekord podróży zawiera lokalizacje odbiór i przyjmowania i razy, numer licencji hack anonimowe (sterownik) i numer Medalionu (taksówki jego unikatowy identyfikator). Dane obejmuje wszystkie rund w roku 2013 i jest dostępne w następujących dwóch zestawów danych dla każdego miesiąca:

"Trip_data" CSV zawiera szczegóły podróży, takie jak liczba pasażerów, pobranie i punkty dropoff czas trwania podróży i długość podróży. Poniżej przedstawiono kilka przykładowych rekordów:

       medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868



Trip_fare CSV zawiera szczegółowe informacje o klasie za każdym razem, takie jak typ płatności, kwota taryfy, przeciążenia i podatków, porady i przejazd i sumy płatnej. Poniżej przedstawiono kilka przykładowych rekordów:

       medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Unikatowy klucz do przyłączenia podróży\_danych i podróży\_taryfy składa się z trzech poniższych pól: Medalionu, hack\_licencji i pobrania\_daty/godziny. Nieprzetworzone pliki CSV są dostępne z obiektu blob magazynu Azure publicznego. Skrypt U-SQL dla tego sprzężenia znajduje się w [łączenia tabel podróży i taryfy](#join) sekcji.

## <a name="process-data-with-u-sql"></a>Przetwarzaj dane w języku U-SQL
Zadania przetwarzania danych, przedstawione w tej sekcji zawierają wprowadzania, sprawdzanie jakości eksploracji i pobierania próbek danych. Sposobu łączenia tabel podróży i taryfy jest także pokazany. W końcowej sekcji przedstawiono wykonywania zadania przy użyciu skryptu U-SQL przy użyciu portalu Azure. Oto łącza do każdego podsekcji:

* [Wprowadzanie danych: odczytać danych z obiektu blob publiczny](#ingest)
* [Kontrola jakości danych](#quality)
* [Eksploracja danych](#explore)
* [Dołącz do tabel podróży i taryfy](#join)
* [Próbkowanie danych](#sample)
* [Uruchamianie zadań U-SQL](#run)

Skryptów U-SQL są opisane w tym miejscu i dostępne w oddzielnym pliku. Możesz pobrać pełny **skryptów U-SQL** z [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Do wykonania skryptu U-SQL, Otwórz program Visual Studio, kliknij przycisk **Plik--> Nowa--> Projekt**, wybierz **projektu U-SQL**, nazw i zapisz go w folderze.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> Istnieje możliwość wykonania U-SQL, zamiast programu Visual Studio za pomocą portalu Azure. Możesz przejść do zasobu usługi Azure Data Lake Analytics w portalu i przesyłania zapytań bezpośrednio jako ilustrowane na poniższej ilustracji:
> 
> 

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="ingest"></a>Wprowadzanie danych: Odczyt danych z obiektu blob publiczny
Lokalizacja danych obiektów blob platformy Azure jest określany jako **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name** i wyodrębniona przy użyciu **Extractors.Csv()**. Podstawić własną nazwę kontenera i nazwy konta magazynu w następujących skryptów dla container_name@blob_storage_account_name adres wasb. Ponieważ nazwy plików są w tym samym formacie, możliwe jest **podróży\_data_ {\*\}CSV** do odczytu we wszystkich plikach 12 podróży. 

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

Ponieważ w pierwszym wierszu nagłówków, należy usunąć nagłówki i zmiany do nich odpowiednie typy kolumn. Można zapisać przetworzone dane przy użyciu usługi Azure Data Lake Storage **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ lub przy użyciu konta magazynu obiektów Blob platformy Azure  **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name**. 

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

Podobnie można znaleźć w klasie zestawów danych. Kliknij prawym przyciskiem myszy Azure Data Lake Store można przyjrzeć się dane **portalu Azure--> Eksploratora danych** lub **Eksploratora plików** w programie Visual Studio. 

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="quality"></a>Kontrola jakości danych
Po odczytano podróży i taryfy tabel w kontroli jakości danych może odbywać się w następujący sposób. Wynikowe pliki CSV można dane wyjściowe do magazynu obiektów Blob platformy Azure lub usługi Azure Data Lake Store. 

Znajdź liczbę medallions i unikatowy numer medallions:

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

Znajdź te medallions, w których zastosowano więcej niż 100 rund:

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



### <a name="explore"></a>Eksploracja danych
Czy niektóre Eksploracja danych z poniższych skryptów uzyskanie lepszego zrozumienia danych.

Znajdź dystrybucji rund Przechylony i Przechylony:

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

Znajdź dystrybucji kwoty Porada wartościami odcięty: 0, 5, 10 do 20 kwoty.

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

Znajdź podstawowe dane statystyczne odległości podróży:

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

Znajdź percentylu odległości podróży:

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


### <a name="join"></a>Dołącz do tabel podróży i taryfy
Mogą zostać sprzężone tabele podróży i taryfy Medalionu, hack_license i pickup_time.

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


Dla każdego poziomu liczby pasażerów oblicza liczbę rekordów, porada średnia kwota, odchylenie kwoty Porada stopień podróży Przechylony.

    // contigency table
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


### <a name="sample"></a>Próbkowanie danych
Po pierwsze losowo wybierać 0,1% danych z dołączonym do tabeli:

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

Następnie wykonaj uporządkować próbkowania przez binarny tip_class zmiennej:

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


### <a name="run"></a>Uruchamianie zadań U-SQL
Po zakończeniu edycji skryptów U-SQL, można je przesłać do serwera przy użyciu konta usługi Azure Data Lake Analytics. Kliknij przycisk **usługi Data Lake**, **Prześlij zadanie**, wybierz użytkownika **konta usługi Analytics**, wybierz **równoległości**i kliknij przycisk **przesyłania**  przycisku.  

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

Gdy zadanie jest spełnione pomyślnie, stan zadania jest wyświetlany w programie Visual Studio do monitorowania. Po zakończeniu zadania można nawet powtarzania proces wykonywania zadania i dowiedzieć się, aby zwiększyć wydajność zadania procedurę "wąskie gardło". Można także przejść do portalu Azure, aby sprawdzić stan zadań U-SQL.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Teraz można sprawdzić pliki wyjściowe w magazynie obiektów Blob Azure lub w portalu Azure. Użyj stratyfikowana przykładowych danych do naszej modelowania w następnym kroku.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Tworzenie i wdrażanie modeli w usłudze Azure Machine Learning
Dwie opcje są dostępne umożliwiające ściąganie danych do usługi Azure Machine Learning do tworzenia i 

* W przypadku opcji pierwszy używasz próbki danych, który został zapisany do obiektów Blob platformy Azure (w **danych próbkowania** kroku powyżej) i Python umożliwia tworzenie i wdrażanie modeli z usługi Azure Machine Learning. 
* W drugiej opcji można wysyłać zapytania o dane w usłudze Azure Data Lake bezpośrednio za pomocą zapytań programu Hive. Ta opcja wymaga utworzenia nowego klastra usługi HDInsight, lub użyj istniejącego klastra usługi HDInsight, gdzie tabele programu Hive polecenie dane taksówki NY w usłudze Azure Data Lake Storage.  W poniższych sekcjach omówiono obie te opcje. 

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Opcja 1: Python używany do tworzenia i wdrażania modeli uczenia komputera.
Tworzenie i wdrażanie modeli uczenia komputera przy użyciu języka Python, tworzenie notesu Jupyter na komputerze lokalnym lub w usłudze Azure Machine Learning Studio. Podanego notesu Jupyter w [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) zawiera pełny kod, aby eksplorować, wizualizację danych, funkcja zespołu inżynieryjnego, modelowania i wdrożenia. W tym artykule opisano tylko modelowania i wdrażania. 

### <a name="import-python-libraries"></a>Importuj biblioteki języka Python
Aby można było uruchomić przykładowy plik, Python następujące pakiety są wymagane skryptu notesu Jupyter lub Python. Jeśli używasz usługi uczenie maszynowe Azure notesu te pakiety zostały wstępnie zainstalowane.

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


### <a name="read-in-the-data-from-blob"></a>Odczytać danych z obiektu blob
* Parametry połączenia   
  
        CONTAINERNAME = 'test1'
        STORAGEACCOUNTNAME = 'XXXXXXXXX'
        STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
        BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
        blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
* Odczytu w postaci tekstu
  
        t1 = time.time()
        data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
        t2 = time.time()
        print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))
  
  ![17](./media/data-lake-walkthrough/17-python_readin_csv.PNG)    
* Dodaj nazwy kolumn i oddziel kolumn
  
        colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
        'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
        df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
* Zmień niektóre kolumny liczbowe
  
        cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
        ,'tipped','tip_class','rownum']
        for col in cols_2_float:
            df1[col] = df1[col].astype(float)

### <a name="build-machine-learning-models"></a>Tworzenie modeli uczenia maszyny
W tym miejscu należy utworzyć model klasyfikacji binarnej do prognozowania, czy podróży jest Przechylony lub nie. Notesu Jupyter można znaleźć inne dwa modele: wieloklasowej klasyfikacji i regresji modeli.

* Najpierw należy utworzyć fikcyjny zmienne, które mogą być używane w scikit — Dowiedz się modeli
  
        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
* Utwórz ramkę danych modelowania
  
        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])
  
        X = data.iloc[:,1:]
        Y = data.tipped
* Uczenie i testowanie 60 40 podziału
  
        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
* Regresja logistyczna w zestawie szkolenia
  
        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)
  
       ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)
* Wynik testowania zestawu danych
  
        Y_test_pred = logit_fit.predict(X_test)
* Oblicz metryki oceny
  
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

### <a name="build-web-service-api-and-consume-it-in-python"></a>Tworzenie interfejsu API usługi sieci Web i używać go w języku Python
Chcesz operacjonalizacji uczenia modelu po został skompilowany. Binarny logistyczna model jest używany jako przykład. Upewnij się, że scikit — Dowiedz się więcej wersji w komputerze lokalnym jest 0.15.1. Nie trzeba martwić się o to, jeśli używasz usługi Azure ML studio usługi.

* Znajdź poświadczenia obszar roboczy z usługi Azure ML studio ustawień. W usłudze Azure Machine Learning Studio, kliknij przycisk **ustawienia** --> **nazwa** --> **tokeny autoryzacji**. 
  
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
* Pobieranie poświadczeń usługi sieci web
  
        url = predictNYCTAXI.service.url
        api_key =  predictNYCTAXI.service.api_key
  
        print url
        print api_key
  
        @services.service(url, api_key)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(float)
        def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            pass
* Wywołanie interfejsu API usługi sieci Web. Należy poczekać 5 – 10 sekund po poprzednim kroku.
  
        NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)
  
       ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>Opcja 2: Tworzenie i wdrażanie modeli bezpośrednio w usłudze Azure Machine Learning
Azure Machine Learning Studio mogą odczytywać dane bezpośrednio z usługi Azure Data Lake Store, a następnie służyć do tworzenia i wdrażania modeli. Ta metoda korzysta z tabeli programu Hive, który wskazuje na Azure Data Lake Store. Wymaga to, że udostępniane oddzielny klaster Azure HDInsight, na jest tworzona w tabeli Hive. Poniższe sekcje pokazują, jak to zrobić. 

### <a name="create-an-hdinsight-linux-cluster"></a>Tworzenie klastra usługi HDInsight w systemie Linux
Tworzenie klastra usługi HDInsight (Linux) z [portalu Azure](http://portal.azure.com). Aby uzyskać więcej informacji, zobacz **tworzenia klastra usługi HDInsight z dostępem do usługi Azure Data Lake Store** sekcji [tworzenia klastra usługi HDInsight z Data Lake Store za pomocą portalu Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Tworzenie tabeli Hive w usłudze HDInsight
Teraz możesz utworzyć tabele programu Hive do użycia w usłudze Azure Machine Learning Studio w klastrze usługi HDInsight przy użyciu danych przechowywanych w usłudze Azure Data Lake Store w poprzednim kroku. Przejdź do klastra usługi HDInsight utworzony. Kliknij przycisk **ustawienia** --> **właściwości** --> **klastra tożsamości w usłudze AAD** --> **dostępem ADLS**, Upewnij się, że konto usługi Azure Data Lake Store zostanie dodane na liście o odczytu, zapisu i wykonywania praw. 

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Następnie kliknij przycisk **pulpitu nawigacyjnego** obok **ustawienia** przycisk i okno podręczne z. Kliknij przycisk **Hive View** w prawym górnym rogu strony i powinna zostać wyświetlona **edytora zapytań**.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Wklej następujący skrypty gałęzi w celu utworzenia tabeli. Lokalizacja źródła danych jest w dokumentacji usługi Azure Data Lake Store w ten sposób: **adl://data_lake_store_name.azuredatalakestore.net:443/nazwa_folderu/nazwa_pliku**.

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


Po zakończeniu zapytanie powinny być widoczne wyniki następująco:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Tworzenie i wdrażanie modeli w usłudze Azure Machine Learning Studio
Teraz można przystąpić do tworzenia i wdrażania modelu, który wskazuje, czy etykietki otrzymuje przy użyciu usługi Azure Machine Learning. Stratyfikowana przykładowych danych jest gotowy do użycia w tym klasyfikacji binarnej (Porada lub nie) problem. Modeli predykcyjnych, przy użyciu wieloklasowej klasyfikacji (tip_class) i regresji (tip_amount) również może być wbudowane i wdrożony w usłudze Azure Machine Learning Studio, ale w tym miejscu jego jest wyświetlana tylko w sposób obsługi w przypadku, używając modelu klasyfikacji binarnej.

1. Pobieranie danych do usługi Azure ML przy użyciu **i zaimportuj dane** modułu dostępne w **danych wejściowych i wyjściowych** sekcji. Aby uzyskać więcej informacji, zobacz [modułu importu danych](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) strony odwołania.
2. Wybierz **zapytanie Hive** jako **źródła danych** w **właściwości** panelu.
3. Wklej poniższy skrypt Hive w **zapytanie bazy danych Hive** edytora
   
        select * from nyc_stratified_sample;
4. Wprowadź identyfikator URI usługi HDInsight klastra (znajdują się w portalu Azure), poświadczenia usługi Hadoop, lokalizacja danych wyjściowych i nazwę kontenera nazwa/kluczy konta magazynu platformy Azure.
   
   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)  

Przykład klasyfikacji binarnej odczytu eksperymentu, danych z tabeli Hive jest wyświetlane na poniższej ilustracji:

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

Po utworzeniu eksperyment, kliknij przycisk **ustawić usługę sieci Web** --> **predykcyjnej usługi sieci Web**

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Uruchom automatycznie utworzone oceniania eksperymentu, gdy zakończy pracę, kliknij przycisk **wdrażanie usługi sieci Web**

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

Pulpit nawigacyjny usługi sieci web wyświetla wkrótce:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Podsumowanie
Kończenie pracy tego przewodnika utworzono środowisku nauki danych do tworzenia skalowalnych rozwiązań end-to-end w usłudze Azure Data Lake. To środowisko zostało użyte do analizowania dużych publicznego zestawu danych, zabierz canonical kroki procesu nauki danych z pozyskiwania danych za pomocą uczenia modelu, a następnie do wdrażania modelu jako usługę sieci web. U-SQL została użyta do przetwarzania i Eksploruj przykładowe dane. Azure Machine Learning Studio Python i Hive były używane do tworzenia i wdrażania modeli predykcyjnych.

## <a name="whats-next"></a>Co dalej?
Ścieżka szkoleniowa dotycząca [zespołu danych nauki procesu (TDSP)](http://aka.ms/datascienceprocess) zawiera łącza do tematów opisujących każdego kroku w procesie zaawansowana analityka. Istnieje szereg wskazówki wyszczególnione w [wskazówki dotyczące procesu nauki danych zespołu](walkthroughs.md) strony, które pokazują, jak korzystać z zasobów i usług w różnych scenariuszach analizy predykcyjnej:

* [Proces nauki danych zespołu w działaniu: przy użyciu magazynu danych SQL](sqldw-walkthrough.md)
* [Proces nauki danych zespołu w działaniu: z użyciem klastrów usługi HDInsight Hadoop](hive-walkthrough.md)
* [Procesu nauki danych zespołu: SQL Server przy użyciu](sql-walkthrough.md)
* [Omówienie procesu nauki danych przy użyciu platformy Spark w usłudze Azure HDInsight](spark-overview.md)
