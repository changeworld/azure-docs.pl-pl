---
title: Migrowanie klastra bazy danych HBase do nowej wersji — Usługa Azure HDInsight
description: Jak przeprowadzić migrację klastrów Apache HBase do nowszej wersji w usłudze Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 30cda7a83feddaeb41385252a61d1dc68a881a47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646510"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Migrowanie klastra Apache HBase do nowej wersji

W tym artykule omówiono kroki wymagane do zaktualizowania klastra Apache HBase w usłudze Azure HDInsight do nowszej wersji.

Przestoje podczas aktualizacji powinny być minimalne, w kolejności minut. Ten przestój jest spowodowany przez kroki opróżnienia wszystkich danych w pamięci, a następnie czas, aby skonfigurować i ponownie uruchomić usługi w nowym klastrze. Wyniki będą się różnić w zależności od liczby węzłów, ilości danych i innych zmiennych.

## <a name="review-apache-hbase-compatibility"></a>Przegląd zgodności apache HBase

Przed uaktualnieniem apache HBase upewnij się, że wersje HBase w klastrach źródłowych i docelowych są zgodne. Aby uzyskać więcej informacji, zobacz [Apache Hadoop komponentów i wersji dostępnych z HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]  
> Zdecydowanie zalecamy zapoznanie się z macierzą zgodności wersji w [książce HBase](https://hbase.apache.org/book.html#upgrading). Wszelkie niezgodności z łamaniem powinny być opisane w informacjach o wersji HBase.

Oto przykładowa macierz zgodności wersji. Y wskazuje zgodność, a N wskazuje potencjalną niezgodność:

| Typ zgodności | Wersja główna| Wersja pomocnicza | Patch |
| --- | --- | --- | --- |
| Zgodność z siecią klient-serwer | Nie | Tak | Tak |
| Zgodność z serwerem a serwerem | Nie | Tak | Tak |
| Zgodność formatu pliku | Nie | Tak | Tak |
| Zgodność interfejsu API klienta | Nie | Tak | Tak |
| Zgodność binarna klienta | Nie | Nie | Tak |
| **Zgodność interfejsu API po stronie serwera** |  |  |  |
| Stable | Nie | Tak | Tak |
| Ewoluuje | Nie | Nie | Tak |
| Niestabilny | Nie | Nie | Nie |
| Zgodność zależności | Nie | Tak | Tak |
| Kompatybilność operacyjna | Nie | Nie | Tak |

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Uaktualnij z tą samą wersją główną Apache HBase

Aby uaktualnić klaster Apache HBase w usłudze Azure HDInsight, wykonaj następujące kroki:

1. Upewnij się, że aplikacja jest zgodna z nową wersją, jak pokazano w macierzy zgodności HBase i informacje o wersji. Przetestuj aplikację w klastrze z uruchomiona docelową wersją hdinsight i HBase.

1. [Skonfiguruj nowy docelowy klaster HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) przy użyciu tego samego konta magazynu, ale o innej nazwie kontenera:

    ![Użyj tego samego konta magazynu, ale utwórz inny kontener](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

1. Opróżnij źródłowy klaster HBase, który jest klastrem, który uaktualniasz. HBase zapisuje przychodzące dane w magazynie w pamięci, zwany _memstore_. Po osiągnięciu określonego rozmiaru memstore HBase opróżnia go na dysk dla długoterminowego magazynu na koncie magazynu klastra. Podczas usuwania starego klastra, memstores są poddane recyklingowi, potencjalnie utraty danych. Aby ręcznie opróżnić magazyn memów dla każdej tabeli na dysk, uruchom następujący skrypt. Najnowsza wersja tego skryptu jest dostępna na [platformie Azure GitHub.](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh)

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
    
            Provide hostname as a parameter (i.e. hn0, hn1, hn2.. or wn2 etc.).
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

1. Zatrzymaj pozyskiwania do starego klastra HBase.

1. Aby upewnić się, że wszystkie ostatnie dane w memstore są opróżniane, uruchom ponownie poprzedni skrypt.

1. Zaloguj się do [Apache Ambari](https://ambari.apache.org/) `https://OLDCLUSTERNAME.azurehdidnsight.net`na starym klastrze ( ) i zatrzymaj usługi HBase. Po wyświetleniu monitu o potwierdzenie, że chcesz zatrzymać usługi, zaznacz to pole wyboru, aby włączyć tryb konserwacji dla bazy HBase. Aby uzyskać więcej informacji na temat łączenia się z ambari i korzystania z niego, zobacz [Zarządzanie klastrami HDInsight przy użyciu interfejsu użytkownika sieci Web Ambari](../hdinsight-hadoop-manage-ambari.md).

    ![W ambari kliknij pozycję Usługi > > zatrzymują się w obszarze Akcje usługi](./media/apache-hbase-migrate-new-version/stop-hbase-services1.png)

    ![Zaznacz pole wyboru Włącz tryb konserwacji dla bazy HBase, a następnie potwierdź](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

1. Zaloguj się do Ambari w nowym klastrze HDInsight. Zmień `fs.defaultFS` ustawienie HDFS, aby wskazywało nazwę kontenera używanego przez oryginalny klaster. To ustawienie znajduje się w obszarze **Usługi HDFS > Configs > Advanced > Advanced core-site**.

    ![W Ambari kliknij pozycję Usługi > usługi HDFS > configs > Zaawansowane](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![W Ambari zmień nazwę kontenera](./media/apache-hbase-migrate-new-version/change-container-name.png)

1. Jeśli klastry HBase nie są używane z funkcją Rozszerzone zapisy, pomiń ten krok. Jest to potrzebne tylko dla klastrów HBase z funkcją Rozszerzone zapisy.

   Zmień `hbase.rootdir` ścieżkę, aby wskazywała kontener oryginalnego klastra.

    ![W Ambari zmień nazwę kontenera dla rootdiru bazy HBase](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)

1. Jeśli aktualizujesz hdinsight 3.6 do 4.0, wykonaj poniższe czynności, w przeciwnym razie przejdź do kroku 10:
    1. Uruchom ponownie wszystkie wymagane usługi w Ambari, wybierając opcję**Uruchom ponownie wszystkie wymagane** **usługi** > .
    1. Zatrzymaj usługę HBase.
    1. SSH do węzła Zookeeper i wykonać polecenie `rmr /hbase-unsecure` [zkCli,](https://github.com/go-zkcli/zkcli) aby usunąć znode root HBase z Zookeeper.
    1. Uruchom ponownie bazę danych HBase.

1. Jeśli uaktualniasz do innej wersji HDInsight oprócz wersji 4.0, wykonaj następujące kroki:
    1. Zapisz zmiany.
    1. Uruchom ponownie wszystkie wymagane usługi zgodnie z wskazaniami Ambari.

1. Wskaż aplikację do nowego klastra.

    > [!NOTE]  
    > Statyczny system DNS aplikacji zmienia się podczas uaktualniania. Zamiast kodować ten dns, można skonfigurować CNAME w ustawieniach DNS nazwy domeny, który wskazuje nazwę klastra. Inną opcją jest użycie pliku konfiguracyjnego dla aplikacji, który można zaktualizować bez ponownego rozmieszczania.

1. Rozpocznij połk, aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami.

1. Jeśli nowy klaster jest zadowalający, usuń oryginalny klaster.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o [Apache HBase](https://hbase.apache.org/) i uaktualnianiu klastrów HDInsight, zobacz następujące artykuły:

* [Uaktualnianie klastra USŁUGI HDInsight do nowszej wersji](../hdinsight-upgrade-cluster.md)
* [Monitorowanie usługi Azure HDInsight i zarządzanie nim przy użyciu interfejsu użytkownika sieci Web Apache Ambari](../hdinsight-hadoop-manage-ambari.md)
* [Apache Hadoop komponenty i wersje](../hdinsight-component-versioning.md)
* [Optymalizuj konfiguracje za pomocą Apache Ambari](../hdinsight-changing-configs-via-ambari.md#apache-hbase-optimization-with-the-ambari-web-ui)
