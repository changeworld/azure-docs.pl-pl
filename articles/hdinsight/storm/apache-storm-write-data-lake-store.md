---
title: 'Samouczek: USŁUGA HDInsight Apache Storm to Storage — usługa Azure/Data Lake'
description: Samouczek — dowiedz się, jak używać usługi Apache Storm do zapisywania w magazynie zgodnym z systemem HDFS dla usługi Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 579163180f6c7ba19927ca66d20bd92d1b2de52e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73241210"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Samouczek: Napisz do Apache Hadoop HDFS z Apache Storm na platformie Azure HDInsight

W tym samouczku pokazano, jak używać usługi Apache Storm do zapisywania danych w pamięci masowej zgodnej z systemem HDFS używanej przez Apache Storm w programie HDInsight. Usługa HDInsight może używać zarówno usługi Azure Storage, jak i usługi Azure Data Lake Storage jako magazynu zgodnego z systemem HDFS. Storm udostępnia składnik [HdfsBolt,](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) który zapisuje dane do usługi HDFS. Ten dokument zawiera informacje na temat zapisu do jednego typu magazynu z HdfsBolt.

Przykładowa topologia używana w tym dokumencie opiera się na składnikach, które są dołączone do Storm on HDInsight. Może wymagać modyfikacji do pracy z usługą Azure Data Lake Storage, gdy jest używana z innymi klastrami Usługi Apache Storm.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie klastra za pomocą akcji skryptu
> * Tworzenie i pakowanie topologii
> * Wdrażanie i uruchamianie topologii
> * Wyświetlanie danych wyjściowych
> * Zatrzymywanie topologii

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw Java Developer Kit (JDK) w wersji 8](https://aka.ms/azure-jdks)

* [Apache Maven](https://maven.apache.org/download.cgi) prawidłowo [zainstalowany](https://maven.apache.org/install.html) zgodnie z Apache.  Maven to system budowania projektów java.

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* [Schemat identyfikatorów URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) dla magazynu podstawowego klastrów. Dotyczy to `wasb://` usługi Azure `abfs://` Storage, usługi Azure `adl://` Data Lake Storage Gen2 lub usługi Azure Data Lake Storage Gen1. Jeśli bezpieczny transfer jest włączony dla usługi Azure `wasbs://`Storage, identyfikator URI będzie .  Zobacz też [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md).

### <a name="example-configuration"></a>Przykładowa konfiguracja

Poniższy YAML jest fragmentem `resources/writetohdfs.yaml` pliku zawarte w przykładzie. Ten plik definiuje topologię Storm przy użyciu struktury [Flux](https://storm.apache.org/releases/current/flux.html) dla Apache Storm.

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

Ten YAML definiuje następujące elementy:

* `syncPolicy`: Określa, kiedy pliki są synchronizowane/opróżniane z systemem plików. W tym przykładzie co 1000 krotek.
* `fileNameFormat`: Definiuje wzorzec ścieżki i nazwy pliku, który ma być używany podczas pisania plików. W tym przykładzie ścieżka jest dostarczana w czasie wykonywania `.txt`przy użyciu filtru, a rozszerzenie pliku jest .
* `recordFormat`: Definiuje wewnętrzny format zapisanych plików. W tym przykładzie pola są `|` rozdzielane przez znak.
* `rotationPolicy`: Określa, kiedy mają być obracane pliki. W tym przykładzie nie jest wykonywane obracanie.
* `hdfs-bolt`: Używa poprzednich komponentów jako `HdfsBolt` parametrów konfiguracji dla klasy.

Aby uzyskać więcej informacji na [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html)temat struktury Flux, zobacz .

## <a name="configure-the-cluster"></a>Konfigurowanie klastra

Domyślnie Storm on HDInsight nie zawiera `HdfsBolt` składników, które służy do komunikowania się z usługi Azure Storage lub Data Lake Storage w ścieżce klasy storm. Użyj następującej akcji skryptu, `extlib` aby dodać te składniki do katalogu storm w klastrze:

| Właściwość | Wartość |
|---|---|
|Typ skryptu |- Niestandardowe|
|Identyfikator URI skryptu bash |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|Typy węzłów |Nimbus, Inspektor|
|Parametry |Brak|

Aby uzyskać informacje na temat używania tego skryptu z klastrem, zobacz [Dostosowywanie klastrów HDInsight przy użyciu dokumentu akcji skryptu.](./../hdinsight-hadoop-customize-cluster-linux.md)

## <a name="build-and-package-the-topology"></a>Tworzenie i pakowanie topologii

1. Pobierz przykładowy [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) projekt z środowiska programistycznego.

2. W wierszu polecenia, terminalu lub sesji powłoki zmień katalogi na katalogi główne pobranego projektu. Aby skompilować i spakować topologię, użyj następującego polecenia:

    ```cmd
    mvn compile package
    ```

    Po zakończeniu kompilacji i pakowania pojawia się `target`nowy katalog o `StormToHdfs-1.0-SNAPSHOT.jar`nazwie , który zawiera plik o nazwie . Ten plik zawiera skompilowaną topologię.

## <a name="deploy-and-run-the-topology"></a>Wdrażanie i uruchamianie topologii

1. Użyj następującego polecenia, aby skopiować topologię do klastra HDInsight. Zamień `CLUSTERNAME` na nazwę klastra.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. Po zakończeniu przekazywania należy połączyć się z klastrem HDInsight przy użyciu funkcji SSH. Zamień `CLUSTERNAME` na nazwę klastra.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Po nawiązaniu połączenia użyj następującego `dev.properties`polecenia, aby utworzyć plik o nazwie:

    ```bash
    nano dev.properties
    ```

1. Użyj następującego tekstu jako `dev.properties` zawartości pliku. W razie potrzeby należy poprawić na podstawie [schematu URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    Aby zapisać plik, użyj __klawiszy Ctrl + X__, a następnie __Y__, a na koniec __Wprowadź__. Wartości w tym pliku ustawiają adres URL magazynu i nazwę katalogu, na które są zapisywane dane.

1. Aby uruchomić topologię, użyj następującego polecenia:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    To polecenie uruchamia topologię przy użyciu struktury Flux, przesyłając ją do węzła Nimbus klastra. Topologia jest definiowana `writetohdfs.yaml` przez plik zawarty w słoiku. Plik `dev.properties` jest przekazywany jako filtr, a wartości zawarte w pliku są odczytywane przez topologię.

## <a name="view-output-data"></a>Wyświetlanie danych wyjściowych

Aby wyświetlić dane, użyj następującego polecenia:

  ```bash
  hdfs dfs -ls /stormdata/
  ```

Zostanie wyświetlona lista plików utworzonych przez tę topologię. Poniższa lista jest przykładem danych zwracanych przez poprzednie polecenia:

```output
Found 23 items
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-0-1561407909895.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-1-1561407915577.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-10-1561407943327.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-11-1561407946312.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-12-1561407949320.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-13-1561407952662.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-14-1561407955502.txt
```

## <a name="stop-the-topology"></a>Zatrzymywanie topologii

Topologie burzowe są uruchamiane do momentu zatrzymania lub usunięcie klastra. Aby zatrzymać topologię, użyj następującego polecenia:

```bash
storm kill hdfswriter
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby wyczyścić zasoby utworzone w tym samouczku, możesz usunąć grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego klastra usługi HDInsight i wszystkich innych zasobów skojarzonych z tą grupą zasobów.

Aby usunąć grupę zasobów za pomocą witryny Azure Portal:

1. W witrynie Azure Portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję __Grupy zasobów__, aby wyświetlić listę grup zasobów.
2. Znajdź grupę zasobów do usunięcia, a następnie kliknij prawym przyciskiem myszy przycisk __Więcej (...)__ po prawej stronie listy.
3. Wybierz pozycję __Usuń grupę zasobów__ i potwierdź.

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak używać Apache Storm do zapisywania danych w pamięci masowej zgodnej z systemem HDFS używanej przez Apache Storm w programie HDInsight.

> [!div class="nextstepaction"]
> Odkryj inne [przykłady Burzy Apache dla HDInsight](apache-storm-example-topology.md)