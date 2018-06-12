---
title: Przy użyciu Analytics — narzędzie wyszukiwania zaawansowanego w Azure Application Insights | Dokumentacja firmy Microsoft
description: 'Przy użyciu Analytics, narzędzie zaawansowane wyszukiwanie diagnostycznych z usługi Application Insights. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: c3b34430-f592-4c32-b900-e9f50ca096b3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2017
ms.reviewer: danha
ms.author: mbullwin
ms.openlocfilehash: 7f8f49cf88bda8e485d2365281c13680ef796196
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295523"
---
# <a name="using-analytics-in-application-insights"></a>Za pomocą analizy w usłudze Application Insights
[Analiza](app-insights-analytics.md) to funkcja wyszukiwania zaawansowanego [usługi Application Insights](app-insights-overview.md). Te strony opisano język zapytań usługi Analiza dzienników.

* **[Obejrzyj klip wideo wprowadzenia](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Przetestuj Analytics na naszych danych symulowane](https://analytics.applicationinsights.io/demo)**  aplikacji nie jest wysyłania danych do usługi Application Insights jeszcze.

## <a name="open-analytics"></a>Otwórz analityka
Macierzysty zasobów aplikacji w usłudze Application Insights kliknij Analytics.

![Otwórz portal.azure.com otworzyć zasobu usługi Application Insights, a następnie kliknij przycisk Analytics.](./media/app-insights-analytics-using/001.png)

Samouczek wbudowany daje sugestii dotyczących co można zrobić.

Brak [szerszej samouczek tutaj](app-insights-analytics-tour.md).

## <a name="query-your-telemetry"></a>Zapytanie telemetrii
### <a name="write-a-query"></a>Napisz zapytanie
![Wyświetlanie schematu](./media/app-insights-analytics-using/150.png)

Zaczyna się od nazwy tabel wymienionych po lewej stronie (lub [zakres](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/range-operator) lub [Unii](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/union-operator) operatory). Użyj `|` można utworzyć potoku o [operatory](https://docs.loganalytics.io/docs/Learn/References/Useful-operators). 

IntelliSense wyświetla operatory i elementy wyrażenia, które są dostępne. Kliknij ikonę informacji (lub naciśnij klawisze CTRL + SPACJA) Aby uzyskać opis dłużej i przykłady sposobu używania poszczególnych elementów.

Zobacz [samouczek języka Analytics](app-insights-analytics-tour.md) i [materiały referencyjne dotyczące języka](app-insights-analytics-reference.md).

### <a name="run-a-query"></a>Uruchamianie zapytania
![Wykonanie kwerendy](./media/app-insights-analytics-using/130.png)

1. Pojedynczy podziały wierszy można użyć w zapytaniu.
2. Umieść kursor wewnątrz lub na końcu zapytania, który chcesz uruchomić.
3. Sprawdź zakres czasu zapytania. (Można zmienić lub zmienić, umieszczając w niej własnych [ `where...timestamp...` ](https://docs.loganalytics.io/docs/Learn/Tutorials/Date-and-time-operations) klauzuli w zapytaniu.)
3. Kliknij polecenie Przejdź do uruchomienia zapytania.
4. Nie umieszczaj pustych wierszy w zapytaniu. Kilka zapytań rozdzielonych można przechowywać w jedną kartę zapytanie, rozdzielając je puste wiersze. Uruchamia tylko kwerendy, w której znajduje się kursor.

### <a name="save-a-query"></a>Zapisz zapytanie
![Zapisywanie zapytania](./media/app-insights-analytics-using/140.png)

1. Zapisz bieżący plik zapytania.
2. Otwórz plik zapisanego zapytania.
3. Utwórz nowy plik zapytania.

## <a name="see-the-details"></a>Zobacz szczegóły
Rozwiń wszystkie wiersze w wynikach, aby wyświetlić jego pełną listę właściwości. Można rozwinąć dowolnej właściwości, która jest wartością strukturalnych — na przykład, niestandardowe wymiary lub stosu w Wystąpił wyjątek.

![Rozwiń węzeł wiersza](./media/app-insights-analytics-using/070.png)

## <a name="arrange-the-results"></a>Rozmieszczenie wyników
Można sortować, filtrowanie, z podziałem na strony i grupy wyników zwróconych z zapytania.

> [!NOTE]
> Sortowanie, grupowanie i filtrowanie w przeglądarce nie ponownie uruchom zapytanie. Rozmieszczanie one tylko wyników zwróconych przez kwerendę ostatniego. 
> 
> Aby wykonać te zadania na serwerze przed są zwracane, zapisać zapytanie z [sortowania](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator), [Podsumuj](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) i [gdzie](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) operatorów.
> 
> 

Wybierz kolumny, które chcesz wyświetlić, przeciągnij nagłówek kolumny w celu ich ponownego rozmieszczenia i zmiana rozmiaru kolumn przez przeciągnięcie ich obramowania.

![Rozmieść kolumn](./media/app-insights-analytics-using/030.png)

### <a name="sort-and-filter-items"></a>Sortowanie i filtrowanie elementów
Posortować wyników, kliknij nagłówek kolumny. Kliknij ponownie, aby posortować inny sposób, a następnie kliknij przycisk innej czasu, aby powrócić do oryginalnej kolejność zwracanych przez zapytanie.

Użyj ikony filtru, aby zawęzić kryteria wyszukiwania.

![Sortowanie i filtrowanie kolumn](./media/app-insights-analytics-using/040.png)

### <a name="group-items"></a>Grupowanie elementów
Aby posortować według więcej niż jedną kolumnę, użyj grupowania. Najpierw go włączyć, a następnie przeciągnij nagłówków kolumn na miejsce powyżej tabeli.

![Grupa](./media/app-insights-analytics-using/060.png)

### <a name="missing-some-results"></a>Brakuje niektórych wyników?

Jeśli uważasz, że nie występują wszystkie wyniki, które miały, istnieje kilka możliwych przyczyn.

* **Filtr zakresu czasu**. Domyślnie będą widoczne są tylko wyniki z ostatnich 24 godzin. Brak automatyczny filtr, który ogranicza zakres wyniki, które są pobierane z tabel źródłowych. 

    Można jednak zmienić zakres czasu filtru przy użyciu menu rozwijanego.

    Lub zakresie automatycznego można zastąpić, umieszczając w niej własnych [ `where  ... timestamp ...` klauzuli](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) do zapytania. Na przykład:

    `requests | where timestamp > ago('2d')`

* **Limit wyników**. Ma limitu 10 wierszy k na wyniki zwrócone z portalu. Ostrzeżenie pokazuje, jeśli można przejść przekracza limit. Jeśli tak się stanie, sortowanie wyników w tabeli nie będzie zawsze pokazywać wszystkie rzeczywiste pierwszego lub ostatniego wyniki. 

    Dobrym rozwiązaniem, aby uniknąć naciśnięcie limit jest. Użyj filtru zakres czasu lub używać operatorów, takich jak:

  * [100 najpopularniejszych przez sygnatury czasowej](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator) 
  * [podejmij 100](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/take-operator)
  * [Podsumowanie ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) 
  * [gdzie sygnatury czasowej > ago(3d)](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator)

(Więcej niż 10 KB wierszy chcesz? Należy rozważyć użycie [eksportu ciągłego](app-insights-export-telemetry.md) zamiast tego. Analiza jest przeznaczona dla analizy, a nie podczas pobierania danych pierwotnych).

## <a name="diagrams"></a>Diagramy
Wybierz typ diagramu, który chcesz:

![Wybierz typ diagramu](./media/app-insights-analytics-using/230.png)

Jeśli masz kilka kolumn prawego typów można x i osi y, a kolumna wymiarów, aby podzielić wyniki według.

Domyślnie wyniki są początkowo wyświetlane jako tabelę i ręcznie wybrać diagramu. Jednak można użyć [renderowania dyrektywy](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) na końcu zapytania, aby wybrać diagram.

### <a name="analytics-diagnostics"></a>Diagnostyka analityka


Na timechart w przypadku nagłego kolekcji lub krok danych, mogą pojawić się zaznaczony punkt w wierszu. Oznacza to, Diagnostyka Analytics zidentyfikowane kombinację właściwości, które odfiltrowywania nagła zmiana. Kliknij punkt, aby uzyskać więcej szczegółów na temat filtr i aby zobaczyć filtrowana wersja. To może pomóc w identyfikacji, co powoduje zmianę. 

[Dowiedz się więcej o diagnostyce analityka](app-insights-analytics-diagnostics.md)


![Diagnostyka analityka](./media/app-insights-analytics-using/analytics-diagnostics.png)

## <a name="pin-to-dashboard"></a>Przypnij do pulpitu nawigacyjnego
Możesz przypinać diagramu lub tabeli do jednego z Twojej [udostępnionych pulpitów nawigacyjnych](app-insights-dashboards.md) — wystarczy kliknąć numer pin. 

![Kliknij przycisk kod pin](./media/app-insights-analytics-using/pin-01.png)

Oznacza to, że jeśli Podsumowując pulpicie nawigacyjnym, aby ułatwić sobie monitorowanie wydajności lub użycia usług sieci web mogą być dość złożone analizy obok innych metryk. 

Możesz przypinać tabeli do pulpitu nawigacyjnego, jeśli ma cztery lub mniejszą liczbę kolumn. Górne siedmiu wiersze są wyświetlane.

### <a name="dashboard-refresh"></a>Odświeżanie pulpitu nawigacyjnego
Wykres przypięty do pulpitu nawigacyjnego jest automatycznie odświeżany przez ponowne uruchomienie zapytania mniej więcej co godzinę. Można również kliknij przycisk Odśwież.

### <a name="automatic-simplifications"></a>Automatyczne uproszczenia

Niektóre uproszczenia są stosowane do wykresu, gdy przypiąć go do pulpitu nawigacyjnego.

**Czas ograniczeń:** zapytania są automatycznie ograniczony do ostatnich 14 dni. Efekt jest taki sam jak, jeśli kwerenda zawiera `where timestamp > ago(14d)`.

**Ograniczenie liczby bin:** podczas wyświetlania wykresu, który ma wiele odrębny bins (zazwyczaj wykres słupkowy), mniej wypełnione bins są automatycznie grupowane w pojedynczy "inne" bin. Na przykład tego zapytania:

    requests | summarize count_search = count() by client_CountryOrRegion

wygląda następująco w module analiz:

![Wykres z tail długa](./media/app-insights-analytics-using/pin-07.png)

Jednak gdy przypiąć go do pulpitu nawigacyjnego wygląda następująco:

![Wykres z ograniczoną bins](./media/app-insights-analytics-using/pin-08.png)

## <a name="export-to-excel"></a>Eksportuj do programu Excel
Po uruchomieniu kwerendy, można pobrać pliku CSV. Kliknij przycisk **wyeksportować Excel**.

## <a name="export-to-power-bi"></a>Eksport do usługi Power BI
Umieść kursor w zapytaniu i wybierz polecenie **wyeksportować usługi Power BI**.

![Eksportowanie z Analytics z usługą Power BI](./media/app-insights-analytics-using/240.png)

Możesz uruchomić zapytanie w usłudze Power BI. Można ustawić, aby odświeżyć zgodnie z harmonogramem.

Usługa Power BI można tworzyć pulpity nawigacyjne, które zbieranie danych z różnych źródeł.

[Dowiedz się więcej na temat eksportowania do usługi Power BI](app-insights-export-power-bi.md)

## <a name="deep-link"></a>Głębokie łącze

Uzyskaj link w obszarze **eksportu, link udziału** wysyłanej do innego użytkownika. Podany użytkownik ma [dostępu do tej grupy zasobów](app-insights-resources-roles-access-control.md), zapytanie zostanie otwarty w Interfejsie użytkownika Analytics.

(Łącza, tekst zapytania jest umieszczany po "? q =" gzip skompresowane i algorytmem base-64. Można napisać kod, aby wygenerować głębokiego łącza, które zapewniają użytkownikom. Jednak zalecanym sposobem Uruchom Analytics z kodu jest przy użyciu [interfejsu API REST](https://dev.applicationinsights.io/).)


## <a name="automation"></a>Automatyzacja

Użyj [interfejsu API REST dostępu do danych](https://dev.applicationinsights.io/) do wykonywania kwerend Analytics. [Na przykład](https://dev.applicationinsights.io/apiexplorer/query?appId=DEMO_APP&apiKey=DEMO_KEY&query=requests%0A%7C%20where%20timestamp%20%3E%3D%20ago%2824h%29%0A%7C%20count) (przy użyciu programu PowerShell):

```PS
curl "https://api.applicationinsights.io/beta/apps/DEMO_APP/query?query=requests%7C%20where%20timestamp%20%3E%3D%20ago(24h)%7C%20count" -H "x-api-key: DEMO_KEY"
```

W przeciwieństwie do interfejsu użytkownika analizy interfejsu API REST nie powoduje automatycznego dodania podlega żadnym ograniczeniom sygnatury czasowej do zapytania. Pamiętaj, aby dodać własne-klauzuli where, aby uniknąć pobierania ogromnych odpowiedzi.



## <a name="import-data"></a>Importowanie danych

Dane można zaimportować z pliku CSV. Zwykle jest używane do importowania danych statycznych, które można połączyć z tabelami z telemetrii. 

Na przykład uwierzytelnieni użytkownicy są oznaczane w obrębie telemetrii alias lub zaciemnionego identyfikator, można zaimportować tabelę, która mapuje aliasów do rzeczywistej nazwy. Wykonując sprzężenie na dane telemetryczne żądania, można zidentyfikować użytkowników według ich rzeczywistym nazw w raportach analizy.

### <a name="define-your-data-schema"></a>Zdefiniuj schemat danych

1. Kliknij przycisk **ustawienia** (u góry po lewej), a następnie **źródeł danych**. 
2. Dodawanie źródła danych, postępując zgodnie z instrukcjami. Zostanie wyświetlona prośba o podanie przykładowych danych, który powinien zawierać co najmniej 10 wierszy. Następnie Popraw schemat.

Określa źródło danych, które można następnie zaimportować poszczególnych tabel.

### <a name="import-a-table"></a>Importowanie tabeli

1. Otwórz z definicji źródła danych z listy.
2. Kliknij przycisk "Przekaż", a następnie postępuj zgodnie z instrukcjami, aby przekazać tabeli. Obejmuje to wywołanie interfejsu API REST i dlatego jest łatwy do automatyzacji. 

Tabela jest teraz dostępna do użycia w zapytaniach Analytics. Zostanie wyświetlony na analityka 

### <a name="use-the-table"></a>Skorzystaj z tabeli

Załóżmy, że Twoje definicji źródła danych jest nazywany `usermap`, i ma dwa pola `realName` i `user_AuthenticatedId`. `requests` Tabeli również zawiera pole o nazwie `user_AuthenticatedId`, dzięki czemu łatwiej je dołączyć:

```AIQL

    requests
    | where notempty(user_AuthenticatedId) | take 10
    | join kind=leftouter ( usermap ) on user_AuthenticatedId 
```
Tabeli wynikowej żądania ma dodatkowe kolumny `realName`.

### <a name="import-from-logstash"></a>Importuj z LogStash

Jeśli używasz [LogStash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html), analizy służy do badania dzienników. Użyj [wtyczkę, która powoduje przekazanie w potoku danych do analizy](https://github.com/Microsoft/logstash-output-application-insights). 

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

