---
title: Tworzenie funkcji danych klastra platformy Hadoop za pomocą zapytań Hive | Dokumentacja firmy Microsoft
description: Przykłady zapytań Hive, które generują funkcje w danych przechowywanych w klastrze usługi Azure HDInsight Hadoop.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: e8a94c71-979b-4707-b8fd-85b47d309a30
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: bradsev
ms.openlocfilehash: f49eeee2dd26d54674b4619e6c986952718caa47
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Tworzenie funkcji danych klastra usługi Hadoop przy użyciu zapytań programu Hive
Ten dokument przedstawia sposób tworzenia funkcji danych przechowywanych w klastrze usługi Azure HDInsight Hadoop za pomocą zapytań Hive. Te zapytania Hive funkcji osadzonych Hive User-Defined (UDF), skryptów, dla której są dostarczane.

Operacje potrzebne do utworzenia funkcje mogą być pamięci. Wydajność zapytań programu Hive staje się ważniejsze w takich przypadkach i można zwiększyć przez dostrajanie określonych parametrów. Dostrajanie parametrów została szczegółowo opisana w sekcji końcowego.

Przykłady zapytań, które są przedstawiane są specyficzne dla [danych podróży taksówki NYC](http://chriswhong.com/open-data/foil_nyc_taxi/) scenariuszy znajdują się również w [repozytorium GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Te zapytania już mieć określony schemat danych i jest gotowe do przesłania do uruchomienia. W końcowej sekcji omówiono również parametry, które użytkownicy można dostosować tak, aby ulepszyć wydajność zapytań programu Hive.

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

To **menu** linki do tematów opisujących sposób tworzenia funkcji dla danych w różnych środowiskach. To zadanie jest krokiem w [zespołu danych nauki procesu (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że masz:

* Utworzone konto magazynu platformy Azure. Aby uzyskać instrukcje, zobacz [Tworzenie konta usługi Azure Storage](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* Zainicjowano obsługę administracyjną dostosowane klastra usługi Hadoop w usłudze HDInsight.  Aby uzyskać instrukcje, zobacz [dostosować Azure klastrów usługi HDInsight Hadoop zaawansowane analizy](customize-hadoop-cluster.md).
* Dane przekazane do tabele programu Hive w klastrach usługi Azure HDInsight Hadoop. Jeśli nie, wykonaj [tworzenie i ładowanie danych do tabel Hive](move-hive-tables.md) najpierw przekazywać dane do tabele programu Hive.
* Włączyć dostęp zdalny do klastra. Aby uzyskać instrukcje, zobacz [dostęp węzła Head klastra usługi Hadoop](customize-hadoop-cluster.md).

## <a name="hive-featureengineering"></a>Funkcja generowania
W tej sekcji opisano kilka przykładów sposoby, w którym można generowania funkcji za pomocą zapytań Hive. Po wygenerowaniu dodatkowe funkcje, możesz dodać je jako kolumny do istniejącej tabeli lub Utwórz nową tabelę z dodatkowych funkcji i klucza podstawowego, który następnie może być połączony z oryginalnej tabeli. Poniżej przedstawiono w przykładach przedstawionych:

1. [Generowanie na podstawie częstotliwości funkcji](#hive-frequencyfeature)
2. [Ryzyko podzielone na kategorie zmiennych w klasyfikacji binarnej](#hive-riskfeature)
3. [Wyodrębnij funkcji z pola daty/godziny](#hive-datefeatures)
4. [Wyodrębnij funkcji z pola tekstowego.](#hive-textfeatures)
5. [Oblicz odległość między współrzędne GPS](#hive-gpsdistance)

### <a name="hive-frequencyfeature"></a>Generowanie na podstawie częstotliwości funkcji
Często jest przydatne do obliczania częstotliwości poziomów podzielone na kategorie zmiennej lub częstotliwości niektórych kombinacji poziomy wiele zmiennych podzielone na kategorie. Użytkownicy mogą używać poniższy skrypt, aby obliczyć częstotliwości te:

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


### <a name="hive-riskfeature"></a>Ryzyko podzielone na kategorie zmiennych w klasyfikacji binarnej
W klasyfikacji binarnej zmienne podzielone na kategorie nieliczbowy musi konwertowane na funkcje numeryczne, po modelach tylko używane mieć funkcje numeryczne. Ta konwersja odbywa się przez zastąpienie każdy poziom nieliczbowy liczbowych ryzyka. W tej sekcji przedstawiono pewne ogólne zapytań programu Hive obliczające wartości ryzyka (dziennika prawdopodobieństwo) podzielone na kategorie zmiennej.

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

W tym przykładzie zmienne `smooth_param1` i `smooth_param2` ustawioną smooth wartości ryzyka obliczana na podstawie danych. Ryzyko ma zakresie -Inf Inf. Ryzyko > 0 wskazuje, że prawdopodobieństwo, że element docelowy jest równa 1, jest większa niż 0,5.

Po ryzyko jest obliczana tabeli, użytkownicy mogą przypisywać wartości ryzyka do tabeli przez dołączenie go do tabeli ryzyka. Zapytanie łącząca Hive została dostarczona w poprzedniej sekcji.

### <a name="hive-datefeatures"></a>Wyodrębnij funkcji z pól daty i godziny
Gałąź jest dostarczany z zestawem funkcji UDF przetwarzania pól daty i godziny. W gałęzi, domyślny format daty/godziny jest "RRRR MM-dd 00:00:00" ("1970-01-01-12:21:32" na przykład). W tej sekcji przedstawiono przykłady, które wyodrębnianie dnia miesiąca, miesiąc z polem datetime i inne przykłady, które konwertowanie ciągu daty i godziny w formacie innym niż domyślny format ciąg daty i godziny w domyślnym formacie.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

To zapytanie Hive przy założeniu, że *<datetime field>* jest w formacie daty/godziny domyślne.

Pole daty i godziny nie ma domyślnego formatu, należy najpierw przekonwertuj pole daty/godziny na sygnatury czasowej systemu Unix, a następnie wykonać konwersję sygnaturę czasową Unix ciąg daty i godziny w formacie domyślne. W przypadku datę i godzinę w domyślnym formacie, użytkownicy mogą stosować osadzonych funkcji UDF, aby wyodrębnić funkcji daty/godziny.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

W tym zapytaniu Jeśli *<datetime field>* ma wzorzec, takich jak *2015-03-26 12:04:39*,  *<pattern of the datetime field>"* powinien być `'MM/dd/yyyy HH:mm:ss'`. Aby ją przetestować, użytkownicy mogą uruchamiać

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

*Hivesampletable* w tym zapytaniu preinstalowane na wszystkich klastrach Azure HDInsight Hadoop domyślnie podczas przydzielania klastrów.

### <a name="hive-textfeatures"></a>Wyodrębnij funkcji z pola tekstowe
Jeśli w tabeli Hive pola tekstowego, który zawiera ciąg słowa, które są rozdzielone spacjami, następujące zapytanie wyodrębnia długość ciągu i liczbę słów w ciągu.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="hive-gpsdistance"></a>Obliczanie odległości między zestawami współrzędne GPS
Zapytanie podane w tej sekcji można bezpośrednio odnosić się do danych podróży taksówki NYC. To zapytanie ma na celu pokazują, jak zastosować osadzonych funkcji matematycznych w gałęzi do generowania funkcji.

Pola, które są używane w tym zapytaniu są współrzędne GPS odbiór i dropoff lokalizacji o nazwie *podnoszenia\_geograficzne*, *podnoszenia\_szerokości geograficznej*,  *dropoff\_geograficzne*, i *dropoff\_szerokości geograficznej*. Zapytania obliczające bezpośrednie odległość między współrzędne odbiór i dropoff są:

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

Równania matematyczne, które obliczyć odległość między dwoma współrzędne GPS można znaleźć w <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">skryptów typu ruchome</a> lokacji utworzone przez Lapisu Peterowi. W języku Javascript, ta funkcja `toRad()` jest po prostu *lat_or_lon*pi/180 *, który Konwertuje stopnie na radiany. W tym miejscu *lat_or_lon* jest zakres lub długość geograficzną. Ponieważ gałąź nie zawiera funkcji `atan2`, ale udostępnia funkcję `atan`, `atan2` funkcji jest implementowany przez `atan` funkcji w powyższym zapytaniu gałęzi przy użyciu definicji w <a href="http://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![Utwórz obszar roboczy](./media/create-features-hive/atan2new.png)

Pełną listę gałęzi funkcji UDF embedded znajdują się w **wbudowanych funkcji** sekcji na <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive wiki</a>).  

## <a name="tuning"></a> Tematy zaawansowane: parametry strojenia gałęzi do zwiększenia szybkości zapytania
Domyślne ustawienia parametru gałęzi klastra może nie być odpowiednie dla zapytań Hive i dane, które są przetwarzania zapytania. W tej sekcji omówiono niektóre parametry, które użytkownicy można dostosować w celu poprawy wydajności zapytań programu Hive. Użytkownicy muszą dodać parametr strojenia kwerendy przed zapytania przetwarzania danych.

1. **Miejsce na stercie Java**: dla zapytań obejmujących dołączenie dużych zestawów danych lub przetwarzania rekordów długi **kończy się wolne miejsce na stercie** jest jednym z typowych błędów. Tego błędu można uniknąć przez ustawienie parametrów *mapreduce.map.java.opts* i *mapreduce.task.io.sort.mb* do żądanej wartości. Oto przykład:
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Ten parametr przydziela 4GB pamięci, aby miejsce na stercie Java i powoduje sortowanie efektywniejsze przez przydzielanie większej ilości pamięci. Należy dobrze gry z tych przydziałów, jeśli istnieją wszystkie zadania błędy związane z miejsca na stercie.

1. **Rozmiaru bloku systemu plików DFS**: ten parametr określa najmniejsza jednostka danych przechowywanych w systemie plików. Na przykład jeśli rozmiar bloku systemu plików DFS jest 128 MB, a następnie żadnych danych o rozmiarze mniejsza i maksymalnie 128 MB są przechowywane w jeden blok. Przydzielony dodatkowe bloki danych, który jest większy niż 128 MB. 
2. Wybieranie mały rozmiar bloku powoduje duże koszty Hadoop, ponieważ nazwa węzła ma przetwarzać wiele żądań więcej można znaleźć odpowiedniego bloku odnoszące się do pliku. Zalecane ustawienia po dotyczących gigabajty (lub więcej) danych jest:

        set dfs.block.size=128m;

2. **Optymalizacja operacji tworzenia sprzężenia w gałęzi**: podczas operacji łączenia w ramach mapy/Zmniejsz zazwyczaj miejsce w fazie Zmniejsz czasami znaczne zyski można osiągnąć poprzez zaplanowanie sprzężenia w fazie mapy (zwane również "mapjoins"). Aby skierować gałąź, aby to zrobić, jeśli to możliwe, należy ustawić:
   
       set hive.auto.convert.join=true;

3. **Określanie liczby mapowań do gałęzi**: podczas Hadoop zezwala użytkownikowi na określenie liczby reduktory, liczba mapowań jest zwykle nie jest ustawiony przez użytkownika. Lewy, umożliwiający pewien stopień kontroli tego numeru jest wybranie zmienne Hadoop *mapred.min.split.size* i *mapred.max.split.size* jako rozmiar map zadań zależy od:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    Zwykle wartość domyślna:
    
    - *mapred.min.split.SIZE* ma wartość 0, który z
    - *mapred.max.split.SIZE* jest **Long.MAX** i 
    - *DFS.Block.SIZE* to 64 MB.

    Jak możemy stwierdzić, podany rozmiar danych dostrajanie parametrów "ustawienia" ich pozwala nam dostosować liczbę mapowań używane.

4. Poniżej przedstawiono kilka innych kolejnych **zaawansowane opcje** optymalizacji wydajności Hive. Te umożliwiają skonfigurowanie pamięć przydzielona do mapowania i zmniejszyć zadań i mogą być przydatne w Dostosowywanie wydajności. Należy pamiętać, że *mapreduce.reduce.memory.mb* nie może być większa niż rozmiar pamięci fizycznej w każdym węźle procesu roboczego klastra usługi Hadoop.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

