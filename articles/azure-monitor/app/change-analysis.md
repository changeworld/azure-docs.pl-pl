---
title: Użyj analizy zmiany aplikacji w usłudze Azure Monitor, aby znaleźć problemy z aplikacji sieci web | Dokumentacja firmy Microsoft
description: Umożliwia zmianę analiza w usłudze Azure Monitor Rozwiązywanie problemów z aplikacjami w witrynach na żywo w usłudze Azure App Service.
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 45df8f9e57223ea60a11c6af2187d362184cae2b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443371"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Użyj analizy zmiany aplikacji (wersja zapoznawcza) w usłudze Azure Monitor

W przypadku wystąpienia problemu z aktywnej witryny lub awarii szybkie ustalenie głównej przyczyny problemu ma krytyczne znaczenie. Standardowe rozwiązania do monitorowania może informujące o problem. Może nawet wskazywać, które można załadować składnika. Jednak ten alert nie będzie zawsze od razu opisano przyczyny niepowodzenia. Dostęp do witryny przepracowanych pięć minut temu i teraz jest uszkodzony. Co zmieniło się w ciągu ostatnich pięciu minut? Jest to pytanie, analiza zmiany aplikacji jest przeznaczony do odpowiedzi w usłudze Azure Monitor.

Opierając się na możliwości [wykres zasobów Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview), zmiana analizy zapewnia wgląd w zmiany aplikacji platformy Azure, aby zwiększyć observability i zmniejszyć MTTR (średniego czasu naprawy).

> [!IMPORTANT]
> Zmiana analizy jest obecnie w wersji zapoznawczej. Ta wersja zapoznawcza jest dostarczane bez umowy dotyczącej poziomu usług. Ta wersja nie jest zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane lub mogą mieć ograniczone możliwości. Aby uzyskać więcej informacji, zobacz [dodatkowe warunki użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Przegląd

Zmiana analizy wykrywa różnego rodzaju zmiany z warstwy infrastruktury do wdrożenia aplikacji. Jest dostawcę zasobów platformy Azure na poziomie subskrypcji, który sprawdza, czy zmiany zasobów w subskrypcji. Zmiany analizy zapewnia dane dotyczące różnych narzędzia diagnostyczne ułatwić użytkownikom zrozumienie, jakie zmiany mogą powodować problemy.

Na poniższym diagramie przedstawiono architekturę analizy zmiany:

![Diagram architektury, jak zmienić analizy pobiera zmian danych i przekazuje go do narzędzia klienckie](./media/change-analysis/overview.png)

Obecnie jest zintegrowana analiza zmiany **diagnozowanie i rozwiązywanie problemów** środowiska w aplikacji sieci web usługi App Service. Aby włączyć wykrywanie zmian i wyświetlić zmiany w aplikacji sieci web, zobacz *zmienić analizy dla funkcji Web Apps* sekcję w dalszej części tego artykułu.

### <a name="azure-resource-manager-deployment-changes"></a>Platforma Azure zmienia wdrażania usługi Resource Manager

Za pomocą [wykres zasobów Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview), zmiana analizy zapewnia historyczny zapis jak zmieniono zasobów platformy Azure, które hostują aplikację wraz z upływem czasu. Na przykład wykrywać zmiany analizy, zmienia reguły konfiguracji adresu IP, zarządzanych tożsamości i ustawienia protokołu SSL. Dlatego jeśli tag jest dodawany do aplikacji sieci web, zmień analizy odzwierciedla zmianę. Ta informacja jest dostępna tak długo, jak `Microsoft.ChangeAnalysis` dostawcy zasobów jest włączona w subskrypcji platformy Azure.

### <a name="changes-in-web-app-deployment-and-configuration"></a>Zmiany we wdrożeniu aplikacji sieci web i konfiguracji

Zmiana analizy przechwytuje stan wdrażania i konfigurowania aplikacji co 4 godziny. Potrafi wykrywać, na przykład zmiany w zmiennych środowiskowych aplikacji. Narzędzie oblicza różnice i przedstawia, co zmieniło się. Inaczej niż w przypadku zmiany usługi Resource Manager informacje na temat wdrażania zmian kodu nie może być dostępny natychmiast za pomocą narzędzia. Aby wyświetlić najnowsze zmiany w analizie zmian, wybierz **skanowania zmienia się teraz**.

![Zrzut ekranu przedstawiający przycisk "Skanowania zmienia się teraz"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Zmiany zależności

Zmiany zależności zasobów także mogą powodować problemy w aplikacji sieci web. Na przykład jeśli aplikacja sieci web wywołuje w pamięci podręcznej Redis, pamięć podręczna Redis jednostki SKU mogą mieć wpływ na wydajność aplikacji sieci web. Aby wykryć zmiany, w zależności, analizy zmiany sprawdza rekord DNS aplikacji sieci web. Dzięki temu identyfikuje zmiany w wszystkie składniki aplikacji, które mogą powodować problemy.

## <a name="change-analysis-for-the-web-apps-feature"></a>Analiza zmian w funkcji Web Apps

W usłudze Azure Monitor zmiany analizy aktualnie jest wbudowana w samoobsługi **diagnozowanie i rozwiązywanie problemów** środowiska. Dostęp do tego środowiska z **Przegląd** strony aplikację usługi App Service.

![Zrzut ekranu przedstawiający przycisk "Przegląd" i "diagnozowanie i rozwiązywanie problemów" przycisk](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Włącz analizę zmiany w diagnozowanie i rozwiązywanie problemów narzędzia

1. Wybierz **dostępność i wydajność**.

    ![Zrzut ekranu przedstawiający "Dostępność i wydajność" Opcje rozwiązywania problemów](./media/change-analysis/availability-and-performance.png)

1. Wybierz **zmian aplikacji**. Nie, że ta funkcja jest również dostępna w **wystąpiła awaria aplikacji**.

   ![Zrzut ekranu przedstawiający przycisk "Wystąpiła awaria aplikacji"](./media/change-analysis/application-changes.png)

1. Aby włączyć analizę zmiany, wybierz **Włącz teraz**.

   ![Zrzut ekranu z opcjami "Wystąpiła awaria aplikacji"](./media/change-analysis/enable-changeanalysis.png)

1. Włącz **analizy zmiany** i wybierz **Zapisz**.

    ![Zrzut ekranu przedstawiający interfejs użytkownika "Włącz analizę zmiany"](./media/change-analysis/change-analysis-on.png)


1. Aby uzyskać dostęp do analizy zmiany, wybierz pozycję **diagnozowanie i rozwiązywanie problemów** > **dostępność i wydajność** > **wystąpiła awaria aplikacji**. Zostanie wyświetlony wykres, który zawiera podsumowanie typów zmian, wraz z upływem czasu, wraz z informacjami na temat tych zmian:

     ![Zrzut ekranu przedstawiający widok różnic zmiany](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Włącz analizę zmiany na dużą skalę

Jeśli Twoja subskrypcja obejmuje wiele aplikacji sieci web, może być nieefektywne włączania usługi na poziomie aplikacji sieci web. W tym przypadku wykonaj te instrukcje alternatywne.

### <a name="register-the-change-analysis-resource-provider-for-your-subscription"></a>Zarejestruj dostawcę zasobów analizy zmiany dla Twojej subskrypcji

1. Zarejestruj się flagi funkcji analizy zmian (wersja zapoznawcza). Ponieważ flaga funkcji jest dostępna w wersji zapoznawczej, musisz zarejestrować, aby była widoczna dla Twojej subskrypcji:

   1. Otwórz usługę [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/).

      ![Zrzut ekranu przedstawiający zmiany w usłudze Cloud Shell](./media/change-analysis/cloud-shell.png)

   1. Zmień typ powłoki na **PowerShell**.

      ![Zrzut ekranu przedstawiający zmiany w usłudze Cloud Shell](./media/change-analysis/choose-powershell.png)

   1. Uruchom następujące polecenie programu PowerShell:

        ``` PowerShell
        Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to enable
        Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
        Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag
        ```

1. Zarejestruj dostawcę zasobów analizy zmiany dla subskrypcji.

   - Przejdź do **subskrypcje**i wybierz subskrypcję, aby włączyć w usłudze zmiany. Następnie wybierz dostawców zasobów:

        ![Zrzut ekranu przedstawiający sposób zarejestrować dostawcę zasobów analizy zmiany](./media/change-analysis/register-rp.png)

       - Wybierz **Microsoft.ChangeAnalysis**. Następnie w górnej części strony wybierz pozycję **zarejestrować**.

       - Po włączeniu dostawcy zasobów można ustawić ukryte tagu w aplikacji sieci web, aby wykryć zmiany na poziomie wdrożenia. Można ustawić tagu ukryte, postępuj zgodnie z instrukcjami zawartymi w sekcji **nie można pobrać informacji analizy zmiany**.

   - Alternatywnie służy skrypt programu PowerShell można zarejestrować dostawcy zasobów:

        ```PowerShell
        Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
        ```

        Aby ustawić ukryte tagu dla aplikacji sieci web przy użyciu programu PowerShell, uruchom następujące polecenie:

        ```powershell
        $webapp=Get-AzWebApp -Name <name_of_your_webapp>
        $tags = $webapp.Tags
        $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
        Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
        ```

     > [!NOTE]
     > Po dodaniu znacznika ukryte, nadal może być konieczne poczekaj maksymalnie 4 godziny przed rozpoczęciem wyświetlania zmian. Wyniki są opóźnione, ponieważ zmiana analizy skanuje aplikację sieci web co 4 godziny. Harmonogram 4 godzin ogranicza wpływ na wydajność skanowania.

## <a name="next-steps"></a>Kolejne kroki

- Monitorowanie usługi App Service efektywniej przez [włączenie funkcji usługi Application Insights](azure-web-apps.md) w usłudze Azure Monitor.
- Dowiedz się więcej o [wykres zasobów Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview), która pomaga power analizy zmiany.
