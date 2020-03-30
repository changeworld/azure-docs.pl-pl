---
title: Jak wdrożyć system Windows 10 na platformie Azure z prawami do hostingu wielodostępnego
description: Dowiedz się, jak zmaksymalizować korzyści związane z pakietem Windows Software Assurance, aby przenieść licencje lokalne na platformę Azure
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: 9ff8cc64266375a2d439763b222870843136f67a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101505"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Jak wdrożyć system Windows 10 na platformie Azure z prawami do hostingu wielodostępnego 
Dla klientów z systemem Windows 10 Enterprise E3/E5 na użytkownika lub Windows Virtual Desktop Access na użytkownika (Licencje subskrypcyjne użytkowników lub licencje subskrypcyjne dla dodatkowych użytkowników), multitenant hosting rights for Windows 10 pozwala przenieść licencje systemu Windows 10 do chmury i uruchomić maszyny wirtualne systemu Windows 10 na platformie Azure bez płacenia za inną licencję. Aby uzyskać więcej informacji, zobacz [Hosting wielodostępny dla systemu Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> W tym artykule przedstawiono implementowanie korzyści licencyjnych dla obrazów pulpitu systemu Windows 10 Pro w portalu Azure Marketplace.
> - W przypadku obrazów systemu Windows 7, 8.1, 10 Enterprise (x64) w portalu Azure Marketplace dla subskrypcji msdn zapoznaj się z [klientem systemu Windows na platformie Azure, aby zapoznać się ze scenariuszami tworzenia/testowania](client-images.md)
> - Aby uzyskać korzyści związane z licencjonowania systemu Windows Server, zapoznaj się z [zaletami korzystania z usługi Azure Hybrid dla obrazów systemu Windows Server](hybrid-use-benefit-licensing.md).
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Wdrażanie obrazu systemu Windows 10 z witryny Azure Marketplace 
W przypadku wdrożeń szablonów programu Powershell, CLI i Azure Resource Manager obraz systemu Windows 10 można znaleźć pod następującą nazwa wydawcą, oferta, sku.

| System operacyjny  |      PublisherName      |  Oferta | SKU |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS2-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2-ProN  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS3-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3-ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Przekazywanie dysku twardego w systemie Windows 10 na platformę Azure
jeśli przesyłasz uogólniony Windows 10 VHD, należy pamiętać, że system Windows 10 nie ma wbudowanego konta administratora włączone domyślnie. Aby włączyć wbudowane konto administratora, dołącz następujące polecenie jako część rozszerzenia Skrypt niestandardowy.

```powershell
Net user <username> /active:yes
```

Poniższy fragment kodu programu PowerShell oznacza wszystkie konta administratora jako aktywne, w tym wbudowanego administratora. W tym przykładzie jest przydatne, jeśli wbudowana nazwa użytkownika administratora jest nieznany.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Więcej informacji: 
* [Jak przekazać dysk VHD na platformę Azure](upload-generalized-managed.md)
* [Jak przygotować dysk VHD systemu Windows do przekazania na platformę Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Wdrażanie systemu Windows 10 z prawami do hostingu wielodostępnego
Upewnij się, że [zainstalowano i skonfigurowano najnowszą usługę Azure PowerShell](/powershell/azure/overview). Po przygotowaniu dysku VHD przekaż dysk VHD na `Add-AzVhd` swoje konto usługi Azure Storage przy użyciu polecenia cmdlet w następujący sposób:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Wdrażanie przy użyciu wdrażania szablonów usługi Azure Resource Manager** W szablonach Menedżera zasobów `licenseType` można określić dodatkowy parametr. Więcej informacji na temat [tworzenia szablonów usługi Azure Resource Manager](../../resource-group-authoring-templates.md)można przeczytać. Po przekazaniu dysku VHD na platformę Azure edytuj szablon Resource Manager, aby uwzględnić typ licencji jako część dostawcy danych obliczeniowych i wdrożyć szablon w zwykły sposób:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Wdrażanie za pośrednictwem programu PowerShell** Podczas wdrażania maszyny Wirtualnej systemu Windows Server za pośrednictwem `-LicenseType`programu PowerShell, masz dodatkowy parametr dla . Po przekazaniu dysku VHD na platformę Azure `New-AzVM` utworzysz maszynę wirtualną przy użyciu i określisz typ licencjonowania w następujący sposób:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Sprawdź, czy maszyna wirtualna korzysta z korzyści wynikających z licencjonowania
Po wdrożeniu maszyny wirtualnej za pomocą metody wdrażania programu PowerShell `Get-AzVM` lub Resource Manager sprawdź typ licencji w następujący sposób:
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Dane wyjściowe są podobne do następującego przykładu dla systemu Windows 10 z poprawnym typem licencji:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Ten wynik danych kontrastuje z następującą maszyną wirtualną wdrożoną bez licencjonowania korzyści użycia hybrydowego platformy Azure, na przykład maszynę wirtualną wdrożoną bezpośrednio z galerii platformy Azure:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Dodatkowe informacje dotyczące dołączania do usługi Azure AD
>[!NOTE]
>Platforma Azure udostępnia wszystkie maszyny wirtualne systemu Windows z wbudowanym kontem administratora, którego nie można użyć do dołączenia do usługi AAD. Na przykład *ustawienia > konto > Access Work lub School > +Connect* nie będą działać. Aby ręcznie dołączyć do usługi Azure AD, należy utworzyć i zalogować się jako drugie konto administratora. Można również skonfigurować usługi Azure AD przy użyciu pakietu inicjowania obsługi administracyjnej, użyj łącza jest *następnych kroków* sekcji, aby dowiedzieć się więcej.
>
>

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [konfigurowaniu usługi VDA dla systemu Windows 10](https://docs.microsoft.com/windows/deployment/vda-subscription-activation)
- Dowiedz się więcej o [hostingu wielodostępnego dla systemu Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)


