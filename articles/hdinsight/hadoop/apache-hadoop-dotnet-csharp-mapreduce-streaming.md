---
title: Za pomocą języka C# MapReduce na platformie Hadoop w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać języka C# do tworzenia rozwiązań MapReduce z Hadoop w usłudze Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d83def76-12ad-4538-bb8e-3ba3542b7211
ms.custom: hdinsightactive
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 7287972ccf63f33a8cf08065f8d5d30ee1b1afb5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400881"
---
# <a name="use-c-with-mapreduce-streaming-on-hadoop-in-hdinsight"></a>Za pomocą języka C# MapReduce, przesyłanie strumieniowe na platformie Hadoop w usłudze HDInsight

Dowiedz się, jak używać języka C# do tworzenia rozwiązań MapReduce w usłudze HDInsight.

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz [przechowywanie wersji składnika usługi HDInsight](../hdinsight-component-versioning.md).

Przesyłanie strumieniowe Hadoop to narzędzie, które umożliwia uruchamianie zadań MapReduce przy użyciu skryptu lub pliku wykonywalnego. W tym przykładzie .NET jest używane do implementowania mapowania i reduktor liczba rozwiązania programu word.

## <a name="net-on-hdinsight"></a>.NET w usłudze HDInsight

__HDInsight opartych na systemie Linux__ klastrów użyj [Mono (https://mono-project.com) ](https://mono-project.com) na uruchamianie aplikacji .NET. Wersja mono 4.2.1 jest uwzględniona w usłudze HDInsight w wersji 3,6. Aby uzyskać więcej informacji w wersji Mono dołączone do usługi HDInsight, zobacz [wersji składnika usługi HDInsight](../hdinsight-component-versioning.md). Aby użyć określonej wersji Mono, zobacz [instalacji lub aktualizacji Mono](../hdinsight-hadoop-install-mono.md) dokumentu.

Aby uzyskać więcej informacji na Mono zgodność z wersji systemu .NET Framework, zobacz [Mono zgodności](http://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Jak działa przesyłanie strumieniowe Hadoop

Podstawowy proces używany do przesyłania strumieniowego w tym dokumencie jest następujący:

1. Hadoop przekazuje dane do mapowania (mapper.exe w tym przykładzie) na STDIN.
2. Mapowania przetwarza danych i emituje pary klucz wartość tabulacji stdout.
3. Dane wyjściowe odczytywane przez Hadoop, a następnie przekazywane do reduktor (reducer.exe w tym przykładzie) na STDIN.
4. Reduktor odczytuje pary klucz wartość znaki tabulacji, przetwarza dane, a następnie emituje wynik jako pary klucz wartość tabulacji ze strumienia STDOUT.
5. Dane wyjściowe są odczytywane przez Hadoop i zapisywane do katalogu wyjściowego.

Aby uzyskać więcej informacji dotyczących przesyłania strumieniowego, zobacz [przesyłania strumieniowego usługi Hadoop (https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html)](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Wymagania wstępne

* Znajomość pisania i kompilowania kodu C#, przeznaczonego dla programu .NET Framework 4.5. Kroki opisane w tym dokumencie, użyj programu Visual Studio 2017 r.

* Sposób, aby przekazać pliki .exe do klastra. Kroki opisane w tym dokumencie użyj narzędzi Data Lake Tools dla programu Visual Studio, aby przekazać pliki do podstawowy magazyn dla klastra.

* Program Azure PowerShell lub klient SSH.

* Hadoop w klastrze usługi HDInsight. Aby uzyskać więcej informacji na temat tworzenia klastra, zobacz [tworzenia klastra usługi HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-the-mapper"></a>Utwórz mapowania

W programie Visual Studio Utwórz nową __aplikacja Konsolowa__ o nazwie __mapowania__. Dla aplikacji, należy użyć poniższego kodu:

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

Po utworzeniu aplikacji, skompiluj go, aby utworzyć `/bin/Debug/mapper.exe` pliku w katalogu projektu.

## <a name="create-the-reducer"></a>Utwórz reduktor

W programie Visual Studio Utwórz nową __aplikacja Konsolowa__ o nazwie __reduktor__. Dla aplikacji, należy użyć poniższego kodu:

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

Po utworzeniu aplikacji, skompiluj go, aby utworzyć `/bin/Debug/reducer.exe` pliku w katalogu projektu.

## <a name="upload-to-storage"></a>Przekaż do magazynu

1. W programie Visual Studio Otwórz **Eksploratora serwera**.

2. Rozwiń węzeł **Azure**, a następnie rozwiń węzeł **HDInsight**.

3. Jeśli zostanie wyświetlony monit, wprowadź swoje poświadczenia subskrypcji platformy Azure, a następnie kliknij przycisk **logowania**.

4. Rozwiń węzeł klastra usługi HDInsight, którą chcesz wdrożyć tę aplikację. Wpis z tekstem __(domyślne konto magazynu)__ ma na liście.

    ![Konta magazynu dla klastra przedstawiający Eksploratora serwera](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/storage.png)

    * Jeśli tego wpisu można rozszerzać, używasz __konta magazynu Azure__ jako domyślny magazyn dla klastra. Aby wyświetlić pliki na domyślny magazyn dla klastra, rozwiń pozycję, a następnie kliknij dwukrotnie __(domyślny kontener)__.

    * Jeśli tego wpisu nie można rozwijać, używasz __Azure Data Lake Store__ jako domyślny magazyn dla klastra. Aby wyświetlić pliki na domyślny magazyn dla klastra, kliknij dwukrotnie __(domyślne konto magazynu)__ wpisu.

5. Aby przekazać pliki .exe, użyj jednej z następujących metod:

    * Jeśli przy użyciu __konta magazynu Azure__, kliknij ikonę przekazywania, a następnie przejdź do **bin\debug** folder **mapowania** projektu. Na koniec wybierz **mapper.exe** plik i kliknij przycisk **Ok**.

        ![Przekaż ikonę](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/upload.png)
    
    * Jeśli przy użyciu __Azure Data Lake Store__, kliknij prawym przyciskiem myszy pusty obszar na liście plików, a następnie wybierz __przekazać__. Na koniec wybierz **mapper.exe** plik i kliknij przycisk **Otwórz**.

    Raz __mapper.exe__ przekazywania została ukończona, powtórz proces przekazywania __reducer.exe__ pliku.

## <a name="run-a-job-using-an-ssh-session"></a>Uruchom zadanie: przy użyciu sesji SSH

1. Używanie protokołu SSH, aby połączyć się z klastrem usługi HDInsight. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Użyj jednej z poniższych poleceń, aby uruchomienie zadania MapReduce:

    * Jeśli przy użyciu __usługi Data Lake Store__ jako domyślnego magazynu:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files adl:///mapper.exe,adl:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
        ```
    
    * Jeśli przy użyciu __usługi Azure Storage__ jako domyślnego magazynu:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files wasb:///mapper.exe,wasb:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
        ```

    Na poniższej liście opisano, co oznacza każdego parametru:

    * `hadoop-streaming.jar`: Plik jar zawierający funkcje przesyłania strumieniowego MapReduce.
    * `-files`: Dodaje `mapper.exe` i `reducer.exe` plików do tego zadania. `adl:///` Lub `wasb:///` przed każdego pliku jest ścieżką do katalogu głównego domyślny magazyn dla klastra.
    * `-mapper`: Określa plik, który implementuje usługę mapowania.
    * `-reducer`: Określa plik, który implementuje reduktor.
    * `-input`: Dane wejściowe.
    * `-output`: Katalog wyjściowy.

3. Po zakończeniu zadania MapReduce, należy użyć następującego, aby wyświetlić wyniki:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Przykładem danych zwróconych przez to polecenie jest następujący tekst:

        you     1128
        young   38
        younger 1
        youngest        1
        your    338
        yours   4
        yourself        34
        yourselves      3
        youth   17

## <a name="run-a-job-using-powershell"></a>Uruchom zadanie: przy użyciu programu PowerShell

Poniższy skrypt programu PowerShell umożliwia uruchomienie zadania MapReduce i pobierania wyników.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Ten skrypt wyświetla monit o podanie nazwy konta logowania klastra i hasła, wraz z nazwą klastra usługi HDInsight. Po zakończeniu zadania, dane wyjściowe zostanie pobrana do pliku o nazwie `output.txt`. Następujący tekst jest przykładem dane w `output.txt` pliku:

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

Aby uzyskać więcej informacji na temat używania MapReduce z usługą HDInsight, zobacz [Użyj MapReduce z usługą HDInsight](hdinsight-use-mapreduce.md).

Aby uzyskać informacje o używaniu języka C# z Hive i Pig, zobacz [C# — funkcja zdefiniowana przez użytkownika za pomocą technologii Hive i Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Aby uzyskać informacje o używaniu języka C# z systemu Storm w usłudze HDInsight, zobacz [topologii opracowywania C# dla Storm w usłudze HDInsight](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
