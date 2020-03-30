---
title: Apache Spark & Hive — łącznik magazynu hive — Usługa Azure HDInsight
description: Dowiedz się, jak zintegrować apache spark i apache hive z łącznikiem magazynu hive w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: f386530ffb3a074a5c1db1d9f28535d28c8b1284
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252407"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Integracja platformy Apache Spark i ula Apache z łącznikiem magazynu hive

Apache Hive Warehouse Connector (HWC) to biblioteka, która umożliwia łatwiejszą pracę z Apache Spark i Apache Hive, obsługując zadania, takie jak przenoszenie danych między tabelami Spark DataFrames i Hive, a także kierowanie danych przesyłania strumieniowego platformy Spark do tabel hive. Łącznik magazynu gałęzi działa jak most między spark i hive. Obsługuje Scala, Java i Python dla rozwoju.

Łącznik magazynu hive umożliwia korzystanie z unikatowych funkcji hive i spark do tworzenia zaawansowanych aplikacji big data. Apache Hive oferuje obsługę transakcji bazy danych, które są Atomic, Spójne, Izolowane i trwałe (ACID). Aby uzyskać więcej informacji na temat ACID i transakcji w hive, zobacz [Transakcje hive](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Hive oferuje również szczegółowe mechanizmy kontroli bezpieczeństwa za pośrednictwem Apache Ranger i przetwarzania analitycznego o małym opóźnieniu, które nie są dostępne w apache spark.

Apache Spark, ma structured streaming API, który daje możliwości przesyłania strumieniowego nie są dostępne w Apache Hive. Począwszy od HDInsight 4.0, Apache Spark 2.3.1 i Apache Hive 3.1.0 mają oddzielne magazyny metastores, które mogą utrudniać współdziałanie. Łącznik magazynu hive ułatwia używanie spark i hive razem. Biblioteka HWC ładuje dane z demonów LLAP do executorów platformy Spark równolegle, dzięki czemu jest bardziej wydajna i skalowalna niż przy użyciu standardowego połączenia JDBC z platformy Spark do hive.

![architektura łącznika magazynu ul](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Niektóre operacje obsługiwane przez łącznik magazynu hive są:

* Opisywanie tabeli
* Tworzenie tabeli dla danych w formacie ORC
* Wybieranie danych gałęzi i pobieranie elementu DataFrame
* Zapisywanie elementu DataFrame do gałęzi w partii
* Wykonywanie instrukcji aktualizacji gałęzi
* Odczytywanie danych tabeli z gałęzi, przekształcanie ich w programach Spark i zapisywanie ich w nowej tabeli ula
* Zapisywanie strumienia DataFrame lub Spark do gałęzi przy użyciu usługi HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Konfiguracja łącznika magazynu gałęzi

Wykonaj następujące kroki, aby skonfigurować łącznik magazynu gałęzi między klastrem spark i interakcyjne zapytania w usłudze Azure HDInsight:

### <a name="create-clusters"></a>Tworzenie klastrów

1. Utwórz klaster platformy HDInsight Spark **4.0** z kontem magazynu i niestandardową siecią wirtualną platformy Azure. Aby uzyskać informacje dotyczące tworzenia klastra w sieci wirtualnej platformy Azure, zobacz [Dodawanie usługi HDInsight do istniejącej sieci wirtualnej](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).

1. Utwórz klaster programu HDInsight Interactive Query (LLAP) **4.0** z tym samym kontem magazynu i siecią wirtualną platformy Azure co klaster platformy Spark.

### <a name="modify-hosts-file"></a>Modyfikowanie pliku hosts

Skopiuj `/etc/hosts` informacje o węźle z pliku na headnode0 `/etc/hosts` klastra zapytań interaktywnych i połącz informacje z plikiem głównym (headnode0 klastra platformy Spark). Ten krok umożliwi klastrowi platformy Spark rozpoznawanie adresów IP węzłów w klastrze zapytań interaktywnych. Wyświetl zawartość zaktualizowanego pliku `cat /etc/hosts`za pomocą pliku . Ostateczne dane wyjściowe powinny wyglądać mniej więcej tak, jak pokazano na poniższym zrzucie ekranu.

![plik hostów łącznika magazynu gałęzi](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>Zbieranie wstępnych informacji

#### <a name="from-your-interactive-query-cluster"></a>Z klastra zapytań interaktywnych

1. Przejdź do strony gałęzi Apache Ambari `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` klastra, używając nazwy `LLAPCLUSTERNAME` klastra zapytań interaktywnych.

1. Przejdź do **sekcji Advanced** > **General** > **hive.metastore.uris** i zanotuj wartość. Wartość może być podobna do: `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`.

1. Przejdź do **zaawansowanego** > **zaawansowanego hive-site** > **hive.zookeeper.quorum** i zanotuj wartość. Wartość może być podobna do: `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`.

#### <a name="from-your-apache-spark-cluster"></a>Z klastra Apache Spark

1. Przejdź do strony gałęzi Apache Ambari `https://SPARKCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` klastra, używając nazwy `SPARKCLUSTERNAME` klastra Apache Spark.

1. Przejdź do lokalizacji w sekcji **zaawansowanej** > **hive-interactive-site** > **hive.llap.daemon.service.hosts** i zanotuj wartość. Wartość może być podobna do: `@llap0`.

### <a name="configure-spark-cluster-settings"></a>Konfigurowanie ustawień klastra platformy Spark

Z interfejsu użytkownika sieci Web Spark Ambari przejdź do ustawień domyślnych **spark2** > **CONFIGS** > **Custom spark2.**

![Konfiguracja Apache Ambari Spark2](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

Wybierz **dodaj właściwość...** w razie potrzeby, aby dodać/zaktualizować następujące elementy:

| Klucz | Wartość |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|Wartość uzyskana wcześniej z **hive.llap.daemon.service.hosts**.|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`. Ustaw ciąg połączenia JDBC, który łączy się z usługą Hiveserver2 w klastrze zapytań interaktywnych. ZASTĄP `LLAPCLUSTERNAME` nazwą klastra zapytań interaktywnych. Zamień `PWD` na rzeczywiste hasło.|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. Ustaw odpowiedni katalog przemieszczania zgodny z systemem HDFS. Jeśli masz dwa różne klastry, katalog przemieszczania powinien być folderem w katalogu przemieszczania konta magazynu klastra LLAP, tak aby HiveServer2 miał do niego dostęp.  Zastąp `STORAGE_ACCOUNT_NAME` nazwą konta magazynu używanego przez `STORAGE_CONTAINER_NAME` klaster i nazwą kontenera magazynu.|
|`spark.datasource.hive.warehouse.metastoreUri`|Wartość uzyskana wcześniej z **hive.metastore.uris**.|
|`spark.security.credentials.hiveserver2.enabled`|`false`dla trybu wdrażania klienta YARN.|
|`spark.hadoop.hive.zookeeper.quorum`|Wartość uzyskana wcześniej z **hive.zookeeper.quorum**.|

W razie potrzeby zapisz zmiany i uruchom ponownie składniki.

## <a name="using-the-hive-warehouse-connector"></a>Korzystanie z łącznika magazynu gałęzi

### <a name="connecting-and-running-queries"></a>Łączenie i uruchamianie zapytań

Można wybrać jedną z kilku różnych metod łączenia się z klastrem zapytań interaktywnych i wykonywania zapytań przy użyciu łącznika magazynu hive. Obsługiwane metody obejmują następujące narzędzia:

* [iskry](../spark/apache-spark-shell.md)
* PySpark (pyspark)
* spark-submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Wszystkie przykłady podane w tym artykule będą wykonywane za pośrednictwem powłoki iskrowej.

Aby rozpocząć sesję powłoki iskierki, wykonaj następujące czynności:

1. SSH do headnode dla klastra Apache Spark. Aby uzyskać więcej informacji na temat łączenia się z klastrem za pomocą funkcji SSH, zobacz Łączenie się z [hdinsight (Apache Hadoop) przy użyciu funkcji SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Wprowadź następujące polecenie, aby uruchomić powłokę iskrzącą:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    Zostanie wyświetlenie powitalnej `scala>` wiadomości i monitu, w którym można wprowadzić polecenia.

1. Po uruchomieniu powłoki iskrowej wystąpienie łącznika magazynu gałęzi można uruchomić za pomocą następujących poleceń:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Łączenie i uruchamianie zapytań w klastrach pakietu zabezpieczeń przedsiębiorstwa (ESP)

Pakiet zabezpieczeń przedsiębiorstwa (ESP) zapewnia funkcje klasy korporacyjnej, takie jak uwierzytelnianie oparte na usłudze Active Directory, obsługa wielu użytkowników i kontrola dostępu oparta na rolach dla klastrów Apache Hadoop w usłudze Azure HDInsight. Aby uzyskać więcej informacji na temat esp, zobacz [Korzystanie z pakietu zabezpieczeń przedsiębiorstwa w programie HDInsight](../domain-joined/apache-domain-joined-architecture.md).

1. SSH do headnode dla klastra Apache Spark. Aby uzyskać więcej informacji na temat łączenia się z klastrem za pomocą funkcji SSH, zobacz Łączenie się z [hdinsight (Apache Hadoop) przy użyciu funkcji SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Wpisz `kinit` i zaloguj się u użytkownika domeny.

1. Rozpocznij iskrę z pełną listą parametrów konfiguracji, jak pokazano poniżej. Wszystkie wartości we wszystkich wielkich literach między nawiasami kątowymi muszą być określone na podstawie klastra. Jeśli chcesz znaleźć wartości do wprowadzenia dla któregokolwiek z poniższych parametrów, zobacz sekcję [dotyczącą konfiguracji łącznika magazynu hive](#hive-warehouse-connector-setup).:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>Tworzenie ram danych platformy Spark z zapytań gałęzi

Wyniki wszystkich zapytań przy użyciu biblioteki HWC są zwracane jako DataFrame. Poniższe przykłady pokazują, jak utworzyć kwerendę podstawową.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Wyniki kwerendy są Spark DataFrames, które mogą być używane z biblioteki Spark, takich jak MLIB i SparkSQL.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Zapisywanie ram danych platformy Spark do tabel gałęzi

Spark nie natywnie obsługuje zapisywania do zarządzanych tabel ACID hive. Za pomocą HWC, jednak można zapisać dowolny DataFrame do tabeli hive. Tę funkcję można zobaczyć w pracy w poniższym przykładzie:

1. Utwórz tabelę o nazwie `sampletable_colorado` i określ jej kolumny za pomocą następującego polecenia:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Filtruj `hivesampletable` tabelę, w której kolumna `state` jest `Colorado`równa . Ta kwerenda tabeli gałęzi jest zwracana jako ramka danych platformy Spark. Następnie DataFrame jest zapisywany w `sampletable_colorado` tabeli Hive za pomocą `write` funkcji.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. Wyświetl wyniki za pomocą następującego polecenia:

    ```scala
    hive.table("sampletable_colorado").show()
    ```

    ![łącznik magazynu gałęzi pokazuje tabelę gałęzi](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Ustrukturyzowane zapisy strumieniowe

Za pomocą łącznika magazynu hive można używać przesyłania strumieniowego platformy Spark do zapisywania danych w tabelach gałęzi.

Wykonaj poniższe kroki, aby utworzyć przykład łącznika magazynu hive, który pochyli dane ze strumienia platformy Spark na porcie localhost 9999 do tabeli hive.

1. Postępuj zgodnie z instrukcjami w obszarze [Łączenie i uruchamianie kwerend](#connecting-and-running-queries).

1. Rozpocznij strumień iskrowy następującym poleceniem:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Generuj dane dla utworzonego strumienia platformy Spark, wykonując następujące kroki:
    1. Otwórz drugą sesję SSH w tym samym klastrze platformy Spark.
    1. W wierszu polecenia `nc -lk 9999`wpisz polecenie . To polecenie używa narzędzia netcat do wysyłania danych z wiersza polecenia do określonego portu.

1. Wróć do pierwszej sesji SSH i utwórz nową tabelę hive do przechowywania danych przesyłania strumieniowego. W osłonie iskry wprowadź następujące polecenie:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Następnie zapisz dane przesyłania strumieniowego do nowo utworzonej tabeli za pomocą następującego polecenia:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > Opcje `metastoreUri` `database` i opcje muszą być obecnie ustawione ręcznie ze względu na znany problem w Apache Spark. Aby uzyskać więcej informacji na temat tego problemu, zobacz [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Wróć do drugiej sesji SSH i wprowadź następujące wartości:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Wróć do pierwszej sesji SSH i zanotuj krótkie działanie. Aby wyświetlić dane, użyj następującego polecenia:

    ```scala
    hive.table("stream_table").show()
    ```

Użyj **klawiszy Ctrl + C,** aby zatrzymać netcat w drugiej sesji SSH. Służy `:q` do zamykania powłoki iskrowej podczas pierwszej sesji SSH.

### <a name="securing-data-on-spark-esp-clusters"></a>Zabezpieczanie danych w klastrach Spark ESP

1. Utwórz `demo` tabelę z przykładowymi danymi, wprowadzając następujące polecenia:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Wyświetl zawartość tabeli za pomocą następującego polecenia. Przed zastosowaniem zasad `demo` w tabeli jest wyświetlana pełna kolumna.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![tabela demonstracyjna przed zastosowaniem zasad ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Zastosuj zasadę maskowania kolumn, która pokazuje tylko cztery ostatnie znaki kolumny.  
    1. Przejdź do interfejsu użytkownika administratora rangera w `https://CLUSTERNAME.azurehdinsight.net/ranger/`.
    1. Kliknij usługę Gałąź dla klastra w obszarze **Gałąź**.
        ![menedżer serwisu ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Kliknij kartę **Maskowanie,** a następnie **dodaj nowe zasady**

        ![lista zasad gałąź łącznika magazynu gałęzi ul](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. Podaj żądaną nazwę zasad. Wybierz bazę danych: **Domyślna**, Tabela gałęzi: **demo**, Kolumna Gałąź: **nazwa**, Użytkownik: **rsadmin2**, Typy dostępu: **select**, i **maska częściowa: pokaż ostatnie 4** z menu **Wybierz opcję maskowania.** Kliknij przycisk **Dodaj**.
                ![tworzenie zasad](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Ponownie wyświetl zawartość tabeli. Po zastosowaniu zasad ranger, możemy zobaczyć tylko cztery ostatnie znaki kolumny.

    ![tabela demonstracyjna po zastosowaniu zasad ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Następne kroki

* [Używanie zapytań interakcyjnych w usłudze HDInsight](./apache-interactive-query-get-started.md)
* [Przykłady interakcji z łącznikiem magazynu hive przy użyciu zeppelin, Livy, spark-submit i pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
