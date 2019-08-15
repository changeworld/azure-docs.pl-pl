---
title: Informacje o wersji usługi Azure HDInsight
description: Najnowsze informacje o wersji usługi Azure HDInsight. Uzyskaj porady deweloperskie i szczegóły dotyczące usługi Hadoop, Spark, R Server, Hive i innych.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 7c31520efd881e8e0b5ed309f62d273bac59c0e3
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945026"
---
# <a name="release-notes"></a>Informacje o wersji

Ten artykuł zawiera informacje o najnowszych aktualizacjach wersji usługi Azure HDInsight. Aby uzyskać informacje dotyczące wcześniejszych wersji, zobacz [archiwum informacji o wersji usługi HDInsight](hdinsight-release-notes-archive.md).

> [!IMPORTANT]  
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz artykuł dotyczący obsługi [wersji usługi HDInsight](hdinsight-component-versioning.md).

## <a name="summary"></a>Podsumowanie

Azure HDInsight jest jedną z najpopularniejszych usług używanych przez klientów korporacyjnych na potrzeby analizy w narzędziach typu open source Apache Hadoop i Apache Spark na platformie Azure.

## <a name="new-features"></a>Nowe funkcje

Aby uzyskać więcej informacji na temat ważnych zmian w usłudze HDInsight 4,0. zobacz [co nowego w HDI 4,0?](../hdinsight/hdinsight-version-release.md).

## <a name="component-versions"></a>Wersje składników

Poniżej wymieniono oficjalne wersje oprogramowania Apache wszystkich składników usługi HDInsight 4,0. Wymienione składniki są wersjami najnowszych dostępnych wersji.

- Apache Ambari 2.7.1
- Apache Hadoop 3.1.1
- Apache HBase 2.0.0
- Apache Hive 3.1.0
- Apache Kafka 1.1.1, 2.1.0
- Apache Mahout 0.9.0 +
- Apache Oozie 4.2.0
- Apache Phoenix 4.7.0
- Apache wieprz 0.16.0
- Apache Ranger 0.7.0
- Suwak Apache 0.92.0
- Apache Spark 2.3.1, 2.4.0
- Apache Sqoop 1.4.7
- Apache TEZ od 0.9.1
- Apache Zeppelin 0.8.0
- Apache ZooKeeper 3.4.6

Nowsze wersje składników Apache są czasami powiązane z dystrybucją HDP oraz z wymienionymi powyżej wersjami. W takim przypadku te nowsze wersje są wymienione w tabeli wersji zapoznawczych Technical Preview i nie powinny podstawić na wersje składnika Apache powyższej listy w środowisku produkcyjnym.

## <a name="apache-patch-information"></a>Informacje o poprawkach Apache

Aby uzyskać więcej informacji na temat poprawek dostępnych w usłudze HDInsight 4,0, zapoznaj się z listą poprawek dla każdego produktu w poniższej tabeli.

| Nazwa produktu | Informacje o poprawkach |
|---|---|
| Ambari | [Informacje o poprawkach Ambari](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| Hadoop | [Informacje o poprawkach usługi Hadoop](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| HBase | [Informacje o poprawkach HBase](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Hive  | Ta wersja zawiera 3.1.0 Hive bez dodatkowych poprawek Apache.  |
| Kafka | Ta wersja zawiera Kafka 1.1.1 bez dodatkowych poprawek Apache. |
| Oozie | [Informacje o poprawkach Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [Informacje o poprawkach w Phoenix](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig | [Informacje o poprawkach świń](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [Informacje o poprawkach Ranger](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| platforma Spark | [Informacje o poprawkach platformy Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | Ta wersja udostępnia Sqoop 1.4.7 bez dodatkowych poprawek Apache. |
| Tez | Ta wersja udostępnia tez od 0.9.1 bez dodatkowych poprawek Apache. |
| Zeppelin | Ta wersja udostępnia Zeppelin 0.8.0 bez dodatkowych poprawek Apache. |
| ZooKeeper | [Informacje o poprawkach dozorcy](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>Rozwiązano typowe luki w zabezpieczeniach i zagrożenia

Aby uzyskać więcej informacji na temat problemów z zabezpieczeniami rozwiązanych w tej wersji, zobacz Hortonworks " [stałe typowe luki w zabezpieczeniach i zagrożenia dla HDP 3.0.1](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html).

## <a name="known-issues"></a>Znane problemy

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>Replikacja jest uszkodzona dla bezpiecznego HBase z instalacją domyślną

W przypadku usługi HDInsight 4,0 wykonaj następujące czynności:

1. Włącz komunikację między klastrami.
1. Zaloguj się do usługi Active węzła głównego.
1. Pobierz skrypt, aby włączyć replikację za pomocą następującego polecenia:

    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Wpisz polecenie `sudo kinit <domainuser>`.
1. Wpisz następujące polecenie, aby uruchomić skrypt:

    ```
    sudo bash hdi_enable_replication.sh -m <hn0> -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```
W przypadku usługi HDInsight 3,6 wykonaj następujące czynności:

1. Zaloguj się do usługi Active serwera hmaster ZK.
1. Pobierz skrypt, aby włączyć replikację za pomocą następującego polecenia:
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Wpisz polecenie `sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>`.
1. Wpisz następujące polecenie:

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>Podczas migrowania klastra HBase do usługi HDInsight 4,0 w Phoenix nie działa zakończenie SqlLine

Wykonaj następujące czynności:

1. Porzuć następujące tabele w Phoenix:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. Jeśli nie możesz usunąć żadnej z tabel, uruchom ponownie program HBase, aby wyczyścić wszystkie połączenia z tabelami.
1. Uruchom ponownie polecenie `sqlline.py`. Phoenix utworzy ponownie wszystkie tabele, które zostały usunięte w kroku 1.
1. Wygeneruj ponownie tabele i widoki usługi Phoenix dla danych HBase.

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>Podczas replikowania metadanych HBase Phoenix z usługi HDInsight 3,6 do 4,0

Wykonaj następujące czynności:

1. Przed przeprowadzeniem replikacji przejdź do docelowego klastra 4,0 i wykonaj `sqlline.py`polecenie. To polecenie spowoduje wygenerowanie tabel Phoenix `SYSTEM.MUTEX` , `SYSTEM.LOG` takich jak i, które znajdują się tylko w 4,0.
1. Porzuć następujące tabele:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. Uruchom replikację HBase

## <a name="deprecation"></a>Przestarzałe

Usługi Apache Storm i l nie są dostępne w usłudze HDInsight 4,0.
