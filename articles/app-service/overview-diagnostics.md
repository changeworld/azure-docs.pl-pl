---
title: Omówienie diagnostyki w usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak sposoby rozwiązywania problemów z aplikacją za pomocą diagnostyki usługi App Service.
keywords: Usługa App service, usłudze azure app service, Diagnostyka, pomocy technicznej, aplikacji sieci web, rozwiązywania problemów, samodzielne uzyskiwanie pomocy
services: app-service
documentationcenter: ''
author: jen7714
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: 3e304df51133d53adad50e672249bde6c9960712
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65539707"
---
# <a name="azure-app-service-diagnostics-overview"></a>Omówienie diagnostyki w usłudze Azure App Service

Po uruchomieniu aplikacji sieci web, należy się przygotować wszelkich problemów, które mogą powstać w wyniku błędów 500 informujący użytkowników, które witryna nie działa. Diagnostyki usługi App Service to środowisko im zwiększenie inteligencji i interaktywnych, aby ułatwić rozwiązywanie problemów z aplikacją, bez konieczności konfiguracji. Jeśli napotkasz problemy z aplikacją, diagnostyki usługi App Service wskazuje na czym polega problem poświęcany na właściwe informacje, aby łatwiej i szybciej rozwiązać problem.

Mimo że to środowisko jest najbardziej przydatne, gdy masz problemy z aplikacją w ciągu ostatnich 24 godzin, diagnostycznych wykresy są zawsze dostępne służących do analizowania.

Diagnostyki usługi App Service działa w przypadku nie tylko Twojej aplikacji na Windows, ale również aplikacji na [kontenerów systemu Linux/](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), [środowiska App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro), i [usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview).

## <a name="open-app-service-diagnostics"></a>Otwórz diagnostyki usługi App Service

Aby uzyskać dostęp do diagnostyki usługi App Service, przejdź do aplikacji sieci web usługi App Service lub App Service Environment w [witryny Azure portal](https://portal.azure.com). Na lewym pasku nawigacyjnym kliknij **diagnozowanie i rozwiązywanie problemów**.

Dla usługi Azure Functions, przejdź do aplikacji funkcji, a w górnym menu nawigacyjnym kliknij pozycję **funkcje platformy**i wybierz **diagnozowanie i rozwiązywanie problemów** z **funkcjizarządzaniazasobami** sekcji.

W na stronie głównej diagnostyki usługi App Service można wybrać kategorię, która najlepiej opisuje problem z aplikacją przy użyciu słów kluczowych w każdym kafelków na stronie głównej. Ponadto ta strona jest, gdzie można znaleźć **narzędzia diagnostyczne** dla aplikacji Windows. Zobacz [narzędzi diagnostycznych (tylko dla aplikacji Windows)](#diagnostic-tools-only-for-windows-app).

![Strona główna](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

## <a name="interactive-interface"></a>Interaktywny interfejs

Po wybraniu kategorii strony głównej, najlepiej pasującą w Twojej aplikacji w rozwiązaniu problemu, mogą ukierunkować diagnostyki usługi App Service interaktywny interfejs Genie, diagnozowania i rozwiązywania problemów z aplikacją. Można użyć skrótów kafelka, dostarczone przez Genie, aby wyświetlić pełny raport diagnostyczny kategorii problemu wybrane. Skróty kafelków umożliwiają bezpośredni sposób uzyskiwania dostępu do swojej metryki diagnostyki.

![Skróty kafelka](./media/app-service-diagnostics/tile-shortcuts-2.png)

Po kliknięciu przycisku na tych kafelkach, można wyświetlić listę tematów związanych z problemu opisanego w na kafelku. Te tematy zawierają fragmenty istotnych informacji z pełny raport. Możesz kliknąć dowolny z poniższych tematów, aby zbadać problemy w dalszej. Ponadto możesz kliknąć **Wyświetl pełny raport** do zbadania wszystkich tematów na jednej stronie.

![Tematy](./media/app-service-diagnostics/application-logs-insights-3.png)

![Wyświetl pełny raport](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Raport diagnostyczny

Po wybraniu zbadać problem przez kliknięcie tematu, można wyświetlić więcej szczegółów na temat często uzupełnione za pomocą wykresów i promocjach cenowych. Raport diagnostyczny może być zaawansowanego narzędzia na potrzeby precyzyjne określenie problem z aplikacją.

![Raport diagnostyczny](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Kontrolę kondycji

Jeśli nie wiesz, czym jest problem z aplikacją lub nie wiadomo, gdzie zacząć Rozwiązywanie problemów związanych z kontrolę kondycji jest dobrym miejscem do rozpoczęcia. Kontrolę kondycji analizuje aplikacje umożliwiają krótkiego, interaktywnego omówienia, który wskazuje, co jest w dobrej kondycji i na czym polega problem, informujący, gdzie należy zbadać problem. Jego im zwiększenie inteligencji i interaktywny interfejs zapewnia wskazówki proces rozwiązywania problemów. Kontrolę kondycji jest zintegrowany ze środowiskiem Genie dla aplikacji Windows i aplikacji sieci web w dół diagnostycznych raportu dla aplikacji systemu Linux.

### <a name="health-checkup-graphs"></a>Wykresy kontrolę kondycji

Istnieją cztery różne wykresy w kontrolę kondycji.

- **żądania i błędy:** Wykres, który pokazuje liczbę żądań w ciągu ostatnich 24 godzin oraz błędy serwera HTTP.
- **wydajność aplikacji:** Wykres, który pokazuje czas odpowiedzi w ciągu ostatnich 24 godzin dla różnych grup percentyl.
- **Użycie procesora CPU:** Wykres przedstawiający całkowity procent użycia procesora CPU na każde wystąpienie w ciągu ostatnich 24 godzin.  
- **Użycie pamięci:** Wykres przedstawiający całkowity procent fizyczne użycie pamięci na wystąpienie w ciągu ostatnich 24 godzin.

![Kontrolę kondycji](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Badanie problemów z kodem aplikacji (tylko dla aplikacji Windows)

Ponieważ wiele problemów z aplikacjami są powiązane z nim problemów w kodzie aplikacji, diagnostyki usługi App Service integruje się z [usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) do wyróżnienia wyjątków i problemów z zależnością skorelować wybranego przestojów. Usługa Application Insights ma włączenia oddzielnie.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Zaznacz, aby wyświetlić wyjątków usługi Application Insights i zależności **aplikacji sieci web w dół** lub **aplikacja internetowa działa wolno** Kafelek skróty.

### <a name="troubleshooting-steps-only-for-windows-app"></a>Kroki rozwiązywania problemów (tylko dla aplikacji Windows)

W przypadku wykrycia problemu z kategorią konkretnego problemu w ciągu ostatnich 24 godzin, można wyświetlić raport diagnostyczny pełnej i diagnostyki usługi App Service może zostać wyświetlony komunikat do wyświetlenia porady dotyczące bardziej rozwiązywania problemów oraz kolejne kroki dotyczące bardziej przewodnikiem.

![Usługa Application Insights i rozwiązywania problemów i następne kroki](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Narzędzia diagnostyczne (tylko dla aplikacji Windows)

Narzędzia diagnostyczne zawierają bardziej zaawansowane narzędzia diagnostyczne problemy, powolność, parametry połączenia i kod: Pomoc badania aplikacji. i aktywnego narzędzia, które pomagają rozwiązać problemy związane z użycia procesora CPU, żądań i pamięci.

### <a name="proactive-cpu-monitoring"></a>Proaktywne monitorowanie Procesora

Proaktywne monitorowanie procesora CPU zapewnia łatwy w użyciu i proaktywne sposób wykonania akcji w przypadku, gdy proces aplikacji lub podrzędnej aplikacji zużywa dużo zasobów Procesora. Możesz ustawić własne reguły progu procesora CPU do tymczasowo zminimalizować wysokiej problem procesora CPU, aż do znalezienia rzeczywistych przyczynę nieoczekiwany problem.

![Proaktywne monitorowanie Procesora](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="proactive-auto-healing"></a>Proaktywne automatyczne naprawianie

Jak Proaktywne monitorowanie procesora CPU, proaktywne automatyczne naprawianie zapewnia łatwy w użyciu i proaktywne podejście do łagodzenia nieoczekiwane zachowanie aplikacji. Możesz ustawić własne reguły na podstawie liczby żądań, powoli przetwarzanych żądań, limit pamięci i kod stanu HTTP do działania łagodzące wyzwalacza. To narzędzie, można tymczasowo ograniczyć nieoczekiwane zachowanie, aż do znalezienia rzeczywistych przyczynę problemu. Aby uzyskać więcej informacji na temat proaktywne automatyczne naprawianie, odwiedź stronę [ogłoszenie nowych automatycznego naprawiania środowiska w diagnostyce usługi app](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html).

![Proaktywne automatyczne naprawianie](./media/app-service-diagnostics/proactive-auto-healing-10.png)

## <a name="change-analysis"></a>Analiza zmian

W środowisku dynamicznym rozwoju czasami może być trudno jest śledzić wszystkie zmiany wprowadzone do aplikacji i umożliwić pinpoint tylko w przypadku zmiany, który spowodował nieprawidłowe zachowanie. Analiza zmiany może pomóc w zawężeniu na temat zmian wprowadzonych do aplikacji w celu ułatwienia rozwiązywania problemów środowisko. Analizy zmiany są osadzone w raporcie diagnostycznych, takich jak **wystąpiła awaria aplikacji** aby można było używać równocześnie z innymi metrykami.

![Zmień stronę domyślną analizy](./media/app-service-diagnostics/change-analysis-default-page-11.png)

![Widoku różnic](./media/app-service-diagnostics/diff-view-12.png)

Analiza zmiany musi umożliwiać przed rozpoczęciem korzystania z funkcji. Aby uzyskać więcej informacji na analizie zmian, odwiedź stronę [Przedstawiamy nowe środowisko analizy zmiany w diagnostyce usługi App](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html).