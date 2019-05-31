---
title: Aplikacji usługi Azure Monitor zmienić analizy — odnajdywanie zmian, które mogą mieć wpływ na aktywnej witryny problemów/awarii, za pomocą aplikacji usługi Azure Monitor zmienić analizy | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z problemów z aktywną witryną aplikacji w usłudze Azure App Services za pomocą analizy zmiany aplikacji usługi Azure Monitor
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 5bd3816e65398283de85b4551a137b3f97db4cc7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226329"
---
# <a name="application-change-analysis-public-preview"></a>Analiza zmian aplikacji (publiczna wersja zapoznawcza)

W przypadku awarii lokacji na żywo problem/szybkiego określenia głównej przyczyny ma krytyczne znaczenie. Standardowe rozwiązania do monitorowania mogą pomóc Ci szybko zidentyfikować, że występuje problem, a często nawet określają składnik, kończy się niepowodzeniem. Ale to zawsze nie zaprowadzi do natychmiastowego wyjaśnienie, dlaczego występuje błąd. Witryny pracował pięć minut temu, teraz jest on uszkodzony. Co zmieniło się w ciągu ostatnich pięciu minut? Jest to pytanie, na które nowej funkcji usługi Azure Monitor zmiany analiza jest przeznaczona do odpowiedzi. Dzięki możliwości [wykres zasobów Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview) zmiany analiza zapewnia wgląd w zmiany aplikacji systemu Azure, aby zwiększyć observability i zmniejszyć MTTR (średniego czasu naprawy).

> [!IMPORTANT]
> Analiza zmiany aplikacji w usłudze Azure Monitor jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview-of-change-analysis-service"></a>Omówienie usługi analizy zmiany
Usługa analizy zmiany wykrywa różnego rodzaju zmiany z warstwy infrastruktury do wdrożenia aplikacji. To jest dostawca zasobów platformy Azure na poziomie subskrypcji, wygląda na zmian zasobów w subskrypcji, która zapewnia dane dotyczące różnych narzędzia diagnostyczne ułatwić użytkownikom zrozumienie, jakie zmiany mogą powodować problemy.

Na poniższym diagramie przedstawiono architekturę usług analysis Services zmiany: ![Diagram architektury sposób uzyskiwania usługa analityczna zmiany zmianie danych i przekazywania danych do narzędzia klienckie](./media/change-analysis/overview.png)

Obecnie narzędzie jest zintegrowany z usług aplikacji w aplikacji sieci web, diagnozowanie i rozwiązywanie problemów środowisko. Zobacz *zmiany Analysis Services dla aplikacji sieci Web usługi App* sekcję dotyczącą sposobu włączania i wyświetlić zmiany wprowadzone do aplikacji sieci web.

### <a name="azure-resource-manager-deployment-changes"></a>Platforma Azure zmienia wdrażania usługi Resource Manager
Wykorzystując [wykres zasobów Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview) narzędzia analizy zmian zapewnia historyczny zapis jak zmieniono zasobów platformy Azure, które hostują aplikację wraz z upływem czasu. Na przykład jeśli aplikacja sieci web do niej dodać tag, zmiany zostaną odzwierciedlone w narzędzia do analizy zmiany.
Te informacje są zawsze dostępne tak długo, jak `Microsoft.ChangeAnalysis` dostawcy zasobów jest dołączona do subskrypcji platformy Azure.

### <a name="web-application-deployment-and-configuration-changes"></a>Zmian konfiguracji i wdrażania aplikacji sieci Web
Narzędzie do analizy zmiany przechwytuje stan wdrażania i konfigurowania aplikacji co 4 godziny obliczeniowe różnice i przedstawiają co zmieniło się. Przykładami takich zmian zmiany zmiennych środowiska aplikacji, zmiany reguł konfiguracji adresów IP, zmiany tożsamości usługi zarządzanej, zmiany ustawień protokołu SSL i tak dalej.
Inaczej niż w przypadku zmiany usługi Resource Manager tego rodzaju informacje o zmianach nie może być dostępny natychmiast za pomocą narzędzia. Aby wyświetlić najnowsze zmiany, użyj przycisku "Skanowania zmienia się teraz" w narzędziu.

![Zrzut ekranu przedstawiający skanowanie w poszukiwaniu zmian teraz przycisk w diagnozowania i rozwiązywania problemów narzędzie dzięki integracji analizy zmiany z usługami app service Web Apps](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Zmiany zależności
Zależności zasobów mogą być również przyczyny problemów. Na przykład jeśli aplikacja sieci web wywołuje w pamięci podręcznej Redis, wydajności aplikacji sieci web mogą mieć wpływ pamięci podręcznej redis Cache jednostki SKU. Zmiany analysis service także prezentować zależności informacje o zmianach, aby zidentyfikować zmiany wszystkich składników aplikacji, która może być przyczyną problemów przyjrzeć rekord DNS aplikacji sieci web.


## <a name="change-analysis-service-for-app-services-web-app"></a>Zmień Analysis Services dla aplikacji sieci Web usługi aplikacji

Analiza zmiany aplikacji w usłudze Azure Monitor jest obecnie wbudowany w samoobsługi **diagnozowanie i rozwiązywanie problemów** środowisko, które są dostępne z **Przegląd** sekcji usługi Azure App Service Aplikacja:

![Zrzut ekranu w usłudze Azure App Service — Omówienie strony czerwone pola wokół przycisku — omówienie i diagnozowanie i rozwiązywanie problemów, przycisk](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-diagnose-and-solve-problems-tool"></a>Włącz analizę zmiany w diagnozowanie i rozwiązywanie problemów narzędzia

1. Wybierz **dostępności i wydajności**

    ![Zrzut ekranu przedstawiający dostępność i wydajność opcje rozwiązywania problemów](./media/change-analysis/availability-and-performance.png)

2. Kliknij przycisk **wystąpiła awaria aplikacji** kafelka.

   ![Zrzut ekranu z kafelkiem awarii aplikacji](./media/change-analysis/application-crashes-tile.png)

3. Aby włączyć **analizy zmiany** wybierz **Włącz teraz**.

   ![Zrzut ekranu przedstawiający dostępność i wydajność opcje rozwiązywania problemów](./media/change-analysis/application-crashes.png)

4. Aby móc korzystać z pełnego zmienić zestaw funkcji analizy **zmienić analizy**, **skanowania w poszukiwaniu zmian w kodzie**, i **zawsze na** do **na** i wybierz **Zapisz**.

    ![Zrzut ekranu przedstawiający interfejs użytkownika analizy zmiany Włączanie usługi Azure App Service](./media/change-analysis/change-analysis-on.png)

    Jeśli **analizy zmiany** jest włączone, można wykryć zmiany na poziomie zasobów. Jeśli **skanowania w poszukiwaniu zmian w kodzie** jest włączone, będziesz również pliki wdrożenia są wyświetlane i zmiany w konfiguracji lokacji. Włączanie **zawsze na** zoptymalizuje zmiany skanowanie wydajność, ale może pociągnąć za sobą dodatkowe koszty z punktu widzenia rozliczeń.

5.  Gdy wszystko jest włączona, wybierając **diagnozowanie i rozwiązywanie problemów** > **dostępność i wydajność** > **wystąpiła awaria aplikacji** Umożliwia dostęp do środowiska analizy zmiany. Wykres zawiera podsumowanie typu zmian, które wystąpiły w czasie, wraz z informacjami na temat tych zmian:

     ![Zrzut ekranu przedstawiający zmiany widoku różnic](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-service-at-scale"></a>Włącz usługę Analysis zmiany na dużą skalę
Jeśli masz wiele aplikacji sieci web w ramach subskrypcji, włącz usługę na na poziomie aplikacji sieci web będzie nieefektywne. Poniżej przedstawiono niektóre instrukcje dołączania alternatywne.

#### <a name="registering-change-analysis-resource-provider-for-your-subscription"></a>Rejestrowanie dostawcy zasobów analizy zmiany dla Twojej subskrypcji

1. Zarejestruj flagi funkcji w wersji zapoznawczej analizy zmiany

    Ponieważ ta funkcja jest dostępna w wersji zapoznawczej, musisz najpierw zarejestrować flagę funkcji, aby była widoczna dla Twojej subskrypcji.
    - Otwórz usługę [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/).

    ![Zrzut ekranu przedstawiający zmian usługi Azure Cloud Shell](./media/change-analysis/cloud-shell.png)

    - Zmień typ powłoki programu PowerShell:

    ![Zrzut ekranu przedstawiający zmian usługi Azure Cloud Shell](./media/change-analysis/choose-powershell.png)

    - Uruchom następujące polecenie programu PowerShell:

    ``` PowerShell

    Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to onboard
    Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
    Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag

    ```

2. Procedura Rejestruj dostawcę zasobów analizy zmiany dla subskrypcji

    - Przejdź do subskrypcji, wybierz subskrypcję, której chcesz dodać usługę zmiany, a następnie dostawców zasobów:

        ![Zrzut ekranu przedstawiający rejestrowanie RP analizy zmiany z bloku subskrypcje](./media/change-analysis/register-rp.png)

    - Wybierz *Microsoft.ChangeAnalysis* i kliknij przycisk *zarejestrować* górnej części strony.

    - Gdy dostawca zasobów jest dołączona, wykonaj instrukcje z *nie można pobrać informacji zmienić analizy* poniżej można ustawić tagu ukryte w aplikacji sieci web umożliwia wdrożenie poziom zmienić wykrywania dla aplikacji sieci web.

3. Można również do kroku 2 powyżej, można zarejestrować dostawcy zasobów za pomocą skryptu programu PowerShell:

    ```PowerShell
    Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
    ```

4. Aby ustawić ukryte tag aplikacji sieci web przy użyciu programu PowerShell, uruchom następujące polecenie:

    ```powershell
    $webapp=Get-AzWebApp -Name <name_of_your_webapp>
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
    ```

> [!NOTE]
> Po dodaniu znacznika ukryte, nadal konieczne może być początkowo czekać nawet 4 godzin najpierw wyświetlać zmiany. Jest to spowodowane freqeuncy 4 godziny, z których korzysta usługa analizy zmiany do skanowania aplikacji sieci web, jednocześnie ograniczając wpływ na wydajność skanowania.

## <a name="next-steps"></a>Kolejne kroki

- Ulepszyć monitorowanie usługi Azure App Services [przez włączenie funkcji usługi Application Insights](azure-web-apps.md) usługi Azure Monitor.
- Pomóc lepiej zrozumieć [wykres zasobów Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview) pozwalającemu aplikacja usługi Azure Monitor power zmienić analizy.
