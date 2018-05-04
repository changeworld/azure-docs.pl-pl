---
title: Migrowanie klastra HBase do nowej wersji - Azure HDInsight | Dokumentacja firmy Microsoft
description: Jak przeprowadzić migrację klastrów HBase do nowej wersji.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 3ca982e7fc0ce56bee2ee2e193c82a78fac44362
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="migrate-an-hbase-cluster-to-a-new-version"></a>Migrowanie klastra HBase do nowej wersji

Zadanie klastrów, takie jak Spark i Hadoop, są oczywiste uaktualnić — zobacz [uaktualnienia klastra usługi HDInsight do nowszej wersji](../hdinsight-upgrade-cluster.md):

1. Utwórz kopię zapasową danych przejściowy (przechowywanych lokalnie).
2. Usuwanie istniejącego klastra.
3. Tworzenie nowego klastra w tej samej podsieci sieci Wirtualnej.
4. Importuj dane przejściowej.
5. Uruchom zadania i kontynuować przetwarzanie w nowym klastrze.

Aby uaktualnić klaster HBase są potrzebne dodatkowe kroki, zgodnie z opisem w tym artykule.

> [!NOTE]
> Powinien być minimalny rzędu minut przestoju podczas uaktualniania. Ten czas przestoju jest spowodowany przez kroki opróżnić wszystkie dane w pamięci, a następnie czasu, aby skonfigurować i uruchomić ponownie usługi w nowym klastrze. Wyniki będą się różnić w zależności od liczby węzłów, ilości danych i inne zmienne.

## <a name="review-hbase-compatibility"></a>Przegląd zgodności bazy danych HBase

Przed rozpoczęciem uaktualniania bazy danych HBase, upewnij się, że wersje bazy danych HBase w klastrze źródłowym i docelowym są zgodne. Aby uzyskać więcej informacji, zobacz [Hadoop składniki i wersje dostępne w usłudze HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]
> Zdecydowanie zaleca się przejrzenie macierz zgodności wersji w [książki HBase](https://hbase.apache.org/book.html#upgrading).

Oto przykład wersji macierz zgodności, gdzie t wskazuje zgodności i N wskazuje potencjalne niezgodności:

| Typ zgodności | Wersja główna| Wersja pomocnicza | Poprawka |
| --- | --- | --- | --- |
| Zgodność przewodowy klient-serwer | N | Tak | Tak |
| Zgodność serwer-serwer | N | Tak | Tak |
| Plik formatu zgodności | N | Tak | Tak |
| Interfejs API klienta ze zgodnością | N | Tak | Tak |
| Zgodność binarną klienta | N | N | Tak |
| **Interfejs API zgodności ograniczone po stronie serwera** |  |  |  |
| Stable | N | Tak | Tak |
| Zmieniające się | N | N | Tak |
| Niestabilny | N | N | N |
| Zgodność zależności | N | Tak | Tak |
| Zgodność operacyjne | N | N | Tak |

> [!NOTE]
> Wszelkie niezgodności podziału powinny być opisane w informacjach o wersji bazy danych HBase.

## <a name="upgrade-with-same-hbase-major-version"></a>Uaktualnienie z tej samej wersji głównej bazy danych HBase

Poniższy scenariusz ma uaktualniania programu HDInsight 3.4 do 3,6 (wchodzą w skład bazy danych Apache HBase 1.1.2) z taką samą wersję główną HBase. Inne uaktualnienia wersji są podobne, dopóki nie ma żadnych problemów ze zgodnością między wersjami źródłowym i docelowym.

1. Upewnij się, że aplikacji jest zgodna z nowej wersji, jak pokazano w notatkach macierzy i wersja zgodności bazy danych HBase. Testowanie aplikacji w klastrze z systemem w wersji docelowej HDInsight i HBase.

2. [Konfigurowanie nowego klastra usługi HDInsight docelowego](../hdinsight-hadoop-provision-linux-clusters.md) przy użyciu tego samego konta magazynu, ale o nazwie różnych kontenerów:

    ![Użyj tego samego konta magazynu, ale Utwórz innego kontenera](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

3. Usuń klaster HBase źródła. Jest to klaster, który jest uaktualniany. HBase zapisuje dane przychodzące do magazynu w pamięci o nazwie _parametru_. Po magazynu osiągnie określony rozmiar, jest opróżniany magazynu na dysku do przechowywania długoterminowego na koncie magazynu klastra. Podczas usuwania starego klastra, memstores są odzyskiwane, potencjalnie utraty danych. Aby ręcznie opróżnić magazynu dla każdej tabeli na dysku, uruchom następujący skrypt. Najnowsza wersja tego skryptu jest na platformie Azure przez [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh).

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
    
4. Zatrzymaj wprowadzanie starego klastra HBase.
5. Aby upewnić się, że wszystkie dane w parametru jest opróżniany, uruchom ponownie poprzednie skryptu.
6. Zaloguj się do narzędzia Ambari w starym klastrze (https://OLDCLUSTERNAME.azurehdidnsight.net) i zatrzymywanie usług HBase. Po wyświetleniu monitu o potwierdzenie, czy chcesz zatrzymać usługi, pole wyboru, aby włączyć tryb konserwacji bazy danych hbase. Aby uzyskać więcej informacji na łączenie się i za pomocą narzędzia Ambari, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Ambari](../hdinsight-hadoop-manage-ambari.md).

    ![W Ambari kliknij kartę usług, a następnie HBase w menu po lewej stronie, a następnie zatrzymać w obszarze akcje usługi](./media/apache-hbase-migrate-new-version/stop-hbase-services.png)

    ![Włącz na trybu konserwacji dla bazy danych HBase pole wyboru, a następnie potwierdź](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

7. Zaloguj się do narzędzia Ambari w nowym klastrze usługi HDInsight. Zmień `fs.defaultFS` ustawienie systemu plików HDFS wskaż używane przez klaster oryginalna nazwa kontenera. To ustawienie znajduje się w **HDFS > Configs > Zaawansowane > Zaawansowane witryny core**.

    ![W Ambari kliknij kartę usługi, a następnie system plików HDFS menu po lewej stronie, następnie na karcie Configs, następnie kartę Zaawansowane poniżej](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![W Ambari Zmień nazwę kontenera](./media/apache-hbase-migrate-new-version/change-container-name.png)

8. Zapisz zmiany.
9. Uruchom ponownie wszystkie wymagane usługi, wskazywany przez Ambari.
10. Polecenie aplikacji do nowego klastra.

    > [!NOTE]
    > Statyczne DNS wprowadzanie zmian aplikacji podczas uaktualniania. Zamiast kodować to DNS, można skonfigurować rekord CNAME w ustawieniach nazwę domeny DNS, które wskazuje nazwę klastra. Innym rozwiązaniem jest przy użyciu pliku konfiguracji aplikacji, które można zaktualizować bez ponownego wdrażania.

11. Rozpocznij wprowadzanie, aby zobaczyć, czy jest wszystko działa zgodnie z oczekiwaniami.
12. Jeśli nowy klaster znajduje się zadowalające, Usuń oryginalnego klastra.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat HBase i uaktualnianie klastrów usługi HDInsight, zobacz następujące artykuły:

* [Uaktualnij do nowszej wersji klastra usługi HDInsight](../hdinsight-upgrade-cluster.md)
* [Monitorowanie i zarządzanie nimi za pomocą interfejsu użytkownika sieci Web Ambari w usłudze Azure HDInsight](../hdinsight-hadoop-manage-ambari.md)
* [Składniki platformy Hadoop i wersje](../hdinsight-component-versioning.md)
* [Optymalizowanie konfiguracji przy użyciu Ambari](../hdinsight-changing-configs-via-ambari.md#hbase-optimization-with-the-ambari-web-ui)
