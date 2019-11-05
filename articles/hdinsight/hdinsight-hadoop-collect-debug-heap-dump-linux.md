---
title: Włączanie zrzutów sterty dla usług Apache Hadoop w usłudze HDInsight — Azure
description: Włącz Zrzuty sterty dla usług Apache Hadoop z klastrów usługi HDInsight opartych na systemie Linux na potrzeby debugowania i analizy.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 90de0b4bfad4c5096ebc38eb3d31fc41bca6649b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494847"
---
# <a name="enable-heap-dumps-for-apache-hadoop-services-on-linux-based-hdinsight"></a>Włączanie zrzutów sterty dla usług Apache Hadoop w usłudze HDInsight opartej na systemie Linux

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Zrzuty sterty zawierają migawkę pamięci aplikacji, w tym wartości zmiennych w momencie utworzenia zrzutu. Są one przydatne do diagnozowania problemów występujących w czasie wykonywania.

## <a name="whichServices"></a>Services

Można włączyć Zrzuty sterty dla następujących usług:

* **Apache hcatalog** — tempelton
* **Apache Hive** — serwera hiveserver2, metadanych, derbyserver
* **MapReduce** — jobhistoryserver
* **Apache przędze** -ResourceManager, nodemanager, timelineserver
* **Apache HDFS** — datanode, secondarynamenode, namenode

Można również włączyć Zrzuty sterty dla mapy i ograniczyć procesy uruchamiane przez HDInsight.

## <a name="configuration"></a>Informacje o konfiguracji zrzutu sterty

Zrzuty sterty są włączane przez przekazanie opcji (czasami znanych jako opcje lub parametry) do JVM podczas uruchamiania usługi. W przypadku większości usług [Apache Hadoop](https://hadoop.apache.org/) można zmodyfikować skrypt powłoki używany do uruchomienia usługi, aby przekazać te opcje.

W każdym skrypcie istnieje eksport dla **\*\_** opcji, który zawiera opcje przesłane do JVM. Na przykład w skrypcie **Hadoop-ENV.sh** wiersz zaczynający się od `export HADOOP_NAMENODE_OPTS=` zawiera opcje dla usługi NameNode.

Procesy mapowania i zmniejszania są nieco inne, ponieważ te operacje są procesem podrzędnym usługi MapReduce. Każda mapa lub ograniczenie procesu są uruchamiane w kontenerze podrzędnym, a istnieją dwa wpisy, które zawierają opcje JVM. Oba zawarte w **mapred-site. XML**:

* **MapReduce. admin. map. Child. Java.**
* **MapReduce. admin. Zredukuj. Child. Java.**

> [!NOTE]  
> Zalecamy użycie platformy [Apache Ambari](https://ambari.apache.org/) w celu zmodyfikowania zarówno ustawień skryptów, jak i mapred-site. XML, jako obsługi Ambari Replikowanie zmian między węzłami w klastrze. Szczegółowe instrukcje znajdują się w sekcji Korzystanie z usługi [Apache Ambari](#using-apache-ambari) .

### <a name="enable-heap-dumps"></a>Włączanie zrzutów stosu

Następująca opcja włącza Zrzuty sterty, gdy występuje OutOfMemoryError:

    -XX:+HeapDumpOnOutOfMemoryError

**+** wskazuje, że ta opcja jest włączona. Domyślne ustawienie to Wyłączony.

> [!WARNING]  
> Zrzuty sterty nie są domyślnie włączone dla usług Hadoop w usłudze HDInsight, ponieważ pliki zrzutu mogą być duże. Jeśli włączysz je do rozwiązywania problemów, pamiętaj, aby je wyłączyć po ponownym utworzeniu problemu i zebraniu plików zrzutu.

### <a name="dump-location"></a>Lokalizacja zrzutu

Domyślna lokalizacja pliku zrzutu jest bieżącym katalogiem roboczym. Możesz kontrolować miejsce przechowywania pliku przy użyciu następującej opcji:

    -XX:HeapDumpPath=/path

Na przykład użycie `-XX:HeapDumpPath=/tmp` powoduje, że zrzuty mają być przechowywane w katalogu/tmp.

### <a name="scripts"></a>Scripts

Możesz również wyzwolić skrypt w przypadku wystąpienia **OutOfMemoryError** . Na przykład Wyzwól powiadomienie, aby wiedzieć, że wystąpił błąd. Użyj następującej opcji, aby wyzwolić skrypt na __OutOfMemoryError__:

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]  
> Ponieważ Apache Hadoop jest systemem rozproszonym, każdy używany skrypt musi być umieszczony na wszystkich węzłach w klastrze, na którym działa usługa.
> 
> Skrypt musi znajdować się również w lokalizacji dostępnej dla konta, na którym jest uruchomiona usługa, i musi zapewnić uprawnienia do wykonywania. Można na przykład przechowywać skrypty w `/usr/local/bin` i używać `chmod go+rx /usr/local/bin/filename.sh` do przyznawania uprawnień odczytu i wykonania.

## <a name="using-apache-ambari"></a>Korzystanie z usługi Apache Ambari

Aby zmodyfikować konfigurację usługi, wykonaj następujące czynności:

1. Otwórz interfejs użytkownika sieci Web Ambari dla klastra. Adres URL jest https://YOURCLUSTERNAME.azurehdinsight.net.

    Po wyświetleniu monitu należy uwierzytelnić się w lokacji przy użyciu nazwy konta HTTP (domyślnie: admin) i hasła dla klastra.

   > [!NOTE]  
   > Monit o podanie nazwy użytkownika i hasła może zostać wyświetlony po raz drugi. Jeśli tak, wprowadź nazwę konta i hasło.

2. Korzystając z listy po lewej stronie, wybierz obszar usługi, który chcesz zmodyfikować. Na przykład system plików **HDFS**. W środkowym obszarze **Wybierz kartę konfiguracje** .

    ![Obraz przedstawiający kartę Ambari Web with HDFS configs Selected](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-service-config-tab.png)

3. Korzystając z wpisu **Filter...** **, wpisz polecenie**. Wyświetlane są tylko elementy zawierające ten tekst.

    ![Lista przefiltrowanych konfiguracji oprogramowania Apache Ambari](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdinsight-filter-list.png)

4. Znajdź wpis **\*\_** opcje dla usługi, dla której chcesz włączyć Zrzuty sterty dla programu, i Dodaj opcje, które chcesz włączyć. Na poniższej ilustracji dodaliśmy `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` do\_usługi HADOOP, **NAMENODE\_** :

    ![Apache Ambari Hadoop-namenode-](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hadoop-namenode-opts.png)

   > [!NOTE]  
   > Podczas włączania zrzutów sterty dla mapy lub Zmniejsz proces podrzędny należy poszukać pól o nazwach **MapReduce. admin. map. Child. Java** .

    Użyj przycisku **Zapisz** , aby zapisać zmiany. Możesz wprowadzić krótką notatkę opisującą zmiany.

5. Po zastosowaniu zmian ikona **wymagane ponowne uruchomienie** zostanie wyświetlona obok jednej lub kilku usług.

    ![ikona wymagane ponowne uruchomienie i przycisk ponownego uruchomienia](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/restart-required-icon.png)

6. Wybierz każdą usługę, która wymaga ponownego uruchomienia, a następnie użyj przycisku **Akcje usługi** , aby **włączyć tryb konserwacji**. Tryb konserwacji uniemożliwia generowanie alertów z usługi po jej ponownym uruchomieniu.

    ![Włącz menu Tryb konserwacji HDI](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-maintenance-mode.png)

7. Po włączeniu trybu konserwacji Użyj przycisku **Uruchom ponownie** , aby **ponownie uruchomić** usługę.

    ![Apache Ambari uruchamia ponownie wszystkie uwzględnione wpisy](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-restart-all-button.png)

   > [!NOTE]  
   > Wpisy dla przycisku **Uruchom ponownie** mogą się różnić w zależności od innych usług.

8. Po ponownym uruchomieniu usług użyj przycisku **Akcje usługi** , aby wyłączyć **tryb konserwacji**. Ambari to wznowienie monitorowania alertów dla usługi.

