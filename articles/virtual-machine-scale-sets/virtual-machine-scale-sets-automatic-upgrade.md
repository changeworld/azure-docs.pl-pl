---
title: Zestawy skalowania automatycznego uaktualniania obrazu systemu operacyjnego za pomocą maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak automatycznie uaktualnić obrazu systemu operacyjnego na wystąpieniach maszyn wirtualnych w zestawie skalowania
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
ms.date: 02/25/2019
ms.author: manayar
ms.openlocfilehash: 007f2801efed8da4964808056563418dec7f64d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60328820"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Automatyczne uaktualnienia obrazu systemu operacyjnego zestawu skalowania maszyn wirtualnych platformy Azure

Włączanie automatycznego obrazu systemu operacyjnego uaktualnia na zarządzania aktualizacji łatwość pomaga zestawu skalowania, bezpieczne i automatycznie uaktualniając dysku systemu operacyjnego dla wszystkich wystąpień w zestawie skalowania.

Automatyczne uaktualnianie systemu operacyjnego ma następującą charakterystykę:

- Po skonfigurowaniu najnowszego obrazu systemu operacyjnego opublikowane przez wydawców obrazów jest automatycznie stosowany do skalowania, ustawić bez interwencji użytkownika.
- Uaktualnia partie wystąpień w sposób stopniowego każdorazowo, gdy nowy obraz platformy jest opublikowana przez wydawcę.
- Integruje się z sond kondycji aplikacji i [kondycji aplikacji rozszerzenia](virtual-machine-scale-sets-health-extension.md).
- Działa dla wszystkich rozmiarów maszyn wirtualnych i systemów Windows i Linux obrazów platformy.
- Można zrezygnować z automatycznych uaktualnień w dowolnym momencie (uaktualniania systemu operacyjnego może być inicjowana ręcznie również).
- Dysk systemu operacyjnego maszyny wirtualnej jest zastępowany nowy dysk systemu operacyjnego, utworzony za pomocą najnowszej wersji obrazu. Rozszerzenia skonfigurowany i skrypty niestandardowe dane są uruchamiane podczas utrwalonych danych, które dyski zostaną zachowane.
- [Sekwencjonowania rozszerzeń](virtual-machine-scale-sets-extension-sequencing.md) jest obsługiwana.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Jak działa automatyczne systemu operacyjnego obrazu uaktualnienia pracy?

Uaktualnienie działa przez zamianę dysku systemu operacyjnego maszyny wirtualnej na nowy dysk utworzony przy użyciu najnowszej wersji obrazu. Wszystkie rozszerzenia skonfigurowany i skrypty niestandardowe dane są uruchamiane na dysku systemu operacyjnego, podczas utrwalonych danych, które dyski zostaną zachowane. Aby zminimalizować czas przestoju aplikacji, uaktualnienia miejsce z nie więcej niż 20% zestawu uaktualnienia w dowolnej chwili skalowania partiami. Można również zintegrować sondę kondycji aplikacji usługi Azure Load Balancer lub [kondycji aplikacji rozszerzenia](virtual-machine-scale-sets-health-extension.md). Firma Microsoft zaleca się włączenie pulsu aplikacji i sprawdź poprawność uaktualnianie powiodło się dla każdej partii w procesie uaktualniania.

Proces uaktualniania przebiega następująco:
1. Przed rozpoczęciem procesu uaktualniania, koordynatora będzie upewnij się, że nie więcej niż 20% wystąpień w zestawie skalowania całej złej kondycji (z jakiegokolwiek powodu).
2. Uaktualnianie programu orchestrator identyfikuje partii wystąpień maszyn wirtualnych do uaktualnienia, przy użyciu dowolnej serii o maksymalnej wysokości 20% przychodów liczba całkowita wystąpień.
3. Dysk systemu operacyjnego zaznaczoną partię wystąpień maszyn wirtualnych jest zastępowany odciskiem nowego dysku systemu operacyjnego utworzonego na podstawie najnowszego obrazu i wszystkich określonych rozszerzeń i konfiguracje w modelu zestawu skalowania, które są stosowane do uaktualnionego wystąpienia.
4. Uaktualnienia dla zestawów skalowania za pomocą sondy kondycji skonfigurowanej aplikacji lub rozszerzenia kondycji aplikacji, czeka, do 5 minut, zanim wystąpienia poprawi, przed przejściem do następnej partii uaktualniania.
5. Uaktualnianie programu orchestrator pozwala również śledzić wartości procentowej wystąpień, które stają się wpis w złej kondycji uaktualnienie. Uaktualnienie zostanie zatrzymane, jeśli więcej niż 20% uaktualnionego wystąpienia stają się zła w procesie uaktualnienia.
6. Powyżej proces jest kontynuowany, dopóki nie zostały uaktualnione wszystkich wystąpień w zestawie skalowania.

Zestaw skalowania systemu operacyjnego sprawdzanie uaktualniania programu orchestrator dotyczące ogólnej kondycji zestawu skalowania przed rozpoczęciem uaktualniania każdej partii. Podczas uaktualniania usługi batch, może być inne działania współbieżnych planowaną lub nieplanowaną konserwacją, które mogłyby wpływać na kondycję wystąpieniami danego zestawu skalowania. W takich przypadkach jeśli więcej niż 20% wystąpień zestawu skalowania, stanie się zła, następnie zestawu skalowania zatrzymuje uaktualniania na koniec bieżącej partii.

## <a name="supported-os-images"></a>Obsługiwane obrazy systemu operacyjnego
Obecnie obsługiwane są tylko niektóre obrazy platformy systemu operacyjnego. Obrazy niestandardowe nie są obecnie obsługiwane.

Obecnie obsługiwane są następujące jednostki SKU (i więcej co jakiś czas są dodawane):

| Wydawca               | Oferty systemu operacyjnego      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| Rogue Wave (OpenLogic)  | CentOS        | 7.5                |
| CoreOS                  | CoreOS        | Stable             |
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016 centrum danych za pomocą kontenerów |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2019 centrum danych za pomocą kontenerów |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Wymagania dotyczące konfigurowania automatycznego uaktualnienia obrazu systemu operacyjnego

- *Wersji* właściwości obrazu platformy musi być równa *najnowsze*.
- Użyj sond kondycji aplikacji lub [kondycji aplikacji rozszerzenia](virtual-machine-scale-sets-health-extension.md) skalowanie — Service Fabric Ustawia.
- Upewnij się, że określone w modelu zestawu skalowania zasobów zewnętrznych dostępnych i zaktualizowane. Przykłady obejmują identyfikatora URI połączenia SAS do uruchomienia ładunku we właściwościach rozszerzenia maszyny Wirtualnej, ładunek na koncie magazynu, odwołania do kluczy tajnych w modelu i nie tylko.

## <a name="configure-automatic-os-image-upgrade"></a>Konfigurowanie automatycznego uaktualnienia obrazu systemu operacyjnego
Aby skonfigurować automatyczne uaktualnienia obrazu systemu operacyjnego, upewnij się, że *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* właściwość jest ustawiona na *true* w zestawu skalowania definicję modelu.

### <a name="rest-api"></a>Interfejs API REST
Na poniższym przykładzie opisano, jak skonfigurować automatyczne uaktualnienia systemu operacyjnego w modelu zestawu skalowania:

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

### <a name="azure-powershell"></a>Azure PowerShell
Użyj [AzVmss aktualizacji](/powershell/module/az.compute/update-azvmss) polecenia cmdlet można znaleźć w historii uaktualnienia systemu operacyjnego dla zestawu skalowania. Poniższy przykład umożliwia skonfigurowanie automatycznych uaktualnień dla zestawu skalowania o nazwie *myVMSS* w grupie zasobów o nazwie *myResourceGroup*:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
Użyj [aktualizacji az vmss](/cli/azure/vmss#az-vmss-update) można znaleźć w historii uaktualnienia systemu operacyjnego dla zestawu skalowania. Interfejs wiersza polecenia platformy Azure 2.0.47 lub nowszej. Poniższy przykład umożliwia skonfigurowanie automatycznych uaktualnień dla zestawu skalowania o nazwie *myVMSS* w grupie zasobów o nazwie *myResourceGroup*:

```azurecli-interactive
az vmss update --name myVMSS --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

## <a name="using-application-health-probes"></a>Za pomocą kondycji aplikacji sondy

Podczas uaktualniania systemu operacyjnego wystąpień maszyn wirtualnych w zestawie skalowania są uaktualniane jedną partię w danym momencie. Uaktualnianie powinno być kontynuowane tylko jeśli aplikacja klienta jest w dobrej kondycji na uaktualnionym wystąpieniach maszyn wirtualnych. Zaleca się, że aplikacja udostępnia sygnałów kondycji z aparatem uaktualnienia systemu operacyjnego zestawu skalowania. Domyślnie podczas uaktualniania systemu operacyjnego platformy uwzględnia stan zasilania maszyny Wirtualnej i stanu, aby określić, jeśli wystąpienie maszyny Wirtualnej jest w dobrej kondycji po uaktualnieniu aprowizacji rozszerzenia. Podczas uaktualniania systemu operacyjnego wystąpienia maszyny Wirtualnej dysk systemu operacyjnego na wystąpieniu maszyny Wirtualnej jest zastępowany nowy dysk, na podstawie najnowszej wersji obrazu. Po zakończeniu uaktualniania systemu operacyjnego skonfigurowany rozszerzenia są uruchamiane na tych maszynach wirtualnych. Aplikacja jest uznawana za dobrej kondycji, tylko wtedy, gdy wszystkie rozszerzenia w wystąpieniu są pomyślnie aprowizowane.

Opcjonalnie można skonfigurować zestaw skalowania za pomocą sondy kondycji aplikacji pozwala udostępnić platformę dokładne informacje o trwających stanu aplikacji. Sondy kondycji aplikacji są niestandardowe obciążenia równoważenia sondy używany jako sygnał kondycji. Aplikacja była uruchomiona na wystąpieniu maszyny Wirtualnej zestawu skalowania mogą odpowiadać na zewnętrzne żądania HTTP lub TCP, wskazującą, czy jest w dobrej kondycji. Aby uzyskać więcej informacji na temat sposobu działania niestandardowego obciążenia sondy modułu równoważenia zobacz Aby [sondy modułu równoważenia obciążenia omówienie](../load-balancer/load-balancer-custom-probe-overview.md). Sonda kondycji aplikacji nie jest wymagana dla zestawów skalowania usługi Service Fabric, ale jest zalecane. Zestawy skalowania bez usługi Service Fabric wymagają sondy kondycji aplikacji albo modułu równoważenia obciążenia lub [kondycji aplikacji rozszerzenia](virtual-machine-scale-sets-health-extension.md).

Jeśli zestaw skalowania jest skonfigurowany do używania wielu grup umieszczania, sondy, za pomocą [Balancer w warstwie standardowa](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) trzeba było używać.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurowanie sondy modułu równoważenia obciążenia niestandardowe jako sonda kondycji aplikacji w skali zestawu
Najlepszym rozwiązaniem należy jawnie utworzyć sondy modułu równoważenia obciążenia, dla kondycji zestawu skalowania. Można użyć tego samego punktu końcowego dla istniejących sondy HTTP lub sonda TCP, ale sondę kondycji może wymagać różne zachowanie z sondy modułu równoważenia obciążenia tradycyjnych. Na przykład sondy modułu równoważenia obciążenia tradycyjnych może zwrócić złej kondycji, jeśli obciążenie na wystąpieniu jest zbyt duża, ale który nie jest właściwe ustalania kondycji wystąpień podczas automatycznego uaktualniania systemu operacyjnego. Konfigurowanie sondy użycia mają wysoki wskaźnik sondowania wynosi mniej niż dwie minuty.

Sondy modułu równoważenia obciążenia może być przywoływany w *elementy networkProfile* skali zestawu i może być skojarzony z obu wewnętrznego lub publicznego — moduł równoważenia obciążenia w następujący sposób:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
}
```

> [!NOTE]
> Korzystając z automatycznych uaktualnień systemu operacyjnego za pomocą usługi Service Fabric, nowy obraz systemu operacyjnego jest udostępniona domena aktualizacji według domeny aktualizacji, aby zapewnić wysoką dostępność usługi działające w usłudze Service Fabric. Korzystanie z automatycznego uaktualniania systemu operacyjnego w usłudze Service Fabric klaster musi być skonfigurowana pod kątem użycia warstwy trwałości Silver lub nowszej. Aby uzyskać więcej informacji na temat właściwości niezawodność klastrów usługi Service Fabric, zobacz [tej dokumentacji](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Aktualizowanie poświadczeń
Jeśli zestaw skalowania korzysta z żadnych poświadczeń dostępu do zasobów zewnętrznych, na przykład jeśli skonfigurowano rozszerzenie maszyny Wirtualnej, która używa tokenu sygnatury dostępu Współdzielonego dla konta magazynu, upewnij się, że poświadczenia są aktualizowane. Jeśli wszystkie poświadczenia, w tym certyfikaty i tokenów, ważność wygasła, uaktualnienie nie powiedzie i pierwszej partii maszyn wirtualnych zostanie pozostawiony w stanie niepowodzenia.

Są zalecane kroki, aby odzyskać maszyn wirtualnych, a następnie ponownie Włącz automatyczne uaktualnianie systemu operacyjnego w przypadku niepowodzenia uwierzytelniania zasobów:

* Ponowne generowanie tokenu (lub innych poświadczeń) przekazany do Twojego rozszerzenia.
* Upewnij się, że dowolnych poświadczeń, używane z wewnątrz maszyny Wirtualnej na komunikowanie się z podmiotów zewnętrznych jest aktualny.
* Zaktualizuj rozszerzenia w modelu zestawu skalowania przy użyciu dowolnej nowych tokenów.
* Wdrażanie zestawu skalowania zaktualizowane, który zaktualizuje wszystkie wystąpienia maszyn wirtualnych, w tym te, które nie powiodło się.

## <a name="using-application-health-extension"></a>Przy użyciu rozszerzenia kondycji aplikacji
Rozszerzenie kondycji aplikacji jest wdrażany w wystąpieniu zestawu skalowania maszyny wirtualnej i raportów o kondycji maszyny Wirtualnej z wewnątrz wystąpienia w zestawie skalowania. Można skonfigurować rozszerzenia sondowania w punkcie końcowym usługi aplikacji i aktualizuje stan aplikacji, w tym wystąpieniu. Ten stan wystąpienia jest sprawdzany przez platformę Azure, aby ustalić, czy wystąpienie kwalifikują się do operacji uaktualnienia.

Jako rozszerzenie raporty o kondycji z na maszynie wirtualnej, rozszerzenia mogą być używane w sytuacjach, w której zewnętrzny sondy przykład sondy kondycji aplikacji (które korzystanie z niestandardowych usługi Azure Load Balancer [sondy](../load-balancer/load-balancer-custom-probe-overview.md)) nie może być używane.

Istnieje wiele sposobów wdrażania kondycji aplikacji, Ustawia rozszerzenie do skalowania, zgodnie z opisem w przykładach w [w tym artykule](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension).

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Pobieranie historii automatyczne uaktualnienia obrazu systemu operacyjnego
Możesz sprawdzić historię najnowsze uaktualnienie systemu operacyjnego, wykonywane na zestawie skalowania jest ustawiana za pomocą programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub interfejsów API REST. Możesz też uzyskać historii ostatnich pięciu próby uaktualnienia systemu operacyjnego, w ciągu ostatnich dwóch miesięcy.

### <a name="rest-api"></a>Interfejs API REST
W poniższym przykładzie użyto [interfejsu API REST](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) można sprawdzić stanu dla zestawu skalowania o nazwie *myVMSS* w grupie zasobów o nazwie *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```

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

### <a name="azure-powershell"></a>Azure PowerShell
Użyj [Get AzVmss](/powershell/module/az.compute/get-azvmss) polecenia cmdlet można znaleźć w historii uaktualnienia systemu operacyjnego dla zestawu skalowania. W poniższym przykładzie opisano, jak Przejrzyj stan uaktualnienia systemu operacyjnego dla zestawu skalowania o nazwie *myVMSS* w grupie zasobów o nazwie *myResourceGroup*:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
Użyj [az vmss get-systemu operacyjnego — — Historia uaktualniania](/cli/azure/vmss#az-vmss-get-os-upgrade-history) można znaleźć w historii uaktualnienia systemu operacyjnego dla zestawu skalowania. Interfejs wiersza polecenia platformy Azure 2.0.47 lub nowszej. W poniższym przykładzie opisano, jak Przejrzyj stan uaktualnienia systemu operacyjnego dla zestawu skalowania o nazwie *myVMSS* w grupie zasobów o nazwie *myResourceGroup*:

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myVMSS
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Jak uzyskać najnowszą wersję obrazu platformy systemu operacyjnego?

Można uzyskać wersji obrazu dla systemu operacyjnego automatycznego uaktualniania obsługiwane jednostki SKU, przy użyciu poniższych przykładów:

### <a name="rest-api"></a>Interfejs API REST
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="deploy-with-a-template"></a>Wdrażanie przy użyciu szablonu

Szablony umożliwia wdrożenie zestawu skalowania z automatycznych uaktualnień systemu operacyjnego dla obsługiwanych obrazów takich jak [Ubuntu 16.04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej przykładów na temat korzystania z automatycznego uaktualniania systemu operacyjnego za pomocą zestawów skalowania, przejrzyj [repozytorium GitHub](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
