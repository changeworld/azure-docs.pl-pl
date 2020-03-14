---
title: Apache Ambari aby zoptymalizować konfiguracje klastrów — Azure HDInsight
description: Użyj interfejsu użytkownika sieci Web Apache Ambari w celu skonfigurowania i zoptymalizowania klastrów usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: 15a2c75a7619a815655be0fd9fd3044d86acd057
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79272567"
---
# <a name="use-apache-ambari-to-optimize-hdinsight-cluster-configurations"></a>Optymalizowanie konfiguracji klastra usługi HDInsight przy użyciu oprogramowania Apache Ambari

Usługa HDInsight udostępnia klastry [Apache Hadoop](https://hadoop.apache.org/) dla aplikacji do przetwarzania danych na dużą skalę. Zarządzanie, monitorowanie i optymalizowanie złożonych klastrów wielowęzłowych może być trudne. [Apache Ambari](https://ambari.apache.org/) to interfejs sieci Web umożliwiający zarządzanie i monitorowanie klastrów usługi HDInsight w systemie Linux.  W przypadku klastrów systemu Windows należy użyć [interfejsu API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

Aby zapoznać się z wprowadzeniem do korzystania z interfejsu użytkownika sieci Web Ambari, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md)

Zaloguj się do Ambari w `https://CLUSTERNAME.azurehdidnsight.net` z poświadczeniami klastra. Na ekranie początkowym zostanie wyświetlony pulpit nawigacyjny przegląd.

![Wyświetlany pulpit nawigacyjny użytkownika Apache Ambari](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

Interfejs użytkownika sieci Web Ambari może służyć do zarządzania hostami, usługami, alertami, konfiguracjami i widokami. Ambari nie można użyć do utworzenia klastra usługi HDInsight, uaktualnienia usług, zarządzania stosów i wersji, likwidowania lub rewizji hostów lub dodawania usług do klastra.

## <a name="manage-your-clusters-configuration"></a>Zarządzanie konfiguracją klastra

Ustawienia konfiguracji ułatwiają dostrajanie określonej usługi. Aby zmodyfikować ustawienia konfiguracji usługi, wybierz usługę z paska bocznego **usług** (po lewej stronie), a następnie przejdź **do karty konfiguracje** na stronie Szczegóły usługi.

![Pasek boczny usług Apache Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Modyfikuj rozmiar sterty Java NameNode

Rozmiar sterty Java NameNode zależy od wielu czynników, takich jak obciążenie klastra, liczby plików i liczby bloków. Domyślny rozmiar 1 GB działa dobrze w przypadku większości klastrów, chociaż niektóre obciążenia mogą wymagać więcej lub mniejszej ilości pamięci.

Aby zmodyfikować rozmiar sterty języka Java NameNode:

1. Na pasku bocznym usługi wybierz pozycję **HDFS** i przejdź **do karty konfiguracje** .

    ![Konfiguracja systemu Apache Ambari HDFS](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. Znajdź ustawienie **NameNode rozmiar sterty Java**. Możesz również użyć pola tekstowego **Filtr** , aby wpisać i znaleźć określone ustawienie. Wybierz ikonę **pióra** obok nazwy ustawienia.

    ![Rozmiar sterty Java Ambari NameNode](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. Wpisz nową wartość w polu tekstowym, a następnie naciśnij klawisz **Enter** , aby zapisać zmiany.

    ![Ambari Edytuj NameNode stertę Java size1](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. Rozmiar sterty Java NameNode jest zmieniany na 1 GB z 2 GB.

    ![Edytowanie sterty NameNode Java Size2](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Zapisz zmiany, klikając zielony przycisk **Zapisz** znajdujący się u góry ekranu konfiguracja.

    ![Ambari Ambari Zapisz konfiguracje](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="apache-hive-optimization"></a>Optymalizacja Apache Hive

W poniższych sekcjach opisano opcje konfiguracji dotyczące optymalizowania ogólnej wydajności Apache Hive.

1. Aby zmodyfikować parametry konfiguracji programu Hive, wybierz pozycję **Hive** z paska bocznego usług.
1. Przejdź **do karty konfiguracje** .

### <a name="set-the-hive-execution-engine"></a>Ustawianie aparatu wykonywania programu Hive

Program Hive oferuje dwa aparaty wykonywania: [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) i [Apache tez](https://tez.apache.org/). Tez jest większa niż MapReduce. Klastry usługi HDInsight w systemie Linux mają tez jako domyślny aparat wykonywania. Aby zmienić aparat wykonywania:

1. Na **karcie konfiguracje** programu Hive wpisz **aparat wykonywania** w polu Filtr.

    ![Aparat wykonywania wyszukiwania Apache Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-search-execution.png)

1. Wartość domyślna właściwości **optymalizacji** to **tez**.

    ![Optymalizacja — aparat Apache Tez](./media/hdinsight-changing-configs-via-ambari/optimization-apache-tez.png)

### <a name="tune-mappers"></a>Dostrajaj mapowania

Usługa Hadoop próbuje podzielić (*zmapować*) pojedynczy plik na wiele plików i przetwarzać pliki w sposób równoległy. Liczba odwzorowań zależy od liczby podziałów. Dwa następujące parametry konfiguracji dotyczą liczby podziałów dla aparatu wykonywania tez:

* `tez.grouping.min-size`: Dolny limit rozmiaru zgrupowanego podziału przy wartości domyślnej wynoszącej 16 MB (16 777 216 bajtów).
* `tez.grouping.max-size`: górny limit rozmiaru zgrupowanego podziału z wartością domyślną 1 GB (1 073 741 824 bajtów).

W ramach reguły wydajności przycisku przewijania Zmniejsz oba te parametry, aby zwiększyć opóźnienia, i zwiększyć przepływność.

Na przykład, aby ustawić cztery zadania mapowania dla rozmiaru danych 128 MB, należy ustawić oba parametry na 32 MB dla każdego (33 554 432 bajty).

1. Aby zmodyfikować parametry limitu, przejdź **do karty konfiguracje** usługi tez. Rozwiń panel **Ogólne** i Znajdź `tez.grouping.max-size` i `tez.grouping.min-size` parametry.

1. Ustaw oba parametry na **33 554 432** bajtów (32 MB).

    ![Rozmiary grupowania Apache Ambari tez](./media/hdinsight-changing-configs-via-ambari/apache-tez-grouping-size.png)

Te zmiany wpływają na wszystkie zadania tez na serwerze. Aby uzyskać optymalny wynik, wybierz odpowiednie wartości parametrów.

### <a name="tune-reducers"></a>Dostrajaj ograniczenia

Funkcje [Apache Orc](https://orc.apache.org/) i [przyciągania](https://google.github.io/snappy/) zapewniają wysoką wydajność. Jednak program Hive może mieć domyślnie zbyt mało zmniejszeń, co powoduje wąskie gardła.

Załóżmy na przykład, że masz rozmiar danych wejściowych wynoszący 50 GB. Te dane w formacie ORC z kompresją przyciągania to 1 GB. Program Hive szacuje liczbę elementów ograniczających potrzebną jako: (liczba bajtów wejściowych do mapera/`hive.exec.reducers.bytes.per.reducer`).

W przypadku ustawień domyślnych ten przykład dotyczy 4 obniżyć.

Parametr `hive.exec.reducers.bytes.per.reducer` określa liczbę bajtów przetworzonych na zmniejszenie. Wartość domyślna to 64 MB. Dostrajanie tej wartości powoduje zwiększenie równoległości i może poprawić wydajność. Dostrajanie go za mało może również generować zbyt wiele obniżyć, co może mieć negatywny wpływ na wydajność. Ten parametr jest oparty na określonych wymaganiach dotyczących danych, ustawieniach kompresji i innych czynnikach środowiskowych.

1. Aby zmodyfikować parametr, przejdź **do karty konfiguracje** programu Hive i Znajdź na stronie Ustawienia parametr **dane dla ograniczenia** .

    ![Dane Apache Ambari na zmniejszenie](./media/hdinsight-changing-configs-via-ambari/ambari-data-per-reducer.png)

1. Wybierz pozycję **Edytuj** , aby zmodyfikować wartość na 128 MB (134 217 728 bajtów), a następnie naciśnij klawisz **Enter** , aby zapisać.

    ![Ambari danych na mniejszą liczbę modyfikacji](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Mając rozmiar danych wejściowych wynoszący 1 024 MB, z 128 MB na mniejszą liczbę elementów, istnieje 8 obniżyć (1024/128).

1. Niepoprawna wartość w parametrze **danych na zmniejszenie** wartości może spowodować powstanie dużej liczby obniżyć wydajności zapytań. Aby ograniczyć maksymalną liczbę elementów ograniczających, należy ustawić `hive.exec.reducers.max` na odpowiednią wartość. Wartość domyślna to 1009.

### <a name="enable-parallel-execution"></a>Włącz wykonywanie równoległe

Zapytanie programu Hive jest wykonywane w jednym lub kilku etapach. Jeśli niezależne etapy można uruchomić równolegle, spowoduje to zwiększenie wydajności zapytania.

1. Aby włączyć równoległe wykonywanie zapytań, przejdź do karty **Konfiguracja** programu Hive i wyszukaj Właściwość `hive.exec.parallel`. Wartość domyślna to false. Zmień wartość na true, a następnie naciśnij klawisz **Enter** , aby zapisać wartość.

1. Aby ograniczyć liczbę zadań, które mają być uruchamiane równolegle, zmodyfikuj Właściwość `hive.exec.parallel.thread.number`. Wartość domyślna to 8.

    ![Wyświetlanie równoległe Apache Hive exec](./media/hdinsight-changing-configs-via-ambari/apache-hive-exec-parallel.png)

### <a name="enable-vectorization"></a>Włącz wektoryzacji

Program Hive przetwarza wiersz danych według wiersza. Wektoryzacji kieruje gałąź do przetwarzania danych w blokach wierszy 1 024, a nie w jednym wierszu naraz. Wektoryzacji ma zastosowanie tylko do formatu pliku ORC.

1. Aby włączyć wektorowe wykonywanie zapytań, przejdź **do karty konfiguracje** programu Hive i wyszukaj parametr `hive.vectorized.execution.enabled`. Wartość domyślna to true dla programu Hive 0.13.0 lub nowszego.

1. Aby włączyć wykonywanie wektorowe dla strony ze zmniejszeniem zapytania, ustaw dla parametru `hive.vectorized.execution.reduce.enabled` wartość true. Wartość domyślna to false.

    ![Apache Hive wykonywanie wektorowe](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Włącz optymalizację opartą na kosztach (CBO)

Domyślnie gałąź jest zgodna z zestawem reguł, aby znaleźć jeden optymalny plan wykonywania zapytań. Optymalizacja oparta na kosztach (CBO) szacuje wiele planów, aby wykonać zapytanie i przypisuje koszt do każdego planu, a następnie określa najtańszy plan wykonywania zapytania.

Aby włączyć program CBO, przejdź do opcji **Hive** > **configs** > **Ustawienia** i Znajdź pozycję **Włącz Optymalizator oparty na kosztach**, a następnie Przełącz przycisk przełączania na pozycję **włączone**.

![Optymalizator oparty na kosztach usługi HDInsight](./media/hdinsight-changing-configs-via-ambari/hdinsight-cbo-config.png)

Następujące dodatkowe parametry konfiguracji zwiększają wydajność zapytań Hive, gdy jest włączony CBO:

* `hive.compute.query.using.stats`

    Gdy ma wartość true, Hive używa statystyk przechowywanych w magazynie metadanych do odpowiedzi prostych zapytań, takich jak `count(*)`.

    ![Apache Hive kwerendy obliczeniowej przy użyciu statystyk](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Statystyki kolumn są tworzone, gdy funkcja CBO jest włączona. Program Hive używa statystyk kolumn, które są przechowywane w magazynie metadanych w celu optymalizowania zapytań. Pobieranie statystyk kolumn dla każdej kolumny trwa dłużej, gdy liczba kolumn jest wysoka. Po ustawieniu na wartość false to ustawienie wyłącza Pobieranie statystyk kolumn z magazynu metadanych.

    ![Statystyka kolumny zestawu statystyk Apache Hive](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Podstawowe statystyki partycji, takie jak liczba wierszy, rozmiar danych i rozmiar pliku, są przechowywane w magazynie metadanych. Po ustawieniu na wartość true statystyki partycji są pobierane z magazynu metadanych. W przypadku wartości false rozmiar pliku jest pobierany z systemu plików, a liczba wierszy jest pobierana z schematu wiersza.

    ![Statystyka partycji zestawu statystyk Hive](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Włącz kompresję pośrednią

Zadania map tworzą pliki pośrednie, które są używane przez zadania zmniejszania. Kompresja pośrednia zmniejsza rozmiar pliku pośredniego.

Zadania usługi Hadoop są zwykle w wąskim obłączeniem we/wy. Kompresowanie danych może przyspieszyć operacji we/wy i ogólnego transferu sieciowego.

Dostępne typy kompresji to:

| Format | Narzędzie | Algorytm | Rozszerzenie pliku | Podzielne? |
| -- | -- | -- | -- | -- |
| Gzip | Gzip | WKLĘŚNIĘCIE | .gz | Nie |
| Bzip2 | Bzip2 | Bzip2 |.bz2 | Tak |
| LZO | Lzop | LZO | .lzo | Tak, jeśli indeksowane |
| Snappy | N/D | Snappy | Snappy | Nie |

Ogólną zasadą jest, że posiadanie podziału metody kompresji jest ważne, w przeciwnym razie zostanie utworzone mapowanie. Jeśli dane wejściowe są tekstowe, `bzip2` jest najlepszą opcją. W przypadku formatu ORC przyciąganie jest najszybszą opcją kompresji.

1. Aby włączyć kompresję pośrednią, przejdź do **karty konfiguracje** programu Hive, a następnie ustaw dla parametru `hive.exec.compress.intermediate` wartość true. Wartość domyślna to false.

    ![Pośrednika kompresji programu Hive exec](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > Aby skompresować pliki pośrednie, wybierz koder-dekoder kompresji z niższym kosztem procesora, nawet jeśli koder-dekoder nie ma danych wyjściowych o dużej kompresji.

1. Aby ustawić pośredni koder-dekoder kompresji, Dodaj właściwość niestandardową `mapred.map.output.compression.codec` do pliku `hive-site.xml` lub `mapred-site.xml`.

1. Aby dodać ustawienie niestandardowe:

    a. Przejdź do **gałęzi** > **konfiguracjami** > **Advanced** > **Custom Hive-site**.

    b. Wybierz pozycję **Dodaj właściwość...** w dolnej części okienka niestandardowe gałęzie — lokacja.

    c. W oknie Dodawanie właściwości wprowadź `mapred.map.output.compression.codec` jako klucz i `org.apache.hadoop.io.compress.SnappyCodec` jako wartość.

    d. Wybierz pozycję **Dodaj**.

    ![Apache Hive dodanie właściwości niestandardowej](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Spowoduje to skompresowanie pliku pośredniego za pomocą kompresji przyciągania. Po dodaniu właściwości zostanie ona wyświetlona w okienku niestandardowe gałęzie — lokacja.

    > [!NOTE]  
    > Ta procedura modyfikuje plik `$HADOOP_HOME/conf/hive-site.xml`.

### <a name="compress-final-output"></a>Kompresuj końcowe dane wyjściowe

Ostateczne dane wyjściowe programu Hive mogą być również skompresowane.

1. Aby skompresować końcową wersję wyjściową programu Hive, przejdź **do karty konfiguracje** programu Hive, a następnie ustaw dla parametru `hive.exec.compress.output` wartość true. Wartość domyślna to false.

1. Aby wybrać koder-dekoder kompresji wyjściowej, Dodaj `mapred.output.compression.codec` właściwość niestandardową do okienka niestandardowa gałąź aplikacji, zgodnie z opisem w poprzedniej sekcji Krok 3.

    ![Apache Hive właściwości niestandardowej ADD2](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Włącz wykonywanie spekulacyjne

Wykonanie spekulacyjne uruchamia określoną liczbę zduplikowanych zadań w celu wykrywania i zabronionego długotrwałego śledzenia zadań, przy jednoczesnym poprawieniu całkowitego wykonywania zadań, optymalizując poszczególne wyniki zadań.

Wykonanie spekulacyjne nie powinno być włączone dla długotrwałych zadań MapReduce z dużymi ilościami danych wejściowych.

* Aby włączyć wykonywanie spekulacyjne, przejdź **do karty konfiguracje** programu Hive, a następnie ustaw dla parametru `hive.mapred.reduce.tasks.speculative.execution` wartość true. Wartość domyślna to false.

    ![Mapred Hive zmniejsza zadania spekulacyjne wykonywanie](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Dostrajanie partycji dynamicznych

Program Hive umożliwia tworzenie partycji dynamicznych podczas wstawiania rekordów do tabeli bez definiowania wstępnie zdefiniowanych i każdej partycji. Jest to zaawansowana funkcja, chociaż może to spowodować utworzenie dużej liczby partycji i dużej liczby plików dla każdej partycji.

1. W przypadku platformy Hive do wykonywania partycji dynamicznych wartość parametru `hive.exec.dynamic.partition` powinna być równa true (domyślnie).

1. Zmień tryb partycji dynamicznej na *Strict*. W trybie ścisłym co najmniej jedna partycja musi być statyczna. Zapobiega to zapytaniom bez filtru partycji w klauzuli WHERE, oznacza to, że *rygorystyczne* uniemożliwiają zapytania, które skanują wszystkie partycje. Przejdź **do karty konfiguracje** programu Hive, a następnie ustaw `hive.exec.dynamic.partition.mode` na **Strict**. Wartość domyślna to **nierygorystyczne**.

1. Aby ograniczyć liczbę partycji dynamicznych do utworzenia, zmodyfikuj parametr `hive.exec.max.dynamic.partitions`. Wartość domyślna to 5000.

1. Aby ograniczyć łączną liczbę partycji dynamicznych na węzeł, zmodyfikuj `hive.exec.max.dynamic.partitions.pernode`. Wartość domyślna to 2000.

### <a name="enable-local-mode"></a>Włącz tryb lokalny

Tryb lokalny umożliwia Hive wykonywanie wszystkich zadań zadania na jednej maszynie lub czasami w pojedynczym procesie. Zwiększa to wydajność zapytań, jeśli dane wejściowe są małe, a obciążenie uruchamiania zadań dla zapytań zużywa znaczną część całkowitego wykonania zapytania.

Aby włączyć tryb lokalny, należy dodać parametr `hive.exec.mode.local.auto` do niestandardowego panelu witryny Hive, jak wyjaśniono w kroku 3 w sekcji [Włączanie kompresji pośredniej](#enable-intermediate-compression) .

![Lokalne Autokonfiguracja trybu Apache Hive exec](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Ustaw pojedyncze MapReduce wielogrupuj według

Gdy ta właściwość ma wartość true, kwerenda wielogrupowanie według wspólnych kluczy GROUP BY generuje pojedyncze zadanie MapReduce.  

Aby włączyć to zachowanie, należy dodać parametr `hive.multigroupby.singlereducer` do niestandardowego okienka programu Hive, jak wyjaśniono w kroku 3 w sekcji [Włączanie kompresji pośredniej](#enable-intermediate-compression) .

![Zestaw Hive MapReduce pojedynczej grupie](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Dodatkowe optymalizacje Hive

W poniższych sekcjach opisano dodatkowe optymalizacje dotyczące technologii Hive, które można ustawić.

#### <a name="join-optimizations"></a>Przełączaj optymalizacje

Domyślny typ sprzężenia w programie Hive to *sprzężenie losowe*. W programie Hive specjalne mapowania odczytują dane wejściowe i emitują parę klucz/wartość sprzężenia do pliku pośredniego. Usługa Hadoop sortuje i scala te pary na etapie losowym. Ten etap losowy jest kosztowny. Wybranie odpowiedniego sprzężenia na podstawie danych może znacząco poprawić wydajność.

| Typ sprzężenia | Czasie | Jaka | Ustawienia programu Hive | Komentarze |
| -- | -- | -- | -- | -- |
| Rozłączenie losowe | <ul><li>Wybór domyślny</li><li>Zawsze działa</li></ul> | <ul><li>Odczytuje z części jednej z tabel</li><li>Zasobniki i sortuje według klucza sprzężenia</li><li>Wysyła jeden zasobnik do każdego zmniejszenia</li><li>Przyłączanie odbywa się po stronie Zmniejsz</li></ul> | Nie jest wymagana znaczna wartość ustawienia Hive | Działa za każdym razem |
| Sprzężenie mapy | <ul><li>Jedna tabela może zmieścić się w pamięci</li></ul> | <ul><li>Odczytuje małą tabelę w tabeli skrótów pamięci</li><li>Strumienie w ramach dużego pliku</li><li>Sprzęga każdy rekord z tabeli skrótów</li><li>Sprzężenia są zależne od mapowania</li></ul> | `hive.auto.confvert.join=true` | Bardzo szybka, ale ograniczona |
| Sortuj zasobnik scalania | Jeśli obie tabele są: <ul><li>Sortowane tak samo</li><li>Przedziały te same</li><li>Przyłączanie do kolumny posortowanej/przedziału</li></ul> | Każdy proces: <ul><li>Odczytuje zasobnik z każdej tabeli</li><li>Przetwarza wiersz o najniższej wartości</li></ul> | `hive.auto.convert.sortmerge.join=true` | Bardzo wydajne |

#### <a name="execution-engine-optimizations"></a>Optymalizacje aparatu wykonywania

Dodatkowe zalecenia dotyczące optymalizacji aparatu wykonywania programu Hive:

| Ustawienie | Zalecane | Domyślna Usługa HDInsight |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | Prawda = bezpieczniejsze, wolniejsze; FAŁSZ = szybsze | {1&gt;false&lt;1} |
| `tez.am.resource.memory.mb` | Górna granica 4 GB dla większości | Dostrajanie autodostrajania |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10 000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="apache-pig-optimization"></a>Optymalizacja przez Apache świni

Właściwości z [Apache świni](https://pig.apache.org/) można modyfikować za pomocą interfejsu użytkownika sieci Web Ambari, aby dostosowywać zapytania do trzody chlewnej. Modyfikowanie właściwości świni z Ambari bezpośrednio modyfikuje właściwości świni w pliku `/etc/pig/2.4.2.0-258.0/pig.properties`.

1. Aby zmodyfikować **Właściwości świni, przejdź do karty konfiguracje** trzody chlewnej, a następnie rozwiń okienko **Zaawansowane właściwości świni** .

1. Znajdź, Usuń komentarz i zmień wartość właściwości, którą chcesz zmodyfikować.

1. Wybierz pozycję **Zapisz** w prawym górnym rogu okna, aby zapisać nową wartość. Niektóre właściwości mogą wymagać ponownego uruchomienia usługi.

    ![Zaawansowane właściwości z Apache świni](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)

> [!NOTE]  
> Wszystkie ustawienia poziomu sesji przesłaniają wartości właściwości w pliku `pig.properties`.

### <a name="tune-execution-engine"></a>Dostrajanie aparatu wykonywania

Dostępne są dwa aparaty wykonywania, które umożliwiają wykonywanie skryptów świń: MapReduce i tez. Tez to zoptymalizowany aparat i jest znacznie szybszy niż MapReduce.

1. Aby zmodyfikować aparat wykonywania, w okienku **Zaawansowane właściwości trzody chlewnej** znajdź Właściwość `exectype`.

1. Wartość domyślna to **MapReduce**. Zmień ją na **tez**.

### <a name="enable-local-mode"></a>Włącz tryb lokalny

Podobnie jak w przypadku programu Hive, tryb lokalny służy do przyspieszenia zadań z stosunkowo mniejszymi ilościami danych.

1. Aby włączyć tryb lokalny, ustaw dla `pig.auto.local.enabled` **wartość true**. Wartość domyślna to false.

1. Zadania o rozmiarze danych wejściowych mniejszym niż wartość właściwości `pig.auto.local.input.maxbytes` są uznawane za małe zadania. Wartość domyślna to 1 GB.

### <a name="copy-user-jar-cache"></a>Kopiuj pamięć podręczną jar użytkownika

Świnie kopiuje pliki JAR wymagane przez UDF do rozproszonej pamięci podręcznej w celu udostępnienia ich dla węzłów zadań. Te Jars nie zmieniają się często. Jeśli ta opcja jest włączona, ustawienie `pig.user.cache.enabled` umożliwia umieszczenie Jars w pamięci podręcznej w celu ponownego użycia dla zadań uruchomionych przez tego samego użytkownika. Powoduje to niewielki wzrost wydajności zadania.

1. Aby włączyć, ustaw dla `pig.user.cache.enabled` wartość true. Wartość domyślna to false.

1. Aby ustawić ścieżkę bazową w pamięci podręcznej Jars, ustaw `pig.user.cache.location` na ścieżkę bazową. Wartość domyślna to `/tmp`.

### <a name="optimize-performance-with-memory-settings"></a>Optymalizowanie wydajności przy użyciu ustawień pamięci

Poniższe ustawienia pamięci mogą pomóc zoptymalizować wydajność skryptu trzody chlewnej.

* `pig.cachedbag.memusage`: ilość pamięci przydzieloną do zbioru. Zbiór to kolekcja krotek. Krotka jest uporządkowanym zestawem pól, a pole jest fragmentem danych. Jeśli dane w zbiorze wykraczają poza przydzieloną pamięć, są one rozlane na dysk. Wartość domyślna to 0,2, która reprezentuje 20% dostępnej pamięci. Ta pamięć jest współdzielona przez wszystkie torby w aplikacji.

* `pig.spill.size.threshold`: torby większe niż ten próg rozmiaru rozlania (w bajtach) są rozlane na dysk. Wartość domyślna to 5 MB.

### <a name="compress-temporary-files"></a>Kompresuj pliki tymczasowe

Świnie generuje pliki tymczasowe podczas wykonywania zadania. Kompresowanie plików tymczasowych powoduje wzrost wydajności podczas odczytywania lub zapisywania plików na dysku. Poniższe ustawienia mogą służyć do kompresowania plików tymczasowych.

* `pig.tmpfilecompression`: wartość true powoduje włączenie kompresji plików tymczasowych. Wartość domyślna to false.

* `pig.tmpfilecompression.codec`: koder-dekoder kompresji służący do kompresowania plików tymczasowych. Zalecane kodeki kompresji to [LZO](https://www.oberhumer.com/opensource/lzo/) i przyciąganie w celu obniżenia poziomu użycia procesora CPU.

### <a name="enable-split-combining"></a>Włącz łączenie podzielone

Gdy ta funkcja jest włączona, małe pliki są łączone do mniejszej liczby zadań mapy. Poprawia to wydajność zadań z wieloma małymi plikami. Aby włączyć, ustaw dla `pig.noSplitCombination` wartość true. Wartość domyślna to false.

### <a name="tune-mappers"></a>Dostrajaj mapowania

Liczba odwzorowań jest kontrolowana przez zmodyfikowanie właściwości `pig.maxCombinedSplitSize`. Określa rozmiar danych, które mają być przetwarzane przez pojedyncze zadanie mapowania. Wartość domyślna to domyślny rozmiar bloku systemu plików. Zwiększenie tej wartości skutkuje zmniejszeniem liczby zadań mapowania.

### <a name="tune-reducers"></a>Dostrajaj ograniczenia

Liczba zmniejszeń jest obliczana na podstawie parametru `pig.exec.reducers.bytes.per.reducer`. Parametr określa liczbę bajtów przetworzonych na program, domyślnie 1 GB. Aby ograniczyć maksymalną liczbę elementów ograniczających, ustaw właściwość `pig.exec.reducers.max` domyślnie 999.

## <a name="apache-hbase-optimization-with-the-ambari-web-ui"></a>Optymalizacja oprogramowania Apache HBase z interfejsem użytkownika sieci Web Ambari

Konfiguracja [Apache HBase](https://hbase.apache.org/) została zmodyfikowana z karty **configs** . W poniższych sekcjach opisano niektóre ważne ustawienia konfiguracji, które mają wpływ na wydajność HBase.

### <a name="set-hbase_heapsize"></a>Set HBASE_HEAPSIZE

Rozmiar sterty HBase określa maksymalną ilość sterty, która ma być używana w megabajtach według *regionów* i serwerów *głównych* . Wartość domyślna to 1 000 MB. Ta wartość powinna być dostrojona dla obciążenia klastra.

1. Aby zmodyfikować, przejdź do okienka **Zaawansowane HBase-ENV** **na karcie konfiguracje** HBase, a następnie znajdź ustawienie `HBASE_HEAPSIZE`.

1. Zmień wartość domyślną na 5 000 MB.

    ![Heapsize pamięci HBase Apache Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-hbase-heapsize.png)

### <a name="optimize-read-heavy-workloads"></a>Optymalizowanie obciążeń z dużą ilością odczytu

Następujące konfiguracje są ważne, aby poprawić wydajność obciążeń z dużą ilością odczytu.

#### <a name="block-cache-size"></a>Rozmiar bloku pamięci podręcznej

Pamięć podręczna bloków jest pamięcią podręczną odczytu. Jego rozmiar jest kontrolowany przez parametr `hfile.block.cache.size`. Wartość domyślna to 0,4, czyli 40 procent całkowitej ilości pamięci serwera regionu. Im większy rozmiar pamięci podręcznej bloku, tym szybsze są odczyty losowe.

1. Aby zmodyfikować ten parametr, przejdź do karty **Ustawienia** na karcie HBase **configs (Konfiguracja** ), a następnie Znajdź **% RegionServer przydzieloną do buforów odczytu**.

    ![Rozmiar pamięci podręcznej blokowania pamięci Apache HBase](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)

1. Aby zmienić wartość, wybierz ikonę **edycji** .

#### <a name="memstore-size"></a>Rozmiar magazynu

Wszystkie edycje są przechowywane w buforze pamięci o nazwie *magazynu*. Powoduje to zwiększenie łącznej ilości danych, które mogą być zapisywane na dysku w ramach jednej operacji, i przyspiesza dalsze dostęp do ostatnich zmian. Rozmiar magazynu jest definiowany przez następujące dwa parametry:

* `hbase.regionserver.global.memstore.UpperLimit`: określa maksymalny procent serwera regionu, który może być używany przez magazynu połączone.

* `hbase.regionserver.global.memstore.LowerLimit`: określa minimalny procent serwera regionu, który może być używany przez magazynu.

Aby zoptymalizować odczyty losowe, można zmniejszyć górną i dolną granicę magazynu.

#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Liczba wierszy pobranych podczas skanowania z dysku

Ustawienie `hbase.client.scanner.caching` definiuje liczbę wierszy odczytanych z dysku w przypadku wywołania metody `next` na skanerze.  Wartość domyślna to 100. Im większa liczba, tym mniej wywołań zdalnych wykonanych z klienta do serwera regionów, co skutkuje szybszymi skanami. Jednak spowoduje to również zwiększenie poziomu wykorzystania pamięci przez klienta.

![Liczba pobranych wierszy Apache HBase](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Nie ustawiaj wartości w taki sposób, aby czas między wywołaniem następnej metody na skanerze był większy niż limit czasu skanera. Czas trwania skanera jest definiowany przez właściwość `hbase.regionserver.lease.period`.

### <a name="optimize-write-heavy-workloads"></a>Optymalizowanie dużych obciążeń zapisu

Następujące konfiguracje są ważne do poprawienia wydajności dużych obciążeń zapisu.

#### <a name="maximum-region-file-size"></a>Maksymalny rozmiar pliku regionu

HBase przechowuje dane w wewnętrznym formacie pliku o nazwie *HFile*. Właściwość `hbase.hregion.max.filesize` definiuje rozmiar pojedynczego HFile dla regionu.  Region jest podzielony na dwa regiony, jeśli suma wszystkich HFiles w regionie jest większa niż to ustawienie.

![HBase Apache HRegion max rozmiar pliku](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Im większy rozmiar pliku regionu, tym mniejsza jest liczba podziałów. Można zwiększyć rozmiar pliku, aby określić wartość, która powoduje maksymalną wydajność zapisu.

#### <a name="avoid-update-blocking"></a>Unikaj blokowania aktualizacji

* Właściwość `hbase.hregion.memstore.flush.size` definiuje rozmiar, pod którym magazynu jest opróżniany na dysk. Domyślny rozmiar to 128 MB.

* Mnożnik bloku regionu HBase jest definiowany przez `hbase.hregion.memstore.block.multiplier`. Wartość domyślna to 4. Maksymalna dozwolona wartość to 8.

* HBase blokuje aktualizacje, jeśli magazynu jest (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) b.

    Przy domyślnych wartościach rozmiaru opróżniania i mnożnika bloku aktualizacje są blokowane, gdy magazynu jest 128 * 4 = 512 MB. Aby zmniejszyć liczbę blokowania aktualizacji, zwiększ wartość `hbase.hregion.memstore.block.multiplier`.

![Mnożnik bloku regionu Apache HBase](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)

### <a name="define-memstore-size"></a>Zdefiniuj rozmiar magazynu

Rozmiar magazynu jest definiowany przez parametry `hbase.regionserver.global.memstore.UpperLimit` i `hbase.regionserver.global.memstore.LowerLimit`. Ustawienie tych wartości w taki sposób, że zmniejsza przerwy podczas operacji zapisu (co również powoduje częstsze opróżnianie) i skutkuje zwiększoną wydajnością zapisu.

### <a name="set-memstore-local-allocation-buffer"></a>Ustaw bufor przydziału lokalnego magazynu

Użycie magazynu lokalnego buforu alokacji jest określane przez `hbase.hregion.memstore.mslab.enabled`właściwości. Po włączeniu (true) zapobiega fragmentacji sterty podczas ciężkiej operacji zapisu. Wartością domyślną jest true.

![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Interfejs API REST usługi Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
