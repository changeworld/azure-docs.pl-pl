---
title: Optymalizacja wydajności - Azure HDInsight Spark zadania | Dokumentacja firmy Microsoft
description: Pokazuje typowe strategie, aby uzyskać najlepszą wydajność klastry Spark.
services: hdinsight
documentationcenter: ''
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: maxluk
ms.openlocfilehash: 381f9ef2dac2c1dfdada32a917626b17c5969a98
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="optimize-spark-jobs"></a>Optymalizowanie zadań platformy Spark

Dowiedz się, jak zoptymalizować konfigurację klastra Spark dla określonego obciążenia.  Najbardziej typowe wyzwaniem jest wykorzystania pamięci z powodu nieprawidłowej konfiguracji (szczególnie błąd o rozmiarze z modułów wykonujących) długotrwałe operacje i zadania, które powodują powstanie kartezjańskimi operacji. Można przyspieszyć zadania z buforowaniem odpowiednie, a w celu umożliwienia [zegara danych](#optimize-joins-and-shuffles). Aby uzyskać najlepszą wydajność monitorowanie i badanie wykonania zadania Spark długotrwałe i korzystanie z zasobów.

W poniższych sekcjach opisano typowe Spark zadania optymalizacji i zalecenia.

## <a name="choose-the-data-abstraction"></a>Wybierz pozyskiwania danych

Platforma Spark jest 1.x używa RDDs abstrakcyjny danych, a następnie Spark 2.x wprowadzone DataFrames i zestawy danych. Należy wziąć pod uwagę następujące względnych zalet:

* **DataFrames**
    * Najlepszym rozwiązaniem w większości sytuacji
    * Zapewnia optymalizację zapytania za pomocą Catalyst
    * Generowanie kodu w całości etap
    * Bezpośredni dostęp do pamięci
    * Narzut mało pamięci (GC)
    * Nie jako przyjazny dla dewelopera jako zestaw danych, ponieważ nie ma żadnych kontroli w czasie kompilacji lub programowania obiektu domeny
* **Zestawy danych**
    * Dobra w złożonych potoki ETL, których wpływ na wydajność jest dopuszczalne
    * Nie są odpowiednie w agregacji, gdzie może być znaczny wpływ na wydajność
    * Zapewnia optymalizację zapytania za pomocą Catalyst
    * Przyjazny dla dewelopera zapewniając obiektu domeny kontroli programowania i kompilacji
    * Zwiększa serializacji/deserializacji obciążenia
    * Wysokie obciążenie GC
    * Dzieli generowanie kodu w całości etap
* **RDDs**
    * W łączniku Spark 2.x, nie trzeba używać RDDs, chyba że potrzebne do tworzenia nowych RDD niestandardowych
    * Brak optymalizacji zapytania za pomocą Catalyst
    * Generowanie kodu w całości etap nie
    * Wysokie obciążenie GC
    * Należy użyć starszego 1.x Spark interfejsów API

## <a name="use-optimal-data-format"></a>Użyj formatu optymalne danych

Platforma Spark obsługuje wiele formatów, takich jak csv, json, xml, parquet, orc i avro. Spark może zostać rozszerzony do obsługi wielu formatów więcej z zewnętrznymi źródłami danych — Aby uzyskać więcej informacji, zobacz [Spark pakiety](https://spark-packages.org).

Format najlepszy wydajności jest parquet z *szałowe kompresji*, co jest ustawieniem domyślnym w łączniku Spark 2.x. Parquet przechowuje dane w formacie kolumnowym i stopniu zoptymalizowany w łączniku Spark.

## <a name="select-default-storage"></a>Wybierz domyślną magazynu

Podczas tworzenia nowego klastra Spark, użytkownik może wybrać magazyn obiektów Blob Azure lub usługi Azure Data Lake Store jako magazyn domyślnej sieci klastra. Obie te opcje umożliwiają korzyści z magazynu długoterminowego przejściowej klastrów, więc dane nie są automatycznie usuwane po usunięciu klastra. Można ponownie utworzyć klaster przejściowych i nadal uzyskiwać dostęp do danych.

| Typ magazynu | System plików | Szybkość | Przejściowy | Przypadki użycia |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:**//url/ | **Standardowa** | Yes | Przejściowa klastra |
| Azure Data Lake Store | **ADL:**//url/ | **Szybciej** | Yes | Przejściowa klastra |
| HDFS lokalne | **hdfs:**//url/ | **Najszybszy** | Nie | Interakcyjne klastra 24/7 |

## <a name="use-the-cache"></a>Użycie pamięci podręcznej

Spark udostępnia własny natywnego buforowania mechanizmy, które mogą być używane przez różne metody takie jak `.persist()`, `.cache()`, i `CACHE TABLE`. To buforowanie natywnego obowiązuje z małych zestawów danych oraz jak potoki ETL konieczne wyników pośrednich w pamięci podręcznej. Jednak Spark natywnego buforowanie aktualnie nie działają prawidłowo w partycje, ponieważ tabela pamięci podręcznej nie zachowuje partycjonowania danych. Bardziej ogólne i niezawodna metoda buforowania *buforowanie warstwy magazynu*.

* Natywny Spark buforowania (niezalecane)
    * Dobra dla małych zestawów danych.
    * Działa nie z partycje, które mogą ulec zmianie w przyszłych wersjach platformy Spark.

* Magazyn poziomu buforowania (zalecane)
    * Można zaimplementować przy użyciu [Alluxio](http://www.alluxio.org/).
    * Użycie w pamięci i buforowanie dysków SSD.

* Lokalny system plików HDFS (zalecane)
    * `hdfs://mycluster` Ścieżka.
    * Używa dysków SSD buforowania.
    * Buforowane dane zostaną utracone po usunięciu klastra, wymagających Odbuduj pamięci podręcznej.

## <a name="use-memory-efficiently"></a>Efektywnie korzystać z pamięci

Platforma Spark działa przez umieszczenie danych w pamięci, dlatego zarządzanie zasobami pamięci jest kluczowym aspektem optymalizacji wykonywanie zadań Spark.  Istnieje kilka technik, które można zastosować do efektywnie korzystać z pamięci klastra.

* Preferowane jest mniejsze partycje danych i uwzględnić rozmiar danych, typy i dystrybucji w strategii partycjonowania.
* Należy wziąć pod uwagę nowszą, bardziej wydajne [Kryo szeregowanie danych](https://github.com/EsotericSoftware/kryo), zamiast domyślnej serializacji Java.
* Preferowane jest przy użyciu YARN, ponieważ umożliwia oddzielenie od `spark-submit` przez partię.
* Monitorowania i dostrajania ustawień konfiguracji platformy Spark.

Użytkownikowi strukturę pamięci platforma Spark i niektóre parametry pamięci klucza Moduł wykonujący są wyświetlane w następnym obrazu.

### <a name="spark-memory-considerations"></a>Uwagi dotyczące pamięci Spark

Jeśli używasz YARN YARN steruje maksymalną suma pamięci używanej przez wszystkie kontenery w każdym węźle Spark.  Na poniższym diagramie przedstawiono klucza obiektów i relacji między nimi.

![Zarządzanie pamięcią Spark YARN](./media/apache-spark-perf/yarn-spark-memory.png)

Adres "Brak pamięci" wiadomości, spróbuj:

* Przejrzyj przesuwa zarządzania grupy DAG. Zmniejsz przez reducting po stronie mapy, wstępnie partycji (lub bucketize) źródła danych, zmaksymalizować pojedynczego przesuwa i zmniejszyć ilość danych przesyłanych.
* Preferowane jest `ReduceByKey` z jej limitu pamięci stały się `GroupByKey`, które obejmują agregacji, okien i inne funkcje, ale ma limit pamięci niepowiązany pods.
* Preferowane jest `TreeReduce`, więcej pracy nad modułów lub partycji, która działa do `Reduce`, który wykonuje wszystkie pracę od sterownika.
* Korzystaj z DataFrames, a nie obiekty RDD niższego poziomu.
* Utwórz complexType, które hermetyzują akcji, takich jak "Pierwszych N", agregacje różnych lub operacje okien.

## <a name="optimize-data-serialization"></a>Optymalizacja serializacja danych

Zadania Spark są rozpowszechniane, więc odpowiednie dane serializacji jest ważne, aby uzyskać najlepszą wydajność.  Dostępne są dwie opcje serializacji dla platformy Spark:

* Serializacja Java jest ustawieniem domyślnym.
* Kryo szeregowanie jest w nowszym formacie i może spowodować szybciej i spójne serializacji niż Java.  Kryo wymaga, aby zarejestrować klas w programie, i nie obsługuje jeszcze wszystkich typów możliwych do serializacji.

## <a name="use-bucketing"></a>Użyj bucketing

Bucketing jest podobny do partycjonowania danych, ale każdego zasobnika może przechowywać, a nie tylko jeden zestaw wartości w kolumnie. Bucketing który dobrze partycjonowania na dużą (w milionach lub więcej) liczbę wartości, takich jak identyfikatory produktu. Zasobnik zależy od tworzenia skrótu klucza zasobnika wiersza. Tabele zasobnikach oferują unikatowe optymalizacje ponieważ przechowują metadane dotyczące sposobu zostały zasobnikach i sortowane.

Niektóre zaawansowane funkcje bucketing są:

* Zapytanie optymalizacji na podstawie bucketing meta informacji
* Zoptymalizowane agregacji
* Zoptymalizowane sprzężeń

Można użyć partycjonowania i bucketing w tym samym czasie.

## <a name="optimize-joins-and-shuffles"></a>Optymalizacja sprzężenia i przesuwa

Jeśli masz powolne zadania na Join lub losowa przyczyną jest prawdopodobnie *zegara danych*, która jest asymetrii danych zadania. Na przykład zadanie mapy może potrwać 20 sekund, ale uruchomienie zadania, których dane są przyłączone do lub przemieszane zajmuje godzin.   Aby naprawić pochylenia danych, należy soli cały klucz, lub użyj *izolowane soli* dla niektórych podzbiór kluczy.  Jeśli używane są izolowane salt, należy dokładniej przefiltrować do izolowania podzbiór solone kluczy w sprzężeniach mapy. Inną możliwością jest wprowadzenie zasobnik kolumny i wstępnie najpierw agregacji w zasobników.

Innym czynnikiem powodujące powolne sprzężenia może być typ sprzężenia. Domyślnie platforma Spark jest używa `SortMerge` typ sprzężenia. Tego typu sprzężenia jest najbardziej odpowiednie dla dużych zestawów danych, ale w przeciwnym razie jest praktyce kosztowne, ponieważ jego musi najpierw posortować lewej i prawej stronie danych przed scaleniem je.

A `Broadcast` sprzężenia jest najodpowiedniejsza dla mniejszych zestawów danych lub gdy jeden strony sprzężenia jest znacznie mniejszy niż druga strona. Tego typu sprzężenia emituje jednej stronie do wszystkich modułów i dlatego na ogół wymaga większej ilości pamięci w emisji.

W tej konfiguracji można zmienić typ sprzężenia, ustawiając `spark.sql.autoBroadcastJoinThreshold`, lub możesz ustawić wskazówki sprzężenia, za pomocą interfejsów API DataFrame (`dataframe.join(broadcast(df2))`).

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

Jeśli używasz zasobnikach tabel, a następnie masz innego typu, dołącz `Merge` sprzężenia. Poprawnie wstępnie podzielonym na partycje i wstępnie posortowana zestawu danych będzie pomijana jest faza kosztowne sortowania z `SortMerge` sprzężenia.

Kolejność sprzężenia jest ważne, szczególnie w przypadku bardziej złożonych zapytań. Rozpocznij od wyselekcjonowanych sprzężenia. Ponadto Przenieś sprzężenia, które zwiększenie liczby wierszy po agregacji, gdy jest to możliwe.

Do zarządzania równoległości, szczególnie w przypadku kartezjańskimi sprzężenia, można dodać struktury zagnieżdżone okien i może pominąć kroki co najmniej jednego zadania Spark.

## <a name="customize-cluster-configuration"></a>Dostosowywanie konfiguracji klastra

W zależności od obciążenia klastra Spark, może okaże się, że niedomyślna Konfiguracja Spark spowodowałoby więcej zoptymalizowanych pod kątem wykonywania zadań Spark.  Wykonywanie testów porównawczych testowanie za pomocą obciążeń próbki do sprawdzania poprawności konfiguracji klastra z systemem innym niż domyślny.

Poniżej przedstawiono niektóre typowe parametry, które można dostosować:

* `--num-executors` Ustawia odpowiednią liczbę wykonawców.
* `--executor-cores` Ustawia liczbę rdzeni dla każdego wykonawcy. Zazwyczaj powinien mieć middle-sized modułów, zgodnie z innymi procesami korzystać z niektórych dostępnej pamięci.
* `--executor-memory` Ustawia rozmiar pamięci dla każdego wykonawcy, który kontroluje rozmiar sterty na PRZĘDZY. Należy pozostawić pamięci do wykonania czynności.

### <a name="select-the-correct-executor-size"></a>Wybierz rozmiar poprawne Moduł wykonujący

Podczas podejmowania decyzji o konfigurację modułu wykonującego, należy wziąć pod uwagę obciążenie Java odzyskiwanie kolekcji (GC).

* Czynników autoryzacji, aby zmniejszyć rozmiar modułu wykonującego:
    1. Zmniejsz rozmiar stosu poniżej 32 GB, aby zachować GC narzutów < 10%.
    2. Zmniejsz liczbę rdzeni, aby zachować GC narzutów < 10%.

* Czynniki zwiększyć rozmiar modułu wykonującego:
    1. Zmniejsz komunikacji narzut między modułów.
    2. Zmniejsz liczbę otwartych połączeń między modułów (N2) na większe klastry (> modułów 100).
    3. Zwiększ rozmiar stosu przyjmować wymagają dużej ilości pamięci zadania.
    4. Opcjonalnie: Zmniejszyć obciążenie pamięci na moduł wykonujący.
    5. Opcjonalnie: Zwiększenie wykorzystania i współbieżność przez subskrybowanie nadmiernej ilości procesora CPU.

Jako ogólne zasadą wybierając rozmiar modułu wykonującego:
    
1. Rozpocznij 30 GB na moduł wykonujący i rozpowszechniania rdzeni dostępne maszyny.
2. Zwiększ liczbę rdzeni Moduł wykonujący dla dużych klastrów (modułów > 100).
3. Zwiększyć lub zmniejszyć rozmiary zarówno na podstawie uruchomień wersji próbnej i na poprzednim czynników, takich jak koszty GC.

Podczas uruchamiania zapytania jednoczesne, należy rozważyć następujące kwestie:

1. Rozpocznij od 30 GB na moduł wykonujący i wszystkie rdzenie komputera.
2. Tworzenie aplikacji Spark równoległe wielu przez subskrybowanie nadmiernej ilości procesora CPU (około 30% poprawy opóźnienia).
3. Dystrybuuj zapytania w aplikacjach równoległych.
4. Zwiększyć lub zmniejszyć rozmiary zarówno na podstawie uruchomień wersji próbnej i na poprzednim czynników, takich jak koszty GC.

Monitorować wydajność zapytań, dla wartości odstających lub inne problemy z wydajnością, analizując widoku osi czasu, wykres SQL, statystyki zadania i tak dalej. Czasami co najmniej kilka modułów wolniej niż inne, i zadania trwać znacznie dłużej do wykonania. Często dzieje się tak na większe klastry (węzłów > 30). W takim przypadku najlepiej podzielić go na większą liczbę zadań tak planista kompensuje powolne zadania. Na przykład mieć co najmniej dwa razy jako wiele zadań jako liczba rdzeni Moduł wykonujący w aplikacji. Można również włączyć rozważana wykonywanie zadań z `conf: spark.speculation = true`.

## <a name="optimize-job-execution"></a>Optymalizacja wykonywania zadania

* W pamięci podręcznej jak jest to konieczne, na przykład możesz użyć danych dwa razy, a następnie pamięci podręcznej go.
* Zmienne emisji do wszystkich modułów. Zmienne tylko są serializowane raz, co powoduje szybsze wyszukiwań.
* Pula wątków w systemie sterownik, który powoduje szybsze operacji dla wielu zadań.

Monitorowanie sieci uruchomionych zadań regularnie problemy z wydajnością. Uzyskać lepszy wgląd w niektórych problemów, należy wziąć pod uwagę jedną z następujących profilowania narzędzi wydajności:

* [Narzędzie PAL Intel](https://github.com/intel-hadoop/PAT) monitoruje Procesora, pamięci masowej i wykorzystanie przepustowości sieci.
* [Oracle Java 8 Mission Control](http://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) kod Spark i wykonawcy, profile.

Kluczem do wydajność zapytań Spark 2.x jest aparat wolframu, który jest zależna od generowanie kodu w całości etap. W niektórych przypadkach mogą być wyłączone generowanie kodu w całości etapu. Na przykład, jeśli używasz niemodyfikowalnym typem (`string`) w wyrażeniu agregacji `SortAggregate` pojawia się zamiast `HashAggregate`. Na przykład w celu poprawy wydajności spróbować wykonać następujące czynności, a następnie ponownie Włącz generowanie kodu:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Kolejne kroki

* [Program Debug: polecenie Spark zadań uruchamianych w usługi HDInsight](apache-spark-job-debugging.md)
* [Zarządzanie zasobami klastra Spark w usłudze HDInsight](apache-spark-resource-manager.md)
* [Użyj interfejsu API usługi REST Spark umożliwiającą przesyłanie zadań zdalnego do klastra Spark](apache-spark-livy-rest-interface.md)
* [Dostrajanie Spark](https://spark.apache.org/docs/latest/tuning.html)
* [Jak faktycznie strojenie Spark swoje zadania tak działają](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [Serializacja Kryo](https://github.com/EsotericSoftware/kryo)
