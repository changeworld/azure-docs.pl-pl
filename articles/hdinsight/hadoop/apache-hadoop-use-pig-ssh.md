---
title: Use Apache Pig przy użyciu protokołu SSH w klastrze usługi HDInsight — Azure
description: Dowiedz się, jak połączyć do klastra Apache Hadoop oparte na systemie Linux przy użyciu protokołu SSH, a następnie użyj polecenia Pig do uruchomienia instrukcji Pig Latin interaktywnie lub w trybie wsadowym.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 503449e36b2b11e5663449dd732fdaf785417570
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110754"
---
# <a name="run-apache-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Uruchamiać zadania Apache Pig klastrze opartych na systemie Linux przy użyciu polecenia Pig (SSH)

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Dowiedz się, jak interaktywnie uruchamiać zadania Apache Pig z połączenia SSH do klastra usługi HDInsight. Język programowania Pig Latin służy do opisywania przekształceń, które są stosowane do danych wejściowych żądaną produktu wyjściowego.

> [!IMPORTANT]  
> Kroki opisane w tym dokumencie wymagają klastra HDInsight opartych na systemie Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a id="ssh"></a>Połącz przy użyciu protokołu SSH

Za pomocą protokołu SSH Połącz się z klastrem usługi HDInsight. Poniższy przykład łączy się klastra o nazwie **myhdinsight** jako konta o nazwie **sshuser**:

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="pig"></a>Użyj polecenia Apache Pig

1. Po nawiązaniu połączenia, należy uruchomić Pig interfejsu wiersza polecenia (CLI) przy użyciu następującego polecenia:

    ```bash
    pig
    ```

    Po chwili wiersz polecenia zmieni się na`grunt>`.

2. Wprowadź następującą instrukcję:

    ```piglatin
    LOGS = LOAD '/example/data/sample.log';
    ```

    To polecenie ładuje zawartość pliku sample.log do DZIENNIKÓW. Możesz wyświetlić zawartość pliku przy użyciu następujących instrukcji:

    ```piglatin
    DUMP LOGS;
    ```

3. Następnie można przekształcać dane za pomocą wyrażeń regularnych do wyodrębniania poziom rejestrowania z każdego rekordu przy użyciu następujących instrukcji:

    ```piglatin
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    ```

    Możesz użyć **zrzutu** do wyświetlania danych po transformacji. W takim przypadku należy użyć `DUMP LEVELS;`.

4. Kontynuuj, stosowanie przekształceń przy użyciu instrukcji w poniższej tabeli:

    | Instrukcji pig Latin | Działanie instrukcji |
    | ---- | ---- |
    | `FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;` | Usuwa wiersze, które zawierają wartości null do określenia poziomu dziennika i zapisuje wyniki w `FILTEREDLEVELS`. |
    | `GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;` | Grupuje wiersze według poziomu dziennika i zapisuje wyniki w `GROUPEDLEVELS`. |
    | `FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;` | Tworzy zestaw danych zawierający każdego dziennika unikatową wartość poziomu i ile razy występuje. Zestaw danych jest przechowywany w `FREQUENCIES`. |
    | `RESULT = order FREQUENCIES by COUNT desc;` | Porządkuje poziomy dziennika według liczby (malejąco) i zapisuje w `RESULT`. |

    > [!TIP]  
    > Użyj `DUMP` Aby wyświetlić wynik przekształcenia po każdym kroku.

5. Można także zapisać wyniki przekształcenia przy użyciu `STORE` instrukcji. Na przykład następująca instrukcja zapisuje `RESULT` do `/example/data/pigout` katalogu domyślny magazyn dla klastra:

    ```piglatin
    STORE RESULT into '/example/data/pigout';
    ```

   > [!NOTE]  
   > Dane są przechowywane w wybranym katalogu w plikach o nazwie `part-nnnnn`. Jeśli katalog już istnieje, otrzymasz komunikat o błędzie.

6. Aby zamknąć wiersz grunt, wprowadź następującą instrukcję:

    ```piglatin
    QUIT;
    ```

### <a name="pig-latin-batch-files"></a>Pig Latin, pliki wsadowe

Polecenie Pig umożliwia również Uruchom Pig Latin zawarte w pliku.

1. Po zamknięciu wiersza grunt, użyj następującego polecenia, aby utworzyć plik o nazwie `pigbatch.pig`:

    ```bash
    nano ~/pigbatch.pig
    ```

2. Wpisz lub wklej następujące wiersze:

    ```piglatin
    LOGS = LOAD '/example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;
    ```

    Po zakończeniu użyj __Ctrl__ + __X__, __Y__, a następnie __Enter__ można zapisać pliku.

3. Użyj następującego polecenia, aby uruchomić `pigbatch.pig` plików za pomocą polecenia Pig.

    ```bash
    pig ~/pigbatch.pig
    ```

    Po zakończeniu zadania usługi batch, zostaną wyświetlone następujące dane wyjściowe:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)


## <a id="nextsteps"></a>Następne kroki

Aby uzyskać ogólne informacje na temat technologii Pig w HDInsight zobacz następujący dokument:

* [Use Apache Pig z platformą Apache Hadoop w HDInsight](hdinsight-use-pig.md)

Aby uzyskać więcej informacji na temat innych sposobów pracy z platformą Hadoop w HDInsight zobacz następujące dokumenty:

* [Apache Hive za pomocą technologii Apache Hadoop w HDInsight](hdinsight-use-hive.md)
* [Korzystanie z technologii MapReduce z platformą Apache Hadoop w HDInsight](hdinsight-use-mapreduce.md)
