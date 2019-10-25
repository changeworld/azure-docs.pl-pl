---
title: Informacje dotyczące użycia aplikacji mobilnych i zachowania użytkowników w usługach Visual Studio App Center i Azure
description: Dowiedz się więcej na temat usług takich jak App Center, które ułatwiają podejmowanie szybkich decyzji firmowych przez zrozumienie, jak użytkownicy korzystają z aplikacji mobilnej.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: d60e9437a2f33788eb183cfcad0f3a10d71fb79d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795798"
---
# <a name="analyze-and-understand-usage-of-mobile-applications"></a>Analizowanie i poznawanie użycia aplikacji mobilnych
Jak dobrze rozumiesz, jak użytkownicy korzystają z aplikacji? Ile aktywnych użytkowników posiada Twoja aplikacja i jak zmienia się użycie z upływem czasu? Jakie funkcje są używane i które z nich korzystają? Gdzie są oparte te użytkownicy? Ilu użytkowników korzysta z najnowszej wersji aplikacji? Wszystkie te pytania są ważne, aby zrozumieć, jak przekształcić swoją aplikację w pomyślną działalność. Aby odpowiedzieć na te rodzaje pytań dotyczących analizy użycia, należy zebrać dane użycia z aplikacji.

Im więcej informacji znajdziesz w danych, tym więcej można znaleźć sposoby ulepszania aplikacji i zapewnić zadowolenie użytkowników. Ważne jest, aby korzystać z danych w celu uzyskania szczegółowych informacji i zapewnić użytkownikom zadowolenie.

## <a name="importance-of-analytics"></a>Znaczenie analiz
- **Zapoznaj** się z użytkownikami, sposobami ich współpracy z aplikacją i Dowiedz się, w jaki sposób można dostosować swoją aplikację i zapewnić doskonałe środowisko rozwoju firmy.
- **Śledź** metryki użycia, aby podejmować świadome decyzje dotyczące wprowadzania na rynek aplikacji i lepszego udostępniania klienta.
- **Zmierz** wydajność aplikacji.
- **Dowiedz się** , które części aplikacji są używane przez wartość i wydajność.
- **Szczegółowe informacje** na temat problemów związanych z zmianą i przechowywaniem danych.

Użyj następujących usług, aby włączyć funkcję analizy aplikacji mobilnych.

## <a name="visual-studio-app-center"></a>Centrum aplikacji programu Visual Studio
[App Center Analytics](/appcenter/analytics/) pozwala rozwijać odbiorców, koncentrując się na tym, co ważne, dzięki głębokiemu raportowaniu i wglądowi w informacje o sesjach użytkowników, najważniejszych urządzeniach, wersjach systemu operacyjnego i analizie behawioralnej. Łatwo twórz niestandardowe zdarzenia, aby śledzić wszystko dzięki rozbudowanej analizie aplikacji.

   **Najważniejsze funkcje**
   - **Śledź** wzorce użycia, wdrażanie użytkowników i inne metryki zaangażowania bezpłatnie.
   - **Zidentyfikuj** trendy, zachowanie użytkowników i zaangażowanie za poorednictwem zdarzeń niestandardowych.
   - Wbudowane **metryki** i **szczegółowe informacje** dotyczące użycia aplikacji (codziennie, co tydzień, co miesiąc), sesji, właściwości urządzenia i demograficznych użytkowników na **jednym pulpicie nawigacyjnym**.
   - **Ciągle Eksportuj wszystkie dane analityczne do platformy Azure** , aby uzyskać nieograniczony czas przechowywania. Obsługuje eksport do usługi Azure Blob Storage i platformy Azure Application Insights.
   - **Integruj z platformą Azure Application Insights** , aby uzyskać jeszcze dokładniejsze informacje, takie jak przechowywanie, analizowanie lejkowe i kohorty
   - **Integracja z zestawem SDK z jednym wierszem** , aby rozpocząć w ciągu kilku minut.
   - **Obsługa platform** — iOS, Android, MacOS, systemu tvOS, Xamarin, system reaguje na natywny, Unity, Cordova.

   **Wołują**
   - [Zarejestruj się w usłudze App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [Wprowadzenie do usługi App Center Analytics](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
Azure Monitor zawiera [Application Insights](/azure/azure-monitor/app/app-insights-overview) , które udostępnia narzędzia do zbierania i analizowania danych telemetrycznych w celu zmaksymalizowania wydajności i monitorowania aplikacji mobilnej. Korzystając z Application Insights, możesz skorzystać z usługi App Center Analytics i skonfigurować Eksportowanie do Application Insights. Application Insights może wysyłać zapytania, segmentować, filtrować i analizować dane telemetryczne zdarzeń niestandardowych z aplikacji poza narzędziami analitycznymi, App Center zapewnia.

**Najważniejsze funkcje**
   - **Zbadaj** niestandardową telemetrię zdarzeń.
   - **Filtrowanie** danych telemetrycznych zdarzeń z zaawansowanymi możliwościami segmentacji.
   - **Analizuj** wzorce konwersji, przechowywania i nawigacji w aplikacji.
     - **Lejki** — służy do analizowania i monitorowania współczynników konwersji.
     - **Przechowywanie** do analizowania, jak dobrze aplikacja zachowuje użytkowników w czasie.
     - **Skoroszyty** — służy do łączenia wizualizacji i tekstu w raporcie, który można udostępniać.
     - **Kohorty** — służy do nadawania nazw i zapisywania określonych grup użytkowników lub zdarzeń, aby można było łatwo odwoływać się do nich z innych narzędzi analitycznych.

**Wołują**
- [Azure Portal](https://portal.azure.com/)
- [Analizuj swoją aplikację mobilną przy użyciu App Center i Application Insights](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Analiza użycia za pomocą Application Insights](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[Usługa Azure PlayFab](https://playfab.com/) oferuje kompletną platformę zaplecza z usługami gier, analizą w czasie rzeczywistym i LiveOps, które należy utworzyć światowej klasy gry połączone z chmurą. Te usługi zmniejszają bariery do uruchomienia dla deweloperów gier, oferując duże i małe Studios ekonomiczne rozwiązania programistyczne, które można skalować wraz z ich gramami i pomóc im w obsłużeniu, obtrzymywania i zarabiaj graczy. PlayFab umożliwia deweloperom korzystanie z inteligentnej chmury do kompilowania i obsługi gier, analizowania danych gier i ulepszania ogólnych środowisk gier.

**Najważniejsze funkcje**
   - **Monitoruj** pulpity nawigacyjne w czasie rzeczywistym.
   - **Oceń** wydajność gry, korzystając z najważniejszych metryk.
   - **Zgłoś** , aby przejrzeć podsumowania dziennej i miesięcznej wydajności gry dzięki wygenerowanym automatycznie raportom, które mogą być wyświetlane w programie Game Manager i pobierane lub dostarczane do Twojej skrzynki odbiorczej codziennie.
   - **Testowanie a/B** w celu uruchomienia eksperymentów i określenia optymalnego ustawienia dla określonej zmiennej.
   - **Segmentacja** dla graczy umożliwia definiowanie zautomatyzowanych grup graczy.
    
**Wołują**
- [Portal PlayFab](https://developer.playfab.com/en-US/sign-up)
- [Analiza](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Przewodniki Szybki start](/gaming/playfab/#pivot=documentation&panel=quickstarts)    
