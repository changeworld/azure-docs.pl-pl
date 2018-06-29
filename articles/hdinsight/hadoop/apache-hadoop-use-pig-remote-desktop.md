---
title: Korzystanie z języka Pig Hadoop przy użyciu pulpitu zdalnego w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać polecenia Pig do uruchomienia instrukcji Pig Latin połączenie pulpitu zdalnego do klastra z systemem Windows Hadoop w usłudze HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e034a286-de0f-465f-8bf1-3d085ca6abed
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/17/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: f293aab67fb5e952dc32e20a6370d45cded682b5
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "35762001"
---
# <a name="run-pig-jobs-from-a-remote-desktop-connection"></a>Uruchamianie zadań Pig połączenie pulpitu zdalnego
[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Ten dokument zawiera wskazówki dotyczące korzystania z polecenia Pig do uruchomienia instrukcji Pig Latin połączenie pulpitu zdalnego do klastra usługi HDInsight opartej na systemie Windows. Pig Latin umożliwia tworzenie aplikacji MapReduce przez opisywania przekształceń danych, a nie mapy i zmniejszyć funkcji.

> [!IMPORTANT]
> Pulpit zdalny jest dostępna tylko w klastrach HDInsight, które używają systemu Windows jako system operacyjny. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).
>
> Dla usługi HDInsight w wersji 3.4 lub większą, zobacz [Use Pig HDInsight i SSH](apache-hadoop-use-pig-ssh.md) informacji na temat interakcyjnego uruchamiania zadań Pig bezpośrednio w klastrze z wiersza polecenia.

## <a id="prereq"></a>Wymagania wstępne
Aby wykonać kroki opisane w tym artykule, potrzebne następujące elementy.

* Klastra z systemem Windows HDInsight (Hadoop w usłudze HDInsight)
* Komputer kliencki z systemem Windows 10, Windows 8 lub Windows 7

## <a id="connect"></a>Uzyskuj dostęp do usług pulpitu zdalnego
Włączenie pulpitu zdalnego dla klastra usługi HDInsight, a następnie nawiązać zgodnie z instrukcjami w [Connect do klastrów usługi HDInsight za pomocą protokołu RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="pig"></a>Użyj polecenia Pig
1. Po utworzeniu połączenia pulpitu zdalnego rozpocząć **wiersza polecenia platformy Hadoop** przy użyciu ikony na pulpicie.
2. Aby uruchomić polecenie Pig, skorzystaj z następujących:

        %pig_home%\bin\pig

    Użytkownik zobaczy `grunt>` wiersza.
3. Wprowadź następująca instrukcja:

        LOGS = LOAD 'wasb:///example/data/sample.log';

    To polecenie ładuje zawartość pliku sample.log do plików DZIENNIKÓW. Można wyświetlić zawartość pliku za pomocą następującego polecenia:

        DUMP LOGS;
4. Przekształcanie danych za pomocą wyrażenia regularnego można wyodrębnić poziom rejestrowania z każdego rekordu:

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Można użyć **zrzutu** do wyświetlania danych po przekształceniu. W takim przypadku `DUMP LEVELS;`.
5. Kontynuować stosowanie przekształceń za pomocą następujących instrukcji. Użyj `DUMP` do wyświetlania wyniku transformacji po każdym kroku.

    <table>
    <tr>
    <th>Oświadczenie</th><th>Wyniki działania</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = poziomy filtru przez LOGLEVEL nie ma wartości null;</td><td>Usuwa wiersze, które zawierają wartość null dla poziomu dziennika i przechowuje wyniki w FILTEREDLEVELS.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = FILTEREDLEVELS grupy przez LOGLEVEL;</td><td>Grupuje wiersze według poziom dziennika i przechowuje wyniki w GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>CZĘSTOTLIWOŚCI = foreach GROUPEDLEVELS Generowanie grupy jako LOGLEVEL, COUNT (FILTEREDLEVELS. LOGLEVEL) jako liczba;</td><td>Tworzy nowy zestaw danych zawierający każdego dziennika unikatową wartość poziomu i ile razy występuje. To jest przechowywane w częstotliwości</td>
    </tr>
    <tr>
    <td>WYNIK = kolejności częstotliwości przez liczbę desc;</td><td>Porządkuje poziomy dziennika według liczby (malejąco) i są przechowywane w wyniku</td>
    </tr>
</table>

6. Można także zapisać wyniki przekształcania za pomocą `STORE` instrukcji. Na przykład następujące polecenie zapisuje `RESULT` do **/example/data/pigout** katalogu w domyślnego kontenera magazynu dla klastra:

        STORE RESULT into 'wasb:///example/data/pigout'

   > [!NOTE]
   > Dane są przechowywane w katalogu określonym w plikach o nazwie **nnnnn części**. Jeśli katalog już istnieje, zostanie wyświetlony komunikat o błędzie.
   >
   >
   
7. Aby zamknąć wiersz grunt, wprowadź następującą instrukcję.

        QUIT;

### <a name="pig-latin-batch-files"></a>Pig Latin pliki wsadowe
Polecenie Pig umożliwia również uruchomić Pig Latin, który jest zawarty w pliku.

1. Po zakończeniu wiersza grunt, otwórz **Notatnik** i Utwórz nowy plik o nazwie **pigbatch.pig** w **PIG_HOME %** katalogu.
2. Wpisz lub wklej następujące wiersze do **pigbatch.pig** pliku, a następnie zapisz go:

        LOGS = LOAD 'wasb:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;
3. Użyj następujących czynności, aby uruchomić **pigbatch.pig** plików za pomocą polecenia pig.

        pig %PIG_HOME%\pigbatch.pig

    Po zakończeniu zadania wsadowego, powinny pojawić się następujące dane wyjściowe, która powinna być taka sama jak kiedy używany `DUMP RESULT;` w poprzednich krokach:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="summary"></a>Podsumowanie
Jak widać, polecenie Pig umożliwia interakcyjnego uruchamiania operacji MapReduce lub uruchamianie zadań Pig Latin, które są przechowywane w pliku wsadowym.

## <a id="nextsteps"></a>Następne kroki
Aby uzyskać ogólne informacje na temat Pig w usłudze HDInsight:

* [Korzystanie z języka Pig z usługą Hadoop w usłudze HDInsight](hdinsight-use-pig.md)

Aby uzyskać informacje o innych metodach pracy z platformą Hadoop w usłudze HDInsight:

* [Korzystanie z programu Hive z usługą Hadoop w usłudze HDInsight](hdinsight-use-hive.md)
* [Używanie MapReduce z usługą Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md)
