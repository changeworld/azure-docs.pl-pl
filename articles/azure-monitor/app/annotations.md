---
title: Adnotacje wydania dla Application Insights | Microsoft Docs
description: Dodaj znaczniki wdrożenia lub kompilacji do wykresów Eksploratora metryk w Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/01/2019
ms.openlocfilehash: 9dbdd683a8545e0f8c573dfba60daa96ef5ff08d
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677853"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Adnotacje na wykresach metryk w Application Insights

Adnotacje na wykresach [Eksplorator metryk](../../azure-monitor/app/metrics-explorer.md) pokazują, gdzie wdrożono nową kompilację lub inne istotne zdarzenia. Adnotacje ułatwiają sprawdzenie, czy zmiany miały wpływ na wydajność aplikacji. Mogą być tworzone automatycznie przez system kompilacji [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/tasks/) . Możesz również utworzyć adnotacje, aby oflagować dowolne wydarzenie, tworząc je za pomocą programu PowerShell.

> [!NOTE]
> Ten artykuł odzwierciedla przestarzałe **środowisko klasycznej metryk**. Adnotacje są obecnie dostępne tylko w klasycznym doświadczeniu i w **[skoroszytach](../../azure-monitor/app/usage-workbooks.md)** . Aby dowiedzieć się więcej na temat bieżącego środowiska metryk, zobacz [Zaawansowane funkcje usługi Azure Eksplorator metryk](../../azure-monitor/platform/metrics-charts.md).

![Przykład adnotacji](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Adnotacje wersji z kompilacją Azure Pipelines

Adnotacje wydań to funkcja Azure Pipelines opartej na chmurze usługi Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Zainstaluj rozszerzenie adnotacji (jednorazowo)
Aby można było tworzyć adnotacje wersji, należy zainstalować jeden z wielu rozszerzeń platformy Azure DevOps dostępnych w Visual Studio Marketplace.

1. Zaloguj się do projektu [usługi Azure DevOps](https://azure.microsoft.com/services/devops/) .
   
1. Na stronie [rozszerzenia Visual Studio Marketplace wersje adnotacji](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) wybierz organizację usługi Azure DevOps, a następnie wybierz pozycję **Zainstaluj** , aby dodać rozszerzenie do organizacji usługi Azure DevOps.
   
   ![Wybierz organizację usługi Azure DevOps, a następnie wybierz pozycję Zainstaluj.](./media/annotations/1-install.png)
   
Wystarczy zainstalować rozszerzenie tylko raz dla organizacji usługi Azure DevOps. Teraz można skonfigurować adnotacje wydania dla dowolnego projektu w organizacji.

### <a name="configure-release-annotations"></a>Konfigurowanie adnotacji wersji

Utwórz oddzielny klucz interfejsu API dla każdego z Azure Pipelines szablonów wydań.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i otwórz zasób Application Insights, który monitoruje aplikację. Lub jeśli go nie masz, [Utwórz nowy zasób Application Insights](../../azure-monitor/app/app-insights-overview.md).
   
1. Otwórz kartę **dostęp do interfejsu API** i skopiuj **Identyfikator Application Insights**.
   
   ![W obszarze dostęp do interfejsu API Skopiuj identyfikator aplikacji.](./media/annotations/2-app-id.png)

1. W osobnym oknie przeglądarki Otwórz lub Utwórz szablon zlecenia, który zarządza wdrożeniami Azure Pipelines.
   
1. Wybierz pozycję **Dodaj zadanie**, a następnie w menu wybierz zadanie **adnotacji Application Insights wydania** .
   
   ![Wybierz pozycję Dodaj zadanie i wybierz pozycję Application Insights adnotacja wydania.](./media/annotations/3-add-task.png)
   
1. W obszarze **Identyfikator aplikacji**wklej identyfikator Application Insights skopiowany z karty dostęp do **interfejsu API** .
   
   ![Wklej identyfikator Application Insights](./media/annotations/4-paste-app-id.png)
   
1. Wróć do okna Application Insights **dostęp do interfejsu API** wybierz pozycję **Utwórz klucz interfejsu API**. 
   
   ![Na karcie dostęp do interfejsu API wybierz pozycję Utwórz klucz interfejsu API.](./media/annotations/5-create-api-key.png)
   
1. W oknie **Tworzenie klucza interfejsu API** wpisz opis, wybierz pozycję **Zapisz adnotacje**, a następnie wybierz pozycję **Generuj klucz**. Skopiuj nowy klucz.
   
   ![W oknie Tworzenie klucza interfejsu API wpisz opis, wybierz pozycję Zapisz adnotacje, a następnie wybierz pozycję Generuj klucz.](./media/annotations/6-create-api-key.png)
   
1. W oknie szablon zlecenia na karcie **zmienne** wybierz pozycję **Dodaj** , aby utworzyć definicję zmiennej dla nowego klucza interfejsu API.

1. W obszarze **Nazwa**wprowadź `ApiKey` i w obszarze **wartość**Wklej klucz interfejsu API skopiowany z karty dostęp do **interfejsu API** .
   
   ![Na karcie zmienne usługi Azure DevOps wybierz pozycję Dodaj, nazwij zmienną ApiKey i Wklej klucz interfejsu API w polu wartość.](./media/annotations/7-paste-api-key.png)
   
1. Wybierz pozycję **Zapisz** w oknie głównym szablonu zlecenia, aby zapisać szablon.

## <a name="view-annotations"></a>Wyświetl adnotacje
Teraz za każdym razem, gdy użyjesz szablonu zlecenia do wdrożenia nowej wersji, adnotacja zostanie wysłana do Application Insights. Adnotacje są wyświetlane na wykresach w **Eksplorator metryk**.

Wybierz dowolny znacznik adnotacji (jasnoszara strzałka), aby otworzyć szczegółowe informacje o wydaniu, w tym obiekt żądający, gałąź kontroli źródła, potok wersji i środowisko.

![Wybierz znacznik adnotacji wydania.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Tworzenie adnotacji niestandardowych w programie PowerShell
Możesz użyć skryptu programu [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) PowerShell z usługi GitHub, aby utworzyć adnotacje z dowolnego procesu, który ma być używany, bez używania platformy Azure DevOps. 

1. Utwórz lokalną kopię [CreateReleaseAnnotation. ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Wykonaj kroki opisane w poprzedniej procedurze, aby pobrać identyfikator Application Insights i utworzyć klucz interfejsu API z karty Application Insights **dostęp do interfejsu API** .
   
1. Wywołaj skrypt programu PowerShell przy użyciu następującego kodu, zastępując symbole zastępcze w nawiasach kwadratowych wartościami. @No__t_0 są opcjonalne. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Możesz zmodyfikować skrypt, na przykład aby utworzyć adnotacje dla przeszłości.

## <a name="next-steps"></a>Następne kroki

* [Utwórz elementy robocze](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automatyzacja przy użyciu programu PowerShell](../../azure-monitor/app/powershell.md)
