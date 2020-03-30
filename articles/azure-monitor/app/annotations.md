---
title: Dodawanie adnotacji do usługi Application Insights | Dokumenty firmy Microsoft
description: Dodaj znaczniki wdrażania lub kompilacji do wykresów eksploratora metryk w usłudze Application Insights.
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: e0e2a106b276110e13b3c68889e4d1d349ba73a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666517"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Adnotacje na wykresach metryk w usłudze Application Insights

Adnotacje na [wykresach Eksploratora metryk](../../azure-monitor/app/metrics-explorer.md) pokazują, gdzie wdrożono nową kompilację lub inne znaczące zdarzenia. Adnotacje ułatwiają sprawdzenie, czy zmiany miały jakikolwiek wpływ na wydajność aplikacji. Mogą one być automatycznie tworzone przez system [kompilacji usługi Azure Potoki.](https://docs.microsoft.com/azure/devops/pipelines/tasks/) Można również utworzyć adnotacje do flagi dowolnego zdarzenia, tworząc je z programu PowerShell.

> [!NOTE]
> Ten artykuł odzwierciedla przestarzałe **klasyczne metryki**. Adnotacje są obecnie dostępne tylko w klasycznym doświadczeniu i w **[skoroszytach](../../azure-monitor/app/usage-workbooks.md)**. Aby dowiedzieć się więcej o bieżącym metryk, zobacz [Zaawansowane funkcje Usługi Azure Metrics Explorer](../../azure-monitor/platform/metrics-charts.md).

![Przykład adnotacji](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Zwolnij adnotacje za pomocą kompilacji Potoki platformy Azure

Adnotacje dotyczące wersji są funkcją usługi Azure Pipelines opartej na chmurze usługi Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Instalowanie rozszerzenia Adnotacje (jednorazowo)
Aby móc tworzyć adnotacje dotyczące wersji, musisz zainstalować jedno z wielu rozszerzeń usługi Azure DevOps dostępnych w witrynie Visual Studio Marketplace.

1. Zaloguj się do projektu [usługi Azure DevOps.](https://azure.microsoft.com/services/devops/)
   
1. Na stronie [rozszerzenia Annotacji wydania](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) programu Visual Studio Marketplace wybierz organizację programu Azure DevOps, a następnie wybierz pozycję **Zainstaluj,** aby dodać rozszerzenie do organizacji Programu Azure DevOps.
   
   ![Wybierz organizację Programu Azure DevOps, a następnie wybierz pozycję Zainstaluj.](./media/annotations/1-install.png)
   
Wystarczy zainstalować rozszerzenie tylko raz dla organizacji Azure DevOps. Teraz można skonfigurować adnotacje wersji dla dowolnego projektu w organizacji.

### <a name="configure-release-annotations"></a>Konfigurowanie adnotacji wydania

Utwórz oddzielny klucz interfejsu API dla każdego szablonu wydania usługi Azure Pipelines.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i otwórz zasób usługi Application Insights, który monitoruje aplikację. Jeśli go nie masz, [utwórz nowy zasób usługi Application Insights](../../azure-monitor/app/app-insights-overview.md).
   
1. Otwórz kartę **Dostęp do interfejsu API** i skopiuj identyfikator usługi Application **Insights**.
   
   ![W obszarze Dostęp do interfejsu API skopiuj identyfikator aplikacji.](./media/annotations/2-app-id.png)

1. W osobnym oknie przeglądarki otwórz lub utwórz szablon wydania, który zarządza wdrożeniami usługi Azure Pipelines.
   
1. Wybierz **polecenie Dodaj zadanie**, a następnie wybierz zadanie **Antacji wydania aplikacji Insights** z menu.
   
   ![Wybierz pozycję Dodaj zadanie i wybierz pozycję Adnotacja wydania usługi Application Insights.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > Zadanie Annotacji wydania obsługuje obecnie tylko agentów opartych na systemie Windows; nie będzie działać na Linuksie, macOS ani innych typach agentów.
   
1. W obszarze **Identyfikator aplikacji**wklej identyfikator usługi Application Insights skopiowany z karty Dostęp do **interfejsu API.**
   
   ![Wklej identyfikator usługi Application Insights](./media/annotations/4-paste-app-id.png)
   
1. W oknie **Dostęp do interfejsu API** usługi Application Insights wybierz pozycję **Utwórz klucz interfejsu API**. 
   
   ![Na karcie Dostęp do interfejsu API wybierz pozycję Utwórz klucz interfejsu API.](./media/annotations/5-create-api-key.png)
   
1. W oknie **Tworzenie klucza interfejsu API** wpisz opis, wybierz pozycję Napisz **adnotacje**, a następnie wybierz pozycję **Generuj klawisz**. Skopiuj nowy klucz.
   
   ![W oknie Utwórz klucz interfejsu API wpisz opis, wybierz pozycję Napisz adnotacje, a następnie wybierz pozycję Generuj klucz.](./media/annotations/6-create-api-key.png)
   
1. W oknie szablonu wydania na karcie **Zmienne** wybierz pozycję **Dodaj,** aby utworzyć definicję zmiennej dla nowego klucza interfejsu API.

1. W **Name**obszarze `ApiKey`Nazwa wprowadź i w obszarze **Wartość**wklej klucz interfejsu API skopiowany z karty Dostęp do **interfejsu API.**
   
   ![Na karcie Zmienne programu Azure DevOps wybierz pozycję Dodaj, nazwij zmienną ApiKey i wklej klucz interfejsu API w obszarze Wartość.](./media/annotations/7-paste-api-key.png)
   
1. Wybierz **pozycję Zapisz** w głównym oknie szablonu wydania, aby zapisać szablon.

## <a name="view-annotations"></a>Wyświetlanie adnotacji
Teraz za każdym razem, gdy używasz szablonu wydania do wdrażania nowej wersji, adnotacja jest wysyłana do usługi Application Insights. Adnotacje są wyświetlane na wykresach w **Eksploratorze metryk**.

Wybierz dowolny znacznik adnotacji (strzałka jasnoszary), aby otworzyć szczegółowe informacje o wydaniu, w tym żądanie, gałąź kontroli źródła, potok zwalniania i środowisko.

![Wybierz znacznik adnotacji wydania.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Tworzenie niestandardowych adnotacji z programu PowerShell
Skrypt [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) PowerShell z usługi GitHub służy do tworzenia adnotacji z dowolnego procesu, który ci się podoba, bez użycia usługi Azure DevOps. 

1. Utwórz lokalną kopię [createreleaseannotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Użyj kroków w poprzedniej procedurze, aby uzyskać identyfikator usługi Application Insights i utworzyć klucz interfejsu API z karty **dostęp do interfejsu API** usługi Application Insights.
   
1. Wywołanie skryptu programu PowerShell z następującym kodem, zastępując symbole zastępcze z nawiasami kątowymi wartościami. Są `-releaseProperties` opcjonalne. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Można zmodyfikować skrypt, na przykład, aby utworzyć adnotacje dla przeszłości.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie elementów roboczych](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automatyzacja przy użyciu programu PowerShell](../../azure-monitor/app/powershell.md)
