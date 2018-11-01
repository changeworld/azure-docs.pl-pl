---
title: Skalowanie w pionie zestawów skalowania maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Jak skalowanie w pionie maszynę wirtualną w odpowiedzi na monitorowanie alertów w usłudze Azure Automation
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: manayar
ms.openlocfilehash: 8080cdf78333eed9541311ba67221c713341a21a
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741576"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Ustawia pionowe skalowania automatycznego za pomocą skalowania maszyn wirtualnych
W tym artykule opisano sposób skalowanie w pionie Azure [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/) z lub bez reprovisioning. Skalowanie w pionie maszyn wirtualnych, które nie są w zestawach skalowania, można znaleźć [skalowanie w pionie maszyn wirtualnych platformy Azure z usługą Azure Automation](../virtual-machines/windows/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Skalowanie w pionie, znany także jako *skalowanie w górę* i *skalowanie w dół*, oznacza, że zwiększenie lub zmniejszenie rozmiarów maszyn wirtualnych (VM) w odpowiedzi na obciążenie. Porównaj tego zachowania [skalowanie w poziomie](virtual-machine-scale-sets-autoscale-overview.md), nazywany również *skalowanie w poziomie* i *skalowanie w pionie*, gdy liczba maszyn wirtualnych ulega zmianie w zależności od obciążenia.

Reprovisioning oznacza usunięcie istniejącej maszyny Wirtualnej, a następnie zamienić go na nowy. Kiedy zwiększyć lub zmniejszyć rozmiar maszyn wirtualnych w skali maszyny wirtualnej ustawić w niektórych przypadkach potrzebne do zmiany rozmiaru istniejących maszyn wirtualnych i przechowywać dane, a w innych przypadkach należy wdrożyć nowe maszyny wirtualne z nowego rozmiaru. W tym dokumencie opisano w obu przypadkach.

Skalowanie w pionie może być przydatne podczas:

* Usługi oparte na maszynach wirtualnych jest niewykorzystywanych (na przykład w weekendy). Zmniejszenie rozmiaru maszyny Wirtualnej można zmniejszyć koszty miesięczne.
* Zwiększanie rozmiaru maszyny Wirtualnej, aby poradzić sobie z większych żądanie bez konieczności tworzenia dodatkowych maszyn wirtualnych.

Możesz skonfigurować skalowanie w pionie być wyzwalane na podstawie alertów na podstawie metryk z zestawu skalowania maszyn wirtualnych. Po aktywowaniu alertu go wyzwala element webhook tego wyzwalaczy, ustawionych przez element runbook, który można skalować skalowania w górę lub w dół. Skalowanie w pionie można skonfigurować, wykonując następujące czynności:

1. Utwórz konto usługi Azure Automation za pomocą funkcji Uruchom jako.
2. Importowanie elementów runbook usługi Azure Automation pionowy skalowania zestawów skalowania maszyn wirtualnych w ramach subskrypcji.
3. Dodawanie elementu webhook do elementu runbook.
4. Dodawanie alertu do Twojej maszyny wirtualnej zestawu skalowania przy użyciu powiadomień elementu webhook.

> [!NOTE]
> Automatyczne skalowanie w pionie może występować tylko w określonym zakresie rozmiarów maszyn wirtualnych. Porównanie specyfikacji każdego rozmiaru przed podjęciem decyzji o Skaluj od jednej do innego (większa liczba zostanie podana nie zawsze wskazuje większy rozmiar maszyny Wirtualnej). Możesz skalować między następujące pary rozmiarów:
> 
> | Rozmiary maszyn wirtualnych, skalowanie parę |  |
> | --- | --- |
> | Standardowa_A0 |Standardowa_A11 |
> | Standardowa_D1 |Standardowa_D14 |
> | Standardowa_DS1 |Standardowa_DS14 |
> | Standard_D1v2 |Standard_D15v2 |
> | Standardowa_G1 |Maszyna wirtualna Standard_G5 |
> | Standardowa_GS1 |Standard_GS5 |
> 
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Tworzenie konta usługi Azure Automation za pomocą funkcji Uruchom jako
Pierwszą rzeczą, jaką należy wykonać jest utworzyć konto usługi Azure Automation, który jest hostem elementów runbook umożliwia skalowanie wystąpień zestawu skalowania maszyn wirtualnych. Ostatnio [usługi Azure Automation](https://azure.microsoft.com/services/automation/) wprowadza funkcję "Uruchom jako konto", który sprawia, że ustawienia zapasowej jednostki usługi do automatycznego uruchamiania elementów runbook w imieniu użytkownika. Aby uzyskać więcej informacji, zobacz:

* [Uwierzytelnianie elementów Runbook przy użyciu konta Uruchom jako platformy Azure](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importowanie elementów runbook usługi Azure Automation pionowy skalowania w ramach subskrypcji
Elementy runbook konieczne skalowanie w pionie Twoje zestawy skalowania maszyn wirtualnych są już opublikowany w galerii elementów Runbook automatyzacji Azure. Aby zaimportować je do subskrypcji postępuj zgodnie z instrukcjami w tym artykule:

* [Galeria elementów Runbook i modułów usługi Azure Automation](../automation/automation-runbook-gallery.md)

Wybierz opcję Przeglądaj galerię menu elementów Runbook:

![Elementy Runbook do zaimportowania][runbooks]

Elementy runbook, które muszą zostać zaimportowane, są wyświetlane. Wybierz element runbook, oparte na tego, czy chcesz, aby pionowy, Skalowanie z użyciem lub bez reprovisioning:

![Galeria elementów Runbook][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Dodawanie elementu webhook do elementu runbook
Po zaimportowaniu elementów runbook, Dodaj element webhook do elementu runbook, dzięki czemu mogą być wyzwalane przez alert z zestawu skalowania maszyn wirtualnych. W tym artykule opisano szczegóły tworzenia elementu webhook dla elementu Runbook:

* [Usługa Azure Automation elementów webhook](../automation/automation-webhooks.md)

> [!NOTE]
> Upewnij się, że Skopiuj identyfikator URI elementu webhook przed zamknięciem okna dialogowego elementu webhook, ponieważ będzie potrzebny, ten adres w następnej sekcji.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Dodawanie alertu do zestawu skalowania maszyn wirtualnych
Poniżej jest skrypt programu PowerShell, który pokazuje, jak dodać alert do skalowania maszyn wirtualnych z zestawu. Zapoznaj się z następującym artykułem, aby uzyskać nazwę metryki wyzwolenie alertu na: [typowe metryki automatycznego skalowania usługi Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md).

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzureRmMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> Zalecane jest, aby skonfigurować okno uzasadnionym czasie alertu, aby uniknąć wyzwalania skalowania w pionie i wszystkie skojarzone zakłóceń zbyt często. Należy wziąć pod uwagę okna co najmniej 20 – 30 minut lub dłużej. Należy wziąć pod uwagę poziomej, skalowanie, aby uniknąć przerw w działaniu.
> 
> 

Aby uzyskać więcej informacji na temat tworzenia alertów zobacz następujące artykuły:

* [Przykłady szybkiego startu programu Azure PowerShell monitora](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [Przykłady szybkiego startu usługi Azure Monitor międzyplatformowego interfejsu wiersza polecenia](../monitoring-and-diagnostics/insights-cli-samples.md)

## <a name="summary"></a>Podsumowanie
W tym artykule przedstawiono proste przykłady skalowanie pionowe. Przy użyciu tych bloków konstrukcyjnych — konto usługi Automation, elementami runbook, elementy webhook, alerty — możesz łączyć z wielu różnych zdarzeń za pomocą dostosowanego zestawu działań.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
