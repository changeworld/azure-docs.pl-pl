---
title: Korzystanie z języka Pig Apache — usługa Azure HDInsight
description: Dowiedz się, jak korzystanie z języka Pig z platformą Apache Hadoop w HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.openlocfilehash: c39c332055943f915471d0da18526f793d4d7a4a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64705028"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>Use Apache Pig z platformą Apache Hadoop w HDInsight

Dowiedz się, jak używać [Apache Pig](https://pig.apache.org/) z HDInsight.

Apache Pig to platforma do tworzenia programów dla usługi Apache Hadoop przy użyciu języka proceduralne, znane jako *Pig Latin*. Pig stanowi alternatywę dla języka Java do tworzenia *MapReduce* rozwiązania i jest dołączone do usługi Azure HDInsight. Skorzystaj z poniższej tabeli, aby dowiedzieć się, że różne sposoby, Pig, może być używany z HDInsight:

| **Użyj tej** Jeśli chcesz... | ...an **interakcyjne** powłoki | ...**partii** przetwarzania | ...zwykle to **systemu operacyjnego klastra** | ...from to **system operacyjny klienta** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X lub Windows |
| [Interfejs API REST](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |System Linux lub Windows |Linux, Unix, Mac OS X lub Windows |
| [Zestaw SDK dla platformy .NET usługi Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |System Linux lub Windows |Windows (na razie) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |System Linux lub Windows |Windows |

> [!IMPORTANT]  
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a id="why"></a>Dlaczego warto używać Apache Pig

Jednym z wyzwań związanych z przetwarzaniem danych przy użyciu technologii MapReduce w usłudze Hadoop implementuje logikę przetwarzania przy użyciu tylko mapy i funkcja redukcji. Złożone przetwarzanie, często należy podzielić przetwarzania na wiele operacji MapReduce, które są powiązane ze sobą, aby osiągnąć oczekiwany rezultat.

Pig umożliwia definiowanie przetwarzania w postaci serii obiektów przekształceń, które dane przepływają przez do generowania żądaną danych wyjściowych.

Język Pig Latin umożliwia opisywanie przepływu danych z nieprzetworzone dane wejściowe, przez co najmniej jeden przekształcenia, aby wygenerować żądaną produktu wyjściowego. Programów pig Latin korzystać z tego wzoru ogólne:

* **Obciążenia**: Odczytywanie danych ustawianych w systemie plików.

* **Przekształcanie**: Manipulowanie danymi.

* **Zrzuć ani nie przechowują**: Dane wyjściowe do ekranu, czy zapisać je do przetworzenia.

### <a name="user-defined-functions"></a>Funkcje zdefiniowane przez użytkownika

Pig Latin obsługuje również funkcje zdefiniowane przez użytkownika (UDF), dzięki czemu można wywołać składników zewnętrznych, które implementują logikę, która jest trudne do modelu w Pig Latin.

Aby uzyskać więcej informacji na temat Pig Latin, zobacz [1 Ręczne odwołanie Pig Latin](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) i [2 ręczne odwołanie Pig Latin](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html).

Przykład użycia funkcji zdefiniowanych przez użytkownika przy użyciu programu Pig na ten temat można znaleźć w następujących dokumentach:

* [Korzystanie z funkcji Apache DataFu przy użyciu programu Apache Pig w HDInsight](apache-hadoop-use-pig-datafu-udf.md) -DataFu to zbiór przydatne funkcje zdefiniowane przez użytkownika utrzymywane przez Apache
* [Za pomocą języka Python przy użyciu Apache Pig i Apache Hive w HDInsight](python-udf-hdinsight.md)
* [Użyj C# za pomocą programu Apache Hive i Apache Pig w HDInsight](apache-hadoop-hive-pig-udf-dotnet-csharp.md)

## <a id="data"></a>Przykładowe dane

HDInsight zapewnia różne przykładowe zestawy danych, które są przechowywane w `/example/data` i `/HdiSamples` katalogów. Te katalogi znajdują się w domyślny magazyn dla klastra. W przykładzie Pig, w tym dokumencie użyto *log4j* plik wchodzącej w skład `/example/data/sample.log`.

Każdy dziennik wewnątrz pliku składa się z pól wiersz, który zawiera `[LOG LEVEL]` pola, aby wyświetlić typ i ważność, na przykład:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

W poprzednim przykładzie poziom dziennika jest błąd.

> [!NOTE]  
> Można również wygenerować za pomocą pliku log4j [mechanizmu Apache Log4j](https://en.wikipedia.org/wiki/Log4j) narzędzia rejestrowania, a następnie przekaż ten plik do obiektu blob. Zobacz [przekazywanie danych do HDInsight](../hdinsight-upload-data.md) instrukcje. Aby uzyskać więcej informacji o używaniu obiektów blob w usłudze Azure storage za pomocą HDInsight, zobacz [użycia usługi Azure Blob Storage za pomocą HDInsight](../hdinsight-hadoop-use-blob-storage.md).

## <a id="job"></a>Przykładowe zadania

Następujące zadania Pig Latin ładuje `sample.log` plik z domyślnego magazynu dla klastra usługi HDInsight. Następnie wykonuje szereg przekształceń, które powoduje liczba ile razy w danych wejściowych wystąpił każdy poziom dziennika. Wyniki są zapisywane do strumienia wyjściowego STDOUT.

    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

Na poniższej ilustracji przedstawiono podsumowanie funkcji co każde przekształcenie danych.

![Graficzne przedstawienie przekształcenia][image-hdi-pig-data-transformation]

## <a id="run"></a>Uruchamianie zadania Pig Latin

HDInsight można uruchamiać zadania Pig Latin, przy użyciu różnych metod. Skorzystaj z poniższej tabeli do określania, która metoda jest odpowiedni dla Ciebie, a następnie kliknij link, aby uzyskać wskazówki.

| **Użyj tej** Jeśli chcesz... | ...an **interakcyjne** powłoki | ...**partii** przetwarzania | ...zwykle to **systemu operacyjnego klastra** | ...from to **klienta** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X lub Windows |
| [Curl](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |System Linux lub Windows |Linux, Unix, Mac OS X lub Windows |
| [Zestaw SDK dla platformy .NET usługi Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |System Linux lub Windows |Windows (na razie) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |System Linux lub Windows |Windows |

> [!IMPORTANT]  
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a name="pig-and-sql-server-integration-services"></a>Programów pig i SQL Server Integration Services

Do uruchomienia zadania Pig, można użyć programu SQL Server Integration Services (SSIS). Pakiety Azure Feature Pack dla usług SSIS zawiera następujące składniki, które działają z zadania Pig na HDInsight.

* [Zadanie usługi Azure HDInsight Pig][pigtask]

* [Menedżer połączeń w usłudze Azure subskrypcji][connectionmanager]

Dowiedz się więcej o Azure Feature Pack dla usług SSIS [tutaj][ssispack].

## <a id="nextsteps"></a>Następne kroki
Teraz, gdy wiesz jak korzystanie z języka Pig z HDInsight, użyj następujących linków, aby poznać inne sposoby pracy z usługi Azure HDInsight.

* [Przekazywanie danych do HDInsight](../hdinsight-upload-data.md)
* [Korzystanie z programu Apache Hive z usługą HDInsight][hdinsight-use-hive]
* [Za pomocą HDInsight przy użyciu narzędzia Apache Sqoop](hdinsight-use-sqoop.md)
* [Za pomocą usług Apache Oozie HDInsight](../hdinsight-use-oozie-linux-mac.md)
* [Korzystanie z zadań MapReduce z HDInsight][hdinsight-use-mapreduce]

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: https://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: https://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: https://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-mapreduce]:../hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
