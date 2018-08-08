---
title: Optymalizowanie zadań platformy Spark dla wydajności — Azure HDInsight
description: Pokazuje typowe strategie uzyskać najlepszą wydajność klastry Spark.
services: hdinsight
ms.service: hdinsight
author: maxluk
ms.author: maxluk
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/11/2018
ms.openlocfilehash: 7d542a4fe8c63bb44e073f3af05aed926fc4ba81
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39619553"
---
# <a name="optimize-spark-jobs"></a>Optymalizowanie zadań platformy Spark

Dowiedz się, jak zoptymalizować konfigurację klastra platformy Spark dla określonego obciążenia.  Najbardziej typowe wyzwaniem jest wykorzystanie pamięci, z powodu nieprawidłowej konfiguracji (szczególnie problem o rozmiarze executors) długotrwałych operacji i zadań, które powoduje Kartezjańskiego operacji. Można przyspieszyć zadania za pomocą odpowiedniej pamięci podręcznej i w celu umożliwienia [niesymetryczność danych](#optimize-joins-and-shuffles). Uzyskać najlepszą wydajność monitorować i przejrzyj długotrwałych i korzystające z zasobów wykonań zadań platformy Spark.

W poniższych sekcjach opisano typowe optymalizacje zadanie platformy Spark i zalecenia.

## <a name="choose-the-data-abstraction"></a>Wybierz pozyskiwania danych

Spark 1.x używa danych abstrakcyjne danych, a następnie Spark 2.x wprowadzone elementy Dataframe i zestawów danych. Należy wziąć pod uwagę następujące względnych zalet:

* **Elementy Dataframe**
    * W większości przypadków najlepszy wybór
    * Udostępnia optymalizacji zapytań za pomocą Catalyst
    * Generowanie kodu w całości etapu
    * Bezpośredni dostęp do pamięci
    * Niski wyrzucania elementów bezużytecznych (GC)
    * Nie jak przyjazny dla dewelopera jako zestawy danych, ponieważ nie ma żadnych sprawdzanie w czasie kompilacji lub programowanie obiektów domeny
* **Zestawy danych**
    * Dobre w złożone potoki przetwarzania ETL, w których wpływ na wydajność jest do zaakceptowania
    * Nie są odpowiednie w agregacji, gdzie może być znaczny wpływ na wydajność
    * Udostępnia optymalizacji zapytań za pomocą Catalyst
    * Przyjazne dla deweloperów, zapewniając kontroli programowania i kompilacji obiektu domeny
    * Dodaje serializacji/deserializacji obciążenie
    * Wysokie obciążenie GC
    * Przerywa generowanie kodu w całości etapu
* **Zestawów danych**
    * Platformie Spark 2.x, nie trzeba używać zestawów danych, chyba że potrzebne do tworzenia nowych RDD niestandardowe
    * Brak optymalizacji zapytań, za pośrednictwem Catalyst
    * Generowanie kodu w całości etapu nie
    * Wysokie obciążenie GC
    * Należy użyć starszej wersji 1.x Spark interfejsów API

## <a name="use-optimal-data-format"></a>Użyj formatu optymalne danych

Platforma Spark obsługuje wiele formatów, takich jak csv, json, xml, parquet, orc i avro. Platforma Spark można rozszerzyć do obsługi wielu formatach więcej z zewnętrznymi źródłami danych — Aby uzyskać więcej informacji, zobacz [Spark pakietów](https://spark-packages.org).

Najlepszy format dla wydajności jest parquet z *snappy kompresji*, co jest ustawieniem domyślnym platformie Spark 2.x. Parquet przechowuje dane w formacie kolumnowym i jest wysoce zoptymalizowane pod kątem platformie Spark.

## <a name="select-default-storage"></a>Wybierz magazyn domyślny

Gdy tworzysz nowy klaster Spark, masz możliwość dokonania wyboru z usługi Azure Blob Storage lub Azure Data Lake Store jako magazynem domyślnym klastra. Obie opcje zapewniają korzyści długoterminowego przejściowy klastrów, więc dane nie automatycznie usuwane po usunięciu klastra. Można ponownie utworzyć klaster przejściowy i nadal uzyskiwać dostęp do danych.

| Typ Store | System plików | Szybkość | Przejściowe | Przypadki użycia |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:**//url/ | **Standardowa** | Yes | Przejściowy klastra |
| Azure Data Lake Store | **ADL:**//url/ | **Szybciej** | Yes | Przejściowy klastra |
| Lokalny system plików HDFS | **hdfs:**//url/ | **Najszybszy** | Nie | Interaktywne klastra 24/7 |

## <a name="use-the-cache"></a>Użycie pamięci podręcznej

Platforma Spark udostępnia swoje własne natywnej pamięci podręcznej mechanizmów, które mogą być używane za pośrednictwem różnych metod takich jak `.persist()`, `.cache()`, i `CACHE TABLE`. Natywne buforowania obowiązuje z małymi zestawami danych oraz jak wymagających wyników pośrednich w pamięci podręcznej dostępne potoki przetwarzania ETL. Jednak buforowania natywnego platformy Spark obecnie nie działa dobrze w przypadku partycjonowania, ponieważ tabeli pamięci podręcznej nie zachowuje partycjonowania danych. Jest bardziej ogólny i niezawodne techniki buforowania *buforowania warstwy magazynowania*.

* Natywne Spark buforowania (niezalecane)
    * Dobra w przypadku małych zestawów danych.
    * Działa nie z podziałem na partycje, które mogą ulec zmianie w przyszłych wersjach platformy Spark.

* Magazyn poziom buforowania (zalecane)
    * Można zaimplementować przy użyciu [Alluxio](http://www.alluxio.org/).
    * Używa w pamięci i buforowania dysku SSD.

* Lokalnego systemu HDFS (zalecane)
    * `hdfs://mycluster` Ścieżka.
    * Korzysta z pamięci podręcznej dysków SSD.
    * Buforowane dane zostaną utracone po usunięciu klastra, konieczności ponownej kompilacji pamięci podręcznej.

## <a name="use-memory-efficiently"></a>Efektywnie wykorzystać pamięci

Platforma Spark działa, umieszczając dane w pamięci, dlatego zarządzanie zasobami pamięci jest kluczowym aspektem optymalizacji wykonywania zadań platformy Spark.  Istnieje kilka technik, które można zastosować, aby efektywnie wykorzystać klastra pamięci.

* Preferowanie mniejszych partycji danych i uwzględnić rozmiar danych, typy i dystrybucji w strategii partycjonowania.
* Należy wziąć pod uwagę nowszą, bardziej wydajne [serializacja danych Kryo](https://github.com/EsotericSoftware/kryo), zamiast domyślnej serializacji języka Java.
* Preferuj za pomocą usługi YARN, ponieważ są dzielone `spark-submit` przy użyciu usługi batch.
* Monitorowanie i dostosowywanie ustawień konfiguracji platformy Spark.

Dla Twojej informacji struktury pamięci platforma Spark i niektóre parametry pamięci kluczowych funkcji wykonawczej są wyświetlane na następnej ilustracji.

### <a name="spark-memory-considerations"></a>Uwagi dotyczące pamięci aparatu Spark

Jeśli używasz usługi YARN YARN steruje maksymalną suma pamięci używanej przez wszystkie kontenery w każdym węźle platformy Spark.  Na poniższym diagramie przedstawiono kluczowe obiektów i ich wzajemne relacje.

![Zarządzanie pamięcią platformy Spark usługi YARN](./media/apache-spark-perf/yarn-spark-memory.png)

Aby rozwiązać "za mało pamięci" wiadomości, wypróbuj:

* Przejrzyj przesuwa zarządzania DAG. Obniżone reducting po stronie mapy, wstępnie partycji (lub Podziel na przedziały) dane źródłowe, zmaksymalizować przesuwa pojedynczego i zmniejszyć ilość wysyłanych danych.
* Preferuj `ReduceByKey` z jej limitu pamięci stały się `GroupByKey`, zapewniającą agregacji, obsługi okien i innych funkcji, ale ma limitu pamięci niepowiązane pods.
* Preferuj `TreeReduce`, który wykonuje więcej pracy nad funkcja wykonawcza lub partycji, aby `Reduce`, który wykonuje całą pracę od sterownika.
* Wykorzystaj elementy Dataframe, a nie obiekty RDD niższego poziomu.
* Utwórz complexType, które hermetyzują akcje, takie jak "N pierwszych", różne agregacji lub operacje obsługi okien.

## <a name="optimize-data-serialization"></a>Optymalizowanie serializacja danych

Zadań platformy Spark są dystrybuowane, dzięki czemu serializacji odpowiednie dane to ważne, aby uzyskać najlepszą wydajność.  Istnieją dwie opcje serializacji dla platformy Spark:

* Serializacja Java jest ustawieniem domyślnym.
* Serializacja Kryo jest nowszy format i może spowodować szybsze i spójne serializacji niż języka Java.  Kryo wymaga zarejestrowania klas w programie, i nie obsługuje jeszcze wszystkich typów możliwych do serializacji.

## <a name="use-bucketing"></a>Użyj obsługą zasobników

Obsługą zasobników jest podobny do partycjonowanie danych, ale może zawierać każdego przedziału, a nie tylko jeden zestaw wartości w kolumnie. Podziału na zasobniki działa dobrze w przypadku partycjonowania na (w milionach lub więcej) dużą liczbę wartości, takich jak identyfikatory produktu. Zasobnik jest określany przez utworzenie skrótu klucza zasobnika wiersza. Zasobnikach usługa tabele oferuje unikatowe optymalizacji, ponieważ są one przechowywane metadane dotyczące ich zostały zasobnikach i posortowane.

Niektóre zaawansowane funkcje podziału na zasobniki są:

* Optymalizacji zapytań w oparciu o podziału na zasobniki meta informacje
* Zoptymalizowane agregacji
* Zoptymalizowane sprzężenia

Można użyć partycjonowania i obsługą zasobników w tym samym czasie.

## <a name="optimize-joins-and-shuffles"></a>Optymalizacja sprzężenia i przesuwa

Jeśli masz powolne zadania na przyłączenie lub losowa przyczyną jest prawdopodobnie *niesymetryczność danych*, czyli rozbieżności danych zadania. Na przykład zadanie mapy może potrwać 20 sekund, ale uruchomienie zadania, gdzie dane są połączone lub przekazanych zajmuje.   Aby rozwiązać problem niesymetryczność danych, należy soli cały klucz lub użyj *izolowany ziarna* dla pewien podzbiór kluczy.  Jeśli używasz izolowanych ziarna powinien dokładniej przefiltrować do izolowania podzbiór solone kluczy w mapie sprzężenia. Innym rozwiązaniem jest wprowadzenie kolumny zasobnika i wstępnej agregacji w zasobnikach najpierw.

Innym czynnikiem powodujące powolne sprzężeń może być typ sprzężenia. Domyślnie platforma Spark korzysta `SortMerge` typ sprzężenia. Ten typ sprzężenia jest najlepszym rozwiązaniem w przypadku dużych zestawów danych, ale w przeciwnym razie jest obliczeniowo kosztowne, ponieważ jego musi najpierw posortować lewej i prawej stronie danych przed scaleniem ich.

A `Broadcast` sprzężenia jest najodpowiedniejsza dla mniejszych zestawów danych lub gdzie jednej strony sprzężenia jest znacznie mniejsza niż druga strona. Tego rodzaju łączenia jednej strony jest wysyłana do wszystkich executors i wymaga większej ilości pamięci emisji ogólnie rzecz biorąc.

W konfiguracji można zmienić typ sprzężenia, ustawiając `spark.sql.autoBroadcastJoinThreshold`, można także ustawić wskazówki sprzężenia, przy użyciu interfejsów API DataFrame (`dataframe.join(broadcast(df2))`).

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")
sql("SELECT col1, col2 FROM V_JOIN")
```

Jeśli używasz tabel zasobnikach, a następnie jest innego typu, dołącz `Merge` sprzężenia. Poprawnie wstępnie podzielonym na partycje i wstępnie posortowany zestaw danych zostanie pominięta faza kosztowne sortowania z `SortMerge` sprzężenia.

Kolejność sprzężenia ma znaczenie, szczególnie w przypadku bardziej złożonych zapytań. Zacznij od wyselekcjonowanych sprzężenia. Ponadto Przenieś sprzężenia zwiększenia liczby wierszy po agregacji, jeśli jest to możliwe.

Do zarządzania równoległości, szczególnie w przypadku sprzężenia kartezjańskich wizualizacji, można dodać zagnieżdżone struktury obsługi okien, a być może pominąć kroki co najmniej jeden w ramach zadania Spark.

## <a name="customize-cluster-configuration"></a>Dostosowywanie konfiguracji klastra

W zależności od obciążenia klastra platformy Spark, może stwierdzisz, że niedomyślna Konfiguracja Spark spowodowałoby więcej zoptymalizowane pod kątem wykonywania zadań platformy Spark.  Wykonywanie testów porównawczych, testowanie za pomocą przykładowych obciążeń do sprawdzania poprawności konfiguracji klastra innych niż domyślne.

Poniżej przedstawiono niektóre typowe parametry, które można dostosować:

* `--num-executors` Ustawia właściwe liczbie funkcji wykonawczych.
* `--executor-cores` Ustawia liczbę rdzeni dla każdej funkcji wykonawczej. Zazwyczaj powinien mieć middle-sized executors, ponieważ inne procesy zużywają niektóre dostępnej pamięci.
* `--executor-memory` Określa rozmiar pamięci dla każdego wykonawcy, który kontroluje, Rozmiar sterty w ramach platformy YARN. Należy pozostawić trochę pamięci dla wykonywania czynności.

### <a name="select-the-correct-executor-size"></a>Wybierz rozmiar poprawne funkcji wykonawczej

Podczas podejmowania decyzji o konfiguracji funkcji wykonawczej, należy wziąć pod uwagę Java wyrzucania elementów bezużytecznych (GC).

* Czynniki, aby zmniejszyć rozmiar funkcji wykonawczej:
    1. Zmniejsz rozmiar sterty poniżej 32 GB, aby zachować GC obciążenie < 10%.
    2. Zmniejsz liczbę rdzeni, aby zachować GC obciążenie < 10%.

* Czynniki, które zwiększają rozmiar funkcji wykonawczej:
    1. Ogranicz komunikację obciążenie między executors.
    2. Zmniejsz liczbę otwartych połączeń między executors (N2) w klastrach większa (> 100 executors).
    3. Zwiększ rozmiar stosu w celu uwzględnienia zadania o znacznym wykorzystaniu pamięci.
    4. Opcjonalnie: Zmniejsz obciążenie pamięci dla funkcji wykonawczej.
    5. Opcjonalnie: Zwiększ wykorzystanie i współbieżności, subskrybowanie nadmiernej ilości procesora CPU.

Ogólna zasada mówi podczas wybierania rozmiaru funkcji wykonawczej:
    
1. Rozpocznij 30 GB na wykonawcy i rozpowszechniać maszyny dostępnych rdzeni.
2. Zwiększ liczbę rdzeni wykonawca w przypadku większych klastrów (> 100 executors).
3. Zwiększanie lub zmniejszanie rozmiary oparte zarówno na przebiegi wersji próbnej i poprzednim czynników, takich jak obciążenie GC.

Podczas uruchamiania zapytań jednoczesnych, należy wziąć pod uwagę następujące informacje:

1. Zacznij od 30 GB dla funkcji wykonawczej i wszystkich rdzeni maszyny.
2. Tworzenie aplikacji Spark równolegle wielu przez subskrybowanie nadmiernej ilości procesora CPU (około poprawy opóźnienie 30%).
3. Dystrybuować zapytania równolegle aplikacji MPI.
4. Zwiększanie lub zmniejszanie rozmiary oparte zarówno na przebiegi wersji próbnej i poprzednim czynników, takich jak obciążenie GC.

Monitorować wydajność zapytań, elementy odstające lub inne problemy z wydajnością analizując widok osi czasu, wykres SQL, statystyki zadania i tak dalej. Czasami jedno lub kilka executors są wolniej niż inne, a zadania trwa znacznie dłużej, do wykonania. Często zdarza się to w klastrach większa (> 30 węzłów). W tym przypadku podzielić pracę większej liczby zadań dzięki kompensuje harmonogram zadań powolne. Na przykład mieć co najmniej dwa razy więcej zadań, jak liczba rdzeni wykonywania w aplikacji. Można również włączyć związanego z wykonywaniem spekulatywnym zadań o `conf: spark.speculation = true`.

## <a name="optimize-job-execution"></a>Optymalizowanie wykonywania zadania

* Buforowanie, gdy jest to konieczne, na przykład możesz użyć danych dwa razy, a następnie buforowanie.
* Zmienne można rozgłaszać do wszystkich executors. Zmienne są serializowane tylko, gdy skutkuje szybszy wyszukiwań.
* Sterownik, co powoduje szybsze działanie w przypadku wielu zadań, należy użyć puli wątków.

Monitorowanie zadań uruchomionych regularne wyszukiwanie problemów z wydajnością. Aby uzyskać lepszy wgląd w niektórych problemów, należy rozważyć użycie jednej z następujących wydajności narzędzi profilowania:

* [Narzędzie PAL Intel](https://github.com/intel-hadoop/PAT) monitoruje procesora CPU, pamięci masowej i wykorzystanie przepustowości sieci.
* [Oracle Java 8 centrum sterowania](http://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) profilów platformy Spark i kodu funkcji wykonawczej.

Kluczem do wydajności zapytań 2.x Spark jest aparat Wolfram, który jest zależna od generowania kodu całości etapu. W niektórych przypadkach mogą być wyłączone generowanie kodu w całości etapu. Na przykład, jeśli używasz typu niemodyfikowalnej (`string`) w wyrażeniu agregacji `SortAggregate` pojawia się zamiast `HashAggregate`. Na przykład lepszą wydajność, spróbuj wykonać następujące czynności, a następnie ponownie Włącz generowanie kodu:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Kolejne kroki

* [Debugowanie zadania Spark działające w usłudze Azure HDInsight](apache-spark-job-debugging.md)
* [Zarządzanie zasobami klastra Spark na HDInsight](apache-spark-resource-manager.md)
* [Użyj interfejsu API REST platformy Spark, aby przesłać zdalnej obsługi zadań do klastra Spark](apache-spark-livy-rest-interface.md)
* [Dostrajanie platformy Spark](https://spark.apache.org/docs/latest/tuning.html)
* [Jak faktycznie dostrajania platformy Spark zadania tak działają](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [Serializacja Kryo](https://github.com/EsotericSoftware/kryo)
