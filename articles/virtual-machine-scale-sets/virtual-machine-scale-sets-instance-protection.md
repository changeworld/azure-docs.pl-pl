---
title: Ochrona wystąpień dla wystąpień zestawu skalowania maszyny wirtualnej platformy Azure
description: Dowiedz się, jak chronić wystąpienia zestawu skalowania maszyny wirtualnej platformy Azure przed operacjami skalowania i skalowania.
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 021faad28fb575c4ffeb4d895ad451d8cd82b1a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254120"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Ochrona wystąpień dla wystąpień zestawu skalowania maszyny wirtualnej platformy Azure

Zestawy skalowania maszyny wirtualnej platformy Azure umożliwiają większą elastyczność dla obciążeń za pomocą [skalowania automatycznego,](virtual-machine-scale-sets-autoscale-overview.md)dzięki czemu można skonfigurować, kiedy infrastruktura jest skalowana w poziomie i kiedy jest skalowana. Zestawy skalowania umożliwiają również centralne zarządzanie, konfigurowanie i aktualizowanie dużej liczby maszyn wirtualnych za pomocą różnych ustawień [zasad uaktualniania.](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) Można skonfigurować aktualizację w modelu zestawu skalowania, a nowa konfiguracja jest stosowana automatycznie do każdego wystąpienia zestawu skalowania, jeśli ustawiono zasady uaktualniania na Automatyczne lub Stopniowe.

Ponieważ aplikacja przetwarza ruch, mogą wystąpić sytuacje, w których określone wystąpienia mają być traktowane inaczej niż pozostałe wystąpienie zestawu skalowania. Na przykład niektóre wystąpienia w zestawie skalowania może wykonywać długotrwałe operacje i nie chcesz, aby te wystąpienia były skalowane w aż do zakończenia operacji. Można również mieć wyspecjalizowane kilka wystąpień w zestawie skalowania do wykonywania dodatkowych lub innych zadań niż inne elementy członkowskie zestawu skalowania. Te "specjalne" maszyny wirtualne nie są modyfikowane z innymi wystąpieniami w zestawie skalowania. Ochrona wystąpienia zapewnia dodatkowe formanty, aby włączyć te i inne scenariusze dla aplikacji.

W tym artykule opisano, jak można zastosować i używać różnych funkcji ochrony wystąpienia z wystąpień zestawu skalowania.

## <a name="types-of-instance-protection"></a>Rodzaje ochrony wystąpień
Zestawy skalowania zapewniają dwa typy możliwości ochrony wystąpień:

-   **Ochrona przed skalowaniem**
    - Włączono za pomocą **właściwości protectFromScaleIn** w wystąpieniu zestawu skalowania
    - Chroni wystąpienie przed skalowaniem automatycznym inicjowane skalowanie w
    - Operacje instancja inicjowane przez użytkownika (w tym usuwanie wystąpień) nie są **blokowane**
    - Operacje inicjowane na zestawie skalowania (upgrade, reimage, deallocate itp.) nie są **blokowane**

-   **Ochrona przed akcjami zestawu skalowania**
    - Włączono za pomocą **właściwości protectFromScaleSetActions** w wystąpieniu zestawu skalowania
    - Chroni wystąpienie przed skalowaniem automatycznym inicjowane skalowanie w
    - Chroni wystąpienie przed operacjami inicjowanymi w zestawie skalowania (takimi jak uaktualnianie, ponowne zakładanie, przydzielanie itp.)
    - Operacje instancja inicjowane przez użytkownika (w tym usuwanie wystąpień) nie są **blokowane**
    - Usuwanie pełnego zestawu skalowania nie jest **blokowane**

## <a name="protect-from-scale-in"></a>Ochrona przed skalowaniem
Ochrona wystąpienia mogą być stosowane do skalowania zestaw wystąpień po utworzeniu wystąpień. Ochrona jest stosowana i modyfikowana tylko w [modelu wystąpienia,](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) a nie w [modelu zestawu skalowania](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Istnieje wiele sposobów stosowania ochrony skalowania w wystąpieniach zestawu skalowania, jak opisano w poniższych przykładach.

### <a name="azure-portal"></a>Portal Azure

Ochronę skalowania można zastosować za pośrednictwem witryny Azure portal do wystąpienia w zestawie skalowania. Nie można dostosować więcej niż jedno wystąpienie naraz. Powtórz kroki dla każdego wystąpienia, które chcesz chronić.
 
1. Przejdź do istniejącego zestawu skalowania maszyny wirtualnej.
1. Wybierz **wystąpienia** z menu po lewej stronie w obszarze **Ustawienia**.
1. Wybierz nazwę wystąpienia, które chcesz chronić.
1. Wybierz kartę **Zasady ochrony.**
1. W **bloku Zasady ochrony** wybierz opcję **Chroń przed skalowaniem.**
1. Wybierz **pozycję Zapisz**. 

### <a name="rest-api"></a>Interfejs API REST

Poniższy przykład stosuje ochronę skalowania w wystąpieniu w zestawie skalowania.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true
    }
  }        
}

```

> [!NOTE]
>Ochrona wystąpień jest obsługiwana tylko w wersji INTERFEJSU API 2019-03-01 i wyższej

### <a name="azure-powershell"></a>Azure PowerShell

Użyj polecenia cmdlet [Update-AzVmssVM,](/powershell/module/az.compute/update-azvmssvm) aby zastosować ochronę skalowania do wystąpienia zestawu skalowania.

Poniższy przykład stosuje ochronę skalowania w wystąpieniu w zestawie skalowania o identyfikatorze wystąpienia 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Użyj [aktualizacji az vmss,](/cli/azure/vmss#az-vmss-update) aby zastosować ochronę skalowania w wystąpieniu zestawu skalowania.

Poniższy przykład stosuje ochronę skalowania w wystąpieniu w zestawie skalowania o identyfikatorze wystąpienia 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Ochrona przed akcjami zestawu skalowania
Ochrona wystąpienia mogą być stosowane do skalowania zestaw wystąpień po utworzeniu wystąpień. Ochrona jest stosowana i modyfikowana tylko w [modelu wystąpienia,](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) a nie w [modelu zestawu skalowania](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Ochrona wystąpienia przed akcjami zestawu skalowania chroni również wystąpienie przed skalowaniem automatycznym inicjowanym skalowaniem.

Istnieje wiele sposobów stosowania ochrony akcji zestawu skalowania w wystąpieniach zestawu skalowania, jak opisano w poniższych przykładach.

### <a name="azure-portal"></a>Portal Azure

Można zastosować ochronę od akcji zestawu skalowania za pośrednictwem witryny Azure portal do wystąpienia w zestawie skalowania. Nie można dostosować więcej niż jedno wystąpienie naraz. Powtórz kroki dla każdego wystąpienia, które chcesz chronić.
 
1. Przejdź do istniejącego zestawu skalowania maszyny wirtualnej.
1. Wybierz **wystąpienia** z menu po lewej stronie w obszarze **Ustawienia**.
1. Wybierz nazwę wystąpienia, które chcesz chronić.
1. Wybierz kartę **Zasady ochrony.**
1. W **bloku Zasady ochrony** wybierz opcję **Chroń przed zestawem skalowania.**
1. Wybierz **pozycję Zapisz**. 

### <a name="rest-api"></a>Interfejs API REST

Poniższy przykład stosuje ochronę przed akcjami zestawu skalowania do wystąpienia w zestawie skalowania.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vMScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true,
      "protectFromScaleSetActions": true
    }
  }        
}

```

> [!NOTE]
>Ochrona wystąpień jest obsługiwana tylko w wersji interfejsu API 2019-03-01 i wyższej.</br>
Ochrona wystąpienia przed akcjami zestawu skalowania chroni również wystąpienie przed skalowaniem automatycznym inicjowanym skalowaniem. Nie można określić "protectFromScaleIn": false podczas ustawiania "protectFromScaleSetActions": true

### <a name="azure-powershell"></a>Azure PowerShell

Polecenie cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) służy do stosowania ochrony przed akcjami zestawu skalowania do wystąpienia zestawu skalowania.

Poniższy przykład stosuje ochronę przed akcjami zestawu skalowania do wystąpienia w zestawie skalowania o identyfikatorze wystąpienia 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Użyj [aktualizacji az vmss,](/cli/azure/vmss#az-vmss-update) aby zastosować ochronę przed akcjami zestawu skalowania do wystąpienia zestawu skalowania.

Poniższy przykład stosuje ochronę przed akcjami zestawu skalowania do wystąpienia w zestawie skalowania o identyfikatorze wystąpienia 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Rozwiązywanie problemów
### <a name="no-protectionpolicy-on-scale-set-model"></a>Brak ochronyPolicy na modelu zestawu skalowania
Ochrona wystąpienia ma zastosowanie tylko w przypadku wystąpień zestawu skalowania, a nie w modelu zestawu skalowania.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Brak ochronyPolicy na modelu wystąpienia zestawu skalowania
Domyślnie zasady ochrony nie są stosowane do wystąpienia podczas jej tworzenia.

Ochronę wystąpienia można zastosować do skalowania ustawionych wystąpień po utworzeniu wystąpień.

### <a name="not-able-to-apply-instance-protection"></a>Nie można zastosować ochrony wystąpienia
Ochrona wystąpień jest obsługiwana tylko w wersji interfejsu API 2019-03-01 i wyższej. Sprawdź wersję interfejsu API, która jest używana i aktualizuj zgodnie z wymaganiami. Może być również konieczne zaktualizowanie programu PowerShell lub interfejsu wiersza polecenia do najnowszej wersji.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [wdrożyć aplikację](virtual-machine-scale-sets-deploy-app.md) w zestawach skalowania maszyny wirtualnej.
