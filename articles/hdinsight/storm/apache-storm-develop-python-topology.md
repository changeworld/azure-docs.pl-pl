---
title: Apache Storm ze składnikami języka Python — Azure HDInsight
description: Dowiedz się, jak utworzyć topologii Apache Storm, który używa składników w języku Python.
author: hrasheed-msft
ms.reviewer: jasonh
keywords: Apache storm w języku python
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: hrasheed
ms.openlocfilehash: a5cbd54dd07143688b676c063133bb1a73bed01a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64694386"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Opracowywanie topologii usługi Apache Storm na HDInsight przy użyciu języka Python

Dowiedz się, jak utworzyć [Apache Storm](https://storm.apache.org/) topologię, która używa składników w języku Python. Apache Storm obsługuje wiele języków, nawet co pozwoli na łączenie składników w kilku językach, w jednym topologii. [Strumień](https://storm.apache.org/releases/current/flux.html) framework (wprowadzona w systemie Storm 0.10.0) pozwala na łatwe tworzenie rozwiązań korzystających z składników w języku Python.

> [!IMPORTANT]  
> Informacje przedstawione w tym dokumencie został przetestowany przy użyciu technologii Storm w HDInsight 3.6. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

Kod dla tego projektu znajduje się w temacie [ https://github.com/Azure-Samples/hdinsight-python-storm-wordcount ](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount).

## <a name="prerequisites"></a>Wymagania wstępne

* Python 2.7 lub nowszego

* Java JDK 1.8 lub nowszej

* [Apache Maven 3](https://maven.apache.org/download.cgi)

* (Opcjonalnie) Lokalne Środowisko deweloperskie systemu Storm. W lokalnym środowisku Storm jest wymagane tylko w przypadku, jeśli chcesz uruchamiać lokalnie topologii. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowiska deweloperskiego](https://storm.apache.org/releases/1.1.2/Setting-up-development-environment.html).

## <a name="storm-multi-language-support"></a>Obsługa wielu języków systemu STORM

Apache Storm był projektowany do pracy ze składnikami napisanymi przy użyciu dowolnego języka programowania. Składniki należy zrozumieć sposób pracy z [definicją Thrift STORM](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Dla języka Python moduł jest dostarczany jako część projektu Apache Storm, która pozwala na łatwe łączenie się z systemu Storm. Możesz znaleźć tego modułu w [ https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py ](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Platforma STORM jest proces Java, który działa na maszynie wirtualnej Java (JVM). Składników napisanych w innych językach są wykonywane jako procesy podrzędne. Storm komunikuje się z tymi procesy podrzędne przy użyciu formatu JSON komunikaty wysyłane za pośrednictwem stdin/stdout. Szczegółowe informacje na temat komunikacji między składnikami, które można znaleźć w [protokołu Multi lang](https://storm.apache.org/documentation/Multilang-protocol.html) dokumentacji.

## <a name="python-with-the-flux-framework"></a>Python z architekturą strumienia

W ramach strumienia umożliwia definiowanie topologii Storm niezależnie od składników. W ramach strumienia używa YAML do definiowania topologii systemu Storm. Następujący tekst jest przykładem sposobu się odwoływać do składnika języka Python w dokumencie YAML:

```yaml
# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "org.apache.storm.flux.wrappers.spouts.FluxShellSpout"
    constructorArgs:
      # Command line
      - ["python", "sentencespout.py"]
      # Output field(s)
      - ["sentence"]
    # parallelism hint
    parallelism: 1
```

Klasa `FluxShellSpout` są używane do uruchamiania `sentencespout.py` skrypt, który implementuje spout.

Strumień oczekuje skryptów w języku Python w `/resources` katalog wewnątrz pliku jar, który zawiera topologii. Dlatego w tym przykładzie przechowuje skryptów języka Python w `/multilang/resources` katalogu. `pom.xml` Zawiera ten plik za pomocą następujący kod XML:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Jak wspomniano wcześniej, ma `storm.py` pliku, który implementuje definicją Thrift dla usługi Storm. Struktura strumień obejmuje `storm.py` automatycznie podczas kompilowania projektu, więc nie trzeba już martwić się o tym go.

## <a name="build-the-project"></a>Kompilowanie projektu

W folderze głównym projektu użyj następującego polecenia:

```bash
mvn clean compile package
```

To polecenie umożliwia utworzenie `target/WordCount-1.0-SNAPSHOT.jar` plik zawierający skompilowane topologii.

## <a name="run-the-topology-locally"></a>Lokalne uruchamianie topologii

Aby uruchomić lokalnie topologii, użyj następującego polecenia:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> To polecenie wymaga lokalnego środowiska programistycznego platformy Storm. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowiska deweloperskiego](https://storm.apache.org/releases/current/Setting-up-development-environment.html)

Po rozpoczęciu topologii emituje informacje do konsoli lokalnej, która jest podobny do następującego tekstu:


    24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
    24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
    24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
    24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}


Aby zatrzymać topologię, użyj __klawisze Ctrl + C__.

## <a name="run-the-storm-topology-on-hdinsight"></a>Uruchamianie topologii systemu Storm na HDInsight

1. Użyj następującego polecenia, aby skopiować `WordCount-1.0-SNAPSHOT.jar` pliku do usługi Storm w klastrze HDInsight:

    ```bash
    scp target\WordCount-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net
    ```

    Zastąp `sshuser` użytkownika SSH dla klastra. Zastąp `mycluster` nazwą klastra. Może być wyświetlony monit o wprowadzenie hasła dla użytkownika SSH.

    Aby uzyskać więcej informacji o korzystaniu protokołów SSH i SCP, zobacz [użycia protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Po przekazaniu pliku Połącz się z klastrem przy użyciu protokołu SSH:

    ```bash
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. W sesji SSH wpisz następujące polecenie do uruchomienia topologii w klastrze:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

3. Interfejs użytkownika platformy Storm można użyć, aby wyświetlić topologię w klastrze. Interfejs użytkownika platformy Storm znajduje się w folderze https://mycluster.azurehdinsight.net/stormui. Zastąp `mycluster` nazwą klastra.

> [!NOTE]  
> Po rozpoczęciu topologii Storm uruchamia, aż do zatrzymania. Aby zatrzymać topologię, użyj jednej z następujących metod:
>
> * `storm kill TOPOLOGYNAME` Polecenia z wiersza polecenia
> * **Kill** przycisk interfejs użytkownika platformy Storm.


## <a name="next-steps"></a>Kolejne kroki

Zobacz następujące dokumenty, aby inne sposoby korzystania z języka Python za pomocą HDInsight:

* [Jak używać języka Python określone funkcje użytkownika (UDF) w Apache Pig i Apache Hive](../hadoop/python-udf-hdinsight.md)
