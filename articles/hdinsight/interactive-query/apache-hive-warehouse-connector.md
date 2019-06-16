---
title: Integrowanie platformy Apache Spark i Apache Hive za pomocą łącznika magazynu Hive
description: Dowiedz się, jak zintegrować platformę Apache Spark i Apache Hive za pomocą łącznika magazynu programu Hive w usłudze Azure HDInsight.
ms.service: hdinsight
author: nakhanha
ms.author: nakhanha
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: b245661ab8f26c1f529a049d326d2c72838c7a17
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056728"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Integrowanie platformy Apache Spark i Apache Hive za pomocą łącznika magazynu Hive

Apache Hive magazynu łącznika (HWC) jest biblioteką, która pozwala łatwiej pracować przy użyciu platformy Apache Spark i Apache Hive, dzięki obsłudze zadania, takie jak przenoszenie danych między elementy Dataframe platformy Spark i tabel programu Hive, a także kierowanie Spark dane przesyłane strumieniowo do tabel programu Hive. Hive łącznika magazynu działa jak most między platformami Spark i Hive. Obsługuje ona Scala, Java i Python do tworzenia aplikacji.

Łącznik magazynu Hive pozwala korzystać z unikatowych funkcji Hive i platformy Spark, aby tworzyć zaawansowane aplikacje danych big data. Apache Hive zapewnia obsługę transakcji bazy danych, które są niepodzielność, spójność, izolowany i trwałe (kwas). Aby uzyskać więcej informacji na kwasu i transakcje w gałęzi, zobacz [Hive transakcji](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Gałąź zapewnia również zabezpieczenia szczegółowe sterowanie przy użyciu struktury Apache Ranger i niskie opóźnienie analitycznych przetwarzania nie jest dostępna w Apache Spark.

Apache Spark jest strukturą API przesyłania strumieniowego, który daje możliwość przesyłania strumieniowego nie są dostępne w Apache Hive. Począwszy od HDInsight 4.0 Apache Spark 2.3.1 i Apache Hive 3.1.0 mają oddzielne magazyny metadanych, który może utrudnić współdziałania. Łącznik magazynu Hive ułatwia korzystanie z platformy Spark i Hive razem. Biblioteka użytkownika powoduje załadowanie danych z funkcji LLAP demonów do executors platformy Spark w sposób równoległy, dzięki czemu bardziej wydajne i skalowalne niż przy użyciu standardowego połączenia sterownika JDBC z platformy Spark do gałęzi.

![Architektura](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Operacje obsługiwane przez łącznik magazynu Hive, należą:

* Zawierająca opis tabeli
* Tworzenie tabeli danych w formacie ORC
* Wybór danych Hive i pobierania ramkę danych
* Zapisywanie ramkę danych programu Hive w usłudze batch
* Wykonywanie instrukcji update gałęzi
* Odczytywanie danych z tabeli z programu Hive, przekształcania go w Spark i zapisywanie w nową tabelę programu Hive
* Zapisywanie strumienia ramkę danych lub Spark Hive przy użyciu HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Instalacja łącznika magazynu hive

Wykonaj następujące kroki, aby skonfigurować łącznik magazynu Hive od klastra platformy Spark i interakcyjnych zapytań w usłudze Azure HDInsight:

1. Tworzenie klastra HDInsight Spark 4.0 przy użyciu witryny Azure portal za pomocą konta magazynu i niestandardowych sieci wirtualnej platformy Azure. Aby uzyskać informacje dotyczące tworzenia klastra w sieci wirtualnej platformy Azure, zobacz [HDInsight dodać do istniejącej sieci wirtualnej](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md#existingvnet).
1. Tworzenie klastra HDInsight Interactive Query (LLAP) 4.0 w witrynie Azure portal przy użyciu tego samego konta magazynu i sieci wirtualnej platformy Azure jako klaster Spark.
1. Skopiuj zawartość `/etc/hosts` pliku na headnode0 klastra zapytania interakcyjnego `/etc/hosts` pliku na headnode0 klastra Spark. Ten krok umożliwi klastra Spark rozpoznać adresów IP węzłów klastra zapytania interakcyjnego. Wyświetlanie zawartości zaktualizowany plik za pomocą `cat /etc/hosts`. Dane wyjściowe powinny wyglądać podobnie jak pokazano na poniższym zrzucie ekranu.

    ![Wyświetlanie w pliku hosts](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

1. Konfigurowanie ustawień klastra platformy Spark, wykonując następujące czynności: 
    1. Przejdź do witryny Azure portal, możesz wybrać klastry HDInsight, a następnie kliknij nazwę klastra.
    1. Po prawej stronie w obszarze **pulpity nawigacyjne klastra**, wybierz opcję **Ambari macierzystego**.
    1. W sieci web Ambari interfejsu użytkownika, kliknij przycisk **SPARK2** > **CONFIGS** > **niestandardowe spark2 — domyślne**.

        ![Konfiguracja Spark2 Ambari](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

    1. Ustaw `spark.hadoop.hive.llap.daemon.service.hosts` taką samą wartość jak właściwość **nazwę aplikacji funkcji LLAP** w obszarze **zaawansowane hive-interactive — env**. Na przykład: `@llap0`

    1. Ustaw `spark.sql.hive.hiveserver2.jdbc.url` aby parametry połączenia sterownika JDBC, który łączy się z usługi Hiveserver2 na to klastra zapytania interakcyjnego. Parametry połączenia dla klastra będzie wyglądać jak poniżej identyfikator URI. `CLUSTERNAME` jest nazwą klastra Spark i `user` i `password` parametry są ustawione na poprawne wartości dla klastra.

        ```
        jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2
        ```

        >[!Note] 
        > Adres URL JDBC powinien zawierać poświadczenia do łączenia z usługą Hiveserver2 takie jak nazwa użytkownika i hasło.

    1. Ustaw `spark.datasource.hive.warehouse.load.staging.dir` do odpowiedniego katalogu przemieszczania zgodnego systemem plików HDFS. W przypadku dwóch różnych klastrach katalogu przemieszczania należy folderu w katalogu przemieszczania konta magazynu klastra LLAP tak, aby serwera HiveServer2 ma do niego dostęp. Na przykład `wasb://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp` gdzie `STORAGE_ACCOUNT_NAME` jest nazwą konta magazynu używany przez klaster, i `STORAGE_CONTAINER_NAME` to nazwa kontenera magazynu.

    1. Ustaw `spark.datasource.hive.warehouse.metastoreUri` z wartością magazynu metadanych URI to klastra zapytania interakcyjnego. Aby znaleźć metastoreUri klastra LLAP, poszukaj **hive.metastore.uris** właściwości w Interfejsie użytkownika Ambari dla Twojej funkcji LLAP klastra w obszarze **Hive** > **zaawansowane**  >  **Ogólne**. Wartość będzie wyglądać na przykład następujący identyfikator URI:

        ```
        thrift://hn0-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083,
        thrift://hn1-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083
        ```

    1. Ustaw `spark.security.credentials.hiveserver2.enabled` do `false` dla klienta usługi YARN wdrażania trybu.
    1. Ustaw `spark.hadoop.hive.zookeeper.quorum` do kworum dozorcy LLAP klastra. Aby znaleźć dozorcy kworum dla klastra funkcji LLAP, poszukaj **hive.zookeeper.quorum** właściwości w Interfejsie użytkownika Ambari dla Twojej funkcji LLAP klastra w obszarze **Hive** > **zaawansowane**  >  **Zaawansowane witryny hive**. Wartość będzie wyglądać podobnie do następującego ciągu:

        ```
        zk1-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk4-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk6-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181
        ```

Aby przetestować konfigurację łącznika magazynu Hive, postępuj zgodnie z instrukcjami w sekcji [nawiązywania połączenia i uruchamianie zapytań](#connecting-and-running-queries).

## <a name="using-the-hive-warehouse-connector"></a>Za pomocą łącznika magazynu Hive

### <a name="connecting-and-running-queries"></a>Łączenie i uruchamiania zapytań

Można wybrać kilka różnych metod, aby nawiązać połączenie z klastrem Interactive Query i wykonywanie zapytań za pomocą łącznika magazynu Hive. Obsługiwane metody obejmują następujące narzędzia:

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* spark-submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Wszystkie przykłady zawarte w tym artykule będą wykonywane za pośrednictwem powłoki aparatu spark.

Aby rozpocząć sesję powłoki aparatu spark, wykonaj następujące czynności:

1. SSH do węzła głównego klastra. Aby uzyskać więcej informacji na temat nawiązywania połączenia z klastrem przy użyciu protokołu SSH, zobacz [nawiązywanie połączenia z HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
1. Przejdź do poprawnego katalogu, wpisując `cd /usr/hdp/current/hive_warehouse_connector` lub podać pełną ścieżkę do wszystkich plików jar używane jako parametry w poleceniu powłoki aparatu spark.
1. Wprowadź następujące polecenie, aby uruchomić powłoki spark:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Zostanie wyświetlony komunikat powitalny i `scala>` wiersza, w którym możesz wprowadzić poleceń.

1. Po uruchomieniu powłoki aparatu spark, można uruchomić wystąpienia łącznika magazynu Hive przy użyciu następujących poleceń:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Łączenie i uruchamianie zapytań w klastrach Enterprise Security pakietu (ESP)

Pakiet zabezpieczeń przedsiębiorstwa (ESP) zapewnia funkcji przeznaczonych dla przedsiębiorstw, takich jak uwierzytelnianie oparte na usłudze Active Directory, obsługa wielu użytkowników i kontrola dostępu oparta na rolach dla klastrów Apache Hadoop w usłudze Azure HDInsight. Aby uzyskać więcej informacji na temat ESP, zobacz [wprowadzenie do zabezpieczeń platformy Apache Hadoop z pakietem Enterprise Security](../domain-joined/apache-domain-joined-introduction.md).

1. Należy wykonać początkowe kroki 1 i 2 w ramach [nawiązywania połączenia i uruchamianie zapytań](#connecting-and-running-queries).
1. Typ `kinit` i zaloguj się przy użyciu użytkownika domeny.
1. Start powłoki aparatu spark z pełną listą parametrów konfiguracji, jak pokazano poniżej. Wszystkie wartości z wielkimi literami między nawiasy kątowe muszą być określone na podstawie w klastrze. Jeśli potrzebujesz dowiedzieć się, wartości jako danych wejściowych dla dowolnego z poniższych parametrów, zobacz sekcję dotyczącą [Instalatora łącznika magazynu Hive](#hive-warehouse-connector-setup).:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>Tworzenie elementy Dataframe platformy Spark z zapytań programu Hive

Wszystkie zapytania przy użyciu biblioteki użytkownika są zwracane jako ramkę danych. W poniższych przykładach pokazano sposób tworzenia podstawowego zapytania.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Wyniki zapytania są elementy Dataframe platformy Spark, które mogą być używane z platformy Spark bibliotek, takich jak MLIB oraz SparkSQL.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Zapisywanie się elementy Dataframe platformy Spark do tabel programu Hive

Platforma Spark nie obsługuje natywnie zapisu do zarządzanego tabel ACID gałęzi. Za pomocą użytkownika, jednak można napisać się wszystkie ramki danych do tabeli programu Hive. Możesz zobaczyć tę funkcję w miejscu pracy, w poniższym przykładzie:

1. Tworzenie tabeli o nazwie `sampletable_colorado` i określ jej kolumn, używając następującego polecenia:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

2. Filtruj tabelę `hivesampletable` gdzie kolumna `state` jest równa `Colorado`. To zapytanie w tej tabeli są zwracane jako elementów DataFrame aparatu Spark. Ramka danych jest zapisywany w tabeli programu Hive, a następnie `sampletable_colorado` przy użyciu `write` funkcji.
    
    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

Widać tabeli wynikowej na poniższym zrzucie ekranu.

![Pokaż tabelę wynikową](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Zapisy przesyłania strumieniowego ze strukturą

Za pomocą łącznika magazynu Hive, można użyć platformy Spark streaming można zapisać danych do tabel programu Hive.

Wykonaj poniższe kroki, aby utworzyć przykładową łącznika magazynu Hive, które pozwalają pozyskać dane ze strumienia platformy Spark na porcie localhost 9999 do tabeli programu Hive.

1. Otwórz terminal w klastrze Spark.
1. Rozpocznij usługa spark stream przy użyciu następującego polecenia:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9988).load()
    ```

1. Generowanie danych dla strumienia platformy Spark, który został utworzony, wykonując następujące czynności:
    1. Operacji otwórz kolejny terminal w tym samym klastrze Spark.
    1. W wierszu polecenia wpisz `nc -lk 9999`. To polecenie używa narzędzia netcat do przesyłania danych z poziomu wiersza polecenia do określonego portu.
    1. Wpisz wyrazy, które chcesz, aby usługa Spark stream do pozyskiwania, a następnie przez powrót karetki.
        ![dane wejściowe, aby usługa spark stream](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark-stream-data-input.png)
1. Utwórz nową tabelę programu Hive do przechowywania danych przesyłanych strumieniowo. W powłoce platformy spark wpisz następujące polecenia:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Zapis danych przesyłanych strumieniowo nowo utworzoną tabelę, używając następującego polecenia:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > `metastoreUri` i `database` opcje muszą być obecnie ustawiane ręcznie z powodu znanego błędu dotyczącego platformy Apache Spark. Aby uzyskać więcej informacji na temat tego problemu, zobacz [SPARK 25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Można wyświetlić danych wstawione do tabeli za pomocą następującego polecenia:

    ```scala
    hive.table("stream_table").show()
    ```

### <a name="securing-data-on-spark-esp-clusters"></a>Zabezpieczanie danych w klastrach platformy Spark ESP

1. Utwórz tabelę `demo` z pewnymi przykładowymi danymi, wprowadzając następujące polecenia:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Wyświetlanie zawartości tabeli za pomocą następującego polecenia. Przed zastosowaniem zasad, `demo` tabeli przedstawiono pełną kolumnę.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![Pokaz tabeli przed zastosowaniem zasady platformy ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Zastosuj kolumny maskowania zasad, która zawiera tylko cztery ostatnie znaki kolumny.  
    1. Przejdź do użytkownika administratora platformy Ranger w `https://CLUSTERNAME.azurehdinsight.net/ranger/`.
    1. Kliknij pozycję usługi Hive dla klastra w obszarze **Hive**.
        ![Pokaz tabeli przed zastosowaniem zasady platformy ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Kliknij pozycję **maskowania** kartę i następnie **— Dodawanie nowych zasad** ![Lista zasad](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)
    1. Podaj nazwę żądane zasady. Wybierz bazę danych: **Domyślne**, tabelę programu Hive: **pokaz**, kolumny Hive: **nazwa**, użytkownik: **rsadmin2**, typy dostępu: **wybierz**i **Częściowe maski: Pokaż ostatnie 4** z **wybierz opcję maskowania** menu. Kliknij pozycję **Add** (Dodaj).
                ![Lista zasad](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Wyświetlanie zawartości tabeli ponownie. Po zastosowaniu zasad ranger, widać tylko cztery ostatnie znaki kolumny.

    ![Pokaz tabeli po zastosowaniu zasad platformy ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Kolejne kroki

* [Używanie zapytań interakcyjnych w usłudze HDInsight](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [Przykłady interakcji z łącznika magazynu Hive za pomocą rozwiązania Zeppelin, usługi Livy, skryptu spark-submit i pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
