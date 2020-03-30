---
title: Tworzenie i wdrażanie modelu w maszynie Wirtualnej programu SQL Server — proces nauki o danych zespołu
description: Tworzenie i wdrażanie modelu uczenia maszynowego przy użyciu programu SQL Server na maszynie Wirtualnej platformy Azure z publicznie dostępnym zestawem danych.
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
ms.openlocfilehash: a47f30cf00624faf098c8b605534cf355eacadee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251585"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>Proces nauki o danych zespołu w akcji: przy użyciu programu SQL Server
W tym samouczku można przejść przez proces tworzenia i wdrażania modelu uczenia maszynowego przy użyciu programu SQL Server i publicznie dostępnego zestawu danych — zestaw danych [NYC Taxi Trips.](https://www.andresmh.com/nyctaxitrips/) Procedura jest zgodna ze standardowym przepływem pracy nauki o danych: pozyskiwania i eksplorowania danych, inżynierii funkcji w celu ułatwienia uczenia się, a następnie tworzenia i wdrażania modelu.

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>Nyc Taxi Trips Opis zestawu danych
Dane NYC Taxi Trip to około 20 GB skompresowanych plików CSV (~ 48 GB nieskompresowanych), co obejmuje ponad 173 miliony pojedynczych podróży i opłaty za każdą podróż. Każdy rekord podróży zawiera miejsce odbioru i nadsyłania i czas, anonimowy hack (kierowca) numer licencji i medalion (taxi's unique id) numer. Dane obejmują wszystkie podróże w 2013 r. i są dostępne w następujących dwóch zestawach danych dla każdego miesiąca:

1. Csv "trip_data" zawiera szczegóły podróży, takie jak liczba pasażerów, punkty odbioru i wysiadania, czas trwania podróży i długość podróży. Oto kilka przykładowych rekordów:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. "trip_fare" CSV zawiera szczegóły taryfy zapłaconej za każdą podróż, takie jak rodzaj płatności, kwota taryfy, dopłaty i podatki, napiwki i opłaty drogowe oraz całkowita kwota zapłacona. Oto kilka przykładowych rekordów:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Unikalny klucz do\_przyłączenia\_się do danych podróży i taryfy podróży\_składa\_się z pól: medalion, licencja hack i data odbioru.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Przykłady zadań przewidywania
Będziemy formułować trzy problemy przewidywania na podstawie *kwoty końcówki,\_* a mianowicie:

* Klasyfikacja binarna: Wytypuj, czy napiwek został zapłacony za podróż, czyli *kwota napiwku\_* większa niż 0 USD jest pozytywnym przykładem, podczas gdy kwota *\_napiwku* w wysokości 0 USD jest przykładem ujemnym.
* Klasyfikacja wieloklasowa: Aby przewidzieć zakres napiwku opłaconego za podróż. Dzielimy *ilość\_końcówki* na pięć pojemników lub klas:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
* Zadanie regresji: Aby przewidzieć kwotę napiwku zapłaconego za podróż.  

## <a name="setting-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Konfigurowanie środowiska do nauki o danych platformy Azure dla zaawansowanej analizy
Jak widać w przewodniku [Planowanie środowiska,](plan-your-environment.md) istnieje kilka opcji do pracy z nyc taxi trips zestaw danych na platformie Azure:

* Praca z danymi w obiektach blob platformy Azure, a następnie modelowanie w usłudze Azure Machine Learning
* Ładowanie danych do bazy danych programu SQL Server, a następnie modelowanie w usłudze Azure Machine Learning

W tym samouczku zademonstrujemy równoległy zbiorczy import danych do programu SQL Server, eksplorację danych, inżynierię funkcji i próbkowanie w dół przy użyciu programu SQL Server Management Studio, a także przy użyciu notesu IPython. [Przykładowe skrypty](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) i [notesy IPython](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) są udostępniane w usłudze GitHub. Przykładowy notes IPython do pracy z danymi w obiektach blob platformy Azure jest również dostępny w tej samej lokalizacji.

Aby skonfigurować środowisko nauki o danych platformy Azure:

1. [Tworzenie konta magazynu](../../storage/common/storage-account-create.md)
2. [Tworzenie obszaru roboczego usługi Azure Machine Learning](../studio/create-workspace.md)
3. [Aprowizuj maszynę wirtualną do nauki o danych,](../data-science-virtual-machine/setup-sql-server-virtual-machine.md)która zapewnia serwer SQL i serwer notesu IPython.
   
   > [!NOTE]
   > Przykładowe skrypty i notesy IPython zostaną pobrane na maszynę wirtualną nauki o danych podczas procesu instalacji. Po zakończeniu skryptu po instalacji maszyny Wirtualnej próbki będą znajdować się w bibliotece Dokumentów maszyny Wirtualnej:  
   > 
   > * Przykładowe skrypty:`C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Przykładowe notesy IPython:`C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   gdzie `<user_name>` jest nazwa logowania maszyny Wirtualnej systemu Windows. Będziemy odnosić się do przykładowych folderów jako **przykładowe skrypty** i **przykładowe notesy IPython**.
   > 
   > 

Na podstawie rozmiaru zestawu danych, lokalizacji źródła danych i wybranego środowiska docelowego platformy Azure ten scenariusz jest podobny do [ \#scenariusza 5: Duży zestaw danych w plikach lokalnych, docelowy program SQL Server w usłudze Azure VM](plan-sample-scenarios.md#largelocaltodb).

## <a name="get-the-data-from-public-source"></a><a name="getdata"></a>Pobierz dane ze źródła publicznego
Aby uzyskać zestaw danych [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) z jego lokalizacji publicznej, można użyć dowolnej z metod opisanych w Przenieś dane do i z usługi Azure [Blob Storage,](move-azure-blob.md) aby skopiować dane do nowej maszyny wirtualnej.

Aby skopiować dane za pomocą programu AzCopy:

1. Zaloguj się do maszyny wirtualnej
2. Utwórz nowy katalog na dysku danych maszyny Wirtualnej (uwaga: Nie należy używać dysku tymczasowego dostarczanego z maszyną wirtualną jako dyskiem danych).
3. W oknie wiersza wiersza polecenia uruchom następujący wiersz polecenia Azcopy, zastępując <path_to_data_folder> folderem danych utworzonym w (2):
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    Po zakończeniu AzCopy w folderze danych powinny znajdować się łącznie 24 spakowane pliki CSV (12 dla danych podróży\_i 12 w przypadku taryfy podróżnej).\_
4. Rozpaj pobrane pliki. Zanotuj folder, w którym znajdują się nieskompresowane pliki. Ten folder będzie określany jako ścieżka\_<\_\_do\>plików danych .

## <a name="bulk-import-data-into-sql-server-database"></a><a name="dbload"></a>Zbiorcze importowanie danych do bazy danych programu SQL Server
Wydajność ładowania/przesyłania dużych ilości danych do bazy danych SQL i kolejnych zapytań można poprawić za pomocą *tabel i widoków podzielonych na partycje.* W tej sekcji będziemy postępować zgodnie z instrukcjami opisanymi w [równoległym zbiorczym imporcie danych przy użyciu tabel partycji SQL,](parallel-load-sql-partitioned-tables.md) aby utworzyć nową bazę danych i załadować dane do tabel podzielonych na partycje równolegle.

1. Po zalogowaniu się do maszyny Wirtualnej uruchom **program SQL Server Management Studio**.
2. Połącz się przy użyciu uwierzytelniania systemu Windows.
   
    ![Połączenie SSMS][12]
3. Jeśli tryb uwierzytelniania programu SQL Server nie został jeszcze zmieniony i utworzono nowego użytkownika logowania SQL, otwórz plik skryptu o nazwie **change\_auth.sql** w folderze **Przykładowe skrypty.** Zmień domyślną nazwę użytkownika i hasło. Kliknij **przycisk Wykonaj** na pasku narzędzi, aby uruchomić skrypt.
   
    ![Wykonywanie skryptu][13]
4. Sprawdź i/lub zmień domyślną bazę danych i foldery dziennika programu SQL Server, aby upewnić się, że nowo utworzone bazy danych będą przechowywane na dysku danych. Obraz maszyny Wirtualnej programu SQL Server zoptymalizowany pod kątem obciążenia magazynowania danych jest wstępnie skonfigurowany z dyskami danych i dziennika. Jeśli maszyna wirtualna nie zawierała dysku danych i dodano nowe wirtualne dyski twarde podczas procesu instalacji maszyny Wirtualnej, zmień foldery domyślne w następujący sposób:
   
   * Kliknij prawym przyciskiem myszy nazwę programu SQL Server w lewym panelu i kliknij polecenie **Właściwości**.
     
       ![Właściwości programu SQL Server][14]
   * Wybierz **pozycję Ustawienia bazy danych** z listy Wybierz **stronę** po lewej stronie.
   * Sprawdź i/lub zmień **domyślne lokalizacje bazy danych** na wybrane lokalizacje **dysków danych.** Ta lokalizacja jest miejscem, w którym znajdują się nowe bazy danych, jeśli zostały utworzone przy ustawieniach domyślnych.
     
       ![Domyślne ustawienia bazy danych SQL][15]  
5. Aby utworzyć nową bazę danych i zestaw grup plików do przechowywania tabel podzielonych na partycje, otwórz przykładowy **skrypt, utwórz\_plik db\_default.sql**. Skrypt utworzy nową bazę danych o nazwie **TaxiNYC** i 12 grup plików w domyślnej lokalizacji danych. Każda grupa plików będzie przechowywać jeden miesiąc danych podróży\_i danych taryfy podróży.\_ W razie potrzeby zmodyfikuj nazwę bazy danych. Kliknij **przycisk Wykonaj,** aby uruchomić skrypt.
6. Następnie utwórz dwie tabele partycji, jedną dla danych podróży,\_a drugą dla taryfy podróży.\_ Otwórz przykładowy skrypt **utworzyć\_partitioned\_table.sql**, który będzie:
   
   * Utwórz funkcję partycji, aby podzielić dane według miesiąca.
   * Utwórz schemat partycji, aby mapować dane każdego miesiąca do innej grupy plików.
   * Utwórz dwie partycjonowane tabele mapowane do schematu\_partycji: **podróż nyctaxi\_** będzie zawierać dane podróży, a **taryfa nyctaxi\_** będzie przechowywać dane taryfy podróży.\_
     
     Kliknij **przycisk Wykonaj,** aby uruchomić skrypt i utworzyć tabele podzielone na partycje.
7. W folderze **Przykładowe skrypty** znajdują się dwa przykładowe skrypty programu PowerShell dostarczone w celu wykazania równoległego zbiorczego importu danych do tabel programu SQL Server.
   
   * **bcp\_\_parallel generic.ps1** to ogólny skrypt do zbiorczego importu danych do tabeli. Zmodyfikuj ten skrypt, aby ustawić zmienne wejściowe i docelowe zgodnie z wierszami komentarza w skrypcie.
   * **bcp\_\_parallel nyctaxi.ps1** jest wstępnie skonfigurowaną wersją skryptu ogólnego i może być używany do ładowania obu tabel dla danych NYC Taxi Trips.  
8. Kliknij prawym przyciskiem myszy nazwę **skryptu\_bcp parallel\_nyctaxi.ps1** i kliknij polecenie **Edytuj,** aby otworzyć go w programie PowerShell. Przejrzyj wstępnie ustawione zmienne i modyfikuj zgodnie z wybraną nazwą bazy danych, folderem danych wejściowych, docelowym folderem dziennika i ścieżkami do przykładowych plików formatu **nyctaxi_trip.xml** i **nyctaxi\_fare.xml** (dostępnych w **folderze Przykładowe skrypty).**
   
    ![Zbiorcze dane importu][16]
   
    Można również wybrać tryb uwierzytelniania, domyślnie jest uwierzytelnianie systemu Windows. Kliknij zieloną strzałkę na pasku narzędzi, aby uruchomić. Skrypt uruchomi 24 operacje importu zbiorczego równolegle, 12 dla każdej tabeli podzielonej na partycje. Postęp importu danych można monitorować, otwierając domyślny folder danych programu SQL Server, jak określono powyżej.
9. Skrypt programu PowerShell raportuje godziny rozpoczęcia i zakończenia. Po zakończeniu wszystkich importów zbiorczych jest raportowana godzina zakończenia. Sprawdź docelowy folder dziennika, aby sprawdzić, czy import zbiorczy zakończył się pomyślnie, czyli nie zgłoszono żadnych błędów w docelowym folderze dziennika.
10. Baza danych jest teraz gotowa do eksploracji, inżynierii funkcji i innych operacji zgodnie z potrzebami. Ponieważ tabele są podzielone na partycje zgodnie z **polem data-data odbioru,\_** zapytania, które zawierają warunki **daty odbioru\_** w klauzuli **WHERE,** będą korzystać ze schematu partycji.
11. W **programie SQL Server Management Studio**zapoznaj się z przykładowym **przykładowym\_programem queries.sql.** Aby uruchomić dowolny przykładowy kwerendę, wyróżnij wiersze kwerendy, a następnie kliknij przycisk **Wykonaj** na pasku narzędzi.
12. Dane nyc taxi trips jest ładowany w dwóch oddzielnych tabelach. Aby poprawić operacje sprzężenia, zdecydowanie zaleca się indeksowania tabel. Przykładowy skrypt **\_utworzyć\_partitioned index.sql** tworzy indeksy podzielone na partycje na kompozytowym klucza dołączenia **medalion, licencja hack\_i datatime odbioru\_**.

## <a name="data-exploration-and-feature-engineering-in-sql-server"></a><a name="dbexplore"></a>Eksploracja danych i inżynieria funkcji w programie SQL Server
W tej sekcji będziemy przeprowadzać eksplorację danych i generowanie funkcji, uruchamiając zapytania SQL bezpośrednio w **programie SQL Server Management Studio** przy użyciu bazy danych programu SQL Server utworzonej wcześniej. Przykładowy skrypt o nazwie **sample\_queries.sql** znajduje się w folderze **Przykładowe skrypty.** Zmodyfikuj skrypt, aby zmienić nazwę bazy danych, jeśli różni się od domyślnej: **TaxiNYC**.

W tym ćwiczeniu:

* Połącz się z **programem SQL Server Management Studio** przy użyciu uwierzytelniania systemu Windows lub uwierzytelniania SQL oraz nazwy i hasła logowania SQL.
* Eksploruj dystrybucje danych w kilku polach w różnych oknach czasowych.
* Zbadaj jakość danych pól długości i szerokości geograficznej.
* Generowanie etykiet klasyfikacji binarnej i wieloklasowej na podstawie **kwoty końcówki\_**.
* Generuj funkcje i obliczaj/porównuj odległości podróży.
* Dołącz do dwóch tabel i wyodrębnić losową próbkę, która będzie używana do tworzenia modeli.

Gdy będziesz gotowy, aby przejść do usługi Azure Machine Learning, możesz:  

1. Zapisz ostateczną kwerendę SQL, aby wyodrębnić i próbkować dane oraz skopiować i wkleić kwerendę bezpośrednio do [modułu Importuj dane][import-data] w usłudze Azure Machine Learning, lub
2. Utrwalić próbkowane i inżynierii danych, które mają być używane do tworzenia modelu w nowej tabeli bazy danych i użyj nowej tabeli w [module Importuj dane][import-data] w usłudze Azure Machine Learning.

W tej sekcji zapiszemy kwerendę końcową, aby wyodrębnić i pobrać próbkowanie danych. Druga metoda jest zademonstrowana w [eksploracji danych i inżynierii funkcji w notesie IPython](#ipnb) sekcji.

Aby szybko zweryfikować liczbę wierszy i kolumn w tabelach wypełnionych wcześniej przy użyciu importu zbiorczego równoległego,

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Eksploracja: Dystrybucja podróży przez medalion
W tym przykładzie identyfikuje medalion (numery taksówek) z ponad 100 podróży w danym okresie czasu. Kwerenda będzie korzystać z podzielonego na partycje dostępu do tabeli, ponieważ jest to uwarunkowane przez schemat partycji **datatime odbioru\_**. Kwerenda pełnego zestawu danych będzie również korzystać z partycjonowanych tabeli i/lub skanowania indeksu.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Eksploracja: Dystrybucja podróży przez medalion i hack_license
    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Ocena jakości danych: Weryfikuj rekordy z nieprawidłową długością i/lub szerokością geograficzną
W tym przykładzie bada, czy którykolwiek z pól długości i/lub szerokości geograficznej albo zawierają nieprawidłową wartość (promieniowe stopnie powinny być między -90 i 90), lub mają (0, 0) współrzędne.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Eksploracja: Dystrybucja Tipped vs. Not Tipped Trips
W tym przykładzie znajduje się liczba podróży, które zostały przechylone vs nie przechylone w danym okresie czasu (lub w pełnym zestawie danych, jeśli obejmuje cały rok). Ta dystrybucja odzwierciedla dystrybucji etykiet binarnych, które mają być później używane do modelowania klasyfikacji binarnej.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Eksploracja: Klasa końcówka/ zasięg dystrybucji
W tym przykładzie oblicza rozkład zakresów końcówek w danym okresie czasu (lub w pełnym zestawie danych, jeśli obejmuje cały rok). Ten rozkład klas etykiet będzie używany później do modelowania klasyfikacji wieloklasowej.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

#### <a name="exploration-compute-and-compare-trip-distance"></a>Eksploracja: Obliczanie i porównywanie odległości podróży
W tym przykładzie konwertuje długość i szerokość geograficzną odbioru i dropoff do punktów geografii SQL, oblicza odległość podróży przy użyciu różnicy punktów geografii SQL i zwraca losową próbkę wyników do porównania. Przykład ogranicza wyniki do prawidłowych współrzędnych tylko przy użyciu kwerendy oceny jakości danych omówione wcześniej.

    SELECT
    pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
    ,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
    ,trip_distance
    ,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
    FROM nyctaxi_trip
    tablesample(0.01 percent)
    WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

#### <a name="feature-engineering-in-sql-queries"></a>Inżynieria funkcji w kwerendach SQL
Kwerendy eksploracji generowania etykiet i lokalizacji geograficznej mogą być również używane do generowania etykiet/operacji przez usunięcie części liczącej. Dodatkowe funkcje inżynierii przykłady SQL znajdują się w [eksploracji danych i inżynierii funkcji w IPython Notes](#ipnb) sekcji. Jest bardziej wydajne, aby uruchomić kwerendy generowania funkcji na pełny zestaw danych lub duży podzbiór go przy użyciu zapytań SQL, które są uruchamiane bezpośrednio w wystąpieniu bazy danych programu SQL Server. Kwerendy mogą być wykonywane w **programie SQL Server Management Studio,** notesie IPython lub dowolnym narzędziu lub środowisku programistycznym, które może uzyskać dostęp do bazy danych lokalnie lub zdalnie.

#### <a name="preparing-data-for-model-building"></a>Przygotowywanie danych do budowania modelu
Poniższa kwerenda łączy **tabele\_taryf nyctaxi** i **nyctaxi,\_** generuje etykietę klasyfikacji binarnej z **podziałką**, klasę etykiet **\_** klasy wieloklasowej i wyodrębnia losową próbkę 1% z pełnego połączonego zestawu danych. Tę kwerendę można skopiować, a następnie wkleić bezpośrednio w module [Importowanie danych][import-data] [usługi Azure Machine Learning Studio](https://studio.azureml.net) w celu bezpośredniego pozyskiwania danych z wystąpienia bazy danych programu SQL Server na platformie Azure. Kwerenda wyklucza rekordy z niepoprawnymi współrzędnymi (0, 0).

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 percent)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'


## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Eksploracja danych i inżynieria funkcji w notesie IPython
W tej sekcji będziemy wykonywać eksploracji danych i generowania funkcji przy użyciu zapytań Języka Python i SQL względem bazy danych programu SQL Server utworzonej wcześniej. Przykładowy notes IPython o nazwie **machine-Learning-data-science-process-sql-story.ipynb** znajduje się w **folderze Przykładowe notesy IPython.** Ten notes jest również dostępny na [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

Podczas pracy z dużymi zbiorami danych postępuj zgodnie z zalecaną sekwencją:

* Odczyt w małej próbce danych do ramki danych w pamięci.
* Wykonaj niektóre wizualizacje i eksploracje przy użyciu próbkowania danych.
* Eksperymentuj z inżynierią funkcji przy użyciu próbkowania danych.
* Aby uzyskać większe eksploracji danych, manipulowania danymi i inżynierii funkcji, użyj języka Python do wystawiania zapytań SQL bezpośrednio względem bazy danych programu SQL Server w maszynie Wirtualnej platformy Azure.
* Zdecyduj rozmiar przykładu do użycia w tworzeniu modelu usługi Azure Machine Learning.

Gdy będzie gotowy do przejścia do usługi Azure Machine Learning, możesz:  

1. Zapisz ostateczną kwerendę SQL, aby wyodrębnić i próbkować dane oraz skopiować i wkleić kwerendę bezpośrednio do [modułu Importuj dane][import-data] w usłudze Azure Machine Learning. Ta metoda jest zademonstrowana w sekcji [Modely tworzenia w usłudze Azure Machine Learning.](#mlmodel)    
2. Utrwalić próbkowane i inżynierii danych, które mają być używane do tworzenia modelu w nowej tabeli bazy danych, a następnie użyj nowej tabeli w [moduł danych importu.][import-data]

Poniżej przedstawiono kilka eksploracji danych, wizualizacji danych i przykładów inżynierii funkcji. Aby uzyskać więcej przykładów, zobacz przykładowy notes SQL IPython w **przykładowym folderze Notesy IPython.**

#### <a name="initialize-database-credentials"></a>Inicjowanie poświadczeń bazy danych
Zainicjowanie ustawień połączenia z bazą danych w następujących zmiennych:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Tworzenie połączenia z bazą danych
    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Raport liczbę wierszy i kolumn w tabeli nyctaxi_trip
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('nyctaxi_trip')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('nyctaxi_trip')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Całkowita liczba wierszy = 173179759  
* Całkowita liczba kolumn = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Odczyt małego przykładu danych z bazy danych programu SQL Server
    t0 = time.time()

    query = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (0.05 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Czas odczytu przykładowej tabeli wynosi 6,492000 sekundy  
Liczba pobranych wierszy i kolumn = (84952, 21)

#### <a name="descriptive-statistics"></a>Statystyki opisowe
Teraz są gotowe do eksplorowania próbkowania danych. Zaczynamy od przyjrzenia się statystykom opisowym dla **odległości podróży\_** (lub innych) pól:

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Wizualizacja: przykład wykresu pola
Następnie patrzymy na wykres polu dla odległości podróży do wizualizacji quantiles

    df1.boxplot(column='trip_distance',return_type='dict')

![#1 działki][1]

#### <a name="visualization-distribution-plot-example"></a>Wizualizacja: przykład wykresu dystrybucji
    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![#2 wykreślenia][2]

#### <a name="visualization-bar-and-line-plots"></a>Wizualizacja: wykresy słupkowe i liniowe
W tym przykładzie możemy bin odległość podróży do pięciu pojemników i wizualizacji wyników binning.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Możemy wykreślić powyższy rozkład pojemnika na pasku lub na wykresie liniowym, jak poniżej

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![#3 działki][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![#4 działki][4]

#### <a name="visualization-scatterplot-example"></a>Wizualizacja: Przykład scatterplot
Pokazujemy wykres punktowy między **czasem\_\_podróży w\_sekundach** a **odległością podróży,\_** aby sprawdzić, czy istnieje korelacja

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![#6 działki][6]

Podobnie możemy sprawdzić związek między **kodem stawki\_** a **odległością podróży.\_**

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![#8 działki][8]

### <a name="sub-sampling-the-data-in-sql"></a>Próbkowanie podrzędne danych w języku SQL
Podczas przygotowywania danych do tworzenia modelu w [usłudze Azure Machine Learning Studio](https://studio.azureml.net)można zdecydować się na **kwerendę SQL do użycia bezpośrednio w module Importuj dane** lub utrwalić dane opracowane i próbkowane w nowej tabeli, których można użyć w module [Importuj dane][import-data] za pomocą prostego select * FROM <**\_nową\_nazwę tabeli\_>**.

W tej sekcji utworzymy nową tabelę do przechowywania próbkowanych i zaprojektowanych danych. Przykład bezpośredniej kwerendy SQL dla tworzenia modelu znajduje się w sekcji [Eksploracja danych i Inżynieria funkcji w](#dbexplore) programie SQL Server.

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Utwórz przykładową tabelę i wypełnij 1% połączonych tabel. Najpierw upuszczaj tabelę, jeśli istnieje.
W tej sekcji dołączamy do tabel **nyctaxi\_trip** i **nyctaxi,\_** wyodrębniamy losową próbkę 1% i utrwalimy próbkowane dane w nowej nazwie tabeli **nyctaxi\_o jeden\_procent:**

    cursor = conn.cursor()

    drop_table_if_exists = '''
        IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
    '''

    nyctaxi_one_percent_insert = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
        INTO nyctaxi_one_percent
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (1 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
        AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
    '''

    cursor.execute(drop_table_if_exists)
    cursor.execute(nyctaxi_one_percent_insert)
    cursor.commit()

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Eksploracja danych przy użyciu zapytań SQL w notesie IPython
W tej sekcji firma We eksplorujemy dystrybucje danych przy użyciu 1% próbkowanych danych, które są zachowywane w nowej tabeli, którą utworzyliśmy powyżej. Podobne eksploracje można przeprowadzić przy użyciu oryginalnych tabel, opcjonalnie przy użyciu **TABLESAMPLE** ograniczyć próbki eksploracji lub ograniczając wyniki do danego okresu przy użyciu partycji **\_datetime odbioru,** jak pokazano w [eksploracji danych i inżynierii funkcji w SQL Server](#dbexplore) sekcji.

#### <a name="exploration-daily-distribution-of-trips"></a>Eksploracja: Codzienna dystrybucja podróży
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Eksploracja: Dystrybucja podróży na medalion
    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Generowanie funkcji przy użyciu zapytań SQL w notesie IPython
W tej sekcji wygenerujemy nowe etykiety i funkcje bezpośrednio przy użyciu zapytań SQL, działających w tabeli przykładowej 1% utworzonej w poprzedniej sekcji.

#### <a name="label-generation-generate-class-labels"></a>Generowanie etykiet: generowanie etykiet klas
W poniższym przykładzie generujemy dwa zestawy etykiet do użycia do modelowania:

1. Binary Class Labels **przechylony** (przewidywanie, czy wskazówka zostanie podana)
2. **Klasa końcówki\_** etykiet wieloklasowych (przewidywanie pojemnika na końcówkę lub zakresu)
   
        nyctaxi_one_percent_add_col = '''
            ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
        '''
   
        cursor.execute(nyctaxi_one_percent_add_col)
        cursor.commit()
   
        nyctaxi_one_percent_update_col = '''
            UPDATE nyctaxi_one_percent
            SET
               tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
               tip_class = CASE WHEN (tip_amount = 0) THEN 0
                                WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                                WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                                WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                                ELSE 4
                            END
        '''
   
        cursor.execute(nyctaxi_one_percent_update_col)
        cursor.commit()

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Inżynieria funkcji: Liczba funkcji dla kolumn kategorycznych
W tym przykładzie przekształca pole kategoryczne w pole liczbowe, zastępując każdą kategorię liczbą jego wystąpień w danych.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B AS
        (
            SELECT medallion, hack_license,
                SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
                SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
            FROM nyctaxi_one_percent
            GROUP BY medallion, hack_license
        )

        UPDATE nyctaxi_one_percent
        SET nyctaxi_one_percent.cmt_count = B.cmt_count,
            nyctaxi_one_percent.vts_count = B.vts_count
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion AND A.hack_license = B.hack_license
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Inżynieria funkcji: Funkcje pojemnika dla kolumn numerycznych
W tym przykładzie przekształca ciągłe pole liczbowe w wstępnie ustawione zakresy kategorii, czyli przekształca pole liczbowe w pole kategoryczne.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
        (
            SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
            NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
        )

        UPDATE nyctaxi_one_percent
        SET trip_time_bin = B.BinNumber
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion
        AND A.hack_license = B.hack_license
        AND A.pickup_datetime = B.pickup_datetime
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Inżynieria funkcji: wyodrębnianie operacji lokalizacji z dziesiętnej szerokości/długości geograficznej
W tym przykładzie rozkłada dziesiętną reprezentację pola szerokości i/lub długości geograficznej na wiele pól regionu o różnej szczegółowości, takich jak kraj/region, miasto, miasto, blok itp. Nowe pola geograficzne nie są mapowane na rzeczywiste lokalizacje. Aby uzyskać informacje na temat mapowania lokalizacji geokodów, zobacz [Usługi REST mapy Bing .](https://msdn.microsoft.com/library/ff701710.aspx)

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent
        ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
            l5 varchar(3), l6 varchar(3), l7 varchar(3)
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET l1=round(pickup_longitude,0)
            , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
            , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
            , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
            , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
            , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
            , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Sprawdź ostateczną formę tabeli featurized
    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Jesteśmy teraz gotowi przystąpić do tworzenia modelu i wdrażania modelu w [usłudze Azure Machine Learning.](https://studio.azureml.net) Dane są gotowe na każdy z problemów z przewidywaniem zidentyfikowanych wcześniej, a mianowicie:

1. Klasyfikacja binarna: Aby przewidzieć, czy napiwek został opłacony za podróż.
2. Klasyfikacja wieloklasowa: Aby przewidzieć zakres końcówki wypłacane, zgodnie z wcześniej zdefiniowanych klas.
3. Zadanie regresji: Aby przewidzieć kwotę napiwku zapłaconego za podróż.  

## <a name="building-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Tworzenie modeli w usłudze Azure Machine Learning
Aby rozpocząć ćwiczenie modelowania, zaloguj się do obszaru roboczego usługi Azure Machine Learning. Jeśli nie utworzono jeszcze obszaru roboczego uczenia maszynowego, zobacz [Tworzenie obszaru roboczego usługi Azure Machine Learning](../studio/create-workspace.md).

1. Aby rozpocząć korzystanie z usługi Azure Machine Learning, zobacz [Co to jest usługa Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Zaloguj się do [usługi Azure Machine Learning Studio](https://studio.azureml.net).
3. Strona główna studio zawiera wiele informacji, filmy, samouczki, linki do odwołania modułów i innych zasobów. Aby uzyskać więcej informacji na temat usługi Azure Machine Learning, zapoznaj się z [Centrum dokumentacji usługi Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Typowy eksperyment szkoleniowy składa się z następujących kroków:

1. Utwórz **eksperyment +NOWY.**
2. Pobierz dane do usługi Azure Machine Learning.
3. W razie potrzeby przetwórz, przekształcaj i manipuluj danymi.
4. Generuj funkcje w razie potrzeby.
5. Podziel dane na zestawy danych szkoleniowe/walidacyjne/testujące(lub mają oddzielne zestawy danych dla każdego z nich).
6. Wybierz jeden lub więcej algorytmów uczenia maszynowego w zależności od problemu uczenia się do rozwiązania. Na przykład klasyfikacja binarna, klasyfikacja wieloklasowa, regresja.
7. Trenuj jeden lub więcej modeli przy użyciu zestawu danych szkolenia.
8. Ocena zestawu danych sprawdzania poprawności przy użyciu wyszkolonych modeli.
9. Oceń model(y), aby obliczyć odpowiednie metryki problemu uczenia się.
10. Dostroić model(y) i wybrać najlepszy model do wdrożenia.

W tym ćwiczeniu zbadaliśmy już i zaprojektowaliśmy dane w programie SQL Server i zdecydowaliśmy się na rozmiar próbki do pozyskiwania w usłudze Azure Machine Learning. Aby utworzyć jeden lub więcej modeli prognozowania, zdecydowaliśmy:

1. Pobierz dane do usługi Azure Machine Learning przy użyciu modułu [Importuj dane,][import-data] dostępne w danych **wejściowych i wyjściowych** sekcji. Aby uzyskać więcej informacji, zobacz stronę odwołania do modułu [importu][import-data] danych.
   
    ![Dane importu usługi Azure Machine Learning][17]
2. Wybierz **usługę Azure SQL Database** jako źródło **danych** w panelu **Właściwości.**
3. Wprowadź nazwę DNS bazy danych w polu **Nazwa serwera bazy danych.** Formacie:`tcp:<your_virtual_machine_DNS_name>,1433`
4. Wprowadź **nazwę bazy danych** w odpowiednim polu.
5. Wprowadź **nazwę użytkownika SQL** w **nazwie konta użytkownika serwera**oraz **hasło** w **haśle konta użytkownika serwera**.
7. W obszarze edycji tekstu **kwerendy bazy danych** wklej kwerendę, która wyodrębnia niezbędne pola bazy danych (w tym wszystkie obliczone pola, takie jak etykiety) i w dół próbkuje dane do żądanego rozmiaru próbki.

Przykład eksperymentu klasyfikacji binarnej odczytu danych bezpośrednio z bazy danych programu SQL Server znajduje się na poniższym rysunku. Podobne eksperymenty mogą być konstruowane dla wieloklasowych problemów klasyfikacji i regresji.

![Pociąg usługi Azure Machine Learning][10]

> [!IMPORTANT]
> W przykładach wyodrębniania danych modelowania i próbkowania zapytań podanych w poprzednich sekcjach **wszystkie etykiety dla trzech ćwiczeń modelowania są zawarte w kwerendzie**. Ważnym (wymaganym) krokiem w każdym z ćwiczeń modelowania jest **wykluczenie** zbędnych etykiet dla pozostałych dwóch problemów i wszelkich innych **przecieków docelowych.** Na przykład w przypadku korzystania z klasyfikacji binarnej należy użyć etykiety **z końcówką** i wykluczyć **klasę\_końcówki**pól, kwotę **końcówki\_** i łączną **\_kwotę.** Te ostatnie są przecieki docelowe, ponieważ implikują napiwek wypłacane.
> 
> Aby wykluczyć niepotrzebne kolumny i/lub przecieki [docelowe,][select-columns] można użyć modułu Wybierz kolumny w zestawie danych lub [Edytuj metadane][edit-metadata]. Aby uzyskać więcej informacji, zobacz [Wybieranie kolumn w zestawie danych][select-columns] i Edytowanie stron referencyjnych [metadanych.][edit-metadata]
> 
> 

## <a name="deploying-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Wdrażanie modeli w usłudze Azure Machine Learning
Gdy model jest gotowy, można łatwo wdrożyć go jako usługę sieci web bezpośrednio z eksperymentu. Aby uzyskać więcej informacji na temat wdrażania usług azure machine learning sieci web, zobacz [Wdrażanie usługi sieci Web usługi Azure Machine Learning.](../studio/deploy-a-machine-learning-web-service.md)

Aby wdrożyć nową usługę sieci web, należy:

1. Tworzenie eksperymentu oceniania.
2. Wdrażanie usługi sieci web.

Aby utworzyć eksperyment oceniania z eksperymentu **Zakończone szkolenie,** kliknij przycisk **UTWÓRZ EKSPERYMENT PUNKTACJI** na dolnym pasku akcji.

![Ocena platformy Azure][18]

Usługa Azure Machine Learning podejmie próbę utworzenia eksperymentu oceniania na podstawie składników eksperymentu szkoleniowego. W szczególności:

1. Zapisz przeszkolony model i usuń moduły szkoleniowe modelu.
2. Identyfikowanie logicznego **portu wejściowego** w celu reprezentowania oczekiwanego schematu danych wejściowych.
3. Identyfikowanie logicznego **portu wyjściowego** w celu reprezentowania oczekiwanego schematu wyjściowego usługi sieci Web.

Podczas tworzenia eksperymentu oceniania przejrzyj go i dostosuj w razie potrzeby. Typowym dostosowaniem jest zastąpienie wejściowego zestawu danych i/lub kwerendy takim, który wyklucza pola etykiet, ponieważ te etykiety nie będą dostępne w schemacie, gdy usługa jest wywoływana. Jest również dobrą praktyką, aby zmniejszyć rozmiar wejściowego zestawu danych i/lub kwerendy do kilku rekordów, wystarczy wskazać schemat wejściowy. Dla portu wyjściowego jest wspólne, aby wykluczyć wszystkie pola wejściowe i obejmują tylko **ocenione etykiety** i **ocenione prawdopodobieństwa** w danych wyjściowych przy użyciu Wybierz kolumny w module [zestawu danych.][select-columns]

Przykładowy eksperyment oceniania znajduje się na poniższym rysunku. Gdy będzie gotowy do wdrożenia, kliknij przycisk **PUBLIKUJ USŁUGĘ SIECI WEB** na dolnym pasku akcji.

![Publikowanie usługi Azure Machine Learning][11]

Podsumowując, w tym samouczku utworzono środowisko do nauki o danych platformy Azure, które pracowało z dużym publicznym zestawem danych, od pozyskiwania danych do szkolenia modelu i wdrażania usługi sieci web usługi Azure Machine Learning.

### <a name="license-information"></a>Informacje o licencji
Ten przykładowy instruktaż i towarzyszące mu skrypty i notesy IPython są udostępniane przez firmę Microsoft na licencji MIT. Sprawdź plik LICENSE.txt w katalogu przykładowego kodu w usłudze GitHub, aby uzyskać więcej informacji.

### <a name="references"></a>Dokumentacja
• [Andrés Monroy NYC Taxi Trips Strona pobierania](https://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC's Taxi Trip Dane Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi i Limousine Komisji Badań i Statystyk](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sql-walkthrough/azuremltrain.png
[11]: ./media/sql-walkthrough/azuremlpublish.png
[12]: ./media/sql-walkthrough/ssmsconnect.png
[13]: ./media/sql-walkthrough/executescript.png
[14]: ./media/sql-walkthrough/sqlserverproperties.png
[15]: ./media/sql-walkthrough/sqldefaultdirs.png
[16]: ./media/sql-walkthrough/bulkimport.png
[17]: ./media/sql-walkthrough/amlreader.png
[18]: ./media/sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
