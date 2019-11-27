---
title: Kontrola konserwacji maszyn wirtualnych platformy Azure
description: Dowiedz się, jak kontrolować, kiedy obsługi jest stosowany do maszyn wirtualnych platformy Azure przy użyciu funkcji kontroli konserwacji.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: cynthn
ms.openlocfilehash: f336bd0e208e847dbb24123285066330d8a1ce40
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535849"
---
# <a name="preview-control-updates-with-maintenance-control-and-the-azure-cli"></a>Wersja zapoznawcza: sterowanie aktualizacjami przy użyciu sterowania konserwacją i interfejsu wiersza polecenia platformy Azure

Zarządzaj aktualizacjami platformy, które nie wymagają ponownego uruchomienia, przy użyciu funkcji kontroli konserwacji. Platforma Azure często aktualizuje swoją infrastrukturę w celu poprawy niezawodności, wydajności, zabezpieczeń lub uruchamiania nowych funkcji. Większość aktualizacji jest niewidoczna dla użytkowników. Niektóre wrażliwe obciążenia, takie jak gry, przesyłanie strumieniowe multimediów i transakcje finansowe, nie mogą tolerować nawet kilku sekund zamarzania maszyny wirtualnej lub odłączenia jej do konserwacji. Sterowanie konserwacją umożliwia zaczekanie na aktualizacje platformy i stosowanie ich w 35-dniowym oknie kroczącym. 

Kontrola konserwacji pozwala określić, kiedy mają być stosowane aktualizacje odizolowanych maszyn wirtualnych i hostów dedykowanych platformy Azure.

Za pomocą kontrolki konserwacji można:
- Aktualizacje wsadowe w jednym pakiecie aktualizacji.
- Zaczekaj do 35 dni, aby zastosować aktualizacje. 
- Automatyzuj aktualizacje platformy dla Twojego okna obsługi przy użyciu Azure Functions.
- Konfiguracje konserwacji działają w ramach subskrypcji i grup zasobów. 

> [!IMPORTANT]
> Kontrola konserwacji jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 

## <a name="limitations"></a>Ograniczenia

- Maszyny wirtualne muszą znajdować się na [dedykowanym hoście](./linux/dedicated-hosts.md)lub być tworzone przy użyciu [IZOLOWANEGO rozmiaru maszyny wirtualnej](./linux/isolation.md).
- Po upływie 35 dni zostanie automatycznie zastosowana aktualizacja.
- Użytkownik musi mieć dostęp **właściciela zasobu** .


## <a name="install-the-maintenance-extension"></a>Zainstaluj rozszerzenie konserwacji

Jeśli zdecydujesz się zainstalować [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) lokalnie, musisz mieć wersję 2.0.76 lub nowszą.

Zainstaluj rozszerzenie interfejsu wiersza polecenia `maintenance` w wersji zapoznawczej lokalnie lub w Cloud Shell. 

```azurecli-interactive
az extension add -n maintenance
```


## <a name="create-a-maintenance-configuration"></a>Utwórz konfigurację konserwacji

Użyj `az maintenance configuration create`, aby utworzyć konfigurację konserwacji. W tym przykładzie zostanie utworzona konfiguracja konserwacji o nazwie Moja *config* objęta zakresem hosta. 

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

Skopiuj identyfikator konfiguracji z danych wyjściowych do użycia później.

Użycie `--maintenanceScope host` zapewnia, że konfiguracja konserwacji służy do kontrolowania aktualizacji hosta.

Jeśli spróbujesz utworzyć konfigurację o tej samej nazwie, ale w innej lokalizacji, zostanie wyświetlony komunikat o błędzie. Nazwy konfiguracji muszą być unikatowe dla Twojej subskrypcji.

Można wykonać zapytanie o dostępne konfiguracje konserwacji przy użyciu `az maintenance configuration list`.

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>Przypisz konfigurację

Użyj `az maintenance assignment create`, aby przypisać konfigurację do izolowanej maszyny wirtualnej lub dedykowanego hosta platformy Azure.

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

Zastosuj konfigurację do maszyny wirtualnej przy użyciu identyfikatora konfiguracji. Określ `--resource-type virtualMachines` i podaj nazwę maszyny wirtualnej dla `--resource-name`oraz grupę zasobów dla maszyny wirtualnej w `--resource-group`i lokalizację maszyny wirtualnej dla `--location`. 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id '/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig'
```

### <a name="dedicated-host"></a>Dedykowany host

Aby zastosować konfigurację do dedykowanego hosta, należy dołączyć `--resource-type hosts`, `--resource-parent-name` z nazwą grupy hostów i `--resource-parent-type hostGroups`. 

Parametr `--resource-id` jest IDENTYFIKATORem hosta. Aby uzyskać identyfikator dedykowanego hosta, można użyć [AZ VM Host Get-instance-View](/cli/azure/vm/host#az-vm-host-get-instance-view) .

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

Można sprawdzić, czy konfiguracja została zastosowana prawidłowo, lub sprawdzić, jaka konfiguracja jest aktualnie stosowana, przy użyciu `az maintenance assignment list`.

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


## <a name="check-for-pending-updates"></a>Wyszukaj oczekujące aktualizacje

Użyj `az maintenance update list`, aby sprawdzić, czy istnieją oczekujące aktualizacje. Update--Subscription to identyfikator subskrypcji zawierającej maszynę wirtualną.

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

Sprawdź, czy istnieją oczekujące aktualizacje dla izolowanej maszyny wirtualnej. W tym przykładzie dane wyjściowe są sformatowane jako tabela na potrzeby czytelności.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Dedykowany host

Aby sprawdzić oczekujące aktualizacje dla dedykowanego hosta. W tym przykładzie dane wyjściowe są sformatowane jako tabela na potrzeby czytelności. Zamień wartości dla zasobów własnymi.

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

Użyj `az maintenance apply update`, aby zastosować oczekujące aktualizacje.

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

Utwórz żądanie zastosowania aktualizacji dla izolowanej maszyny wirtualnej.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myMaintenanceRG\
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Dedykowany host

Zastosuj aktualizacje do dedykowanego hosta.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>Sprawdź stan zastosowania aktualizacji 

Postęp aktualizacji można sprawdzić przy użyciu `az maintenance applyupdate get`. 

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

Zastąp `myUpdateName` nazwą aktualizacji, która została zwrócona podczas uruchomienia `az maintenance applyupdate create`.

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name myUpdateName 
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
   --apply-update-name default \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>Usuń konfigurację konserwacji

Użyj `az maintenance configuration delete`, aby usunąć konfigurację konserwacji. Usunięcie konfiguracji spowoduje usunięcie kontroli konserwacji ze skojarzonych zasobów.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej, zobacz temat [konserwacja i aktualizacje](maintenance-and-updates.md).