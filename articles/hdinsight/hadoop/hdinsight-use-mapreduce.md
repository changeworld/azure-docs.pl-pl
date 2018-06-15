---
title: MapReduce z Hadoop w usłudze HDInsight | Dokumentacja firmy Microsoft
description: Informacje o sposobie uruchamiania zadań MapReduce na platformie Hadoop w klastrach usługi HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7f321501-d62c-4ffc-b5d6-102ecba6dd76
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: larryfr
ms.openlocfilehash: 131e3065da4ccfb20d63856844a302a94806fd19
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202632"
---
# <a name="use-mapreduce-in-hadoop-on-hdinsight"></a>Korzystać z usługi MapReduce w Hadoop w usłudze HDInsight

Informacje o sposobie uruchamiania zadań MapReduce na klastrach usługi HDInsight. Skorzystaj z poniższej tabeli, aby odnaleźć różne sposoby, że MapReduce mogą być używane z usługą HDInsight:

| **Użyj tej**... | **. Aby to zrobić** | ...zwykle to **systemu operacyjnego klastra** | ...from to **system operacyjny klienta** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Użyj polecenia Hadoop za pomocą **SSH** |Linux |Linux, Unix, Mac OS X lub systemu Windows |
| [REST](apache-hadoop-use-mapreduce-curl.md) |Przesłać zadanie zdalnie przy użyciu **REST** (przykłady użycia cURL) |Linux lub Windows |Linux, Unix, Mac OS X lub systemu Windows |
| [Środowisko Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Przesłać zadanie zdalnie przy użyciu **środowiska Windows PowerShell** |Linux lub Windows |Windows |

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).
>

## <a id="whatis"></a>Co to jest MapReduce

MapReduce z Hadoop to platforma oprogramowania do zapisywania zadania, które przetwarzają olbrzymich ilości danych. Dane wejściowe jest podzielony na niezależne fragmenty. Każdego fragmentu są przetwarzane równolegle w węzłach w klastrze. Zadania MapReduce składa się z dwóch funkcji:

* **Mapowania**: zużywa dane wejściowe, analizuje je (zazwyczaj z filtrowanie i sortowanie operations) i emituje krotek (pary klucz wartość)

* **Reduktor**: wykorzystuje krotek emitowane przez funkcję mapowania, a następnie wykonuje operację podsumowania, która tworzy mniejsze, łączny wynik z danych mapowania

Na poniższym diagramie przedstawiono w przykładzie zadania MapReduce liczba podstawowe programu word:

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

Dane wyjściowe tego zadania jest liczba ile razy każdego wyrazu wystąpił w tekście.

* Podziały go na słowa mapowania i przyjmuje każdego wiersza w tekście wejściowych jako danych wejściowych. Emituje go klucz/wartość pary zawsze wyraz występuje Word następuje 1. Dane wyjściowe są sortowane przed wysłaniem ich do reduktor.
* Reduktor sumuje tych poszczególnych liczników dla każdego wyrazu i emituje parę jednego klucza i wartości, zawierający słowo następuje sumę jego wystąpienia.

MapReduce może być wdrożonych w różnych językach. Java najczęściej implementacji i jest używany w celach demonstracyjnych, w tym dokumencie.

## <a name="development-languages"></a>Języki programowania

Języków i platform, które są oparte na języku Java i maszyny wirtualnej Java można uruchomiono bezpośrednio jako zadania MapReduce. Przykład używane w tym dokumencie jest aplikacji Java MapReduce. Języki non-Java, takich jak C#, Python lub pliki wykonywalne autonomicznej, należy użyć **przesyłania strumieniowego usługi Hadoop**.

Przesyłanie strumieniowe Hadoop komunikuje się z mapowania i reduktor za pośrednictwem STDIN i STDOUT. Mapowania i reduktor danych wiersza w czasie stdin, do odczytu i zapisu danych wyjściowych STDOUT. Każdy wiersz odczytu lub emitowane przez program mapowania i reduktor musi być w formacie parę klucz wartość ograniczonej przez znak tabulacji:

    [key]/t[value]

Aby uzyskać więcej informacji, zobacz [przesyłania strumieniowego usługi Hadoop](http://hadoop.apache.org/docs/r1.2.1/streaming.html).

Przykłady korzystania z platformy Hadoop przesyłania strumieniowego z usługą HDInsight można znaleźć w następujących dokumentach:

* [Tworzenie zadań MapReduce C#](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Tworzenie zadań MapReduce języka Python](apache-hadoop-streaming-python.md)

## <a id="data"></a>Przykładowe dane

Usługa HDInsight zapewnia różne przykład zestawów danych, które są przechowywane w `/example/data` i `/HdiSamples` katalogu. Te katalogi są domyślny magazyn dla klastra. W tym dokumencie używamy `/example/data/gutenberg/davinci.txt` pliku. Ten plik zawiera notebooki Leonardo Da Vinci.

## <a id="job"></a>Przykład MapReduce

Przykład MapReduce word liczba aplikacji znajduje się z klastrem usługi HDInsight. W tym przykładzie znajduje się pod adresem `/example/jars/hadoop-mapreduce-examples.jar` na domyślny magazyn dla klastra.

Poniższy kod języka Java jest źródłem zawartych w aplikacji MapReduce `hadoop-mapreduce-examples.jar` pliku:

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

Aby uzyskać instrukcje dotyczące pisać własne aplikacje MapReduce można znaleźć w następujących dokumentach:

* [Tworzenie aplikacji Java MapReduce dla usługi HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Tworzenie aplikacji MapReduce języka Python dla usługi HDInsight](apache-hadoop-streaming-python.md)

## <a id="run"></a>Uruchom MapReduce

HDInsight można uruchamiać zadania HiveQL przy użyciu różnych metod. Skorzystaj z poniższej tabeli do określania, która metoda jest odpowiednie dla Ciebie, a następnie kliknij link, aby uzyskać wskazówki.

| **Użyj tej**... | **. Aby to zrobić** | ...zwykle to **systemu operacyjnego klastra** | ...from to **system operacyjny klienta** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Użyj polecenia Hadoop za pomocą **SSH** |Linux |Linux, Unix, Mac OS X lub systemu Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |Przesłać zadanie zdalnie przy użyciu **REST** |Linux lub Windows |Linux, Unix, Mac OS X lub systemu Windows |
| [Środowisko Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Przesłać zadanie zdalnie przy użyciu **środowiska Windows PowerShell** |Linux lub Windows |Windows |

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a id="nextsteps"></a>Następne kroki

Aby dowiedzieć się więcej na temat pracy z danymi w usłudze HDInsight, można znaleźć w następujących dokumentach:

* [Tworzenie programów Java MapReduce dla usługi HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Opracowywanie Python przesyłania strumieniowego programy MapReduce dla usługi HDInsight](apache-hadoop-streaming-python.md)

* [Korzystanie z programu Hive z usługą HDInsight][hdinsight-use-hive]

* [Korzystanie z języka Pig z usługą HDInsight][hdinsight-use-pig]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-pig]:hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
