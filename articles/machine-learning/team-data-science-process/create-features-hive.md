---
title: Tworzenie funkcji dla danych w klastrze usługi Hadoop — zespołu danych dla celów naukowych
description: Przykłady zapytań programu Hive, które generują funkcji w danych przechowywanych w klastrze usługi Azure HDInsight Hadoop.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/21/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a491f923d7755513d84adfe765d595a3a7a80715
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60399360"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Tworzenie funkcji dla danych w klastrze usługi Hadoop przy użyciu zapytań Hive
W tym dokumencie przedstawiono sposób tworzenia funkcji — dane przechowywane w klastrze usługi Azure HDInsight Hadoop przy użyciu zapytań programu Hive. Te zapytania programu Hive za pomocą osadzonych funkcji Hive User-Defined przez użytkownika (UDF), skryptów, dla której są dostarczane.

Operacje wymagane do utworzenia funkcji może być intensywnie korzystających z pamięci. Wydajność zapytań technologii Hive staje się ważniejsze w takich przypadkach i można zwiększyć przez dostrojenie określonych parametrów. Dostrajanie tych parametrów jest omówiona w sekcji końcowej.

Przykłady zapytań, które są prezentowane są specyficzne dla [danych podróży taksówek NYC](https://chriswhong.com/open-data/foil_nyc_taxi/) scenariusze są również dostępne w [repozytorium GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Te zapytania już mają określony schemat danych i gotowe do wysłania do uruchomienia. W sekcji końcowej parametry, które użytkownicy można dostrajanie, dzięki czemu można zwiększyć wydajność zapytań technologii Hive zostały również omówione.

To zadanie jest to krok w [Team Data Science naukowych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz:

* Utworzone konto magazynu platformy Azure. Aby uzyskać instrukcje, zobacz [Tworzenie konta usługi Azure Storage](../../storage/common/storage-quickstart-create-account.md)
* Zainicjowano obsługę administracyjną dostosowane klaster Hadoop w usłudze HDInsight.  Aby uzyskać instrukcje, zobacz [dostosować Azure HDInsight klastry Hadoop dla usługi Advanced Analytics](customize-hadoop-cluster.md).
* Danych został przekazany do tabel programu Hive w klastrach usługi Azure HDInsight Hadoop. Jeśli nie, postępuj zgodnie z [tworzenie i ładowanie danych do tabel programu Hive](move-hive-tables.md) najpierw przekazywania danych do tabel programu Hive.
* Włączony zdalny dostęp do klastra. Aby uzyskać instrukcje, zobacz [dostęp węzeł główny klastra Hadoop](customize-hadoop-cluster.md).

## <a name="hive-featureengineering"></a>Generowanie funkcji
W tej sekcji opisano kilka przykładów sposobów, w którym można generowania funkcji przy użyciu zapytań programu Hive. Po wygenerowaniu dodatkowe funkcje, możesz dodać je jako kolumny do istniejącej tabeli lub Utwórz nową tabelę z dodatkowych funkcji i klucza podstawowego, który następnie może być łączone z oryginalnej tabeli. Poniżej przedstawiono w przykładach przedstawionych:

1. [Generowanie na podstawie częstotliwości funkcji](#hive-frequencyfeature)
2. [Ryzyko związane z kategorii zmiennych w klasyfikacji binarnej](#hive-riskfeature)
3. [Wyodrębnianie funkcji z pola daty/godziny](#hive-datefeatures)
4. [Wyodrębnianie funkcji z pola tekstowego](#hive-textfeatures)
5. [Obliczyć odległość między współrzędne GPS](#hive-gpsdistance)

### <a name="hive-frequencyfeature"></a>Generowanie na podstawie częstotliwości funkcji
Często jest to przydatne do obliczania częstotliwości poziomów podzielonych na kategorie zmiennej lub częstotliwości niektórych kombinacji poziomów od wielu zmiennych podzielonych na kategorie. Użytkownicy, można użyć poniższego skryptu do obliczania tych częstotliwości:

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


### <a name="hive-riskfeature"></a>Ryzyko związane z kategorii zmiennych w klasyfikacji binarnej
W klasyfikacji binarnej nieliczbową zmienne podzielonych na kategorie muszą konwertowane na funkcje numeryczne, gdy są używane tylko trwają liczbowe funkcje. Ta konwersja odbywa się przez zamianę każdy poziom nieliczbową liczbowe o podwyższonym ryzyku. W tej sekcji przedstawiono pewne ogólne zapytań programu Hive, obliczających wartości ryzyka (dziennik kolizję) zmiennej podzielonych na kategorie.

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

W tym przykładzie zmienne `smooth_param1` i `smooth_param2` są ustawione do wygładzania wartości o podwyższonym ryzyku obliczane na podstawie danych. Ryzyko ma zakres od -Inf do pliku Inf. Ryzyko > 0 wskazuje, że prawdopodobieństwo, że obiekt docelowy jest równy 1 jest większa niż 0,5.

Po ryzyko tabeli jest obliczane, użytkownicy mogą przypisywać wartości ryzyka do tabeli, łącząc go z tabeli o podwyższonym ryzyku. Przyłączany zapytania programu Hive podano w poprzedniej sekcji.

### <a name="hive-datefeatures"></a>Wyodrębnianie funkcji z pól daty i godziny
Gałąź jest dostarczany z zestawu funkcji zdefiniowanych przez użytkownika do przetwarzania pola daty/godziny. W gałęzi, domyślny format daty/godziny jest "RRRR MM-dd 00:00:00" ("01-01-1970 12:21:32" na przykład). W tej sekcji przedstawiono przykłady, które umożliwiają wyodrębnianie dnia miesiąca, miesięcy od pola daty/godziny, a także inne przykłady, które konwertują ciąg daty/godziny w formacie innym niż domyślny format ciągu daty/godziny w domyślnym formatowania.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

To zapytanie Hive założono, że  *\<pola daty/godziny >* znajduje się w domyślnym formacie daty/godziny.

Pole daty i godziny nie jest w domyślnym formacie, musisz najpierw przekonwertuj pole daty/godziny na sygnatura czasowa systemu Unix, a następnie wykonać konwersję sygnatura czasowa systemu Unix ciąg daty/godziny, który jest w domyślnym formacie. Gdy daty/godziny jest w domyślnym formatem, użytkownicy mogą stosować osadzone funkcje zdefiniowane przez użytkownika, aby wyodrębnić funkcji daty i godziny.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

W tym zapytaniu Jeśli  *\<pola daty/godziny >* ma wzorca, takich jak *2015-03-26 12:04:39*,  *\<wzorzec pola daty/godziny > "* powinien być `'MM/dd/yyyy HH:mm:ss'`. Aby przetestować go, użytkownicy mogą uruchamiać

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

*Hivesampletable* w tym zapytaniu preinstalowane na wszystkich klastrach usługi Azure HDInsight Hadoop domyślnie, gdy klastry są udostępnione.

### <a name="hive-textfeatures"></a>Wyodrębnianie funkcji z pól tekstowych
Gdy pole tekstowe, który zawiera ciąg wyrazów, które są rozdzielane spacjami znajdują się w tabeli programu Hive, następujące zapytanie wyodrębnia długość ciągu i liczbę słów w ciągu.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="hive-gpsdistance"></a>Oblicz odległości między zestawami współrzędne GPS
Zapytanie podane w tej części mogą być stosowane bezpośrednio do danych podróży NYC taksówek. Ta kwerenda ma na celu pokazują, jak zastosować osadzonych funkcji matematycznych, w gałęzi do generowania funkcji.

Pola, które są używane w tym zapytaniu są współrzędne GPS odbiór i dropoff lokalizacji o nazwie *odbioru\_długości geograficznej*, *odbioru\_latitude*,  *dropoff\_długości geograficznej*, i *dropoff\_latitude*. Zapytania, które obliczyć bezpośrednie odległość między współrzędne odbiór i dropoff są:

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

Wyrażenia matematyczne, które obliczyć odległość między dwoma współrzędne GPS znajduje się na <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">skryptów typu ruchome</a> witryny, opracowane przez Peter Lapisu. W tym Javascript, funkcja `toRad()` jest po prostu *lat_or_lon*pi/180, która konwertuje stopnie na radiany. W tym miejscu *lat_or_lon* jest geograficzna i szerokość geograficzną. Ponieważ gałąź nie zapewnia funkcji `atan2`, ale zapewnia funkcję `atan`, `atan2` funkcja jest zaimplementowana przez `atan` funkcji w powyższym zapytaniu Hive przy użyciu definicji w <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![Tworzenie obszaru roboczego](./media/create-features-hive/atan2new.png)

Pełną listę gałęzi osadzone funkcje zdefiniowane przez użytkownika można znaleźć w **funkcje wbudowane** sekcji na <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive wiki</a>).  

## <a name="tuning"></a> Zaawansowane tematy: Dostosuj parametry gałąź do zwiększenia szybkości zapytań
Domyślne parametry Hive klastra może nie być odpowiednie dla zapytań programu Hive i dane, które są przetwarzania zapytań. W tej sekcji omówiono niektóre parametry, które użytkownicy można dostrajanie poprawić wydajność zapytań technologii Hive. Użytkownicy musieli dodać parametr dostrajania kwerendy przed zapytania przetwarzania danych.

1. **Miejsca na stercie Java**: Dla zapytań obejmujących dołączenie do dużych zestawów danych lub przetwarzania rekordów długie **brakować miejsca na stercie** jest jednym z typowych problemów. Ten błąd można uniknąć przez ustawienie parametrów *mapreduce.map.java.opts* i *mapreduce.task.io.sort.mb* na odpowiednie wartości. Oto przykład:
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Ten parametr przydziela pamięci 4GB miejsca na stercie Java i sprawia, że sortowanie bardziej wydajne przez przydzielanie większej ilości pamięci dla niego. To dobry pomysł, aby odtworzyć za pomocą tych środków w przypadku dowolnego zadania, błędy związane z miejsca na stercie.

1. **Systemu plików DFS rozmiaru bloku**: Ten parametr określa najmniejsza jednostka danych przechowywanych w systemie plików. Na przykład jeśli rozmiar bloku systemu plików DFS jest 128 MB, a następnie dowolnych danych o rozmiarze mniejsza i maksymalnie 128 MB jest przechowywany w jednym bloku. Dane, które są większe niż 128 MB jest przydzielony dodatkowe bloki. 
2. Wybierając rozmiar małych blokach powoduje dużych kosztów ogólnych na platformie Hadoop, ponieważ węzeł nazw ma do przetworzenia wiele więcej żądań w celu znalezienia odpowiedniego bloku odnoszących się do pliku. Zalecane ustawienie w przypadku, gdy zajmujących się gigabajty (lub więcej) danych:

        set dfs.block.size=128m;

2. **Optymalizacja operacji tworzenia sprzężenia w gałęzi**: Podczas operacji łączenia w ramach map/reduce zazwyczaj miejsce w fazie reduce, czasami ogromne korzyści można osiągnąć dzięki zaplanowaniu sprzężeń w fazie mapy (zwane również "mapjoins"). Aby skierować gałęzi, aby to zrobić, jeśli to możliwe, należy ustawić:
   
       set hive.auto.convert.join=true;

3. **Określanie liczby liczby maperów do gałęzi**: Gdy Hadoop zezwala użytkownikowi na ustawianie liczby reduktorów, liczba liczby maperów jest zwykle nie można ustawić przez użytkownika. Lewy, umożliwiająca pewien stopień kontroli o tym numerze jest wybranie zmienne Hadoop *mapred.min.split.size* i *mapred.max.split.size* jako rozmiar każdej mapy zadania jest określana przez:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    Zazwyczaj wartość domyślna:
    
   - *mapred.min.split.SIZE* ma wartość 0, w przypadku
   - *mapred.max.split.SIZE* jest **Long.MAX** i 
   - *DFS.Block.SIZE* to 64 MB.

     Jak widać, uwzględniając rozmiar danych dostosowywania tych parametrów, ustawiając wartość"" ich pozwala nam dostosować liczbę liczby maperów używane.

4. Oto kilka innych kolejnych **zaawansowane opcje** optymalizacji wydajności technologii Hive. Te umożliwiają ustawianie pamięć przydzielona programowi mapowania i redukcji zadań i może być przydatne w Dostosowywanie wydajności. Należy pamiętać, że *mapreduce.reduce.memory.mb* nie może być większa niż rozmiar pamięci fizycznej w każdym węzłem procesu roboczego w klastrze usługi Hadoop.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

