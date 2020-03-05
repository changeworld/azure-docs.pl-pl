---
title: Automatyczne naprawianie wystąpień przy użyciu zestawów skalowania maszyn wirtualnych platformy Azure
description: Dowiedz się, jak skonfigurować zasady naprawy automatycznych dla wystąpień maszyn wirtualnych w zestawie skalowania
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274816"
---
# <a name="preview-automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Wersja zapoznawcza: automatyczne naprawy wystąpień dla zestawów skalowania maszyn wirtualnych platformy Azure

Włączenie automatycznego naprawiania wystąpień dla zestawów skalowania maszyn wirtualnych platformy Azure ułatwia uzyskanie wysokiej dostępności dla aplikacji przez utrzymywanie zestawu prawidłowych wystąpień. Jeśli wystąpienie w zestawie skalowania zostanie uznane za niezdrowe zgodnie z informacjami podanymi przez [rozszerzenie kondycji aplikacji](./virtual-machine-scale-sets-health-extension.md) lub [sondy kondycji modułu równoważenia obciążenia](../load-balancer/load-balancer-custom-probe-overview.md), ta funkcja automatycznie wykonuje naprawy wystąpienia, usuwając wystąpienie złej kondycji i tworząc nowe, aby je zastąpić.

> [!NOTE]
> Ta funkcja w wersji zapoznawczej jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Wymagania dotyczące korzystania z automatycznych napraw wystąpień

**Zrezygnuj z wersji zapoznawczej automatycznego naprawiania wystąpienia**

Użyj interfejsu API REST lub Azure PowerShell, aby zrezygnować z wersji zapoznawczej automatycznego wystąpienia. Te kroki spowodują zarejestrowanie subskrypcji dla funkcji w wersji zapoznawczej. Należy pamiętać, że jest to tylko jednorazowa konfiguracja wymagana do korzystania z tej funkcji. Jeśli subskrypcja jest już zarejestrowana na potrzeby automatycznych napraw wystąpień, nie musisz rejestrować się ponownie. 

Korzystanie z interfejsu API REST 

1. Zarejestruj się, aby uzyskać dostęp do funkcji przy użyciu [funkcji-Register](/rest/api/resources/features/register) 

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

2. Poczekaj kilka minut, aż *stan* zmieni się na *zarejestrowano*. Aby to potwierdzić, możesz użyć poniższego interfejsu API.

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

3. Po zmianie *stanu* na *zarejestrowano*Uruchom następujące polecenie.

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2015-12-01'
```

Korzystanie z programu Azure PowerShell

1. Zarejestruj się, aby uzyskać dostęp do funkcji za pomocą polecenia cmdlet [register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) , a następnie [register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature)

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute

Register-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
```

2. Zaczekaj kilka minut, aż *RegistrationState* zmieni się na *zarejestrowane*. Aby to potwierdzić, można użyć następującego polecenia cmdlet.

```azurepowershell-interactive
Get-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
 ```

 Odpowiedź powinna być następująca.

| FeatureName                           | ProviderName            | RegistrationState       |
|---------------------------------------|-------------------------|-------------------------|
| RepairVMScaleSetInstancesPreview      | Microsoft.Compute       | Zarejestrowany              |

3. Gdy *RegistrationState* się zmienić na *zarejestrowane*, uruchom następujące polecenie cmdlet.

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute
```

**Włączanie monitorowania kondycji aplikacji w zestawie skalowania**

Dla zestawu skalowania powinna być włączona funkcja monitorowania kondycji aplikacji dla wystąpień. Można to zrobić przy użyciu funkcji [rozszerzenia kondycji aplikacji](./virtual-machine-scale-sets-health-extension.md) lub [sond kondycji modułu równoważenia obciążenia](../load-balancer/load-balancer-custom-probe-overview.md). Można jednocześnie włączyć tylko jedną z nich. Aby określić stan kondycji aplikacji, należy użyć rozszerzenia kondycja aplikacji lub sond modułu równoważenia obciążenia. Ten stan kondycji jest używany przez koordynatora zestawu skalowania do monitorowania kondycji wystąpienia i wykonywania napraw, gdy jest to wymagane.

**Skonfiguruj punkt końcowy, aby zapewnić stan kondycji**

Przed włączeniem zasad automatycznego naprawiania wystąpienia, upewnij się, że wystąpienia zestawu skalowania mają skonfigurowany punkt końcowy aplikacji do emisji stanu kondycji aplikacji. Gdy wystąpienie zwraca stan 200 (OK) w tym punkcie końcowym aplikacji, wystąpienie zostanie oznaczone jako "w dobrej kondycji". We wszystkich innych przypadkach wystąpienie jest oznaczone jako "w złej kondycji", w tym następujące scenariusze:

- Jeśli nie ma skonfigurowanego punktu końcowego aplikacji w wystąpieniach maszyny wirtualnej w celu zapewnienia stanu kondycji aplikacji
- Gdy punkt końcowy aplikacji jest niepoprawnie skonfigurowany
- Gdy punkt końcowy aplikacji jest nieosiągalny

W przypadku wystąpień oznaczonych jako "w złej kondycji" Automatyczne naprawy są wyzwalane przez zestaw skalowania. Upewnij się, że punkt końcowy aplikacji został prawidłowo skonfigurowany przed włączeniem zasad automatycznego naprawiania w celu uniknięcia nieplanowanych napraw wystąpień, gdy punkt końcowy jest skonfigurowany.

**Włącz pojedynczą grupę umieszczania**

Ta wersja zapoznawcza jest obecnie dostępna tylko w przypadku zestawów skalowania wdrożonych jako pojedyncza Grupa umieszczania. Właściwość *singlePlacementGroup* powinna mieć ustawioną *wartość true* , aby zestaw skalowania używał funkcji automatycznego naprawiania wystąpień. Dowiedz się więcej na temat [grup umieszczania](./virtual-machine-scale-sets-placement-groups.md#placement-groups).

**Wersja interfejsu API**

Zasady automatycznego naprawiania są obsługiwane w przypadku interfejsu API obliczeń w wersji 2018-10-01 lub nowszej.

**Ograniczenia dotyczące przenoszenia zasobów lub subskrypcji**

W ramach tej wersji zapoznawczej przenoszenie zasobów lub subskrypcji nie jest obecnie obsługiwane w przypadku zestawów skalowania, gdy zasady naprawy automatyczne są włączone.

**Ograniczenie dla zestawów skalowania usługi Service Fabric**

Ta funkcja w wersji zapoznawczej nie jest obecnie obsługiwana w przypadku zestawów skalowania usługi Service Fabric.

## <a name="how-do-automatic-instance-repairs-work"></a>Jak działają automatyczne naprawy wystąpień?

Funkcja automatycznej naprawy wystąpienia polega na monitorowaniu kondycji poszczególnych wystąpień w zestawie skalowania. Wystąpienia maszyn wirtualnych w zestawie skalowania można skonfigurować tak, aby emitować stan kondycji aplikacji przy użyciu [rozszerzenia kondycji aplikacji](./virtual-machine-scale-sets-health-extension.md) lub [sond kondycji modułu równoważenia obciążenia](../load-balancer/load-balancer-custom-probe-overview.md). Jeśli wystąpienie jest w złej kondycji, zestaw skalowania wykonuje akcję naprawy, usuwając wystąpienie złej kondycji i tworząc nowe, aby je zastąpić. Tę funkcję można włączyć w modelu zestawu skalowania maszyn wirtualnych przy użyciu obiektu *automaticRepairsPolicy* .

### <a name="batching"></a>Tworzenie partii

Operacje naprawy wystąpienia automatycznego są wykonywane w partiach. W danym momencie nie więcej niż 5% wystąpień w zestawie skalowania jest naprawione za pomocą zasad automatycznej naprawy. Pozwala to uniknąć jednoczesnego usunięcia i ponownego utworzenia dużej liczby wystąpień, jeśli znaleziono w złej kondycji.

### <a name="grace-period"></a>Okres prolongaty

Gdy wystąpienie przechodzi przez operację zmiany stanu z powodu akcji PUT, PATCH lub POST wykonanej na zestawie skalowania (na przykład reobraz, ponownym wdrożeniu, aktualizacji itp.), wówczas każda akcja naprawy na tym wystąpieniu jest wykonywana dopiero po upływie okresu prolongaty. Okres prolongaty to ilość czasu, przez jaką wystąpienie może powrócić do stanu prawidłowego. Okres prolongaty rozpoczyna się po zakończeniu zmiany stanu. Pozwala to uniknąć wszelkich przedwczesnych lub przypadkowych operacji naprawy. Okres prolongaty jest uznawany za wszystkie nowo utworzone wystąpienia w zestawie skalowania (łącznie z tym utworzonym w wyniku operacji naprawy). Okres prolongaty jest określany w minutach w formacie ISO 8601 i można go ustawić przy użyciu właściwości *automaticRepairsPolicy. gracePeriod*. Okres prolongaty może wynosić od 30 minut do 90 minut i ma wartość domyślną 30 minut.

Proces automatycznego naprawiania wystąpienia przebiega w następujący sposób:

1. Funkcja [rozszerzenia kondycji aplikacji](./virtual-machine-scale-sets-health-extension.md) lub [sondy kondycji modułu równoważenia obciążenia wysyła](../load-balancer/load-balancer-custom-probe-overview.md) polecenie ping do punktu końcowego aplikacji wewnątrz każdej maszyny wirtualnej w zestawie skalowania, aby uzyskać stan kondycji aplikacji dla każdego wystąpienia.
2. Jeśli punkt końcowy odpowie na status 200 (OK), wystąpienie zostanie oznaczone jako "w dobrej kondycji". We wszystkich innych przypadkach (łącznie z tym, czy punkt końcowy jest nieosiągalny) wystąpienie jest oznaczone jako "w złej kondycji".
3. Jeśli wystąpienie jest w złej kondycji, zestaw skalowania wyzwala akcję naprawy, usuwając wystąpienie złej kondycji i tworząc nowe, aby je zastąpić.
4. Naprawy wystąpień są wykonywane w partiach. W danym momencie nie można naprawić więcej niż 5% łącznej liczby wystąpień w zestawie skalowania. Jeśli zestaw skalowania ma mniej niż 20 wystąpień, naprawy są wykonywane dla jednego wystąpienia w złej kondycji.
5. Powyższy proces jest kontynuowany do momentu naprawienia wszystkich wystąpień w zestawie skalowania w złej kondycji.

## <a name="instance-protection-and-automatic-repairs"></a>Ochrona wystąpień i automatyczne naprawy

Jeśli wystąpienie w zestawie skalowania jest chronione przez zastosowanie *[zasad ochrony z zestawu skalowania w poziomie](./virtual-machine-scale-sets-instance-protection.md#protect-from-scale-set-actions)* , a następnie automatyczne naprawy nie są wykonywane w tym wystąpieniu.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Włączanie zasad automatycznego naprawiania podczas tworzenia nowego zestawu skalowania

Aby włączyć automatyczne naprawy zasad podczas tworzenia nowego zestawu skalowania, należy upewnić się, że spełnione są wszystkie [wymagania](#requirements-for-using-automatic-instance-repairs) dotyczące tego funkcji. Punkt końcowy aplikacji powinien być poprawnie skonfigurowany dla wystąpień zestawu skalowania, aby uniknąć wyzwalania niezamierzonych napraw podczas konfigurowania punktu końcowego. W przypadku nowo utworzonych zestawów skalowania wszystkie naprawy wystąpienia są wykonywane dopiero po upływie czasu oczekiwania przez okres prolongaty. Aby włączyć automatyczne naprawianie wystąpienia w zestawie skalowania, użyj obiektu *automaticRepairsPolicy* w modelu zestawu skalowania maszyn wirtualnych.

### <a name="rest-api"></a>Interfejs API REST

Poniższy przykład pokazuje, jak włączyć automatyczne naprawianie wystąpień w modelu zestawu skalowania. Użyj interfejsu API w wersji 2018-10-01 lub nowszej.

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

Funkcję automatycznej naprawy wystąpienia można włączyć podczas tworzenia nowego zestawu skalowania za pomocą polecenia cmdlet [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) . Ten przykładowy skrypt przeprowadzi Cię przez proces tworzenia zestawu skalowania i skojarzonych zasobów przy użyciu pliku konfiguracji: [Utwórz kompletny zestaw skalowania maszyn wirtualnych](./scripts/powershell-sample-create-complete-scale-set.md). Można skonfigurować zasady automatycznego naprawiania wystąpienia, dodając parametry *EnableAutomaticRepair* i *AutomaticRepairGracePeriod* do obiektu konfiguracji w celu utworzenia zestawu skalowania. Poniższy przykład włącza funkcję z okresem prolongaty wynoszącym 30 minut.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Włączanie zasad automatycznego naprawiania podczas aktualizowania istniejącego zestawu skalowania

Przed włączeniem zasad automatycznego naprawiania w istniejącym zestawie skalowania upewnij się, że spełniono wszystkie [wymagania](#requirements-for-using-automatic-instance-repairs) , które należy wykonać, aby uzyskać dostęp do tej funkcji. Punkt końcowy aplikacji powinien być poprawnie skonfigurowany dla wystąpień zestawu skalowania, aby uniknąć wyzwalania niezamierzonych napraw podczas konfigurowania punktu końcowego. Aby włączyć automatyczne naprawianie wystąpienia w zestawie skalowania, użyj obiektu *automaticRepairsPolicy* w modelu zestawu skalowania maszyn wirtualnych.

### <a name="rest-api"></a>Interfejs API REST

Poniższy przykład włącza zasady z okresem prolongaty wynoszącym 40 minut. Użyj interfejsu API w wersji 2018-10-01 lub nowszej.

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

Użyj polecenia cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) , aby zmodyfikować konfigurację funkcji automatycznego naprawiania wystąpienia w istniejącym zestawie skalowania. Poniższy przykład aktualizuje okres prolongaty do 40 minut.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

## <a name="troubleshoot"></a>Rozwiązywanie problemów

**Nie można włączyć zasad automatycznego naprawiania**

Jeśli zostanie wyświetlony błąd "nieprawidłowego żądania" z komunikatem "nie można odnaleźć elementu członkowskiego" automaticRepairsPolicy "w obiekcie typu" Properties "", sprawdź wersję interfejsu API używaną dla zestawu skalowania maszyn wirtualnych. Dla tej funkcji wymagany jest interfejs API w wersji 2018-10-01 lub nowszej.

**Wystąpienie nie jest naprawione nawet po włączeniu zasad**

Wystąpienie może być w okresie prolongaty. Jest to czas oczekiwania po zmianie stanu wystąpienia przed wykonaniem napraw. Ma to na celu uniknięcie jakichkolwiek przedwczesnych lub przypadkowych napraw. Akcja naprawy powinna wystąpić po zakończeniu okresu prolongaty dla tego wystąpienia.

**Wyświetlanie stanu kondycji aplikacji dla wystąpień zestawu skalowania**

Aby wyświetlić stan kondycji aplikacji, można użyć [interfejsu API widoku wystąpienia](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) dla wystąpień w zestawie skalowania maszyn wirtualnych. Za pomocą Azure PowerShell można użyć polecenia cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) z flagą *-InstanceView* . Stan kondycji aplikacji jest podany pod właściwością *vmHealth*.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak skonfigurować [rozszerzenia kondycji aplikacji](./virtual-machine-scale-sets-health-extension.md) lub [sondy kondycji modułu równoważenia obciążenia](../load-balancer/load-balancer-custom-probe-overview.md) dla zestawów skalowania.
