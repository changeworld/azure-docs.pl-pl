---
title: Inteligentne wykrywania - anomalii wydajności | Dokumentacja firmy Microsoft
description: Usługa Application Insights wykonuje inteligentne analizy telemetrii aplikacji i ostrzega o potencjalnych problemach. Ta funkcja wymaga ustawień.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 6acd41b9-fbf0-45b8-b83b-117e19062dd2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mbullwin; antonfr
ms.openlocfilehash: 59b88a940e83ed89e8638b62680a57ca1514f8b0
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="smart-detection---performance-anomalies"></a>Inteligentne wykrywanie - anomalii wydajności

[Usługa Application Insights](app-insights-overview.md) automatycznie analizuje wydajność aplikacji sieci web i może zostać wyświetlone ostrzeżenie o potencjalnych problemach. Możesz może być odczytu to ponieważ jeden z naszych wykrywania inteligentne powiadomienia odbierane.

Ta funkcja wymaga specjalnych ustawień, niż Konfigurowanie aplikacji dla usługi Application Insights (na [ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), lub [Node.js](app-insights-nodejs.md), a następnie w [kodu strony sieci web](app-insights-javascript.md)). Jest aktywna, kiedy aplikacja generuje za mało danych telemetrii.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Gdy otrzyma powiadomienie o inteligentne wykrywanie?

Usługi Application Insights wykrył, że wydajność aplikacji ma obniżoną w jeden z następujących sposobów:

* **Degradacji czasu odpowiedzi** — aplikacja została uruchomiona wolniej niż odpowiada na żądania. Zmiana mogły zostać szybkie, na przykład powodu regresji we wdrożeniu najnowsze. Lub było stopniowe, może być spowodowany przeciek pamięci. 
* **Zależności czas trwania degradacji** -aplikacji wywołań interfejsu API REST, bazy danych lub innych zależności. Zależność odpowiada wolniej niż.
* **Niska wydajność wzorzec** -aplikacji wydaje się być problem z wydajnością, który ma wpływ na tylko niektórych żądań. Na przykład strony są ładowane wolniej na jeden typ przeglądarki niż inne; lub żądania są podawane wolniej z jednego określonego serwera. Obecnie nasze algorytmy przyjrzeć się czas ładowania strony, czas odpowiedzi na żądanie i czasy odpowiedzi zależności.  

Wykrywanie inteligentne wymaga co najmniej 8 dni dane telemetryczne głośność możliwego Aby ustalić linię bazową normalnej wydajności. Tak po aplikacja została uruchomiona w tym okresie, wszelkie istotne problemy w wyniku powiadomienia.


## <a name="does-my-app-definitely-have-a-problem"></a>Aplikacja my uwielbiamy ma problem?

Nie, powiadomienia nie oznacza, że aplikacja ma ostatecznie problem. Jest po prostu sugestię o coś się, że należy przyjrzeć się więcej ściśle.

## <a name="how-do-i-fix-it"></a>Jak rozwiązać ten problem?

Powiadomienia obejmują informacje diagnostyczne. Oto przykład:


![Oto przykład wykrywania degradacji czas odpowiedzi serwera](./media/app-insights-proactive-diagnostics/server_response_time_degradation.png)

1. **Klasyfikacja**. Powiadomienie zawiera liczbę użytkowników lub dotyczy ile operacji. Może to ułatwić można przypisać priorytet problemu.
2. **Zakres**. Problem wpływa na cały ruch lub tylko do niektórych strony? Jest on ograniczony do określonej przeglądarki lub lokalizacji? Te informacje można uzyskać powiadomienia.
3. **Diagnozowanie**. Często informacje diagnostyczne w powiadomieniu sugeruje rodzaj problemu. Na przykład, jeśli czas reakcji spowalnia po wysoki współczynnik żądań, które sugeruje się, że serwer lub zależności są przeciążone. 

    W przeciwnym razie otwarcie bloku wydajności w usłudze Application Insights. Istnieje, można znaleźć [profilera](app-insights-profiler.md) danych. Jeśli istnieją wyjątki zgłaszane, możesz również spróbować [debugera migawki](app-insights-snapshot-debugger.md).



## <a name="configure-email-notifications"></a>Skonfiguruj powiadomienia E-mail

Inteligentne powiadomienia wykrywania są domyślnie włączone i wysyłane do tych, którzy mają [właściciele, współautorzy i czytelnicy dostęp do zasobu usługi Application Insights](app-insights-resources-roles-access-control.md). Aby zmienić to ustawienie, kliknij przycisk **Konfiguruj** powiadomienia e-mail lub Otwórz ustawienia inteligentne wykrywanie w usłudze Application Insights. 
  
  ![Inteligentne wykrywania ustawień](./media/app-insights-proactive-diagnostics/smart_detection_configuration.png)
  
  * Można użyć **subskrypcję** link w wiadomości e-mail inteligentne wykrywania, aby przestać otrzymywać powiadomienia e-mail.

Wiadomości e-mail o inteligentne wykrywania anomalii wydajności mogą zawierać maksymalnie jeden adres e-mail dziennie według zasobu usługi Application Insights. Wiadomości e-mail będą wysyłane tylko wtedy, gdy istnieje co najmniej jeden nowy problem, która została wykryta w tym dniu. Użytkownik nie będzie otrzymywał powtarza wiadomości. 

## <a name="faq"></a>Często zadawane pytania

* *Tak pracownicy firmy Microsoft przyjrzeć się dane?*
  * Nie. Usługa jest całkowicie automatyczne. Tylko otrzymasz powiadomień. Twoje dane są [prywatnej](app-insights-data-retention-privacy.md).
* *Czy można analizować wszystkie dane zebrane przez usługę Application Insights?*
  * Nie w chwili obecnej. Obecnie możemy analizowanie żądania czas reakcji i czas odpowiedzi zależności czas ładowania. Analiza dodatkowe metryki znajduje się na naszych zaległości wyszukiwania do przodu.

* Jakie typy aplikacji jest to odpowiednie dla?
  * Te degradations są wykrywane w dowolnej aplikacji, który generuje odpowiednie dane telemetryczne. Po zainstalowaniu usługi Application Insights w aplikacji sieci web, następnie żądania i zależności są automatycznie śledzone. Ale w usługi wewnętrznej bazy danych lub inne aplikacje, jeśli dodaje wywołań [TrackRequest()](app-insights-api-custom-events-metrics.md#trackrequest) lub [TrackDependency](app-insights-api-custom-events-metrics.md#trackdependency), Inteligentne wykrywanie będzie działać w taki sam sposób.

* *Można utworzyć własny anomalii reguł wykrywania lub dostosować istniejące zasady?*

  * Nie została jeszcze ale można wykonywać następujące czynności:
    * [Konfigurowanie alertów](app-insights-alerts.md) który informujące, gdy metryki przekracza próg.
    * [Eksportuj dane telemetryczne](app-insights-export-telemetry.md) do [bazy danych](app-insights-code-sample-export-sql-stream-analytics.md) lub [do usługi Power BI](app-insights-export-power-bi.md), gdzie można analizować je samodzielnie.
* *Jak często jest wykonywane analizy*

  * Analiza jest przeprowadzana codziennie na podstawie danych telemetrycznych z poprzedniego dnia (pełny dzień w strefie czasowej UTC).
* *To samo dzieje się to replace [metryki alerty](app-insights-alerts.md)?*
  * Nie.  Nie możemy przekazać do wykrywania co warto rozważyć nietypowe zachowanie.


* *Jeśli I nie wykonuj żadnych czynności w odpowiedzi na powiadomienie, I otrzyma monitu?*
  * Nie, pojawi się komunikat o każdym tylko raz. Jeśli problem będzie się powtarzał zostaną zaktualizowane w inteligentne wykrywanie źródła bloku.
* *Utratą wiadomości e-mail. Gdzie można znaleźć powiadomienia w portalu?*
  * W obszarze Przegląd usługi Application Insights aplikacji, kliknij przycisk **inteligentne wykrywanie** kafelka. Można odnaleźć wszystkie powiadomienia się do 90 dni wstecz.

## <a name="how-can-i-improve-performance"></a>Jak może poprawić wydajność?
Powolne i nie powiodło się odpowiedzi są jedną z największych frustrations dla użytkowników witryny sieci web, ponieważ znasz z własnego środowiska. Dlatego ważne jest do rozwiązania problemów.

### <a name="triage"></a>Klasyfikacja
Najpierw czy ma znaczenie? Jeśli strona jest zawsze powolnego ładowania, ale istniało przyjrzeć się on tylko 1% użytkowników witryny, być może masz więcej ważnych kwestii. Z drugiej strony jeśli tylko 1% użytkowników go otworzyć, ale zwraca zawsze wyjątków, który może być warto badania.

Za pomocą instrukcji wpływ (narażeni użytkownicy lub % ruchu) jako ogólne wytyczne, ale należy pamiętać, że nie jest cały artykuł. Zbierać inne dokumenty, aby potwierdzić.

Należy wziąć pod uwagę parametry problem. Jeśli jest zależny od lokalizacji geograficznej, skonfiguruj [testów dostępności](app-insights-monitor-web-app-availability.md) łącznie tego regionu: może po prostu występować problemy z siecią w tym obszarze.

### <a name="diagnose-slow-page-loads"></a>Diagnozowanie ładunków strona powolne
Gdzie jest problem? Jest powolne odpowiedzi serwera, jest bardzo długa strony lub czy przeglądarka musi wykonać dużo pracy w celu wyświetlenia go?

Otwiera blok metryki przeglądarki. Segmentowanych wyświetlania przeglądarki strony obciążenia czasu wskazuje, gdzie będzie czasu. 

* Jeśli **czas wysyłania żądania** jest wysoka, albo serwer odpowiada powoli lub żądanie jest żądaniem post z dużą ilością danych. Przyjrzyj się [metryki wydajności](app-insights-web-monitor-performance.md#metrics) do sprawdzania, czy czas odpowiedzi.
* Konfigurowanie [śledzenia zależności](app-insights-asp-net-dependencies.md) czy powolność znajduje się z powodu usług zewnętrznych lub bazy danych.
* Jeśli **odbierania odpowiedzi** jest dominujący, strony i jego zależne elementy - JavaScript, CSS, obrazy i tak dalej (ale nie asynchronicznie załadowanych danych) jest długa. Konfigurowanie [test dostępności](app-insights-monitor-web-app-availability.md)i należy ustawić opcję ładowania zależne elementy. Po wyświetleniu okna pewnych wyników, Otwórz szczegóły wyniku i rozwiń, aby wyświetlić czasy ładowania różnych plików.
* Wysoka **czasu przetwarzania klienta** sugeruje skrypty działają wolno. Jeśli przyczyna nie jest widoczne, należy dodać kodu czasu i wysłać godziny w wywołaniach trackMetric.

### <a name="improve-slow-pages"></a>Poprawa powolne strony
Brak pełnej porady na temat zwiększania odpowiedzi serwera, a czas ładowania strony, więc nie zostanie podjęta do wszystkich tutaj powtarzać sieci web. Poniżej przedstawiono kilka wskazówek, które prawdopodobnie już wiedzieć o, tak aby zacząć myśląc:

* Powolne ładowanie ze względu na duże pliki: ładowanie skryptów i innymi częściami asynchronicznie. Użyj skryptu tworzenie pakietów. Podziel strony głównej na elementy widget, które oddzielnie załadować ich danych. Nie wysyłaj zwykły stary kod HTML pod kątem długich tabel: request dane JSON lub w innym formacie kompaktowym za pomocą skryptu, a następnie wypełnij tabeli w miejscu. Brak struktur dużą ułatwiające to. (One również pociąga za sobą big skrypty oczywiście.)
* Powolna zależności serwera: należy wziąć pod uwagę lokalizacjach geograficznych składników. Na przykład jeśli używasz platformy Azure, upewnij się, że serwer sieci web i bazy danych znajdują się w tym samym regionie. Czy zapytania pobierają więcej informacji, niż jest to wymagane? Czy buforowanie lub przetwarzania wsadowego pomocy?
* Problemy dotyczące pojemności: przyjrzeć się metryki serwera czasów odpowiedzi i liczby żądań. Jeśli czas reakcji nieproporcjonalnie szczytowa z szczytów żądań, jest prawdopodobne, że serwery są rozciągana.


## <a name="server-response-time-degradation"></a>Degradacji czas odpowiedzi serwera

Powiadomienia o degradacji czasu odpowiedzi zawiera:

* Czas odpowiedzi w porównaniu do czasu odpowiedzi normalne dla tej operacji.
* Ilu użytkowników są zagrożone.
* Średni czas odpowiedzi i 90-procentowy czas odpowiedzi dla tej operacji na dzień wykrywania i 7 dni przed. 
* Liczba żądań tej operacji na dzień wykrywania i 7 dni przed.
* Korelacja degradacji w tej operacji i degradations w powiązanych zależności. 
* Łącza w celu zdiagnozowania problemu.
  * Ślady profilera, aby wyświetlić, gdzie jest zużywany czas operacji (łącze jest dostępne, jeśli zebrano profilera śledzenia przykłady dla tej operacji w okresie wykrywania). 
  * Raporty wydajności w Eksploratorze metryki, gdy użytkownik może kątami filtrów zakresu czasu dla tej operacji.
  * Wyszukiwania dla tego wywołania wyświetlić właściwości wywołań.
  * Zgłasza błąd — jeśli count > 1 oznacza to, że wystąpiły błędy w tej operacji, która może przyczynić się do zmniejszenia wydajności.

## <a name="dependency-duration-degradation"></a>Zależności degradacji czas trwania

Nowoczesnych aplikacji bardziej przyjąć podejście do projektowania micro usług, który w wielu przypadkach prowadzi do duże niezawodności na usług zewnętrznych. Na przykład jeśli aplikacja korzysta niektóre platformy danych lub nawet wtedy, gdy tworzenia własnych bot usługi, który prawdopodobnie będzie to przekazywać niektóre dostawcy usług kognitywnych umożliwiające Twojej robotów wchodzić w interakcje w sposób bardziej człowieka i niektóre usługi magazynu danych dla bot ściągania odpowiedzi z.  

Przykładowe powiadomienie degradacji zależności:

![Oto przykład wykrywania degradacji czas trwania zależności](./media/app-insights-proactive-diagnostics/dependency_duration_degradation.png)

Należy zauważyć, że informuje o tym:

* Czas trwania w porównaniu do czasu odpowiedzi normalne dla tej operacji
* Ilu użytkowników ma wpływ
* Średni czas trwania i 90-procentowy czas trwania tej zależności w dniu wykrywania i 7 dni przed
* Liczba zależności wywołań w dniu wykrywania i 7 dni przed
* Łącza w celu zdiagnozowania problemu
  * Raporty wydajności w Eksploratorze Metryka tej zależności
  * Wyszukaj wywołania tej zależności, aby wyświetlić właściwości wywołania
  * Raporty błąd — jeśli count > 1, oznacza to, że wystąpiły zależności nie powiodło się w okresie wykrywania, która może przyczynić się do pogorszenia się czas trwania. 
  * Otwórz Analytics z kwerendy obliczające ten czas trwania zależności i liczby  

## <a name="smart-detection-of-slow-performing-patterns"></a>Inteligentne wykrywanie powolnego wzorce wykonywania 

Usługa Application Insights umożliwia znalezienie problemy z wydajnością, które mogłyby dotyczą tylko części użytkowników, lub dotyczą tylko użytkownicy w niektórych przypadkach. Na przykład powiadomienia o stron obciążenia działa wolniej na jeden typ przeglądarki niż na inne typy przeglądarek, lub jeśli żądania są wolniej podawana z określonego serwera. Umożliwia również odnajdywanie problemów związanych z kombinacji właściwości, takie jak powolne ładuje w jednym obszarze geograficznym w przypadku klientów z określonym systemem operacyjnym.  

Anomalie, takich jak te są bardzo trudne do wykrycia sprawdzając tylko dane, ale są częściej niż się wydaje. Często ich powierzchni tylko, gdy klienci skarżą się. Do tego czasu jest za późno: narażeni użytkownicy są już przełączanie do konkurencji!

Obecnie nasze algorytmy przyjrzeć się czas ładowania strony, czas odpowiedzi na żądania na serwerze i czasy odpowiedzi zależności.  

Nie trzeba ustawić wszystkie progi lub skonfigurować reguły. Uczenie maszynowe i algorytmy wyszukiwania danych są używane na wykrywanie nieprawidłowych wzorców.

![Alerty e-mail kliknij łącze, aby otworzyć raport diagnostyczny na platformie Azure](./media/app-insights-proactive-performance-diagnostics/03.png)

* **Gdy** przedstawia czas wykrycia problemu.
* **Co** opisano:

  * Ten problem, który został wykryty;
  * Właściwości zestawu zdarzeń, które znaleziono wyświetlane zachowanie problem.
* W tabeli porównano niskiej zestaw z zachowaniem średni inne zdarzenia.

Kliknij łącza, aby otworzyć Eksploratora metryki i wyszukiwania w odpowiednich raportów filtrowane wg czasu i właściwości powolne wykonywania zestawu.

Zmodyfikuj przedziału czasu i filtry, aby eksplorować dane telemetryczne.

## <a name="next-steps"></a>Kolejne kroki
Te narzędzia diagnostyczne pomóc sprawdzić dane telemetryczne z aplikacji:

* [Profiler](app-insights-profiler.md) 
* [Debuger migawki](app-insights-snapshot-debugger.md)
* [Analiza](app-insights-analytics-tour.md)
* [Diagnostyka inteligentne analityka](app-insights-analytics-diagnostics.md)

Inteligentne wykryć są całkowicie automatyczne. A może chcesz skonfigurować niektóre alerty więcej?

* [Ręcznie skonfigurowanej metryki alertów](app-insights-alerts.md)
* [Dostępność testy sieci web](app-insights-monitor-web-app-availability.md)
