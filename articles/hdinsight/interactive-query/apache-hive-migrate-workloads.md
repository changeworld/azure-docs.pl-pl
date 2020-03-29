---
title: Migrowanie obciążeń gałęzi usługi Azure HDInsight 3.6 do usługi HDInsight 4.0
description: Dowiedz się, jak migrować obciążenia gałęzi Apache w programie HDInsight 3.6 do HDInsight 4.0.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: ec96189185a06c1fcbd95eed6216ade47f3089c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214646"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrowanie obciążeń gałęzi usługi Azure HDInsight 3.6 do usługi HDInsight 4.0

W tym dokumencie pokazano, jak przeprowadzić migrację obciążeń Apache Hive i LLAP w programie HDInsight 3.6 do HDInsight 4.0. HdInsight 4.0 udostępnia nowsze funkcje Hive i LLAP, takie jak zmaterializowane widoki i buforowanie wyników zapytań. Podczas migracji obciążeń do programu HDInsight 4.0 można użyć wielu nowszych funkcji hive 3, które nie są dostępne w programie HDInsight 3.6.

Ten artykuł obejmuje następujące tematy:

* Migracja metadanych hive do pliku HDInsight 4.0
* Bezpieczna migracja tabel KWASOWYCH i niekALNYCH
* Zachowanie zasad zabezpieczeń hive w wersjach HDInsight
* Wykonywanie i debugowanie zapytań z programu HDInsight 3.6 do HDInsight 4.0

Jedną z zalet hive jest możliwość eksportowania metadanych do zewnętrznej bazy danych (dalej Hive Metastore). **Magazyn hive** jest odpowiedzialny za przechowywanie statystyk tabel, w tym lokalizacji przechowywania tabel, nazw kolumn i informacji o indeksie tabeli. Schemat bazy danych magazynu metastore różni się między wersjami hive. Zalecanym sposobem uaktualnienia magazynu metastore hive bezpiecznie jest utworzenie kopii i uaktualnienia kopii zamiast bieżącego środowiska produkcyjnego.

## <a name="copy-metastore"></a>Kopiuj metasklep

Pliki HDInsight 3.6 i HDInsight 4.0 wymagają różnych schematów metastore i nie mogą współużytkować pojedynczego magazynu metastore.

### <a name="external-metastore"></a>Metasklep zewnętrzny

Utwórz nową kopię zewnętrznego magazynu metastore. Jeśli używasz zewnętrznego magazynu metastore, jednym z bezpiecznych i łatwych sposobów, aby wykonać kopię magazynu meta jest [przywrócenie bazy danych](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore) o innej nazwie przy użyciu funkcji przywracania bazy danych SQL.  Zobacz [Korzystanie z zewnętrznych magazynów metadanych w usłudze Azure HDInsight,](../hdinsight-use-external-metadata-stores.md) aby dowiedzieć się więcej o dołączaniu zewnętrznego magazynu metasklepu do klastra USŁUGI HDInsight.

### <a name="internal-metastore"></a>Wewnętrzny metasklep

Jeśli używasz wewnętrznego magazynu metastore, można użyć kwerend do eksportowania definicji obiektów w arkuszu meteoringu hive i importowania ich do nowej bazy danych.

Po zakończeniu tego skryptu zakłada się, że stary klaster nie będzie już używany do uzyskiwania dostępu do dowolnej tabel lub baz danych, o których mowa w skrypcie.

> [!NOTE]
> W przypadku tabel ACID zostanie utworzona nowa kopia danych pod tabelą.

1. Połącz się z klastrem HDInsight przy użyciu [klienta bezpiecznej powłoki (SSH).](../hdinsight-hadoop-linux-use-ssh-unix.md)

1. Połącz się z HiveServer2 z [klientem Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) z otwartej sesji SSH, wprowadzając następujące polecenie:

    ```hiveql
    for d in `beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show databases;"`; 
    do
        echo "Scanning Database: $d"
        echo "create database if not exists $d; use $d;" >> alltables.hql; 
        for t in `beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show tables;"`;
        do
            echo "Copying Table: $t"
            ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;"`;

            echo "$ddl;" >> alltables.hql;
            lowerddl=$(echo $ddl | awk '{print tolower($0)}')
            if [[ $lowerddl == *"'transactional'='true'"* ]]; then
                if [[ $lowerddl == *"partitioned by"* ]]; then
                    # partitioned
                    raw_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "CREATE TABLE .*" | cut -d"(" -f2- | cut -f1 -d")" | sed 's/`//g');
                    ptn_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "PARTITIONED BY .*" | cut -f1 -d")" | cut -d"(" -f2- | sed 's/`//g');
                    final_cols=$(echo "(" $raw_cols "," $ptn_cols ")")

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t $final_cols TBLPROPERTIES ('transactional'='false');";
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    parsed_ptn_cols=$(echo $ptn_cols| sed 's/ [a-z]*,/,/g' | sed '$s/\w*$//g');
                    echo "create table flattened_$t $final_cols;" >> alltables.hql;
                    echo "load data inpath '$dir' into table flattened_$t;" >> alltables.hql;
                    echo "insert into $t partition($parsed_ptn_cols) select * from flattened_$t;" >> alltables.hql;
                    echo "drop table flattened_$t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                else
                    # not partitioned
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t like $t TBLPROPERTIES ('transactional'='false');";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    echo "load data inpath '$dir' into table $t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                fi
            fi
            echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t;" >> alltables.hql;
        done;
    done
    ```

    To polecenie generuje plik o nazwie **alltables.hql**.

1. Zamknij sesję SSH. Następnie wprowadź polecenie scp, aby pobrać **alltables.hql** lokalnie.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. Prześlij **alltables.hql** do *nowego* klastra HDInsight.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Następnie użyj SSH, aby połączyć się z *nowym* klastrem HDInsight. Uruchom następujący kod z sesji SSH:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -i alltables.hql
    ```


## <a name="upgrade-metastore"></a>Aktualizacja metasklepu

Po zakończeniu **kopiowania** metastore uruchom skrypt uaktualnienia schematu w [akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md) w istniejącym klastrze HDInsight 3.6, aby uaktualnić nowy magazyn meta do schematu hive 3. Dzięki temu baza danych ma być dołączony jako HDInsight 4.0 metastore.

Użyj wartości w poniższej tabeli. Zamień `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` na odpowiednie wartości dla **skopiowanego** magazynu mete hive, oddzielone spacjami. Nie dołączaj ".database.windows.net" podczas określania nazwy serwera SQL.

|Właściwość | Wartość |
|---|---|
|Typ skryptu|- Niestandardowe|
|Nazwa|Uaktualnienie gałęzi|
|Identyfikator URI skryptu bash|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Typy węzłów|Head|
|Parametry|HASŁO NAZWY UŻYTKOWNIKA BAZY DANYCH SQLSERVERNAME|

> [!Warning]  
> Uaktualnienie, które konwertuje schemat metadanych HDInsight 3.6 na schemat HDInsight 4.0, nie można odwrócić.

Uaktualnienie można zweryfikować, uruchamiając następującą kwerendę sql w bazie danych:

```sql
select * from dbo.version
```

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Migrowanie tabel gałęzi do systemu HDInsight 4.0

Po wykonaniu poprzedniego zestawu kroków migracji magazynu hive metastore do hdinsight 4.0 tabele i bazy danych zarejestrowane w metastore będą widoczne z `show tables` `show databases` poziomu klastra HDInsight 4.0 przez wykonanie lub z poziomu klastra. Zobacz [wykonywanie kwerend w wersjach usługi HDInsight, aby](#query-execution-across-hdinsight-versions) uzyskać informacje na temat wykonywania zapytań w klastrach HDInsight 4.0.

Rzeczywiste dane z tabel nie są jednak dostępne, dopóki klaster nie będzie miał dostępu do niezbędnych kont magazynu. Aby upewnić się, że klaster HDInsight 4.0 ma dostęp do tych samych danych, co stary klaster HDInsight 3.6, wykonaj następujące czynności:

1. Określ konto magazynu platformy Azure tabeli lub bazy danych.

1. Jeśli klaster usługi HDInsight 4.0 jest już uruchomiony, dołącz konto magazynu platformy Azure do klastra za pośrednictwem usługi Ambari. Jeśli nie utworzono jeszcze klastra HDInsight 4.0, upewnij się, że konto magazynu platformy Azure jest określone jako podstawowe lub pomocnicze konto magazynu klastra. Aby uzyskać więcej informacji na temat dodawania kont magazynu do klastrów USŁUGI HDInsight, zobacz [Dodawanie dodatkowych kont magazynu do usługi HDInsight](../hdinsight-hadoop-add-storage.md).

## <a name="deploy-new-hdinsight-40-and-connect-to-the-new-metastore"></a>Wdrażanie nowego pliku HDInsight 4.0 i łączenie się z nowym sklepem meteoretystycznym

Po zakończeniu uaktualniania schematu należy wdrożyć nowy klaster programu HDInsight 4.0 i połączyć uaktualniony magazyn metastore. Jeśli masz już wdrożony 4.0, ustaw go tak, aby można było połączyć się z metasklep z Ambari.

## <a name="run-schema-migration-script-from-hdinsight-40"></a>Uruchamianie skryptu migracji schematu z programu HDInsight 4.0

Tabele są traktowane inaczej w formatach HDInsight 3.6 i HDInsight 4.0. Z tego powodu nie można udostępnić tych samych tabel dla klastrów różnych wersji. Jeśli chcesz używać hdinsight 3.6 w tym samym czasie co HDInsight 4.0, musisz mieć oddzielne kopie danych dla każdej wersji.

Obciążenie gałęzi może zawierać kombinację tabel ACID i non-ACID. Jedną z kluczowych różnic między gałęzi na HDInsight 3.6 (Hive 2) i Hive na HDInsight 4.0 (Hive 3) jest zgodność acid dla tabel. W programie HDInsight 3.6 włączenie zgodności z hive acid wymaga dodatkowej konfiguracji, ale w tabelach HDInsight 4.0 domyślnie są zgodne z ACID. Jedyną czynnością wymaganą przed migracją jest uruchomienie głównego zagęszczania względem tabeli ACID w klastrze 3.6. W widoku Gałąź lub z beeline uruchom następującą kwerendę:

```sql
alter table myacidtable compact 'major';
```

To zagęszczanie jest wymagane, ponieważ tabele HDInsight 3.6 i HDInsight 4.0 ACID inaczej rozumieją różnice acid. Zagęszczanie wymusza czysty łupek, który gwarantuje spójność. Sekcja 4 [dokumentacji migracji hive](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) zawiera wskazówki dotyczące zbiorczego zagęszczania tabel HDInsight 3.6 ACID.

Po zakończeniu kroków migracji i zagęszczania metastore można przeprowadzić migrację rzeczywistego magazynu. Po zakończeniu migracji magazynu hive magazyn HDInsight 4.0 będzie miał następujące właściwości:

|3.6 |4.0 |
|---|---|
|Tabele zewnętrzne|Tabele zewnętrzne|
|Tabele zarządzane nietransakcyjne|Tabele zewnętrzne|
|Tabele zarządzane transakcyjne|Tabele zarządzane|

Może być konieczne dostosowanie właściwości magazynu przed wykonaniem migracji. Na przykład jeśli oczekujesz, że niektóre tabele będą dostępne dla innej firmy (na przykład klastra HDInsight 3.6), ta tabela musi być zewnętrzna po zakończeniu migracji. W hdinsight 4.0 wszystkie tabele zarządzane są transakcyjne. W związku z tym tabel zarządzanych w hdinsight 4.0 powinny być dostępne tylko przez klastry HDInsight 4.0.

Po prawidłowym ustawieniu właściwości tabeli należy wykonać narzędzie migracji magazynu gałęzi z jednego z węzłów headnodes klastra przy użyciu powłoki SSH:

1. Połącz się z węzłem główny klastra za pomocą SSH. Aby uzyskać instrukcje, zobacz [Łączenie się z hdinsight za pomocą SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Otwórz powłokę logowania jako użytkownik gałęzi, uruchamiając`sudo su - hive`
1. Określ wersję stosu platformy `ls /usr/hdp`danych, wykonując program . Spowoduje to wyświetlenie ciągu wersji, który należy użyć w następnym poleceniu.
1. Wykonaj następujące polecenie z powłoki. Zamień `STACK_VERSION` ciąg wersji z poprzedniego kroku:

```bash
/usr/hdp/STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

Po zakończeniu narzędzia migracji magazyn hive będzie gotowy do pracy w formacie HDInsight 4.0.

> [!Important]  
> Tabele zarządzane w programie HDInsight 4.0 (w tym tabele zmigrowane z 3.6) nie powinny być dostępne dla innych usług lub aplikacji, w tym klastrów HDInsight 3.6.

## <a name="secure-hive-across-hdinsight-versions"></a>Bezpieczna gałąź w wersjach HDInsight

Od czasu programu HDInsight 3.6 program HDInsight integruje się z usługą Azure Active Directory przy użyciu pakietu zabezpieczeń dla przedsiębiorstw HDInsight (ESP). Protokół ESP używa protokołu Kerberos i Apache Ranger do zarządzania uprawnieniami określonych zasobów w klastrze. Zasady ranger wdrożone dla hive w hdinsight 3.6 można migrować do hdinsight 4.0 z następujących kroków:

1. Przejdź do panelu Menedżer usług Ranger w klastrze HDInsight 3.6.
2. Przejdź do zasad o nazwie **HIVE** i wyeksportuj zasady do pliku json.
3. Upewnij się, że wszyscy użytkownicy, o których mowa w wyeksportowanych zasad json istnieje w nowym klastrze. Jeśli użytkownik jest określany w json zasad, ale nie istnieje w nowym klastrze, albo dodać użytkownika do nowego klastra lub usunąć odwołanie z zasad.
4. Przejdź do panelu **Menedżer usług Ranger** w klastrze HDInsight 4.0.
5. Przejdź do zasad o nazwie **HIVE** i zaimportuj json zasad ranger z kroku 2.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Sprawdzanie zgodności i modyfikowanie kodów zgodnie z potrzebami w aplikacji testowej

Podczas migracji obciążeń, takich jak istniejące programy i kwerendy, sprawdź informacje o wersji i dokumentację dla zmian i zastosuj zmiany w razie potrzeby. Jeśli klaster HDInsight 3.6 używa udostępnionego magazynu platformy Spark i Hive, wymagana jest [dodatkowa konfiguracja przy użyciu łącznika magazynu hive.](./apache-hive-warehouse-connector.md)

## <a name="deploy-new-app-for-production"></a>Wdrażanie nowej aplikacji dla produkcji

Aby przełączyć się do nowego klastra, można na przykład zainstalować nową aplikację kliencką i używać jej jako nowego środowiska produkcyjnego lub uaktualnić istniejącą aplikację kliencką i przełączyć się na hdinsight 4.0.

## <a name="switch-hdinsight-40-to-the-production"></a>Przełącz HDInsight 4.0 na produkcję

Jeśli różnice zostały utworzone w metastore podczas testowania, należy zaktualizować zmiany tuż przed przełączeniem. W takim przypadku można wyeksportować & zaimportować magazyn meteorsowy, a następnie uaktualnić ponownie.

## <a name="remove-the-old-production"></a>Usuń starą produkcję

Po potwierdzeniu, że wydanie jest kompletne i w pełni sprawne, możesz usunąć wersję 3.6 i poprzednią metasklep. Przed usunięciem środowiska upewnij się, że wszystko jest migrowane.

## <a name="query-execution-across-hdinsight-versions"></a>Wykonywanie kwerend w wersjach programu HDInsight

Istnieją dwa sposoby wykonywania i debugowania zapytań Hive/LLAP w klastrze HDInsight 3.6. HiveCLI zapewnia środowisko wiersza polecenia, a widok Tez view/hive zapewnia przepływ pracy oparty na interfejsie użytkownika.

W hdinsight 4.0 HiveCLI został zastąpiony beeline. HiveCLI jest klientem oszczędności dla hiveserver 1, a Beeline jest klientem JDBC, który zapewnia dostęp do hiveserver 2. Beeline może być również używany do łączenia się z dowolnym innym punktem końcowym bazy danych zgodnym z JDBC. Beeline jest dostępny po wyjęciu z pudełka na HDInsight 4.0 bez konieczności instalacji.

W umia dla HDInsight 3.6 klient gui do interakcji z serwerem hive jest ambari hive view. HDInsight 4.0 nie jest dostarczany z widokiem Ambari. Zapewniliśmy naszym klientom możliwość korzystania z Data Analytics Studio (DAS), które nie jest podstawową usługą HDInsight. DAS nie jest dostarczany z klastrami HDInsight out-of-the-box i nie jest oficjalnie obsługiwany pakiet. Jednak das można zainstalować w klastrze przy użyciu [akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md) w następujący sposób:

|Właściwość | Wartość |
|---|---|
|Typ skryptu|- Niestandardowe|
|Nazwa|Das|
|Identyfikator URI skryptu bash|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|Typy węzłów|Head|

Odczekaj od 10 do 15 minut, `https://CLUSTERNAME.azurehdinsight.net/das/`a następnie uruchom Data Analytics Studio za pomocą tego adresu URL: .

Przed dostępem do systemu DAS może być wymagane odświeżenie interfejsu użytkownika Ambari i/lub ponowne uruchomienie wszystkich składników Ambari.

Po zainstalowaniu das, jeśli nie widzisz kwerendy, które zostały uruchomione w przeglądarce zapytań, wykonaj następujące kroki:

1. Ustaw konfiguracje dla gałęzi, Tez i DAS, jak opisano w [tym przewodniku, aby rozwiązać problem z instalacją das](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Upewnij się, że następujące konfiguracje katalogu usługi Azure storage to `fs.azure.page.blob.dirs`obiekty blob strony i że są one wymienione w obszarze:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Uruchom ponownie pliki HDFS, Hive, Tez i DAS na obu głowach.

## <a name="next-steps"></a>Następne kroki

* [Zapowiedź HDInsight 4.0](../hdinsight-version-release.md)
* [HdInsight 4.0 głębokie nurkowanie](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Ule 3 Tabele ACID](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
