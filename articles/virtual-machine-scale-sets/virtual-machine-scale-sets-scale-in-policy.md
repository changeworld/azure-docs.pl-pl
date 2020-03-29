---
title: Używanie niestandardowych zasad skalowania w przypadku zestawów skalowania maszyn wirtualnych platformy Azure
description: Dowiedz się, jak używać niestandardowych zasad skalowania w przypadku zestawów skalowania maszyny wirtualnej platformy Azure, które używają konfiguracji skalowania automatycznego do zarządzania liczbą wystąpień
services: virtual-machine-scale-sets
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: ffcdaf76bdd08ee5505ddbeff6a6698e231b6171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919842"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Używanie niestandardowych zasad skalowania w przypadku zestawów skalowania maszyn wirtualnych platformy Azure

Wdrożenie zestawu skalowania maszyny wirtualnej można skalować w poziomie lub skalować na podstawie tablicy metryk, w tym metryki niestandardowe zdefiniowane przez platformę i użytkownika. Podczas skalowania w poziomie tworzy nowe maszyny wirtualne na podstawie modelu zestawu skalowania, skalowanie w wpływa na uruchamianie maszyn wirtualnych, które mogą mieć różne konfiguracje i/lub funkcje w miarę ewolucji obciążenia zestawu skalowania. 

Funkcja zasad skalowania w umożliwia użytkownikom skonfigurowanie kolejności skalowania maszyn wirtualnych w drodze trzech konfiguracji skalowania: 

1. Domyślne
2. Najnowszyvm
3. Najstarszyvm

### <a name="default-scale-in-policy"></a>Domyślna zasada skalowania

Domyślnie zestaw skalowania maszyny wirtualnej stosuje tę zasadę, aby określić, które wystąpienia będą skalowane. W przypadku zasad *domyślnych* maszyny wirtualne są wybierane do skalowania w następującej kolejności:

1. Równoważ maszyny wirtualne w strefach dostępności (jeśli zestaw skalowania jest wdrażany w konfiguracji strefowej)
2. Równoważ maszyny wirtualne między domenami błędów (najlepszy wysiłek)
3. Usuwanie maszyny wirtualnej o najwyższym identyfikatorze wystąpienia

Użytkownicy nie muszą określać zasad skalowania w, jeśli chcą po prostu domyślnej kolejności.

Należy zauważyć, że równoważenie między strefami dostępności lub domenami błędów nie przenosi wystąpień między strefami dostępności lub domenami błędów. Równoważenie jest osiągane przez usunięcie maszyn wirtualnych z niezrównoważonych stref dostępności lub domen błędów, dopóki dystrybucja maszyn wirtualnych nie stanie się zrównoważona.

### <a name="newestvm-scale-in-policy"></a>Zasady skalowania newestvm

Ta zasada spowoduje usunięcie najnowszej utworzonej maszyny wirtualnej w zestawie skalowania, po zrównoważeniu maszyn wirtualnych w strefach dostępności (dla wdrożeń strefowych). Włączenie tej zasady wymaga zmiany konfiguracji w modelu zestawu skalowania maszyny wirtualnej.

### <a name="oldestvm-scale-in-policy"></a>Zasady skalowania najstarszego VM

Ta zasada spowoduje usunięcie najstarszej utworzonej maszyny wirtualnej w zestawie skalowania po zrównoważeniu maszyn wirtualnych w strefach dostępności (dla wdrożeń strefowych). Włączenie tej zasady wymaga zmiany konfiguracji w modelu zestawu skalowania maszyny wirtualnej.

## <a name="enabling-scale-in-policy"></a>Włączanie zasad skalowania

Zasady skalowania w jest zdefiniowany w modelu zestawu skalowania maszyny wirtualnej. Jak wspomniano w powyższych sekcjach, definicja zasad skalowania w jest potrzebna podczas korzystania z zasad "NewestVM" i "OldestVM". Zestaw skalowania maszyny wirtualnej będzie automatycznie używać zasad skalowania "Domyślne", jeśli w modelu zestawu skalowania nie znaleziono definicji zasad skalowania. 

Zasady skalowania w można zdefiniować w modelu zestawu skalowania maszyny wirtualnej w następujący sposób:

### <a name="azure-portal"></a>Portal Azure
 
Poniższe kroki definiują zasady skalowania podczas tworzenia nowego zestawu skalowania. 
 
1. Przejdź do **zestawów skalowania maszyny wirtualnej**.
1. Wybierz **+ Dodaj,** aby utworzyć nowy zestaw skalowania.
1. Przejdź do karty **Skalowanie.** 
1. Znajdź sekcję **zasad skalowania w.**
1. Wybierz zasadę skalowania w z listy rozwijanej.
1. Po zakończeniu tworzenia nowego zestawu skalowania wybierz przycisk **Przejrzyj + utwórz.**

### <a name="using-api"></a>Używanie interfejsu API

Wykonaj put na zestaw skalowania maszyny wirtualnej przy użyciu interfejsu API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["OldestVM"]  
        } 
    }    
} 
```
### <a name="azure-powershell"></a>Azure PowerShell

Utwórz grupę zasobów, a następnie utwórz nowy zestaw skalowania z zasadami skalowania ustawionymi jako *OldestVM*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Poniższy przykład dodaje zasady skalowania w podczas tworzenia nowego zestawu skalowania. Najpierw utwórz grupę zasobów, a następnie utwórz nowy zestaw skalowania z zasadami skalowania jako *OldestVM*. 

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Korzystanie z szablonu

W szablonie w obszarze "właściwości" dodaj następujące elementy:

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

Powyższe bloki określają, że zestaw skalowania maszyny wirtualnej spowoduje usunięcie najstarszej maszyny wirtualnej w zestawie skalowania zbilansowaną strefą, gdy zostanie wyzwolona skala (za pomocą skalowania automatycznego lub ręcznego usuwania).

Gdy zestaw skalowania maszyny wirtualnej nie jest zrównoważony strefą, zestaw skalowania najpierw usunie maszyny wirtualne w strefach nierównomjnych. W strefach nierównowagi zestaw skalowania użyje zasad skalowania w określonych powyżej, aby określić, które maszyny Wirtualnej do skalowania. W takim przypadku w strefie nierównowagi zestaw skalowania wybierze najstarszą maszynę wirtualną w tej strefie do usunięcia.

W przypadku niezgonalnego zestawu skalowania maszyny wirtualnej zasada wybiera najstarszą maszynę wirtualną w skali ustawionej do usunięcia.

Ten sam proces ma zastosowanie w przypadku korzystania z "NewestVM" w powyższych zasadach skalowania.

## <a name="modifying-scale-in-policies"></a>Modyfikowanie zasad skalowania

Modyfikowanie zasad skalowania w przebiega zgodnie z tym samym procesem, co stosowanie zasad skalowania w. Na przykład, jeśli w powyższym przykładzie chcesz zmienić zasady z "OldestVM" na "NewestVM", możesz to zrobić przez:

### <a name="azure-portal"></a>Portal Azure

Można zmodyfikować zasady skalowania istniejącej skali ustawionej za pośrednictwem witryny Azure portal. 
 
1. W istniejącym zestawie skalowania maszyny wirtualnej wybierz **opcję Skalowanie** z menu po lewej stronie.
1. Wybierz kartę **Zasady skalowania w.**
1. Wybierz zasadę skalowania w z listy rozwijanej.
1. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**. 

### <a name="using-api"></a>Używanie interfejsu API

Wykonaj put na zestaw skalowania maszyny wirtualnej przy użyciu interfejsu API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01 

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["NewestVM"]  
        } 
    }    
}
```
### <a name="azure-powershell"></a>Azure PowerShell

Zaktualizuj zasady skalowania istniejącego zestawu skalowania:

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Poniżej przedstawiono przykład aktualizacji zasad skalowania w istniejącym zestawie skalowania: 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Korzystanie z szablonu

W szablonie w obszarze "właściwości" zmodyfikuj szablon w następujący sposób i ponownie wdrożyć: 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

Ten sam proces będzie miał zastosowanie, jeśli zdecydujesz się zmienić "NewestVM" na "Default" lub "OldestVM"

## <a name="instance-protection-and-scale-in-policy"></a>Zasady ochrony wystąpień i skalowania

Zestawy skalowania maszyny wirtualnej zapewniają dwa typy [ochrony przed wystąpieniami:](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection)

1. Ochrona przed skalowaniem
2. Ochrona przed akcjami skalowania

Chroniona maszyna wirtualna nie jest usuwana za pomocą akcji skalowania w, niezależnie od zastosowanej zasady skalowania. Na przykład jeśli VM_0 (najstarsza maszyna wirtualna w zestawie skalowania) jest chroniona przed skalowaniem, a zestaw skalowania ma włączoną zasadę skalowania "OldestVM", VM_0 nie będą brane pod uwagę pod kątem skalowania, nawet jeśli jest to najstarsza maszyna wirtualna w zestawie skalowania. 

Chroniona maszyna wirtualna może zostać ręcznie usunięta przez użytkownika w dowolnym momencie, niezależnie od zasad skalowania w zestawie skalowania. 

## <a name="usage-examples"></a>Przykłady użycia 

Poniższe przykłady pokazują, jak zestaw skalowania maszyny wirtualnej wybierze maszyny wirtualne do usunięcia po wyzwoleniu zdarzenia skalowania. Maszyny wirtualne o najwyższych identyfikatorach wystąpień są zakładane jako najnowsze maszyny wirtualne w zestawie skalowania i maszyny wirtualne o najmniejszych identyfikatorach wystąpień są uważane za najstarsze maszyny wirtualne w zestawie skalowania. 

### <a name="oldestvm-scale-in-policy"></a>Zasady skalowania najstarszego VM

| Wydarzenie                 | Identyfikatory wystąpień w strefie 1  | Identyfikatory wystąpień w strefie 2  | Identyfikatory wystąpień w strefie3  | Wybór skali w                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Wartość początkowa               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Skalowanie w              | 3, 4, 5, 10            | ***2***, 6, 9, 11      | 1, 7, 8                | Wybierz strefę 1 i 2, mimo że strefa 3 ma najstarszą maszynę wirtualną. Usuń VM2 ze strefy 2, ponieważ jest to najstarsza maszyna wirtualna w tej strefie.   |
| Skalowanie w              | ***3***, 4, 5, 10      | 6, 9, 11               | 1, 7, 8                | Wybierz strefę 1, mimo że strefa 3 ma najstarszą maszynę wirtualną. Usuń maszynę wirtualną VM3 ze strefy 1, ponieważ jest to najstarsza maszyna wirtualna w tej strefie.                  |
| Skalowanie w              | 4, 5, 10               | 6, 9, 11               | ***1***, 7, 8          | Strefy są zrównoważone. Usuń maszynę wirtualną1 w strefie 3, ponieważ jest to najstarsza maszyna wirtualna w zestawie skalowania.                                               |
| Skalowanie w              | ***4***, 5, 10         | 6, 9, 11               | 7, 8                   | Wybierz strefę 1 i strefę 2. Usuń VM4 w strefie 1, ponieważ jest to najstarsza maszyna wirtualna w dwóch strefach.                              |
| Skalowanie w              | 5, 10                  | ***6***, 9, 11         | 7, 8                   | Wybierz strefę 2, mimo że strefa 1 ma najstarszą maszynę wirtualną. Usuń maszynę wirtualną6 w strefie 1, ponieważ jest najstarszą maszyną wirtualną w tej strefie.                    |
| Skalowanie w              | ***5***, 10            | 9, 11                  | 7, 8                   | Strefy są zrównoważone. Usuń VM5 w strefie 1, ponieważ jest to najstarsza maszyna wirtualna w zestawie skalowania.                                                |

W przypadku niezdonalnych zestawów skalowania maszyn wirtualnych zasada wybiera najstarszą maszynę wirtualną w skali ustawionej do usunięcia. Każda "chroniona" maszyna wirtualna zostanie pominięta w celu usunięcia.

### <a name="newestvm-scale-in-policy"></a>Zasady skalowania newestvm

| Wydarzenie                 | Identyfikatory wystąpień w strefie 1  | Identyfikatory wystąpień w strefie 2  | Identyfikatory wystąpień w strefie3  | Wybór skali w                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Wartość początkowa               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Skalowanie w              | 3, 4, 5, 10            | 2, 6, 9, ***11***      | 1, 7, 8                | Wybierz strefę 1 i 2. Usuń VM11 ze strefy 2, ponieważ jest to najnowsza maszyna wirtualna w obu strefach.                                |
| Skalowanie w              | 3, 4, 5, ***10***      | 2, 6, 9                | 1, 7, 8                | Wybierz strefę 1, ponieważ ma więcej maszyn wirtualnych niż pozostałe dwie strefy. Usuń maszynę wirtualną10 ze strefy 1, ponieważ jest to najnowsza maszyna wirtualna w tej strefie.          |
| Skalowanie w              | 3, 4, 5                | 2, 6, ***9***          | 1, 7, 8                | Strefy są zrównoważone. Usuń maszynę wirtualną9 w strefie 2, ponieważ jest to najnowsza maszyna wirtualna w zestawie skalowania.                                                |
| Skalowanie w              | 3, 4, 5                | 2, 6                   | 1, 7, ***8***          | Wybierz strefę 1 i strefę 3. Usuń VM8 w strefie 3, ponieważ jest to najnowsza maszyna wirtualna w tej strefie.                                      |
| Skalowanie w              | 3, 4, ***5***          | 2, 6                   | 1, 7                   | Wybierz strefę 1, mimo że strefa 3 ma najnowszą maszynę wirtualną. Usuń VM5 w strefie 1, ponieważ jest to najnowsza maszyna wirtualna w tej strefie.                    |
| Skalowanie w              | 3, 4                   | 2, 6                   | 1, ***7***             | Strefy są zrównoważone. Usuń VM7 w strefie 3, ponieważ jest to najnowsza maszyna wirtualna w zestawie skalowania.                                                |

W przypadku niezdonalnych zestawów skalowania maszyn wirtualnych zasady wybierają najnowszą maszynę wirtualną w skali ustawionej do usunięcia. Każda "chroniona" maszyna wirtualna zostanie pominięta w celu usunięcia. 

## <a name="troubleshoot"></a>Rozwiązywanie problemów

1. Nie można włączyć scaleInPolicy Jeśli pojawi się błąd "BadRequest" z komunikatem o błędzie z napisem "Nie można znaleźć elementu członkowskiego "scaleInPolicy" na obiekcie typu 'właściwości'", sprawdź wersję interfejsu API używaną dla zestawu skalowania maszyny wirtualnej. Dla tej funkcji wymagana jest wersja interfejsu API 2019-03-01 lub nowsza.

2. Niewłaściwy wybór maszyn wirtualnych dla skalowania w Zapoznaj się z powyższymi przykładami. Jeśli zestaw skalowania maszyny wirtualnej jest wdrożeniem strefowym, zasady skalowania w są stosowane najpierw do stref nierównowagalnych, a następnie w przypadku zestawu skalowania po zrównoważeniu strefy. Jeśli kolejność skalowania w nie jest zgodna z powyższymi przykładami, należy podnieść kwerendę z zespołem zestawu skalowania maszyny wirtualnej w celu rozwiązywania problemów.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wdrożyć aplikację](virtual-machine-scale-sets-deploy-app.md) w zestawach skalowania maszyny wirtualnej.