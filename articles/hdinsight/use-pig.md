---
title: Korzystanie z platformy Apache Pig
titleSuffix: Azure HDInsight
description: Dowiedz się, jak używać Pig z Apache Hadoop na HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: ea960a92aee1c9447bb12d27cffdc42de9fd907a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672127"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>Użyj Apache Pig z Apache Hadoop na HDInsight

Dowiedz się, jak używać [Apache Pig](https://pig.apache.org/) z HDInsight.

Apache Pig jest platformą do tworzenia programów dla Apache Hadoop za pomocą języka proceduralnego znanego jako *Pig Latin*. Pig jest alternatywą dla języka Java do tworzenia rozwiązań *MapReduce* i jest dołączony do usługi Azure HDInsight. Poniższa tabela służy do odkrywania różnych sposobów używania leku Pig z programem HDInsight:

## <a name="why-use-apache-pig"></a><a id="why"></a>Dlaczego warto skorzystać z Apache Pig

Jednym z wyzwań związanych z przetwarzaniem danych przy użyciu MapReduce w Hadoop jest implementowanie logiki przetwarzania przy użyciu tylko mapy i funkcji zmniejszenia. W przypadku przetwarzania złożonego często trzeba podzielić przetwarzania na wiele operacji MapReduce, które są połączone ze sobą, aby osiągnąć pożądany wynik.

Pig pozwala zdefiniować przetwarzanie jako serię przekształceń, które dane przepływają w celu uzyskania żądanego wyjścia.

Język łaciński Pig pozwala opisać przepływ danych z nieprzetworzonego wejścia, za pośrednictwem jednego lub więcej przekształceń, w celu uzyskania żądanego wyjścia. Programy łaciny świni zgodne z tym ogólnym wzorem:

* **Ładowanie:** Odczyt danych, które mają być manipulowane z systemu plików.

* **Transform**: Manipulować danymi.

* **Zrzut lub przechowywanie**: Dane wyjściowe na ekranie lub przechowywać go do przetworzenia.

### <a name="user-defined-functions"></a>Funkcje zdefiniowane przez użytkownika

Pig Latin obsługuje również funkcje zdefiniowane przez użytkownika (UDF), który pozwala na wywoływanie składników zewnętrznych, które implementują logikę, która jest trudna do modelowania w pig latin.

Aby uzyskać więcej informacji na temat Pig Latin, zobacz [Pig Latin Reference Manual 1](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) i Pig Latin Reference Manual [2](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html).

## <a name="example-data"></a><a id="data"></a>Przykładowe dane

HDInsight zawiera różne przykładowe zestawy danych, które są przechowywane w `/example/data` katalogach i `/HdiSamples` katalogach. Te katalogi znajdują się w domyślnym magazynie klastra. Pig przykład w tym dokumencie używa pliku `/example/data/sample.log` *log4j* z .

Każdy dziennik wewnątrz pliku składa się z `[LOG LEVEL]` wiersza pól zawierającego pole, aby pokazać typ i ważność, na przykład:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

W poprzednim przykładzie poziom dziennika jest BŁĄD.

> [!NOTE]  
> Można również wygenerować plik log4j za pomocą narzędzia do rejestrowania [Apache Log4j,](https://en.wikipedia.org/wiki/Log4j) a następnie przekazać ten plik do obiektu blob. Aby uzyskać [instrukcje, zobacz Przekazywanie danych do usługi HDInsight.](hdinsight-upload-data.md) Aby uzyskać więcej informacji na temat sposobu używania obiektów blob w magazynie platformy Azure z usługą HDInsight, zobacz [Korzystanie z usługi Azure Blob Storage z usługą HDInsight.](hdinsight-hadoop-use-blob-storage.md)

## <a name="example-job"></a><a id="job"></a>Przykładowe zadanie

Następujące zadanie Pig Latin `sample.log` ładuje plik z domyślnego magazynu dla klastra HDInsight. Następnie wykonuje serię przekształceń, które powodują liczbę razy każdy poziom dziennika wystąpił w danych wejściowych. Wyniki są zapisywane do STDOUT.

    ```
    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;
    ```

Na poniższej ilustracji przedstawiono podsumowanie tego, co każda transformacja ma do danych.

![Graficzna reprezentacja przekształceń][image-hdi-pig-data-transformation]

## <a name="run-the-pig-latin-job"></a><a id="run"></a>Uruchom zadanie Pig Latin

PROGRAM HDInsight może uruchamiać zadania Pig Latin przy użyciu różnych metod. Użyj poniższej tabeli, aby zdecydować, która metoda jest odpowiednia dla Ciebie, a następnie kliknij łącze dla instruktażu.

## <a name="pig-and-sql-server-integration-services"></a>Usługi integracji pig i SQL Server

Za pomocą programu SQL Server Integration Services (SSIS) można uruchomić zadanie Świnia. Pakiet funkcji platformy Azure dla pakietu SSIS zawiera następujące składniki, które współpracują z zadaniami pig w programie HDInsight.

* [Zadanie świni usługi Azure HDInsight][pigtask]

* [Menedżer połączeń subskrypcji platformy Azure][connectionmanager]

Dowiedz się więcej o pakiecie funkcji platformy Azure dla SSIS [tutaj][ssispack].

## <a name="next-steps"></a><a id="nextsteps"></a>Następne kroki

Teraz, gdy masz instrukcje korzystania z pig z HDInsight, użyj następujących łączy, aby zbadać inne sposoby pracy z usługi Azure HDInsight.

* [Przekazywanie danych do usługi HDInsight](hdinsight-upload-data.md)
* [Korzystanie z programu Apache Hive z usługą HDInsight](./hadoop/hdinsight-use-hive.md)
* [Korzystanie z programu Apache Sqoop z usługą HDInsight](hdinsight-use-sqoop.md)
* [Korzystanie z zadań MapReduce z usługą HDInsight](./hadoop/hdinsight-use-mapreduce.md)

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: https://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: https://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: https://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/use-pig/hdi-data-transformation.gif
