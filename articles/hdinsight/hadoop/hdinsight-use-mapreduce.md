---
title: MapReduce z Apache Hadoop na HDInsight
description: Dowiedz się, jak uruchamiać zadania Apache MapReduce w apache Hadoop w klastrach HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 238f37cc1e8cbc8d3260693181d6e54ac5f592f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435721"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>Korzystanie z technologii MapReduce na platformie Apache Hadoop w usłudze HDInsight

Dowiedz się, jak uruchamiać zadania MapReduce w klastrach HDInsight.

## <a name="example-data"></a>Przykładowe dane

HDInsight zawiera różne przykładowe zestawy danych, które są przechowywane w `/example/data` katalogu i katalogu. `/HdiSamples` Te katalogi znajdują się w domyślnym magazynie klastra. W tym dokumencie `/example/data/gutenberg/davinci.txt` używamy pliku. Ten plik zawiera zeszyty Leonarda da Vinci.

## <a name="example-mapreduce"></a>Przykład mapReduce

Przykładowa aplikacja MapReduce word count jest dołączona do klastra HDInsight. W tym przykładzie znajduje się na `/example/jars/hadoop-mapreduce-examples.jar` domyślnym magazynie dla klastra.

Następujący kod Java jest źródłem aplikacji MapReduce `hadoop-mapreduce-examples.jar` zawartej w pliku:

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

Aby uzyskać instrukcje dotyczące pisania własnych aplikacji MapReduce, zobacz [Tworzenie aplikacji Java MapReduce dla hdinsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

## <a name="run-the-mapreduce"></a>Uruchom mapreduce

HDInsight można uruchomić zadania HiveQL przy użyciu różnych metod. Użyj poniższej tabeli, aby zdecydować, która metoda jest odpowiednia dla Ciebie, a następnie kliknij łącze dla instruktażu.

| **Użyj tego**... | **... aby to zrobić** |  ... z tego **systemu operacyjnego klienta** |
|:--- |:--- |:--- |:--- |
| [Ssh](apache-hadoop-use-mapreduce-ssh.md) |Użyj polecenia Hadoop za pośrednictwem **SSH** |Linux, Unix, Mac OS X lub Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |Przesyłanie zadania zdalnie przy użyciu **funkcji REST** |Linux, Unix, Mac OS X lub Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Przesyłanie zadania zdalnie przy użyciu **programu Windows PowerShell**  |Windows |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o pracy z danymi w umiań HD, zobacz następujące dokumenty:

* [Tworzenie programów Java MapReduce dla programu HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Korzystanie z programu Apache Hive z usługą HDInsight](./hdinsight-use-hive.md)
