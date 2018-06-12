---
title: Adnotacje dla usługi Application Insights o wersji | Dokumentacja firmy Microsoft
description: Dodaj wdrożenie lub kompilacji znaczników w wykresach Eksploratora metryk w usłudze Application Insights.
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
ms.openlocfilehash: fb4bcd57062017c0d7ee802ba1f46660476f6af2
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293439"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Adnotacje na wykresach metryki w usłudze Application Insights
Adnotacje w [Eksploratora metryk](app-insights-metrics-explorer.md) wdrożonym nowej kompilacji lub innego istotnego zdarzenia na wykresach. Ułatwiają one Zobacz, czy zmiany będzie miało wpływu na wydajność aplikacji. Mogą być automatycznie tworzone przez [systemu kompilacji programu Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/build/build-your-app-vs). Można również utworzyć adnotacje do dowolnego zdarzenia, które chcesz przez flagi [tworzenie je z programu PowerShell](#create-annotations-from-powershell).

![Przykład adnotacje z korelacją widoczne z czas odpowiedzi serwera](./media/app-insights-annotations/00.png)



## <a name="release-annotations-with-vsts-build"></a>Adnotacje wydania z kompilacji programu VSTS

Adnotacje wersji to funkcja oparta na chmurze kompilacji i wersję usługi Visual Studio Team Services. 

### <a name="install-the-annotations-extension-one-time"></a>Zainstaluj rozszerzenie adnotacji (jeden raz)
Aby móc tworzyć adnotacji wersji, należy zainstalować jedną wiele rozszerzeń zespołu usługi dostępne w witrynie Marketplace programu Visual Studio.

1. Zaloguj się do Twojego [Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/setup/sign-up-for-visual-studio-online) projektu.
2. W programie Visual Studio Marketplace [uzyskać rozszerzenie wersji adnotacje](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)i dodaj go do konta usługi Team Services.

![AT z góry po prawej stronie sieci web usługi Team Services, otwórz Marketplace. Wybierz Visual Team Services, a następnie w obszarze kompilacji i wydania, wybierz pozycję Zobacz więcej.](./media/app-insights-annotations/10.png)

Należy w tym celu raz dla Twojego konta Visual Studio Team Services. Adnotacje wersji można teraz skonfigurować dla każdego projektu w ramach Twojego konta. 

### <a name="configure-release-annotations"></a>Skonfiguruj adnotacje zlecenia

Potrzebujesz oddzielnych klucz interfejsu API dla każdego szablonu wersji usługi VSTS.

1. Zaloguj się do [portalu Microsoft Azure](https://portal.azure.com) , a następnie otwórz zasobu usługi Application Insights, który monitoruje aplikacji. (Lub [teraz utworzyć](app-insights-overview.md), jeśli nie zostało to jeszcze zrobione jeszcze.)
2. Otwórz **dostępu do interfejsu API**, **Application Insights identyfikator**.
   
    ![Portal.azure.com Otwórz zasobu usługi Application Insights i wybierz polecenie Ustawienia. Otwórz dostępu do interfejsu API. Skopiuj identyfikator aplikacji](./media/app-insights-annotations/20.png)

4. W osobnym oknie przeglądarki otworzyć lub utworzyć szablon zlecenia, który zarządza wdrożeń z Visual Studio Team Services. 
   
    Dodać zadanie, a następnie wybierz zadanie Application Insights wersji adnotacji z menu.
   
    Wklej **identyfikator aplikacji** skopiowany z bloku dostępu do interfejsu API.
   
    ![W programie Visual Studio Team Services otwórz wersji, wybierz definicji wersji i wybierz polecenie Edytuj. Kliknij pozycję Dodaj zadanie, a następnie wybierz Application Insights wersji adnotacji. Wklej identyfikator aplikacji szczegółowych informacji.](./media/app-insights-annotations/30.png)
4. Ustaw **APIKey** pole do zmiennej `$(ApiKey)`.

5. W oknie Azure Utwórz nowy klucz interfejsu API i wykonać jego kopię.
   
    ![W bloku dostępu do interfejsu API w oknie Azure kliknij przycisk Utwórz klucz interfejsu API. Podaj komentarz, sprawdź adnotacje zapisu, a następnie kliknij przycisk Wygeneruj klucz. Skopiuj nowy klucz.](./media/app-insights-annotations/40.png)

6. Otwórz kartę Konfiguracja szablonu zlecenia.
   
    Tworzenie definicji zmiennej `ApiKey`.
   
    Wklej klucz interfejsu API ApiKey definicji zmiennej.
   
    ![W oknie usługi Team Services wybierz kartę Konfiguracja, a następnie kliknij przycisk Dodaj zmienną. Ustaw nazwę ApiKey i na wartość, Wklej klucz, który wygenerowane przed chwilą, a następnie kliknij ikonę blokady.](./media/app-insights-annotations/50.png)
7. Na koniec **zapisać** definicji wersji.


## <a name="view-annotations"></a>Adnotacje widoku
Teraz gdy wdrażania nowej wersji przy użyciu szablonu wersji, adnotacja będą wysyłane do usługi Application Insights. Adnotacje będą wyświetlane na wykresach w Eksploratorze metryk.

Kliknij znacznik żadnych adnotacji w taki sposób, aby otworzyć szczegółowe informacje o wersji, m.in. obiekt żądający, Rozgałęzienie kontroli źródła, wersji definicji, środowisko i inne.

![Kliknij znacznik adnotacji dowolnej wersji.](./media/app-insights-annotations/60.png)

## <a name="create-custom-annotations-from-powershell"></a>Tworzenie niestandardowych adnotacje z programu PowerShell
Można również utworzyć adnotacje z żaden proces, który chcesz (bez użycia programu VS Team System). 


1. Utwórz lokalne kopie z [skrypt programu Powershell z usługi GitHub](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

2. Uzyskiwanie Identyfikatora aplikacji i Utwórz klucz interfejsu API w bloku dostępu do interfejsu API.

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

Jest łatwy w celu zmodyfikowania skryptu, na przykład do tworzenia adnotacji w ciągu ostatnich.

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie elementów roboczych](app-insights-diagnostic-search.md#create-work-item)
* [Automatyzacja przy użyciu programu PowerShell](app-insights-powershell.md)
