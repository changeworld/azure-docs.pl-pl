---
title: Włącz zrzuty sterty dla usług Apache Hadoop w usłudze HDInsight — Azure
description: Włącz zrzuty sterty dla usług Apache Hadoop z klastrów HDInsight opartych na systemie Linux do debugowania i analizy.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 9134eb6922b0ed37bbe6051b138da2c7c082b175
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75658801"
---
# <a name="enable-heap-dumps-for-apache-hadoop-services-on-linux-based-hdinsight"></a>Włącz zrzuty sterty dla usług Apache Hadoop w systemie HDInsight opartym na systemie Linux

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Zrzuty sterty zawierają migawkę pamięci aplikacji, w tym wartości zmiennych w momencie utworzenia zrzutu. Są więc przydatne do diagnozowania problemów, które występują w czasie wykonywania.

## <a name="services"></a>Usługi

Można włączyć zrzuty sterty dla następujących usług:

* **Apache hcatalog** - tempelton
* **Ul Apache** - hiveserver2, metastore, derbyserver
* **mapreduce** - jobhistoryserver
* **Przędza Apache** - resourcemanager, nodemanager, timelineserver
* **Apache hdfs** - datanode, secondarynamenode, namenode

Można również włączyć zrzuty sterty dla mapy i zmniejszyć procesy uruchomiona przez HDInsight.

## <a name="understanding-heap-dump-configuration"></a>Opis konfiguracji zrzutu sterty

Zrzuty sterty są włączane przez przekazywanie opcji (czasami nazywanych opts lub parametrami) do usługi JVM po uruchomieniu usługi. W przypadku większości usług [Apache Hadoop](https://hadoop.apache.org/) można zmodyfikować skrypt powłoki używany do uruchomienia usługi w celu przekazania tych opcji.

W każdym skrypcie jest eksport dla ** \* \_OPTS**, który zawiera opcje przekazywane do JVM. Na przykład w skrypcie **hadoop-env.sh** wiersz, `export HADOOP_NAMENODE_OPTS=` który zaczyna się od zawiera opcje dla usługi NameNode.

Map i zmniejszyć procesy są nieco inne, ponieważ te operacje są procesem podrzędnym Usługi MapReduce. Każda mapa lub zmniejszyć przebiegi procesu w kontenerze podrzędnym i istnieją dwa wpisy, które zawierają opcje JVM. Oba zawarte w **mapred-site.xml:**

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [!NOTE]  
> Zalecamy użycie [Apache Ambari](https://ambari.apache.org/) do modyfikowania zarówno skryptów, jak i ustawień mapred-site.xml, ponieważ Ambari obsługuje replikowanie zmian między węzłami w klastrze. Zobacz [korzystanie z Apache Ambari](#using-apache-ambari) sekcji dla określonych kroków.

### <a name="enable-heap-dumps"></a>Włączanie zrzutów stosu

Następująca opcja włącza zrzuty sterty, gdy outOfMemoryError występuje:

    -XX:+HeapDumpOnOutOfMemoryError

Wskazuje, **+** że ta opcja jest włączona. Domyślne ustawienie to Wyłączony.

> [!WARNING]  
> Zrzuty sterty nie są domyślnie włączone dla usług Hadoop w programie HDInsight, ponieważ pliki zrzutu mogą być duże. Jeśli włączysz je do rozwiązywania problemów, pamiętaj, aby wyłączyć je po odtworzeniu problemu i zebraniu plików zrzutu.

### <a name="dump-location"></a>Lokalizacja zrzutu

Domyślną lokalizacją pliku zrzutu jest bieżący katalog roboczy. Miejsce przechowywania pliku można kontrolować za pomocą następującej opcji:

    -XX:HeapDumpPath=/path

Na przykład `-XX:HeapDumpPath=/tmp` za pomocą powoduje, że zrzuty mają być przechowywane w katalogu /tmp.

### <a name="scripts"></a>Scripts

Można również wyzwolić skrypt, gdy **outofMemoryError** występuje. Na przykład wyzwalanie powiadomienia, aby wiedzieć, że wystąpił błąd. Użyj następującej opcji, aby wyzwolić skrypt na __OutOfMemoryError:__

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]  
> Ponieważ Apache Hadoop jest systemem rozproszonym, każdy skrypt używany musi być umieszczony we wszystkich węzłach w klastrze, na których działa usługa.
> 
> Skrypt musi również znajdować się w lokalizacji, która jest dostępna dla konta, na którym działa usługa, i musi zapewniać uprawnienia do wykonywania. Na przykład można przechowywać skrypty `/usr/local/bin` i `chmod go+rx /usr/local/bin/filename.sh` używać do udzielania uprawnień do odczytu i wykonywania.

## <a name="using-apache-ambari"></a>Korzystanie z Apache Ambari

Aby zmodyfikować konfigurację usługi, należy wykonać następujące czynności:

1. W przeglądarce internetowej `https://CLUSTERNAME.azurehdinsight.net`przejdź `CLUSTERNAME` do miejsca , gdzie jest nazwa klastra.

2. Korzystając z listy po lewej stronie, wybierz obszar serwisu, który chcesz zmodyfikować. Na **przykład, HDFS**. W obszarze środkowym wybierz kartę **Konfiguracje.**

    ![Obraz sieci Web Ambari z wybraną kartą Konfiguracje HDFS](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-service-config-tab.png)

3. Za pomocą wpisu **Filtr...** wprowadź **opts**. Wyświetlane są tylko elementy zawierające ten tekst.

    ![Apache Ambari config filtrowana lista](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdinsight-filter-list.png)

4. Znajdź wpis ** \_OPTS dla usługi, dla której chcesz włączyć zrzuty sterty, i dodaj opcje, które chcesz włączyć. \*** Na poniższej ilustracji dodałem `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` do wpisu **HADOOP\_NAMENODE\_OPTS:**

    ![Apache Ambari hadoop-namenode-opts](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hadoop-namenode-opts.png)

   > [!NOTE]  
   > Podczas włączania zrzutów sterty dla mapy lub zmniejszania procesu podrzędnego poszukaj pól o nazwie **mapreduce.admin.map.child.java.opts** i **mapreduce.admin.reduce.child.java.opts**.

    Użyj przycisku **Zapisz,** aby zapisać zmiany. Można wprowadzić krótką notatkę opisującą zmiany.

5. Po zastosowaniu zmian obok jednej lub więcej usług pojawi się ikona **Wymagane ponowne uruchomienie.**

    ![ikona wymaganego ponownego uruchomienia i przycisk ponownego uruchamiania](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/restart-required-icon.png)

6. Wybierz każdą usługę, która wymaga ponownego uruchomienia, i użyj przycisku **Akcje usługi,** aby **włączyć tryb konserwacji**. Tryb konserwacji zapobiega generowaniu alertów z usługi po ponownym uruchomieniu.

    ![Włącz menu trybu konserwacji hdi](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-maintenance-mode.png)

7. Po włączeniu trybu konserwacji użyj przycisku **Uruchom ponownie** dla usługi, aby ponownie **uruchomić wszystkie**

    ![Apache Ambari Uruchom ponownie wszystkie wpisy, których dotyczy problem](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-restart-all-button.png)

   > [!NOTE]  
   > Wpisy przycisku **Uruchom ponownie** mogą być różne dla innych usług.

8. Po ponownym uruchomieniu usług użyj przycisku **Akcje usługi,** aby **wyłączyć tryb konserwacji**. To Ambari wznowić monitorowanie alertów dla usługi.