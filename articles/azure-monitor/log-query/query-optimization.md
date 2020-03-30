---
title: Optymalizuj zapytania dzienników w usłudze Azure Monitor
description: Najważniejsze wskazówki dotyczące optymalizacji zapytań dziennika w usłudze Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/28/2019
ms.openlocfilehash: c32731ce2de2b0f886a1e21ee8ccad3996e395eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480270"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>Optymalizuj zapytania dzienników w usłudze Azure Monitor
Dzienniki usługi Azure Monitor używają [usługi Azure Data Explorer (ADX)](/azure/data-explorer/) do przechowywania danych dziennika i uruchamiania zapytań do analizowania tych danych. Tworzy, zarządza i utrzymuje klastry ADX dla Ciebie i optymalizuje je pod kątem obciążenia analizy dziennika. Po uruchomieniu kwerendy jest zoptymalizowana i kierowana do odpowiedniego klastra ADX, który przechowuje dane obszaru roboczego. Dzienniki usługi Azure Monitor i Eksplorator danych platformy Azure używają wielu mechanizmów automatycznej optymalizacji zapytań. Podczas automatycznej optymalizacji zapewniają znaczny wzrost, są one w niektórych przypadkach, gdzie można znacznie zwiększyć wydajność kwerendy. W tym artykule wyjaśniono zagadnienia dotyczące wydajności i kilka technik, aby je naprawić.

Większość technik są wspólne dla kwerend, które są uruchamiane bezpośrednio w usłudze Azure Data Explorer i dzienników usługi Azure Monitor, chociaż istnieje kilka unikatowych dzienników usługi Azure Monitor, które zostały omówione w tym miejscu. Aby uzyskać więcej wskazówek dotyczących optymalizacji usługi Azure Data Explorer, zobacz [Najważniejsze wskazówki dotyczące kwerendy.](/azure/kusto/query/best-practices)

Zoptymalizowane zapytania będą:

- Uruchom szybciej, zmniejszyć ogólny czas wykonywania kwerendy.
- Mają mniejsze szanse na ograniczenie lub odrzucenie.

Należy zwrócić szczególną uwagę na zapytania, które są używane do powtarzających się i bursty użytkowania, takich jak pulpity nawigacyjne, alerty, aplikacje logiki i usługi Power BI. Wpływ nieskuteczne zapytanie w tych przypadkach jest znaczny.

## <a name="query-performance-pane"></a>Okienko wydajności kwerendy
Po uruchomieniu kwerendy w usłudze Log Analytics kliknij strzałkę w dół nad wynikami kwerendy, aby wyświetlić okienko wydajności kwerendy, które pokazuje wyniki kilku wskaźników wydajności kwerendy. Te wskaźniki wydajności są opisane w poniższej sekcji.

![Okienko wydajności kwerendy](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>Wskaźniki wydajności kwerendy

Następujące wskaźniki wydajności kwerendy są dostępne dla każdej kwerendy, która jest wykonywana:

- [Całkowita wartość procesora:](#total-cpu)Ogólne obliczenia używane do przetwarzania kwerendy we wszystkich węzłach obliczeniowych. Reprezentuje czas używany do obliczania, analizowania i pobierania danych. 

- [Dane używane do przetworzonej kwerendy:](#data-used-for-processed-query)Ogólne dane, które zostały uzyskiczone do przetworzenia kwerendy. Pod wpływem rozmiaru tabeli docelowej, używanego przedziału czasu, zastosowanych filtrów i liczby kolumn, do których się odwołuje.

- [Przedział czasu przetworzonej kwerendy:](#time-span-of-the-processed-query)Odstęp między najnowszymi i najstarszymi danymi, do których dostęp uzyskał dostęp do przetworzenia kwerendy. Wpływ jawnego zakresu czasu określonego dla kwerendy.

- [Wiek przetworzonych danych:](#age-of-processed-data)różnica między teraz a najstarszymi danymi, do których dostęp uzyskał, aby przetworzyć kwerendę. To bardzo wpływa na wydajność pobierania danych.

- [Liczba obszarów roboczych:](#number-of-workspaces)Ile obszarów roboczych było dostępnych podczas przetwarzania kwerendy z powodu niejawnego lub jawnego wyboru.

- [Liczba regionów:](#number-of-regions)Ile regionów było dostępnych podczas przetwarzania zapytań na podstawie niejawnego lub jawnego wyboru obszarów roboczych. Zapytania wieloregionowe są znacznie mniej wydajne, a wskaźniki wydajności stanowią częściowy zasięg.

- [Równoległość:](#parallelism)Wskazuje, ile system był w stanie wykonać tę kwerendę w wielu węzłach. Dotyczy tylko zapytań, które mają wysokie zużycie procesora CPU. Pod wpływem korzystania z określonych funkcji i operatorów.


## <a name="total-cpu"></a>Całkowita wartość procesora
Rzeczywisty procesor obliczeniowy, który został zainwestowany w celu przetworzenia tej kwerendy we wszystkich węzłach przetwarzania kwerend. Ponieważ większość zapytań są wykonywane na dużej liczbie węzłów, to zwykle będzie znacznie większy niż czas trwania kwerendy faktycznie miały do wykonania. 

Czas przetwarzania zapytań jest poświęcwana na:
- Pobieranie danych — pobieranie starych danych zużywa więcej czasu niż pobieranie najnowszych danych.
- Przetwarzanie danych – logika i ocena danych. 

Poza czasem spędzonym w węzłach przetwarzania kwerend, istnieje dodatkowy czas, który jest spędzany przez dzienniki usługi Azure Monitor, aby: uwierzytelnić użytkownika i sprawdzić, czy mogą uzyskać dostęp do tych danych, zlokalizować magazyn danych, przeanalizować kwerendę i przydzielić przetwarzanie kwerendy Węzłów. Ten czas nie jest uwzględniony w zapytaniu całkowity czas procesora CPU.

### <a name="early-filtering-of-records-prior-of-using-high-cpu-functions"></a>Wczesne filtrowanie rekordów przed użyciem wysokich funkcji procesora

Niektóre polecenia i funkcje kwerendy są ciężkie w ich zużycie procesora CPU. Jest to szczególnie ważne w przypadku poleceń, które analizują JSON i XML lub wyodrębniają złożone wyrażenia regularne. Takie analizowanie może odbywać się jawnie za pośrednictwem funkcji [parse_json()](/azure/kusto/query/parsejsonfunction) lub [parse_xml()](/azure/kusto/query/parse-xmlfunction) lub niejawnie w przypadku odwoływania się do kolumn dynamicznych.

Te funkcje zużywają procesora CPU proporcjonalnie do liczby wierszy, które przetwarzają. Najbardziej efektywną optymalizacją jest dodanie warunków na początku kwerendy, które mogą odfiltrować jak najwięcej rekordów, jak to możliwe przed wykonaniem funkcji intensywnie korzystającej z procesora CPU.

Na przykład następujące zapytania dają dokładnie taki sam wynik, ale drugi jest zdecydowanie najbardziej efektywny, jak warunek [where](/azure/kusto/query/whereoperator) przed analizowanie wyklucza wiele rekordów:

```Kusto
//less efficient
SecurityEvent
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // Problem: irrelevant results are filtered after all processing and parsing is done
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```

### <a name="avoid-using-evaluated-where-clauses"></a>Unikaj używania ocenianych klauzul gdzie

Kwerendy, które [zawierają, gdzie](/azure/kusto/query/whereoperator) klauzule w ocenianej kolumnie, a nie w kolumnach, które są fizycznie obecne w zestawie danych, tracą wydajność. Filtrowanie na ocenianych kolumnach zapobiega niektórym optymalizacjom systemu podczas obsługi dużych zestawów danych.
Na przykład następujące zapytania dają dokładnie taki sam wynik, ale drugi jest bardziej wydajny, ponieważ [warunek, w którym](/azure/kusto/query/whereoperator) jest używany, odnosi się do wbudowanej kolumny

```Kusto
//less efficient
Heartbeat 
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| where IPRegion == "WestCoast"
| summarize count() by Computer
```
```Kusto
//more efficient
Heartbeat 
| where RemoteIPLongitude  < -94
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| summarize count() by Computer
```

### <a name="use-effective-aggregation-commands-and-dimmentions-in-summarize-and-join"></a>Użyj skutecznych poleceń agregacji i przyciemnień w podsumowaniu i sprzężeniu

Podczas gdy niektóre polecenia agregacji, takie jak [max()](/azure/kusto/query/max-aggfunction), [sum()](/azure/kusto/query/sum-aggfunction), [count()](/azure/kusto/query/count-aggfunction)i [avg()](/azure/kusto/query/avg-aggfunction) mają niewielki wpływ procesora ze względu na ich logikę, inne są bardziej złożone i zawierają heurystykę i szacunki, które pozwalają na ich wydajne wykonanie. Na przykład [dcount()](/azure/kusto/query/dcount-aggfunction) używa algorytmu HyperLogLog, aby zapewnić dokładne oszacowanie do odrębnej liczby dużych zestawów danych bez faktycznie zliczania każdej wartości; funkcje percentyla robią podobne przybliżenia przy użyciu algorytmu percentyla najbliższej rangi. Kilka poleceń zawiera opcjonalne parametry, aby zmniejszyć ich wpływ. Na przykład funkcja [makeset()](/azure/kusto/query/makeset-aggfunction) ma opcjonalny parametr definiujący maksymalny rozmiar zestawu, co znacząco wpływa na procesor i pamięć.

[Dołączanie](/azure/kusto/query/joinoperator?pivots=azuremonitor) i [podsumowanie](/azure/kusto/query/summarizeoperator) poleceń może spowodować wysokie wykorzystanie procesora CPU podczas przetwarzania dużego zestawu danych. Ich złożoność jest bezpośrednio związana z liczbą możliwych wartości, określanych jako *kardynalność*, kolumn, które są używane jako `by` in summarize lub jako atrybuty sprzężenia. Aby uzyskać wyjaśnienie i optymalizację sprzężenia i podsumowania, zobacz ich artykuły dokumentacji i wskazówki dotyczące optymalizacji.

Na przykład następujące kwerendy dają dokładnie taki sam wynik, ponieważ **CounterPath** jest zawsze jeden do jednego mapowane do **CounterName** i **ObjectName**. Drugi jest bardziej wydajny, ponieważ wymiar agregacji jest mniejszy:

```Kusto
//less efficient
Perf
| summarize avg(CounterValue) 
by CounterName, CounterPath, ObjectName
```
```Kusto
//make the group expression more compact improve the performance
Perf
| summarize avg(CounterValue), any(CounterName), any(ObjectName) 
by CounterPath
```

Na zużycie procesora CPU może mieć również wpływ warunki lub rozszerzone kolumny, które wymagają intensywnego przetwarzania. Wszystkie trywialne porównania ciągów, takie jak [equal ==](/azure/kusto/query/datatypes-string-operators) i [startswith](/azure/kusto/query/datatypes-string-operators) mają mniej więcej taki sam wpływ procesora, podczas gdy zaawansowane dopasowania tekstu mają większy wpływ. W szczególności [ma](/azure/kusto/query/datatypes-string-operators) operator jest bardziej wydajne, że [zawiera](/azure/kusto/query/datatypes-string-operators) operatora. Ze względu na techniki obsługi ciągów, jest bardziej efektywne szukać ciągów, które są dłuższe niż cztery znaki niż krótkie ciągi.

Na przykład następujące kwerendy dają podobne wyniki, w zależności od zasady nazewnictwa komputera, ale druga jest bardziej wydajna:

```Kusto
//less efficient – due to filter based on contains
Heartbeat
| where Computer contains "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//less efficient – due to filter based on extend
Heartbeat
| extend MyComputer = Computer
| where MyComputer startswith "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//more efficient
Heartbeat
| where Computer startswith "Production" 
| summarize count() by ComputerIP 
```

> [!NOTE]
> Wskaźnik ten przedstawia tylko procesora CPU z bezpośredniego klastra. W kwerendzie wieloregionowej będzie reprezentować tylko jeden z regionów. W kwerendzie wieloprzestrzeniowej może nie zawierać wszystkich obszarów roboczych.


## <a name="data-used-for-processed-query"></a>Dane używane do przetworzonej kwerendy

Kluczowym czynnikiem w przetwarzaniu kwerendy jest ilość danych, która jest skanowana i używana do przetwarzania kwerendy. Usługa Azure Data Explorer używa agresywnych optymalizacji, które znacznie zmniejszają ilość danych w porównaniu z innymi platformami danych. Mimo to istnieją krytyczne czynniki w kwerendzie, które mogą mieć wpływ na wolumin danych, który jest używany.

W dziennikach usługi Azure Monitor kolumna **TimeGenerated** jest używana jako sposób indeksowania danych. Ograniczenie **timegenerated** wartości do jak zawęzić zakres, jak to możliwe spowoduje znaczną poprawę wydajności kwerendy przez znaczne ograniczenie ilości danych, które mają być przetwarzane.

### <a name="avoid-unnecessary-use-of-search-and-union-operators"></a>Unikanie niepotrzebnego korzystania z operatorów wyszukiwania i unii

Innym czynnikiem, który zwiększa dane, które są procesem jest użycie dużej liczby tabel. Zwykle dzieje `search *` się `union *` tak, gdy używane są polecenia. Polecenia te wymuszają system do oceny i skanowania danych ze wszystkich tabel w obszarze roboczym. W niektórych przypadkach może istnieć setki tabel w obszarze roboczym. Staraj się unikać jak najwięcej za pomocą "szukaj *" lub wyszukiwania bez określania zakresu do konkretnej tabeli.

Na przykład następujące zapytania dają dokładnie taki sam wynik, ale ostatni jest zdecydowanie najbardziej efektywny:

```Kusto
// This version scans all tables though only Perf has this kind of data
search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This version scans all strings in Perf tables – much more efficient
Perf
| search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This is the most efficient version 
Perf 
| where CounterName == "% Processor Time"  
| summarize count(), avg(CounterValue)  by Computer
```

### <a name="add-early-filters-to-the-query"></a>Dodawanie wczesnych filtrów do kwerendy

Inną metodą zmniejszenia ilości danych jest, aby mieć [warunki](/azure/kusto/query/whereoperator) na początku kwerendy. Platforma Azure Data Explorer zawiera pamięć podręczną, która informuje, które partycje zawierają dane, które są istotne dla określonego warunku gdzie. Na przykład jeśli kwerenda zawiera `where EventID == 4624` następnie będzie dystrybuować kwerendę tylko do węzłów, które obsługują partycje z pasującymi zdarzeniami.

Następujące przykładowe kwerendy dają dokładnie taki sam wynik, ale drugi jest bardziej wydajny:

```Kusto
//less efficient
SecurityEvent
| summarize LoginSessions = dcount(LogonGuid) by Account
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 4624 //Logon GUID is relevant only for logon event
| summarize LoginSessions = dcount(LogonGuid) by Account
```

### <a name="reduce-the-number-of-columns-that-is-retrieved"></a>Zmniejszanie liczby pobieranych kolumn

Ponieważ Usługa Azure Data Explorer jest magazynem danych kolumnowych, pobieranie każdej kolumny jest niezależne od innych. Liczba pobieranych kolumn ma bezpośredni wpływ na ogólną ilość danych. Należy uwzględnić tylko kolumny w danych wyjściowych, które są potrzebne przez [podsumowanie](/azure/kusto/query/summarizeoperator) wyników lub [rzutowanie](/azure/kusto/query/projectoperator) określonych kolumn. Usługa Azure Data Explorer ma kilka optymalizacji, aby zmniejszyć liczbę pobranych kolumn. Jeśli stwierdzi, że kolumna nie jest potrzebna, na przykład jeśli nie odwołuje się do niego w [poleceniu podsumować,](/azure/kusto/query/summarizeoperator) nie będzie go pobrać.

Na przykład druga kwerenda może przetwarzać trzy razy więcej danych, ponieważ musi pobrać nie jedną kolumnę, ale trzy:

```Kusto
//Less columns --> Less data
SecurityEvent
| summarize count() by Computer  
```
```Kusto
//More columns --> More data
SecurityEvent
| summarize count(), dcount(EventID), avg(Level) by Computer  
```

## <a name="time-span-of-the-processed-query"></a>Przedział czasu przetworzonej kwerendy

Wszystkie dzienniki w dziennikach usługi Azure Monitor są podzielone na partycje zgodnie z **kolumną TimeGenerated.** Liczba partycji, które są dostępne są bezpośrednio związane z przedziału czasu. Zmniejszenie zakresu czasu jest najbardziej efektywnym sposobem zapewnienia wykonywania zapytania monitu.

Zakres czasu można ustawić za pomocą selektora zakresu czasu na ekranie Usługi Log Analytics, zgodnie z opisem w [obszarze Zakres zapytania dziennika i zakres czasu w usłudze Azure Monitor Log Analytics](scope.md#time-range). Jest to zalecana metoda, ponieważ wybrany zakres czasu jest przekazywany do wewnętrznej bazy danych przy użyciu metadanych kwerendy. 

Alternatywną metodą jest jawnie [dołączyć, gdzie](/azure/kusto/query/whereoperator) warunek **timegenerated** w kwerendzie. Należy użyć tej metody, ponieważ zapewnia, że przedział czasu jest stały, nawet wtedy, gdy kwerenda jest używana z innego interfejsu.
Należy upewnić się, że wszystkie części kwerendy mają **filtry TimeGenerated.** Gdy kwerenda ma podzespołów pobierania danych z różnych tabel lub tej samej tabeli, każdy musi zawierać [własne, gdzie](/azure/kusto/query/whereoperator) warunek.

### <a name="make-sure-all-sub-queries-have-timegenerated-filter"></a>Upewnij się, że wszystkie kwerendy podrzędne mają filtr TimeGenerated

Na przykład w poniższej kwerendzie, podczas gdy **tabela Perf** będzie skanowana tylko przez ostatni dzień, **tabela Pulsu** zostanie zeskanowana dla całej jej historii, która może wynosić do dwóch lat:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize IPs = makeset(ComputerIP, 10) by  Computer
) on Computer
```

Typowym przypadkiem, gdy wystąpi taki błąd, jest [użycie arg_max()](/azure/kusto/query/arg-max-aggfunction) w celu znalezienia najnowszego wystąpienia. Przykład:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Można to łatwo poprawić, dodając filtr czasu w wewnętrznej kwerendzie:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    | where TimeGenerated > ago(1d) //filter for this part
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Innym przykładem tego błędu jest podczas wykonywania filtrowania zakresu czasu tuż po [unii](/azure/kusto/query/unionoperator?pivots=azuremonitor) przez kilka tabel. Podczas wykonywania unii, każda kwerenda podrzędna powinna być w zakresie. Instrukcji let można [użyć,](/azure/kusto/query/letstatement) aby zapewnić spójność zakresu.

Na przykład następująca kwerenda przeskanuje wszystkie dane w tabelach *Pulsu i* *Perf,* a nie tylko w ciągu ostatniego dnia:

```Kusto
Heartbeat 
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | summarize arg_min(TimeGenerated,*) by Computer) 
| where TimeGenerated > ago(1d)
| summarize min(TimeGenerated) by Computer
```

Ta kwerenda powinna zostać ustalona w następujący sposób:

```Kusto
let MinTime = ago(1d);
Heartbeat 
| where TimeGenerated > MinTime
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | where TimeGenerated > MinTime
    | summarize arg_min(TimeGenerated,*) by Computer) 
| summarize min(TimeGenerated) by Computer
```

### <a name="time-span-measurement-limitations"></a>Ograniczenia pomiaru przedziału czasowego

Pomiar jest zawsze większy niż określony rzeczywisty czas. Na przykład jeśli filtr w kwerendzie wynosi 7 dni, system może skanować 7,5 lub 8,1 dnia. Dzieje się tak, ponieważ system dzieli dane na fragmenty o zmiennym rozmiarze. Aby upewnić się, że wszystkie odpowiednie rekordy są skanowane, skanuje całą partycję, która może obejmować kilka godzin, a nawet więcej niż jeden dzień.

Istnieje kilka przypadków, w których system nie może zapewnić dokładnego pomiaru zakresu czasu. Dzieje się tak w większości przypadków, gdy zakres kwerendy mniej niż jeden dzień lub w kwerendach wielu obszarów roboczych.


> [!IMPORTANT]
> Wskaźnik ten przedstawia tylko dane przetwarzane w bezpośrednim klastrze. W kwerendzie wieloregionowej będzie reprezentować tylko jeden z regionów. W kwerendzie wieloprzestrzeniowej może nie zawierać wszystkich obszarów roboczych.

## <a name="age-of-processed-data"></a>Wiek przetwarzanych danych
Usługa Azure Data Explorer używa kilku warstw magazynu: w pamięci, dysków SSD lokalnych i znacznie wolniejszych obiektów blob platformy Azure. Im nowsze dane, tym wyższa jest szansa, że jest przechowywany w warstwie bardziej wydajne z mniejszym opóźnieniem, zmniejszając czas trwania kwerendy i procesora CPU. Inne niż dane, system ma również pamięć podręczną metadanych. Im starsze dane, tym mniejsza szansa, że ich metadane będą w pamięci podręcznej.

Podczas gdy niektóre zapytania wymagają użycia starych danych, istnieją przypadki, w których stare dane są używane przez pomyłkę. Dzieje się tak, gdy kwerendy są wykonywane bez zapewnienia zakresu czasu w ich meta-danych i nie wszystkie odwołania do tabeli obejmują filtr w **TimeGenerated** kolumny. W takich przypadkach system przeskanuje wszystkie dane, które są przechowywane w tej tabeli. Gdy przechowywanie danych jest długa, może obejmować długi zakres czasu, a tym samym dane, które są tak stare, jak okres przechowywania danych.

Takie przypadki mogą być na przykład:

- Nie ustawia zakresu czasu w usłudze Log Analytics za pomocą zapytania podrzędnego, które nie jest ograniczone. Zobacz przykład powyżej.
- Korzystanie z interfejsu API bez parametrów opcjonalnych zakresu czasu.
- Korzystanie z klienta, który nie wymusza zakresu czasu, takiego jak złącze usługi Power BI.

Zobacz przykłady i notatki w sekcji pervious, ponieważ są one również istotne w tym przypadku.

## <a name="number-of-regions"></a>Liczba regionów
Istnieje kilka sytuacji, w których pojedyncze zapytanie może być wykonywane w różnych regionach:

- Gdy kilka obszarów roboczych są jawnie wymienione i znajdują się w różnych regionach.
- Gdy kwerenda o zakresie zasobów pobiera dane, a dane są przechowywane w wielu obszarach roboczych, które znajdują się w różnych regionach.

Wykonywanie kwerendy między regionami wymaga, aby system serializował i przesyłał w wewnętrznej bazy danych duże fragmenty danych pośrednich, które są zwykle znacznie większe niż wyniki końcowe kwerendy. Ogranicza również zdolność systemu do wykonywania optymalizacji, heurystyki i korzystania z pamięci podręcznej.
Jeśli nie ma żadnego rzeczywistego powodu, aby skanować wszystkie te regiony, należy dostosować zakres, tak aby obejmował mniej regionów. Jeśli zakres zasobu jest zminimalizowany, ale nadal wiele regionów są używane, może się zdarzyć z powodu błędnej konfiguracji. Na przykład dzienniki inspekcji i ustawienia diagnostyczne są wysyłane do różnych obszarów roboczych w różnych regionach lub istnieje wiele konfiguracji ustawień diagnostycznych. 

> [!IMPORTANT]
> Gdy kwerenda jest uruchamiana w kilku regionach, pomiary procesora CPU i danych nie będą dokładne i będą reprezentować pomiar tylko w jednym z regionów.

## <a name="number-of-workspaces"></a>Liczba obszarów roboczych
Obszary robocze są kontenery logiczne, które są używane do segregowania i administrowania danymi dzienników. Wewnętrznej bazy danych optymalizuje miejsca docelowe obszaru roboczego w klastrach fizycznych w wybranym regionie.

Użycie wielu obszarów roboczych może wynikać z: 

- Gdzie kilka obszarów roboczych są jawnie wymienione.
- Gdy kwerenda o zakresie zasobów pobiera dane, a dane są przechowywane w wielu obszarach roboczych.
 
Wykonywanie zapytań między regionami i między klastrami wymaga, aby system serializował i przesyłał w wewnętrznej bazy danych duże fragmenty danych pośrednich, które są zwykle znacznie większe niż wyniki końcowe kwerendy. Ogranicza również zdolność systemu do wykonywania optymalizacji, heurystyki i korzystania z pamięci podręcznej.

> [!IMPORTANT]
> W niektórych scenariuszach z wieloma obszarami roboczymi pomiary procesora CPU i danych nie będą dokładne i będą reprezentować pomiar tylko dla kilku obszarów roboczych.

## <a name="parallelism"></a>Równoległości prostych
Dzienniki usługi Azure Monitor używa dużych klastrów Usługi Azure Data Explorer do uruchamiania kwerend, a te klastry różnią się skalą, potencjalnie uzyskując do kilkudziesięciu węzłów obliczeniowych. System automatycznie skaluje klastry zgodnie z logiką i pojemnością umieszczania obszaru roboczego.

Aby skutecznie wykonać kwerendę, jest ona podzielona na partycje i dystrybuowana do węzłów obliczeniowych na podstawie danych wymaganych do jego przetwarzania. Istnieją sytuacje, w których system nie może tego skutecznie zrobić. Może to prowadzić do długiego czasu trwania kwerendy. 

Zachowania kwerend, które mogą zmniejszyć równoległość obejmują:

- Użycie funkcji serializacji i okien, takich jak [operator serializacji](/azure/kusto/query/serializeoperator), [next()](/azure/kusto/query/nextfunction), [prev()](/azure/kusto/query/prevfunction)i funkcje [wiersza.](/azure/kusto/query/rowcumsumfunction) Szeregi czasowe i funkcje analizy użytkowników mogą być używane w niektórych z tych przypadków. Nieefektywna serializacja może się również zdarzyć, jeśli na końcu zapytania używane są następujące operatory: [zakres](/azure/kusto/query/rangeoperator), [sort ,](/azure/kusto/query/sortoperator) [order](/azure/kusto/query/orderoperator), [top](/azure/kusto/query/topoperator), [top-hitters](/azure/kusto/query/tophittersoperator), [getschema](/azure/kusto/query/getschemaoperator).
-    Użycie funkcji agregacji [dcount()](/azure/kusto/query/dcount-aggfunction) zmusza system do centralnej kopii różnych wartości. Gdy skala danych jest wysoka, należy rozważyć użycie funkcji dcount opcjonalne parametry, aby zmniejszyć dokładność.
-    W wielu przypadkach operator [sprzężenia](/azure/kusto/query/joinoperator?pivots=azuremonitor) obniża ogólny równoległość. Sprawdź shuffle sprzężenia jako alternatywę, gdy wydajność jest problematyczna.
-    W kwerendach zakresu zasobów kontrole RBAC przed wykonaniem mogą występnąć w sytuacjach, gdy istnieje bardzo duża liczba przypisań RBAC. Może to prowadzić do dłuższych kontroli, które mogłyby spowodować niższy równoległość. Na przykład kwerenda jest wykonywana w ramach subskrypcji, w której istnieją tysiące zasobów, a każdy zasób ma wiele przypisań ról na poziomie zasobu, a nie w grupie subskrypcji lub zasobów.
-    Jeśli kwerenda przetwarza małe fragmenty danych, jego równoległość będzie niska, ponieważ system nie rozłoży go na wiele węzłów obliczeniowych.



## <a name="next-steps"></a>Następne kroki

- [Dokumentacja referencyjna dla języka zapytań Kusto](/azure/kusto/query/).
