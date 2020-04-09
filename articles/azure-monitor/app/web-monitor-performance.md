---
title: Monitorowanie kondycji i użycia aplikacji za pomocą usługi Application Insights
description: Wprowadzenie do usługi Application Insights. Analizuj użycie, dostępność i wydajność aplikacji lokalnych lub microsoft azure.
ms.topic: conceptual
ms.date: 05/10/2018
ms.reviewer: sdash
ms.openlocfilehash: 873fc41585c387246d83008a8f97d6c4d9a32c3b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985069"
---
# <a name="monitor-performance-in-web-applications"></a>Monitorowanie wydajności w aplikacjach internetowych


Upewnij się, że aplikacja działa dobrze i szybko dowiedzieć się o wszelkich błędach. [Usługa Application Insights][start] poinformuje Cię o wszelkich problemach z wydajnością i wyjątkami oraz pomoże Ci znaleźć i zdiagnozować główne przyczyny.

Usługa Application Insights może monitorować zarówno aplikacje i usługi internetowe Java, jak i ASP.NET, usługi WCF. Mogą one być hostowane lokalnie, na maszynach wirtualnych lub w witrynach sieci Web platformy Microsoft Azure. 

Po stronie klienta usługa Application Insights może przyjmować dane telemetryczne ze stron sieci web i wielu różnych urządzeń, w tym aplikacji z systemem iOS, Android i Ze Sklepu Windows.

## <a name="set-up-performance-monitoring"></a><a name="setup"></a>Konfigurowanie monitorowania wydajności
Jeśli usługa Application Insights nie została jeszcze dodana do projektu (oznacza to, że jeśli nie ma aplikacji ApplicationInsights.config), wybierz jeden z następujących sposobów rozpoczęcia pracy:

* [Aplikacje internetowe platformy ASP.NET](../../azure-monitor/app/asp-net.md)
  * [Dodawanie monitorowania wyjątków](../../azure-monitor/app/asp-net-exceptions.md)
  * [Dodawanie monitorowania zależności](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Aplikacje internetowe Java EE](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)

## <a name="exploring-performance-metrics"></a><a name="view"></a>Eksplorowanie wskaźników wydajności
W [witrynie Azure portal](https://portal.azure.com)przejdź do zasobu usługi Application Insights skonfigurowany dla aplikacji. Blok przeglądu zawiera podstawowe dane dotyczące wydajności:

Kliknij dowolny wykres, aby wyświetlić więcej szczegółów i wyświetlić wyniki przez dłuższy okres. Na przykład kliknij kafelek Żądania, a następnie wybierz zakres czasu:

![Kliknij, aby uzyskać więcej danych i wybrać zakres czasu](./media/web-monitor-performance/appinsights-48metrics.png)

Kliknij wykres, aby wybrać wyświetlane metryki, lub dodaj nowy wykres i wybierz jego dane:

![Kliknij wykres, aby wybrać dane](./media/web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Odznacz wszystkie metryki,** aby wyświetlić pełny dostępny wybór. Wskaźniki dzielą się na grupy; gdy wybrano dowolnego członka grupy, pojawiają się tylko inni członkowie tej grupy.

## <a name="what-does-it-all-mean-performance-tiles-and-reports"></a><a name="metrics"></a>Co to wszystko oznacza? Kafelki i raporty wydajności
Istnieją różne metryki wydajności, które można uzyskać. Zacznijmy od tych, które pojawiają się domyślnie w bloku aplikacji.

### <a name="requests"></a>Żądania
Liczba żądań HTTP odebranych w określonym czasie. Porównaj to z wynikami w innych raportach, aby zobaczyć, jak aplikacja zachowuje się w zależności od obciążenia.

Żądania HTTP obejmują wszystkie żądania GET lub POST dla stron, danych i obrazów.

Kliknij kafelek, aby uzyskać liczbę określonych adresów URL.

### <a name="average-response-time"></a>Średni czas reakcji
Mierzy czas między żądaniem sieci web wprowadzania aplikacji i odpowiedzi zwracane.

Punkty pokazują średnią ruchomą. Jeśli istnieje wiele żądań, mogą istnieć pewne, które odbiegają od średniej bez oczywistego szczytu lub spadku na wykresie.

Poszukaj niezwykłych szczytów. Ogólnie rzecz biorąc, należy spodziewać się wzrostu czasu reakcji wraz ze wzrostem liczby żądań. Jeśli wzrost jest nieproporcjonalny, aplikacja może być uderzenie limitu zasobów, takich jak procesor CPU lub pojemność usługi, której używa.

Kliknij kafelek, aby uzyskać czas dla określonych adresów URL.

![](./media/web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Najwolniejsze żądania
![](./media/web-monitor-performance/appinsights-44slowest.png)

Pokazuje, które żądania mogą wymagać dostrajania wydajności.

### <a name="failed-requests"></a>Żądania zakończone niepowodzeniem
![](./media/web-monitor-performance/appinsights-46failed.png)

Liczba żądań, które zgłosiły nieprzechowywczone wyjątki.

Kliknij kafelek, aby wyświetlić szczegóły określonych błędów, a następnie wybierz indywidualne żądanie, aby wyświetlić jego szczegóły. 

Do indywidualnej kontroli zatrzymywane są jedynie reprezentatywna próbka uschy.

### <a name="other-metrics"></a>Inne metryki
Aby zobaczyć, jakie inne metryki można wyświetlić, kliknij wykres, a następnie usuń zaznaczenie wszystkich metryk, aby wyświetlić pełny dostępny zestaw. Kliknij (i), aby wyświetlić definicję każdej metryki.

![Usuń zaznaczenie wszystkich danych, aby wyświetlić cały zestaw](./media/web-monitor-performance/appinsights-62allchoices.png)

Wybranie dowolnej metryki powoduje wyłączenie innych, które nie mogą pojawić się na tym samym wykresie.

## <a name="set-alerts"></a>Ustawianie alertów
Aby otrzymywać powiadomienia pocztą e-mail o nietypowych wartościach dowolnej metryki, dodaj alert. Możesz wysłać wiadomość e-mail do administratorów konta lub na określone adresy e-mail.

![](./media/web-monitor-performance/appinsights-413setMetricAlert.png)

Ustaw zasób przed innymi właściwościami. Nie wybieraj zasobów webtest, jeśli chcesz ustawić alerty dotyczące wydajności lub metryk użycia.

Należy zwrócić uwagę na jednostki, w których zostaniesz poproszony o wprowadzenie wartości progowej.

*Nie widzę przycisku Dodaj alert.* - Czy jest to konto grupowe, do którego masz dostęp tylko do odczytu? Skontaktuj się z administratorem konta.

## <a name="diagnosing-issues"></a><a name="diagnosis"></a>Diagnozowanie problemów
Oto kilka wskazówek dotyczących znajdowania i diagnozowania problemów z wydajnością:

* Skonfiguruj [testy internetowe,][availability] aby otrzymywać alerty, jeśli witryna sieci Web ustępuje lub reaguje niepoprawnie lub wolno. 
* Porównaj liczbę żądań z innymi metrykami, aby sprawdzić, czy błędy lub powolna odpowiedź są związane z obciążeniem.
* [Wstaw i wyszukaj instrukcje śledzenia][diagnostic] w kodzie, aby ułatwić określenie problemów.
* Monitoruj aplikację sieci Web w pracy za pomocą [strumienia metryk na żywo][livestream].
* Przechwyć stan aplikacji .NET za pomocą [debugera migawek][snapshot].

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>Znajdowanie i naprawianie wąskich gardeł wydajności dzięki badaniu wydajności

Za pomocą środowiska badania wydajności można przejrzeć operacje powolne wykonywanie w aplikacji sieci Web. Można szybko wybrać określoną powolną operację i użyć [profilera](../../azure-monitor/app/profiler.md) do głównego źródła, co powoduje, że powolne operacje są wykonywane do kodu. Korzystając z nowej dystrybucji czasu trwania wyświetlanej dla wybranej operacji, można szybko szybko ocenić, jak złe jest środowisko dla klientów. Możesz zobaczyć, ile interakcji użytkownika miało wpływ na każdą powolną operację. W poniższym przykładzie zdecydowaliśmy się przyjrzeć bliżej środowisko dla get klienci/szczegóły operacji. W dystrybucji czasu trwania widzimy, że istnieją trzy kolce. Kolec po lewej stronie wynosi około 400 ms i reprezentuje świetne wrażenia responsywne. Średni skok wynosi około 1,2 s i stanowi przeciętne doświadczenie. Wreszcie na 3,6 s mamy kolejny mały skok, który reprezentuje 99-ty percentyl doświadczenie, co może spowodować, że nasi klienci zostawić niezadowolony. To doświadczenie jest dziesięć razy wolniejsze niż wielkie doświadczenie dla tej samej operacji. 

![UZYSKAJ klientów/Szczegóły trzy skoki czasu trwania](./media/web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Aby lepiej zrozumieć środowisko użytkownika dla tej operacji, możemy wybrać większy zakres czasu. Następnie możemy również zawęzić w czasie w określonym oknie czasowym, w którym operacja była powolna. W poniższym przykładzie przełączyliśmy domyślny zakres czasu 24 godzin do zakresu czasu 7 dni, a następnie powiększyliśmy przedział czasu 9:47 na 12:47 między wt wt. 12 i 13. Zarówno rozkład czasu trwania, jak i liczba śladów próbki i profilera zostały zaktualizowane po prawej stronie.

![UZYSKAJ klientów/Szczegóły trzy skoki czasu trwania w zakresie 7 dni z przedziałem czasu](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

Aby zawęzić powolne doświadczenia, następnie przybliżamy czas trwania, który przypada między 95 a 99 percentylem. Stanowią one 4% interakcji użytkownika, które były powolne.

![UZYSKAJ klientów/Szczegóły trzy skoki czasu trwania w zakresie 7 dni z przedziałem czasu](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Możemy teraz albo spojrzeć na reprezentatywne próbki, klikając przycisk Próbki lub na reprezentatywne ślady profilera, klikając przycisk Ślady profilera. W tym przykładzie istnieją cztery ślady, które zostały zebrane dla klientów GET/Szczegóły w przedziale czasu i zakresie czasu trwania zainteresowania.

Czasami problem nie będzie w kodzie, ale raczej w zależności wywołane kodem. Można przełączyć się do zależności kartę w widoku klasyfikacji wydajności, aby zbadać takie powolne zależności. Domyślnie widok wydajności jest trendy średnie, ale to, co naprawdę chcesz spojrzeć na to 95 percentyl (lub 99,, w przypadku monitorowania usługi dojrzałe). W poniższym przykładzie skupiliśmy się na powolnej zależności usługi Azure BLOB, gdzie nazywamy PUT fabrikamaccount. Dobre doświadczenia klastra około 40 ms, podczas gdy powolne wywołania tej samej zależności są trzy razy wolniejsze, klastrowanie około 120 ms. Nie trzeba wiele z tych wywołań, aby dodać do spowodować odpowiednie działanie zauważalnie spowolnić. Można przejść do szczegółów w reprezentatywnych próbek i śladów profilera, podobnie jak w za pomocą operacji kartę.

![UZYSKAJ klientów/Szczegóły trzy skoki czasu trwania w zakresie 7 dni z przedziałem czasu](./media/web-monitor-performance/SlowDependencies95thTrend.png)

Doświadczenie badania wydajności pokazuje istotne spostrzeżenia wraz z zestawem próbek, na których zdecydowano się skupić. Najlepszym sposobem, aby spojrzeć na wszystkie dostępne statystyki jest przejście do zakresu czasu 30 dni, a następnie wybierz ogólne, aby zobaczyć szczegółowe informacje we wszystkich operacjach w ciągu ostatniego miesiąca.

![UZYSKAJ klientów/Szczegóły trzy skoki czasu trwania w zakresie 7 dni z przedziałem czasu](./media/web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next-steps"></a><a name="next"></a>Następne kroki
[Testy sieci Web][availability] — żądania sieci web wysyłane do aplikacji w regularnych odstępach czasu z całego świata.

[Przechwytywanie i wyszukiwanie śladów diagnostycznych][diagnostic] — wstaw wywołania śledzenia i przesiewać wyniki, aby zidentyfikować problemy.

[Śledzenie użycia][usage] — dowiedz się, jak użytkownicy korzystają z Twojej aplikacji.

[Rozwiązywanie problemów][qna] - i Q & A



<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[usage]: usage-overview.md
[livestream]: ../../azure-monitor/app/live-stream.md
[snapshot]: ../../azure-monitor/app/snapshot-debugger.md



