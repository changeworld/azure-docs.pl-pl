---
title: Monitoruj kondycję swojej aplikacji i użycia za pomocą usługi Application Insights
description: Rozpocznij pracę z usługą Application Insights. Analizowanie użycia, dostępności i wydajności aplikacji lokalnej lub aplikacji Microsoft Azure.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/10/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: d7b8037f50fc4877fe233925f3e922648169f73b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60373155"
---
# <a name="monitor-performance-in-web-applications"></a>Monitorowanie wydajności w aplikacjach internetowych


Upewnij się, że aplikacja działa optymalnie i szybko dowiedzieć się o wszelkich błędach. [Usługa Application Insights] [ start] Opisz wszelkie problemy z wydajnością i wyjątków i ułatwiać znajdowanie i diagnozowanie głównych przyczyn.

Usługa Application Insights można monitorować aplikacje sieci web platformy ASP.NET oraz Java i usługi, usługi WCF. Mogą być hostowane lokalnie, na maszynach wirtualnych lub jako witryny sieci Web Microsoft Azure. 

Po stronie klienta usługi Application Insights może potrwać dane telemetryczne z stron sieci web i szerokiej gamy urządzeń, w tym systemów iOS, Android i Windows Store apps.

## <a name="setup"></a>Konfigurowanie monitorowania wydajności
Jeśli nie zostały jeszcze dodane usługi Application Insights do projektu, (to znaczy, jeśli nie ma w pliku ApplicationInsights.config), wybierz jedną z następujących sposobów, aby rozpocząć pracę:

* [Aplikacje sieci web ASP.NET](../../azure-monitor/app/asp-net.md)
  * [Dodawanie monitorowania wyjątku bez agenta](../../azure-monitor/app/asp-net-exceptions.md)
  * [Dodanie funkcji monitorowania zależności](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Aplikacje sieci web Java EE](../../azure-monitor/app/java-get-started.md)
  * [Dodanie funkcji monitorowania zależności](../../azure-monitor/app/java-agent.md)

## <a name="view"></a>Eksplorowanie metryk wydajności
W [witryny Azure portal](https://portal.azure.com), przejdź do zasobu usługi Application Insights, skonfigurowanej dla swojej aplikacji. Blok przeglądu przedstawia dane wydajności podstawowych:

Kliknij dowolny wykres, aby wyświetlić więcej szczegółów i aby zobaczyć wyniki przez dłuższy czas. Na przykład, kliknij Kafelek żądania, a następnie wybierz zakres czasu:

![Kliknij, aby większej ilości danych i wybierz zakres czasu](./media/web-monitor-performance/appinsights-48metrics.png)

Kliknij wykres, aby wybrać metryki, które wyświetla, lub Dodaj nowy wykres i wybierz swoje metryki:

![Kliknij wykres, aby wybrać metryki](./media/web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Usuń zaznaczenie wszystkich metryk** aby zobaczyć całą sekcję, który jest dostępny. Metryki można podzielić na grupy; Po wybraniu dowolnego członka grupy są wyświetlane tylko innych członków tej grupy.

## <a name="metrics"></a>Jaki jest średnią wszystkich? Kafelki wydajności i raporty
Istnieją różne metryki wydajności, które mogą uzyskać. Zacznijmy od tych, które pojawiają się domyślnie w bloku aplikacji.

### <a name="requests"></a>Żądania
Liczba żądań HTTP odebrane w określonym przedziale czasu. Porównaj te z wynikiem na inne raporty, aby zobaczyć, jak aplikacja zachowuje się jak obciążenia różni się.

Żądania HTTP obejmują wszystkie żądania GET lub POST dla stron, obrazów i danych.

Kliknij Kafelek, aby uzyskać liczniki dla określonych adresów URL.

### <a name="average-response-time"></a>Średni czas odpowiedzi
Mierzy czas między żądania sieci web, wprowadzanie aplikacji i odpowiedzi zostały zwrócone.

Punkty umożliwia wyświetlenie ruchomą średnią. W przypadku dużej liczby żądań może istnieć niektórych funkcji, które odbiegają od średniej bez widocznych szczytu lub zanurzyć na wykresie.

Zwróć uwagę na wartości szczytowe nietypowe. Ogólnie rzecz biorąc oczekiwany czas odpowiedzi wzrasta z wzrost liczby żądań. W przypadku nieproporcjonalne rośnie aplikacji może osiągnięcia limitu zasobów, takich jak Procesor lub pojemność usługi, które są używane.

Kliknij Kafelek, aby uzyskać czasu z określonych adresów URL.

![](./media/web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Najwolniejsze żądania
![](./media/web-monitor-performance/appinsights-44slowest.png)

Pokazuje, które żądania może być konieczne dostrojenie wydajności.

### <a name="failed-requests"></a>Żądania zakończone niepowodzeniem
![](./media/web-monitor-performance/appinsights-46failed.png)

Liczba żądań, które zgłosiło nieprzechwyconych wyjątków.

Kliknij Kafelek, aby wyświetlić szczegółowe informacje o konkretnych błędów i wybierz pojedyncze żądanie, aby wyświetlić jego szczegóły. 

Przeanalizowana reprezentatywna próbka błędów został zachowany na potrzeby inspekcji poszczególnych.

### <a name="other-metrics"></a>Inne metryki
Aby zobaczyć, jak ustawić innych metryk, które można wyświetlić, kliknij wykres, a następnie usuń zaznaczenie wszystkich metryk, aby wyświetlić dostępne pełne. Kliknij (i), aby zobaczyć definicję wszystkie metryki.

![Usuń zaznaczenie wszystkich metryk, aby zobaczyć cały zestaw](./media/web-monitor-performance/appinsights-62allchoices.png)

Wybranie dowolnego metryki wyłącza osobom, które nie pojawiają się na tym samym wykresie.

## <a name="set-alerts"></a>Ustawianie alertów
Aby otrzymywać powiadomienia pocztą e-mail wyjątkowe wartości dowolnej metryce, należy dodać alert. Możesz wybrać albo Wyślij wiadomość e-mail do administratorów konta lub adresów e-mail określonych.

![](./media/web-monitor-performance/appinsights-413setMetricAlert.png)

Ustaw zasób przed innymi właściwościami. Nie należy wybierać zasoby testu internetowego, aby ustawić alerty dotyczące metryk wydajności lub użycia.

Należy zachować ostrożność, należy pamiętać, jednostki, w których pojawi się prośba o podanie wartości progowej.

*Przycisk Dodaj Alert nie jest widoczny.* — Jest to grupa kont, do którego masz dostęp tylko do odczytu? Skontaktuj się z administratorem konta.

## <a name="diagnosis"></a>Diagnozowanie problemów
Poniżej przedstawiono kilka porad dotyczących Znajdowanie i diagnozowanie problemów z wydajnością:

* Konfigurowanie [testy sieci web] [ availability] alertów, jeśli witryna sieci web ulegnie awarii lub odpowiada niepoprawnie lub powoli. 
* Porównaj liczby żądań z innymi metrykami, aby sprawdzić, czy błędy lub powolne odpowiedzi odnoszą się do obciążenia.
* [Wstaw i wyszukiwania instrukcji śledzenia] [ diagnostic] w kodzie, aby ułatwić identyfikowanie problemów.
* Monitorowanie aplikacji sieci Web w operację, podając [Live Stream metryki][livestream].
* Przechwyć stan aplikacji .NET za pomocą [rozszerzenia Snapshot Debugger][snapshot].

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>Znajdowanie i eliminowanie wąskich gardeł wydajności przy użyciu środowisko badania wydajności

Środowisko badania wydajności umożliwia przeglądanie wolno przeprowadzanie operacji w aplikacji sieci Web. Możesz szybko wybierz określoną operację powolne i używać [Profiler](../../azure-monitor/app/profiler.md) głównym spowodować wolnych operacji do kodu. Przy użyciu dystrybucji nowy czas trwania przedstawić dla wybranej operacji możesz szybko rzut oka ocenić jak poważny jest środowisko dla swoich klientów. Aby zobaczyć, ile interakcji użytkownika został zmieniony. dla każdej operacji powolne. W poniższym przykładzie zostały zdecydowaliśmy się przyjrzeć bliżej środowisko dla operacji GET Customers/Details. W Rozkład czasów trwania widać, że istnieją trzy skoki. Skrajnie po lewej stronie kolekcji jest około 400 ms i stanowi doskonałą interaktywnych doświadczenia. Środkowy kolekcji jest wokół 1,2 s i reprezentuje daje środowisko. Na koniec na 3.6 s mamy innej kolekcji małe, reprezentujący 99 doświadczenia percentyl, może spowodować naszych klientów pozostawić niesatysfakcjonujące. Środowisko to dziesięć razy wolniej niż doskonałe środowisko dla tej samej operacji. 

![Wartości graniczne czas trwania trzy GET Customers/Details](./media/web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Aby uzyskać lepszym zrozumieniu funkcji środowiska użytkownika dla tej operacji, firma Microsoft wybierz większy zakres czasu. Możemy następnie również zawęzić w czasie w oknie określonym czasie, gdy operacja zakończyła się wolno. W poniższym przykładzie zostały przeszliśmy z domyślnego 24 godziny, zakres czasu do 7 dni zakres czasu, a następnie wyświetlana w powiększeniu 9:47 do 12:47 przedział czasu między: wtorek 12 i środa 13. Rozkład czasów trwania i liczbę ślady próbki i programem profilującym, zostały zaktualizowane po prawej stronie.

![GET Customers/Details trzy czas trwania skoki w ciągu 7 dni w zakresie przedział czasu](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

Aby zawęzić w wolnych doświadczeń, firma Microsoft następnie powiększenie czasy trwania wierszom wypadających pomiędzy 95 i 99. percentylu. Te reprezentują % 4 interakcji użytkownika, które zostały powolne.

![GET Customers/Details trzy czas trwania skoki w ciągu 7 dni w zakresie przedział czasu](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Można teraz albo przyjrzeć reprezentatywnej próbki, klikając przycisk przykłady, lub na ślady profilera reprezentatywny przez kliknięcie przycisku ślady Profiler. W tym przykładzie istnieją cztery dane śledzenia, które zostały zebrane dla GET Customers/Details w czas trwania okna i zakres zainteresowania.

Czasami ten problem nie będzie w kodzie, ale raczej powstanie zależności kod wywołuje. Można przełączyć na kartę zależności w widoku Klasyfikacja wydajności do badania tych zależności powolne. Domyślnie widok wydajności popularnych średnie, ale co tak naprawdę chcesz Przyjrzyj się jest używany 95. percentyl (lub 99th, w przypadku, gdy monitorujesz dojrzała usługi). W poniższym przykładzie koncentrowaliśmy się na wolne działanie zależności obiektów BLOB platformy Azure, w którym Zadzwonimy PUT fabrikamaccount. Dobra środowisk klastra około 40 ms, powolne wywołania do tej samej zależności są trzykrotnie wolniej, klastrowanie około 120 ms. Nie przyjmuje wiele z tych wywołań do dodawania spowodować znacznie spowolnić odpowiednich operacji. Możesz przejść do reprezentatywnej próbki i ślady narzędzia profiler, podobnie jak za pomocą karty operacji.

![GET Customers/Details trzy czas trwania skoki w ciągu 7 dni w zakresie przedział czasu](./media/web-monitor-performance/SlowDependencies95thTrend.png)

Środowisko badania wydajności pokazuje właściwe szczegółowe informacje, wzdłuż krawędzi zestaw przykładowy, który chcesz skupić się na. Najlepszym sposobem, aby wyświetlić wszystkie dostępne wglądu w szczegółowe dane jest przełączyć się do zakresu czasu 30 dni, a następnie wybierz pozycję ogólnej, aby zobaczyć szczegółowe informacje we wszystkich operacjach w ostatnim miesiącu.

![GET Customers/Details trzy czas trwania skoki w ciągu 7 dni w zakresie przedział czasu](./media/web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next"></a>Następne kroki
[Testy sieci Web] [ availability] -żądania sieci web wysyłanych do aplikacji w regularnych odstępach czasu z całego świata.

[Rejestrowanie i wyszukiwanie śledzenia diagnostycznego] [ diagnostic] — Wstawianie wywołań śladu i zapoznawanie się wyniki, aby wyszukać błędy.

[Śledzenie użycia] [ usage] — Dowiedz się, jak inni używają Twojej aplikacji.

[Rozwiązywanie problemów z] [ qna] — i pytań i odpowiedzi



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



