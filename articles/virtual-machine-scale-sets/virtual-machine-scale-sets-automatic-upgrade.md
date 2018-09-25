---
title: Zestawy skalowania automatycznego uaktualniania systemu operacyjnego za pomocą maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak automatycznie uaktualnić system operacyjny na wystąpieniach maszyn wirtualnych w zestawie skalowania
services: virtual-machine-scale-sets
documentationcenter: ''
author: yeki
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/03/2018
ms.author: yeki
ms.openlocfilehash: 935b3ff0fe03984b02dc2e1137f48e53b06ce0c2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995112"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-upgrades"></a>Automatyczne uaktualnianie systemu operacyjnego zestawu skalowania maszyn wirtualnych platformy Azure

Automatyczne uaktualnienia obrazu systemu operacyjnego jest funkcją w wersji zapoznawczej dla zestawów skalowania maszyn wirtualnych platformy Azure, która automatycznie uaktualnia wszystkich maszyn wirtualnych do najnowszego obrazu systemu operacyjnego.

Automatyczne uaktualnianie systemu operacyjnego ma następującą charakterystykę:

- Po skonfigurowaniu najnowszego obrazu systemu operacyjnego opublikowane przez wydawców obrazów jest automatycznie stosowany do skalowania, ustawić bez interwencji użytkownika.
- Uaktualnia partie wystąpień w sposób stopniowego każdorazowo, gdy nowy obraz platformy jest opublikowana przez wydawcę.
- Integruje się z sondy kondycji aplikacji (opcjonalne, lecz zdecydowanie zalecane pod kątem bezpieczeństwa).
- Działa w przypadku wszystkich rozmiarów maszyn wirtualnych.
- Obrazy platformy działa dla Windows i Linux.
- Można zrezygnować z automatycznych uaktualnień w dowolnym momencie (uaktualniania systemu operacyjnego można uruchomić ręcznie, jak i).
- Dysk systemu operacyjnego maszyny wirtualnej jest zastępowany nowy dysk systemu operacyjnego, utworzony za pomocą najnowszej wersji obrazu. Rozszerzenia skonfigurowany i skrypty niestandardowe dane są uruchamiane podczas utrwalonych danych, które dyski zostaną zachowane.


## <a name="preview-notes"></a>Informacje o wersji zapoznawczej 
W wersji zapoznawczej, obowiązują poniższe ograniczenia i ograniczenia:

- OS automatycznych uaktualnień jest obsługiwany tylko [cztery jednostki SKU systemu operacyjnego](#supported-os-images). Nie ma umowy SLA lub gwarancji. Zaleca się, że nie używasz automatycznych uaktualnień na krytycznych obciążeń produkcyjnych w trakcie okresu zapoznawczego.
- Usługa Azure disk encryption jest **nie** obecnie obsługiwane w przypadku maszyn wirtualnych skalowania Ustaw automatyczne uaktualnianie systemu operacyjnego.


## <a name="register-to-use-automatic-os-upgrade"></a>Zarejestruj się, aby użyć automatycznego uaktualniania systemu operacyjnego
Aby użyć funkcja aktualizacji automatycznych systemu operacyjnego, należy zarejestrować dostawcę w wersji zapoznawczej za pomocą programu Azure Powershell lub interfejsu wiersza polecenia platformy Azure w wersji 2.0.

### <a name="powershell"></a>PowerShell

1. Zarejestrowanie [element Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature):

     ```powershell
     Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName AutoOSUpgradePreview
     ```

2. Trwa około 10 minut, zanim stan rejestracji raport jako *zarejestrowanej*. Można sprawdzić bieżący stan rejestracji za pomocą [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). 

3. Po zarejestrowaniu, upewnij się, że *Microsoft.Compute* dostawca jest zarejestrowany. W poniższym przykładzie użyto programu Azure Powershell za pomocą [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider):

     ```powershell
     Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
     ```


### <a name="cli-20"></a>Interfejs wiersza polecenia 2.0

1. Zarejestrowanie [az feature register](/cli/azure/feature#az-feature-register):

     ```azurecli
     az feature register --name AutoOSUpgradePreview --namespace Microsoft.Compute
     ```

2. Trwa około 10 minut, zanim stan rejestracji raport jako *zarejestrowanej*. Można sprawdzić bieżący stan rejestracji za pomocą [az feature show](/cli/azure/feature#az-feature-show). 
 
3. Po zarejestrowaniu, upewnij się, że *Microsoft.Compute* dostawca jest zarejestrowany. W poniższym przykładzie użyto interfejsu wiersza polecenia platformy Azure (2.0.20 lub nowszej) z [az provider register](/cli/azure/provider#az-provider-register):

     ```azurecli
     az provider register --namespace Microsoft.Compute
     ```

> [!NOTE]
> Klastry usługi Service Fabric ma swoje własne pojęcie kondycji aplikacji, ale zestawy skalowania bez usługi Service Fabric użyć sondy kondycji modułu równoważenia obciążenia do monitorowania kondycji aplikacji. 
>
> ### <a name="azure-powershell"></a>Azure PowerShell
>
> 1. Zarejestruj funkcję dostawcy dla sondy kondycji za pomocą [element Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature):
>
>      ```powershell
>      Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVmssHealthProbe
>      ```
>
> 2. Ponownie, trwa około 10 minut, zanim stan rejestracji raport jako *zarejestrowanej*. Można sprawdzić bieżący stan rejestracji za pomocą [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature)
>
> 3. Gdy zarejestrowana upewnij się, że *Microsoft.Network* dostawca jest zarejestrowany przy użyciu [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider):
>
>      ```powershell
>      Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
>      ```
>
>
> ### <a name="cli-20"></a>Interfejs wiersza polecenia 2.0
>
> 1. Zarejestruj funkcję dostawcy dla sondy kondycji za pomocą [az feature register](/cli/azure/feature#az-feature-register):
>
>      ```azurecli
>      az feature register --name AllowVmssHealthProbe --namespace Microsoft.Network
>      ```
>
> 2. Ponownie, trwa około 10 minut, zanim stan rejestracji raport jako *zarejestrowanej*. Można sprawdzić bieżący stan rejestracji za pomocą [az feature show](/cli/azure/feature#az-feature-show). 
>
> 3. Gdy zarejestrowana upewnij się, że *Microsoft.Network* dostawca jest zarejestrowany przy użyciu [az provider register](/cli/azure/provider#az-provider-register) w następujący sposób:
>
>      ```azurecli
>      az provider register --namespace Microsoft.Network
>      ```

## <a name="portal-experience"></a>Środowisko portalu
Po wykonaniu powyższych kroków rejestracji możesz przejść do [witryny Azure portal](https://aka.ms/managed-compute) można włączyć automatyczne uaktualnianie systemu operacyjnego na zestawami skalowania i wyświetlany jest postęp uaktualnienia:

![](./media/virtual-machine-scale-sets-automatic-upgrade/automatic-upgrade-portal.png)


## <a name="supported-os-images"></a>Obsługiwane obrazy systemu operacyjnego
Obecnie obsługiwane są tylko niektóre obrazy platformy systemu operacyjnego. Nie można obecnie używać obrazów niestandardowych utworzone przez Ciebie. *Wersji* właściwości obrazu platformy musi być równa *najnowsze*.

Obecnie obsługiwane są następujące jednostki SKU (więcej rozwiązań zostanie dodanych):
    
| Wydawca               | Oferta         |  SKU               | Wersja  |
|-------------------------|---------------|--------------------|----------|
| Canonical               | UbuntuServer  | 16.04-LTS          | najnowsza   |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter | najnowsza   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    | najnowsza   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Smalldisk | najnowsza   |
| MicrosoftWindowsServer  | WindowsServer | 2016 centrum danych za pomocą kontenerów | najnowsza   |



## <a name="application-health-without-service-fabric"></a>Kondycja aplikacji bez usługi Service Fabric
> [!NOTE]
> Ta sekcja dotyczy tylko dla zestawów skalowania bez usługi Service Fabric. Usługa Service Fabric ma swój własny pojęcie kondycji aplikacji. Korzystając z automatycznych uaktualnień systemu operacyjnego za pomocą usługi Service Fabric, nowy obraz systemu operacyjnego jest udostępniona domena aktualizacji według domeny aktualizacji, aby zapewnić wysoką dostępność usługi działające w usłudze Service Fabric. Aby uzyskać więcej informacji na temat właściwości niezawodność klastrów usługi Service Fabric, zobacz [tej dokumentacji](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

Podczas uaktualniania systemu operacyjnego wystąpień maszyn wirtualnych w zestawie skalowania są uaktualniane jedną partię w danym momencie. Uaktualnianie powinno być kontynuowane tylko jeśli aplikacja klienta jest w dobrej kondycji na uaktualnionym wystąpieniach maszyn wirtualnych. Z tego powodu aplikacja musi udostępniać sygnałów kondycji z aparatem uaktualnienia systemu operacyjnego zestawu skalowania. Podczas uaktualniania systemu operacyjnego platformy uwzględnia stan zasilania maszyny Wirtualnej i stanu, aby określić, jeśli wystąpienie maszyny Wirtualnej jest w dobrej kondycji po uaktualnieniu aprowizacji rozszerzenia. Podczas uaktualniania systemu operacyjnego wystąpienia maszyny Wirtualnej dysk systemu operacyjnego na wystąpieniu maszyny Wirtualnej jest zastępowany nowy dysk, na podstawie najnowszej wersji obrazu. Po zakończeniu uaktualniania systemu operacyjnego skonfigurowany rozszerzenia są uruchamiane na tych maszynach wirtualnych. Tylko wtedy, gdy wszystkie rozszerzenia na maszynie Wirtualnej są pomyślnie zainicjowano obsługę administracyjną, aplikacji uznaje się dobrej kondycji. 

Ponadto zestaw skalowania *musi* można skonfigurować za pomocą sondy kondycji aplikacji pozwala udostępnić platformę przy użyciu poprawne informacje o aplikacji. Sondy kondycji aplikacji są niestandardowe obciążenia równoważenia sondy używany jako sygnał kondycji. Aplikacja była uruchomiona na wystąpieniu maszyny Wirtualnej zestawu skalowania mogą odpowiadać na zewnętrzne żądania HTTP lub TCP, wskazującą, czy jest w dobrej kondycji. Aby uzyskać więcej informacji na temat sposobu działania niestandardowego obciążenia sondy modułu równoważenia zobacz Aby [sondy modułu równoważenia obciążenia omówienie](../load-balancer/load-balancer-custom-probe-overview.md).

Jeśli zestaw skalowania jest skonfigurowany do używania wielu grup umieszczania, sondy, za pomocą [Balancer w warstwie standardowa](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) trzeba było używać.

### <a name="important-keep-credentials-up-to-date"></a>Ważne: Zachowaj poświadczenia aktualne
Jeśli zestaw skalowania korzysta z żadnych poświadczeń dostępu do zasobów zewnętrznych, należy upewnić się, że poświadczenia są zawsze na bieżąco. Na przykład rozszerzenie maszyny Wirtualnej może być skonfigurowany do użycia tokenu sygnatury dostępu Współdzielonego dla konta magazynu. Jeśli wszystkie poświadczenia, w tym certyfikaty i tokeny wygasły, uaktualnienie nie powiedzie, a pierwszej partii maszyn wirtualnych zostanie pozostawiony w stanie niepowodzenia.

Są zalecane kroki, aby odzyskać maszyn wirtualnych, a następnie ponownie Włącz automatyczne uaktualnianie systemu operacyjnego w przypadku niepowodzenia uwierzytelniania zasobów:

* Ponowne generowanie tokenu (lub innych poświadczeń) przekazany do Twojego rozszerzenia.
* Upewnij się, że dowolnych poświadczeń, używane z wewnątrz maszyny Wirtualnej na komunikowanie się z podmiotów zewnętrznych jest aktualny.
* Zaktualizuj rozszerzenia w modelu zestawu skalowania przy użyciu dowolnej nowych tokenów.
* Wdrażanie zestawu skalowania zaktualizowane, który zaktualizuje wszystkie wystąpienia maszyn wirtualnych, w tym te, które nie powiodło się. 

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurowanie sondy modułu równoważenia obciążenia niestandardowe jako sonda kondycji aplikacji w skali zestawu
Możesz *musi* jawnie utworzyć sondy modułu równoważenia obciążenia dla kondycji zestawu skalowania. Można użyć tego samego punktu końcowego dla istniejących sondy HTTP lub sonda TCP, ale sondę kondycji, mogą wymagać różnych zachowanie z sondy modułu równoważenia obciążenia tradycyjnych. Na przykład sondy modułu równoważenia obciążenia tradycyjnych może zwrócić w złej kondycji, jeśli obciążenie na wystąpieniu jest zbyt duża. Z drugiej strony, może nie być odpowiednie dla określania kondycji wystąpień podczas automatycznego uaktualniania systemu operacyjnego. Konfigurowanie sondy użycia mają wysoki wskaźnik sondowania wynosi mniej niż dwie minuty.

Sondy modułu równoważenia obciążenia może być przywoływany w *elementy networkProfile* skali zestawu i może być skojarzony z obu wewnętrznego lub publicznego — moduł równoważenia obciążenia w następujący sposób:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```


## <a name="enforce-an-os-image-upgrade-policy-across-your-subscription"></a>Wymuszanie zasad uaktualnienia obrazu systemu operacyjnego w ramach subskrypcji
Bezpieczne uaktualnień zaleca do wymuszania zasad uaktualniania. Ta zasada może wymagać sondy kondycji aplikacji w ramach subskrypcji. Następujące zasady usługi Azure Resource Manager odrzuca wdrożeń, które nie mają automatycznych obrazu systemu operacyjnego, konfigurowane ustawienia uaktualniania:

### <a name="powershell"></a>PowerShell
1. Uzyskaj wbudowaną definicję zasad usługi Azure Resource Manager za pomocą [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) w następujący sposób:

    ```powershell
    $policyDefinition = Get-AzureRmPolicyDefinition -Id "/providers/Microsoft.Authorization/policyDefinitions/465f0161-0087-490a-9ad9-ad6217f4f43a"
    ```

2. Przypisywanie zasad do subskrypcji przy użyciu [New-AzureRmPolicyAssignment](/powershell/module/AzureRM.Resources/New-AzureRmPolicyAssignment) w następujący sposób:

    ```powershell
    New-AzureRmPolicyAssignment `
        -Name "Enforce automatic OS upgrades with app health checks" `
        -Scope "/subscriptions/<SubscriptionId>" `
        -PolicyDefinition $policyDefinition
    ```

### <a name="cli-20"></a>Interfejs wiersza polecenia 2.0
Przypisz zasady do subskrypcji przy użyciu wbudowanych zasad usługi Azure Resource Manager:

```azurecli
az policy assignment create --display-name "Enforce automatic OS upgrades with app health checks" --name "Enforce automatic OS upgrades" --policy 465f0161-0087-490a-9ad9-ad6217f4f43a --scope "/subscriptions/<SubscriptionId>"
```

## <a name="configure-auto-updates"></a>Skonfiguruj aktualizacje automatyczne
Aby skonfigurować automatyczne uaktualnienia, upewnij się, że *automatyczne uaktualnianie systemu operacyjnego* właściwość jest ustawiona na *true* w zestawu skalowania definicję modelu. Tej właściwości można skonfigurować przy użyciu programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

### <a name="powershell"></a>PowerShell
W poniższym przykładzie użyto programu Azure PowerShell (4.4.1 lub nowszej) Aby skonfigurować automatyczne uaktualnienia dla zestawu skalowania o nazwie *myVMSS* w grupie zasobów o nazwie *myResourceGroup*:

```powershell
$rgname = myResourceGroup
$vmssname = myVMSS
$vmss = Get-AzureRmVMss -ResourceGroupName $rgname -VmScaleSetName $vmssname
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true
Update-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname -VirtualMachineScaleSet $vmss
```

### <a name="cli-20"></a>Interfejs wiersza polecenia 2.0
W poniższym przykładzie użyto interfejsu wiersza polecenia platformy Azure (2.0.20 lub nowszej) Aby skonfigurować automatyczne uaktualnienia dla zestawu skalowania o nazwie *myVMSS* w grupie zasobów o nazwie *myResourceGroup*:

```azurecli
rgname="myResourceGroup"
vmssname="myVMSS"
az vmss update --name $vmssname --resource-group $rgname --set upgradePolicy.AutomaticOSUpgrade=true
```


## <a name="check-the-status-of-an-automatic-os-upgrade"></a>Sprawdź stan automatycznego uaktualniania systemu operacyjnego
Można sprawdzić status najnowszych uaktualnienia systemu operacyjnego, wykonywane na zestawie skalowania jest ustawiana za pomocą programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsów API REST.

### <a name="powershell"></a>PowerShell
Poniższy przykład używa programu Azure PowerShell (4.4.1 lub nowszej) można sprawdzić stanu dla zestawu skalowania o nazwie *myVMSS* w grupie zasobów o nazwie *myResourceGroup*:

```powershell
Get-AzureRmVmssRollingUpgrade -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W poniższym przykładzie użyto interfejsu wiersza polecenia platformy Azure (2.0.20 lub nowszej) można sprawdzić stanu dla zestawu skalowania o nazwie *myVMSS* w grupie zasobów o nazwie *myResourceGroup*:

```azurecli
az vmss rolling-upgrade get-latest --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>Interfejs API REST
W poniższym przykładzie użyto interfejsu API REST w celu sprawdzenia stanu dla zestawu skalowania o nazwie *myVMSS* w grupie zasobów o nazwie *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/rollingUpgrades/latest?api-version=2017-03-30`
```

Wywołanie GET zwraca właściwości podobne do następujących przykładowych danych wyjściowych:

```json
{
  "properties": {
    "policy": {
      "maxBatchInstancePercent": 20,
      "maxUnhealthyInstancePercent": 5,
      "maxUnhealthyUpgradedInstancePercent": 5,
      "pauseTimeBetweenBatches": "PT0S"
    },
    "runningStatus": {
      "code": "Completed",
      "startTime": "2017-06-16T03:40:14.0924763+00:00",
      "lastAction": "Start",
      "lastActionTime": "2017-06-22T08:45:43.1838042+00:00"
    },
    "progress": {
      "successfulInstanceCount": 3,
      "failedInstanceCount": 0,
      "inprogressInstanceCount": 0,
      "pendingInstanceCount": 0
    }
  },
  "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
  "location": "southcentralus"
}
```


## <a name="automatic-os-upgrade-execution"></a>Wykonanie automatycznego uaktualniania systemu operacyjnego
Aby rozwinąć związane z użyciem sond kondycji aplikacji, uaktualnienia systemu operacyjnego zestawu skalowania wykonaj następujące czynności:

1. Jeśli więcej niż 20% wystąpień ma złą kondycję, Zatrzymaj uaktualniania. w przeciwnym razie Kontynuuj.
2. Zidentyfikuj następną partię wystąpień maszyn wirtualnych do uaktualnienia przy użyciu usługi batch, o maksymalnej 20% liczba całkowita wystąpień.
3. Uaktualnij system operacyjny z następną partię wystąpień maszyn wirtualnych.
4. Jeśli więcej niż 20% uaktualnionego wystąpień ma złą kondycję, Zatrzymaj uaktualniania. w przeciwnym razie Kontynuuj.
5. Dla zestawów skalowania, które nie są częścią klastra usługi Service Fabric uaktualnienie czeka maksymalnie 5 minut, zanim sondy do poprawi, a następnie od razu będzie kontynuowane na następną partię. Dla zestawów skalowania, które są częścią klastra usługi Service Fabric zestawu skalowania w tym czasie czeka 30 minut przed przejściem do następnej partii.
6. Jeśli są pozostałe wystąpienia do uaktualnienia, przejdź do kroku 1) dla nowej partii; w przeciwnym razie uaktualnienie jest pełny.

Aparat uaktualnienia systemu operacyjnego sprawdza, czy ogólnej kondycji wystąpień maszyny Wirtualnej przed uaktualnieniem każdej partii zestawu skalowania. Podczas uaktualniania usługi batch, może być innych jednoczesnych planowana lub nieplanowana konserwacja działań wykonywanych w centrach danych platformy Azure, które mogą mieć wpływ na dostępność maszyn wirtualnych z systemem. W związku z tym jest możliwe, że może to być tymczasowo więcej niż 20% wystąpień. W takich przypadkach na koniec bieżącej partii zestawu skalowania zatrzymuje uaktualnienia.


## <a name="deploy-with-a-template"></a>Wdrażanie przy użyciu szablonu

Następujący szablon umożliwia wdrożenie zestawu skalowania, który korzysta z automatycznych uaktualnień <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>automatycznych uaktualnień stopniowych — Ubuntu 16.04-LTS</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej przykładów na temat korzystania z automatycznego uaktualniania systemu operacyjnego za pomocą zestawów skalowania, zobacz [repozytorium GitHub dla funkcji w wersji zapoznawczej](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
