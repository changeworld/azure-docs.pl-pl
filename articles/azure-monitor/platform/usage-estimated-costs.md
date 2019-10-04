---
title: Monitorowanie użycia i szacowane koszty w Azure Monitor
description: Przegląd procesu korzystania z Azure Monitor użycia i szacowanych kosztów
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 787618b59cd18dd4c38892ddf0861808211671cb
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936625"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Monitorowanie użycia i szacowane koszty w Azure Monitor

> [!NOTE]
> W tym artykule opisano sposób wyświetlania użycia i szacowane koszty w wielu funkcjach monitorowania platformy Azure dla różnych modeli cen. Pokrewne artykuły dla określonych składników Azure Monitor obejmują:
> - [Zarządzanie użyciem i kosztami za pomocą dzienników Azure monitor](manage-cost-storage.md) opisuje sposób kontrolowania kosztów przez zmianę okresu przechowywania danych oraz analizowanie i zgłaszanie alertów dotyczących użycia danych.
> - [Zarządzanie użyciem i kosztami Application Insights](../../azure-monitor/app/pricing.md) opisuje sposób analizowania użycia danych w programie Application Insights.

W centrum monitorów Azure Portal na stronie **użycie i szacowane koszty** objaśniono użycie podstawowych funkcji monitorowania, takich jak [alerty, metryki, powiadomienia](https://azure.microsoft.com/pricing/details/monitor/), [Azure log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)i [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). W przypadku klientów korzystających z planów cenowych dostępnych przed 2018 kwietnia będzie to również Log Analytics użycie zakupione w ramach oferty usługi Insights i Analytics.

Na tej stronie użytkownicy mogą wyświetlić użycie zasobów przez ostatnie 31 dni, zagregowane na subskrypcję. Przechodzenia do szczegółów pokazują trendy użycia w okresie 31 dni. Do tego oszacowania należy wiele danych, dlatego należy poczekać, aż strona zostanie załadowana.

W tym przykładzie przedstawiono monitorowanie użycia i szacunkowe koszty:

![Zrzut ekranu portalu użycia i szacowanej kosztów](./media/usage-estimated-costs/001.png)

Wybierz łącze w kolumnie użycie miesięczne, aby otworzyć wykres pokazujący trendy użycia w ciągu ostatnich 31 dni:

![Zrzut ekranu przedstawiający wykres słupkowy na węzeł](./media/usage-estimated-costs/002.png)

Oto inne podobne użycie i podsumowanie kosztów. Ten przykład przedstawia subskrypcję w nowym modelu cen opartym na zużyciu 2018 kwietnia. Zwróć uwagę na brak wszystkich rozliczeń opartych na węzłach. Pozyskiwanie i przechowywanie danych dla Log Analytics i Application Insights są teraz zgłaszane na nowym wspólnym mierniku.

![Zrzut ekranu portalu użycie i szacowane koszty — kwiecień 2018](./media/usage-estimated-costs/003.png)

## <a name="pricing-model"></a>Model cen

W kwietniu 2018 [wydano nowy model cen monitorowania](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/).  Ta funkcja jest zoptymalizowana pod kątem użycia w chmurze cen opartych na użyciu ("płatność zgodnie z rzeczywistym użyciem"). Płacisz tylko za to, czego używasz, bez zobowiązań opartych na węzłach. Szczegóły nowego modelu cen są dostępne dla [alertów, metryk, powiadomień](https://azure.microsoft.com/pricing/details/monitor/), [log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) i [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Oprócz modelu "płatność zgodnie z rzeczywistym użyciem" we wrześniu 2019 dodaliśmy rezerwacje zdolności produkcyjnych dla Log Analytics, co pozwala zaoszczędzić do 25% w porównaniu z ceną płatność zgodnie z rzeczywistym użyciem. Cennik rezerwacji zdolności produkcyjnych umożliwia zakupienie rezerwacji rozpoczynającej się o 100 GB/dzień. Każde użycie powyżej poziomu rezerwacji będzie rozliczane według stawki płatności zgodnie z rzeczywistym użyciem. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/monitor/) o cenach rezerwacji pojemności.

W przypadku klientów, którzy dołączeli do Log Analytics lub Application Insights po 2 kwietnia 2018, nowy model cen jest jedyną opcją. W przypadku klientów, którzy już korzystają z tych usług, przeniesienie do nowego modelu cen jest opcjonalne.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Ocenianie wpływu nowego modelu cen
Nowy model cen będzie miał różne wpływ na poszczególnych klientów w oparciu o ich wzorce użycia monitorowania. W przypadku klientów, którzy korzystali z Log Analytics lub Application Insights przed 2 kwietnia 2018, Strona **użycie i szacowany koszt** w Azure monitor szacuje wszelkie zmiany kosztów w przypadku przechodzenia do nowego modelu cen. Umożliwia przeniesienie subskrypcji do nowego modelu. W przypadku większości klientów będzie korzystne użycie nowego modelu cen. W przypadku klientów, którzy mają szczególnie duże użycie danych lub w regionach z wyższymi kosztami, może to nie być przypadek.

Aby zobaczyć oszacowanie kosztów subskrypcji wybranych na stronie **użycie i szacowane koszty** , wybierz niebieski baner w górnej części strony. Najlepiej wykonać tę jedną subskrypcję w danym momencie, ponieważ jest to poziom, na którym można przyjąć nowy model cen.

![Monitoruj użycie i szacowane koszty na zrzucie ekranu nowego modelu cen](./media/usage-estimated-costs/004.png)

Na nowej stronie zostanie wyświetlona Podobna wersja poprzedniej strony z zielonym transparentem:

![Monitoruj użycie i szacowane koszty na zrzucie ekranu bieżącego modelu cen](./media/usage-estimated-costs/005.png)

Na stronie przedstawiono również różne zestawy liczników, które odpowiadają nowemu modelowi cen. Ta lista jest przykładem:

- Wgląd i Analytics\Overage na węzeł
- Wgląd i Analytics\Included na węzeł
- Dane nadwyżkowe aplikacji Insights\Basic
- Dane Insights\Included aplikacji

Nowy model cen nie obejmuje przydziałów danych opartych na węzłach. W związku z tym te liczniki pozyskiwania danych są połączone z nowym wspólnym miernikiem pozyskiwania danych o nazwie **Shared Services\Data**pozyskiwanie. 

Istnieje inna zmiana danych wprowadzanych do Log Analytics lub Application Insights w regionach z wyższymi kosztami. Dane dla tych regionów o wysokim koszcie zostaną wyświetlone przy użyciu nowych liczników regionalnych. Przykładem jest pozyskiwanie **danych (Zachodnie stany USA)** .

> [!NOTE]
> Szacowane koszty na subskrypcję nie są uwzględniane dla uprawnień na poziomie konta na węzeł w ramach subskrypcji pakietu Operations Management Suite (OMS). Zapoznaj się z przedstawicielem swojego konta, aby uzyskać bardziej szczegółowe omówienie nowego modelu cen w tym przypadku.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Nowy model cen i uprawnienia do subskrypcji pakietu Operations Management Suite

Klienci, którzy zakupili Microsoft Operations Management Suite E1 i E2, mają uprawnienia do pozyskiwania danych na węzłach dla [log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) i [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing). Aby otrzymywać te uprawnienia dla Log Analytics obszarów roboczych lub Application Insights zasobów w ramach danej subskrypcji: 

- Model cenowy subskrypcji musi pozostać w modelu 2018 z góry kwietnia.
- Obszary robocze Log Analytics powinny korzystać z warstwy cenowej "na węzeł (OMS)".
- Zasoby Application Insights powinny korzystać z planu cenowego "Enterprise".

W zależności od liczby węzłów pakietu zakupionej przez Twoją organizację przeniesienie niektórych subskrypcji do nowego modelu cen może być korzystne, ale wymaga to starannej uwagi. Ogólnie rzecz biorąc, zaleca się, aby zachować w modelu 2018 z góry kwietnia, jak opisano powyżej.

> [!WARNING]
> Jeśli Twoja organizacja zakupiła Microsoft Operations Management Suite E1 i E2, zazwyczaj najlepszym rozwiązaniem jest utrzymywanie subskrypcji w modelu cen z góry kwietnia 2018. 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>Zmiany po przeniesieniu do nowego modelu cen

Nowy model cen upraszcza Log Analytics i Application Insights opcje cenowe tylko do jednej warstwy (lub planu). Przeniesienie subskrypcji do nowego modelu cen spowoduje:

- Zmień warstwę cenową dla każdego Log Analytics na nową warstwę za GB (o nazwie "pergb2018" w Azure Resource Manager)
- Wszystkie zasoby Application Insights w planie Enterprise są zmieniane na plan podstawowy.

Oszacowanie kosztów pokazuje wpływ tych zmian.

> [!WARNING]
> Oto ważna Uwaga w przypadku używania Azure Resource Manager lub programu PowerShell do wdrażania [log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) lub [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell) w ramach subskrypcji przeniesionej do nowego modelu cen. W przypadku określenia warstwy cenowej/planu innego niż "pergb2018" dla Log Analytics lub "Basic" dla Application Insights, a nie niepowodzeniem wdrożenia z powodu określenia nieprawidłowej warstwy cenowej/planu, zostanie ona wykonana pomyślnie, **ale będzie używać tylko prawidłowej warstwy cenowej/ Planowanie** (nie dotyczy to log Analytics warstwy Bezpłatna, w której jest generowany nieprawidłowy komunikat warstwy cenowej).
>

## <a name="moving-to-the-new-pricing-model"></a>Przechodzenie do nowego modelu cen

Jeśli podjęto decyzję o przyjęciu nowego modelu cen dla danej subskrypcji, przejdź do każdego zasobu Application Insights, Otwórz **użycie i szacowane koszty** i upewnij się, że znajduje się w warstwie cenowej podstawowa, i przejdź do każdego log Analytics obszarze roboczym, Otwórz każdą z **nich. Strona warstwy cenowej** i przejdź do warstwy cenowej **na GB (2018)** . 

> [!NOTE]
> Wymaganie, aby wszystkie zasoby Application Insights i Log Analytics obszary robocze w ramach danej subskrypcji zostały teraz usunięte, co pozwala na większą pracę i lepszą konfigurację. 

## <a name="automate-moving-to-the-new-pricing-model"></a>Automatyzowanie przejścia do nowego modelu cen

Jak wspomniano powyżej, nie jest już wymagane przeniesienie wszystkich zasobów monitorowania w ramach subskrypcji do nowego modelu cen w tym samym czasie, w związku z czym akcja ``migratetonewpricingmodel`` nie będzie już miała żadnego efektu. Teraz można przenosić Application Insights zasoby i Log Analytics obszary robocze oddzielnie do najnowszych warstw cenowych.  

Automatyzacja tej zmiany jest udokumentowana dla Application Insights przy użyciu polecenia [Set-AzureRmApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/azurerm.applicationinsights/set-azurermapplicationinsightspricingplan) z ``-PricingPlan "Basic"`` i log Analytics za pomocą polecenia [Set-AzureRmOperationalInsightsWorkspace](https://docs.microsoft.com/powershell/module/AzureRM.OperationalInsights/Set-AzureRmOperationalInsightsWorkspace) z ``-sku "PerGB2018"``. 
