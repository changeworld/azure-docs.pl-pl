---
title: Korzystanie z analizy — wyszukiwanie zaawansowane narzędzia usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: 'Korzystanie z analizy, Narzędzia zaawansowane wyszukiwanie diagnostyczne usługi Application Insights. '
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
ms.date: 07/02/2018
ms.reviewer: danha
ms.author: mbullwin
ms.openlocfilehash: aa86e2f3b1fb147ab167c948475a5207693143c2
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341566"
---
# <a name="using-analytics-in-application-insights"></a>Za pomocą analizy w usłudze Application Insights
[Analiza](app-insights-analytics.md) jest funkcją zaawansowanej funkcji przeszukiwania [usługi Application Insights](app-insights-overview.md). Strony te opisują język zapytań usługi Log Analytics.

* **[Obejrzyj klip wideo](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Testowanie usługi Analytics na naszych symulowane dane](https://analytics.applicationinsights.io/demo)**  aplikacji nie jest wysyłania danych do usługi Application Insights jeszcze.

## <a name="open-analytics"></a>Otwórz analizę
Zasób macierzysty swojej aplikacji w usłudze Application Insights kliknij analizy.

![Otwórz portal.azure.com otwórz zasób usługi Application Insights, a następnie kliknij przycisk Analiza.](./media/app-insights-analytics-using/001.png)

Samouczek wbudowane zapewnia kilka sugestii dotyczących co można zrobić.

Brak [bardziej rozległe samouczek w tym miejscu](app-insights-analytics-tour.md).

## <a name="query-your-telemetry"></a>Odpytanie telemetrii
### <a name="write-a-query"></a>Napisz zapytanie
![Wyświetlanie schematu](./media/app-insights-analytics-using/150.png)

Zaczynają się od nazwy tabel, na liście po lewej stronie (lub [zakres](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/range-operator) lub [Unii](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/union-operator) operatory). Użyj `|` umożliwia tworzenie potoku z [operatory](https://docs.loganalytics.io/docs/Learn/References/Useful-operators). 

Funkcja IntelliSense wyświetli monit o przy użyciu operatorów i elementy wyrażenia, które są dostępne. Kliknij ikonę informacji (lub naciśnij klawisze CTRL + SPACJA) Aby uzyskać opis dłużej i przykłady sposobów użycia każdego elementu.

Zobacz [samouczek języka Analytics](app-insights-analytics-tour.md) i [Skorowidz języka](app-insights-analytics-reference.md).

### <a name="run-a-query"></a>Uruchom zapytanie
![Uruchamianie zapytania](./media/app-insights-analytics-using/130.png)

1. Można użyć pojedynczego podziały wierszy w zapytaniu.
2. Umieść kursor wewnątrz lub na końcu zapytania, które chcesz uruchomić.
3. Sprawdź przedział czasu zapytania. (Można zmienić lub zmienić, dołączając własne [ `where...timestamp...` ](https://docs.loganalytics.io/docs/Learn/Tutorials/Date-and-time-operations) klauzuli w zapytaniu.)
3. Kliknij Go, aby uruchomić zapytanie.
4. Nie umieszczaj puste wiersze w zapytaniu. Kilka zapytań rozdzielonych można przechowywać w jednej karty zapytania, rozdzielając je średnikami pustych wierszy. Uruchamia tylko zapytania, które zawiera kursora.

### <a name="save-a-query"></a>Zapisz zapytanie
![Zapisywanie zapytania](./media/app-insights-analytics-using/140.png)

1. Zapisz bieżący plik zapytania.
2. Otwórz plik zapisanego zapytania.
3. Utwórz nowy plik zapytania.

## <a name="see-the-details"></a>Zobacz szczegóły
Rozwiń wszystkie wiersze w wynikach, aby wyświetlić jego pełną listę właściwości. Można rozwinąć dowolnej właściwości, która jest strukturą wartość — na przykład, wymiary niestandardowe lub stosu w wyjątku.

![Rozwiń węzeł wiersza](./media/app-insights-analytics-using/070.png)

## <a name="arrange-the-results"></a>Rozmieszczenie wyników
Można sortować, filtrowanie, stronicowanie i grupowanie wyników zwrócone w wyniku zapytania.

> [!NOTE]
> Sortowanie, grupowanie i filtrowanie w przeglądarce nie uruchamiaj ponownie zapytanie. Rozmieszczanie ich tylko wyniki zwrócone przez ostatnie zapytanie. 
> 
> Aby wykonać te zadania na serwerze, zanim wyniki są zwracane, napisz zapytanie za pomocą [sortowania](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator), [Podsumuj](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) i [gdzie](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) operatorów.
> 
> 

Wybierz kolumny, które chcesz zobaczyć, przeciągnij nagłówek kolumny w celu ich rozmieszczenia i zmienianie rozmiaru kolumn, przeciągając jego obramowania.

![Rozmieść kolumny](./media/app-insights-analytics-using/030.png)

### <a name="sort-and-filter-items"></a>Sortowanie i filtrowanie elementów
Wyniki można sortować, klikając nagłówek kolumny. Kliknij ponownie, aby posortować odwrotnie, a następnie kliknij przycisk trzecią czasu, aby powrócić do oryginalnej kolejności zwracanej przez zapytanie.

Użyj ikony filtru, aby zawęzić kryteria wyszukiwania.

![Sortowanie i filtrowanie kolumn](./media/app-insights-analytics-using/040.png)

### <a name="group-items"></a>Grupowanie elementów
Aby posortować według więcej niż jedną kolumnę, należy użyć grupowania. Najpierw je włączyć, a następnie przeciągnij nagłówków kolumn, w miejsce powyższej tabeli.

![Grupa](./media/app-insights-analytics-using/060.png)

### <a name="missing-some-results"></a>Brakuje niektórych wyników?

Jeśli uważasz, że nie występują uzyskuje oczekiwane rezultaty, istnieje kilka możliwych przyczyn.

* **Filtr zakresu czasu**. Domyślnie widoczne są tylko wyniki z ostatnich 24 godzin. Istnieje automatyczny filtr, który ogranicza zakres wyników, które są pobierane z tabel źródłowych. 

    Jednakże, można zmienić zakres czasu filtru, korzystając z menu rozwijanego.

    Lub możesz zastąpić automatyczne zakresu, umieszczając własne [ `where  ... timestamp ...` klauzuli](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) do zapytania. Na przykład:

    `requests | where timestamp > ago('2d')`

* **Limit wyników**. Ma limitu około 10 tysięcy wierszy na wyniki zwrócone z portalu. Ostrzeżenie jest wyświetlane Jeśli przekroczysz limit. Jeśli tak się stanie, sortowanie wyniki w tabeli nie zawsze pokazano wszystkie rzeczywiste pierwsze lub ostatnie wyniki. 

    Jest dobrą praktyką, aby uniknąć osiągnięcia limitu. Użyj filtru zakresu czasu lub użyć operatorów, takich jak:

  * [100 czołowych według sygnatur czasowych](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator) 
  * [Wykonaj 100](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/take-operator)
  * [Podsumowanie ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) 
  * [gdy sygnatura czasowa > ago(3d)](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator)

(Potrzebujesz więcej niż 10 tysięcy wierszy? Należy rozważyć użycie [eksportu ciągłego](app-insights-export-telemetry.md) zamiast tego. Analiza jest przeznaczona do analizy, a nie podczas pobierania danych pierwotnych.)

## <a name="diagrams"></a>Diagramy
Wybierz typ diagramu, który chcesz:

![Wybierz typ diagramu](./media/app-insights-analytics-using/230.png)

Jeśli masz kilka kolumn odpowiednie typy, można x i osi y i wymiarów, aby podzielić wyniki według kolumny.

Domyślnie wyniki początkowo są wyświetlane jako tabelę, a następnie ręcznie Wybierz diagram. Ale można użyć [renderowania dyrektywy](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) na końcu zapytania, aby wybrać diagram.

### <a name="analytics-diagnostics"></a>Diagnostyka usługi Analytics


Na wykres czasu w przypadku nagłym skokiem lub krok w danych, mogą pojawić się wyróżniony punktu, w wierszu. Oznacza to, czy diagnostyki Analytics zidentyfikowała kombinacji właściwości, które odfiltrować nagła zmiana. Kliknij punkt, aby uzyskać więcej szczegółów na temat filtr i wyświetlić filtrowanej wersji. To może pomóc w zidentyfikowaniu, co spowodowało zmianę. 

[Dowiedz się więcej o diagnostyce analizy](app-insights-analytics-diagnostics.md)


![Diagnostyka usługi Analytics](./media/app-insights-analytics-using/analytics-diagnostics.png)

## <a name="pin-to-dashboard"></a>Przypnij do pulpitu nawigacyjnego
Możesz przypiąć diagramu lub tabeli do jednego z Twojej [udostępnione pulpity nawigacyjne](app-insights-dashboards.md) — wystarczy kliknąć numeru pin. 

![Kliknij numer pin](./media/app-insights-analytics-using/pin-01.png)

Oznacza to, że jeśli Podsumowując pulpit nawigacyjny, aby ułatwić monitorowanie wydajności lub użycia usługi sieci web mogą być bardzo złożone analizy wraz z innymi metrykami. 

Możesz przypiąć tabelę do pulpitu nawigacyjnego, jeśli ma cztery lub mniejszą liczbę kolumn. Wyświetlanych jest tylko pierwszych wierszy siedem.

### <a name="dashboard-refresh"></a>Odświeżanie pulpitu nawigacyjnego
Wykres przypięty do pulpitu nawigacyjnego są odświeżane automatycznie przez ponowne uruchomienie zapytania mniej więcej co godzinę. Możesz również kliknąć przycisk Odśwież.

### <a name="automatic-simplifications"></a>Automatyczne definiowaniu

Niektóre definiowaniu są stosowane do wykresu, gdy możesz przypiąć do pulpitu nawigacyjnego.

**Czas ograniczania:** zapytania są automatycznie ograniczone do ostatnich 30 dni. Efekt jest taki sam, jak, jeśli kwerenda zawiera `where timestamp > ago(30d)`.

**Ograniczenie liczby bin:** Jeśli wykres, który ma wiele dyskretnych pojemników (zazwyczaj wykres słupkowy) są wyświetlane, mniej wypełnione pojemniki są automatycznie grupowane w jednym "inne" bin. Na przykład tego zapytania:

    requests | summarize count_search = count() by client_CountryOrRegion

wygląda to w usłudze Analytics:

![Wykres z długi ciąg](./media/app-insights-analytics-using/pin-07.png)

Jednak gdy możesz przypiąć do pulpitu nawigacyjnego, wygląda następująco:

![Wykres z ograniczoną pojemników](./media/app-insights-analytics-using/pin-08.png)

## <a name="export-to-excel"></a>Eksportuj do programu Excel
Po uruchomieniu kwerendy, możesz pobrać plik CSV. Kliknij przycisk **eksportowania programu Excel**.

## <a name="export-to-power-bi"></a>Eksport do usługi Power BI
Umieść kursor w zapytaniu, a następnie wybierz **eksportu usługi Power BI**.

![Eksportowanie z analizy do usługi Power BI](./media/app-insights-analytics-using/240.png)

Możesz uruchomić zapytanie w usłudze Power BI. Można ustawić, aby odświeżyć zgodnie z harmonogramem.

Usługa Power BI można tworzyć pulpity nawigacyjne, które łączą ze sobą dane z wielu różnych źródeł.

[Dowiedz się więcej na temat eksportowania do usługi Power BI](app-insights-export-power-bi.md)

## <a name="deep-link"></a>Link bezpośredni

Uzyskaj link w obszarze **eksportu, Udostępnij link** wysyłanych do innego użytkownika. Podany użytkownik ma [dostęp do swojej grupy zasobów](app-insights-resources-roles-access-control.md), zapytanie zostanie otwarty w Interfejsie użytkownika usługi Analytics.

(W linku, tekst zapytania pojawia się po "? q =" kompresji gzip oraz szyfrowany algorytmem base-64. Można napisać kod na potrzeby generowania linków bezpośrednich, które zapewniają użytkownikom. Jednak jest to zalecany sposób wykonywać analizy z kodu za pomocą [interfejsu API REST](https://dev.applicationinsights.io/).)


## <a name="automation"></a>Automatyzacja

Użyj [interfejsu API REST dostępu do danych](https://dev.applicationinsights.io/) uruchamianie zapytań analitycznych. [Na przykład](https://dev.applicationinsights.io/apiexplorer/query?appId=DEMO_APP&apiKey=DEMO_KEY&query=requests%0A%7C%20where%20timestamp%20%3E%3D%20ago%2824h%29%0A%7C%20count) (przy użyciu programu PowerShell):

```PS
curl "https://api.applicationinsights.io/beta/apps/DEMO_APP/query?query=requests%7C%20where%20timestamp%20%3E%3D%20ago(24h)%7C%20count" -H "x-api-key: DEMO_KEY"
```

W przeciwieństwie do analizy interfejsu użytkownika interfejs API REST nie powoduje automatycznego dodania żadnym ograniczeniom sygnatury czasowej do zapytań. Pamiętaj, aby dodać własne-klauzuli where, aby uniknąć ogromna odpowiedzi.



## <a name="import-data"></a>Importowanie danych

Dane można zaimportować z pliku CSV. Zwykle jest używane do importowania danych statycznych, które można połączyć się z tabelami ze swojej telemetrii. 

Na przykład uwierzytelnieni użytkownicy są identyfikowane w danych telemetrycznych przez alias lub zaciemnionego identyfikator, można zaimportować tabelę, która mapuje rzeczywiste nazwy aliasów. Wykonując sprzężenie na dane telemetryczne żądania, można zidentyfikować użytkowników według rzeczywistych nazw w raportach analizy.

### <a name="define-your-data-schema"></a>Definiowania schematu danych

1. Kliknij przycisk **ustawienia** (w lewym górnym rogu) i następnie **źródeł danych**. 
2. Dodawanie źródła danych, postępując zgodnie z instrukcjami. Zostanie wyświetlony monit o podanie przykładowych danych, który powinien zawierać co najmniej 10 wierszy. Następnie Rozwiąż schematu.

Definiuje ze źródłem danych, w którym można następnie zaimportować poszczególnych tabel.

### <a name="import-a-table"></a>Importuj tabelę

1. Otwórz Definicja źródła danych z listy.
2. Kliknij przycisk "Przekaż" i postępuj zgodnie z instrukcjami, aby przekazać w tabeli. Obejmuje to wywołanie interfejsu API REST, a więc jest łatwy do automatyzacji. 

Tabela jest teraz dostępny do użytku w zapytań analitycznych. Pojawi się w usłudze Analytics 

### <a name="use-the-table"></a>Skorzystaj z tabeli

Załóżmy, że definicja źródła danych jest nazywany `usermap`, a ma dwa pola, `realName` i `user_AuthenticatedId`. `requests` Tabela również zawiera pole o nazwie `user_AuthenticatedId`, dzięki czemu można łatwo do dołączenia do nich:

```AIQL

    requests
    | where notempty(user_AuthenticatedId) | take 10
    | join kind=leftouter ( usermap ) on user_AuthenticatedId 
```
Tabela wynikowa żądań, które zawiera dodatkową kolumnę `realName`.

### <a name="import-from-logstash"></a>Importuj z programu LogStash

Jeśli używasz [LogStash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html), Analytics służy do wykonywania zapytań dzienników. Użyj [wtyczkę, która powoduje przekazanie w potoku danych do analizy](https://github.com/Microsoft/logstash-output-application-insights). 

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

