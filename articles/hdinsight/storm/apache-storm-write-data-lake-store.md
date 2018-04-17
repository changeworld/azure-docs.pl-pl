---
title: Apache Storm zapisu do przechowywania/Data Lake Store - Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zapisywanie zgodnego systemem plików HDFS magazynu dla usługi HDInsight przy użyciu platformy Apache Storm. Azure magazynu lub usługi Azure Data Lake Store zapewnienia magazynu dla systemu plików HDFS comptabile dla usługi HDInsight. Ten dokument i skojarzone przykład pokazują, jak składnika HdfsBolt może służyć do zapisu w magazynie domyślne platformy Storm w klastrze usługi HDInsight.
services: hdinsight
documentationcenter: na
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 1df98653-a6c8-4662-a8c6-5d288fc4f3a6
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 0c870b0c8de648ac65bec6857bf850c2913e7aeb
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="write-to-hdfs-from-apache-storm-on-hdinsight"></a>Zapis do systemu plików HDFS z systemu Apache Storm w usłudze HDInsight

Dowiedz się, jak używać systemu Storm do zapisu danych do magazynu zgodny z systemem plików HDFS używane przez Apache Storm w usłudze HDInsight. HDInsight można użyć zarówno usługi Azure Storage i Azure Data Lake przechowywane jako magazyn zgodnego systemem plików HDFS. STORM udostępnia [HdfsBolt](http://storm.apache.org/releases/1.1.0/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) składnik, który zapisuje dane do systemu plików HDFS. Ten dokument zawiera informacje o pisaniu do dowolnego typu magazynu z HdfsBolt. 

> [!IMPORTANT]
> Przykładową topologię używane w tym dokumencie polega na składnikach, które są dołączone do systemu Storm w usłudze HDInsight. Może wymagać modyfikacji do pracy z usługi Azure Data Lake Store w przypadku użycia z innych klastrów platformy Apache Storm.

## <a name="get-the-code"></a>Uzyskiwanie kodu

Projekt zawierający Ta topologia jest dostępny do pobrania z [ https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

Aby skompilować ten projekt, należy następującej konfiguracji dla swojego środowiska programowania:

* [Java JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) lub nowszej. HDInsight w wersji 3.5 lub nowszej wymagają Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

Po zainstalowaniu na deweloperskiej stacji roboczej Java i zestaw JDK, który można konfigurować następujące zmienne środowiskowe. Jednak należy sprawdzić, czy istnieją i że zawierają one poprawne wartości dla systemu.

* `JAVA_HOME` -powinny wskazywać na katalog, w którym jest zainstalowany zestaw JDK.
* `PATH` — powinna zawierać następujących ścieżkach:
  
    * `JAVA_HOME` (lub równoważne ścieżki).
    * `JAVA_HOME\bin` (lub równoważne ścieżki).
    * Katalog, w którym zainstalowano Maven.

## <a name="how-to-use-the-hdfsbolt-with-hdinsight"></a>Jak używać HdfsBolt z usługą HDInsight

> [!IMPORTANT]
> Przed użyciem HdfsBolt z systemu Storm w usłudze HDInsight, musisz najpierw użyć akcji skryptu można skopiować pliki jar wymagane do `extpath` systemu STORM. Aby uzyskać więcej informacji, zobacz [skonfigurować klaster](#configure) sekcji.

HdfsBolt wykorzystuje schemat pliku dostarczające zrozumienie, jak można zapisać w systemie plików HDFS. Z usługą HDInsight użyj jednej z następujących systemów:

* `wasb://`: Używany przy użyciu konta usługi Azure Storage.
* `adl://`: Używane z usługi Azure Data Lake Store.

Poniższa tabela zawiera przykłady użycia systemu plików dla różnych scenariuszy:

| Schemat | Uwagi |
| ----- | ----- |
| `wasb:///` | Domyślne konto magazynu jest kontenera obiektów blob na koncie magazynu Azure |
| `adl:///` | Domyślne konto magazynu jest katalogiem w usłudze Azure Data Lake Store. Podczas tworzenia klastra określ katalog w Data Lake Store, który jest elementem głównym klastra systemu plików HDFS. Na przykład `/clusters/myclustername/` katalogu. |
| `wasb://CONTAINER@ACCOUNT.blob.core.windows.net/` | Konto magazynu platformy Azure (dodatkowe) innych niż domyślne skojarzone z klastrem. |
| `adl://STORENAME/` | Katalog główny usługi Data Lake Store używane przez klaster. Ten program umożliwia dostęp do danych znajdujących się poza katalog, który zawiera klastra systemu plików. |

Aby uzyskać więcej informacji, zobacz [HdfsBolt](http://storm.apache.org/releases/1.1.0/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) odwołania w serwisie Apache.org.

### <a name="example-configuration"></a>Przykładowa konfiguracja

Następujące yaml programu znajduje się fragment `resources/writetohdfs.yaml` plików zawarte w przykładzie. Ten plik definiuje przy użyciu topologii Storm [strumień](https://storm.apache.org/releases/1.1.0/flux.html) framework Apache STORM.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
```

Ta yaml programu definiuje następujące elementy:

* `syncPolicy`: Określa, kiedy pliki są zsynchronizowane/opróżnione do systemu plików. W tym przykładzie każdy krotek 1000.
* `fileNameFormat`: Definiuje wzorzec nazwa i ścieżka pliku do użycia podczas zapisywania plików. W tym przykładzie ścieżka znajduje się w środowisku uruchomieniowym przy użyciu filtru i rozszerzenie pliku jest `.txt`.
* `recordFormat`: Definiuje wewnętrzny format plików zapisywane. W tym przykładzie pola są rozdzielone `|` znaków.
* `rotationPolicy`: Określa, kiedy do obracania plików. W tym przykładzie jest wykonywane bez obrotu.
* `hdfs-bolt`: Używa poprzednie składniki jako parametry konfiguracji `HdfsBolt` klasy.

Aby uzyskać więcej informacji w ramach strumienia, zobacz [ https://storm.apache.org/releases/1.1.0/flux.html ](https://storm.apache.org/releases/1.1.0/flux.html).

## <a name="configure-the-cluster"></a>Konfigurowanie klastra

Domyślnie Storm w usłudze HDInsight nie zawiera składniki używane do komunikacji z magazynu Azure lub usługi Data Lake Store w ścieżce Storm w HdfsBolt. Użyj następujących akcji skryptu można dodać te składniki `extlib` katalogu systemu STORM w klastrze:

* Identyfikator URI skryptu: `https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`
* Węzły do zastosowania do: Nimbus, opiekuna
* Parametry: Brak

Aby uzyskać informacji na temat używania tego skryptu z klastrem, zobacz [HDInsight dostosować klastry za pomocą akcji skryptu](./../hdinsight-hadoop-customize-cluster-linux.md) dokumentu.

## <a name="build-and-package-the-topology"></a>Tworzenie i pakietu topologii

1. Pobierz przykładowy projekt z [ https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) w środowisku deweloperskim.

2. Z wiersza polecenia terminala lub skorupach sesji, zmień katalogi na katalog główny pobranego projektu. Aby skompilować i pakiet topologii, użyj następującego polecenia:
   
        mvn compile package
   
    Po zakończeniu kompilacji i opakowanie jest nowy katalog o nazwie `target`, który zawiera plik o nazwie `StormToHdfs-1.0-SNAPSHOT.jar`. Ten plik zawiera skompilowanych topologii.

## <a name="deploy-and-run-the-topology"></a>Wdrażanie i uruchamianie topologii

1. Użyj następującego polecenia, aby skopiować topologii do klastra usługi HDInsight. Zastąp **użytkownika** z nazwą użytkownika SSH, które są używane podczas tworzenia klastra. Zamień ciąg **CLUSTERNAME** na nazwę klastra.
   
        scp target\StormToHdfs-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
   
    Po wyświetleniu monitu wprowadź hasło użyte podczas tworzenia użytkownika SSH dla klastra. Jeśli używasz klucza publicznego zamiast hasła, może być konieczne użycie `-i` parametr, aby określić ścieżkę do odpowiedniego klucza prywatnego.
   
   > [!NOTE]
   > Aby uzyskać więcej informacji na temat używania `scp` z usługą HDInsight, zobacz [używanie SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Po zakończeniu przekazywania, użyj następującego polecenia nawiązać połączenia z klastrem usługi HDInsight przy użyciu protokołu SSH. Zastąp **użytkownika** z nazwą użytkownika SSH, które są używane podczas tworzenia klastra. Zamień ciąg **CLUSTERNAME** na nazwę klastra.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    Po wyświetleniu monitu wprowadź hasło użyte podczas tworzenia użytkownika SSH dla klastra. Jeśli używasz klucza publicznego zamiast hasła, może być konieczne użycie `-i` parametr, aby określić ścieżkę do odpowiedniego klucza prywatnego.
   
   Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Po nawiązaniu połączenia użyj następującego polecenia, aby utworzyć plik o nazwie `dev.properties`:

        nano dev.properties

4. Skorzystaj z poniższego tekstu jako zawartość `dev.properties` pliku:

        hdfs.write.dir: /stormdata/
        hdfs.url: wasb:///

    > [!IMPORTANT]
    > W tym przykładzie przyjęto założenie, że klaster używa konta usługi Azure Storage jako domyślnego magazynu. Jeśli klaster używa usługi Azure Data Lake Store, użyj `hdfs.url: adl:///` zamiast tego.
    
    Aby zapisać plik, użyj __Ctrl + X__, następnie __Y__, a na końcu __Enter__. Wartości w tym pliku Ustaw adres URL usługi Data Lake i nazwę katalogu, którego dane są zapisywane.

3. Użyj następującego polecenia, aby uruchomić topologii:
   
        storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties

    To polecenie uruchamia topologię za pomocą framework strumienia poprzez przesłanie go do węzeł Nimbus klastra. Topologia jest definiowana za pomocą `writetohdfs.yaml` pliku jar. `dev.properties` Plik jest przekazywany jako filtr i wartości zawartych w pliku są odczytywane przez topologii.

## <a name="view-output-data"></a>Widok danych wyjściowych

Aby wyświetlić dane, użyj następującego polecenia:

    hdfs dfs -ls /stormdata/

Zostanie wyświetlona lista plików utworzonych przez tej topologii.

Poniżej znajduje się przykład danych zwróconej przez poprzednie polecenia:

    Found 30 items
    -rw-r-----+  1 sshuser sshuser       488000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 sshuser sshuser       444000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 sshuser sshuser       502000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 sshuser sshuser       582000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 sshuser sshuser       464000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt

## <a name="stop-the-topology"></a>Zatrzymywanie topologii

STORM topologie działają aż do zatrzymania lub usunąć klastra. Aby zatrzymać topologii, użyj następującego polecenia:

    storm kill hdfswriter

## <a name="delete-your-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz jak używać systemu Storm do zapisu do usługi Azure Storage i Azure Data Lake Store, odnajdywanie innych [Storm przykłady dla usługi HDInsight](apache-storm-example-topology.md).

