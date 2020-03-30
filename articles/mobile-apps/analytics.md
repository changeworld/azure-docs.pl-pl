---
title: Opis użycia aplikacji mobilnych i zachowania użytkowników w programie Visual Studio App Center i usługach platformy Azure
description: Dowiedz się więcej o usługach, takich jak App Center, które pomagają podejmować inteligentne decyzje biznesowe, dowiedzieć się, jak użytkownicy korzystają z aplikacji mobilnej.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 4dc3cea4497c55dda0d8da2ca29201615dadff19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241080"
---
# <a name="analyze-and-understand-mobile-application-use"></a>Analizowanie i zrozumienie korzystania z aplikacji mobilnych
Jak dobrze rozumiesz, jak użytkownicy korzystają z aplikacji? Ilu aktywnych użytkowników ma aplikacja i jak zmienia się użycie w czasie? Z jakich funkcji korzystają i z jakich tych najczęściej korzystamy? Gdzie są ci użytkownicy? Ilu użytkowników korzysta z najnowszej wersji aplikacji? Wszystkie te pytania są ważne, aby zrozumieć, aby przekształcić aplikację w udany biznes. Aby odpowiedzieć na tego rodzaju pytania dotyczące analizy użycia, musisz zbierać dane użycia z aplikacji.

Im bardziej spojrzysz na dane, tym bardziej możesz znaleźć sposoby na ulepszenie aplikacji i uszczęśliwienie użytkowników. Ważne jest, aby używać danych, aby znaleźć zasysanych informacji i zapewnić użytkownikom satysfakcję.

## <a name="importance-of-analytics"></a>Znaczenie analityki
- Poznaj użytkowników, jak wchodzą w interakcje z aplikacją i co przynosi im z powrotem, aby dostosować aplikację i zapewnić wspaniałe środowisko, aby rozwijać swoją firmę.
- Śledź swoje dane użycia, aby podejmować świadome decyzje dotyczące wprowadzania aplikacji na rynek i lepszej obsługi klientów.
- Zmierz wydajność aplikacji.
- Dowiedz się, które części dysku aplikacji są wartością i wydajnością.
- Uzyskaj wgląd w analizy oparte na danych dotyczące problemów związanych z zmianami i przechowywaniem.

Skorzystaj z następujących usług, aby włączyć analizę aplikacji mobilnych.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Analytics](/appcenter/analytics/) pozwala zwiększyć grono odbiorców, koncentrując się na tym, co ważne. Oferuje głębokie raportowanie i szczegółowe informacje na temat sesji użytkowników, najlepszych urządzeń, wersji systemu operacyjnego i analizy behawioralnej. Łatwe tworzenie niestandardowych zdarzeń w celu śledzenia czegokolwiek dzięki rozbudowanej analizie aplikacji.

   **Najważniejsze funkcje**
   - Śledź wzorce użycia, przyjęcie użytkowników i inne metryki zaangażowania za darmo.
   - Identyfikowanie trendów, zachowań użytkowników i zaangażowania za pomocą zdarzeń niestandardowych.
   - Uzyskaj metryki gotowe do użycia aplikacji i szczegółowe informacje na temat użycia aplikacji (codziennie, co tydzień, co miesiąc), sesji, właściwości urządzenia i danych demograficznych użytkowników na jednym pulpicie nawigacyjnym.
   - Ciągłe eksportowanie wszystkich danych usługi App Center Analytics na platformę Azure w celu uzyskania nieograniczonego przechowywania. Usługa App Center Analytics obsługuje eksportowanie do usługi Azure Blob storage i usługi Azure Application Insights.
   - Integracja z usługą Azure Application Insights w celu uzyskania jeszcze głębszych informacji, takich jak przechowywanie, analiza ścieżek i kohorty.
   - Użyj integracji z jednowierszowym pakietem SDK, aby rozpocząć pracę w ciągu kilku minut.
   - Uzyskaj obsługę platformy dla systemów iOS, Android, macOS, tvOS, Xamarin, React Native, Unity i Cordova.

   **Odwołania**
   - [Zarejestruj się w App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [Wprowadzenie do usługi App Center Analytics](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
Usługa Azure Monitor zawiera [usługę Application Insights](/azure/azure-monitor/app/app-insights-overview), która udostępnia narzędzia do zbierania i analizowania danych telemetrycznych w celu maksymalizacji wydajności i monitorowania aplikacji mobilnej. Usługi Application Insights można korzystać z usługi App Center Analytics w celu skonfigurowania eksportu do usługi Application Insights. Usługa Application Insights może wyszukiwać, segmentować, filtrować i analizować niestandardowe dane telemetryczne zdarzeń z aplikacji, poza narzędziami analitycznymi udostępnianych przez Centrum aplikacji.

**Najważniejsze funkcje**
   - Wykonaj zapytanie względem danych telemetrycznych zdarzeń niestandardowych.
   - Filtrowanie danych telemetrycznych zdarzeń z zaawansowanymi możliwościami segmentacji.
   - Analizuj wzorce konwersji, przechowywania i nawigacji w aplikacji. Możesz użyć:
     - Ścieżki do analizowania i monitorowania współczynników konwersji.
     - Przechowywanie do analizy, jak dobrze aplikacja zachowuje użytkowników w czasie.
     - Skoroszyty do łączenia wizualizacji i tekstu w raport udostępniany.
     - Kohorty do nazywania i zapisywania określonych grup użytkowników lub zdarzeń, dzięki czemu można do nich łatwo odwoływać się z innych narzędzi analitycznych.

**Odwołania**
- [Portal Azure](https://portal.azure.com/)
- [Analizowanie aplikacji mobilnej za pomocą usługi App Center i usługi Application Insights](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Korzystanie z usługi App Center Analytics w usłudze Application Insights](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[Usługa Azure PlayFab](https://playfab.com/) oferuje kompletną platformę zaplecza z usługami gier, analizami w czasie rzeczywistym i funkcjami LiveOps, które są potrzebne do tworzenia światowej klasy gier połączonych z chmurą. Usługi te zmniejszają bariery do uruchomienia dla twórców gier. Oferują one zarówno duże, jak i małe studia opłacalne rozwiązania programistyczne, które skalują się wraz z ich grami. Usługi mogą pomóc studiom w angażowaniu, zatrzymywania i zarabiania na graczach. Dzięki PlayFab deweloperzy mogą korzystać z inteligentnej chmury do tworzenia i obsługi gier, analizowania danych dotyczących gier i ulepszania ogólnych wrażeń z gry.

**Najważniejsze funkcje**
   - Monitoruj pulpity nawigacyjne w czasie rzeczywistym.
   - Oceń skuteczność swojej gry za pomocą najlepszych wskaźników.
   - Przejrzyj podsumowania codziennych i miesięcznych wyników gry za pomocą automatyczniegenerowanych raportów. Raporty można wyświetlać w Menedżerze gier i codziennie pobierać je lub dostarczać do skrzynki odbiorczej.
   - Użyj testowania A/B, aby przeprowadzić eksperymenty i określić optymalne ustawienie dla określonej zmiennej.
   - Użyj segmentacji dla graczy, aby zdefiniować automatyczne grupowanie graczy.
    
**Odwołania**
- [Portal PlayFab](https://developer.playfab.com/en-US/sign-up)
- [Analiza](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Szybki start](/gaming/playfab/#pivot=documentation&panel=quickstarts) 
