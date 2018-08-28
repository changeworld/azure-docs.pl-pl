---
title: Użyj Apache Phoenix i SQLLine z bazą danych HBase w usłudze Azure HDInsight
description: Dowiedz się, jak używać rozwiązania Apache Phoenix w HDInsight. Ponadto Dowiedz się, jak zainstalować i skonfigurować SQLLine na komputerze, aby nawiązać połączenie z klastra usługi HBase na platformie HDInsight.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: jasonh
ms.openlocfilehash: 92e6dcf7bf467bac31798df994e7efa8da40f035
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43045662"
---
# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>Apache Phoenix za pomocą klastrów HBase opartych na systemie Linux na platformie HDInsight
Dowiedz się, jak używać [Apache Phoenix](http://phoenix.apache.org/) w usłudze Azure HDInsight i sposobu użycia SQLLine. Aby uzyskać więcej informacji na temat Phoenix zobacz [Phoenix w ciągu 15 minut lub szybciej](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Aby gramatykę Phoenix, zobacz [gramatyki Phoenix](http://phoenix.apache.org/language/index.html).

> [!NOTE]
> Aby Phoenix wersji o HDInsight, zobacz [nowości w wersjach klastra Hadoop dostarczanych przez HDInsight](../hdinsight-component-versioning.md).
>
>

## <a name="use-sqlline"></a>Użyj SQLLine
[SQLLine](http://sqlline.sourceforge.net/) to narzędzie wiersza polecenia do wykonania SQL.

### <a name="prerequisites"></a>Wymagania wstępne
Zanim użyjesz SQLLine, należy dysponować następującymi elementami:

* **Klaster bazy danych HBase w HDInsight**. Aby go utworzyć, zobacz [Rozpoczynanie pracy z usługą Apache HBase w HDInsight](./apache-hbase-tutorial-get-started-linux.md).

Po nawiązaniu połączenia z klastrem HBase, należy połączyć się z jednej z maszyn wirtualnych ZooKeeper. Każdy klaster HDInsight ma trzy dozorcy maszyny wirtualne.

**Aby uzyskać nazwę hosta dozorcy**

1. Otworzyć narzędzie Ambari, przechodząc do **https://\<nazwy klastra\>. azurehdinsight.net**.
2. Aby się zarejestrować, wprowadź HTTP (klaster), nazwę użytkownika i hasło.
3. W menu po lewej stronie wybierz **dozorcy**. Trzy **serwera dozorcy** wystąpienia są wyświetlane.
4. Wybierz jedną z **serwera dozorcy** wystąpień. Na **Podsumowanie** okienku Znajdź **Hostname**. Będzie on podobny do *zk1 jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Aby użyć SQLLine**

1. Połącz się z klastrem przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. W przypadku protokołu SSH Użyj następujących poleceń, aby uruchomić SQLLine:

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ZOOKEEPER SERVER FQDN>:2181:/hbase-unsecure
3. Do tworzenia tabel HBase i wstawić dane, uruchom następujące polecenia:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

Aby uzyskać więcej informacji, zobacz [ręczne SQLLine](http://sqlline.sourceforge.net/#manual) i [gramatyki Phoenix](http://phoenix.apache.org/language/index.html).

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób korzystania z rozwiązaniem Apache Phoenix w HDInsight. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Omówienie bazy danych HDInsight HBase][hdinsight-hbase-overview].
  Baza danych HBase jest Apache, bazy danych NoSQL typu open-source, oparte na platformie Hadoop, która zapewnia dostęp losowy i wysoki poziom spójności w przypadku dużych ilości danych z częściową strukturą i bez struktury.
* [Obsługa administracyjna klastrów HBase w usłudze Azure Virtual Network][hdinsight-hbase-provision-vnet].
  Dzięki integracji sieci wirtualnej można wdrożyć klastry HBase do tej samej sieci wirtualnej jako aplikacji, dzięki czemu aplikacje mogą komunikować się bezpośrednio z bazą danych HBase.
* [Konfigurowanie replikacji bazy danych HBase w HDInsight](apache-hbase-replication.md). Dowiedz się, jak skonfigurować replikacji bazy danych HBase w dwóch centrach danych platformy Azure.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp
[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:apache-hbase-overview.md


