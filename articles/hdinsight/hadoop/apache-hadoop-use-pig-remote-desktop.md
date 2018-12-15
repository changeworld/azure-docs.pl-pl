---
title: Apache Pig za pomocą pulpitu zdalnego w HDInsight — Azure
description: Dowiedz się, jak polecenie Pig służy do uruchomienia instrukcji Apache Pig Latin za pośrednictwem połączenia pulpitu zdalnego w klastrze Apache Hadoop oparte na Windows na platformie HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/17/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 69eeadd8252d20dd25ac6d0abbbf0c6c158383d5
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438629"
---
# <a name="run-apache-pig-jobs-from-a-remote-desktop-connection"></a>Uruchamiać zadania Apache Pig za pośrednictwem połączenia pulpitu zdalnego
[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Ten dokument zawiera wskazówki dotyczące korzystania z polecenia Apache Pig do uruchomienia instrukcji Pig Latin za pośrednictwem połączenia pulpitu zdalnego w klastrze HDInsight z systemem Windows. Pig Latin umożliwia tworzenie aplikacji MapReduce poprzez opisanie przekształcenia danych zamiast mapowania i redukcji funkcji.

> [!IMPORTANT]  
> Pulpit zdalny jest dostępna tylko w klastrach HDInsight, które używają Windows jako systemu operacyjnego. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).
>
> HDInsight 3.4 lub nowszej, zobacz [Use Apache Pig HDInsight i SSH](apache-hadoop-use-pig-ssh.md) informacji na temat interakcyjnego uruchamiania zadania Pig bezpośrednio w klastrze z wiersza polecenia.

## <a id="prereq"></a>Wymagania wstępne
Aby wykonać kroki opisane w tym artykule, potrzebne następujące elementy.

* Klaster HDInsight dla komputerów z systemem Windows (platformy Hadoop w HDInsight)
* Komputer kliencki z systemem Windows 10, Windows 8 lub Windows 7

## <a id="connect"></a>Połącz przy użyciu pulpitu zdalnego
Włączanie pulpitu zdalnego dla klastra HDInsight, a następnie nawiązać z nim, postępując zgodnie z instrukcjami na [nawiązywanie połączenia z klastrami HDInsight przy użyciu protokołu RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="pig"></a>Użyj polecenia Pig
1. Po nawiązaniu połączenia z pulpitem zdalnym, uruchom **wiersza polecenia usługi Hadoop** przy użyciu ikony na pulpicie.
2. Uruchom polecenie Pig za pomocą następujących czynności:

        %pig_home%\bin\pig

    Użytkownik zobaczy `grunt>` wiersza.
3. Wprowadź następującą instrukcję:

        LOGS = LOAD 'wasb:///example/data/sample.log';

    To polecenie ładuje zawartość pliku sample.log do plików DZIENNIKÓW. Możesz wyświetlić zawartość pliku przy użyciu następującego polecenia:

        DUMP LOGS;
4. Przekształcanie danych za pomocą wyrażeń regularnych do wyodrębniania poziom rejestrowania z każdego rekordu:

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Możesz użyć **zrzutu** do wyświetlania danych po transformacji. W tym przypadku `DUMP LEVELS;`.
5. Kontynuuj, stosowanie przekształceń przy użyciu następujących instrukcji. Użyj `DUMP` Aby wyświetlić wynik przekształcenia po każdym kroku.

    <table>
    <tr>
    <th>Oświadczenie</th><th>Wyniki działania</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = poziomie LOGLEVEL z filtru nie jest zerowa;</td><td>Usuwa wiersze, które zawierają wartości null do określenia poziomu dziennika i przechowuje wyniki w FILTEREDLEVELS.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = FILTEREDLEVELS grupy przez LOGLEVEL;</td><td>Grupuje wiersze według poziomu dziennika i przechowuje wyniki w GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>CZĘSTOTLIWOŚCI = foreach GROUPEDLEVELS Generowanie grupy jako LOGLEVEL, liczba (FILTEREDLEVELS. LOGLEVEL) jako liczba;</td><td>Tworzy nowy zestaw danych zawierający każdego dziennika unikatową wartość poziomu i ile razy występuje. To jest przechowywany w częstotliwości</td>
    </tr>
    <tr>
    <td>WYNIK = order częstotliwości według malejącej liczbę;</td><td>Porządkuje poziomy dziennika według liczby (malejąco) i są przechowywane w wyniku</td>
    </tr>
</table>

6. Można także zapisać wyniki przekształcenia przy użyciu `STORE` instrukcji. Na przykład poniższe polecenie zapisuje `RESULT` do **/example/data/pigout** katalogu w domyślnym kontenerze magazynu dla klastra:

        STORE RESULT into 'wasb:///example/data/pigout'

   > [!NOTE]  
   > Dane są przechowywane w wybranym katalogu w plikach o nazwie **nnnnn część**. Jeśli katalog już istnieje, otrzymasz komunikat o błędzie.
   >
   >
   
7. Aby zamknąć wiersz grunt, wprowadź następującą instrukcję.

        QUIT;

### <a name="pig-latin-batch-files"></a>Pig Latin, pliki wsadowe
Polecenie Pig umożliwia również Uruchom Pig Latin, który jest zawarty w pliku.

1. Po zamknięciu wiersza grunt, otwórz **Notatnik** i Utwórz nowy plik o nazwie **pigbatch.pig** w **PIG_HOME %** katalogu.
2. Wpisz lub wklej następujące wiersze do **pigbatch.pig** pliku, a następnie zapisz go:

        LOGS = LOAD 'wasb:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;
3. Poniższa tabela Uruchom **pigbatch.pig** plików za pomocą polecenia pig.

        pig %PIG_HOME%\pigbatch.pig

    Po zakończeniu zadania usługi batch, powinien zostać wyświetlony następujące dane wyjściowe, która powinna być taka sama jak kiedy użyć `DUMP RESULT;` w poprzednich krokach:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="summary"></a>Podsumowanie
Jak widać, polecenie Pig pozwala interaktywnie Uruchom operacji MapReduce lub uruchomienie zadania Pig Latin, które są przechowywane w pliku wsadowym.

## <a id="nextsteps"></a>Następne kroki
Aby uzyskać ogólne informacje na temat Pig w HDInsight:

* [Use Apache Pig z platformą Apache Hadoop w HDInsight](hdinsight-use-pig.md)

Aby uzyskać informacje o innych metodach można pracować z platformą Hadoop w HDInsight:

* [Apache Hive za pomocą technologii Apache Hadoop w HDInsight](hdinsight-use-hive.md)
* [Korzystanie z technologii MapReduce z platformą Apache Hadoop w HDInsight](hdinsight-use-mapreduce.md)
