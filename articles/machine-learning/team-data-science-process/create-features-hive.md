---
title: Tworzenie funkcji dla danych w klastrze usługi Azure HDInsight Hadoop — proces nauki o danych zespołowych
description: Przykłady zapytań hive, które generują funkcje w danych przechowywanych w klastrze usługi Azure HDInsight Hadoop.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c926aac3ea4360793ff52b616a55dc6198357c8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721782"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Tworzenie funkcji dla danych w klastrze Hadoop przy użyciu zapytań hive
W tym dokumencie pokazano, jak utworzyć funkcje dla danych przechowywanych w klastrze usługi Azure HDInsight Hadoop przy użyciu zapytań hive. Te zapytania hive używać osadzonych funkcji zdefiniowanych przez użytkownika hive (UDFs), skrypty, dla których są dostarczane.

Operacje potrzebne do tworzenia funkcji mogą być wymagające dużej ilości pamięci. Wydajność zapytań hive staje się bardziej krytyczne w takich przypadkach i można poprawić przez dostrajanie niektórych parametrów. Dostrajanie tych parametrów jest omówione w ostatniej sekcji.

Przykłady zapytań, które są prezentowane są specyficzne dla [nyc taxi trip dane](https://chriswhong.com/open-data/foil_nyc_taxi/) scenariusze są również w [repozytorium GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Te zapytania mają już określony schemat danych i są gotowe do przesłania do uruchomienia. W końcowej sekcji parametry, które użytkownicy mogą dostroić, tak aby można było poprawić wydajność zapytań hive.

To zadanie jest krokiem w [procesie nauki o danych zespołu (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz:

* Utworzono konto magazynu platformy Azure. Jeśli potrzebujesz instrukcji, zobacz [Tworzenie konta usługi Azure Storage](../../storage/common/storage-account-create.md)
* Aprowizowana dostosowana klaster Hadoop z usługą HDInsight.  Jeśli potrzebujesz instrukcji, zobacz [Dostosowywanie klastrów usługi Azure HDInsight Hadoop dla analizy zaawansowanej](customize-hadoop-cluster.md).
* Dane zostały przekazane do tabel hive w klastrach usługi Azure HDInsight Hadoop. Jeśli tak nie jest, wykonaj tworzenie [i ładowanie danych do tabel hive,](move-hive-tables.md) aby najpierw przekazać dane do tabel hive.
* Włączony zdalny dostęp do klastra. Jeśli potrzebujesz instrukcji, zobacz [Dostęp do węzła głównego klastra Hadoop](customize-hadoop-cluster.md).

## <a name="feature-generation"></a><a name="hive-featureengineering"></a>Generowanie funkcji
W tej sekcji opisano kilka przykładów sposobów generowania funkcji przy użyciu zapytań hive. Po wygenerowaniu dodatkowych funkcji można dodać je jako kolumny do istniejącej tabeli lub utworzyć nową tabelę z dodatkowymi funkcjami i kluczem podstawowym, które następnie można połączyć z oryginalną tabelą. Oto przedstawione przykłady:

1. [Generowanie funkcji opartych na częstotliwościach](#hive-frequencyfeature)
2. [Ryzyko zmiennych kategorycznych w klasyfikacji binarnej](#hive-riskfeature)
3. [Wyodrębnianie funkcji z pola Datetime](#hive-datefeatures)
4. [Wyodrębnianie funkcji z pola tekstowego](#hive-textfeatures)
5. [Obliczanie odległości między współrzędnymi GPS](#hive-gpsdistance)

### <a name="frequency-based-feature-generation"></a><a name="hive-frequencyfeature"></a>Generowanie funkcji opartych na częstotliwościach
Często jest to przydatne do obliczania częstotliwości poziomów zmiennej kategorycznej lub częstotliwości niektórych kombinacji poziomów z wielu zmiennych kategorii. Użytkownicy mogą używać następującego skryptu do obliczania tych częstotliwości:

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


### <a name="risks-of-categorical-variables-in-binary-classification"></a><a name="hive-riskfeature"></a>Ryzyko zmiennych kategorii w klasyfikacji binarnej
W klasyfikacji binarnej nienumeryczne zmienne kategoryczne muszą być konwertowane na operacje liczbowe, gdy używane modele przyjmują tylko operacje liczbowe. Ta konwersja odbywa się poprzez zastąpienie każdego poziomu nienumerycznego ryzykiem liczbowym. W tej sekcji przedstawiono niektóre ogólne zapytania hive, które obliczają wartości ryzyka (kursy dziennika) zmiennej kategorycznej.

        set smooth_param1=1;
        set smooth_param2=20;
        select
            <column_name1>,<column_name2>,
            ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
        from
            (
            select
                <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
            from
                (
                select
                    <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
                from <databasename>.<tablename>
                )a
            group by <column_name1>, <column_name2>
            )b

W tym przykładzie `smooth_param1` `smooth_param2` zmienne i są ustawione na wygładzanie wartości ryzyka obliczonych na podstawie danych. Ryzyko ma zakres między -Inf i Inf. Ryzyko > 0 wskazuje, że prawdopodobieństwo, że cel jest równy 1 jest większa niż 0,5.

Po obliczeniu tabeli ryzyka użytkownicy mogą przypisać wartości ryzyka do tabeli, łącząc ją z tabelą ryzyka. Zapytanie o łączenie gałęzi zostało dostarczone w poprzedniej sekcji.

### <a name="extract-features-from-datetime-fields"></a><a name="hive-datefeatures"></a>Wyodrębnianie obiektów z pól datetime
Hive jest dostarczany z zestawem plików UDF do przetwarzania pól datetime. W hive domyślny format datetime to 'yyyy-MM-dd 00:00:00' ('1970-01-01 12:21:32'). W tej sekcji przedstawiono przykłady, które wyodrębniają dzień miesiąca, miesiąc z pola datetime i inne przykłady, które konwertowane ciąg datetime w formacie innym niż domyślny do ciągu datetime w formacie domyślnym.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Ta kwerenda hive zakłada, że * \<pole datetime>* jest w domyślnym formacie datetime.

Jeśli pole datetime nie jest w formacie domyślnym, należy najpierw przekonwertować pole datetime na sygnaturę czasową uniksu, a następnie przekonwertować sygnaturę czasową Uniksa na ciąg datetime w formacie domyślnym. Gdy datetime jest w formacie domyślnym, użytkownicy mogą zastosować osadzone pliki UEDF datatime, aby wyodrębnić funkcje.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

W tej kwerendzie, jeśli * \<pole datetime>* ma wzorzec jak *26.03.2015 12:04:39,* * \<wzór pola datetime>"* powinien być `'MM/dd/yyyy HH:mm:ss'`. Aby go przetestować, użytkownicy mogą

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

*Ulewiowa* w tej kwerendzie jest preinstalowana na wszystkich klastrach usługi Azure HDInsight Hadoop domyślnie, gdy klastry są aprowizacji.

### <a name="extract-features-from-text-fields"></a><a name="hive-textfeatures"></a>Wyodrębnianie obiektów z pól tekstowych
Gdy tabela Hive zawiera pole tekstowe, które zawiera ciąg wyrazów, które są rozdzielane spacjami, następująca kwerenda wyodrębnia długość ciągu i liczbę wyrazów w ciągu.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="calculate-distances-between-sets-of-gps-coordinates"></a><a name="hive-gpsdistance"></a>Obliczanie odległości między zestawami współrzędnych GPS
Kwerenda podana w tej sekcji może być bezpośrednio stosowana do danych podróży taksówką NYC. Celem tej kwerendy jest pokazanie, jak zastosować osadzoną funkcję matematyczną w gałęzi do generowania funkcji.

Pola, które są używane w tej kwerendzie są współrzędne GPS lokalizacji odbioru i dropoff, o nazwie *długość\_odbioru,* *szerokość odbioru,\_* długość do *krojenia kropla\_w*wysokości i *\_szerokość nadsyłania.* Kwerendy obliczające bezpośrednią odległość między współrzędnymi odbioru i nadania są następujące:

        set R=3959;
        set pi=radians(180);
        select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
            ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
            *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
            *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
            /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
            +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
            pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
        from nyctaxi.trip
        where pickup_longitude between -90 and 0
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and 0
        and dropoff_latitude between 30 and 90
        limit 10;

Równania matematyczne, które obliczają odległość między dwoma współrzędnymi GPS można znaleźć na stronie <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Movable Type Scripts,</a> autorstwa Petera Lapisu. W tym Javascript, `toRad()` funkcja jest po prostu *lat_or_lon*pi/180, który konwertuje stopnie na radiany. Tutaj *lat_or_lon* jest szerokość geograficzna lub długość geograficzna. Ponieważ Hive nie zapewnia `atan2`funkcji, ale `atan`zapewnia `atan2` funkcję, funkcja `atan` jest implementowana przez funkcję w powyższym zapytaniu Hive przy użyciu definicji podanej w <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedii.</a>

![Tworzenie obszaru roboczego](./media/create-features-hive/atan2new.png)

Pełną listę osadzonych plików UDF hive można znaleźć w sekcji **Wbudowane funkcje** na <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">wiki Apache Hive</a>).  

## <a name="advanced-topics-tune-hive-parameters-to-improve-query-speed"></a><a name="tuning"></a>Tematy zaawansowane: dostrajanie parametrów gałęzi w celu zwiększenia szybkości kwerendy
Domyślne ustawienia parametrów klastra hive mogą nie być odpowiednie dla zapytań hive i danych, które są przetwarzane przez kwerendy. W tej sekcji omówiono niektóre parametry, które użytkownicy mogą dostroić, aby zwiększyć wydajność zapytań hive. Użytkownicy muszą dodać zapytania dostrajania parametrów przed zapytaniami o przetwarzanie danych.

1. **Miejsce na stercie Java:** W przypadku kwerend obejmujących łączenie dużych zestawów danych lub przetwarzanie długich rekordów **wyczerpanie miejsca na stercie** jest jednym z typowych błędów. Tego błędu można uniknąć, ustawiając parametry *mapreduce.map.java.opts* i *mapreduce.task.io.sort.mb* na żądane wartości. Oto przykład:
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Ten parametr przydziela pamięć 4 GB do miejsca na stercie Java, a także sprawia, że sortowanie jest bardziej wydajne, przydzielając dla niej więcej pamięci. Jest to dobry pomysł, aby grać z tych alokacji, jeśli istnieją błędy niepowodzenia zadania związane z miejsca na stercie.

1. **Rozmiar bloku systemu plików DFS:** Ten parametr ustawia najmniejszą jednostkę danych, jaką przechowuje system plików. Na przykład jeśli rozmiar bloku systemu plików DFS wynosi 128 MB, wszystkie dane o rozmiarze mniejszym niż i maksymalnie 128 MB są przechowywane w jednym bloku. Dane, które są większe niż 128 MB jest przydzielone dodatkowe bloki. 
2. Wybranie małego rozmiaru bloku powoduje duże obciążenie w Hadoop, ponieważ węzeł nazwy musi przetwarzać o wiele więcej żądań, aby znaleźć odpowiedni blok odnoszący się do pliku. Zalecane ustawienie w przypadku danych gigabajtów (lub większych) jest:

        set dfs.block.size=128m;

2. **Optymalizacja operacji sprzężenia w Hive:** Podczas gdy operacje sprzężenia w ramach mapy/redukcji zazwyczaj odbywają się w fazie redukcji, czasami ogromne zyski można osiągnąć, planując sprzężenia w fazie mapy (nazywane również "mapjoins"). Ustaw tę opcję:
   
       set hive.auto.convert.join=true;

3. **Określając liczbę maperów do hive:** Podczas Hadoop pozwala użytkownikowi ustawić liczbę reduktorów, liczba maperów zazwyczaj nie jest ustawiana przez użytkownika. Sztuczka, która pozwala na pewien stopień kontroli na tej liczbie jest wybór zmiennych Hadoop *mapred.min.split.size* i *mapred.max.split.size* jako rozmiar każdego zadania mapy zależy od:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    Zazwyczaj domyślna wartość:
    
   - *mapred.min.split.size* wynosi 0,
   - *mapred.max.split.size* to **Long.MAX,** a 
   - *dfs.block.size* ma 64 MB.

     Jak widać, biorąc pod uwagę rozmiar danych, dostrajanie tych parametrów przez "ustawienie" pozwala nam dostroić liczbę używanych maperów.

4. Oto kilka innych bardziej **zaawansowanych opcji** optymalizacji wydajności hive. Te opcje umożliwiają ustawienie pamięci przydzielonej do mapowania i zmniejszenia liczby zadań, a także mogą być przydatne w dostosowywaniu wydajności. Należy pamiętać, że *mapreduce.reduce.memory.mb* nie może być większy niż rozmiar pamięci fizycznej każdego węzła procesu roboczego w klastrze Hadoop.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

