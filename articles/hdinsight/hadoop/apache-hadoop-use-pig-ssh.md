---
title: Korzystanie z języka Hadoop Pig przy użyciu protokołu SSH w klastrze usługi HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak łączyć się z SSH, a następnie użyć polecenia Pig do uruchomienia instrukcji Pig Latin interakcyjnego lub w trybie wsadowym klastra opartą na systemie Linux platformą Hadoop.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b646a93b-4c51-4ba4-84da-3275d9124ebe
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: c296e01096480b85aea52ace69f25aff39e3bd2d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31401153"
---
# <a name="run-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Uruchamianie zadań Pig na klastrze opartych na systemie Linux przy użyciu polecenia Pig (SSH)

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Dowiedz się, jak interakcyjnego uruchamiania zadań Pig z połączenia SSH do klastra usługi HDInsight. Język programowania Pig Latin służy do opisywania przekształceń, które są stosowane do danych wejściowych w celu utworzenia żądanego wyniku.

> [!IMPORTANT]
> Kroki opisane w tym dokumencie wymagają klastra usługi HDInsight opartej na systemie Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a id="ssh"></a>Połącz przy użyciu protokołu SSH

Używanie protokołu SSH, aby nawiązać połączenie z klastrem usługi HDInsight. Poniższy przykład łączy do klastra o nazwie **myhdinsight** jako konta o nazwie **sshuser**:

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="pig"></a>Użyj polecenia Pig

1. Po nawiązaniu połączenia, należy uruchomić Pig interfejsu wiersza polecenia (CLI) za pomocą następującego polecenia:

    ```bash
    pig
    ```

    Po chwili zmieni się na`grunt>`.

2. Wprowadź następująca instrukcja:

    ```piglatin
    LOGS = LOAD '/example/data/sample.log';
    ```

    To polecenie ładuje zawartość pliku sample.log do DZIENNIKÓW. Można wyświetlić zawartość pliku za pomocą następujących instrukcji:

    ```piglatin
    DUMP LOGS;
    ```

3. Następnie przekształcać dane za pomocą wyrażenia regularnego można wyodrębnić poziom rejestrowania z każdego rekordu przy użyciu następujących instrukcji:

    ```piglatin
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    ```

    Można użyć **zrzutu** do wyświetlania danych po przekształceniu. W takim przypadku należy użyć `DUMP LEVELS;`.

4. Kontynuować stosowanie przekształceń przy użyciu instrukcje w poniższej tabeli:

    | Pig Latin — instrukcja | Jaki jest instrukcji |
    | ---- | ---- |
    | `FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;` | Usuwa wiersze, które zawierają wartość null dla poziomu dziennika i przechowuje wyniki w `FILTEREDLEVELS`. |
    | `GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;` | Grupuje wiersze według poziom dziennika i przechowuje wyniki w `GROUPEDLEVELS`. |
    | `FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;` | Tworzy zestaw danych zawierający każdego dziennika unikatową wartość poziomu i ile razy występuje. Zestaw danych jest przechowywany w `FREQUENCIES`. |
    | `RESULT = order FREQUENCIES by COUNT desc;` | Porządkuje poziomy dziennika według liczby (malejąco) i są przechowywane w `RESULT`. |

    > [!TIP]
    > Użyj `DUMP` do wyświetlania wyniku transformacji po każdym kroku.

5. Można także zapisać wyniki przekształcania za pomocą `STORE` instrukcji. Na przykład następująca instrukcja zapisuje `RESULT` do `/example/data/pigout` katalogu na domyślny magazyn dla klastra:

    ```piglatin
    STORE RESULT into '/example/data/pigout';
    ```

   > [!NOTE]
   > Dane są przechowywane w katalogu określonym w plikach o nazwie `part-nnnnn`. Jeśli katalog już istnieje, zostanie wyświetlony błąd.

6. Aby zamknąć wiersz grunt, wprowadź następującą instrukcję:

    ```piglatin
    QUIT;
    ```

### <a name="pig-latin-batch-files"></a>Pig Latin pliki wsadowe

Polecenie Pig umożliwia również uruchomić Pig Latin zawarte w pliku.

1. Po wyjściu z wiersza grunt, użyj następującego polecenia, aby utworzyć plik o nazwie `pigbatch.pig`:

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

    Gdy skończysz, użyj __Ctrl__ + __X__, __Y__, a następnie __Enter__ można zapisać pliku.

3. Użyj następującego polecenia do uruchomienia `pigbatch.pig` pliku za pomocą polecenia Pig.

    ```bash
    pig ~/pigbatch.pig
    ```

    Po zakończeniu zadania wsadowego, zapoznaj się następujące dane wyjściowe:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)


## <a id="nextsteps"></a>Następne kroki

Ogólne informacje na temat Pig w usłudze HDInsight zobacz następujący dokument:

* [Korzystanie z języka Pig z usługą Hadoop w usłudze HDInsight](hdinsight-use-pig.md)

Aby uzyskać więcej informacji na inne sposoby pracy z platformą Hadoop w usłudze HDInsight można znaleźć w następujących dokumentach:

* [Korzystanie z programu Hive z usługą Hadoop w usłudze HDInsight](hdinsight-use-hive.md)
* [Używanie MapReduce z usługą Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md)
