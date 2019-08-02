---
title: Apache Storm ze składnikami języka Python — Azure HDInsight
description: Dowiedz się, jak utworzyć topologię Apache Storm, która używa składników języka Python.
author: hrasheed-msft
ms.reviewer: jasonh
keywords: środowisko Python burzy Apache
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: hrasheed
ms.openlocfilehash: b9faf33734ba17e9912246fe9c5c2ac45c55ba44
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598478"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Opracowywanie topologii Apache Storm przy użyciu języka Python w usłudze HDInsight

Dowiedz się, jak utworzyć topologię [Apache Storm](https://storm.apache.org/) , która używa składników języka Python. Apache Storm obsługuje wiele języków, nawet umożliwiając Łączenie składników z kilku języków w jednej topologii. Platforma [strumień](https://storm.apache.org/releases/current/flux.html) (wprowadzona przy użyciu burzy 0.10.0) umożliwia łatwe tworzenie rozwiązań wykorzystujących składniki języka Python.

> [!IMPORTANT]  
> Informacje przedstawione w tym dokumencie zostały przetestowane przy użyciu burzy w usłudze HDInsight 3,6. 

Kod dla tego projektu jest dostępny pod adresem [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount).

## <a name="prerequisites"></a>Wymagania wstępne

* Python 2,7 lub nowszy

* Java JDK 1,8 lub nowszy

* [Apache Maven 3](https://maven.apache.org/download.cgi)

* Obowiązkowe Lokalne środowisko projektowe burzy. Lokalne środowisko burzowe jest wymagane tylko wtedy, gdy chcesz uruchomić topologię lokalnie. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowiska deweloperskiego](http://storm.apache.org/releases/current/Setting-up-development-environment.html).

## <a name="storm-multi-language-support"></a>Obsługa wielu języków

Apache Storm został zaprojektowany z myślą o pracy ze składnikami napisane przy użyciu dowolnego języka programowania. Składniki muszą zrozumieć sposób pracy z [definicją Thrift dla burzy](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). W przypadku języka Python moduł jest dostarczany jako część projektu Apache Storm, który umożliwia łatwe interfejsowanie z burzą. Ten moduł można znaleźć pod adresem [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Burza jest procesem języka Java, który działa na wirtualna maszyna Java (JVM). Składniki w innych językach są wykonywane jako podprocesy. Burza komunikuje się z tymi podprocesami przy użyciu komunikatów JSON wysyłanych za pośrednictwem stdin/stdout. Więcej informacji na temat komunikacji między składnikami można znaleźć w dokumentacji [protokołu z wieloma językami](https://storm.apache.org/documentation/Multilang-protocol.html) .

## <a name="python-with-the-flux-framework"></a>Python z platformą strumień

Platforma strumień umożliwia definiowanie topologii burzy niezależnie od składników. Struktura strumienia używa YAML do definiowania topologii burzy. Poniższy tekst przedstawia przykład sposobu odwoływania się do składnika języka Python w dokumencie YAML:

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

Klasa `FluxShellSpout` jest używana do `sentencespout.py` uruchomienia skryptu, który implementuje elementu Spout.

Strumień oczekuje, że skrypty języka Python mają znajdować `/resources` się w katalogu wewnątrz pliku JAR, który zawiera topologię. W tym przykładzie są przechowywane skrypty języka Python w `/multilang/resources` katalogu. `pom.xml` Obejmuje następujący plik XML:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Jak wspomniano wcześniej, istnieje `storm.py` plik, który implementuje definicję Thrift dla burzy. Platforma strumień jest `storm.py` automatycznie tworzona podczas kompilowania projektu, więc nie trzeba martwić się o dołączenie do niego.

## <a name="build-the-project"></a>Skompiluj projekt

Z poziomu katalogu głównego projektu, użyj następującego polecenia:

```bash
mvn clean compile package
```

To polecenie tworzy `target/WordCount-1.0-SNAPSHOT.jar` plik, który zawiera skompilowaną topologię.

## <a name="run-the-topology-locally"></a>Uruchamianie topologii lokalnie

Aby uruchomić topologię lokalnie, użyj następującego polecenia:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> To polecenie wymaga lokalnego środowiska projektowego burzy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowiska deweloperskiego](https://storm.apache.org/releases/current/Setting-up-development-environment.html)

Po uruchomieniu topologii emitują informacje do konsoli lokalnej podobne do następującego tekstu:


    24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
    24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
    24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
    24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}


Aby zatrzymać topologię, użyj __kombinacji klawiszy CTRL + C__.

## <a name="run-the-storm-topology-on-hdinsight"></a>Uruchamianie topologii burzy w usłudze HDInsight

1. Użyj następującego polecenia, aby skopiować `WordCount-1.0-SNAPSHOT.jar` plik do burzy w klastrze usługi HDInsight:

    ```bash
    scp target\WordCount-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net
    ```

    Zastąp `sshuser` wartość użytkownikiem SSH klastra. Zamień `mycluster` na nazwę klastra. Może zostać wyświetlony monit o wprowadzenie hasła dla użytkownika SSH.

    Aby uzyskać więcej informacji na temat używania protokołów SSH i SCP, zobacz [Używanie protokołu SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Po przekazaniu pliku Połącz się z klastrem przy użyciu protokołu SSH:

    ```bash
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. W sesji SSH Użyj następującego polecenia, aby uruchomić topologię w klastrze:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

3. Można użyć interfejsu użytkownika burzy do wyświetlania topologii w klastrze. Interfejs użytkownika burzy znajduje się https://mycluster.azurehdinsight.net/stormui w lokalizacji. Zastąp `mycluster` nazwą klastra.

> [!NOTE]  
> Po uruchomieniu topologia burza jest uruchamiana do momentu zatrzymania. Aby zatrzymać topologię, należy użyć jednej z następujących metod:
>
> * `storm kill TOPOLOGYNAME` Polecenie z wiersza polecenia
> * Przycisk **kasowania** w interfejsie użytkownika burzy.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi dokumentami, aby poznać inne sposoby używania języka Python z usługą HDInsight:

* [Jak używać funkcji języka Python zdefiniowanej przez użytkownika (UDF) w programie Apache świni i Apache Hive](../hadoop/python-udf-hdinsight.md)
