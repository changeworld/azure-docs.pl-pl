---
title: Korzyści hybrydowe platformy Azure dla systemu Windows Server | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zmaksymalizować korzyści programu Windows Software Assurance w celu przełączenia lokalnej licencji Azure
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: jeconnoc
editor: ''
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing-ms
ms.openlocfilehash: a4b0baefc8c3c839a06d6540e57b34657138c8ff
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server
W przypadku klientów z Software Assurance Azure hybrydowego korzyści dla systemu Windows Server umożliwia użyć lokalnego licencji systemu Windows Server i uruchamiania maszyn wirtualnych Windows Azure taniego. Azure hybrydowego korzyści dla systemu Windows Server służy do wdrażania nowych maszyn wirtualnych z systemem operacyjnym Windows. W tym artykule przechodzi przez kroki dotyczące sposobu wdrażania nowych maszyn wirtualnych z Azure hybrydowego korzyści dla systemu Windows Server oraz sposób aktualizowania istniejących działających maszyn wirtualnych. Aby uzyskać więcej informacji na temat Azure hybrydowego korzyści dla systemu Windows Server zobacz oszczędności kosztów i licencjonowania, [strony licencjonowania Azure hybrydowego korzyści dla systemu Windows Server](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!Important]
> Każda licencja na 2 procesory lub każdy zestaw licencji na 16 rdzeni jest uprawniony do korzystania z dwóch wystąpień z maksymalnie 8 rdzeniami lub jednego wystąpienia z maksymalnie 16 rdzeniami. Korzyści z używania hybrydowej platformy Azure dla licencji Standard Edition mogą być używane tylko raz lokalnie lub na platformie Azure. Korzyści wersji Datacenter Edition pozwalają na równoczesne używanie zarówno lokalnie, jak i na platformie Azure.
>

> [!Important]
> Przy użyciu Azure hybrydowego korzyści dla systemu Windows Server z dowolnej maszyny wirtualne z systemami systemu operacyjnego Windows Server są teraz obsługiwane we wszystkich regionach, w tym o maszynach wirtualnych z dodatkowego oprogramowania, takich jak SQL Server lub oprogramowania innych firm w witrynie marketplace. 
>

> [!NOTE]
> Klasycznych maszyn wirtualnych obsługiwane są tylko wdrażania nowej maszyny Wirtualnej z niestandardowymi obrazami lokalnego. Aby skorzystać z możliwości obsługiwane w tym artykule, należy najpierw przeprowadzić migrację maszyn wirtualnych w klasycznym modelu Resource Manager.
>


## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Sposoby używania Azure hybrydowego korzyści dla systemu Windows Server
Istnieje kilka sposobów maszyn wirtualnych systemu Windows za pomocą korzyści hybrydowe platformy Azure:

1. Można wdrożyć maszyn wirtualnych z jednego z dostarczonych [obrazów systemu Windows Server w witrynie Azure Marketplace](#https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.WindowsServer?tab=Overview)
2. Możesz przekazać niestandardowe maszyny Wirtualnej i wdrażać przy użyciu szablonu usługi Resource Manager lub programu Azure PowerShell
3. Można przełączyć i konwersji istniejącej maszyny Wirtualnej między systemem z korzyści hybrydowe usługi Azure lub Zapłać koszt na żądanie w systemie Windows Server
4. Można także zastosować Azure hybrydowego korzyści dla systemu Windows Server na także zestaw skalowania maszyny wirtualnej


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Utwórz maszynę Wirtualną z korzyści hybrydowe platformy Azure dla systemu Windows Server
Wszystkie obrazy systemu operacyjnego serwera, na podstawie są obsługiwane dla Azure hybrydowego korzyści dla systemu Windows Server. Można używać obrazów pomocy technicznej platformy Azure lub Przekaż własnych niestandardowych obrazów systemu Windows Server. 

### <a name="portal"></a>Portal
Aby utworzyć Maszynę wirtualną z Azure hybrydowego korzyści dla systemu Windows Server, należy użyć przełącznik w sekcji "Oszczędności".

### <a name="powershell"></a>PowerShell
```powershell
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -ImageName "Win2016Datacenter" `
    -LicenseType "Windows_Server"
```

### <a name="cli"></a>Interfejs wiersza polecenia
```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --license-type Windows_Server
```

### <a name="template"></a>Szablon
W szablonach usługi Resource Manager dodatkowy parametr `licenseType` musi być określona. Możesz przeczytać dodatkowe informacje [tworzenia szablonów usługi Azure Resource Manager](../../resource-group-authoring-templates.md)
```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Konwertuj istniejącą maszynę Wirtualną przy użyciu Azure hybrydowego korzyści dla systemu Windows Server
Jeśli masz istniejącą maszynę Wirtualną, którą chcesz przekonwertować przeprowadzać Azure hybrydowego korzyści dla systemu Windows Server, można zaktualizować typu licencji maszyny Wirtualnej w następujący sposób:

### <a name="portal"></a>Portal
Z portalu bloku maszyny Wirtualnej można zaktualizować maszyny Wirtualnej do użycia korzystać hybrydowe platformy Azure, wybierając opcję "Konfiguracja" i ustaw opcję "korzystać hybrydowe platformy Azure"

### <a name="powershell"></a>PowerShell
- Konwertuj istniejących maszyn wirtualnych z serwera systemu Windows Azure hybrydowego korzyści dla systemu Windows Server

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Konwertowanie maszyn wirtualnych systemu Windows Server za pomocą płatność za rzeczywiste użycie korzyści

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>Interfejs wiersza polecenia
- Konwertuj istniejących maszyn wirtualnych z serwera systemu Windows Azure hybrydowego korzyści dla systemu Windows Server

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```
    
### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Jak sprawdzić, maszyna wirtualna jest wykorzystania asysty licencjonowania
Po wdrożeniu maszyny Wirtualnej za pomocą obu programu PowerShell szablonu usługi Resource Manager lub portalu, możesz sprawdzić ustawienie w poniższych metod.

### <a name="portal"></a>Portal
Z portalu bloku maszyny Wirtualnej można wyświetlić przełącznik Azure hybrydowego korzyści dla systemu Windows Server, wybierając kartę "Configuration".

### <a name="powershell"></a>PowerShell
W poniższym przykładzie przedstawiono typ licencji dla jednej maszyny Wirtualnej
```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Dane wyjściowe:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Output to różni się z maszyną Wirtualną następujące wdrożenie bez licencjonowania Azure hybrydowego korzyści dla systemu Windows Server:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

### <a name="cli"></a>Interfejs wiersza polecenia
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query '[?licenseType==Windows_Server]' -o table
```

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Wyświetl listę wszystkich maszyn wirtualnych z Azure hybrydowego korzyści dla systemu Windows Server w ramach subskrypcji
Aby wyświetlić i liczba wszystkie maszyny wirtualne wdrażane z Azure hybrydowego korzyści dla systemu Windows Server, można uruchomić następujące polecenie z subskrypcji:

### <a name="portal"></a>Portal
Z maszyny wirtualnej lub maszyny wirtualnej skali zestawy bloku zasobów można wyświetlić listę wirtualne i licencjonowania typu konfigurując kolumny tabeli do dołączenia "Korzyści hybrydowe platformy Azure". Ustawienia maszyny Wirtualnej może być w "Włączone", "Wyłączone" lub "Nie jest obsługiwany" stanu.

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzureRMVM 
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>Interfejs wiersza polecenia
```azurecli
az vm list --query '[?licenseType==Windows_Server]' -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Wdrażanie zestaw skali maszyny wirtualnej z korzyści hybrydowe platformy Azure dla systemu Windows Server
W ramach maszyny wirtualnej zestawu skalowania maszyny wirtualnej szablonów Resource Manager, dodatkowy parametr `licenseType` muszą być określone w Twojej VirtualMachineProfile właściwości. Można to zrobić podczas tworzenia lub aktualizacji dla Twojej skali ustawiana za pośrednictwem szablonu ARM, programu Powershell, interfejsu wiersza polecenia Azure lub REST.

W poniższym przykładzie użyto szablonu ARM przy użyciu obrazu systemu Windows Server Datacenter 2016:
```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
Można też uzyskać więcej informacji o sposobie [zmodyfikować zestaw skali maszyny wirtualnej](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) więcej sposobów, aby zaktualizować na skalę można ustawić.

## <a name="next-steps"></a>Kolejne kroki
- Przeczytaj więcej na temat [jak zaoszczędzić z asysty hybrydowe platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- Przeczytaj więcej na temat [często zadawane pytania dotyczące korzyści hybrydowe platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- Dowiedz się więcej o [Azure hybrydowego korzyści dla systemu Windows Server szczegółowe wskazówki dotyczące licencjonowania](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)
- Dowiedz się więcej o [Azure hybrydowego korzyści dla systemu Windows Server i usługi Azure Site Recovery upewnij migrowanie aplikacji Azure jeszcze bardziej ekonomiczne](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)
- Dowiedz się więcej o [systemu Windows 10 na platformie Azure z prawem Hosting wielodostępnej](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)
- Dowiedz się więcej o [szablonów za pomocą Menedżera zasobów](../../azure-resource-manager/resource-group-overview.md)
