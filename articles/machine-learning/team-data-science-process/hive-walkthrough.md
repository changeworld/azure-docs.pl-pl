---
title: Eksplorowanie danych w klastrze usługi Hadoop — zespołu danych dla celów naukowych
description: Scenariusz end-to-end, wykorzystujące klastra usługi HDInsight Hadoop, aby skompilować i wdrożyć model, za pomocą procesie nauki o danych zespołu.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/29/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d26bc6044ca106b0f081cee5a39405b4b78ce7ac
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60303970"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>Zespół danych dla celów naukowych w działaniu: Korzystanie z klastrów Azure HDInsight Hadoop
W tym przewodniku używamy [Team Data Science naukowych](overview.md) w scenariuszu end-to-end. Używamy [klastra Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) przechowywać, eksplorować, inżynier ds. funkcji dane i z publicznie dostępnych [rund taksówek NYC](https://www.andresmh.com/nyctaxitrips/) zestawu danych i obniżenie częstotliwości próbkowania danych. Aby obsłużyć binarne i wieloklasowej klasyfikacji i regresji predykcyjnego zadań, firma Microsoft tworzy modele danych przy użyciu usługi Azure Machine Learning. 

Aby uzyskać wskazówki, który pokazuje, jak obsłużyć większy zestaw danych, zobacz [zespołu danych dla celów naukowych — przy użyciu usługi Azure HDInsight klastrów Hadoop w zestawie 1 TB](hive-criteo-walkthrough.md).

Program IPython notebook służy również do wykonywania zadań przedstawionych w instruktażu, który używa zestawu danych 1 TB. Aby uzyskać więcej informacji, zobacz [wskazówki Criteo przy użyciu połączenia ODBC programu Hive](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).

## <a name="dataset"></a>Opis zestawu danych podróży taksówek NYC
Dane podróży taksówek NYC to około 20 GB plików skompresowanych wartości rozdzielanych przecinkami (CSV) (~ 48 GB nieskompresowane). Ma ponad milion 173 poszczególnych podróży i obejmuje opłaty za każdy podróży. Każdy podróży rekord zawiera odbiór i dropoff lokalizacji i czasu, hack anonimowe (sterownika) numer licencji i numer Medalionu (taksówek Unikatowy identyfikator). Dane obejmuje wszystkie podróży w roku 2013 i znajduje się w następujących dwóch zestawów danych w każdym miesiącu:

- Pliki CSV trip_data zawierają szczegóły podróży. W tym liczba osób, odbiór i dropoff punkty, czasu trwania podróży i długość podróży. Poniżej przedstawiono kilka przykładowych rekordów:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
- Pliki CSV trip_fare zawierają szczegółowe informacje o klasie opłacony każdego podróży. Obejmuje to typ płatności, kwota taryfy, opłata za opcję i podatków, porady i drogi, a łączna kwota płatności. Poniżej przedstawiono kilka przykładowych rekordów:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Unikatowy klucz, aby dołączyć podróży\_danych i podróży\_taryfy składa się z pól: Medalionu hakowanie\_licencji i odbiór\_daty/godziny. Aby uzyskać wszystkie szczegóły dotyczą konkretnego podróży, jest wystarczające, aby przyłączyć się te trzy klucze.

## <a name="mltasks"></a>Przykłady zadań prognoz
Określa rodzaj prognoz, które mają być oparty na analizie danych. Dzięki temu można wyjaśnić zadania, które należy uwzględnić w procesie. Poniżej przedstawiono trzy przykłady problemów prognoz, które można rozwiązać w tym przewodniku. Są one oparte na *Porada\_kwota*:

- **Klasyfikacja binarna**: Przewidywania, czy porady zapłacono komunikacji dwustronnej. Oznacza to *Porada\_kwota* większą niż 0 zł jest przykładem dodatnią, podczas gdy *Porada\_kwota* wynosi 0 zł jest przykładem ujemna.
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0
- **Klasyfikacji wieloklasowej**: Przewidywanie zakres kwoty Porada opłacony podróż. Możemy podzielić *Porada\_kwota* na pięć klasy:
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0 and tip_amount <= $5
        Class 2: tip_amount > $5 and tip_amount <= $10
        Class 3: tip_amount > $10 and tip_amount <= $20
        Class 4: tip_amount > $20
- **Zadanie regresji**: Przewidywanie kwotę napiwku płatnych komunikacji dwustronnej.  

## <a name="setup"></a>Konfigurowanie klastra usługi HDInsight Hadoop, zaawansowanej analizy
> [!NOTE]
> Zazwyczaj jest zadaniem administracyjnym.
> 
> 

Możesz skonfigurować środowisko platformy Azure na potrzeby zaawansowanej analizy, korzystającej z klastra usługi HDInsight w trzech krokach:

1. [Tworzenie konta magazynu](../../storage/common/storage-quickstart-create-account.md): To konto magazynu jest używane do przechowywania danych w usłudze Azure Blob storage. Dane używane w klastrach HDInsight znajduje się także w tym miejscu.
2. [Dostosowywanie klastrów usługi Azure HDInsight Hadoop dla Advanced Analytics Process and Technology](customize-hadoop-cluster.md). Ten krok umożliwia utworzenie klastra usługi HDInsight Hadoop za pomocą 64-bitowego pakietu Anaconda Python 2.7 zainstalowane we wszystkich węzłach. Istnieją dwie ważne czynności, należy pamiętać podczas dostosowywania klastra usługi HDInsight.
   
   * Pamiętaj, aby połączyć konto usługi storage utworzonego w kroku 1 z klastrem usługi HDInsight, podczas jego tworzenia. To konto magazynu ma dostęp do danych, które są przetwarzane w klastrze.
   * Po utworzeniu klastra, należy włączyć dostęp zdalny z węzłem głównym klastra. Przejdź do **konfiguracji** , a następnie wybierz pozycję **Włączanie zdalnego**. Ten krok Określa poświadczenia użytkownika służące do logowania zdalnego.
3. [Tworzenie obszaru roboczego usługi Azure Machine Learning](../studio/create-workspace.md): Ten obszar roboczy służy do tworzenia modeli uczenia maszynowego. To zadanie jest skierowana po wypełnieniu eksploracji danych początkowych i w dół — w przypadku próbkowania przy użyciu klastra HDInsight.

## <a name="getdata"></a>Pobierz dane ze źródła publiczne
> [!NOTE]
> Zazwyczaj jest zadaniem administracyjnym.
> 
> 

Aby skopiować [rund taksówek NYC](https://www.andresmh.com/nyctaxitrips/) zestawu danych na komputerze w lokalizacji publicznej użyć dowolnej z metod opisanych w [przenoszenie danych do i z usługi Azure Blob storage](move-azure-blob.md).

W tym miejscu opisano sposób użycia narzędzia AzCopy do transferu plików zawierających dane. Aby pobrać i zainstalować narzędzia AzCopy, postępuj zgodnie z instrukcjami w artykule [wprowadzenie do narzędzia wiersza polecenia AzCopy](../../storage/common/storage-use-azcopy.md).

1. W oknie wiersza polecenia, uruchom następujące polecenia narzędzia AzCopy, zastępując  *\<path_to_data_folder >* z docelowej lokalizacji:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. Po zakończeniu kopiowania, zobaczysz łącznie 24 skompresowane pliki z folderu danych wybrana. Rozpakuj pobranych plików, w tym samym katalogu na komputerze lokalnym. Zanotuj folder, w którym znajdują się pliki nieskompresowane. Ten folder jest określany jako *\<ścieżki\_do\_unzipped_data\_pliki\>* w poniżej.

## <a name="upload"></a>Przekaż dane do domyślnego kontenera klastra usługi HDInsight Hadoop
> [!NOTE]
> Zazwyczaj jest zadaniem administracyjnym.
> 
> 

Następujące polecenia narzędzia AzCopy, należy zastąpić następujące parametry przy użyciu rzeczywistych wartości, które zostały określone podczas tworzenia klastra usługi Hadoop i Rozpakowywanie plików danych.

* ***\<path_to_data_folder >*** katalogu (wraz ze ścieżką) na komputerze, który zawiera pliki danych rozpakowany.  
* ***\<Nazwa konta magazynu klastra Hadoop >*** konto magazynu skojarzone z klastrem usługi HDInsight.
* ***\<domyślny kontener klastra Hadoop >*** domyślny kontener używane przez klaster. Należy pamiętać, że nazwa domyślnego kontenera zwykle taką samą nazwę jak samego klastra. Na przykład jeśli klaster jest nazywany "abc123.azurehdinsight.net", domyślny kontener jest abc123.
* ***\<klucz konta magazynu >*** klucz dla konta magazynu używanego przez klaster.

Z wiersza polecenia lub okno programu Windows PowerShell Uruchom dwa poniższe polecenia narzędzia AzCopy.

To polecenie przesyła dane podróży do ***nyctaxitripraw*** katalogu w domyślnym kontenerze klastra Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

To polecenie przesyła dane taryfy ***nyctaxifareraw*** katalogu w domyślnym kontenerze klastra Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Dane powinno być teraz w usłudze Blob storage i gotowe do użycia w klastrze HDInsight.

## <a name="#download-hql-files"></a>Zaloguj się do węzła głównego klastra usługi Hadoop i przygotować się do analizy danych eksploracyjne
> [!NOTE]
> Zazwyczaj jest zadaniem administracyjnym.
> 
> 

Dostęp do węzłem głównym klastra na potrzeby analizy danych poznawcze i w dół pobierania próbek danych, wykonaj procedury opisane w temacie [dostęp do węzła głównego klastra usługi Hadoop](customize-hadoop-cluster.md).

W tym przewodniku używamy przede wszystkim napisane w zapytania [Hive](https://hive.apache.org/), język zapytań przypominający SQL, wykonaj wstępne dane eksploracji. Zapytania programu Hive są przechowywane w plikach .hql. Firma Microsoft następnie obniżenie częstotliwości próbkowania tych danych do użycia w ramach uczenia maszynowego do tworzenia modeli.

Aby przygotować klaster do analizy danych poznawczych, pobierać pliki .hql, zawierających odpowiednie skrypty Hive z [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) do katalogu lokalnego (C:\temp) w węźle głównym. Aby to zrobić, otwórz wiersz polecenia z w obrębie węzła głównego klastra i uruchom dwa poniższe polecenia:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Te dwa polecenia Pobierz wszystkie pliki .hql potrzebne w tym przewodniku do katalogu lokalnego ***C:\temp&#92;***  w węzła głównego.

## <a name="#hive-db-tables"></a>Tworzenie bazy danych programu Hive i tabel na partycje według miesiąca
> [!NOTE]
> Zazwyczaj jest zadaniem administracyjnym.
> 
> 

Teraz możesz przystąpić do tworzenia tabel programu Hive dla zestawu danych taksówek NYC.
Węzeł główny klastra usługi Hadoop Otwórz wiersza polecenia usługi Hadoop na pulpicie węzła głównego. Wprowadź katalog gałęzi, uruchamiając następujące polecenie:

    cd %hive_home%\bin

> [!NOTE]
> Uruchom wszystkie polecenia gałąź w tym przewodniku z Hive bin / directory wiersza. Automatycznie obsługuje wszystkie ścieżce problemy. Używane pojęcia "Gałęzi katalogu wiersz", "Hive bin / directory wiersz" i "Wiersza polecenia usługi Hadoop" zamiennie w tym przewodniku.
> 
> 

W wierszu polecenia katalogu programu Hive uruchom następujące polecenie w wierszu polecenia Hadoop węzła głównego. Przesyła zapytanie programu Hive w celu utworzenia bazy danych Hive i tabele:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Poniżej przedstawiono zawartość **C:\temp\sample\_hive\_tworzenie\_db\_i\_tables.hql** pliku. Spowoduje to utworzenie bazy danych Hive **nyctaxidb**, tabelach i **podróży** i **taryfy**.

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

* **Podróży** tabela zawiera szczegóły podróży każdy jazdy (Szczegóły sterownika, czas odbiór, odległość podróży i razy).
* **Taryfy** tabela zawiera szczegóły taryfy (kwota taryfy, kwota poradę, drogi i dopłaty).

Jeśli potrzebujesz uzyskania dodatkowej pomocy, przy użyciu tych procedur lub chcesz zbadać te alternatywnych, zobacz sekcję [przesłać Hive zapytania bezpośrednio z poziomu wiersza polecenia usługi Hadoop](move-hive-tables.md#submit).

## <a name="#load-data"></a>Ładowanie danych do tabel programu Hive przez partycje
> [!NOTE]
> Zazwyczaj jest zadaniem administracyjnym.
> 
> 

Zestaw danych taksówek NYC ma naturalny partycjonowanie według miesiąca, które firma Microsoft umożliwiają szybsze przetwarzanie i zapytania. Następujące polecenia programu PowerShell (wydać z katalogu programu Hive przy użyciu wiersza polecenia usługi Hadoop) ładowanie danych do wyzwolenie i klasie tabel programu Hive, podzielone na partycje według miesiąca.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

**Przykładowe\_hive\_obciążenia\_danych\_przez\_partitions.hql** pliku zawiera następujące **obciążenia** poleceń:

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Należy pamiętać, że liczba zapytań programu Hive, użyć w tym miejscu procesu eksploracji obejmują spojrzenie na tylko jedną lub dwie partycje. Jednak te zapytania mogą być uruchamiane na całym zestawie danych.

### <a name="#show-db"></a>Wyświetl bazy danych w klastrze usługi HDInsight Hadoop
Aby wyświetlić bazy danych utworzone w klastrze usługi HDInsight Hadoop, w oknie wiersza polecenia usługi Hadoop, uruchom następujące polecenie w wierszu polecenia usługi Hadoop:

    hive -e "show databases;"

### <a name="#show-tables"></a>Pokaż tabele programu Hive w **nyctaxidb** bazy danych
Aby wyświetlić tabele w **nyctaxidb** bazy danych, uruchom następujące polecenie w wierszu polecenia usługi Hadoop:

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

## <a name="#explore-hive"></a>Eksplorowanie danych i inżynieria funkcji w gałęzi
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

Rekordy można zapisać do pliku w celu wyświetlania wygodne. Niewielkie zmiany do poprzedniego zapytania rozwiązanie to:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Eksploracja: Wyświetlanie liczby rekordów w każdej z 12 partycjami
> [!NOTE]
> Zazwyczaj jest to zadanie naukowego przetwarzania danych.
> 
> 

Odsetek jest jak Liczba podróży zmienia się w roku kalendarzowym. Grupowanie według miesięcy, pokazuje rozkład podróży.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Daje następujące wyniki:

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

Daje to:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Za pomocą poleceń, podobnie jak te wyświetlane dla zestawu danych podróży, firma Microsoft może wystawiać zapytań programu Hive w wierszu polecenia programu Hive katalogu dla zestawu danych taryfy do sprawdzania poprawności liczby rekordów.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Daje następujące wyniki:

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

Należy pamiętać, że ten sam dokładną liczbę rund miesięcznie jest zwracany w przypadku obu zestawów danych. Zapewnia to pierwsze sprawdzenie, czy dane zostały załadowane prawidłowo.

Całkowita liczba rekordów w zestawie danych klasie może być traktowane w przy użyciu następującego polecenia w wierszu polecenia katalogu Hive:

    hive -e "select count(*) from nyctaxidb.fare;"

Daje to:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

Całkowita liczba rekordów w obu tabelach jest również. Zapewnia to drugi weryfikacji, czy dane zostały załadowane prawidłowo.

### <a name="exploration-trip-distribution-by-medallion"></a>Eksploracja: Dystrybucja podróży według Medalionu
> [!NOTE]
> Zazwyczaj jest to zadanie naukowego przetwarzania danych.
> 
> 

W tym przykładzie identyfikuje medallions (numery taksówek) o rozmiarze większym niż 100 podróży w danym okresie czasu. Zapytanie korzyści z dostępu do tabeli partycjonowanej, ponieważ zmienna partycji należy przygotować **miesiąca**. Wyniki zapytania są zapisywane w pliku lokalnym **queryoutput.tsv**w `C:\temp` w węźle głównym.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Poniżej przedstawiono zawartość **przykładowe\_hive\_podróży\_liczba\_przez\_medallion.hql** pliku do inspekcji.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Medalionu w zestawie danych taksówek NYC identyfikuje unikatowy pliku cab. Można zidentyfikować, które pliki cab są stosunkowo zajęte przez zadanie, które z nich wprowadzone więcej niż określoną liczbę rund w określonym przedziale czasu. Poniższy przykład określa pliki cab, wprowadzone więcej niż sto podróży w pierwsze trzy miesiące, a następnie zapisuje wyniki zapytania do pliku lokalnego **C:\temp\queryoutput.tsv**.

Poniżej przedstawiono zawartość **przykładowe\_hive\_podróży\_liczba\_przez\_medallion.hql** pliku do inspekcji.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

W wierszu polecenia katalogu programu Hive uruchom następujące polecenie:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Eksploracja: Dystrybucja podróży według Medalionu i hack licencji
> [!NOTE]
> Zazwyczaj jest to zadanie naukowego przetwarzania danych.
> 
> 

Podczas eksplorowania zestawu danych, często chcemy sprawdzić liczbę wystąpień wspólnej grupy wartości. Ta sekcja zawiera przykład sposobu wykonania tego zadania dla plików cab i sterowników.

**Przykładowe\_hive\_podróży\_liczba\_przez\_Medalionu\_license.hql** dataset taryfy grup plików na **Medalionu** i **hack_license**i zwraca liczbę dla każdej kombinacji. Oto jego zawartość:

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Ta kwerenda zwraca kombinacji pliku cab i sterowników, uporządkowane według malejącej liczbę rund.

W wierszu polecenia katalogu programu Hive Uruchom polecenie:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

Wyniki zapytania są zapisywane w pliku lokalnym **C:\temp\queryoutput.tsv**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Eksploracja: Ocena jakości danych przez sprawdzenie, czy nieprawidłowe rekordy współrzędnych geograficznych
> [!NOTE]
> Zazwyczaj jest to zadanie naukowego przetwarzania danych.
> 
> 

Typowe celem analizy danych eksploracyjnego jest chwastów się nieprawidłowe lub uszkodzone rekordów. W przykładzie w tej sekcji określa, czy długość geograficzna lub szerokość pola zawierają wartość daleko poza obszarem NYC. Ponieważ jest to prawdopodobnie, że takie rekordy mają wartość błędne szerokości geograficznej, chcemy je wyeliminować wszelkie dane, które ma być używane do modelowania.

Poniżej przedstawiono zawartość **przykładowe\_hive\_jakości\_assessment.hql** pliku do inspekcji.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


W wierszu polecenia katalogu programu Hive Uruchom polecenie:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

*-S:* argument zawarte w tym poleceniu pomija wydruk ekranu stanu zadań Hive Map/Reduce. Jest to przydatne, ponieważ sprawia, że ekran wydruku danych wyjściowych zapytania programu Hive bardziej czytelne.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Eksploracja: Klasa binarne dystrybucje porad podróży
> [!NOTE]
> Zazwyczaj jest to zadanie naukowego przetwarzania danych.
> 
> 

Dla problemu klasyfikacji binarnej, opisane w temacie [przykłady zadań prognozowania](hive-walkthrough.md#mltasks) sekcji, warto wiedzieć, czy porady przekazano lub nie. Tej dystrybucji porady jest binarny:

* Porada podane (klasy 1, porada\_kwota > 0 USD)  
* nie tip (klasy 0, porada\_kwota = 0 USD)

Następujące **przykładowe\_hive\_Przechylony\_frequencies.hql** pliku robi to:

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

Problem klasyfikacji wieloklasowej opisane w temacie [przykłady zadań prognozowania](hive-walkthrough.md#mltasks) sekcji tego zestawu danych również jest przydatna do naturalnym klasyfikacji do prognozowania ilość porady, biorąc pod uwagę. Możemy użyć pojemniki do definiowania zakresów Porada w zapytaniu. Aby uzyskać dystrybucje klasy dla różnych Porada zakresów, użyj **przykładowe\_hive\_Porada\_zakresu\_frequencies.hql** pliku. Oto jego zawartość.

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

Aby zobaczyć porównanie odległość rzeczywiste podróży i [odległość Haversine](https://en.wikipedia.org/wiki/Haversine_formula) dostępne funkcje trygonometryczne między dwoma punktami szerokości geograficzne ("koła" wielkiego), można użyć w ramach gałęzi:

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

W poprzednim zapytaniu R to promień ziemi w milach i pi jest konwertowana na wartość w radianach. Należy pamiętać, że punkty szerokości geograficznej są filtrowane, aby usunąć wartości, które są dalekie od wartości w obszarze NYC.

W tym przypadku pisania wyniki katalog o nazwie **queryoutputdir**. Sekwencja poniższe polecenia najpierw tworzy ten katalog wyjściowy, a następnie uruchamia polecenia programu Hive.

W wierszu polecenia katalogu programu Hive Uruchom polecenie:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


Wyniki zapytania są zapisywane w dziewięciu obiekty BLOB platformy Azure (**queryoutputdir/000000\_0** do **queryoutputdir/000008\_0**), w ramach kontenera domyślnego klastra usługi Hadoop.

Aby wyświetlić rozmiar poszczególne obiekty BLOB, uruchom następujące polecenie w wierszu polecenia katalogu Hive:

    hdfs dfs -ls wasb:///queryoutputdir

Aby wyświetlić zawartość określonego pliku, powiedz **000000\_0**, korzystanie z usługi Hadoop firmy `copyToLocal` polecenia.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> `copyToLocal` mogą być bardzo wolne dla dużych plików, a nie jest zalecany do używania z nimi.  
> 
> 

Kluczową zaletą o te dane, które znajdują się w usłudze Azure blob to firma Microsoft na Eksplorowanie danych w ramach usługi Machine Learning, za pomocą [importu danych] [ import-data] modułu.

## <a name="#downsample"></a>Obniżenie częstotliwości próbkowania danych i tworzyć modele w usłudze Machine Learning
> [!NOTE]
> Zazwyczaj jest to zadanie naukowego przetwarzania danych.
> 
> 

Po fazie analizy danych poznawczych możemy teraz przystąpić do obniżenie częstotliwości próbkowania danych przeznaczone do budowania modeli w usłudze Machine Learning. W tej sekcji przedstawiono, jak korzystać z zapytania programu Hive obniżenie częstotliwości próbkowania danych. Następnie usługi Machine Learning korzysta z [importu danych] [ import-data] modułu.

### <a name="down-sampling-the-data"></a>Dół próbkowanie danych
Istnieją dwa kroki w tej procedurze. Firma Microsoft przyłączyć **nyctaxidb.trip** i **nyctaxidb.fare** tabel na trzy klucze, które znajdują się we wszystkich rekordach: **Medalionu**, **hack\_ Licencja**, i **odbioru\_daty/godziny**. Firma Microsoft następnie wygeneruj etykietę klasyfikacji binarnej **Przechylony**i etykietę klasyfikacji wieloklasowej **Porada\_klasy**.

Aby można było użyć próbkowana w dół danych bezpośrednio z [importu danych] [ import-data] modułu w usłudze Machine Learning, należy przechowywać wyniki poprzedniego zapytania do wewnętrznej tabeli programu Hive. W poniżej utworzymy wewnętrznej tabeli programu Hive i wypełnić jego zawartość z danymi połączone i próbkowana w dół.

Zapytania mają zastosowanie standardowe funkcje gałęzi bezpośrednio, aby wygenerować następujących poleceń w **odbioru\_daty/godziny** pola:
- godziny dnia
- tydzień roku
- dzień tygodnia (stoisk 1 dla kalendarza poniedziałek i 7 stoisk dla kalendarza niedziela)

Zapytanie generuje również bezpośrednie odległość między lokalizacjami pobrania i dropoff. Aby uzyskać pełną listę takich funkcji, zobacz [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

Następnie w dół — przykłady zapytań danych tak, aby wyniki zapytania można dopasować do usługi Azure Machine Learning Studio. Tylko przez około 1 procent oryginalnego zestawu danych jest importowany do programu studio.

Poniżej przedstawiono zawartość **przykładowe\_hive\_przygotowanie\_dla\_aml\_full.hql** pliku, który przygotowuje dane do modelu tworzenia w usłudze Machine Learning:

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

W efekcie powstał wewnętrznej tabeli **nyctaxidb.nyctaxi_downsampled_dataset**, który jest możliwy za pomocą [importu danych] [ import-data] modułu na podstawie usługi Machine Learning. Ponadto możemy użyć tego zestawu danych do tworzenia modeli usługi Machine Learning.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>Użyj modułu importu danych w usłudze Machine Learning na dostęp do danych próbkowana w dół
Do wysyłania zapytań programu Hive w [importu danych] [ import-data] modułów usługi Machine Learning, musisz mieć dostęp do obszaru roboczego uczenia maszynowego. Należy również dostęp do poświadczeń klastra i jego skojarzonego konta magazynu.

Poniżej przedstawiono niektóre szczegóły dotyczące [importu danych] [ import-data] moduł i parametry wejściowe:

**Identyfikator URI serwera HCatalog**: Jeśli nazwa klastra jest **abc123**, to po prostu: https://abc123.azurehdinsight.net.

**Nazwa konta użytkownika usługi Hadoop**: Nazwa użytkownika, wybrany dla klastra (nie nazwa użytkownika dostępu zdalnego).

**Hasło konta użytkownika usługi Hadoop**: Hasło dla klastra (a nie hasło dostępu zdalnego).

**Lokalizacja danych wyjściowych**: To jest wybierany jako platformy Azure.

**Nazwa konta usługi Azure storage**: Nazwa domyślnego konta magazynu skojarzonego z klastrem.

**Nazwa kontenera platformy Azure**: To jest domyślna nazwa kontenera dla klastra i jest zwykle taka sama jak nazwa klastra. W przypadku klastra o nazwie **abc123**, to wartości abc123.

> [!IMPORTANT]
> Wszystkie tabele, firma Microsoft chce wykonywać zapytania za pomocą [importu danych] [ import-data] modułu w usłudze Machine Learning musi być wewnętrznej tabeli.
> 
> 

Poniżej przedstawiono sposób określenia, czy tabela **T** w bazie danych **D.db** jest wewnętrznej tabeli. W wierszu polecenia katalogu programu Hive uruchom następujące polecenie:

    hdfs dfs -ls wasb:///D.db/T

Jeśli tabela jest wewnętrznej tabeli i jest on wypełniany, jego zawartość musi być wyświetlana w tym miejscu.

Innym sposobem ustalenia, czy tabela jest wewnętrznej tabeli jest użyć Eksploratora usługi Azure Storage. Umożliwia ona przejdź do kontenera domyślną nazwę klastra, a następnie filtrować dane według nazwy tabeli. Jeśli w tabeli i jego zawartość widoczne, będzie to potwierdzenie jest wewnętrznej tabeli.

Poniżej przedstawiono zrzut ekranu zapytania programu Hive i [importu danych] [ import-data] modułu:

![Zrzut ekranu z zapytania programu Hive w dla modułu importu danych](./media/hive-walkthrough/1eTYf52.png)

Ponieważ naszego próbkowana w dół danych znajduje się w domyślnym kontenerze, wynikowe zapytanie programu Hive z usługi Machine Learning jest bardzo proste. Jest po prostu **wybierz * z nyctaxidb.nyctaxi\_próbkowane w dół\_danych**.

Zestaw danych może teraz służyć jako punktu wyjścia do tworzenia modeli usługi Machine Learning.

### <a name="mlmodel"></a>Tworzenie modeli w usłudze Machine Learning
Teraz można przystąpić do konstruowania modelu i wdrażania modelu w [uczenia maszynowego](https://studio.azureml.net). Danych jest gotowa do użycia w zakresie rozwiązywania problemów prognozowania wymienionych wcześniej:

- **Klasyfikacja binarna**: Przewidywanie czy Porada zapłacono komunikacji dwustronnej.

  **Uczeń używane:** Regresja logistyczna dwuklasowych

  a. W przypadku tego problemu jest etykieta docelowego (lub klasy) **Przechylony**. Oryginalnego zestawu danych próbkowana w dół ma kilka kolumn, które są przecieki docelowego w tym eksperymencie klasyfikacji. W szczególności **Porada\_klasy**, **Porada\_kwota**, i **całkowita\_kwota** ujawniania informacji o docelowej etykiety, która nie jest dostępny na czas testowania. Możemy usunąć następujące kolumny pod uwagę przy użyciu [Select Columns in Dataset] [ select-columns] modułu.

  Na poniższym diagramie przedstawiono naszych eksperymentu w celu przewidywania, czy porady zapłacono komunikacji dwustronnej danego:

  ![Diagram przedstawiający eksperymentu w celu przewidywania, jeśli została zapłacona Porada](./media/hive-walkthrough/QGxRz5A.png)

  b. W tym eksperymencie naszych dystrybucje etykiety docelowej zostały około 1:1.

   Na poniższym wykresie przedstawiono rozkład Porada klasy etykiet dla problemu klasyfikacji binarnej:

  ![Wykres rozkładu Porada klasy etykiet](./media/hive-walkthrough/9mM4jlD.png)

    W rezultacie uzyskany powierzchni pod krzywą (AUC) 0.987, jak pokazano na poniższej ilustracji:

  ![Wykres przedstawiający AUC wartość](./media/hive-walkthrough/8JDT0F8.png)

- **Klasyfikacji wieloklasowej**: Do prognozowania zakresu Porada kwoty wypłacane komunikacji dwustronnej, przy użyciu uprzednio zdefiniowany klas.

  **Uczeń używane:** Wieloklasowej regresji logistycznej

  a. W przypadku tego problemu jest naszą etykietę docelowego (lub klasy) **Porada\_klasy**, które można wykonać jedną z pięciu wartości (0,1,2,3,4). Tak jak w przypadku klasyfikacji binarnej mamy kilka kolumn, które są przecieki docelowego w tym eksperymencie. W szczególności **Przechylony**, **Porada\_kwota**, i **całkowita\_kwota** ujawnić informacji na temat etykietą docelową, która nie jest dostępna w czas testowania. Możemy usunąć następujące kolumny przy użyciu [Select Columns in Dataset] [ select-columns] modułu.

  Na poniższym diagramie przedstawiono eksperymentu w celu przewidywania, w których bin Porada jest mogącego wchodzić. Pojemniki to: Klasa 0: Porada = 0 zł, klasy 1: Porada > 0 USD i Porada < = 5 USD; klasy 2: Porada > 5 USD i Porada < = 10 USD, klasy 3: Porada > w wysokości 10 USD i Porada < = 20 USD i klasy 4: Porada > 20 USD.

  ![Diagram przedstawiający eksperymentu do prognozowania pojemnika dla wskazówki](./media/hive-walkthrough/5ztv0n0.png)

  Teraz pokazujemy, jak wygląda rozkład klasy rzeczywiste testu. Klasa 0 i 1 klasy są powszechnie znane i innych klas są rzadkie.

  ![Wykres rozkładu klasy testu](./media/hive-walkthrough/Vy1FUKa.png)

  b. W tym eksperymencie używamy macierz pomyłek do wzięcia pod dokładności prognozy. Jest to pokazane poniżej:

  ![Macierz pomyłek](./media/hive-walkthrough/cxFmErM.png)

  Należy pamiętać, że dokładności klasy na klasy powszechnie znane są bardzo dobre, model nie działa Dobra robota "usługi" w klasach rzadkich.

- **Zadanie regresji**: Przewidywanie ilość Porada płatne komunikacji dwustronnej.

  **Uczeń używane:** wzmocnione drzewo decyzyjnego

  a. W przypadku tego problemu jest etykieta docelowego (lub klasy) **Porada\_kwota**. Przecieki docelowego w tym przypadku są: **Przechylony**, **Porada\_klasy**, i **całkowita\_kwota**. Te zmienne ujawniać informacje o ilości porady, które są zwykle niedostępne na czas testowania. Możemy usunąć następujące kolumny przy użyciu [Select Columns in Dataset] [ select-columns] modułu.

  Na poniższym diagramie przedstawiono eksperymentu do prognozowania kwotę napiwku danego:

  ![Diagram przedstawiający eksperymentu do prognozowania ilość Porada](./media/hive-walkthrough/11TZWgV.png)

  b. W przypadku problemów z regresji mierzymy dokładności przewidywania patrząc pierwiastek błędu w prognoz i współczynnika determinacji:

  ![Zrzut ekranu przedstawiający statystyki prognoz](./media/hive-walkthrough/Jat9mrz.png)

  W tym miejscu współczynnika determinacji jest 0.709, co oznacza, że około 71 procent wariancji tłumaczy współczynniki modelu.

> [!IMPORTANT]
> Aby dowiedzieć się więcej na temat usługi Machine Learning i jak uzyskać dostęp i korzystać z niego, zobacz [co to jest usługa Machine Learning](../studio/what-is-machine-learning.md). Ponadto [galerii Azure AI](https://gallery.cortanaintelligence.com/) obejmuje przestrzeni eksperymentów i zapewnia szczegółowe wprowadzenie w zakresie możliwości usługi Machine Learning.
> 
> 

## <a name="license-information"></a>Informacje o licencji
Ten przewodnik przykładowy i jego towarzyszący skrypty są udostępniane przez firmę Microsoft na licencji MIT. Aby uzyskać więcej informacji, zobacz **LICENSE.txt** pliku w katalogu przykładowego kodu w serwisie GitHub.

## <a name="references"></a>Dokumentacja
• [Andrés Monroy taksówek NYC przesłania strony pobierania](https://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC taksówki danych podróży, Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [Taksówek NYC i Limousine Komisji badań i statystyk](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/



