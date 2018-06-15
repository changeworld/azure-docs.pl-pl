---
title: Konfigurowanie kopii zapasowej bazy danych HBase i Phoenix i replikacji - Azure HDInsight | Dokumentacja firmy Microsoft
description: Konfigurowanie kopii zapasowej i replikacji bazy danych HBase i Phoenix.
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
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: f43edaf16784e5ba5cc3d2b39df285582954210b
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165336"
---
# <a name="set-up-backup-and-replication-for-hbase-and-phoenix-on-hdinsight"></a>Konfigurowanie kopii zapasowej i replikacji bazy danych HBase i Phoenix w usłudze HDInsight

Ochrona przed utratą danych HBase obsługuje kilka metod:

* Kopiuj `hbase` folderu
* Następnie zaimportuj eksportu
* Kopiowanie tabel
* Migawki
* Replikacja

> [!NOTE]
> Apache Phoenix przechowuje metadanych w tabelach HBase, dzięki czemu metadanych kopii zapasowej podczas wykonywania kopii zapasowej tabel wykazu systemu HBase.

W poniższych sekcjach opisano scenariusz użycia dla każdego z tych metod.

## <a name="copy-the-hbase-folder"></a>Skopiuj folder bazy danych hbase

Takie podejście możesz kopiowanie wszystkich danych HBase, nie jest możliwe wybranie podzbioru tabel lub rodzinami kolumn. Kolejne podejść zapewnia większą kontrolę.

Baza danych HBase w usłudze HDInsight używa magazynu domyślne wybranej podczas tworzenia klastra, obiektach blob magazynu Azure lub usługi Azure Data Lake Store. W obu przypadkach HBase przechowuje pliki danych i metadanych w następującej ścieżce:

    /hbase

* Na koncie magazynu Azure `hbase` folder znajduje się w katalogu głównym kontenera obiektów blob:

    ```
    wasbs://<containername>@<accountname>.blob.core.windows.net/hbase
    ```

* W usłudze Azure Data Lake Store `hbase` znajduje się folder w ścieżce katalogu głównego określone podczas inicjowania obsługi klastra. Zazwyczaj jest to ścieżka katalogu głównego `clusters` folderu podfolder o nazwie po z klastrem usługi HDInsight:

    ```
    /clusters/<clusterName>/hbase
    ```

W obu przypadkach `hbase` folder zawiera wszystkie dane, które HBase opróżnia na dysku, ale nie może zawierać danych w pamięci. Aby dla tego folderu może wykorzystywać jako dokładną reprezentację danych HBase, należy wyłączyć klastra.

Po usunięciu klastra możesz pozostawić bez zmian danych lub skopiować dane do nowej lokalizacji:

* Utwórz nowe wystąpienie usługi HDInsight wskazuje bieżącą lokalizację magazynu. Nowe wystąpienie jest tworzony z istniejącymi danymi.

* Kopiuj `hbase` folderu do innego magazynu Azure blob kontenera lub lokalizacji usługi Data Lake Store, a następnie uruchom nowy klaster z tymi danymi. Dla usługi Azure Storage, użyj [AzCopy](../../storage/common/storage-use-azcopy.md)i do użycia usługi Data Lake Store [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md).

## <a name="export-then-import"></a>Następnie zaimportuj eksportu

W źródłowym klastrze usługi HDInsight należy użyć narzędzia eksportu (dołączony do bazy danych HBase) do eksportowania danych z tabeli źródłowej do magazynu domyślnego dołączony. Następnie można skopiować folder wyeksportowany do lokalizacji magazynu docelowego i uruchom narzędzie importu w docelowym klastrze usługi HDInsight.

Aby wyeksportować tabeli pierwszy SSH do węzła głównego klastra usługi HDInsight źródła, a następnie uruchom następujące `hbase` polecenia:

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"

Do importowania tabeli, SSH do węzła głównego klastra usługi HDInsight docelowego, a następnie uruchom następujące `hbase` polecenia:

    hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"

Określ eksportu pełną ścieżkę do magazynu domyślnego lub opcje magazynu. Na przykład w usłudze Azure Storage:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

W usłudze Azure Data Lake Store składnia jest następująca:

    adl://<accountName>.azuredatalakestore.net:443/<path>

To rozwiązanie oferuje tabeli poziom szczegółowości. Można również określić zakresu dat wiersze do dołączenia, dzięki czemu można przyrostowo wykonania procesu. Każdy Data jest w milisekundach od epoki systemu Unix.

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>

Należy pamiętać, że trzeba określić numer wersji każdego wiersza, aby wyeksportować. Aby dołączyć wszystkie wersje zakres dat, ustaw `<numberOfVersions>` do wartości większej niż Twojej wersji maksymalna możliwa wiersza, takich jak 100000.

## <a name="copy-tables"></a>Kopiowanie tabel

Narzędzie CopyTable kopiuje dane z tabeli źródłowej, wiersz po wierszu do istniejącej tabeli docelowej z tego samego schematu jako źródło. Tabela docelowa może być na tym samym klastrze lub do innego klastra HBase.

Aby użyć CopyTable w klastrze, SSH do węzła głównego klastra usługi HDInsight źródła, a następnie uruchom to `hbase` polecenia:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>

Aby użyć CopyTable do skopiowania do tabeli na innego klastra, Dodaj `peer` przełącznik z adres klastra docelowego:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>

Adres docelowy składa się z następujących trzech części:

    <destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>

* `<ZooKeeperQuorum>` na przykład jest rozdzielana przecinkami lista węzły dozorcy:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>` w usłudze HDInsight, wartością domyślną będzie 2181 i `<ZnodeParent>` jest `/hbase-unsecure`, więc pełną `<destinationAddress>` będzie:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-unsecure

Zobacz [ręcznie zbierania listy kworum dozorcy](#manually-collect-the-zookeeper-quorum-list) w tym artykule, aby uzyskać więcej informacji na temat sposobu pobierania tych wartości dla klastra usługi HDInsight.

Narzędzie CopyTable obsługuje również parametry, aby określić zakres czasu wierszy, aby skopiować i określić podzestaw rodzinami kolumn w tabeli, aby skopiować. Aby wyświetlić pełną listę parametrów obsługiwanych przez CopyTable, uruchom CopyTable bez żadnych parametrów:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable

CopyTable skanuje całej tabeli zawartości źródłowej, które będą kopiowane do tabeli docelowej. Może to zmniejszyć wydajność klastra HBase podczas wykonuje CopyTable.

> [!NOTE]
> Aby zautomatyzować kopiowanie danych między tabelami, zobacz `hdi_copy_table.sh` skryptu w [witryny HBase Azure](https://github.com/Azure/hbase-utils/tree/master/replication) repozytorium w witrynie GitHub.

### <a name="manually-collect-the-zookeeper-quorum-list"></a>Ręcznie zebrać kworum dozorcy listy

Gdy w obu klastrach HDInsight znajdują się w tej samej sieci wirtualnej, jak opisano wcześniej, rozpoznawania nazw wewnętrznych hosta odbywa się automatycznie. Aby użyć CopyTable klastrów usługi HDInsight w dwóch różnych sieciach wirtualnych połączenie przez bramę sieci VPN, konieczne będzie Zapewnij hostowi adresy IP węzły dozorcy w kworum.

Uzyskanie nazwy hostów kworum, uruchom następujące polecenie curl:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"

Polecenia curl pobiera dokument JSON o informacje o konfiguracji bazy danych HBase, a polecenie grep zwraca tylko wpis "hbase.zookeeper.quorum", na przykład:

    "hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"

Wartość nazwy hosta kworum jest cały ciąg z prawej strony dwukropkiem.

Aby pobrać adresy IP dla tych hostów, należy użyć następującego polecenia curl dla każdego hosta na poprzedniej liście:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"

W tym poleceniu curl `<zookeeperHostFullName>` jest pełną nazwą DNS hosta dozorcy, takich jak na przykład `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net`. Dane wyjściowe polecenia zawiera adres IP, na przykład na określonym hoście:

    100    "ip" : "10.0.0.9",

Po zebraniu adresów IP dla wszystkich węzłów dozorcy w Twojej kworum, skompiluj ponownie adres docelowy:

    <destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>

W naszym przykładzie:

    <destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure

## <a name="snapshots"></a>Migawki

Migawki pozwalają zająć punktu w czasie tworzenia kopii zapasowej danych w sieci magazynu danych HBase. Migawki mają minimalnego koszty i ukończyć w ciągu kilku sekund, ponieważ operacja migawki skutecznie jest operacją metadanych Przechwytywanie nazwy wszystkich plików w magazynie w tej chwili. W czasie migawki nie rzeczywiste dane są kopiowane. Migawki są zależne od niezmienne rodzaj danych przechowywanych w systemie plików HDFS, gdzie aktualizacji, usuwania i wstawia są wszystkie reprezentowane jako nowe dane. Możesz przywrócić (*klonowania*) migawki na tym samym klastrze lub wyeksportować migawkę do innego klastra.

Do tworzenia migawek, SSH w do węzła głównego hbase HDInsight, z klastra, a następnie rozpocznij `hbase` powłoki:

    hbase shell

W ramach powłoki hbase za pomocą polecenia migawki nazwy tabeli i tej migawki:

    snapshot '<tableName>', '<snapshotName>'

Aby przywrócić migawkę według nazwy w `hbase` powłoki, najpierw wyłącz tabeli, a następnie przywrócenia migawki i ponownie włączyć tabelę:

    disable '<tableName>'
    restore_snapshot '<snapshotName>'
    enable '<tableName>'

Aby przywrócić migawki do nowej tabeli, należy użyć clone_snapshot:

    clone_snapshot '<snapshotName>', '<newTableName>'

Aby wyeksportować migawkę do systemu plików HDFS do użytku przez innego klastra, najpierw Utwórz migawkę opisanych powyżej, a następnie użyj narzędzia ExportSnapshot. Uruchom to narzędzie z sesji SSH z węzłem głównym nie poziomu `hbase` powłoki:

     hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>

`<hdfsHBaseLocation>` Może być dowolną lokalizacji przechowywania dostępne dla źródłowego klastra i powinny wskazywać na folder bazy danych hbase, używane przez klaster docelowy. Na przykład jeśli masz konto magazynu Azure dodatkowej dołączony do klastra źródłowego, a to konto zapewnia dostęp do kontenera używane przez Magazyn domyślny klastra docelowego, można użyć tego polecenia:

    hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'

Po wyeksportowaniu migawki SSH do węzła głównego klastra docelowego i przywracanie migawki za pomocą restore_snapshot polecenie sposób opisany wcześniej.

Migawki zapewnienia pełnej kopii zapasowej tabeli w czasie `snapshot` polecenia. Migawki nie umożliwiają przyrostowe migawki przez system windows w czasie wykonywania, ani określić podzbiór rodzin kolumn, aby uwzględnić w migawce.

## <a name="replication"></a>Replikacja

Replikacja bazy danych HBase automatycznie wypycha transakcji z klastra źródłowego do klastra docelowego, przy użyciu mechanizmu asynchroniczne przy minimalnym obciążeniu w klastrze źródłowym. W usłudze HDInsight, można skonfigurować replikację między klastrami gdzie:

* Klastry źródłowy i docelowy znajdują się w tej samej sieci wirtualnej.
* Klastry źródło i lokalizacja docelowe są w różnych sieciach wirtualnych połączenie przez bramę sieci VPN, ale istnieje zarówno klastrów w tej samej lokalizacji geograficznej.
* Źródło klastra i klastrów miejsc docelowych znajdują się w różnych sieciach wirtualnych połączenie przez bramę sieci VPN i każdego klastra nie istnieje w innej lokalizacji geograficznej.

Dostępne są następujące ogólne kroki w celu skonfigurowania replikacji:

1. W klastrze źródłowym tworzenia tabel i wypełnić danych.
2. W docelowym klastrze należy utworzyć tabel docelowych pusty ze schematem tabeli źródłowej.
3. Zarejestruj klastra docelowego jako elementu równorzędnego do klastra źródłowego.
4. Włącz replikację w tabelach żądanego źródła.
5. Skopiuj dane z tabel źródłowych do tabel docelowych.
6. Replikacja automatycznie kopiuje nowe modyfikacji danych do tabel źródłowych do tabel docelowych.

Aby włączyć replikację w usłudze HDInsight, dotyczą akcji skryptu uruchomionego źródłowego klastra usługi HDInsight. Aby uzyskać wskazówki włączania replikacji w klastrze lub do eksperymentów z replikacji w klastrach próbki utworzone w sieci wirtualnych za pomocą szablonów zarządzania zasobami Azure, zobacz [replikacji bazy danych HBase skonfigurować](apache-hbase-replication.md). Ten artykuł zawiera również instrukcje dotyczące włączania replikacji Phoenix metadanych.

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie replikacji bazy danych HBase](apache-hbase-replication.md)
