---
title: Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zmaksymalizować korzyści Windows Software Assurance użycie licencji lokalnych na platformie Azure
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: gwallace
editor: ''
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing-ms
ms.openlocfilehash: 739c867171d7b59a68f7e4d11bbf50a189568ce7
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722768"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server
Klienci z pakietem Software Assurance Azure korzyści użycia hybrydowego dla systemu Windows Server umożliwia użycie licencji na system Windows Server w środowisku lokalnym i uruchamianie maszyn wirtualnych Windows na platformie Azure, przy niższych kosztach. Azure korzyści użycia hybrydowego dla systemu Windows Server służy do wdrażania nowych maszyn wirtualnych z systemem operacyjnym Windows. W tym artykule przechodzi przez instrukcje dotyczące sposobu wdrażania nowych maszyn wirtualnych za pomocą usługi Azure korzyści użycia hybrydowego dla systemu Windows Server oraz jak zaktualizować istniejące uruchamianie maszyn wirtualnych. Aby uzyskać więcej informacji na temat usługi Azure korzyści użycia hybrydowego dla systemu Windows Server oszczędności licencjonowania i kosztów, zobacz [strony licencjonowania platformy Azure korzyści użycia hybrydowego dla systemu Windows Server](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!Important]
> Każda licencja na 2 procesory lub każdy zestaw licencji na 16 rdzeni jest uprawniony do korzystania z dwóch wystąpień z maksymalnie 8 rdzeniami lub jednego wystąpienia z maksymalnie 16 rdzeniami. Korzyści z używania hybrydowej platformy Azure dla licencji Standard Edition mogą być używane tylko raz lokalnie lub na platformie Azure. Korzyści wersji Datacenter Edition pozwalają na równoczesne używanie zarówno lokalnie, jak i na platformie Azure.
>

> [!Important]
> Azure korzyści użycia hybrydowego dla systemu Windows Server przy użyciu maszyn wirtualnych z systemem operacyjnym Windows Server są teraz obsługiwane we wszystkich regionach, w tym maszyn wirtualnych przy użyciu dodatkowego oprogramowania, takiego jak SQL Server lub oprogramowania innych firm w witrynie marketplace. 
>

> [!NOTE]
> Dla klasycznych maszyn wirtualnych jest obsługiwane tylko Wdrażanie nowej maszyny Wirtualnej z na obrazach niestandardowych lokalnie. Aby móc korzystać z funkcji obsługiwanych w tym artykule, należy najpierw przeprowadzić migrację maszyn wirtualnych w klasycznym modelu usługi Resource Manager.
>

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Sposoby korzystania z platformy Azure korzyści użycia hybrydowego dla systemu Windows Server
Istnieje kilka sposobów na maszynach wirtualnych Windows za pomocą korzyści użycia hybrydowego platformy Azure:

1. Można wdrożyć maszyny wirtualne z jednego z podanych obrazy systemu Windows Server w witrynie Azure Marketplace
2. Możesz przekazać niestandardową maszynę Wirtualną i wdrażanie jej za pomocą szablonu usługi Resource Manager lub programu Azure PowerShell
3. Możesz przełączać i konwertowanie istniejącej maszyny Wirtualnej między systemem dzięki korzyści użycia hybrydowego platformy Azure lub naliczana jest opłata za na żądanie w systemie Windows Server
4. Azure korzyści użycia hybrydowego dla systemu Windows Server można zastosować również na także zestaw skalowania maszyn wirtualnych


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Utwórz Maszynę wirtualną z korzyścią użycia hybrydowego platformy Azure dla systemu Windows Server
Wszystkie obrazy z systemem operacyjnym Windows Server są obsługiwane dla usługi Azure korzyści użycia hybrydowego dla systemu Windows Server. Możesz używać obrazów pomocy technicznej platformy Azure lub przekazać własne niestandardowe obrazy systemu Windows Server. 

### <a name="portal"></a>Portal
Aby utworzyć Maszynę wirtualną za pomocą usługi Azure korzyści użycia hybrydowego dla systemu Windows Server, użyj przełącznika, w sekcji "Oszczędności".

### <a name="powershell"></a>PowerShell


```powershell
New-AzVm `
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
W szablonach usługi Resource Manager, dodatkowy parametr `licenseType` musi być określona. Przeczytaj więcej o [Tworzenie szablonów usługi Azure Resource Manager](../../resource-group-authoring-templates.md)
```json
"properties": {
    "licenseType": "Windows_Server",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Konwertowanie istniejącej maszyny Wirtualnej przy użyciu usługi Azure korzyści użycia hybrydowego dla systemu Windows Server
W przypadku istniejącej maszyny Wirtualnej, który chcesz przekonwertować z zalet platformy Azure korzyści użycia hybrydowego dla systemu Windows Server można zaktualizować typu licencji maszyny Wirtualnej, postępując zgodnie z poniższymi instrukcjami.

> [!NOTE]
> Zmiana typu licencji na maszynie Wirtualnej nie powoduje ponowne uruchomienie lub spowodować, że interuption usługi systemu.  Jest po prostu aktualizację flagi metadanych.
> 

### <a name="portal"></a>Portal
W witrynie portal w bloku maszyny Wirtualnej możesz zaktualizować maszyny Wirtualnej, użyj korzyści użycia hybrydowego platformy Azure, wybierając opcję "Konfiguracja" i ustaw opcję "korzyść użycia hybrydowego platformy Azure"

### <a name="powershell"></a>PowerShell
- Konwertowanie istniejących maszyn wirtualnych z serwera systemu Windows na platformie Azure korzyści użycia hybrydowego dla systemu Windows Server

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Konwertuj maszyny wirtualne z systemem Windows Server dzięki korzyściom z powrotem do płatność za rzeczywiste użycie

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>Interfejs wiersza polecenia
- Konwertowanie istniejących maszyn wirtualnych z serwera systemu Windows na platformie Azure korzyści użycia hybrydowego dla systemu Windows Server

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```

### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Jak zweryfikować maszyny Wirtualnej wykorzystywanych korzyść z licencjonowania
Po wdrożeniu maszyny Wirtualnej za pomocą programu PowerShell, szablon usługi Resource Manager lub portalu, można sprawdzić ustawienia za pomocą następujących metod.

### <a name="portal"></a>Portal
W witrynie portal w bloku maszyny Wirtualnej można wyświetlić przełącznik Azure korzyści użycia hybrydowego dla systemu Windows Server, wybierając kartę "Configuration".

### <a name="powershell"></a>PowerShell
Poniższy przykład przedstawia typ licencji dla pojedynczej maszyny Wirtualnej
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Dane wyjściowe:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

To różni się w danych wyjściowych za pomocą następujących maszyn wirtualnych wdrożyć bez licencjonowania platformy Azure korzyści użycia hybrydowego dla systemu Windows Server:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

### <a name="cli"></a>Interfejs wiersza polecenia
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query "[?licenseType=='Windows_Server']" -o table
```

> [!NOTE]
> Zmiana typu licencji na maszynie Wirtualnej nie powoduje ponowne uruchomienie lub spowodować, że interuption usługi systemu. Jest licencjonowanie flagi tylko metadane.
>

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Lista wszystkich maszyn wirtualnych za pomocą usługi Azure korzyści użycia hybrydowego dla systemu Windows Server w ramach subskrypcji
Aby wyświetlić i liczba wszystkich maszyn wirtualnych wdrożonych przy użyciu usługi Azure korzyści użycia hybrydowego dla systemu Windows Server, można uruchomić następujące polecenie z subskrypcji:

### <a name="portal"></a>Portal
Z maszyny wirtualnej lub maszyny wirtualnej scale sets bloku zasobów możesz wyświetlić listę wszystkich maszyn wirtualnych i typ licencji, konfigurując kolumny tabeli, aby uwzględnić "Korzyść użycia hybrydowego platformy Azure". Ustawienia maszyny Wirtualnej może być w "Włączone", "Nie jest włączona" lub "Nie jest obsługiwane" stanu.

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzVM
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>Interfejs wiersza polecenia
```azurecli
az vm list --query "[?licenseType=='Windows_Server']" -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Wdrażanie zestawu skalowania maszyn wirtualnych z korzyścią użycia hybrydowego platformy Azure dla systemu Windows Server
W ramach maszyny wirtualnej zestawu skalowania szablonów usługi Resource Manager, dodatkowy parametr `licenseType` muszą być określone w swojej właściwości VirtualMachineProfile. Można to zrobić podczas tworzenia lub aktualizacji dla skalowania jest ustawiana przy użyciu szablonu ARM, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub REST.

W poniższym przykładzie użyto szablonu ARM przy użyciu obrazu systemu Windows Server 2016 Datacenter:
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
Można też uzyskać więcej informacji o sposobie [modyfikowanie zestawu skalowania maszyn wirtualnych](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) więcej sposobów zaktualizować skalowania można ustawić.

## <a name="next-steps"></a>Następne kroki
- Przeczytaj więcej na temat [jak oszczędzać pieniądze dzięki korzyści użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- Przeczytaj więcej na temat [często zadawane pytania dotyczące korzyści użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- Dowiedz się więcej o [Azure hybrydowego korzyści dla systemu Windows Server szczegółowe wskazówki dotyczące licencjonowania](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)
- Dowiedz się więcej o [Azure korzyści użycia hybrydowego dla systemu Windows Server i usługi Azure Site Recovery, że migracja aplikacji do platformy Azure jeszcze bardziej ekonomiczne](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)
- Dowiedz się więcej o [systemu Windows 10 na platformie Azure dzięki wielodostępnej prawo hostingu](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)
- Dowiedz się więcej o [szablonów przy użyciu usługi Resource Manager](../../azure-resource-manager/resource-group-overview.md)
