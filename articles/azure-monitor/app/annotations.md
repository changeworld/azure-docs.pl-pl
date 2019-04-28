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
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mbullwin
ms.openlocfilehash: 652591fc4539e6f19c0606c1502609a823327f2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60794884"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Adnotacje na wykresy metryk w usłudze Application Insights

Adnotacje na [Eksploratora metryk](../../azure-monitor/app/metrics-explorer.md) wykresy pokazują, w której została wdrożona nowa kompilacja lub innych istotnego zdarzenia. Ułatwiają one można łatwo sprawdzić, czy zmiany miały wpływu na wydajność aplikacji. Mogą być automatycznie tworzone przez [usługom DevOps platformy Azure, system kompilacji](https://docs.microsoft.com/azure/devops/pipelines/tasks/). Można również utworzyć adnotacje mógł oflagować dowolne zdarzenie, które, tworząc je za pomocą programu PowerShell.

> [!NOTE]
> W tym artykule odzwierciedla przestarzałego **metryki klasycznego środowiska**. Adnotacje tylko są obecnie dostępne w środowisku klasycznym i  **[skoroszyty](../../azure-monitor/app/usage-workbooks.md)**. Aby dowiedzieć się więcej na temat bieżącego środowiska metryk, należy skontaktować się [w tym artykule](../../azure-monitor/platform/metrics-charts.md).

![Przykład adnotacje z korelacją widoczne z czas odpowiedzi serwera](./media/annotations/00.png)

## <a name="release-annotations-with-azure-devops-services-build"></a>Adnotacje dotyczące dzięki usługom DevOps platformy Azure kompilacji wersji

Adnotacji dotyczących wersji są funkcją usługi Azure potoki oparte na chmurze usługom DevOps platformy Azure. 

### <a name="install-the-annotations-extension-one-time"></a>Zainstaluj rozszerzenie adnotacji (raz)
Aby można było utworzyć adnotacji dotyczących wersji, należy zainstalować jedną z wielu rozszerzeń usługom DevOps platformy Azure dostępnych w witrynie Visual Studio Marketplace.

1. Zaloguj się do Twojej [usługom DevOps platformy Azure](https://visualstudio.microsoft.com/vso/) projektu.
2. W witrynie Visual Studio Marketplace [Pobierz rozszerzenie adnotacji dotyczących wersji](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)i dodaj go do swojej organizacji usługom DevOps platformy Azure.

![W prawym górnym rogu strony sieci web usługom DevOps platformy Azure, Otwórz w portalu Marketplace. Wybierz usługom DevOps platformy Azure i w obszarze Azure potoki, wybierz Zobacz więcej.](./media/annotations/10.png)

Wystarczy zrobić to raz dla swojej organizacji usługom DevOps platformy Azure. Teraz można skonfigurować adnotacji dotyczących wersji dla każdego projektu w Twojej organizacji. 

### <a name="configure-release-annotations"></a>Konfigurowanie adnotacji dotyczących wersji

Należy uzyskać osobne klucz interfejsu API dla każdego szablonu wydania usługom DevOps platformy Azure.

1. Zaloguj się do [portalu Microsoft Azure](https://portal.azure.com) , a następnie otwórz zasób usługi Application Insights monitoruje aplikację. (Lub [utwórz ją teraz](../../azure-monitor/app/app-insights-overview.md), jeśli nie zostało to zrobione jeszcze.)
2. Otwórz **dostęp do interfejsu API**, **Application Insights identyfikator**.
   
    ![W witrynie portal.azure.com otwórz zasób usługi Application Insights, a następnie wybierz pozycję Ustawienia. Otwórz dostęp do interfejsu API. Skopiuj identyfikator aplikacji](./media/annotations/20.png)

4. W osobnym oknie przeglądarki Otwórz (lub Utwórz) szablon wydania, który zarządza wdrożeń z usługom DevOps platformy Azure. 
   
    Dodaj zadanie, a następnie wybierz zadanie adnotację dotyczącą wersji widoczną Application Insights z menu.
   
    Wklej **identyfikator aplikacji** skopiowany z bloku dostęp do interfejsu API.
   
    ![W usługach infrastruktury DevOps platformy Azure Otwórz wersji, wybierz potok tworzenia wersji, a następnie wybierz edycji. Kliknij przycisk Dodaj zadanie, a następnie wybierz adnotację dotyczącą wersji widoczną Application Insights. Wklej identyfikator aplikacji szczegółowych informacji.](./media/annotations/30.png)
4. Ustaw **APIKey** pola do zmiennej `$(ApiKey)`.

5. W oknie platformy Azure Utwórz nowy klucz interfejsu API, a następnie utwórz jego kopię.
   
    ![W bloku dostęp do interfejsu API, w oknie platformy Azure kliknij przycisk Utwórz klucz interfejsu API. Podaj komentarz, sprawdź adnotacje zapisu, a następnie kliknij przycisk Generuj klucz. Skopiuj nowy klucz.](./media/annotations/40.png)

6. Otwórz kartę Konfiguracja szablonu wydania.
   
    Utwórz definicję zmiennej `ApiKey`.
   
    Wklej swój klucz interfejsu API, aby ApiKey definicji zmiennej.
   
    ![W oknie usługom DevOps platformy Azure wybierz kartę Konfiguracja, a następnie kliknij przycisk Dodaj zmienną. Ustaw nazwę, aby ApiKey i do wartości, Wklej klucz, który został wygenerowany, a następnie kliknij ikonę blokady.](./media/annotations/50.png)
7. Na koniec **Zapisz** potoku tworzenia wersji.


## <a name="view-annotations"></a>Wyświetlaj adnotacje
Teraz zawsze, gdy używasz szablonu wydania do wdrażania nowej wersji, adnotacja będą wysyłane do usługi Application Insights. Adnotacje będą wyświetlane na wykresach w Eksploratorze metryk.

Kliknięcie dowolnego znacznik adnotacji, aby otworzyć szczegółowe informacje o wersji, m.in. obiekt żądający, gałęzi kontroli źródła, zwolnij potoku, środowiska i nie tylko.

![Kliknij znacznik adnotacji w dowolnej wersji.](./media/annotations/60.png)

## <a name="create-custom-annotations-from-powershell"></a>Tworzenie niestandardowych adnotacje za pomocą programu PowerShell
Adnotacje można również utworzyć z żaden proces, który chcesz (bez użycia usługom DevOps platformy Azure). 


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

* [Tworzenie elementów roboczych](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automatyzacja przy użyciu programu PowerShell](../../azure-monitor/app/powershell.md)
