---
title: Automatyczne naprawy wystąpień za pomocą zestawów skalowania maszyny wirtualnej platformy Azure
description: Dowiedz się, jak skonfigurować zasady automatycznego napraw dla wystąpień maszyn wirtualnych w zestawie skalowania
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: avverma
ms.openlocfilehash: f335b0fb3396103c321d740bcf6d125e60e95086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274816"
---
# <a name="preview-automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Wersja zapoznawcza: automatyczne naprawy wystąpień dla zestawów skalowania maszyny wirtualnej platformy Azure

Włączenie automatycznych napraw wystąpień dla zestawów skalowania maszyny wirtualnej platformy Azure pomaga osiągnąć wysoką dostępność dla aplikacji, utrzymując zestaw zdrowych wystąpień. Jeśli wystąpienie w zestawie skalowania zostanie uznane za złej kondycji, jak zgłaszane przez [rozszerzenie kondycji aplikacji](./virtual-machine-scale-sets-health-extension.md) lub [sondy kondycji modułu równoważenia obciążenia,](../load-balancer/load-balancer-custom-probe-overview.md)ta funkcja automatycznie wykonuje naprawę wystąpienia, usuwając wystąpienie w złej kondycji i tworząc nowe, aby go zastąpić.

> [!NOTE]
> Ta funkcja w wersji zapoznawczej jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Wymagania dotyczące stosowania automatycznych napraw wystąpień

**Zapisz się do automatycznego podglądu napraw instancji**

Użyj interfejsu API REST lub programu Azure PowerShell, aby zdecydować się na automatyczną wersję zapoznawczą naprawy wystąpień. Te kroki zarejestrują subskrypcję dla funkcji w wersji zapoznawczej. Należy pamiętać, że jest to tylko jednorazowa konfiguracja wymagana do korzystania z tej funkcji. Jeśli subskrypcja jest już zarejestrowana w wersji zapoznawczej automatycznych napraw instancji, nie musisz się ponownie rejestrować. 

Korzystanie z interfejsu API REST 

1. Zarejestruj się w funkcji za pomocą [funkcji — zarejestruj się](/rest/api/resources/features/register) 

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview/register?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registering"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

2. Poczekaj kilka minut, aż *państwo* zmieni się na *Zarejestrowany*. Aby to potwierdzić, można użyć następującego interfejsu API.

```
GET on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registered"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

3. Po zmianie *stanu* na *Zarejestrowany*uruchom następujące czynności.

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2015-12-01'
```

Korzystanie z programu Azure PowerShell

1. Zarejestruj się dla tej funkcji przy użyciu [polecenia rejestru poleceń-AzureRmResourceProvider,](/powershell/module/azurerm.resources/register-azurermresourceprovider) po którym następuje [funkcja Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature)

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute

Register-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
```

2. Poczekaj kilka minut, aż *stan rejestracji* zmieni się na *Zarejestrowany*. Aby to potwierdzić, można użyć następującego polecenia cmdlet.

```azurepowershell-interactive
Get-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
 ```

 Odpowiedź powinna być następująca.

| Nazwa funkcji                           | ProviderName            | Stan rejestracji       |
|---------------------------------------|-------------------------|-------------------------|
| Naprawa VMScaleSetInstancesPreview      | Microsoft.Compute       | Zarejestrowany              |

3. Po *registrationState,* aby zmienić na *Zarejestrowany*, a następnie uruchomić następujące polecenie cmdlet.

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute
```

**Włącz monitorowanie kondycji aplikacji dla zestawu skalowania**

Zestaw skalowania powinien mieć włączone monitorowanie kondycji aplikacji dla wystąpień. Można to zrobić za pomocą [rozszerzenia kondycji aplikacji](./virtual-machine-scale-sets-health-extension.md) lub sond kondycji [modułu równoważenia obciążenia.](../load-balancer/load-balancer-custom-probe-overview.md) Tylko jeden z nich można włączyć w czasie. Rozszerzenie kondycji aplikacji lub moduł równoważenia obciążenia sonduje ping punktu końcowego aplikacji skonfigurowany w wystąpieniach maszyny wirtualnej w celu określenia stanu kondycji aplikacji. Ten stan kondycji jest używany przez orchestrator zestaw skalowania do monitorowania kondycji wystąpienia i wykonywania napraw, gdy jest to wymagane.

**Konfigurowanie punktu końcowego w celu zapewnienia stanu kondycji**

Przed włączeniem zasad automatycznego naprawy wystąpienia upewnij się, że wystąpienia zestawu skalowania mają punkt końcowy aplikacji skonfigurowany do emitowania stanu kondycji aplikacji. Gdy wystąpienie zwraca stan 200 (OK) w tym punkcie końcowym aplikacji, wystąpienie jest oznaczone jako "W dobrej kondycji". We wszystkich innych przypadkach wystąpienie jest oznaczone jako "w złej kondycji", w tym następujące scenariusze:

- Jeśli wewnątrz wystąpień maszyny wirtualnej nie skonfigurowano punktu końcowego aplikacji w celu zapewnienia stanu kondycji aplikacji
- Gdy punkt końcowy aplikacji jest niepoprawnie skonfigurowany
- Gdy punkt końcowy aplikacji jest niezdysklidalny

W przypadku wystąpień oznaczonych jako "W złej kondycji" automatyczne naprawy są wyzwalane przez zestaw skalowania. Upewnij się, że punkt końcowy aplikacji jest poprawnie skonfigurowany przed włączeniem zasad automatycznych napraw, aby uniknąć niezamierzonych napraw wystąpień, gdy punkt końcowy jest coraz skonfigurowany.

**Włączanie pojedynczej grupy umieszczania**

Ta wersja zapoznawcza jest obecnie dostępna tylko dla zestawów skalowania wdrożonych jako pojedyncza grupa miejsc docelowych. Właściwość *singlePlacementGroup* powinna być ustawiona na *true* dla zestawu skalowania, aby używać funkcji automatycznego naprawy wystąpienia. Dowiedz się więcej o [grupach miejsc docelowych](./virtual-machine-scale-sets-placement-groups.md#placement-groups).

**Wersja API**

Zasady automatycznych napraw są obsługiwane dla interfejsu API obliczeń w wersji 2018-10-01 lub nowszej.

**Ograniczenia dotyczące przenoszenia zasobów lub subskrypcji**

W ramach tej wersji zapoznawczej przenoszenie zasobów lub subskrypcji nie są obecnie obsługiwane w przypadku zestawów skalowania, gdy zasada automatycznej naprawy jest włączona.

**Ograniczenie dla zestawów wag sieci szkieletowych usług**

Ta funkcja podglądu nie jest obecnie obsługiwana w przypadku zestawów skalowania sieci szkieletowej usług.

## <a name="how-do-automatic-instance-repairs-work"></a>Jak działają automatyczne naprawy instancji?

Funkcja automatycznej naprawy wystąpienia opiera się na monitorowaniu kondycji poszczególnych wystąpień w zestawie skalowania. Wystąpienia maszyn wirtualnych w zestawie skalowania można skonfigurować do emitowania stanu kondycji aplikacji przy użyciu [rozszerzenia kondycji aplikacji](./virtual-machine-scale-sets-health-extension.md) lub sond kondycji [modułu równoważenia obciążenia.](../load-balancer/load-balancer-custom-probe-overview.md) Jeśli wystąpienie zostanie uznane za w złej kondycji, zestaw skalowania wykonuje akcję naprawy, usuwając w złej kondycji wystąpienie i tworząc nowe, aby go zastąpić. Ta funkcja może być włączona w modelu zestawu skalowania maszyny wirtualnej przy użyciu *obiektu automaticRepairsPolicy.*

### <a name="batching"></a>Tworzenie partii

Operacje automatycznej naprawy wystąpienia są wykonywane w partiach. W danym momencie nie więcej niż 5% wystąpień w zestawie skalowania są naprawiane za pomocą zasad automatycznych napraw. Pomaga to uniknąć jednoczesnego usuwania i ponownego tworzenia dużej liczby wystąpień, jeśli zostanie znaleziony w złej kondycji w tym samym czasie.

### <a name="grace-period"></a>Okres prolongaty

Gdy wystąpienie przechodzi przez operację zmiany stanu z powodu put, patch lub post akcji wykonywanej na zestaw skalowania (na przykład reimage, ponowne wdrożenie, aktualizacja, itp.), a następnie wszelkie działania naprawcze w tym wystąpieniu jest wykonywana tylko po oczekiwaniu na okres prolongaty. Okres prolongaty to czas, aby umożliwić wystąpieniu powrót do stanu dobrej kondycji. Okres prolongaty rozpoczyna się po zakończeniu zmiany stanu. Pomaga to uniknąć przedwczesnych lub przypadkowych operacji naprawy. Okres prolongaty jest honorowany dla każdego nowo utworzonego wystąpienia w zestawie skalowania (w tym utworzonego w wyniku operacji naprawy). Okres prolongaty jest określony w minutach w formacie ISO 8601 i można go ustawić za pomocą właściwości *automaticRepairsPolicy.gracePeriod*. Okres prolongaty może wynosić od 30 minut do 90 minut i ma wartość domyślną 30 minut.

Proces automatycznej naprawy wystąpienia działa w następujący sposób:

1. [Rozszerzenie kondycji aplikacji](./virtual-machine-scale-sets-health-extension.md) lub [sondy kondycji modułu równoważenia obciążenia](../load-balancer/load-balancer-custom-probe-overview.md) ping punktu końcowego aplikacji wewnątrz każdej maszyny wirtualnej w zestawie skalowania, aby uzyskać stan kondycji aplikacji dla każdego wystąpienia.
2. Jeśli punkt końcowy odpowiada ze stanem 200 (OK), wystąpienie jest oznaczone jako "W dobrej kondycji". We wszystkich innych przypadkach (w tym jeśli punkt końcowy jest nieosiągalny), wystąpienie jest oznaczone jako "W złej kondycji".
3. Gdy wystąpienie okaże się w złej kondycji, zestaw skalowania wyzwala akcję naprawy, usuwając w złej kondycji wystąpienie i tworząc nowy, aby go zastąpić.
4. Naprawy wystąpień są wykonywane w partiach. W danym momencie nie więcej niż 5% całkowitych wystąpień w zestawie skalowania są naprawiane. Jeśli zestaw skalowania ma mniej niż 20 wystąpień, naprawy są wykonywane dla jednego wystąpienia w złej kondycji naraz.
5. Powyższy proces jest kontynuowany do momentu naprawienia wszystkich niezdrowych wystąpień w zestawie skalowania.

## <a name="instance-protection-and-automatic-repairs"></a>Ochrona przed wystąpieniami i automatyczne naprawy

Jeśli wystąpienie w zestawie skalowania jest chronione przez zastosowanie *[zasad ochrony akcji protect from scale-set,](./virtual-machine-scale-sets-instance-protection.md#protect-from-scale-set-actions)* w tym przypadku nie są wykonywane automatyczne naprawy.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Włączanie zasad automatycznych napraw podczas tworzenia nowego zestawu skalowania

Aby włączyć zasady automatycznego napraw podczas tworzenia nowego zestawu skalowania, upewnij się, że wszystkie [wymagania dotyczące](#requirements-for-using-automatic-instance-repairs) włączenia tej funkcji są spełnione. Punkt końcowy aplikacji powinien być poprawnie skonfigurowany dla wystąpień zestawu skalowania, aby uniknąć wyzwalania niezamierzonych napraw podczas konfigurowania punktu końcowego. W przypadku nowo utworzonych zestawów skalowania wszelkie naprawy wystąpień są wykonywane tylko po odczekaniu okresu prolongaty. Aby włączyć automatyczną naprawę wystąpienia w zestawie skalowania, użyj *automatycznegoRepairsPolicy* obiektu w modelu zestawu skalowania maszyny wirtualnej.

### <a name="rest-api"></a>Interfejs API REST

W poniższym przykładzie pokazano, jak włączyć automatyczną naprawę wystąpienia w modelu zestawu skalowania. Użyj wersji interfejsu API 2018-10-01 lub nowszej.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Funkcję automatycznej naprawy wystąpienia można włączyć podczas tworzenia nowego zestawu skalowania przy użyciu polecenia cmdlet [New-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig) Ten przykładowy skrypt przechodzi przez tworzenie zestawu skalowania i skojarzonych zasobów przy użyciu pliku konfiguracji: [Utwórz kompletny zestaw skalowania maszyny wirtualnej](./scripts/powershell-sample-create-complete-scale-set.md). Zasady automatycznego naprawy wystąpienia można skonfigurować, dodając parametry *EnableAutomaticRepair* i *AutomaticRepairGracePeriod* do obiektu konfiguracji do tworzenia zestawu skalowania. Poniższy przykład włącza operację z okresem prolongaty 30 minut.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Włączanie zasad automatycznych napraw podczas aktualizowania istniejącego zestawu skalowania

Przed włączeniem zasad automatycznego naprawy w istniejącym zestawie skalowania upewnij się, że wszystkie [wymagania dotyczące](#requirements-for-using-automatic-instance-repairs) włączenia tej funkcji są spełnione. Punkt końcowy aplikacji powinien być poprawnie skonfigurowany dla wystąpień zestawu skalowania, aby uniknąć wyzwalania niezamierzonych napraw podczas konfigurowania punktu końcowego. Aby włączyć automatyczną naprawę wystąpienia w zestawie skalowania, użyj *automatycznegoRepairsPolicy* obiektu w modelu zestawu skalowania maszyny wirtualnej.

### <a name="rest-api"></a>Interfejs API REST

Poniższy przykład umożliwia zasady z okresem prolongaty 40 minut. Użyj wersji interfejsu API 2018-10-01 lub nowszej.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Polecenie cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) służy do modyfikowania konfiguracji funkcji automatycznego naprawy wystąpienia w istniejącym zestawie skalowania. W poniższym przykładzie aktualizuje okres prolongaty do 40 minut.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

## <a name="troubleshoot"></a>Rozwiązywanie problemów

**Niepodtrzymanie zasad automatycznego napraw**

Jeśli pojawi się błąd "BadRequest" z komunikatem "Nie można znaleźć elementu członkowskiego 'automaticRepairsPolicy' na obiekcie typu 'właściwości'", sprawdź wersję interfejsu API używaną do zestawu skalowania maszyny wirtualnej. Dla tej funkcji wymagana jest wersja interfejsu API 2018-10-01 lub nowsza.

**Wystąpienie nie jest naprawione nawet wtedy, gdy zasada jest włączona**

Wystąpienie może być w okresie prolongaty. Jest to czas oczekiwania po każdej zmianie stanu w wystąpieniu przed wykonaniem napraw. Ma to na celu uniknięcie przedwczesnych lub przypadkowych napraw. Akcja naprawy powinna nastąpić po zakończeniu okresu prolongaty dla wystąpienia.

**Wyświetlanie stanu kondycji aplikacji dla wystąpień zestawu skalowania**

Można użyć [get instance view api](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) dla wystąpień w zestawie skali maszyny wirtualnej, aby wyświetlić stan kondycji aplikacji. Za pomocą programu Azure PowerShell można użyć polecenia cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) z flagą *-InstanceView.* Stan kondycji aplikacji jest podany w ramach właściwości *vmHealth*.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak skonfigurować [rozszerzenie kondycji aplikacji](./virtual-machine-scale-sets-health-extension.md) lub [sondy kondycji modułu równoważenia obciążenia](../load-balancer/load-balancer-custom-probe-overview.md) dla zestawów skalowania.
