---
title: Inteligentne wykrywanie - anomalie wydajności | Dokumentacja firmy Microsoft
description: Usługa Application Insights wykonuje inteligentne analizy telemetrii aplikacji i ostrzega o potencjalnych problemach. Ta funkcja wymaga ustawień.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 6acd41b9-fbf0-45b8-b83b-117e19062dd2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/04/2017
ms.reviewer: antonfr
ms.author: mbullwin
ms.openlocfilehash: b1a3b04427839736359c88f8ad6a8db5eedf8488
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61294087"
---
# <a name="smart-detection---performance-anomalies"></a>Wykrywanie inteligentne — anomalie wydajności

[Usługa Application Insights](../../azure-monitor/app/app-insights-overview.md) automatycznie analizuje wydajność aplikacji sieci web i może zostać wyświetlone ostrzeżenie o potencjalnych problemach. Użytkownik może być odczytywanie to ponieważ jeden z naszych wykrywanie inteligentne powiadomienia otrzymane.

Ta funkcja wymaga nie specjalne ustawienia innego niż Konfigurowanie aplikacji dla usługi Application Insights (na [ASP.NET](../../azure-monitor/app/asp-net.md), [Java](../../azure-monitor/app/java-get-started.md), lub [Node.js](../../azure-monitor/app/nodejs.md), a następnie w [strony sieci web Kod](../../azure-monitor/app/javascript.md)). Może to być aktywny, gdy aplikacja generuje wystarczającej ilości danych telemetrycznych.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Gdy otrzyma powiadomienie wykrywania inteligentnego

Usługa Application Insights wykrył, że wydajność aplikacji ma negatywny wpływ na dostępność w jednym z następujących sposobów:

* **Wydłużenie czasu odpowiedzi** — aplikacja została uruchomiona, odpowiada na żądania wolniej niż. Zmiany mogły zostać szybkie, na przykład powodu regresji we wdrożeniu najnowszych. Lub było stopniowe, może być spowodowany przez przeciek pamięci. 
* **Spadek czasu trwania zależności** -Twojej aplikacji wykonywania wywołań do interfejsu API REST, bazy danych lub innych zależności. Zależność odpowiada wolniej niż.
* **Wzorzec niską wydajność** -aplikacji wydaje się mieć problem z wydajnością, który ma wpływ na tylko niektóre żądania. Na przykład strony są ładowane wolniej na jednym typie przeglądarki niż inne osoby; lub żądania są podawane wolniej z określonego serwera. Obecnie nasze algorytmy Przyjrzyj się krótszy czas ładowania strony, czasów odpowiedzi żądań i czasów odpowiedzi zależności.  

Wykrywanie inteligentne wymaga co najmniej 8 dni telemetrii w ilościach wymagającego, aby ustalić punkt odniesienia normalnej wydajności. Tak po aplikacja została uruchomiona w tym okresie, wszelkie istotne problemy w wyniku powiadomienia.


## <a name="does-my-app-definitely-have-a-problem"></a>Czy Moja aplikacja ma zdecydowanie problem?

Nie, powiadomienia nie oznacza, że Twoja aplikacja ma zdecydowanie problem. Jest po prostu sugestią dotyczącą coś czy może chcesz Przyjrzyj się bliżej więcej.

## <a name="how-do-i-fix-it"></a>Jak go naprawić?

Powiadomienia obejmują informacje diagnostyczne. Oto przykład:


![Oto przykład wykrywania wydłużenie czasu odpowiedzi serwera](media/proactive-performance-diagnostics/server_response_time_degradation.png)

1. **Klasyfikacja**. Powiadomienie zawiera informację, ilu użytkowników lub ile operacji uległy zmianie. Może to pomóc Ci przypisać priorytet problemu.
2. **Zakres**. Ten problem wpływa na cały ruch lub tylko niektóre strony? Jest on ograniczony do określonego przeglądarki lub lokalizacji? Te informacje można uzyskać z powiadomienia.
3. **Diagnozowanie**. Często informacje diagnostyczne w powiadomieniu sugeruje rodzaj problemu. Na przykład jeśli czas odpowiedzi spowalnia, jeżeli liczba żądań jest wysoka, który sugeruje, że serwer lub zależności są przeciążone. 

    W przeciwnym przypadku Otwórz blok wydajność, w usłudze Application Insights. Tam możesz znaleźć [Profiler](profiler.md) danych. Jeśli istnieją wyjątki zgłaszane, możesz też spróbować [rozszerzenia snapshot debugger](../../azure-monitor/app/snapshot-debugger.md).



## <a name="configure-email-notifications"></a>Konfigurowanie powiadomień E-mail

Inteligentne wykrywanie powiadomienia są domyślnie włączone i wysyłane do tych, którzy mają [właściciele, współautorzy i czytelnicy dostęp do zasobu usługi Application Insights](../../azure-monitor/app/resources-roles-access-control.md). Aby zmienić to ustawienie, kliknij przycisk **Konfiguruj** powiadomienia e-mail, lub Otwórz ustawienia wykrywania inteligentnego w usłudze Application Insights. 
  
  ![Ustawienia wykrywania inteligentnego](media/proactive-performance-diagnostics/smart_detection_configuration.png)
  
  * Możesz użyć **anulować** linku w wiadomości e-mail wykrywania inteligentnego, aby przestać otrzymywać powiadomienia e-mail.

Wiadomości e-mail o anomaliach wydajności wykrywania inteligentnego mogą zawierać maksymalnie jeden adres e-mail każdego dnia na zasób usługi Application Insights. Wiadomości e-mail będą wysyłane tylko wtedy, gdy co najmniej jeden nowy problem, które zostało wykryte w tym dniu. Nie otrzymasz powtórzeń wszystkie komunikaty. 

## <a name="faq"></a>Często zadawane pytania

* *Tak pracownicy firmy Microsoft Przyjrzyj się moje dane?*
  * Nie. Usługa jest całkowicie automatyczne. Tylko otrzymasz powiadomienia. Twoje dane są [prywatnej](../../azure-monitor/app/data-retention-privacy.md).
* *Czy można analizować wszystkie dane zebrane przez usługę Application Insights?*
  * Nie w chwili obecnej. Obecnie analizujemy żądania czas odpowiedzi oraz czas odpowiedzi zależności czas ładowania. Analiza dodatkowe metryki znajduje się na naszej liście prac wyszukiwania do przodu.

* Jakie typy aplikacji to działa dla?
  * Te spadku wydajności są wykrywane w dowolnej aplikacji, która generuje odpowiednie dane telemetryczne. Po zainstalowaniu usługi Application Insights w aplikacji sieci web, następnie żądania i zależności są automatycznie śledzone. Ale w usług zaplecza lub innych aplikacji, jeśli wstawiono wywołania [TrackRequest()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) lub [TrackDependency](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency), Inteligentne wykrywanie będzie działać w taki sam sposób.

* *Można utworzyć reguły wykrywania anomalii własnego lub dostosować istniejące zasady?*

  * Jeszcze nie ale możesz:
    * [Konfigurowanie alertów](../../azure-monitor/app/alerts.md) , poinformować Cię, gdy Metryka przekracza próg.
    * [Eksportowanie telemetrii](../../azure-monitor/app/export-telemetry.md) do [bazy danych](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) lub [do usługi Power BI](../../azure-monitor/app/export-power-bi.md ), gdzie można analizować je samodzielnie.
* *Jak często jest wykonywana analiza*

  * Firma Microsoft analizy Codzienne uruchamianie WE dane telemetryczne z poprzedniego dnia (pełny dzień w strefie czasowej UTC).
* *To samo dotyczy to Zastąp [alertów dotyczących metryk](../../azure-monitor/app/alerts.md)?*
  * Nie.  Nie jesteśmy zaangażowani w zapewnienie wykrywania co warto rozważyć nietypowe zachowanie.


* *Jeśli I nie robią niczego w odpowiedzi na powiadomienie, I zostanie wyświetlony monit?*
  * Nie, zostanie wyświetlony komunikat o każdym tylko raz. Jeśli problem będzie się powtarzać zostaną również zaktualizowane w inteligentne wykrywanie kanału informacyjnego bloku.
* *Czy mogę utracone wiadomości e-mail. Gdzie znaleźć powiadomienia w portalu*
  * W ramach przeglądu usługi Application Insights w swojej aplikacji kliknij **wykrywania inteligentnego** kafelka. Będzie mógł odnaleźć wszystkie powiadomienia się do tyłu 90 dni.

## <a name="how-can-i-improve-performance"></a>Jak poprawić wydajność?
Powolne, jak i niepowodzeniem odpowiedzi to jeden z największych frustrations dla użytkowników witryny sieci web, wiemy z własnego doświadczenia. Tak jest ważne, aby rozwiązać problemy z.

### <a name="triage"></a>Klasyfikacja
Najpierw czy ma to znaczenie? Jeśli strona jest zawsze ładować się wolno, ale tylko 1% witryny użytkownicy nigdy nie trzeba przyjrzeć się im, być może masz więcej o czym należy wziąć pod uwagę. Z drugiej strony jeśli tylko 1 procent użytkowników, otwórz go, ale zawsze zgłasza wyjątek wyjątków, który może być warte zbadania.

Jako ogólnej wskazówki należy użyć instrukcji wpływ (narażeni użytkownicy lub % ruchu), ale należy pamiętać, że nie jest cały artykuł. Zbierać inne dokumenty, aby potwierdzić.

Należy wziąć pod uwagę parametry tego problemu. Jeśli jest to zależne od lokalizacji geograficznej, skonfiguruj [testy dostępności](../../azure-monitor/app/monitor-web-app-availability.md) łącznie z tego regionu: może po prostu być problemy z siecią w tym obszarze.

### <a name="diagnose-slow-page-loads"></a>Diagnozowanie powolne ładowanie stron
Gdzie jest problem? Jest wolne odpowiedzi serwera, jest bardzo długi strony lub czy przeglądarka musi wykonać dużo pracy, aby go wyświetlić?

Otwórz blok przeglądarki, metryki. Segmenty wyświetlania przeglądarki strony obciążenia czasu wskazuje, gdzie czas. 

* Jeśli **czas wysyłania żądania** jest wysokie, albo serwer odpowiada powoli lub żądanie jest żądaniem post z dużą ilością danych. Przyjrzyj się [metryki wydajności](../../azure-monitor/app/web-monitor-performance.md#metrics) do badania czasy odpowiedzi.
* Konfigurowanie [śledzenia zależności](../../azure-monitor/app/asp-net-dependencies.md) aby zobaczyć, czy powolność wynika z usługami zewnętrznymi lub bazy danych.
* Jeśli **odbierania odpowiedzi** jest dominującym, strony i jego zależne elementy — JavaScript, CSS, obrazów i tak dalej (ale nie asynchronicznie załadowanych danych) są długie. Konfigurowanie [testu dostępności](../../azure-monitor/app/monitor-web-app-availability.md)i należy ustawić opcję, aby załadować zależne elementy. Po otrzymaniu niektórych wyników Otwórz szczegóły wyniku i rozwiń go, aby zobaczyć, czasy ładowania różnych plików.
* Wysoka **czas przetwarzania klienta** sugeruje skrypty działają wolno. Jeśli przyczyną jest oczywiste, Rozważ dodanie kodu czasu i czasy wysyłania w wywołaniach trackMetric.

### <a name="improve-slow-pages"></a>Poprawa powolne strony
Brak sieci web, pełna porad na temat zwiększania Twoje odpowiedzi serwera i czasy ładowania stron, dlatego firma Microsoft nie będzie podejmowana próba powtórzyć je wszystkie w tym miejscu. Poniżej przedstawiono kilka wskazówek, które prawdopodobnie już wiesz, po prostu pomagające w myśl:

* Powolne ładowanie ze względu na duże pliki: Załaduj asynchronicznie skrypty i inne elementy. Za pomocą tworzenia pakietów skryptów. Strona główna przerwać działanie elementów widget, które ładują dane oddzielnie. Nie wysyłaj zwykłe stare kod HTML dla długich tabel: dane żądania JSON lub innych kompaktowego formatu za pomocą skryptu, a następnie wypełnij tabelę w miejscu. Brak wspaniałych środowisk pomoże Ci to wszystko. (One także pociąga za sobą skrypty big Data, oczywiście.)
* Zależności serwera wolne: Należy wziąć pod uwagę lokalizacji geograficznych poszczególnych składnikach. Na przykład jeśli używasz platformy Azure, upewnij się, że serwer sieci web i bazy danych znajdują się w tym samym regionie. Czy zapytania pobierają więcej informacji, niż jest to wymagane? Czy buforowanie lub adapterów przetwarzania wsadowego pomocy?
* Pojemności problemy: Spójrz na metryk serwera czasów odpowiedzi i liczby żądań. Jeśli czas reakcji osiągają szczytowe użycie nieproporcjonalnie z okresami liczby żądań, prawdopodobnie konfiguracji serwerów.


## <a name="server-response-time-degradation"></a>Wydłużenie czasu odpowiedzi serwera

Powiadomienia o spadku czas odpowiedzi zawiera:

* Czas odpowiedzi w porównaniu do czasu odpowiedzi normalne dla tej operacji.
* Ilu użytkowników dotyczy problem.
* Średni czas odpowiedzi i 90. percentyl czas odpowiedzi dla tej operacji w dniu wykrywania i 7 dni przed. 
* Liczba żądań tej operacji w dniu wykrywania i 7 dni przed.
* Korelacja między obniżenie wydajności tej operacji i obniżenia wydajności w powiązane zależności. 
* Linki pomagające zdiagnozować problem.
  * Ślady Profiler, aby wyświetlić, gdy operacja jest zużywany czas (łącze jest dostępne, jeśli Profiler śledzenia przykłady zostały zebrane dla tej operacji w okresie wykrywania). 
  * Raporty wydajności w Eksploratorze metryk, których użytkownik może kątami filtry zakresów czasu dla tej operacji.
  * Wyszukaj to wywołanie wyświetlić właściwości wywołań.
  * Zgłasza błąd — Jeśli liczba 1 > oznacza to, że wystąpiły błędy w tej operacji, które mogą przyczynić się do obniżenia wydajności.

## <a name="dependency-duration-degradation"></a>Spadek czasu trwania zależności

Nowoczesną aplikacją bardziej przyjąć podejście do projektowania wczesnych, który w wielu przypadkach prowadzi do dużych niezawodność w przypadku usług zewnętrznych. Na przykład jeśli aplikacja opiera się na niektórych platformach danych lub nawet wtedy, gdy tworzysz własne bot usługę, którą należy prawdopodobnie będzie przekazywał dla niektórych dostawcy usług cognitive services, dzięki której Boty wchodzić w interakcje w sposób bardziej ludzki i niektóre usługi magazynu danych dla bot do ściągania dla odpowiedzi m.  

Przykładowe zależności degradacji powiadomień:

![Oto przykład wykrywania spadek czasu trwania zależności](media/proactive-performance-diagnostics/dependency_duration_degradation.png)

Zwróć uwagę, że informuje:

* Czas trwania w porównaniu do czasu odpowiedzi normalne dla tej operacji
* Ilu użytkowników dotyczy problem
* Średni czas trwania i 90. percentyl czas trwania tej zależności w dniu wykrywania i 7 dni przed
* Liczba zależności wywołań w dniu wykrywania i 7 dni przed
* Linki pomagające zdiagnozować problem
  * Raporty dotyczące wydajności w Eksploratorze metryk dla tej zależności
  * Wyszukaj wywołania tej zależności, aby wyświetlić właściwości wywołania
  * Raporty awarii — jeśli liczba > 1, oznacza to, że wystąpiły zależność zakończona niepowodzeniem wywołuje w okresie wykrywania, która może przyczynić się do spadek czasu trwania. 
  * Otwórz Analytics z zapytaniami, które obliczają ten czas trwania zależności i liczba  

## <a name="smart-detection-of-slow-performing-patterns"></a>Inteligentne wykrywanie powolnego wzorce wykonywania 

Usługa Application Insights umożliwia znalezienie problemów z wydajnością, którzy mogą wpływ tylko na część użytkowników lub wpływ tylko na użytkowników, w niektórych przypadkach. Na przykład powiadomienia o ładowania stron jest wolniejszy na jednym typie przeglądarki niż na inne typy przeglądarek, lub jeśli żądania są obsługiwane wolniej z określonego serwera. Umożliwia również odnajdywanie problemów związanych z kombinacji właściwości, takie jak powolne strona ładuje się w jednym obszarze geograficznym w przypadku klientów z określonym systemem operacyjnym.  

Anomalie, takie jak te są bardzo trudne do wykrycia, po prostu, sprawdzając dane, ale są bardziej powszechne, niż myślisz. Często są tylko powierzchni, gdy klienci zaczną zgłaszać skargi. Do tego czasu jest za późno: narażeni użytkownicy są już przełączanie do Twoich konkurentów!

Obecnie nasze algorytmy Przyjrzyj się krótszy czas ładowania strony, czasów odpowiedzi żądania na serwerze i czasów odpowiedzi zależności.  

Nie masz ustawić któryś z progów lub skonfigurować reguły. Uczenie maszynowe i algorytmy wyszukiwania danych są używane do wykrywania nietypowe wzorce.

![Alert e-mail kliknij link, aby otworzyć raport diagnostyczny na platformie Azure](./media/proactive-performance-diagnostics/03.png)

* **Gdy** przedstawia czas wykrycia problemu.
* **Co** opisano:

  * Ten problem, który został wykryty;
  * Właściwości zestaw zdarzeń, które znaleźliśmy wyświetlane zachowanie problem.
* W tabeli przedstawiono porównanie zestawu niskiej, średniej zachowanie inne zdarzenia.

Kliknij łącza, aby otworzyć Eksploratora metryk i wyszukiwanie w odpowiednich raportach, filtrowanie czasu i właściwości zestawu działają wolno.

Zmodyfikuj zakres czasu i filtry, aby eksplorować dane telemetryczne.

## <a name="next-steps"></a>Kolejne kroki
Te narzędzia diagnostyczne ułatwiają sprawdzanie danych telemetrycznych z Twojej aplikacji:

* [Profiler](profiler.md) 
* [Rozszerzenie Snapshot debugger](../../azure-monitor/app/snapshot-debugger.md)
* [Analiza](../../azure-monitor/log-query/get-started-portal.md)
* [Analiza inteligentna Diagnostyka](../../azure-monitor/app/analytics.md)

Wykrywanie inteligentne są całkowicie automatyczny. A może chcesz skonfigurować niektóre alerty więcej?

* [Ręcznie skonfigurowane alertów dotyczących metryk](../../azure-monitor/app/alerts.md)
* [Testy sieci web dostępności](../../azure-monitor/app/monitor-web-app-availability.md)
