---
title: Włączanie zrzutów sterty dla usługi Apache Hadoop w HDInsight — Azure
description: Włączanie zrzutów sterty dla usługi Apache Hadoop z klastrów HDInsight opartych na systemie Linux do debugowania i analizy.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: a1b816656e019a214e8c0dc72b79575c49d99e68
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098358"
---
# <a name="enable-heap-dumps-for-apache-hadoop-services-on-linux-based-hdinsight"></a>Włączanie zrzutów sterty dla usługi Apache Hadoop w HDInsight opartych na systemie Linux

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Zrzuty sterty zawierają migawkę pamięci aplikacji, w tym wartości zmiennych w czasie tworzenia zrzutu. Tak, aby były przydatne do diagnozowania problemów, które występują w czasie wykonywania.

> [!IMPORTANT]  
> Kroki opisane w tym dokumencie pracować tylko z klastrami HDInsight, które używają systemu Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a name="whichServices"></a>Usługi

Można włączyć zrzutów sterty dla następujących usług:

* **Apache hcatalog** -tempelton
* **Apache hive** — serwera hiveserver2, Magazyn metadanych, derbyserver
* **mapreduce** -jobhistoryserver
* **Apache yarn** -resourcemanager nodemanager, timelineserver
* **System plików hdfs Apache** -datanode secondarynamenode, namenode

Możesz również włączanie zrzutów sterty dla mapy i zmniejszyć procesów uruchomionych przez HDInsight.

## <a name="configuration"></a>Konfigurowanie zrzutu sterty opis

Zrzuty sterty jest włączane przez przeniesienie opcji (czasami określane jako zdecyduje, lub parametrów) do maszyny JVM podczas uruchamiania usługi. Dla większości [Apache Hadoop](https://hadoop.apache.org/) usług, możesz zmodyfikować skrypt powłoki, używane do uruchamiania usługi do przekazania tych opcji.

W każdy skrypt ma Eksport  **\* \_OPTS**, który zawiera opcje przekazywane do maszyny wirtualnej Java. Na przykład w **hadoop env.sh** skryptu wiersza, który rozpoczyna się od `export HADOOP_NAMENODE_OPTS=` zawiera opcje usługi NameNode.

Mapowania i redukcji procesy nieco się różnią, jak te operacje są Proces podrzędny usługi MapReduce. Każdy mapowania lub Zmniejsz proces jest uruchamiany w kontenerze, wiąże się z dwóch wpisów, które zawierają opcje maszyny JVM. Zarówno zawarte w **mapred-site.xml**:

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [!NOTE]  
> Firma Microsoft zaleca używanie [Apache Ambari](https://ambari.apache.org/) do modyfikowania skryptów i ustawienia mapred-site.xml, jako Ambari obsługi replikacji zmian na węzłach w klastrze. Zobacz [przy użyciu Apache Ambari](#using-apache-ambari) sekcji, aby poznać konkretne kroki.

### <a name="enable-heap-dumps"></a>Włączanie zrzutów stosu

Poniższa opcja umożliwia zrzutów stosu sytuacji OutOfMemoryError:

    -XX:+HeapDumpOnOutOfMemoryError

**+** Wskazuje, że ta opcja jest włączona. Domyślne ustawienie to Wyłączony.

> [!WARNING]  
> Zrzuty sterty nie są włączone dla usługi Hadoop w HDInsight domyślnie jako pliki zrzutu mogą być duże. Po włączeniu ich do rozwiązywania problemów, pamiętaj, aby je wyłączyć, po problemu i zebrane pliki zrzutu.

### <a name="dump-location"></a>Lokalizacja zrzutu

Domyślna lokalizacja pliku zrzutu jest bieżący katalog roboczy. Możesz kontrolować, gdzie plik jest przechowywany przy użyciu następujących opcji:

    -XX:HeapDumpPath=/path

Na przykład za pomocą `-XX:HeapDumpPath=/tmp` powoduje, że zrzuty mają być przechowywane w folderze/tmp katalogu.

### <a name="scripts"></a>Scripts

Skrypt można również wyzwalać przy **OutOfMemoryError** występuje. Na przykład wyzwala powiadomienia, aby było wiadomo, że wystąpił błąd. Użyj opcji uruchomienie wyzwalacza skrypt __OutOfMemoryError__:

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]  
> Ponieważ Apache Hadoop to Rozproszony system, dowolny skrypt używany muszą znajdować się we wszystkich węzłach w klastrze, w którym ta usługa działa na.
> 
> Skrypt musi także być w lokalizacji, który jest dostępny dla konta, działa jako usługa i musisz podać uprawnienia do wykonywania. Na przykład możesz chcieć przechowywać skrypty w `/usr/local/bin` i użyj `chmod go+rx /usr/local/bin/filename.sh` umożliwia udzielenie odczytu i uprawnienia do wykonywania.

## <a name="using-apache-ambari"></a>Przy użyciu narzędzia Apache Ambari

Aby zmodyfikować konfigurację dla usługi, użyj następujących kroków:

1. Otwórz interfejs webowy Ambari dla klastra. Adres URL jest https://YOURCLUSTERNAME.azurehdinsight.net.

    Po wyświetleniu monitu uwierzytelniania do witryny przy użyciu nazwy konta HTTP (domyślne: admin) i hasło dla klastra.

   > [!NOTE]  
   > Może pojawić się prośba drugi raz przez Ambari podanie nazwy użytkownika i hasło. Jeśli tak, wprowadź taką samą nazwę konta i hasło.

2. Przy użyciu listy po lewej stronie, wybierz obszar usługi, który chcesz zmodyfikować. Na przykład **HDFS**. W obszarze Centrum, wybierz **Configs** kartę.

    ![Obraz sieci web Ambari, z wybraną kartą konfiguracje systemu plików HDFS](./media/hdinsight-hadoop-heap-dump-linux/serviceconfig.png)

3. Za pomocą **filtr...**  wpisu, wprowadź **zdecyduje**. Wyświetlane są tylko elementy zawierające tekst.

    ![Filtrowana lista](./media/hdinsight-hadoop-heap-dump-linux/filter.png)

4. Znajdź  **\* \_OPTS** wpisu dla usługi, którą chcesz Włączanie zrzutów sterty dla, a następnie dodania tych opcji, które ma zostać włączone. Na poniższej ilustracji zostały dodane `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` do **HADOOP\_NAMENODE\_OPTS** wpis:

    ![HADOOP_NAMENODE_OPTS przy użyciu - XX: + HeapDumpOnOutOfMemoryError - XX: HeapDumpPath = / tmp /](./media/hdinsight-hadoop-heap-dump-linux/opts.png)

   > [!NOTE]  
   > Podczas włączania sterty zrzuty mapy lub Zmniejsz proces podrzędny, poszukaj dla pola o nazwie **mapreduce.admin.map.child.java.opts** i **mapreduce.admin.reduce.child.java.opts**.

    Użyj **Zapisz** przycisk, aby zapisać zmiany. Można wprowadzić krótkie notatki opisujący zmiany.

5. Po zastosowaniu zmiany **wymagane jest ponowne uruchomienie** ikona pojawia się obok co najmniej jednej usługi.

    ![Uruchom ponownie ikonę wymagane i ponownie uruchomić przycisku](./media/hdinsight-hadoop-heap-dump-linux/restartrequiredicon.png)

6. Wybierz każda usługa, która wymaga ponownego uruchomienia komputera, a następnie użyć **działania usługi** przycisk, aby **włączyć w trybie konserwacji**. Tryb konserwacji zapobiega alerty generowane z usługi po ponownym jej uruchomieniu.

    ![Włącz menu Tryb konserwacji](./media/hdinsight-hadoop-heap-dump-linux/maintenancemode.png)

7. Po włączeniu trybu konserwacji, użyj **ponowne uruchomienie** przycisk usługi **ponowne uruchomienie wszystkich będzie to miało wpływu**

    ![Uruchom ponownie wpływ na wszystkie wejścia](./media/hdinsight-hadoop-heap-dump-linux/restartbutton.png)

   > [!NOTE]  
   > wpisy dla **ponowne uruchomienie** przycisk mogą być różne dla innych usług.

8. Po uruchomieniu usługi użyj **działania usługi** przycisk, aby **Włącz Wyłącz tryb konserwacji**. Tego Ambari w celu wznowienia monitorowania alertów dla usługi.

