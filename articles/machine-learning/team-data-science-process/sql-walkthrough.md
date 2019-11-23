---
title: Tworzenie i wdrażanie modelu w maszynę Wirtualną SQL Server — zespołu danych dla celów naukowych
description: Tworzenie i wdrażanie przy użyciu programu SQL Server na Maszynie wirtualnej platformy Azure przy użyciu publicznie dostępnego zestawu danych model uczenia maszynowego.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/29/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 148d0c203248e4dcde5baaadc596d56e8b8ea17a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73669395"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>Zespół danych dla celów naukowych w działaniu: przy użyciu programu SQL Server
W tym samouczku opisano proces tworzenia i wdrażania modelu uczenia maszynowego, przy użyciu programu SQL Server i publicznie dostępnego zestawu danych — [rund taksówek NYC](https://www.andresmh.com/nyctaxitrips/) zestawu danych. Procedura następuje pracy do analizy danych w warstwie standardowa: pozyskiwanie i eksplorować dane, Projektuj funkcje ułatwić szkolenia, a następnie utworzyć i wdrożyć model.

## <a name="dataset"></a>Taksówek NYC podróży opis zestawu danych
Dane podróży taksówek NYC około 20GB skompresowanych plików CSV (~ 48GB nieskompresowane), zawierających ponad milion 173 poszczególnych podróży i opłaty opłacony każdego podróży. Każdy rekord podróży obejmuje odbiór i dropoff lokalizacji i czasu, hack anonimowe (sterownika) numer licencji i numer Medalionu (unikatowy identyfikator dla taksówek). Dane obejmuje wszystkie podróży w roku 2013 i znajduje się w następujących dwóch zestawów danych w każdym miesiącu:

1. "Trip_data" CSV zawiera szczegóły podróży, takie jak liczba osób, pobrania i dropoff punkty, czasu trwania podróży i długość podróży. Poniżej przedstawiono kilka przykładowych rekordów:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Trip_fare CSV zawiera szczegółowe informacje o opłatę za każdym razem, takich jak typ płatności, kwota taryfy, opłata za opcję i podatków, porady i drogi, a łączna kwota płatne. Poniżej przedstawiono kilka przykładowych rekordów:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Unikatowy klucz, aby dołączyć podróży\_danych i podróży\_taryfy składa się z pól: Medalionu hakowanie\_licencji oraz pobrania\_daty/godziny.

## <a name="mltasks"></a>Przykłady zadań prognoz
Firma Microsoft będzie sformułować trzy problemów prognozowania na podstawie *Porada\_kwota*, to znaczy:

1. Klasyfikacja binarna: przewidywania, czy porady zapłacono w podróży, tj *Porada\_kwota* większą niż 0 zł jest przykładem dodatnią, podczas gdy *Porada\_kwota* wynosi 0 zł jest przykład ujemna.
2. Klasyfikacji wieloklasowej: przewidywanie zakres Porada opłacony wyzwolenie. Możemy podzielić *Porada\_kwota* do pięciu pojemniki lub klasy:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. Zadanie regresji: przewidywanie ilość Porada płatnych komunikacji dwustronnej.  

## <a name="setup"></a>Ustawienie zapasowej platformy Azure środowiska nauki o danych zaawansowanej analizy
Jak widać na [Planowanie środowiska](plan-your-environment.md) przewodnik, dostępnych jest kilka opcji do pracy z zestawem danych podróży taksówek NYC na platformie Azure:

* Praca z danych w obiektach blob platformy Azure, a następnie modelu w usłudze Azure Machine Learning
* Załaduj dane do bazy danych programu SQL Server, a następnie modelu w usłudze Azure Machine Learning

W tym samouczku przedstawiony zostanie równoległy zbiorczy import danych do programu SQL Server, eksploracji danych, funkcja inżynieryjne i w dół próbkowania przy użyciu programu SQL Server Management Studio, a także przy użyciu IPython Notebook. [Przykładowe skrypty](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) i [Notesy programu IPython](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) są udostępniane w usłudze GitHub. Przykładowe IPython notebook do pracy z danymi w obiektach blob platformy Azure jest również dostępna w tej samej lokalizacji.

Aby skonfigurować środowisko nauki o danych platformy Azure:

1. [Tworzenie konta magazynu](../../storage/common/storage-quickstart-create-account.md)
2. [Tworzenie obszaru roboczego usługi Azure Machine Learning](../studio/create-workspace.md)
3. [Aprowizowanie maszyny wirtualnej do nauki o danych](../data-science-virtual-machine/setup-sql-server-virtual-machine.md), który zapewnia programu SQL Server i serwera IPython Notebook.
   
   > [!NOTE]
   > Przykładowe skrypty i Notesy programu IPython zostaną pobrane na maszynę wirtualną do nauki o danych, podczas procesu instalacji. Po zakończeniu działania skryptu poinstalacyjnego maszyny Wirtualnej, przykłady będą w bibliotece dokumentów maszyny Wirtualnej:  
   > 
   > * Przykładowe skrypty: `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Notesy programu IPython próbki: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   gdzie `<user_name>` jest nazwą logowania Windows maszyny Wirtualnej. Będziemy nazywać foldery przykładowe jako **przykładowe skrypty** i **Notesy programu IPython przykładowe**.
   > 
   > 

Na podstawie rozmiaru zestawu danych, lokalizacja źródła danych i środowiska wybranego obiektu docelowego platformy Azure, ten scenariusz jest podobny do [scenariusza \#5: duży zestaw danych w lokalnych plikach docelowych programu SQL Server na maszynie Wirtualnej platformy Azure](plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>Pobierz dane ze źródła publiczne
Aby uzyskać [rund taksówek NYC](https://www.andresmh.com/nyctaxitrips/) zestawu danych w lokalizacji publicznej, możesz użyć dowolnej z metod opisanych w [przenoszenie danych do i z usługi Azure Blob Storage](move-azure-blob.md) Aby skopiować dane do nowej maszyny wirtualnej.

Aby skopiować dane za pomocą narzędzia AzCopy:

1. Zaloguj się do maszyny wirtualnej (VM)
2. Utwórz nowy katalog w dysku danych maszyny Wirtualnej (Uwaga: nie należy używać dysku tymczasowego, który jest dostarczany z maszyny Wirtualnej jako dysk danych).
3. W oknie wiersza polecenia Uruchom następujący wiersz polecenia narzędzia Azcopy, zastępując < path_to_data_folder > folderu danych utworzonych w (2):
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    Po zakończeniu działania narzędzia AzCopy, łącznie z 24 spakowane pliki CSV (12 komunikacji dwustronnej\_danych i 12 komunikacji dwustronnej\_taryfy) powinien znajdować się w folderze danych.
4. Rozpakuj pobranych plików. Zwróć uwagę na folder, w którym są przechowywane pliki bez kompresji. Ten folder zostanie wcześniej określano < ścieżka\_do\_danych\_pliki\>.

## <a name="dbload"></a>Zbiorcze importowanie danych do bazy danych serwera SQL
Można zwiększyć wydajność ładowania/przesyłania dużych ilości danych do bazy danych SQL i kolejne zapytania przy użyciu *partycjonowane tabele i widoki*. W tej sekcji będziemy działać zgodnie z instrukcji przedstawionych w temacie [równoległe zbiorcze danych importowania przy użyciu tabeli partycji SQL](parallel-load-sql-partitioned-tables.md) Utwórz nową bazę danych i ładować dane do partycjonowane tabele równolegle.

1. Po zalogowaniu się do maszyny Wirtualnej, należy uruchomić **SQL Server Management Studio**.
2. Połącz przy użyciu uwierzytelniania Windows.
   
    ![Łączenie programu SSMS][12]
3. Jeśli jeszcze nie masz zmienić tryb uwierzytelniania programu SQL Server i utworzenie nowego użytkownika logowania SQL, otwórz plik skryptu o nazwie **zmienić\_auth.sql** w **przykładowe skrypty** folderu. Zmienić domyślną nazwę użytkownika i hasło. Kliknij przycisk **! Wykonaj** na pasku narzędzi, aby uruchomić skrypt.
   
    ![Uruchom skrypt][13]
4. Sprawdź i/lub zmienić programu SQL Server domyślne bazy danych i dziennika foldery, aby upewnić się, które nowo utworzonych baz danych będą przechowywane na dysku z danymi. Obraz maszyny Wirtualnej programu SQL Server, który jest zoptymalizowany pod kątem obciążeń magazynowania danych jest wstępnie skonfigurowany z użyciem dysków danych i dziennika. Jeśli maszyna wirtualna nie zawiera dysk z danymi i dodać nowe wirtualnych dysków twardych podczas procesu instalacji maszyny Wirtualnej, zmień folderów domyślnych w następujący sposób:
   
   * Kliknij prawym przyciskiem myszy nazwę programu SQL Server w taki sposób, w lewym panelu, a następnie kliknij przycisk **właściwości**.
     
       ![Właściwości serwera SQL][14]
   * Wybierz **ustawienia bazy danych** z **wybierz stronę** liście po lewej stronie.
   * Sprawdź i/lub zmienić **bazy danych domyślne lokalizacje** do **dysk z danymi** lokalizacje wybranych przez użytkownika. Jest to, gdzie nowe bazy danych znajdują się jeśli utworzony przy użyciu ustawień domyślnych w lokalizacji.
     
       ![Wartości domyślne bazy danych SQL][15]  
5. Aby utworzyć nową bazę danych i zestaw grup plików do przechowywania podzielonych tabel, Otwórz przykładowy skrypt **tworzenie\_db\_default.sql**. Skrypt utworzy nową bazę danych o nazwie **TaxiNYC** i 12 grup plików w domyślnej lokalizacji danych. Każda grupa plików będzie przechowywać jednego miesiąca podróży\_danych i podróży\_taryfy danych. W razie potrzeby, zmodyfikować nazwę bazy danych. Kliknij przycisk **! Wykonaj** do uruchomienia skryptu.
6. Następnie należy utworzyć dwie tabele partycji, jeden dla wyzwolenie\_danych i inny wpis dla wyzwolenie\_klasie. Otwórz przykładowy skrypt **tworzenie\_partycjonowane\_table.sql**, który będzie:
   
   * Utwórz funkcję partycji, aby podzielić dane według miesiąca.
   * Utwórz schemat partycji do mapowania danych każdego miesiąca do innej grupy plików.
   * Utwórz dwie tabele partycjonowane mapowane na schemat partycji: **nyctaxi\_podróży** będzie przechowywać wyzwolenie\_danych i **nyctaxi\_taryfy** będzie przechowywać podróży\_taryfy danych.
     
     Kliknij przycisk **! Wykonaj** Aby uruchomić skrypt i utworzyć tabele partycjonowane.
7. W **przykładowe skrypty** folderu, istnieją dwa przykładowe skrypty programu PowerShell demonstrujących równoległy zbiorczy importuje danych do tabel programu SQL Server.
   
   * **Narzędzie BCP\_równoległe\_generic.ps1** jest skrypt rodzajowy równoległy zbiorczy import danych do tabeli. Zmodyfikuj ten skrypt, aby ustawić zmienne wejściowe i docelowy, wskazane wiersze komentarzy w skrypcie.
   * **Narzędzie BCP\_równoległe\_nyctaxi.ps1** jest wstępnie skonfigurowana wersja skrypt rodzajowy i może służyć do obu tabel danych podróży taksówek NYC obciążenia.  
8. Kliknij prawym przyciskiem myszy **bcp\_równoległe\_nyctaxi.ps1** nazwę skryptu wraz z kliknij **Edytuj** aby go otworzyć w programie PowerShell. Przejrzyj wstępnie zdefiniowane zmienne i modyfikować zgodnie z wybranej nazwie bazy danych, folderu danych wejściowych, folder dziennika docelowego i ścieżki do plików format przykładowe **nyctaxi_trip.xml** i **nyctaxi\_fare.xml** (podany w **przykładowe skrypty** folder).
   
    ![Zbiorcze importowanie danych][16]
   
    Można również wybrać tryb uwierzytelniania, domyślny jest uwierzytelnianie Windows. Kliknij zieloną strzałkę na pasku narzędzi, aby uruchomić. Skrypt zostanie uruchomiony 24 operacji importu zbiorczego w 12 równolegle, dla każdej tabeli partycjonowanej. Możesz monitorować postęp importowania danych, otwierając folder danych domyślnego programu SQL Server według stawki ustalonej powyżej.
9. Skrypt programu PowerShell raporty godziny rozpoczęcia i zakończenia. Gdy wszystkie masowe pełny import, godzina zakończenia jest zgłaszany. Sprawdź folder docelowy dziennika, aby sprawdzić, czy operacji importu zbiorczego zakończyły się pomyślnie, czyli żadne błędy zgłoszone w docelowym folderze dziennika.
10. Baza danych jest teraz gotowy do eksploracji, technicznego opracowywania funkcji i innych operacji, zgodnie z potrzebami. Ponieważ tabele są partycjonowane na podstawie położenia **odbioru\_daty/godziny** pola zapytania, które obejmują **odbioru\_daty/godziny** postanowień w **gdzie** klauzuli będą mogli korzystać z schemat partycji.
11. W **SQL Server Management Studio**, zapoznaj się z podanego przykładowy skrypt **przykładowe\_queries.sql**. Aby uruchomić dowolne przykładowe zapytania, wyróżnianie wierszy zapytania a następnie kliknij przycisk **! Wykonaj** na pasku narzędzi.
12. Dane podróży taksówek NYC są ładowane w dwóch oddzielnych tabelach. Aby poprawić operacji łączenia, zaleca indeksu w tabelach. Przykładowy skrypt **tworzenie\_partycjonowane\_index.sql** tworzy indeksy podzielone na partycje na klucza złożonego łączenia **Medalionu hakowanie\_licencji i odbiór\_ Data i godzina**.

## <a name="dbexplore"></a>Eksplorowanie danych i inżynieria funkcji w programie SQL Server
W tej sekcji zostaną wykonane Generowanie funkcji i eksploracji danych, uruchamiając zapytania SQL bezpośrednio w **SQL Server Management Studio** przy użyciu bazy danych programu SQL Server utworzonej wcześniej. Przykładowy skrypt o nazwie **przykładowe\_queries.sql** znajduje się w **przykładowe skrypty** folderu. Zmodyfikuj skrypt, aby zmienić nazwę bazy danych, jeśli jest inny niż domyślny: **TaxiNYC**.

W tym ćwiczeniu obejmuje następujące czynności:

* Połączyć się z **SQL Server Management Studio** przy użyciu uwierzytelniania za Windows lub uwierzytelniania SQL i nazwa logowania SQL i hasło.
* Zapoznaj się z dystrybucji danych kilka pól w różnych okna czasowe.
* Zbadaj dobrej jakości danych pola długości i szerokości geograficznej.
* Generowanie etykiety binarne i wieloklasowej klasyfikacji na podstawie **Porada\_kwota**.
* Generowanie funkcji i obliczeń/compare odległości podróży.
* Dołącz do dwóch tabel i Wyodrębnij losowej próbki, która będzie służyć do tworzenia modeli.

Gdy wszystko jest gotowe do przejścia do usługi Azure Machine Learning, użytkownik może:  

1. Zapisz ostateczną kwerendę SQL w celu wyodrębnienia i próbkowania danych i skopiowania zapytania bezpośrednio do modułu [importowania danych][import-data] w Azure Machine Learning lub
2. Utrwalaj próbkowane i opracowane dane, które planujesz użyć do kompilowania modelu w nowej tabeli bazy danych, i użyj nowej tabeli w module [Importuj dane][import-data] w Azure Machine Learning.

W tej sekcji oszczędzamy ostatecznemu zapytaniu, aby wyodrębnić i przykładowe dane. Druga metoda jest przedstawiona w [eksplorację danych inżynieryjnych i związanych z funkcji w IPython Notebook](#ipnb) sekcji.

Szybkie weryfikacji liczby wierszy i kolumn w tabelach wypełniać wcześniej przy użyciu równoległy zbiorczy import,

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Eksploracja: Podróży rozkład według Medalionu
W tym przykładzie identyfikuje Medalionu (numery taksówek) z więcej niż 100 podróży w danym okresie czasu. Zapytania będą korzystać z dostępu do tabeli partycjonowanej, ponieważ w schemacie partycji należy przygotować **odbioru\_daty/godziny**. Wykonywanie zapytań pełnego zestawu danych spowoduje również, że użycie tabeli partycjonowanej i/lub indeksu skanowania.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Eksploracja: Podróży rozkład według Medalionu i hack_license
    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Ocena jakości danych: Sprawdź rekordy o długości geograficznej niepoprawne i/lub szerokości geograficznej
W tym przykładzie bada, jeśli żadnego pola geograficzne i/lub szerokość geograficzną albo zawiera nieprawidłową wartość (stopnie radianach powinna być od-90 do 90), lub (0, 0) współrzędnych.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Eksploracja: rozchylony a przechylony
W tym przykładzie wyszukuje Liczba podróży, które zostały Przechylony a nie Przechylony w danym momencie okresu (lub pełnego zestawu danych, jeśli obejmujące pełny rok). Tej dystrybucji odzwierciedla dystrybucji binarne etykiety później służący do modelowania klasyfikacji binarnej.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Eksploracja: Porada dystrybucji klasy/zakresu
W tym przykładzie oblicza rozkład Porada zakresów w danym okresie czasu (lub pełnego zestawu danych, jeśli obejmujące pełny rok). To jest podział klasy etykiety, które będą potrzebne później do modelowania klasyfikacji wieloklasowej.

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

#### <a name="exploration-compute-and-compare-trip-distance"></a>Eksploracji: Obliczeń i porównywanie odległość podróży
Ten przykład konwertuje odbiór i dropoff długości geograficznej i szerokości geograficznej do lokalizacji geograficznej SQL punktów oblicza odległość podróży za pomocą różnicę punktów lokalizacji geograficznej SQL i zwraca losowej próbki wyniki porównania. Przykład ogranicza wyniki do prawidłowe współrzędne wyłącznie przy użyciu zapytania oceny jakości danych omówione wcześniej.

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

#### <a name="feature-engineering-in-sql-queries"></a>Inżynieria funkcji w zapytania SQL
Etykieta generowania i położenia geograficznego konwersji eksploracji zapytania również może służyć do generowania etykiet/funkcji, usuwając część obliczeń. Dodatkową cechą inżynierów przykłady programu SQL znajdują się w [eksplorację danych inżynieryjnych i związanych z funkcji w IPython Notebook](#ipnb) sekcji. Jest bardziej wydajne działanie funkcji generowania zapytania na pełny zestaw danych lub dużego podzbioru go za pomocą zapytań SQL, których uruchamianie bezpośrednio w wystąpieniu bazy danych programu SQL Server. Zapytania mogą być wykonywane w **SQL Server Management Studio**, IPython Notebook lub dowolnego narzędzia/środowiska deweloperskiego, które mogą dostęp do bazy danych, lokalnie lub zdalnie.

#### <a name="preparing-data-for-model-building"></a>Przygotowywanie danych do konstruowania modelu
Poniższe zapytanie sprzęga **nyctaxi\_podróży** i **nyctaxi\_taryfy** tabele, generuje etykietę klasyfikacji binarnej **Przechylony**, Etykieta klasyfikacji wieloklasowej **Porada\_klasy**i wyodrębnia 1% losowej próbki z pełnego dołączonym do zestawu danych. To zapytanie można skopiować, a następnie wkleić bezpośrednio do modułu [Azure Machine Learning Studio](https://studio.azureml.net) [Importowanie danych][import-data] w celu bezpośredniego pozyskiwania danych z wystąpienia bazy danych SQL Server na platformie Azure. Zapytanie wyklucza rekordy z niepoprawny (0, 0) współrzędnych.

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


## <a name="ipnb"></a>Eksplorowanie danych i inżynieria funkcji w IPython Notebook
W tej sekcji firma Microsoft będzie wykonywać eksploracji danych i generowanie funkcji za pomocą środowiska Python, jak i SQL zapytań względem bazy danych programu SQL Server, utworzony wcześniej. Program IPython notebook próbki o nazwie **machine-Learning-data-science-process-sql-story.ipynb** znajduje się w **Notesy programu IPython przykładowe** folderu. Ten notes jest dostępna również na [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

Zalecana kolejność podczas pracy z danymi big data jest następująca:

* Przeczytaj w niewielką próbkę danych do ramki danych w pamięci.
* Wykonaj niektóre wizualizacje i eksploracji przy użyciu próbki danych.
* Poeksperymentuj z technicznego opracowywania funkcji przy użyciu próbki danych.
* Dla większych eksplorację danych, manipulowania danymi i technicznego opracowywania funkcji wysyłać zapytania SQL bezpośrednio w odniesieniu do bazy danych programu SQL Server w maszynie Wirtualnej platformy Azure przy użyciu języka Python.
* Zdecyduj, czy rozmiar próbki na potrzeby tworzenia modelu usługi Azure Machine Learning

Gdy wszystko będzie gotowe do przejścia do usługi Azure Machine Learning, użytkownik może:  

1. Zapisz ostateczną kwerendę SQL w celu wyodrębnienia i próbkowania danych i skopiowania zapytania bezpośrednio do modułu [importowania danych][import-data] w Azure Machine Learning. Ta metoda jest przedstawiona w [tworzenie modeli w usłudze Azure Machine Learning](#mlmodel) sekcji.    
2. Utrwalaj próbkowane i zaprojektowane dane, które planujesz użyć do kompilowania modelu w nowej tabeli bazy danych, a następnie użyj nowej tabeli w module [Importuj dane][import-data] .

Poniżej przedstawiono kilka eksplorację danych, Wizualizacja danych i funkcji inżynierii przykłady. Aby uzyskać więcej przykładów, zobacz przykładowy notes SQL IPython w **Notesy programu IPython przykładowe** folderu.

#### <a name="initialize-database-credentials"></a>Inicjowanie poświadczenia bazy danych
Inicjuj ustawienia połączenia bazy danych w następujących zmiennych:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Utwórz połączenie z bazą danych
    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Raport liczba rzędów i kolumn w tabeli nyctaxi_trip
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

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Przykład małej dane z bazy danych programu SQL Server w odczytu
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

Czas odczytu z przykładowej tabeli jest 6.492000 sekund  
Liczba wierszy i kolumn pobrać = (84952, 21)

#### <a name="descriptive-statistics"></a>Statystyki opisowe
Teraz przystąpić do eksplorowania próbki danych. Rozpoczniemy pracę spojrzenie na opisową statystyki **podróży\_odległość** (lub innych) użytkowniku:

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Wizualizacji: Przykład wykres pola
Następnie przyjrzymy się skrzynkowy odległość podróży do wizualizacji quantiles

    df1.boxplot(column='trip_distance',return_type='dict')

![Wykreślania #1][1]

#### <a name="visualization-distribution-plot-example"></a>Wizualizacji: Przykładowy diagram dystrybucji
    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Wykreślania #2][2]

#### <a name="visualization-bar-and-line-plots"></a>Wizualizacji: Pasek i powierzchnie wiersza
W tym przykładzie firma Microsoft bin odległość podróży do pięciu pojemników, a wizualizacja wyników pakowania.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Możemy wykresu powyżej dystrybucji bin na pasku lub wykres zgodnie z poniższymi instrukcjami wiersza

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Wykreślania #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Wykreślania #4][4]

#### <a name="visualization-scatterplot-example"></a>Wizualizacji: Przykładowy wykres punktowy
Pokazujemy, wykres punktowy między **podróży\_czasu\_w\_s** i **podróży\_odległość** można sprawdzić, czy jest wszelka korelacja

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Wykreślania #6][6]

Podobnie można sprawdzić relacje między **współczynnik\_kodu** i **podróży\_odległość**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![#8 wykreślania][8]

### <a name="sub-sampling-the-data-in-sql"></a>Podrzędne próbkowanie danych w SQL
Podczas przygotowywania danych do kompilowania modeli w [Azure Machine Learning Studio](https://studio.azureml.net)można zdecydować, czy **zapytanie SQL ma być używane bezpośrednio w module Importuj dane** , czy utrwalać przetworzone i próbkowane dane w nowej tabeli, której można użyć podczas importowania. [ Moduł danych][import-data] z prostą opcją **SELECT * FROM <\_nową tabelę\_\_nazwa >** .

W tej sekcji utworzymy nową tabelę do przechowywania danych próbkowanych i odtworzone. Przykładem bezpośrednie zapytanie SQL do tworzenia modelu znajduje się w [eksplorację danych inżynieryjnych i związanych z funkcji w programie SQL Server](#dbexplore) sekcji.

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Tworzenie przykładowej tabeli i wypełnić połączonych tabel: % 1. Upuść pierwszej tabeli, jeśli taki istnieje.
W tej sekcji, firma Microsoft sprzężenia **nyctaxi\_podróży** i **nyctaxi\_taryfy**, Wyodrębnij losowej próbki 1% i zachować próbki danych w nowej nazwy tabeli  **nyctaxi\_jeden\_procent**:

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

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Eksplorowanie danych za pomocą zapytań SQL w IPython Notebook
W tej sekcji omówimy dystrybucji danych przy użyciu danych % 1, próbkowania, który jest utrwalony w nowej tabeli, którą utworzyliśmy powyżej. Należy pamiętać, że podobne eksploracji mogą być wykonywane przy użyciu oryginalnego tabel, opcjonalnie używając **TABLESAMPLE** ograniczyć eksploracji przykładowe lub ograniczając wyniki w danym momencie okresu przy użyciu **odbioru\_daty/godziny** dzieli na partycje, jak pokazano w [eksplorację danych inżynieryjnych i związanych z funkcji w programie SQL Server](#dbexplore) sekcji.

#### <a name="exploration-daily-distribution-of-trips"></a>Eksploracji: Codzienne rozkład rund
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

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Generowanie funkcji, za pomocą zapytań SQL w IPython Notebook
W tej sekcji wygenerujemy nowe etykiety i funkcje bezpośrednio przy użyciu zapytań SQL, wykonywanie operacji na tabeli 1%, przykładowe utworzonego w poprzedniej sekcji.

#### <a name="label-generation-generate-class-labels"></a>Generowanie etykiety: Generuj klasy etykiety
W poniższym przykładzie możemy wygenerować dwa zestawy etykiety na potrzeby modelowania:

1. Binarny etykiety klasy **Przechylony** (przewidywania, jeśli będzie miał Porada)
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

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Technicznego opracowywania funkcji: Funkcje Count podzielone na kategorie kolumny
W tym przykładzie przekształca pole kategorii w pole liczbowe, zastępując każdej kategorii, liczba jego wystąpień, w danych.

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

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Technicznego opracowywania funkcji: Funkcje Bin kolumny liczbowe
W tym przykładzie przekształca ciągłego pola liczbowego w zakresach wstępnie zdefiniowanych kategorii, czyli przekształcania pole liczbowe na pole kategorii.

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

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Inżynieria funkcji: Prowadzenie dziesiętna szerokości/długości geograficznej lokalizacji funkcji
Ten przykład dzieli dziesiętną reprezentację pola szerokości geograficznej i/lub długości geograficznej na kilka regionów pól o różnych stopnia szczegółowości, takich jak kraj/region, miasto, miejscowość, blok itp. Należy pamiętać, że nowe pola geograficzne nie są zamapowane na rzeczywiste lokalizacje. Informacje na temat mapowania geocode lokalizacji można zobaczyć [usług REST mapy usługi Bing](https://msdn.microsoft.com/library/ff701710.aspx).

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

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Sprawdź ostatecznej postaci tabeli neural
    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Teraz jesteśmy gotowi przejść do konstruowania modelu i wdrażania modelu w [usługi Azure Machine Learning](https://studio.azureml.net). Dane są gotowe jakichkolwiek problemów prognozowania wymienionych wcześniej, to znaczy:

1. Klasyfikacja binarna: przewidywanie czy Porada zapłacono komunikacji dwustronnej.
2. Klasyfikacji wieloklasowej: przewidywanie zakres Porada płatnej zgodnie z wcześniej zdefiniowanych klas.
3. Zadanie regresji: przewidywanie ilość Porada płatnych komunikacji dwustronnej.  

## <a name="mlmodel"></a>Tworzenie modeli w usłudze Azure Machine Learning
Aby rozpocząć wykonywania modelowania, zaloguj się do swojego obszaru roboczego usługi Azure Machine Learning. Jeśli jeszcze nie utworzono obszaru roboczego uczenia maszynowego, zobacz [Tworzenie obszaru roboczego usługi Azure Machine Learning](../studio/create-workspace.md).

1. Aby rozpocząć pracę z usługą Azure Machine Learning, zobacz [co to jest Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Zaloguj się do [usługi Azure Machine Learning Studio](https://studio.azureml.net).
3. Strona główna Studio udostępnia mnóstwo informacji, pliki wideo, samouczki, łącza do dokumentacji dotyczącej modułów i innych zasobów. Aby uzyskać więcej informacji na temat usługi Azure Machine Learning, zapoznaj się z [Centrum dokumentacji usługi Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Typowe eksperyment składa się z następujących czynności:

1. Tworzenie **+ nowy** eksperymentować.
2. Pobieranie danych do usługi Azure Machine Learning.
3. Wstępne przetwarzanie, przekształcania i manipulowania danymi zgodnie z potrzebami.
4. Generowanie funkcji zgodnie z potrzebami.
5. Podzielić dane na szkolenie i sprawdzania poprawności/testowania zestawów danych (lub rozdzielanie zestawów danych dla każdego).
6. Wybierz co najmniej jeden algorytmów uczenia maszynowego w zależności od problemu nauki do rozwiązania. Np. klasyfikacji binarnej, wieloklasowej klasyfikacji, regresji.
7. Szkolenie jednego lub więcej modeli przy użyciu zestawu danych szkoleniowych.
8. Wynik sprawdzania poprawności zestawu danych za pomocą wytrenowane modele.
9. Ocena modeli można obliczyć metryki istotne dla nauczanym problemem.
10. Prawidłowo dostrojenie modele i wybrać najlepszy model wdrażania.

W tym ćwiczeniu firma ma już przedstawione odtwarzane danych w programie SQL Server i ustalony rozmiar próbki do pozyskania w usłudze Azure Machine Learning. Do tworzenia co najmniej jeden modele predykcyjne podjęliśmy decyzję o:

1. Pobierz dane do Azure Machine Learning przy użyciu modułu [Importuj dane][import-data] dostępne w sekcji **dane wejściowe i wyjściowe** . Aby uzyskać więcej informacji, zobacz stronę odwołania modułu [importowania danych][import-data] .
   
    ![Usługi Azure Machine Learning importu danych][17]
2. Wybierz **usługi Azure SQL Database** jako **źródła danych** w **właściwości** panelu.
3. Wprowadź nazwę DNS bazy danych w **nazwy serwera bazy danych** pola. Format: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Wprowadź **Nazwa bazy danych** w odpowiednich pól.
5. Wprowadź **nazwa użytkownika SQL** w **nazwę konta użytkownika serwera**i **hasło** w **hasło konta użytkownika serwera**.
7. W **zapytanie bazy danych** edytowania obszaru tekstu, Wklej zapytanie, który wyodrębnia pola niezbędne bazy danych (w tym wszelkie pola obliczane, takie jak etykiety) i w dół próbkuje dane, do rozmiaru próbki żądaną.

Przykład eksperymentu klasyfikacji binarnej odczytywania danych bezpośrednio z bazy danych programu SQL Server to na poniższej ilustracji. Podobne eksperymenty można konstruować wieloklasowej klasyfikacji i regresji problemów.

![Usługi Azure Machine Learning szkolenie][10]

> [!IMPORTANT]
> W danych modelowania wyodrębniania i próbkowanie przykłady zapytań dostarczane w poprzednich sekcjach **wszystkie etykiety dla trzech ćwiczenia modelowania są uwzględnione w zapytaniu**. Ważnym krokiem (wymagane), w każdym z ćwiczeniami modelowania jest **wykluczyć** niepotrzebne etykiety dla innych problemów oraz wszelkie inne **docelowe przecieki**. Np. Korzystając z klasyfikacji binarnej, użytku etykiety **Przechylony** i wykluczyć pola **Porada\_klasy**, **Porada\_kwota**i **całkowita\_kwota**. Są one przecieki docelowy od momentu oznaczają porady płatne.
> 
> Aby wykluczyć niepotrzebne kolumny i/lub wycieki docelowe, możesz użyć modułu [SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych][select-columns] ) lub [edytować metadane][edit-metadata]. Aby uzyskać więcej informacji, zobacz [Wybieranie kolumn w zestawie danych][select-columns] i edytowanie stron odwołań do [metadanych][edit-metadata] .
> 
> 

## <a name="mldeploy"></a>Wdrażanie modeli w usłudze Azure Machine Learning
Gdy model jest gotowy, można łatwo wdrażać je jako usługi sieci web bezpośrednio z poziomu eksperymentu. Aby uzyskać więcej informacji na temat wdrażania usługi sieci web Azure Machine Learning, zobacz [wdrażanie usługi sieci web Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md).

Aby wdrożyć nową usługę sieci web, należy:

1. Tworzenie eksperymentu oceniania.
2. Wdrażanie usługi sieci web.

Utworzyć eksperyment oceniania z **Zakończono** szkolenia eksperymentu, kliknij przycisk **tworzenie oceny EKSPERYMENTÓW** na dolnym pasku akcji.

![Ocenianie przez usługę Azure][18]

Usługa Azure Machine Learning spróbuje utworzyć eksperyment oceniania na podstawie składników eksperymentu szkolenia. W szczególności będą wykonywane następujące czynności:

1. Zapisania trenowanego modelu, a następnie usuń moduły szkolenie modelu.
2. Zidentyfikuj wartość logiczna **portem wejściowym modułu** do reprezentowania schematu oczekiwanych danych wejściowych.
3. Zidentyfikuj wartość logiczna **danych wyjściowych portu** reprezentujący schemat danych wyjściowych usługi oczekiwanego sieci web.

Podczas tworzenia eksperymentu oceniania jego przejrzenie i dostosować zgodnie z potrzebami. Typowe dopasowania jest Zamień wejściowy zestaw danych i/lub zapytanie taki, który wyklucza etykietę pola, jak te nie będą dostępne podczas wywoływania usługi. Jest również dobrym rozwiązaniem, aby zmniejszyć rozmiar wejściowy zestaw danych i/lub kwerendy na kilka rekordów wystarczający wskazać schemat danych wejściowych. Dla portu wyjściowego często wyklucza wszystkie pola wejściowe i zawiera tylko **etykiety z wynikami** i **oceny prawdopodobieństwa** w danych wyjściowych za pomocą modułu [SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych][select-columns] ).

Przykład oceniania eksperymentu jest na poniższej ilustracji. Gdy wszystko będzie gotowe do wdrożenia, kliknij przycisk **OPUBLIKOWAĆ usługi sieci WEB** przycisk na dolnym pasku akcji.

![Publikowanie w usłudze Azure Machine Learning][11]

Podsumowanie, w tym samouczku instruktażu utworzono środowisko do nauki o danych platformy Azure z dużego zestawu danych publicznych od pozyskiwanie danych do modelu uczenia i wdrażania usługi sieci web Azure Machine Learning.

### <a name="license-information"></a>Informacje o licencji
Ten przewodnik po przykładzie i towarzyszące jej IPython notebook(s) i skrypty są udostępniane przez firmę Microsoft na licencji MIT. Sprawdź, czy w pliku LICENSE.txt w katalogu przykładowego kodu w serwisie GitHub Aby uzyskać więcej informacji.

### <a name="references"></a>Dokumentacja
• [Andrés Monroy taksówek NYC przesłania strony pobierania](https://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC taksówki danych podróży, Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [Taksówek NYC i Limousine Komisji badań i statystyk](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

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
