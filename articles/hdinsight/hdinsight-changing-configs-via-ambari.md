---
title: Optymalizacja konfiguracji klastra za pomocą systemu Apache Ambari — Azure HDInsight
description: Do konfigurowania i Optymalizowanie klastrów HDInsight, należy użyć interfejsu użytkownika sieci web Apache Ambari.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: f0db36fa380d0d1bb7f2b581c4bf8fa1abfaadaf
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58805384"
---
# <a name="use-apache-ambari-to-optimize-hdinsight-cluster-configurations"></a>Korzystanie z systemu Apache Ambari w celu optymalizacji konfiguracje klastrów HDInsight

HDInsight zapewnia [Apache Hadoop](https://hadoop.apache.org/) klastry dla aplikacji przetwarzających dane na dużą skalę. Zarządzanie, monitorowanie i optymalizowanie tych złożonych klastrami z wieloma węzłami może być trudne. [Apache Ambari](https://ambari.apache.org/) jest interfejsem sieci web do zarządzania i monitorowania klastrów HDInsight w systemie Linux.  W przypadku klastrów Windows użyj [interfejsu API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

Wprowadzenie do korzystania z interfejsu użytkownika sieci Web Ambari, zobacz [HDInsight Zarządzanie klastrami za pomocą Interfejsu sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md)

Zaloguj się do systemu Ambari pod `https://CLUSTERNAME.azurehdidnsight.net` przy użyciu poświadczeń usługi klastra. Na ekranie początkowej zostanie wyświetlony pulpit nawigacyjny przeglądu.

![Pulpit nawigacyjny systemu Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-dashboard.png)

Interfejs użytkownika sieci web Ambari, może służyć do zarządzania hostami, usług, alerty, konfiguracje i widoków. Ambari nie może służyć do tworzenia klastra usługi HDInsight, usługi uaktualniania, zarządzanie stosy i wersje, zlikwidować recommission hostów lub dodawanie usług do klastra.

## <a name="manage-your-clusters-configuration"></a>Zarządzanie konfiguracją klastra

Ustawienia konfiguracji pomagają dostosować określonej usługi. Aby zmodyfikować ustawienia konfiguracji usługi, wybierz usługę z **usług** paska bocznego (po lewej stronie), a następnie przejdź do **Configs** karty na stronie szczegółów usługi.

![Pasek boczny usług](./media/hdinsight-changing-configs-via-ambari/services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Zmodyfikuj rozmiar sterty środowiska NameNode Java

Rozmiar sterty środowiska NameNode Java zależy od wielu czynników, takich jak obciążenia w klastrze, liczby plików i liczby bloków. Domyślny rozmiar 1 GB dobrze działa z Większość klastrów, mimo że niektóre obciążenia mogą wymagać więcej lub mniej pamięci. 

Aby zmodyfikować rozmiar sterty środowiska NameNode Java:

1. Wybierz **HDFS** z paska bocznego usług i przejdź do **Configs** kartę.

    ![Konfiguracja systemu plików HDFS](./media/hdinsight-changing-configs-via-ambari/hdfs-config.png)

1. Znajdź ustawienie **Rozmiar sterty środowiska NameNode Java**. Można również użyć **filtru** pola tekstowego wpisz i znaleźć określonego ustawienia. Wybierz **pióra** ikony obok nazwy ustawienia.

    ![Rozmiar sterty środowiska NameNode Java](./media/hdinsight-changing-configs-via-ambari/java-heap-size.png)

1. Wpisz nową wartość w polu tekstowym, a następnie naciśnij klawisz **Enter** można zapisać zmiany.

    ![Edytuj rozmiar sterty środowiska NameNode Java](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit.png)

1. Rozmiar sterty środowiska NameNode Java jest zmieniany na 1 GB z 2 GB.

    ![Rozmiar sterty środowiska NameNode Java edytowany](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Zapisz zmiany, klikając na zielony wskaźnik **Zapisz** przycisku w górnej części ekranu konfiguracji.

    ![Zapisz zmiany](./media/hdinsight-changing-configs-via-ambari/save-changes.png)

## <a name="apache-hive-optimization"></a>Apache Hive optymalizacji

W poniższych sekcjach opisano opcje konfiguracji dla optymalizacji wydajności ogólnej Apache Hive.

1. Aby zmodyfikować parametrów konfiguracyjnych programu Hive, wybierz **Hive** z paska bocznego usług.
1. Przejdź do **Configs** kartę.

### <a name="set-the-hive-execution-engine"></a>Ustaw aparat Hive wykonywania

Gałąź zawiera dwa aparatów wykonywania: [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) i [Apache TEZ](https://tez.apache.org/). Tez jest szybsze niż MapReduce. Klastry HDInsight Linux ma Tez jako aparatu wykonywania domyślne. Aby zmienić aparatu wykonywania:

1. W gałęzi **Configs** karty, wpisz **aparatu wykonywania** w polu filtru.

    ![Aparat wykonywania wyszukiwania](./media/hdinsight-changing-configs-via-ambari/search-execution.png)

1. **Optymalizacji** właściwości wartość domyślna to **Tez**.

    ![Optymalizacja - Tez](./media/hdinsight-changing-configs-via-ambari/optimization-tez.png)

### <a name="tune-mappers"></a>Dostosowywanie liczby maperów

Hadoop próbuje podziału (*mapy*) pojedynczy plik do wielu plików i proces wynikowe pliki równolegle. Liczba liczby maperów jest zależna od liczby dzieli dane. Następujące parametry dwóch konfiguracji dysku liczba dzieli dane na aparat wykonywania platformy Tez:

* `tez.grouping.min-size`: Dolny limit na rozmiar grupowanych podziału, z wartością domyślną 16 MB (16,777,216 w bajtach).
* `tez.grouping.max-size`: Górny limit na rozmiar grupowanych podziału, przy użyciu wartości domyślnej 1 GB (1 073 741 824 w bajtach).

Wydajność zasada mówi Zmniejsz obu tych parametrów, aby poprawić czas oczekiwania, zwiększyć większą przepustowość.

Na przykład aby ustawić cztery zadania mapowania w przypadku danych o rozmiarze 128 MB, należy ustawić oba parametry do 32 MB każdy (33,554,432 w bajtach).

1. Aby zmodyfikować parametry limitu, przejdź do **Configs** kartę usługi Tez. Rozwiń **ogólne** panelu, a następnie zlokalizuj `tez.grouping.max-size` i `tez.grouping.min-size` parametrów.

1. Ustawiono oba parametry **33,554,432** bajtów (32 MB).

    ![Rozmiary grupowania tez](./media/hdinsight-changing-configs-via-ambari/tez-grouping-size.png)
 
Te zmiany mają wpływ na wszystkie zadania w aplikacji Tez na serwerze. Aby uzyskać optymalne wyniki, wybierz odpowiedni parametr wartości.

### <a name="tune-reducers"></a>Dostosowywanie reduktorów

[Apache ORC](https://orc.apache.org/) i [Snappy](https://google.github.io/snappy/) oba programy oferują wysoką wydajność. Jednak gałęzi może być zbyt mało reduktorów domyślnie przyczyną wąskich gardeł.

Na przykład załóżmy, że rozmiar danych wejściowych, 50 GB. Czy dane w ORC formacie Snappy kompresji jest 1 GB. Hive Szacuje liczbę potrzebne reduktorów: (liczba bajtów odebranych na liczby maperów / `hive.exec.reducers.bytes.per.reducer`).

Z ustawieniami domyślnymi w tym przykładzie jest 4 reduktorów.

`hive.exec.reducers.bytes.per.reducer` Parametr określa liczbę bajtów przetworzonych w ramach reduktor. Wartość domyślna to 64 MB. Dostrajanie tę wartość w dół zwiększa równoległości i może zwiększyć wydajność. Dostrajanie na zbyt niską może utworzyć reduktorów zbyt wiele, potencjalnie negatywnego wpływu na wydajność. Ten parametr zależy od wymagań dotyczących danych, ustawienia kompresji i innych czynników środowiskowych.

1. Zmodyfikuj parametr, przejdź do gałęzi **Configs** kartę i Znajdź **danych na reduktor** parametru na stronie Ustawienia.

    ![Dane na reduktor](./media/hdinsight-changing-configs-via-ambari/data-per-reducer.png)
 
1. Wybierz **Edytuj** zmodyfikuj wartość do 128 MB (w bajtach 134,217,728), a następnie naciśnij klawisz **Enter** do zapisania.

    ![Dane na reduktor - edytowany](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Biorąc pod uwagę rozmiar wejściowy 1024 MB, 128 MB danych na reduktor, istnieją reduktorów 8 (1024/128).

1. Nieprawidłowa wartość parametru **danych na reduktor** parametru może skutkować dużą liczbę reduktorów, negatywnego wpływu na wydajność zapytań. Aby ograniczyć liczbę maksymalną liczbę reduktorów, ustaw `hive.exec.reducers.max` odpowiednią wartość. Wartość domyślna to 1009.

### <a name="enable-parallel-execution"></a>Włącz wykonywanie równoległe

Zapytanie Hive jest wykonywane w jeden czy więcej etapów. Jeśli etapy niezależnych można uruchomić równolegle, który zwiększa wydajność zapytań.

1.  Aby włączyć równoległego wykonywania zapytań, przejdź do gałęzi **Config** kartę i wyszukaj `hive.exec.parallel` właściwości. Wartość domyślna to false. Zmień wartość na wartość PRAWDA, a następnie naciśnij klawisz **Enter** można zapisać wartości.
 
1.  Aby ograniczyć liczbę zadań do uruchomienia w sposób równoległy, zmodyfikuj `hive.exec.parallel.thread.number` właściwości. Wartość domyślna to 8.

    ![Hive exec równoległych](./media/hdinsight-changing-configs-via-ambari/hive-exec-parallel.png)


### <a name="enable-vectorization"></a>Włącz wektoryzacji

Gałąź przetwarza dane wiersz po wierszu. Wektoryzacji kieruje Hive do przetwarzania danych w blokach 1024 wierszy, a nie na jeden wiersz w danym momencie. Wektoryzacji ma zastosowanie tylko do formatu plików ORC.

1. Aby włączyć wykonywanie zapytań zwektoryzowane, przejdź do gałęzi **Configs** kartę i wyszukaj `hive.vectorized.execution.enabled` parametru. Wartością domyślną jest wartość true dla gałęzi 0.13.0 lub nowszej.
 
1. Aby włączyć zwektoryzowane wykonywanie po stronie redukcji zapytania, ustaw `hive.vectorized.execution.reduce.enabled` parametru na wartość true. Wartość domyślna to false.

    ![Wykonywania zadań hive wektoryzowana](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Włącz Optymalizacja oparta na koszt (CBO)

Domyślnie gałąź poniżej zestaw reguł można znaleźć jeden plan wykonania zapytania optymalne. Optymalizacja oparta na koszt (CBO) ocenia wiele planów, aby wykonać zapytanie i przypisuje koszt każdego planu, a następnie określa najtańszej planu, aby wykonać zapytanie.

Aby włączyć CBO, przejdź do gałęzi **Configs** kartę i wyszukaj `parameter hive.cbo.enable`, następnie Przełącz przycisk przełączania **na**.

![CBO config](./media/hdinsight-changing-configs-via-ambari/cbo.png)

Następujące dodatkowe parametry konfiguracji zwiększyć wydajność zapytań technologii Hive, po włączeniu CBO:

* `hive.compute.query.using.stats`

    Gdy wartość true, Hive są używane statystyki przechowywane w jego magazynu metadanych do prostych zapytań, takich jak `count(*)`.

    ![Statystyki CBO](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Statystyki kolumny są tworzone, gdy CBO jest włączona. Gałąź używa statystyki kolumny, które są przechowywane w Magazyn metadanych, w celu optymalizacji zapytań. Pobieranie statystyk kolumny, dla każdej kolumny trwa dłużej, gdy liczba kolumn jest wysoka. Po ustawieniu na wartość false, to ustawienie wyłącza pobierania statystyk kolumny z magazynu metadanych.

    ![Hive statystyki kolumn w zestawie statystyki](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Podstawowa partycja statystyki, takie jak liczba wierszy, rozmiar danych i rozmiar pliku są przechowywane w magazynu metadanych. Po ustawieniu na wartość true, partycji, który statystyki są pobierane z magazynu metadanych. W przypadku wartości FAŁSZ, rozmiar pliku jest pobierana z systemu plików, a liczbę wierszy pobieranych ze schematu wiersza.

    ![Hive statystyki zestaw partycji statystyki](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Włącz kompresję pośrednich

Mapa zadania Utwórz pośrednie pliki, które są używane przez zadania reduktor. Pośredni Kompresja zmniejsza rozmiar pliku pośredniego.

Zadania Hadoop są zazwyczaj bottlenecked we/wy. Skompresowanie danych można przyspieszyć We/Wy i ogólną transferu sieciowego.

Kompresja dostępne typy to:

| Format | Tool | Algorytm | Rozszerzenie pliku | Podzielne? |
| -- | -- | -- | -- | -- |
| Gzip | Gzip | KORYGOWANIA | .gz | Nie |
| Bzip2 | Bzip2 | Bzip2 |.bz2 | Yes |
| LZO | Lzop | LZO | .lzo | Tak, jeśli indeksowane |
| Snappy | ND | Snappy | Snappy | Nie |

Zgodnie z ogólną zasadą ważne jest posiadanie podzielne metoda kompresji, w przeciwnym razie zostanie utworzony liczby maperów bardzo mało. Jeśli dane wejściowe to tekst, `bzip2` jest najlepszym rozwiązaniem. Dla formatu ORC Snappy jest najszybszą opcję kompresji.

1. Aby włączyć kompresję pośredniego, przejdź do gałęzi **Configs** kartę, a następnie ustaw `hive.exec.compress.intermediate` parametru na wartość true. Wartość domyślna to false.

    ![Kompresuj exec hive pośrednich](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > Aby skompresować pośrednie pliki, należy wybrać kodera-dekodera kompresji z Procesora niższym kosztom, kodera-dekodera nie zainstalowano wysoką kompresję danych wyjściowych.

1. Aby ustawić kodera-dekodera kompresji pośredniego, dodawanie właściwości niestandardowych `mapred.map.output.compression.codec` do `hive-site.xml` lub `mapred-site.xml` pliku.

1. Aby dodać niestandardową wartość ustawienia:

    a. Przejdź do gałęzi **Configs** kartę, a następnie wybierz pozycję **zaawansowane** kartę.

    b. W obszarze **zaawansowane** kartę, Znajdź i rozwiń **lokacjami hive niestandardowe** okienka.

    c. Kliknij łącze **Dodaj właściwość** w dolnej części okienka niestandardowe witryny programu hive.

    d. W oknie Dodaj właściwość wprowadzić `mapred.map.output.compression.codec` jako klucz i `org.apache.hadoop.io.compress.SnappyCodec` jako wartość.

    e. Kliknij pozycję **Add** (Dodaj).

    ![Właściwość niestandardowa gałęzi](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Umożliwia kompresję pliku pośredniego, przy użyciu kompresji Snappy. Po dodaniu właściwość pojawia się w okienku niestandardowe witryny programu hive.

    > [!NOTE]  
    > Ta procedura modyfikuje `$HADOOP_HOME/conf/hive-site.xml` pliku.

### <a name="compress-final-output"></a>Kompresowanie pliku wyjściowego

Również można skompresować do pliku wyjściowego Hive.

1. Aby skompresować do pliku wyjściowego gałęzi, przejdź do gałęzi **Configs** kartę, a następnie ustaw `hive.exec.compress.output` parametru na wartość true. Wartość domyślna to false.

1. Aby wybrać kodera-dekodera kompresji danych wyjściowych, należy dodać `mapred.output.compression.codec` właściwości niestandardowych do okienka lokacji hive niestandardowe, zgodnie z opisem w poprzedniej sekcji Krok 3.

    ![Właściwość niestandardowa gałęzi](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Włącz związanego z wykonywaniem spekulatywnym

Związanego z wykonywaniem spekulatywnym spowoduje uruchomienie określonej liczby duplikatów zadań w celu wykrycia i niedozwolonych śledzenia zadań wolno działające, jednocześnie poprawiając ogólną wykonywania zadania, optymalizując wyniki poszczególnych zadań.

Związanego z wykonywaniem spekulatywnym nie powinna być włączona dla długotrwałych zadań MapReduce z dużą ilością danych wejściowych.

* Aby włączyć związanego z wykonywaniem spekulatywnym, przejdź do gałęzi **Configs** kartę, a następnie ustaw `hive.mapred.reduce.tasks.speculative.execution` parametru na wartość true. Wartość domyślna to false.

    ![Gałąź mapred zmniejszyć zadania związanego z wykonywaniem spekulatywnym](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Dostosowywanie partycji dynamicznych

Gałąź umożliwia tworzenie partycji dynamicznych podczas wstawiania rekordów do tabeli, bez wstępnego definiowania każdej partycji. Jest zaawansowaną funkcją, chociaż może to spowodować utworzenie dużej liczby partycji i dużą liczbę plików, dla każdej partycji.

1. Dla usługi Hive w celu partycji dynamicznych `hive.exec.dynamic.partition` wartość parametru powinna być prawdziwe (ustawienie domyślne).

1. Zmień tryb partycji dynamicznych, aby *strict*. W trybie ścisłym co najmniej jedna partycja musi być statyczne. Zapobiega to zapytania bez filtru partycji w klauzuli WHERE, czyli *strict* zapobiega zapytań, które skanowania wszystkich partycji. Przejdź do gałęzi **Configs** kartę, a następnie ustaw `hive.exec.dynamic.partition.mode` do **strict**. Wartość domyślna to **nonstrict**.
 
1. Aby ograniczyć liczbę partycji dynamicznych, które ma zostać utworzony, zmodyfikuj `hive.exec.max.dynamic.partitions` parametru. Wartość domyślna to 5000.
 
1. Ogranicz całkowitą liczbę partycji dynamicznych w każdym węźle, należy zmodyfikować `hive.exec.max.dynamic.partitions.pernode`. Wartość domyślna to 2000.

### <a name="enable-local-mode"></a>Włączanie trybu lokalnego

Tryb lokalny umożliwia gałęzi umożliwia wykonywanie wszystkich zadań, zadania, na jednym komputerze lub czasami w pojedynczym procesie. Zwiększa to wydajność zapytań, jeśli dane wejściowe są małe, a koszty uruchamiania zadania dotyczące zapytań zużywa znacznego procentu całkowitej wykonywania zapytania.

Aby włączyć tryb lokalny, Dodaj `hive.exec.mode.local.auto` parametru do panelu lokacji hive niestandardowe, zgodnie z opisem w kroku 3 procedury [Włącz kompresję pośrednich](#enable-intermediate-compression) sekcji.

![Gałąź exec trybu lokalnego automatycznie](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Zestaw pojedynczego MapReduce MultiGROUP przez

Gdy ta właściwość jest ustawiona na wartość true, zapytania MultiGROUP przez klucze wspólne Grupuj według generuje pojedynczego zadania MapReduce.  

Aby włączyć to zachowanie, należy dodać `hive.multigroupby.singlereducer` parametrów do okienka lokacji hive niestandardowe, zgodnie z opisem w kroku 3 procedury [Włącz kompresję pośrednich](#enable-intermediate-compression) sekcji.

![Gałąź ustawić MultiGROUP pojedynczego MapReduce](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Dodatkowe optymalizacje gałęzi

W poniższych sekcjach opisano dodatkowe optymalizacje związane z programu Hive, które można ustawić.

#### <a name="join-optimizations"></a>Dołącz do optymalizacji

Domyślny typ sprzężenia w gałęzi jest *sprzężenia shuffle*. W gałęzi liczby maperów specjalnych odczytać dane wejściowe i emisji, sprzężenia para klucza i wartości, do pliku pośredniego. Hadoop sortuje i scala te pary na etapie losowa. Ten etap losowa jest kosztowne. Wybieranie prawego sprzężenia, na podstawie danych może znacznie poprawić wydajność.

| Typ przyłączenia | Kiedy | Jak | Ustawienia programu hive | Komentarze |
| -- | -- | -- | -- | -- |
| Shuffle sprzężenia | <ul><li>Wybór domyślny</li><li>Zawsze działa.</li></ul> | <ul><li>Odczytuje z częścią jednej z tabel</li><li>Przedziały i sortowanie na kluczu sprzężenia</li><li>Wysyła jeden przedział do każdego Zmniejsz</li><li>Sprzężenia odbywa się po stronie Zmniejsz</li></ul> | Nie znaczących ustawienie potrzebne gałęzi | Działa z każdym |
| Przyłączanie do mapy | <ul><li>Jedna tabela mieści się w pamięci</li></ul> | <ul><li>Odczytuje małej tabeli do tabeli mieszania pamięci</li><li>Strumienie przez część dużych plików</li><li>Dołącza każdy rekord w tabeli wyznaczania wartości skrótu</li><li>Sprzężenia są przez samego mapowania</li></ul> | `hive.auto.confvert.join=true` | Bardzo szybko, ale jest ograniczony |
| Sort Merge Bucket | Jeśli obie tabele: <ul><li>Sortowane takie same</li><li>Bucketed takie same</li><li>Łączenie sortowane zasobnikach kolumny</li></ul> | Każdy proces: <ul><li>Odczytuje Wiadro z każdej tabeli</li><li>Przetwarza wiersz mający najmniejszą wartość</li></ul> | `hive.auto.convert.sortmerge.join=true` | Bardzo wydajny |

#### <a name="execution-engine-optimizations"></a>Optymalizacje aparatu wykonywania

Dodatkowe zalecenia dotyczące optymalizowania aparat Hive wykonywania:

| Ustawienie | Zalecane | HDInsight domyślne |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | TRUE = bezpieczniejsze, wolniejsze; FALSE = szybciej | false |
| `tez.am.resource.memory.mb` | 4 GB górną granicę dla większości | Automatycznie dopasowane |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10 000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="apache-pig-optimization"></a>Apache Pig optymalizacji

[Apache Pig](https://pig.apache.org/) właściwości można modyfikować interfejs webowy Ambari do dostrojenia zapytań Pig. Modyfikowanie właściwości języka Pig z systemu Ambari bezpośrednio modyfikuje właściwości Pig w `/etc/pig/2.4.2.0-258.0/pig.properties` pliku.

1. Aby zmodyfikować właściwości Pig, przejdź do Pig **Configs** kartę, a następnie rozwiń **zaawansowane właściwości pig** okienka.

1. Znajdź, usuń znaczniki komentarza i zmień wartość właściwości, które chcesz zmodyfikować.

1. Wybierz **Zapisz** w prawym górnym rogu okna, aby zapisać nową wartość. Niektóre właściwości mogą wymagać ponownego uruchomienia tej usługi.

    ![Zaawansowane właściwości pig](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)
 
> [!NOTE]  
> Wszystkie ustawienia na poziomie sesji przesłonią wartości właściwości w `pig.properties` pliku.

### <a name="tune-execution-engine"></a>Dostosowywanie aparatu wykonywania

Dostępne do wykonywania skryptów usługi Pig są dwoma silnikami wykonywania: MapReduce i Tez. Tez to aparat zoptymalizowane i jest znacznie wyższa niż MapReduce.

1. Aby zmodyfikować aparatu wykonywania w **zaawansowane właściwości pig** okienku znaleźć właściwości `exectype`.

1. Wartość domyślna to **MapReduce**. Zmień ją na **Tez**.


### <a name="enable-local-mode"></a>Włączanie trybu lokalnego

Podobnie jak Hive, tryb lokalny jest używany do szybkości zadań z stosunkowo mniejszych ilości danych.

1. Aby włączyć tryb lokalny, ustaw `pig.auto.local.enabled` do **true**. Wartość domyślna to false.

1. Zadania z rozmiarem dane wejściowe mniejsza niż `pig.auto.local.input.maxbytes` wartości właściwości są uznawane za małe zadania. Wartość domyślna to 1 GB.


### <a name="copy-user-jar-cache"></a>Skopiuj plik jar użytkownika w pamięci podręcznej

Pig kopiuje pliki JAR, wymagane przez funkcje zdefiniowane przez użytkownika do rozproszonej pamięci podręcznej, aby były one dostępne dla zadań węzłów. Tych plikach JAR nie zmieniają się często. Jeśli włączona, `pig.user.cache.enabled` ustawienie umożliwia plikach JAR do umieszczenia w pamięci podręcznej w celu użycia dla zadania wykonywane przez tego samego użytkownika. Skutkuje to niewielki wzrost wydajności zadania.

1. Aby włączyć, ustaw `pig.user.cache.enabled` na wartość true. Wartość domyślna to false.

1. Podstawowa ścieżka pamięci podręcznej plikach JAR ustawia `pig.user.cache.location` do ścieżki podstawowej. Wartość domyślna to `/tmp`.


### <a name="optimize-performance-with-memory-settings"></a>Optymalizuj wydajność przy użyciu ustawień pamięci

Następujące ustawienia pamięci może pomóc w optymalizacji wydajności skryptu Pig.

* `pig.cachedbag.memusage`: Ilość pamięci przydzielonej do zbioru. Zbiór to kolekcja krotek. Spójna kolekcja jest uporządkowany zestaw pól, a pole to fragment danych. W przypadku danych, w torebce poza ilość przydzielonej pamięci, są rozrzucone na dysku. Wartość domyślna to 0,2, który reprezentuje 20 procent dostępnej pamięci. Ta pamięć jest współużytkowana przez wszystkie zbiory w aplikacji.

* `pig.spill.size.threshold`: Zbiory większy niż ten próg rozmiaru rozlania (w bajtach) są rozrzucone na dysku. Wartość domyślna to 5 MB.


### <a name="compress-temporary-files"></a>Kompresuj pliki tymczasowe

Pig generuje pliki tymczasowe podczas wykonywania zadania. Kompresowanie plików tymczasowych powoduje wzrost wydajności podczas odczytywania lub zapisywania plików na dysku. Następujące ustawienia może służyć do kompresji plików tymczasowych.

* `pig.tmpfilecompression`: W przypadku wartości true umożliwia kompresję pliku tymczasowego. Wartość domyślna to false.

* `pig.tmpfilecompression.codec`: Koder-dekoder kompresji na potrzeby kompresowanie plików tymczasowych. Kompresja zalecane kodery-dekodery są [LZO](https://www.oberhumer.com/opensource/lzo/) i Snappy niższe wykorzystania procesora CPU.

### <a name="enable-split-combining"></a>Włącz łączenie podziału

Po włączeniu małe pliki są łączone na mniejszą liczbę zadań mapy. Zwiększa to wydajność zadań z wielu małych plików. Aby włączyć, ustaw `pig.noSplitCombination` na wartość true. Wartość domyślna to false.


### <a name="tune-mappers"></a>Dostosowywanie liczby maperów

Liczba liczby maperów jest kontrolowana przez modyfikowanie właściwości `pig.maxCombinedSplitSize`. To ustawienie określa rozmiar danych do przetworzenia przez zadania jedną mapę. Wartość domyślna to system plików domyślny rozmiar bloku. Zwiększenie tej wartości powoduje zmniejszenie liczby zadań mapowania.


### <a name="tune-reducers"></a>Dostosowywanie reduktorów

Liczba reduktorów jest obliczana na podstawie parametru `pig.exec.reducers.bytes.per.reducer`. Parametr określa liczbę bajtów przetworzonych w ramach reduktor, domyślnie 1 GB. Aby ograniczyć liczbę maksymalną liczbę reduktorów, ustaw `pig.exec.reducers.max` właściwości domyślnie 999.


## <a name="apache-hbase-optimization-with-the-ambari-web-ui"></a>Optymalizacja bazy danych Apache HBase przy użyciu interfejsu użytkownika sieci web systemu Ambari

[Apache HBase](https://hbase.apache.org/) konfiguracji zostało zmodyfikowane w porównaniu **konfiguracje bazy danych HBase** kartę. W poniższych sekcjach opisano niektóre ważnych ustawień konfiguracyjnych, które mają wpływ na wydajność bazy danych HBase.

### <a name="set-hbaseheapsize"></a>Set HBASE_HEAPSIZE

Rozmiar sterty HBase określa maksymalną ilość sterty do użycia w megabajtach, *region* i *wzorca* serwerów. Wartość domyślna to 1000 MB. Powinny to być dostosowane dla obciążenia klastra.

1. Aby zmodyfikować, przejdź do **zaawansowane środowisko bazy danych HBase** okienko w bazie danych HBase **Configs** kartę, a następnie znajdź `HBASE_HEAPSIZE` ustawienie.

1. Do 5000 MB, należy zmienić wartość domyślną.

    ![HBASE_HEAPSIZE](./media/hdinsight-changing-configs-via-ambari/hbase-heapsize.png)


### <a name="optimize-read-heavy-workloads"></a>Optymalizowanie odczycie obciążeń

Następujące konfiguracje są ważne, aby zwiększyć wydajność obciążeń odczycie.

#### <a name="block-cache-size"></a>Rozmiar pamięci podręcznej bloku

Pamięć podręczna bloku jest pamięci podręcznej odczytu. Jego rozmiar jest kontrolowana przez `hfile.block.cache.size` parametru. Wartość domyślna to Update 0.4, czyli 40 procent całkowitej region pamięci serwera. Im większy blok rozmiar pamięci podręcznej, będzie szybsze losowych operacji odczytu.

1. Aby modyfikować tego parametru, przejdź do **ustawienia** kartę w bazie danych HBase **Configs** kartę, a następnie zlokalizuj **% RegionServer przydzielone do buforów odczytu**.

    ![Rozmiar pamięci podręcznej bloku bazy danych HBase](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)
 
1. Aby zmienić wartość, zaznacz **Edytuj** ikony.


#### <a name="memstore-size"></a>Rozmiar magazynu pamięci

Wszystkie modyfikacje są przechowywane w buforze pamięci o nazwie *magazynu pamięci*. Zwiększa to łączna ilość danych, które mogą być zapisywane na dysku w ramach jednej operacji i zwiększa kolejny dostęp do najnowszych zmian. Rozmiar magazynu pamięci jest definiowany przez następujące dwa parametry:

* `hbase.regionserver.global.memstore.UpperLimit`: Określa maksymalną wartość procentową, można użyć w połączeniu magazynu pamięci serwera regionu.

* `hbase.regionserver.global.memstore.LowerLimit`: Określa minimalny procent, można użyć w połączeniu magazynu pamięci serwera regionu.

Zoptymalizowane pod kątem losowych operacji odczytu, można zmniejszyć górny i dolny limit magazynu pamięci.


#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Liczba pobranych podczas skanowania dysku wierszy

`hbase.client.scanner.caching` Ustawienie określa liczbę wierszy odczytu z dysku po `next` metoda jest wywoływana w skaner.  Wartość domyślna to 100. Im większa liczba, tym mniej wywołań zdalnych wprowadzone od klienta do regionu serwera, skutkuje szybszy skanowania. Jednak to spowoduje zwiększenie wykorzystania pamięci na komputerze klienckim.

![Baza danych HBase liczba pobranych wierszy](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Nie należy ustawiać wartości w taki sposób, że czas od wywołania metody dalej na skanerze jest większy niż limit czasu skanera. Skaner limitu czasu jest definiowany przez `hbase.regionserver.lease.period` właściwości.


### <a name="optimize-write-heavy-workloads"></a>Optymalizowanie obciążeń procesów

Następujące konfiguracje są ważne, aby zwiększyć wydajność obciążeń procesów.


#### <a name="maximum-region-file-size"></a>Region maksymalny rozmiar pliku

Baza danych HBase przechowuje dane w formacie pliku wewnętrznego, o nazwie *HFile*. Właściwość `hbase.hregion.max.filesize` definiuje rozmiar pojedynczego hFile — dla regionu.  Region jest podzielony na dwa regiony, jeśli suma wszystkich HFiles w regionie jest większa niż to ustawienie.
 
![Maksymalny rozmiar pliku HRegion bazy danych HBase](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Im większy rozmiar pliku regionu, im mniejsza liczba dzieli dane. Możesz zwiększyć rozmiar pliku, aby określić wartość z wynikiem maksymalną wydajność zapisu.


#### <a name="avoid-update-blocking"></a>Należy unikać blokowania aktualizacji

* Właściwość `hbase.hregion.memstore.flush.size` definiuje rozmiar, w którym magazynu pamięci jest opróżniany do dysku. Domyślny rozmiar to 128 MB.

* Mnożnik bloku regionu Hbase jest definiowany przez `hbase.hregion.memstore.block.multiplier`. Wartość domyślna to 4. Maksymalna dozwolona wartość to 8.

* Baza danych HBase blokuje aktualizacji, jeśli jest magazynu w pamięci (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) bajtów.

    Z wartościami domyślnymi opróżniania rozmiaru i mnożnik bloku aktualizacje są blokowane po 128 * 4 = 512 MB rozmiar magazynu pamięci. Aby zmniejszyć aktualizacji blokuje liczba, zwiększ wartość `hbase.hregion.memstore.block.multiplier`.

![Mnożnik bloku regionu HBase](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)


### <a name="define-memstore-size"></a>Określ wielkość magazynu pamięci

Rozmiar magazynu pamięci jest definiowany przez `hbase.regionserver.global.memstore.UpperLimit` i `hbase.regionserver.global.memstore.LowerLimit` parametrów. Ustawienia te wartości równe, który ogranicza do każdego z innych wstrzymuje działanie podczas zapisu (również co powoduje częstsze opróżniania) i powoduje zapisu zwiększoną wydajność.


### <a name="set-memstore-local-allocation-buffer"></a>Zestaw magazynu pamięci lokalnej alokacji buforu

Użycie bufora alokacji lokalnego magazynu pamięci jest określany przez właściwość `hbase.hregion.memstore.mslab.enabled`. Po włączeniu (PRAWDA) zapobiega to fragmentację sterty podczas operacji wysokie obciążenia podczas zapisu. Wartość domyślna to true.
 
![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)


## <a name="next-steps"></a>Kolejne kroki

* [Zarządzanie klastrami HDInsight przy użyciu interfejsu użytkownika sieci web Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
