---
title: Monitorowanie użycia i szacowanych kosztów w usłudze Azure Monitor
description: Omówienie procesu stosowania optymalizacji użycia usługi Azure Monitor i szacowane koszty strony
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/11/2018
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 7911bd398b6760fb4f83382868f040382b86cd1f
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58480550"
---
# <a name="monitoring-usage-and-estimated-costs"></a>Monitorowanie użycia i szacowanych kosztów

> [!NOTE]
> W tym artykule opisano sposób wyświetlania użycie i szacunkowe koszty między wiele funkcji monitorowania platformy Azure dla różnych modeli cen.  Zapoznaj się z następującymi artykułami, aby uzyskać powiązane informacje.
> - [Zarządzanie kosztami przez kontrolowanie ilości danych i przechowywania w usłudze Log Analytics](../../azure-monitor/platform/manage-cost-storage.md) opisano, jak kontrolować ponoszone koszty, zmieniając okresu przechowywania danych.
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

Jeśli zdecydujesz, który wdrożył nowy model cen subskrypcji, wybierz **wybór modelu cen** opcji w górnej części **użycie i szacunkowe koszty** strony:

![Monitoruj użycie i szacowane koszty w nowych cen zrzut ekranu z modelu](./media/usage-estimated-costs/006.png)

**Wybór modelu cen** zostanie otwarta strona. Pokazuje listę wszystkich subskrypcji, dla których są wyświetlane na stronie wcześniejszych:

![Zrzut ekranu wyboru modelu cen](./media/usage-estimated-costs/007.png)

Aby przenieść subskrypcję do nowego modelu cen, po prostu zaznacz pole, a następnie wybierz pozycję **Zapisz**. Można cofnąć do starszego modelu cen w taki sam sposób. Należy pamiętać, że właściciel subskrypcji lub do zmiany modelu cen. wymagane są uprawnienia współautora.

## <a name="automate-moving-to-the-new-pricing-model"></a>Automatyzowanie przenoszenia do nowego modelu cen

Poniższe skrypty wymagają modułu programu Azure PowerShell. Aby sprawdzić, czy masz najnowszą wersję, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).

Gdy masz najnowszą wersję programu Azure PowerShell, czy należy najpierw uruchomić ``Connect-AzAccount``.

``` PowerShell
# To check if your subscription is eligible to adjust pricing models.
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
```

W wyniku wartość True w obszarze isGrandFatherableSubscription wskazuje, że model cenowy dla tej subskrypcji mogą być przenoszone między modelami cenowymi. Brak wartości w obszarze optedInDate oznacza, że ta subskrypcja jest aktualnie ustawiona na poprzedni model cen.

```
isGrandFatherableSubscription optedInDate
----------------------------- -----------
                         True            
```

Aby przeprowadzić migrację tej subskrypcji do nowego modelu cen, uruchom:

```powershell
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action migratetonewpricingmodel `
 -Force
```

Aby sprawdzić, czy zmiana została pomyślnie ponownie:

```powershell
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
```

Jeśli migracja się powiodła, wynik Twojego powinna teraz wyglądać podobnie jak:

```
isGrandFatherableSubscription optedInDate                      
----------------------------- -----------                      
                         True 2018-05-31T13:52:43.3592081+00:00
```

OptInDate zawiera teraz podczas tej subskrypcji wybranych do nowego modelu cen sygnaturę czasową.

Jeśli potrzebujesz powrócić do starego modelu cen, możesz uruchomić następujące polecenie:

```powershell
 $ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action rollbacktolegacypricingmodel `
 -Force
```

Jeśli następnie ponownie uruchom poprzedni skrypt, który ma ``-Action listmigrationdate``, powinien zostać wyświetlony pusty optedInDate wartość wskazującą Twoja subskrypcja została zwrócona do starszego modelu cen.

Jeśli masz wiele subskrypcji, które chcesz zmigrować, które są obsługiwane w ramach tej samej dzierżawy można utworzyć własne wariant przy użyciu fragmentów następujące skrypty:

```powershell
#Query tenant and create an array comprised of all of your tenants subscription ids
$TenantId = <Your-tenant-id>
$Tenant =Get-AzSubscription -TenantId $TenantId
$Subscriptions = $Tenant.Id
```

Aby sprawdzić, czy wszystkie subskrypcje w ramach dzierżawy są kwalifikuje się do nowego modelu cen, można uruchomić:

```powershell
Foreach ($id in $Subscriptions)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
}
```

Skrypt może być dostosowany dalsze poprzez utworzenie skryptu, który generuje trzech tablic. Jedna tablica będzie obejmować wszystkich subskrypcji identyfikatory mających ```isGrandFatherableSubscription``` ustawiona na wartość True, a optedInDate nie ma obecnie wartość. Druga tablica żadnych subskrypcji, obecnie w nowym modelu cen. I trzeci tablicy, wypełniony ich identyfikatorów subskrypcji w Twojej dzierżawie, które nie są uprawnione do nowego modelu cen:

```powershell
[System.Collections.ArrayList]$Eligible= @{}
[System.Collections.ArrayList]$NewPricingEnabled = @{}
[System.Collections.ArrayList]$NotEligible = @{}

Foreach ($id in $Subscriptions)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
$Result= Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force

     if ($Result.isGrandFatherableSubscription -eq $True -and [bool]$Result.optedInDate -eq $False)
     {
     $Eligible.Add($id)
     }

     elseif ($Result.isGrandFatherableSubscription -eq $True -and [bool]$Result.optedInDate -eq $True)
     {
     $NewPricingEnabled.Add($id)
     }

     elseif ($Result.isGrandFatherableSubscription -eq $False)
     {
     $NotEligible.add($id)
     }
}
```

> [!NOTE]
> W zależności od liczby subskrypcji powyższy skrypt może potrwać pewien czas do uruchomienia. Z powodu użycia metody .add() okno programu PowerShell będzie echo wartości zwiększającej się wartości, jak elementy są dodawane do każdej macierzy.

Teraz, gdy masz subskrypcji, który został podzielony na trzy tablice należy dokładnie przejrzeć wyniki. Możesz chcieć wykonanie kopii zapasowej zawartości tablic, tak że można łatwo przywrócić zmiany to konieczne w przyszłości. Jeśli zdecydujesz, chcesz przekonwertować wszystkie uprawnione subskrypcje, które są aktualnie na poprzedni model cen do nowego modelu cen to zadanie teraz mogą być realizowane za pomocą:

```powershell
Foreach ($id in $Eligible)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action migratetonewpricingmodel `
 -Force
}

```
