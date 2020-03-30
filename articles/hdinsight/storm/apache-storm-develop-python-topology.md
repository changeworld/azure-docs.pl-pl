---
title: Usługa Apache Storm ze składnikami języka Python — Usługa Azure HDInsight
description: Dowiedz się, jak utworzyć topologię usługi Apache Storm, która używa składników języka Python w usłudze Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/16/2019
ms.openlocfilehash: 20e4827b1a86bff338646ef71f0dd732255c09c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77460028"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Rozwijaj topologie Apache Storm za pomocą Pythona w programie HDInsight

Dowiedz się, jak utworzyć topologię [Apache Storm,](https://storm.apache.org/) która używa składników języka Python. Apache Storm obsługuje wiele języków, umożliwiając nawet łączenie składników z kilku języków w jednej topologii. Struktura [Flux](https://storm.apache.org/releases/current/flux.html) (wprowadzona z Storm 0.10.0) pozwala na łatwe tworzenie rozwiązań, które używają składników Pythona.

> [!IMPORTANT]  
> Informacje zawarte w tym dokumencie zostały przetestowane przy użyciu funkcji Storm w programie HDInsight 3.6.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Burzy Apache w programie HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu portalu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) i wybierz opcję **Burza** dla **typu klastra**.

* Lokalne środowisko programistyczne Burza (opcjonalnie). Lokalne środowisko Storm jest potrzebne tylko wtedy, gdy chcesz uruchomić topologię lokalnie. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowiska programistycznego](https://storm.apache.org/releases/current/Setting-up-development-environment.html).

* [Python 2.7 lub wyższy](https://www.python.org/downloads/).

* [Zestaw Java Developer Kit (JDK) w wersji 8](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) prawidłowo [zainstalowany](https://maven.apache.org/install.html) zgodnie z Apache.  Maven to system budowania projektów java.

## <a name="storm-multi-language-support"></a>Obsługa wielu języków storm

Apache Storm został zaprojektowany do pracy ze składnikami napisanymi przy użyciu dowolnego języka programowania. Składniki muszą zrozumieć, jak pracować z definicją Thrift dla Storm. W przypadku Języka Python moduł jest dostarczany w ramach projektu Apache Storm, który umożliwia łatwy interfejs z stormem. Ten moduł można [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py)znaleźć na poziomie .

Storm to proces Java, który działa na maszynie wirtualnej Java (JVM). Składniki napisane w innych językach są wykonywane jako podprocesy. Burza komunikuje się z tymi podprocesami za pomocą wiadomości JSON wysyłanych za pośrednictwem stdin/stdout. Więcej informacji na temat komunikacji między komponentami można znaleźć w dokumentacji [protokołu Multi-lang.](https://storm.apache.org/releases/current/Multilang-protocol.html)

## <a name="python-with-the-flux-framework"></a>Python z ramą Flux

Struktura Flux umożliwia definiowanie topologii storm oddzielnie od składników. Struktura Flux używa YAML do definiowania topologii Storm. Poniższy tekst jest przykładem odwoływania się do składnika Języka Python w dokumencie YAML:

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

Klasa `FluxShellSpout` jest używana do `sentencespout.py` uruchamiania skryptu, który implementuje dziobek.

Flux oczekuje, że skrypty Języka `/resources` Python znajdują się w katalogu wewnątrz pliku jar, który zawiera topologię. W tym przykładzie przechowywane są `/multilang/resources` skrypty języka Python w katalogu. Obejmuje `pom.xml` ten plik przy użyciu następującego kodu XML:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Jak wspomniano wcześniej, istnieje `storm.py` plik, który implementuje definicję Thrift dla Storm. Struktura Flux `storm.py` zawiera automatycznie, gdy projekt jest zbudowany, więc nie musisz się martwić o włączenie go.

## <a name="build-the-project"></a>Kompilowanie projektu

1. Pobierz projekt [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount)z pliku .

1. Otwórz wiersz polecenia i przejdź do `hdinsight-python-storm-wordcount-master`katalogu głównego projektu: . Wprowadź następujące polecenie:

    ```cmd
    mvn clean compile package
    ```

    To polecenie `target/WordCount-1.0-SNAPSHOT.jar` tworzy plik zawierający skompilowaną topologię.

## <a name="run-the-storm-topology-on-hdinsight"></a>Uruchamianie topologii burzy w programie HDInsight

1. Użyj [polecenia ssh,](../hdinsight-hadoop-linux-use-ssh-unix.md) aby skopiować `WordCount-1.0-SNAPSHOT.jar` plik do grupy Storm w programie HDInsight. Edytuj poniższe polecenie, zastępując clustername nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    scp target/WordCount-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Po przekazaniu pliku połącz się z klastrem za pomocą funkcji SSH:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. W sesji SSH użyj następującego polecenia, aby uruchomić topologię w klastrze:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

    Po uruchomieniu topologia burzy działa do momentu zatrzymania.

1. Użyj interfejsu użytkownika burzy, aby wyświetlić topologię w klastrze. Interfejs burzowy znajduje `https://CLUSTERNAME.azurehdinsight.net/stormui`się pod adresem . Zamień `CLUSTERNAME` na nazwę klastra.

1. Zatrzymaj topologię Burzy. Użyj następującego polecenia, aby zatrzymać topologię w klastrze:

    ```bash
    storm kill wordcount
    ```

    Alternatywnie można użyć interfejsu użytkownika burzy. W obszarze **Akcje topologii** dla topologii wybierz pozycję **Zabij**.

## <a name="run-the-topology-locally"></a>Uruchamianie topologii lokalnie

Aby uruchomić topologię lokalnie, użyj następującego polecenia:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> To polecenie wymaga lokalnego środowiska programistycznego Storm. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowiska programistycznego](https://storm.apache.org/releases/current/Setting-up-development-environment.html).

Po uruchomieniu topologii emituje informacje do konsoli lokalnej podobne do następującego tekstu:

```output
24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}
```

Aby zatrzymać topologię, użyj __klawiszy Ctrl + C__.

## <a name="next-steps"></a>Następne kroki

Zobacz następujące dokumenty, aby uzyskać inne sposoby używania języka Python z funkcją HDInsight: [Jak korzystać z funkcji zdefiniowanych przez użytkownika języka Python (UDF) w aplikacji Apache Pig i Apache Hive](../hadoop/python-udf-hdinsight.md).
