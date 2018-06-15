---
title: Za pomocą portalu wyszukiwania dziennika w Azure Log Analytics | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera samouczek, który opisuje sposób tworzenia dziennik wyszukiwania i analizowania danych przechowywanych w obszarze roboczym analizy dzienników przy użyciu portalu wyszukiwania dziennika.  Samouczek obejmuje uruchamianie pewnych prostych zapytań na potrzeby zwracania różnych typów danych i analizowanie wyników.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: bwren
ms.openlocfilehash: 37213012e817f0fae21a47a4334a519bbbca206b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "29805086"
---
# <a name="create-log-searches-in-azure-log-analytics-using-the-log-search-portal"></a>Tworzenie wyszukiwań dziennika w Analiza dzienników Azure przy użyciu portalu wyszukiwania dziennika

> [!NOTE]
> W tym artykule opisano portalu wyszukiwania dziennika w przy użyciu nowego języka zapytań usługi Analiza dzienników Azure.  Możesz dowiedzieć się więcej o nowy język i uzyskać Procedura uaktualniania obszaru roboczego na [uaktualnienia obszaru roboczego analizy dzienników Azure do nowego wyszukiwania dziennika](log-analytics-log-search-upgrade.md).  
>
> Jeśli nowy język kwerendy nie została ona uaktualniona obszaru roboczego, należy zapoznać się [wyszukiwanie danych przy użyciu dziennika wyszukiwania w analizy dzienników](log-analytics-log-searches.md) Aby uzyskać informacje o bieżącej wersji portalu wyszukiwania dziennika.

Ten artykuł zawiera samouczek, który opisuje sposób tworzenia dziennik wyszukiwania i analizowania danych przechowywanych w obszarze roboczym analizy dzienników przy użyciu portalu wyszukiwania dziennika.  Samouczek obejmuje uruchamianie pewnych prostych zapytań na potrzeby zwracania różnych typów danych i analizowanie wyników.  Głównie funkcji w portalu wyszukiwania dziennika modyfikowania zapytania, a nie ich modyfikować.  Aby uzyskać więcej informacji dotyczących bezpośredniego edytowania zapytania, zobacz [Query Language reference](https://go.microsoft.com/fwlink/?linkid=856079).

Aby utworzyć wyszukiwania w portalu analityka zaawansowane zamiast portalu wyszukiwania dziennika, zobacz [wprowadzenie do korzystania z portalu usługi analiza](https://go.microsoft.com/fwlink/?linkid=856587).  Obu portalach używany ten sam język kwerendy można uzyskać dostępu do tych samych danych, w obszarze roboczym analizy dzienników.

## <a name="prerequisites"></a>Wymagania wstępne
Ten samouczek zakłada, że masz już obszar roboczy analizy dzienników z co najmniej jedno źródło połączonych, który generuje dane dla zapytań do analizowania.  

- Jeśli nie masz obszaru roboczego, możesz utworzyć bezpłatne przy użyciu procedury na [Rozpoczynanie pracy z obszaru roboczego analizy dzienników](log-analytics-get-started.md).
- Co najmniej jednego połączenia [agenta Windows](log-analytics-windows-agent.md) lub [agenta systemu Linux](log-analytics-linux-agents.md) do obszaru roboczego.  

## <a name="open-the-log-search-portal"></a>Otwieranie portalu do wyszukiwania w dziennikach
Zacznij od otwarcia portalu do wyszukiwania w dziennikach. 

1. Otwórz witrynę Azure Portal.
2. Przejdź do analizy dzienników i wybierz obszar roboczy.
3. Wybierz **dziennika wyszukiwania**.

![Przycisk wyszukiwania dziennika](media/log-analytics-log-search-log-search-portal/log-search-button.png)

## <a name="create-a-simple-search"></a>Tworzenie prostego wyszukiwania
Najszybszym sposobem pobrania danych do pracy jest użycie prostego zapytania zwracającego wszystkie rekordy w tabeli.  Jeśli z obszarem roboczym są połączeni jacyś klienci z systemem Windows lub Linux, istnieją dane w tabeli Event („Zdarzenie”, system Windows) lub Syslog („Dziennik systemu”, system Linux).

Wpisz jedno z następujących zapytań w polu wyszukiwania, a następnie kliknij przycisk wyszukiwania.  

```
Event
```
```
Syslog
```

Dane są zwracane w domyślnym widoku listy i widać, ile zwrócono wszystkich rekordów.

![Proste zapytanie](media/log-analytics-log-search-log-search-portal/log-search-portal-01.png)

Wyświetlanych jest tylko kilka pierwszych właściwości każdego rekordu.  Kliknij pozycję **Pokaż więcej** w celu wyświetlenia wszystkich właściwości dla określonego rekordu.

![Szczegóły rekordu](media/log-analytics-log-search-log-search-portal/log-search-portal-02.png)

## <a name="set-the-time-scope"></a>Ustaw zakres czasu
Każdy rekord zebrane przez analizy dzienników ma **TimeGenerated** właściwość, która zawiera Data i godzina utworzenia rekordu.  Zapytania w portalu wyszukiwania dziennika zwraca tylko rekordy z **TimeGenerated** w zakresie czasu, który jest wyświetlany po lewej stronie ekranu.  

Filtr czasu można zmienić, wybierając z menu rozwijanego albo modyfikując suwaka.  Suwak Wyświetla wykres słupkowy przedstawiający względną liczbę rekordów dla każdego segmentu czasu w zakresie.  Ten segment będą się różnić w zależności od zakresu.

Domyślny zakres czasu to **1 dzień**.  Zmień tę wartość na **7 dni**, i zwiększyć całkowita liczba rekordów.

![Zakres czasu daty](media/log-analytics-log-search-log-search-portal/log-search-portal-03.png)

## <a name="filter-results-of-the-query"></a>Filtrowanie wyników zapytania
Po lewej stronie ekranu znajduje się okienko filtrowania, które pozwala dodać filtrowanie do zapytania bez modyfikowania go bezpośrednio.  Kilka właściwości zwracanych rekordów są wyświetlane z wartościami pierwszych dziesięciu z ich liczba rekordów.

Jeśli pracujesz z tabelą **Event** (Zdarzenie), zaznacz pole wyboru obok pozycji **Error** (Błąd) w obszarze **EVENTLEVELNAME** (Nazwa zdarzenia).   Jeśli pracujesz z tabelą **Syslog** (Dziennik systemu), zaznacz pole wyboru obok pozycji **err** (Błąd) w obszarze **SEVERITYLEVEL** (Poziom ważności).  Spowoduje to zmianę zapytania na jedno z następujących w celu ograniczenia wyników do zdarzeń błędów.

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![Filtr](media/log-analytics-log-search-log-search-portal/log-search-portal-04.png)

Dodaj właściwości do okienka filtrowania, wybierając pozycję **Dodaj do filtrów** z menu właściwości jednego z rekordów.

![Menu dodawania do filtrów](media/log-analytics-log-search-log-search-portal/log-search-portal-02a.png)

Ten sam filtr można ustawić, wybierając pozycję **Filtruj** z menu właściwości rekordu z wartością do odfiltrowania.  

Masz tylko **filtru** opcji dla właściwości o nazwie ich w kolorze niebieskim.  Są to pola *z możliwością wyszukiwania*, które są indeksowane na potrzeby warunków wyszukiwania.  Pola oznaczone kolorem szarym są polami *wyszukiwania dowolnego tekstu*, dla których jest dostępna tylko opcja **Pokaż odwołania**.  Ta opcja zwraca rekordy, dla których ta wartość istnieje w dowolnej właściwości.

![Menu filtrowania](media/log-analytics-log-search-log-search-portal/log-search-portal-01a.png)

Wyniki można grupować według pojedynczej właściwości, wybierając opcję **Grupuj według** z menu rekordu.  Spowoduje to dodanie do zapytania operatora [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) wyświetlającego wyniki na wykresie.  Można grupować według więcej niż jednej właściwości, ale w tym celu trzeba edytować zapytanie bezpośrednio.  Następnie wybierz menu rekordu **komputera** właściwości i wybierz **Grupuj według "Komputer"**.  

![Grupowanie według komputerów](media/log-analytics-log-search-log-search-portal/log-search-portal-10.png)

## <a name="work-with-results"></a>Praca z wynikami
Portal do wyszukiwania w dziennikach udostępnia wiele funkcji do pracy z wynikami zapytania.  Można sortować, filtrować i grupować wyniki w celu analizowania danych bez modyfikowania samego zapytania.  Domyślnie wyniki zapytania nie są sortowane.

Aby wyświetlić dane w postaci tabeli, która zapewnia dodatkowe opcje filtrowania i sortowania, kliknij pozycję **Tabela**.  

![Widok tabeli](media/log-analytics-log-search-log-search-portal/log-search-portal-05.png)

Kliknij strzałkę obok rekordu, aby wyświetlić szczegóły dla tego rekordu.

![Sortowanie wyników](media/log-analytics-log-search-log-search-portal/log-search-portal-06.png)

Sortuj według dowolnego pola, klikając nagłówek kolumny.

![Sortowanie wyników](media/log-analytics-log-search-log-search-portal/log-search-portal-07.png)

Filtruj wyniki według określonej wartości w kolumnie, klikając przycisk filtru i podając warunek filtru.

![Filtrowanie wyników](media/log-analytics-log-search-log-search-portal/log-search-portal-08.png)

Grupuj według kolumny przez przeciągnięcie nagłówka kolumny do góry listy wyników.  Można grupować według wielu pól, przeciągając wiele kolumn do góry.

![Grupowanie wyników](media/log-analytics-log-search-log-search-portal/log-search-portal-09.png)



## <a name="work-with-performance-data"></a>Praca z danymi wydajności
Dane wydajności dla agentów systemów Windows i Linux są przechowywane w obszarze roboczym usługi Log Analytics w tabeli **Perf**.  Rejestruje wydajności wyglądać podobnie jak dowolny inny rekord i firma Microsoft może zapisywać prostego zapytania, która zwraca wszystkie rekordy wydajności, podobnie jak ze zdarzeniami.

```
Perf
```

![Dane wydajności](media/log-analytics-log-search-log-search-portal/log-search-portal-11.png)

Jednak zwracanie milionów rekordów wszystkich obiektów i liczników wydajności nie jest bardzo przydatne.  Za pomocą tych samych metod, które zostały użyte powyżej, można przefiltrować dane, a można też po prostu wpisać poniższe zapytanie bezpośrednio w polu wyszukiwania w dziennikach.  Zostaną zwrócone tylko rekordy użycia procesorów dla komputerów z systemem zarówno Windows, jak i Linux.

```
Perf | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time")
```

![Użycie procesorów](media/log-analytics-log-search-log-search-portal/log-search-portal-12.png)

To ogranicza dane do określonego licznika, ale nadal nie udostępnia ich w szczególnie przydatnej postaci.  Można wyświetlić dane na wykresie liniowym, ale najpierw należy je zgrupować według właściwości Computer (Komputer) i TimeGenerated (Godzina wygenerowania).  Aby zgrupować według wielu pól, należy bezpośrednio zmodyfikować zapytanie. Zmodyfikuj je więc do następującej postaci.  Wykorzystywana jest tu funkcja [avg](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/avg()) działająca na właściwości **CounterValue** w celu obliczania średniej wartości dla każdej godziny.

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated
```

![Wykres danych wydajności](media/log-analytics-log-search-log-search-portal/log-search-portal-13.png)

Dane są już odpowiednio zgrupowane, można je więc wyświetlić na wykresie przez dodanie operatora [render](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator).  

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated | render timechart
```

![Wykres liniowy](media/log-analytics-log-search-log-search-portal/log-search-portal-14.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o język zapytań usługi Analiza dzienników w [wprowadzenie do korzystania z portalu usługi analiza](https://go.microsoft.com/fwlink/?linkid=856079).
- Przewodnik po użyciu samouczka [portal analityka zaawansowane](https://go.microsoft.com/fwlink/?linkid=856587) co pozwala na tej samej kwerend i dostęp do tych samych danych jako portal wyszukiwania dziennika.
