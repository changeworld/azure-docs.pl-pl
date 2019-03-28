---
title: MapReduce z platformą Apache Hadoop w HDInsight
description: Dowiedz się, jak uruchamiać zadania MapReduce na technologii Apache Hadoop w klastrach HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: f2baaf598be8ede69fd6e1fa49a5f5a6b64c24ff
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521204"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>Korzystanie z technologii MapReduce na platformie Apache Hadoop w HDInsight

Dowiedz się, jak uruchamiać zadania MapReduce w klastrach HDInsight. 

## <a id="whatis"></a>Co to jest MapReduce

Apache Hadoop MapReduce to platforma oprogramowania do pisania zadań, które przetwarzają duże ilości danych. Dane wejściowe jest podzielony na niezależne fragmenty. Każdego fragmentu są przetwarzane równolegle na węzłach w klastrze. Zadania MapReduce składa się z dwóch funkcji:

* **Mapowania**: Wykorzystuje dane wejściowe, analizuje je (zwykle przy użyciu filtrowania i sortowania operacji) i emituje krotek (pary klucz wartość)

* **Reduktor**: Wykorzystuje krotek emitowane przez funkcję mapowania, a następnie wykonuje operację podsumowania, która tworzy wynik mniejsze, połączone z danych mapowania

Przykład zadania MapReduce liczba wyrazy zostało zilustrowane na poniższym diagramie:

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

Danymi wyjściowymi tego zadania jest to liczba liczbę wystąpień każdego wyrazu w tekście.

* Mapowania przyjmuje każdego wiersza w tekście wejściowym jako dane wejściowe i dzieli je na słowa. Emituje klucz/wartość pary każdorazowo słowa występuje słowa następuje 1. Dane wyjściowe są sortowane przed wysłaniem ich do reduktor.
* Reduktor sumuje tych poszczególnych liczb dla każdego wyrazu i emituje parą pojedynczy klucz/wartość, która zawiera słowo, następuje sumę jego wystąpienia.

MapReduce mogą być implementowane w różnych językach. Java jest najczęstszą implementacją i jest używany w celach demonstracyjnych, w tym dokumencie.

## <a name="development-languages"></a>Języki programowania

Języków i struktur, które są oparte na języku Java i maszyny wirtualnej Java można uruchomiono bezpośrednio jako zadanie MapReduce. W przykładzie użyto w tym dokumencie jest aplikacją MapReduce w języku Java. Języków non-Java, takich jak C#, Python lub autonomiczne pliki wykonywalne, należy użyć **przesyłania strumieniowego usługi Hadoop**.

Przesyłanie strumieniowe Hadoop komunikuje się z mapowania i reduktor STDIN i STDOUT. Mapowania reduktor odczytać wiersza danych w czasie stdin i zapisać dane wyjściowe do strumienia wyjściowego STDOUT. Każdy wiersz odczytu lub wyemitowane przez mapowania i reduktor musi być w formacie parę klucz wartość ograniczonej przez znak tabulacji:

    [key]/t[value]

Aby uzyskać więcej informacji, zobacz [przesyłania strumieniowego usługi Hadoop](https://hadoop.apache.org/docs/r1.2.1/streaming.html).

Przykłady korzystania z usługi Hadoop, przesyłanie strumieniowe z HDInsight można znaleźć w następujących dokumentach:

* [Tworzenie zadań MapReduce w języku C#](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Tworzenie zadań MapReduce w języku Python](apache-hadoop-streaming-python.md)

## <a id="data"></a>Przykładowe dane

HDInsight zapewnia różne przykładowe zestawy danych, które są przechowywane w `/example/data` i `/HdiSamples` katalogu. Te katalogi znajdują się w domyślny magazyn dla klastra. W tym dokumencie użyjemy `/example/data/gutenberg/davinci.txt` pliku. Ten plik zawiera notesów Leonardo Da Vinci.

## <a id="job"></a>Przykład technologii MapReduce

Przykładem MapReduce word liczba aplikacji jest dołączone do klastra usługi HDInsight. W tym przykładzie znajduje się w `/example/jars/hadoop-mapreduce-examples.jar` na domyślny magazyn dla klastra.

Następujący kod w języku Java jest źródłem zawartych w aplikacji MapReduce `hadoop-mapreduce-examples.jar` pliku:

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

Aby uzyskać instrukcje dotyczące pisania własnych aplikacji MapReduce zobacz następujące dokumenty:

* [Twórz aplikacje MapReduce w języku Java dla HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Tworzenie aplikacji Python MapReduce for HDInsight](apache-hadoop-streaming-python.md)

## <a id="run"></a>Uruchamianie technologii MapReduce

HDInsight można uruchamiać zadania HiveQL, przy użyciu różnych metod. Skorzystaj z poniższej tabeli do określania, która metoda jest odpowiedni dla Ciebie, a następnie kliknij link, aby uzyskać wskazówki.

| **Użyj tego**... | **. Aby to zrobić** | ...zwykle to **systemu operacyjnego klastra** | ...from to **system operacyjny klienta** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Użyj polecenia usługi Hadoop za pośrednictwem **SSH** |Linux |Linux, Unix, Mac OS X lub Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |Prześlij zadanie zdalnie przy użyciu **REST** |System Linux lub Windows |Linux, Unix, Mac OS X lub Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Prześlij zadanie zdalnie przy użyciu **programu Windows PowerShell** |System Linux lub Windows |Windows |

## <a id="nextsteps"></a>Następne kroki

Aby dowiedzieć się więcej na temat pracy z danymi w HDInsight, zobacz następujące dokumenty:

* [Opracowywanie programów MapReduce w języku Java dla HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Opracowywanie programów MapReduce przesyłania strumieniowego HDInsight w języku Python](apache-hadoop-streaming-python.md)

* [Korzystanie z programu Apache Hive z usługą HDInsight][hdinsight-use-hive]

* [Korzystanie z programu Apache Pig z usługą HDInsight][hdinsight-use-pig]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-pig]:hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
