---
title: Rozpoczynanie pracy z przykładem bazy danych HBase na HDInsight — Azure
description: Zapoznaj się z tym przykładem bazy danych Apache HBase, aby rozpocząć korzystanie z usługi Hadoop w usłudze HDInsight. Utwórz tabele z poziomu powłoki HBase i wykonuj zapytania przy użyciu aplikacji Hive.
keywords: hbasecommand,przykład hbase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: c1c582c60a7c91de40983daa07bdec1e8d748f8a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718882"
---
# <a name="get-started-with-an-apache-hbase-example-in-hdinsight"></a>Rozpoczynanie pracy z przykładem bazy danych Apache HBase w usłudze HDInsight

Dowiedz się, jak utworzyć [bazy danych Apache HBase](https://hbase.apache.org/) klastra w systemie HDInsight, tworzyć tabele bazy danych HBase i wykonywać zapytania dotyczące tabel za pomocą [Apache Hive](https://hive.apache.org/).  Aby uzyskać ogólne informacje o bazie danych HBase, zobacz [Omówienie bazy danych HBase w usłudze HDInsight][hdinsight-hbase-overview].

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem prób korzystania z tego przykładu bazy danych HBase należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Bezpieczna powłoka (SSH)](../hdinsight-hadoop-linux-use-ssh-unix.md). 
* [Program curl](https://curl.haxx.se/download.html).

## <a name="create-apache-hbase-cluster"></a>Tworzenie klastra Apache HBase
W poniższej procedurze użyto szablonu usługi Azure Resource Manager do utworzenia klastra HBase i zależnego domyślnego konta usługi Azure Storage. Aby zapoznać się z parametrami używanymi w tej procedurze oraz innymi metodami tworzenia klastra, zobacz temat [Tworzenie opartych na systemie Linux klastrów Hadoop w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Aby uzyskać więcej informacji na temat korzystania z usługi Data Lake Storage Gen2, zobacz [Szybki start: Konfigurowanie klastrów w usłudze HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

1. Kliknij poniższy obraz, aby otworzyć szablon w usłudze Azure Portal. Szablon znajduje się w [szablony szybkiego startu platformy](https://azure.microsoft.com/resources/templates/).
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. W bloku **Wdrożenie niestandardowe** wprowadź następujące wartości:
   
   * **Subskrypcja**: Wybieranie subskrypcji platformy Azure, który służy do tworzenia klastra.
   * **Grupa zasobów**: Utwórz grupę zasobów platformy Azure management lub użyj istniejącej.
   * **Lokalizacja**: Określ lokalizację grupy zasobów. 
   * **ClusterName**: Wprowadź nazwę klastra HBase.
   * **Nazwa logowania i hasło klastra**: Domyślna nazwa logowania to **admin**.
   * **Nazwa użytkownika protokołu SSH i hasło**: Domyślna nazwa użytkownika to **sshuser**.  Tę nazwę można zmienić.
     
     Inne parametry są opcjonalne.  
     
     Każdy klaster zależy od konta usługi Azure Storage. Po usunięciu klastra dane pozostają zachowane na koncie magazynu. Domyślna nazwa konta magazynu klastra to nazwa klastra z dołączonym ciągiem „store”. Jest ona umieszczona w kodzie w sekcji zmiennych szablonu.
3. Zaznacz pozycję **Wyrażam zgodę na powyższe warunki i postanowienia**, a następnie kliknij przycisk **Kup**. Utworzenie klastra trwa około 20 minut.

> [!NOTE]  
> Po usunięciu klastra HBase można utworzyć inny klaster HBase za pomocą tego samego domyślnego kontenera obiektów blob. Nowy klaster przejmuje tabele bazy danych HBase utworzone w oryginalnym klastrze. Aby uniknąć niespójności, zaleca się wyłączenie tabel HBase przed usunięciem klastra.
> 
> 

## <a name="create-tables-and-insert-data"></a>Tworzenie tabel i wstawianie danych
Protokół SSH umożliwia połączenie z klastrami HBase, a następnie korzystanie z [powłoki Apache HBase](https://hbase.apache.org/0.94/book/shell.html) w celu tworzenia tabel bazy danych HBase, wstawiania danych i wykonywania zapytań o dane. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Dla większości użytkowników dane są wyświetlane w formacie tabelarycznym:

![Dane tabelaryczne usługi HDInsight HBase][img-hbase-sample-data-tabular]

W bazie danych HBase (implementacja [BigTable chmury](https://cloud.google.com/bigtable/)), te same dane wygląda następująco:

![Dane BigTable usługi HDInsight HBase][img-hbase-sample-data-bigtable]


**Aby użyć powłoki HBase**

1. Z poziomu bezpiecznej powłoki (SSH) uruchom następujące polecenie bazy danych HBase:
   
    ```bash
    hbase shell
    ```

2. Utwórz bazę danych HBase z dwiema rodzinami kolumn:

    ```hbaseshell   
    create 'Contacts', 'Personal', 'Office'
    list
    ```
3. Wstaw dowolne dane:
    
    ```hbaseshell   
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    scan 'Contacts'
    ```
   
    ![Powłoka HBase HDInsight Hadoop][img-hbase-shell]
4. Pobierz pojedynczy wiersz:
   
    ```hbaseshell
    get 'Contacts', '1000'
    ```
   
    Wyświetlone zostaną te same wyniki, co w przypadku polecenia scan, ponieważ istnieje tylko jeden wiersz.
   
    Aby uzyskać więcej informacji na temat schematu tabeli HBase, zobacz [wprowadzenie do projektu schematu HBase Apache][hbase-schema]. Więcej poleceń bazy danych HBase można znaleźć w [Podręczniku bazy danych Apache HBase][hbase-quick-start].
5. Wyjdź z powłoki:
   
    ```hbaseshell
    exit
    ```

**Aby zbiorczo załadować dane do tabeli kontaktów HBase**

Baza danych HBase obsługuje kilka metod ładowania danych do tabel.  Aby uzyskać więcej informacji, zobacz temat [Ładowanie zbiorcze](https://hbase.apache.org/book.html#arch.bulk.load).

Przykładowy plik danych znajdują się w publicznym kontenerze obiektów blob, *wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt*.  Plik danych ma następującą zawartość:

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

Opcjonalnie możesz utworzyć plik tekstowy i przesłać go na swoje konto magazynu. Aby uzyskać instrukcje, zobacz [przekazywanie danych na potrzeby zadań usługi Apache Hadoop w HDInsight][hdinsight-upload-data].

> [!NOTE]  
> W tej procedurze jest używana tabela kontaktów HBase utworzona w poprzedniej procedurze.

1. Z poziomu bezpiecznej powłoki (SSH) uruchom następujące polecenie, aby przekształcić plik danych do postaci StoreFiles i zapisać go w ścieżce względnej określonej przez parametr Dimporttsv.bulk.output.  Jeśli jest otwarta powłoka HBase, użyj polecenia exit, aby z niej wyjść.

    ```bash   
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Uruchom następujące polecenie, aby przekazać dane z katalogu /example/data/storeDataFileOutput do tabeli HBase:
   
    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Możesz otworzyć powłokę HBase i użyć polecenia scan w celu wyświetlenia zawartości tabeli.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Użyj Apache Hive, aby wykonać zapytanie bazy danych Apache HBase

Wykonywanie zapytań danych w tabelach HBase przy użyciu [Apache Hive](https://hive.apache.org/). W tej sekcji zostanie utworzona tabela programu Hive odwzorowująca dane w tabeli HBase, która będzie używana do wykonywania zapytań o dane w tabeli HBase.

1. Otwórz program **PuTTY** i połącz się z klastrem.  Zapoznaj się z instrukcjami w poprzedniej procedurze.
2. W sesji SSH wpisz następujące polecenie, aby uruchomić usługę Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Aby uzyskać więcej informacji o usłudze Beeline, zobacz [Używanie technologii Hive z usługą Hadoop w usłudze HDInsight z usługą Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
       
3. Uruchom następujące polecenie [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) skrypt, aby utworzyć tabelę programu Hive, która mapuje dane do tabeli HBase. Upewnij się, że utworzono wspomnianą wcześniej w tym samouczku tabelę przykładową, używając powłoki HBase przed uruchomieniem tej instrukcji.

    ```hiveql   
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

4. Uruchom poniższy skrypt HiveQL, aby wykonać zapytanie o dane w tabeli HBase:

    ```hiveql   
    SELECT count(rowkey) FROM hbasecontacts;
    ```

## <a name="use-hbase-rest-apis-using-curl"></a>Korzystanie z interfejsów API REST HBase przy użyciu programu Curl

Interfejs API REST jest zabezpieczony za pomocą [uwierzytelniania podstawowego](https://en.wikipedia.org/wiki/Basic_access_authentication). Należy zawsze tworzyć żądania przy użyciu protokołu HTTPS (HTTP Secure), aby mieć pewność, że poświadczenia są bezpiecznie wysyłane do serwera.

1. Użyj następującego polecenia, aby wyświetlić listę istniejących tabel HBase:

    ```bash
    curl -u <UserName>:<Password> \
    -G https://<ClusterName>.azurehdinsight.net/hbaserest/
    ```

1. Użyj następującego polecenia, aby utworzyć nową tabelę HBase z dwiema rodzinami kolumn:

    ```bash   
    curl -u <UserName>:<Password> \
    -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    Schemat jest podany w formacie JSon.
1. Użyj następującego polecenia, aby wstawić dane:

    ```bash   
    curl -u <UserName>:<Password> \
    -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
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
    curl -u <UserName>:<Password> \
    -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
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

1. Logowanie do interfejsu użytkownika sieci Web systemu Ambari pod https://&lt;Nazwa_klastra >. azurehdinsight.net.
2. W menu po lewej kliknij pozycję **HBase**.
3. Na górze strony kliknij pozycję **Szybkie linki**, wskaż aktywny link węzła dozorcy, a następnie kliknij pozycję **Główny interfejs użytkownika bazy danych HBase**.  Interfejs użytkownika zostanie otwarty w innej karcie przeglądarki:

   ![Główny interfejs użytkownika HDInsight HBase](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   Główny interfejs użytkownika HBase zawiera następujące sekcje:

   - serwery regionów
   - wzorce kopii zapasowej
   - tabele
   - zadania
   - atrybuty oprogramowania

## <a name="delete-the-cluster"></a>Usuwanie klastra
Aby uniknąć niespójności, zaleca się wyłączenie tabel HBase przed usunięciem klastra.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Kolejne kroki
W tym artykule pokazano, jak utworzyć klaster Apache HBase i tworzyć tabele oraz jak wyświetlać dane w tych tabelach z poziomu powłoki HBase. Przedstawiono również sposób wykonywania zapytań programu Hive względem danych w tabelach HBase oraz korzystania z interfejsów API REST HBase w języku C# w celu tworzenia tabel HBase i pobierania danych z tabeli.

Aby dowiedzieć się więcej, zobacz:

* [Omówienie bazy danych HDInsight HBase][hdinsight-hbase-overview]: Apache HBase to Apache, typu open-source, baza danych NoSQL oparta na technologii Apache Hadoop, która zapewnia dostęp losowy i wysoki poziom spójności w przypadku dużych ilości danych z częściową strukturą i bez struktury.

[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hbase-reference]: https://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: https://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]:apache-hbase-overview.md
[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: https://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hbase-shell]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png
