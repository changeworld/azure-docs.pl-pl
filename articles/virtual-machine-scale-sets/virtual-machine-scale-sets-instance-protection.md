---
title: Wystąpienia ustawiona ochrona wystąpienia dla skalowania maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak chronić wystąpień zestawu skalowania maszyn wirtualnych platformy Azure z operacje skalowania na zewnątrz i zestawu skalowania.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: manayar
ms.openlocfilehash: 6c271c2c9feb1520951b2a8e301da4878970d60a
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259425"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances-preview"></a>Wystąpienia (wersja zapoznawcza) ustawiona ochrona wystąpienia dla skalowania maszyn wirtualnych platformy Azure
Maszyna wirtualna platformy Azure scale sets umożliwia lepsze elastyczność obciążeń za pośrednictwem [skalowania automatycznego](virtual-machine-scale-sets-autoscale-overview.md), dzięki czemu możesz skonfigurować, kiedy się skaluje infrastruktury i podczas jej skalowana w pionie. Zestawy skalowania umożliwiają także centralnie zarządzanie, konfigurowanie i aktualizowanie dużej liczby maszyn wirtualnych za pośrednictwem różnych [zasad uaktualniania](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) ustawienia. Aktualizację można skonfigurować w modelu zestawu skalowania i nowa konfiguracja jest stosowane automatycznie do każdego wystąpienia w zestawie skalowania, gdy zasad uaktualniania został ustawiony na tryb automatyczny lub wycofywanie.

Aplikacja przetwarza ruchu, może wystąpić sytuacje wymagające konkretnych wystąpień, aby być traktowane inaczej niż pozostałe skalowania wystąpienia zestawu. Na przykład niektórych wystąpień w zestawie skalowania można wykonywania długotrwałych operacji, a nie chcesz, aby te wystąpienia można skalować-w do momentu ukończenia operacji. Użytkownik może również mieć wyspecjalizowane kilka wystąpień w zestawie skalowania, aby wykonywać zadania dodatkowym lub innym niż pozostałe elementy członkowskie zestawu skalowania. Potrzebujesz tych "specjalne" maszyn wirtualnych nie można modyfikować za pomocą innych wystąpień w zestawie skalowania. Ochrona wystąpienia zapewnia dodatkowe formanty, aby włączyć te i inne scenariusze dla aplikacji.

W tym artykule opisano, jak można zastosować i inne wystąpienie funkcji ochrony za pomocą wystąpień zestawu skalowania.

> [!NOTE]
>Ochrona wystąpienia jest obecnie w publicznej wersji zapoznawczej. Żadna procedura uczestnictwo jest potrzebna do korzystania z funkcji w publicznej wersji zapoznawczej opisano poniżej. Wystąpienie protection w wersji zapoznawczej jest obsługiwana tylko przy użyciu interfejsu API w wersji 2019-03-01 i nowszych.

## <a name="types-of-instance-protection"></a>Typy ochrony wystąpienia
Zestawy skalowania zapewniają dwa typy funkcji ochrony wystąpienie:

-   **Ochrona przed skalowania na zewnątrz**
    - Włączone za pomocą **protectFromScaleIn** na skali właściwością wystąpienia
    - Ochronę wystąpienia skalowania automatycznego skalowania zainicjowane w
    - Operacje wystąpienie zainicjowane przez użytkownika (w tym wystąpienia delete) są **nie zostało zablokowane**
    - Działania zainicjowane w zestawie skalowania (uaktualnianie, odtwarzanie z obrazu, cofnięcie przydziału itp.) są **nie zostało zablokowane**

-   **Ochrona przed akcje zestawu skalowania**
    - Włączone za pomocą **protectFromScaleSetActions** na skali właściwością wystąpienia
    - Ochronę wystąpienia skalowania automatycznego skalowania zainicjowane w
    - Ochronę wystąpienia w zestawie skalowania operacji (takich jak uaktualnianie, odtwarzanie z obrazu, cofnięcie przydziału itp.)
    - Operacje wystąpienie zainicjowane przez użytkownika (w tym wystąpienia delete) są **nie zostało zablokowane**
    - Usuń zestaw skali jest **nie zostało zablokowane**

## <a name="protect-from-scale-in"></a>Ochrona przed skalowania na zewnątrz
Wystąpienie ochronę można zastosować wystąpień zestawów skalowania, po utworzeniu wystąpienia. Ochrona jest stosowane i modyfikować tylko w systemach [modelu wystąpień](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) a nie w systemie [modelu zestawu skalowania](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Istnieje wiele sposobów stosowania ochrony w skali na wystąpieniami danego zestawu skalowania zgodnie z opisem w poniższych przykładach.

### <a name="rest-api"></a>Interfejs API REST

Poniższy przykład dotyczy ochrony skalowania na zewnątrz wystąpienia w zestawie skalowania.

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
>Ochrona wystąpienia jest obsługiwany tylko przy użyciu interfejsu API w wersji 2019-03-01 i nowszych

### <a name="azure-powershell"></a>Azure PowerShell

Użyj [AzVmssVM aktualizacji](/powershell/module/az.compute/update-azvmssvm) wystąpienie zestawu polecenia cmdlet, aby zabezpieczać skalowania na zewnątrz do skalowania.

Poniższy przykład dotyczy ochrony skalowania na zewnątrz wystąpienia w zestawie skalowania, mających identyfikator wystąpienia 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Użyj [aktualizacji az vmss](/cli/azure/vmss#az-vmss-update) do stosowania ochrony w skalowania na zewnątrz do wystąpienia w zestawie skalowania.

Poniższy przykład dotyczy ochrony skalowania na zewnątrz wystąpienia w zestawie skalowania, mających identyfikator wystąpienia 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Ochrona przed akcje zestawu skalowania
Wystąpienie ochronę można zastosować wystąpień zestawów skalowania, po utworzeniu wystąpienia. Ochrona jest stosowane i modyfikować tylko w systemach [modelu wystąpień](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) a nie w systemie [modelu zestawu skalowania](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Ochrona wystąpienia akcje zestawu skalowania chroni także wystąpienia ze skalowania automatycznego skalowania zainicjowane w.

Istnieje wiele sposobów stosowania skalowania nastavit ochrony akcje skalowania wystąpień zestawu zgodnie z opisem w poniższych przykładach.

### <a name="rest-api"></a>Interfejs API REST

Poniższy przykład dotyczy ochrony z akcje zestawu skalowania do wystąpienia w zestawie skalowania.

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
>Ochrona wystąpienia jest obsługiwana tylko przy użyciu interfejsu API w wersji 2019-03-01 i nowszych.</br>
Ochrona wystąpienia akcje zestawu skalowania chroni także wystąpienia ze skalowania automatycznego skalowania zainicjowane w. Nie można określić "protectFromScaleIn": FAŁSZ, gdy ustawienie "protectFromScaleSetActions": true

### <a name="azure-powershell"></a>Azure PowerShell

Użyj [AzVmssVM aktualizacji](/powershell/module/az.compute/update-azvmssvm) polecenia cmdlet w celu zastosowania ochrony od skali ustawiania akcji do wystąpienia w zestawie skalowania.

Poniższy przykład dotyczy ochrony z akcje zestawu skalowania do wystąpienia w zestawie skalowania, mających identyfikator wystąpienia 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Użyj [aktualizacji az vmss](/cli/azure/vmss#az-vmss-update) Aby zastosować ochronę z akcje zestawu skalowania do wystąpienia w zestawie skalowania.

Poniższy przykład dotyczy ochrony z akcje zestawu skalowania do wystąpienia w zestawie skalowania, mających identyfikator wystąpienia 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Rozwiązywanie problemów
### <a name="no-protectionpolicy-on-scale-set-model"></a>Nie protectionPolicy w modelu zestawu skalowania
Ochrona wystąpienia ma zastosowanie tylko w wystąpieniach zestawu skalowania, a nie w modelu zestawu skalowania.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Nie protectionPolicy w modelu wystąpień zestawu skalowania
Domyślnie zasady ochrony nie ma zastosowania do wystąpienia podczas jego tworzenia.

Można zastosować ochronę wystąpienia wystąpień zestawów skalowania, po utworzeniu wystąpienia.

### <a name="not-able-to-apply-instance-protection"></a>Nie można zastosować ochronę wystąpienia
Ochrona wystąpienia jest obsługiwana tylko przy użyciu interfejsu API w wersji 2019-03-01 i nowszych. Sprawdź używana wersja interfejsu API i zaktualizuj zgodnie z potrzebami. Może być również konieczne aktualizacji programu PowerShell lub interfejsu wiersza polecenia do najnowszej wersji.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [wdrożyć aplikację](virtual-machine-scale-sets-deploy-app.md) ustawia skalowania maszyn wirtualnych.
