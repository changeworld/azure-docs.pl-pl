---
title: Tworzenie tabel programu Hive i ładowanie danych z magazynu obiektów Blob platformy Azure | Dokumentacja firmy Microsoft
description: Tworzenie tabel programu Hive i ładowania danych w obiekcie blob do tabel
services: machine-learning,storage
documentationcenter: ''
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: cff9280d-18ce-4b66-a54f-19f358d1ad90
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: 593df249429bf1dcc5a59312830ed78f7cf642e8
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Tworzenie tabel programu Hive i ładowanie danych z magazynu obiektów Blob Azure
W tym temacie przedstawiono ogólne zapytań Hive, które Tworzenie tabel programu Hive i ładowanie danych z magazynu obiektów blob platformy Azure. Instrukcje dostępne są również na partycje tabele programu Hive i przy użyciu zoptymalizowanych pod kątem wiersza kolumnowy (ORC) formatowanie, aby poprawić wydajność zapytań.

To **menu** linki do tematów opisujących sposób pozyskiwania danych w środowisku docelowym, gdzie dane mogą być przechowywane i przetwarzane podczas procesu nauki danych zespołu (TDSP).

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że masz:

* Utworzone konto magazynu platformy Azure. Aby uzyskać instrukcje, zobacz [kont magazynu Azure o](../../storage/common/storage-create-storage-account.md).
* Zainicjowano obsługę administracyjną dostosowane klastra usługi Hadoop w usłudze HDInsight.  Aby uzyskać instrukcje, zobacz [dostosować Azure HDInsight Hadoop clusters zaawansowana analityka](customize-hadoop-cluster.md).
* Włączony dostęp zdalny do klastra, zalogowany i otworzyć konsolę wiersza polecenia usługi Hadoop. Aby uzyskać instrukcje, zobacz [dostęp węzła Head klastra usługi Hadoop](customize-hadoop-cluster.md).

## <a name="upload-data-to-azure-blob-storage"></a>Przekazywanie danych do magazynu obiektów blob platformy Azure
Jeśli utworzono maszynę wirtualną platformy Azure, wykonując instrukcje podane w [Konfigurowanie maszyny wirtualnej platformy Azure, zaawansowana analityka](../data-science-virtual-machine/setup-virtual-machine.md), ten plik skryptu powinien zostały pobrane *C:\\użytkowników \\ \<nazwy użytkownika\>\\dokumenty\\skryptów nauki danych* katalogu na maszynie wirtualnej. Te zapytania Hive wymagają tylko należy podłączyć własny schemat danych i konfiguracji magazynu obiektów blob platformy Azure w odpowiednich polach gotowość do przesłania.

Przyjęto założenie, że dane dla tabele programu Hive jest w **nieskompresowanych** tabelarycznej i czy dane został przesłany do domyślnej (lub dodatkowe) kontenera konta magazynu używane przez klaster usługi Hadoop.

Jeśli chcesz praktyki na **NYC taksówki podróży danych**, musisz:

* **Pobierz** 24 [danych podróży taksówki NYC](http://www.andresmh.com/nyctaxitrips) plików (12 pliki podróży i pliki taryfy 12),
* **Rozpakuj** wszystkie pliki w pliki CSV, a następnie
* **Przekaż** ich domyślne (lub odpowiedniego kontenera) konto magazynu Azure, która została utworzona przez procedury opisane w temacie [dostosować Azure HDInsight Hadoop clusters zaawansowane procesu analizy i technologii](customize-hadoop-cluster.md)tematu. Proces przekazywania plików CSV do domyślnego kontenera na koncie magazynu można znaleźć na tym [strony](hive-walkthrough.md#upload).

## <a name="submit"></a>Temat dotyczący przesyłania zapytań programu Hive
Można przesłać zapytań programu hive za pomocą:

1. [Wysyłanie zapytań programu Hive za pomocą wiersza polecenia platformy Hadoop w headnode klastra usługi Hadoop](#headnode)
2. [Wysyłanie zapytań programu Hive w edytorze Hive](#hive-editor)
3. [Wysyłanie zapytań programu Hive z poleceń programu PowerShell Azure](#ps)

Zapytania hive są przypominającego SQL. Jeśli znasz SQL, może się okazać [Hive arkusza Cheat użytkowników SQL](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) przydatne.

Podczas składania zapytań programu Hive, można też kontrolować miejsce docelowe danych wyjściowych z zapytań programu Hive, czy jest na ekranie lub do pliku lokalnego w węźle głównym lub obiektów blob platformy Azure.

### <a name="headnode"></a> 1. Wysyłanie zapytań programu Hive za pomocą wiersza polecenia platformy Hadoop w headnode klastra usługi Hadoop
Jeśli zapytanie Hive jest złożone, przesłania go bezpośrednio w węźle głównym Hadoop klastra zwykle prowadzi do szybciej włączyć niż przesłania go za pomocą skryptów Edytor Hive lub Azure PowerShell.

Zaloguj się do węzła głównego klastra usługi Hadoop, Otwórz okno wiersza polecenia usługi Hadoop na pulpicie węzła głównego, a następnie wprowadź polecenie `cd %hive_home%\bin`.

Istnieją trzy sposoby wysyłanie zapytań programu Hive w wierszu polecenia Hadoop:

* bezpośrednio
* przy użyciu plików .hql
* z konsoli poleceń gałęzi

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Wysyłanie zapytań programu Hive bezpośrednio w Hadoop wiersza polecenia.
Możesz uruchomić polecenie, takie jak `hive -e "<your hive query>;` do przesyłania zapytań programu Hive proste bezpośrednio w Hadoop wiersza polecenia. Oto przykład, gdzie czerwonym prostokątem przedstawiono polecenia, który przesyła zapytanie Hive, a pole zielony przedstawiono dane wyjściowe z zapytania Hive.

![Utwórz obszar roboczy](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Wysyłanie zapytań programu Hive w plikach .hql
Jeśli zapytanie Hive jest bardziej skomplikowany i zawiera wiele wierszy, edytowanie zapytań w wierszu polecenia lub gałęzi konsoli poleceń nie jest praktyczne. Alternatywą jest używany edytor tekstu w węzła głównego klastra usługi Hadoop do zapisania zapytań Hive w pliku .hql w katalogu lokalnym węzła głównego. Następnie można przesłać zapytań Hive w pliku .hql przy użyciu `-f` argument w następujący sposób:

    hive -f "<path to the .hql file>"

![Utwórz obszar roboczy](./media/move-hive-tables/run-hive-queries-3.png)

**Pomiń postępu stan ekranu drukowania zapytań Hive**

Domyślnie po wysłaniu zapytania Hive w wierszu polecenia Hadoop postęp zadania mapy/Zmniejsz jest drukowany na ekranie. Aby pominąć drukowania ekranu mapy/Zmniejsz postępu zadania, można użyć argumentu `-S` ("S" wielkimi literami) w poleceniu wiersza w następujący sposób:

    hive -S -f "<path to the .hql file>"
.    -S -e hive "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Wysyłanie zapytań programu Hive z konsoli poleceń Hive.
Można również najpierw wprowadzić konsoli poleceń Hive, uruchamiając polecenie `hive` w wierszu polecenia Hadoop, a następnie wysyłanie zapytań programu Hive z konsoli poleceń Hive. Oto przykład. W tym przykładzie dwa pola czerwone zaznacz polecenia służące do wprowadzania Hive konsoli poleceń i zapytań Hive przesłane w konsoli polecenie gałęzi, odpowiednio. Pole zielony prezentuje dane wyjściowe z zapytania Hive.

![Utwórz obszar roboczy](./media/move-hive-tables/run-hive-queries-2.png)

Poprzednich przykładach output bezpośrednio na ekranie wyniki zapytania Hive. Można także zapisywać dane wyjściowe do pliku lokalnego węzła głównego lub do obiektów blob platformy Azure. Następnie można użyć innych narzędzi do dalszej analizy dane wyjściowe zapytań programu Hive.

**Dane wyjściowe Hive wyników zapytania do pliku lokalnego.**
Można wyświetlić wyników zapytania Hive w lokalnym katalogu w węźle głównym, należy przesłać zapytanie Hive w wierszu polecenia Hadoop w następujący sposób:

    hive -e "<hive query>" > <local path in the head node>

W poniższym przykładzie zapytanie Hive dane wyjściowe są zapisywane do pliku `hivequeryoutput.txt` w katalogu `C:\apps\temp`.

![Utwórz obszar roboczy](./media/move-hive-tables/output-hive-results-1.png)

**Wyniki zapytania Hive dane wyjściowe do obiektów blob platformy Azure**

Można również output wyników zapytania Hive do obiektów blob platformy Azure, w ramach kontenera domyślnego klastra usługi Hadoop. Zapytanie Hive to wygląda następująco:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

W poniższym przykładzie danych wyjściowych zapytanie Hive są zapisywane do katalogu obiektu blob `queryoutputdir` w domyślnym kontenerze klastra usługi Hadoop. W tym miejscu wystarczy podać nazwę katalogu bez nazwy obiektu blob. Zostanie zgłoszony błąd, jeśli zostaną podane nazwy usługi directory i obiektów blob, takich jak `wasb:///queryoutputdir/queryoutput.txt`.

![Utwórz obszar roboczy](./media/move-hive-tables/output-hive-results-2.png)

Po otwarciu domyślny kontener klastra usługi Hadoop przy użyciu Eksploratora usługi Storage Azure zostanie wyświetlone dane wyjściowe zapytania Hive, jak pokazano na poniższej ilustracji. Filtr (wyróżniony przez czerwonym prostokątem) może dotyczyć tylko pobieranie obiektu blob o określonej litery w nazwach.

![Utwórz obszar roboczy](./media/move-hive-tables/output-hive-results-3.png)

### <a name="hive-editor"></a> 2. Wysyłanie zapytań programu Hive w edytorze Hive
Umożliwia także konsolę kwerendy (Edytor Hive), wprowadzając adres URL w formacie *https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor* w przeglądarce sieci web. Musi być rejestrowane widzą tę konsolę i dlatego należy poświadczenia klastra usługi Hadoop.

### <a name="ps"></a> 3. Wysyłanie zapytań programu Hive z poleceń programu PowerShell Azure
Można również wysyłanie zapytań programu Hive za pomocą programu PowerShell. Aby uzyskać instrukcje, zobacz [zadania Hive przesyłania przy użyciu programu PowerShell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-tables"></a>Utwórz gałąź bazy danych i tabel
Zapytania Hive są udostępniane w [repozytorium GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) i może zostać pobrany z tego miejsca.

Oto zapytania Hive, która tworzy tabeli programu Hive.

    create database if not exists <database name>;
    CREATE EXTERNAL TABLE if not exists <database name>.<table name>
    (
        field1 string,
        field2 int,
        field3 float,
        field4 double,
        ...,
        fieldN string
    )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
    STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Poniżej przedstawiono opisy pola, które należy podłączyć i inne konfiguracje:

* **<database name>**: Nazwa bazy danych, który chcesz utworzyć. Jeśli chcesz użyć domyślnej bazy danych, zapytanie *tworzenie bazy danych...*  można pominąć.
* **<table name>**: Nazwa tabeli, która ma zostać utworzona w ramach określonej bazy danych. Jeśli chcesz użyć domyślnej bazy danych, tabeli może być bezpośrednio odwołuje się  *<table name>*  bez <database name>.
* **<field separator>**: separator ograniczającego pól w pliku danych do przekazania do tabeli Hive.
* **<line separator>**: separator ograniczającego wierszy w pliku danych.
* **<storage location>**: lokalizację magazynu Azure, aby zapisać dane tabele programu Hive. Jeśli nie określisz *lokalizacji <storage location>* , bazy danych i tabele są przechowywane w *gałęzi/magazynu/* katalogu w domyślnym kontenerze klastra gałąź domyślną. Jeśli chcesz określić lokalizację magazynu, lokalizacja magazynu musi być w kontenerze domyślna bazy danych i tabele. Tej lokalizacji musi być określony jako lokalizacji względnej wobec domyślny kontener klastra w formacie *"wasb: / / / < katalog 1 > /"* lub *"wasb: / / / < katalog 1 > / < katalog 2 > /"*itp. Po wykonaniu zapytania względną katalogi są tworzone w ramach kontenera domyślnego.
* **TBLPROPERTIES("SKIP.Header.line.Count"="1")**: Jeśli plik danych ma wiersz nagłówka, należy dodać tę właściwość **na końcu** z *Utwórz tabelę* zapytania. W przeciwnym razie wiersz nagłówka jest załadowany jako rekordu do tabeli. Jeśli plik danych nie ma wiersz nagłówka, można pominąć tej konfiguracji w zapytaniu.

## <a name="load-data"></a>Ładowanie danych do tabele programu Hive
Oto zapytania Hive, który ładuje dane do tabeli programu Hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* **<path to blob data>**: Jeśli plik obiektu blob do przekazania do tabeli Hive znajduje się w domyślnym kontenerze klastra usługi HDInsight Hadoop  *<path to blob data>*  powinien być w formacie *"wasb: / / /<directory in this container> / <blob file name>'*. Można też pliku obiektu blob w kontenerze dodatkowe klastra usługi HDInsight Hadoop. W takim przypadku  *<path to blob data>*  powinien być w formacie *"wasb: / /<container name><storage account name>.blob.core.windows.net/<blob file name>"*.

  > [!NOTE]
  > Dane obiektu blob do przekazania do tabeli Hive musi się znajdować w domyślnych lub dodatkowego kontenera konta magazynu dla klastra usługi Hadoop. W przeciwnym razie *danych obciążenia* działanie nie powiodło się Strona skarżąca, że nie można uzyskać dostępu do danych.
  >
  >

## <a name="partition-orc"></a>Tematy zaawansowane: podzielona na partycje tabeli i magazynu danych gałęzi w formacie ORC
W przypadku dużych danych partycjonowania tabeli jest korzystne dla zapytań, które należy tylko skanowanie kilka partycji tabeli. Na przykład jest uzasadnione do partycjonowania danych dziennika witryny sieci web według daty.

Oprócz partycje tabele programu Hive, jest również przydatne do przechowywania danych gałęzi w formacie zoptymalizowanych pod kątem wiersza kolumnowy (ORC). Aby uzyskać więcej informacji na ORC formatowania, zobacz <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">plików ORC przy użyciu zwiększa wydajność podczas czytania, zapisywania i przetwarzania danych Hive</a>.

### <a name="partitioned-table"></a>Tabeli partycjonowanej
Oto zapytania Hive, które tworzy tabeli partycjonowanej i ładuje dane do niego.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Podczas wykonywania zapytania partycjonowane tabele, zalecane jest aby dodać warunek partycji w **początku** z `where` skuteczności wyszukiwanie znacznie zwiększa klauzuli jako to.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Przechowywanie danych gałęzi w formacie ORC
Nie można bezpośrednio ładowanie danych z magazynu obiektów blob do gałęzi tabel, które są przechowywane w formacie ORC. Poniżej przedstawiono kroki, które należy wykonać, aby załadować dane platformy Azure z obiektów blob do tabele programu Hive przechowywane w formacie ORC.

Tworzenie tabeli zewnętrznej **TEXTFILE AS PRZECHOWYWANE** i ładowanie danych z magazynu obiektów blob do tabeli.

        CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
            lines terminated by '<line separator>' STORED AS TEXTFILE
            LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

        LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

Tworzenie wewnętrznej tabeli z tym samym schematem co tabeli zewnętrznej w kroku 1, z tym samym ogranicznik pola i przechowywania danych gałęzi w formacie ORC.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Wybierz dane z tabeli zewnętrznej w kroku 1 i Wstaw do tabeli ORC

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> Jeśli w tabeli TEXTFILE  *<database name>.<external textfile table name>* zawiera partycje, w KROKU 3 `SELECT * FROM <database name>.<external textfile table name>` polecenia wybiera zmiennej partycji jako pole w zestawie danych zwracanych. Wstawianie do  *<database name>.<ORC table name>* kończy się niepowodzeniem, ponieważ  *<database name>.<ORC table name>* nie ma partycji zmiennej jako pole schemat tabeli. W takim przypadku należy wybrać pola, które ma zostać wstawiony do  *<database name>.<ORC table name>* w następujący sposób:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

Można bezpiecznie usunąć  *<external textfile table name>*  po po wszystkich danych za pomocą następującej kwerendy został wstawiony do  *<database name>.<ORC table name>* :

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Po wykonaniu tej procedury, powinien mieć tabelę z danymi w formacie ORC gotowe do użycia.  
