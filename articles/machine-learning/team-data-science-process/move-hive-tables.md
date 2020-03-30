---
title: Tworzenie tabel gałęzi i ładowanie danych z magazynu obiektów Blob — proces nauki o danych zespołu
description: Użyj zapytań gałęzi, aby utworzyć tabele gałęzi i załadować dane z magazynu obiektów blob platformy Azure. Tabele gałęzi partycji i użyj formatowania zoptymalizowane kolumny wiersza (ORC), aby zwiększyć wydajność kwerendy.
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
ms.openlocfilehash: 625d9d5c5ecf095d4acbff625754b2065f184536
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251663"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Tworzenie tabel gałęzi i ładowanie danych z usługi Azure Blob Storage

W tym artykule przedstawiono ogólne zapytania gałęzi, które tworzą tabele gałęzi i załadować dane z magazynu obiektów blob platformy Azure. Niektóre wskazówki są również dostępne w przypadku partycjonowania tabel gałęzi i używania formatowania kolumny zoptymalizowanego wiersza (ORC) w celu zwiększenia wydajności kwerendy.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz:

* Utworzono konto usługi Azure Storage. Jeśli potrzebujesz instrukcji, zobacz [Informacje o kontach usługi Azure Storage](../../storage/common/storage-introduction.md).
* Aprowizowana dostosowana klaster Hadoop z usługą HDInsight.  Jeśli potrzebujesz instrukcji, zobacz [Ustawienia klastrów w umiań HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).
* Włączono zdalny dostęp do klastra, zalogowano się i otworzyło konsolę Wiersz polecenia Hadoop. Jeśli potrzebujesz instrukcji, zobacz [Zarządzanie klastrami Apache Hadoop](../../hdinsight/hdinsight-administer-use-portal-linux.md).

## <a name="upload-data-to-azure-blob-storage"></a>Przekazywanie danych do magazynu obiektów blob platformy Azure
Jeśli utworzono maszynę wirtualną platformy Azure, postępując zgodnie z instrukcjami podanymi w [Konfigurowanie maszyny wirtualnej platformy Azure do zaawansowanej analizy,](../../machine-learning/data-science-virtual-machine/overview.md)ten plik skryptu powinien zostać pobrany do katalogu *\\C: Użytkownicy\\\<nazwa\>\\użytkownika Dokumenty\\Data Science Scripts* na maszynie wirtualnej. Te zapytania hive wymagają tylko, aby podać schemat danych i konfiguracji magazynu obiektów blob platformy Azure w odpowiednich polach, aby być gotowy do przesłania.

Zakładamy, że dane dla tabel hive jest w **nieskompresowanym** formacie tabelarycznym i że dane zostały przekazane do domyślnego (lub do dodatkowego) kontenera konta magazynu używanego przez klaster Hadoop.

Jeśli chcesz ćwiczyć na **NYC Taxi Trip Data**, musisz:

* **pobrać** 24 pliki [danych nyc taxi trip](https://www.andresmh.com/nyctaxitrips) (12 plików Trip i 12 plików Fare),
* **rozpakowaj** wszystkie pliki do plików csv, a następnie
* **przekaż** je do domyślnego (lub odpowiedniego kontenera) konta usługi Azure Storage; opcje dla takiego konta są wyświetlane w [temacie Użyj usługi Azure Storage z klastrami usługi Azure HDInsight.](../../hdinsight/hdinsight-hadoop-use-blob-storage.md) Proces przekazywania plików csv do domyślnego kontenera na koncie magazynu można znaleźć na tej [stronie](hive-walkthrough.md#upload).

## <a name="how-to-submit-hive-queries"></a><a name="submit"></a>Jak przesyłać zapytania dotyczące gałęzi
Zapytania gałęzi można przesyłać za pomocą:

* [Przesyłanie zapytań hive za pośrednictwem linii poleceń Hadoop w headnode klastra Hadoop](#headnode)
* [Przesyłanie zapytań gałęzi za pomocą edytora gałęzi](#hive-editor)
* [Przesyłanie zapytań gałęzi za pomocą poleceń programu Azure PowerShell](#ps)

Zapytania gałęzi są podobne do języka SQL. Jeśli znasz SQL, może okazać się [hive dla użytkowników SQL Ściągawka](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) przydatne.

Podczas przesyłania zapytania hive, można również kontrolować miejsce docelowe danych wyjściowych z zapytań hive, czy to na ekranie lub do pliku lokalnego w węźle głównym lub do obiektu blob platformy Azure.

### <a name="submit-hive-queries-through-hadoop-command-line-in-headnode-of-hadoop-cluster"></a><a name="headnode"></a>Przesyłanie zapytań hive za pośrednictwem linii poleceń Hadoop w headnode klastra Hadoop
Jeśli zapytanie hive jest złożone, przesyłanie go bezpośrednio w węźle głównym klastra Hadoop zazwyczaj prowadzi do szybszego odwróć niż przesyłanie go za pomocą skryptów Edytora hive lub programu Azure PowerShell.

Zaloguj się do węzła głównego klastra Hadoop, otwórz wiersz polecenia Hadoop na `cd %hive_home%\bin`pulpicie węzła głównego i wprowadź polecenie .

W wierszu polecenia Hadoop można przesyłać zapytania hive na trzy sposoby:

* Bezpośrednio
* za pomocą plików '.hql'
* z konsolą poleceń Hive

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Prześlij zapytania gałęzi bezpośrednio w wierszu polecenia Hadoop.
Można uruchomić polecenie, takie jak `hive -e "<your hive query>;` przesyłanie prostych zapytań hive bezpośrednio w wierszu polecenia Hadoop. Oto przykład, gdzie czerwone pole przedstawia polecenie, które przesyła zapytanie hive, a zielone pole przedstawia dane wyjściowe z zapytania hive.

![Polecenie przesyłania kwerendy hive z wyjściem z zapytania hive](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Przesyłanie zapytań hive w plikach '.hql'
Gdy kwerenda hive jest bardziej skomplikowana i ma wiele wierszy, edytowanie zapytań w wierszu polecenia lub konsoli poleceń hive nie jest praktyczne. Alternatywą jest użycie edytora tekstu w węźle głównym klastra Hadoop do zapisania zapytań hive w pliku ".hql" w lokalnym katalogu węzła głównego. Następnie kwerendę hive w pliku '.hql' można `-f` przesłać za pomocą następującego argumentu:

    hive -f "<path to the '.hql' file>"

![Zapytanie gałęzi w pliku '.hql'](./media/move-hive-tables/run-hive-queries-3.png)

**Pomijanie drukowania na ekranie stanu postępu zapytań gałęzi**

Domyślnie po przesłaniu zapytania hive w wierszu polecenia Hadoop postęp zadania Mapa/Zmniejszenie jest drukowany na ekranie. Aby pominąć wydruk ekranu postępu zadania Mapa/Zmniejsz, `-S` można użyć argumentu ("S" w wielkich literach) w wierszu polecenia w następujący sposób:

    hive -S -f "<path to the '.hql' file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Prześlij zapytania gałęzi w konsoli poleceń gałęzi.
Można również najpierw wprowadzić konsolę poleceń `hive` Hive, uruchamiając polecenie w wierszu polecenia Hadoop, a następnie przesyłając zapytania hive w konsoli poleceń hive. Oto przykład. W tym przykładzie dwa czerwone pola wyróżniają polecenia używane do wprowadzenia konsoli poleceń Hive i kwerendę hive przesłaną odpowiednio w konsoli poleceń hive. Zielone pole wyróżnia dane wyjściowe z zapytania hive.

![Otwórz konsolę poleceń hive i wprowadź polecenie, wyświetl dane wyjściowe kwerendy hive](./media/move-hive-tables/run-hive-queries-2.png)

Poprzednie przykłady bezpośrednio wyprowadzają wyniki zapytania hive na ekranie. Można również zapisać dane wyjściowe do pliku lokalnego w węźle głównym lub do obiektu blob platformy Azure. Następnie można użyć innych narzędzi do dalszej analizy danych wyjściowych zapytań hive.

**Wynik zapytania gałęzi danych do pliku lokalnego.**
Aby przesłać wyniki kwerendy hive do katalogu lokalnego w węźle głównym, należy przesłać kwerendę hive w wierszu polecenia Hadoop w następujący sposób:

    hive -e "<hive query>" > <local path in the head node>

W poniższym przykładzie dane wyjściowe zapytania hive `hivequeryoutput.txt` są `C:\apps\temp`zapisywane w pliku w katalogu .

![Dane wyjściowe kwerendy hive](./media/move-hive-tables/output-hive-results-1.png)

**Wyniki kwerendy gałęzi danych wyjściowych do obiektu blob platformy Azure**

Można również wyprowadzić wyniki kwerendy hive do obiektu blob platformy Azure, w domyślnym kontenerze klastra Hadoop. Zapytanie hive w tym celu jest w następujący sposób:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

W poniższym przykładzie dane wyjściowe zapytania hive jest `queryoutputdir` zapisywany w katalogu obiektów blob w domyślnym kontenerze klastra Hadoop. W tym miejscu wystarczy podać nazwę katalogu, bez nazwy obiektu blob. Błąd jest generowany, jeśli podasz zarówno nazwy katalogu, jak i obiektów blob, takie jak `wasb:///queryoutputdir/queryoutput.txt`.

![Dane wyjściowe kwerendy hive](./media/move-hive-tables/output-hive-results-2.png)

Jeśli otworzysz domyślny kontener klastra Hadoop przy użyciu Eksploratora usługi Azure Storage, możesz zobaczyć dane wyjściowe zapytania hive, jak pokazano na poniższym rysunku. Filtr (wyróżniony czerwonym polem) można zastosować, aby pobrać tylko obiekt blob z określonymi literami w nazwach.

![Eksplorator usługi Azure Storage przedstawiający dane wyjściowe kwerendy hive](./media/move-hive-tables/output-hive-results-3.png)

### <a name="submit-hive-queries-with-the-hive-editor"></a><a name="hive-editor"></a>Przesyłanie zapytań gałęzi za pomocą edytora gałęzi
Można również użyć Konsoli zapytań (Edytoru gałęzi), wprowadzając adres URL formularza *https:\//\<Hadoop nazwa klastra>.azurehdinsight.net/Home/HiveEditor* w przeglądarce sieci web. Musisz być zalogowany w tej konsoli, a więc potrzebujesz poświadczeń klastra Hadoop tutaj.

### <a name="submit-hive-queries-with-azure-powershell-commands"></a><a name="ps"></a>Przesyłanie zapytań gałęzi za pomocą poleceń programu Azure PowerShell
Można również użyć programu PowerShell do przesyłania zapytań hive. Aby uzyskać instrukcje, zobacz [Przesyłanie zadań gałęzi przy użyciu programu PowerShell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-hive-database-and-tables"></a><a name="create-tables"></a>Tworzenie bazy danych i tabel hive
Zapytania hive są udostępniane w [repozytorium GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) i można je pobrać stamtąd.

Oto zapytanie hive, który tworzy tabelę hive.

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

Oto opisy pól, które należy podłączyć i innych konfiguracji:

* nazwa bazy danych : nazwa bazy danych, którą chcesz utworzyć. ** \<\>** Jeśli chcesz tylko użyć domyślnej bazy danych, kwerenda "*create database..."*" może zostać pominięta.
* nazwa tabeli : nazwa tabeli, którą chcesz utworzyć w określonej bazie danych. ** \<\>** Jeśli chcesz użyć domyślnej bazy danych, tabela może być \<bezpośrednio\>odwoływana przez * \<\> nazwę tabeli* bez nazwy bazy danych .
* **separator\>pól : separator, który rozdziela pola w pliku danych, które mają zostać przekazane do tabeli Gałąź. \<**
* **separator\>linii : separator, który rozgranicza linie w pliku \<** danych.
* lokalizacja magazynu: lokalizacja usługi Azure Storage, aby zapisać dane tabel hive. ** \<\>** Jeśli lokalizacja *\>magazynu \<lokalizacja*nie zostanie określona, baza danych i tabele są domyślnie przechowywane w katalogu *gałęzi/magazynu/* w domyślnym kontenerze klastra Hive. Jeśli chcesz określić lokalizację magazynu, lokalizacja magazynu musi znajdować się w domyślnym kontenerze bazy danych i tabel. Ta lokalizacja musi być określana jako lokalizacja względem domyślnego kontenera klastra w formacie *"wasb:///\<katalogu 1>/"* lub *"wasb:///\<katalogu\<1>/ katalog 2>/"* itp. Po wykonaniu kwerendy katalogi względne są tworzone w domyślnym kontenerze.
* **TBLPROPERTIES("skip.header.line.count"="1")**: Jeśli plik danych ma wiersz nagłówka, należy dodać tę właściwość **na końcu** *kwerendy tabeli tworzenia.* W przeciwnym razie wiersz nagłówka jest ładowany jako rekord do tabeli. Jeśli plik danych nie ma wiersza nagłówka, tę konfigurację można pominąć w kwerendzie.

## <a name="load-data-to-hive-tables"></a><a name="load-data"></a>Ładowanie danych do tabel gałęzi
Oto zapytanie hive, które ładuje dane do tabeli hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* ścieżka **do danych\>obiektów blob: Jeśli plik obiektu blob, który ma zostać przekazany do tabeli Gałąź, znajduje się w domyślnym kontenerze klastra HDInsight Hadoop, ścieżka do danych obiektów blob powinna być w formacie "wasb:// katalogu w tym kontenerze>/ nazwa pliku obiektów blob>" . \<** * \<\> * *\<\<* Plik obiektu blob może również znajdować się w dodatkowym kontenerze klastra HDInsight Hadoop. W takim przypadku * \<ścieżka do\> danych obiektów blob* powinna mieć format *"wasb://\<nazwa kontenera>nazwa konta \<magazynu>.blob.core.windows.net/\<nazwa pliku obiektu blob>"*.

  > [!NOTE]
  > Dane obiektów blob, które mają zostać przekazane do tabeli hive musi być w domyślnym lub dodatkowym kontenerze konta magazynu dla klastra Hadoop. W przeciwnym razie kwerenda *LOAD DATA* kończy się niepowodzeniem, narzekając, że nie może uzyskać dostępu do danych.
  >
  >

## <a name="advanced-topics-partitioned-table-and-store-hive-data-in-orc-format"></a><a name="partition-orc"></a>Tematy zaawansowane: tabela podzielona na partycje i przechowywanie danych gałęzi w formacie ORC
Jeśli dane są duże, partycjonowanie tabeli jest korzystne dla kwerend, które trzeba tylko zeskanować kilka partycji tabeli. Na przykład uzasadnione jest partycjonowanie danych dziennika witryny sieci web według dat.

Oprócz partycjonowania tabel hive, jest również korzystne do przechowywania danych gałęzi w formacie Zoptymalizowany wiersz kolumnowy (ORC). Aby uzyskać więcej informacji na temat formatowania ORC, zobacz <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">Korzystanie z plików ORC zwiększa wydajność podczas odczytywania, zapisywania i przetwarzania danych przez hive</a>.

### <a name="partitioned-table"></a>Tabela podzielona na partycje
Oto zapytanie hive, który tworzy tabelę podzieloną na partycje i ładuje do niej dane.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Podczas wykonywania zapytań tabel podzielonych na partycje, **beginning** zaleca `where` się, aby dodać warunek partycji na początku klauzuli, co zwiększa wydajność wyszukiwania.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="store-hive-data-in-orc-format"></a><a name="orc"></a>Przechowywanie danych gałęzi w formacie ORC
Nie można bezpośrednio załadować danych z magazynu obiektów blob do tabel hive, które są przechowywane w formacie ORC. Oto kroki, które należy podjąć, aby załadować dane z obiektów blob platformy Azure do tabel hive przechowywanych w formacie ORC.

Utwórz tabelę zewnętrzną **przechowywaną jako plik tekstowy** i załaduj dane z magazynu obiektów blob do tabeli.

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

Utwórz tabelę wewnętrzną z tym samym schematem co tabela zewnętrzna w kroku 1 z tym samym ogranicznikiem pola i przechowuj dane gałęzi w formacie ORC.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Wybierz dane z tabeli zewnętrznej w kroku 1 i wstaw do tabeli ORC

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> Jeśli * \<nazwa\>bazy danych tabeli TEXTFILE\< . nazwa\> tabeli plików tekstowych zewnętrznych ma partycje,* w kroku 3 `SELECT * FROM <database name>.<external textfile table name>` polecenie wybiera zmienną partycji jako pole w zwróconym zestawie danych. Wstawianie go do * \<nazwy\>bazy danych\< . Nazwa\> tabeli ORC* kończy się niepowodzeniem od * \<\>czasu, gdy nazwa bazy danych .\< Nazwa\> tabeli ORC* nie ma zmiennej partycji jako pola w schemacie tabeli. W takim przypadku należy w szczególności wybrać pola, które mają zostać wstawione do * \<nazwy\>bazy danych .\< Nazwa tabeli\> ORC w* następujący sposób:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

Można bezpiecznie upuścić * \<\> nazwę tabeli plików tekstowych zewnętrznego* podczas korzystania z następującej kwerendy po wstawieniu wszystkich danych do * \<nazwy\>bazy danych.\< Nazwa\>tabeli ORC:*

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Po zastosowaniu tej procedury powinien mieć tabelę z danymi w formacie ORC gotowy do użycia.  
