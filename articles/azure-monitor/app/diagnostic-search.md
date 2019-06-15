---
title: Za pomocą wyszukiwania w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Wyszukiwanie i filtrowanie nieprzetworzone dane telemetryczne wysyłane przez aplikację sieci web.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 2a437555-8043-45ec-937a-225c9bf0066b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mbullwin
ms.openlocfilehash: dfbaabd3d27804909334a7a370bcc89115e625c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60900270"
---
# <a name="using-search-in-application-insights"></a>Za pomocą wyszukiwania w usłudze Application Insights
Wyszukiwanie jest funkcją [usługi Application Insights](../../azure-monitor/app/app-insights-overview.md) służącego do wyszukania i zapoznaj się z elementów telemetrii, takich jak wyświetleń stron, wyjątki lub żądania sieci web. I przeglądać ślady dzienników i zdarzeń, które zostały zakodowane.

(W przypadku bardziej złożone zapytania na danych, użyj [analizy](../../azure-monitor/log-query/get-started-portal.md).)

## <a name="where-do-you-see-search"></a>Gdzie można zobaczyć wyszukiwania?

### <a name="in-the-azure-portal"></a>W witrynie Azure Portal

Wyszukiwanie diagnostyczne można otworzyć jawnie z bloku Omówienie usługi Application Insights w Twojej aplikacji:

![Otwórz wyszukiwanie diagnostyczne](./media/diagnostic-search/001.png)

![Zrzut ekranu przedstawiający wykresy wyszukiwanie diagnostyczne](./media/diagnostic-search/002.png)

Główną wyszukiwanie diagnostyczne jest lista elementów danych telemetrycznych — żądania serwera stronie widoków, zdarzenia niestandardowe, które zostały zakodowane i tak dalej. W górnej części listy jest podsumowanie wykres pokazujący liczbę zdarzeń, wraz z upływem czasu.

Kliknij przycisk Odśwież, aby uzyskać nowe zdarzenia.

### <a name="in-visual-studio"></a>W programie Visual Studio

W programie Visual Studio jest również okno wyszukiwania usługi Application Insights. Jest to najbardziej przydatne do wyświetlania danych telemetrycznych zdarzeń generowanych przez aplikację, którą debugujesz. Jednak może również pokazać zdarzenia zebrane z opublikowanych aplikacji w witrynie Azure portal.

Otwórz okno wyszukiwania w programie Visual Studio:

![Program Visual Studio Otwórz wyszukiwania usługi Application Insights](./media/diagnostic-search/32.png)

Okno wyszukiwania oferuje funkcje podobne do portalu sieci web:

![Okno wyszukiwania usługi Visual Studio Application Insights](./media/diagnostic-search/34.png)

Karta śledzenie jest dostępna, po otwarciu żądania lub wyświetlenia strony. "Operacji" to sekwencja zdarzeń, który jest skojarzony z widoku pojedynczego żądania lub strony. Na przykład wywołania zależności, wyjątki, dzienników i zdarzeń niestandardowych, może być częścią jednej operacji. Na karcie Śledzenie wyświetlane w formie graficznej czas i czas trwania tych zdarzeń w odniesieniu do żądanie lub wyświetlenie strony. 

## <a name="inspect-individual-items"></a>Sprawdź poszczególne elementy

Wybierz dowolny element telemetrii, aby wyświetlić pola klucza i powiązanych elementów.

![Zrzut ekranu przedstawiający żądanie poszczególnych zależności](./media/diagnostic-search/003.png)

Spowoduje to uruchomienie widoku szczegółów transakcji end-to-end:

![Zrzut ekranu przedstawiający widok szczegółów transakcji end-to-end.](./media/diagnostic-search/004.png)

## <a name="filter-event-types"></a>Filtruj typy zdarzeń
Otwórz blok filtru, a następnie wybierz typy zdarzeń, które mają być wyświetlane. (Jeśli później chcesz przywrócić filtrów, z którymi blok otwarty, kliknij Resetuj).

![Wybierz filtr, a następnie wybierz typy telemetrii](./media/diagnostic-search/02-filter-req.png)

Dostępne są następujące typy zdarzeń:

* **Śledzenie** - [dzienniki diagnostyczne](../../azure-monitor/app/asp-net-trace-logs.md) tym TrackTrace, log4Net, NLog i System.Diagnostic.Trace wywołania.
* **Żądanie** -żądania HTTP odebrane przez aplikacji serwera, w tym stron, skrypty, obrazy, pliki stylów i dane. Zdarzenia te są używane do tworzenia żądań i odpowiedzi, wykresy Przegląd.
* **Widok strony** - [dane telemetryczne wysyłane przez klienta internetowego](../../azure-monitor/app/javascript.md), który jest używany do tworzenia raportów widoku strony. 
* **Zdarzenie niestandardowe** — Jeśli wstawiono wywołania poleceń TrackEvent() do [monitorować użycie](../../azure-monitor/app/api-custom-events-metrics.md), możesz je znaleźć tutaj.
* **Wyjątek** — nieprzechwyconych [wyjątki na serwerze](../../azure-monitor/app/asp-net-exceptions.md)i tych, którzy logują się przy użyciu funkcji TrackException().
* **Zależność** - [wywołania z aplikacji serwera](../../azure-monitor/app/asp-net-dependencies.md) do innych usług, takich jak interfejsy API REST lub baz danych i AJAX wywołań z Twojej [kod klienta](../../azure-monitor/app/javascript.md).
* **Dostępność** — wyniki [testy dostępności](../../azure-monitor/app/monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filtrowanie według wartości właściwości
Można filtrować zdarzenia według wartości ich właściwości. Dostępne właściwości zależą od wybranych typów zdarzeń. 

Na przykład wyszukać żądań z kodem określoną odpowiedź. 

![Rozwiń właściwość, a następnie wybierz wartość](./media/diagnostic-search/03-response500.png)

Wybieranie żadnych wartości danej właściwości ma taki sam skutek jak wybranie wszystkich wartości. Przełącza się filtrowanie dla tej właściwości jest wyłączone.

### <a name="narrow-your-search"></a>Zawęzić kryteria wyszukiwania
Należy zauważyć, że liczby wartości filtru po prawej stronie pokazują, jak wiele wystąpień są w bieżącym zestawie filtrowanych. 

W tym przykładzie to oczywiste, że "Rpt/pracowników" prosić o wyniki w większości błędów "500":

![Rozwiń właściwość, a następnie wybierz wartość](./media/diagnostic-search/04-failingReq.png)

## <a name="find-events-with-the-same-property"></a>Znajdź wydarzenia z tej samej właściwości
Znajdź wszystkie elementy z taką samą wartość właściwości:

![Kliknij prawym przyciskiem myszy właściwość](./media/diagnostic-search/12-samevalue.png)

## <a name="search-the-data"></a>Wyszukiwanie danych

> [!NOTE]
> Aby napisać bardziej złożone zapytania, należy otworzyć [ **Analytics** ](../../azure-monitor/log-query/get-started-portal.md) w górnej części bloku przeszukiwania.
> 

Można wyszukiwać terminy w dowolnej wartości właściwości. Jest to szczególnie przydatne, jeśli napisano [zdarzenia niestandardowe](../../azure-monitor/app/api-custom-events-metrics.md) przy użyciu wartości właściwości. 

Można ustawić czas, zakres, są szybsze jako krótszy zakres wyszukiwania. 

![Otwórz wyszukiwanie diagnostyczne](./media/diagnostic-search/appinsights-311search.png)

Wyszukaj kompletne wyrazy, nie podciągów. Użyj znaków cudzysłowu, należy ująć w znaki specjalne.

| string | jest *nie* znalezione przez | Jednak te go znaleźć |
| --- | --- | --- |
| HomeController.About |Strona główna<br/>Kontroler<br/>limit | homecontroller<br/>— informacje<br/>"homecontroller.about"|
|Stany Zjednoczone|Sygnalizowanie UNI<br/>ted|Zjednoczone<br/>Stany<br/>Stany Zjednoczone i<br/>"united states"

Poniżej przedstawiono wyrażeniach wyszukiwania, których można użyć:

| Przykładowe zapytanie | Efekt |
| --- | --- |
| `apple` |Znajdowanie wszystkich zdarzeń w zakresie czasu, w których pola zawierają wyraz "apple" |
| `apple AND banana` <br/>`apple banana` |Znajdź wydarzenia, które zawierają zarówno słów. Użyj kapitału "i" nie "i". <br/>Krótka. |
| `apple OR banana` |Znajdź wydarzenia, które zawierają program Microsoft word. Użyj "OR", nie "or". |
| `apple NOT banana` |Znajdź wydarzenia, które zawierają jeden wyraz, ale nie drugiej. |

## <a name="sampling"></a>Próbkowanie
Jeśli aplikacja generuje wiele danych telemetrycznych (i używasz zestawu SDK platformy ASP.NET wersji 2.0.0-beta3 lub nowszej), moduł próbkowania adaptacyjnego automatycznie ogranicza ilość danych wysyłanych do portalu, wysyłając tylko reprezentatywny ułamek zdarzeń. Jednak zdarzenia, które są powiązane z tym samym żądaniem wybrane lub pominięte jako grupa, dzięki czemu można nawigować między powiązanymi zdarzeniami. 

[Więcej informacji na temat próbkowania](../../azure-monitor/app/sampling.md).

## <a name="create-work-item"></a>Utwórz element roboczy
Można utworzyć usterkę w usłudze GitHub lub DevOps platformy Azure przy użyciu informacji z dowolnego elementu danych telemetrycznych. 

![Kliknij nowy element roboczy, Edytuj pola, a następnie kliknij przycisk OK.](./media/diagnostic-search/42.png)

Możesz to zrobić, po raz pierwszy, zostanie wyświetlony monit skonfigurować łącze do organizacji DevOps platformy Azure i projektu.

![Wprowadź adres URL usługi Azure DevOps i nazwę projektu, a następnie kliknij przycisk Autoryzuj](./media/diagnostic-search/41.png)

(Można również skonfigurować łącze w bloku elementów roboczych.)

## <a name="send-more-telemetry-to-application-insights"></a>Wyślij większej ilości danych telemetrycznych do usługi Application Insights
Oprócz poza pole dane telemetryczne wysyłane przez zestaw SDK usługi Application Insights możesz wykonywać następujące czynności:

* Przechwytywanie danych śledzenia dziennika z preferowanej struktury rejestrowania w [.NET](../../azure-monitor/app/asp-net-trace-logs.md) lub [Java](../../azure-monitor/app/java-trace-logs.md). Oznacza to, można przeszukiwać Twoje ślady dzienników i skoreluj je z wyświetleń stron, wyjątki i inne zdarzenia. 
* [Pisanie kodu](../../azure-monitor/app/api-custom-events-metrics.md) wysyłać zdarzenia niestandardowe, wyświetleń stron i wyjątki. 

[Dowiedz się, jak wysyłać dzienniki i niestandardowych danych telemetrycznych do usługi Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).

## <a name="questions"></a>PYTANIA I ODPOWIEDZI
### <a name="limits"></a>Jak dużo danych jest zachowywana?

Zobacz [podsumowanie ograniczeń](../../azure-monitor/app/pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Jak sprawdzić danych POST w Moje żądania serwera
Firma Microsoft nie rejestruj danych POST automatycznie, ale można użyć [TrackTrace lub dziennik wywołań](../../azure-monitor/app/asp-net-trace-logs.md). Umieść dane POST w parametrze wiadomości. Nie można filtrować wiadomości w taki sam sposób, który można filtrować według właściwości, ale limit rozmiaru jest dłuższy.

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="add"></a>Następne kroki
* [Zapisywanie złożonych zapytań w usłudze Analytics](../../azure-monitor/log-query/get-started-portal.md)
* [Wyślij dzienniki i niestandardowych danych telemetrycznych do usługi Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
* [Konfigurowanie dostępności i czasu odpowiedzi testów](../../azure-monitor/app/monitor-web-app-availability.md)
* [Rozwiązywanie problemów](../../azure-monitor/app/troubleshoot-faq.md)
