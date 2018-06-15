---
title: Apache Storm z comopnents Python - Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć topologii Apache Storm, która używa składników języka Python.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
keywords: Apache storm w języku python
ms.assetid: edd0ec4f-664d-4266-910c-6ecc94172ad8
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: python
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: larryfr
ms.openlocfilehash: 0a8930da2d44714573155477d791dd9cb78d872a
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
ms.locfileid: "32311696"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Opracowywanie topologii Apache Storm w usłudze HDInsight przy użyciu języka Python

Dowiedz się, jak utworzyć topologii Apache Storm, która używa składników języka Python. Apache Storm obsługuje wiele języków, umożliwiając nawet łączyć składniki z kilku języków w topologii jeden. Framework strumienia (wprowadzona w systemie Storm 0.10.0) pozwala na łatwe tworzenie rozwiązań, które używają składników języka Python.

> [!IMPORTANT]
> Informacje przedstawione w tym dokumencie przetestowano korzystanie z systemu Storm w usłudze HDInsight 3,6. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

Kod dla tego projektu jest dostępne pod adresem [ https://github.com/Azure-Samples/hdinsight-python-storm-wordcount ](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount).

## <a name="prerequisites"></a>Wymagania wstępne

* Python 2.7 lub nowszej

* Java JDK 1.8 lub nowszej

* Maven 3

* (Opcjonalnie) Lokalne Środowisko deweloperskie Storm. Środowisko lokalne Storm jest potrzebne tylko wtedy, jeśli chcesz uruchomić topologii lokalnie. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowiska projektowego](http://storm.apache.org/releases/1.1.0/Setting-up-development-environment.html).

## <a name="storm-multi-language-support"></a>Obsługa wielu języków STORM

Apache Storm został zaprojektowany do pracy ze składnikami napisane przy użyciu dowolnego języka programowania. Składniki zrozumieć, jak pracować z [Thrift definicji Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Dla języka Python moduł jest dostarczane jako część projektu systemu Apache Storm, która pozwala łatwo łączyć się z systemu Storm. Możesz znaleźć tego modułu w [ https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py ](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

STORM jest proces Java, który działa na maszynie wirtualnej Java (JVM). Składniki napisanych w innych językach są wykonywane jako podprocesy. Storm komunikuje się z tych podprocesy przy użyciu JSON wiadomości wysyłane za pośrednictwem stdin/stdout. Więcej informacji dotyczących komunikacji między składnikami znajdują się w [protokołu Multi-lang](https://storm.apache.org/documentation/Multilang-protocol.html) dokumentacji.

## <a name="python-with-the-flux-framework"></a>Python Framework strumienia

W ramach strumienia można zdefiniować topologii Storm niezależnie od składników. W ramach strumienia używa yaml programu w celu zdefiniowania topologii Storm. Następujący tekst jest przykładem odwołać składnika Python w dokumencie yaml programu:

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

Strumień oczekuje skrypty języka Python w `/resources` katalogu wewnątrz plik jar zawierający topologii. Dlatego w tym przykładzie przechowuje skrypty języka Python w `/multilang/resources` katalogu. `pom.xml` Zawiera ten plik przy użyciu następującego kodu XML:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Jak wspomniano wcześniej, Brak `storm.py` pliku, który implementuje z definicją Thrift Storm. Struktura strumień obejmuje `storm.py` automatycznie gdy projekt jest budowany, tak aby nie trzeba martwić się o tym go.

## <a name="build-the-project"></a>Kompilowanie projektu

W katalogu głównym projektu użyj następującego polecenia:

```bash
mvn clean compile package
```

To polecenie tworzy `target/WordCount-1.0-SNAPSHOT.jar` plik zawierający skompilowanych topologii.

## <a name="run-the-topology-locally"></a>Uruchom lokalnie topologii

Aby uruchomić topologii lokalnie, należy użyć następującego polecenia:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]
> To polecenie wymaga lokalne Środowisko deweloperskie Storm. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowiska projektowego](http://storm.apache.org/releases/1.0.1/Setting-up-development-environment.html)

Po uruchomieniu topologii go emituje informacje podobne do następującego konsoli lokalnej:


    24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
    24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
    24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
    24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}


Aby zatrzymać topologii, użyj __klawisze Ctrl + C__.

## <a name="run-the-storm-topology-on-hdinsight"></a>Uruchom topologii Storm w usłudze HDInsight

1. Użyj następującego polecenia, aby skopiować `WordCount-1.0-SNAPSHOT.jar` pliku Storm w klastrze usługi HDInsight:

    ```bash
    scp target\WordCount-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net
    ```

    Zastąp `sshuser` użytkownika SSH dla klastra. Zastąp `mycluster` z nazwą klastra. Może pojawić się prośba o podanie hasła dla użytkownika SSH.

    Aby uzyskać więcej informacji o korzystaniu protokołów SSH i SCP, zobacz [używanie SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Po przekazaniu pliku nawiązać połączenia z klastrem przy użyciu protokołu SSH:

    ```bash
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. W sesji SSH Użyj następującego polecenia, aby uruchomić topologii w klastrze:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

3. Interfejs użytkownika platformy Storm służy do wyświetlania topologii w klastrze. Interfejs użytkownika platformy Storm znajduje się pod adresem https://mycluster.azurehdinsight.net/stormui. Zastąp `mycluster` z nazwą klastra.

> [!NOTE]
> Po rozpoczęciu topologii Storm uruchamia aż do zatrzymania. Aby zatrzymać topologii, użyj jednej z następujących metod:
>
> * `storm kill TOPOLOGYNAME` Polecenia z wiersza polecenia
> * **Kill** przycisk w Interfejsie użytkownika platformy Storm.


## <a name="next-steps"></a>Kolejne kroki

Można znaleźć w następujących dokumentach inne sposoby korzystania z języka Python z usługą HDInsight:

* [Jak używać języka Python dla zadań MapReduce przesyłania strumieniowego](../hadoop/apache-hadoop-streaming-python.md)
* [Jak używać języka Python użytkownika określone funkcje (UDF) i Hive](../hadoop/python-udf-hdinsight.md)
