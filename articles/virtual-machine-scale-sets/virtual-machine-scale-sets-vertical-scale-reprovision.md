---
title: Zestawy skalowania maszyny wirtualnej platformy Azure w pionie
description: Jak skalować maszynę wirtualną w pionie w odpowiedzi na alerty monitorowania za pomocą usługi Azure Automation
author: mimckitt
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mimckitt
ms.openlocfilehash: c28c50ef78f67daa6adc4022d8feb559f5c1c1f0
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273362"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Pionowa skalowanie automatyczne z zestawami skalowania maszyny wirtualnej

W tym artykule opisano sposób skalowania w pionie [zestawów skalowania maszyny wirtualnej](https://azure.microsoft.com/services/virtual-machine-scale-sets/) platformy Azure z lub bez ponownego obsługi administracyjnej. 

Skalowanie w pionie, znane również jako *skalowanie w górę* i *w dół,* oznacza zwiększenie lub zmniejszenie rozmiarów maszyny wirtualnej w odpowiedzi na obciążenie. Porównaj to zachowanie ze [skalowaniem w poziomie,](virtual-machine-scale-sets-autoscale-overview.md)zwanym również *skalą* w poziomie i *skalowaniem w*programie , w którym liczba maszyn wirtualnych jest zmieniana w zależności od obciążenia.

Ponowne anulowanie obsługi administracyjnej oznacza usunięcie istniejącej maszyny Wirtualnej i zastąpienie jej nową. Po zwiększeniu lub zmniejszeniu rozmiaru maszyn wirtualnych w zestawie skalowania maszyny wirtualnej, w niektórych przypadkach chcesz zmienić rozmiar istniejących maszyn wirtualnych i zachować dane, podczas gdy w innych przypadkach należy wdrożyć nowe maszyny wirtualne o nowym rozmiarze. Dokument ten obejmuje obie sprawy.

Skalowanie w pionie może być przydatne, gdy:

* Usługa oparta na maszynach wirtualnych jest niedostatecznego wykorzystania (na przykład w weekendy). Zmniejszenie rozmiaru maszyny Wirtualnej może zmniejszyć miesięczne koszty.
* Zwiększenie rozmiaru maszyny Wirtualnej, aby sprostać większemu zapotrzebowaniu bez tworzenia dodatkowych maszyn wirtualnych.

Skalowanie pionowe można skonfigurować na podstawie alertów opartych na metrykach z zestawu skalowania maszyny wirtualnej. Po włączeniu alertu uruchamia element webhook, który wyzwala element runbook, który może skalować skalę skonfigurowaną w górę lub w dół. Skalowanie w pionie można skonfigurować, wykonując następujące kroki:

1. Utwórz konto usługi Azure Automation z możliwością uruchamiania.
2. Importowanie do subskrypcji zestawów skalowania pionowego usługi Azure Automation vertical scale dla zestawów skalowania maszyny wirtualnej.
3. Dodaj element webhook do elementu runbook.
4. Dodaj alert do zestawu skalowania maszyny wirtualnej przy użyciu powiadomienia elementu webhook.

> [!NOTE]
> Ze względu na rozmiar pierwszej maszyny wirtualnej rozmiary, do których można skalować, mogą być ograniczone ze względu na dostępność innych rozmiarów w klastrze bieżącej maszyny wirtualnej jest wdrażany w. W opublikowanych likemi automatyzacji używane w tym artykule zajmujemy się tym przypadkiem i skalujemy tylko w obrębie poniższych par rozmiaru maszyny Wirtualnej. Oznacza to, że Standard_D1v2 maszyna wirtualna nie zostanie nagle przeskalowana do Standard_G5 lub skalowana w dół do Basic_A0. Również ograniczone rozmiary maszyny wirtualnej skalowane w górę/w dół nie jest obsługiwany. Można wybrać skalowanie między następującymi parami rozmiarów:
> 
> | Para skalowania rozmiarów maszyn wirtualnych |  |
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
> | Standardowa_G1 |Standard_G5 |
> | Standardowa_GS1 |Standardowa_GS5 |
> | Standardowa_H8 |Standardowa_H16 |
> | Standardowa_H8m |Standardowa_H16m |
> | Standardowa_L4s |Standardowa_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standardowa_NC6 |Standardowa_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standardowa_NV6 |Standardowa_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> | Standard_NV12s_v3 |Standard_NV48s_v3 |
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Tworzenie konta automatyzacji platformy Azure z możliwością uruchamiania
Pierwszą rzeczą, którą musisz zrobić, to utworzyć konto usługi Azure Automation, które obsługuje elementy runbook używane do skalowania wystąpień zestawu skalowania maszyny wirtualnej. Ostatnio [usługa Azure Automation](https://azure.microsoft.com/services/automation/) wprowadziła funkcję "Uruchom jako konto", która umożliwia skonfigurowanie jednostki usługi do automatycznego uruchamiania elementów runbook w imieniu użytkownika. Aby uzyskać więcej informacji, zobacz:

* [Uwierzytelnianie elementów Runbook przy użyciu konta Uruchom jako platformy Azure](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importowanie do subskrypcji 400 000 000 000 000 000 000 00

Elementy runbook potrzebne do pionowego skalowania zestawów skalowania maszyny wirtualnej są już publikowane w Galerii likwidów automatyzacji usługi Azure. Aby zaimportować je do subskrypcji, wykonaj czynności opisane w tym artykule:

* [Galerie elementów Runbook i modułów dla usługi Azure Automation](../automation/automation-runbook-gallery.md)

Wybierz opcję Przeglądaj galerię z menu Elementy runbook:

![Księgi runbook do zaimportowania][runbooks]

Zostaną wyświetlone programy runbook, które muszą zostać zaimportowane. Wybierz projekt runbook na podstawie tego, czy chcesz skalować w pionie z reprowidowanie lub bez:

![Galeria eksmisja][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Dodawanie elementu webhook do elementu runbook

Po zaimportowaniu elementów runbook dodaj element webhook do elementu runbook, aby mógł zostać wyzwolony przez alert z zestawu skalowania maszyny wirtualnej. Szczegóły tworzenia elementu webhook dla elementu runbook są opisane w tym artykule:

* [Elementy webhook usługi Azure Automation](../automation/automation-webhooks.md)

> [!NOTE]
> Przed zamknięciem okna dialogowego elementu webhook należy skopiować identyfikator URI elementu webhook, ponieważ ten adres będzie potrzebny w następnej sekcji.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Dodawanie alertu do zestawu skalowania maszyny wirtualnej

Poniżej znajduje się skrypt programu PowerShell, który pokazuje, jak dodać alert do zestawu skalowania maszyny wirtualnej. Zapoznaj się z następującym artykułem, aby uzyskać nazwę metryki do wypalania alertu na: [Azure Monitor skalowania automatycznego wspólnych metryk](../azure-monitor/platform/autoscale-common-metrics.md).

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
> Zaleca się skonfigurowanie rozsądnego przedziału czasowego dla alertu, aby uniknąć wyzwalania skalowania pionowego i wszelkich związanych z nimi przerw w świadczeniu usług, zbyt często. Rozważmy okno co najmniej 20-30 minut lub więcej. Należy wziąć pod uwagę skalowanie w poziomie, jeśli trzeba uniknąć przerw.
> 
> 

Aby uzyskać więcej informacji na temat tworzenia alertów, zobacz następujące artykuły:

* [Przykłady przewodnika Szybki start programu Azure Monitor PowerShell](../azure-monitor/platform/powershell-quickstart-samples.md)
* [Przykłady szybkiego startu interfejsu wiersza szybkiego interfejsu wiersza interfejsu wiersza platformy Azure Monitor](../azure-monitor/platform/cli-samples.md)

## <a name="summary"></a>Podsumowanie

W tym artykule pokazano proste przykłady skalowania pionowego. Dzięki tym blokom konstrukcyjnym — kontu automatyzacji, elementom runbook, elementom webhook, alertom — można połączyć bogatą różnorodność zdarzeń z dostosowanym zestawem akcji.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
