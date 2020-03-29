---
title: Zapytanie dziennika Splunk do usługi Azure Monitor | Dokumenty firmy Microsoft
description: Pomoc dla użytkowników, którzy są zaznajomieni ze Splunk w nauce zapytań dziennika usługi Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: 6346055f1169bfa533d5dbfe441ecf27fb0d78a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75397747"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Zapytanie dziennika Splunk to Azure Monitor

Ten artykuł ma na celu pomóc użytkownikom, którzy są zaznajomieni ze Splunk, aby dowiedzieć się języka zapytań Kusto do pisania zapytań dziennika w usłudze Azure Monitor. Dokonuje się bezpośrednich porównań między nimi, aby zrozumieć kluczowe różnice, a także podobieństwa, w których można wykorzystać istniejącą wiedzę.

## <a name="structure-and-concepts"></a>Struktura i koncepcje

W poniższej tabeli porównano pojęcia i struktury danych między dziennikami Splunk i Azure Monitor.

 | Pojęcie  | Splunk | Azure Monitor |  Komentarz
 | --- | --- | --- | ---
 | Jednostka rozmieszczania  | cluster |  cluster |  Usługa Azure Monitor umożliwia dowolne zapytania klastra krzyżowego. Splunk nie. |
 | Pamięć podręczna danych |  Wiadra  |  Zasady buforowania i przechowywania |  Steruje okresem i poziomem buforowania danych. To ustawienie ma bezpośredni wpływ na wydajność zapytań i koszt wdrożenia. |
 | Logiczna partycja danych  |  indeks  |  database  |  Umożliwia logiczne oddzielenie danych. Obie implementacje umożliwiają związki i łączenie się między tymi partycjami. |
 | Metadane zdarzeń strukturalnych | Nie dotyczy | tabela |  Splunk nie ma pojęcia narażone na język wyszukiwania metadanych zdarzeń. Dzienniki usługi Azure Monitor ma koncepcję tabeli, która ma kolumny. Każde wystąpienie zdarzenia jest mapowane do wiersza. |
 | Rekord danych | event | Wiersza |  Zmiana terminologii tylko. |
 | Atrybut rekordu danych | pole |  kolumna |  W usłudze Azure Monitor jest to wstępnie zdefiniowane jako część struktury tabeli. W splunk, każde zdarzenie ma swój własny zestaw pól. |
 | Types | Datatype |  Datatype |  Typy danych usługi Azure Monitor są bardziej jawne, ponieważ są one ustawione w kolumnach. Oba mają możliwość dynamicznej pracy z typami danych i mniej więcej równoważny zestaw typów danych, w tym obsługi JSON. |
 | Zapytanie i wyszukiwanie  | search | query |  Pojęcia są zasadniczo takie same między azure monitor i Splunk. |
 | Czas pozyskiwania zdarzeń | Czas systemowy | ingestion_time() |  W splunk każde zdarzenie pobiera sygnaturę czasową systemu czasu, który zdarzenie zostało zindeksowane. W usłudze Azure Monitor można zdefiniować zasadę o nazwie ingestion_time, która udostępnia kolumnę systemową, do której można się odwoływać za pośrednictwem funkcji ingestion_time(). |

## <a name="functions"></a>Funkcje

W poniższej tabeli określono funkcje w usłudze Azure Monitor, które są równoważne funkcji Splunk.

|Splunk | Azure Monitor |Komentarz
|---|---|---
|strcat (strcat) | strcat()| (1) |
|split  | split() | (1) |
|if     | iff()   | (1) |
|numer tonu | todouble()<br>tolong()<br>toint() | (1) |
|Górnej<br>Niższe |toupper()<br>tolower()|(1) |
| Zastąp | Replace() | (1)<br> Należy również `replace()` zauważyć, że podczas gdy przyjmuje trzy parametry w obu produktach, parametry są różne. |
| podstr | podciąg() | (1)<br>Należy również pamiętać, że Splunk używa indeksów opartych na jednym. Usługa Azure Monitor odnotowuje indeksy oparte na wartościach zerowych. |
| Tolower |  tolower() | (1) |
| Toupper | toupper() | (1) |
| match | dopasowuje regex |  (2)  |
| Regex | dopasowuje regex | W Splunk, `regex` jest operatorem. W usłudze Azure Monitor jest operatorem relacyjnego. |
| searchmatch ( searchmatch ) | == | W splunku `searchmatch` umożliwia wyszukiwanie dokładnego ciągu.
| losowo | Rand()<br>rand(n) | Funkcja Splunk zwraca liczbę z zera do 2<sup>31</sup>-1. Usługa Azure Monitor zwraca liczbę z 0,0 do 1,0 lub jeśli dostarczony parametr, między 0 a n-1.
| teraz | now() | (1)
| relative_time | totimespan() | (1)<br>W usłudze Azure Monitor odpowiednik splunka relative_time(datetimeVal, offsetVal) to datetimeVal + totimespan(offsetVal).<br>Na przykład, <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> staje się <code>...  &#124; extend myTime = now() - totimespan("1d")</code>.

(1) W Splunk funkcja jest wywoływana z operatorem. `eval` W usłudze Azure Monitor jest `extend` `project`on używany jako część lub .<br>(2) W Splunk funkcja jest wywoływana z operatorem. `eval` W usłudze Azure Monitor można `where` go używać z operatorem.


## <a name="operators"></a>Operatory

W poniższych sekcjach podano przykłady używania różnych operatorów między splunk i azure monitor.

> [!NOTE]
> Na potrzeby poniższego przykładu _reguła_ pola Splunk jest mapowana do tabeli w usłudze Azure Monitor, a domyślna sygnatura czasowa Splunka jest mapowana do kolumny Usługi _ingestion_time()._

### <a name="search"></a>Wyszukiwanie
W splunk, można pominąć `search` słowa kluczowego i określić ciąg niecytowane. W usłudze Azure Monitor `find`należy uruchomić każdą kwerendę za pomocą , niecytowany ciąg jest nazwą kolumny, a wartość odnośnika musi być ciągiem cytowanym. 

| |  | |
|:---|:---|:---|
| Splunk | **Szukaj** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Azure Monitor | **find** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>Filtr
Zapytania dziennika usługi Azure Monitor rozpoczynają się od zestawu wyników tabelaryczne, w którym filtr. W splunku filtrowanie jest domyślną operacją bieżącego indeksu. Można również `where` użyć operatora w splunku, ale nie jest to zalecane.

| |  | |
|:---|:---|:---|
| Splunk | **Szukaj** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Azure Monitor | **where** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>Uzyskiwanie n zdarzeń/wierszy do kontroli 
Zapytania dziennika usługi Azure `take` Monitor również `limit`obsługują jako alias do . W splunk, jeśli wyniki są `head` uporządkowane, zwróci pierwsze wyniki n. W usłudze Azure Monitor limit nie jest uporządkowany, ale zwraca pierwsze n wierszy, które zostały znalezione.

| |  | |
|:---|:---|:---|
| Splunk | **Głowy** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Azure Monitor | **Limit** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Pierwsze n zdarzenia/wiersze uporządkowane według pola/kolumny
W przypadku wyników na dole `tail`w splunku używasz pliku . W usłudze Azure Monitor można `asc`określić kierunek zamawiania za pomocą pliku .

| |  | |
|:---|:---|:---|
| Splunk | **Głowy** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Azure Monitor | **Do góry** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Rozszerzanie zestawu wyników o nowe pola/kolumny
Splunk posiada `eval` również funkcję, która nie może `eval` być porównywalna z operatorem. `eval` Operator w splunk i `extend` operator w usłudze Azure Monitor obsługują tylko funkcje skalarne i operatory arytmetyczne.

| |  | |
|:---|:---|:---|
| Splunk | **Eval** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Azure Monitor | **Rozszerzyć** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>Zmiana nazwy 
Usługa Azure Monitor `project-rename` używa operatora do zmiany nazwy pola. `project-rename`umożliwia kwerendzie korzystanie z wszelkich indeksów wstępnie utworzonych dla pola. Splunk ma `rename` operatora, aby zrobić to samo.

| |  | |
|:---|:---|:---|
| Splunk | **Zmienić nazwę** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Azure Monitor | **zmiana nazwy projektu** | <code>Office_Hub_OHubBGTaskError<br>&#124; project-rename exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>Formatowanie wyników/projekcji
Splunk nie wydaje się mieć `project-away`operatora podobnego do . Za pomocą interfejsu użytkownika można odfiltrować pola.

| |  | |
|:---|:---|:---|
| Splunk | **table** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Azure Monitor | **Projektu**<br>**projekt-away** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>Agregacja
Zobacz [agregacje w usłudze Azure Monitor kwerend dziennika](aggregations.md) dla różnych funkcji agregacji.

| |  | |
|:---|:---|:---|
| Splunk | **Statystyki** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Azure Monitor | **Podsumować** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Join
Dołącz do Splunk ma znaczne ograniczenia. Podkwerendy ma limit 10000 wyników (ustawiony w pliku konfiguracji wdrożenia) i istnieje ograniczona liczba smaków sprzężenia.

| |  | |
|:---|:---|:---|
| Splunk | **join** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Azure Monitor | **join** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>Sortowanie
W Splunk, aby sortować w porządku `reverse` rosnącym, należy użyć operatora. Usługa Azure Monitor obsługuje również definiowanie, gdzie umieścić wartości null, na początku lub na końcu.

| |  | |
|:---|:---|:---|
| Splunk | **Sortowania** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Azure Monitor | **zamów przez** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>Rozwijanie wielowartościowe
Jest to podobny operator w splunk i azure monitor.

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Azure Monitor | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>Aspekty wyników, ciekawe pola
W usłudze Log Analytics w witrynie Azure portal jest dostępna tylko pierwsza kolumna. Wszystkie kolumny są dostępne za pośrednictwem interfejsu API.

| |  | |
|:---|:---|:---|
| Splunk | **Pola** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Azure Monitor | **Aspekty** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>Usuwanie duplikatu
Zamiast tego `summarize arg_min()` można użyć, aby odwrócić kolejność, który rekord zostanie wybrany.

| |  | |
|:---|:---|:---|
| Splunk | **dedup** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Azure Monitor | **podsumuj arg_max()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>Następne kroki

- Przejdź przez lekcję na [temat pisania zapytań dziennika w usłudze Azure Monitor](get-started-queries.md).
