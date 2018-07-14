---
title: Adnotacje dotyczące usługi Application Insights wersji | Dokumentacja firmy Microsoft
description: Dodaj wdrożenie lub utworzyć znaczniki do wykresy Eksploratora metryk w usłudze Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 11/16/2016
ms.author: mbullwin
ms.openlocfilehash: 056716e243980f0a8aadc1ff7e9b8776809ad88e
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036401"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Adnotacje na wykresy metryk w usłudze Application Insights
Adnotacje na [Eksploratora metryk](app-insights-metrics-explorer.md) wykresy pokazują, w której została wdrożona nowa kompilacja lub innych istotnego zdarzenia. Ułatwiają one można łatwo sprawdzić, czy zmiany miały wpływu na wydajność aplikacji. Mogą być automatycznie tworzone przez [system kompilacji Visual Studio Team Services](https://docs.microsoft.com/vsts/pipelines/tasks/). Można również utworzyć adnotacje do dowolnego zdarzenia, które chcesz przez [ich tworzenia za pomocą programu PowerShell](#create-annotations-from-powershell).

![Przykład adnotacje z korelacją widoczne z czas odpowiedzi serwera](./media/app-insights-annotations/00.png)



## <a name="release-annotations-with-vsts-build"></a>Adnotacji dotyczących wersji kompilacji usługi VSTS

Adnotacji dotyczących wersji to funkcja oparta na chmurze kompilacji i wersji usługi Visual Studio Team Services. 

### <a name="install-the-annotations-extension-one-time"></a>Zainstaluj rozszerzenie adnotacji (raz)
Aby można było utworzyć adnotacji dotyczących wersji, należy zainstalować jedną z wielu rozszerzeń Team Service dostępnych w witrynie Visual Studio Marketplace.

1. Zaloguj się do Twojej [Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/setup/sign-up-for-visual-studio-online) projektu.
2. W witrynie Visual Studio Marketplace [Pobierz rozszerzenie adnotacji dotyczących wersji](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)i dodaj go do konta usługi Team Services.

![W prawym górnym rogu strony sieci web usługi Team Services, Otwórz w portalu Marketplace. Wybierz Visual Team Services, a następnie w obszarze kompilacji i wydania wybierz Zobacz więcej.](./media/app-insights-annotations/10.png)

Wystarczy zrobić to raz dla swojego konta programu Visual Studio Team Services. Teraz można skonfigurować adnotacji dotyczących wersji dla każdego projektu w ramach Twojego konta. 

### <a name="configure-release-annotations"></a>Konfigurowanie adnotacji dotyczących wersji

Należy uzyskać osobne klucz interfejsu API dla każdego szablonu wydania usługi VSTS.

1. Zaloguj się do [portalu Microsoft Azure](https://portal.azure.com) , a następnie otwórz zasób usługi Application Insights monitoruje aplikację. (Lub [utwórz ją teraz](app-insights-overview.md), jeśli nie zostało to zrobione jeszcze.)
2. Otwórz **dostęp do interfejsu API**, **Application Insights identyfikator**.
   
    ![W witrynie portal.azure.com otwórz zasób usługi Application Insights, a następnie wybierz pozycję Ustawienia. Otwórz dostęp do interfejsu API. Skopiuj identyfikator aplikacji](./media/app-insights-annotations/20.png)

4. W osobnym oknie przeglądarki Otwórz (lub Utwórz) szablon wydania, która zarządza wdrożeń z programu Visual Studio Team Services. 
   
    Dodaj zadanie, a następnie wybierz zadanie adnotację dotyczącą wersji widoczną Application Insights z menu.
   
    Wklej **identyfikator aplikacji** skopiowany z bloku dostęp do interfejsu API.
   
    ![W programie Visual Studio Team Services otwórz wersji, wybierz definicję wydania, a następnie wybierz edycji. Kliknij przycisk Dodaj zadanie, a następnie wybierz adnotację dotyczącą wersji widoczną Application Insights. Wklej identyfikator aplikacji szczegółowych informacji.](./media/app-insights-annotations/30.png)
4. Ustaw **APIKey** pola do zmiennej `$(ApiKey)`.

5. W oknie platformy Azure Utwórz nowy klucz interfejsu API, a następnie utwórz jego kopię.
   
    ![W bloku dostęp do interfejsu API, w oknie platformy Azure kliknij przycisk Utwórz klucz interfejsu API. Podaj komentarz, sprawdź adnotacje zapisu, a następnie kliknij przycisk Generuj klucz. Skopiuj nowy klucz.](./media/app-insights-annotations/40.png)

6. Otwórz kartę Konfiguracja szablonu wydania.
   
    Utwórz definicję zmiennej `ApiKey`.
   
    Wklej swój klucz interfejsu API, aby ApiKey definicji zmiennej.
   
    ![W oknie Team Services wybierz kartę Konfiguracja, a następnie kliknij przycisk Dodaj zmienną. Ustaw nazwę, aby ApiKey i do wartości, Wklej klucz, który został wygenerowany, a następnie kliknij ikonę blokady.](./media/app-insights-annotations/50.png)
7. Na koniec **Zapisz** definicji wydania.


## <a name="view-annotations"></a>Wyświetlaj adnotacje
Teraz zawsze, gdy używasz szablonu wydania do wdrażania nowej wersji, adnotacja będą wysyłane do usługi Application Insights. Adnotacje będą wyświetlane na wykresach w Eksploratorze metryk.

Kliknięcie dowolnego znacznik adnotacji, aby otworzyć szczegółowe informacje o wersji, m.in. obiekt żądający, gałęzi kontroli źródła, wersji definicji, środowiska i nie tylko.

![Kliknij znacznik adnotacji w dowolnej wersji.](./media/app-insights-annotations/60.png)

## <a name="create-custom-annotations-from-powershell"></a>Tworzenie niestandardowych adnotacje za pomocą programu PowerShell
Adnotacje można również utworzyć z żaden proces, który chcesz (bez użycia programu VS Team System). 


1. Utwórz lokalne kopie z [skrypt programu Powershell z serwisu GitHub](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

2. Uzyskiwanie Identyfikatora aplikacji i Utwórz klucz interfejsu API z poziomu bloku dostęp do interfejsu API.

3. Wywołanie skryptu następująco:

```PS

     .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }
```

To proste w celu zmodyfikowania skryptu, na przykład aby utworzyć adnotacje w ciągu ostatnich.

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie elementów roboczych](app-insights-diagnostic-search.md#create-work-item)
* [Automatyzacja przy użyciu programu PowerShell](app-insights-powershell.md)
