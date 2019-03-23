---
title: Uruchamianie przykładów dla usługi Hadoop w HDInsight — Azure
description: Rozpocznij pracę przy użyciu usługi Azure HDInsight przy użyciu przykłady przekazane. Użycie skryptów programu PowerShell, których uruchamianie programów MapReduce w klastrach danych.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 3d27a9cc8dd4b460a75e2a43106413ed9ee1d559
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361527"
---
# <a name="run-mapreduce-samples-in-windows-based-hdinsight"></a>Uruchamianie przykładów technologii MapReduce w HDInsight z systemem Windows
[!INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Aby ułatwić wprowadzenie uruchomionych zadań MapReduce w klastrach usługi Apache Hadoop przy użyciu usługi Azure HDInsight jest dostępny zestaw przykładów. Te przykłady są udostępniane na każdym z klastrów HDInsight zarządzane, które tworzysz. Uruchamianie tych przykładów zapoznanie się z przy użyciu poleceń cmdlet programu Azure PowerShell do uruchamiania zadań w klastrach usługi Hadoop.

* [**Liczby słów**][hdinsight-sample-wordcount]: Zlicza wystąpienia wyrazów w pliku tekstowym.
* [**C#przesyłanie strumieniowe wyrazów**][hdinsight-sample-csharp-streaming]: Zlicza wystąpienia wyrazów w pliku tekstowym za pomocą interfejsu przesyłania strumieniowego usługi Hadoop.
* [**Estymatora liczby pi**][hdinsight-sample-pi-estimator]: Używa statystycznego (quasi Monte Carlo) metodę, aby oszacować wartość liczby pi.
* [**10 GB Graysort**][hdinsight-sample-10gb-graysort]: Uruchom GraySort ogólnego przeznaczenia w pliku o rozmiarze 10 GB, przy użyciu HDInsight. Istnieją trzy zadania do uruchomienia: Teragen do generowania danych Terasort, aby posortować dane, a Teravalidate, aby upewnić się, czy dane zostały prawidłowo posortowane.

> [!NOTE]  
> Kod źródłowy można znaleźć w dodatku.

Znacznie dodatkową dokumentację istnieje w sieci web w technologii związanych z usługi Hadoop, takich jak Programowanie oparte na języku Java MapReduce i przesyłania strumieniowego oraz dokumentacji dotyczącej poleceń cmdlet, które są używane w programie Windows PowerShell skryptów. Aby uzyskać więcej informacji na temat tych zasobów Zobacz:

* [Opracowywanie programów MapReduce w języku Java dla usługi Apache Hadoop w HDInsight](hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Przesyłanie zadań usługi Apache Hadoop w HDInsight](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Wprowadzenie do usługi Azure HDInsight][hdinsight-introduction]

Dzisiaj wiele osób wybiera Apache Hive i Apache Pig za pośrednictwem MapReduce.  Aby uzyskać więcej informacji, zobacz:

* [Use Apache Hive w HDInsight](hadoop/hdinsight-use-hive.md)
* [Use Apache Pig w HDInsight](hadoop/hdinsight-use-pig.md)

**Wymagania wstępne**:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Klaster usługi HDInsight**. Aby uzyskać instrukcje na różne sposoby, w których można utworzyć takie klastry, zobacz [klastrów utworzyć Apache Hadoop w HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* **Stacja robocza z programem Azure PowerShell**.

    > [!IMPORTANT]  
    > Obsługa środowiska Azure PowerShell do celów zarządzania zasobami usługi HDInsight przy użyciu usługi Azure Service Manager jest **przestarzała** i zostanie usunięta z dniem 1 stycznia 2017 r. W czynnościach opisanych w niniejszym dokumencie są używane nowe polecenia cmdlet usługi HDInsight współpracujące z usługą Azure Resource Manager.
    >
    > Postępuj zgodnie z instrukcjami w [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azureps-cmdlets-docs) Aby zainstalować najnowszą wersję programu Azure PowerShell. Jeśli masz skrypty wymagające modyfikacji w celu użycia nowych poleceń cmdlet współpracujących z usługi Azure Resource Manager, zobacz [Migrowanie do narzędzi programistycznych opartych na usłudze Azure Resource Manager w celu obsługi klastrów HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="hdinsight-sample-wordcount"></a>Word licznik — Java
Aby przesłać projektu MapReduce, należy najpierw utworzyć definicję zadania MapReduce. W definicji zadania należy określić plik jar programu MapReduce i lokalizację pliku jar, który jest **wasb:///example/jars/hadoop-mapreduce-examples.jar**, nazwa klasy i argumentów.  Uruchamianie programu MapReduce wordcount przyjmuje dwa argumenty: plik źródłowy, który służy do zliczania wyrazów i lokalizację danych wyjściowych.

Kod źródłowy znajduje się w dodatku A.

Procedura tworzenia MapReduce w języku Java programowania, zobacz - [programów MapReduce Programowanie w języku Java dla usługi Apache Hadoop w HDInsight](hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)

**Aby przesłać zadanie MapReduce liczba programu word**

1. Otwórz **środowiska Windows PowerShell ISE**. Aby uzyskać instrukcje, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell][powershell-install-configure].
2. Wklej poniższy skrypt programu PowerShell:

    ```powershell
    $subscriptionName = "<Azure Subscription Name>"
    $resourceGroupName = "<Resource Group Name>"
    $clusterName = "<HDInsight cluster name>"             # HDInsight cluster name

    Select-AzSubscription -SubscriptionName $subscriptionName

    # Define the MapReduce job
    $mrJobDefinition = New-AzHDInsightMapReduceJobDefinition `
                                -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "wordcount" `
                                -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    # Submit the job and wait for job completion
    $cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:"
    $mrJob = Start-AzHDInsightJob `
                        -ResourceGroupName $resourceGroupName `
                        -ClusterName $clusterName `
                        -HttpCredential $cred `
                        -JobDefinition $mrJobDefinition

    Wait-AzHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -HttpCredential $cred `
        -JobId $mrJob.JobId

    # Get the job output
    $cluster = Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
    $defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
    $defaultStorageContainer = $cluster.DefaultStorageContainer

    Get-AzHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -HttpCredential $cred `
        -DefaultStorageAccountName $defaultStorageAccount `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultStorageContainer  `
        -JobId $mrJob.JobId `
        -DisplayOutputType StandardError

    # Download the job output to the workstation
    $storageContext = New-AzStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey
    Get-AzStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    # Display the output file
    cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"
    ```

    Zadania MapReduce tworzy plik o nazwie *część r-00000*, który zawiera wyrazy i liczby elementów. Skrypt używa **findstr** polecenie, aby wyświetlić listę wszystkich słów, zawierający *"Brak"*.
3. Ustawianie pierwszych trzech zmiennych i uruchom skrypt.

## <a name="hdinsight-sample-csharp-streaming"></a>Word Licznik — C# przesyłania strumieniowego
Hadoop udostępnia interfejs API przesyłania strumieniowego do MapReduce, co pozwala na zapis mapy i zmniejszyć funkcji w językach innych niż Java.

> [!NOTE]  
> Te kroki w tym samouczku mają zastosowanie tylko w klastrach HDInsight z systemem Windows. Aby uzyskać przykład przesyłania strumieniowego dla klastrów HDInsight opartych na systemie Linux, zobacz [opracowywanie programów do przesyłania strumieniowego dla HDInsight języka Python](hadoop/apache-hadoop-streaming-python.md).

W przykładzie są pliki wykonywalne, które odczytują dane wejściowe z mapowania i reduktor [stdin] [ stdin-stdout-stderr] (wiersz po wierszu) i wyemituj dane wyjściowe do [stdout] [ stdin-stdout-stderr]. Program zlicza wszystkie wyrazy w tekście.

Gdy plik wykonywalny jest określona dla **liczby maperów**, każde zadanie mapowania spowoduje uruchomienie pliku wykonywalnego jako oddzielny proces po zainicjowaniu mapowania. Po uruchomieniu zadania mapowania go konwertuje dane wejściowe do wierszy, a źródła wierszy do [stdin] [ stdin-stdout-stderr] procesu.

W międzyczasie mapera służy do zbierania danych wyjściowych w wierszu ze strumienia wyjściowego stdout procesu. Konwertuje każdy wiersz w parę klucza i wartości, które są zbierane jako dane wyjściowe mapowania. Domyślnie prefiks linii maksymalnie pierwszy znak tabulacji jest kluczem, a pozostała część wiersza (z wyjątkiem znak tabulacji) to wartość. Jeśli w wierszu jest nie znak tabulacji, cały wiersz jest traktowany jako klucz, a ma wartość null.

Gdy plik wykonywalny jest określona dla **reduktorów**, każde zadanie reduktor spowoduje uruchomienie pliku wykonywalnego jako oddzielny proces po zainicjowaniu reduktor. Jak reduktor zadanie jest uruchamiane, jego pary klucza/wartości wejściowe są konwertowane na wiersze i jego źródła wierszy do [stdin] [ stdin-stdout-stderr] procesu.

W międzyczasie reduktor umożliwia zbieranie informacji o wierszu danych wyjściowych [stdout] [ stdin-stdout-stderr] procesu. Konwertuje każdy wiersz na parę klucza i wartości, które są zbierane jako dane wyjściowe reduktor. Domyślnie prefiks linii maksymalnie pierwszy znak tabulacji jest kluczem, a pozostała część wiersza (z wyjątkiem znak tabulacji) to wartość.

**Aby przesłać języku C# słowa liczba zadanie przesyłania strumieniowego**

* Postępuj zgodnie z procedurą w wyrazów — Java i Zastąp definicję zadania o następujący wiersz:

    ```powershell
    $mrJobDefinition = New-AzHDInsightStreamingMapReduceJobDefinition `
                            -Files "/example/apps/cat.exe","/example/apps/wc.exe" `
                            -Mapper "cat.exe" `
                            -Reducer "wc.exe" `
                            -InputPath "/example/data/gutenberg/davinci.txt" `
                            -OutputPath "/example/data/StreamingOutput/wc.txt"
    ```

    Plik wyjściowy jest:

        example/data/StreamingOutput/wc.txt/part-00000

## <a name="hdinsight-sample-pi-estimator"></a>Estymatora liczby PI
Statystyczne używa estymatora liczby pi (quasi Monte Carlo) metodę, aby oszacować wartość liczby pi. Punkty, w losowo wybranym momencie umieszczone wewnątrz jednostka kwadratu również mieszczą się w okręgu wpisanego w tym kwadrat z prawdopodobieństwem równa pole koła, pi/4. Wartość liczby pi można oszacować od wartości 4R, gdzie języka R to stosunek liczby punktów znajdujące się wewnątrz okręgu całkowitą liczbę punktów należących do kwadratu. Im większy próbka punkty używane jest lepiej oszacować.

Skryptu podane w tym przykładzie przesyła plik jar zadania usługi Hadoop i jest ustawiona do uruchamiania z wartością 16 mapy, z których każdy jest wymagany do obliczenia 10 milionów wybierała punkty według wartości parametru. Aby poprawić szacunkowa wartość liczby pi można zmienić te wartości parametrów. W przypadku odwołania 10 pierwszych miejsc dziesiętnych pi są 3.1415926535.

**Aby przesłać zadanie szacowania pi**

* Postępuj zgodnie z procedurą w wyrazów — Java i Zastąp definicję zadania o następujący wiersz:

    ```powershell
    $mrJobJobDefinition = New-AzHDInsightMapReduceJobDefinition `
                                -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "pi" `
                                -Arguments "16", "10000000"
    ```

## <a name="hdinsight-sample-10gb-graysort"></a>Graysort 10 GB
Ta próbka używa skromną 10 GB danych, dzięki czemu mogą być uruchamiane względnie szybko. Używa ona aplikacje MapReduce, opracowane przez Owen O'Malley oraz organizacji i Arun Murthy, który wygrał roczne porównawczych sortowania terabajt ogólnego przeznaczenia ("daytona") w 2009 wysokości 0.578 TB na minutę (100 TB w ciągu minut 173). Aby uzyskać więcej informacji na ten temat i inne wzorce sortowania, zobacz [Sortbenchmark](https://sortbenchmark.org/) lokacji.

W tym przykładzie użyto trzy rodzaje programów MapReduce:

1. **TeraGen** to program MapReduce, który służy do generowania wiersze danych do sortowania.
2. **TeraSort** próbkuje dane wejściowe i używa MapReduce, aby posortować dane w kolejności całkowitej. TeraSort jest standardowa sortowania funkcji MapReduce, z wyjątkiem niestandardowego partycjonera, który używa posortowanej listy kluczy podczas próbkowania n-1, które określają zakres kluczy dla każdego redukcji. W szczególności, wszystkie klucze takie, które [i-1] < = klucz < przykładowe [i] są wysyłane do i zmniejszyć. To gwarantuje, że dane wyjściowe zmniejszyć i są dostępne w mniej niż dane wyjściowe zmniejszyć i + 1.
3. **TeraValidate** to program MapReduce, który sprawdza, czy dane wyjściowe są sortowane globalnie. Tworzy jedną mapę dla pliku w katalogu wyjściowym i każdej mapy gwarantuje, że każdy klucz jest mniejsza niż poprzedniego. Funkcja mapy generuje również rekordów klucze imię i nazwisko każdego pliku, a funkcja redukcji gwarantuje, że pierwszy klucz w pliku jest większy niż określony ostatni klucz i-1 pliku. Wszelkie problemy są zgłaszane jako dane wyjściowe Zmniejsz przy użyciu kluczy, które są poza kolejnością.

Format wejściowy i wyjściowy używany przez wszystkie trzy aplikacje, odczytuje i zapisuje pliki tekstowe w odpowiednim formacie. Dane wyjściowe Zmniejsz ma replikacji ustawiona na 1, zamiast domyślnego 3, ponieważ konkursu testów porównawczych nie jest wymagane, czy dane wyjściowe być replikowane na wielu węzłach.

Trzy zadania są wymagane przez przykładowy każdej odpowiadającej jednego z programów MapReduce opisano we wprowadzeniu:

1. Generowanie danych do sortowania, uruchamiając **TeraGen** zadania MapReduce.
2. Sortowanie danych, uruchamiając **TeraSort** zadania MapReduce.
3. Upewnij się, że dane zostały prawidłowo posortowane, uruchamiając **TeraValidate** zadania MapReduce.

**Aby przesłać zadania**

* Postępuj zgodnie z procedurą w wyrazów — Java i użyj następujących definicji zadania:

    ```powershell
    $teragen = New-AzHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "teragen" `
                                -Arguments "-Dmapred.map.tasks=50", "100000000", "/example/data/10GB-sort-input"

    $terasort = New-AzHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "terasort" `
                                -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-input", "/example/data/10GB-sort-output"

    $teravalidate = New-AzHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "teravalidate" `
                                -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-output", "/example/data/10GB-sort-validate"
    ```

## <a name="next-steps"></a>Kolejne kroki
W tym artykule i artykuły we wszystkich przykładach przedstawiono sposób uruchamiania przykładów dołączona z klastrami HDInsight przy użyciu programu Azure PowerShell. Samouczki dotyczące przy HDInsight Pig i Hive, MapReduce zobacz następujące tematy:

* [Rozpoczynanie pracy przy użyciu technologii Apache Hadoop przy użyciu technologii Hive w HDInsight do analizowania użycia przenośnych słuchawki][hdinsight-get-started]
* [Use Apache Pig z platformą Apache Hadoop w HDInsight][hdinsight-use-pig]
* [Apache Hive za pomocą technologii Apache Hadoop w HDInsight][hdinsight-use-hive]
* [Przesyłanie zadań Hadoop Apache w HDInsight][hdinsight-submit-jobs]

## <a name="appendix-a---the-word-count-source-code"></a>Dodatek A — kod źródłowy liczba programu Word

```java
package org.apache.hadoop.examples;
import java.io.IOException;
import java.util.StringTokenizer;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

    public static class TokenizerMapper
    extends Mapper<Object, Text, Text, IntWritable>{

private final static IntWritable one = new IntWritable(1);
private Text word = new Text();

public void map(Object key, Text value, Context context
                ) throws IOException, InterruptedException {
    StringTokenizer itr = new StringTokenizer(value.toString());
    while (itr.hasMoreTokens()) {
    word.set(itr.nextToken());
    context.write(word, one);
        }
    }
    }

    public static class IntSumReducer
    extends Reducer<Text,IntWritable,Text,IntWritable> {
private IntWritable result = new IntWritable();

public void reduce(Text key, Iterable<IntWritable> values,
                    Context context
                    ) throws IOException, InterruptedException {
    int sum = 0;
    for (IntWritable val : values) {
    sum += val.get();
    }
    result.set(sum);
    context.write(key, result);
    }
    }

    public static void main(String[] args) throws Exception {
Configuration conf = new Configuration();
String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
if (otherArgs.length != 2) {
    System.err.println("Usage: wordcount <in> <out>");
    System.exit(2);
    }
Job job = new Job(conf, "word count");
job.setJarByClass(WordCount.class);
job.setMapperClass(TokenizerMapper.class);
job.setCombinerClass(IntSumReducer.class);
job.setReducerClass(IntSumReducer.class);
job.setOutputKeyClass(Text.class);
job.setOutputValueClass(IntWritable.class);
FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
    }
```

## <a name="appendix-b---the-word-count-streaming-source-code"></a>Dodatek B — liczba wyrazów, przesyłanie strumieniowe kodu źródłowego
MapReduce używana aplikacja cat.exe jako interfejs mapowania strumienia tekstu do konsoli oraz aplikacji wc.exe jako interfejs Zmniejsz liczbę wyrazów, które są przesyłane strumieniowo z dokumentu. Mapowania i reduktor znaków, wiersz po wierszu, ze standardowego strumienia wejściowego (stdin) do odczytu i zapisu strumienia wyjścia standardowego (stdout).

```csharp
// The source code for the cat.exe (Mapper).

using System;
using System.IO;

namespace cat
{
    class cat
    {
        static void Main(string[] args)
        {
            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }

            string line;
            char[] separators = { ' ', '\n'};
            while ((line = Console.ReadLine()) != null)
            {
                string[] words = line.Split(separators);
                foreach (var word in words)
                {
                    Console.WriteLine("{0}\t1", word);
                }
            }
        }
    }
}
```

Kod mapowania w pliku cat.cs używa [StreamReader] [ streamreader] obiektu do odczytu znaków strumienia przychodzącego do konsoli, który następnie zapisuje dane strumienia do strumienia wyjścia standardowego za pomocą statycznych [ Elementu Console.Writeline] [ console-writeline] metody.

```csharp
// The source code for wc.exe (Reducer) is:

using System;
using System.IO;
using System.Linq;
using System.Collections;

namespace wc
{
    class wc
    {
        static void Main(string[] args)
        {
            string line;

            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }

            Hashtable wordCount = new Hashtable();
            while ((line = Console.ReadLine()) != null)
            {
                string[] words = line.Split('\t');

                string key = words[0];

                if (wordCount.ContainsKey(key) == true)
                {
                    int n = Convert.ToInt32(wordCount[key]);
                    wordCount[key] = Convert.ToString(n + 1);
                }
                else
                {
                    wordCount[key] = words[1];
                }
            }
            foreach (var key in wordCount.Keys)
            {
                Console.WriteLine("{0} {1}", key, wordCount[key]);
            }
        }
    }
}
```

Kod reduktor w pliku wc.cs używa [StreamReader] [ streamreader] obiektu, aby odczytywać znaki ze standardowego strumienia wejściowego, że zostały danych wyjściowych przez mapowania cat.exe. Jak odczytuje znaki z [elementu Console.Writeline] [ console-writeline] metody liczy wyrazów przez liczenie spacje i znaki końca wiersza na końcu każdego wyrazu. Następnie zapisuje Suma do standardowego strumienia wyjściowego przy użyciu [elementu Console.Writeline] [ console-writeline] metody.

## <a name="appendix-c---the-pi-estimator-source-code"></a>Dodatek C - Pi kod źródłowy narzędzie do szacowania
Estymatora liczby pi kodu języka Java, który zawiera mapowania i reduktor funkcje jest dostępna dla inspekcji poniżej. Program mapowania generuje określoną liczbę punktów w losowo wybranym momencie umieszczone wewnątrz kwadratu jednostki i następnie zlicza te punkty, które znajdują się wewnątrz okręgu. Program reduktor gromadzi punkty uwzględniane przez liczby maperów i następnie szacuje wartość liczby pi z formuły 4R, gdzie języka R to stosunek liczby punktów liczone wewnątrz okręgu całkowitą liczbę punktów należących do kwadratu.

```java
/**
* Licensed to the Apache Software Foundation (ASF) under one
* or more contributor license agreements. See the NOTICE file
* distributed with this work for additional information
* regarding copyright ownership. The ASF licenses this file
* to you under the Apache License, Version 2.0 (the
* "License"); you may not use this file except in compliance
* with the License. You may obtain a copy of the License at
*
* https://www.apache.org/licenses/LICENSE-2.0
*
* Unless required by applicable law or agreed to in writing, software
* distributed under the License is distributed on an "AS IS" BASIS,
* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or     implied.
* See the License for the specific language governing permissions and
* limitations under the License.
*/

package org.apache.hadoop.examples;

import java.io.IOException;
import java.math.BigDecimal;
import java.util.Iterator;

import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.BooleanWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.SequenceFile;
import org.apache.hadoop.io.Writable;
import org.apache.hadoop.io.WritableComparable;
import org.apache.hadoop.io.SequenceFile.CompressionType;
import org.apache.hadoop.mapred.FileInputFormat;
import org.apache.hadoop.mapred.FileOutputFormat;
import org.apache.hadoop.mapred.JobClient;
import org.apache.hadoop.mapred.JobConf;
import org.apache.hadoop.mapred.MapReduceBase;
import org.apache.hadoop.mapred.Mapper;
import org.apache.hadoop.mapred.OutputCollector;
import org.apache.hadoop.mapred.Reducer;
import org.apache.hadoop.mapred.Reporter;
import org.apache.hadoop.mapred.SequenceFileInputFormat;
import org.apache.hadoop.mapred.SequenceFileOutputFormat;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

//A Map-reduce program to estimate the value of Pi
//using quasi-Monte Carlo method.
//
//Mapper:
//Generate points in a unit square
//and then count points inside/outside of the inscribed circle of the square.
//
//Reducer:
//Accumulate points inside/outside results from the mappers.
//Let numTotal = numInside + numOutside.
//The fraction numInside/numTotal is a rational approximation of
//the value (Area of the circle)/(Area of the square),
//where the area of the inscribed circle is Pi/4
//and the area of unit square is 1.
//Then, Pi is estimated value to be 4(numInside/numTotal).
//

public class PiEstimator extends Configured implements Tool {
//tmp directory for input/output
static private final Path TMP_DIR = new Path(
PiEstimator.class.getSimpleName() + "_TMP_3_141592654");

//2-dimensional Halton sequence {H(i)},
//where H(i) is a 2-dimensional point and i >= 1 is the index.
//Halton sequence is used to generate sample points for Pi estimation.
private static class HaltonSequence {
// Bases
static final int[] P = {2, 3};
//Maximum number of digits allowed
static final int[] K = {63, 40};

private long index;
private double[] x;
private double[][] q;
private int[][] d;

//Initialize to H(startindex),
//so the sequence begins with H(startindex+1).
HaltonSequence(long startindex) {
index = startindex;
x = new double[K.length];
q = new double[K.length][];
d = new int[K.length][];
for(int i = 0; i < K.length; i++) {
q[i] = new double[K[i]];
d[i] = new int[K[i]];
}

for(int i = 0; i < K.length; i++) {
long k = index;
x[i] = 0;

for(int j = 0; j < K[i]; j++) {
q[i][j] = (j == 0? 1.0: q[i][j-1])/P[i];
d[i][j] = (int)(k % P[i]);
k = (k - d[i][j])/P[i];
x[i] += d[i][j] * q[i][j];
}
}
}

//Compute next point.
//Assume the current point is H(index).
//Compute H(index+1).
//@return a 2-dimensional point with coordinates in [0,1)^2
double[] nextPoint() {
index++;
for(int i = 0; i < K.length; i++) {
for(int j = 0; j < K[i]; j++) {
d[i][j]++;
x[i] += q[i][j];
if (d[i][j] < P[i]) {
break;
}
d[i][j] = 0;
x[i] -= (j == 0? 1.0: q[i][j-1]);
}
}
return x;
}
}

//Mapper class for Pi estimation.
//Generate points in a unit square and then
//count points inside/outside of the inscribed circle of the square.
public static class PiMapper extends MapReduceBase
implements Mapper<LongWritable, LongWritable, BooleanWritable, LongWritable> {

//Map method.
//@param offset samples starting from the (offset+1)th sample.
//@param size the number of samples for this map
//@param out output {true->numInside, false->numOutside}
//@param reporter
public void map(LongWritable offset,
LongWritable size,
OutputCollector<BooleanWritable, LongWritable> out,
Reporter reporter) throws IOException {

final HaltonSequence haltonsequence = new HaltonSequence(offset.get());
long numInside = 0L;
long numOutside = 0L;

for(long i = 0; i < size.get(); ) {
//generate points in a unit square
final double[] point = haltonsequence.nextPoint();

//count points inside/outside of the inscribed circle of the square
final double x = point[0] - 0.5;
final double y = point[1] - 0.5;
if (x*x + y*y > 0.25) {
numOutside++;
} else {
numInside++;
}

//report status
i++;
if (i % 1000 == 0) {
reporter.setStatus("Generated " + i + " samples.");
}
}

//output map results
out.collect(new BooleanWritable(true), new LongWritable(numInside));
out.collect(new BooleanWritable(false), new LongWritable(numOutside));
}
}

//Reducer class for Pi estimation.
//Accumulate points inside/outside results from the mappers.
public static class PiReducer extends MapReduceBase
implements Reducer<BooleanWritable, LongWritable, WritableComparable<?>, Writable> {

private long numInside = 0;
private long numOutside = 0;
private JobConf conf; //configuration for accessing the file system

//Store job configuration.
@Override
public void configure(JobConf job) {
conf = job;
}

// Accumulate number of points inside/outside results from the mappers.
// @param isInside Is the points inside?
// @param values An iterator to a list of point counts
// @param output dummy, not used here.
// @param reporter

public void reduce(BooleanWritable isInside,
Iterator<LongWritable> values,
OutputCollector<WritableComparable<?>, Writable> output,
Reporter reporter) throws IOException {
if (isInside.get()) {
for(; values.hasNext(); numInside += values.next().get());
} else {
for(; values.hasNext(); numOutside += values.next().get());
}
}

//Reduce task done, write output to a file.
@Override
public void close() throws IOException {
//write output to a file
Path outDir = new Path(TMP_DIR, "out");
Path outFile = new Path(outDir, "reduce-out");
FileSystem fileSys = FileSystem.get(conf);
SequenceFile.Writer writer = SequenceFile.createWriter(fileSys, conf,
outFile, LongWritable.class, LongWritable.class,
CompressionType.NONE);
writer.append(new LongWritable(numInside), new LongWritable(numOutside));
writer.close();
}
}

//Run a map/reduce job for estimating Pi.
//@return the estimated value of Pi.
public static BigDecimal estimate(int numMaps, long numPoints, JobConf jobConf
)
throws IOException {
//setup job conf
jobConf.setJobName(PiEstimator.class.getSimpleName());

jobConf.setInputFormat(SequenceFileInputFormat.class);

jobConf.setOutputKeyClass(BooleanWritable.class);
jobConf.setOutputValueClass(LongWritable.class);
jobConf.setOutputFormat(SequenceFileOutputFormat.class);

jobConf.setMapperClass(PiMapper.class);
jobConf.setNumMapTasks(numMaps);

jobConf.setReducerClass(PiReducer.class);
jobConf.setNumReduceTasks(1);

// turn off speculative execution, because DFS doesn't handle
// multiple writers to the same file.
jobConf.setSpeculativeExecution(false);

//setup input/output directories
final Path inDir = new Path(TMP_DIR, "in");
final Path outDir = new Path(TMP_DIR, "out");
FileInputFormat.setInputPaths(jobConf, inDir);
FileOutputFormat.setOutputPath(jobConf, outDir);

final FileSystem fs = FileSystem.get(jobConf);
if (fs.exists(TMP_DIR)) {
throw new IOException("Tmp directory " + fs.makeQualified(TMP_DIR)
+ " already exists. Remove it first.");
}
if (!fs.mkdirs(inDir)) {
throw new IOException("Cannot create input directory " + inDir);
}

//generate an input file for each map task
try {
for(int i=0; i < numMaps; ++i) {
final Path file = new Path(inDir, "part"+i);
final LongWritable offset = new LongWritable(i * numPoints);
final LongWritable size = new LongWritable(numPoints);
final SequenceFile.Writer writer = SequenceFile.createWriter(
fs, jobConf, file,
LongWritable.class, LongWritable.class, CompressionType.NONE);
try {
writer.append(offset, size);
} finally {
writer.close();
}
System.out.println("Wrote input for Map #"+i);
}

//start a map/reduce job
System.out.println("Starting Job");
final long startTime = System.currentTimeMillis();
JobClient.runJob(jobConf);
final double duration = (System.currentTimeMillis() - startTime)/1000.0;
System.out.println("Job Finished in " + duration + " seconds");

//read outputs
Path inFile = new Path(outDir, "reduce-out");
LongWritable numInside = new LongWritable();
LongWritable numOutside = new LongWritable();
SequenceFile.Reader reader = new SequenceFile.Reader(fs, inFile, jobConf);
try {
reader.next(numInside, numOutside);
} finally {
reader.close();
}

//compute estimated value
return BigDecimal.valueOf(4).setScale(20)
.multiply(BigDecimal.valueOf(numInside.get()))
.divide(BigDecimal.valueOf(numMaps))
.divide(BigDecimal.valueOf(numPoints));
} finally {
fs.delete(TMP_DIR, true);
}
}

//Parse arguments and then runs a map/reduce job.
//Print output in standard out.
//@return a non-zero if there is an error. Otherwise, return 0.
public int run(String[] args) throws Exception {
if (args.length != 2) {
System.err.println("Usage: "+getClass().getName()+" <nMaps> <nSamples>");
ToolRunner.printGenericCommandUsage(System.err);
return -1;
}

final int nMaps = Integer.parseInt(args[0]);
final long nSamples = Long.parseLong(args[1]);

System.out.println("Number of Maps = " + nMaps);
System.out.println("Samples per Map = " + nSamples);

final JobConf jobConf = new JobConf(getConf(), getClass());
System.out.println("Estimated value of Pi is "
+ estimate(nMaps, nSamples, jobConf));
return 0;
}

//main method for running it as a stand alone command.
public static void main(String[] argv) throws Exception {
System.exit(ToolRunner.run(null, new PiEstimator(), argv));
}
}
```

## <a name="appendix-d---the-10gb-graysort-source-code"></a>Załącznik D - kod źródłowy graysort 10gb
Kod programu TeraSort MapReduce jest przedstawiane do kontroli w tej sekcji.

```java
/**
    * Licensed to the Apache Software Foundation (ASF) under one
    * or more contributor license agreements.  See the NOTICE file
    * distributed with this work for additional information
    * regarding copyright ownership.  The ASF licenses this file
    * to you under the Apache License, Version 2.0 (the
    * "License"); you may not use this file except in compliance
    * with the License.  You may obtain a copy of the License at
    *
    *     https://www.apache.org/licenses/LICENSE-2.0
    *
    * Unless required by applicable law or agreed to in writing, software
    * distributed under the License is distributed on an "AS IS" BASIS,
    * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    * See the License for the specific language governing permissions and
    * limitations under the License.
    */

package org.apache.hadoop.examples.terasort;

import java.io.IOException;
import java.io.PrintStream;
import java.net.URI;
import java.util.ArrayList;
import java.util.List;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.filecache.DistributedCache;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.NullWritable;
import org.apache.hadoop.io.SequenceFile;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapred.FileOutputFormat;
import org.apache.hadoop.mapred.JobClient;
import org.apache.hadoop.mapred.JobConf;
import org.apache.hadoop.mapred.Partitioner;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

/**
    * Generates the sampled split points, launches the job,
    * and waits for it to finish.
    * <p>
    * To run the program:
    * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>
    */

public class TeraSort extends Configured implements Tool {
    private static final Log LOG = LogFactory.getLog(TeraSort.class);

    /**
    * A partitioner that splits text keys into roughly equal
    * partitions in a global sorted order.
    */

    static class TotalOrderPartitioner implements Partitioner<Text,Text>{
    private TrieNode trie;
    private Text[] splitPoints;

    /**
        * A generic trie node
        */
    static abstract class TrieNode {
        private int level;
        TrieNode(int level) {
        this.level = level;
        }
        abstract int findPartition(Text key);
        abstract void print(PrintStream strm) throws IOException;
        int getLevel() {
        return level;
        }
    }

    /**
        * An inner trie node that contains 256 children based on the next
        * character.
        */
    static class InnerTrieNode extends TrieNode {
        private TrieNode[] child = new TrieNode[256];

        InnerTrieNode(int level) {
        super(level);
        }
        int findPartition(Text key) {
        int level = getLevel();
        if (key.getLength() <= level) {
            return child[0].findPartition(key);
        }
        return child[key.getBytes()[level]].findPartition(key);
        }
        void setChild(int idx, TrieNode child) {
        this.child[idx] = child;
        }
        void print(PrintStream strm) throws IOException {
        for(int ch=0; ch < 255; ++ch) {
            for(int i = 0; i < 2*getLevel(); ++i) {
            strm.print(' ');
            }
            strm.print(ch);
            strm.println(" ->");
            if (child[ch] != null) {
            child[ch].print(strm);
            }
        }
        }
    }

    /**
        * A leaf trie node that does string compares to figure out where the given
        * key belongs between lower..upper.
        */
    static class LeafTrieNode extends TrieNode {
        int lower;
        int upper;
        Text[] splitPoints;
        LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
        super(level);
        this.splitPoints = splitPoints;
        this.lower = lower;
        this.upper = upper;
        }
        int findPartition(Text key) {
        for(int i=lower; i<upper; ++i) {
            if (splitPoints[i].compareTo(key) >= 0) {
            return i;
            }
        }
        return upper;
        }
        void print(PrintStream strm) throws IOException {
        for(int i = 0; i < 2*getLevel(); ++i) {
            strm.print(' ');
        }
        strm.print(lower);
        strm.print(", ");
        strm.println(upper);
        }
    }

    /**
        * Read the cut points from the given sequence file.
        * @param fs the file system
        * @param p the path to read
        * @param job the job config
        * @return the strings to split the partitions on
        * @throws IOException
        */
    private static Text[] readPartitions(FileSystem fs, Path p,
                                            JobConf job) throws IOException {
        SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
        List<Text> parts = new ArrayList<Text>();
        Text key = new Text();
        NullWritable value = NullWritable.get();
        while (reader.next(key, value)) {
        parts.add(key);
        key = new Text();
        }
        reader.close();
        return parts.toArray(new Text[parts.size()]);
    }

    /**
        * Given a sorted set of cut points, build a trie that will find the correct
        * partition quickly.
        * @param splits the list of cut points
        * @param lower the lower bound of partitions 0..numPartitions-1
        * @param upper the upper bound of partitions 0..numPartitions-1
        * @param prefix the prefix that we have already checked against
        * @param maxDepth the maximum depth we will build a trie for
        * @return the trie node that will divide the splits correctly
        */
    private static TrieNode buildTrie(Text[] splits, int lower, int upper,
                                        Text prefix, int maxDepth) {
        int depth = prefix.getLength();
        if (depth >= maxDepth || lower == upper) {
        return new LeafTrieNode(depth, splits, lower, upper);
        }
        InnerTrieNode result = new InnerTrieNode(depth);
        Text trial = new Text(prefix);
        // append an extra byte on to the prefix
        trial.append(new byte[1], 0, 1);
        int currentBound = lower;
        for(int ch = 0; ch < 255; ++ch) {
        trial.getBytes()[depth] = (byte) (ch + 1);
        lower = currentBound;
        while (currentBound < upper) {
            if (splits[currentBound].compareTo(trial) >= 0) {
            break;
            }
            currentBound += 1;
        }
        trial.getBytes()[depth] = (byte) ch;
        result.child[ch] = buildTrie(splits, lower, currentBound, trial,
                                        maxDepth);
        }
        // pick up the rest
        trial.getBytes()[depth] = 127;
        result.child[255] = buildTrie(splits, currentBound, upper, trial,
                                    maxDepth);
        return result;
    }

    public void configure(JobConf job) {
        try {
        FileSystem fs = FileSystem.getLocal(job);
        Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
        splitPoints = readPartitions(fs, partFile, job);
        trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
        } catch (IOException ie) {
        throw new IllegalArgumentException("can't read partitions file", ie);
        }
    }

    public TotalOrderPartitioner() {
    }

    public int getPartition(Text key, Text value, int numPartitions) {
        return trie.findPartition(key);
    }

    }

    public int run(String[] args) throws Exception {
    LOG.info("starting");
    JobConf job = (JobConf) getConf();
    Path inputDir = new Path(args[0]);
    inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
    Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
    URI partitionUri = new URI(partitionFile.toString() +
                                "#" + TeraInputFormat.PARTITION_FILENAME);
    TeraInputFormat.setInputPaths(job, new Path(args[0]));
    FileOutputFormat.setOutputPath(job, new Path(args[1]));
    job.setJobName("TeraSort");
    job.setJarByClass(TeraSort.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(Text.class);
    job.setInputFormat(TeraInputFormat.class);
    job.setOutputFormat(TeraOutputFormat.class);
    job.setPartitionerClass(TotalOrderPartitioner.class);
    TeraInputFormat.writePartitionFile(job, partitionFile);
    DistributedCache.addCacheFile(partitionUri, job);
    DistributedCache.createSymlink(job);
    job.setInt("dfs.replication", 1);
    TeraOutputFormat.setFinalSync(job, true);
    JobClient.runJob(job);
    LOG.info("done");
    return 0;
    }

    /**
    * @param args
    */

    public static void main(String[] args) throws Exception {
    int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
    System.exit(res);
    }
}
```

[hdinsight-submit-jobs]: hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-introduction]:hadoop/apache-hadoop-introduction.md

[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: #hdinsight-sample-10gb-graysort
[hdinsight-sample-csharp-streaming]: #hdinsight-sample-csharp-streaming
[hdinsight-sample-pi-estimator]: #hdinsight-sample-pi-estimator
[hdinsight-sample-wordcount]: #hdinsight-sample-wordcount

[hdinsight-use-hive]: hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]: hadoop/hdinsight-use-pig.md

[streamreader]: https://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: https://msdn.microsoft.com/library/system.console.writeline
[stdin-stdout-stderr]: https://msdn.microsoft.com/library/3x292kth.aspx
