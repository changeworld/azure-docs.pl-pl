---
title: Samouczek — użycia systemu Apache Storm można zapisać do usługi Storage/Data Lake Storage — Azure HDInsight
description: Samouczek — Dowiedz się, jak używać platformy Apache Storm można zapisać do magazynu zgodnego z systemem HDFS dla usługi Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 5c1376c7d1afe9c9702cfb43a146ac1cd17d6e58
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428345"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Samouczek: Zapis Apache Hadoop HDFS z systemu Apache Storm w usłudze Azure HDInsight

Ten samouczek pokazuje, jak używać platformy Apache Storm można zapisać danych do magazynu zgodnego z systemem HDFS, używane przez system Apache Storm w HDInsight. HDInsight można użyć usługi Azure Data Lake Storage i Azure Storage jako magazynu zgodnego systemem plików HDFS. System STORM udostępnia [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) składnik, który zapisuje dane do systemu plików HDFS. Ten dokument zawiera informacje na temat pisania do dowolnego typu magazynu z HdfsBolt.

Przykładową topologię używane w tym dokumencie opiera się na składniki, które są dołączone do systemu Storm w HDInsight. Może wymagać modyfikacji do pracy z usługą Azure Data Lake Storage używany z innymi klastrów Apache Storm.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie klastra za pomocą akcji skryptu
> * Tworzenie i pakowanie topologii
> * Wdrażanie i uruchamianie topologii
> * Wyświetlanie danych wyjściowych
> * Zatrzymywanie topologii

## <a name="prerequisites"></a>Wymagania wstępne

* [Java Developer Kit (JDK) w wersji 8](https://aka.ms/azure-jdks)

* [Narzędzia Apache Maven](https://maven.apache.org/download.cgi) prawidłowo [zainstalowane](https://maven.apache.org/install.html) zgodnie z Apache.  Maven to projekt system kompilacji dla projektów Java.

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* [Schemat identyfikatora URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) do obsługi klastrów magazynu podstawowego. Takie rozwiązanie byłoby `wasb://` dla usługi Azure Storage `abfs://` dla usługi Azure Data Lake Storage Gen2 lub `adl://` dla usługi Azure Data Lake Storage Gen1. Bezpieczny transfer jest włączona dla usługi Azure Storage lub Data Lake Storage Gen2, identyfikator URI będzie mieć `wasbs://` lub `abfss://`odpowiednio Zobacz też [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md).

### <a name="example-configuration"></a>Przykładowa konfiguracja

Poniższego kodu YAML znajduje się fragment `resources/writetohdfs.yaml` pliku zawartym w przykładzie. Ten plik definiuje przy użyciu topologii Storm [strumień](https://storm.apache.org/releases/1.1.2/flux.html) umożliwiająca Apache Storm.

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

Ta YAML definiuje następujące elementy:

* `syncPolicy`: Określa, kiedy pliki są zsynchronizowane/opróżniany do systemu plików. W tym przykładzie każdy krotek 1000.
* `fileNameFormat`: Określa ścieżkę i wzorzec nazwy do użycia podczas zapisywania plików. W tym przykładzie ścieżka jest podana w czasie wykonywania przy użyciu filtru, a plik ma rozszerzenie `.txt`.
* `recordFormat`: Definiuje wewnętrzny format pliki zapisane. W tym przykładzie te pola są rozdzielone `|` znaków.
* `rotationPolicy`: Określa, kiedy do obracania plików. W tym przykładzie jest wykonywane bez obrotu.
* `hdfs-bolt`: Używa poprzednie składniki jako parametry konfiguracji `HdfsBolt` klasy.

Aby uzyskać więcej informacji na strukturze strumienia, zobacz [ https://storm.apache.org/releases/current/flux.html ](https://storm.apache.org/releases/current/flux.html).

## <a name="configure-the-cluster"></a>Konfigurowanie klastra

Domyślnie system Storm w HDInsight nie ma składników, `HdfsBolt` komunikuje się z usługi Azure Storage lub magazynu usługi Data Lake w ścieżce systemu Storm w. Użyj następujących akcji skryptu, aby dodać te składniki mogą `extlib` katalogu dla usługi Storm w klastrze:

| Właściwość | Wartość |
|---|---|
|Typ skryptu |— Niestandardowa|
|Identyfikator URI skryptu powłoki systemowej |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|Typy węzłów |Nimbus, nadzorca|
|Parametry |Brak|

Aby uzyskać informacje przy użyciu tego skryptu z klastrem, zobacz [HDInsight Dostosowywanie klastrów za pomocą akcji skryptu](./../hdinsight-hadoop-customize-cluster-linux.md) dokumentu.

## <a name="build-and-package-the-topology"></a>Tworzenie i pakowanie topologii

1. Pobierz przykładowy projekt z [ https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) do swojego środowiska projektowego.

2. Z poziomu wiersza polecenia terminal lub skorupach sesji Zmień katalogi na katalog główny pobranego projektu. Aby skompilować i pakiet topologii, użyj następującego polecenia:

    ```cmd
    mvn compile package
    ```

    Po zakończeniu kompilacji i pakowanie jest nowy katalog o nazwie `target`, który zawiera plik o nazwie `StormToHdfs-1.0-SNAPSHOT.jar`. Ten plik zawiera skompilowanych topologii.

## <a name="deploy-and-run-the-topology"></a>Wdrażanie i uruchamianie topologii

1. Użyj następującego polecenia, aby skopiować topologii do klastra HDInsight. Zastąp `CLUSTERNAME` nazwą klastra.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. Po ukończeniu przekazywania, użyj następującego polecenia, aby połączyć się z klastrem HDInsight przy użyciu protokołu SSH. Zastąp `CLUSTERNAME` nazwą klastra.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Po nawiązaniu połączenia użyj następującego polecenia, aby utworzyć plik o nazwie `dev.properties`:

    ```bash
    nano dev.properties
    ```

1. Skorzystaj z poniższego tekstu jako zawartość `dev.properties` pliku. Na podstawie Popraw stosownie do potrzeb Twojej [schemat identyfikatora URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    Aby zapisać plik, użyj __Ctrl + X__, następnie __Y__, a na koniec __Enter__. Wartości w tym pliku Ustaw adres URL magazynu i nazwę katalogu, który dane są zapisywane.

1. Użyj następującego polecenia, aby rozpocząć topologii:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    To polecenie uruchamia topologii przy użyciu platformy strumienia, przesyłając do węzeł Nimbus klastra. Topologia jest definiowany przez `writetohdfs.yaml` plik dołączony plik jar. `dev.properties` Plik jest przekazywany jako filtr, a wartości zawarte w pliku są odczytywane przez topologię.

## <a name="view-output-data"></a>Wyświetlanie danych wyjściowych

Aby wyświetlić dane, użyj następującego polecenia:

  ```bash
  hdfs dfs -ls /stormdata/
  ```

Zostanie wyświetlona lista plików utworzonych przez tej topologii. Poniżej przedstawiono przykładowy dane zwrócone przez poprzednie polecenia:

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

STORM topologie działają aż do zatrzymania lub klaster jest usuwany. Aby zatrzymać topologię, użyj następującego polecenia:

```bash
storm kill hdfswriter
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby wyczyścić zasoby utworzone w tym samouczku, możesz usunąć grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego klastra usługi HDInsight i wszystkich innych zasobów skojarzonych z tą grupą zasobów.

Aby usunąć grupę zasobów za pomocą witryny Azure Portal:

1. W witrynie Azure Portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję __Grupy zasobów__, aby wyświetlić listę grup zasobów.
2. Znajdź grupę zasobów do usunięcia, a następnie kliknij prawym przyciskiem myszy przycisk __Więcej (...)__ po prawej stronie listy.
3. Wybierz pozycję __Usuń grupę zasobów__ i potwierdź.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób użycia systemu Apache Storm można zapisać danych do magazynu zgodnego z systemem HDFS, używane przez system Apache Storm w HDInsight.

> [!div class="nextstepaction"]
> Odkryj inne [przykłady HDInsight Apache Storm](apache-storm-example-topology.md)