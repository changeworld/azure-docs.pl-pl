---
title: Tworzenie zadań MapReduce z usługą HDInsight - Azure przesyłania strumieniowego Python | Dokumentacja firmy Microsoft
description: Informacje o sposobie korzystania z języka Python w strumieniowym zadań MapReduce. Hadoop udostępnia interfejs API przesyłania strumieniowego dla MapReduce do pisania w językach innych niż Java.
services: hdinsight
keyword: mapreduce python,python map reduce,python mapreduce
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 7631d8d9-98ae-42ec-b9ec-ee3cf7e57fb3
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: b5e19f81c3e869347f21ab3c70a70016196b946d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400518"
---
# <a name="develop-python-streaming-mapreduce-programs-for-hdinsight"></a>Opracowywanie Python przesyłania strumieniowego programy MapReduce dla usługi HDInsight

Informacje o sposobie korzystania z języka Python w strumieniowym działania MapReduce. Hadoop udostępnia interfejs API przesyłania strumieniowego dla MapReduce, umożliwiającą zapis mapy i ograniczyć funkcje w językach innych niż Java. Kroki opisane w tym dokumencie mapy implementacji i zmniejszyć składników w języku Python.

## <a name="prerequisites"></a>Wymagania wstępne

* Opartych na systemie Linux platformą Hadoop w klastrze usługi HDInsight

  > [!IMPORTANT]
  > Kroki opisane w tym dokumencie wymagają klastra usługi HDInsight, który używa systemu Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* Edytor tekstu

  > [!IMPORTANT]
  > Edytor tekstu, należy użyć LF jako zakończenia linii. Przy użyciu wiersza koniec CRLF powoduje, że błędy podczas uruchamiania zadania MapReduce w klastrach HDInsight opartych na systemie Linux.

* `ssh` i `scp` polecenia, lub [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-3.8.0)

## <a name="word-count"></a>Liczba programu Word

W tym przykładzie jest podstawowe wyrazów zaimplementowana w języku python mapowania i reduktor. Mapowania dzieli zdania na poszczególnych wyrazów, a reduktor agreguje słowa i liczby do generowania danych wyjściowych.

Poniższy schemat przedstawia co się dzieje podczas mapy i ograniczyć faz.

![Ilustracja procesu mapreduce](./media/apache-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="streaming-mapreduce"></a>MapReduce przesyłania strumieniowego

Hadoop umożliwia Określ plik, który zawiera mapy i zmniejszyć logikę, która jest używana przez zadanie. Określone wymagania dotyczące mapy i zmniejszyć logiki są:

* **Wejściowy**: mapy i zmniejszyć składniki muszą odczytać dane wejściowe z STDIN.
* **Dane wyjściowe**: mapy i zmniejszyć składniki musi zapisać danych wyjściowych stdout.
* **Format danych**: dane używane i wyprodukowanych muszą być parę klucza i wartości, rozdzielając znak tabulacji.

Python może z łatwością obsłużyć te wymagania przy użyciu `sys` modułu do odczytu z STDIN z zastosowaniem `print` do drukowania do STDOUT. Pozostałe zadania jest po prostu formatowania danych przy użyciu karty (`\t`) znak między kluczem i wartością.

## <a name="create-the-mapper-and-reducer"></a>Utwórz mapowania i reduktor

1. Utwórz plik o nazwie `mapper.py` i użyć poniższego kodu jako zawartości:

   ```python
   #!/usr/bin/env python

   # Use the sys module
   import sys

   # 'file' in this case is STDIN
   def read_input(file):
       # Split each line into words
       for line in file:
           yield line.split()

   def main(separator='\t'):
       # Read the data using read_input
       data = read_input(sys.stdin)
       # Process each word returned from read_input
       for words in data:
           # Process each word
           for word in words:
               # Write to STDOUT
               print '%s%s%d' % (word, separator, 1)

   if __name__ == "__main__":
       main()
   ```

2. Utwórz plik o nazwie **reducer.py** i użyć poniższego kodu jako zawartości:

   ```python
   #!/usr/bin/env python

   # import modules
   from itertools import groupby
   from operator import itemgetter
   import sys

   # 'file' in this case is STDIN
   def read_mapper_output(file, separator='\t'):
       # Go through each line
       for line in file:
           # Strip out the separator character
           yield line.rstrip().split(separator, 1)

   def main(separator='\t'):
       # Read the data using read_mapper_output
       data = read_mapper_output(sys.stdin, separator=separator)
       # Group words and counts into 'group'
       #   Since MapReduce is a distributed process, each word
       #   may have multiple counts. 'group' will have all counts
       #   which can be retrieved using the word as the key.
       for current_word, group in groupby(data, itemgetter(0)):
           try:
               # For each word, pull the count(s) for the word
               #   from 'group' and create a total count
               total_count = sum(int(count) for current_word, count in group)
               # Write to stdout
               print "%s%s%d" % (current_word, separator, total_count)
           except ValueError:
               # Count was not a number, so do nothing
               pass

   if __name__ == "__main__":
       main()
   ```

## <a name="run-using-powershell"></a>Uruchamianie przy użyciu programu PowerShell

Aby upewnić się, że pliki mają prawo zakończenia wierszy, użyj następującego skryptu programu PowerShell:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=138-140)]

Poniższy skrypt programu PowerShell umożliwia przekazanie plików, uruchom zadanie i wyświetlić dane wyjściowe:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=5-134)]

## <a name="run-from-an-ssh-session"></a>Uruchom w sesji SSH

1. Ze środowiska programowania, w tym samym katalogu co `mapper.py` i `reducer.py` pliki, użyj następującego polecenia:

    ```bash
    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:
    ```

    Zastąp `username` z nazwą użytkownika SSH dla klastra, i `clustername` z nazwą klastra.

    To polecenie kopiuje pliki z systemu lokalnego do węzła głównego.

    > [!NOTE]
    > Jeśli użyto hasła, aby zabezpieczyć konto SSH, zostanie wyświetlony monit o hasło. Jeśli używasz klucza SSH, może być konieczne użycie `-i` parametru i ścieżkę do klucza prywatnego. Na przykład `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

2. Połącz się z klastrem przy użyciu protokołu SSH:

    ```bash
    ssh username@clustername-ssh.azurehdinsight.net`
    ```

    Aby uzyskać więcej informacji o zobacz [używanie SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Aby zapewnić, że mapper.py i reducer.py mają zakończenia prawidłowe wierszy, użyj następujących poleceń:

    ```bash
    perl -pi -e 's/\r\n/\n/g' mapper.py
    perl -pi -e 's/\r\n/\n/g' reducer.py
    ```

4. Użyj następującego polecenia, aby uruchomić zadanie MapReduce.

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
    ```

    To polecenie zawiera następujące części:

   * **hadoop streaming.jar**: używane podczas wykonywania operacji MapReduce przesyłania strumieniowego. Hadoop go interfejsów z kodu zewnętrznego MapReduce, podane przez użytkownika.

   * **— pliki**: Dodaje określone pliki do zadania MapReduce.

   * **-mapowania**: Określa plik, który ma być używana jako mapowania Hadoop.

   * **-Reduktor**: Określa plik, który ma być używana jako reduktor Hadoop.

   * **-wejściowych**: wejściowego, możemy należy policzyć wyrazów z.

   * **-dane wyjściowe**: katalog, w którym są zapisywane dane wyjściowe.

    Jak działa zadania MapReduce, proces jest wyświetlana jako wartości procentowe.

        05-15-02 19:01:04 informacji o mapreduce. Zadanie: 0% zmniejszyć mapy 0% 05-15-02 19:01:16 informacji o mapreduce. Zadanie: 0% zmniejszyć mapy 100% 05-15-02 19:01:27 informacji o mapreduce. Zadania: 100% zmniejszyć mapy 100%


5. Aby wyświetlić dane wyjściowe, użyj następującego polecenia:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    To polecenie wyświetla listę słów i ile razy wyraz wystąpił.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposobu używania zadań przesyłania strumieniowego MapRedcue z usługą HDInsight, użyj następujących linków, aby poznać inne sposoby pracy z usługą Azure HDInsight.

* [Korzystanie z programu Hive z usługą HDInsight](hdinsight-use-hive.md)
* [Korzystanie z języka Pig z usługą HDInsight](hdinsight-use-pig.md)
* [Korzystanie z zadań MapReduce z usługą HDInsight](hdinsight-use-mapreduce.md)
