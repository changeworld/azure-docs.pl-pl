---
title: Korzyści hybrydowe platformy Azure dla systemu Windows Server | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zmaksymalizować korzyści programu Windows Software Assurance w celu przełączenia lokalnej licencji Azure
services: virtual-machines-windows
documentationcenter: ''
author: kmouss
manager: jeconnoc
editor: ''
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/22/2017
ms.author: kmouss
ms.openlocfilehash: bfad3ff71be07026cf4a7dd6ad75df399349f844
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server
W przypadku klientów z Software Assurance Azure hybrydowego korzyści dla systemu Windows Server umożliwia użyć lokalnego licencji systemu Windows Server i uruchamiania maszyn wirtualnych Windows Azure taniego. Azure hybrydowego korzyści dla systemu Windows Server służy do wdrażania nowych maszyn wirtualnych za pomocą dowolnego obrazu platformy systemu Windows Server lub niestandardowych obrazów systemu Windows obsługiwane przez platformę Azure. W tym artykule przechodzi przez kroki dotyczące sposobu wdrażania nowych maszyn wirtualnych z Azure hybrydowego korzyści dla systemu Windows Server oraz sposób aktualizowania istniejących działających maszyn wirtualnych. Aby uzyskać więcej informacji na temat Azure hybrydowego korzyści dla systemu Windows Server zobacz oszczędności kosztów i licencjonowania, [strony licencjonowania Azure hybrydowego korzyści dla systemu Windows Server](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!IMPORTANT]
> Począwszy od 2017-9/11 została wycofana starszych obrazów [Centrum] systemu Windows Server, które zostały opublikowane w przypadku klientów z umowy Enterprise Agreement w witrynie Azure Marketplace, użyj standardowych systemu Windows Server z opcją "Zaoszczędzić" w portalu Azure hybrydowego korzyści dla System Windows Server. Aby uzyskać więcej informacji, zapoznaj się to [artykułu.](https://support.microsoft.com/en-us/help/4036360/retirement-azure-hybrid-use-benefit-images-for-ea-subscriptions)
>

> [!NOTE]
> Przy użyciu Azure hybrydowego korzyści dla systemu Windows Server z maszynami wirtualnymi, które są pobierane dla dodatkowego oprogramowania, takich jak SQL Server lub dowolny obrazów marketplace innej firmy jest rozwijane. Jeśli błąd 409 takich jak: zmiana właściwości "LicenseType" jest niedozwolony. następnie próbujesz konwertować lub wdrażania nowego systemu Windows Server maszynę Wirtualną, która ma koszt, dodatkowego oprogramowania, które może nie być obsługiwana w tym regionie. Tej samej, jeśli próbuje wyglądać Konfiguracja portalu opcja wykonywania konwersji i nie może uzyskać go dla tej maszyny Wirtualnej.
>


> [!NOTE]
> Klasycznych maszyn wirtualnych obsługiwane są tylko wdrażania nowej maszyny Wirtualnej z niestandardowymi obrazami lokalnego. Aby skorzystać z możliwości obsługiwane w tym artykule, należy najpierw przeprowadzić migrację maszyn wirtualnych w klasycznym modelu Resource Manager.
>


## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Sposoby używania Azure hybrydowego korzyści dla systemu Windows Server
Istnieje kilka sposobów maszyn wirtualnych systemu Windows za pomocą korzyści hybrydowe platformy Azure:

1. Można wdrożyć maszyn wirtualnych z jednego z dostarczonych [obrazów systemu Windows Server w witrynie Azure Marketplace](#https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.WindowsServer?tab=Overview)
2. Możesz [przekazać niestandardowe wirtualna](#upload-a-windows-vhd) i [wdrażanie przy użyciu szablonu usługi Resource Manager](#deploy-a-vm-via-resource-manager) lub [programu Azure PowerShell](#detailed-powershell-deployment-walkthrough)
3. Można przełączyć i konwersji istniejącej maszyny Wirtualnej między systemem z korzyści hybrydowe usługi Azure lub Zapłać koszt na żądanie w systemie Windows Server
4. Można także wdrożyć skali maszyny wirtualnej ustawić z Azure hybrydowego korzyści dla systemu Windows Server

> [!NOTE]
> Konwertowanie istniejących skalowania maszyn wirtualnych, które są skonfigurowane do korzystania z Azure hybrydowego korzyści dla systemu Windows Server nie jest obsługiwany.
>

## <a name="deploy-a-vm-from-a-windows-server-marketplace-image"></a>Wdrażanie maszyny Wirtualnej z obrazu systemu Windows Server z witryny Marketplace
Wszystkie obrazy systemu Windows Server, które są dostępne z poziomu portalu Azure Marketplace są włączone w Azure hybrydowego korzyści dla systemu Windows Server. Na przykład 2008SP1 systemu Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 i Windows Server i inne. Obrazy te można użyć do wdrożenia maszyn wirtualnych bezpośrednio z portalu Azure, szablonów usługi Resource Manager, programu Azure PowerShell lub innych zestawów SDK.

Można wdrażać te obrazy bezpośrednio z portalu Azure. Do użycia w szablonach usługi Resource Manager i przy użyciu programu Azure PowerShell wyświetlić listę obrazów w następujący sposób:

### <a name="powershell"></a>PowerShell
```powershell
Get-AzureRmVMImagesku -Location westus -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```
Możesz wykonać kroki, aby [Utwórz maszynę wirtualną z systemem Windows przy użyciu programu PowerShell](#https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) i przekaż LicenseType = "Windows_Server". Ta opcja umożliwia przy użyciu istniejących licencji systemu Windows Server na platformie Azure.

### <a name="portal"></a>Portal
Możesz wykonać kroki, aby [Utwórz maszynę wirtualną z systemem Windows przy użyciu portalu Azure](#https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) i wybierz opcję użycia istniejącej licencji systemu Windows Server.

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Konwertuj istniejącą maszynę Wirtualną przy użyciu Azure hybrydowego korzyści dla systemu Windows Server
Jeśli masz istniejącą maszynę Wirtualną, którą chcesz przekonwertować przeprowadzać Azure hybrydowego korzyści dla systemu Windows Server, można zaktualizować typu licencji maszyny Wirtualnej w następujący sposób:

### <a name="convert-to-using-azure-hybrid-benefit-for-windows-server"></a>Konwertuj na użyciu Azure hybrydowego korzyści dla systemu Windows Server
```powershell
$vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
$vm.LicenseType = "Windows_Server"
Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
```

### <a name="convert-back-to-pay-as-you-go"></a>Konwertuj do płatności zgodnie z rzeczywistym
```powershell
$vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
$vm.LicenseType = "None"
Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
```

### <a name="portal"></a>Portal
Z portalu bloku maszyny Wirtualnej można zaktualizować maszyny Wirtualnej do użycia korzystać hybrydowe platformy Azure, wybierając opcję "Konfiguracja" i ustaw opcję "korzystać hybrydowe platformy Azure"

> [!NOTE]
> Jeśli nie widzisz opcji Przełącz "hybrydowe platformy Azure korzyści" w obszarze "Konfiguracja" to, że konwersja nie jest jeszcze obsługiwane dla wybranego typu maszyny Wirtualnej (na przykład utworzony przez Maszynę wirtualną z niestandardowego obrazu lub obraz, który ma dodatkowe oprogramowanie płatną, takie jak SQL Server lub Azure Marketplace oprogramowania innych firm).
>

## <a name="upload-a-windows-server-vhd"></a>Przekazywanie wirtualnego dysku twardego Windows Server
Aby wdrożyć Maszynę wirtualną systemu Windows Server na platformie Azure, należy najpierw utworzyć wirtualny dysk twardy zawiera podstawowe kompilacji systemu Windows. Tego wirtualnego dysku twardego, należy odpowiednio przygotować za pomocą programu Sysprep przed przekazaniem go do platformy Azure. Możesz [Dowiedz się więcej na temat wirtualnego dysku twardego wymagań i procesu Sysprep](upload-generalized-managed.md) i [Obsługa programu Sysprep dla ról serwera](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Utwórz kopię zapasową maszyny Wirtualnej przed uruchomieniem programu Sysprep. 

Po przygotowaniu dysk VHD, przekazanie dysku VHD do konta magazynu Azure w następujący sposób:

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server, SharePoint Server i Dynamics można również korzystać z programu Software Assurance licencjonowania. Są należy przygotować obraz systemu Windows Server instalowania składniki aplikacji i zapewnianie odpowiednio kluczy licencji, a następnie przekazywanie obrazu dysku na platformie Azure. Zapoznaj się z dokumentacją odpowiedniego do uruchamiania narzędzia Sysprep z aplikacji, takich jak [zagadnienia dotyczące instalowania programu SQL Server za pomocą programu Sysprep](https://msdn.microsoft.com/library/ee210754.aspx) lub [Utwórz obraz referencyjny programu SharePoint Server 2016 (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx).
>
>

Można również uzyskać więcej informacji [przekazywanie wirtualnego dysku twardego do procesu systemu Azure](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account)

## <a name="deploy-a-vm-via-resource-manager-template"></a>Wdróż Maszynę wirtualną za pomocą szablonu usługi Resource Manager
W szablonach usługi Resource Manager dodatkowy parametr `licenseType` musi być określona. Możesz przeczytać dodatkowe informacje [tworzenia szablonów usługi Azure Resource Manager](../../resource-group-authoring-templates.md). Po utworzeniu dysk VHD przekazany do platformy Azure, Edytuj szablon Menedżera zasobów, aby dołączyć typ licencji w ramach dostawcy obliczeń i wdrażania szablonu w zwykły:

```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="deploy-a-vm-via-powershell-quickstart"></a>Wdróż Maszynę wirtualną za pomocą programu PowerShell — Szybki Start
Podczas wdrażania maszyny Wirtualnej systemu Windows Server za pomocą programu PowerShell, masz dodatkowy parametr `-LicenseType`. Po utworzeniu dysk VHD przekazany do platformy Azure, utwórz maszynę Wirtualną przy użyciu `New-AzureRmVM` i określić typ licencjonowania w następujący sposób:

W systemie Windows Server:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

Możesz przeczytać bardziej opisowe Przewodnik na temat różnych kroków do [Utwórz maszynę Wirtualną z systemem Windows przy użyciu usługi Resource Manager i programu PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Sprawdź, czy maszyna wirtualna jest wykorzystania asysty licencjonowania
Po wdrożeniu maszyny Wirtualnej za pomocą obu programu PowerShell szablonu usługi Resource Manager lub portalu, możesz sprawdzić typu licencji z `Get-AzureRmVM` w następujący sposób:

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Wynik jest podobny do poniższego przykładu w systemie Windows Server:

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

## <a name="list-all-azure-hybrid-benefit-for-windows-server-vms-in-a-subscription"></a>Wyświetl listę wszystkich hybrydowego korzyści dla systemu Windows Server maszynach wirtualnych platformy Azure w ramach subskrypcji

Aby wyświetlić i liczba wszystkie maszyny wirtualne wdrażane z Azure hybrydowego korzyści dla systemu Windows Server, można uruchomić następujące polecenie z subskrypcji:

```powershell
$vms = Get-AzureRMVM 
foreach ($vm in $vms) {"VM Name: " + $vm.Name, "   Azure Hybrid Benefit for Windows Server: "+ $vm.LicenseType}
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Wdrażanie skalowania maszyny wirtualnej, ustaw o Azure hybrydowego korzyści dla systemu Windows Server
W ramach maszyny wirtualnej zestawu skalowania maszyny wirtualnej szablonów Resource Manager, dodatkowy parametr `licenseType` musi być określona. Możesz przeczytać dodatkowe informacje [tworzenia szablonów usługi Azure Resource Manager](../../resource-group-authoring-templates.md). Edytuj szablon Menedżera zasobów można uwzględnić właściwość licenseType jako część virtualMachineProfile zestaw skali i wdrażania szablonu w zwykły — zobacz poniższy przykład przy użyciu obrazu systemu Windows Server 2016:


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
Możesz również [tworzenie i wdrażanie zestaw skali maszyny wirtualnej](#https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-create) i ustaw właściwość LicenseType

## <a name="next-steps"></a>Kolejne kroki
Przeczytaj więcej na temat [jak zaoszczędzić z asysty hybrydowe platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

Dowiedz się więcej o [Azure hybrydowego korzyści dla systemu Windows Server szczegółowe wskazówki dotyczące licencjonowania](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)

Dowiedz się więcej o [szablonów za pomocą Menedżera zasobów](../../azure-resource-manager/resource-group-overview.md)

Dowiedz się więcej o [Azure hybrydowego korzyści dla systemu Windows Server i usługi Azure Site Recovery upewnij migrowanie aplikacji Azure jeszcze bardziej ekonomiczne](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)

Dowiedz się więcej o [systemu Windows 10 na platformie Azure z prawem Hosting wielodostępnej](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)

Przeczytaj więcej na temat [— często zadawane pytania](#https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
