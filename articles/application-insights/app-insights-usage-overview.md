---
title: Analiza użycia za pomocą usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Dowiedz się, użytkownicy i ich działania z aplikacją.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 10/10/2017
ms.author: mbullwin
ms.openlocfilehash: 79f4f77f08653c40591137268b87dc2feeb0acf6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="usage-analysis-with-application-insights"></a>Analiza użycia za pomocą usługi Application Insights

Funkcje sieci web lub aplikacji mobilnej są najbardziej popularnych? Czy użytkownicy osiągnąć cele związane z aplikacją? Czy one usunąć w szczególności, i zwracają później?  [Azure Application Insights](app-insights-overview.md) pomaga uzyskać zaawansowane wgląd w sposób użytkownicy korzystają z aplikacji. Za każdym razem, gdy aktualizacji aplikacji, można ocenić, jak działa dla użytkowników. Z tym wiedzy możesz wprowadzić decyzje dotyczące Twojej dalej programistycznych opartych na danych.

## <a name="send-telemetry-from-your-app"></a>Wysłać dane telemetryczne z aplikacji

Najlepsze środowisko są uzyskiwane przez zainstalowanie usługi Application Insights, zarówno w kodzie serwera aplikacji, jak i na stronach sieci web. Składniki klienta i serwera aplikacji wysłać dane telemetryczne, wróć do portalu Azure do analizy.

1. **Kod serwera:** zainstalować moduł odpowiednie dla Twojej [ASP.NET](app-insights-asp-net.md), [Azure](app-insights-azure.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md), lub [innych](app-insights-platforms.md) aplikacji.

    * *Nie chcesz zainstalować kod serwera? Po prostu [tworzenie zasobów Azure Application Insights](app-insights-create-new-resource.md).*

2. **Kod strony sieci Web:** Otwórz [portalu Azure](https://portal.azure.com), otwórz zasobu usługi Application Insights dla aplikacji, a następnie **wprowadzenie > Monitorowanie i diagnozowanie po stronie klienta**. 

    ![Skopiuj skrypt do Nagłówek strony wzorcowej sieci web.](./media/app-insights-usage-overview/02-monitor-web-page.png)

3. **Kod aplikacji mobilnej:** Center zestawu SDK aplikacji umożliwia zbieranie zdarzeń z aplikacji, a następnie wysyła kopie tych zdarzeń do usługi Application Insights do analizy przez [czynności przedstawione w tym przewodniku](app-insights-mobile-center-quickstart.md).

4. **Pobierz dane telemetryczne:** uruchomić projekt w trybie debugowania na kilka minut, a następnie sprawdź wyniki w bloku omówienie w usłudze Application Insights.

    Publikowanie aplikacji w celu monitorowania wydajności aplikacji i dowiedzieć się, co robią użytkownicy z aplikacją.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Uwzględnia Identyfikatora użytkownika i sesji w obrębie telemetrii
Aby śledzić użytkowników w czasie, usługi Application Insights wymaga do ich identyfikowania. Narzędzie zdarzenia jest tylko narzędzie użycia, które nie wymagają Identyfikatora użytkownika lub identyfikator sesji.

Rozpocznij wysyłanie użytkownika i identyfikatory sesji przy użyciu [ten proces](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Eksploruj demograficznych użycia oraz statystyki
Dowiedz się, gdy użytkownicy korzystają z aplikacji, jakie stron one jest najbardziej zainteresowani, gdzie znajdują się użytkownicy, jakie przeglądarek i systemów operacyjnych korzystają. 

Raporty użytkowników i sesji filtrować dane według stron lub zdarzeń niestandardowych, a segment je za pomocą właściwości, takie jak lokalizacja, środowisko i strony. Można także dodać własne filtry.

![Użytkownicy](./media/app-insights-usage-overview/users.png)  

Szczegółowe informacje, z prawej strony punktu interesujących wzorców w zestawie danych.  

* **Użytkowników** raport liczby unikatowych użytkowników, które uzyskują dostęp do stron w ramach Twojej wybranych okresów. W przypadku aplikacji sieci web użytkownicy są liczone przy użyciu plików cookie. Jeśli ktoś uzyskuje dostęp do witryny za pomocą różnych przeglądarkach lub komputerów klienckich, lub czyści ich plików cookie, a następnie będzie można zliczane więcej niż raz.
* **Sesji** raport zlicza sesje użytkowników, które uzyskują dostęp do witryny. Sesja jest okres aktywności przez użytkownika, został przerwany przez okres aktywności ponad pół godziny.

[Więcej informacji na temat narzędzia użytkownikami, sesjami i zdarzenia](app-insights-usage-segmentation.md)  

## <a name="page-views"></a>Wyświetlenia strony

W bloku użycia kliknij za pośrednictwem kafelkiem wyświetleń strony, aby uzyskać podział najpopularniejszych stron:

![W bloku Przegląd kliknij wykres widoków strony](./media/app-insights-usage-overview/05-games.png)

W powyższym przykładzie jest gry witryny sieci web. Wykresy firma Microsoft może natychmiast zobacz:

* Użycie nie ulepszona w zeszłym tygodniu. Może być powinniśmy pomyśleć o optymalizacji dla aparatów wyszukiwania?
* Tenisa jest najbardziej popularnym strona gier. Ta funkcja pozwala skupić się na dalszych poprawek do tej strony.
* Średnio odwiedzin strony tenisa około trzy razy w tygodniu. (Istnieją sesje około trzy razy więcej niż użytkowników.)
* Większość użytkowników w witrynie w tygodniu pracy USA, a w godzinach pracy. Być może należy udostępniamy przycisk "szybkie Ukryj" na stronie sieci web.
* [Adnotacje](app-insights-annotations.md) na wykresie Pokaż, gdy nowe wersje witryny sieci Web zostały wdrożone. Brak najnowszych wdrożeń ma zauważalnego wpływu na sposób użycia.

Co zrobić, jeśli chcesz zbadać ruch do witryny bardziej szczegółowo, takich jak dzielenie według właściwości niestandardowej, wysyłanych w jego dane telemetryczne wyświetleń strony witryny?

1. Otwórz **zdarzenia** narzędzia w menu zasobu usługi Application Insights. To narzędzie umożliwia analizowanie, ile strony widoków i zdarzeń niestandardowych, które zostały wysłane z aplikacji, w oparciu o różne opcje filtrowania, cohorting i segmentacji.
2. Na liście rozwijanej "Kto używane" Wybierz "Dowolny widok strony".
3. Na liście rozwijanej "Podział według" Wybierz właściwość, według której będzie dzielony Twoje dane telemetryczne wyświetleń strony.

## <a name="retention---how-many-users-come-back"></a>Przechowywania - wróć ilu użytkowników?

Przechowywania pomaga zrozumieć, jak często użytkownicy powrócić do używać aplikacji w oparciu stado użytkowników, którzy wykonać akcję biznesowych w przedziale czasu. 

- Zrozumienie, jakie określone funkcje, że użytkownicy przejdzie ponownie więcej niż inne 
- Na podstawie danych użytkownika rzeczywistych hipotez formularza 
- Określić, czy przechowywania jest problem w programie 

![Przechowywanie](./media/app-insights-usage-overview/retention.png) 

Formanty przechowywania u góry, umożliwiają definiowanie określonych zdarzeń i zakres czasu, aby obliczyć przechowywania. Wykres w środku zapewnia wizualną reprezentację ogólny procent przechowywania przez podany zakres czasu. Wykres w dolnej reprezentuje indywidualne przechowywania w danym okresie. Taki poziom szczegółowości pozwala zrozumieć, co robią użytkownicy i co może mieć wpływ na użytkowników zwracająca na bardziej szczegółowy poziom szczegółowości.  

[Więcej informacji na temat narzędzia przechowywania](app-insights-usage-retention.md)

## <a name="custom-business-events"></a>Zdarzenia niestandardowe biznesowe

Aby uzyskać przejrzysty użytkowników czy z aplikacją, jest przydatne do wstawiania wierszy kodu do dziennika zdarzeń niestandardowych. Zdarzenia te można śledzić żadnych czynności użytkownika szczegółowe akcje, takie jak kliknięcie określonego przycisków, aby bardziej znaczących zdarzenia biznesowe, takie jak dokonywania zakupu lub zastosowanie gier. 

Chociaż w niektórych przypadkach wyświetleń strony może reprezentować przydatne zdarzeń, nie jest PRAWDA ogólnie. Użytkownik może otworzyć stronę produktu bez zakupu produktu. 

Zdarzenia firmy można wykresu postępu użytkowników za pośrednictwem witryny. Można sprawdzić ich preferencje dotyczące różnych opcjach i gdzie one usunąć out lub ma trudności. Z tej wiedzy można podejmowania świadomych decyzji o priorytetów w rozwoju listę prac.

Zdarzenia mogą być rejestrowane po stronie klienta aplikacji:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Lub po stronie serwera:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

Wartości właściwości można dołączyć do tych zdarzeń, dzięki czemu można filtrować lub podzielić zdarzenia inspekcji w portalu. Ponadto standardowy zestaw właściwości jest dołączany do każdego zdarzenia, takie jak identyfikator użytkownika anonimowego, co pozwala na śledzenie sekwencję działań użytkownika.

Dowiedz się więcej o [zdarzeń niestandardowych](app-insights-api-custom-events-metrics.md#trackevent) i [właściwości](app-insights-api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Zdarzenia metod selekcji i projekcji

W menu Narzędzia użytkownikami, sesjami i zdarzenia możesz można kątami zdarzeń niestandardowych przez użytkownika, nazwę zdarzenia i właściwości.
![Użytkownicy](./media/app-insights-usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Projekt telemetrii w aplikacji

Podczas projektowania każdej funkcji aplikacji, należy wziąć pod uwagę, jak zamierzasz oceny sukcesu jej z użytkownikami. Zdecyduj, jakie zdarzenia biznesowe, które trzeba zarejestrować i kod wywołuje śledzenia dla tych zdarzeń w swojej aplikacji od początku.

## <a name="a--b-testing"></a>A | Testowanie B
Jeśli nie wiesz, który wariant funkcji więcej powiedzie się, zwolnij ich wprowadzania każdego dostępne dla różnych użytkowników. Mierzony sukces każdego z nich, a następnie przesuń do ujednoliconego wersji.

Dla tej metody możesz dołączyć wartości różnych właściwości wszystkie dane telemetryczne wysyłany przez poszczególnych wersji aplikacji. Możesz to zrobić przez definiowanie właściwości w active TelemetryContext. Te właściwości domyślne są dodawane do każdej telemetrii komunikat, który wysyła aplikacji — nie tylko komunikaty niestandardowe, ale także standardowe telemetrii.

W portalu usługi Application Insights filtrowania i podziału danych w wartości właściwości w taki sposób, aby porównać różne wersje.

Aby to zrobić, [Konfigurowanie inicjatora telemetrii](app-insights-api-filtering-sampling.md##add-properties-itelemetryinitializer):

```csharp


    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

W inicjatorze aplikacji sieci web, takich jak Global.asax.cs:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```

Wszystkie nowe TelemetryClients automatycznie dodają podaną wartość właściwości. Zdarzenia telemetrii poszczególnych można zastąpić wartości domyślne.

## <a name="next-steps"></a>Kolejne kroki
   - [Użytkownicy, sesje, zdarzenia](app-insights-usage-segmentation.md)
   - [Lejki](usage-funnels.md)
   - [Przechowywanie](app-insights-usage-retention.md)
   - [User Flows (Przepływy użytkowników)](app-insights-usage-flows.md)
   - [Skoroszyty](app-insights-usage-workbooks.md)
   - [Dodaj kontekstu użytkownika](app-insights-usage-send-user-context.md)
