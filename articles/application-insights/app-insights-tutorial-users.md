---
title: Zrozumienie klientów w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Samouczek dotyczący programu Azure Application Insights, aby zrozumieć, jak klienci korzystają z aplikacji.
keywords: ''
services: application-insights
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: db61c300ad82270e59d315fa3372d9e4390c7a21
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2017
ms.locfileid: "24099025"
---
# <a name="use-azure-application-insights-to-understand-how-customers-are-using-your-application"></a>Użyj Azure Application Insights, aby zrozumieć, jak klienci używają aplikacji

Azure Application Insights zbiera informacje o użyciu, aby lepiej zrozumieć, jak użytkownicy korzystają z Twojej aplikacji.  Ten samouczek przedstawia różne zasoby, które można analizować te informacje.  Dowiesz się jak:

> [!div class="checklist"]
> * Analizować szczegółowe informacje dotyczące użytkowników uzyskujących dostęp do aplikacji
> * Użyj informacji o sesji, aby przeanalizować, jak klienci korzystają z aplikacji
> * Zdefiniuj Lejki, które pozwalają porównać Twojej aktywności użytkownika odpowiednią do ich rzeczywistego działania 
> * Utwórz skoroszyt skonsolidować wizualizacje i zapytań do pojedynczego dokumentu
> * Grupy użytkowników podobne do przeanalizowania razem
> * Więcej informacji o użytkownikach, którzy są wracając do aplikacji
> * Sprawdź, jak użytkownicy nawigują między aplikacji


## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- Zainstaluj program [Visual Studio 2017](https://www.visualstudio.com/downloads/) z następującymi pakietami roboczymi:
    - Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych
    - Tworzenie aplikacji na platformie Azure
- Pobierz i zainstaluj [Visual Studio Debugger migawki](http://aka.ms/snapshotdebugger).
- Wdrażanie aplikacji .NET na platformie Azure i [włączyć zestaw SDK usługi Application Insights](app-insights-asp-net.md). 
- [Wysłać dane telemetryczne z aplikacji](app-insights-usage-overview.md#send-telemetry-from-your-app) dodawania widoków niestandardowych zdarzeń/strony
- Wyślij [kontekstu użytkownika](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context) śledzenia, co użytkownik wykona wraz z upływem czasu i pełni wykorzystywać funkcje użycia.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Zaloguj się do portalu Azure pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="get-information-about-your-users"></a>Uzyskać informacje na temat użytkowników
**Użytkowników** panel umożliwia zrozumieć ważne informacje dotyczące użytkowników w różny sposób. Umożliwia Panel zrozumieć takie informacje jak gdzie użytkownicy nawiązują połączenie z szczegóły swoich klientów, a obszary aplikacji uzyskiwany jest dostęp. 

1. Wybierz **usługi Application Insights** , a następnie wybierz subskrypcję.
2. Wybierz **użytkowników** w menu.
3. Widok domyślny pokazuje liczbę unikatowych użytkowników podłączonych do aplikacji w ciągu ostatnich 24 godzin.  Możesz zmienić przedział czasu i ustawić różnych kryteriów filtrowania tych informacji.

    ![Konstruktor kwerend](media\app-insights-tutorial-users\QueryBuilder.png)

6. Kliknij przycisk **podczas** listy rozwijanej i zmień przedział czasu na 7 dni.  Zwiększa to dane zawarte w różnych wykresów w panelu.

    ![Zmień zakres czasu](media\app-insights-tutorial-users\TimeRange.png)

4. Kliknij przycisk **podział według** listy rozwijanej, aby dodać podział według właściwości użytkownika do wykresu.  Wybierz **kraj lub region**.  Wykres zawiera te same dane, ale umożliwia wyświetlenie podział liczbę użytkowników, dla każdego kraju.

    ![Wykres kraj lub Region](media\app-insights-tutorial-users\CountryorRegion.png)

5. Umieść kursor na różnych słupki na wykresie i należy pamiętać, że liczba dla każdego kraju odzwierciedla reprezentowany przez ten pasek tylko przedział czasu.
6. Obejrzyj **Insights** kolumnę po prawej, które wykonywać analizy na podstawie danych użytkownika.  Zapewnia informacje, takie jak liczba unikatowych sesji w ciągu okresu i rekordy z wspólnych właściwości, które tworzą znaczących danych użytkownika 

    ![Informacje na temat technologii kolumny](media\app-insights-tutorial-users\insights.png)


## <a name="analyze-user-sessions"></a>Analizowanie sesji użytkownika
**Sesji** panelu jest podobny do **użytkowników** panelu.  Gdzie **użytkowników** pomaga w zrozumieniu szczegółów dotyczących użytkowników, uzyskiwanie dostępu do aplikacji, **sesji** pomaga w zrozumieniu sposobu używania aplikacji przez użytkowników.  

1. Wybierz **sesji** w menu.
2. Obejrzyj wykres i należy pamiętać, że mają te same opcje służące do filtrowania i podziału danych jako w **użytkowników** panelu.

    ![Konstruktor kwerend sesji](media\app-insights-tutorial-users\SessionsBuilder.png)

3. **Próbki te sesje** w okienku po prawej stronie listy sesji, które zawierają dużą liczbę zdarzeń.  Są to interesujące sesji do analizy.

    ![Przykładowe te sesje](media\app-insights-tutorial-users\SessionsSample.png)

4. Kliknij jedną z sesji, aby wyświetlić jego **osi czasu sesji**, który zawiera wszystkie akcje w sesji.  Może to pomóc w identyfikacji informacje, takie jak sesje z dużej liczby wyjątków.

    ![Oś czasu sesji](media\app-insights-tutorial-users\SessionsTimeline.png)

## <a name="group-together-similar-users"></a>Grupowanie podobnych użytkowników
A **kohorty** jest zestawem groupd użytkowników o podobnej charakterystyce.  Można użyć stado można filtrować dane w innych paneli, co umożliwia analizowania określonych grup użytkowników.  Na przykład można analizować tylko użytkownicy, którzy ukończone zakupu.

1.  Wybierz **stado** w menu.
2.  Kliknij przycisk **nowy** do utworzenia nowego kohorty.
3.  Wybierz **kto używane** listy rozwijanej i wybierz akcję.  Tylko użytkownicy, którzy wykonać tej akcji w obrębie przedział czasu raportu zostaną uwzględnione.

    ![Kohorty, który wykonał określone akcje](media\app-insights-tutorial-users\CohortsDropdown.png)

4.  Wybierz **użytkowników** w menu.
5.  W **Pokaż** listy rozwijanej wybierz kohorty właśnie utworzony.  Dane wykresu są ograniczone do tych użytkowników.

    ![Kohorty w narzędziu użytkownicy](media\app-insights-tutorial-users\UsersCohort.png)


## <a name="compare-desired-activity-to-reality"></a>Porównanie odpowiednie działanie w rzeczywistości
Gdy poprzednie panele są fokus na to, co zostało użytkowników aplikacji, **Lejki** skupić się na co ma być czy użytkowników.  Rozdzielacz reprezentuje zestaw kroków w aplikacji i procent użytkowników, którzy przenoszenia między krokami.  Na przykład można utworzyć rozdzielacz mierzy procent użytkowników łączących się z aplikacji wyszukiwania produktu.  Możesz sprawdzić procent użytkowników, którzy dodać tego produktu do koszyka zakupów i procent osób zakończyć zakupu.

1. Wybierz **Lejki** menu, a następnie kliknij polecenie **nowy**. 

    ![](media\app-insights-tutorial-users\funnelsnew.png)

2. Wpisz w **Lejkowy nazwa**.
3. Utwórz rozdzielacz z co najmniej dwa kroki, wybierając akcję dla każdego kroku.  Listę akcji jest oparty na podstawie danych użycia zbieranych przez usługę Application Insights.

    ![](media\app-insights-tutorial-users\funnelsedit.png)

4. Kliknij przycisk **zapisać** Aby zapisać lejka, a następnie przejrzyj wyniki.  W oknie po prawej stronie lejka wyświetlane najbardziej typowe zdarzenia przed pierwsze działanie i po ostatniej aktywności ułatwią zrozumienie tendencji użytkownika wokół określonej sekwencji.

    ![](media\app-insights-tutorial-users\funnelsright.png)


## <a name="learn-which-customers-return"></a>Dowiedz się, którzy zwracać
**Przechowywania** pomaga w zrozumieniu użytkowników, którzy są powracające do aplikacji.  

1. Wybierz **przechowywania** w menu.
2. Domyślnie przeanalizowane informacje zawiera użytkowników, którzy wykonać dowolną akcję, a następnie zwracany do wykonywania dowolnych akcji.  Ten filtr, można zmienić na dowolnym include, na przykład tylko tych użytkowników, którzy zwracane po zakończeniu zakupu.

    ![](media\app-insights-tutorial-users\retentionquery.png)

3. Zwracanie użytkowników spełniających kryteria są wyświetlane w graficznym i tabeli formularza dla innego czasu trwania.  Typowy wzorzec jest stopniowego upuszczania zwracany użytkowników w czasie.  Gwałtowny spadek z jednego okresu do następnego może podnieść znaczenie. 

    ![](media\app-insights-tutorial-users\retentiongraph.png)

## <a name="analyze-user-navigation"></a>Analizowanie nawigacji użytkownika
A **przepływu użytkownika** wizualizuje, jak użytkownicy nawigują między stronami i funkcji aplikacji.  Pomaga to w odpowiedzi na pytania, takie jak gdzie zwykle przenieść użytkowników z określonej strony, jak one zazwyczaj zakończyć działanie aplikacji i czy istnieją wszystkie akcje, które są regularnie powtarzać.

1.  Wybierz **użytkownika przepływów** w menu.
2.  Kliknij przycisk **nowy** utworzyć nowy przepływ użytkownika, a następnie kliknij przycisk **Edytuj** Aby edytować jego szczegóły.
3.  Zwiększ **zakres czasu** do 7 dni, a następnie wybierz zdarzenie początkowej.  Przepływ zostanie śledzenia sesji użytkownika, rozpoczynających się od tego zdarzenia.

    ![](media\app-insights-tutorial-users\flowsedit.png)

4.  Przepływ użytkownika jest wyświetlane i można zobaczyć ścieżki innego użytkownika i liczby sesji.  Niebieski linie wskazują akcję użytkownika wykonywane po bieżącej akcji.  Czerwona linia wskazuje koniec sesji użytkownika.

    ![](media\app-insights-tutorial-users\flows.png)

5.  Usuń zdarzenia z przepływu, klikając przycisk **x** w rogu akcji, a następnie kliknij przycisk **Tworzenie wykresu**.  Wykres jest rysowany ponownie z uwzględnieniem dowolnych wystąpień tego zdarzenia usunięte.  Kliknij przycisk **Edytuj** aby zobaczyć, że zdarzenie zostało teraz dodane do **wykluczone zdarzenia**.

    ![](media\app-insights-tutorial-users\flowsexclude.png)

## <a name="consolidate-usage-data"></a>Konsolidowanie danych użycia
**Skoroszyty** łączyć wizualizacje danych, zapytania analityczne i tekstu w dokumentach interaktywnego.  Skoroszyty służy do grupowania typowych informacji o użyciu, Konsoliduj określonego zdarzenia lub raportować do zespołu na użycie aplikacji.

1.  Wybierz **skoroszytów** w menu.
2.  Kliknij przycisk **nowy** Aby utworzyć nowy skoroszyt.
3.  Zapytanie jest już pod warunkiem, że zawierającej wszystkie dane użycia w ostatni dzień wyświetlany w postaci wykresu słupkowego.  Użyj tego zapytania, ręcznie go edytować lub kliknij przycisk **przykładowe zapytania** można wybierać inne przydatne zapytania.

    ![](media\app-insights-tutorial-users\samplequeries.png)

4.  Kliknij przycisk **zakończeniu edytowania**.
5.  Kliknij przycisk **Edytuj** w górnym okienku można edytować tekst w górnej części skoroszytu.  To jest sformatowany przy użyciu języka znaczników markdown.

    ![](media\app-insights-tutorial-users\markdown.png)

6.  Kliknij przycisk **dodawania użytkowników** można dodać, wykres o informacje o użytkowniku.  Edytuj szczegóły wykresu, a następnie kliknij przycisk **zakończeniu edytowania** go zapisać.


## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już sposobu analizowania użytkowników, przejdź do następnego samouczkiem, aby dowiedzieć się, jak tworzyć niestandardowe pulpity nawigacyjne, łączące tego informacje z innych przydatnych danych aplikacji.

> [!div class="nextstepaction"]
> [Tworzyć niestandardowe pulpity nawigacyjne](app-insights-tutorial-dashboards.md)
