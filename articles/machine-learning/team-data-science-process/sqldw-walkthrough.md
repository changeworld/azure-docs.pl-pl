---
title: 'Proces nauki danych zespołu w działaniu: przy użyciu usługi SQL Data Warehouse | Dokumentacja firmy Microsoft'
description: Proces zaawansowane metody analizy i technologii w akcji
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 88ba8e28-0bd7-49fe-8320-5dfa83b65724
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: deguhath
ms.openlocfilehash: 12eb182c859617139fce6553496c6aa6e9bcdc98
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839065"
---
# <a name="the-team-data-science-process-in-action-using-sql-data-warehouse"></a>Proces nauki danych zespołu w działaniu: przy użyciu magazynu danych SQL
W tym samouczku, możemy opisano tworzenie i wdrażanie modelu uczenia maszynowego przy użyciu magazynu danych SQL (SQL DW) dla elementu dataset publicznie dostępnych — [rund taksówki NYC](http://www.andresmh.com/nyctaxitrips/) zestawu danych. Model klasyfikacji binarnej skonstruowany prognozuje czy poradę ma być stosowany w podróży i modele wieloklasowej klasyfikacji i regresji omówiono także które prognozowania dystrybucji dla kwoty Porada płatnej.

Wykonuje procedurę [zespołu danych nauki procesu (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) przepływu pracy. Firma Microsoft pokazują, jak skonfigurować środowisko analizy danych, jak załadować dane do magazynu danych SQL i sposobie używania magazynu danych SQL lub notesu IPython, aby eksplorować dane i odtwarzania funkcje do modelu. Firma Microsoft następnie przedstawiono sposób tworzenia i wdrażania modelu przy użyciu usługi Azure Machine Learning.

## <a name="dataset"></a>Zestaw danych rund taksówki NYC
Dane podróży taksówki NYC składa się z około 20GB skompresowanego plików CSV (~ 48GB nieskompresowane), rejestrowanie ponad milion 173 poszczególnych podróży i opłaty płatnej dla każdej podróży. Każdy rekord podróży obejmuje odbiór i przyjmowania lokalizacje i godziny, hack anonimowe (sterownik) numer licencji i numer Medalionu (taksówki jego unikatowy identyfikator). Dane obejmuje wszystkie rund w roku 2013 i jest dostępne w następujących dwóch zestawów danych dla każdego miesiąca:

1. **Trip_data.csv** plik zawiera szczegóły podróży, takie jak liczba pasażerów, punkty odbiór i dropoff, czas trwania podróży i długość podróży. Poniżej przedstawiono kilka przykładowych rekordów:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. **Trip_fare.csv** pliku zawiera szczegółowe informacje o klasie za każdym razem, takie jak typ płatności, kwota taryfy, przeciążenia i podatków, porady i przejazd i sumy płatnej. Poniżej przedstawiono kilka przykładowych rekordów:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

**Unikatowy klucz** używane do przyłączania podróży\_danych i podróży\_taryfy składa się z trzech następujących pól:

* Medalionu,
* Funkcja\_licencji i
* pobranie\_daty/godziny.

## <a name="mltasks"></a>Adres trzy typy zadań prognozowania
Firma Microsoft sformułować trzy problemów prognozowania na podstawie *Porada\_kwota* ilustrujący trzy rodzaje modelowania zadań:

1. **Klasyfikacji binarnej**: przewidywanie czy poradę został płatnej podróży, tj. *Porada\_kwota* większą niż $0 jest przykład dodatnią, podczas gdy *Porada\_kwota* $ 0 to przykład ujemna.
2. **Wieloklasowej klasyfikacji**: przewidywanie zakres Porada uregulowaniu płatności podróży. Możemy podzielić *Porada\_kwota* do pięciu bins lub klasy:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **Zadanie regresji**: przewidywanie ilość Porada płatnej w podróży.  

## <a name="setup"></a>Konfigurowanie środowiska nauki danych Azure, zaawansowana analityka
Aby skonfigurować środowisko nauki danych Azure, wykonaj następujące kroki.

**Utwórz własne konto magazynu obiektów blob platformy Azure**

* Podczas obsługi administracyjnej magazynu obiektów blob platformy Azure, wybierz lokalizację geograficzną magazynu obiektów blob platformy Azure w lub możliwie najbliżej do **południowo-środkowe stany**, czyli przechowywania danych taksówki NYC. Dane zostaną skopiowane przy użyciu narzędzia AzCopy z publicznym kontenerze obiektów blob magazynu do kontenera na koncie magazynu. Bliższe magazynu obiektów blob platformy Azure południowo-środkowe stany, tym szybciej będzie można ukończyć tego zadania (krok 4).
* Aby utworzyć konta magazynu Azure, wykonaj czynności podane w [kont magazynu Azure o](../../storage/common/storage-create-storage-account.md). Należy pamiętać o wykonaniu uwagi dla wartości poniższych poświadczeń konta magazynu, ponieważ będą one potrzebne w dalszej części tego przewodnika.
  
  * **Nazwa konta magazynu**
  * **Klucz konta magazynu**
  * **Nazwa kontenera** (które mają dane mają być przechowywane w magazynie obiektów blob Azure)

**Udostępnij wystąpienie magazynu danych SQL Azure.**
Postępuj zgodnie z dokumentacją dostępną pod [Tworzenie usługi SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) do udostępnienia wystąpienia programu SQL Data Warehouse. Upewnij się, odpowiednie notacji na następujących poświadczeń magazynu danych SQL, które będą używane w kolejnych krokach.

* **Nazwa serwera**: <server Name>. database.windows.net
* **Nazwa SQLDW (baza danych)**
* **Nazwa użytkownika**
* **Hasło**

**Zainstaluj program Visual Studio i SQL Server Data Tools.** Aby uzyskać instrukcje, zobacz [instalacji programu Visual Studio 2015 i/lub program SSDT (SQL Server Data Tools) dla usługi SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Połączyć się z magazynu danych Azure SQL z programem Visual Studio.** Aby uzyskać instrukcje, zobacz kroki 1 i 2 w [Połącz z magazynu danych SQL Azure z programem Visual Studio](../../sql-data-warehouse/sql-data-warehouse-connect-overview.md).

> [!NOTE]
> Uruchom następujące zapytanie SQL w bazie danych utworzonego w magazynie danych SQL (zamiast zapytania w kroku 3 w temacie connect) do **utworzyć klucz główny**.
> 
> 

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Tworzenie obszaru roboczego uczenia maszynowego Azure w ramach Twojej subskrypcji platformy Azure.** Aby uzyskać instrukcje, zobacz [Utwórz obszar roboczy usługi Azure Machine Learning](../studio/create-workspace.md).

## <a name="getdata"></a>Ładowanie danych do magazynu danych SQL
Otwórz konsolę polecenia programu Windows PowerShell. Uruchom program PowerShell następujące polecenia, aby pobrać przykład SQL skryptu pliki, które firma Microsoft udostępnia z Tobą w witrynie GitHub w lokalnym katalogu, który należy określić przy użyciu parametru *- DestDir*. Można zmienić wartości parametru *- DestDir* do dowolnego katalogu lokalnego. Jeśli *- DestDir* nie istnieje, zostanie on utworzony przez skrypt programu PowerShell.

> [!NOTE]
> Konieczne może być **Uruchom jako Administrator** podczas wykonywania następującego skryptu programu PowerShell, jeśli Twoje *DestDir* katalogu wymaga uprawnień administratora do utworzenia lub do zapisu.
> 
> 

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Po pomyślnym wykonaniu bieżący katalog roboczy zmienia się na *- DestDir*. Można wyświetlić ekranu, takich jak poniżej:

![][19]

W Twojej *- DestDir*, uruchom następujący skrypt programu PowerShell w trybie administratora:

    ./SQLDW_Data_Import.ps1

Po uruchomieniu skryptu programu PowerShell po raz pierwszy, użytkownik jest proszony o podanie informacji z Twojego magazynu danych SQL Azure i konta magazynu obiektów blob platformy Azure. Po zakończeniu tego skryptu PowerShell uruchomiony po raz pierwszy, poświadczenia wprowadzania można będzie zapisano do pliku konfiguracyjnego SQLDW.conf istnieje katalog roboczy. Przyszłych uruchamianie tego pliku skryptu programu PowerShell może odczytać wszystkich potrzebnych parametrów z tego pliku konfiguracji. Jeśli musisz zmienić niektóre parametry, można wybrać przez usunięcie tego pliku konfiguracji i wprowadzanie wartości parametrów w danych wejściowych parametrów na ekranie po wierszu lub zmiany wartości parametrów, edytując plik SQLDW.conf Twojego *- DestDir* katalogu.

> [!NOTE]
> Aby uniknąć konfliktów nazw schematu z tymi, które już istnieją w Twojej SQL DW Azure podczas odczytywania parametrów bezpośrednio z pliku SQLDW.conf, 3-cyfrowy numer losowe zostanie dodany do nazwy schematu z pliku SQLDW.conf jako domyślną nazwę schematu dla każdego przebiegu. Skrypt programu PowerShell może wyświetlenie monitu o nazwę schematu: należy określić nazwę w uznania użytkownika.
> 
> 

To **skrypt programu PowerShell** plików wykonuje następujące zadania:

* **Pobiera i instaluje narzędzie AzCopy**, jeśli nie zainstalowano narzędzia AzCopy
  
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
* **Kopiuje dane na konto magazynu obiektów blob prywatnej** z publicznego obiektu blob z narzędzia AzCopy
  
        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
* **Ładuje dane przy użyciu programu Polybase (wykonując LoadDataToSQLDW.sql) do programu SQL DW Azure** z konta magazynu prywatnych obiektów blob za pomocą następujących poleceń.
  
  * Tworzenie schematu
    
          EXEC (''CREATE SCHEMA {schemaname};'');
  * Tworzenie poświadczeń o zakresie bazy danych
    
          CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
          WITH IDENTITY = ''asbkey'' ,
          Secret = ''{StorageAccountKey}''
  * Tworzenie zewnętrznego źródła danych dla obiektu blob magazynu Azure
    
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
  * Tworzenie zewnętrznego formatu pliku dla pliku csv. Jest nieskompresowanych danych i pola są oddzielone znakiem kreski pionowej.
    
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
  * Tworzenie zewnętrznego taryfy i tabele podróży dla zestawu danych taksówki NYC w magazynie obiektów blob platformy Azure.
    
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

    - Ładowanie danych z tabel zewnętrznych w magazynie obiektów blob Azure SQL Data Warehouse

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

    - Utwórz tabelę danych przykładowych (NYCTaxi_Sample) i wstawiania danych do niego z wybranie zapytania SQL w tabelach podróży i klasie. (Niektóre kroki tego przewodnika musi używać tej tabeli.)

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

Lokalizacja geograficzna kont magazynu ma wpływ na czas ładowania.

> [!NOTE]
> W zależności od lokalizacji geograficznej konto magazynu obiektów blob prywatne, proces kopiowania danych z publicznego obiektu blob na koncie magazynu prywatnego może zająć około 15 minut, a nawet dłużej, a proces ładowania danych z konta magazynu do platformy Azure Może to potrwać magazynu danych SQL 20 minut lub dłużej.  
> 
> 

Należy podjąć decyzję czego, jeśli masz zduplikowane pliki źródłowe i docelowe.

> [!NOTE]
> Jeśli pliki CSV do skopiowania z magazynu publicznego obiektu blob na koncie magazynu prywatnego obiektu blob już istnieją w konto magazynu obiektów blob prywatne, AzCopy zapyta, czy chcesz je zastąpić. Jeśli nie chcesz je zastąpić, wprowadź **n** po wyświetleniu monitu. Jeśli chcesz zastąpić **wszystkie** z nich, wprowadź po wyświetleniu monitu. Możesz również wpisać **y** indywidualnie zastąpić pliki CSV.
> 
> 

![Wykreślenia #21][21]

Można użyć własnych danych. Jeśli do komputera lokalnego w aplikacji rzeczywistych danych, można nadal używać narzędzia AzCopy przekazywania danych lokalnych do magazynu obiektów blob platformy Azure prywatnych. Musisz zmienić **źródła** lokalizacji, `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, w poleceniu AzCopy pliku skryptu PowerShell do katalogu lokalnego, który zawiera dane.

> [!TIP]
> Jeśli danych jest już w magazynie obiektów blob platformy Azure prywatnych w rzeczywistych aplikacji, można pominąć krok AzCopy w skrypcie programu PowerShell i bezpośrednio przekazać dane do magazynu danych SQL Azure. Wymaga to dodatkowe zmiany skryptu, aby dopasować ją do formatu danych.
> 
> 

Ten skrypt programu Powershell również podłącza się w informacjach dotyczących magazynu danych SQL Azure do plików przykład Eksploracja danych SQLDW_Explorations.sql, SQLDW_Explorations.ipynb i SQLDW_Explorations_Scripts.py tak, aby te trzy pliki są gotowe do próby natychmiast po Wykonuje skrypt programu PowerShell.

Po pomyślnym wykonaniu, zostanie wyświetlony ekran, takich jak poniżej:

![][20]

## <a name="dbexplore"></a>Eksploracja danych i funkcji inżynieryjne w usłudze Azure SQL Data Warehouse
W tej sekcji możemy wykonywać Generowanie funkcji i eksploracja danych przez wykonywanie zapytań SQL magazynu danych SQL Azure bezpośrednio przy użyciu **programu Visual Studio Tools danych**. Wszystkie zapytania SQL w tej sekcji znajdują się w przykładowy skrypt o nazwie *SQLDW_Explorations.sql*. Ten plik została już pobrana do katalogu lokalnego za pomocą skryptu programu PowerShell. Można również pobrać go z [GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Ale plik w witrynie GitHub nie ma informacji magazynu danych SQL Azure z sieci.

Nawiązywanie połączenia z magazynu danych SQL Azure za pomocą programu Visual Studio przy użyciu magazynu danych SQL, nazwa logowania i hasła i otwarcie **Eksplorator obiektów SQL** o potwierdzenie bazy danych i tabele zostały zaimportowane. Pobrać *SQLDW_Explorations.sql* pliku.

> [!NOTE]
> Aby otworzyć Edytor zapytań równoległych magazynu danych (PDW), użyj **nowe zapytanie** polecenia, a Twoje PDW wybrano w **Eksplorator obiektów SQL**. Standardowy Edytor zapytań SQL nie jest obsługiwany przez PDW.
> 
> 

Poniżej przedstawiono dane tego typu funkcji i eksploracja generowania wykonywane w tej sekcji:

* Eksplorowanie danych dystrybucji kilka pól w różnym czasie systemu windows.
* Zbadaj jakości danych pól długości i szerokości geograficznej.
* Generowanie na podstawie etykiet klasyfikacji binarnej i wieloklasowej **Porada\_kwota**.
* Generowanie funkcji i obliczeń lub porównania odległości podróży.
* Dołącz do dwóch tabel i wyodrębniać losowej próbki, która będzie służyć do tworzenia modeli.

### <a name="data-import-verification"></a>Weryfikacja importu danych
Kwerendy te zapewniają szybki weryfikacji liczby wierszy i kolumn w tabelach wypełniać wcześniej przy użyciu zbiorczego równoległe w programie Polybase zaimportować,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Dane wyjściowe:** należy pobrać 173,179,759 wierszy i kolumn 14.

### <a name="exploration-trip-distribution-by-medallion"></a>Eksploracja: Podróży dystrybucji przez Medalionu
To przykładowe zapytanie identyfikuje medallions (taksówki numery) wykonanych więcej niż 100 rund w określonym przedziale czasu. Zapytanie będzie korzystać z dostępu do tabeli partycjonowanej ponieważ należy przygotować w schemacie partycji **podnoszenia\_datetime**. Wykonywanie zapytania pełnego zestawu danych umożliwiają użycie tabeli partycjonowanej i/lub indeksu skanowania.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Dane wyjściowe:** zapytania powinien zwrócić tabelę z wierszami 13,369 medallions (taksówkach) i Liczba podróży zakończone przez nich 2013. Ostatnia kolumna zawiera Liczba rund ukończone.

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Eksploracja: Podróży dystrybucji Medalionu i hack_license
W tym przykładzie identyfikuje medallions (taksówki cyfry) i hack_license cyfry (sterowniki) zakończona ponad 100 rund w określonym przedziale czasu.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Dane wyjściowe:** zapytanie powinna zwrócić tabelę z wierszami 13,369 określenie 13,369 identyfikatory samochodu/driver, które zostały ukończone więcej czy 100 podróży w 2013. Ostatnia kolumna zawiera Liczba rund ukończone.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Ocena jakości danych: Weryfikuj rekordy z geograficzne niepoprawne i/lub szerokość geograficzną
W tym przykładzie sprawdza, czy polach geograficzne i/lub szerokość geograficzną albo zawiera nieprawidłową wartość (stopni w radianach powinna należeć do zakresu od-90 do 90,) lub (0, 0) współrzędnych.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Dane wyjściowe:** zapytanie zwraca 837,467 podróży, które mają nieprawidłowe pola geograficzne i/lub szerokość geograficzną.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Eksploracja: Przechylony a nie Przechylony rund dystrybucji
W tym przykładzie znajduje się liczba podróży, które zostały Przechylony a liczba, które nie zostały Przechylony w określonym przedziale czasu (lub pełnego zestawu danych, jeśli obejmujące pełny rok, ponieważ jest skonfigurowana w tym miejscu). Tej dystrybucji odzwierciedla dystrybucji binarne etykiety później służący do modelowania klasyfikacji binarnej.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Dane wyjściowe:** zapytanie powinna zwrócić następujące częstotliwości Porada roku 2013: 90,447,622 Przechylony i 82,264,709 Przechylony nie.

### <a name="exploration-tip-classrange-distribution"></a>Eksploracja: Porada klasy/zakres dystrybucji
W tym przykładzie oblicza rozkład Porada zakresów w danym przedziale czasu (lub pełnego zestawu danych, jeśli obejmujące pełny rok). Jest to dystrybucji klasy etykiety, które zostanie później użyty do modelowania wieloklasowej klasyfikacji.

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

### <a name="exploration-compute-and-compare-trip-distance"></a>Eksploracja: Obliczania i porównać odległość podróży
W tym przykładzie konwertuje długość odbiór i przyjmowania i szerokości geograficznej lokalizacji SQL punkty, oblicza odległość podróży przy użyciu różnicę punktów geograficzne SQL i zwraca losowej próbki wyniki do porównania. Przykład ogranicza wyniki do prawidłowe współrzędne tylko przy użyciu zapytania oceny jakości danych objętych wcześniej.

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

### <a name="feature-engineering-using-sql-functions"></a>Funkcja engineering przy użyciu funkcji SQL
Czasami funkcje SQL mogą być wydajne opcję engineering funkcji. W tym przewodniku zdefiniowanego funkcji SQL, aby obliczyć bezpośredniego odległość między lokalizacjami odbiór i dropoff. Można uruchomić następujące skrypty SQL w **programu Visual Studio Tools danych**.

Poniżej przedstawiono skrypt SQL, który definiuje funkcję odległości.

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

**Dane wyjściowe:** to zapytanie generuje tabeli (z 2,803,538 wierszy) z odbiór i dropoff długości i szerokości geograficzne i odpowiadający mu bezpośrednie odległości w milach. Poniżej przedstawiono wyniki dla pierwsze 3 wiersze:

|  | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40.731804 |-74.001083 |40.736622 |-73.988953 |.7169601222 |
| 2 |40.715794 |-74,010635 |40.725338 |-74.00399 |.7448343721 |
| 3 |40.761456 |-73.999886 |40.766544 |-73.988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Przygotowanie do tworzenia modelu danych
Następujące zapytanie sprzęga **nyctaxi\_podróży** i **nyctaxi\_taryfy** tabel, generuje etykiety klasyfikacji binarnej **Przechylony**, etykiety klasyfikacji wielu klasy **Porada\_klasy**i wyodrębnia próbki z dołączonym do pełnego zestawu danych. Próbki odbywa się przez pobranie podzbiór rund na podstawie czasu odbioru.  To zapytanie może skopiować następnie wkleić bezpośrednio w [Azure Machine Learning Studio](https://studio.azureml.net) [i zaimportuj dane] [ import-data] modułu dla wprowadzanie danych bezpośrednio z wystąpienia bazy danych SQL w Azure. Zapytanie nie obejmuje rekordy z niepoprawne (0, 0) współrzędnych.

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

Gdy wszystko będzie gotowe przejść do usługi Azure Machine Learning, użytkownik może:  

1. Zapisz końcowego zapytanie SQL do wyodrębnienia przykładowe dane i skopiuj i Wklej zapytanie bezpośrednio do [i zaimportuj dane] [ import-data] modułu w usłudze Azure Machine Learning, lub
2. Zachować dane próbki i odtworzone będą używane dla modelu tworzenie w nowej tabeli magazynu danych SQL i użyj nowej tabeli w [i zaimportuj dane] [ import-data] modułu w usłudze Azure Machine Learning. Skrypt programu PowerShell w poprzednim kroku ma zostało to zrobione automatycznie. Możesz przeczytać bezpośrednio z tej tabeli w module importu danych.

## <a name="ipnb"></a>Eksploracja danych i funkcji inżynieryjne w notesie IPython
W tej sekcji zostaną wykonane Eksplorowanie danych oraz generowanie funkcji za pomocą obu Python i zapytań SQL magazynu danych SQL utworzony wcześniej. Przykładowe IPython Notes o nazwie **SQLDW_Explorations.ipynb** plik skryptu języka Python **SQLDW_Explorations_Scripts.py** zostały pobrane do lokalnego katalogu. Są również dostępne na [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Te dwa pliki są identyczne w skryptach Python. Plik skryptu języka Python są dostępne w przypadku, gdy nie masz serwera IPython notesu. Te dwa przykładowe Python plików zostały zaprojektowane pod **Python 2.7**.

Potrzebnych informacji magazynu danych SQL Azure w próbce notesu IPython i plik skryptu języka Python pobrane na komputer lokalny został podłączony za pomocą skryptu programu PowerShell wcześniej. Są one wykonywalny bez żadnych modyfikacji.

Jeśli obszar roboczy uczenie maszynowe Azure zostały już skonfigurowane, możesz bezpośrednio przekazać przykładowe notesu IPython do usługi uczenie maszynowe Azure IPython notesu i jej uruchamianie. Poniżej przedstawiono kroki, aby przekazać do usługi uczenie maszynowe Azure IPython notesu:

1. Zaloguj się do swojego obszaru roboczego uczenie maszynowe Azure, kliknij przycisk "Studio" u góry, a następnie kliknij przycisk "Komputery przenośne", po lewej stronie strony sieci web.
   
    ![Wykreślenia #22][22]
2. Kliknij przycisk "Nowy" w lewym dolnym rogu strony sieci web, a następnie wybierz pozycję "Python 2". Następnie podaj nazwę z notesem i kliknij znacznik wyboru, aby utworzyć nowy notes IPython puste.
   
    ![Wykreślenia #23][23]
3. Kliknij symbol "Jupyter" w lewym górnym rogu nowy notes IPython.
   
    ![Wykreślenia #24][24]
4. Przeciągnij i upuść próbki notesu IPython do **drzewa** z usługi uczenie maszynowe Azure IPython notesu, a następnie kliknij przycisk **przekazać**. Następnie próbki IPython notesu zostanie przekazany do usługi uczenie maszynowe Azure IPython notesu.
   
    ![Wykreślenia #25][25]

Aby można było uruchomić przykładowy plik, Python następujące pakiety są wymagane skryptu notesu IPython lub Python. Jeśli używasz usługi uczenie maszynowe Azure IPython notesu te pakiety zostały wstępnie zainstalowane.

    - pandas
    - numpy
    - matplotlib
    - pyodbc
    - PyTables

Zalecana kolejność podczas tworzenia zaawansowanych rozwiązań analitycznych na uczenie maszynowe Azure z dużej ilości danych jest następujący:

* Odczyt w małych przykładowych danych do ramki danych w pamięci.
* Wykonywanie niektórych wizualizacje i eksploracji przy użyciu próbki danych.
* Wypróbuj engineering funkcji przy użyciu próbki danych.
* Dla większych Eksploracja danych, do manipulowania danymi i inżynieria funkcji należy użyć Python do wysyłania zapytań SQL bezpośrednio z magazynu danych SQL.
* Określ wielkość próbki się odpowiednie do konstruowania modelu uczenia maszynowego Azure.

Następujących typów to kilka Eksploracja danych, wizualizacji danych i funkcji inżynierii przykłady. Więcej eksploracji danych można znaleźć w przykładzie notesu IPython oraz przykładowy plik skryptu języka Python.

### <a name="initialize-database-credentials"></a>Inicjowanie poświadczenia bazy danych
Inicjowanie ustawienia połączenia bazy danych w następujących zmiennych:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Utwórz połączenie z bazą danych
Oto parametry połączenia, które tworzy połączenie z bazą danych.

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

### <a name="read-in-a-small-data-sample-from-the-sql-data-warehouse-database"></a>Odczyt w przykładowych małych danych z bazy danych magazynu danych SQL
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

Czas odczytu Przykładowa tabela jest 14.096495 sekund.  
Liczba wierszy i kolumn pobrać = (1000, 21).

### <a name="descriptive-statistics"></a>Statystyki opisowe
Teraz można przystąpić do eksplorowania próbki danych. Możemy zaczynać się patrzeć statystykami opisowy dla **podróży\_odległość** (lub innych pól samodzielnie określić).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Wizualizacji: Przykład kreślenia pola
Następnie przyjrzymy się skrzynkowy odległość podróży do wizualizacji quantiles.

    df1.boxplot(column='trip_distance',return_type='dict')

![Wykreślenia #1][1]

### <a name="visualization-distribution-plot-example"></a>Wizualizacji: Przykład kreślenia dystrybucji
Wykresy, które wizualizacji dystrybucji i histogram odległości próbkowanych podróży.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Wykreślenia #2][2]

### <a name="visualization-bar-and-line-plots"></a>Wizualizacja: Pasek i geograficzne wiersza
W tym przykładzie mamy bin odległość podróży, w pojemnikach pięć i wizualizacja wyników binning.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Firma Microsoft wykreślenia powyżej dystrybucji bin na pasku lub wiersz kreślenia z:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Wykreślenia #3][3]

i

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Wykreślenia #4][4]

### <a name="visualization-scatterplot-examples"></a>Wizualizacji: Przykłady Scatterplot
Zostanie przedstawiony jest wykres punktowy między **podróży\_czasu\_w\_s** i **podróży\_odległość** się czy ma żadnych korelacji

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Wykreślenia #6][6]

Podobnie można sprawdzić relacji między **szybkość\_kod** i **podróży\_odległość**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Wykreślenia #8][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Eksploracja danych na próbki danych za pomocą zapytań SQL w notesie IPython
W tej sekcji możemy Eksplorowanie danych dystrybucji przy użyciu próbki danych, który jest utrwalona w nowej tabeli utworzone powyżej. Należy pamiętać, że podobne eksploracji może zostać wykonane przy użyciu oryginalnego tabel.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Eksploracja: Raport liczba wierszy i kolumn w tabeli próbki
    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Eksploracja: Przechylony nie zwracać dystrybucji
    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Eksploracja: Porada klasy dystrybucji
    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Eksploracja: Wykreślenia dystrybucji Porada przez klasę
    tip_class_dist['tip_freq'].plot(kind='bar')

![#26 kreślenia][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Eksploracji: Codzienne dystrybucji rund
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Eksploracja: Podróży dystrybucji na Medalionu
    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Eksploracja: Podróży dystrybucji przez Medalionu i hack licencji
    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Eksploracja: Podróży czasu dystrybucji
    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Eksploracja: Podróży odległość dystrybucji
    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Eksploracji: Dystrybucja typ płatności
    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Sprawdź ostatecznej formie tabeli featurized
    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Tworzenie modeli w usłudze Azure Machine Learning
Firma Microsoft są teraz gotowe do przejścia do konstruowania modelu i wdrażania modelu w [usługi Azure Machine Learning](https://studio.azureml.net). Dane są gotowe do użycia w jednym z problemów prognozowania wymienionych wcześniej, to znaczy:

1. **Klasyfikacji binarnej**: przewidywanie, czy etykietki został płatnej w podróży.
2. **Wieloklasowej klasyfikacji**: przewidywanie zakres Porada płatną zgodnie z uprzednio zdefiniowanej klasy.
3. **Zadanie regresji**: przewidywanie ilość Porada płatnej w podróży.  

Aby rozpocząć wykonywania modelowania, zaloguj się do Twojego **usługi Azure Machine Learning** obszaru roboczego. Jeśli jeszcze nie utworzono obszaru roboczego uczenia maszynowego, zobacz [Utwórz obszar roboczy usługi Azure ML](../studio/create-workspace.md).

1. Aby zacząć korzystać z usługi Azure Machine Learning, zobacz [co to jest Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Zaloguj się do [Azure Machine Learning Studio](https://studio.azureml.net).
3. Strona główna Studio zapewnia wiele informacji, wideo, samouczki, linki do odwołania moduły i innych zasobów. Aby uzyskać więcej informacji na temat usługi Azure Machine Learning, zapoznaj się [Centrum dokumentacji uczenia maszynowego Azure](https://azure.microsoft.com/documentation/services/machine-learning/).

Eksperyment typowe szkolenia składa się z następujących czynności:

1. Utwórz **+ nowy** eksperymentu.
2. Pobierz dane do uczenia Maszynowego Azure.
3. Wstępnie przetworzyć, transformacji i manipulowanie danymi w razie potrzeby.
4. Generowanie funkcji zgodnie z potrzebami.
5. Podział danych na zestaw szkolenia / / sprawdzanie poprawności danych (lub mieć osobne zestawy danych dla każdego).
6. Wybierz co najmniej jeden algorytmów uczenia maszynowego w zależności od problemu learning do rozwiązania. Przykład klasyfikacji binarnej, wieloklasowej klasyfikacji, regresji.
7. Szkolenie co najmniej jednego modelu przy użyciu zestawu danych szkoleniowych.
8. Wynik sprawdzania poprawności zestawu danych przy użyciu modeli przeszkolone.
9. Należy ocenić modele obliczeniowe odpowiednich metryki learning problem.
10. Prawidłowo dostrojenie modele i wybrać najlepsze modelu do wdrożenia.

W tym ćwiczeniu firma Microsoft ma już przedstawione odtwarzane danych w usłudze SQL Data Warehouse i postanowiła na rozmiar próbki pozyskiwania w uczenie Maszynowe Azure. Poniżej przedstawiono procedurę, aby utworzyć co najmniej jeden modele predykcyjne:

1. Pobieranie danych do usługi Azure ML przy użyciu [i zaimportuj dane] [ import-data] modułu dostępne w **danych wejściowych i wyjściowych** sekcji. Aby uzyskać więcej informacji, zobacz [i zaimportuj dane] [ import-data] strony odwołanie do modułu.
   
    ![Uczenie Maszynowe Azure i zaimportuj dane][17]
2. Wybierz **bazy danych SQL Azure** jako **źródła danych** w **właściwości** panelu.
3. Wprowadź nazwę DNS bazy danych w **nazwę serwera bazy danych** pola. Format: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Wprowadź **Nazwa bazy danych** w odpowiednie pole.
5. Wprowadź *nazwa użytkownika SQL* w **nazwę konta użytkownika serwera**i *hasło* w **hasło konta użytkownika serwera**.
7. W **zapytanie bazy danych** edytowania obszaru tekstu, Wklej zapytanie, które zwraca pola niezbędne bazy danych (w tym wszystkie pola obliczanej, takich jak etykiety) i w dół próbek danych do żądanego próbkowania.

Przykład eksperyment klasyfikacji binarnej odczytywanie danych bezpośrednio z bazy danych usługi SQL Data Warehouse to na poniższej ilustracji (Pamiętaj zastąpić nyctaxi_trip nazwy tabeli i nyctaxi_fare przez nazwę schematu i nazwy tabeli używana w Twojej wskazówki). Podobne eksperymenty można utworzyć dla wieloklasowej klasyfikacji i regresji problemów.

![Azure ML pociągu][10]

> [!IMPORTANT]
> W danych modelowania wyodrębniania i pobierania próbek przykłady zapytania podane w poprzednich sekcjach, **wszystkich etykiet trzy ćwiczeń modelowania są uwzględnione w zapytaniu**. Ważnym krokiem (wymagane) w każdym ćwiczeń modelowania jest **wykluczyć** niepotrzebne etykiety dla innych problemów oraz wszelkie inne **target przecieki**. Na przykład, korzystając z klasyfikacji binarnej, użyj etykiety **Przechylony** i wykluczyć pola **Porada\_klasy**, **Porada\_kwota**i **całkowita\_kwota**. Są one przecieki docelowy one zakłada poradę płatnej.
> 
> Aby wykluczyć wszystkie zbędne kolumny lub docelowe przecieki, możesz użyć [Select Columns in Dataset] [ select-columns] modułu lub [edytowanie metadanych][edit-metadata]. Aby uzyskać więcej informacji, zobacz [Select Columns in Dataset] [ select-columns] i [edytowanie metadanych] [ edit-metadata] odwołania stron.
> 
> 

## <a name="mldeploy"></a>Wdrażanie modeli w usłudze Azure Machine Learning
Gdy model jest gotowy, łatwo można go wdrożyć jako usługę sieci web bezpośrednio z eksperymentu. Aby uzyskać więcej informacji na temat wdrażania usług sieci web uczenie Maszynowe Azure, zobacz [wdrażanie usługi sieci web Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).

Aby wdrożyć nową usługę sieci web, musisz:

1. Tworzenie eksperymentu oceniania.
2. Wdrażanie usługi sieci web.

Aby utworzyć eksperyment oceniania z **zakończone** szkolenia eksperyment, kliknij przycisk **utworzyć OCENIANIA EKSPERYMENTU** na dolnym pasku akcji.

![Ocenianie przez usługę Azure][18]

Usługa Azure Machine Learning podejmie próbę utworzenia eksperyment oceniania na podstawie składników eksperyment uczenia. W szczególności będzie:

1. Zapisania trenowanego modelu oraz usuwanie modułów uczenia modelu.
2. Zidentyfikuj logicznych **port wejściowy** do reprezentowania schematu oczekiwanych danych wejściowych.
3. Zidentyfikuj logicznych **output portu** do reprezentowania schemat danych wyjściowych usługi sieci web oczekiwanego.

Podczas tworzenia eksperymentu oceniania, zapoznaj się z nim i upewnij się, Dopasuj zależnie od potrzeb. Typowy korekty jest Zamień wejściowy zestaw danych i/lub zapytania taki, który wyklucza pola etykiety, jak te nie będą dostępne, gdy usługa jest wywoływana. Jest również dobrym rozwiązaniem, aby zmniejszyć rozmiar wejściowy zestaw danych i/lub kwerendy na kilka rekordów wystarczającego wskazująca schemat danych wejściowych. Port wyjściowy jest wspólny dla wszystkich wejściowych Dołączanie i wykluczanie tylko **oceniane etykiety** i **wynik prawdopodobieństwa** w danych wyjściowych przy użyciu [Select Columns in Dataset] [ select-columns] modułu.

Przykładowe oceniania eksperymentu znajduje się na poniższej ilustracji. Gdy będzie gotowy do wdrożenia, kliknij przycisk **OPUBLIKOWAĆ usługi sieci WEB** na dolnym pasku akcji.

![Azure ML Publish][11]

## <a name="summary"></a>Podsumowanie
Aby recap, co możemy to zostało zrobione w tym samouczku wskazówki, utworzono środowisku nauki danych Azure doświadczenie z dużego zestawu danych publicznych, dostarczanej przez proces nauki zespołu danych od pozyskiwania danych do uczenia modelu, a następnie do Wdrażanie usługi sieci web uczenie maszynowe Azure.

### <a name="license-information"></a>Informacje o licencji
Ten przewodnik próbki i jego towarzyszące IPython notebook(s) i skrypty są udostępniane przez firmę Microsoft w ramach licencji MIT. Sprawdź plik LICENSE.txt w katalogu przykładowy kod w witrynie GitHub, aby uzyskać więcej informacji.

## <a name="references"></a>Dokumentacja
• [Andrés Monroy taksówki NYC rund stronę pobierania](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC taksówki podróży danych przez Krzysztof Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [Taksówki NYC i Limousine Komisji badań i statystyki](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

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
