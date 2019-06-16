---
title: Konfigurowanie kopii zapasowej bazy danych Apache HBase i Apache Phoenix i replikacji — Azure HDInsight
description: Konfigurowanie replikacji bazy danych HBase i rozwiązania Phoenix i kopii zapasowych.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: e60aef7b1848197f41f96a1b5f5414bb0c8f4a15
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64696375"
---
# <a name="set-up-backup-and-replication-for-apache-hbase-and-apache-phoenix-on-hdinsight"></a>Konfigurowanie kopii zapasowej i replikacji dla bazy danych Apache HBase i Apache Phoenix w HDInsight

Ochrona przed utratą danych Apache HBase obsługuje kilka metod:

* Kopiuj `hbase` folderu
* Następnie zaimportuj eksportu
* Kopiowanie tabel
* Migawki
* Replikacja

> [!NOTE]  
> Apache Phoenix przechowuje jego metadane w tabelach HBase, dzięki czemu metadanych kopia zapasowa jest tworzona podczas wykonywania kopii zapasowej katalogu tabele systemowe bazy danych HBase.

W poniższych sekcjach opisano scenariusz użycia dla każdego z tych metod.

## <a name="copy-the-hbase-folder"></a>Skopiuj folder bazy danych hbase

Nie jest możliwe wybranie podzbioru tabel lub rodziny kolumn, w przypadku tej metody, skopiuj wszystkie dane z bazy danych HBase. Kolejne podejścia zapewniają większą kontrolę.

Baza danych HBase w HDInsight korzysta z domyślnego magazynu wybrane podczas tworzenia klastra usługi Azure Storage blob lub magazynu usługi Azure Data Lake. W obu przypadkach HBase przechowuje jego plików danych i metadanych w następującej ścieżce:

    /hbase

* Na koncie usługi Azure Storage `hbase` folder znajduje się w folderze głównym określonego kontenera obiektów blob:

    ```
    wasbs://<containername>@<accountname>.blob.core.windows.net/hbase
    ```

* W usłudze Azure Data Lake Storage `hbase` folder znajduje się w ścieżce katalogu głównego, zostały określone podczas inicjowania obsługi klastra. Zazwyczaj jest to ścieżka katalogu głównego `clusters` folderem podfolder o nazwie po klastra usługi HDInsight:

    ```
    /clusters/<clusterName>/hbase
    ```

W obu przypadkach `hbase` folder zawiera wszystkie dane, które bazy danych HBase jest opróżniany do dysku, ale nie może zawierać dane w pamięci. Zanim możesz polegać na ten folder jako dokładne reprezentacja danych HBase, należy zamknąć klastra.

Po usunięciu klastra, możesz pozostawić dane w miejscu lub skopiować dane do nowej lokalizacji:

* Utwórz nowe wystąpienie HDInsight wskazuje bieżącą lokalizację magazynu. Z istniejącymi danymi tworzone jest nowe wystąpienie.

* Kopiuj `hbase` folderu do innego magazynu Azure blob kontenera lub lokalizacji magazynu usługi Data Lake i uruchom nowy klaster z tymi danymi. W przypadku usługi Azure Storage, użyj [AzCopy](../../storage/common/storage-use-azcopy.md)i do użycia usługi Data Lake Storage [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md).

## <a name="export-then-import"></a>Następnie zaimportuj eksportu

W klastrze HDInsight źródła narzędzie eksportu (dołączone do bazy danych HBase) eksportowanie danych z tabeli źródłowej do domyślny, dołączony magazyn. Możesz następnie skopiować folder wyeksportowane do lokalizacji magazynu docelowego i uruchom narzędzie importu w docelowym klastrze HDInsight.

Aby wyeksportować tabelę pierwszy SSH do węzła głównego klastra usługi HDInsight źródła i następnie uruchom następujące polecenie, `hbase` polecenia:

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"

Aby zaimportować tabelę, SSH do węzła głównego klastra usługi HDInsight docelowego, a następnie uruchom następujące `hbase` polecenia:

    hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"

Określ ścieżkę pełną eksportu do domyślnego magazynu lub jedną z opcji dołączonego magazynu. Na przykład w usłudze Azure Storage:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

W usłudze Azure Data Lake magazynu Gen2 składnia jest następująca:

    abfs://<containername>@<accountname>.dfs.core.windows.net/<path>

W usłudze Azure Data Lake magazynu Gen1 składnia jest następująca:

    adl://<accountName>.azuredatalakestore.net:443/<path>

To podejście zapewnia poziom szczegółowości na poziomie tabeli. Można również określić zakres dat dla wierszy uwzględnić, co pozwala na wykonanie procesu przyrostowo. Każdy przypada w milisekundach od początku epoki systemu Unix.

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>

Pamiętaj, że należy określić liczbę wersji każdego wiersza do wyeksportowania. Aby dołączyć wszystkie wersje zakres dat, ustaw `<numberOfVersions>` do wartości większej niż swoje wersje maksymalna możliwa wiersz, takie jak 100000.

## <a name="copy-tables"></a>Kopiowanie tabel

Narzędzie CopyTable kopiuje dane z tabeli źródłowej, wiersz po wierszu, do istniejącej tabeli docelowej za pomocą tego samego schematu jako źródło. Tabela docelowa może być na tym samym klastrze lub do innego klastra HBase.

Aby użyć CopyTable w klastrze, SSH do węzła głównego klastra usługi HDInsight źródła, a następnie uruchom to `hbase` polecenia:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>

Aby użyć CopyTable do skopiowania do tabeli w innym klastrze, należy dodać `peer` Przełącz się z adres klastra docelowego:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>

Adres docelowy składa się z następujących trzech części:

    <destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>

* `<ZooKeeperQuorum>` jest rozdzielaną przecinkami listę węzłów Apache ZooKeeper, na przykład:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>` ustawieniami domyślnymi HDInsight do 2181 i `<ZnodeParent>` jest `/hbase-unsecure`, dlatego pełny `<destinationAddress>` będzie:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-unsecure

Zobacz [ręcznego gromadzenia listy kworum dozorcy Apache](#manually-collect-the-apache-zookeeper-quorum-list) w tym artykule, aby uzyskać szczegółowe informacje na temat sposobu pobierania tych wartości dla klastra usługi HDInsight.

Narzędzie CopyTable obsługuje również parametry, aby określić zakres czasu wierszy, aby skopiować, a także określić podzbiór rodziny kolumn w tabeli, aby skopiować. Aby wyświetlić pełną listę parametrów obsługiwanych przez CopyTable, uruchom CopyTable bez żadnych parametrów:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable

CopyTable skanuje źródło całej zawartości tabeli, które mają zostać skopiowane do tabeli docelowej. Może to zmniejszyć wydajność klastra HBase, podczas CopyTable wykonuje.

> [!NOTE]  
> Aby zautomatyzować kopiowanie danych między tabelami, zobacz `hdi_copy_table.sh` skryptu w [Utils bazy danych HBase w usłudze Azure](https://github.com/Azure/hbase-utils/tree/master/replication) repozytorium w witrynie GitHub.

### <a name="manually-collect-the-apache-zookeeper-quorum-list"></a>Ręcznego zebrania kworum Apache ZooKeeper listy

W przypadku obu klastrach HDInsight znajdują się w tej samej sieci wirtualnej, zgodnie z opisem w poprzedniej sekcji, rozpoznawanie nazwy hosta wewnętrznego jest automatycznie. Aby użyć CopyTable klastrów HDInsight w dwóch odrębnych sieci wirtualne połączone przez bramę sieci VPN, należy podać hosta adresów IP węzłów dozorcy kworum.

Uzyskanie nazwy hostów kworum, uruchom następujące polecenie curl:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"

Polecenie programu curl pobiera dokument JSON przy użyciu informacji o konfiguracji bazy danych HBase i grep polecenie zwraca tylko wpis "hbase.zookeeper.quorum", na przykład:

    "hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"

Wartość nazwy hosta kworum jest cały ciąg po prawej stronie dwukropkiem.

Aby pobrać adresy IP dla tych hostów, należy użyć następującego polecenia curl dla każdego hosta na poprzedniej liście:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"

W tym poleceniu curl `<zookeeperHostFullName>` jest pełną nazwą DNS hosta ZooKeeper, takiego jak przykładowy `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net`. Dane wyjściowe polecenia zawierają na przykład adres IP dla określonego hosta:

    100    "ip" : "10.0.0.9",

Po zebraniu adresów IP dla wszystkich węzłów dozorcy w swojej kworum, należy ponownie skompilować adres docelowy:

    <destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>

W naszym przykładzie:

    <destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure

## <a name="snapshots"></a>Migawki

Migawki pozwalają wzięcie punktu w czasie tworzenia kopii zapasowych danych w magazyn danych z bazy danych HBase. Migawki mają minimalne obciążenie i ukończyć w ciągu sekund, ponieważ operacja migawki jest skutecznie przechwytywania nazwy wszystkich plików w magazynie w tej chwili operacja na metadanych. W momencie migawki nie rzeczywiste dane są kopiowane. Migawki zależą od niezmienne rodzaj danych przechowywanych w systemie plików HDFS, w którym aktualizacje, usunięcia i wstawienia są reprezentowane jako nowe dane. Możesz przywrócić (*klonowania*) migawki na tym samym klastrze lub Eksportuj migawkę do innego klastra.

Do tworzenia migawek, SSH w do węzła głównego hbase usługi HDInsight, klaster i rozpocząć `hbase` powłoki:

    hbase shell

W ramach powłoki hbase polecenie migawki za pomocą nazwy tabeli i tej migawki:

    snapshot '<tableName>', '<snapshotName>'

Można przywrócić migawki według nazwy w obrębie `hbase` powłoki, najpierw wyłącz tabeli, a następnie przywrócić migawki i ponownie włącz tabeli:

    disable '<tableName>'
    restore_snapshot '<snapshotName>'
    enable '<tableName>'

Aby przywrócić migawki do nowej tabeli, należy użyć clone_snapshot:

    clone_snapshot '<snapshotName>', '<newTableName>'

Aby wyeksportować migawkę do systemu plików HDFS do użytku przez inny klaster, najpierw Utwórz migawkę, zgodnie z opisem w poprzedniej sekcji, a następnie użyj narzędzia ExportSnapshot. Uruchom to narzędzie z w ramach sesji SSH z węzłem głównym, nie w ramach `hbase` powłoki:

     hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>

`<hdfsHBaseLocation>` Może być dowolną z lokalizacji magazynu dostępne dla klastra źródłowego i powinien wskazywać folder bazy danych hbase, używany przez Twój klaster docelowy. Na przykład jeśli masz pomocniczego konta usługi Azure Storage dołączone do klastra źródłowego, a to konto zapewnia dostęp do kontenera, użyty przez domyślnego magazynu klastra docelowego, można użyć tego polecenia:

    hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'

Po wyeksportowaniu migawki SSH z węzłem głównym klastra docelowego i przywracanie migawki za pomocą restore_snapshot poleceń w postaci opisany wcześniej.

Migawki zapewniają pełna kopia zapasowa tabeli w czasie `snapshot` polecenia. Migawki są oferowane możliwości wykonywania migawek przyrostowych przez system windows czasu ani określić podzbiór rodzin kolumn do uwzględnienia w migawce.

## <a name="replication"></a>Replikacja

Replikacji bazy danych HBase automatycznie wypycha transakcji w źródłowym klastrze docelowym klastrze przy użyciu mechanizmu asynchronicznego przy minimalnym obciążeniu w klastrze źródłowym. W HDInsight, możesz skonfigurować replikację między klastrami gdzie:

* Klastry źródłowy i docelowy znajdują się w tej samej sieci wirtualnej.
* Klastry źródła i miejsca docelowe są w różnych sieciach wirtualnych połączonych przez bramę sieci VPN, ale starym istnieje w tej samej lokalizacji geograficznej.
* Klastra źródłowego i klastrów miejsc docelowych znajdują się w różnych sieciach wirtualnych połączonych przez bramę sieci VPN, a każdy klaster istnieje w innej lokalizacji geograficznej.

Dostępne są następujące ogólne kroki, aby skonfigurować replikację:

1. W klastrze źródłowym tworzenia tabel, a następnie wprowadź dane.
2. W klastrze docelowym Tworzenie tabel docelowych pusty schemat tabeli źródłowej.
3. Rejestrowanie klastra docelowego jako elementu równorzędnego do klastra źródłowego.
4. Włącz replikację w tabelach źródłowych żądaną.
5. Skopiuj dane z tabel źródłowych do tabel docelowych.
6. Replikacja automatycznie kopiuje nowy modyfikacji danych do tabel źródłowych w tabelach docelowych.

Aby włączyć replikację na HDInsight, dotyczą akcji skryptu uruchomionego źródłowego klastra HDInsight. Instrukcje dotyczące włączania replikacji w klastrze lub na eksperymentowanie z replikacją w klastrach przykładowe utworzone w sieciach wirtualnych za pomocą szablonów usługi Azure Resource Management, zobacz [replikacji Konfigurowanie bazy danych Apache HBase](apache-hbase-replication.md). Ten artykuł zawiera również instrukcje dotyczące włączania replikacji Phoenix metadanych.

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie replikacji bazy danych Apache HBase](apache-hbase-replication.md)
