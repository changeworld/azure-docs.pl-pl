---
title: Zestawy skalowania automatycznego uaktualniania obrazu systemu operacyjnego za pomocą maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak automatycznie uaktualnić obrazu systemu operacyjnego na wystąpieniach maszyn wirtualnych w zestawie skalowania
services: virtual-machine-scale-sets
documentationcenter: ''
author: rajsqr
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: rajraj
ms.openlocfilehash: c8ba9ac3150b5a84b2902afaaefcf78c76764fed
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036194"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Automatyczne uaktualnienia obrazu systemu operacyjnego zestawu skalowania maszyn wirtualnych platformy Azure

Automatyczne uaktualnienia obrazu systemu operacyjnego jest funkcją zestawów skalowania maszyn wirtualnych platformy Azure, która automatycznie uaktualnia wszystkich maszyn wirtualnych do najnowszego obrazu systemu operacyjnego.

Automatyczne uaktualnianie systemu operacyjnego ma następującą charakterystykę:

- Po skonfigurowaniu najnowszego obrazu systemu operacyjnego opublikowane przez wydawców obrazów jest automatycznie stosowany do skalowania, ustawić bez interwencji użytkownika.
- Uaktualnia partie wystąpień w sposób stopniowego każdorazowo, gdy nowy obraz platformy jest opublikowana przez wydawcę.
- Integruje się z sondy kondycji aplikacji.
- Działa dla wszystkich rozmiarów maszyn wirtualnych i systemów Windows i Linux obrazów platformy.
- Można zrezygnować z automatycznych uaktualnień w dowolnym momencie (uaktualniania systemu operacyjnego może być inicjowana ręcznie również).
- Dysk systemu operacyjnego maszyny wirtualnej jest zastępowany nowy dysk systemu operacyjnego, utworzony za pomocą najnowszej wersji obrazu. Rozszerzenia skonfigurowany i skrypty niestandardowe dane są uruchamiane podczas utrwalonych danych, które dyski zostaną zachowane.
- Usługa Azure disk encryption (w wersji zapoznawczej) nie jest obecnie obsługiwane.  

## <a name="how-does-automatic-os-image-upgrade-work"></a>Jak działa automatyczne systemu operacyjnego obrazu uaktualnienia pracy?

Uaktualnienie działa przez zastąpienie dysku systemu operacyjnego maszyny wirtualnej z nowym utworzone za pomocą najnowszej wersji obrazu. Dowolne skonfigurowane rozszerzeń i skrypty niestandardowe dane działają podczas utrwalonych danych, które dyski zostaną zachowane. Aby zminimalizować czas przestoju aplikacji, uaktualnienia mieć miejsce w partii maszyn wirtualnych z nie więcej niż 20% skalowania Ustaw uaktualnienia w dowolnej chwili. Istnieje również możliwość integracji sondę kondycji aplikacji usługi Azure Load Balancer. Zdecydowanie zaleca się dołączyć pulsu aplikacji, a następnie sprawdź uaktualnianie powiodło się dla każdej partii w procesie uaktualniania. Procedura wykonywania jest następująca: 

1. Przed rozpoczęciem procesu uaktualniania, koordynatora zapewni nie więcej niż 20% wystąpień jest w złej kondycji. 
2. Identyfikowanie wystąpień maszyn wirtualnych, aby uaktualnić z usługą batch, o maksymalnej wysokości 20% przychodów liczba całkowita wystąpień usługi batch.
3. Uaktualnienia obrazu systemu operacyjnego z tej partii wystąpień maszyn wirtualnych.
4. Jeśli klient skonfiguruje sond kondycji aplikacji, uaktualnienie czeka maksymalnie 5 minut, zanim sondy do poprawi, przed przejściem do następnej partii uaktualniania. 
5. Jeśli są pozostałe wystąpienia do uaktualnienia, przejdź do kroku 1) dla nowej partii; w przeciwnym razie uaktualnienie jest pełny.

Zestaw skalowania systemu operacyjnego sprawdzanie uaktualniania programu orchestrator dotyczące ogólnej kondycji wystąpień maszyny Wirtualnej przed uaktualnieniem każdej partii. Podczas uaktualniania usługi batch, może być innych jednoczesnych planowana lub nieplanowana konserwacja działań wykonywanych w centrach danych platformy Azure, które mogą mieć wpływ na dostępność maszyn wirtualnych z systemem. Dzięki temu jest możliwe, że tymczasowo więcej niż 20% wystąpień może nie działać. W takich przypadkach na koniec bieżącej partii zestawu skalowania zatrzymuje uaktualnienia.

## <a name="supported-os-images"></a>Obsługiwane obrazy systemu operacyjnego
Obecnie obsługiwane są tylko niektóre obrazy platformy systemu operacyjnego. Obecnie nie można używać obrazów niestandardowych, które zostały utworzone samodzielnie. 

Obecnie obsługiwane są następujące jednostki SKU (więcej rozwiązań zostanie dodanych w przyszłości):
    
| Wydawca               | Oferty systemu operacyjnego      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04 LTS *        | 
| Rogue Wave (OpenLogic)  | CentOS        | 7.5 *              | 
| CoreOS                  | CoreOS        | Stable             | 
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter | 
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    | 
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016 centrum danych za pomocą kontenerów |

* Obsługa tych obrazów obecnie wprowadza i będą dostępne we wszystkich regionach platformy Azure wkrótce. 

## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Wymagania dotyczące konfigurowania automatycznego uaktualnienia obrazu systemu operacyjnego

- *Wersji* właściwości obrazu platformy musi być równa *najnowsze*.
- Dla zestawów skalowania bez usługi Service Fabric, należy użyć sondy kondycji aplikacji.
- Upewnij się, że zasoby modelu zestawu skalowania odwołuje się do jest dostępny i zawsze aktualne. 
  Identyfikator URI Exa.SAS uruchamianie ładunku we właściwościach rozszerzenia maszyny Wirtualnej, ładunek na koncie magazynu, odwołania do kluczy tajnych w modelu. 

## <a name="configure-automatic-os-image-upgrade"></a>Konfigurowanie automatycznego uaktualnienia obrazu systemu operacyjnego
Aby skonfigurować automatyczne uaktualnienia obrazu systemu operacyjnego, upewnij się, że *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* właściwość jest ustawiona na *true* w zestawu skalowania definicję modelu. 

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2018-10-01`
```

```json
{ 
  "properties": { 
    "upgradePolicy": { 
      "automaticOSUpgradePolicy": { 
        "enableAutomaticOSUpgrade":  true 
      } 
    } 
  } 
} 
```

W poniższym przykładzie użyto interfejsu wiersza polecenia platformy Azure (2.0.47 lub nowszy) Aby skonfigurować automatyczne uaktualnienia dla zestawu skalowania o nazwie *myVMSS* w grupie zasobów o nazwie *myResourceGroup*:

```azurecli
az vmss update --name myVMSS --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```
Obsługa konfigurowania tej właściwości, za pomocą programu Azure PowerShell zostanie udostępniona wkrótce.

## <a name="using-application-health-probes"></a>Za pomocą kondycji aplikacji sondy 

Podczas uaktualniania systemu operacyjnego wystąpień maszyn wirtualnych w zestawie skalowania są uaktualniane jedną partię w danym momencie. Uaktualnianie powinno być kontynuowane tylko jeśli aplikacja klienta jest w dobrej kondycji na uaktualnionym wystąpieniach maszyn wirtualnych. Zaleca się, że aplikacja udostępnia sygnałów kondycji z aparatem uaktualnienia systemu operacyjnego zestawu skalowania. Domyślnie podczas uaktualniania systemu operacyjnego platformy uwzględnia stan zasilania maszyny Wirtualnej i stanu, aby określić, jeśli wystąpienie maszyny Wirtualnej jest w dobrej kondycji po uaktualnieniu aprowizacji rozszerzenia. Podczas uaktualniania systemu operacyjnego wystąpienia maszyny Wirtualnej dysk systemu operacyjnego na wystąpieniu maszyny Wirtualnej jest zastępowany nowy dysk, na podstawie najnowszej wersji obrazu. Po zakończeniu uaktualniania systemu operacyjnego skonfigurowany rozszerzenia są uruchamiane na tych maszynach wirtualnych. Tylko wtedy, gdy wszystkie rozszerzenia na maszynie Wirtualnej są pomyślnie zainicjowano obsługę administracyjną, aplikacji uznaje się dobrej kondycji. 

Opcjonalnie można skonfigurować zestaw skalowania za pomocą sondy kondycji aplikacji pozwala udostępnić platformę dokładne informacje o trwających stanu aplikacji. Sondy kondycji aplikacji są niestandardowe obciążenia równoważenia sondy używany jako sygnał kondycji. Aplikacja była uruchomiona na wystąpieniu maszyny Wirtualnej zestawu skalowania mogą odpowiadać na zewnętrzne żądania HTTP lub TCP, wskazującą, czy jest w dobrej kondycji. Aby uzyskać więcej informacji na temat sposobu działania niestandardowego obciążenia sondy modułu równoważenia zobacz Aby [sondy modułu równoważenia obciążenia omówienie](../load-balancer/load-balancer-custom-probe-overview.md). Sonda kondycji aplikacji nie jest wymagany dla automatycznego uaktualniania systemu operacyjnego, ale zdecydowanie zaleca się.

Jeśli zestaw skalowania jest skonfigurowany do używania wielu grup umieszczania, sondy, za pomocą [Balancer w warstwie standardowa](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) trzeba było używać.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurowanie sondy modułu równoważenia obciążenia niestandardowe jako sonda kondycji aplikacji w skali zestawu
Najlepszym rozwiązaniem należy jawnie utworzyć sondy modułu równoważenia obciążenia, dla kondycji zestawu skalowania. Może być używany ten sam punkt końcowy dla istniejących sondy HTTP lub sonda TCP, ale sondę kondycji, mogą wymagać różnych zachowanie z sondy modułu równoważenia obciążenia tradycyjnych. Na przykład sondy modułu równoważenia obciążenia tradycyjnych mogą zwracać w złej kondycji, jeśli obciążenie wystąpienia jest zbyt duże, które mogą nie być odpowiednie dla określania kondycji wystąpień podczas automatycznego uaktualniania systemu operacyjnego. Konfigurowanie sondy użycia mają wysoki wskaźnik sondowania wynosi mniej niż dwie minuty.

Sondy modułu równoważenia obciążenia może być przywoływany w *elementy networkProfile* skali zestawu i może być skojarzony z obu wewnętrznego lub publicznego — moduł równoważenia obciążenia w następujący sposób:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```
> [!NOTE]
> Korzystając z automatycznych uaktualnień systemu operacyjnego za pomocą usługi Service Fabric, nowy obraz systemu operacyjnego jest udostępniona domena aktualizacji według domeny aktualizacji, aby zapewnić wysoką dostępność usługi działające w usłudze Service Fabric. Korzystanie z automatycznego uaktualniania systemu operacyjnego w usłudze Service Fabric klaster musi być skonfigurowana pod kątem użycia warstwy trwałości Silver lub nowszej. Aby uzyskać więcej informacji na temat właściwości niezawodność klastrów usługi Service Fabric, zobacz [tej dokumentacji](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Aktualizowanie poświadczeń
Jeśli zestaw skalowania korzysta z żadnych poświadczeń dostępu do zasobów zewnętrznych, na przykład jeśli skonfigurowano rozszerzenie maszyny Wirtualnej, która używa tokenu sygnatury dostępu Współdzielonego dla konta magazynu, należy upewnić się, że poświadczenia są zawsze na bieżąco. Jeśli wszystkie poświadczenia, w tym certyfikaty i tokeny wygasły, uaktualnienie nie powiedzie, a pierwszej partii maszyn wirtualnych zostanie pozostawiony w stanie niepowodzenia.

Są zalecane kroki, aby odzyskać maszyn wirtualnych, a następnie ponownie Włącz automatyczne uaktualnianie systemu operacyjnego w przypadku niepowodzenia uwierzytelniania zasobów:

* Ponowne generowanie tokenu (lub innych poświadczeń) przekazany do Twojego rozszerzenia.
* Upewnij się, że dowolnych poświadczeń, używane z wewnątrz maszyny Wirtualnej na komunikowanie się z podmiotów zewnętrznych jest aktualny.
* Zaktualizuj rozszerzenia w modelu zestawu skalowania przy użyciu dowolnej nowych tokenów.
* Wdrażanie zestawu skalowania zaktualizowane, który zaktualizuje wszystkie wystąpienia maszyn wirtualnych, w tym te, które nie powiodło się. 

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Pobieranie historii automatyczne uaktualnienia obrazu systemu operacyjnego 
Możesz sprawdzić historię najnowsze uaktualnienie systemu operacyjnego, wykonywane na zestawie skalowania jest ustawiana za pomocą programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub interfejsów API REST. Możesz też uzyskać historii ostatnich pięciu próby uaktualnienia systemu operacyjnego, w ciągu ostatnich dwóch miesięcy.

### <a name="azure-powershell"></a>Azure PowerShell
Na poniższym przykładzie używany program Azure PowerShell, aby sprawdzić stan dla zestawu skalowania o nazwie *myVMSS* w grupie zasobów o nazwie *myResourceGroup*:

```powershell
Get-AzureRmVmss -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
W poniższym przykładzie użyto interfejsu wiersza polecenia platformy Azure (2.0.47 lub nowszy) aby sprawdzić stan dla zestawu skalowania o nazwie *myVMSS* w grupie zasobów o nazwie *myResourceGroup*:

```azurecli
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>Interfejs API REST
W poniższym przykładzie użyto interfejsu API REST w celu sprawdzenia stanu dla zestawu skalowania o nazwie *myVMSS* w grupie zasobów o nazwie *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```
Zapoznaj się z dokumentacją dotyczącą tego interfejsu API tutaj: https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getosupgradehistory.

Wywołanie GET zwraca właściwości podobne do następujących przykładowych danych wyjściowych:

```json
{
  "value": [
    {
      "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
} 
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Jak uzyskać najnowszą wersję obrazu platformy systemu operacyjnego? 

Można uzyskać wersji obrazu dla systemu operacyjnego automatycznego uaktualniania obsługiwane jednostki SKU, przy użyciu poniższych przykładów: 

```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

```powershell
Get-AzureRmVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

```azurecli
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="deploy-with-a-template"></a>Wdrażanie przy użyciu szablonu

Następujący szablon umożliwia wdrożenie zestawu skalowania, który korzysta z automatycznych uaktualnień <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>automatycznych uaktualnień stopniowych — Ubuntu 16.04-LTS</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej przykładów na temat korzystania z automatycznego uaktualniania systemu operacyjnego za pomocą zestawów skalowania, zobacz [repozytorium GitHub dla funkcji w wersji zapoznawczej](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
