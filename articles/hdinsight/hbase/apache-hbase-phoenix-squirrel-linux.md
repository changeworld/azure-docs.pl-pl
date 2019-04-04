---
title: Użyj Apache Phoenix i SQLLine z bazą danych HBase w usłudze Azure HDInsight
description: Dowiedz się, jak używać rozwiązania Apache Phoenix w HDInsight. Ponadto Dowiedz się, jak zainstalować i skonfigurować SQLLine na komputerze, aby nawiązać połączenie z klastra usługi HBase na platformie HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: hrasheed
ms.openlocfilehash: 38f86bd19c85440fbad0e7fd56a3dd9ba836c7b8
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58903306"
---
# <a name="use-apache-phoenix-with-linux-based-apache-hbase-clusters-in-hdinsight"></a>Użyj rozwiązania Apache Phoenix klastrów opartych na systemie Linux bazy danych Apache HBase na platformie HDInsight
Dowiedz się, jak używać [Apache Phoenix](https://phoenix.apache.org/) w usłudze Azure HDInsight i sposobu użycia SQLLine. Aby uzyskać więcej informacji na temat Phoenix zobacz [Apache Phoenix w ciągu 15 minut lub szybciej](https://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Aby gramatykę Phoenix, zobacz [gramatyki Apache Phoenix](https://phoenix.apache.org/language/index.html).

> [!NOTE]  
> Aby Phoenix wersji o HDInsight, zobacz [nowości w wersjach klastra platformy Apache Hadoop, dostarczone przez HDInsight](../hdinsight-component-versioning.md).
>
>

## <a name="use-sqlline"></a>Użyj SQLLine
[SQLLine](http://sqlline.sourceforge.net/) to narzędzie wiersza polecenia do wykonania SQL.

### <a name="prerequisites"></a>Wymagania wstępne
Zanim użyjesz SQLLine, należy dysponować następującymi elementami:

* **Klaster bazy danych Apache HBase w HDInsight**. Aby go utworzyć, zobacz [Rozpoczynanie pracy z usługą Apache HBase w HDInsight](./apache-hbase-tutorial-get-started-linux.md).

Po nawiązaniu połączenia z klastrem HBase, należy połączyć się z jednego z [Apache ZooKeeper](https://zookeeper.apache.org/) maszyn wirtualnych. Każdy klaster HDInsight ma trzy dozorcy maszyny wirtualne.

**Aby uzyskać nazwę hosta dozorcy**

1. Otwórz [Apache Ambari](https://ambari.apache.org/) , przechodząc do **https://\<nazwy klastra\>. azurehdinsight.net**.
2. Aby się zarejestrować, wprowadź HTTP (klaster), nazwę użytkownika i hasło.
3. W menu po lewej stronie wybierz **dozorcy**. Trzy **serwera dozorcy** wystąpienia są wyświetlane.
4. Wybierz jedną z **serwera dozorcy** wystąpień. Na **Podsumowanie** okienku Znajdź **Hostname**. Będzie on podobny do *zk1 jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Aby użyć SQLLine**

1. Połącz się z klastrem przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. W przypadku protokołu SSH Użyj następujących poleceń, aby uruchomić SQLLine:

        cd /usr/hdp/current/phoenix-client/bin
        ./sqlline.py <ZOOKEEPER SERVER FQDN>:2181:/hbase-unsecure
3. Do tworzenia tabel HBase i wstawić dane, uruchom następujące polecenia:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

Aby uzyskać więcej informacji, zobacz [ręczne SQLLine](http://sqlline.sourceforge.net/#manual) i [gramatyki Apache Phoenix](https://phoenix.apache.org/language/index.html).

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób korzystania z rozwiązaniem Apache Phoenix w HDInsight. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Omówienie bazy danych HDInsight HBase][hdinsight-hbase-overview].
  Apache HBase to Apache, typu open-source, baza danych NoSQL oparta na technologii Apache Hadoop, która zapewnia dostęp losowy i wysoki poziom spójności w przypadku dużych ilości danych z częściową strukturą i bez struktury.
* [Aprowizacja Apache HBase klastrów w usłudze Azure Virtual Network][hdinsight-hbase-provision-vnet].
  Dzięki integracji sieci wirtualnej można wdrożyć klastry Apache HBase do tej samej sieci wirtualnej jako aplikacji, dzięki czemu aplikacje mogą komunikować się bezpośrednio z bazą danych HBase.
* [Konfigurowanie replikacji bazy danych Apache HBase w HDInsight](apache-hbase-replication.md). Dowiedz się, jak skonfigurować replikację bazy danych Apache HBase w dwóch centrach danych platformy Azure.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:apache-hbase-overview.md


