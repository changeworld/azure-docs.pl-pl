---
title: Migracja klastra HBase do nowej wersji — Azure HDInsight
description: Jak przeprowadzić migrację bazy danych HBase clusters do nowej wersji.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: ac7984c50e6adec888c112cc260cf2e6af02fc97
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64695694"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Migrowanie klastra Apache HBase do nowszej wersji

Klastry oparte na zadaniach, takich jak [platformy Apache Spark](https://spark.apache.org/) i [Apache Hadoop](https://hadoop.apache.org/), są proste do uaktualnienia — zobacz [klastra HDInsight uaktualnienia do nowszej wersji](../hdinsight-upgrade-cluster.md):

1. Utwórz kopię zapasową danych przejściowych (przechowywane lokalnie).
2. Usuń istniejący klaster.
3. Utwórz nowy klaster w tej samej podsieci sieci Wirtualnej.
4. Importuj dane przejściowy.
5. Uruchom zadania i kontynuować przetwarzanie w nowym klastrze.

Aby uaktualnić [bazy danych Apache HBase](https://hbase.apache.org/) klastra pewne dodatkowe kroki są wymagane, zgodnie z opisem w tym artykule.

> [!NOTE]  
> Czas przestoju podczas uaktualniania powinien być minimalny, rzędu kilku minut. Ten czas przestoju jest spowodowany przez kroki Opróżnij wszystkie dane w pamięci, a następnie czasu, aby skonfigurować i uruchomić ponownie usługi w nowym klastrze. Wyniki różnią się w zależności od liczby węzłów, ilości danych i inne zmienne.

## <a name="review-apache-hbase-compatibility"></a>Przegląd zgodności bazy danych Apache HBase

Przed rozpoczęciem uaktualniania bazy danych Apache HBase, upewnij się, że wersje bazy danych HBase w klastrach źródłowe i docelowe są zgodne. Aby uzyskać więcej informacji, zobacz [Apache Hadoop, składniki i wersje, które udostępniono HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]  
> Zdecydowanie zalecamy przejrzenie macierz zgodności wersji w [książki HBase](https://hbase.apache.org/book.html#upgrading).

Oto przykład wersji macierz zgodności, gdzie t wskazuje zgodności i N wskazuje potencjalne niezgodności:

| Typ zgodności | Wersja główna| Wersja pomocnicza | Poprawka |
| --- | --- | --- | --- |
| Zgodność przewodowy klient-serwer | Nie | Tak | Tak |
| Zgodność z serwerami | Nie | Tak | Tak |
| Zgodność z formatem pliku | Nie | Tak | Tak |
| Zgodność klienta interfejsu API | Nie | Tak | Tak |
| Zgodność binarną klienta | Nie | Nie | Tak |
| **Po stronie serwera ograniczone zgodnością z interfejsem API** |  |  |  |
| Stable | Nie | Tak | Tak |
| Zmieniające się | Nie | Nie | Tak |
| Niestabilne | Nie | Nie | Nie |
| Zgodność zależności | Nie | Tak | Tak |
| Zgodność operacyjną | Nie | Nie | Tak |

> [!NOTE]  
> Wszelkie niezgodności podziału powinny być opisane w informacjach o wersji bazy danych HBase.

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Uaktualnienie z tej samej wersji głównej bazy danych Apache HBase

Poniższy scenariusz ma dla uaktualnienie oprogramowania HDInsight 3.4 do 3.6 (wchodzą w skład bazy danych Apache HBase 1.1.2) przy użyciu tej samej wersji głównej bazy danych HBase. Innych uaktualnień wersji są podobne, tak długo, jak nie ma żadnych problemów ze zgodnością między źródłowym i docelowym.

1. Upewnij się, że aplikacja jest zgodne z nową wersją zgodnie z bazy danych HBase i wersji macierzy uwagi dotyczące zgodności. Przetestuj aplikację w klastrze z systemem w wersji docelowej, HDInsight i bazy danych HBase.

2. [Skonfiguruj nowy klaster HDInsight docelowy](../hdinsight-hadoop-provision-linux-clusters.md) przy użyciu tego samego konta magazynu, ale nazwą innego kontenera:

    ![Użyj tego samego konta magazynu, ale Utwórz innego kontenera](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

3. Usuń z klastra HBase źródła. Jest to klaster, z którego jest uaktualniany. Baza danych HBase zapisuje dane przychodzące do magazynu w pamięci o nazwie _magazynu pamięci_. Po magazynu pamięci osiągnie określony rozmiar, magazynu w pamięci jest opróżniany do dysku w celu przechowywania długoterminowego na koncie magazynu klastra. Podczas usuwania starego klastra, memstores są recyklingowi, potencjalnie utraty danych. Aby ręcznie opróżniania magazynu pamięci dla każdej tabeli na dysku, uruchom następujący skrypt. Najnowszą wersję tego skryptu znajduje się na platformie Azure [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh).

    ```bash
    #!/bin/bash
    
    #-------------------------------------------------------------------------------#
    # SCRIPT TO FLUSH ALL HBASE TABLES.
    #-------------------------------------------------------------------------------#
    
    LIST_OF_TABLES=/tmp/tables.txt
    HBASE_SCRIPT=/tmp/hbase_script.txt
    TARGET_HOST=$1
    
    usage ()
    {
        if [[ "$1" == "-h" ]] || [[ "$1" == "--help" ]]
        then
            cat << ...
    
    Usage: 
    
    $0 [hostname]
    
    Providing hostname is optional and not required when the script is executed within HDInsight cluster with access to 'hbase shell'.
    
    However hostname should be provided when executing the script as a script-action from HDInsight portal.
    
    For Example:
    
        1.  Executing script inside HDInsight cluster (where 'hbase shell' is 
            accessible):
    
            $0 
    
            [No need to provide hostname]
    
        2.  Executing script from HDinsight Azure portal:
    
            Provide Script URL.
    
            Provide hostname as a parameter (i.e. hn0, hn1 or wn2 etc.).
    ...
            exit
        fi
    }
    
    validate_machine ()
    {
        THIS_HOST=`hostname`
    
        if [[ ! -z "$TARGET_HOST" ]] && [[ $THIS_HOST  != $TARGET_HOST* ]]
        then
            echo "[INFO] This machine '$THIS_HOST' is not the right machine ($TARGET_HOST) to execute the script."
            exit 0
        fi
    }
    
    get_tables_list ()
    {
    hbase shell << ... > $LIST_OF_TABLES 2> /dev/null
        list
        exit
    ...
    }
    
    add_table_for_flush ()
    {
        TABLE_NAME=$1
        echo "[INFO] Adding table '$TABLE_NAME' to flush list..."
        cat << ... >> $HBASE_SCRIPT
            flush '$TABLE_NAME'
    ...
    }
    
    clean_up ()
    {
        rm -f $LIST_OF_TABLES
        rm -f $HBASE_SCRIPT
    }
    
    ########
    # MAIN #
    ########
    
    usage $1
    
    validate_machine
    
    clean_up
    
    get_tables_list
    
    START=false
    
    while read LINE 
    do 
        if [[ $LINE == TABLE ]] 
        then
            START=true
            continue
        elif [[ $LINE == *row*in*seconds ]]
        then
            break
        elif [[ $START == true ]]
        then
            add_table_for_flush $LINE
        fi
    
    done < $LIST_OF_TABLES
    
    cat $HBASE_SCRIPT
    
    hbase shell $HBASE_SCRIPT << ... 2> /dev/null
    exit
    ...
    
    ```
    
4. Zatrzymać pozyskiwania dla starego klastra HBase.
5. Aby upewnić się, że wszystkie najnowsze dane w magazynu w pamięci jest opróżniany, uruchom ponownie poprzednim skrypcie.
6. Zaloguj się do [Apache Ambari](https://ambari.apache.org/) w starym klastrze (https://OLDCLUSTERNAME.azurehdidnsight.net) i zatrzymywanie usług bazy danych HBase. Po wyświetleniu monitu, aby upewnić się, że chcesz zatrzymać usługi, pole wyboru, aby włączyć tryb konserwacji dla bazy danych HBase. Aby uzyskać więcej informacji na temat nawiązywania połączenia i przy użyciu narzędzia Ambari, zobacz [HDInsight Zarządzanie klastrami przy użyciu interfejsu użytkownika sieci Web Ambari](../hdinsight-hadoop-manage-ambari.md).

    ![W Ambari kliknij kartę usługi, a następnie HBase w menu po lewej stronie, a następnie Zatrzymaj w obszarze akcji usługi](./media/apache-hbase-migrate-new-version/stop-hbase-services.png)

    ![Włącz na trybu konserwacji dla bazy danych HBase pole wyboru, a następnie potwierdź](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

7. Zaloguj się do systemu Ambari w nowym klastrze HDInsight. Zmiana `fs.defaultFS` systemu plików HDFS ustawienie, aby wskazać nazwę kontenera posługują się do oryginalnego klastra. To ustawienie ma pod **systemu plików HDFS > konfiguracje > Zaawansowane > Zaawansowane lokacji podstawowej**.

    ![W Ambari kliknij kartę usługi, a następnie HDFS menu po lewej stronie, następnie na karcie konfiguracje, następnie kartę Zaawansowane poniżej](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![W Ambari Zmień nazwę kontenera](./media/apache-hbase-migrate-new-version/change-container-name.png)

8. **Jeśli nie używasz klastrów HBase za pomocą funkcji rozszerzone zapisuje, Pomiń ten krok. Potrzebny jest tylko w przypadku klastrów HBase za pomocą funkcji rozszerzone zapisuje.**
   
   Zmień ścieżkę hbase.rootdir, aby wskazać kontener oryginalnego klastra.

    ![W Ambari Zmień nazwę kontenera rootdir bazy danych hbase](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)
    
9. Zapisz zmiany.
10. Uruchom ponownie wszystkie wymagane usługi, wskazane przez Ambari.
11. Punkt aplikacji do nowego klastra.

    > [!NOTE]  
    > Statyczny serwera DNS dla zmian aplikacji podczas uaktualniania. Zamiast kodować na tym DNS, można skonfigurować rekord CNAME w ustawieniach DNS nazwy domeny, które wskazuje nazwę klastra. Innym rozwiązaniem jest przy użyciu pliku konfiguracji aplikacji, którą można zaktualizować bez ponownego wdrażania.

12. Rozpocznij wprowadzanie, aby zobaczyć, jeśli to wszystko działa zgodnie z oczekiwaniami.
13. Jeśli nowy klaster jest zadowalające, należy usunąć oryginalnego klastra.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat [bazy danych Apache HBase](https://hbase.apache.org/) i uaktualniania klastrów HDInsight, zobacz następujące artykuły:

* [Uaktualnianie klastra usługi HDInsight do nowszej wersji](../hdinsight-upgrade-cluster.md)
* [Monitorowanie i zarządzanie nimi w usłudze Azure HDInsight przy użyciu Interfejsu sieci Web Apache Ambari](../hdinsight-hadoop-manage-ambari.md)
* [Apache Hadoop, składniki i wersje](../hdinsight-component-versioning.md)
* [Optymalizacja konfiguracji przy użyciu Apache Ambari](../hdinsight-changing-configs-via-ambari.md#apache-hbase-optimization-with-the-ambari-web-ui)
