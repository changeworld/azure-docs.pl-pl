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
ms.date: 04/18/2019
ms.author: manayar
ms.openlocfilehash: 3846815dabdc9e351f3d8449feb88affb9c6efdb
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001205"
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
> Ze względu na rozmiar pierwszej maszyny wirtualnej, rozmiary, które mogą być skalowane, może być ograniczona z powodu dostępności o innych rozmiarach w klastrze bieżącej maszyny wirtualnej jest wdrożony w. W opublikowanych elementów runbook usługi automation używane w tym artykule zajmie się tym przypadku firma Microsoft i można skalować tylko w ramach poniżej pary rozmiar maszyny Wirtualnej. Oznacza to, że Standard_D1v2 maszyny wirtualnej będzie nie nagle skalowany w górę aby maszyna wirtualna Standard_G5 lub skalowane w dół do Basic_A0. Ponadto ograniczone maszyny wirtualnej rozmiary skalowanie w górę/w dół nie jest obsługiwana. Możesz skalować między następujące pary rozmiarów:
> 
> | Rozmiary maszyn wirtualnych, skalowanie parę |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standardowa_A0 |Standardowa_A4 |
> | Standardowa_A5 |Standardowa_A7 |
> | Standard_A8 |Standard_A9 |
> | Standardowa_A10 |Standardowa_A11 |
> | Standardowa_A1_v2 |Standardowa_A8_v2 |
> | Standardowa_A2m_v2 |Standardowa_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | Standardowa_D1 |Standardowa_D4 |
> | Standardowa_D11 |Standardowa_D14 |
> | Standardowa_DS1 |Standardowa_DS4 |
> | Standardowa_DS11 |Standardowa_DS14 |
> | Standardowa_D1_v2 |Standardowa_D5_v2 |
> | Standardowa_D11_v2 |Standardowa_D14_v2 |
> | Standardowa_DS1_v2 |Standardowa_DS5_v2 |
> | Standardowa_DS11_v2 |Standardowa_DS14_v2 |
> | Standardowa_D2_v3 |Standard_D64_v3 |
> | Standardowa_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standardowa_E2_v3 |Standardowa_E64_v3 |
> | Standardowa_E2s_v3 |Standardowa_E64s_v3 |
> | Standardowa_F1 |Standardowa_F16 |
> | Standardowa_F1s |Standardowa_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standardowa_G1 |Maszyna wirtualna Standard_G5 |
> | Standardowa_GS1 |Standard_GS5 |
> | Standardowa_H8 |Standardowa_H16 |
> | Standardowa_H8m |Standardowa_H16m |
> | Standardowa_L4s |Standardowa_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Warstwie standardowa_m8ms  |Maszyna wirtualna Standard_M128ms |
> | Warstwie standardowa_m32ls  |Warstwie standardowa_m64ls |
> | Maszyna wirtualna Standard_M64s  |Maszyna wirtualna Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standardowa_NC6 |Standardowa_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standardowa_NV6 |Standardowa_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> 
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Tworzenie konta usługi Azure Automation za pomocą funkcji Uruchom jako
Pierwszą rzeczą, jaką należy wykonać jest utworzyć konto usługi Azure Automation, który jest hostem elementów runbook umożliwia skalowanie wystąpień zestawu skalowania maszyn wirtualnych. Ostatnio [usługi Azure Automation](https://azure.microsoft.com/services/automation/) wprowadza funkcję "Uruchom jako konto", który sprawia, że ustawienia zapasowej jednostki usługi do automatycznego uruchamiania elementów runbook w imieniu użytkownika. Aby uzyskać więcej informacji, zobacz:

* [Uwierzytelnianie elementów Runbook przy użyciu konta Uruchom jako platformy Azure](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importowanie elementów runbook usługi Azure Automation pionowy skalowania w ramach subskrypcji

Elementy runbook konieczne skalowanie w pionie Twoje zestawy skalowania maszyn wirtualnych są już opublikowany w galerii elementów Runbook automatyzacji Azure. Aby zaimportować je do subskrypcji postępuj zgodnie z instrukcjami w tym artykule:

* [Galerie elementów Runbook i modułów dla usługi Azure Automation](../automation/automation-runbook-gallery.md)

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

Poniżej jest skrypt programu PowerShell, który pokazuje, jak dodać alert do skalowania maszyn wirtualnych z zestawu. Zapoznaj się z następującym artykułem, aby uzyskać nazwę metryki wyzwolenie alertu na: [Usługa Azure Monitor autoscaling często używane metryki](../azure-monitor/platform/autoscale-common-metrics.md).

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzMetricAlertRule  -Name  $alertName `
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

* [Przykłady szybkiego startu programu Azure PowerShell monitora](../azure-monitor/platform/powershell-quickstart-samples.md)
* [Przykłady szybkiego startu usługi Azure Monitor międzyplatformowego interfejsu wiersza polecenia](../azure-monitor/platform/cli-samples.md)

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono proste przykłady skalowanie pionowe. Przy użyciu tych bloków konstrukcyjnych — konto usługi Automation, elementami runbook, elementy webhook, alerty — możesz łączyć z wielu różnych zdarzeń za pomocą dostosowanego zestawu działań.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
