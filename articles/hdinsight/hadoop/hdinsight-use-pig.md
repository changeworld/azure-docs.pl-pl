---
title: Korzystanie z języka Pig Hadoop w usłudze HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystanie z języka Pig z usługą Hadoop w usłudze HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: acfeb52b-4b81-4a7d-af77-3e9908407404
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: larryfr
ms.openlocfilehash: d6d42a1e0edb038f6d4cc9c8149768bdb698cafc
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="use-pig-with-hadoop-on-hdinsight"></a>Korzystanie z języka Pig z usługą Hadoop w usłudze HDInsight

Dowiedz się, jak używać [Apache Pig](http://pig.apache.org/) z usługą HDInsight.

Pig to platforma do tworzenia programów dla platformy Hadoop przy użyciu języka procedurach znany jako *Pig Latin*. Pig stanowi alternatywę dla języka Java tworzenia *MapReduce* rozwiązania który znajduje się w usłudze Azure HDInsight. Skorzystaj z poniższej tabeli, aby dowiedzieć się, że różne sposoby, które wieprzowa mogą być używane z usługi HDInsight:

| **Użyj tej** Jeśli chcesz... | ...an **interakcyjne** powłoki | ...**partii** przetwarzania | ...zwykle to **systemu operacyjnego klastra** | ...from to **system operacyjny klienta** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X lub systemu Windows |
| [Interfejs API REST](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux lub Windows |Linux, Unix, Mac OS X lub systemu Windows |
| [Zestaw SDK dla platformy .NET usługi Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux lub Windows |Systemu Windows (na razie) |
| [Środowisko Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux lub Windows |Windows |

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a id="why"></a>Dlaczego warto korzystać z języka Pig

Jednym z wyzwań przetwarzania danych przy użyciu MapReduce w Hadoop przy użyciu tylko mapy i funkcja Zmniejsz implementuje logika przetwarzania. Przetwarzanie złożonych, często należy podzielić przetwarzania na wiele operacji MapReduce, które są powiązane łańcuchem zależności ze sobą w celu osiągnięcia pożądany wynik.

Pig można zdefiniować przetwarzania jako serię transformacje, których dane przechodzi przez do utworzenia żądanego wyniku.

Język Pig Latin umożliwia opisano przepływ danych z nieprzetworzone dane wejściowe, przez co najmniej jeden przekształcenia, aby wygenerować odpowiednie dane wyjściowe. Programy pig Latin wykonaj tego wzorca ogólne:

* **Obciążenia**: odczytywanie danych można manipulować w systemie plików

* **Przekształć**: manipulowanie danymi

* **Zrzut lub przechowywać**: wysyłania danych do ekranu i zapisz go do przetworzenia

### <a name="user-defined-functions"></a>Funkcje zdefiniowane przez użytkownika

Pig Latin obsługuje również funkcje zdefiniowane przez użytkownika (UDF), dzięki czemu można wywołać składników zewnętrznych, które implementują logikę, która jest trudne do modelu w Pig Latin.

Aby uzyskać więcej informacji na temat Pig Latin, zobacz [1 Ręczne odwołanie Pig Latin](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) i [2 ręczne odwołanie Pig Latin](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

Przykład użycia funkcji UDF z Pig na ten temat można znaleźć w następujących dokumentach:

* [DataFu za pomocą Pig w usłudze HDInsight](apache-hadoop-use-pig-datafu-udf.md) -DataFu jest kolekcją przydatne funkcje UDF obsługiwanego przez Apache
* [Używanie języka Python za pomocą Pig i Hive w usłudze HDInsight](python-udf-hdinsight.md)
* [Używanie języka C# z Hive i Pig w usłudze HDInsight](apache-hadoop-hive-pig-udf-dotnet-csharp.md)

## <a id="data"></a>Przykładowe dane

Usługa HDInsight zapewnia różne przykład zestawów danych, które są przechowywane w `/example/data` i `/HdiSamples` katalogów. Te katalogi są domyślny magazyn dla klastra. W przykładzie Pig, w tym dokumencie *log4j* plik `/example/data/sample.log`.

Każdy dziennik wewnątrz pliku składa się z pól wiersz, który zawiera `[LOG LEVEL]` pola, aby wyświetlić typ i ważność, na przykład:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

W poprzednim przykładzie poziom dziennika, występuje błąd.

> [!NOTE]
> Można również tworzyć przy użyciu pliku log4j [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) narzędzia rejestrowania, a następnie przekaż tego pliku do obiektu blob. Zobacz [przekazywanie danych do usługi HDInsight](../hdinsight-upload-data.md) instrukcje. Aby uzyskać więcej informacji o używaniu obiekty BLOB w magazynie Azure z usługą HDInsight, zobacz [Użyj magazynu obiektów Blob Azure z usługą HDInsight](../hdinsight-hadoop-use-blob-storage.md).

## <a id="job"></a>Przykładowe zadania

Ładuje zadanie Pig Latin `sample.log` plik z magazynu domyślnego dla klastra usługi HDInsight. Następnie wykonuje szereg przekształcenia, które powoduje liczba, ile razy w danych wejściowych wystąpił każdy poziom dziennika. Wyniki są zapisywane do strumienia wyjściowego STDOUT.

    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

Na poniższej ilustracji przedstawiono podsumowanie danych działania każdego transformacji.

![Graficzna reprezentacja przekształcenia][image-hdi-pig-data-transformation]

## <a id="run"></a>Uruchom zadanie Pig Latin

HDInsight można uruchamiać zadania Pig Latin przy użyciu różnych metod. Skorzystaj z poniższej tabeli do określania, która metoda jest odpowiednie dla Ciebie, a następnie kliknij link, aby uzyskać wskazówki.

| **Użyj tej** Jeśli chcesz... | ...an **interakcyjne** powłoki | ...**partii** przetwarzania | ...zwykle to **systemu operacyjnego klastra** | ...from to **klienta** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X lub systemu Windows |
| [Curl](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux lub Windows |Linux, Unix, Mac OS X lub systemu Windows |
| [Zestaw SDK dla platformy .NET usługi Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux lub Windows |Systemu Windows (na razie) |
| [Środowisko Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux lub Windows |Windows |

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a name="pig-and-sql-server-integration-services"></a>Pig i SQL Server Integration Services

SQL Server Integration Services (SSIS) służy do uruchamiania zadania programu Pig. Azure Feature Pack dla SSIS zawiera następujące składniki, które współpracują z zadań Pig w usłudze HDInsight.

* [Zadaniem Azure HDInsight Pig][pigtask]

* [Menedżer połączeń subskrypcji platformy Azure][connectionmanager]

Dowiedz się więcej o Azure Feature Pack SSIS [tutaj][ssispack].

## <a id="nextsteps"></a>Następne kroki
Teraz, kiedy znasz jak korzystanie z języka Pig z usługą HDInsight, użyj następujących linków, aby poznać inne sposoby pracy z usługą Azure HDInsight.

* [Przekazywanie danych do usługi HDInsight](../hdinsight-upload-data.md)
* [Korzystanie z programu Hive z usługą HDInsight][hdinsight-use-hive]
* [Korzystanie z usługą HDInsight Sqoop](hdinsight-use-sqoop.md)
* [Korzystanie z Oozie z usługą HDInsight](../hdinsight-use-oozie.md)
* [Korzystanie z zadań MapReduce z usługą HDInsight][hdinsight-use-mapreduce]

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-mapreduce]:../hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
