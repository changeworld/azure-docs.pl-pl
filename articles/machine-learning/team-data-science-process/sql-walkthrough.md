---
title: Tworzenie i wdrażanie modelu w maszynę Wirtualną SQL Server — zespołu danych dla celów naukowych
description: Tworzenie i wdrażanie przy użyciu programu SQL Server na Maszynie wirtualnej platformy Azure przy użyciu publicznie dostępnego zestawu danych model uczenia maszynowego.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718535"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>Zespół danych dla celów naukowych w działaniu: przy użyciu programu SQL Server
W tym samouczku przedstawiono proces kompilowania i wdrażania modelu uczenia maszynowego przy użyciu SQL Server i publicznie dostępnego zestawu danych — zestawu danych [podróży NYC z taksówkami](https://www.andresmh.com/nyctaxitrips/) . Procedura następuje pracy do analizy danych w warstwie standardowa: pozyskiwanie i eksplorować dane, Projektuj funkcje ułatwić szkolenia, a następnie utworzyć i wdrożyć model.

## <a name="dataset"></a>Opis zestawu danych NYC taksówki
Dane dotyczące rejsu z NYC taksówkami dotyczą 20 GB skompresowanych plików CSV (~ 48 GB nieskompresowanych), składających się z ponad 173 000 000 pojedynczych podróży i opłat za każdą podróż. Każdy rekord podróży obejmuje odbiór i dropoff lokalizacji i czasu, hack anonimowe (sterownika) numer licencji i numer Medalionu (unikatowy identyfikator dla taksówek). Dane obejmuje wszystkie podróży w roku 2013 i znajduje się w następujących dwóch zestawów danych w każdym miesiącu:

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

Unikatowy klucz do przyłączenia do podróży\_Data i podróż\_opłaty są złożone z pól: Medallion, hakerzy\_licencję i pobranie\_DateTime.

## <a name="mltasks"></a>Przykłady zadań przewidywania
Będziemy formułować trzy problemy z przewidywaniami w oparciu o *kwotę\_TIP*, czyli:

* Klasyfikacja binarna: przewidywanie, czy Porada została zapłacona za podróż, czyli *\_kwota Porada* , która jest większa niż $0, jest dodatnim przykładem, a *Porada\_kwota* $0 jest nieujemnym przykładem.
* Klasyfikacji wieloklasowej: przewidywanie zakres Porada opłacony wyzwolenie. Podzielmy *poradę\_ą* na pięć przedziałów lub klas:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
* Zadanie regresji: przewidywanie ilość Porada płatnych komunikacji dwustronnej.  

## <a name="setup"></a>Konfigurowanie środowiska nauki o danych Azure na potrzeby zaawansowanej analizy
Jak widać w przewodniku [planowania środowiska](plan-your-environment.md) , istnieje kilka opcji współpracy z zestawem danych podróży NYC taksówkami na platformie Azure:

* Praca z danych w obiektach blob platformy Azure, a następnie modelu w usłudze Azure Machine Learning
* Załaduj dane do bazy danych programu SQL Server, a następnie modelu w usłudze Azure Machine Learning

W tym samouczku przedstawimy równoległy import zbiorczy danych do SQL Server, eksploracji danych, inżynierii funkcji i próbkowania w dół przy użyciu SQL Server Management Studio, a także IPython notesu. [Przykładowe skrypty](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) i [notesy IPython](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) są udostępniane w serwisie GitHub. Przykładowe IPython notebook do pracy z danymi w obiektach blob platformy Azure jest również dostępna w tej samej lokalizacji.

Aby skonfigurować środowisko nauki o danych platformy Azure:

1. [Tworzenie konta magazynu](../../storage/common/storage-account-create.md)
2. [Tworzenie obszaru roboczego Azure Machine Learning](../studio/create-workspace.md)
3. [Zapewnij Data Science Virtual Machine](../data-science-virtual-machine/setup-sql-server-virtual-machine.md), który udostępnia SQL Server i serwer notesu IPython.
   
   > [!NOTE]
   > Przykładowe skrypty i Notesy programu IPython zostaną pobrane na maszynę wirtualną do nauki o danych, podczas procesu instalacji. Po zakończeniu działania skryptu poinstalacyjnego maszyny Wirtualnej, przykłady będą w bibliotece dokumentów maszyny Wirtualnej:  
   > 
   > * Przykładowe skrypty: `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Przykładowe notesy IPython: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   gdzie `<user_name>` jest nazwą logowania systemu Windows maszyny wirtualnej. Będziemy odwoływać się do przykładowych folderów jako **przykładowe skrypty** i **przykładowe notesy IPython**.
   > 
   > 

W oparciu o rozmiar zestawu danych, lokalizację źródła danych i wybrane środowisko docelowe platformy Azure, ten scenariusz jest podobny do [scenariusza \#5: duży zestaw danych w plikach lokalnych, docelowy SQL Server na maszynie wirtualnej platformy Azure](plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>Pobierz dane ze źródła publicznego
Aby pobrać zestaw danych [podróży z NYC taksówkami](https://www.andresmh.com/nyctaxitrips/) z lokalizacji publicznej, możesz użyć dowolnej z metod opisanych w temacie [przenoszenie danych do i z platformy Azure Blob Storage](move-azure-blob.md) , aby skopiować dane na nową maszynę wirtualną.

Aby skopiować dane za pomocą narzędzia AzCopy:

1. Zaloguj się do maszyny wirtualnej (VM)
2. Utwórz nowy katalog na dysku danych maszyny wirtualnej (Uwaga: nie używaj dysku tymczasowego, który jest dostarczany z maszyną wirtualną jako dysk danych).
3. W oknie wiersza polecenia Uruchom następujący wiersz polecenia narzędzia Azcopy, zastępując < path_to_data_folder > folderu danych utworzonych w (2):
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    Po zakończeniu AzCopy, łącznie 24 pliki CSV (12 dla podróży\_danych i 12 dla podróży\_opłaty) powinny znajdować się w folderze danych.
4. Rozpakuj pobranych plików. Zwróć uwagę na folder, w którym są przechowywane pliki bez kompresji. Ten folder będzie określany jako ścieżka <\_do\_\_plików danych\>.

## <a name="dbload"></a>Zbiorcze importowanie danych do bazy danych SQL Server
Wydajność ładowania/przenoszenia dużych ilości danych do SQL Database i kolejnych zapytań można ulepszyć za pomocą *partycjonowanych tabel i widoków*. W tej sekcji będziemy postępować zgodnie z instrukcjami opisanymi w artykule [równoległe Importowanie danych zbiorczych przy użyciu tabel partycji SQL](parallel-load-sql-partitioned-tables.md) w celu utworzenia nowej bazy danych i załadowania danych do tabel partycjonowanych równolegle.

1. Po zalogowaniu się do maszyny wirtualnej Rozpocznij **SQL Server Management Studio**.
2. Połącz przy użyciu uwierzytelniania Windows.
   
    ![Łączenie programu SSMS][12]
3. Jeśli nie zmieniono jeszcze trybu uwierzytelniania SQL Server i utworzono nowego użytkownika logowania SQL, Otwórz plik skryptu o nazwie **change\_auth. SQL** w folderze **przykładowe skrypty** . Zmienić domyślną nazwę użytkownika i hasło. Kliknij przycisk **Execute (wykonaj** ) na pasku narzędzi, aby uruchomić skrypt.
   
    ![Uruchom skrypt][13]
4. Sprawdź i/lub zmienić programu SQL Server domyślne bazy danych i dziennika foldery, aby upewnić się, które nowo utworzonych baz danych będą przechowywane na dysku z danymi. Obraz maszyny wirtualnej SQL Server zoptymalizowany pod kątem ładowania magazynu danych jest wstępnie skonfigurowany przy użyciu dysków danych i dzienników. Jeśli maszyna wirtualna nie zawiera dysk z danymi i dodać nowe wirtualnych dysków twardych podczas procesu instalacji maszyny Wirtualnej, zmień folderów domyślnych w następujący sposób:
   
   * Kliknij prawym przyciskiem myszy nazwę SQL Server w lewym panelu, a następnie kliknij pozycję **Właściwości**.
     
       ![Właściwości serwera SQL][14]
   * Wybierz pozycję **Ustawienia bazy danych** z listy **Wybierz stronę** po lewej stronie.
   * Sprawdź i/lub Zmień **domyślne lokalizacje bazy danych** na wybrane lokalizacje **dysków danych** . W tej lokalizacji znajdują się nowe bazy danych, jeśli zostały utworzone z ustawieniami domyślnymi.
     
       ![Wartości domyślne bazy danych SQL][15]  
5. Aby utworzyć nową bazę danych i zestaw grup plików do przechowywania partycjonowanych tabel, Otwórz przykładowy skrypt **utwórz\_db\_default. SQL**. Skrypt utworzy nową bazę danych o nazwie **TaxiNYC** i 12 grupach plików w domyślnej lokalizacji danych. Każda grupa plików będzie przechowywać jeden miesiąc podróży\_danych i podróży\_dane dotyczące opłat. W razie potrzeby, zmodyfikować nazwę bazy danych. Kliknij przycisk **Execute (wykonaj** ), aby uruchomić skrypt.
6. Następnie Utwórz dwie tabele partycji, jeden dla podróży\_danych i drugi dla podróży\_opłaty. Otwórz przykładowy skrypt **utwórz\_partycjonowany\_Table. SQL.** spowoduje to:
   
   * Utwórz funkcję partycji, aby podzielić dane według miesiąca.
   * Utwórz schemat partycji do mapowania danych każdego miesiąca do innej grupy plików.
   * Utwórz dwie partycjonowane tabele zamapowane na schemat partycji: **nyctaxi\_podróż** będzie utrzymywać podróż\_Data i **nyctaxi\_opłaty za przejazd**\_ą.
     
     Kliknij przycisk **Execute (wykonaj** ), aby uruchomić skrypt i utworzyć partycjonowane tabele.
7. W folderze **przykładowe skrypty** dostępne są dwa przykładowe skrypty programu PowerShell umożliwiające zaprezentowanie równoległych importów zbiorczych danych do tabel SQL Server.
   
   * Narzędzie **bcp\_parallel\_Generic. ps1** to ogólny skrypt do równoległego importowania danych do tabeli. Zmodyfikuj ten skrypt, aby ustawić zmienne wejściowe i docelowy, wskazane wiersze komentarzy w skrypcie.
   * narzędzia **bcp\_parallel\_nyctaxi. ps1** to wstępnie skonfigurowana wersja skryptu ogólnego i może służyć do załadowania obu tabel dla danych podróży w NYC.  
8. Kliknij prawym przyciskiem myszy nazwę skryptu **bcp\_parallel\_nyctaxi. ps1** , a następnie kliknij przycisk **Edytuj** , aby otworzyć go w programie PowerShell. Przejrzyj wstępnie zdefiniowane zmienne i zmodyfikuj je zgodnie z wybraną nazwą bazy danych, folderem danych wejściowych, docelowym folderem dziennika i ścieżkami do plików w formacie przykładowym **nyctaxi_trip. XML** i **nyctaxi\_opłaty. XML** (dostępne w folderze **przykładowe skrypty** ).
   
    ![Zbiorcze importowanie danych][16]
   
    Można również wybrać tryb uwierzytelniania, domyślny jest uwierzytelnianie Windows. Kliknij zieloną strzałkę na pasku narzędzi, aby uruchomić. Skrypt zostanie uruchomiony 24 operacji importu zbiorczego w 12 równolegle, dla każdej tabeli partycjonowanej. Możesz monitorować postęp importowania danych, otwierając folder danych domyślnego programu SQL Server według stawki ustalonej powyżej.
9. Skrypt programu PowerShell raporty godziny rozpoczęcia i zakończenia. Gdy wszystkie masowe pełny import, godzina zakończenia jest zgłaszany. Sprawdź docelowy folder dziennika, aby sprawdzić, czy zbiorcze Importy zostały pomyślne, czyli nie zgłoszono błędów w docelowym folderze dziennika.
10. Baza danych jest teraz gotowy do eksploracji, technicznego opracowywania funkcji i innych operacji, zgodnie z potrzebami. Ponieważ tabele są partycjonowane według pola **\_Data/godzina** , zapytania, które zawierają\_warunki **DateTime** w klauzuli **WHERE** , będą korzystać z schematu partycji.
11. W **SQL Server Management Studio**poznanie dostarczonego przykładowego skryptu **\_zapytania. SQL**. Aby uruchomić dowolne z przykładowych zapytań, zaznacz wiersze zapytania, a następnie kliknij przycisk **Execute (wykonaj** ) na pasku narzędzi.
12. Dane podróży taksówek NYC są ładowane w dwóch oddzielnych tabelach. Aby poprawić operacji łączenia, zaleca indeksu w tabelach. Przykładowy skrypt **tworzy\_partycjonowany\_index. SQL** tworzy partycjonowane indeksy dla złożonego klucza sprzężenia **Medallion, hakerów\_licencję i pobrania\_DateTime**.

## <a name="dbexplore"></a>Eksploracja danych i inżynieria funkcji w SQL Server
W tej sekcji wykonamy eksplorowanie i generowanie funkcji, uruchamiając zapytania SQL bezpośrednio w **SQL Server Management Studio** przy użyciu utworzonej wcześniej bazy danych SQL Server. Przykładowy skrypt o nazwie **sample\_zapytania. SQL** jest dostępny w folderze **przykładowe skrypty** . Zmodyfikuj skrypt, aby zmienić nazwę bazy danych, jeśli różni się od domyślnego: **TaxiNYC**.

W tym ćwiczeniu obejmuje następujące czynności:

* Połącz się z **SQL Server Management Studio** przy użyciu uwierzytelniania systemu Windows lub uwierzytelniania SQL i nazwy logowania SQL i hasła.
* Zapoznaj się z dystrybucji danych kilka pól w różnych okna czasowe.
* Zbadaj dobrej jakości danych pola długości i szerokości geograficznej.
* Generuj etykiety klasyfikacji danych binarnych i wieloklasowych na podstawie **\_Porada**.
* Generowanie funkcji i obliczeń/compare odległości podróży.
* Dołącz do dwóch tabel i Wyodrębnij losowej próbki, która będzie służyć do tworzenia modeli.

Gdy wszystko jest gotowe do przejścia do usługi Azure Machine Learning, użytkownik może:  

1. Zapisz ostateczną kwerendę SQL w celu wyodrębnienia i próbkowania danych i skopiowania zapytania bezpośrednio do modułu [importowania danych][import-data] w Azure Machine Learning lub
2. Utrwalaj próbkowane i opracowane dane, które planujesz użyć do kompilowania modelu w nowej tabeli bazy danych, i użyj nowej tabeli w module [Importuj dane][import-data] w Azure Machine Learning.

W tej sekcji zapiszemy ostateczną kwerendę, aby wyodrębnić i próbkować dane. Druga metoda jest przedstawiona w sekcji " [Eksploracja danych i inżynieria funkcji" w notesie IPython](#ipnb) .

Szybkie weryfikacji liczby wierszy i kolumn w tabelach wypełniać wcześniej przy użyciu równoległy zbiorczy import,

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Eksploracja: Podróży rozkład według Medalionu
W tym przykładzie identyfikuje Medalionu (numery taksówek) z więcej niż 100 podróży w danym okresie czasu. Zapytanie może korzystać z dostępu do partycjonowanej tabeli, ponieważ jest ono warunkiem schematu partycji **pobrania\_DateTime**. Wykonywanie zapytań pełnego zestawu danych spowoduje również, że użycie tabeli partycjonowanej i/lub indeksu skanowania.

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
W tym przykładzie oblicza rozkład Porada zakresów w danym okresie czasu (lub pełnego zestawu danych, jeśli obejmujące pełny rok). Ta dystrybucja klas etykiet zostanie później użyta w przypadku modelowania klasyfikacji wieloklasowej.

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
Etykieta generowania i położenia geograficznego konwersji eksploracji zapytania również może służyć do generowania etykiet/funkcji, usuwając część obliczeń. Dodatkowe przykłady SQL dla inżynierów funkcji są dostępne w sekcji [eksplorowanie i opracowywanie funkcji w notesie IPython](#ipnb) . Bardziej wydajne jest uruchamianie zapytań generacji funkcji na pełnym zestawie danych lub w dużym podzestawie przy użyciu zapytań SQL, które są uruchamiane bezpośrednio w wystąpieniu bazy danych SQL Server. Zapytania mogą być wykonywane w **SQL Server Management Studio**, IPython notesie lub dowolnym narzędziu programistycznym lub środowisku, które może uzyskać dostęp do bazy danych lokalnie lub zdalnie.

#### <a name="preparing-data-for-model-building"></a>Przygotowywanie danych do konstruowania modelu
Następujące zapytanie sprzęga **\_podróż** i **nyctaxi opłaty za\_** , generuje tablicę klasyfikacji binarnej, **przechyloną**, wieloklasową **wskazówkę etykiety klasyfikacji\_klasy**, a następnie wyodrębnia 1% losową próbkę z pełnego dołączonego zestawu danych. To zapytanie można skopiować, a następnie wkleić bezpośrednio do modułu [Azure Machine Learning Studio](https://studio.azureml.net) [Importowanie danych][import-data] w celu bezpośredniego pozyskiwania danych z wystąpienia bazy danych SQL Server na platformie Azure. Zapytanie wyklucza rekordy z niepoprawny (0, 0) współrzędnych.

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


## <a name="ipnb"></a>Eksploracja danych i inżynieria funkcji w notesie IPython
W tej sekcji firma Microsoft będzie wykonywać eksploracji danych i generowanie funkcji za pomocą środowiska Python, jak i SQL zapytań względem bazy danych programu SQL Server, utworzony wcześniej. Przykładowy Notes IPython o nazwie **Machine-Learning-Data-nauka-Process-SQL-historie. ipynb** jest dostępny w folderze **Sample notesy IPython** . Ten Notes jest również dostępny w witrynie [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

Podczas pracy z danymi Big Data postępuj zgodnie z tą zalecaną sekwencją:

* Przeczytaj w niewielką próbkę danych do ramki danych w pamięci.
* Wykonaj niektóre wizualizacje i eksploracji przy użyciu próbki danych.
* Poeksperymentuj z technicznego opracowywania funkcji przy użyciu próbki danych.
* Dla większych eksplorację danych, manipulowania danymi i technicznego opracowywania funkcji wysyłać zapytania SQL bezpośrednio w odniesieniu do bazy danych programu SQL Server w maszynie Wirtualnej platformy Azure przy użyciu języka Python.
* Zdecyduj, czy rozmiar próbki na potrzeby tworzenia modelu usługi Azure Machine Learning

Gdy wszystko będzie gotowe do przejścia do usługi Azure Machine Learning, użytkownik może:  

1. Zapisz ostateczną kwerendę SQL w celu wyodrębnienia i próbkowania danych i skopiowania zapytania bezpośrednio do modułu [importowania danych][import-data] w Azure Machine Learning. Ta metoda jest przedstawiona w sekcji [Tworzenie modeli w Azure Machine Learning](#mlmodel) .    
2. Utrwalaj próbkowane i zaprojektowane dane, które planujesz użyć do kompilowania modelu w nowej tabeli bazy danych, a następnie użyj nowej tabeli w module [Importuj dane][import-data] .

Poniżej przedstawiono kilka eksplorację danych, Wizualizacja danych i funkcji inżynierii przykłady. Aby uzyskać więcej przykładów, zobacz przykładowy Notes SQL IPython w folderze **Sample Notess IPython** .

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
Teraz przystąpić do eksplorowania próbki danych. Zaczynamy od przejrzenia statystyk opisowych dla **\_j odległości** (lub dowolnego innego) pola:

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
Pokazujemy wykres punktowy między **podróżą\_czasie\_w\_sekundach** i **wyjazdem\_** , aby sprawdzić, czy istnieje korelacja

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Wykreślania #6][6]

Podobnie możemy sprawdzić relację między **szybkością\_kod** i **podróż\_odległość**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![#8 wykreślania][8]

### <a name="sub-sampling-the-data-in-sql"></a>Podrzędne próbkowanie danych w SQL
Podczas przygotowywania danych do kompilowania modeli w [Azure Machine Learning Studio](https://studio.azureml.net)można zdecydować, czy **zapytanie SQL ma być używane bezpośrednio w module Importuj dane** , czy przechowywać i próbkowane dane w nowej tabeli, która może być używana w module [Import danych][import-data] z prostym **SELECT * FROM <\_nowej tabeli\_\_** >.

W tej sekcji utworzymy nową tabelę do przechowywania danych próbkowanych i przetworzonych. Przykład bezpośredniej kwerendy SQL na potrzeby konstruowania modelu znajduje się w sekcji [Eksploracja danych i inżynieria funkcji w SQL Server](#dbexplore) .

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Tworzenie przykładowej tabeli i wypełnić połączonych tabel: % 1. Upuść pierwszej tabeli, jeśli taki istnieje.
W tej sekcji dołączymy do tabel **nyctaxi\_podróż** i **nyctaxi\_** , Wyodrębnij losową próbkę 1% i Utrwalaj dane przykładowe w nowej tabeli o nazwie **nyctaxi\_jeden\_procent**:

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
W tej sekcji eksplorujemy dystrybucje danych przy użyciu danych z próbką 1%, które są utrwalane w nowo utworzonej tabeli. Podobne eksploracje mogą być wykonywane przy użyciu oryginalnych tabel, **opcjonalnie przy użyciu** tabeli odnoszącej, aby ograniczyć próbkę eksploracji lub ograniczając wyniki do danego przedziału czasu przy użyciu partycji **\_DateTime** , jak pokazano w sekcji [Eksploracja danych i inżynieria funkcji w SQL Server](#dbexplore) .

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

1. Przerzucane etykiety klas binarnych (przewidywanie, jeśli zostanie podaną wskazówką)
2. Etykietka wieloklasowa **Porada\_klasy** (przewidywanie pojemnika lub zakresu końcówki)
   
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
Ten przykład przekształca ciągłe pole liczbowe do wstępnie ustawionych zakresów kategorii, czyli Przekształć pole numeryczne w pole kategorii.

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
Ten przykład dzieli dziesiętną reprezentację pola szerokości geograficznej i/lub długości geograficznej na kilka regionów pól o różnych stopnia szczegółowości, takich jak kraj/region, miasto, miejscowość, blok itp. Nowe pola geograficzne nie są zamapowane na rzeczywiste lokalizacje. Aby uzyskać informacje na temat mapowania lokalizacji geokodu, zobacz [usługi Bing Maps](https://msdn.microsoft.com/library/ff701710.aspx).

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

Teraz możemy przystąpić do tworzenia modeli i wdrażania modeli w [Azure Machine Learning](https://studio.azureml.net). Dane są gotowe jakichkolwiek problemów prognozowania wymienionych wcześniej, to znaczy:

1. Klasyfikacja binarna: przewidywanie czy Porada zapłacono komunikacji dwustronnej.
2. Klasyfikacji wieloklasowej: przewidywanie zakres Porada płatnej zgodnie z wcześniej zdefiniowanych klas.
3. Zadanie regresji: przewidywanie ilość Porada płatnych komunikacji dwustronnej.  

## <a name="mlmodel"></a>Kompilowanie modeli w Azure Machine Learning
Aby rozpocząć wykonywania modelowania, zaloguj się do swojego obszaru roboczego usługi Azure Machine Learning. Jeśli nie utworzono jeszcze obszaru roboczego uczenia maszynowego, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](../studio/create-workspace.md).

1. Aby rozpocząć pracę z Azure Machine Learning, zobacz [co to jest Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Zaloguj się do [Azure Machine Learning Studio](https://studio.azureml.net).
3. Strona główna Studio udostępnia mnóstwo informacji, pliki wideo, samouczki, łącza do dokumentacji dotyczącej modułów i innych zasobów. Aby uzyskać więcej informacji na temat Azure Machine Learning, zapoznaj się z [centrum dokumentacji Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Typowe eksperyment składa się z następujących czynności:

1. Utwórz **Nowy** eksperyment.
2. Pobieranie danych do usługi Azure Machine Learning.
3. Przed przetworzeniem i przekształcaniem danych oraz manipulowanie nimi.
4. Generowanie funkcji zgodnie z potrzebami.
5. Podzielić dane na szkolenie i sprawdzania poprawności/testowania zestawów danych (lub rozdzielanie zestawów danych dla każdego).
6. Wybierz co najmniej jeden algorytmów uczenia maszynowego w zależności od problemu nauki do rozwiązania. Na przykład klasyfikacja binarna, klasyfikacja wieloklasowa, regresja.
7. Szkolenie jednego lub więcej modeli przy użyciu zestawu danych szkoleniowych.
8. Wynik sprawdzania poprawności zestawu danych za pomocą wytrenowane modele.
9. Ocena modeli można obliczyć metryki istotne dla nauczanym problemem.
10. Dostosuj modele i wybierz najlepszy model do wdrożenia.

W tym ćwiczeniu firma ma już przedstawione odtwarzane danych w programie SQL Server i ustalony rozmiar próbki do pozyskania w usłudze Azure Machine Learning. W celu utworzenia co najmniej jednego modelu predykcyjnego postanowiono:

1. Pobierz dane do Azure Machine Learning przy użyciu modułu [Importuj dane][import-data] dostępne w sekcji **dane wejściowe i wyjściowe** . Aby uzyskać więcej informacji, zobacz stronę odwołania modułu [importowania danych][import-data] .
   
    ![Usługi Azure Machine Learning importu danych][17]
2. Wybierz **Azure SQL Database** jako **Źródło danych** w panelu **Właściwości** .
3. Wprowadź nazwę DNS bazy danych w polu **Nazwa serwera bazy danych** . Format: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Wprowadź **nazwę bazy danych** w odpowiednim polu.
5. Wprowadź **nazwę użytkownika SQL** w polu **nazwa konta użytkownika serwera**i **hasło** w polu **hasło konta użytkownika serwera**.
7. W obszarze tekst **kwerendy bazy danych** Edytuj zapytanie, które wyodrębnia niezbędne pola bazy danych (w tym wszystkie pola obliczane, takie jak etykiety), i w dół próbkuje dane do żądanego rozmiaru próbki.

Przykład eksperymentu klasyfikacji binarnej odczytywania danych bezpośrednio z bazy danych programu SQL Server to na poniższej ilustracji. Podobne eksperymenty można konstruować wieloklasowej klasyfikacji i regresji problemów.

![Usługi Azure Machine Learning szkolenie][10]

> [!IMPORTANT]
> W zapytaniach dotyczących wyodrębniania i próbkowania danych modelowania, które zostały podane w poprzednich sekcjach, **wszystkie etykiety dla trzech ćwiczeń modelowania są zawarte w zapytaniu**. Ważne (wymagane) krok w każdym z ćwiczeń modelowania polega na **wykluczeniu** niepotrzebnych etykiet dla innych dwóch problemów oraz wszelkich innych **przecieków docelowych**. Na przykład, w przypadku używania klasyfikacji binarnej, należy użyć etykiety **przechylonej** i wykluczyć **\_etykietki**pól, **\_ilość**i **łączną kwotę\_** . Są one przecieki docelowy od momentu oznaczają porady płatne.
> 
> Aby wykluczyć niepotrzebne kolumny i/lub wycieki docelowe, możesz użyć modułu [SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych][select-columns] ) lub [edytować metadane][edit-metadata]. Aby uzyskać więcej informacji, zobacz [Wybieranie kolumn w zestawie danych][select-columns] i edytowanie stron odwołań do [metadanych][edit-metadata] .
> 
> 

## <a name="mldeploy"></a>Wdrażanie modeli w Azure Machine Learning
Gdy model jest gotowy, można łatwo wdrażać je jako usługi sieci web bezpośrednio z poziomu eksperymentu. Aby uzyskać więcej informacji na temat wdrażania usług sieci Web Azure Machine Learning, zobacz [wdrażanie usługi sieci web Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md).

Aby wdrożyć nową usługę sieci web, należy:

1. Tworzenie eksperymentu oceniania.
2. Wdrażanie usługi sieci web.

Aby utworzyć eksperyment oceniania z **gotowego** eksperymentu szkoleniowego, kliknij pozycję **Utwórz eksperyment oceniania** na niższym pasku akcji.

![Ocenianie przez usługę Azure][18]

Usługa Azure Machine Learning spróbuje utworzyć eksperyment oceniania na podstawie składników eksperymentu szkolenia. W szczególności będą wykonywane następujące czynności:

1. Zapisania trenowanego modelu, a następnie usuń moduły szkolenie modelu.
2. Określ logiczny **port wejściowy** reprezentujący oczekiwany schemat danych wejściowych.
3. Określ logiczny **port wyjściowy** reprezentujący oczekiwany schemat danych wyjściowych usługi sieci Web.

Podczas tworzenia eksperymentu oceniania jego przejrzenie i dostosować zgodnie z potrzebami. Typowym dopasowaniem jest zastępowanie wejściowego zestawu danych i/lub zapytania, które wyklucza pola etykiety, ponieważ te etykiety nie będą dostępne w schemacie, gdy usługa zostanie wywołana. Dobrym sposobem jest zmniejszenie rozmiaru wejściowego zestawu danych i/lub zapytania do kilku rekordów, wystarczająco że wskazuje schemat wejściowy. Dla portu wyjściowego często wyklucza wszystkie pola wejściowe i zawiera tylko **etykiety z wynikami** i **oceny prawdopodobieństwa** w danych wyjściowych za pomocą modułu [SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych][select-columns] ).

Przykład oceniania eksperymentu jest na poniższej ilustracji. Gdy wszystko będzie gotowe do wdrożenia, kliknij przycisk **Publikuj usługę sieci Web** na dolnym pasku akcji.

![Publikowanie w usłudze Azure Machine Learning][11]

Podsumowanie, w tym samouczku instruktażu utworzono środowisko do nauki o danych platformy Azure z dużego zestawu danych publicznych od pozyskiwanie danych do modelu uczenia i wdrażania usługi sieci web Azure Machine Learning.

### <a name="license-information"></a>Informacje o licencji
Ten przewodnik po przykładzie i towarzyszące jej IPython notebook(s) i skrypty są udostępniane przez firmę Microsoft na licencji MIT. Sprawdź plik LICENSE. txt w katalogu przykładowego kodu w witrynie GitHub, aby uzyskać więcej informacji.

### <a name="references"></a>Dokumentacja
• [Strona pobierania Andrés MONROY NYC TRIPS](https://www.andresmh.com/nyctaxitrips/)  
• [Dane dotyczące podróży z NYCą w folii przez Krzysztof Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [Badania i statystyka NYCych taksówki oraz Komisji Limousine](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

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
