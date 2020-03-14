---
title: Eksplorowanie danych w klastrze usługi Hadoop — zespołu danych dla celów naukowych
description: Scenariusz end-to-end, wykorzystujące klastra usługi HDInsight Hadoop, aby skompilować i wdrożyć model, za pomocą procesie nauki o danych zespołu.
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
ms.openlocfilehash: 005d4fe1b6ec59e7f05be3dd2ab3e72d0e7aa8e0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283422"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>Zespół danych dla celów naukowych w działaniu: klastry użycia usługi Azure HDInsight Hadoop
W tym instruktażu będziemy używać [procesu nauki o danych zespołowych (przetwarzania TDSP)](overview.md) w kompleksowym scenariuszu. Korzystamy z [klastra Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) do przechowywania, eksplorowania i tworzenia funkcji danych z dostępnych publicznie zestawów danych [podróży NYCych](https://www.andresmh.com/nyctaxitrips/) w postaci pośrednich oraz do próbkowania danych. Aby obsłużyć binarne i wieloklasowej klasyfikacji i regresji predykcyjnego zadań, firma Microsoft tworzy modele danych przy użyciu usługi Azure Machine Learning. 

Aby zapoznać się z przewodnikiem, który pokazuje, jak obsłużyć większy zestaw danych, zobacz [zespołowe przetwarzanie danych — używanie klastrów Azure HDInsight Hadoop w zestawie danych 1 TB](hive-criteo-walkthrough.md).

Za pomocą notesu IPython można także wykonać zadania przedstawione w przewodniku, który używa zestawu danych o pojemności 1 TB. Aby uzyskać więcej informacji, zobacz [Przewodnik Criteo przy użyciu połączenia ODBC platformy Hive](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).

## <a name="dataset"></a>Opis zestawu danych NYC taksówki
Dane podróży taksówek NYC to około 20 GB plików skompresowanych wartości rozdzielanych przecinkami (CSV) (~ 48 GB nieskompresowane). Ma ponad milion 173 poszczególnych podróży i obejmuje opłaty za każdy podróży. Każdy podróży rekord zawiera odbiór i dropoff lokalizacji i czasu, hack anonimowe (sterownika) numer licencji i numer Medalionu (taksówek Unikatowy identyfikator). Dane obejmuje wszystkie podróży w roku 2013 i znajduje się w następujących dwóch zestawów danych w każdym miesiącu:

- Pliki CSV trip_data zawierają szczegóły podróży: liczbę pasażerów, punkty pobierania i Dropoff, czas trwania podróży i długość podróży. Poniżej przedstawiono kilka przykładowych rekordów:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
- Pliki CSV trip_fare zawierają szczegóły dotyczące opłat za każdą podróż: typ płatności, Wysokość opłaty, opłaty i podatki, porady i opłaty oraz łączną kwotę płatną. Poniżej przedstawiono kilka przykładowych rekordów:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Unikatowy klucz do przyłączenia do podróży\_Data i podróż\_opłaty są złożone z pól: Medallion, haker\_License i pobrania\_DateTime. Aby uzyskać wszystkie szczegóły dotyczą konkretnego podróży, jest wystarczające, aby przyłączyć się te trzy klucze.

## <a name="mltasks"></a>Przykłady zadań przewidywania
Określ rodzaj prognoz, które chcesz utworzyć na podstawie analizy danych, aby ułatwić wyjaśnienie wymaganych zadań procesów. Poniżej przedstawiono trzy przykłady problemów przewidywania, które są używane w tym instruktażu, w oparciu o *kwotę\_TIP*:

- **Klasyfikacja binarna**: przewidywanie, czy Porada została zapłacona za podróż. Oznacza to, że *\_Porada* , która jest większa niż $0, jest dodatnim przykładem, a *Porada\_kwota* $0 jest przykładem negatywnym.
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0
- **Klasyfikacja wieloklasowa**: przewidywanie zakresu kwot TIP płatnych za podróż. Podzielmy *wskazówkę\_* na pięć klas:
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0 and tip_amount <= $5
        Class 2: tip_amount > $5 and tip_amount <= $10
        Class 3: tip_amount > $10 and tip_amount <= $20
        Class 4: tip_amount > $20
- **Zadanie regresji**: przewidywanie kwoty Porada płatnej dla podróży.  

## <a name="setup"></a>Skonfiguruj klaster usługi HDInsight Hadoop na potrzeby zaawansowanej analizy
> [!NOTE]
> Zazwyczaj jest zadaniem administracyjnym.
> 
> 

Możesz skonfigurować środowisko platformy Azure na potrzeby zaawansowanej analizy, korzystającej z klastra usługi HDInsight w trzech krokach:

1. [Utwórz konto magazynu](../../storage/common/storage-account-create.md): to konto magazynu jest używane do przechowywania danych w usłudze Azure Blob Storage. Dane używane w klastrach HDInsight znajduje się także w tym miejscu.
2. [Dostosowywanie klastrów Azure HDInsight Hadoop na potrzeby zaawansowanego procesu i technologii analitycznej](customize-hadoop-cluster.md). Ten krok umożliwia utworzenie klastra usługi HDInsight Hadoop za pomocą 64-bitowego pakietu Anaconda Python 2.7 zainstalowane we wszystkich węzłach. Istnieją dwie ważne czynności, należy pamiętać podczas dostosowywania klastra usługi HDInsight.
   
   * Pamiętaj, aby połączyć konto usługi storage utworzonego w kroku 1 z klastrem usługi HDInsight, podczas jego tworzenia. To konto magazynu ma dostęp do danych, które są przetwarzane w klastrze.
   * Po utworzeniu klastra, należy włączyć dostęp zdalny z węzłem głównym klastra. Przejdź do karty **Konfiguracja** i wybierz pozycję **Włącz zdalne**. Ten krok Określa poświadczenia użytkownika służące do logowania zdalnego.
3. [Utwórz obszar roboczy Azure Machine Learning](../studio/create-workspace.md): ten obszar roboczy służy do kompilowania modeli uczenia maszynowego. To zadanie jest skierowana po wypełnieniu eksploracji danych początkowych i w dół — w przypadku próbkowania przy użyciu klastra HDInsight.

## <a name="getdata"></a>Pobieranie danych z publicznego źródła
> [!NOTE]
> Zazwyczaj jest zadaniem administracyjnym.
> 
> 

Aby skopiować zestaw danych [podróży z NYC taksówkami](https://www.andresmh.com/nyctaxitrips/) do maszyny z lokalizacji publicznej, użyj dowolnej z metod opisanych w temacie [przenoszenie danych do i z usługi Azure Blob Storage](move-azure-blob.md).

W tym miejscu opisano sposób użycia narzędzia AzCopy do transferu plików zawierających dane. Aby pobrać i zainstalować AzCopy, postępuj zgodnie z instrukcjami podanymi na stronie [wprowadzenie do narzędzia wiersza polecenia AzCopy](../../storage/common/storage-use-azcopy.md).

1. W oknie wiersza polecenia Uruchom następujące polecenia AzCopy, zastępując *\<path_to_data_folder >* z żądanym miejscem docelowym:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. Po zakończeniu kopiowania, zobaczysz łącznie 24 skompresowane pliki z folderu danych wybrana. Rozpakuj pobranych plików, w tym samym katalogu na komputerze lokalnym. Zanotuj folder, w którym znajdują się pliki nieskompresowane. Ten folder jest określany mianem *\<ścieżki\_do\_unzipped_data\_plików\>* .

## <a name="upload"></a>Przekaż dane do domyślnego kontenera klastra usługi HDInsight Hadoop
> [!NOTE]
> Zazwyczaj jest zadaniem administracyjnym.
> 
> 

Następujące polecenia narzędzia AzCopy, należy zastąpić następujące parametry przy użyciu rzeczywistych wartości, które zostały określone podczas tworzenia klastra usługi Hadoop i Rozpakowywanie plików danych.

* ***\<path_to_data_folder >*** Katalog (wraz ze ścieżką) na komputerze, który zawiera niespakowane pliki danych.  
* ***\<nazwę konta magazynu klastra usługi Hadoop >*** Konto magazynu skojarzone z klastrem usługi HDInsight.
* ***\<domyślnego kontenera klastra usługi Hadoop >*** Domyślny kontener używany przez klaster. Nazwa domyślnego kontenera jest zwykle taka sama jak nazwa klastra. Na przykład jeśli klaster jest nazywany "abc123.azurehdinsight.net", domyślny kontener jest abc123.
* ***> klucz konta magazynu\<*** Klucz dla konta magazynu używanego przez klaster.

Z wiersza polecenia lub okno programu Windows PowerShell Uruchom dwa poniższe polecenia narzędzia AzCopy.

To polecenie przekazuje dane podróży do katalogu ***nyctaxitripraw*** w domyślnym kontenerze klastra Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

To polecenie przekazuje dane dotyczące taryfy do katalogu ***nyctaxifareraw*** w domyślnym kontenerze klastra Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Dane powinno być teraz w usłudze Blob storage i gotowe do użycia w klastrze HDInsight.

## <a name="#download-hql-files"></a>Zaloguj się do węzła głównego klastra usługi Hadoop i przygotuj się do analizy danych w sposób naukowy
> [!NOTE]
> Zazwyczaj jest zadaniem administracyjnym.
> 
> 

Aby uzyskać dostęp do węzła głównego klastra w celu analizy danych i próbkowania danych, postępuj zgodnie z procedurą przedstawioną w temacie [dostęp do węzła głównego klastra usługi Hadoop](customize-hadoop-cluster.md).

W tym instruktażu przede wszystkim korzystamy z zapytań utworzonych w programie [Hive](https://hive.apache.org/), języka zapytań PRZYPOMINAJĄCYCH kod SQL, aby przeprowadzać wstępne eksploracje danych. Zapytania programu Hive są przechowywane w plikach ". HQL". Firma Microsoft następnie obniżenie częstotliwości próbkowania tych danych do użycia w ramach uczenia maszynowego do tworzenia modeli.

Aby przygotować klaster do analizy danych w sposób naukowy, Pobierz pliki ". HQL" zawierające odpowiednie skrypty Hive z usługi [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) do katalogu lokalnego (C:\Temp) w węźle głównym. Otwórz wiersz polecenia z poziomu węzła głównego klastra i uruchom następujące dwa polecenia:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Te dwa polecenia pobierają wszystkie pliki ". HQL", które są konieczne w tym instruktażu, do katalogu lokalnego ***C:\Temp&#92;***  w węźle głównym.

## <a name="#hive-db-tables"></a>Tworzenie bazy danych i tabel programu Hive partycjonowane według miesiąca
> [!NOTE]
> To zadanie jest zazwyczaj przeznaczone dla administratora.
> 
> 

Teraz możesz przystąpić do tworzenia tabel programu Hive dla zestawu danych taksówek NYC.
Węzeł główny klastra usługi Hadoop Otwórz wiersza polecenia usługi Hadoop na pulpicie węzła głównego. Wprowadź katalog gałęzi, uruchamiając następujące polecenie:

    cd %hive_home%\bin

> [!NOTE]
> Uruchom wszystkie polecenia gałąź w tym przewodniku z Hive bin / directory wiersza. Automatycznie obsługuje wszystkie ścieżce problemy. Używane pojęcia "Gałęzi katalogu wiersz", "Hive bin / directory wiersz" i "Wiersza polecenia usługi Hadoop" zamiennie w tym przewodniku.
> 
> 

W wierszu polecenia usługi Hive, uruchom następujące polecenie w wierszu poleceń Hadoop węzła głównego, który tworzy bazę danych i tabele programu Hive:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Poniżej znajduje się zawartość **C:\temp\sample\_hive\_tworzenia\_db\_i\_Tables. HQL** , który tworzy bazę danych programu Hive **nyctaxidb**oraz informacje o wykorzystaniu i **obtaryfie** **tabel.**

    create database if not exists nyctaxidb;

    create external table if not exists nyctaxidb.trip
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double)  
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

    create external table if not exists nyctaxidb.fare
    (
        medallion string,
        hack_license string,
        vendor_id string,
        pickup_datetime string,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double)
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

Ten skrypt programu Hive tworzy dwie tabele:

* Tabela **rejsów** zawiera szczegółowe informacje o podróży poszczególnych przebiegów (Szczegóły sterownika, czas pobierania, odległość podróży i godziny).
* Tabela **opłata** zawiera szczegółowe informacje o opłatach (opłata za opłatę, kwotę Porada, opłaty i opłaty).

Jeśli potrzebujesz dodatkowej pomocy z tymi procedurami lub chcesz zbadać alternatywy, zobacz sekcję [przesyłanie zapytań Hive bezpośrednio z wiersza polecenia usługi Hadoop](move-hive-tables.md#submit).

## <a name="#load-data"></a>Ładowanie danych do tabel programu Hive według partycji
> [!NOTE]
> To zadanie jest zazwyczaj przeznaczone dla administratora.
> 
> 

Zestaw danych taksówek NYC ma naturalny partycjonowanie według miesiąca, które firma Microsoft umożliwiają szybsze przetwarzanie i zapytania. Następujące polecenia programu PowerShell (wydać z katalogu programu Hive przy użyciu wiersza polecenia usługi Hadoop) ładowanie danych do wyzwolenie i klasie tabel programu Hive, podzielone na partycje według miesiąca.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

**Przykład\_hive\_obciążenie\_dane\_przez\_plik partitions. HQL** zawiera następujące polecenia **ładowania** :

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Liczba zapytań Hive użytych w tym miejscu w procesie eksploracji obejmuje tylko jedną lub dwie partycje. Jednak te zapytania mogą być uruchamiane na całym zestawie danych.

### <a name="#show-db"></a>Wyświetlanie baz danych w klastrze usługi HDInsight Hadoop
Aby wyświetlić bazy danych utworzone w klastrze usługi HDInsight Hadoop, w oknie wiersza polecenia usługi Hadoop, uruchom następujące polecenie w wierszu polecenia usługi Hadoop:

    hive -e "show databases;"

### <a name="#show-tables"></a>Pokaż tabele programu Hive w bazie danych **nyctaxidb**
Aby wyświetlić tabele w bazie danych **nyctaxidb** , uruchom następujące polecenie w wierszu polecenia usługi Hadoop:

    hive -e "show tables in nyctaxidb;"

Możemy potwierdzić, że tabele są podzielone na partycje, uruchamiając następujące polecenie:

    hive -e "show partitions nyctaxidb.trip;"

Oto oczekiwane dane wyjściowe:

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 2.075 seconds, Fetched: 12 row(s)

Podobnie Upewniamy się, że tabela klasie jest podzielona na partycje, uruchamiając następujące polecenie:

    hive -e "show partitions nyctaxidb.fare;"

Oto oczekiwane dane wyjściowe:

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 1.887 seconds, Fetched: 12 row(s)

## <a name="#explore-hive"></a>Eksploracja danych i inżynieria funkcji w usłudze Hive
> [!NOTE]
> Zazwyczaj jest to zadanie naukowego przetwarzania danych.
> 
> 

Zapytania programu Hive można użyć do wykonywania eksploracji danych i funkcji, inżynierii zadania dla danych ładowane do tabel programu Hive. Poniżej przedstawiono przykłady takich zadań:

* Wyświetl rekordy 10 najważniejszych w obu tabelach.
* Zapoznaj się z dystrybucji danych kilka pól w różnych okna czasowe.
* Zbadaj dobrej jakości danych pola długości i szerokości geograficznej.
* Generowanie etykiety binarne i wieloklasowej klasyfikacji na podstawie ilości porada.
* Wygeneruj funkcje, przetwarzanie odległości bezpośrednie podróży.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Eksploracja: Wyświetl rekordy 10 najważniejszych w podróży tabeli
> [!NOTE]
> Zazwyczaj jest to zadanie naukowego przetwarzania danych.
> 
> 

Aby zobaczyć, jak wygląda danych, sprawdź 10 rekordów z każdej tabeli. Aby przeprowadzić inspekcję rekordy, należy uruchomić następujące dwa zapytania niezależnie od wiersza katalogu programu Hive, w konsoli wiersza polecenia usługi Hadoop.

Aby uzyskać 10 pierwszych rekordów w tabeli podróży z pierwszego miesiąca:

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Aby uzyskać 10 pierwszych rekordów w tabeli taryfy z pierwszego miesiąca:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

Rekordy można zapisać do pliku, aby wygodnie przeglądać z niewielką zmianą w powyższym zapytaniu:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Eksploracja: Wyświetlanie liczby rekordów w każdej z 12 partycjami
> [!NOTE]
> Zazwyczaj jest to zadanie naukowego przetwarzania danych.
> 
> 

Odsetek jest jak Liczba podróży zmienia się w roku kalendarzowym. Grupowanie według miesięcy, pokazuje rozkład podróży.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

To polecenie tworzy następujące dane wyjściowe:

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 283.406 seconds, Fetched: 12 row(s)

W tym miejscu pierwsza kolumna miesiąc, a drugi jest liczba podróży w danym miesiącu.

Firma Microsoft liczba całkowita liczba rekordów w zestawie danych podróży, uruchamiając następujące polecenie w wierszu katalogu Hive:

    hive -e "select count(*) from nyctaxidb.trip;"

To polecenie daje:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Za pomocą poleceń, podobnie jak te wyświetlane dla zestawu danych podróży, firma Microsoft może wystawiać zapytań programu Hive w wierszu polecenia programu Hive katalogu dla zestawu danych taryfy do sprawdzania poprawności liczby rekordów.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

To polecenie tworzy następujące dane wyjściowe:

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 253.955 seconds, Fetched: 12 row(s)

Dokładna liczba rejsów miesięcznie jest zwracana dla obu zestawów danych, co zapewnia pierwsze sprawdzenie poprawności, czy dane zostały załadowane poprawnie.

Całkowita liczba rekordów w zestawie danych klasie może być traktowane w przy użyciu następującego polecenia w wierszu polecenia katalogu Hive:

    hive -e "select count(*) from nyctaxidb.fare;"

To polecenie daje:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

Całkowita liczba rekordów w obu tabelach jest również taka sama, co zapewnia drugą weryfikację, że dane zostały prawidłowo załadowane.

### <a name="exploration-trip-distribution-by-medallion"></a>Eksploracja: Podróży rozkład według Medalionu
> [!NOTE]
> Ta analiza jest zazwyczaj zadaniem Analityka danych.
> 
> 

W tym przykładzie identyfikuje medallions (numery taksówek) o rozmiarze większym niż 100 podróży w danym okresie czasu. Zapytanie ma zalety dostępu do partycjonowanej tabeli, ponieważ jest ono warunkiem **miesiąca**zmiennej partycji. Wyniki zapytania są zapisywane w pliku lokalnym, **queryoutput. tsv**, w `C:\temp` węzła głównego.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Poniżej znajduje się zawartość **przykładu\_hive\_\_liczbę\_przez\_plik Medallion. HQL** do inspekcji.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Medalionu w zestawie danych taksówek NYC identyfikuje unikatowy pliku cab. Można zidentyfikować, które pliki cab są stosunkowo zajęte przez zadanie, które z nich wprowadzone więcej niż określoną liczbę rund w określonym przedziale czasu. Poniższy przykład identyfikuje OOZ, którzy wykonali ponad setki podróży w ciągu pierwszych trzech miesięcy i zapisuje wyniki zapytania do pliku lokalnego, **C:\temp\queryoutput.tsv**.

Poniżej znajduje się zawartość **przykładu\_hive\_\_liczbę\_przez\_plik Medallion. HQL** do inspekcji.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

W wierszu polecenia katalogu programu Hive uruchom następujące polecenie:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Eksploracja: Podróży rozkład według Medalionu i hack licencji
> [!NOTE]
> To zadanie jest zwykle przeznaczone dla analityków danych.
> 
> 

Podczas eksplorowania zestawu danych często chcemy przeanalizować dystrybucje grup wartości. Ta sekcja zawiera przykład sposobu przeprowadzania tej analizy dla plików CAB i sterowników.

**Przykładowa\_\_\_liczba\_,\_medallion\_licencji. plik HQL** grupuje zestaw danych taryf na **Medallion** i **hack_license**i zwraca liczbę każdej kombinacji. Oto jego zawartość:

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Ta kwerenda zwraca kombinacji pliku cab i sterowników, uporządkowane według malejącej liczbę rund.

W wierszu polecenia katalogu programu Hive Uruchom polecenie:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

Wyniki zapytania są zapisywane w pliku lokalnym, **C:\temp\queryoutput.tsv**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Eksploracja: Oceny jakości danych przez sprawdzenie, czy rekordy szerokości geograficznej lub nieprawidłową długość geograficzną
> [!NOTE]
> Zazwyczaj jest to zadanie naukowego przetwarzania danych.
> 
> 

Typowe celem analizy danych eksploracyjnego jest chwastów się nieprawidłowe lub uszkodzone rekordów. W przykładzie w tej sekcji określa, czy długość geograficzna lub szerokość pola zawierają wartość daleko poza obszarem NYC. Ponieważ jest to prawdopodobnie, że takie rekordy mają wartość błędne szerokości geograficznej, chcemy je wyeliminować wszelkie dane, które ma być używane do modelowania.

Poniżej znajduje się zawartość **przykładowej\_hive\_quality\_Assessment. HQL** do inspekcji.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


W wierszu polecenia katalogu programu Hive Uruchom polecenie:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

Argument *-S* zawarty w tym poleceniu pomija wydruk ekranu stanu mapy Hive/Zmniejsz zadania. To polecenie jest przydatne, ponieważ sprawia, że ekran wydruku zapytania programu Hive jest bardziej czytelny.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Eksploracja: Binarny klasy dystrybucje porad podróży
> [!NOTE]
> Zazwyczaj jest to zadanie naukowego przetwarzania danych.
> 
> 

W przypadku problemu klasyfikacji binarnej podanej w sekcji [przykłady zadań przewidywania](hive-walkthrough.md#mltasks) warto sprawdzić, czy podano poradę. Tej dystrybucji porady jest binarny:

* podaną wskazówką (Klasa 1, Porada\_kwota > $0)  
* Brak Porada (klasa 0, TIP\_kwota = $0)

Poniższy **przykład\_hive\_** przerzucane\_częstotliwości, plik HQL pokazuje polecenie do uruchomienia:

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

W wierszu polecenia katalogu programu Hive Uruchom polecenie:

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Eksploracja: Klasa dystrybucje w ustawieniu wieloklasowej
> [!NOTE]
> Zazwyczaj jest to zadanie naukowego przetwarzania danych.
> 
> 

W przypadku problemu klasyfikacji wieloklasowej, którą opisano w sekcji [przykłady zadań przewidywania](hive-walkthrough.md#mltasks) , ten zestaw danych pozwala również na naturalną klasyfikację do przewidywania liczby podanych wskazówek. Możemy użyć pojemniki do definiowania zakresów Porada w zapytaniu. Aby uzyskać dystrybucje klas dla różnych zakresów etykiet, użyj **przykładu\_hive\_tip\_zakres\_częstotliwości. HQL** . Oto jego zawartość.

    SELECT tip_class, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount=0, 0,
            if(tip_amount>0 and tip_amount<=5, 1,
            if(tip_amount>5 and tip_amount<=10, 2,
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

Uruchom następujące polecenie z poziomu konsoli wiersza polecenia usługi Hadoop:

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Eksploracja: Obliczenia bezpośrednie odległość między dwiema lokalizacjami szerokości geograficznej
> [!NOTE]
> Zazwyczaj jest to zadanie naukowego przetwarzania danych.
> 
> 

Warto wiedzieć, czy istnieje różnica pomiędzy bezpośrednie odległość między dwiema lokalizacjami i odległość rzeczywiste podróży taksówek. Pasażera może być mniej prawdopodobne, porada, jeśli one zorientować się, że sterownik celowo miało ich przez dłuższy trasy.

Aby zobaczyć porównanie między rzeczywistą odległością podróży a [odległością Haversine](https://en.wikipedia.org/wiki/Haversine_formula) między dwoma punktami szerokości geograficznej (odległości "doskonały okrąg"), można użyć funkcji trygonometrycznych dostępnych w ramach programu Hive:

    set R=3959;
    set pi=radians(180);

    insert overwrite directory 'wasb:///queryoutputdir'

    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
     *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
     *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
     /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
     +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
     pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
    from nyctaxidb.trip
    where month=1
    and pickup_longitude between -90 and -30
    and pickup_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
    and dropoff_latitude between 30 and 90;

W poprzednim zapytaniu R to promień ziemi w milach i pi jest konwertowana na wartość w radianach. Punkty szerokości geograficznej są filtrowane w celu usunięcia wartości, które są daleko od obszaru NYC.

W takim przypadku napiszemy wyniki do katalogu o nazwie **queryoutputdir**. Sekwencja poniższe polecenia najpierw tworzy ten katalog wyjściowy, a następnie uruchamia polecenia programu Hive.

W wierszu polecenia katalogu programu Hive Uruchom polecenie:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


Wyniki zapytania są zapisywane do dziewięciu obiektów blob platformy Azure (**queryoutputdir/000000\_0** do **queryoutputdir/000008\_0**), w obszarze domyślnego kontenera klastra Hadoop.

Aby wyświetlić rozmiar poszczególne obiekty BLOB, uruchom następujące polecenie w wierszu polecenia katalogu Hive:

    hdfs dfs -ls wasb:///queryoutputdir

Aby wyświetlić zawartość danego pliku, powiedz **000000\_0**, użyj polecenia `copyToLocal` usługi Hadoop.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> `copyToLocal` mogą być bardzo wolne dla dużych plików i nie są zalecane do użycia z nimi.  
> 
> 

Główną zaletą, że te dane znajdują się w obiekcie blob platformy Azure, można eksplorować dane w Machine Learning przy użyciu modułu [Importuj dane][import-data] .

## <a name="#downsample"></a>Przykładowe dane i modele kompilacji w Machine Learning
> [!NOTE]
> Zazwyczaj jest to zadanie naukowego przetwarzania danych.
> 
> 

Po fazie analizy danych poznawczych możemy teraz przystąpić do obniżenie częstotliwości próbkowania danych przeznaczone do budowania modeli w usłudze Machine Learning. W tej sekcji przedstawiono, jak korzystać z zapytania programu Hive obniżenie częstotliwości próbkowania danych. Machine Learning następnie uzyskuje dostęp do niego z modułu [Importuj dane][import-data] .

### <a name="down-sampling-the-data"></a>Dół próbkowanie danych
Istnieją dwa kroki w tej procedurze. Najpierw dołączymy tabele **nyctaxidb. Trip** i **nyctaxidb. opłaty** za trzy klucze, które znajdują się we wszystkich rekordach: **Medallion**, **haker\_License**i **\_DateTime**. Następnie generujemy binarną etykietę klasyfikacji, **przechyloną**i wieloklasową etykietę klasyfikacji, **\_Porada**.

Aby można było korzystać z danych z próbką w dół bezpośrednio z modułu [Importuj dane][import-data] w Machine Learning, należy przechowywać wyniki poprzedniego zapytania w wewnętrznej tabeli programu Hive. W poniżej utworzymy wewnętrznej tabeli programu Hive i wypełnić jego zawartość z danymi połączone i próbkowana w dół.

Zapytanie stosuje standardowe funkcje programu Hive bezpośrednio, aby generować następujące parametry czasu z pola **\_DateTime** :
- godziny dnia
- tydzień roku
- dzień tygodnia ("1" oznacza poniedziałek, a "7" oznacza niedzielę)

Zapytanie generuje również bezpośrednie odległość między lokalizacjami pobrania i dropoff. Aby zapoznać się z pełną listą takich funkcji, zobacz [LANGUAGEMANUAL UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

Następnie w dół — przykłady zapytań danych tak, aby wyniki zapytania można dopasować do usługi Azure Machine Learning Studio. Tylko przez około 1 procent oryginalnego zestawu danych jest importowany do programu studio.

Poniżej przedstawiono zawartość **przykładu\_hive\_przygotuj\_dla\_aml\_Full. HQL** pliku, który przygotowuje dane do kompilowania modelu w Machine Learning:

        set R = 3959;
        set pi=radians(180);

        create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
        )
        row format delimited fields terminated by ','
        lines terminated by '\n'
        stored as textfile;

        --- now insert contents of the join into the above internal table

        insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
        select
        t.medallion,
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        t.direct_distance,
        f.payment_type,
        f.fare_amount,
        f.surcharge,
        f.mta_tax,
        f.tip_amount,
        f.tolls_amount,
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

        from
        (
        select
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
        ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
        rand() as sample_key

        from nyctaxidb.trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select
        medallion,
        hack_license,
        vendor_id,
        pickup_datetime,
        payment_type,
        fare_amount,
        surcharge,
        mta_tax,
        tip_amount,
        tolls_amount,
        total_amount
        from nyctaxidb.fare
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01

Aby uruchomić to zapytanie w wierszu polecenia katalogu Hive:

    hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

Mamy teraz wewnętrzną tabelę **nyctaxidb. nyctaxi_downsampled_dataset**, do której można uzyskać dostęp za pomocą modułu [Import danych][import-data] z Machine Learning. Ponadto możemy użyć tego zestawu danych do tworzenia modeli usługi Machine Learning.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>Użyj modułu importu danych w usłudze Machine Learning na dostęp do danych próbkowana w dół
Aby wystawić zapytania Hive w module [importu danych][import-data] Machine Learning, musisz mieć dostęp do Machine Learning obszaru roboczego. Należy również dostęp do poświadczeń klastra i jego skojarzonego konta magazynu.

Poniżej przedstawiono niektóre szczegóły dotyczące modułu [importowania danych][import-data] i parametrów do wprowadzenia:

**Identyfikator URI serwera HCatalog**: Jeśli nazwa klastra to **abc123**, użyj: https://abc123.azurehdinsight.net.

**Nazwa konta użytkownika usługi Hadoop**: Nazwa użytkownika wybrana dla klastra (nie nazwa użytkownika dostępu zdalnego).

**Hasło konta użytkownika usługi Hadoop**: hasło wybrane dla klastra (nie hasło dostępu zdalnego).

**Lokalizacja danych wyjściowych**: wybrano jako platformę Azure.

**Nazwa konta usługi Azure Storage**: Nazwa domyślnego konta magazynu skojarzonego z klastrem.

**Nazwa kontenera platformy Azure**: domyślna nazwa kontenera dla klastra i jest zwykle taka sama jak nazwa klastra. W przypadku klastra o nazwie **abc123**nazwa to abc123.

> [!IMPORTANT]
> Każda tabela, do której chcemy wykonać zapytanie, przy użyciu modułu [Importuj dane][import-data] w Machine Learning musi być tabelą wewnętrzną.
> 
> 

Oto jak ustalić, czy tabela **T** w bazie danych **D. DB** jest tabelą wewnętrzną. W wierszu polecenia katalogu programu Hive uruchom następujące polecenie:

    hdfs dfs -ls wasb:///D.db/T

Jeśli tabela jest wewnętrznej tabeli i jest on wypełniany, jego zawartość musi być wyświetlana w tym miejscu.

Innym sposobem ustalenia, czy tabela jest wewnętrznej tabeli jest użyć Eksploratora usługi Azure Storage. Umożliwia ona przejdź do kontenera domyślną nazwę klastra, a następnie filtrować dane według nazwy tabeli. Jeśli w tabeli i jego zawartość widoczne, będzie to potwierdzenie jest wewnętrznej tabeli.

Poniżej znajduje się zrzut ekranu zapytania programu Hive i modułu [Importuj dane][import-data] :

![Zrzut ekranu z zapytania programu Hive w dla modułu importu danych](./media/hive-walkthrough/1eTYf52.png)

Ponieważ nasze dane w dalszej części znajdują się w domyślnym kontenerze, powstaje zapytanie Hive z Machine Learning jest proste. Jest to tylko **wybór * z nyctaxidb. nyctaxi\_próbkowane\_dane**.

Zestaw danych może teraz służyć jako punktu wyjścia do tworzenia modeli usługi Machine Learning.

### <a name="mlmodel"></a>Tworzenie modeli w Machine Learning
Teraz możesz przystąpić do tworzenia modeli i wdrażania modelu w [Machine Learning](https://studio.azureml.net). Danych jest gotowa do użycia w zakresie rozwiązywania problemów prognozowania wymienionych wcześniej:

- **Klasyfikacja binarna**: w celu przewidywania, czy Porada została zapłacona za podróż.

  **Używany przez Ciebie opis:** Regresja logistyczna dla dwóch klas

  a. W przypadku tego problemu, etykieta docelowa (lub klasy) jest wyrzucana. Oryginalnego zestawu danych próbkowana w dół ma kilka kolumn, które są przecieki docelowego w tym eksperymencie klasyfikacji. W szczególności **\_Porada, Klasa**, **\_kwota**i **łączna kwota\_** ujawniają informacje o etykiecie docelowej, która nie jest dostępna podczas testowania. Te kolumny są usuwane z rozważenia przy użyciu modułu [SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych][select-columns] ).

  Na poniższym diagramie przedstawiono naszych eksperymentu w celu przewidywania, czy porady zapłacono komunikacji dwustronnej danego:

  ![Diagram przedstawiający eksperymentu w celu przewidywania, jeśli została zapłacona Porada](./media/hive-walkthrough/QGxRz5A.png)

  b. W tym eksperymencie naszych dystrybucje etykiety docelowej zostały około 1:1.

   Na poniższym wykresie przedstawiono rozkład Porada klasy etykiet dla problemu klasyfikacji binarnej:

  ![Wykres rozkładu Porada klasy etykiet](./media/hive-walkthrough/9mM4jlD.png)

    W rezultacie uzyskany powierzchni pod krzywą (AUC) 0.987, jak pokazano na poniższej ilustracji:

  ![Wykres przedstawiający AUC wartość](./media/hive-walkthrough/8JDT0F8.png)

- **Klasyfikacja wieloklasowa**: aby przewidzieć zakres kwot TIP płatnych za podróż przy użyciu wcześniej zdefiniowanych klas.

  **Używany przez Ciebie opis:** Wieloklasowa regresja logistyczna

  a. W przypadku tego problemu, nasza etykieta docelowa (lub Klasa) to **Porada\_Klasa**, która może przyjmować jedną z pięciu wartości (0, 1, 2, 3, 4). Tak jak w przypadku klasyfikacji binarnej mamy kilka kolumn, które są przecieki docelowego w tym eksperymencie. W szczególności przerzucana, **Porada\_kwota**i **łączna kwota\_** ujawniać informacje o etykiecie **docelowej, która**nie jest dostępna podczas testowania. Te kolumny zostaną usunięte przy użyciu modułu [SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych][select-columns] ).

  Na poniższym diagramie przedstawiono eksperymentu w celu przewidywania, w których bin Porada jest mogącego wchodzić. Pojemniki to: klasy 0: Porada = 0 zł, klasy 1: Porada > 0 USD i Porada < = 5 USD; klasy 2: Porada > 5 USD i Porada < = 10 USD, klasy 3: Porada > w wysokości 10 USD i Porada < = 20 USD i klasy 4: Porada > 20 USD.

  ![Diagram przedstawiający eksperymentu do prognozowania pojemnika dla wskazówki](./media/hive-walkthrough/5ztv0n0.png)

  Teraz pokazujemy, jak wygląda rozkład klasy rzeczywiste testu. Klasa 0 i 1 klasy są powszechnie znane i innych klas są rzadkie.

  ![Wykres rozkładu klasy testu](./media/hive-walkthrough/Vy1FUKa.png)

  b. Na potrzeby tego eksperymentu używamy niezmienionej macierzy, aby przyjrzeć się dokładnościom przewidywania, jak pokazano poniżej:

  ![Macierz pomyłek](./media/hive-walkthrough/cxFmErM.png)

  Chociaż nieścisłości klas w przypadku znanych klas są dobre, model nie wykonuje dobrego zadania "Uczenie się" na klasach rzadkich.

- **Zadanie regresji**: przewidywanie kwoty Porada płatnej dla podróży.

  **Używany przez Ciebie opis:** Drzewo podwyższanych decyzji

  a. W przypadku tego problemu etykieta Target (lub Class) jest **Porada\_kwota**. W takim przypadku wycieki docelowe są następujące: **przechylona**, **\_a Klasa**i **łączna kwota\_** . Te zmienne ujawniać informacje o ilości porady, które są zwykle niedostępne na czas testowania. Te kolumny zostaną usunięte przy użyciu modułu [SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych][select-columns] ).

  Na poniższym diagramie przedstawiono eksperymentu do prognozowania kwotę napiwku danego:

  ![Diagram przedstawiający eksperymentu do prognozowania ilość Porada](./media/hive-walkthrough/11TZWgV.png)

  b. W przypadku problemów z regresji mierzymy dokładności przewidywania patrząc pierwiastek błędu w prognoz i współczynnika determinacji:

  ![Zrzut ekranu przedstawiający statystyki prognoz](./media/hive-walkthrough/Jat9mrz.png)

  W tym miejscu współczynnika determinacji jest 0.709, co oznacza, że około 71 procent wariancji tłumaczy współczynniki modelu.

> [!IMPORTANT]
> Aby dowiedzieć się więcej na temat Machine Learning i sposobu uzyskiwania dostępu do niego i używania go, zobacz [co to jest Machine Learning](../studio/what-is-machine-learning.md). Ponadto [Azure AI Gallery](https://gallery.cortanaintelligence.com/) obejmuje gamę eksperymentów i zapewnia szczegółowe wprowadzenie do zakresu możliwości Machine Learning.
> 
> 

## <a name="license-information"></a>Informacje o licencji
Ten przewodnik przykładowy i jego towarzyszący skrypty są udostępniane przez firmę Microsoft na licencji MIT. Aby uzyskać więcej informacji, zobacz plik **License. txt** w katalogu przykładowego kodu w witrynie GitHub.

## <a name="references"></a>Dokumentacja
• [Strona pobierania Andrés MONROY NYC TRIPS](https://www.andresmh.com/nyctaxitrips/)  
• [Dane dotyczące podróży z NYCą w folii przez Krzysztof Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [Badania i statystyka NYCych taksówki oraz Komisji Limousine](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/



