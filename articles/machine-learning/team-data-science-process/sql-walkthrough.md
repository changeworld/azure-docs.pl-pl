---
title: Tworzenie i wdrażanie modelu uczenia maszynowego przy użyciu programu SQL Server na maszynie Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Proces zaawansowane metody analizy i technologii w akcji
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 6066b083-262c-4453-a712-a5c05acc3df8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: deguhath
ms.openlocfilehash: 1e53814c0c0598380944d576e1937e38d1e1d792
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>Proces nauki danych zespołu w działaniu: przy użyciu programu SQL Server
W tym samouczku opisano proces tworzenia i wdrażania modelu uczenia maszynowego przy użyciu programu SQL Server i publicznie dostępnych danych — [rund taksówki NYC](http://www.andresmh.com/nyctaxitrips/) zestawu danych. Procedura następuje nauki standardowych danych przepływu pracy: pozyskiwania i eksplorować dane funkcje odtwarzania w celu ułatwienia learning, a następnie Skompiluj i Wdróż model.

## <a name="dataset"></a>Taksówki NYC podróży opis zestawu danych
Dane podróży taksówki NYC to około 20GB skompresowanego plików CSV (~ 48GB nieskompresowane), składającej się z więcej niż 173 milionów poszczególnych podróży i opłaty płatnej dla każdej podróży. Każdy rekord podróży obejmuje odbiór i przyjmowania lokalizacji i czasu, hack anonimowe (sterownik) numer licencji i numer Medalionu (taksówki jego unikatowy identyfikator). Dane obejmuje wszystkie rund w roku 2013 i jest dostępne w następujących dwóch zestawów danych dla każdego miesiąca:

1. "Trip_data" CSV zawiera szczegóły podróży, takie jak liczba pasażerów, pobranie i punkty dropoff czas trwania podróży i długość podróży. Poniżej przedstawiono kilka przykładowych rekordów:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Trip_fare CSV zawiera szczegółowe informacje o klasie za każdym razem, takie jak typ płatności, kwota taryfy, przeciążenia i podatków, porady i przejazd i sumy płatnej. Poniżej przedstawiono kilka przykładowych rekordów:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Unikatowy klucz do przyłączenia podróży\_danych i podróży\_taryfy składa się z pola: Medalionu, hack\_licencji i pobrania\_daty/godziny.

## <a name="mltasks"></a>Przykłady prognozowania zadań
Firma Microsoft będzie sformułować trzy problemów prognozowania na podstawie *Porada\_kwota*, to znaczy:

1. Klasyfikacji binarnej: prognozowania, czy etykietki został płatnej podróży, tj. *Porada\_kwota* większą niż $0 jest przykład dodatnią, podczas gdy *Porada\_kwota* $ 0 jest ujemny przykład.
2. Wieloklasowej klasyfikacji: przewidywanie zakres Porada uregulowaniu płatności podróży. Możemy podzielić *Porada\_kwota* do pięciu bins lub klasy:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. Zadanie regresji: przewidywanie ilość Porada płatnej w podróży.  

## <a name="setup"></a>Ustawienie zapasowej Azure danych nauki środowiska zaawansowana analityka
Jak widać w [Planowanie środowiska Your](plan-your-environment.md) przewodnik, dostępnych jest kilka opcji do pracy z zestawem danych rund taksówki NYC na platformie Azure:

* Praca z danych w obiektach blob Azure, a następnie modelu w usłudze Azure Machine Learning
* Ładowanie danych do bazy danych programu SQL Server, a następnie modelu w usłudze Azure Machine Learning

W tym samouczku przedstawiono równoległych zbiorczego importowania danych do programu SQL Server, Eksploracja danych, funkcja inżynierii i w dół próbkowania przy użyciu programu SQL Server Management Studio, a także za pomocą notesu IPython. [Przykładowe skrypty](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) i [notesów IPython](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) są udostępniane w witrynie GitHub. Przykładowy notes IPython, aby pracować z danymi w obiektach blob Azure jest również dostępna w tej samej lokalizacji.

Aby skonfigurować środowisko nauki danych Azure:

1. [Tworzenie konta magazynu](../../storage/common/storage-create-storage-account.md)
2. [Utwórz obszar roboczy usługi Azure Machine Learning](../studio/create-workspace.md)
3. [Udostępnianie danych maszyny wirtualnej nauki](../data-science-virtual-machine/setup-sql-server-virtual-machine.md), zapewniające programu SQL Server i serwer IPython notesu.
   
   > [!NOTE]
   > Przykładowe skrypty i notebooki IPython zostaną pobrane do analizy danych maszyny wirtualnej, podczas procesu instalacji. Po zakończeniu działania skryptu poinstalacyjnego maszyny Wirtualnej, przykłady będą biblioteki dokumentów maszyny Wirtualnej:  
   > 
   > * Przykładowe skrypty: `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Przykładowe IPython notesów: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   gdzie `<user_name>` jest nazwą logowania systemu Windows maszyny Wirtualnej. Odnoszą się do folderów próbki jako **przykładowe skrypty** i **notesów IPython próbki**.
   > 
   > 

Na podstawie rozmiaru zestawu danych, lokalizacja źródła danych i środowiska wybranego celu Azure, w tym scenariuszu jest podobny do [scenariusza \#5: dużego zestawu danych w lokalnych plikach, docelowa programu SQL Server w maszynie Wirtualnej platformy Azure](plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>Pobierz dane ze źródła publiczny
Aby uzyskać [rund taksówki NYC](http://www.andresmh.com/nyctaxitrips/) zestawu danych z lokalizacji publicznej, możesz użyć dowolnej z metod opisanych w [przenoszenie danych do i z magazynu obiektów Blob Azure](move-azure-blob.md) Aby skopiować dane do nowej maszyny wirtualnej.

Aby skopiować dane przy użyciu narzędzia AzCopy:

1. Zaloguj się do maszyny wirtualnej (VM)
2. Utwórz nowy katalog dysku danych maszyny Wirtualnej (Uwaga: nie używaj tymczasowych dysk, który jest dostarczany z maszyny Wirtualnej jako dysk z danymi).
3. W oknie wiersza polecenia Uruchom następujący wiersz polecenia Azcopy, zastępując < path_to_data_folder > folderu danych utworzone w (2):
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    Po zakończeniu działania narzędzia AzCopy, łącznie 24 zip plików CSV (12 podczas podróży\_danych i 12 podczas podróży\_taryfy) powinny się znajdować w folderze danych.
4. Rozpakuj pobranych plików. Zwróć uwagę na folder, w którym znajdują się pliki nieskompresowane. Ten folder będzie ona nazywana jako < ścieżka\_do\_danych\_pliki\>.

## <a name="dbload"></a>Zbiorcze importowanie danych do bazy danych serwera SQL
Można zwiększyć wydajność podczas ładowania/transfer dużych ilości danych do bazy danych SQL i kolejne zapytania przy użyciu *partycjonowane tabele i widoki*. W tej sekcji możemy wykonaj instrukcje opisane w [równoległych zbiorczego danych importu za pomocą SQL tabel partycji](parallel-load-sql-partitioned-tables.md) Aby utworzyć nową bazę danych i załadować dane do partycjonowane tabele równolegle.

1. Po zalogowaniu się do maszyny Wirtualnej, należy uruchomić **programu SQL Server Management Studio**.
2. Połącz przy użyciu uwierzytelniania systemu Windows.
   
    ![Połącz SSMS][12]
3. Jeśli nie została jeszcze zmienić tryb uwierzytelniania programu SQL Server i utworzyć nowego użytkownika logowania SQL, otwórz plik skryptu o nazwie **zmienić\_auth.sql** w **przykładowe skrypty** folderu. Zmienić domyślną nazwę użytkownika i hasło. Kliknij przycisk **! Wykonanie** na pasku narzędzi, aby uruchomić skrypt.
   
    ![Uruchom skrypt][13]
4. Sprawdź i/lub zmienić program SQL Server domyślnej bazy danych i dziennika foldery do upewnij się, które nowo utworzone bazy danych będą przechowywane na dysku danych. Obraz maszyny Wirtualnej programu SQL Server, który jest zoptymalizowany pod kątem obciążeń strumienia jest wstępnie skonfigurowana z dyskami danych i dziennika. Jeśli maszyna wirtualna nie zawiera dysk z danymi i dodać nowe wirtualnych dysków twardych podczas procesu konfiguracji maszyny Wirtualnej, zmień domyślne foldery w następujący sposób:
   
   * Kliknij prawym przyciskiem myszy nazwę serwera SQL w lewym panelu, a następnie kliknij przycisk **właściwości**.
     
       ![Właściwości serwera SQL][14]
   * Wybierz **ustawienia bazy danych** z **wybierz stronę** liście z lewej strony.
   * Sprawdź i/lub zmienić **bazy danych domyślne lokalizacje** do **dysku danych** lokalizacje wybranych przez użytkownika. Jest to, gdzie nowe bazy danych znajdują się jeśli utworzony przy użyciu ustawień domyślnych lokalizacji.
     
       ![Domyślne ustawienia bazy danych SQL][15]  
5. Aby utworzyć nową bazę danych i zestaw grup plików do przechowywania partycjonowane tabele, Otwórz przykładowy skrypt **utworzyć\_db\_default.sql**. Skrypt spowoduje utworzenie nowej bazy danych o nazwie **TaxiNYC** i 12 grup plików w domyślnej lokalizacji danych. Każda grupa plików będą przechowywane miesiąc podróży\_danych i podróży\_taryfy danych. W razie potrzeby zmodyfikuj nazwę bazy danych. Kliknij przycisk **! Wykonanie** do uruchomienia skryptu.
6. Następnie należy utworzyć dwie tabele partycji, jeden dla podróży\_danych i drugi dla podróży\_klasie. Otwórz przykładowy skrypt **utworzyć\_partycjonowanej\_table.sql**, który będzie:
   
   * Utwórz funkcję partycji można rozdzielić według miesięcy.
   * Tworzenie schematu partycji do mapowania każdego miesiąca danych do innej grupy plików.
   * Utwórz dwie tabele partycjonowane mapowane na schemat partycji: **nyctaxi\_podróży** będą przechowywane podróż\_danych i **nyctaxi\_taryfy** będą przechowywane podróż\_taryfy danych.
     
     Kliknij przycisk **! Wykonanie** uruchomić skrypt i utworzyć tabele partycjonowane.
7. W **przykładowe skrypty** folderu, istnieją dwie przykładowe skrypty programu PowerShell demonstrujących równoległych zbiorczego importuje danych do tabel programu SQL Server.
   
   * **Narzędzie BCP\_równoległych\_generic.ps1** jest skrypt rodzajowy równoległych zbiorcze i zaimportuj dane do tabeli. Zmodyfikuj ten skrypt, aby ustawić zmienne wejściowe i docelowy wskazane wiersze komentarzy w skrypcie.
   * **Narzędzie BCP\_równoległych\_nyctaxi.ps1** jest wstępnie skonfigurowana wersja skrypt rodzajowy i może służyć do załadować obu tabel danych NYC taksówki rund.  
8. Kliknij prawym przyciskiem myszy **bcp\_równoległych\_nyctaxi.ps1** kliknij nazwę skryptu wraz z **Edytuj** go otworzyć w programie PowerShell. Przejrzyj predefiniowanych zmiennych i zmodyfikować zgodnie z nazwa wybranej bazy danych, folderu danych wejściowych, folder docelowy dziennika i ścieżki do plików formatu próbki **nyctaxi_trip.xml** i **nyctaxi\_fare.xml** (w **przykładowe skrypty** folderu).
   
    ![Zbiorczego importowania danych][16]
   
    Można również wybrać tryb uwierzytelniania, domyślne uwierzytelniania systemu Windows. Kliknij zieloną strzałkę na pasku narzędzi do uruchomienia. Skrypt zostanie uruchomiony 24 operacji importowania zbiorczego w 12 równoległe, dla każdej tabeli partycjonowanej. Mogą monitorować postęp importowania danych przez otwarcie domyślnym folderem danych programu SQL Server jako zestaw powyżej.
9. Skrypt programu PowerShell zgłasza godziny rozpoczęcia i zakończenia. Jeśli zbiorczo wszystkie Importy ukończone, zgłaszane godzinę zakończenia. Sprawdź folder docelowy dziennika można zweryfikować, że Importy zbiorcze zostały wprowadzone pomyślnie, tj. żadne błędy nie zgłoszone w folderze docelowym dziennika.
10. Baza danych jest teraz gotowe do eksploracji, funkcja inżynieryjne i innych czynności. Ponieważ tabele są podzielone na partycje według **podnoszenia\_daty/godziny** pola zapytania, które obejmują **podnoszenia\_daty/godziny** warunki w **gdzie** klauzuli będą korzystać z schemat partycji.
11. W **programu SQL Server Management Studio**, Eksploruj podana przykładowy skrypt **próbki\_queries.sql**. Do uruchomienia dowolnego przykładowe zapytania, zaznacz wiersze zapytań, a następnie kliknij przycisk **! Wykonanie** na pasku narzędzi.
12. Dane rund taksówki NYC są ładowane w dwóch oddzielnych tabelach. Aby zwiększyć operacji łączenia, zaleca indeksu tabeli. Przykładowy skrypt **utworzyć\_partycjonowanej\_index.sql** tworzy indeksy podzielone na partycje klucza złożonego sprzężenia **Medalionu, hack\_licencji i pobrania\_datetime**.

## <a name="dbexplore"></a>Eksploracja danych i inżynieria funkcji w programie SQL Server
Generowanie funkcji i eksploracja danych w tej sekcji zostaną wykonane przez uruchomienie zapytania SQL bezpośrednio w **programu SQL Server Management Studio** wcześniej utworzony przy użyciu bazy danych programu SQL Server. Przykładowy skrypt o nazwie **próbki\_queries.sql** znajduje się w **przykładowe skrypty** folderu. Zmodyfikuj skrypt, aby zmienić nazwę bazy danych, jeśli jest inny niż domyślny: **TaxiNYC**.

W tym ćwiczeniu zostaną wykonane następujące czynności:

* Połączyć się z **programu SQL Server Management Studio** przy użyciu uwierzytelnianiu systemu Windows lub uwierzytelniania SQL, a nazwa logowania SQL i hasło.
* Eksplorowanie danych dystrybucji kilka pól w różnym czasie systemu windows.
* Zbadaj jakości danych pól długości i szerokości geograficznej.
* Generowanie na podstawie etykiet klasyfikacji binarnej i wieloklasowej **Porada\_kwota**.
* Generowanie funkcji i obliczeń lub porównania odległości podróży.
* Dołącz do dwóch tabel i wyodrębniać losowej próbki, która będzie służyć do tworzenia modeli.

Gdy wszystko będzie gotowe przejść do usługi Azure Machine Learning, użytkownik może:  

1. Zapisz końcowego zapytanie SQL do wyodrębnienia przykładowe dane i skopiuj i Wklej zapytanie bezpośrednio do [i zaimportuj dane] [ import-data] modułu w usłudze Azure Machine Learning, lub
2. Utrwalanie próbki i odtworzone danych, które będą używane do tworzenia nowej bazy danych modelu tabeli i użyj nowej tabeli w [i zaimportuj dane] [ import-data] modułu w usłudze Azure Machine Learning.

W tej sekcji możemy zapisze końcowego zapytanie, aby wyodrębnić i przykładowe dane. Druga metoda jest przedstawiona w [Eksploracja danych i funkcji inżynieryjnego w notesie IPython](#ipnb) sekcji.

Szybkie weryfikacji liczby wierszy i kolumn w tabelach wypełnione wcześniej za pomocą importowania zbiorczego równoległych

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Eksploracja: Podróży dystrybucji przez Medalionu
W tym przykładzie identyfikuje Medalionu (taksówki numery) z więcej niż 100 rund w danym okresie. Zapytanie będzie korzystać z dostępu do tabeli partycjonowanej ponieważ należy przygotować w schemacie partycji **podnoszenia\_datetime**. Wykonywanie zapytania pełnego zestawu danych umożliwiają użycie tabeli partycjonowanej i/lub indeksu skanowania.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Eksploracja: Podróży dystrybucji Medalionu i hack_license
    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Oceny jakości danych: Rekordy z geograficzne niepoprawne i/lub szerokość geograficzną Sprawdź
W tym przykładzie sprawdza, czy polach geograficzne i/lub szerokość geograficzną albo zawiera nieprawidłową wartość (stopni w radianach powinna należeć do zakresu od-90 do 90,) lub (0, 0) współrzędnych.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Eksploracja: Przechylony vs. Nie Przechylony rund dystrybucji
W tym przykładzie znajduje się liczba podróży, które zostały Przechylony a nie Przechylony w danym momencie okres (lub pełnego zestawu danych, jeśli obejmujące pełny rok). Tej dystrybucji odzwierciedla dystrybucji binarne etykiety później służący do modelowania klasyfikacji binarnej.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Eksploracja: Porada dystrybucji klasy/zakresu
W tym przykładzie oblicza rozkład Porada zakresów w danym przedziale czasu (lub pełnego zestawu danych, jeśli obejmujące pełny rok). Jest to dystrybucji klasy etykiety, które zostanie później użyty do modelowania wieloklasowej klasyfikacji.

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

#### <a name="exploration-compute-and-compare-trip-distance"></a>Eksploracja: Obliczania i porównać odległość podróży
W tym przykładzie konwertuje długość odbiór i przyjmowania i szerokości geograficznej lokalizacji SQL punkty, oblicza odległość podróży przy użyciu różnicę punktów geograficzne SQL i zwraca losowej próbki wyniki do porównania. Przykład ogranicza wyniki do prawidłowe współrzędne tylko przy użyciu zapytania oceny jakości danych objętych wcześniej.

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

#### <a name="feature-engineering-in-sql-queries"></a>Funkcja Engineering w zapytania SQL
Etykieta generowania i geograficzne konwersji eksploracji zapytania mogą służyć do generowania etykiet/funkcje przez usunięcie części zliczania. Dodatkową cechą engineering SQL przykłady znajdują się w [Eksploracja danych i funkcji inżynieryjnego w notesie IPython](#ipnb) sekcji. Jest bardziej wydajne działanie funkcji generowania zapytania na pełnego zestawu danych lub duży podzbiór za pomocą zapytania SQL, które Uruchom bezpośrednio w wystąpieniu bazy danych programu SQL Server. Zapytania mogą być wykonywane w **programu SQL Server Management Studio**, notesu IPython lub dowolnego narzędzia/Środowisko deweloperskie, którego można uzyskać dostępu do bazy danych lokalnie lub zdalnie.

#### <a name="preparing-data-for-model-building"></a>Przygotowywanie danych do konstruowania modelu
Następujące zapytanie sprzęga **nyctaxi\_podróży** i **nyctaxi\_taryfy** tabel, generuje etykiety klasyfikacji binarnej **Przechylony**, etykiety klasyfikacji wielu klasy **Porada\_klasy**i wyodrębnia 1% losowej próbki z dołączonym do pełnego zestawu danych. To zapytanie może skopiować następnie wkleić bezpośrednio w [Azure Machine Learning Studio](https://studio.azureml.net) [i zaimportuj dane] [ import-data] modułu dla wprowadzanie danych bezpośrednio z wystąpienia bazy danych programu SQL Server na platformie Azure. Zapytanie nie obejmuje rekordy z niepoprawne (0, 0) współrzędnych.

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


## <a name="ipnb"></a>Eksploracja danych i funkcji inżynieryjne w notesie IPython
W tej sekcji zostaną wykonane Eksplorowanie danych oraz generowanie funkcji za pomocą zarówno Python, jak i SQL zapytań dotyczących bazy danych programu SQL Server wcześniej utworzony. Przykładowe IPython Notes o nazwie **machine-Learning-data-science-process-sql-story.ipynb** znajduje się w **notesów IPython próbki** folderu. Ten notes jest również dostępna w [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

Zalecana kolejność podczas pracy z danymi big data jest następujący:

* Odczyt w małych przykładowych danych do ramki danych w pamięci.
* Wykonywanie niektórych wizualizacje i eksploracji przy użyciu próbki danych.
* Wypróbuj engineering funkcji przy użyciu próbki danych.
* Dla większych Eksploracja danych, do manipulowania danymi i inżynieria funkcji należy użyć Python do wysyłania zapytań SQL bezpośrednio w bazie danych programu SQL Server w maszynie Wirtualnej platformy Azure.
* Określ rozmiar próbki służące do tworzenia modelu uczenia maszynowego Azure.

Po wykonaniu tych czynności można przejść do usługi Azure Machine Learning, użytkownik może:  

1. Zapisz końcowego zapytanie SQL do wyodrębnienia przykładowe dane i skopiuj i Wklej zapytanie bezpośrednio do [i zaimportuj dane] [ import-data] modułu w usłudze Azure Machine Learning. Ta metoda jest przedstawiona w [tworzenia modeli w usłudze Azure Machine Learning](#mlmodel) sekcji.    
2. Zachować dane próbki i odtworzone będą używane dla modelu tworzenie w nowej tabeli bazy danych, a następnie użyj nowej tabeli w [i zaimportuj dane] [ import-data] modułu.

Poniżej przedstawiono kilka Eksploracja danych, wizualizacji danych i funkcji inżynierii przykłady. Więcej przykładów można znaleźć przykładowy notes SQL IPython w **notesów IPython próbki** folderu.

#### <a name="initialize-database-credentials"></a>Inicjowanie poświadczenia bazy danych
Inicjowanie ustawienia połączenia bazy danych w następujących zmiennych:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Utwórz połączenie z bazą danych
    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Raport liczba wierszy i kolumn w tabeli nyctaxi_trip
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
* Łączna liczba kolumn = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Odczyt w przykładowych małych danych z bazy danych programu SQL Server
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

Czas odczytu Przykładowa tabela jest 6.492000 sekund  
Liczba wierszy i kolumn pobrać = (84952, 21)

#### <a name="descriptive-statistics"></a>Statystyki opisowe
Teraz przystąpić do eksplorowania próbki danych. Możemy zaczynać się patrzeć Statystyki opisowe dla **podróży\_odległość** (lub innych) pola/pól:

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Wizualizacji: Przykład kreślenia pola
Następnie przyjrzymy się skrzynkowy odległość podróży do wizualizacji quantiles

    df1.boxplot(column='trip_distance',return_type='dict')

![Wykreślenia #1][1]

#### <a name="visualization-distribution-plot-example"></a>Wizualizacji: Przykład kreślenia dystrybucji
    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Wykreślenia #2][2]

#### <a name="visualization-bar-and-line-plots"></a>Wizualizacji: Pasek i powierzchni wiersza
W tym przykładzie mamy bin odległość podróży, w pojemnikach pięć i wizualizacja wyników binning.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Firma Microsoft może wykreślenia powyżej dystrybucji bin na pasku lub wiersz kreślenia zgodnie z poniższymi instrukcjami

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Wykreślenia #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Wykreślenia #4][4]

#### <a name="visualization-scatterplot-example"></a>Wizualizacji: Przykład Scatterplot
Zostanie przedstawiony jest wykres punktowy między **podróży\_czasu\_w\_s** i **podróży\_odległość** się czy ma żadnych korelacji

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Wykreślenia #6][6]

Podobnie można sprawdzić relacji między **szybkość\_kod** i **podróży\_odległość**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Wykreślenia #8][8]

### <a name="sub-sampling-the-data-in-sql"></a>Podrzędna próbkowanie danych SQL
Podczas przygotowywania danych dla modelu kompilacji w [Azure Machine Learning Studio](https://studio.azureml.net), albo można wyłączyć na **zapytanie SQL do użycia bezpośrednio w module i zaimportuj dane** lub utrwalenia odtworzone i próbki danych w nowej tabeli, którego można użyć w [i zaimportuj dane] [ import-data] modułu przy użyciu prostego **wybierz * FROM < Twojej\_nowe\_tabeli\_nazwa >**.

W tej sekcji utworzymy nową tabelę do przechowywania danych próbki i sztucznymi. Przykład bezpośrednie kwerendy SQL dla tworzenia modelu znajduje się w [Eksploracja danych i funkcji inżynieryjnego w programie SQL Server](#dbexplore) sekcji.

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Tworzenie przykładu tabeli i wypełnić połączonych tabel: % 1. Jeśli istnieje porzucić pierwszej tabeli.
W tej sekcji możemy sprzężenia **nyctaxi\_podróży** i **nyctaxi\_taryfy**, Wyodrębnij losowej próbki 1%, i utrwalić próbki danych w nowej nazwy tabeli **nyctaxi\_jeden\_procent**:

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

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Eksploracja danych za pomocą zapytań SQL w notesie IPython
W tej sekcji możemy Eksplorowanie danych dystrybucji przy użyciu danych próbkowany % 1, która jest utrwalona w nowej tabeli utworzone powyżej. Należy pamiętać, że podobne eksploracji może zostać wykonane przy użyciu oryginalnego tabel, opcjonalnie używając **TABLESAMPLE** ograniczenie eksploracji przykładowa lub ograniczając wyniki w danym momencie okresu using **podnoszenia\_datetime** partycji, jak pokazano na [Eksploracja danych i funkcji inżynieryjnego w programie SQL Server](#dbexplore) sekcji.

#### <a name="exploration-daily-distribution-of-trips"></a>Eksploracji: Codzienne dystrybucji rund
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Eksploracja: Podróży dystrybucji na Medalionu
    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Generowanie funkcji za pomocą zapytań SQL w notesie IPython
W tej sekcji wygenerujemy nowe etykiety i funkcji bezpośrednio za pomocą zapytania SQL, korzysta z 1% Przykładowa tabela utworzyliśmy w poprzedniej sekcji.

#### <a name="label-generation-generate-class-labels"></a>Generowanie etykiety: Generowanie etykiet — klasa
W poniższym przykładzie mamy Generowanie dwa zestawy etykiet do użycia na potrzeby modelowania:

1. Binarny etykiety klasy **Przechylony** (przewidywania, jeśli będzie miał poradę)
2. Etykiety wieloklasowej **Porada\_klasy** (przewidywania bin porada lub zakres)
   
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

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Inżynieria funkcji: Liczba funkcje dla kolumny podzielone na kategorie
W tym przykładzie przekształca pole kategorii do pola liczbowego przez zamianę każdej kategorii liczba ich wystąpień w danych.

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

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Funkcja Engineering: Bin funkcje dla kolumny liczbowe
W tym przykładzie przekształca pola numerycznego ciągłe na zakresy istniejących kategorii, tj., Przekształcanie pola numerycznego w pole kategorii.

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

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Inżynieria funkcji: Wyodrębniania dziesiętną szerokości geograficznej/długości geograficznej lokalizacji funkcji
W tym przykładzie dzieli dziesiętną reprezentację szerokości geograficznej i/lub długości pola na wielu pól region z różnych szczegółowości takich, jak kraj, Miasto, miejscowość, blok itp. Należy zwrócić uwagę na nowe pola geograficznie nie są mapowane do rzeczywistej lokalizacji. Informacje dotyczące mapowania lokalizacji geocode znajdują się w temacie [usług REST mapy usługi Bing](https://msdn.microsoft.com/library/ff701710.aspx).

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

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Sprawdź ostatecznej formie tabeli featurized
    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Firma Microsoft są teraz gotowe do przejścia do konstruowania modelu i wdrażania modelu w [usługi Azure Machine Learning](https://studio.azureml.net). Dane są gotowe do żadnego z prognozowania problemy wcześniej, to znaczy:

1. Klasyfikacji binarnej: przewidywanie, czy etykietki został płatnej w podróży.
2. Wieloklasowej klasyfikacji: przewidywanie zakres Porada płatną zgodnie z uprzednio zdefiniowanej klasy.
3. Zadanie regresji: przewidywanie ilość Porada płatnej w podróży.  

## <a name="mlmodel"></a>Tworzenie modeli w uczenie maszynowe Azure
Aby rozpocząć wykonywania modelowania, zaloguj się do swojego obszaru roboczego uczenia maszynowego Azure. Jeśli jeszcze nie utworzono obszaru roboczego uczenia maszynowego, zobacz [Utwórz obszar roboczy usługi Azure Machine Learning](../studio/create-workspace.md).

1. Aby zacząć korzystać z usługi Azure Machine Learning, zobacz [co to jest Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Zaloguj się do [Azure Machine Learning Studio](https://studio.azureml.net).
3. Strona główna Studio zapewnia wiele informacji, wideo, samouczki, linki do odwołania moduły i innych zasobów. Aby uzyskać więcej informacji na temat usługi Azure Machine Learning, zapoznaj się [Centrum dokumentacji uczenia maszynowego Azure](https://azure.microsoft.com/documentation/services/machine-learning/).

Eksperyment typowe szkolenia składa się z następujących czynności:

1. Utwórz **+ nowy** eksperymentu.
2. Pobieranie danych do usługi Azure Machine Learning.
3. Wstępnie przetworzyć, transformacji i manipulowanie danymi w razie potrzeby.
4. Generowanie funkcji zgodnie z potrzebami.
5. Podział danych na zestaw szkolenia / / sprawdzanie poprawności danych (lub mieć osobne zestawy danych dla każdego).
6. Wybierz co najmniej jeden algorytmów uczenia maszynowego w zależności od problemu learning do rozwiązania. Przykład klasyfikacji binarnej, wieloklasowej klasyfikacji, regresji.
7. Szkolenie co najmniej jednego modelu przy użyciu zestawu danych szkoleniowych.
8. Wynik sprawdzania poprawności zestawu danych przy użyciu modeli przeszkolone.
9. Należy ocenić modele obliczeniowe odpowiednich metryki learning problem.
10. Prawidłowo dostrojenie modele i wybrać najlepsze modelu do wdrożenia.

W tym ćwiczeniu mamy już przedstawione i odtwarzane danych w programie SQL Server i pozyskiwania w usłudze Azure Machine Learning, postanowiła na rozmiar próbki. Aby utworzyć co najmniej jeden modele predykcyjne zdecydowaliśmy:

1. Pobierz dane przy użyciu usługi Azure Machine Learning [i zaimportuj dane] [ import-data] modułu dostępne w **danych wejściowych i wyjściowych** sekcji. Aby uzyskać więcej informacji, zobacz [i zaimportuj dane] [ import-data] strony odwołanie do modułu.
   
    ![Uczenie maszynowe Azure i zaimportuj dane][17]
2. Wybierz **bazy danych SQL Azure** jako **źródła danych** w **właściwości** panelu.
3. Wprowadź nazwę DNS bazy danych w **nazwę serwera bazy danych** pola. Format: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Wprowadź **Nazwa bazy danych** w odpowiednie pole.
5. Wprowadź **nazwa użytkownika SQL** w ** nazwa_serwera aqccount użytkownika i hasło w **hasło konta użytkownika serwera**.
7. W **zapytanie bazy danych** edytowania obszaru tekstu, Wklej zapytanie, które zwraca pola niezbędne bazy danych (w tym wszystkie pola obliczanej, takich jak etykiety) i w dół próbek danych do żądanego próbkowania.

Przykład eksperymentu klasyfikacji binarnej odczytywanie danych bezpośrednio z bazy danych programu SQL Server to na poniższej ilustracji. Podobne eksperymenty można utworzyć dla wieloklasowej klasyfikacji i regresji problemów.

![Azure Machine Learning pociągu][10]

> [!IMPORTANT]
> W danych modelowania wyodrębniania i pobierania próbek przykłady zapytania podane w poprzednich sekcjach, **wszystkich etykiet trzy ćwiczeń modelowania są uwzględnione w zapytaniu**. Ważnym krokiem (wymagane) w każdym ćwiczeń modelowania jest **wykluczyć** niepotrzebne etykiety dla innych problemów oraz wszelkie inne **target przecieki**. Na przykład korzystając z klasyfikacji binarnej, za pomocą etykiety **Przechylony** i wykluczyć pola **Porada\_klasy**, **Porada\_kwota**, i **całkowita\_kwota**. Są one przecieki docelowy one zakłada poradę płatnej.
> 
> Wyklucz zbędne kolumny i/lub przecieki docelowych, można użyć [Select Columns in Dataset] [ select-columns] modułu lub [edytowanie metadanych][edit-metadata]. Aby uzyskać więcej informacji, zobacz [Select Columns in Dataset] [ select-columns] i [edytowanie metadanych] [ edit-metadata] odwołania stron.
> 
> 

## <a name="mldeploy"></a>Wdrażanie modeli w uczenie maszynowe Azure
Gdy model jest gotowy, łatwo można go wdrożyć jako usługę sieci web bezpośrednio z eksperymentu. Aby uzyskać więcej informacji na temat wdrażania usług sieci web uczenie maszynowe Azure, zobacz [wdrażanie usługi sieci web Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).

Aby wdrożyć nową usługę sieci web, musisz:

1. Tworzenie eksperymentu oceniania.
2. Wdrażanie usługi sieci web.

Aby utworzyć eksperyment oceniania z **zakończone** szkolenia eksperyment, kliknij przycisk **utworzyć OCENIANIA EKSPERYMENTU** na dolnym pasku akcji.

![Ocenianie przez usługę Azure][18]

Usługa Azure Machine Learning podejmie próbę utworzenia eksperyment oceniania na podstawie składników eksperyment uczenia. W szczególności będzie:

1. Zapisania trenowanego modelu oraz usuwanie modułów uczenia modelu.
2. Zidentyfikuj logicznych **port wejściowy** do reprezentowania schematu oczekiwanych danych wejściowych.
3. Zidentyfikuj logicznych **output portu** do reprezentowania schemat danych wyjściowych usługi sieci web oczekiwanego.

Po utworzeniu oceniania eksperymentu, przejrzyj go i dostosować zgodnie z potrzebami. Typowy korekty jest Zamień wejściowy zestaw danych i/lub zapytania taki, który wyklucza pola etykiety, jak te nie będą dostępne, gdy usługa jest wywoływana. Jest również dobrym rozwiązaniem, aby zmniejszyć rozmiar wejściowy zestaw danych i/lub kwerendy na kilka rekordów wystarczającego wskazująca schemat danych wejściowych. Port wyjściowy jest wspólny dla wszystkich wejściowych Dołączanie i wykluczanie tylko **oceniane etykiety** i **wynik prawdopodobieństwa** w danych wyjściowych przy użyciu [Select Columns in Dataset] [ select-columns] modułu.

Przykładowe oceniania eksperymentu jest na poniższej ilustracji. Gdy będzie gotowy do wdrożenia, kliknij przycisk **OPUBLIKOWAĆ usługi sieci WEB** na dolnym pasku akcji.

![Publikowanie uczenie maszynowe Azure][11]

Recap, w tym samouczku wskazówki utworzono środowiska nauki danych Azure, pracy z zestawem danych publicznych dużych od pozyskiwania danych do modelu uczenie i wdrażanie usługi sieci web uczenie maszynowe Azure.

### <a name="license-information"></a>Informacje o licencji
Ten przewodnik próbki i jego towarzyszące IPython notebook(s) i skrypty są udostępniane przez firmę Microsoft w ramach licencji MIT. Sprawdź plik LICENSE.txt w katalogu przykładowy kod w witrynie GitHub, aby uzyskać więcej informacji.

### <a name="references"></a>Dokumentacja
• [Andrés Monroy taksówki NYC rund stronę pobierania](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC taksówki podróży danych przez Krzysztof Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [Taksówki NYC i Limousine Komisji badań i statystyki](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

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
