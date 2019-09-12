---
title: Używanie C# z usługą MapReduce na platformie Hadoop w usłudze HDInsight — Azure
description: Dowiedz się, C# jak używać programu do tworzenia rozwiązań MapReduce z Apache Hadoop w usłudze Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: 7f82ad65ecc805d5a45c78e8b190dd0eee4c340c
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70882343"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Używanie C# z usługą MapReduce streaming na Apache Hadoop w usłudze HDInsight

Dowiedz się, C# jak za pomocą programu utworzyć rozwiązanie MapReduce w usłudze HDInsight.

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz [przechowywanie wersji składników usługi HDInsight](../hdinsight-component-versioning.md).

Apache Hadoop Streaming to narzędzie, które umożliwia uruchamianie zadań MapReduce przy użyciu skryptu lub pliku wykonywalnego. W tym przykładzie platforma .NET służy do implementowania mapowania i redukcji dla rozwiązania zliczania wyrazów.

## <a name="net-on-hdinsight"></a>Platforma .NET w usłudze HDInsight

Klastry __HDInsight oparte na systemie Linux__ używają systemu [https://mono-project.com) mono (](https://mono-project.com) do uruchamiania aplikacji .NET. W usłudze HDInsight w wersji 3,6 jest dołączony system mono w wersji. Aby uzyskać więcej informacji na temat wersji programu mono zawartej w usłudze HDInsight, zobacz [wersje składników usługi HDInsight](../hdinsight-component-versioning.md). 

Aby uzyskać więcej informacji na temat zgodności z programem mono z wersjami .NET Framework, zobacz Zgodność z usługą [mono](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Jak działa przesyłanie strumieniowe Hadoop

Podstawowy proces używany do przesyłania strumieniowego w tym dokumencie jest następujący:

1. Usługa Hadoop przekazuje dane do funkcji mapowania (mapera. exe w tym przykładzie) na STDIN.
2. Maper przetwarza dane i emituje rozdzielaną tabulatorami pary klucz/wartość do STDOUT.
3. Dane wyjściowe są odczytywane przez platformę Hadoop, a następnie przekazywane do usługi w ramach tego przykładu.
4. W ramach tego ograniczenia są odczytywane pary klucz/wartość rozdzielane znakami tabulacji, przetwarzanie danych, a następnie emitowanie wyniku jako pary klucz/wartość z ograniczeniami tabulacji.
5. Dane wyjściowe są odczytywane przez platformę Hadoop i zapisywane w katalogu wyjściowym.

Aby uzyskać więcej informacji na temat przesyłania strumieniowego, zobacz Usługa [przesyłania strumieniowego Hadoop](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Wymagania wstępne

* Znajomość pisania i kompilowania C# kodu, który jest przeznaczony dla .NET Framework 4,5. W procedurach przedstawionych w tym dokumencie użyto programu Visual Studio 2017.

* Sposób przekazywania plików exe do klastra. Kroki opisane w tym dokumencie wykorzystują Data Lake Tools for Visual Studio do przekazywania plików do magazynu podstawowego klastra.

* Azure PowerShell lub klient SSH.

* Usługa Hadoop w klastrze usługi HDInsight. Aby uzyskać więcej informacji na temat tworzenia klastra, zobacz [Tworzenie klastra usługi HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-the-mapper"></a>Tworzenie mapowania

W programie Visual Studio Utwórz nową __aplikację konsolową__ o nazwie __Maper__. Użyj następującego kodu dla aplikacji:

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

Po utworzeniu aplikacji Skompiluj ją, aby utworzyć `/bin/Debug/mapper.exe` plik w katalogu projektu.

## <a name="create-the-reducer"></a>Utwórz ograniczenie

W programie Visual Studio Utwórz nową __aplikację konsolową__ o nazwie __redukcja__. Użyj następującego kodu dla aplikacji:

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

Po utworzeniu aplikacji Skompiluj ją, aby utworzyć `/bin/Debug/reducer.exe` plik w katalogu projektu.

## <a name="upload-to-storage"></a>Przekazywanie do magazynu

1. W programie Visual Studio Otwórz **Eksplorator serwera**.

2. Rozwiń węzeł **Azure**, a następnie rozwiń węzeł **HDInsight**.

3. Jeśli zostanie wyświetlony monit, wprowadź swoje poświadczenia subskrypcji platformy Azure, a następnie kliknij przycisk **Zaloguj**.

4. Rozwiń klaster usługi HDInsight, do którego chcesz wdrożyć tę aplikację. Zostanie wyświetlona pozycja z tekstem __(domyślne konto magazynu)__ .

    ![Eksplorator serwera wyświetlania konta magazynu dla klastra](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

    * Jeśli ten wpis można rozszerzyć, używasz __konta usługi Azure Storage__ jako magazynu domyślnego dla klastra. Aby wyświetlić pliki w domyślnym magazynie klastra, rozwiń wpis, a następnie kliknij dwukrotnie __(kontener domyślny)__ .

    * Jeśli tego wpisu nie można rozszerzyć, używany jest __Azure Data Lake Storage__ jako magazyn domyślny dla klastra. Aby wyświetlić pliki w domyślnym magazynie klastra, kliknij dwukrotnie wpis __(domyślne konto magazynu)__ .

5. Aby przekazać pliki. exe, należy użyć jednej z następujących metod:

   * Jeśli używasz __konta usługi Azure Storage__, kliknij ikonę Przekaż, a następnie przejdź do folderu **bin\Debug** dla projektu **mapowania** . Na koniec wybierz plik **mapera. exe** i kliknij przycisk **OK**.

        ![ikona przekazywania](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)
    
   * Jeśli używasz __Azure Data Lake Storage__, kliknij prawym przyciskiem myszy pusty obszar na liście plików, a następnie wybierz polecenie __Przekaż__. Na koniec wybierz plik **Maper. exe** , a następnie kliknij przycisk **Otwórz**.

     Po zakończeniu przekazywania pliku __mapowania. exe__ należy powtórzyć proces przekazywania dla plików. __exe__ .

## <a name="run-a-job-using-an-ssh-session"></a>Uruchom zadanie: Korzystanie z sesji SSH

1. Połącz się z klastrem usługi HDInsight przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Użyj jednego z następujących poleceń, aby uruchomić zadanie MapReduce:

   * Jeśli używasz __Data Lake Storage Gen2__ jako magazynu domyślnego:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files abfs:///mapper.exe,abfs:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

   * Jeśli używasz __Data Lake Storage Gen1__ jako magazynu domyślnego:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files adl:///mapper.exe,adl:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```
    
   * W przypadku korzystania z __usługi Azure Storage__ jako magazynu domyślnego:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files wasb:///mapper.exe,wasb:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

     Na poniższej liście opisano działania poszczególnych parametrów:

   * `hadoop-streaming.jar`: Plik JAR, który zawiera funkcje MapReduce przesyłania strumieniowego.
   * `-files`: Dodaje pliki `reducer.exe` i do tego zadania. `mapper.exe` `abfs:///` Lubprzedkażdyplikjestścieżkądokatalogu`wasb:///` głównego domyślnego magazynu klastra.`adl:///`
   * `-mapper`: Określa, który plik implementuje mapowanie.
   * `-reducer`: Określa, który plik implementuje zmniejszenie.
   * `-input`: Dane wejściowe.
   * `-output`: Katalog wyjściowy.

3. Po zakończeniu zadania MapReduce Użyj następujących danych, aby wyświetlić wyniki:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Następujący tekst to przykład danych zwróconych przez to polecenie:

        you     1128
        young   38
        younger 1
        youngest        1
        your    338
        yours   4
        yourself        34
        yourselves      3
        youth   17

## <a name="run-a-job-using-powershell"></a>Uruchom zadanie: Korzystanie z programu PowerShell

Użyj poniższego skryptu programu PowerShell, aby uruchomić zadanie MapReduce i pobrać wyniki.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Ten skrypt poprosi o nazwę i hasło konta logowania do klastra wraz z nazwą klastra usługi HDInsight. Po zakończeniu zadania dane wyjściowe zostaną pobrane do pliku o nazwie `output.txt`. Następujący tekst to przykład danych w `output.txt` pliku:

    you     1128
    young   38
    younger 1
    youngest        1
    your    338
    yours   4
    yourself        34
    yourselves      3
    youth   17

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat korzystania z usługi MapReduce z usługą HDInsight, zobacz [Używanie MapReduce z usługą HDInsight](hdinsight-use-mapreduce.md).

Aby uzyskać informacje na C# temat korzystania z platformy Hive i świni, zobacz [Używanie funkcji zdefiniowanej przez C# użytkownika z usługami Apache Hive i Apache świni](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Aby uzyskać informacje na C# temat korzystania z funkcji burzy w usłudze HDInsight, zobacz [ C# tworzenie topologii dla Apache Storm w usłudze HDInsight](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
