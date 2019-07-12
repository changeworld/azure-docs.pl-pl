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
ms.date: 07/01/2019
ms.author: mbullwin
ms.openlocfilehash: e3ec202ba6126b150fb78c76591682f163018661
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604541"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Adnotacje na wykresy metryk w usłudze Application Insights

Adnotacje na [Eksploratora metryk](../../azure-monitor/app/metrics-explorer.md) wykresy pokazują, w której została wdrożona nowa kompilacja lub innych istotnych zdarzeń. Adnotacje, należy można łatwo sprawdzić, czy zmiany miały wpływu na wydajność aplikacji. Mogą być automatycznie tworzone przez [potoki Azure](https://docs.microsoft.com/azure/devops/pipelines/tasks/) system kompilacji. Można również utworzyć adnotacje mógł oflagować dowolne zdarzenie, które, tworząc je za pomocą programu PowerShell.

> [!NOTE]
> W tym artykule odzwierciedla przestarzałego **metryki klasycznego środowiska**. Adnotacje tylko są obecnie dostępne w środowisku klasycznym i  **[skoroszyty](../../azure-monitor/app/usage-workbooks.md)** . Aby dowiedzieć się więcej na temat bieżącego środowiska metryk, zobacz [zaawansowane funkcje Eksploratora metryk usługi Azure](../../azure-monitor/platform/metrics-charts.md).

![Przykład adnotacji](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Adnotacje dotyczące z kompilacją Azure potoki wersji

Adnotacji dotyczących wersji są funkcją usługi Azure potoki oparte na chmurze DevOps platformy Azure.

### <a name="install-the-annotations-extension-one-time"></a>Zainstaluj rozszerzenie adnotacji (raz)
Aby można było utworzyć adnotacji dotyczących wersji, należy zainstalować jedną z wielu rozszerzeń DevOps platformy Azure dostępnych w witrynie Visual Studio Marketplace.

1. Zaloguj się do Twojej [DevOps platformy Azure](https://azure.microsoft.com/services/devops/) projektu.
   
1. W Visual Studio Marketplace [adnotacji dotyczących wersji rozszerzenia](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) strony, wybierz organizację DevOps platformy Azure, a następnie wybierz pozycję **zainstalować** można dodać rozszerzenia do organizacji DevOps platformy Azure.
   
   ![Wybierz organizację DevOps platformy Azure, a następnie wybierz pozycję Zainstaluj.](./media/annotations/1-install.png)
   
Musisz zainstalować rozszerzenie jeden raz dla danej organizacji DevOps platformy Azure. Teraz można skonfigurować adnotacji dotyczących wersji dla każdego projektu w Twojej organizacji.

### <a name="configure-release-annotations"></a>Konfigurowanie adnotacji dotyczących wersji

Dla każdego z szablonów wersja potoki usługi Azure, należy utworzyć oddzielny klucz interfejsu API.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) , a następnie otwórz zasób usługi Application Insights monitoruje aplikację. Lub jeśli nie masz, [Utwórz nowy zasób usługi Application Insights](../../azure-monitor/app/app-insights-overview.md).
   
1. Otwórz **dostęp do interfejsu API** kartę i skopiuj **Application Insights identyfikator**.
   
   ![W obszarze dostępu do interfejsu API Skopiuj identyfikator aplikacji.](./media/annotations/2-app-id.png)

1. W osobnym oknie przeglądarki Otwórz lub Utwórz szablon wersji, który zarządza wdrożeń potoki usługi Azure.
   
1. Wybierz **Dodaj zadanie**, a następnie wybierz pozycję **adnotację dotyczącą wersji widoczną Application Insights** zadań z menu.
   
   ![Wybierz pozycję Dodaj zadanie, a następnie wybierz adnotację dotyczącą wersji widoczną Application Insights.](./media/annotations/3-add-task.png)
   
1. W obszarze **identyfikator aplikacji**, wklej identyfikator szczegółowych informacji aplikacji, które są kopiowane z **dostęp do interfejsu API** kartę.
   
   ![Wklej identyfikator aplikacji usługi Insights](./media/annotations/4-paste-app-id.png)
   
1. W usłudze Application Insights **dostęp do interfejsu API** wybierz **Utwórz klucz interfejsu API**. 
   
   ![Na karcie dostęp do interfejsu API wybierz pozycję Utwórz klucz interfejsu API.](./media/annotations/5-create-api-key.png)
   
1. W **Utwórz klucz interfejsu API** oknie wpisz opis, wybierz **zapis adnotacji**, a następnie wybierz pozycję **Wygeneruj klucz**. Skopiuj nowy klucz.
   
   ![W oknie klucza interfejsu API tworzenia wpisz opis, wybierać adnotacje do zapisu, a następnie wybierz Wygeneruj klucz.](./media/annotations/6-create-api-key.png)
   
1. W oknie Szablon wydania na **zmienne** zaznacz **Dodaj** utworzyć definicję zmiennej, aby uzyskać nowy klucz interfejsu API.

1. W obszarze **nazwa**, wprowadź `ApiKey`, a następnie w obszarze **wartość**, Wklej klucz interfejsu API został skopiowany z **dostęp do interfejsu API** kartę.
   
   ![Na karcie Zmienne DevOps platformy Azure wybierz pozycję Dodaj, nazwa zmiennej ApiKey i Wklej klucz interfejsu API w obszarze wartości.](./media/annotations/7-paste-api-key.png)
   
1. Wybierz **Zapisz** w oknie szablonów wersja główna, aby zapisać szablon.

## <a name="view-annotations"></a>Wyświetlaj adnotacje
Teraz zawsze, gdy używasz szablonu wydania do wdrażania nowej wersji, adnotacji są wysyłane do usługi Application Insights. Adnotacje są wyświetlane na wykresach w **Eksploratora metryk**.

Wybierz wszystkie znacznik adnotacji (światła Szara strzałka) aby otworzyć szczegółowe informacje o wersji, m.in. obiekt żądający, gałęzi kontroli źródła, potok wydania i środowisko.

![Wybierz znacznik adnotacji wydania.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Tworzenie niestandardowych adnotacje za pomocą programu PowerShell
Możesz użyć [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) skrypt programu PowerShell z serwisu GitHub, aby utworzyć adnotacje z żaden proces, chcesz, bez korzystania z DevOps platformy Azure. 

1. Utwórz lokalne kopie z [CreateReleaseAnnotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Wykonaj kroki z poprzedniej procedury, aby uzyskanie Identyfikatora usługi Application Insights i Utwórz klucz interfejsu API z usługi Application Insights **dostęp do interfejsu API** kartę.
   
1. Wywołaj skrypt programu PowerShell z następującym kodem, zastępując symbole zastępcze oddzielona kąta z własnymi wartościami. `-releaseProperties` Są opcjonalne. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Możesz zmodyfikować skrypt, na przykład aby utworzyć adnotacje w ciągu ostatnich.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie elementów roboczych](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automatyzacja przy użyciu programu PowerShell](../../azure-monitor/app/powershell.md)
