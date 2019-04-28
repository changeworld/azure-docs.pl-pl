---
title: Splunk do wykonywania zapytań w usłudze Azure Monitor dziennika | Dokumentacja firmy Microsoft
description: Pomoc dla użytkowników, którzy są zaznajomieni z Splunk można znaleźć w usłudze Azure Monitor dziennika zapytań.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: fb637197139001c67a4cfa773f897e6701dc1e9c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61425138"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Splunk do wykonywania zapytań w usłudze Azure Monitor dziennika

W tym artykule jest przeznaczona do pomagają użytkownikom, którzy są zaznajomieni z Splunk do uczenia się języka zapytania Kusto pisania zapytań dzienników w usłudze Azure Monitor. Bezpośrednie porównania są nawiązywane między dwa, aby zrozumieć podstawowe różnice, a także podobieństwa, gdzie można wykorzystać wiedzę na temat istniejących.

## <a name="structure-and-concepts"></a>Struktura i pojęcia

W poniższej tabeli porównano pojęcia i struktur danych między dzienniki Splunk i usługi Azure Monitor.

 | Pojęcie  | Splunk | Azure Monitor |  Komentarz
 | --- | --- | --- | ---
 | Jednostki wdrożenia  | klaster |  klaster |  Usługa Azure Monitor umożliwia dowolnego krzyżowe kwerend klastra. Splunk — nie. |
 | Pamięci podręcznej danych |  przedziałów  |  Zasady buforowania i przechowywania |  Określa okres i buforowanie poziomie dla danych. To ustawienie, bezpośrednio wpływa na wydajność zapytań i obniżyć koszty wdrożenia. |
 | Partycja logiczna danych  |  indeks  |  baza danych  |  Pozwala logicznie rozdzielić dane. Zezwalaj na obu implementacjach, Unii i dołączenie na te partycje. |
 | Metadane strukturalne zdarzeń | ND | tabela |  Splunk nie ma koncepcji narażonych na język wyszukiwania metadanych zdarzenia. Dzienniki platformy Azure Monitor korzysta z koncepcji tabeli, która zawiera kolumny. Każde wystąpienie zdarzenia jest zamapowana na wiersz. |
 | Rekord danych | event | wiersz |  Tylko zmiana terminologii. |
 | Atrybut rekord danych | Pole |  Kolumny |  W usłudze Azure Monitor to wstępnie zdefiniowane jako część struktury tabeli. W usłudze Splunk każde zdarzenie ma swój własny zestaw pól. |
 | Typy | Typ danych |  Typ danych |  Usługa Azure Monitor typy danych są dokładniejsze w taki sposób, jak są one ustawione dla kolumn. Mają możliwość współpracy dynamicznie do typów danych i mniej więcej odpowiednikami zestawu typów danych, łącznie z obsługą notacji JSON. |
 | Zapytania i wyszukiwanie  | szukaj | query |  Pojęcia dotyczące zasadniczo są takie same, od usługi Azure Monitor i Splunk. |
 | Czas trwania zdarzenia pozyskiwania | System Time | ingestion_time() |  W usłudze Splunk każde zdarzenie pobiera sygnatura czasowa systemu na czas, który został zindeksowany zdarzenia. W usłudze Azure Monitor można zdefiniować zasady o nazwie ingestion_time, który udostępnia kolumna systemowa, która może znajdować się za pośrednictwem funkcji ingestion_time(). |

## <a name="functions"></a>Funkcje

W poniższej tabeli określono funkcji w usłudze Azure Monitor, które są równoważne funkcje Splunk.

|Splunk | Azure Monitor |Komentarz
|---|---|---
|strcat — | strcat()| (1) |
|split  | split() | (1) |
|if     | iff()   | (1) |
|tonumber | todouble()<br>tolong()<br>toint() | (1) |
|górny<br>Niższy |toupper()<br>tolower()|(1) |
| Zastąp | replace() | (1)<br> Należy również zauważyć, że podczas `replace()` przyjmuje trzy parametry w obu produktach, parametry są różne. |
| substr | substring() | (1)<br>Należy również zauważyć, że Splunk używa liczonego od jednego indeksów. Usługa Azure Monitor — informacje o indeksy od zera. |
| tolower |  tolower() | (1) |
| toupper | toupper() | (1) |
| dopasowanie | pasuje do wyrażenia regularnego |  (2)  |
| regex | pasuje do wyrażenia regularnego | W usłudze Splunk `regex` jest operator. W usłudze Azure Monitor jest operator relacyjny. |
| searchmatch | == | W usłudze Splunk `searchmatch` umożliwia wyszukiwanie dokładnie taki ciąg znaków.
| losowo | rand()<br>RAND(n) | Funkcja firmy Splunk zwraca liczbę od 0 do 2<sup>31</sup>-1. Usługa Azure Monitor "zwraca liczbę z zakresu od 0,0 do 1,0, lub jeśli parametr podany w przedziale od 0 do n-1.
| teraz | now() | (1)
| relative_time | totimespan() | (1)<br>W usłudze Azure Monitor firmy Splunk wielokrotność relative_time (datetimeVal, offsetVal) jest datetimeVal + totimespan(offsetVal).<br>Na przykład <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> staje się <code>...  &#124; extend myTime = now() - totimespan("1d")</code>.

(1) w ramach Splunk, funkcja jest wywoływana z `eval` operatora. W usłudze Azure Monitor jest używana jako część `extend` lub `project`.<br>(2) w Splunk, funkcja jest wywoływana z `eval` operatora. W usłudze Azure Monitor może służyć za pomocą `where` operatora.


## <a name="operators"></a>Operatory

W poniższych sekcjach znajdują się przykłady przy użyciu różnych operatorów między Splunk i Azure Monitor.

> [!NOTE]
> Na potrzeby poniższym przykładzie pole Splunk _reguły_ mapy do tabeli w usłudze Azure Monitor i firmy Splunk domyślne sygnatura czasowa mapuje do analizy dzienników _ingestion_time()_ kolumny.

### <a name="search"></a>Wyszukiwanie
W usłudze Splunk, można pominąć `search` — słowo kluczowe i określ ciąg bez cudzysłowów. W usłudze Azure Monitor rozpoczynać każde zapytanie za pomocą `find`ciąg bez cudzysłowów jest nazwa kolumny i wartość wyszukiwania musi być ciąg w cudzysłowach. 

| |  | |
|:---|:---|:---|
| Splunk | **Wyszukiwanie** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Azure Monitor | **find** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>Filtr
Usługa Azure Monitor dziennika zapytań rozpoczęcie od wyniku tabelarycznym Ustaw miejsce filtru. W usłudze Splunk filtrowanie jest operacja domyślne na bieżący indeks. Można również użyć `where` operatora w Splunk, ale nie jest zalecane.

| |  | |
|:---|:---|:---|
| Splunk | **Wyszukiwanie** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Azure Monitor | **gdzie** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>Pobieranie n zdarzenia/wierszy dla inspekcji 
Dzienników usługi Azure Monitor zapytania również obsługę `take` jako alias dla `limit`. W usłudze Splunk, jeśli wyniki są uporządkowane `head` zwróci pierwszych n wyników. W usłudze Azure Monitor limitu nie jest określona, ale zwraca pierwsze n wierszy, które znajdują się.

| |  | |
|:---|:---|:---|
| Splunk | **główny** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Azure Monitor | **Limit** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Wprowadzenie pierwszych n zdarzenia/wierszy uporządkowane według pola/kolumny
Wyniki dołu w usłudze Splunk używasz `tail`. W usłudze Azure Monitor możesz określić kierunek sortowania z `asc`.

| |  | |
|:---|:---|:---|
| Splunk | **główny** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Azure Monitor | **Do góry** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Rozszerzanie wynik zestawu przy użyciu nowego pola/kolumny
Ma również Splunk `eval` funkcji, które nie może być porównywalne z `eval` operatora. Zarówno `eval` operatora w usłudze Splunk i `extend` operatora w usłudze Azure Monitor obsługują tylko operatory arytmetyczne i funkcji skalarnych.

| |  | |
|:---|:---|:---|
| Splunk | **Eval** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Azure Monitor | **Rozszerzanie** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>Zmiana nazwy 
Usługa Azure Monitor używa tego samego operatora, aby zmienić nazwę i utworzyć nowe pole. Splunk ma dwa operatory oddzielnych `eval` i `rename`.

| |  | |
|:---|:---|:---|
| Splunk | **Zmień nazwę** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Azure Monitor | **Rozszerzanie** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>Format wyników/projekcji
Splunk nie wydaje się, że podobnie jak operator `project-away`. Aby filtrować away pola, można użyć interfejsu użytkownika.

| |  | |
|:---|:---|:---|
| Splunk | **table** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Azure Monitor | **project**<br>**project-away** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>Agregacja
Zobacz [agregacji w usłudze Azure Monitor rejestrowania zapytań](aggregations.md) agregacji różnych funkcji.

| |  | |
|:---|:---|:---|
| Splunk | **Statystyki** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Azure Monitor | **Podsumowanie** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Join
Sprzężenia w usłudze Splunk ma znaczące ograniczenia. Podzapytanie limitem 10000 wyników (ustawiona w pliku konfiguracyjnym wdrożenia) i ma ograniczoną liczbę sprzężenia odmian.

| |  | |
|:---|:---|:---|
| Splunk | **join** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Azure Monitor | **join** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>Sortowanie
W usłudze Splunk sortowania rosnąco, należy użyć `reverse` operatora. Azure monitoruje również obsługuje definiowanie miejsce umieszczenia wartości null, na początku lub na końcu.

| |  | |
|:---|:---|:---|
| Splunk | **sort** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Azure Monitor | **kolejność według** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>Rozwiń atrybut wielowartościowy elementu
To jest podobne operator zarówno Splunk, jak i usługi Azure Monitor.

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Azure Monitor | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>Aspekty wyniki, interesujące pola
W usłudze Log Analytics w witrynie Azure portal tylko pierwsza kolumna jest uwidaczniany. Wszystkie kolumny są dostępne za pośrednictwem interfejsu API.

| |  | |
|:---|:---|:---|
| Splunk | **Pola** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Azure Monitor | **zestawy reguł** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>Usuń zduplikowane
Możesz użyć `summarize arg_min()` zamiast tego można odwrócić kolejność, z których pobiera wybrany rekord.

| |  | |
|:---|:---|:---|
| Splunk | **Funkcja deduplikacji** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Azure Monitor | **Podsumowanie arg_max()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>Kolejne kroki

- Przejdź przez lekcji [Pisanie zapytań dzienników w usłudze Azure Monitor](get-started-queries.md).
