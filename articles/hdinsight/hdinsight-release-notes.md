---
title: Informacje o wersji dla usługi Azure HDInsight
description: Najnowsze informacje o wersji dla usługi Azure HDInsight. Pobierz porad programistycznych i szczegóły dla usługi Hadoop, Spark, R Server i programu Hive.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 0beac64ceb0bbf729d2f6d0f6a0ca8d5af499f42
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64725429"
---
# <a name="release-notes-for-azure-hdinsight"></a>Informacje o wersji dla usługi Azure HDInsight

Ten artykuł zawiera informacje na temat **najnowszych** aktualizacji wydania usługi Azure HDInsight. Aby uzyskać informacji o wcześniejszych wersjach, zobacz [HDInsight Release Notes archiwum](hdinsight-release-notes-archive.md).

> [!IMPORTANT]  
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz [artykułu versioning HDInsight](hdinsight-component-versioning.md).

## <a name="summary"></a>Podsumowanie

Azure HDInsight jest jedną z najpopularniejszych usług używanych przez klientów korporacyjnych na potrzeby analizy w narzędziach typu open source Apache Hadoop i Apache Spark na platformie Azure.

## <a name="new-features"></a>Nowe funkcje

Aby uzyskać więcej informacji na temat ważnych zmian przy użyciu HDInsight w wersji 4.0., zobacz [co nowego w usłudze HDI w wersji 4.0?](../hdinsight/hdinsight-version-release.md).

## <a name="component-versions"></a>Wersje składników

Oficjalne wersje Apache wszystkie składniki HDInsight 4.0 są podane poniżej. Składniki wymienione są wersje najnowszej dostępnej wersji stabilne.

- Apache Ambari 2.7.1
- Apache Hadoop 3.1.1
- Apache HBase 2.0.0
- Apache Hive 3.1.0
- Apache Kafka 1.1.1
- Apache Mahout 0.9.0+
- Apache Oozie 4.2.0
- Apache Phoenix 4.7.0
- Apache Pig 0.16.0
- Struktury Apache Ranger 0.7.0
- Suwak Apache 0.92.0
- Apache Spark 2.3.1
- Apache Sqoop 1.4.7
- Apache TEZ 0.9.1
- Apache Zeppelin 0.8.0
- Apache ZooKeeper 3.4.6

Nowsze wersje składników Apache czasami są połączone w dystrybucji HDP oprócz wymienionych powyżej. W tym przypadku te nowsze wersje są wymienione w tabeli wersji zapoznawczych Technical Preview i nie należy zastąpić wersje składników Apache powyższej listy w środowisku produkcyjnym.

## <a name="apache-patch-information"></a>Informacje dotyczące poprawki Apache

Aby uzyskać więcej informacji na temat poprawek, dostępne w HDInsight w wersji 4.0 Zobacz poprawki dla każdego produktu w poniższej tabeli.

| Nazwa produktu | Informacje dotyczące poprawki |
|---|---|
| Ambari | [Informacje dotyczące poprawki systemu Ambari](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| Hadoop | [Informacje dotyczące poprawki usługi Hadoop](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| HBase | [Informacje dotyczące poprawki bazy danych HBase](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Hive  | Ta wersja zawiera 3.1.0 z nie dodatkowe poprawki Apache Hive.  |
| Kafka | Ta wersja zawiera 1.1.1 z nie dodatkowe poprawki Apache Kafka. |
| Oozie | [Informacje dotyczące poprawki programu Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [Informacje dotyczące poprawki Phoenix](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig | [Informacje dotyczące poprawki pig](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [Informacje dotyczące poprawki platformy ranger](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| platforma Spark | [Informacje dotyczące poprawki platformy Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | Ta wersja zawiera 1.4.7 z nie dodatkowe poprawki Apache Sqoop. |
| Tez | Ta wersja zawiera 0.9.1 z nie dodatkowe poprawki Apache Tez. |
| Zeppelin | Ta wersja oferuje rozwiązania Zeppelin 0.8.0 z nie dodatkowe poprawki Apache. |
| Dozorca | [Informacje dotyczące poprawki dozorcy](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>Naprawiono Common Vulnerabilities and Exposures

Aby uzyskać więcej informacji na temat zabezpieczeń problemy rozwiązane w tej wersji, zobacz zobowiązanie firmy Hortonworks [stały powszechnych luk w zabezpieczeniach i zagrożeń dla HDP 3.0.1](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html).

## <a name="known-issues"></a>Znane problemy

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>Replikacja nie działa dla Zabezpieczanie bazy danych HBase przy użyciu domyślnej instalacji

HDInsight 4.0 wykonaj następujące czynności:

1. Zezwolenie na komunikację między klastrami.
1. Zaloguj się do aktywnego węzła głównego.
1. Pobierz skrypt, aby włączyć replikację za pomocą następującego polecenia:

    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Wpisz polecenie `sudo kinit <domainuser>`.
1. Wpisz następujące polecenie, aby uruchomić skrypt:

    ```
    sudo bash hdi_enable_replication.sh -m <hn0> -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```
HDInsight 3.6 wykonaj następujące czynności:

1. Zaloguj się do aktywnego serwera HMaster ZK.
1. Pobierz skrypt, aby włączyć replikację za pomocą następującego polecenia:
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Wpisz polecenie `sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>`.
1. Wpisz następujące polecenie:

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>Phoenix Sqlline przestaje działać po przeprowadzeniu migracji klaster HBase HDInsight 4.0

Wykonaj następujące czynności:

1. Upuść w poniższych tabelach Phoenix:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. Jeśli nie można usunąć, tabele, należy ponownie uruchomić bazy danych HBase, aby wyczyścić wszystkie połączenia z tabelami.
1. Uruchom ponownie polecenie `sqlline.py`. Phoenix utworzy go ponownie wszystkie tabele, które zostały usunięte w kroku 1.
1. Ponowne generowanie Phoenix tabele i widoki danych HBase.

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>Phoenix Sqlline przestaje działać po replikacji bazy danych HBase Phoenix metadanych z HDInsight 3.6 4.0

Wykonaj następujące czynności:

1. Przed wykonaniem replikacji, przejdź do klastra docelowego 4.0 i wykonaj `sqlline.py`. To polecenie spowoduje wygenerowanie tabele Phoenix, takie jak `SYSTEM.MUTEX` i `SYSTEM.LOG` występujące w wersji 4.0.
1. Usunięcie następujących tabel:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. Rozpoczyna się replikacja bazy danych HBase

## <a name="deprecation"></a>Przestarzałe

Apache Storm i uczenia Maszynowego usługi nie są dostępne w HDInsight w wersji 4.0.
