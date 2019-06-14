---
title: Do nauki o danych skalowalne, za pomocą usługi Azure Data Lake — zespołu danych dla celów naukowych
description: Jak używać usługi Azure Data Lake wykonywanie zadań data eksploracji i danych binarnych klasyfikacji w zestawie danych.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: cc37109eda2690b4407f9cd0c92851b7c0e3f915
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60399962"
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Analiza danych skalowalna usługa Azure Data Lake: Przewodnik end-to-end
Ten poradnik pokazuje jak eksploracji danych i zadań klasyfikacji binarnej na przykład wyzwolenie taksówek NYC oraz taryfy zestawu danych w celu przewidywania, czy porady płatnego przez opłatę za pomocą usługi Azure Data Lake. Przeprowadza użytkownika przez kroki [zespołu danych dla celów naukowych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), end-to-end, od pozyskiwanie danych do szkolenia modelu, a następnie do wdrożenia usługi sieci web, która umożliwia publikowanie modelu.

### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
[Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) wszystkie możliwości wymagane ułatwia analitykom danych do przechowywania danych o dowolnym rozmiarze, kształcie i szybkości, a także do przeprowadzenia przetwarzania danych, analiza zaawansowana i uczenia maszynowego, modelowanie z wysokim skalowalność w ekonomiczny sposób.   Opłaty są naliczane na podstawie dla zadania tylko wtedy, gdy rzeczywiście przetwarzania danych. Usługa Azure Data Lake Analytics obejmuje U-SQL, język, który łączy deklaratywne SQL Dzięki wszechstronnym możliwościom języka C# umożliwia skalowalne rozproszone możliwość zapytań. Umożliwia przetwarzanie danych bez struktury, stosując schematu przy odczycie, wstawianie niestandardowej logiki i funkcji zdefiniowanych przez użytkownika (UDF) i obejmuje rozszerzalności, aby umożliwić bardziej szczegółową kontrolę nad jak wykonać na dużą skalę. Aby dowiedzieć się więcej na temat zasady projektowania klas za U-SQL, zobacz [wpis w blogu programu Visual Studio](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Usługa Data Lake Analytics to także kluczowa część pakietu Cortana Analytics współdziałająca z usługami Azure SQL Data Warehouse, Power BI i Data Factory. Daje to danych big data w chmurze kompletny i zaawansowana platforma analiz.

W tym przewodniku rozpoczyna się od opisujących sposób zainstalować wstępnie wymagane składniki i zasoby, które są potrzebne do ukończenia zadania procesu do nauki o danych. Opisano kroki przetwarzania danych przy użyciu języka U-SQL, a stwierdza, pokazując, jak używać języka Python i Hive przy użyciu usługi Azure Machine Learning Studio do tworzenia i wdrażania modeli predykcyjnych.

### <a name="u-sql-and-visual-studio"></a>U-SQL i programu Visual Studio
Ten przewodnik zaleca się przy użyciu programu Visual Studio do edycji skryptów U-SQL do przetwarzania zestawu danych. Skryptów U-SQL są opisane w tym miejscu i podano w oddzielnym pliku. Proces obejmuje, umożliwiając pozyskiwanie, eksplorowania i próbkowanie danych. Pokazano również, jak do uruchamiania zadań inicjowanych przez skrypty U-SQL w witrynie Azure portal. Tabele programu hive są tworzone dla danych w klastrze usługi HDInsight skojarzone ułatwiające tworzenie i wdrażanie w usłudze Azure Machine Learning Studio model klasyfikacji binarnej.

### <a name="python"></a>Python
Ten przewodnik zawiera również sekcja przedstawia sposób tworzenia i wdrażania modelu predykcyjnego przy użyciu języka Python za pomocą usługi Azure Machine Learning Studio. Oferuje ona notesu programu Jupyter przy użyciu skryptów języka Python czynności w ramach tego procesu. Notes, zawiera kod dla niektórych dodatkowych funkcji inżynierów kroki i modele konstrukcja takich jak wieloklasowej klasyfikacji i regresji modelowania oprócz model klasyfikacji binarnej, opisana w tym temacie. Zadanie regresji jest przewidzieć kwotę napiwku na podstawie innych funkcji porada.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Usługa Azure Machine Learning Studio jest używany do tworzenia i wdrażania modeli predykcyjnych. Odbywa się przy użyciu dwóch metod: najpierw za pomocą skryptów języka Python, a następnie z tabel programu Hive w klastrze usługi HDInsight (Hadoop).

### <a name="scripts"></a>Scripts
Główne kroki zostały opisane w tym przewodniku. Możesz pobrać pełną **skrypt U-SQL** i **notesu programu Jupyter** z [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tych tematów, musisz mieć następujące czynności:

* Subskrypcja platformy Azure. Jeśli nie masz już jeden, zobacz [uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Zalecane] Program Visual Studio 2013 lub nowszy. Jeśli nie masz już jedną z tych wersji zainstalowany, możesz pobrać bezpłatną wersję Community z [programu Visual Studio Community](https://www.visualstudio.com/vs/community/).

> [!NOTE]
> Zamiast programu Visual Studio można również użyć witryny Azure portal do przesyłania zapytań usługi Azure Data Lake. Instrukcje znajdują się na temat sposobu więc obie opcje, dzięki programowi Visual Studio i w portalu w sekcji **przetwarzanie danych za pomocą języka U-SQL**.
>
>


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Przygotuj środowisko do nauki o danych dla usługi Azure Data Lake
Aby przygotować środowisko do nauki o danych w ramach tego przewodnika, utworzenie następujących zasobów:

* Azure Data Lake Store (ADLS)
* Usługi Azure Data Lake Analytics (ADLA)
* Konto usługi Azure Blob storage
* Konto usługi Azure Machine Learning Studio
* Usługa Azure Data Lake Tools for Visual Studio (zalecane)

Ta sekcja zawiera instrukcje dotyczące sposobu tworzenia tych zasobów. Jeśli zdecydujesz się używać tabel programu Hive z usługą Azure Machine Learning, zamiast Python, do zbudowania modelu, należy również aprowizować klaster usługi HDInsight (Hadoop). Ta procedura alternatywne w opisane w sekcji opcja 2.


> [!NOTE]
> **Usługi Azure Data Lake Store** należy utworzyć osobno lub po utworzeniu **Azure Data Lake Analytics** jako magazyn domyślny. Instrukcje odwołują się do tworzenia tych zasobów oddzielnie, ale konta magazynu usługi Data Lake nie należy utworzyć osobno.
>
>

### <a name="create-an-azure-data-lake-store"></a>Tworzenie usługi Azure Data Lake Store


Tworzenie usługi ADLS z [witryny Azure portal](https://portal.azure.com). Aby uzyskać więcej informacji, zobacz [Tworzenie klastra HDInsight z usługą Data Lake Store przy użyciu witryny Azure portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Pamiętaj skonfigurować klaster tożsamości usługi AAD w **DataSource** bloku **opcjonalna konfiguracja** bloku opisano dostępne.

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Tworzenie konta usługi Azure Data Lake Analytics
Utwórz konto ADLA z [witryny Azure portal](https://portal.azure.com). Aby uzyskać więcej informacji, zobacz [samouczek: rozpoczynanie pracy z usługą Azure Data Lake Analytics przy użyciu witryny Azure portal](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Tworzenie konta magazynu obiektów Blob platformy Azure
Tworzenie konta magazynu obiektów Blob platformy Azure z [witryny Azure portal](https://portal.azure.com). Aby uzyskać szczegółowe informacje, zobacz Tworzenie sekcji konta magazynu w [kontach magazynu Azure o](../../storage/common/storage-create-storage-account.md).

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-account"></a>Skonfiguruj konto usługi Azure Machine Learning Studio
Zaloguj się w górę/w usłudze Azure Machine Learning Studio z [usługi Azure Machine Learning studio](https://azure.microsoft.com/services/machine-learning/) strony. Kliknij pozycję **rozpoczęcie pracy już teraz** przycisk, a następnie wybierz polecenie "Bezpłatny obszar roboczy" lub "Standardowy obszar roboczy". Teraz jesteś gotowy do tworzenia eksperymentów w usłudze Azure Machine Learning studio.

### <a name="install-azure-data-lake-tools-recommended"></a>Instalowanie narzędzi Azure Data Lake Tools [zalecane]
Instalowanie narzędzi Azure Data Lake Tools dla używanej wersji programu Visual Studio z [Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

Po pomyślnym zakończeniu instalacji, Otwórz program Visual Studio. Powinien zostać wyświetlony w menu u góry karty w usłudze Data Lake. Zasoby platformy Azure powinna pojawić się w lewym panelu, po zalogowaniu się do konta platformy Azure.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>Zestaw danych podróży taksówek NYC
Zestaw danych używany w tym miejscu jest publicznie dostępnego zestawu danych — [zestawu danych podróży taksówek NYC](https://www.andresmh.com/nyctaxitrips/). Dane podróży taksówek NYC składa się z około 20 GB skompresowanych plików CSV (~ 48 GB nieskompresowane), rejestrowanie ponad milion 173 poszczególnych podróży i opłaty opłacony każdego podróży. Każdy rekord podróży obejmuje lokalizacji odbioru i dropoff i czas, numer licencji hack anonimowe (sterownika) i numer Medalionu (unikatowy identyfikator dla taksówek). Dane obejmuje wszystkie podróży w roku 2013 i znajduje się w następujących dwóch zestawów danych w każdym miesiącu:

"Trip_data" CSV zawiera szczegóły podróży, takie jak liczba osób, pobrania i dropoff punkty, czasu trwania podróży i długość podróży. Poniżej przedstawiono kilka przykładowych rekordów:

       medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868



Trip_fare CSV zawiera szczegółowe informacje o opłatę za każdym razem, takich jak typ płatności, kwota taryfy, opłata za opcję i podatków, porady i drogi, a łączna kwota płatne. Poniżej przedstawiono kilka przykładowych rekordów:

       medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Unikatowy klucz, aby dołączyć podróży\_danych i podróży\_taryfy składa się z trzech poniższych pól: Medalionu hakowanie\_umowę licencyjną i odbiór\_daty/godziny. Nieprzetworzone pliki CSV jest możliwy z obiektu blob w publicznym magazynie platformy Azure. Skrypt U-SQL w celu utworzenia tego sprzężenia znajduje się w [Dołączanie tabel podróży i klasie](#join) sekcji.

## <a name="process-data-with-u-sql"></a>Przetwarzanie danych za pomocą języka U-SQL
Zadania przetwarzania danych, przedstawione w tej sekcji obejmują, umożliwiając pozyskiwanie, kontroli jakości, eksplorowania i próbkowanie danych. Sposobu łączenia tabel podróży i klasie jest również wyświetlany. W końcowej sekcji pokazano, wykonywania zadania przy użyciu skryptu U-SQL, w witrynie Azure portal. Oto łącza do każdej podsekcji:

* [Pozyskiwanie danych: odczytywać dane z publicznego obiektu blob](#ingest)
* [Kontrola jakości danych](#quality)
* [Eksploracja danych](#explore)
* [Dołączanie podróży i klasie tabel](#join)
* [Próbkowanie danych](#sample)
* [Uruchamianie zadań U-SQL](#run)

Skryptów U-SQL są opisane w tym miejscu i podano w oddzielnym pliku. Możesz pobrać pełną **skryptów U-SQL** z [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Do wykonania U-SQL, Otwórz program Visual Studio, kliknij przycisk **Plik--> New--> Projekt**, wybierz **projekt U-SQL**, nazwy i zapisz go w folderze.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> Istnieje możliwość wykonywanie U-SQL, zamiast programu Visual Studio za pomocą witryny Azure Portal. Można przejdź do zasobu usługi Azure Data Lake Analytics w portalu i przesłać zapytania bezpośrednio, jak przedstawiono na poniższej ilustracji:
>
>

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="ingest"></a>Pozyskiwanie danych: Odczyt danych z publicznego obiektu blob

Lokalizacja danych w usłudze Azure blob jest określany jako **wasb://container\_nazwa\@obiektu blob\_magazynu\_konta\_name.blob.core.windows.net/blob_name**i wyodrębnić za pomocą **Extractors.Csv()** . Podstawić własną nazwę kontenera i nazwy konta magazynu w następujących skryptów dla kontenera\_nazwa\@blob\_magazynu\_konta\_nazwę w polu adres wasb. Ponieważ nazwy plików znajdują się w tym samym formacie, jest możliwe użycie **podróży\_danych\_\{\*\}CSV** odczytać we wszystkich plikach 12 podróży.

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

Ponieważ występują nagłówki w pierwszym wierszu, należy usunąć nagłówki i zmień typy kolumn na odpowiednią z nich. Możesz albo zapisać przetworzone dane do korzystania z usługi Azure Data Lake Storage **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ lub konto magazynu obiektów Blob platformy Azure za pomocą funkcji **wasb: / / container_name\@blob_storage_account_name.blob.core.windows.net/blob_name**.

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

Podobnie możesz odczytać w klasie zestawów danych. Kliknij prawym przyciskiem myszy Azure Data Lake Store możesz przyjrzeć się dane w **witryny Azure portal--> Eksplorator danych** lub **Eksploratora plików** w programie Visual Studio.

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="quality"></a>Kontrola jakości danych
Po podróży i klasie tabele zostały po ich przeczytaniu w procesy kontroli jakości danych może odbywać się w następujący sposób. Wynikowe pliki CSV można dane wyjściowe do usługi Azure Blob storage lub Azure Data Lake Store.

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

Znajdź medallions, te, które miały więcej niż 100 podróży:

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

Znajdź brakuje wartości dla niektórych zmiennych:

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
Czy niektóre eksplorację danych za pomocą następujących skryptów, aby lepiej zrozumieć dane.

Znajdź dystrybucji Przechylony i Przechylony podróży:

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

Znajdź dystrybucję ilości Porada odcięcia wartości: 0, 5, 10 i 20 dolarów.

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

Znajdź percentyle odległość podróży:

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


### <a name="join"></a>Dołączanie podróży i klasie tabel
Tabele podróży i klasie mogą być dołączane za Medalionu hack_license i pickup_time.

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


Dla każdego poziomu liczby pasażerów obliczyć liczbę rekordów, porada średnia kwota, wariancji Porada kwota, procent Przechylony podróży.

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
Po zakończeniu edycji skryptów U-SQL, można je przesłać do serwera przy użyciu konta usługi Azure Data Lake Analytics. Kliknij przycisk **usługi Data Lake**, **Prześlij zadanie**, wybierz opcję usługi **konto usługi Analytics**, wybierz **równoległości**i kliknij przycisk **przesyłania**  przycisku.

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

Gdy zadanie jest pomyślnie spełnione, stan zadania jest wyświetlany w programie Visual Studio do monitorowania. Po zakończeniu zadania możesz nawet oparte na metodzie powtórzeń proces wykonywania zadania i Dowiedz się, kroki wąskiego gardła, aby zwiększyć wydajność zadania. Możesz również przejść do witryny Azure portal, aby sprawdzić stan zadań U-SQL.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Teraz możesz sprawdzić pliki wyjściowe w usłudze Azure Blob storage lub w witrynie Azure portal. Użyj stratyfikowana przykładowe dane do naszych modelowania w następnym kroku.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Tworzenie i wdrażanie modeli w usłudze Azure Machine Learning
Dwie opcje są dostępne dla Ciebie ściągać dane do usługi Azure Machine Learning, aby tworzyć i

* W pierwszej opcji Użyj próbki danych, który został zapisany do obiektu Blob platformy Azure (w **próbkowanie danych** kroku powyżej) i korzystanie z języka Python do tworzenia i wdrażania modeli na podstawie usługi Azure Machine Learning.
* W drugiej opcji możesz wysłać zapytanie dotyczące danych w usłudze Azure Data Lake bezpośrednio przy użyciu zapytań programu Hive. Ta opcja wymaga, Utwórz nowy klaster HDInsight, lub użyj istniejącego klastra HDInsight, gdzie tabele programu Hive wskazują na dane taksówek NY w usłudze Azure Data Lake Storage.  W poniższych sekcjach omówiono obu tych opcji.

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Opcja 1: Tworzenie i wdrażanie modeli uczenia maszynowego przy użyciu języka Python
Do tworzenia i wdrażania modeli uczenia maszynowego przy użyciu języka Python, tworzenie notesu Jupyter na komputerze lokalnym lub w usłudze Azure Machine Learning Studio. Notes Jupyter dostępnym na [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) zawiera pełny kod Eksplorowanie, wizualizowanie danych, technicznego opracowywania funkcji, modelowania i wdrożenia. W tym artykule znajdują się po prostu modelowania i wdrożenia.

### <a name="import-python-libraries"></a>Importowanie biblioteki języka Python
Aby można było uruchomić przykładowy plik, Python następujące pakiety są wymagane skryptu notesu programu Jupyter lub Python. Jeśli używasz usługi Azure Machine Learning notesu te pakiety zostały wstępnie zainstalowane.

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


### <a name="read-in-the-data-from-blob"></a>Odczyt danych z magazynu obiektów blob
* Parametry połączenia

        CONTAINERNAME = 'test1'
        STORAGEACCOUNTNAME = 'XXXXXXXXX'
        STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
        BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
        blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
* Przeczytaj w postaci tekstu

        t1 = time.time()
        data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
        t2 = time.time()
        print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))

  ![17](./media/data-lake-walkthrough/17-python_readin_csv.PNG)
* Dodawanie nazw kolumn i rozdzielić kolumn

        colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
        'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
        df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
* Zmiana niektórych kolumn na liczbowe

        cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
        ,'tipped','tip_class','rownum']
        for col in cols_2_float:
            df1[col] = df1[col].astype(float)

### <a name="build-machine-learning-models"></a>Tworzenie modeli uczenia maszynowego
W tym miejscu możesz tworzyć model klasyfikacji binarnej w celu przewidywania, czy podróż jest Przechylony, czy nie. W notesie Jupyter możesz znaleźć inne dwa modele: wieloklasowej klasyfikacji i modele regresji.

* Najpierw musisz utworzyć fikcyjnego zmiennych, które mogą być używane w scikit-Dowiedz się więcej modeli

        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
* Utwórz ramkę danych modelowania

        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])

        X = data.iloc[:,1:]
        Y = data.tipped
* Szkolenia i testowania 60 40 podziału

        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
* Regresji logistycznej w zestaw szkoleniowy

        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)

       ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)
* Ocena zestawu danych testowych

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

### <a name="build-web-service-api-and-consume-it-in-python"></a>Tworzenie interfejsu API usługi sieci Web i używanie go w języku Python
Chcesz operacjonalizować maszyny uczenie modelu po został skompilowany. Binarny model logistycznej jest używany tutaj jako przykładu. Upewnij się, scikit-Dowiedz się, wersja na komputerze lokalnym jest 0.15.1. Nie trzeba martwić się o tym, jeśli używasz usługi Azure Machine Learning studio.

* Znajdź swoje poświadczenia obszaru roboczego z usługi Azure Machine Learning studio ustawień. W usłudze Azure Machine Learning Studio, kliknij przycisk **ustawienia** --> **nazwa** --> **tokenach autoryzacji**.

    ![c3](./media/data-lake-walkthrough/c3-workspace-id.PNG)

        workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
        auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

* Tworzenie usługi internetowej

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
* Wywołaj interfejs API usługi sieci Web. Należy poczekać 5 – 10 sekund po poprzednim kroku.

        NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)

       ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>Opcja 2: Tworzenie i wdrażanie modeli bezpośrednio w usłudze Azure Machine Learning
Usługa Azure Machine Learning Studio może odczytywać dane bezpośrednio z usługi Azure Data Lake Store, a następnie umożliwia tworzenie i wdrażanie modeli. Ta metoda korzysta z tabeli programu Hive, który wskazuje na platformie Azure Data Lake Store. Wymaga to, że oddzielne klastra Azure HDInsight można zainicjować obsługi administracyjnej, w którym zostanie utworzony w tej tabeli. Poniższe sekcje pokazują, jak to zrobić.

### <a name="create-an-hdinsight-linux-cluster"></a>Tworzenie klastra usługi HDInsight w systemie Linux
Tworzenie klastra usługi HDInsight (Linux) z [witryny Azure portal](https://portal.azure.com). Aby uzyskać więcej informacji, zobacz **tworzenia klastra usługi HDInsight z dostępem do usługi Azure Data Lake Store** sekcji [Tworzenie klastra HDInsight z usługą Data Lake Store przy użyciu witryny Azure portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Tworzenie tabeli programu Hive w HDInsight
Teraz możesz tworzyć tabele programu Hive do użycia w usłudze Azure Machine Learning Studio w klastrze HDInsight przy użyciu danych przechowywanych w usłudze Azure Data Lake Store w poprzednim kroku. Przejdź do klastra HDInsight utworzony. Kliknij przycisk **ustawienia** --> **właściwości** --> **klastra tożsamości usługi AAD** --> **dostępu do usługi ADLS**, Upewnij się, że Twoje konto usługi Azure Data Lake Store zostanie dodany na liście z odczytu, zapisu i wykonywania praw.

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Następnie kliknij przycisk **pulpit nawigacyjny** obok **ustawienia** przycisku i okno podręczne z. Kliknij przycisk **Hive View** w prawym górnym rogu strony i powinien zostać wyświetlony **edytora zapytań**.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Wklej następujące skrypty Hive w celu utworzenia tabeli. Lokalizacja źródła danych jest w dokumentacji usługi Azure Data Lake Store w ten sposób: **adl://data_lake_store_name.azuredatalakestore.net:443/nazwa_folderu/nazwa_pliku**.

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


Gdy zapytanie na zakończenie działania powinny zostać wyświetlone wyniki następująco:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Tworzenie i wdrażanie modeli w usłudze Azure Machine Learning Studio
Teraz można przystąpić do tworzenia i wdrażania modelu przewidującego czy Porada zapłacono za pomocą usługi Azure Machine Learning. Stratyfikowana przykładowych danych jest gotowy do użycia w tym klasyfikacji binarnej (Porada lub nie) problem. Modele predykcyjne przy użyciu klasyfikacji wieloklasowej (tip_class) i regresji (tip_amount) również można skompilować i wdrożyć za pomocą usługi Azure Machine Learning Studio, ale w tym miejscu go jest wyświetlana tylko w sposób obsługi w przypadku, używając model klasyfikacji binarnej.

1. Pobierz dane w usłudze Azure Machine Learning studio, używając **importu danych** moduł dostępny w **danych wejściowych i wyjściowych** sekcji. Aby uzyskać więcej informacji, zobacz [modułu importu danych](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) odwołania do stron.
2. Wybierz **zapytania programu Hive** jako **źródła danych** w **właściwości** panelu.
3. Wklej poniższy skrypt programu Hive w **zapytanie bazy danych Hive** edytora

        select * from nyc_stratified_sample;
4. Wprowadź identyfikator URI HDInsight klastra (ten znajduje się w witrynie Azure portal), poświadczeń usługi Hadoop, lokalizacja danych wyjściowych i nazwę kontenera nazw/klucza konta magazynu platformy Azure.

   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)

Przykład klasyfikacji binarnej odczytu eksperymentu, których dane z tabeli programu Hive jest wyświetlany na poniższej ilustracji:

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

Po utworzeniu eksperymentu, kliknij **ustawić usługę sieci Web** --> **predykcyjne usługi sieci Web**

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Uruchom automatycznie utworzone oceniania eksperymentu, po zakończeniu tej operacji, kliknij przycisk **wdrażanie usługi sieci Web**

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

Pulpit nawigacyjny usług sieci web wyświetla wkrótce:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Podsumowanie
Przez ukończenie tego instruktażu utworzono środowiska nauki o danych, umożliwiające tworzenie skalowalnych rozwiązań end-to-end w usłudze Azure Data Lake. To środowisko zostało użyte do analizowania dużych publicznego zestawu danych, poruszają canonical kroki procesu do nauki o danych z pozyskiwanie danych za pomocą szkoleń modelowych, a następnie wdrożenie modelu jako usługi sieci web. U-SQL został użyty do przetwarzania, eksplorować i przykładowe dane. Python i Hive zostały użyte do tworzenia i wdrażania modeli predykcyjnych za pomocą usługi Azure Machine Learning Studio.

## <a name="whats-next"></a>Co dalej?
Ścieżka szkoleniowa dotycząca [Team Data Science naukowych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) zawiera łącza do tematów opisujących każdy krok w procesie zaawansowanej analizy. Istnieje szereg wskazówki podzielonych na [wskazówki dotyczące procesu do nauki o danych zespołu](walkthroughs.md) strony, które pokazują, jak korzystać z zasobów i usług w różnych scenariuszach analizy predykcyjnej:

* [Zespół danych dla celów naukowych w działaniu: Korzystanie z programu SQL Data Warehouse](sqldw-walkthrough.md)
* [Zespół danych dla celów naukowych w działaniu: przy użyciu klastrów usługi HDInsight Hadoop](hive-walkthrough.md)
* [Zespół danych dla celów naukowych: przy użyciu programu SQL Server](sql-walkthrough.md)
* [Omówienie procesu do nauki o danych przy użyciu platformy Spark w usłudze Azure HDInsight](spark-overview.md)
