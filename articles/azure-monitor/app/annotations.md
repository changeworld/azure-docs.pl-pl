---
title: Dodawanie adnotacji do usługi Application Insights | Dokumenty firmy Microsoft
description: Dodaj znaczniki wdrażania lub kompilacji do wykresów eksploratora metryk w usłudze Application Insights.
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: 0ad773ca6a7102ac718d43dfbbf6a4f834e681a0
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010735"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Adnotacje na wykresach metryk w usłudze Application Insights

Adnotacje pokazują, gdzie wdrożono nową kompilację lub inne znaczące zdarzenia. Adnotacje ułatwiają sprawdzenie, czy zmiany miały jakikolwiek wpływ na wydajność aplikacji. Mogą one być automatycznie tworzone przez system [kompilacji usługi Azure Potoki.](https://docs.microsoft.com/azure/devops/pipelines/tasks/) Można również utworzyć adnotacje do flagi dowolnego zdarzenia, tworząc je z programu PowerShell.

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


   > [!NOTE]
   > Adnotacje wersji nie są obecnie dostępne w okienku Metryki usługi Application Insights

Teraz za każdym razem, gdy używasz szablonu wydania do wdrażania nowej wersji, adnotacja jest wysyłana do usługi Application Insights. Adnotacje można wyświetlać w następujących lokalizacjach:

Okienko użycia, w którym można również ręcznie tworzyć adnotacje wersji:

![Zrzut ekranu przedstawiający wykres słupkowy z liczbą wizyt użytkowników wyświetlanych w ciągu kilku godzin. Adnotacje wersji są wyświetlane jako zielone znaczniki wyboru nad wykresem wskazujące moment, w którym wystąpiło wydanie](./media/annotations/usage-pane.png)

W dowolnej kwerendzie skoroszytu opartej na dzienniku, w której wizualizacja wyświetla czas wzdłuż osi x.

![Zrzut ekranu przedstawiający okienko skoroszytów z kwerendą opartą na dziennikach szeregów czasowych z wyświetlonymi adnotacjami](./media/annotations/workbooks-annotations.png)

Aby włączyć adnotacje w skoroszycie, przejdź do **ustawień zaawansowanych** i wybierz pozycję **Pokaż adnotacje**.

![Zrzut ekranu przedstawiający menu Ustawienia zaawansowane ze słowami wyświetla adnotacje wyróżnione znacznikiem wyboru obok ustawienia, aby go włączyć.](./media/annotations/workbook-show-annotations.png)

Wybierz dowolny znacznik adnotacji, aby otworzyć szczegółowe informacje o wydaniu, w tym żądający, gałąź kontroli źródła, potok wydania i środowisko.

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
