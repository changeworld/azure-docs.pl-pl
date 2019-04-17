---
title: Tworzenie i wdrażanie modelu przy użyciu SQL Data Warehouse — zespołu danych dla celów naukowych
description: Tworzenie i wdrażanie przy użyciu SQL Data Warehouse przy użyciu publicznie dostępnego zestawu danych model uczenia maszynowego.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/24/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e27c4462e7137145917d1284bfb6f8838e8a090b
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523600"
---
# <a name="the-team-data-science-process-in-action-using-sql-data-warehouse"></a>Zespół danych dla celów naukowych w działaniu: Korzystanie z programu SQL Data Warehouse
W tym samouczku, w jaki sposób Cię przez proces tworzenia i wdrażania modelu uczenia maszynowego, przy użyciu magazynu danych SQL (SQL data Warehouse) dla publicznie dostępnego zestawu danych — [rund taksówek NYC](https://www.andresmh.com/nyctaxitrips/) zestawu danych. Model klasyfikacji binarnej skonstruowany przewiduje czy Porada czy płatna komunikacji dwustronnej i modele wieloklasowej klasyfikacji i regresji zostały również omówione, które przewidzieć dystrybucja przypadku ilości Porada płatne.

Następujące procedury [Team Data Science naukowych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) przepływu pracy. Pokazujemy, jak skonfigurować środowisko do nauki o danych, jak załadować dane do magazynu danych SQL i jak za pomocą usługi SQL DW lub IPython Notebook Eksplorowanie danych i inżynier ds. funkcji do modelu. Następnie przedstawiono, jak utworzyć i wdrożyć model przy użyciu usługi Azure Machine Learning.

## <a name="dataset"></a>Zestaw danych podróży taksówek NYC
Dane podróży taksówek NYC składa się z około 20GB skompresowanych plików CSV (~ 48GB nieskompresowane), rejestrowanie ponad milion 173 poszczególnych podróży i opłaty opłacony każdego podróży. Każdy podróży rekord zawiera lokalizacji odbioru i dropoff i godziny, hack anonimowe (sterownika) numer licencji i numer Medalionu (unikatowy identyfikator dla taksówek). Dane obejmuje wszystkie podróży w roku 2013 i znajduje się w następujących dwóch zestawów danych w każdym miesiącu:

1. **Trip_data.csv** plik zawiera szczegóły podróży, np. liczby pasażerów, wybieranie i dropoff punkty, czasu trwania podróży i długość podróży. Poniżej przedstawiono kilka przykładowych rekordów:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. **Trip_fare.csv** plik zawiera szczegółowe informacje o opłatę za każdym razem, takich jak typ płatności, kwota taryfy, opłata za opcję i podatków, porady i drogi, a łączna kwota płatne. Poniżej przedstawiono kilka przykładowych rekordów:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

**Unikatowy klucz** użyte w celu przyłączenia podróży\_danych i podróży\_taryfy składa się z trzech następujących pól:

* Medalionu,
* hack\_licencji i
* pobranie\_daty/godziny.

## <a name="mltasks"></a>Adres trzy typy zadań prognoz
Firma Microsoft sformułować trzy problemów prognozowania na podstawie *Porada\_kwota* aby zilustrować trzy rodzaje zadań do modelowania:

1. **Klasyfikacja binarna**: Do prognozowania czy Porada zapłacono w podróży, tj *Porada\_kwota* większą niż 0 zł jest przykładem dodatnią, podczas gdy *Porada\_kwota* wynosi 0 zł jest ujemna przykład.
2. **Klasyfikacji wieloklasowej**: Do prognozowania zakresu Porada opłacony podróż. Możemy podzielić *Porada\_kwota* do pięciu pojemniki lub klasy:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **Zadanie regresji**: Przewidywanie ilość Porada płatne komunikacji dwustronnej.

## <a name="setup"></a>Konfigurowanie środowiska nauki o danych platformy Azure zaawansowanej analizy
Aby skonfigurować środowisko nauki o danych platformy Azure, wykonaj następujące kroki.

**Utwórz swoje konto magazynu obiektów blob platformy Azure**

* Podczas aprowizowania magazynu obiektów blob platformy Azure wybierz lokalizacji geograficznej dla usługi Azure blob storage w lub możliwie jak najbardziej zbliżone do **południowo-środkowe stany USA**, czyli, gdzie są przechowywane dane taksówek NYC. Dane zostaną skopiowane, za pomocą narzędzia AzCopy z publicznego kontenera obiektów blob storage do kontenera na koncie magazynu. Im bliżej usługi Azure blob storage jest południowo-środkowe stany USA, tym szybciej będzie można ukończyć tego zadania (krok 4).
* Aby utworzyć konta magazynu platformy Azure, wykonaj czynności opisane w [kontach magazynu Azure o](../../storage/common/storage-create-storage-account.md). Pamiętaj robić notatki na podstawie wartości dla następujących poświadczeń konta magazynu, ponieważ będą one potrzebne w dalszej części tego przewodnika.

  * **Nazwa konta magazynu**
  * **Klucz konta magazynu**
  * **Nazwa kontenera** (co chcesz, aby dane były przechowywane w usłudze Azure blob storage)

**Aprowizowanie wystąpienia usługi Azure SQL data Warehouse.**
Postępuj zgodnie z dokumentacją pod adresem [utworzyć SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) do aprowizacji wystąpienia SQL Data Warehouse. Upewnij się, możesz wprowadzać notacji w następujących poświadczeń SQL Data Warehouse, które będą używane w kolejnych krokach.

* **Nazwa serwera**: \<server Name >. database.windows.net
* **Nazwa SQLDW (baza danych)**
* **Nazwa użytkownika**
* **Hasło**

**Zainstaluj program Visual Studio i SQL Server Data Tools.** Aby uzyskać instrukcje, zobacz [instalacji programu Visual Studio 2015 i/lub program SSDT (SQL Server Data Tools) dla usługi SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Nawiązać połączenia usługi Azure SQL data Warehouse z programem Visual Studio.** Aby uzyskać instrukcje, zobacz kroki 1 i 2 w [nawiązywanie połączenia z usługi Azure SQL Data Warehouse z programem Visual Studio](../../sql-data-warehouse/sql-data-warehouse-connect-overview.md).

> [!NOTE]
> Uruchom następujące zapytanie SQL w bazie danych utworzonej w usłudze SQL Data Warehouse (zamiast dostarczone w kroku 3 w temacie connect zapytanie) do **Utwórz klucz główny**.
>
>

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Tworzenie obszaru roboczego usługi Azure Machine Learning, w ramach Twojej subskrypcji platformy Azure.** Aby uzyskać instrukcje, zobacz [Tworzenie obszaru roboczego usługi Azure Machine Learning](../studio/create-workspace.md).

## <a name="getdata"></a>Ładowanie danych do usługi SQL Data Warehouse
Otwórz konsolę polecenia programu Windows PowerShell. Uruchom następujące polecenie programu PowerShell polecenia, aby pobrać przykład SQL script pliki, które możemy udostępnić Ci w witrynie GitHub do katalogu lokalnego, określ parametr *- DestDir*. Można zmienić wartości parametru *- DestDir* do dowolnego katalogu lokalnego. Jeśli *- DestDir* nie istnieje, zostanie on utworzony za pomocą skryptu programu PowerShell.

> [!NOTE]
> Konieczne może być **Uruchom jako Administrator** podczas wykonywania następującego skryptu programu PowerShell, jeśli Twoja *DestDir* katalogu potrzeb uprawnienia administratora, aby utworzyć lub zapisanie w nim.
>
>

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Po pomyślnym wykonaniu Twojego bieżącego katalogu roboczego zmienia się na *- DestDir*. Powinno być możliwe zobaczyć ekran, takich jak poniżej:

![Zmiany w bieżącym katalogu roboczym][19]

W swojej *- DestDir*, uruchom następujący skrypt programu PowerShell w trybie administratora:

    ./SQLDW_Data_Import.ps1

Gdy skrypt programu PowerShell jest uruchamiany po raz pierwszy, pojawi się prośba o podanie informacji z usługi SQL data Warehouse platformy Azure i konta magazynu obiektów blob platformy Azure. Po zakończeniu tego skryptu programu PowerShell uruchomiony po raz pierwszy, poświadczenia danych wejściowych można będzie zapisanych w pliku konfiguracji SQLDW.conf w aktualnym katalogiem roboczym. Przyszłe Uruchom ten plik skryptu programu PowerShell może odczytywać wszystkie wymagane parametry, z tego pliku konfiguracji. Jeśli potrzebujesz zmienić niektóre parametry, można wybrać wprowadzanie parametrów na ekranie po wierszu, usuwając ten plik konfiguracji i wprowadzanie wartości parametrów, zgodnie z monitem lub zmienić wartości parametrów, edytując plik SQLDW.conf w Twojej *- DestDir* katalogu.

> [!NOTE]
> Aby uniknąć konfliktów nazw schematu z tymi, które już istnieją w usługi Azure SQL data Warehouse, podczas odczytywania parametrów bezpośrednio z pliku SQLDW.conf, 3-cyfrowy numer losowe jest dodawany do nazwy schematu z pliku SQLDW.conf jako domyślna nazwa schematu dla każdego uruchomienia. Skrypt programu PowerShell mogą wyświetlenie monitu o nazwę schematu: Nazwa może być określone w gestii użytkownika.
>
>

To **skrypt programu PowerShell** plików wykonuje następujące zadania:

* **Pobiera i instaluje narzędzie AzCopy**, jeśli narzędzie AzCopy nie jest już zainstalowany.

        $AzCopy_path = SearchAzCopy
        if ($AzCopy_path -eq $null){
               Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
            InstallAzCopy
            $AzCopy_path = SearchAzCopy
        }
            $env_path = $env:Path
            for ($i=0; $i -lt $AzCopy_path.count; $i++){
                if ($AzCopy_path.count -eq 1){
                    $AzCopy_path_i = $AzCopy_path
                } else {
                    $AzCopy_path_i = $AzCopy_path[$i]
                }
                if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
                    Write-Host $AzCopy_path_i 'not in system path, add it...'
                    [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
                    $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
                    $env_path = $env:Path
                }
* **Kopiuje dane do swojego konta magazynu prywatnego obiektu blob** z publicznego obiektu blob za pomocą narzędzia AzCopy

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
* **Ładuje dane do usługi w usłudze Azure SQL data Warehouse przy użyciu technologii Polybase (wykonując LoadDataToSQLDW.sql)** z kontem magazynu prywatnego obiektu blob za pomocą następujących poleceń.

  * Utwórz schemat

          EXEC (''CREATE SCHEMA {schemaname};'');
  * Tworzenie poświadczeń o zakresie bazy danych

          CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
          WITH IDENTITY = ''asbkey'' ,
          Secret = ''{StorageAccountKey}''
  * Tworzenie zewnętrznego źródła danych dla usługi Azure storage blob

          CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;

          CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;
  * Utwórz formatu pliku zewnętrznego pliku csv. Data jest bez kompresji i pola są rozdzielone znakiem kreski pionowej.

          CREATE EXTERNAL FILE FORMAT {csv_file_format}
          WITH
          (
              FORMAT_TYPE = DELIMITEDTEXT,
              FORMAT_OPTIONS
              (
                  FIELD_TERMINATOR ='','',
                  USE_TYPE_DEFAULT = TRUE
              )
          )
          ;
  * Tworzenie zewnętrznego klasie i tabele podróży dla zestawu danych taksówek NYC w usłudze Azure blob storage.

          CREATE EXTERNAL TABLE {external_nyctaxi_fare}
          (
              medallion varchar(50) not null,
              hack_license varchar(50) not null,
              vendor_id char(3),
              pickup_datetime datetime not null,
              payment_type char(3),
              fare_amount float,
              surcharge float,
              mta_tax float,
              tip_amount float,
              tolls_amount float,
              total_amount float
          )
          with (
              LOCATION    = ''/nyctaxifare/'',
              DATA_SOURCE = {nyctaxi_fare_storage},
              FILE_FORMAT = {csv_file_format},
              REJECT_TYPE = VALUE,
              REJECT_VALUE = 12
          )

            CREATE EXTERNAL TABLE {external_nyctaxi_trip}
            (
                   medallion varchar(50) not null,
                   hack_license varchar(50)  not null,
                   vendor_id char(3),
                   rate_code char(3),
                   store_and_fwd_flag char(3),
                   pickup_datetime datetime  not null,
                   dropoff_datetime datetime,
                   passenger_count int,
                   trip_time_in_secs bigint,
                   trip_distance float,
                   pickup_longitude varchar(30),
                   pickup_latitude varchar(30),
                   dropoff_longitude varchar(30),
                   dropoff_latitude varchar(30)
            )
            with (
                LOCATION    = ''/nyctaxitrip/'',
                DATA_SOURCE = {nyctaxi_trip_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12
            )

    - Ładowanie danych z tabel zewnętrznych w usłudze Azure blob storage do usługi SQL Data Warehouse

            CREATE TABLE {schemaname}.{nyctaxi_fare}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_fare}
            ;

            CREATE TABLE {schemaname}.{nyctaxi_trip}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_trip}
            ;

    - Utwórz przykładową tabelę danych (NYCTaxi_Sample) i wstawianie danych do niego z wybraniu zapytania SQL w tabelach podróży i klasie. (Niektóre kroki tego przewodnika musi używać tej przykładowej tabeli.)

            CREATE TABLE {schemaname}.{nyctaxi_sample}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            (
                SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
                tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
                tip_class = CASE
                        WHEN (tip_amount = 0) THEN 0
                        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                        ELSE 4
                    END
                FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
                WHERE datepart("mi",t.pickup_datetime) = 1
                AND t.medallion = f.medallion
                AND   t.hack_license = f.hack_license
                AND   t.pickup_datetime = f.pickup_datetime
                AND   pickup_longitude <> ''0''
                AND   dropoff_longitude <> ''0''
            )
            ;

Geograficzna lokalizacja konta magazynu ma wpływ na czas ładowania.

> [!NOTE]
> W zależności od lokalizacji geograficznej swojego konta magazynu prywatnego obiektu blob, proces kopiowania danych z publicznego obiektu blob na koncie magazynu prywatnego może potrwać około 15 minut, a nawet dłużej, a proces ładowania danych z konta magazynu do subskrypcji platformy Azure SQL data Warehouse może trwać 20 minut lub dłużej.
>
>

Musisz zdecydować co robią, jeśli masz zduplikowane pliki źródłowe i docelowe.

> [!NOTE]
> Pliki CSV, skopiowane z magazynu publicznego obiektu blob na koncie magazynu prywatnego obiektu blob już istnieje na koncie magazynu prywatnego obiektu blob, narzędzia AzCopy zapyta, czy chcesz je zastąpić. Jeśli nie chcesz je zastąpić, dane wejściowe **n** po wyświetleniu monitu. Jeśli chcesz zastąpić **wszystkie** z nich, wprowadź po wyświetleniu monitu. Można również wpisać **y** indywidualnie zastąpienie plików CSV.
>
>

![Dane wyjściowe z narzędzia AzCopy][21]

Można użyć własnych danych. W przypadku danych na maszynie lokalnej w realnym aplikacji, możesz nadal używać narzędzia AzCopy do przekazania danych lokalnych do swojej prywatnej usługi Azure blob storage. Musisz zmienić **źródła** lokalizacji `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, w poleceniu narzędzia AzCopy pliku skryptu programu PowerShell do katalogu lokalnego, które zawiera dane.

> [!TIP]
> Jeśli danych jest już w sieci prywatnej usługi Azure blob storage w realnym aplikacji, możesz pominąć ten krok narzędzia AzCopy w skrypcie programu PowerShell i bezpośrednio przekazać dane do usługi Azure SQL data Warehouse. Będzie to wymagało dodatkowej edycji skrypt, aby dopasować ją do formatu danych.
>
>

Ten skrypt programu Powershell także podłączyć w informacjach o usłudze Azure SQL data Warehouse do pliki przykładowe eksploracji danych SQLDW_Explorations.sql SQLDW_Explorations.ipynb i SQLDW_Explorations_Scripts.py tak, aby te trzy pliki są gotowe do próby natychmiast po Wykonuje skrypt programu PowerShell.

Po pomyślnym wykonaniu, zostanie wyświetlony ekran, takich jak poniżej:

![Dane wyjściowe wykonania pomyślnego skryptu][20]

## <a name="dbexplore"></a>Eksplorowanie danych i inżynieria funkcji w usłudze Azure SQL Data Warehouse
W tej sekcji wykonamy Generowanie funkcji i eksploracji danych dzięki uruchamianiu zapytania SQL bezpośrednio przy użyciu usługi Azure SQL DW **Visual Studio Tools danych**. Wszystkie zapytania SQL używany w tej sekcji znajdują się w przykładowym skrypcie o nazwie *SQLDW_Explorations.sql*. Ten plik został już pobrany z katalogiem lokalnym za pomocą skryptu programu PowerShell. Możesz również pobrać go z [GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Ale pliku w usłudze GitHub nie ma informacji usługi Azure SQL data Warehouse jest podłączony.

Nawiązywanie połączenia usługi Azure SQL data Warehouse przy użyciu usługi SQL DW, nazwa logowania i hasła przy użyciu programu Visual Studio i Otwórz **Eksplorator obiektów SQL** aby upewnić się, bazy danych i tabele zostały zaimportowane. Pobieranie *SQLDW_Explorations.sql* pliku.

> [!NOTE]
> Aby otworzyć Edytor zapytań równolegle magazynu danych (PDW), użyj **nowe zapytanie** polecenia, gdy Twoje PDW jest zaznaczony w **Eksplorator obiektów SQL**. Standardowa edytorowi zapytań SQL nie jest obsługiwany przez PDW.
>
>

Poniżej przedstawiono typu danych eksploracji i funkcji generowania na błędy zadania wykonywane w tej sekcji:

* Zapoznaj się z dystrybucji danych kilka pól w różnych okna czasowe.
* Zbadaj dobrej jakości danych pola długości i szerokości geograficznej.
* Generowanie etykiety binarne i wieloklasowej klasyfikacji na podstawie **Porada\_kwota**.
* Generowanie funkcji i obliczeń/compare odległości podróży.
* Dołącz do dwóch tabel i Wyodrębnij losowej próbki, która będzie służyć do tworzenia modeli.

### <a name="data-import-verification"></a>Weryfikacja importu danych
Te zapytania zapewniają szybki weryfikacji liczbę wierszy i kolumn w tabelach wypełniać wcześniej przy użyciu technologii Polybase firmy równoległy zbiorczy zaimportować,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Dane wyjściowe:** Powinna pojawić się 173,179,759 wierszy i kolumn 14.

### <a name="exploration-trip-distribution-by-medallion"></a>Eksploracja: Dystrybucja podróży według Medalionu
To przykładowe zapytanie identyfikuje medallions (taksówek liczb), wykonanych więcej niż 100 podróży w określonym przedziale czasu. Zapytania będą korzystać z dostępu do tabeli partycjonowanej, ponieważ w schemacie partycji należy przygotować **odbioru\_daty/godziny**. Wykonywanie zapytań pełnego zestawu danych spowoduje również, że użycie tabeli partycjonowanej i/lub indeksu skanowania.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Dane wyjściowe:** Zapytanie powinno zwrócić tabelę z wierszami, określając 13,369 medallions (taksówek) i liczby podróży ukończone przez nich w 2013. Ostatnia kolumna zawiera liczbę rund ukończone.

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Eksploracja: Dystrybucja podróży Medalionu i hack_license
W tym przykładzie identyfikuje medallions (numery taksówek) i hack_license cyfry (sterowniki) zakończona ponad 100 podróży w określonym przedziale czasu.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Dane wyjściowe:** Zapytanie powinno zwrócić tabelę z wierszami 13,369, określając 13,369 samochodu/driver identyfikatory, które zostały wykonane więcej tej operacji 100 2013. Ostatnia kolumna zawiera liczbę rund ukończone.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Ocena jakości danych: Weryfikowanie rekordów za pomocą współrzędnych niepoprawne i/lub szerokość geograficzną
W tym przykładzie bada, jeśli żadnego pola geograficzne i/lub szerokość geograficzną albo zawiera nieprawidłową wartość (stopnie radianach powinna być od-90 do 90), lub (0, 0) współrzędnych.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Dane wyjściowe:** Zapytanie zwraca 837,467 wymianie danych, które mają nieprawidłowe pola geograficzne i/lub szerokość geograficzną.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Eksploracja: Przechylony a dystrybucji nie Przechylony podróży
W tym przykładzie wyszukuje Liczba podróży, które zostały Przechylony a liczbą, które nie zostały Przechylony w określonym przedziale czasu (lub pełnego zestawu danych, jeśli obejmujące pełny rok, ponieważ jest skonfigurowana w tym miejscu). Tej dystrybucji odzwierciedla dystrybucji binarne etykiety później służący do modelowania klasyfikacji binarnej.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Dane wyjściowe:** Zapytanie powinno zwrócić następujące częstotliwości Porada w roku 2013: 90,447,622 Przechylony i 82,264,709 Przechylony nie.

### <a name="exploration-tip-classrange-distribution"></a>Eksploracja: Porada dystrybucji klasy/zakresu
W tym przykładzie oblicza rozkład Porada zakresów w danym okresie czasu (lub pełnego zestawu danych, jeśli obejmujące pełny rok). To jest podział klasy etykiety, które będą potrzebne później do modelowania klasyfikacji wieloklasowej.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

**Dane wyjściowe:**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Eksploracja: Obliczeń i porównywanie odległość podróży
Ten przykład konwertuje odbiór i dropoff długości geograficznej i szerokości geograficznej do lokalizacji geograficznej SQL punktów oblicza odległość podróży za pomocą różnicę punktów lokalizacji geograficznej SQL i zwraca losowej próbki wyniki porównania. Przykład ogranicza wyniki do prawidłowe współrzędne wyłącznie przy użyciu zapytania oceny jakości danych omówione wcześniej.

    /****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

### <a name="feature-engineering-using-sql-functions"></a>Korzystanie z funkcji SQL technicznego opracowywania funkcji
Czasami funkcji SQL może być opcją wydajne potrzeby inżynierii cech. W tym przewodniku zdefiniowaliśmy funkcji SQL, aby obliczyć bezpośrednie odległość między lokalizacjami odbiór i dropoff. Możesz uruchomić następujące skrypty SQL w **Visual Studio Tools danych**.

Oto skrypt SQL, który definiuje funkcję odległości.

    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function calculate the direct distance between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

Oto przykład, aby wywołać tę funkcję, aby wygenerować funkcji w zapytaniu SQL:

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Dane wyjściowe:** To zapytanie generuje tabeli (z wierszami 2,803,538) przy użyciu odbiór i dropoff długości i szerokości geograficzne i odpowiednie odległości bezpośrednie w milach. Poniżej przedstawiono wyniki dla pierwsze 3 wiersze:

|  | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40.731804 |-74.001083 |40.736622 |-73.988953 |.7169601222 |
| 2 |40.715794 |-74,010635 |40.725338 |-74.00399 |.7448343721 |
| 3 |40.761456 |-73.999886 |40.766544 |-73.988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Przygotowuje dane do konstruowania modelu
Poniższe zapytanie sprzęga **nyctaxi\_podróży** i **nyctaxi\_taryfy** tabele, generuje etykietę klasyfikacji binarnej **Przechylony**, Etykieta klasyfikacji wieloklasowej **Porada\_klasy**i wyodrębnia próbkę z pełnego dołączonym do zestawu danych. Próbkowanie odbywa się przez pobranie podzbiór podróży, na podstawie czasu odbioru.  To zapytanie może być skopiowane, a następnie wkleić bezpośrednio w [Azure Machine Learning Studio](https://studio.azureml.net) [importu danych] [ import-data] modułu w celu pozyskiwania danych bezpośredni z wystąpienia bazy danych SQL w Azure. Zapytanie wyklucza rekordy z niepoprawny (0, 0) współrzędnych.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

Gdy wszystko jest gotowe do przejścia do usługi Azure Machine Learning, użytkownik może:

1. Zapisz ostatecznemu zapytaniu SQL do wyodrębniania i przykładowe dane i zapytania bezpośrednio do kopiowania i wklejania [importu danych] [ import-data] modułu w usłudze Azure Machine Learning, lub
2. Utrwalanie danych próbkowanych i odtworzone, będą używane dla modelu, tworzenie nowej tabeli SQL data Warehouse i używać nowej tabeli w [importu danych] [ import-data] modułu w usłudze Azure Machine Learning. Skrypt programu PowerShell w poprzednim kroku zrobił to dla Ciebie. Możesz przeczytać bezpośrednio z tej tabeli w modułu importu danych.

## <a name="ipnb"></a>Eksplorowanie danych i inżynieria funkcji w IPython notebook
W tej sekcji zostaną wykonane eksploracji danych i generowania funkcji przy użyciu zarówno języka Python i zapytania SQL w odniesieniu do usługi SQL DW utworzone wcześniej. Program IPython notebook próbki o nazwie **SQLDW_Explorations.ipynb** oraz pliku skryptu języka Python **SQLDW_Explorations_Scripts.py** zostały pobrane z katalogiem lokalnym. Są one również dostępne w [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Te dwa pliki są identyczne w skryptach języka Python. Plik skryptu języka Python jest udostępniany w przypadku, gdy nie masz serwera IPython Notebook. Te dwa przykładowe pliki zostały opracowane w ramach Python **środowisko Python 2.7**.

Magazyn danych SQL Azure informacje wymagane w próbce IPython Notebook oraz pliku skryptu języka Python pobrane na komputer lokalny został podłączony za pomocą skryptu programu PowerShell wcześniej. Są one pliku wykonywalnego bez żadnych modyfikacji.

Jeśli obszar roboczy usługi Azure ml ma już skonfigurowane, możesz bezpośrednio przekazać przykładowe IPython Notebook do usługi Azure ml IPython Notebook i uruchom go. Poniżej przedstawiono kroki, aby przekazać do usługi Azure ml IPython Notebook:

1. Zaloguj się do swojego obszaru roboczego usługi Azure ml, kliknij przycisk "Studio" u góry, a następnie kliknij przycisk "NOTESÓW" po lewej stronie strony sieci web.

    ![Kliknij przycisk Studio, a następnie NOTESÓW][22]
2. Kliknij przycisk "Nowy" w lewym dolnym rogu strony sieci web, a następnie wybierz pozycję "Python 2". Następnie podaj nazwę w notesie i kliknij znacznik wyboru, aby utworzyć nowy pusty IPython Notebook.

    ![Kliknij pozycję Nowy, a następnie wybierz opcję 2 dla języka Python][23]
3. Kliknij symbol "Jupyter" w lewym górnym rogu nowe IPython Notebook.

    ![Kliknij Jupyter symbol][24]
4. Przeciąganie i upuszczanie przykładowe IPython Notebook na **drzewa** strony z usługi Azure ml IPython Notebook, a następnie kliknij przycisk **przekazywanie**. Następnie przykład IPython Notebook zostanie przekazany do usługi Azure ml IPython Notebook.

    ![Kliknij przycisk Przekaż][25]

Aby można było uruchomić przykładowy plik, Python następujące pakiety są wymagane skryptu IPython Notebook lub Python. Jeśli używasz usługi Azure ml IPython Notebook te pakiety zostały wstępnie zainstalowane.

    - pandas
    - numpy
    - matplotlib
    - pyodbc
    - PyTables

Zalecana kolejność podczas tworzenia zaawansowanych rozwiązań analitycznych na usługę Azure ml z dużych ilości danych jest następująca:

* Przeczytaj w niewielką próbkę danych do ramki danych w pamięci.
* Wykonaj niektóre wizualizacje i eksploracji przy użyciu próbki danych.
* Poeksperymentuj z technicznego opracowywania funkcji przy użyciu próbki danych.
* Dla większych eksplorację danych, manipulowania danymi i technicznego opracowywania funkcji wysyłać zapytania SQL bezpośrednio w odniesieniu do usługi SQL DW przy użyciu języka Python.
* Zdecyduj, czy rozmiar próbki za właściwe do konstruowania modelu usługi Azure Machine Learning

Temacie przedstawiono kilka eksplorację danych, wizualizacji danych i funkcji inżynierii przykłady. Więcej eksploracji danych można znaleźć w przykładzie IPython Notebook oraz przykładowy plik skryptu języka Python.

### <a name="initialize-database-credentials"></a>Inicjowanie poświadczenia bazy danych
Inicjuj ustawienia połączenia bazy danych w następujących zmiennych:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Utwórz połączenie z bazą danych
Poniżej przedstawiono parametry połączenia, które tworzy połączenie z bazą danych.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Raport liczba wierszy i kolumn w tabeli < nyctaxi_trip >
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Całkowita liczba wierszy = 173179759
* Łączna liczba kolumn = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxifare"></a>Raport liczba wierszy i kolumn w tabeli < nyctaxi_fare >
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Całkowita liczba wierszy = 173179759
* Łączna liczba kolumn = 11

### <a name="read-in-a-small-data-sample-from-the-sql-data-warehouse-database"></a>Przykład małych danych z bazy danych magazynu danych SQL w odczytu
    t0 = time.time()

    query = '''
        SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
        WHERE datepart("mi",t.pickup_datetime) = 1
        AND   t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Czas odczytu z przykładowej tabeli jest 14.096495 sekund.
Liczba wierszy i kolumn pobrać = (1000, 21).

### <a name="descriptive-statistics"></a>Statystyki opisowe
Teraz można przystąpić do eksplorowania próbki danych. Rozpoczniemy pracę patrząc statystykami opisowe dla **podróży\_odległość** (lub wszelkie inne pola, które chcesz określić).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Wizualizacji: Przykład wykres pola
Następnie przyjrzymy się skrzynkowy odległość podróży quantiles wizualizacji.

    df1.boxplot(column='trip_distance',return_type='dict')

![Okno danych wyjściowych z wykresu][1]

### <a name="visualization-distribution-plot-example"></a>Wizualizacji: Przykładowy diagram dystrybucji
Wykresy wizualizowaniem dystrybucji i histogram odległości próbkowanych podróży.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Diagram dystrybucji w danych wyjściowych][2]

### <a name="visualization-bar-and-line-plots"></a>Wizualizacji: Pasek i wykresy liniowe
W tym przykładzie firma Microsoft bin odległość podróży do pięciu pojemników, a wizualizacja wyników pakowania.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Możemy wykresu powyżej dystrybucji bin na pasku lub linii wykresu za pomocą:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Pasek wykresu danych wyjściowych][3]

i

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Dane wyjściowe wiersza wykresu][4]

### <a name="visualization-scatterplot-examples"></a>Wizualizacji: Przykłady wykres punktowy
Pokazujemy, wykres punktowy między **podróży\_czasu\_w\_s** i **podróży\_odległość** można sprawdzić, czy jest wszelka korelacja

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Wykres punktowy danych wyjściowych relacji między czasem i odległości][6]

Podobnie można sprawdzić relacje między **współczynnik\_kodu** i **podróży\_odległość**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Wykres punktowy danych wyjściowych relacji między kodem i odległości][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Eksplorowanie danych na próbki danych przy użyciu zapytań SQL w IPython notebook
W tej sekcji omówimy dystrybucji danych przy użyciu próbki danych, które są utrwalane w nowej tabeli, którą utworzyliśmy powyżej. Należy pamiętać, że podobne eksploracji mogą być wykonywane przy użyciu oryginalnego tabel.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Eksploracja: Raport liczba wierszy i kolumn w tabeli próbki
    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Eksploracja: Przechylony nie zwrotnego dystrybucji
    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Eksploracja: Porada dystrybucja klasy
    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Eksploracja: Wykreślania dystrybucji poradę, klasa
    tip_class_dist['tip_freq'].plot(kind='bar')

![#26 wykreślania][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Eksploracja: Codzienne dystrybucji podróży
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Eksploracja: Dystrybucja podróży na Medalionu
    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Eksploracja: Dystrybucja podróży według Medalionu i hack licencji
    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Eksploracja: Rozkład czasów podróży
    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Eksploracja: Dystrybucja odległość podróży
    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Eksploracja: Rozkład typu płatności
    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Sprawdź ostatecznej postaci tabeli neural
    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Twórz modele w usłudze Azure Machine Learning
Teraz jesteśmy gotowi przejść do konstruowania modelu i wdrażania modelu w [usługi Azure Machine Learning](https://studio.azureml.net). Dane są gotowe do użycia w jednym z problemów prognozowania wymienionych wcześniej, to znaczy:

1. **Klasyfikacja binarna**: Przewidywanie czy Porada zapłacono komunikacji dwustronnej.
2. **Klasyfikacji wieloklasowej**: Do prognozowania zakresu Porada płatną zgodnie z wcześniej zdefiniowanych klas.
3. **Zadanie regresji**: Przewidywanie ilość Porada płatne komunikacji dwustronnej.

Aby rozpocząć wykonywania modelowania, zaloguj się do Twojej **usługi Azure Machine Learning** obszaru roboczego. Jeśli jeszcze nie utworzono obszaru roboczego uczenia maszynowego, zobacz [Utwórz obszar roboczy usługi Azure Machine Learning studio](../studio/create-workspace.md).

1. Aby rozpocząć pracę z usługą Azure Machine Learning, zobacz [co to jest Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Zaloguj się do [usługi Azure Machine Learning Studio](https://studio.azureml.net).
3. Strona główna Studio udostępnia mnóstwo informacji, pliki wideo, samouczki, łącza do dokumentacji dotyczącej modułów i innych zasobów. Aby uzyskać więcej informacji na temat usługi Azure Machine Learning, zapoznaj się [Centrum dokumentacji usługi Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Typowe eksperyment składa się z następujących czynności:

1. Tworzenie **+ nowy** eksperymentować.
2. Pobierz dane do usługi Azure Machine Learning studio.
3. Wstępne przetwarzanie, przekształcania i manipulowania danymi zgodnie z potrzebami.
4. Generowanie funkcji zgodnie z potrzebami.
5. Podzielić dane na szkolenie i sprawdzania poprawności/testowania zestawów danych (lub rozdzielanie zestawów danych dla każdego).
6. Wybierz co najmniej jeden algorytmów uczenia maszynowego w zależności od problemu nauki do rozwiązania. Np. klasyfikacji binarnej, wieloklasowej klasyfikacji, regresji.
7. Szkolenie jednego lub więcej modeli przy użyciu zestawu danych szkoleniowych.
8. Wynik sprawdzania poprawności zestawu danych za pomocą wytrenowane modele.
9. Ocena modeli można obliczyć metryki istotne dla nauczanym problemem.
10. Prawidłowo dostrojenie modele i wybrać najlepszy model wdrażania.

W tym ćwiczeniu mamy już zbadane i zaprojektowany danych w usłudze SQL Data Warehouse i ustalony rozmiar próbki do pozyskania w usłudze Azure Machine Learning studio. Poniżej przedstawiono procedurę tworzenia co najmniej jeden modele predykcyjne:

1. Pobierz dane w usłudze Azure Machine Learning studio, używając [importu danych] [ import-data] moduł dostępny w **danych wejściowych i wyjściowych** sekcji. Aby uzyskać więcej informacji, zobacz [importu danych] [ import-data] odwołania modułu do stron.

    ![Usługa Azure ML importu danych][17]
2. Wybierz **usługi Azure SQL Database** jako **źródła danych** w **właściwości** panelu.
3. Wprowadź nazwę DNS bazy danych w **nazwy serwera bazy danych** pola. Format: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Wprowadź **Nazwa bazy danych** w odpowiednich pól.
5. Wprowadź *nazwa użytkownika SQL* w **nazwę konta użytkownika serwera**i *hasło* w **hasło konta użytkownika serwera**.
7. W **zapytanie bazy danych** edytowania obszaru tekstu, Wklej zapytanie, który wyodrębnia pola niezbędne bazy danych (w tym wszelkie pola obliczane, takie jak etykiety) i w dół próbkuje dane, do rozmiaru próbki żądaną.

Przykład eksperymentu klasyfikacji binarnej, odczytywanie danych bezpośrednio z bazy danych SQL Data Warehouse to na poniższej ilustracji (Pamiętaj, aby zastąpić nyctaxi_trip nazwy tabeli i nyctaxi_fare według nazwy schematu i nazwy tabel, używany w przewodniku sieci). Podobne eksperymenty można konstruować wieloklasowej klasyfikacji i regresji problemów.

![Szkolenie usługi Azure ML][10]

> [!IMPORTANT]
> W danych modelowania wyodrębniania i próbkowanie przykłady zapytań dostarczane w poprzednich sekcjach **wszystkie etykiety dla trzech ćwiczenia modelowania są uwzględnione w zapytaniu**. Ważnym krokiem (wymagane), w każdym z ćwiczeniami modelowania jest **wykluczyć** niepotrzebne etykiety dla innych problemów oraz wszelkie inne **docelowe przecieki**. Na przykład, korzystając z klasyfikacji binarnej, należy użyć etykiety **Przechylony** i wykluczyć pola **Porada\_klasy**, **Porada\_kwota**i **całkowita\_kwota**. Są one przecieki docelowy od momentu oznaczają porady płatne.
>
> Aby wykluczyć wszelkie niepotrzebne kolumny lub docelowe przecieków, może używać [Select Columns in Dataset] [ select-columns] modułu lub [edytować metadane][edit-metadata]. Aby uzyskać więcej informacji, zobacz [Select Columns in Dataset] [ select-columns] i [edytować metadane] [ edit-metadata] odwoływać się do strony.
>
>

## <a name="mldeploy"></a>Wdrażanie modeli w usłudze Azure Machine Learning
Gdy model jest gotowy, można łatwo wdrażać je jako usługi sieci web bezpośrednio z poziomu eksperymentu. Aby uzyskać więcej informacji na temat wdrażania usługi sieci web Azure ML, zobacz [wdrażanie usługi sieci web Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).

Aby wdrożyć nową usługę sieci web, należy:

1. Tworzenie eksperymentu oceniania.
2. Wdrażanie usługi sieci web.

Utworzyć eksperyment oceniania z **Zakończono** szkolenia eksperymentu, kliknij przycisk **tworzenie oceny EKSPERYMENTÓW** na dolnym pasku akcji.

![Ocenianie przez usługę Azure][18]

Usługa Azure Machine Learning spróbuje utworzyć eksperyment oceniania na podstawie składników eksperymentu szkolenia. W szczególności będą wykonywane następujące czynności:

1. Zapisania trenowanego modelu, a następnie usuń moduły szkolenie modelu.
2. Zidentyfikuj wartość logiczna **portem wejściowym modułu** do reprezentowania schematu oczekiwanych danych wejściowych.
3. Zidentyfikuj wartość logiczna **danych wyjściowych portu** reprezentujący schemat danych wyjściowych usługi oczekiwanego sieci web.

Po utworzeniu oceny eksperymentu, przejrzyj go i wprowadzić zmiany w miarę potrzeb. Typowe dopasowania jest Zamień wejściowy zestaw danych i/lub zapytanie taki, który wyklucza etykietę pola, jak te nie będą dostępne podczas wywoływania usługi. Jest również dobrym rozwiązaniem, aby zmniejszyć rozmiar wejściowy zestaw danych i/lub kwerendy na kilka rekordów wystarczający wskazać schemat danych wejściowych. Port wyjściowy jest wspólne dla wszystkich danych wejściowych pola Dołączanie i wykluczanie tylko **sklasyfikowane etykiety** i **oceniane prawdopodobieństwa** w danych wyjściowych za pomocą [Select Columns in Dataset] [ select-columns] modułu.

Przykład oceniania eksperymentu znajduje się na poniższej ilustracji. Gdy wszystko będzie gotowe do wdrożenia, kliknij przycisk **OPUBLIKOWAĆ usługi sieci WEB** przycisk na dolnym pasku akcji.

![Azure ML Publish][11]

## <a name="summary"></a>Podsumowanie
Na podsumowanie wykonaliśmy w tym samouczku wskazówki, utworzono środowisko do nauki o danych platformy Azure we współpracy z dużego zestawu danych publicznego, poruszają zespołu danych dla celów naukowych, od pozyskiwanie danych do szkolenia modelu, a następnie do Wdrażanie usługi sieci web Azure Machine Learning.

### <a name="license-information"></a>Informacje o licencji
Ten przewodnik po przykładzie i towarzyszące jej IPython notebook(s) i skrypty są udostępniane przez firmę Microsoft na licencji MIT. Sprawdź, czy w pliku LICENSE.txt w katalogu przykładowego kodu w serwisie GitHub Aby uzyskać więcej informacji.

## <a name="references"></a>Dokumentacja
• [Andrés Monroy taksówek NYC przesłania strony pobierania](https://www.andresmh.com/nyctaxitrips/) • [FOILing NYC taksówki danych podróży, Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/) • [taksówek NYC i Limousine Komisji badań i statystyk](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sqldw-walkthrough/azuremltrain.png
[11]: ./media/sqldw-walkthrough/azuremlpublish.png
[12]: ./media/sqldw-walkthrough/ssmsconnect.png
[13]: ./media/sqldw-walkthrough/executescript.png
[14]: ./media/sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/sqldw-walkthrough/bulkimport.png
[17]: ./media/sqldw-walkthrough/amlreader.png
[18]: ./media/sqldw-walkthrough/amlscoring.png
[19]: ./media/sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/sqldw-walkthrough/ps_load_data.png
[21]: ./media/sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
