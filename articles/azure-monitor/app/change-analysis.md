---
title: Użyj analizy zmian aplikacji w Azure Monitor, aby znaleźć problemy z aplikacją sieci Web | Microsoft Docs
description: Korzystając z analizy zmian aplikacji w Azure Monitor, można rozwiązywać problemy z aplikacjami w witrynach na żywo w programie Azure App Service.
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 3efa26a1eaea8f522d9717efb0de0ec8e1682e0e
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875152"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Korzystanie z analizy zmian aplikacji (wersja zapoznawcza) w Azure Monitor

Gdy wystąpi problem lub awaria witryny na żywo, szybkie określenie głównej przyczyny ma krytyczne znaczenie. Standardowe rozwiązania do monitorowania mogą powiadamiać o problemie. Mogą nawet wskazywać, który składnik kończy się niepowodzeniem. Jednak ten alert nie zawsze natychmiast wyjaśni przyczynę niepowodzenia. Wiadomo, że witryna działała pięć minut temu, a teraz jest ona uszkodzona. Co zmieniło się w ciągu ostatnich pięciu minut? Jest to pytanie, że analiza zmian aplikacji została zaprojektowana pod kątem odpowiedzi w Azure Monitor.

W oparciu o możliwości [grafu zasobów platformy Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview)Analiza zmian zapewnia wgląd w zmiany aplikacji platformy Azure w celu zwiększenia zauważalności i zmniejszenia MTTR (średni czas naprawy).

> [!IMPORTANT]
> Analiza zmian jest obecnie w wersji zapoznawczej. Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług. Ta wersja nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane lub mogą mieć ograniczone możliwości. Aby uzyskać więcej informacji, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

## <a name="overview"></a>Omówienie

Analiza zmian wykrywa różne typy zmian z warstwy infrastruktury we wszystkich sposobach wdrażania aplikacji. Jest to dostawca zasobów platformy Azure na poziomie subskrypcji, który sprawdza zmiany zasobów w subskrypcji. Analiza zmian udostępnia dane dla różnych narzędzi diagnostycznych, które pomagają użytkownikom zrozumieć, jakie zmiany mogą powodować problemy.

Na poniższym diagramie przedstawiono architekturę analizy zmian:

![Diagram architektury sposobu, w jaki Analiza zmian pobiera zmiany danych i udostępnia je narzędziom klienckim](./media/change-analysis/overview.png)

Obecnie Analiza zmian jest zintegrowana ze środowiska **diagnozowania i rozwiązywania problemów** w aplikacji sieci Web App Service. Aby włączyć wykrywanie zmian i przeglądać zmiany w aplikacji sieci Web, zobacz sekcję *Analiza zmian dla Web Apps funkcji* w dalszej części tego artykułu.

### <a name="azure-resource-manager-deployment-changes"></a>Azure Resource Manager zmiany wdrożenia

Przy użyciu [grafu zasobów platformy Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview)Analiza zmian zawiera historyczny rekord, w jaki sposób zasoby platformy Azure obsługujące aplikację zostały zmienione w miarę upływu czasu. Analiza zmian może wykrywać na przykład zmiany w regułach konfiguracji protokołu IP, tożsamościach zarządzanych i ustawieniach protokołu SSL. Dlatego jeśli do aplikacji sieci Web zostanie dodany tag, Analiza zmian odzwierciedla zmianę. Te informacje są dostępne, `Microsoft.ChangeAnalysis` o ile dostawca zasobów został włączony w ramach subskrypcji platformy Azure.

### <a name="changes-in-web-app-deployment-and-configuration"></a>Zmiany w wdrażaniu i konfigurowaniu aplikacji sieci Web

Analiza zmian przechwytuje stan wdrożenia i konfiguracji aplikacji co 4 godziny. Może wykrywać na przykład zmiany w zmiennych środowiskowych aplikacji. Narzędzie oblicza różnice i prezentuje, co się zmieniło. W przeciwieństwie do Menedżer zasobów zmian, informacje o zmianie wdrożenia kodu mogą nie być dostępne natychmiast w narzędziu. Aby wyświetlić najnowsze zmiany w analizie zmiany, wybierz pozycję **Skanuj zmiany teraz**.

![Zrzut ekranu przedstawiający przycisk "Skanuj zmiany teraz"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Zmiany zależności

Zmiany zależności zasobów mogą również powodować problemy w aplikacji sieci Web. Na przykład jeśli aplikacja sieci Web wywołuje w pamięci podręcznej Redis, jednostka SKU pamięci podręcznej Redis może mieć wpływ na wydajność aplikacji sieci Web. Aby wykryć zmiany w zależnościach, Analiza zmian sprawdza rekord DNS aplikacji sieci Web. W ten sposób identyfikuje zmiany we wszystkich składnikach aplikacji, które mogą powodować problemy.

## <a name="change-analysis-for-the-web-apps-feature"></a>Zmień analizę dla funkcji Web Apps

W Azure Monitor Analiza zmian jest obecnie wbudowana w funkcję **diagnozowania i rozwiązywania problemów** . Uzyskaj dostęp do tego środowiska na stronie **Przegląd** aplikacji App Service.

![Zrzut ekranu przedstawiający przycisk "przegląd" i przycisk "diagnozowanie i rozwiązywanie problemów"](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Włącz analizę zmian w narzędziu diagnozowanie i rozwiązywanie problemów

1. Wybierz pozycję **dostępność i wydajność**.

    ![Zrzut ekranu przedstawiający opcje rozwiązywania problemów z "dostępnością i wydajnością"](./media/change-analysis/availability-and-performance.png)

1. Wybierz pozycję **zmiany aplikacji**. Nie jest również dostępna w przypadku **awarii aplikacji**.

   ![Zrzut ekranu przedstawiający przycisk "awarie aplikacji"](./media/change-analysis/application-changes.png)

1. Aby włączyć analizę zmian, wybierz pozycję **Włącz teraz**.

   ![Zrzut ekranu opcji "awarie aplikacji"](./media/change-analysis/enable-changeanalysis.png)

1. Włącz **analizę zmian** i wybierz pozycję **Zapisz**.

    ![Zrzut ekranu przedstawiający interfejs użytkownika "Włącz analizę zmian"](./media/change-analysis/change-analysis-on.png)


1. Aby uzyskać dostęp do analizy zmian, wybierz opcję **diagnozowanie i rozwiązywanie problemów dotyczących** > **dostępności i wydajności** > **aplikacji**. Zobaczysz Wykres podsumowujący typ zmian w czasie wraz ze szczegółami dotyczącymi tych zmian:

     ![Zrzut ekranu przedstawiający widok różnic między zmianami](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Włącz analizę zmian na dużą skalę

Jeśli Twoja subskrypcja obejmuje wiele aplikacji sieci Web, włączenie usługi na poziomie aplikacji sieci Web byłoby niewydajne. W takim przypadku postępuj zgodnie z tymi instrukcjami alternatywnymi.

### <a name="register-the-change-analysis-resource-provider-for-your-subscription"></a>Rejestrowanie dostawcy zasobów analizy zmian dla subskrypcji

1. Zarejestruj flagę funkcji zmiany analizy (wersja zapoznawcza). Ponieważ flaga funkcji jest dostępna w wersji zapoznawczej, należy ją zarejestrować, aby była widoczna dla Twojej subskrypcji:

   1. Otwórz usługę [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/).

      ![Zrzut ekranu przedstawiający zmianę Cloud Shell](./media/change-analysis/cloud-shell.png)

   1. Zmień typ powłoki na **PowerShell**.

      ![Zrzut ekranu przedstawiający zmianę Cloud Shell](./media/change-analysis/choose-powershell.png)

   1. Uruchom następujące polecenie programu PowerShell:

        ``` PowerShell
        Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to enable
        Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
        Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag
        ```

1. Zarejestruj dostawcę zasobów analizy zmian dla subskrypcji.

   - Przejdź do pozycji **subskrypcje**i wybierz subskrypcję, którą chcesz włączyć w usłudze zmiany. Następnie wybierz pozycję dostawcy zasobów:

        ![Zrzut ekranu przedstawiający sposób rejestrowania dostawcy zasobów analizy zmian](./media/change-analysis/register-rp.png)

       - Wybierz pozycję **Microsoft. ChangeAnalysis**. Następnie w górnej części strony wybierz pozycję **zarejestruj**.

       - Po włączeniu dostawcy zasobów można ustawić tag ukryty w aplikacji sieci Web w celu wykrycia zmian na poziomie wdrożenia. Aby ustawić tag ukryty, postępuj zgodnie z instrukcjami w sekcji **nie można pobrać informacji o analizie zmian**.

   - Alternatywnie można użyć skryptu programu PowerShell do zarejestrowania dostawcy zasobów:

        ```PowerShell
        Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
        ```

        Aby ustawić ukryty tag w aplikacji sieci Web przy użyciu programu PowerShell, uruchom następujące polecenie:

        ```powershell
        $webapp=Get-AzWebApp -Name <name_of_your_webapp>
        $tags = $webapp.Tags
        $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
        Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
        ```

     > [!NOTE]
     > Po dodaniu ukrytego znacznika nadal może być konieczne odczekanie maksymalnie 4 godzin przed rozpoczęciem wyświetlania zmian. Wyniki są opóźnione, ponieważ analiza zmian skanuje aplikację sieci Web tylko co 4 godziny. Harmonogram 4-godzinny ogranicza wpływ na wydajność skanowania.

## <a name="next-steps"></a>Następne kroki

- Włącz Application Insights dla [aplikacji App Services platformy Azure](azure-web-apps.md).
- Włącz Application Insights dla [maszyny wirtualnej platformy Azure i zestawu skalowania maszyn wirtualnych platformy Azure dla aplikacji hostowanych przez usługi IIS](azure-vm-vmss-apps.md).
- Dowiedz się więcej o [usłudze Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), która ułatwia analizę zmian.
