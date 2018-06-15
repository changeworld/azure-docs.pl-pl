---
title: Operacjonalizuj potoku analizy danych - Azure | Dokumentacja firmy Microsoft
description: Skonfiguruj i uruchom przykład potok danych, który jest wyzwalany przez nowe dane i tworzy zwięzły wyników.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: ashishth
ms.openlocfilehash: 7ac1ed0db15d91ef8af009c879c3634148826286
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31392191"
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Operacjonalizacja potoku analizy danych

*Potoki danych* underly wielu rozwiązań analizy danych. Jak wynika z nazwy, potoku danych przyjmuje dane pierwotne, czyści przekształca ją w razie potrzeby i następnie zazwyczaj wykonuje obliczenia lub agregacji przed przekazaniem przetworzone dane. Przetworzone dane są używane przez klientów, raporty lub interfejsów API. Potoku danych musisz podać powtarzalne wyniki, czy zgodnie z harmonogramem lub wyzwolone przez nowe dane.

W tym artykule opisano sposób operacjonalizacji Twoje potoki danych powtarzalności przy użyciu Oozie uruchamianych w klastrach HDInsight Hadoop. Przykładowy scenariusz przeprowadzi Cię przez potok danych, który przygotowuje i przetwarza dane szeregów czasowych transmitowane linii lotniczych.

W poniższym scenariuszu danych wejściowych jest prosty plik zawierający partii danych transmitowane przez jeden miesiąc. Te dane transmitowane obejmują informacje takie jak lotniska źródło i miejsce docelowe, mil znajdowały się powietrzu, wyjścia i przyjęcia razy i tak dalej. Celem tego potoku jest Podsumowując codzienne linii lotniczych wydajności, gdzie każdy linii lotniczych ma jeden wiersz dla każdego dnia o średniej opóźnienia wyjścia i przyjęcia w protokole i Łączny przebieg znajdowały się powietrzu tego dnia.

| ROK | MIESIĄC | DAY_OF_MONTH | OPERATOR |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | AA | 10.142229 | 7.862926 | 2644539 |
| 2017 | 1 | 3 | JAKO | 9.435449 | 5.482143 | 572289 |
| 2017 | 1 | 3 | DL | 6.935409 | -2.1893024 | 1909696 |

Przykład potoku oczekuje, aż dane transmitowane nowy okres czasu dociera, a następnie przechowuje te informacje szczegółowe przesyłane do magazynu danych programu Hive długoterminowej analizy. Potok tworzy również dużo mniejsze zestawu danych, który znajduje się podsumowanie tylko dziennej transmitowane danych. Dane podsumowania tego dziennej transmitowane są wysyłane do bazy danych SQL do oferuje raportów, takich jak witryny sieci Web.

Na poniższym diagramie przedstawiono przykład potoku.

![Przesyłane dane potoku](./media/hdinsight-operationalize-data-pipeline/pipeline-overview.png)

## <a name="oozie-solution-overview"></a>Omówienie rozwiązania Oozie

Apache Oozie uruchomiona w klastrze usługi HDInsight Hadoop korzysta z tego potoku.

Oozie opisuje jego potoki w postaci liczby *akcje*, *przepływy pracy*, i *koordynatora*. Akcje określają rzeczywistą pracę, aby przeprowadzić, takie jak uruchomienie zapytania programu Hive. Przepływy pracy określają sekwencję akcji. Koordynatora zdefiniuj harmonogram uruchamiania przepływu pracy. Koordynatora można również poczekać na dostępność nowych danych przed uruchomieniem wystąpienia przepływu pracy.

Na poniższym diagramie przedstawiono projekt wysokiego poziomu tego przykładu Oozie potoku.

![Dane transmitowane Oozie potoku](./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png)

### <a name="provision-azure-resources"></a>Udostępnianie zasobów platformy Azure

Tego potoku wymaga bazy danych SQL Azure oraz klaster usługi HDInsight Hadoop w tej samej lokalizacji. Baza danych SQL Azure przechowuje podsumowania dane utworzone przez potok i magazynu metadanych Oozie.

#### <a name="provision-azure-sql-database"></a>Baza danych Azure SQL udostępniania

1. Przy użyciu portalu Azure, Utwórz nową grupę zasobów o nazwie `oozie` zawiera wszystkie zasoby używane przez tego przykładu.
2. W ramach `oozie` grupy zasobów, świadczenia usługi Azure SQL Server i bazy danych. Nie trzeba bazy danych większych niż warstwie cenowej standardowa S1.
3. Przy użyciu portalu Azure, przejdź do okienka nowo wdrożonym bazy danych SQL i wybierz **narzędzia**.

    ![Przycisk narzędzia](./media/hdinsight-operationalize-data-pipeline/sql-db-tools.png)

4. Wybierz **edytora zapytań**.

    ![Edytor zapytań, przycisk](./media/hdinsight-operationalize-data-pipeline/sql-db-query-editor.png)

5. W **edytora zapytań** okienku wybierz **logowania**.

    ![Przycisk logowania](./media/hdinsight-operationalize-data-pipeline/sql-db-login1.png)

6. Wprowadź swoje poświadczenia bazy danych SQL i wybierz **OK**.

   ![Formularz logowania](./media/hdinsight-operationalize-data-pipeline/sql-db-login2.png)

7. W obszarze tekst edytora zapytań, wprowadź poniższe instrukcje SQL, aby utworzyć `dailyflights` tabeli, która zapisze podsumowanych danych z każdego działania w potoku.

    ```
    CREATE TABLE dailyflights
    (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        CARRIER CHAR(2),
        AVG_DEP_DELAY FLOAT,
        AVG_ARR_DELAY FLOAT,
        TOTAL_DISTANCE FLOAT
    )
    GO

    CREATE CLUSTERED INDEX dailyflights_clustered_index on dailyflights(YEAR,MONTH,DAY_OF_MONTH,CARRIER)
    GO
    ```

8. Wybierz **Uruchom** do wykonywania instrukcji SQL.

    ![Przycisk uruchamiania](./media/hdinsight-operationalize-data-pipeline/sql-db-run.png)

Baza danych SQL Azure jest gotowy.

#### <a name="provision-an-hdinsight-hadoop-cluster"></a>Udostępnianie klastra usługi HDInsight Hadoop

1. W portalu Azure wybierz **+ nowy** i wyszukiwania dla usługi HDInsight.
2. Wybierz pozycję **Utwórz**.
3. W okienku podstawy Podaj unikatową nazwę dla klastra, a następnie wybierz pozycję subskrypcji platformy Azure.

    ![Nazwa klastra usługi HDInsight i subskrypcji](./media/hdinsight-operationalize-data-pipeline/hdi-name-sub.png)

4. W **typ klastra** okienku wybierz **Hadoop** klastra typu **Linux** systemu operacyjnego i najnowsza wersja klastra usługi HDInsight. Pozostaw **warstwy klastra** w **standardowe**.

    ![Typ klastra usługi HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-cluster-type.png)

5. Wybierz **wybierz** dotyczyć wybór typu klastra.
6. Zakończenie **podstawy** okienko podawania hasła logowania i wybierając z `oozie` zasobów grupę z listy, a następnie wybierz **dalej**.

    ![Okienko podstawy usługi HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-basics.png)

7. W **magazynu** okienka, pozostaw ustawioną typu podstawowego magazynu **usługi Azure Storage**, wybierz pozycję **Utwórz nowy**i podaj nazwę dla nowego konta.

    ![Ustawienia konta magazynu usługi HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-storage.png)

8. Dla **ustawienia na potrzeby magazynu metadanych**w obszarze **wybierz bazę danych SQL dla gałęzi**, wybierz bazę danych utworzoną wcześniej.

    ![Ustawienia potrzeby magazynu metadanych HDInsight Hive](./media/hdinsight-operationalize-data-pipeline/hdi-metastore-hive.png)

9. Wybierz **uwierzytelniania bazy danych SQL**.

    ![Uwierzytelnianie na potrzeby magazynu metadanych Hive HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql.png)

10. Wprowadź nazwę bazy danych SQL użytkownika i hasło, a następnie wybierz pozycję **wybierz**. 

       ![Potrzeby magazynu metadanych HDInsight Hive uwierzytelnienia logowania](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql-login.png)

11. Ponownie **ustawienia na potrzeby magazynu metadanych** okienku wybierz bazy danych metadanych Oozie przechowywania i Uwierzytelnij się tak samo jak wcześniej. 

       ![Ustawienia na potrzeby magazynu metadanych usługi HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-metastore-settings.png)

12. Wybierz opcję **Dalej**.
13. Na **Podsumowanie** okienku wybierz **Utwórz** do wdrożenia klastra.

### <a name="verify-ssh-tunneling-setup"></a>Sprawdź ustawienia tunelowania SSH

Aby wyświetlić stan koordynatora i wystąpienia przepływu pracy przy użyciu konsoli sieci Web Oozie, skonfiguruj tunelu SSH do klastra usługi HDInsight. Aby uzyskać więcej informacji, zobacz [tunelu SSH](hdinsight-linux-ambari-ssh-tunnel.md).

> [!NOTE]
> Można również użyć programu Chrome z [Foxy Proxy](https://getfoxyproxy.org/) rozszerzenia, aby przeglądać zasoby sieci web klastra dla tunelu SSH. Skonfiguruj ją do serwera proxy wszystkich żądań za pośrednictwem hosta `localhost` na porcie tunelu 9876. Takie podejście jest zgodny z podsystemu systemu Windows, Linux, nazywany również Bash w systemie Windows 10.

1. Uruchom następujące polecenie, aby otworzyć tunel SSH do klastra:

    ```
    ssh -C2qTnNf -D 9876 sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
    ```

2. Sprawdź, czy działa tunelu przechodząc do Ambari w węźle głównym przechodząc do:

    http://headnodehost:8080

3. Aby uzyskać dostęp do **konsoli sieci Web Oozie** w Ambari, zaznacz **Oozie**, **szybkie linki**, a następnie wybierz **Oozie konsoli sieci Web**.

### <a name="configure-hive"></a>Skonfiguruj gałęzi

1. Pobierz przykładowy plik CSV, który zawiera dane transmitowane przez jeden miesiąc. Pobierz plik ZIP, jego `2017-01-FlightData.zip` z [repozytorium HDInsight Github](https://github.com/hdinsight/hdinsight-dev-guide) i Rozpakuj go do pliku CSV `2017-01-FlightData.csv`. 

2. Skopiuj ten plik CSV do konta usługi Magazyn Azure powiązany z klastrem usługi HDInsight i umieść go w `/example/data/flights` folderu.

Możesz skopiować plik przy użyciu połączenia usługi w sieci `bash` powłoki sesji.

1. Użyj punkt połączenia usługi, aby skopiować pliki z komputera lokalnego w magazynie lokalnym węzła głównego klastra usługi HDInsight.

    ```bash
    scp ./2017-01-FlightData.csv sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:2017-01-FlightData.csv
    ```

2. Użyj polecenia systemu plików HDFS można skopiować pliku z magazynu lokalnego węzła głównego do magazynu Azure.

    ```bash
    hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
    ```

Przykładowe dane, jest teraz dostępna. Jednak potoku wymaga dwóch tabel gałęzi do przetwarzania, jeden dla danych przychodzących (`rawFlights`) i jeden dla podsumowanych danych (`flights`). Utwórz te tabele w Ambari w następujący sposób.

1. Zaloguj się do narzędzia Ambari, przechodząc do [ http://headnodehost:8080 ](http://headnodehost:8080).
2. Wybierz z listy usług **Hive**.

    ![Wybranie gałęzi w Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png)

3. Wybierz **przejdź do widoku** obok etykiety Hive widoku 2.0.

    ![Wybranie widoku Hive w Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png)

4. W obszarze tekstu zapytania wklej poniższe instrukcje, aby utworzyć `rawFlights` tabeli. `rawFlights` Tabela zawiera schemat na odczyt plików CSV w `/example/data/flights` folderu w usłudze Azure Storage. 

    ```
    CREATE EXTERNAL TABLE IF NOT EXISTS rawflights (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        FL_DATE STRING,
        CARRIER STRING,
        FL_NUM STRING,
        ORIGIN STRING,
        DEST STRING,
        DEP_DELAY FLOAT,
        ARR_DELAY FLOAT,
        ACTUAL_ELAPSED_TIME FLOAT,
        DISTANCE FLOAT)
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
    WITH SERDEPROPERTIES 
    (
        "separatorChar" = ",",
        "quoteChar"     = "\""
    ) 
    LOCATION '/example/data/flights'
    ```

5. Wybierz **Execute** do utworzenia tabeli.

    ![Zapytanie hive w Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png)

6. Aby utworzyć `flights` tabeli, Zastąp tekst w obszarze tekstu zapytania poniższe instrukcje. `flights` Tabela jest tabelą zarządzanych Hive partycje dane ładowane do niej przez rok, miesiąc i dzień miesiąca. Ta tabela będzie zawierać wszystkie dane transmitowane historycznych, z najniższego stopnia szczegółowości w danych źródłowych o jeden wiersz na transmitowane.

    ```
    SET hive.exec.dynamic.partition.mode=nonstrict;

    CREATE TABLE flights
    (
        FL_DATE STRING,
        CARRIER STRING,
        FL_NUM STRING,
        ORIGIN STRING,
        DEST STRING,
        DEP_DELAY FLOAT,
        ARR_DELAY FLOAT,
        ACTUAL_ELAPSED_TIME FLOAT,
        DISTANCE FLOAT
    )
    PARTITIONED BY (YEAR INT, MONTH INT, DAY_OF_MONTH INT)
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
    WITH SERDEPROPERTIES 
    (
        "separatorChar" = ",",
        "quoteChar"     = "\""
    );
    ```

7. Wybierz **Execute** do utworzenia tabeli.

### <a name="create-the-oozie-workflow"></a>Tworzenie przepływów pracy Oozie

Potoki zwykle przetwarzania danych w partiach w danym przedziale czasu. W takim przypadku potok przetwarza dane transmitowane codziennie. Takie podejście umożliwia dla plików wejściowych CSV odebranie codziennie, co tydzień, co miesiąc lub co rok.

Przykładowy przepływ pracy przetwarza transmitowane danych dzień po dniu, w trzech głównych kroków:

1. Uruchamianie zapytań programu Hive, aby wyodrębnić dane dla zakresu dat tego dnia z pliku CSV źródła reprezentowany przez `rawFlights` tabeli i wstawiania danych do `flights` tabeli.
2. Uruchom zapytanie Hive do dynamicznego tworzenia tabeli tymczasowej w gałęzi do dnia, w którym zawiera kopię dane transmitowane podsumowane według dnia i operatora.
3. Kopiowanie wszystkich danych z tabeli tymczasowej codziennie w gałęzi do miejsca docelowego za pomocą narzędzia Apache Sqoop `dailyflights` tabeli w bazie danych SQL Azure. Sqoop odczytuje wierszy źródła danych w tabeli Hive znajdującej się w usłudze Azure Storage i ładuje je do bazy danych SQL za pomocą połączenia JDBC.

Te trzy kroki są koordynowane przez przepływ pracy Oozie. 

1. Utwórz kwerendę w pliku `hive-load-flights-partition.hql`.

    ```
    SET hive.exec.dynamic.partition.mode=nonstrict;
    
    INSERT OVERWRITE TABLE flights
    PARTITION (YEAR, MONTH, DAY_OF_MONTH)
    SELECT  
        FL_DATE,
        CARRIER,
        FL_NUM,
        ORIGIN,
        DEST,
        DEP_DELAY,
        ARR_DELAY,
        ACTUAL_ELAPSED_TIME,
        DISTANCE,
        YEAR,
        MONTH,
        DAY_OF_MONTH
    FROM rawflights
    WHERE year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    Zmienne Oozie należy użyć składni `${variableName}`. Te zmienne są ustawiane `job.properties` plików zgodnie z opisem w kolejnym kroku. Oozie zastępowane rzeczywistymi wartościami w czasie wykonywania.

2. Utwórz kwerendę w pliku `hive-create-daily-summary-table.hql`.

    ```
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}
    (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        CARRIER STRING,
        AVG_DEP_DELAY FLOAT,
        AVG_ARR_DELAY FLOAT,
        TOTAL_DISTANCE FLOAT
    )
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName}
    SELECT  year, month, day_of_month, carrier, avg(dep_delay) avg_dep_delay, 
            avg(arr_delay) avg_arr_delay, sum(distance) total_distance 
    FROM flights
    GROUP BY year, month, day_of_month, carrier 
    HAVING year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    To zapytanie tworzy tabeli przemieszczania, która będzie przechowywać podsumowanych danych przez jeden dzień, zanotuj instrukcji SELECT, która oblicza średnią opóźnienia i sumę odległość znajdowały powietrzu przez operatora, według dni. Dane do tej tabeli, przechowywane w znanej lokalizacji (ścieżka wskazanego przez zmienną hiveDataFolder), dzięki czemu może służyć jako źródło dla Sqoop w następnym kroku.

3. Uruchom następujące polecenie Sqoop.

    ```
    sqoop export --connect ${sqlDatabaseConnectionString} --table ${sqlDatabaseTableName} --export-dir ${hiveDataFolder} -m 1 --input-fields-terminated-by "\t"
    ```

Te trzy kroki są wyrażane jako osobne akcje trzy w następującym pliku przepływu pracy Oozie o nazwie `workflow.xml`.

```
<workflow-app name="loadflightstable" xmlns="uri:oozie:workflow:0.5">
    <start to = "RunHiveLoadFlightsScript"/>
    <action name="RunHiveLoadFlightsScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScriptLoadPartition}</script>
            <param>year=${year}</param>
            <param>month=${month}</param>
            <param>day=${day}</param>
        </hive>
        <ok to="RunHiveCreateDailyFlightTableScript"/>
        <error to="fail"/>
    </action>

    <action name="RunHiveCreateDailyFlightTableScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScriptCreateDailyTable}</script>
            <param>hiveTableName=${hiveDailyTableName}</param>
            <param>year=${year}</param>
            <param>month=${month}</param>
            <param>day=${day}</param>
            <param>hiveDataFolder=${hiveDataFolder}/${year}/${month}/${day}</param>
        </hive>
        <ok to="RunSqoopExport"/>
        <error to="fail"/>
    </action>

    <action name="RunSqoopExport">
        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.compress.map.output</name>
                <value>true</value>
            </property>
            </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveDataFolder}/${year}/${month}/${day}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\t"</arg>
            <archive>sqljdbc41.jar</archive>
            </sqoop>
        <ok to="end"/>
        <error to="fail"/>
    </action>
    <kill name="fail">
        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
    </kill>
    <end name="end"/>
</workflow-app>
```

Ścieżka ich w usłudze Azure Storage są dostępne dwa zapytania Hive, a pozostałe wartości zmiennych są dostarczane przez następujące `job.properties` pliku. Ten plik służy do konfigurowania przepływu pracy do uruchomienia w dniu 3 stycznia 2017 r.

```
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=hn0-[CLUSTERNAME].[UNIQUESTRING].dx.internal.cloudapp.net:8050
queueName=default
oozie.use.system.libpath=true
appBase=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie
oozie.wf.application.path=${appBase}/load_flights_by_day
hiveScriptLoadPartition=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-load-flights-partition.hql
hiveScriptCreateDailyTable=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-create-daily-summary-table.hql
hiveDailyTableName=dailyflights${year}${month}${day}
hiveDataFolder=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/day/${year}/${month}/${day}
sqlDatabaseConnectionString="jdbc:sqlserver://[SERVERNAME].database.windows.net;user=[USERNAME];password=[PASSWORD];database=[DATABASENAME]"
sqlDatabaseTableName=dailyflights
year=2017
month=01
day=03
```

Poniższa tabela zawiera podsumowanie każdej właściwości oraz wskazuje, gdzie można znaleźć wartości dla własnego środowiska.

| Właściwość | Wartość źródła |
| --- | --- |
| NameNode | Pełna ścieżka do kontenera magazynu Azure powiązany z klastrem usługi HDInsight. |
| jobTracker | Wewnętrzna nazwa hosta do klastra active YARN head węzła. Na stronie głównej Ambari wybierz z listy usług YARN, a następnie wybierz aktywny Resource Manager. Nazwa hosta identyfikatora URI jest wyświetlany w górnej części strony. Dołącz portu 8050. |
| queueName | Nazwa kolejki YARN używany podczas planowania zadań Hive. Pozostaw domyślne. |
| oozie.use.system.libpath | Pozostaw jako true. |
| appBase | Ścieżka do podfolderu w usłudze Azure Storage, w którym jest wdrażany Oozie przepływu pracy i pliki pomocnicze. |
| oozie.wf.application.path | Lokalizacja przepływu pracy, Oozie `workflow.xml` do uruchomienia. |
| hiveScriptLoadPartition | Ścieżka w usłudze Azure Storage do pliku zapytania Hive `hive-load-flights-partition.hql`. |
| hiveScriptCreateDailyTable | Ścieżka w usłudze Azure Storage do pliku zapytania Hive `hive-create-daily-summary-table.hql`. |
| hiveDailyTableName | Dynamicznie generowanym Nazwa do użycia dla tabeli przemieszczania. |
| hiveDataFolder | Ścieżka w usłudze Azure Storage w danych zawarty w tabeli przemieszczania. |
| sqlDatabaseConnectionString | Ciąg JDBC składni połączenia z bazą danych SQL Azure. |
| sqlDatabaseTableName | Nazwa tabeli w bazie danych SQL Azure do której są wstawiane podsumowania wierszy. Pozostaw `dailyflights`. |
| rok | Składnik roku dnia, dla których transmitowane są obliczane podsumowania. Pozostawić. |
| miesiąc | Składnik miesiąca dnia, dla których transmitowane są obliczane podsumowania. Pozostawić. |
| dzień | Dzień miesiąca składnik dnia, dla których transmitowane są obliczane podsumowania. Pozostawić. |

> [!NOTE]
> Pamiętaj zaktualizować kopię `job.properties` pliku z przeznaczeniem dla danego środowiska, aby można było wdrożyć i uruchomić przepływ pracy Oozie wartości.

### <a name="deploy-and-run-the-oozie-workflow"></a>Wdrażanie i uruchamianie przepływu pracy Oozie

Użyj połączenia z sesji bash, aby wdrożyć Oozie przepływu pracy (`workflow.xml`), zapytania Hive (`hive-load-flights-partition.hql` i `hive-create-daily-summary-table.hql`) i konfiguracji zadań (`job.properties`).  W Oozie, tylko `job.properties` pliku może istnieć w magazynie lokalnym headnode. Wszystkie inne pliki muszą być przechowywane w systemie plików HDFS, w tym wielkość magazynu Azure. Akcja Sqoop używany przez przepływ pracy jest zależna od sterownik JDBC do komunikowania się z bazą danych SQL, które muszą być kopiowane z węzła głównego do systemu plików HDFS.

1. Utwórz `load_flights_by_day` podfolder poniżej ścieżki użytkownika w magazynie lokalnym węzła głównego.

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 'mkdir load_flights_by_day'

2. Skopiuj wszystkie pliki w bieżącym katalogu ( `workflow.xml` i `job.properties` plików) do `load_flights_by_day` podfolderu.

        scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:load_flights_by_day

3. Nawiąż połączenie z węzłem głównym i przejdź do `load_flights_by_day` folderu.

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
        cd load_flights_by_day

4. Skopiuj pliki przepływu pracy do systemu plików HDFS.

        hdfs dfs -put ./* /oozie/load_flights_by_day

5. Kopiuj `sqljdbc41.jar` z lokalnego węzła głównego do folderu przepływu pracy w systemie plików HDFS:

        hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /oozie/load_flights_by_day

6. Uruchom przepływ pracy.

        oozie job -config job.properties -run

7. Sprawdź, czy stan za pomocą konsoli sieci Web programu Oozie. W ramach Ambari, zaznacz **Oozie**, **szybkie linki**, a następnie **konsoli sieci Web Oozie**. W obszarze **zadania przepływu pracy** wybierz opcję **wszystkie zadania**.

    ![Przepływy pracy konsoli sieci Web Oozie](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png)

8. Jeśli stan jest zakończyło się pomyślnie, Wyślij zapytanie do tabeli bazy danych SQL, aby wyświetlić wstawionych wierszy. Przy użyciu portalu Azure, przejdź do okienka SQL bazy danych, wybierz **narzędzia**, a następnie otwórz **edytora zapytań**.

        SELECT * FROM dailyflights

Teraz, gdy dzień pojedynczy test jest uruchomiony przepływ pracy, może zawijać się ten przepływ pracy z koordynatorem, która planuje przepływu pracy, aby była uruchamiana codziennie.

### <a name="run-the-workflow-with-a-coordinator"></a>Uruchamianie przepływu pracy z koordynatorem

Można zaplanować ten przepływ pracy, tak aby była uruchamiana codziennie (lub wszystkie dni w pewnym zakresie dat.), można użyć koordynatora. Koordynator jest definiowana za pomocą pliku XML, na przykład `coordinator.xml`:

```
<coordinator-app name="daily_export" start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" timezone="UTC" xmlns="uri:oozie:coordinator:0.4">
    <datasets>
        <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
            <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
            <done-flag></done-flag>
        </dataset>
    </datasets>
    <input-events>
        <data-in name="event_input1" dataset="ds_input1">
            <instance>${coord:current(0)}</instance>
        </data-in>
    </input-events>
    <action>
        <workflow>
            <app-path>${appBase}/load_flights_by_day</app-path>
            <configuration>
                <property>
                    <name>year</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'yyyy')}</value>
                </property>
                <property>
                    <name>month</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'MM')}</value>
                </property>
                <property>
                    <name>day</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>hiveScriptLoadPartition</name>
                    <value>${hiveScriptLoadPartition}</value>
                </property>
                <property>
                    <name>hiveScriptCreateDailyTable</name>
                    <value>${hiveScriptCreateDailyTable}</value>
                </property>
                <property>
                    <name>hiveDailyTableNamePrefix</name>
                    <value>${hiveDailyTableNamePrefix}</value>
                </property>
                <property>
                    <name>hiveDailyTableName</name>
                    <value>${hiveDailyTableNamePrefix}${coord:formatTime(coord:nominalTime(), 'yyyy')}${coord:formatTime(coord:nominalTime(), 'MM')}${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>hiveDataFolderPrefix</name>
                    <value>${hiveDataFolderPrefix}</value>
                </property>
                <property>
                    <name>hiveDataFolder</name>
                    <value>${hiveDataFolderPrefix}${coord:formatTime(coord:nominalTime(), 'yyyy')}/${coord:formatTime(coord:nominalTime(), 'MM')}/${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>sqlDatabaseConnectionString</name>
                    <value>${sqlDatabaseConnectionString}</value>
                </property>
                <property>
                    <name>sqlDatabaseTableName</name>
                    <value>${sqlDatabaseTableName}</value>
                </property>
            </configuration>
        </workflow>
    </action>
</coordinator-app>
```

Jak widać, większość koordynatora właśnie jest przekazanie informacji o konfiguracji do wystąpienia przepływu pracy. Istnieje jednak kilka istotnych elementów do wyróżnienia.

* Punkt 1: `start` i `end` atrybutów na `coordinator-app` elementu kontrolować przedział czasu, w którym jest uruchamiany koordynatora.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    Koordynator jest odpowiedzialny za działania w ramach planowania `start` i `end` data zakresu, zgodnie z interwałem określonym przez `frequency` atrybutu. Każdego zaplanowanego działania z kolei uruchamia przepływ pracy, zgodnie z konfiguracją. W powyższa definicja koordynatora Koordynator jest skonfigurowany do uruchamiania działań z 1 stycznia 2017 do 5. stycznia 2017 r. Częstotliwość jest ustawiona na 1 dzień przez [język wyrażeń Oozie](http://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) wyrażenie częstotliwość `${coord:days(1)}`. Powoduje to koordynatora planowania akcji (i dlatego przepływu pracy) raz dziennie. Dla zakresów daty, które znajdują się w przeszłości, jak w poniższym przykładzie akcji zostanie zaplanowane do uruchomienia bez opóźnień. Początek Data, w którym akcja jest zaplanowane do uruchomienia jest wywoływana *nominalnego czas*. Na przykład do przetwarzania danych dla 1 stycznia 2017 koordynator zaplanowane akcji nominalnego czas 2017-01-01T00:00:00 GMT.

* Punkt 2: W zakresie przepływu pracy `dataset` element określa, gdzie ma zostać wyszukane w systemie plików HDFS danych dla określonego zakresu i konfiguruje sposób Oozie Określa, czy dane są dostępne jeszcze do przetwarzania.

    ```
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    Ścieżka do danych w systemie plików HDFS jest tworzona dynamicznie zgodnie z dostarczonego w wyrażenia `uri-template` elementu. W tym koordynatora częstotliwość jeden dzień jest również używany z zestawem danych. Podczas daty rozpoczęcia i zakończenia w kontrolce elementu koordynatora podczas działania są zaplanowane (i definiuje czas nominalnego), `initial-instance` i `frequency` w zestawie danych kontrolować obliczenia daty, który jest używany podczas konstruowania `uri-template`. W takim przypadku wartość wystąpieniem początkowej jeden dzień przed rozpoczęciem koordynatora, aby upewnić się, że przejmuje pierwszy dzień obiektu (1/1/2017), przez które danych. Obliczenia daty zestawu danych przetwarza od wartości `initial-instance` (12/31 grudnia 2016 r.) zaawansowane w przyrostach częstotliwość zestawu danych (1 dzień), aż do znalezienia najnowszych datę, która nie przekazuje nominalnego czas ustawiony przez koordynatora (2017-01-01T00:00:00 GMT dla pierwszego akcji).

    Pustych `done-flag` element wskazuje, że gdy Oozie sprawdza obecność danych wejściowych w wyznaczonym czasie, Oozie określa danych czy dostępne obecności pliku lub katalogu. W tym przypadku jest obecność pliku csv. Jeśli plik csv jest obecny, Oozie zakłada danych jest gotowa i uruchamia wystąpienia przepływu pracy można przetworzyć pliku. Jeśli plik csv, nie jest obecny nie istnieje, Oozie przyjęto założenie, że dane nie jest jeszcze gotowa do przepływu pracy z programem przechodzi w stan oczekiwania.

* Punkt 3: `data-in` element określa określonej sygnatury czasowej do użycia jako nominalna czas podczas zastępowania wartości `uri-template` dla skojarzonego elementu dataset.

    ```
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    W takim przypadku Przypisz wystąpienie do wyrażenia `${coord:current(0)}`, co przekłada się przy użyciu nominalnego czas działania pierwotnie zaplanowane przez koordynatora. Innymi słowy koordynator planuje akcję wykonania nominalnego czas 2017-01/01, następnie 2017-01/01 po co to jest używany do zastępowania roku (2017) i zmiennych miesiąc (01) w szablon identyfikatora URI. Po szablon identyfikatora URI jest obliczana dla tego wystąpienia, Oozie sprawdza, czy oczekiwany katalog lub plik jest dostępny i odpowiednio planuje przy następnym uruchomieniu przepływu pracy.

Trzy poprzednimi punktami połączenie umożliwia uzyskanie sytuacji, w którym koordynator planuje przetwarzania źródła danych w sposób każdego dnia. 

* Punkt 1: Koordynator rozpoczyna się od nominalnego Data 2017-01-01.

* Punkt 2: Oozie szuka danych dostępne w `sourceDataFolder/2017-01-FlightData.csv`.

* Punkt 3: Jeśli Oozie znajdzie się ten plik, planowana wystąpienia przepływu pracy, który będzie przetwarzać dane dla 2017-01-01. Oozie kontynuuje przetwarzanie 2017-01-02. Ocena powtarza się maksymalnie z wyjątkiem 2017-01-05.

Zgodnie z przepływami pracy, konfigurację koordynatora jest zdefiniowany w `job.properties` pliku, który jest nadzbiorem ustawień używanych przez przepływ pracy.

```
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=hn0-[CLUSTERNAME].[UNIQUESTRING].dx.internal.cloudapp.net:8050
queueName=default
oozie.use.system.libpath=true
appBase=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie
oozie.coord.application.path=${appBase}
sourceDataFolder=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/
hiveScriptLoadPartition=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-load-flights-partition.hql
hiveScriptCreateDailyTable=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-create-daily-summary-table.hql
hiveDailyTableNamePrefix=dailyflights
hiveDataFolderPrefix=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/day/
sqlDatabaseConnectionString="jdbc:sqlserver://[SERVERNAME].database.windows.net;user=[USERNAME];password=[PASSWORD];database=[DATABASENAME]"
sqlDatabaseTableName=dailyflights

```

Tylko nowe właściwości wprowadzone w tym `job.properties` plików są:

| Właściwość | Wartość źródła |
| --- | --- |
| oozie.coord.application.path | Określa lokalizację `coordinator.xml` plik zawierający koordynatora Oozie do uruchomienia. |
| hiveDailyTableNamePrefix | Prefiks używany podczas dynamicznego tworzenia nazwy tabeli w tabeli przemieszczania. |
| hiveDataFolderPrefix | Prefiks ścieżki, w którym będzie przechowywany tabel przemieszczania. |

### <a name="deploy-and-run-the-oozie-coordinator"></a>Wdrażanie i uruchamianie Oozie Coordinator

Aby uruchomić proces z koordynatorem, przejdź w podobny sposób jak w przypadku przepływu pracy, z wyjątkiem pracy z folderu o jeden poziom wyżej folderu zawierającego przepływ pracy. Tę Konwencję folderu oddziela koordynatora od przepływów pracy na dysku, aby można było skojarzyć jednego koordynatora z różnych podrzędne przepływy pracy.

1. Użyj punkt połączenia usługi z komputera lokalnego, aby skopiować pliki koordynatora do magazynu lokalnego węzła głównego klastra.

    ```bash
    scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:~
    ```

2. Nawiąż połączenie z węzłem głównym.

    ```bash
    ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 
    ```

3. Skopiuj pliki koordynatora do systemu plików HDFS.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. Uruchom koordynatora.

    ```bash
    oozie job -config job.properties -run
    ```

5. Sprawdź stan za pomocą konsoli sieci Web programu Oozie, wybierając ten czas **koordynator zadań** kartę, a następnie **wszystkie zadania**.

    ![Oozie Web Console Coordinator Jobs](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png)

6. Wybierz wystąpienie koordynatora, aby wyświetlić listę zaplanowanych akcji. W takim przypadku powinna zostać wyświetlona cztery akcje wraz z czasem nominalnego w zakresie od 1/1/2017 do 1/4/2017 r.

    ![Oozie Web Console Coordinator Job](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png)

    Każda akcja na tej liście odnosi się do wystąpienia przepływu pracy, który przetwarza jeden dzień, przez które danych, której początek danego dnia jest określane przez nominalnego czasu.

## <a name="next-steps"></a>Kolejne kroki

* [Apache Oozie dokumentacji](http://oozie.apache.org/docs/4.2.0/index.html)

<!-- * Build the same pipeline [using Azure Data Factory](tbd.md).  -->
