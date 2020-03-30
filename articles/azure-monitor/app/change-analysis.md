---
title: Znajdowanie problemów z aplikacjami sieci Web za pomocą analizy zmian aplikacji w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Analiza zmian aplikacji w usłudze Azure Monitor umożliwia rozwiązywanie problemów z aplikacjami w witrynach na żywo w usłudze Azure App Service.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: 036b8c084bdfdc11c02274758c550c76bdc7b1e7
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348727"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Korzystanie z analizy zmian aplikacji (wersja zapoznawcza) w usłudze Azure Monitor

W przypadku wystąpienia problemu lub awarii lokacji na żywo, szybkie określenie głównej przyczyny ma kluczowe znaczenie. Standardowe rozwiązania monitorowania mogą ostrzegać o problemie. Mogą nawet wskazywać, który składnik nie działa. Ale ten alert nie zawsze będzie natychmiast wyjaśnić przyczynę awarii. Wiesz, że Twoja strona działała pięć minut temu, a teraz jest zepsuta. Co zmieniło się w ciągu ostatnich pięciu minut? Jest to pytanie, na które aplikacja Change Analysis ma odpowiadać w usłudze Azure Monitor.

Opierając się na możliwości [azure resource graph,](https://docs.microsoft.com/azure/governance/resource-graph/overview)analiza zmian zapewnia wgląd w zmiany aplikacji platformy Azure, aby zwiększyć zauważalność i zmniejszyć MTTR (średni czas naprawy).

> [!IMPORTANT]
> Analiza zmian jest obecnie w wersji zapoznawczej. Ta wersja zapoznawcza jest dostarczana bez umowy dotyczącej poziomu usług. Ta wersja nie jest zalecane dla obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane lub mogą mieć ograniczone możliwości. Aby uzyskać więcej informacji, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Omówienie

Analiza zmian wykrywa różne typy zmian, od warstwy infrastruktury aż po wdrożenie aplikacji. Jest dostawcą zasobów platformy Azure na poziomie subskrypcji, który sprawdza zmiany zasobów w subskrypcji. Analiza zmian zawiera dane dla różnych narzędzi diagnostycznych, aby pomóc użytkownikom zrozumieć, jakie zmiany mogły spowodować problemy.

Na poniższym diagramie przedstawiono architekturę analizy zmian:

![Diagram architektury przedstawiający sposób, w jaki analiza zmian pobiera dane zmian i udostępnia go narzędziom klienckim](./media/change-analysis/overview.png)

Obecnie analiza zmian jest zintegrowana z **diagnozowania i rozwiązywania problemów** środowiska w aplikacji sieci web usługi App Service, a także dostępne jako autonomiczna karta w witrynie Azure portal.
Zobacz *wyświetlanie zmian dla wszystkich zasobów w usłudze Azure* sekcji, aby uzyskać dostęp do analizy zmian bloku i *analizy zmian dla aplikacji sieci Web sekcji funkcji* do korzystania z niego w portalu aplikacji sieci Web w dalszej części tego artykułu.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Zmiany właściwości śledzone przez usługę Azure Resource Manager

Za pomocą [usługi Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), Analiza zmian zapewnia historyczny rekord tego, jak zasoby platformy Azure, które hostują aplikację, uległy zmianie w czasie. Można wykryć śledzone ustawienia, takie jak tożsamości zarządzane, uaktualnianie systemu operacyjnego platformy i nazwy hostów.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Zmiany ustawień usługi Azure Resource Manager
Ustawienia, takie jak reguła konfiguracji IP, ustawienia TLS i wersje rozszerzenia nie są jeszcze dostępne w arg, więc zmień kwerendy analizy i oblicza te zmiany bezpiecznie, aby zapewnić więcej szczegółów na temat tego, co zmieniło się w aplikacji. Te informacje nie są jeszcze dostępne w usłudze Azure Resource Graph, ale będą dostępne wkrótce.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Zmiany we wdrażaniu i konfiguracji aplikacji sieci Web (zmiany w gościu)

Analiza zmian przechwytuje stan wdrożenia i konfiguracji aplikacji co 4 godziny. Może wykryć, na przykład, zmiany w zmiennych środowiska aplikacji. Narzędzie oblicza różnice i przedstawia to, co się zmieniło. W przeciwieństwie do zmian w Menedżerze zasobów informacje o zmianie kodu mogą nie być natychmiast dostępne w narzędziu. Aby wyświetlić najnowsze zmiany w analizie zmian, wybierz opcję **Skanuj zmiany teraz**.

![Zrzut ekranu przedstawiający przycisk "Skanuj zmiany teraz"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Zmiany zależności

Zmiany zależności zasobów mogą również powodować problemy w aplikacji sieci web. Na przykład jeśli aplikacja sieci web wywołuje w pamięci podręcznej Redis, SKU pamięci podręcznej Redis może mieć wpływ na wydajność aplikacji sieci web. Aby wykryć zmiany zależności, analiza zmian sprawdza rekord DNS aplikacji sieci web. W ten sposób identyfikuje zmiany we wszystkich składnikach aplikacji, które mogą powodować problemy.
Obecnie obsługiwane są następujące zależności:
- Web Apps
- Azure Storage
- Azure SQL

### <a name="enablement"></a>Włączanie
Dostawca zasobów "Microsoft.ChangeAnalysis" musi być zarejestrowany w ramach subskrypcji dla właściwości śledzonego usługi Azure Resource Manager i danych zmiany ustawień proxied, które mają być dostępne. Po wejściu do narzędzia diagnozowania i rozwiązywania problemów w aplikacji Web App lub wyświetliniu autonomicznej karty Analiza zmian ten dostawca zasobów jest automatycznie rejestrowany. Nie ma żadnych implementacji wydajności i kosztów dla subskrypcji. Po włączeniu analizy zmian dla aplikacji sieci web (lub włączenie w narzędziu Diagnozowanie i rozwiązywanie problemów) będzie to miało nieznaczny wpływ na wydajność aplikacji sieci web i bez kosztów rozliczeń.
W przypadku zmian w aplikacji sieci Web w gościu wymagane jest oddzielne włączenie do skanowania plików kodu w aplikacji sieci web. Aby uzyskać więcej informacji, zobacz Włączanie analizy zmian w sekcji [Narzędzia Diagnozowanie i rozwiązywanie problemów](https://docs.microsoft.com/azure/azure-monitor/app/change-analysis#enable-change-analysis-in-the-diagnose-and-solve-problems-tool) w dalszej części tego artykułu, aby uzyskać więcej informacji.


## <a name="viewing-changes-for-all-resources-in-azure"></a>Wyświetlanie zmian dla wszystkich zasobów na platformie Azure
W usłudze Azure Monitor istnieje autonomiczny blok dla analizy zmian, aby wyświetlić wszystkie zmiany za pomocą zasobów szczegółowych informacji i zależności aplikacji.

Wyszukaj analizę zmian na pasku wyszukiwania w witrynie Azure portal, aby uruchomić blok.

![Zrzut ekranu przedstawiający wyszukiwanie analizy zmian w witrynie Azure portal](./media/change-analysis/search-change-analysis.png)

Wybierz grupę zasobów i zasoby, aby rozpocząć wyświetlanie zmian.

![Zrzut ekranu przedstawiający blok Analizy zmian w witrynie Azure portal](./media/change-analysis/change-analysis-standalone-blade.png)

Można zobaczyć statystyki i powiązanych zasobów zależności, które hostuje aplikację. Ten widok jest przeznaczony do aplikacji zorientowanych dla deweloperów do rozwiązywania problemów.

Obecnie obsługiwane zasoby obejmują:
- Maszyny wirtualne
- Zestaw skalowania maszyny wirtualnej
- Zasoby sieci platformy Azure
- Aplikacja internetowa ze śledzeniem plików gościa i zmianami zmiennych środowiskowych

Aby uzyskać opinię, użyj przycisku wyślij changeanalysisteam@microsoft.comopinię w bloku lub wiadomości e-mail .

![Zrzut ekranu przedstawiający przycisk opinii w bloku Analiza zmian](./media/change-analysis/change-analysis-feedback.png)

## <a name="change-analysis-for-the-web-apps-feature"></a>Analiza zmian dla funkcji Aplikacji sieci Web

W usłudze Azure Monitor analiza zmian jest również wbudowana w środowisko samoobsługowe **Diagnozowanie i rozwiązywanie problemów.** Dostęp do tego środowiska ze strony **Przegląd** aplikacji usługi App Service.

![Zrzut ekranu przedstawiający przycisk "Przegląd" i przycisk "Diagnozuj i rozwiązuj problemy"](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Włącz analizę zmian w narzędziu Diagnozowanie i rozwiązywanie problemów

1. Wybierz **pozycję Dostępność i wydajność**.

    ![Zrzut ekranu przedstawiający opcje rozwiązywania problemów z dostępnością i wydajnością](./media/change-analysis/availability-and-performance.png)

1. Wybierz **pozycję Zmiany w aplikacji**. Nie, że funkcja jest również dostępna w **awarii aplikacji**.

   ![Zrzut ekranu przedstawiający przycisk "Awarie aplikacji"](./media/change-analysis/application-changes.png)

1. Aby włączyć analizę zmian, wybierz pozycję **Włącz teraz**.

   ![Zrzut ekranu przedstawiający opcje "Awarie aplikacji"](./media/change-analysis/enable-changeanalysis.png)

1. Włącz **analizę zmian** i wybierz pozycję **Zapisz**. Narzędzie wyświetla wszystkie aplikacje internetowe w ramach planu usługi app service. Przełącznik poziomu planu umożliwia włączenie analizy zmian dla wszystkich aplikacji sieci Web w ramach planu.

    ![Zrzut ekranu przedstawiający interfejs użytkownika "Włącz analizę zmian"](./media/change-analysis/change-analysis-on.png)


1. Aby uzyskać dostęp do analizy zmian, wybierz **opcję Diagnozuj i rozwiązuj problemy** > **Dostępność i wydajność** > **awarii aplikacji**. Zobaczysz wykres, który podsumowuje typ zmian w czasie wraz ze szczegółami dotyczącymi tych zmian. Domyślnie zmiany w ciągu ostatnich 24 godzin są wyświetlane w celu pomocy w rozwiązywaniu natychmiastowych problemów.

     ![Zrzut ekranu przedstawiający widok różnic zmiany](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Włącz analizę zmian na dużą skalę

Jeśli subskrypcja zawiera wiele aplikacji sieci web, włączenie usługi na poziomie aplikacji sieci web byłoby nieefektywne. Uruchom następujący skrypt, aby włączyć wszystkie aplikacje sieci web w ramach subskrypcji.

Wymagania wstępne:
* Moduł Az programu PowerShell. Postępuj zgodnie z instrukcjami dotyczącymi [instalowania modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0)

Uruchom następujący skrypt:

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"


# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```



## <a name="next-steps"></a>Następne kroki

- Włącz usługę Application Insights dla [aplikacji usługi Azure App Services](azure-web-apps.md).
- Włącz usługę Application Insights dla [maszyn wirtualnych platformy Azure i aplikacji współzawiększych platformy Azure.](azure-vm-vmss-apps.md)
- Dowiedz się więcej o [programie Azure Resource Graph,](https://docs.microsoft.com/azure/governance/resource-graph/overview)który pomaga w zasilaniu analizy zmian.
