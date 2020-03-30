---
title: Sterowanie konserwacją
description: Dowiedz się, jak kontrolować, kiedy konserwacja jest stosowana do maszyn wirtualnych platformy Azure przy użyciu kontroli konserwacji.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: cynthn
ms.openlocfilehash: 58c0964d170f49066802b955f09dab01eaf998a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250181"
---
# <a name="preview-control-updates-with-maintenance-control-and-the-azure-cli"></a>Wersja zapoznawcza: kontroluj aktualizacje za pomocą kontroli konserwacji i interfejsu wiersza polecenia platformy Azure

Zarządzanie aktualizacjami platformy, które nie wymagają ponownego uruchomienia, przy użyciu kontroli konserwacji. Platforma Azure często aktualizuje swoją infrastrukturę, aby zwiększyć niezawodność, wydajność, zabezpieczenia lub uruchomić nowe funkcje. Większość aktualizacji jest nieprzejrzysta dla użytkowników. Niektóre poufne obciążenia, takie jak gry, przesyłanie strumieniowe multimediów i transakcje finansowe, nie tolerują nawet kilku sekund zamrożenia lub odłączenia maszyny Wirtualnej w celu konserwacji. Kontrola konserwacji umożliwia oczekiwanie na aktualizacje platformy i zastosowanie ich w 35-dniowym oknie kroczącym. 

Kontrola konserwacji pozwala zdecydować, kiedy należy zastosować aktualizacje do izolowanych maszyn wirtualnych i hostów dedykowanych platformy Azure.

Dzięki kontroli konserwacji można:
- Aktualizacje wsadowe do jednego pakietu aktualizacji.
- Poczekaj do 35 dni, aby zastosować aktualizacje. 
- Zautomatyzuj aktualizacje platformy dla okna konserwacji przy użyciu usługi Azure Functions.
- Konfiguracje konserwacji działają między subskrypcjami i grupami zasobów. 

> [!IMPORTANT]
> Kontrola konserwacji jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="limitations"></a>Ograniczenia

- Maszyny wirtualne muszą znajdować się na [dedykowanym hoście](./linux/dedicated-hosts.md)lub być tworzone przy użyciu [izolowanego rozmiaru maszyny Wirtualnej.](./linux/isolation.md)
- Po 35 dniach aktualizacja zostanie automatycznie zastosowana.
- Użytkownik musi mieć dostęp **współautora zasobów.**


## <a name="install-the-maintenance-extension"></a>Zainstaluj rozszerzenie konserwacji

Jeśli zdecydujesz się zainstalować [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) lokalnie, potrzebujesz wersji 2.0.76 lub nowszej.

Zainstaluj `maintenance` rozszerzenie interfejsu wiersza polecenia podglądu lokalnie lub w aplikacji Cloud Shell. 

```azurecli-interactive
az extension add -n maintenance
```


## <a name="create-a-maintenance-configuration"></a>Tworzenie konfiguracji konserwacji

Służy `az maintenance configuration create` do tworzenia konfiguracji konserwacji. W tym przykładzie tworzy konfigurację konserwacji o nazwie *myConfig* o zakresie do hosta. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --name myConfig \
   --maintenanceScope host\
   --location  eastus
```

Skopiuj identyfikator konfiguracji z danych wyjściowych, aby użyć go później.

Korzystanie `--maintenanceScope host` zapewnia, że config konserwacji jest używany do kontrolowania aktualizacji do hosta.

Jeśli spróbujesz utworzyć konfigurację o tej samej nazwie, ale w innej lokalizacji, pojawi się błąd. Nazwy konfiguracji muszą być unikatowe dla subskrypcji.

Za pomocą `az maintenance configuration list`programu .

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>Przypisywanie konfiguracji

Służy `az maintenance assignment create` do przypisywania konfiguracji do izolowanej maszyny Wirtualnej lub dedykowanego hosta platformy Azure.

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

Zastosuj konfigurację do maszyny Wirtualnej przy użyciu identyfikatora konfiguracji. Określ `--resource-type virtualMachines` i podaj nazwę `--resource-name`maszyny Wirtualnej dla , a `--resource-group`grupę zasobów dla maszyny `--location`Wirtualnej w , a lokalizacja maszyny Wirtualnej dla . 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig"
```

### <a name="dedicated-host"></a>Dedykowany host

Aby zastosować konfigurację do dedykowanego hosta, należy dołączyć `--resource-type hosts`, `--resource-parent-name` z `--resource-parent-type hostGroups`nazwą grupy hosta i . 

Parametr `--resource-id` jest identyfikatorem hosta. Aby uzyskać identyfikator dedykowanego hosta, można użyć [widoku get-instance hosta az vm.](/cli/azure/vm/host#az-vm-host-get-instance-view)

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>Sprawdź konfigurację

Można sprawdzić, czy konfiguracja została zastosowana poprawnie, lub sprawdzić, `az maintenance assignment list`jaka konfiguracja jest aktualnie stosowana za pomocą programu .

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>Dedykowany host 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>Sprawdzanie oczekujących aktualizacji

Użyj, `az maintenance update list` aby sprawdzić, czy są oczekujące aktualizacje. Aktualizacja --subscription jest identyfikatorem dla subskrypcji, która zawiera maszynę wirtualną.

Jeśli nie ma żadnych aktualizacji, polecenie zwróci komunikat o błędzie, który będzie zawierał tekst: `Resource not found...StatusCode: 404`.

Jeśli istnieją aktualizacje, tylko jeden zostanie zwrócony, nawet jeśli istnieje wiele aktualizacji oczekujących. Dane dla tej aktualizacji zostaną zwrócone w obiekcie:

```text
[
  {
    "impactDurationInSec": 9,
    "impactType": "Freeze",
    "maintenanceScope": "Host",
    "notBefore": "2020-03-03T07:23:04.905538+00:00",
    "resourceId": "/subscriptions/9120c5ff-e78e-4bd0-b29f-75c19cadd078/resourcegroups/DemoRG/providers/Microsoft.Compute/hostGroups/demoHostGroup/hosts/myHost",
    "status": "Pending"
  }
]
  ```

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

Sprawdź, czy oczekujące aktualizacje dla izolowanej maszyny Wirtualnej. W tym przykładzie dane wyjściowe są formatowane jako tabela dla czytelności.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Dedykowany host

Aby sprawdzić oczekujące aktualizacje dla dedykowanego hosta. W tym przykładzie dane wyjściowe są formatowane jako tabela dla czytelności. Zastąp wartości zasobów własnymi.

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>Stosowanie aktualizacji

Służy `az maintenance apply update` do stosowania oczekujących aktualizacji. Po sukcesie to polecenie zwróci JSON zawierające szczegóły aktualizacji.

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

Utwórz żądanie, aby zastosować aktualizacje do izolowanej maszyny Wirtualnej.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Dedykowany host

Zastosuj aktualizacje do dedykowanego hosta.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>Sprawdzanie stanu stosowania aktualizacji 

Można sprawdzić postęp aktualizacji za `az maintenance applyupdate get`pomocą . 

Można użyć `default` jako nazwy aktualizacji, aby zobaczyć wyniki `myUpdateName` ostatniej aktualizacji lub zastąpić nazwą aktualizacji, `az maintenance applyupdate create`która została zwrócona po uruchomieniu .

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime będzie czas, kiedy aktualizacja została zakończona, zainicjowane przez Ciebie lub przez platformę w przypadku, gdy okno samoobsługi nie był używany. Jeśli nigdy nie było aktualizacji stosowane za pomocą kontroli konserwacji pokaże wartość domyślną.

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name default 
```

### <a name="dedicated-host"></a>Dedykowany host

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name myUpdateName \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>Usuwanie konfiguracji konserwacji

Służy `az maintenance configuration delete` do usuwania konfiguracji konserwacji. Usunięcie konfiguracji powoduje usunięcie kontroli konserwacji ze skojarzonych zasobów.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej, zobacz [Konserwacja i aktualizacje](maintenance-and-updates.md).
