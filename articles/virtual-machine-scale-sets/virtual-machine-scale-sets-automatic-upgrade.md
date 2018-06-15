---
title: Zestawy skalowania automatycznych uaktualnień systemu operacyjnego z maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak automatycznie uaktualnić system operacyjny na wystąpień maszyny Wirtualnej w zestawie skalowania
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: negat
ms.openlocfilehash: 28a9b3d68037aac0c1198da4232c045487b01174
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "30838227"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-upgrades"></a>Automatycznych uaktualnień systemu operacyjnego zestawu skalowania maszyny wirtualnej platformy Azure

Automatyczne uaktualnienia obrazu systemu operacyjnego jest funkcją podglądu dla zestawy skalowania maszyny wirtualnej platformy Azure, która automatycznie uaktualnia wszystkich maszyn wirtualnych do najnowszej obrazu systemu operacyjnego.

Automatyczne uaktualnienie systemu operacyjnego ma następującą charakterystykę:

- Po skonfigurowaniu najnowsze obrazu systemu operacyjnego opublikowane przez wydawców obrazu jest automatycznie stosowana do skalowania, bez interwencji użytkownika.
- Uaktualnia partie wystąpień w sposób stopniowego zawsze, gdy nowego obrazu platformy jest opublikowana przez wydawcę.
- Integruje się z sondy kondycji aplikacji (opcjonalne, lecz zdecydowanie zalecane dla bezpieczeństwa).
- Działa w przypadku wszystkich rozmiarów maszyn wirtualnych.
- Obrazy platformy działa dla systemu Windows i Linux.
- Można zrezygnować z automatycznych uaktualnień, w dowolnym momencie (uaktualnień systemu operacyjnego może być inicjowana ręcznie również).
- Dysk systemu operacyjnego maszyny wirtualnej jest zastępowany nowy dysk systemu operacyjnego utworzonych za pomocą najnowszej wersji obrazu. Skonfigurowanych rozszerzeń i skrypty niestandardowe dane są uruchamiane podczas utrwalonych danych, które dyski są zachowywane.


## <a name="preview-notes"></a>Informacje o wersji zapoznawczej 
W wersji zapoznawczej, stosuje się następujące ograniczenia i ograniczenia:

- Automatyczne OS uaktualnia obsługują tylko [cztery jednostki magazynowe systemu operacyjnego](#supported-os-images). Brak umowy dotyczącej poziomu usług i gwarancji. Firma Microsoft zaleca się, że nie używasz automatycznych uaktualnień na krytycznych obciążeń produkcyjnych wersji zapoznawczej.
- Szyfrowanie dysków Azure (obecnie w wersji zapoznawczej) jest **nie** można obecnie używać z maszyny wirtualnej skali zestaw automatycznego uaktualnienia systemu operacyjnego.


## <a name="register-to-use-automatic-os-upgrade"></a>Zarejestruj, aby użyć automatyczne uaktualnienie systemu operacyjnego
Aby użyć funkcja automatycznego uaktualniania systemu operacyjnego, zarejestruj dostawcę w wersji zapoznawczej z [AzureRmProviderFeature rejestru](/powershell/module/azurerm.resources/register-azurermproviderfeature) w następujący sposób:

```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName AutoOSUpgradePreview
```

Trwa około 10 minut, aż stan rejestracji raportu, ponieważ *zarejestrowanej*. Można sprawdzić bieżącego stanu rejestracji z [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Po zarejestrowaniu, upewnij się, że *Microsoft.Compute* dostawca został zarejestrowany z [AzureRmResourceProvider rejestru](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) w następujący sposób:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

> [!NOTE]
> Klastrów sieci szkieletowej usług mają swoje własne pojęcie kondycji aplikacji, ale zestawy skalowania bez sieci szkieletowej usług używać sondy kondycji modułu równoważenia obciążenia do monitorowania kondycji aplikacji. Aby zarejestrować funkcję dostawcy dla sondy kondycji, należy użyć [AzureRmProviderFeature rejestru](/powershell/module/azurerm.resources/register-azurermproviderfeature) w następujący sposób:
>
> ```powershell
> Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVmssHealthProbe
> ```
>
> Ponownie, do raportu, ponieważ trwa około 10 minut, aż stan rejestracji *zarejestrowanej*. Można sprawdzić bieżącego stanu rejestracji z [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Raz zarejestrowany upewnij się, że *Microsoft.Network* dostawca został zarejestrowany z [AzureRmResourceProvider rejestru](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) w następujący sposób:
>
> ```powershell
> Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
> ```

## <a name="portal-experience"></a>Środowisko portalu
Po wykonaniu powyższych kroków rejestracji można przejść do [portalu Azure](https://aka.ms/managed-compute) można włączyć automatycznych uaktualnień systemu operacyjnego z zestawów skali i wyświetlany jest postęp uaktualnienia:

![](./media/virtual-machine-scale-sets-automatic-upgrade/automatic-upgrade-portal.png)


## <a name="supported-os-images"></a>Obsługiwane obrazów systemu operacyjnego
Obecnie obsługiwane są tylko niektóre obrazy platformy systemu operacyjnego. Obecnie nie można używać niestandardowych obrazów mają utworzonych przez użytkownika. *Wersji* musi mieć ustawioną właściwość obrazu platformy *najnowsze*.

Obecnie obsługiwane są następujące wersje produktu (więcej zostanie dodany):
    
| Wydawca               | Oferta         |  SKU               | Wersja  |
|-------------------------|---------------|--------------------|----------|
| Canonical               | UbuntuServer  | 16.04-LTS          | najnowsza   |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter | najnowsza   |
| MicrosoftWindowsServer  | WindowsServer | Centrum danych 2016    | najnowsza   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Smalldisk | najnowsza   |



## <a name="application-health-without-service-fabric"></a>Kondycja aplikacji bez sieci szkieletowej usług
> [!NOTE]
> Ta sekcja dotyczy tylko zestawy skalowania bez sieci szkieletowej usług. Sieć szkieletowa usług ma własną pojęcie kondycji aplikacji. Podczas korzystania z automatycznych uaktualnień systemu operacyjnego z sieci szkieletowej usług, jest wprowadzanie nowego obrazu systemu operacyjnego domeny aktualizacji przez aktualizację domeny do obsługi wysokiej dostępności usługi działające w sieci szkieletowej usług. Aby uzyskać więcej informacji na temat właściwości trwałości klastrów sieci szkieletowej usług, zobacz [tej dokumentacji](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

Podczas uaktualniania systemu operacyjnego są uaktualniane wystąpień maszyny Wirtualnej w zestawie skalowania jedno zadanie wsadowe w czasie. Uaktualnianie powinno być kontynuowane tylko jeśli aplikacja klienta jest w dobrej kondycji na uaktualnionym wystąpień maszyn wirtualnych. Z tego powodu wymagamy aplikację sygnały kondycji do aparatu uaktualnienia systemu operacyjnego zestaw skali. Podczas uaktualniania systemu operacyjnego platformy uwzględnia stan zasilania maszyny Wirtualnej i rozszerzenie obsługi stanu w celu ustalenia, czy wystąpienie maszyny Wirtualnej jest w dobrej kondycji po uaktualnieniu. Podczas uaktualniania systemu operacyjnego wystąpienia maszyny Wirtualnej dysk systemu operacyjnego w wystąpieniu maszyny Wirtualnej jest zastępowany nowy dysk, na podstawie najnowszej wersji obrazu. Po zakończeniu uaktualnienia systemu operacyjnego skonfigurowanych rozszerzeń są uruchamiane na tych maszynach wirtualnych. Tylko wtedy, gdy wszystkie rozszerzenia na maszynie Wirtualnej są pomyślnie zainicjowano obsługę administracyjną, aplikacja uważa dobrej kondycji. 

Ponadto zestaw skali *musi* można skonfigurować za pomocą aplikacji sondy kondycji zapewnienie platformy dokładnych informacji o bieżących stan aplikacji. Sondy kondycji aplikacji są niestandardowe obciążenia równoważenia sondy używany jako sygnał kondycji. Aplikacja była uruchomiona na wystąpieniu maszyny Wirtualnej zestawu skali może odpowiadać na zewnętrzne żądania HTTP lub TCP wskazującą, czy jest w dobrej kondycji. Aby uzyskać więcej informacji na temat działania niestandardowe załadować sondy modułu równoważenia, zobacz Aby [sondy modułu równoważenia obciążenia omówienie](../load-balancer/load-balancer-custom-probe-overview.md).

Jeśli zestaw skalowania jest skonfigurowany do używania wielu grup umieszczania, sond, za pomocą [standardowego modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) będzie używane.

### <a name="important-keep-credentials-up-to-date"></a>Ważne: Aktualne poświadczenia
Jeśli zestaw skalowania używa wszystkie poświadczenia dostępu do zasobów zewnętrznych, na przykład jeśli skonfigurowano rozszerzenie maszyny Wirtualnej, który używa tokenu sygnatury dostępu Współdzielonego dla konta magazynu, konieczne będzie upewnij się, że poświadczenia są zawsze aktualne. Jeśli wygasły wszystkie poświadczenia, tym certyfikaty i tokenów, uaktualnienie zakończy się niepowodzeniem, a pierwsza partia maszyn wirtualnych zostanie pozostawiony w stanie niepowodzenia.

Zalecane kroki, aby odzyskać maszyn wirtualnych i ponowne włączenie automatycznego uaktualniania systemu operacyjnego w przypadku niepowodzenia uwierzytelniania zasobów są:

* Token (lub innych poświadczeń) przekazany do rozszerzenia Twojej regenerate.
* Upewnij się, że wszystkie poświadczenia użyte z wewnątrz maszyny Wirtualnej do komunikowania się do jednostek zewnętrznych jest aktualny.
* Wszystkie nowe tokeny zaktualizować rozszerzenia w modelu zestawu skali.
* Wdrażanie zestawu skali zaktualizowane, który zaktualizuje wszystkie wystąpienia maszyny Wirtualnej, w tym te nie powiodło się. 

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurowanie sondy modułu równoważenia obciążenia niestandardowe jako sondy kondycji aplikacji w skali ustawić
Możesz *musi* jawnie Utwórz sondę modułu równoważenia obciążenia dla zestawu skalowania kondycji. Tego samego punktu końcowego dla istniejących badanie HTTP lub TCP sondowania mogą być używane, ale sondy kondycji mogą wymagać różnych zachowania sondowania tradycyjnego równoważenia obciążenia. Na przykład sondę modułu równoważenia obciążenia tradycyjnych może zwrócić zła, jeśli obciążenie wystąpienia jest zbyt wysoka, które mogą nie być odpowiednie dla ustalanie kondycji wystąpienia podczas automatycznego uaktualniania systemu operacyjnego. Skonfiguruj sondowania mają wysokie tempo sondowania niespełna dwie minuty.

Sondę modułu równoważenia obciążenia może być przywoływany w *networkProfile* skali ustawiona i można skojarzyć z obu wewnętrznego lub publicznego — moduł równoważenia obciążenia w następujący sposób:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```


## <a name="enforce-an-os-image-upgrade-policy-across-your-subscription"></a>Wymuszanie zasad uaktualniania obrazu systemu operacyjnego w Twojej subskrypcji
Bezpieczne uaktualnień jest zalecane do wymuszania zasad uaktualniania. Ta zasada może wymagać badania kondycji aplikacji w Twojej subskrypcji. Następujące zasady usługi Azure Resource Manager odrzuca wdrożeń, które nie mają skonfigurowane ustawienia uaktualniania automatycznych obrazu systemu operacyjnego:

1. Uzyskaj wbudowanej definicji zasad usługi Azure Resource Manager z [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) w następujący sposób:

    ```powershell
    $policyDefinition = Get-AzureRmPolicyDefinition -Id "/providers/Microsoft.Authorization/policyDefinitions/465f0161-0087-490a-9ad9-ad6217f4f43a"
    ```

2. Przypisz zasady do subskrypcji z [AzureRmPolicyAssignment nowy](/powershell/module/AzureRM.Resources/New-AzureRmPolicyAssignment) w następujący sposób:

    ```powershell
    New-AzureRmPolicyAssignment `
        -Name "Enforce automatic OS upgrades with app health checks" `
        -Scope "/subscriptions/<SubscriptionId>" `
        -PolicyDefinition $policyDefinition
    ```


## <a name="configure-auto-updates"></a>Skonfiguruj aktualizacje automatyczne
Aby skonfigurować automatycznych uaktualnień, upewnij się, że *automaticOSUpgrade* właściwość jest ustawiona na *true* w zestawie skalowania definicji modelu. Właściwość tę można skonfigurować z programu Azure PowerShell lub 2.0 interfejsu wiersza polecenia platformy Azure.

W poniższym przykładzie użyto programu Azure PowerShell (4.4.1 lub nowszym) do konfigurowania automatycznych uaktualnień dla zestaw o nazwie skalowania *myVMSS* w grupie zasobów o nazwie *myResourceGroup*:

```powershell
$rgname = myResourceGroup
$vmssname = myVMSS
$vmss = Get-AzureRmVMss -ResourceGroupName $rgname -VmScaleSetName $vmssname
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true
Update-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname -VirtualMachineScaleSet $vmss
```


W poniższym przykładzie użyto wiersza polecenia platformy Azure (2.0.20 lub nowszym) do konfigurowania automatycznych uaktualnień dla zestaw o nazwie skalowania *myVMSS* w grupie zasobów o nazwie *myResourceGroup*:

```azurecli
rgname="myResourceGroup"
vmssname="myVMSS"
az vmss update --name $vmssname --resource-group $rgname --set upgradePolicy.AutomaticOSUpgrade=true
```


## <a name="check-the-status-of-an-automatic-os-upgrade"></a>Sprawdź stan automatycznego uaktualniania systemu operacyjnego
Można sprawdzić stan ostatniej uaktualnienia systemu operacyjnego wykonać na na skalę ustawiony za pomocą programu PowerShell systemu Azure, Azure CLI 2.0 lub interfejsów API REST.

### <a name="azure-powershell"></a>Azure PowerShell
Na poniższym przykładzie używany program Azure PowerShell (4.4.1 lub nowszym) do sprawdzania stanu dla zestaw o nazwie skalowania *myVMSS* w grupie zasobów o nazwie *myResourceGroup*:

```powershell
Get-AzureRmVmssRollingUpgrade -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
W poniższym przykładzie użyto wiersza polecenia platformy Azure (2.0.20 lub nowszym) do sprawdzania stanu dla zestaw o nazwie skalowania *myVMSS* w grupie zasobów o nazwie *myResourceGroup*:

```azurecli
az vmss rolling-upgrade get-latest --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>Interfejs API REST
W poniższym przykładzie użyto interfejsu API REST, aby sprawdzić stan dla zestaw o nazwie skalowania *myVMSS* w grupie zasobów o nazwie *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/rollingUpgrades/latest?api-version=2017-03-30`
```

Wywołanie GET zwraca właściwości podobnie jak w poniższym przykładzie danych wyjściowych:

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
Aby rozszerzyć badania kondycji aplikacji, uaktualnień systemu operacyjnego zestaw skalowania wykonaj następujące kroki:

1. Jeśli więcej niż 20% wystąpień jest w złej kondycji, Zatrzymaj uaktualniania. w przeciwnym razie Kontynuuj.
2. Zidentyfikuj następną partię wystąpień maszyn wirtualnych o uaktualnienie i partii o 20% maksymalna liczba całkowita liczba wystąpień.
3. Uaktualnienie systemu operacyjnego następną partię wystąpień maszyn wirtualnych.
4. Jeśli więcej niż 20% uaktualnionego wystąpienia jest w złej kondycji, Zatrzymaj uaktualniania. w przeciwnym razie Kontynuuj.
5. Zestawy skalowania, które nie są częścią klastra usługi sieć szkieletowa uaktualnienia czeka sond stała się dobra do 5 minut, a następnie kontynuuje natychmiast na następną partię. Zestawy skalowania, które są częścią klastra usługi sieć szkieletowa skali ustawić czeka 30 minut przed przejściem do następnej partii.
6. Jeśli pozostają wystąpień do uaktualnienia, przejdź do kroku 1) dla następnej partii; w przeciwnym razie uaktualnianie jest pełny.

Aparat uaktualnienia systemu operacyjnego sprawdza, czy ogólną kondycję wystąpienie maszyny Wirtualnej przed uaktualnieniem każdej partii zestawu skalowania. Podczas uaktualniania partii, może być inne równoczesne planowana lub nieplanowana konserwacja wykonywane w centrach danych platformy Azure, które mogą mieć wpływ na dostępność maszyn wirtualnych. W związku z tym jest to możliwe, że tymczasowo ponad 20% wystąpień może nie działać. W takich przypadkach na końcu bieżącej partii skali tabulatory uaktualnienia.


## <a name="deploy-with-a-template"></a>Wdrażanie przy użyciu szablonu

Następujący szablon służy do wdrażania zestawu skalowania, która korzysta z automatycznych uaktualnień <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>automatycznych uaktualnień stopniowych - Ubuntu 16.04-LTS</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>


## <a name="next-steps"></a>Kolejne kroki
Więcej przykładów dotyczących automatycznych uaktualnień systemu operacyjnego za pomocą zestawów skali, zobacz [repozytorium GitHub dla funkcji w wersji zapoznawczej](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
