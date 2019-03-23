---
title: Opracowywanie zadań MapReduce z HDInsight — Azure przesyłania strumieniowego w języku Python
description: Dowiedz się, jak używać języka Python w transmisji strumieniowej zadań MapReduce. Apache Hadoop udostępnia interfejs API przesyłania strumieniowego dla MapReduce do pisania w językach innych niż Java.
services: hdinsight
keyword: mapreduce python,python map reduce,python mapreduce
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: 95a78fafcd83e6f56f26ef6670beaa179085de62
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359584"
---
# <a name="develop-python-streaming-mapreduce-programs-for-hdinsight"></a>Opracowywanie programów MapReduce przesyłania strumieniowego HDInsight w języku Python

Dowiedz się, jak używać języka Python w transmisji strumieniowej działania MapReduce. Apache Hadoop udostępnia interfejs API przesyłania strumieniowego dla MapReduce, która pozwala na zapis mapy i zmniejszyć funkcji w językach innych niż Java. Kroki opisane w tym dokumencie zaimplementować mapy i zmniejszyć składniki w języku Python.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Apache Hadoop oparte na systemie Linux w klastrze HDInsight

  > [!IMPORTANT]
  > Procedura przedstawiona w tym dokumencie wymaga klastra usługi HDInsight używającego systemu Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* Edytor tekstu

  > [!IMPORTANT]
  > Edytor tekstu, należy użyć LF, jako koniec wiersza. Za pomocą rsza z CRLF powoduje, że błędy podczas uruchamiania zadania MapReduce w klastrach HDInsight opartych na systemie Linux.

* `ssh` i `scp` poleceń lub [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)

## <a name="word-count"></a>Liczba słów

W tym przykładzie jest to liczba wyrazów podstawowe zaimplementowane w języku python mapowania i reduktor. Mapowania dzieli zdania na poszczególnych wyrazów i reduktor agreguje wyrazy i liczby w celu wygenerowania danych wyjściowych.

Poniższy schemat przedstawia co się dzieje podczas mapy i zmniejszyć faz.

![Ilustracja przedstawiająca proces mapreduce](./media/apache-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="streaming-mapreduce"></a>MapReduce przesyłania strumieniowego

Hadoop umożliwia określenie pliku, która zawiera mapę i zmniejszyć logikę, która jest używana przez zadanie. Określone wymagania, mapy i zmniejszyć logiki są:

* **Dane wejściowe**: Mapy i zmniejszyć składników musi odczytać dane wejściowe z STDIN.
* **Dane wyjściowe**: Mapy i zmniejszyć składników musi zapisać dane wyjściowe do STDOUT.
* **Format danych**: Dane używane i generowane muszą być parę klucz wartość oddzielone znak tabulacji.

Python można w łatwy sposób spełniaj wymagania w zakresie za pomocą `sys` modułu do odczytu z STDIN z zastosowaniem `print` wydrukowany do strumienia wyjściowego STDOUT. Pozostałe zadania po prostu formatuje dane z zakładką (`\t`) znak między kluczem i wartością.

## <a name="create-the-mapper-and-reducer"></a>Tworzenie mapowania i reduktor

1. Utwórz plik o nazwie `mapper.py` i użyj następującego kodu jako zawartości:

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

2. Utwórz plik o nazwie **reducer.py** i użyj następującego kodu jako zawartości:

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

Aby upewnić się, że Twoje pliki mają prawo zakończenia wierszy, użyj następującego skryptu programu PowerShell:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=138-140)]

Poniższy skrypt programu PowerShell umożliwia przekazywanie plików, uruchom zadanie i wyświetlić dane wyjściowe:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=5-134)]

## <a name="run-from-an-ssh-session"></a>Uruchom w sesji SSH

1. W środowisku deweloperskim, w tym samym katalogu co `mapper.py` i `reducer.py` plików, użyj następującego polecenia:

    ```bash
    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:
    ```

    Zastąp `username` z nazwą użytkownika protokołu SSH dla klastra, a `clustername` nazwą klastra.

    To polecenie kopiuje pliki z systemu lokalnego do węzła głównego.

    > [!NOTE]  
    > Jeśli do zabezpieczenia konta SSH użyto hasła, zostanie wyświetlony monit o hasło. Jeśli używasz klucza SSH, może zajść potrzeba użycia `-i` parametru i ścieżkę do klucza prywatnego. Na przykład `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

2. Połącz się z klastrem przy użyciu protokołu SSH:

    ```bash
    ssh username@clustername-ssh.azurehdinsight.net`
    ```

    Aby uzyskać więcej informacji na temat, zobacz [użycia protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Aby upewnić się, że mapper.py i reducer.py mają poprawne końce, użyj następujących poleceń:

    ```bash
    perl -pi -e 's/\r\n/\n/g' mapper.py
    perl -pi -e 's/\r\n/\n/g' reducer.py
    ```

4. Użyj następującego polecenia, aby uruchomić zadanie MapReduce.

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
    ```

    To polecenie ma następujące elementy:

   * **hadoop-streaming.jar**: Używany podczas wykonywania operacji usługi przesyłania strumieniowego MapReduce. Za pomocą kodu zewnętrznego MapReduce, których udzielasz go interfejsy usługi Hadoop.

   * **-pliki**: Dodaje określone pliki do zadania MapReduce.

   * **-mapowania**: Informuje usługi Hadoop, plik, który do użycia jako usługę mapowania.

   * **-reducer**: Informuje, plik, który do użycia jako reduktor usługi Hadoop.

   * **-wejściowych**: Plik wejściowy powinien zliczamy wyrazów.

   * **-dane wyjściowe**: Katalog, w którym są zapisywane dane wyjściowe.

     Jak działa zadanie MapReduce, proces jest wyświetlana jako wartości procentowe.

        05-15/02 19:01:04 mapreduce informacje. Zadanie: % 0 mapy zmniejszyć 0% 05-15/02 19:01:16 mapreduce informacje. Zadanie: mapy 100% zmniejszyć 0% 05-15-02 19:01:27 mapreduce informacje. Zadanie: mapy 100% zmniejszyć 100%


5. Aby wyświetlić dane wyjściowe, użyj następującego polecenia:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    To polecenie wyświetla listę słów i ile razy wyraz wystąpił.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak używać zadań przesyłania strumieniowego MapRedcue z HDInsight, użyj następujących linków, aby poznać inne sposoby pracy z usługi Azure HDInsight.

* [Use Apache Hive z HDInsight](hdinsight-use-hive.md)
* [Apache Pig za pomocą HDInsight](hdinsight-use-pig.md)
* [Korzystanie z zadań MapReduce z usługą HDInsight](hdinsight-use-mapreduce.md)
