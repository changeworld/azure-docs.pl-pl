---
title: Używanie języka C# za pomocą technologii MapReduce w usłudze Hadoop w HDInsight — Azure
description: Dowiedz się, jak używać C# do tworzenia rozwiązań MapReduce z usługą Apache Hadoop w usłudze Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: 0c3525b73560d3edee5bffa7a391fcedeaaa1e48
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545098"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Użyj C# o MapReduce, przesyłanie strumieniowe na technologii Apache Hadoop w HDInsight

Dowiedz się, jak używać języka C# do tworzenia rozwiązania MapReduce na HDInsight.

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz [przechowywanie wersji składnika HDInsight](../hdinsight-component-versioning.md).

Przesyłanie strumieniowe Apache Hadoop to narzędzie, które umożliwia uruchomienie zadania MapReduce, za pomocą skryptu lub pliku wykonywalnego. W tym przykładzie .NET jest używane do implementowania mapowania i reduktor liczba rozwiązania programu word.

## <a name="net-on-hdinsight"></a>.NET na HDInsight

__HDInsight opartych na systemie Linux__ klastrów użyj [Mono (https://mono-project.com) ](https://mono-project.com) do uruchamiania aplikacji .NET. Wersja platformy mono 4.2.1 jest dołączony do HDInsight w wersji 3.6. Aby uzyskać więcej informacji na wersję platformy Mono dołączone HDInsight, zobacz [wersje składników HDInsight](../hdinsight-component-versioning.md). 

Aby uzyskać więcej informacji na temat zgodności platformy Mono z wersji systemu .NET Framework, zobacz [zgodności platformy Mono](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Jak działa przesyłanie strumieniowe usługi Hadoop

Podstawowy proces używany do przesyłania strumieniowego w tym dokumencie jest następująca:

1. Hadoop przekazuje dane do mapowania (mapper.exe w tym przykładzie) na STDIN.
2. Mapowania przetwarza dane i emituje pary klucz/wartość rozdzielane tabulatorami, stdout.
3. Dane wyjściowe jest odczytywane przez usługi Hadoop, a następnie przekazywane do reduktor (reducer.exe w tym przykładzie) na STDIN.
4. Reduktor odczytuje pary klucz/wartość rozdzielane tabulatorami, przetwarza dane i następnie emituje wynik w postaci par kluczy/wartości rozdzielane tabulatorami ze strumienia STDOUT.
5. Dane wyjściowe są odczytywane przez usługi Hadoop i zapisywane do katalogu wyjściowego.

Aby uzyskać więcej informacji na temat przesyłania strumieniowego, zobacz [przesyłania strumieniowego usługi Hadoop](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Wymagania wstępne

* Znajomość pisanie i kompilowanie kodu C#, który jest przeznaczony dla .NET Framework 4.5. W krokach w tym dokumencie używane programu Visual Studio 2017.

* Sposób przekazywania plików .exe do klastra. Kroki opisane w tym dokumencie użyj narzędzi Data Lake Tools for Visual Studio, aby przekazać pliki do podstawowy magazyn dla klastra.

* Program Azure PowerShell lub klienta SSH.

* Usługi Hadoop w klastrze HDInsight. Aby uzyskać więcej informacji na temat tworzenia klastra, zobacz [Tworzenie klastra usługi HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-the-mapper"></a>Tworzenie mapowania

W programie Visual Studio Utwórz nowy __aplikacji konsolowej programu__ o nazwie __mapowania__. Użyj poniższego kodu dla aplikacji:

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

Po utworzeniu aplikacji, skompiluj go, aby wygenerować `/bin/Debug/mapper.exe` pliku w katalogu projektu.

## <a name="create-the-reducer"></a>Utwórz reduktor

W programie Visual Studio Utwórz nowy __aplikacji konsolowej programu__ o nazwie __reduktor__. Użyj poniższego kodu dla aplikacji:

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

Po utworzeniu aplikacji, skompiluj go, aby wygenerować `/bin/Debug/reducer.exe` pliku w katalogu projektu.

## <a name="upload-to-storage"></a>Przekazywanie do magazynu

1. W programie Visual Studio, otwórz **Eksploratora serwera**.

2. Rozwiń węzeł **Azure**, a następnie rozwiń węzeł **HDInsight**.

3. Po wyświetleniu monitu wprowadź swoje poświadczenia subskrypcji platformy Azure, a następnie kliknij przycisk **Sign In**.

4. Rozwiń węzeł klastra HDInsight, który chcesz wdrożyć tę aplikację. Wpis z tekstem __(domyślne konto magazynu)__ znajduje się na liście.

    ![Konto magazynu dla klastra Eksplorator serwera](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/storage.png)

    * Jeśli ten wpis można rozszerzać, używasz __konta usługi Azure Storage__ jako magazynem domyślnym dla klastra. Aby wyświetlić pliki na domyślny magazyn dla klastra, rozwiń pozycję, a następnie kliknij dwukrotnie __(domyślny kontener)__.

    * Jeśli tego wpisu nie może zostać rozszerzona, którego używasz __usługi Azure Data Lake Storage__ jako magazynem domyślnym dla klastra. Aby wyświetlić pliki na domyślny magazyn dla klastra, kliknij dwukrotnie __(domyślne konto magazynu)__ wpisu.

5. Aby przekazać pliki .exe, użyj jednej z następujących metod:

   * Jeśli przy użyciu __konta usługi Azure Storage__, kliknij ikonę przekazywania, a następnie przejdź do **bin\debug** folder **mapowania** projektu. Na koniec wybierz pozycję **mapper.exe** plik i kliknij przycisk **Ok**.

       ![Przekaż ikonę](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/upload.png)
    
   * Jeśli przy użyciu __usługi Azure Data Lake Storage__, kliknij prawym przyciskiem myszy pusty obszar na liście plików, a następnie wybierz __przekazywanie__. Na koniec wybierz pozycję **mapper.exe** plik i kliknij przycisk **Otwórz**.

     Gdy __mapper.exe__ przekazywanie zostało zakończone, powtórz proces przekazywania __reducer.exe__ pliku.

## <a name="run-a-job-using-an-ssh-session"></a>Uruchamianie zadania: Za pomocą sesji SSH

1. Za pomocą protokołu SSH Połącz się z klastrem HDInsight. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Aby uruchomić zadanie MapReduce, użyj jednej z następujących poleceń:

   * Jeśli przy użyciu __Data Lake Storage Gen2__ jako magazynem domyślnym:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files abfs:///mapper.exe,abfs:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

   * Jeśli przy użyciu __Data Lake Storage Gen1__ jako magazynem domyślnym:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files adl:///mapper.exe,adl:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```
    
   * Jeśli przy użyciu __usługi Azure Storage__ jako magazynem domyślnym:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files wasb:///mapper.exe,wasb:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

     Na poniższej liście opisano, jak działa każdy parametr:

   * `hadoop-streaming.jar`: Plik jar, który zawiera funkcje MapReduce przesyłania strumieniowego.
   * `-files`: Dodaje `mapper.exe` i `reducer.exe` pliki do tego zadania. `abfs:///`,`adl:///` Lub `wasb:///` przed każdego pliku jest ścieżką do katalogu głównego domyślnego magazynu klastra.
   * `-mapper`: Określa plik, który implementuje usługę mapowania.
   * `-reducer`: Określa plik, który implementuje reduktor.
   * `-input`: Dane wejściowe.
   * `-output`: Katalog wyjściowy.

3. Po zakończeniu zadania MapReduce pomocą następującego polecenia, aby wyświetlić wyniki:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Następujący tekst jest przykładem danych zwróconych przez to polecenie:

        you     1128
        young   38
        younger 1
        youngest        1
        your    338
        yours   4
        yourself        34
        yourselves      3
        youth   17

## <a name="run-a-job-using-powershell"></a>Uruchamianie zadania: Korzystanie z programu PowerShell

Poniższy skrypt programu PowerShell umożliwia uruchomienie zadania MapReduce i pobrać wyniki.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Ten skrypt wyświetli monit o podanie nazwy konta logowania klastra i hasło, wraz z nazwą klastra HDInsight. Po ukończeniu zadania, dane wyjściowe zostanie pobrana do pliku o nazwie `output.txt`. Poniższy tekst znajduje się przykład danych w `output.txt` pliku:

    you     1128
    young   38
    younger 1
    youngest        1
    your    338
    yours   4
    yourself        34
    yourselves      3
    youth   17

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji dotyczących korzystania z technologii MapReduce z usługą HDInsight, zobacz [używanie MapReduce z HDInsight](hdinsight-use-mapreduce.md).

Aby uzyskać informacje na temat korzystania z C# z technologiami Hive i Pig, zobacz [użyj C# funkcji zdefiniowanej przez użytkownika przy użyciu Apache Hive i Apache Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Aby uzyskać informacje na temat korzystania z C# przy użyciu systemu Storm, HDInsight, zobacz [programowanie C# topologii STORM Apache na HDInsight](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
