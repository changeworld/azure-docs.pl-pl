---
title: Migrowanie klastra HBase do nowej wersji — usługa Azure HDInsight
description: Jak przeprowadzić migrację klastrów Apache HBase do nowszej wersji w usłudze Azure HDInsight.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: ashishth
ms.openlocfilehash: 546d491c24198d5f7a92765876e5f6919ca32020
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735803"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Migrowanie klastra Apache HBase do nowej wersji

W tym artykule omówiono kroki wymagane do zaktualizowania klastra Apache HBase w usłudze Azure HDInsight do nowszej wersji.

> [!NOTE]  
> Czas przestoju podczas uaktualniania powinien być minimalny w kolejności minut. Ten przestój jest spowodowany przez kroki, aby opróżnić wszystkie dane w pamięci, a następnie skonfigurować i ponownie uruchomić usługi w nowym klastrze. Wyniki będą się różnić w zależności od liczby węzłów, ilości danych i innych zmiennych.

## <a name="review-apache-hbase-compatibility"></a>Przegląd zgodności z Apache HBase

Przed uaktualnieniem oprogramowania Apache HBase upewnij się, że wersje HBase w klastrach źródłowym i docelowym są zgodne. Aby uzyskać więcej informacji, zobacz [Apache Hadoop składniki i wersje dostępne w usłudze HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]  
> Zdecydowanie zalecamy zapoznanie się z macierzą zgodności wersji w [książce HBase](https://hbase.apache.org/book.html#upgrading).

Poniżej znajduje się przykładowa wersja macierzy zgodności. Wartość Y oznacza zgodność i N wskazuje potencjalną niezgodność:

| Typ zgodności | Wersja główna| Wersja pomocnicza | Poprawka |
| --- | --- | --- | --- |
| Zgodność sieci klient-serwer | Nie | T | T |
| Zgodność serwera z serwerem | Nie | T | T |
| Zgodność formatu pliku | Nie | T | T |
| Zgodność z interfejsem API klienta | Nie | T | T |
| Zgodność binarna klienta | Nie | Nie | T |
| **Ograniczona zgodność interfejsu API po stronie serwera** |  |  |  |
| Stable | Nie | T | T |
| Ewoluuje | Nie | Nie | T |
| Stanie | Nie | Nie | Nie |
| Zgodność zależności | Nie | T | T |
| Zgodność operacyjna | Nie | Nie | T |

> [!NOTE]  
> Wszelkie niezgodności należy opisać w informacjach o wersji HBase.

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Uaktualnij z tą samą wersją główną oprogramowania Apache HBase

Aby uaktualnić klaster Apache HBase w usłudze Azure HDInsight, wykonaj następujące czynności:

1. Upewnij się, że aplikacja jest zgodna z nową wersją, jak pokazano w macierzy zgodności HBase i informacje o wersji. Przetestuj swoją aplikację w klastrze z uruchomioną docelową wersją usługi HDInsight i HBase.

2. [Skonfiguruj nowy docelowy klaster usługi HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) przy użyciu tego samego konta magazynu, ale z inną nazwą kontenera:

    ![Użyj tego samego konta magazynu, ale Utwórz inny kontener](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

3. Opróżniaj źródłowy klaster HBase, który jest uaktualnianym klastrem. HBase zapisuje dane przychodzące do magazynu w pamięci o nazwie _magazynu_. Gdy magazynu osiągnie określony rozmiar, HBase opróżnia go na dysk w celu długoterminowego przechowywania na koncie magazynu klastra. Podczas usuwania starego klastra memstores są odtwarzane, potencjalnie tracące dane. Aby ręcznie opróżnić magazynu dla każdej tabeli na dysk, uruchom następujący skrypt. Najnowsza wersja tego skryptu znajduje się w witrynie [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh)platformy Azure.

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
    
4. Zatrzymaj pozyskiwanie w starym klastrze HBase.
5. Aby upewnić się, że wszystkie ostatnie dane w magazynu są opróżniane, ponownie uruchom poprzedni skrypt.
6. Zaloguj się do programu [Apache Ambari](https://ambari.apache.org/) w starym klastrze https://OLDCLUSTERNAME.azurehdidnsight.net) (i Zatrzymaj usługi HBase Services. Po wyświetleniu monitu o potwierdzenie, że chcesz zatrzymać usługi, zaznacz to pole wyboru, aby włączyć tryb konserwacji dla HBase. Aby uzyskać więcej informacji na temat nawiązywania połączenia z usługą Ambari i korzystania z niej, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Ambari](../hdinsight-hadoop-manage-ambari.md).

    ![W Ambari kliknij pozycję usługi > HBase > Zatrzymaj w obszarze Akcje usługi](./media/apache-hbase-migrate-new-version/stop-hbase-services.png)

    ![Zaznacz pole wyboru Włącz tryb konserwacji dla HBase, a następnie potwierdź](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

7. Zaloguj się do usługi Ambari w nowym klastrze usługi HDInsight. Zmień ustawienie systemu HDFS, aby wskazywała nazwę kontenera używanego przez oryginalny klaster. `fs.defaultFS` To ustawienie znajduje się w obszarze **HDFS > konfiguracjami > zaawansowanej > zaawansowanej lokacji głównej**.

    ![W Ambari kliknij pozycję usługi > HDFS > Konfiguracje > Zaawansowane](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![W Ambari Zmień nazwę kontenera](./media/apache-hbase-migrate-new-version/change-container-name.png)

8. **Jeśli nie korzystasz z klastrów HBase z funkcją ulepszone zapisy, Pomiń ten krok. Jest to konieczne tylko w przypadku klastrów HBase z ulepszoną funkcją zapisywania.**
   
   `hbase.rootdir` Zmień ścieżkę, aby wskazywała kontener oryginalnego klastra.

    ![W Ambari Zmień nazwę kontenera dla HBase ROOTDIR](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)
1. Jeśli uaktualniasz usługi HDInsight 3,6 do 4,0, wykonaj poniższe kroki, w przeciwnym razie przejdź do kroku 10:
    1. Uruchom ponownie wszystkie wymagane usługi w programie Ambari, wybierając pozycję **usługi** > **Uruchom ponownie wszystkie wymagane**.
    1. Zatrzymaj usługę HBase.
    1. Użyj protokołu SSH do węzła dozorcy i wykonaj `rmr /hbase-unsecure` polecenie [zkCli](https://github.com/go-zkcli/zkcli) , aby usunąć HBase główny znode z dozorcy.
    1. Uruchom ponownie HBase.
1. Jeśli uaktualniasz do innej wersji usługi HDInsight niż 4,0, wykonaj następujące kroki:
    1. Zapisz zmiany.
    1. Uruchom ponownie wszystkie wymagane usługi zgodnie z Ambari.
1. Wskaż aplikację nowym klastrem.

    > [!NOTE]  
    > Statyczny serwer DNS dla aplikacji zmienia się podczas uaktualniania. Zamiast napisania kodu w systemie DNS można skonfigurować rekord CNAME w ustawieniach DNS nazwy domeny, który wskazuje na nazwę klastra. Innym rozwiązaniem jest użycie pliku konfiguracji dla aplikacji, który można zaktualizować bez ponownego wdrożenia.

12. Rozpocznij pozyskiwanie, aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami.
13. Jeśli nowy klaster jest zadowalający, Usuń oryginalny klaster.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o platformie [Apache HBase](https://hbase.apache.org/) i uaktualnianiu klastrów usługi HDInsight, zobacz następujące artykuły:

* [Uaktualnianie klastra usługi HDInsight do nowszej wersji](../hdinsight-upgrade-cluster.md)
* [Monitorowanie usługi Azure HDInsight i zarządzanie nią za pomocą interfejsu użytkownika sieci Web Apache Ambari](../hdinsight-hadoop-manage-ambari.md)
* [Apache Hadoop składniki i wersje](../hdinsight-component-versioning.md)
* [Optymalizowanie konfiguracji przy użyciu oprogramowania Apache Ambari](../hdinsight-changing-configs-via-ambari.md#apache-hbase-optimization-with-the-ambari-web-ui)
