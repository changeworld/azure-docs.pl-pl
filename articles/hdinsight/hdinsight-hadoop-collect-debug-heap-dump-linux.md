---
title: Włącz zrzuty stosu dla usługi Hadoop w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Włącz zrzuty stosu dla usług Hadoop z klastrów usługi HDInsight opartych na systemie Linux debugowania i analizy.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 8f151adb-f687-41e4-aca0-82b551953725
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: cd906736f2642d764c2b72a0572f63d675613c81
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31405127"
---
# <a name="enable-heap-dumps-for-hadoop-services-on-linux-based-hdinsight"></a>Włącz zrzuty stosu dla usługi Hadoop w HDInsight opartych na systemie Linux

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Zrzuty stosu zawierającego migawkę pamięci aplikacji, w tym wartości zmiennych w czasie tworzenia zrzutu. Tak, aby były przydatne do diagnozowania problemów występujących w czasie wykonywania.

> [!IMPORTANT]
> Kroki opisane w tym dokumencie pracować tylko z klastrami usługi HDInsight, które używają systemu Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a name="whichServices"></a>Usługi

Można włączyć zrzuty stosu dla następujących usług:

* **hcatalog** -tempelton
* **gałąź** -serwera hiveserver2, potrzeby magazynu metadanych, derbyserver
* **mapreduce** -jobhistoryserver
* **yarn** -resourcemanager nodemanager, timelineserver
* **System plików hdfs** -datanode secondarynamenode, namenode

Można także włączyć zrzuty stosu mapy i zmniejszyć procesów uruchomionych przez usługi HDInsight.

## <a name="configuration"></a>Opis sterty zrzutu konfiguracji

Zrzuty stosu są włączane przez przeniesienie opcji (czasami znana jako zdecyduje, lub parametrów) do maszyny JVM podczas uruchamiania usługi. Dla większości usług Hadoop można zmodyfikować skrypt powłoki, używane do uruchamiania usługi do przekazania tych opcji.

Każdy skrypt ma Eksport  **\* \_OPTS**, który zawiera opcje przekazane do maszyny wirtualnej Java. Na przykład w **hadoop env.sh** skryptu wiersza, który rozpoczyna się od `export HADOOP_NAMENODE_OPTS=` zawiera opcje usługi NameNode.

Mapowanie i zmniejszenie procesy są nieco inne operacje te są Proces podrzędny usługi MapReduce. Każdy mapy lub Zmniejsz proces jest uruchomiony w kontenerze, a dwa wpisy zawierające opcje maszyny wirtualnej Java. Zarówno zawarte w **mapred-site.xml**:

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [!NOTE]
> Firma Microsoft zaleca, aby zmodyfikować skrypty i ustawienia mapred-site.xml jako dojście Ambari replikowanie zmian w węzłach w klastrze za pomocą narzędzia Ambari. Zobacz [przy użyciu Ambari](#using-ambari) sekcji, aby poznać konkretne kroki.

### <a name="enable-heap-dumps"></a>Włączanie zrzutów stosu

Poniższa opcja umożliwia zrzuty stosu po wystąpieniu OutOfMemoryError:

    -XX:+HeapDumpOnOutOfMemoryError

**+** Wskazuje, że ta opcja jest włączona. Domyślne ustawienie to Wyłączony.

> [!WARNING]
> Zrzuty stosu nie są włączone dla usługi Hadoop w usłudze HDInsight domyślnie jako pliki zrzutu mogą być duże. Jeśli włączysz ich do rozwiązywania problemów, pamiętaj, aby je wyłączyć, po problemu i zebranych plików zrzutu.

### <a name="dump-location"></a>Lokalizacja zrzutu

Domyślna lokalizacja pliku zrzutu jest bieżący katalog roboczy. Można kontrolować, w przypadku, gdy plik jest przechowywany przy użyciu następujących opcji:

    -XX:HeapDumpPath=/path

Na przykład za pomocą `-XX:HeapDumpPath=/tmp` powoduje, że zrzuty mają być przechowywane w TMP.

### <a name="scripts"></a>Scripts

Można również uruchomić skrypt po **OutOfMemoryError** występuje. Na przykład wyzwalania powiadomienie, aby wiedzieć, że wystąpił błąd. Użyj opcji, aby wyzwolić skrypt na __OutOfMemoryError__:

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]
> Ponieważ Hadoop to Rozproszony system, dowolny skrypt używany muszą znajdować się na wszystkich węzłach w klastrze, na którym usługa jest uruchamiana na.
> 
> Skrypt musi również być w lokalizacji, która jest dostępny dla konta usługi działa jako i podać uprawnienia do wykonywania. Na przykład, warto przechowywać skryptów w `/usr/local/bin` i użyj `chmod go+rx /usr/local/bin/filename.sh` Aby udzielić odczytu i uprawnienia do wykonywania.

## <a name="using-ambari"></a>Za pomocą narzędzia Ambari

Aby zmodyfikować konfigurację usługi, użyj następujące czynności:

1. Otwórz Ambari web UI dla klastra. Adres URL jest https://YOURCLUSTERNAME.azurehdinsight.net.

    Po wyświetleniu monitu uwierzytelniania do witryny przy użyciu nazwy konta HTTP (domyślne: admin) i hasło dla klastra.

   > [!NOTE]
   > Może pojawić się prośba po raz drugi przez Ambari dla nazwy użytkownika i hasła. Jeśli tak, wprowadź nazwę konta i hasło

2. Przy użyciu listy po lewej stronie, wybierz obszar usługi, który chcesz zmodyfikować. Na przykład **HDFS**. W obszarze Centrum wybierz **Configs** kartę.

    ![Obraz z wybraną kartą Configs systemu plików HDFS w sieci Web Ambari](./media/hdinsight-hadoop-heap-dump-linux/serviceconfig.png)

3. Przy użyciu **filtru...**  wpisu, wprowadź **zdecyduje**. Tylko elementy zawierające ten tekst są wyświetlane.

    ![Filtrowana lista](./media/hdinsight-hadoop-heap-dump-linux/filter.png)

4. Znajdź  **\* \_OPTS** wpisu dla usługi chcesz włączyć zrzuty stosu dla, a następnie dodaj odpowiednie opcje, które mają zostać włączone. Na poniższej ilustracji, zostały dodane `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` do **HADOOP\_NAMENODE\_OPTS** wpis:

    ![HADOOP_NAMENODE_OPTS z - XX: + HeapDumpOnOutOfMemoryError - XX: HeapDumpPath = / tmp /](./media/hdinsight-hadoop-heap-dump-linux/opts.png)

   > [!NOTE]
   > Podczas włączania sterty zrzuty mapy lub Zmniejsz procesu podrzędnego poszukać w polach o nazwie **mapreduce.admin.map.child.java.opts** i **mapreduce.admin.reduce.child.java.opts**.

    Użyj **zapisać** przycisk, aby zapisać zmiany. Można podać krótkiej uwagi opisujące zmiany.

5. Po zastosowaniu zmiany **wymagane jest ponowne uruchomienie** pojawi się ikona obok co najmniej jedna usługa.

    ![ponowne uruchomienie ikonę wymagane i uruchom ponownie przycisku](./media/hdinsight-hadoop-heap-dump-linux/restartrequiredicon.png)

6. Wybierz każda usługa, która wymaga ponownego uruchomienia i użyj **akcji usługi** przycisk, aby **włączyć w trybie konserwacji**. Tryb konserwacji zapobiega alerty generowane z usługi po ponownym jej uruchomieniu.

    ![Włącz menu Tryb konserwacji](./media/hdinsight-hadoop-heap-dump-linux/maintenancemode.png)

7. Po włączeniu trybu konserwacji, użyj **Uruchom ponownie** przycisk usługi **Uruchom ponownie wszystkie dokonane**

    ![Uruchom ponownie wszystkie dotyczy wpisu](./media/hdinsight-hadoop-heap-dump-linux/restartbutton.png)

   > [!NOTE]
   > wpisy **ponowne uruchomienie** przycisk mogą być różne dla innych usług.

8. Po uruchomieniu usługi, użyj **akcji usługi** przycisk, aby **włączyć Wyłącz tryb konserwacji**. Tego narzędzia Ambari można wznowić monitorowania alertów dla usługi.

