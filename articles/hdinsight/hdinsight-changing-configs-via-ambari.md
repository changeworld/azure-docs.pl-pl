---
title: Optymalizacja konfiguracje klastrów z Ambari - Azure HDInsight | Dokumentacja firmy Microsoft
description: Konfigurowanie i zoptymalizować klastrów usługi HDInsight za pomocą interfejsu użytkownika sieci web Ambari.
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: ashish
ms.openlocfilehash: f3c1edc767ab07bcdd8b09a0e40e291cbd1f3d9a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="use-ambari-to-optimize-hdinsight-cluster-configurations"></a>Optymalizowanie konfiguracje klastrów usługi HDInsight przy użyciu Ambari

Usługa HDInsight zapewnia klastrów platformy Apache Hadoop dla aplikacji na dużą skalę przetwarzania danych. Monitorowanie i zarządzanie optymalizacji tych złożonych klastrami z wieloma węzłami może być trudne. [Apache Ambari](http://ambari.apache.org/) jest interfejsem sieci web do zarządzania i monitorowania klastrów HDInsight Linux.  W przypadku klastrów systemu Windows należy używać narzędzia Ambari [interfejsu API REST](hdinsight-hadoop-manage-ambari-rest-api.md).

Aby obejrzeć wprowadzenie do za pomocą interfejsu użytkownika sieci Web Ambari, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Ambari](hdinsight-hadoop-manage-ambari.md)

Zaloguj się do narzędzia Ambari w `https://CLUSTERNAME.azurehdidnsight.net` przy użyciu poświadczeń klastra. Ekran początkowy Wyświetla pulpitu nawigacyjnego przeglądu.

![Pulpit nawigacyjny Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-dashboard.png)

Interfejs użytkownika sieci web Ambari może służyć do zarządzania hostami, usług, alerty, konfiguracji i widoków. Ambari nie może służyć do tworzenia klastra usługi HDInsight, usługi uaktualniania, zarządzanie stosy i wersje, zlikwidować lub recommission hostów lub usługi jest dodawany do klastra.

## <a name="manage-your-clusters-configuration"></a>Zarządzanie konfiguracją klastra

Ustawienia konfiguracji pomóc dostosować określonej usługi. Aby zmodyfikować ustawienia konfiguracji usługi, wybierz usługę z **usług** paska bocznego (po lewej), a następnie przejdź do **Configs** kartę na stronie szczegółów usługi.

![Pasek boczny usług](./media/hdinsight-changing-configs-via-ambari/services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Zmodyfikuj rozmiar sterty NameNode Java

Rozmiar sterty NameNode Java zależy od wielu czynników, takich jak obciążenie klastra, liczby plików i liczby bloków. Domyślny rozmiar 1 GB dobrze działa z Większość klastrów, mimo że niektórych zadań może wymagać więcej lub mniej pamięci. 

Aby zmienić rozmiar sterty NameNode Java:

1. Wybierz **HDFS** z paska bocznego usługi i przejdź do **Configs** kartę.

    ![Konfiguracja systemu plików HDFS](./media/hdinsight-changing-configs-via-ambari/hdfs-config.png)

2. Znajdź ustawienie **rozmiar stosu NameNode Java**. Można również użyć **filtru** polu tekstowym wpisz i znalezienie danego ustawienia. Wybierz **pióra** ikona obok nazwy ustawienia.

    ![Rozmiar sterty NameNode Java](./media/hdinsight-changing-configs-via-ambari/java-heap-size.png)

3. Wpisz nową wartość w polu tekstowym, a następnie naciśnij klawisz **Enter** można zapisać zmiany.

    ![Edytowanie NameNode Java rozmiaru sterty](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit.png)

4. Rozmiar sterty NameNode Java jest zmieniany na 2 GB od 1 GB.

    ![Rozmiar sterty NameNode Java edytować](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

5. Zapisz zmiany przez kliknięcie na zielonym **zapisać** przycisk w górnej części ekranu konfiguracji.

    ![Zapisz zmiany](./media/hdinsight-changing-configs-via-ambari/save-changes.png)

## <a name="hive-optimization"></a>Optymalizacja gałęzi

W poniższych sekcjach opisano opcje konfiguracji dla optymalizacji wydajności ogólnej Hive.

1. Aby zmodyfikować parametry konfiguracji Hive, wybierz opcję **Hive** z paska bocznego usługi.
2. Przejdź do **Configs** kartę.

### <a name="set-the-hive-execution-engine"></a>Ustaw aparat wykonywania gałęzi

Gałąź zawiera dwa aparatów wykonywania: MapReduce i Tez. Tez jest szybsza niż MapReduce. Klastry HDInsight Linux ma Tez jako domyślny aparat wykonywania. Aby zmienić aparat wykonywania:

1. W gałęzi **Configs** , wpisz **aparat wykonywania** w polu filtru.

    ![Aparat wykonywania wyszukiwania](./media/hdinsight-changing-configs-via-ambari/search-execution.png)

2. **Optymalizacji** jest wartością domyślną właściwości **Tez**.

    ![Optymalizacja — Tez](./media/hdinsight-changing-configs-via-ambari/optimization-tez.png)

### <a name="tune-mappers"></a>Dostosuj mapowań

Hadoop próbuje podzielić (*mapy*) pliki do wielu plików i procesu powstałe w ten sposób pojedynczy plik równolegle. Liczba podziałów zależy od liczby mapowań. Następujące parametry konfiguracji dwóch dysków liczba podziałów dla aparat wykonywania platformy Tez:

* `tez.grouping.min-size`: Dolny limit na rozmiar grupowanych podziału, wartość domyślna 16 MB (16,777,216 w bajtach).
* `tez.grouping.max-size`: Górny limit na rozmiar grupowanych podziału, wartości domyślnej 1 GB (w bajtach 1,073,741,824).

Jako wydajności zasadą Zmniejsz obu tych parametrów, aby zwiększyć czas oczekiwania, zwiększyć więcej przepustowości.

Na przykład aby ustawić cztery zadania mapowania danych o rozmiarze 128 MB, należy ustawiał oba parametry 32 MB każdy (33,554,432 w bajtach).

1. Aby zmodyfikować parametry limitu, przejdź do **Configs** kartę usługi Tez. Rozwiń węzeł **ogólne** panelu, a następnie zlokalizuj `tez.grouping.max-size` i `tez.grouping.min-size` parametrów.

2. Ustawiono oba parametry **33,554,432** bajtów (32 MB).

    ![Rozmiary grupowania tez](./media/hdinsight-changing-configs-via-ambari/tez-grouping-size.png)
 
Te zmiany wpływają na wszystkie Tez zadania na serwerze. Aby uzyskać optymalne wyniki, wybierz odpowiedni parametr wartości.

### <a name="tune-reducers"></a>Dostosuj reduktory

ORC i Snappy oferują wysoką wydajność. Jednak gałęzi może mieć za mało reduktory domyślnie powoduje wąskich gardeł.

Na przykład załóżmy, że rozmiar danych wejściowych 50 GB. Czy dane w ORC formacie kompresji szałowe jest 1 GB. Gałąź Szacuje liczbę potrzebnych jako reduktory: (liczba bajtów odebranych na mapowań / `hive.exec.reducers.bytes.per.reducer`).

Przy użyciu ustawień domyślnych w tym przykładzie jest reduktory 4.

`hive.exec.reducers.bytes.per.reducer` Parametr określa liczbę bajtów przetwarzanych na reduktor. Wartość domyślna to 64 MB. Dostrajanie tę wartość w dół zwiększa równoległości i może zwiększyć wydajność. Ulepszanie funkcji zbyt niska może utworzyć reduktory zbyt wiele, potencjalnie negatywnego wpływu na wydajność. Ten parametr zależy od wymagań dotyczących danych, ustawienia kompresji i inne czynniki środowiska.

1. Aby zmodyfikować parametr, przejdź do gałęzi **Configs** i Znajdź **danych na reduktor** parametru na stronie Ustawienia.

    ![Dane na reduktor](./media/hdinsight-changing-configs-via-ambari/data-per-reducer.png)
 
2. Wybierz **Edytuj** zmodyfikować wartości do 128 MB (w bajtach 134,217,728), a następnie naciśnij klawisz **Enter** do zapisania.

    ![Dane na reduktor - edytować](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Podany rozmiar wejściowy 1024 MB, 128 MB danych na reduktor, istnieją reduktory 8 (1024/128).

3. Nieprawidłowa wartość parametru **danych na reduktor** parametru może skutkować dużą liczbę reduktory negatywnego wpływu na wydajność kwerend. Aby ograniczyć liczbę reduktory, ustaw `hive.exec.reducers.max` odpowiednią wartość. Wartość domyślna to 1009.

### <a name="enable-parallel-execution"></a>Włącz wykonywanie równoległe

Zapytanie Hive jest wykonywane etapami jeden lub więcej. Jeśli niezależnych etapów mogą być uruchamiane równolegle, która spowoduje zwiększenie wydajności zapytania.

1.  Aby włączyć równoległego wykonywania zapytań, przejdź do gałęzi **Config** karcie i wyszukaj `hive.exec.parallel` właściwości. Wartość domyślna to false. Zmień wartość na true, a następnie naciśnij klawisz **Enter** można zapisać wartości.
 
2.  Aby ograniczyć liczbę zadania mają być uruchamiane równolegle, zmodyfikuj `hive.exec.parallel.thread.number` właściwości. Wartość domyślna to 8.

    ![Hive exec równoległe](./media/hdinsight-changing-configs-via-ambari/hive-exec-parallel.png)


### <a name="enable-vectorization"></a>Włącz vectorization

Gałąź przetwarza dane wiersz po wierszu. Vectorization kieruje gałęzi do przetwarzania danych w blokach wierszy 1024, a nie na jeden wiersz jednocześnie. Vectorization ma zastosowanie tylko do formatu plików ORC.

1. Aby umożliwić wykonanie zapytania zwektoryzowane, przejdź do gałęzi **Configs** karcie i wyszukaj `hive.vectorized.execution.enabled` parametru. Wartość domyślna to true dla gałęzi 0.13.0 lub nowszej.
 
2. Aby włączyć zwektoryzowane wykonywania dla strony zmniejsz zapytania, ustaw `hive.vectorized.execution.reduce.enabled` parametru na wartość true. Wartość domyślna to false.

    ![Wykonywania zadań hive Przekształcono](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Włącz optymalizacji opartej na koszt (CBO)

Domyślnie Hive następuje zestaw reguł, aby znaleźć jeden plan wykonania zapytania optymalne. Optymalizacja oparta na koszt (CBO) ocenia wieloma planami mógł wykonać zapytania przypisuje koszt każdego planu, a następnie określa najtańszej planu wykonania zapytania.

Aby włączyć CBO, przejdź do gałęzi **Configs** karcie i wyszukaj `parameter hive.cbo.enable`, następnie Przełącz przycisk przełączania **na**.

![CBO konfiguracji](./media/hdinsight-changing-configs-via-ambari/cbo.png)

Następujące dodatkowe parametry konfiguracji zwiększyć wydajność zapytań Hive po włączeniu CBO:

* `hive.compute.query.using.stats`

    Po wartość true, gałęzi używa statystyki przechowywane w jego potrzeby magazynu metadanych do odpowiadania na kwerendy proste, takie jak `count(*)`.

    ![Statystyka CBO](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Statystyk kolumny są tworzone po włączeniu CBO. Gałąź używa statystyk kolumny, które są przechowywane w potrzeby magazynu metadanych, aby zoptymalizować zapytania. Pobieranie statystyk kolumny dla każdej kolumny trwa dłużej, gdy liczba kolumn jest wysoka. Gdy ma wartość false, to ustawienie wyłącza pobrano statystyk kolumny z potrzeby magazynu metadanych.

    ![Statystyka kolumny zestawu Statystyka hive](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Partycja podstawowa statystyk, takich jak liczba wierszy, rozmiar danych i rozmiar pliku są przechowywane w potrzeby magazynu metadanych. Jeśli wartość true, partycji, które statystyki są pobierane z potrzeby magazynu metadanych. Gdy ma wartość false, rozmiar pliku jest pobierana z systemu plików, a liczba wierszy jest pobierana ze schematu wiersza.

    ![Statystyka zestawu partycji Statystyka hive](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Włącz kompresję pośredni

Mapa zadania tworzenia plików pośrednich, które są używane przez zadania reduktor. Pośredni Kompresja zmniejsza rozmiar pliku pośredniego.

Zadania usługi Hadoop są zwykle wydajność ruchu We/Wy. Kompresowanie danych można przyspieszyć We/Wy i ogólną transferu sieciowego.

Kompresja dostępne typy to:

| Format | Narzędzie | Algorytm | Rozszerzenie pliku | Podzielne? |
| -- | -- | -- | -- | -- |
| Gzip | Gzip | KORYGOWANIA | .gz | Nie |
| Bzip2 | Bzip2 | Bzip2 |.bz2 | Yes |
| LZO | Lzop | LZO | .lzo | Tak, jeśli indeksowane |
| szałowe | ND | szałowe | szałowe | Nie |

Zasadniczo ważne jest posiadanie podzielne metody kompresji, w przeciwnym razie zostanie utworzony bardzo mało mapowań. Jeśli dane wejściowe jest tekst, `bzip2` jest najlepszym rozwiązaniem. ORC format Snappy jest najszybszą opcję kompresji.

1. Aby włączyć kompresję pośredniego, przejdź do gałęzi **Configs** karcie, a następnie ustaw `hive.exec.compress.intermediate` parametru na wartość true. Wartość domyślna to false.

    ![Kompresuj exec hive pośredni](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]
    > Aby skompresować plików pośrednich, wybierz koder-dekoder kompresji z Procesora niższy koszt, nawet jeśli koder-dekoder nie ma wyjścia wysoki stopień kompresji.

2. Aby ustawić koder-dekoder kompresji pośredniego, dodawanie właściwości niestandardowych `mapred.map.output.compression.codec` do `hive-site.xml` lub `mapred-site.xml` pliku.

3. Aby dodać niestandardową wartość ustawienia:

    a. Przejdź do gałęzi **Configs** i wybierz **zaawansowane** kartę.

    b. W obszarze **zaawansowane** karcie, Znajdź i rozwiń **lokacji hive niestandardowe** okienka.

    c. Kliknij łącze **Dodaj właściwość** w dolnej części okienka gałąź witryny niestandardowe.

    d. W oknie właściwości Dodaj wprowadź `mapred.map.output.compression.codec` jako klucz i `org.apache.hadoop.io.compress.SnappyCodec` jako wartość.

    e. Kliknij pozycję **Add** (Dodaj).

    ![Właściwość niestandardowa gałęzi](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Umożliwia kompresję pliku pośredniego przy użyciu szałowe kompresji. Po dodaniu właściwość zostanie wyświetlony w okienku gałąź witryny niestandardowe.

    > [!NOTE]
    > Ta procedura modyfikuje `$HADOOP_HOME/conf/hive-site.xml` pliku.

### <a name="compress-final-output"></a>Kompresuj ostateczne dane wyjściowe

Ostateczne dane wyjściowe Hive można również skompresować.

1. Aby skompresować do pliku wyjściowego Hive, przejdź do gałęzi **Configs** karcie, a następnie ustaw `hive.exec.compress.output` parametru na wartość true. Wartość domyślna to false.

2. Aby wybrać koder-dekoder kompresji danych wyjściowych, Dodaj `mapred.output.compression.codec` właściwości niestandardowych do okienka gałąź witryny niestandardowej, zgodnie z opisem w poprzedniej sekcji Krok 3.

    ![Właściwość niestandardowa gałęzi](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Włącz rozważana wykonywania

Wykonanie rozważana uruchamia pewne zduplikowane zadań, aby wykryć i wyeliminować śledzenia zadań działa wolno, poprawienie ogólnej wykonywania zadania optymalizując wyniki poszczególnych zadań.

Rozważana wykonywania nie powinna być włączona dla długotrwałych zadań MapReduce z dużą ilością danych wejściowych.

* Aby umożliwić wykonanie rozważana, przejdź do gałęzi **Configs** karcie, a następnie ustaw `hive.mapred.reduce.tasks.speculative.execution` parametru na wartość true. Wartość domyślna to false.

    ![Gałąź mapred zmniejszyć rozważana wykonania zadania](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Dostosuj partycji dynamicznych

Gałąź umożliwia tworzenie dynamicznych partycji podczas wstawiania rekordów do tabeli, bez predefiniowanie każdej partycji. Jest to zaawansowanych funkcji, ale może spowodować utworzenie dużej liczby partycji i dużą liczbę plików dla każdej partycji.

1. Dla gałęzi do partycji dynamicznych `hive.exec.dynamic.partition` wartość parametru powinna mieć wartość true (ustawienie domyślne).

2. Zmień tryb partycji dynamicznych do *strict*. W trybie z ograniczeniami co najmniej jedna partycja musi być statyczny. Zapobiega to zapytań bez filtru partycji w klauzuli WHERE, czyli *strict* uniemożliwia zapytania skanujące wszystkie partycje. Przejdź do gałęzi **Configs** karcie, a następnie ustaw `hive.exec.dynamic.partition.mode` do **strict**. Wartość domyślna to **nonstrict**.
 
3. Aby ograniczyć liczbę partycji dynamicznych należy utworzyć, zmodyfikować '' hive.exec.max.dynamic.partitions' parametru. Wartość domyślna to 5000.
 
4. Aby ograniczyć całkowita liczba partycji dynamicznych w każdym węźle, zmodyfikuj `hive.exec.max.dynamic.partitions.pernode`. Wartość domyślna to 2000.

### <a name="enable-local-mode"></a>Włącz tryb lokalny

Tryb lokalnego umożliwia gałęzi do wykonywania wszystkich zadań zadania, na jednym komputerze, a czasami w ramach jednego procesu. Poprawia to wydajność zapytań, jeśli dane wejściowe jest mały i koszty uruchamiania zadań dla zapytań zużywa znacznego procentu zgłoszeń w ogólnej wykonywania zapytania.

Aby włączyć tryb lokalny, Dodaj `hive.exec.mode.local.auto` parametru do panelu gałąź witryny niestandardowej, zgodnie z objaśnieniem w kroku 3 [Włącz kompresję pośredniego](#enable-intermediate-compression) sekcji.

![Gałąź exec trybu lokalnego automatycznie](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Zestaw pojedynczego MapReduce MultiGROUP przez

Gdy ta właściwość jest równa true, zapytania MultiGROUP przez klucze wspólne Grupuj według generuje jednego zadania MapReduce.  

Aby włączyć ten problem, Dodaj `hive.multigroupby.singlereducer` parametrów do okienka gałąź witryny niestandardowej, zgodnie z objaśnieniem w kroku 3 [Włącz kompresję pośredniego](#enable-intermediate-compression) sekcji.

![Gałąź ustawieniem pojedynczego MapReduce MultiGROUP](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Dodatkowe optymalizacje gałęzi

W poniższych sekcjach opisano dodatkowe optymalizacje związane z gałęzi, które można ustawić.

#### <a name="join-optimizations"></a>Dołącz do optymalizacji

Domyślny typ sprzężenia w gałęzi *sprzężenia losowa*. W gałęzi specjalne mapowań odczytać dane wejściowe i Emituj parę klucz wartość sprzężenia do pliku pośredniego. Hadoop sortuje i scala te pary w fazie losowa. Ten etap losowa jest kosztowna. Zaznaczenie prawym sprzężenia, na podstawie danych może znacznie poprawić wydajność.

| Typ przyłączenia | Kiedy | Jak | Gałąź, ustawienia | Komentarze |
| -- | -- | -- | -- | -- |
| Sprzężenia losowa | <ul><li>Wybór domyślny</li><li>Zawsze działa</li></ul> | <ul><li>Odczytuje z częścią jednej z tabel</li><li>Pakiety i sortowanie w kluczu sprzężenia</li><li>Wysyła jeden zasobnik do każdego Zmniejsz</li><li>Sprzężenia jest wykonywana na tej stronie Zmniejsz</li></ul> | Nie znaczących ustawienie wymagane gałęzi | Zawsze działa |
| Dołącz do mapy | <ul><li>Jedna tabela można zmieścić w pamięci</li></ul> | <ul><li>Odczytuje małą tabelę w tablicy skrótów pamięci</li><li>Strumienie przez część dużych plików</li><li>Dołącza każdy rekord z tablicy skrótów</li><li>Sprzężenia są przez samego mapowania</li></ul> | `hive.auto.confvert.join=true` | Bardzo szybko, ale ograniczone |
| Zasobnik scalania sortowania | Jeśli obie tabele są: <ul><li>Sortowane takie same</li><li>Bucketed takie same</li><li>Sprzęganie sortowane zasobnikach kolumny</li></ul> | Każdy proces: <ul><li>Odczytuje zasobnika z każdej tabeli</li><li>Przetwarza wiersz mający najmniejszą wartość</li></ul> | `hive.auto.convert.sortmerge.join=true` | Bardzo wydajny |

#### <a name="execution-engine-optimizations"></a>Wykonywanie aparatu optymalizacji

Dodatkowe zalecenia dotyczące optymalizacji aparatu wykonywania gałęzi:

| Ustawienie | Zalecane | Domyślne usługi HDInsight |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | TRUE = bezpieczniejsze i wolniejsze; FALSE = szybciej | false |
| `tez.am.resource.memory.mb` | Górna granica większość 4 GB | Automatycznie dopasowane |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10 000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="pig-optimization"></a>Optymalizacja pig

Z sieci web Ambari interfejsu użytkownika, aby dostroić Pig zapytania można zmodyfikować właściwości pig. Bezpośrednie modyfikowanie właściwości Pig z Ambari modyfikuje właściwości Pig w `/etc/pig/2.4.2.0-258.0/pig.properties` pliku.

1. Aby zmodyfikować właściwości Pig, przejdź do Pig **Configs** karcie, a następnie rozwiń **zaawansowane właściwości pig** okienka.

2. Znajdź, usuń znaczniki komentarza i zmień wartość właściwości, którą chcesz zmodyfikować.

3. Wybierz **zapisać** na górnym rogu okna, aby zapisać nową wartość. Niektóre właściwości mogą wymagać ponownego uruchomienia usługi.

    ![Zaawansowane właściwości pig](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)
 
> [!NOTE]
> Wszystkie ustawienia poziomu sesji zastąpić wartości właściwości w `pig.properties` pliku.

### <a name="tune-execution-engine"></a>Aparat wykonywania dostroić

Dwa aparatów wykonywania są dostępne do wykonywania skryptów usługi Pig: MapReduce i Tez. Tez jest zoptymalizowany aparatu i jest znacznie szybsza niż MapReduce.

1. Aby zmodyfikować aparat wykonywania w **zaawansowane właściwości pig** okienku znaleźć właściwości `exectype`.

2. Wartość domyślna to **MapReduce**. Zmień, aby **Tez**.


### <a name="enable-local-mode"></a>Włącz tryb lokalny

Podobny do gałęzi, tryb lokalny jest używana do szybkości zadań z stosunkowo mniejszych ilości danych.

1. Aby włączyć tryb lokalny, ustaw `pig.auto.local.enabled` do **true**. Wartość domyślna to false.

2. Zadania, dla których rozmiar danych wejściowych mniejszej niż `pig.auto.local.input.maxbytes` wartości właściwości są uważane za małych zadań. Wartość domyślna to 1 GB.


### <a name="copy-user-jar-cache"></a>Skopiuj użytkownika jar w pamięci podręcznej

Pig kopiuje pliki JAR wymagana przez funkcje UDF do rozproszonej pamięci podręcznej do udostępnienia ich do węzłów zadań. Te słoików nie zmieniają się często. U możliwia `pig.user.cache.enabled` ustawienie umożliwia słoików do umieszczenia w pamięci podręcznej w celu użycia dla zadania wykonywane przez tego samego użytkownika. Powoduje to niewielkie wzrost wydajności zadania.

1. Aby włączyć, ustaw `pig.user.cache.enabled` na wartość true. Wartość domyślna to false.

2. Podstawowa ścieżka buforowane słoików ustawia `pig.user.cache.location` do ścieżki podstawowej. Wartość domyślna to `/tmp`.


### <a name="optimize-performance-with-memory-settings"></a>Optymalizacja wydajności za pomocą ustawienia pamięci

Następujące ustawienia pamięci może pomóc optymalizacji wydajności skryptu Pig.

* `pig.cachedbag.memusage`: Ilość pamięci przydzielona do zbioru. Zbiór jest kolekcją spójnych kolekcji. Krotka jest uporządkowany zestaw pól, a pole jest element danych. W przypadku danych, w torebce poza alokacji pamięci, są rozrzucone na dysku. Wartość domyślna to 0,2, który reprezentuje 20 procent dostępnej pamięci. Ta pamięć jest współużytkowana przez wszystkie torebki w aplikacji.

* `pig.spill.size.threshold`: Są rozrzucone torebki większa od wartości progu usuwania wycieków rozmiar (w bajtach) na dysku. Wartość domyślna to 5 MB.


### <a name="compress-temporary-files"></a>Kompresuj pliki tymczasowe

Pig generuje pliki tymczasowe podczas wykonywania zadania. Kompresowanie plików tymczasowych powoduje wzrost wydajności podczas odczytywania lub zapisywania plików na dysku. Następujące ustawienia może służyć do skompresowania plików tymczasowych.

* `pig.tmpfilecompression`: W przypadku wartości true, włącza kompresję pliku tymczasowego. Wartość domyślna to false.

* `pig.tmpfilecompression.codec`Koder-dekoder kompresji do użycia na potrzeby kompresowanie plików tymczasowych. Kodeki kompresji zalecane są LZO i Snappy niższe użycia procesora CPU.

### <a name="enable-split-combining"></a>Włącz łączenie podziału

Po włączeniu małych plików są łączone na mniejszą liczbę zadań mapy. Poprawia to wydajność zadań wiele małych plików. Aby włączyć, ustaw `pig.noSplitCombination` na wartość true. Wartość domyślna to false.


### <a name="tune-mappers"></a>Dostosuj mapowań

Liczba mapowań jest kontrolowany przez modyfikowanie właściwości `pig.maxCombinedSplitSize`. Określa rozmiar danych do przetworzenia przez zadanie jedną mapę. Wartość domyślna to domyślny rozmiar bloku systemu plików. Zwiększenie tej wartości powoduje zmniejszenie liczby zadań mapowania.


### <a name="tune-reducers"></a>Dostosuj reduktory

Liczba reduktory jest obliczana na podstawie parametru `pig.exec.reducers.bytes.per.reducer`. Parametr określa liczbę bajtów przetwarzanych na reduktor, domyślnie 1 GB. Aby ograniczyć liczbę reduktory, ustaw `pig.exec.reducers.max` właściwość domyślnie 999.


## <a name="hbase-optimization-with-the-ambari-web-ui"></a>Optymalizacja HBase przy interfejsu użytkownika sieci web Ambari

Konfiguracja bazy danych HBase jest modyfikowany od **HBase Configs** kartę. W poniższych sekcjach opisano niektóre ustawienia konfiguracji ważne, które mają wpływ na wydajność bazy danych HBase.

### <a name="set-hbaseheapsize"></a>Ustaw HBASE_HEAPSIZE

Rozmiar sterty HBase określa maksymalną ilość sterty do użycia w megabajtach przez *region* i *wzorca* serwerów. Wartość domyślna to 1000 MB. Powinna to być dostosowana na potrzeby obciążenie klastra.

1. Aby zmodyfikować, przejdź do **zaawansowane env HBase** okienko w bazie danych HBase **Configs** karcie, a następnie znajdź `HBASE_HEAPSIZE` ustawienie.

2. Zmień wartość domyślną do 5000 MB.

    ![HBASE_HEAPSIZE](./media/hdinsight-changing-configs-via-ambari/hbase-heapsize.png)


### <a name="optimize-read-heavy-workloads"></a>Optymalizacja obciążeń intensywnie odczytu

Następujące konfiguracje są ważne zwiększyć wydajność obciążeń intensywnie odczytu.

#### <a name="block-cache-size"></a>Rozmiar pamięci podręcznej bloku

Pamięć podręczna bloku jest pamięci podręcznej odczytu. Jego rozmiar jest kontrolowany przez `hfile.block.cache.size` parametru. Wartość domyślna to 0,4, czyli 40 procent całkowitej regionu pamięci serwera. Im większa bloku rozmiar pamięci podręcznej, będzie szybsze losowych operacji odczytu.

1. Aby zmodyfikować tego parametru, przejdź do **ustawienia** kartę w bazie danych HBase **Configs** karcie, a następnie zlokalizuj **% RegionServer przydzielonych do buforów odczytu**.

    ![Rozmiar pamięci podręcznej bloku HBase](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)
 
2. Aby zmienić wartość, zaznacz **Edytuj** ikony.


#### <a name="memstore-size"></a>Rozmiar magazynu

Wszystkie edycje są przechowywane w buforze pamięci o nazwie *parametru*. Zwiększa to łączną ilość danych, które mogą być zapisywane na dysku w ramach jednej operacji i zwiększa kolejnych dostęp do najnowszych zmian. Rozmiar parametru jest zdefiniowane przez następujące dwa parametry:

* `hbase.regionserver.global.memstore.UpperLimit`: Określa maksymalny udział procentowy serwera region, który można użyć parametru w połączeniu.

* `hbase.regionserver.global.memstore.LowerLimit`: Określa minimalną wartość procentową serwera region, który można użyć parametru połączone.

Aby zoptymalizować dla losowych odczytów, można zmniejszyć górny i dolny limit magazynu.


#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Liczba wierszy pobranych podczas skanowania z dysku

`hbase.client.scanner.caching` Ustawienie określa liczbę wierszy do odczytu z dysku po `next` metoda jest wywoływana na skanerze.  Wartość domyślna to 100. Im większa liczba, tym mniej wywołań zdalnych wprowadzone od klienta do serwera regionie, co powoduje szybsze skanowania. Jednak to spowoduje zwiększenie wykorzystania pamięci na komputerze klienckim.

![HBase liczba pobranych wierszy](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]
> Nie należy ustawiać wartość w taki sposób, że czas od wywołania metody dalej na skanerze jest większy niż limit czasu skanera. Wartość limitu czasu skanera jest definiowana za pomocą `hbase.regionserver.lease.period` właściwości.


### <a name="optimize-write-heavy-workloads"></a>Optymalizacja obciążeń intensywnie zapisu

Następujące konfiguracje są ważne zwiększyć wydajność obciążeń intensywnie zapisu.


#### <a name="maximum-region-file-size"></a>Region maksymalny rozmiar pliku

HBase przechowuje dane w formacie pliku wewnętrznego, nazywany *HFile*. Właściwość `hbase.hregion.max.filesize` Określa rozmiar pojedynczego hFile — dla regionu.  Region jest podzielony na dwóch regionach, jeśli suma wszystkich HFiles w regionie, który jest większa niż to ustawienie.
 
![Maksymalny rozmiar pliku HBase HRegion](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Im większy rozmiar pliku regionu, im mniejsza liczba podziałów. Można zwiększyć rozmiar pliku, aby określić wartość powoduje maksymalną wydajność zapisu.


#### <a name="avoid-update-blocking"></a>Należy unikać blokowania aktualizacji

* Właściwość `hbase.hregion.memstore.flush.size` Określa rozmiar, w którym jest opróżniany magazynu na dysku. Rozmiar domyślny to 128 MB.

* Mnożnik bloku region Hbase jest definiowana za pomocą `hbase.hregion.memstore.block.multiplier`. Wartość domyślna to 4. Maksymalna dozwolona wartość to 8.

* HBase blokuje aktualizacji, jeśli jest magazynu (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) bajtów.

    Z wartościami domyślnymi opróżniania rozmiar i współczynnik bloku aktualizacje będą blokowane podczas magazynu to 128 * 4 = 512 MB rozmiar. Aby zmniejszyć blokuje liczba aktualizacji, należy zwiększyć wartość `hbase.hregion.memstore.block.multiplier`.

![Mnożnik bloku HBase Region](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)


### <a name="define-memstore-size"></a>Określ wielkość magazynu

Rozmiar parametru jest zdefiniowane przez `hbase.regionserver.global.memstore.UpperLimit` i `hbase.regionserver.global.memstore.LowerLimit` parametrów. Ustawienia te wartości równości, który zmniejsza do każdej inne przerwy podczas zapisuje (powoduje również częstsze opróżnianie) i powoduje zapisu zwiększenia wydajności.


### <a name="set-memstore-local-allocation-buffer"></a>Wartość parametru lokalnej Alokacja buforu

Użycie bufora lokalnej Alokacja magazynu jest określany przez właściwość `hbase.hregion.memstore.mslab.enabled`. Po włączeniu (true) zapobiega to fragmentację stosu podczas operacji zapisu duże. Wartość domyślna to true.
 
![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)


## <a name="next-steps"></a>Kolejne kroki

* [Zarządzanie klastrami HDInsight z interfejsu użytkownika sieci web Ambari](hdinsight-hadoop-manage-ambari.md)
* [Interfejs API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
