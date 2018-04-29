---
title: Użyj Apache Phoenix i SQLLine z bazy danych HBase w usłudze Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać Apache Phoenix w usłudze HDInsight. Ponadto informacje o sposobie instalowania i konfigurowania SQLLine komputera do nawiązania połączenia klastra HBase w usłudze HDInsight.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: cda0f33b-a2e8-494c-972f-ae0bb482b818
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: 64700567b8acf816f42e6bf8cdc5386b6c65fe3f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>Apache Phoenix za pomocą klastrów HBase opartych na systemie Linux w usłudze HDInsight
Dowiedz się, jak używać [Apache Phoenix](http://phoenix.apache.org/) w usłudze Azure HDInsight i sposobu użycia SQLLine. Aby uzyskać więcej informacji na temat Phoenix, zobacz [Phoenix w ciągu 15 minut lub mniej](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Dla gramatyki Phoenix [gramatyki Phoenix](http://phoenix.apache.org/language/index.html).

> [!NOTE]
> Phoenix wersja informacji o HDInsight, zobacz [nowości w wersjach klastra Hadoop dostarczanych z usługą HDInsight](../hdinsight-component-versioning.md).
>
>

## <a name="use-sqlline"></a>Użyj SQLLine
[SQLLine](http://sqlline.sourceforge.net/) jest narzędziem wiersza polecenia, aby wykonać SQL.

### <a name="prerequisites"></a>Wymagania wstępne
Przed użyciem SQLLine, musi mieć następujące elementy:

* **Strumieniowego w HDInsight**. Aby go utworzyć, zobacz [Rozpoczynanie pracy z bazy danych Apache HBase w usłudze HDInsight](./apache-hbase-tutorial-get-started-linux.md).

Po podłączeniu do klastra HBase, należy połączyć się z jedną dozorcy maszyn wirtualnych. Każdy klaster HDInsight ma trzy dozorcy maszyn wirtualnych.

**Aby uzyskać nazwę hosta dozorcy**

1. Otwórz Ambari, przechodząc do **https://\<nazwy klastra\>. azurehdinsight.net**.
2. Aby się zarejestrować, wprowadź HTTP (klaster), nazwę użytkownika i hasło.
3. W menu po lewej stronie wybierz **dozorcy**. Trzy **serwera dozorcy** wystąpienia są wyświetlane.
4. Wybierz jedną z **serwera dozorcy** wystąpień. Na **Podsumowanie** okienku Znajdź **Hostname**. Podobny do *zk1 jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Aby użyć SQLLine**

1. Połącz się z klastrem przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. W SSH Użyj następujących poleceń, aby uruchomić SQLLine:

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ZOOKEEPER SERVER FQDN>:2181:/hbase-unsecure
3. Do tworzenia tabel HBase i wstaw dowolne dane, uruchom następujące polecenia:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

Aby uzyskać więcej informacji, zobacz [ręcznego SQLLine](http://sqlline.sourceforge.net/#manual) i [gramatyki Phoenix](http://phoenix.apache.org/language/index.html).

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób użycia Apache Phoenix w usłudze HDInsight. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Omówienie usługi HDInsight HBase][hdinsight-hbase-overview].
  HBase jest Apache, bazy danych NoSQL open source opartą na platformie Hadoop, która zapewnia dostęp losowy i wysoki poziom spójności w przypadku dużych ilości danych z częściową strukturą i bez struktury.
* [Zapewnij HBase klastrów na Azure wirtualnej sieci][hdinsight-hbase-provision-vnet].
  Dzięki integracji sieci wirtualnej można wdrożyć klastrów HBase do tej samej sieci wirtualnej jako aplikacji, więc aplikacje mogą komunikować się bezpośrednio z bazy danych HBase.
* [Konfigurowanie replikacji HBase w HDInsight](apache-hbase-replication.md). Informacje o sposobie konfigurowania replikacji bazy danych HBase między dwoma centrami danych Azure.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp
[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:apache-hbase-overview.md


