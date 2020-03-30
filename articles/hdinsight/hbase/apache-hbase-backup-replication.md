---
title: Kopia zapasowa replikacji & dla Apache HBase, Phoenix — Usługa Azure HDInsight
description: Konfigurowanie kopii zapasowych i replikacji dla apache HBase i Apache Phoenix w usłudze Azure HDInsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: c6d33158b581bf4394a0d1bac2b277830328e110
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495936"
---
# <a name="set-up-backup-and-replication-for-apache-hbase-and-apache-phoenix-on-hdinsight"></a>Konfigurowanie kopii zapasowych i replikacji dla Apache HBase i Apache Phoenix w programie HDInsight

Apache HBase obsługuje kilka metod ochrony przed utratą danych:

* Kopiowanie `hbase` folderu
* Eksportuj, a następnie importuj
* Kopiowanie tabel
* Migawki
* Replikacja

> [!NOTE]  
> Apache Phoenix przechowuje swoje metadane w tabelach HBase, dzięki czemu metadane są archiwizowane podczas łączenia kopii zapasowych tabel katalogu systemu HBase.

W poniższych sekcjach opisano scenariusz użycia dla każdego z tych podejść.

## <a name="copy-the-hbase-folder"></a>Kopiowanie folderu hbase

Dzięki takiemu podejściu można skopiować wszystkie dane HBase, bez możliwości wybrania podzbioru tabel lub rodzin kolumn. Kolejne podejścia zapewniają większą kontrolę.

HBase w usłudze HDInsight używa domyślnego magazynu wybranego podczas tworzenia klastra, obiektów blob usługi Azure Storage lub usługi Azure Data Lake Storage. W obu przypadkach HBase przechowuje swoje pliki danych i metadanych w następującej ścieżce:

    /hbase

* Na koncie usługi `hbase` Azure Storage folder znajduje się w katalogu głównym kontenera obiektów blob:

    ```
    wasbs://<containername>@<accountname>.blob.core.windows.net/hbase
    ```

* W usłudze Azure `hbase` Data Lake Storage folder znajduje się w obszarze ścieżki głównej określonej podczas inicjowania obsługi administracyjnej klastra. Ta ścieżka główna zazwyczaj `clusters` ma folder z podfolderem nazwanym po klastrze HDInsight:

    ```
    /clusters/<clusterName>/hbase
    ```

W obu przypadkach `hbase` folder zawiera wszystkie dane, które HBase został opróżniony na dysk, ale nie może zawierać danych w pamięci. Aby można było polegać na tym folderze jako dokładnej reprezentacji danych HBase, należy zamknąć klaster.

Po usunięciu klastra można pozostawić dane na swoim miejscu lub skopiować dane do nowej lokalizacji:

* Utwórz nowe wystąpienie HDInsight wskazujące bieżącą lokalizację magazynu. Nowe wystąpienie jest tworzone ze wszystkimi istniejącymi danymi.

* Skopiuj `hbase` folder do innego kontenera obiektów blob usługi Azure Storage lub lokalizacji magazynu usługi Data Lake, a następnie uruchom nowy klaster z tymi danymi. W przypadku usługi Azure Storage należy używać [programu AzCopy](../../storage/common/storage-use-azcopy.md), a w przypadku magazynu usługi Data Lake użyj [programu AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md).

## <a name="export-then-import"></a>Eksportuj, a następnie importuj

W źródłowym klastrze HDInsight użyj [narzędzia Eksportuj](https://hbase.apache.org/book.html#export) (dołączonego do bazy HBase), aby wyeksportować dane z tabeli źródłowej do domyślnego dołączonego magazynu. Następnie można skopiować wyeksportowany folder do docelowej lokalizacji magazynu i uruchomić [narzędzie Importuj](https://hbase.apache.org/book.html#import) w docelowym klastrze HDInsight.

Aby wyeksportować dane tabeli, najpierw SSH do węzła `hbase` głównego źródłowego klastra HDInsight, a następnie uruchom następujące polecenie:

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"

Katalog eksportu nie może już istnieć. W nazwie tabeli jest rozróżniana wielkość liter.

Aby zaimportować dane tabeli, SSH do węzła głównego `hbase` docelowego klastra HDInsight, a następnie uruchom następujące polecenie:

    hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"

Tabela musi już istnieć.

Określ pełną ścieżkę eksportu do magazynu domyślnego lub do dowolnej z dołączonych opcji magazynowania. Na przykład w usłudze Azure Storage:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

W usłudze Azure Data Lake Storage Gen2 składnia jest:

    abfs://<containername>@<accountname>.dfs.core.windows.net/<path>

W usłudze Azure Data Lake Storage Gen1 składnia jest:

    adl://<accountName>.azuredatalakestore.net:443/<path>

Takie podejście oferuje szczegółowość na poziomie tabeli. Można również określić zakres dat dla wierszy do uwzględnienia, co pozwala na wykonywanie procesu przyrostowo. Każda data jest w milisekundach od epoki Uniksa.

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>

Należy zauważyć, że należy określić liczbę wersji każdego wiersza do wyeksportowania. Aby uwzględnić wszystkie wersje w `<numberOfVersions>` zakresie dat, należy ustawić wartość większą niż maksymalna możliwa wersja wiersza, na przykład 100000.

## <a name="copy-tables"></a>Kopiowanie tabel

Narzędzie [CopyTable](https://hbase.apache.org/book.html#copy.table) kopiuje dane z tabeli źródłowej wiersz po wierszu do istniejącej tabeli docelowej z tym samym schematem co źródło. Tabela docelowa może znajdować się w tym samym klastrze lub innym klastrze HBase. W nazwach tabel rozróżniana jest wielkość liter.

Aby użyć copytable w klastrze, SSH do węzła głównego źródłowego `hbase` klastra HDInsight, a następnie uruchom to polecenie:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>


Aby skopiować do tabeli w innym klastrze `peer` tabelę copytable, należy dodać przełącznik z adresem klastra docelowego:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>

Adres docelowy składa się z następujących trzech części:

    <destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>

* `<ZooKeeperQuorum>`jest oddzieloną przecinkami listą węzłów Apache ZooKeeper, na przykład:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>`na HDInsight domyślnie 2181, i `<ZnodeParent>` jest `/hbase-unsecure`, więc pełne `<destinationAddress>` będzie:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-unsecure

Zobacz [ręczne zbieranie listy kworum ZooKeeper Apache](#manually-collect-the-apache-zookeeper-quorum-list) w tym artykule, aby uzyskać szczegółowe informacje na temat pobierania tych wartości dla klastra HDInsight.

Narzędzie CopyTable obsługuje również parametry określające zakres czasu wierszy do skopiowania oraz określające podzbiór rodzin kolumn w tabeli do skopiowania. Aby wyświetlić pełną listę parametrów obsługiwanych przez CopyTable, uruchom copytable bez żadnych parametrów:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable

CopyTable skanuje całą zawartość tabeli źródłowej, która zostanie skopiowana do tabeli docelowej. Może to zmniejszyć wydajność klastra HBase podczas wykonywania CopyTable.

> [!NOTE]  
> Aby zautomatyzować kopiowanie danych między tabelami, zobacz `hdi_copy_table.sh` skrypt w repozytorium usługi Azure [HBase Utils](https://github.com/Azure/hbase-utils/tree/master/replication) w usłudze GitHub.

### <a name="manually-collect-the-apache-zookeeper-quorum-list"></a>Ręcznie zbierz listę kworum Apache ZooKeeper

Gdy oba klastry HDInsight znajdują się w tej samej sieci wirtualnej, jak opisano wcześniej, wewnętrzna rozdzielczość nazw hostów jest automatyczna. Aby używać klastrów CopyTable for HDInsight w dwóch oddzielnych sieciach wirtualnych połączonych bramą sieci VPN, musisz podać adresy IP hosta węzłów Zookeeper w kworum.

Aby uzyskać nazwy hosta kworum, uruchom następujące polecenie curl:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"

Polecenie curl pobiera dokument JSON z informacjami o konfiguracji HBase, a polecenie grep zwraca tylko wpis "hbase.zookeeper.quorum", na przykład:

    "hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"

Wartość nazw hostów kworum jest całym ciągiem po prawej stronie dwukropka.

Aby pobrać adresy IP dla tych hostów, użyj następującego polecenia curl dla każdego hosta na poprzedniej liście:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"

W tym polecenie `<zookeeperHostFullName>` curl jest pełna nazwa DNS hosta ZooKeeper, takich jak przykład `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net`. Dane wyjściowe polecenia zawiera adres IP dla określonego hosta, na przykład:

    100    "ip" : "10.0.0.9",

Po zebraniu adresów IP dla wszystkich węzłów ZooKeeper w kworum odbuduj adres docelowy:

    <destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>

W naszym przykładzie:

    <destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure

## <a name="snapshots"></a>Migawki

[Migawki](https://hbase.apache.org/book.html#ops.snapshots) umożliwiają wykonywanie kopii zapasowej danych w punkcie w czasie w magazynie danych HBase. Migawki mają minimalne obciążenie i zakończyć w ciągu kilku sekund, ponieważ operacja migawki jest skutecznie operacji metadanych przechwytywania nazw wszystkich plików w magazynie w tej chwili. W czasie migawki nie są kopiowane żadne rzeczywiste dane. Migawki opierają się na niezmiennym charakterze danych przechowywanych w u usług HDFS, gdzie aktualizacje, usunięcia i wstawia są reprezentowane jako nowe dane. Można przywrócić *(sklonować)* migawkę w tym samym klastrze lub wyeksportować migawkę do innego klastra.

Aby utworzyć migawkę, SSH w do węzła głównego klastra `hbase` HDInsight HBase i uruchomić powłokę:

    hbase shell

W obrębie powłoki hbase użyj polecenia migawki z nazwami tabeli i tej migawki:

    snapshot '<tableName>', '<snapshotName>'

Aby przywrócić migawkę `hbase` według nazwy w powłoce, najpierw wyłącz tabelę, a następnie przywróć migawkę i ponownie włącz tabelę:

    disable '<tableName>'
    restore_snapshot '<snapshotName>'
    enable '<tableName>'

Aby przywrócić migawkę do nowej tabeli, użyj clone_snapshot:

    clone_snapshot '<snapshotName>', '<newTableName>'

Aby wyeksportować migawkę do systemu PLIKÓW HDFS do użycia przez inny klaster, najpierw utwórz migawkę zgodnie z wcześniejszym opisem, a następnie użyj narzędzia ExportSnapshot. Uruchom to narzędzie z poziomu sesji SSH do `hbase` węzła głównego, a nie w obrębie powłoki:

     hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>

`<hdfsHBaseLocation>` Może to być dowolna lokalizacja magazynu dostępna dla klastra źródłowego i powinna wskazywać folder hbase używany przez klaster docelowy. Na przykład jeśli masz dodatkowe konto usługi Azure Storage dołączone do klastra źródłowego i to konto zapewnia dostęp do kontenera używanego przez domyślne przechowywanie klastra docelowego, można użyć tego polecenia:

    hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'

Po wyeksportowanie migawki SSH do węzła głównego klastra docelowego i przywrócić migawkę przy użyciu polecenia restore_snapshot, jak opisano wcześniej.

Migawki zapewniają pełną kopię zapasową tabeli w czasie `snapshot` polecenia. Migawki nie zapewniają możliwość wykonywania przyrostowych migawek przez okna czasu, ani do określenia podzbiorów kolumn rodzin do uwzględnienia w migawce.

## <a name="replication"></a>Replikacja

[Replikacja HBase](https://hbase.apache.org/book.html#_cluster_replication) automatycznie wypycha transakcje z klastra źródłowego do klastra docelowego przy użyciu mechanizmu asynchroniowego przy minimalnym obciążeniu klastra źródłowego. W umiań HDInsight można skonfigurować replikację między klastrami, w których:

* Klastry źródłowe i docelowe znajdują się w tej samej sieci wirtualnej.
* Klastry źródłowe i docelowe znajdują się w różnych sieciach wirtualnych połączonych bramą sieci VPN, ale oba klastry istnieją w tej samej lokalizacji geograficznej.
* Klastry źródłowe i docelowe znajdują się w różnych sieciach wirtualnych połączonych przez bramę sieci VPN, a każdy klaster istnieje w innej lokalizacji geograficznej.

Ogólne kroki konfigurowania replikacji to:

1. W klastrze źródłowym utwórz tabele i wypełnij dane.
2. W klastrze docelowym utwórz puste tabele docelowe ze schematem tabeli źródłowej.
3. Zarejestruj klaster docelowy jako element równorzędny do klastra źródłowego.
4. Włącz replikację w żądanych tabelach źródłowych.
5. Skopiuj istniejące dane z tabel źródłowych do tabel docelowych.
6. Replikacja automatycznie kopiuje nowe modyfikacje danych do tabel źródłowych do tabel docelowych.

Aby włączyć replikację w programie HDInsight, należy zastosować akcję skryptu do uruchomionego źródłowego klastra HDInsight. Aby uzyskać wskazówki dotyczące włączania replikacji w klastrze lub eksperymentowania z replikacją w przykładowych klastrach utworzonych w sieciach wirtualnych przy użyciu szablonów usługi Azure Resource Management, zobacz [Konfigurowanie replikacji apache HBase](apache-hbase-replication.md). Ten artykuł zawiera również instrukcje dotyczące włączania replikacji metadanych Phoenix.

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie replikacji Bazy danych Apache HBase](apache-hbase-replication.md)
* [Praca z narzędziem importu i eksportu bazy HBase](https://blogs.msdn.microsoft.com/data_otaku/2016/12/21/working-with-the-hbase-import-and-export-utility/)
