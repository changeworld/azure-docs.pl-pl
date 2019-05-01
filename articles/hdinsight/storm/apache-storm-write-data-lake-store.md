---
title: Apache Storm zapisu do usługi Storage/Data Lake Storage — Azure HDInsight
description: Dowiedz się, jak używać platformy Apache Storm można zapisać do magazynu zgodnego z systemem HDFS dla HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.openlocfilehash: 4ba0c861674eb2308cf1f96c33d0792f3e1a0f94
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683678"
---
# <a name="write-to-apache-hadoop-hdfs-from-apache-storm-on-hdinsight"></a>Zapisu Apache Hadoop HDFS z systemu Storm Apache na HDInsight

Dowiedz się, jak używać [Apache Storm](https://storm.apache.org/) można zapisać danych do magazynu zgodnego z systemem HDFS, używane przez system Apache Storm w HDInsight. HDInsight można użyć usługi Azure Data Lake Storage i Azure Storage jako magazynu zgodnego systemem plików HDFS. System STORM udostępnia [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) składnik, który zapisuje dane do systemu plików HDFS. Ten dokument zawiera informacje na temat pisania do dowolnego typu magazynu z HdfsBolt. 

> [!IMPORTANT]  
> Przykładową topologię używane w tym dokumencie opiera się na składniki, które są dołączone do systemu Storm w HDInsight. Może wymagać modyfikacji do pracy z usługą Azure Data Lake Storage używany z innymi klastrów Apache Storm.

## <a name="get-the-code"></a>Uzyskiwanie kodu

Projekt zawierający Ta topologia jest dostępna do pobrania z [ https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

Aby skompilować ten projekt, potrzebne jest następująca konfiguracja środowiska deweloperskiego:

* Zestaw [Java JDK 1.8](https://aka.ms/azure-jdks) lub nowszy. Usługa HDInsight w wersji 3.5 lub nowszej wymaga środowiska Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

Po zainstalowaniu środowiska Java i zestawu JDK na deweloperskiej stacji roboczej mogą zostać ustawione następujące zmienne środowiskowe. Należy jednak sprawdzić, czy te zmienne istnieją i czy zawierają poprawne wartości dla systemu.

* `JAVA_HOME` — powinna wskazywać katalog, w którym jest zainstalowany zestaw JDK.
* `PATH` — powinna zawierać następujące ścieżki:
  
    * `JAVA_HOME` (lub równoważną ścieżkę).
    * `JAVA_HOME\bin` (lub równoważną ścieżkę).
    * Katalog, w którym zainstalowano narzędzie Maven.

## <a name="how-to-use-the-hdfsbolt-with-hdinsight"></a>Jak używać HdfsBolt z HDInsight

> [!IMPORTANT]  
> Przed użyciem HdfsBolt z systemu Storm w HDInsight, należy najpierw użyć akcji skryptu można skopiować pliki jar wymagane do `extpath` dla usługi Storm. Aby uzyskać więcej informacji zobacz Konfigurowanie sekcji klastra.

HdfsBolt wykorzystuje schemat pliku, zapewniające zrozumienie, jak można zapisać do systemu plików HDFS. Za pomocą HDInsight wykonaj jedną z następujących systemów:

* `wasb://`: Używane z konta usługi Azure Storage.
* `abfs://`: Używane, za pomocą usługi Azure Data Lake Storage Gen2.
* `adl://`: Używane, za pomocą usługi Azure Data Lake Storage Gen1.

Poniższa tabela zawiera przykłady użycia systemu plików w różnych scenariuszach:

| Schemat | Uwagi |
| ----- | ----- |
| `wasb:///` | Domyślne konto magazynu jest kontener obiektów blob na koncie usługi Azure Storage |
| `abfs:///` | Domyślne konto magazynu jest katalogiem na koncie usługi Azure Data Lake Storage Gen2 |
| `adl:///` | Domyślne konto magazynu jest katalogiem w usługi Azure Data Lake Storage Gen1. Podczas tworzenia klastra należy określić katalog, w usługi Data Lake Storage, który jest elementem głównym systemu HDFS z klastra. Na przykład `/clusters/myclustername/` katalogu. |
| `wasb://CONTAINER@ACCOUNT.blob.core.windows.net/` | Konto inne niż domyślne, (dodatkowe) usługi Azure storage skojarzonym z klastrem. |
| `abfs://CONTAINER@ACCOUNT.dfs.core.windows.net/` | Konto inne niż domyślne, (dodatkowe) usługi Azure storage skojarzonym z klastrem. |
| `adl://STORENAME/` | Katalog główny usługi Data Lake Storage używane przez klaster. Ten schemat umożliwia dostęp do danych znajdujących się poza katalogiem, który zawiera klastra systemu plików. |

Aby uzyskać więcej informacji, zobacz [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) odwołania w serwisie Apache.org.

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

Domyślnie system Storm w HDInsight nie ma składników, które HdfsBolt używa do komunikowania się za pomocą usługi Azure Storage lub magazynu usługi Data Lake w ścieżce systemu Storm w. Użyj następujących akcji skryptu, aby dodać te składniki mogą `extlib` katalogu dla usługi Storm w klastrze:

* Identyfikator URI skryptu: `https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`
* Węzły do zastosowania do: Nimbus, nadzorca
* Parametry: Brak

Aby uzyskać informacje przy użyciu tego skryptu z klastrem, zobacz [HDInsight Dostosowywanie klastrów za pomocą akcji skryptu](./../hdinsight-hadoop-customize-cluster-linux.md) dokumentu.

## <a name="build-and-package-the-topology"></a>Tworzenie i pakowanie topologii

1. Pobierz przykładowy projekt z [ https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) do swojego środowiska projektowego.

2. Z poziomu wiersza polecenia terminal lub skorupach sesji Zmień katalogi na katalog główny pobranego projektu. Aby skompilować i pakiet topologii, użyj następującego polecenia:
   
        mvn compile package
   
    Po zakończeniu kompilacji i pakowanie jest nowy katalog o nazwie `target`, który zawiera plik o nazwie `StormToHdfs-1.0-SNAPSHOT.jar`. Ten plik zawiera skompilowanych topologii.

## <a name="deploy-and-run-the-topology"></a>Wdrażanie i uruchamianie topologii

1. Użyj następującego polecenia, aby skopiować topologii do klastra HDInsight. Zastąp **użytkownika** z nazwą użytkownika protokołu SSH, które są używane podczas tworzenia klastra. Zamień ciąg **CLUSTERNAME** na nazwę klastra.
   
        scp target\StormToHdfs-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
   
    Po wyświetleniu monitu wprowadź hasło użyte podczas tworzenia użytkownika SSH dla klastra. Jeśli klucz publiczny jest używany zamiast hasła, może być konieczne użycie `-i` parametru do określenia ścieżki do odpowiedniego klucza prywatnego.
   
   > [!NOTE]  
   > Aby uzyskać więcej informacji na temat używania polecenia `scp` w usłudze HDInsight, zobacz [Korzystanie z protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Po ukończeniu przekazywania, użyj następującego polecenia, aby połączyć się z klastrem HDInsight przy użyciu protokołu SSH. Zastąp **użytkownika** z nazwą użytkownika protokołu SSH, które są używane podczas tworzenia klastra. Zamień ciąg **CLUSTERNAME** na nazwę klastra.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    Po wyświetleniu monitu wprowadź hasło użyte podczas tworzenia użytkownika SSH dla klastra. Jeśli klucz publiczny jest używany zamiast hasła, może być konieczne użycie `-i` parametru do określenia ścieżki do odpowiedniego klucza prywatnego.
   
   Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Po nawiązaniu połączenia użyj następującego polecenia, aby utworzyć plik o nazwie `dev.properties`:

        nano dev.properties

4. Skorzystaj z poniższego tekstu jako zawartość `dev.properties` pliku:

        hdfs.write.dir: /stormdata/
        hdfs.url: wasb:///

    > [!IMPORTANT]  
    > W tym przykładzie przyjęto założenie, że klaster używa konta usługi Azure Storage jako magazynu domyślnego. Jeśli klaster używa usługi Azure Data Lake Storage Gen2, użyj `hdfs.url: abfs:///` zamiast tego. Jeśli klaster używa usługi Azure Data Lake Storage Gen1, użyj `hdfs.url: adl:///` zamiast tego.
    
    Aby zapisać plik, użyj __Ctrl + X__, następnie __Y__, a na koniec __Enter__. Wartości w tym pliku Ustaw adres URL magazynu jeziora danych i nazwę katalogu, który dane są zapisywane.

3. Użyj następującego polecenia, aby rozpocząć topologii:
   
        storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties

    To polecenie uruchamia topologii przy użyciu platformy strumienia, przesyłając do węzeł Nimbus klastra. Topologia jest definiowany przez `writetohdfs.yaml` plik dołączony plik jar. `dev.properties` Plik jest przekazywany jako filtr, a wartości zawarte w pliku są odczytywane przez topologię.

## <a name="view-output-data"></a>Wyświetlanie danych wyjściowych

Aby wyświetlić dane, użyj następującego polecenia:

    hdfs dfs -ls /stormdata/

Zostanie wyświetlona lista plików utworzonych przez tej topologii.

Poniżej przedstawiono przykładowy dane zwrócone przez poprzednie polecenia:

    Found 30 items
    -rw-r-----+  1 sshuser sshuser       488000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 sshuser sshuser       444000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 sshuser sshuser       502000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 sshuser sshuser       582000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 sshuser sshuser       464000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt

## <a name="stop-the-topology"></a>Zatrzymywanie topologii

STORM topologie działają aż do zatrzymania lub klaster jest usuwany. Aby zatrzymać topologię, użyj następującego polecenia:

    storm kill hdfswriter

## <a name="delete-your-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak używać platformy Apache Storm można zapisać do usługi Azure Storage i Azure Data Lake Storage odnajdywania innych [przykłady Apache Storm HDInsight](apache-storm-example-topology.md).

## <a name="see-also"></a>Zobacz także
* [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
