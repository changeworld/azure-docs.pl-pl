---
title: Monitorowanie użycia i szacowanych kosztów w usłudze Azure Monitor
description: Omówienie procesu stosowania optymalizacji użycia usługi Azure Monitor i szacowane koszty strony
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 7117e7287f601b306893cb02dc5d7599d7c6224d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60453830"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Monitorowanie użycia i szacowanych kosztów w usłudze Azure Monitor

> [!NOTE]
> W tym artykule opisano sposób wyświetlania użycie i szacunkowe koszty między wiele funkcji monitorowania platformy Azure dla różnych modeli cen.  Zapoznaj się z następującymi artykułami, aby uzyskać powiązane informacje.
> - [Zarządzanie kosztami przez kontrolowanie ilości danych i przechowywania w usłudze Log Analytics](manage-cost-storage.md) opisano, jak kontrolować ponoszone koszty, zmieniając okresu przechowywania danych.
> - [Analizowanie użycia danych w usłudze Log Analytics](../../azure-monitor/platform/data-usage.md) w tym artykule opisano sposób analizowanie i alerty dotyczące użycia danych.
> - [Zarządzanie cenami i ilością danych w usłudze Application Insights](../../azure-monitor/app/pricing.md) opisano, jak i analizowanie użycia danych w usłudze Application Insights.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W Centrum monitora w witrynie Azure Portal **użycie i szacunkowe koszty** stronie wyjaśniamy użycia podstawowej platformy monitorowania funkcji, takich jak [, alertów metryk, powiadomienia](https://azure.microsoft.com/pricing/details/monitor/), [usługi Azure Log Analytics ](https://azure.microsoft.com/pricing/details/log-analytics/), i [usługi Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). W przypadku klientów na plany cenowe dostępne przed kwietnia 2018 r. obejmuje to także użycie usługi Log Analytics, które zostały zakupione w ramach wgląd w dane i Analytics oferuje.

Na tej stronie użytkownicy mogą wyświetlać ich użycia zasobów w ciągu ostatnich 31 dni, zagregowane na subskrypcję. Dodatki testowania odzyskiwania po awarii pokazuje trendy użycia przez 31-dniowego okresu. Dużą ilość danych, trzeba łączą się na te dane szacunkowe, dlatego prosimy o cierpliwość, ponieważ ładowania strony.

Ten przykład przedstawia sposób monitorowania użycia i oszacowanie kosztów wynikowy:

![Użycie i szacowane koszty portalu zrzut ekranu](./media/usage-estimated-costs/001.png)

Wybierz link w kolumnie miesięczne użycie, aby Otwórz wykres, który pokazuje trendy użycia w ciągu ostatnich 31 dni:

![Uwzględnione na węzeł paska wykresu zrzut ekranu](./media/usage-estimated-costs/002.png)

Oto inny podobny sposób użycia i kosztów — podsumowanie. Ten przykład przedstawia subskrypcji w nowym kwietnia 2018 r. na podstawie użycia modelu cen. Należy zwrócić uwagę braku dowolnego rozliczania opartego na węźle. Pozyskiwanie danych i przechowywania dla usługi Log Analytics i usługi Application Insights teraz są zgłaszane na nowego licznika wspólnej.

![Użycie i szacowane koszty portalu zrzut ekranu — ceny z kwietnia 2018 r.](./media/usage-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Nowy model cen

W kwietniu 2018 r. [monitorowania nowego modelu cen został wydany](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/).  Funkcje, cennik przyjazny dla chmury, na podstawie użycia. Płacisz tylko za rzeczywiste użycie bez zobowiązań oparte na węzłach. Szczegóły nowego modelu cen są dostępne dla [, alertów metryk, powiadomienia](https://azure.microsoft.com/pricing/details/monitor/), [usługi Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) i [usługi Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Dla klientów dołączanie do usługi Log Analytics lub usługi Application Insights po 2 kwietnia 2018 r. nowy model cen jest jedyną opcją. W przypadku klientów, którzy używają już tych usług przejście do nowego modelu cen jest opcjonalne.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Ocena wpływu nowego modelu cen
Nowy model cen będzie mieć inny wpływ na poszczególnych klientów, na podstawie ich monitorowania wzorców użycia. Dla klientów, którzy zostały przy użyciu usługi Log Analytics lub usługi Application Insights przed 2 kwietnia 2018 r. **użycia i szacowanych kosztów** strony w usłudze Azure Monitor szacuje jakichkolwiek zmian w koszty, jeśli zostały przeniesione do nowego modelu cen. Zapewnia sposób, aby przenieść subskrypcję do nowego modelu. W przypadku większości klientów nowego modelu cen będzie korzystne. W przypadku klientów z wzorców użycia szczególnie dużej ilości danych lub w regionach wyższe koszty może to być nie tak.

Aby wyświetlić oszacowanie kosztów dla subskrypcji, które wybrano na **użycie i szacunkowe koszty** wybierz niebieski Baner w górnej części strony. Najlepiej zrobić to jedną subskrypcję, w czasie, ponieważ jest to poziom nowego modelu cen może zostać przyjęta.

![Monitoruj użycie i szacowane koszty w nowych cen zrzut ekranu z modelu](./media/usage-estimated-costs/004.png)

Nowa strona zawiera podobne wersję strony zielony transparent z wcześniejszych:

![Monitoruj użycie i szacowane koszty w bieżącym cen zrzut ekranu z modelu](./media/usage-estimated-costs/005.png)

Na stronie znajdują się również inny zbiór liczników, które odnoszą się do nowego modelu cen. Ta lista jest przykładem:

- Usługa Insight and Analytics\Overage na węzeł
- Usługa Insight and Analytics\Included na węzeł
- Dane nadwyżkowe Insights\Basic aplikacji
- Dane Insights\Included aplikacji

Nowy model cen nie ma alokacje oparte na węzłach uwzględnione dane. W związku z tym, te liczniki pozyskiwania danych są łączone w nowego licznika przyjmowanie wspólnych danych o nazwie **udostępnione pozyskiwania Services\Data**. 

Istnieje inna zmiana na dane pozyskane do usługi Log Analytics lub usługi Application Insights w regionach z wyższych kosztów. Dane dotyczące tych regionów kosztu wysokiej będą wyświetlane przy użyciu nowego mierniki regionalne. Na przykład **pozyskiwanie danych (zachodnio-środkowe stany USA)**.

> [!NOTE]
> Na subskrypcję szacowane koszty nie wziąć pod uwagę na poziomie konta na węźle uprawnień subskrypcji usługi Operations Management Suite (OMS). Zapoznaj się z przedstawicielem klienta dla bardziej szczegółowym omówieniem nowego modelu cen w tym przypadku.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Nowy cennik modelu i uprawnienia subskrypcji pakietu Operations Management Suite

Klienci, którzy zakupili pakiet Microsoft Operations Management Suite E1 i E2 kwalifikują się do każdego węzła uprawnień do wprowadzania danych dla [usługi Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) i [usługi Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing). Aby otrzymać te uprawnienia dla obszarów roboczych usługi Log Analytics i zasoby usługi Application Insights w ramach danej subskrypcji: 

- Model cen subskrypcji musi pozostać w modelu sprzed kwietnia 2018 r.
- Obszary robocze usługi log Analytics, należy używać "za węzeł (OMS)" warstwy cenowej.
- Zasoby usługi Application Insights, należy użyć "Enterprise" plan cenowy.

W zależności od liczby węzłów pakietu nabytym przez organizację przeniesienie niektórych subskrypcji do nowego modelu cen może być korzystne, ale wymaga to szczególną uwagę. Ogólnie rzecz biorąc zalecane jest po prostu, aby pozostać w modelu sprzed kwietnia 2018 r. zgodnie z powyższym opisem.

> [!WARNING]
> Jeśli Twoja organizacja zakupiła pakiet Microsoft Operations Management Suite E1 i E2, zazwyczaj najlepiej jest zapewnienie subskrypcji w modelu cenowym sprzed kwietnia 2018 r. 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>Zmiany w przypadku przenoszenia do nowego modelu cen

Nowy model cen upraszcza usługi Log Analytics i opcje do tylko jednej warstwy (lub planu) ceny usługi Application Insights. Przeniesienie subskrypcji do nowego będzie modelu cen:

- Zmień warstwę cenową dla każdej usługi Log Analytics na nową warstwę na GB (o nazwie "pergb2018" w usłudze Azure Resource Manager)
- Wszystkie zasoby usługi Application Insights w planie Enterprise jest zmieniany na Basic plan.

Szacowanie kosztów pokazuje wpływ tych zmian.

> [!WARNING]
> Ważna uwaga, jeśli używasz usługi Azure Resource Manager lub programu PowerShell do wdrożenia w tym miejscu [usługi Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) lub [usługi Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell) w ramach subskrypcji zostały przeniesione do nowego modelu cen. Jeśli określisz warstwy/planu cenowego innych niż "pergb2018" dla usługi Log Analytics lub "Podstawowa" dla usługi Application Insights, zamiast niepowodzenie wdrożenia ze względu na określanie nieprawidłowy warstwy/plan cenowy ona powiedzie się **, ale będzie używać jedyne prawidłowe Warstwa/plan cenowy** (nie dotyczy organizacji Log Analytics bezpłatna którym generowany jest komunikat nieprawidłowy warstwy cenowej).
>

## <a name="moving-to-the-new-pricing-model"></a>Przejście do nowego modelu cen

Jeśli zdecydujesz, który wdrożył nowy model cen w ramach danej subskrypcji, przejdź do każdego zasobu usługi Application Insights, otwórz **użycie i szacunkowe koszty** Pamiętaj, że jest on warstwy cenowej podstawowa i przejdź do każdej usługi Log Analytics obszar roboczy, Otwórz każdy **warstwa cenowa** strony i zmienić **na GB (2018)** warstwy cenowej. 

> [!NOTE]
> Wymóg, że wszystkie zasoby usługi Application Insights i obszarów roboczych usługi Log Analytics w ramach danej subskrypcji przyjąć najnowszej model cen zostanie usunięte, dzięki czemu większa elastyczność i łatwiejsze konfiguracji. 

## <a name="automate-moving-to-the-new-pricing-model"></a>Automatyzowanie przenoszenia do nowego modelu cen

Jak wspomniano powyżej, nie jest już wymagane Przenieś wszystkie zasoby monitorowania w ramach subskrypcji do nowego modelu cen, w tym samym czasie, a wówczas ``migratetonewpricingmodel`` akcji nie będzie już mieć żadnego efektu. Teraz można przenieść zasobów usługi Application Insights i obszary robocze usługi Log Analytics oddzielnie do najnowszych warstw cenowych.  

Automatyzowanie ta zmiana jest udokumentowany dla usługi Application Insights przy użyciu [AzureRmApplicationInsightsPricingPlan zestaw](https://docs.microsoft.com/powershell/module/azurerm.applicationinsights/set-azurermapplicationinsightspricingplan) z ``-PricingPlan "Basic"`` i przy użyciu usługi Log Analytics [Set-AzureRmOperationalInsightsWorkspace](https://docs.microsoft.com/powershell/module/AzureRM.OperationalInsights/Set-AzureRmOperationalInsightsWorkspace) z ``-sku "PerGB2018"``. 

