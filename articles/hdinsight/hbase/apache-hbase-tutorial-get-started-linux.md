---
title: Samouczek — Korzystanie Apache HBase w usłudze Azure HDInsight
description: Postępuj zgodnie z tym samouczkiem bazy danych Apache HBase, aby rozpocząć korzystanie z usługi hadoop w HDInsight. Utwórz tabele z poziomu powłoki HBase i wykonuj zapytania przy użyciu aplikacji Hive.
keywords: hbasecommand,przykład hbase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: tutorial
ms.date: 06/25/2019
ms.author: hrasheed
ms.openlocfilehash: 48b02a042b55af9ff65f57220f7a64c9cbde8848
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445548"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>Samouczek: Użyj bazy danych Apache HBase w usłudze Azure HDInsight

W tym samouczku pokazano, jak utworzyć klaster Apache HBase w usłudze Azure HDInsight, tabele bazy danych HBase i wykonywać zapytania dotyczące tabel za pomocą technologii Hive.  Aby uzyskać ogólne informacje bazy danych HBase, zobacz [Przegląd bazy danych HDInsight HBase](./apache-hbase-overview.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie klastra Apache HBase
> * Tworzenie tabel bazy danych HBase i wstawianie danych
> * Użyj Apache Hive, aby wykonać zapytanie bazy danych Apache HBase
> * Korzystanie z interfejsów API REST HBase przy użyciu programu Curl
> * Sprawdzanie stanu klastra

## <a name="prerequisites"></a>Wymagania wstępne

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Bash. Przykłady w niniejszym artykule Użyj powłoki Bash w systemie Windows 10 dla polecenia curl. Zobacz [podsystemu Windows dla systemu Linux instalacji Guide for Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) dla czynności instalacyjne.  Inne [powłoki systemu Unix](https://www.gnu.org/software/bash/) będzie działać tak dobrze.  Przykłady programu curl, za pomocą pewne niewielkie modyfikacje mogą pracować w wierszu polecenia Windows.  Alternatywnie, można użyć polecenia cmdlet programu Windows PowerShell [Invoke RestMethod](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-restmethod).

## <a name="create-apache-hbase-cluster"></a>Tworzenie klastra Apache HBase

Poniższej procedurze użyto szablonu usługi Azure Resource Manager, aby utworzyć klaster HBase i zależnego domyślnego konta usługi Azure Storage. Aby zapoznać się z parametrami używanymi w tej procedurze oraz innymi metodami tworzenia klastra, zobacz temat [Tworzenie opartych na systemie Linux klastrów Hadoop w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Wybierz poniższy obraz, aby otworzyć szablon w witrynie Azure portal. Szablon znajduje się w [szablony szybkiego startu platformy](https://azure.microsoft.com/resources/templates/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. W bloku **Wdrożenie niestandardowe** wprowadź następujące wartości:

    |Właściwość |Opis |
    |---|---|
    |Subskrypcja|Wybieranie subskrypcji platformy Azure, który służy do tworzenia klastra.|
    |Grupa zasobów|Utwórz grupę zasobów platformy Azure management lub użyj istniejącej.|
    |Location|Określ lokalizację grupy zasobów. |
    |ClusterName|Wprowadź nazwę klastra HBase.|
    |Nazwa logowania klastra i hasło|Domyślna nazwa logowania to **admin**.|
    |Nazwa użytkownika protokołu SSH i hasło|Domyślna nazwa użytkownika to **sshuser**.|

    Inne parametry są opcjonalne.  

    Każdy klaster zależy od konta usługi Azure Storage. Po usunięciu klastra dane pozostają zachowane na koncie magazynu. Domyślna nazwa konta magazynu klastra to nazwa klastra z dołączonym ciągiem „store”. Jest ona umieszczona w kodzie w sekcji zmiennych szablonu.

3. Zaznacz pozycję **Wyrażam zgodę na powyższe warunki i postanowienia**, a następnie kliknij przycisk **Kup**. Utworzenie klastra trwa około 20 minut.

Po usunięciu klastra HBase można utworzyć inny klaster HBase za pomocą tego samego domyślnego kontenera obiektów blob. Nowy klaster przejmuje tabele bazy danych HBase utworzone w oryginalnym klastrze. Aby uniknąć niespójności, zaleca się wyłączenie tabel HBase przed usunięciem klastra.

## <a name="create-tables-and-insert-data"></a>Tworzenie tabel i wstawianie danych

Protokół SSH umożliwia połączenie z klastrami HBase, a następnie korzystanie z [powłoki Apache HBase](https://hbase.apache.org/0.94/book/shell.html) w celu tworzenia tabel bazy danych HBase, wstawiania danych i wykonywania zapytań o dane.

Dla większości użytkowników dane są wyświetlane w formacie tabelarycznym:

![Dane tabelaryczne usługi HDInsight HBase](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

W bazie danych HBase (implementacja [BigTable chmury](https://cloud.google.com/bigtable/)), te same dane wygląda następująco:

![Dane BigTable usługi HDInsight HBase](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**Aby użyć powłoki HBase**

1. Użyj `ssh` polecenie, aby nawiązać połączenie z klastrem HBase. Edytuj poniższe polecenie, zastępując `CLUSTERNAME` nazwą klastra, a następnie wpisz polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Użyj `hbase shell` polecenia do uruchamiania interaktywnej powłoki HBase. Wprowadź następujące polecenie, w związku z protokołu SSH:

    ```bash
    hbase shell
    ```

1. Użyj `create` polecenie, aby utworzyć tabelę HBase z dwiema rodzinami kolumn. Nazwy tabel i kolumn jest rozróżniana wielkość liter. Wprowadź następujące polecenie:

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. Użyj `list` polecenie, aby wyświetlić wszystkie tabele w bazie danych HBase. Wprowadź następujące polecenie:

    ```hbase
    list
    ```

1. Użyj `put` polecenie, aby wstawić wartości w określonej kolumnie w określonym wierszu określonej tabeli. Wprowadź następujące polecenia:

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. Użyj `scan` polecenia do skanowania i zwracać `Contacts` tabeli danych. Wprowadź następujące polecenie:

    ```hbase
    scan 'Contacts'
    ```

    ![Powłoka HBase HDInsight Hadoop](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. Użyj `get` polecenie, aby pobrać zawartość wiersza. Wprowadź następujące polecenie:

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    Zostaną wyświetlone wyniki podobne, używając `scan` polecenia, ponieważ istnieje tylko jeden wiersz.

    Aby uzyskać więcej informacji na temat schematu tabeli HBase, zobacz [wprowadzenie do projektu schematu HBase Apache](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Aby uzyskać więcej poleceń HBase, zobacz [podręczniku bazy danych Apache HBase](https://hbase.apache.org/book.html#quickstart).

1. Użyj `exit` polecenie, aby zatrzymać interaktywnej powłoki HBase. Wprowadź następujące polecenie:

    ```hbaseshell
    exit
    ```

**Aby zbiorczo załadować dane do tabeli kontaktów HBase**

Baza danych HBase obsługuje kilka metod ładowania danych do tabel.  Aby uzyskać więcej informacji, zobacz temat [Ładowanie zbiorcze](https://hbase.apache.org/book.html#arch.bulk.load).

Przykładowy plik danych znajdują się w publicznym kontenerze obiektów blob, `wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt`.  Plik danych ma następującą zawartość:

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

Opcjonalnie możesz utworzyć plik tekstowy i przesłać go na swoje konto magazynu. Aby uzyskać instrukcje, zobacz [przekazywanie danych na potrzeby zadań usługi Apache Hadoop w HDInsight](../hdinsight-upload-data.md).

Ta procedura wykorzystuje `Contacts` tabeli bazy danych HBase utworzone w poprzedniej procedurze.

1. Z usługi open ssh połączenie, uruchom następujące polecenie, aby przekształcać dane pliku StoreFiles i zapisać w ścieżce względnej określonej przez `Dimporttsv.bulk.output`.

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Uruchom następujące polecenie, aby przekazać dane z `/example/data/storeDataFileOutput` w tabeli HBase:

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Aby otworzyć powłokę HBase oraz używać `scan` polecenie, aby wyświetlić listę zawartości tabeli.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Użyj Apache Hive, aby wykonać zapytanie bazy danych Apache HBase

Wykonywanie zapytań danych w tabelach HBase przy użyciu [Apache Hive](https://hive.apache.org/). W tej sekcji zostanie utworzona tabela programu Hive odwzorowująca dane w tabeli HBase, która będzie używana do wykonywania zapytań o dane w tabeli HBase.

1. Z usługi otworzyć połączenia ssh, użyj następującego polecenia, aby uruchomić usługę Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Aby uzyskać więcej informacji o usłudze Beeline, zobacz [Używanie technologii Hive z usługą Hadoop w usłudze HDInsight z usługą Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).

1. Uruchom następujące polecenie [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) skrypt, aby utworzyć tabelę programu Hive, która mapuje dane do tabeli HBase. Upewnij się, że utworzono przykładową tabelę, do których odwołuje się wcześniej w tym artykule za pomocą powłoki HBase przed uruchomieniem tej instrukcji.

    ```hiveql
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

1. Uruchom poniższy skrypt HiveQL, aby wykonać zapytanie o dane w tabeli HBase:

    ```hiveql
    SELECT count(rowkey) AS rk_count FROM hbasecontacts;
    ```

1. Aby wyjść z usługi Beeline, należy użyć `!exit`.

1. Aby zakończyć działanie sieci protokołu ssh połączenie, użyj `exit`.

## <a name="use-hbase-rest-apis-using-curl"></a>Korzystanie z interfejsów API REST HBase przy użyciu programu Curl

Interfejs API REST jest zabezpieczony za pomocą [uwierzytelniania podstawowego](https://en.wikipedia.org/wiki/Basic_access_authentication). Należy zawsze tworzyć żądania przy użyciu protokołu HTTPS (HTTP Secure), aby mieć pewność, że poświadczenia są bezpiecznie wysyłane do serwera.

1. Zainicjuj zmienną środowiskową na łatwość użycia. Edytuj poniższe polecenia, zastępując `MYPASSWORD` z hasło logowania klastra. Zastąp `MYCLUSTERNAME` o nazwie klastra HBase. Następnie wprowadź polecenia.

    ```bash
    export password='MYPASSWORD'
    export clustername=MYCLUSTERNAME
    ```

1. Użyj następującego polecenia, aby wyświetlić listę istniejących tabel HBase:

    ```bash
    curl -u admin:$password \
    -G https://$clustername.azurehdinsight.net/hbaserest/
    ```

1. Użyj następującego polecenia, aby utworzyć nową tabelę HBase z dwiema rodzinami kolumn:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    Schemat jest podany w formacie JSon.
1. Użyj następującego polecenia, aby wstawić dane:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```

    Należy zakodować wartości określone w przełączniku -d w formacie base64. W przykładzie:

   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Personal:Name
   * Sm9obiBEb2xl: John Dole

     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) umożliwia wstawianie wielu wartości (w partiach).

1. Użyj następującego polecenia, aby pobrać wiersz:

    ```bash
    curl -u admin:$password \
    GET "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

Aby uzyskać więcej informacji o interfejsie Rest HBase, zobacz [Apache HBase Reference Guide](https://hbase.apache.org/book.html#_rest) (Podręcznik referencyjny Apache HBase).

> [!NOTE]  
> Platforma Thrift nie jest obsługiwana przez bazę danych HBase w usłudze HDInsight.
>
> Używając programu Curl lub innego połączenia REST z usługą WebHCat, należy uwierzytelnić żądania, podając nazwę użytkownika i hasło administratora klastra usługi HDInsight. Należy również użyć nazwy klastra jako części identyfikatora URI stosowanego przy wysyłaniu żądań do serwera:
> 
>   
>        curl -u <UserName>:<Password> \
>        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
>   
>    Powinna zostać zwrócona odpowiedź podobna do następującej:
>   
>        {"status":"ok","version":"v1"}

## <a name="check-cluster-status"></a>Sprawdzanie stanu klastra

Baza danych HBase w usłudze HDInsight jest dostarczana z interfejsem użytkownika sieci Web służącym do monitorowania klastrów. Za pośrednictwem interfejsu użytkownika sieci Web możesz przesyłać żądania dotyczące statystyk lub informacji o regionach.

**Aby uzyskać dostęp do głównego interfejsu użytkownika HBase**

1. Zaloguj się do Interfejsu sieci Web Ambari w `https://CLUSTERNAME.azurehdinsight.net` gdzie `CLUSTERNAME` jest nazwą klastra HBase.

1. Wybierz **HBase** menu po lewej stronie.

1. Wybierz **szybkich łączy** górnej części strony, wskaż aktywny link węzła dozorcy, a następnie wybierz **głównego interfejsu użytkownika HBase**.  Interfejs użytkownika zostanie otwarty w innej karcie przeglądarki:

   ![Główny interfejs użytkownika HDInsight HBase](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   Główny interfejs użytkownika HBase zawiera następujące sekcje:

   - serwery regionów
   - wzorce kopii zapasowej
   - tabele
   - zadania
   - atrybuty oprogramowania

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby uniknąć niespójności, zaleca się wyłączenie tabel HBase przed usunięciem klastra. Możesz użyć polecenia bazy danych HBase `disable 'Contacts'`. Jeśli nie zamierzasz nadal korzystać z tej aplikacji, usuń utworzony klaster bazy danych HBase, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. W polu **Wyszukaj** w górnej części wpisz **HDInsight**.
1. Wybierz pozycję **Klastry usługi HDInsight** w obszarze **Usługi**.
1. Na wyświetlonej liście klastrów usługi HDInsight kliknij symbol **...** obok klastra utworzonego na potrzeby tego samouczka.
1. Kliknij przycisk **Usuń**. Kliknij przycisk **Yes** (Tak).

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku pokazano, jak utworzyć klaster Apache HBase i tworzyć tabele oraz jak wyświetlać dane w tych tabelach z poziomu powłoki HBase. Przedstawiono również sposób wykonywania zapytań programu Hive względem danych w tabelach HBase oraz korzystania z interfejsów API REST HBase w języku C# w celu tworzenia tabel HBase i pobierania danych z tabeli. Aby dowiedzieć się więcej, zobacz:

> [!div class="nextstepaction"]
> [Omówienie bazy danych HDInsight HBase](./apache-hbase-overview.md)