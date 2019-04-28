---
title: Jak wdrożyć system Windows 10 na platformie Azure dzięki wielodostępnej praw hostingu
description: Dowiedz się, jak zmaksymalizować korzyści Windows Software Assurance użycie licencji lokalnych na platformie Azure
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: 7f43528c55cd22c2649ca0f1208da6f41695b98e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485485"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Jak wdrożyć system Windows 10 na platformie Azure dzięki wielodostępnej praw hostingu 
W przypadku klientów Windows 10 Enterprise E3/E5 dla poszczególnych użytkowników lub dostęp do usług pulpitu wirtualnego Windows na użytkownika (licencje subskrypcji użytkownika lub licencji subskrypcji użytkownika dodatku) wielodostępnej hostingu praw dla systemu Windows 10 umożliwia Przenieś licencje systemu Windows 10 do chmury i uruchamianie maszyn wirtualnych z systemem Windows 10 na platformie Azure bez konieczności płacenia za innej licencji. Aby uzyskać więcej informacji, zobacz [wielodostępnej Hosting w systemie Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> W tym artykule dowiesz się, aby zaimplementować korzyść licencjonowania dla Windows 10 Pro obrazów w portalu Azure Marketplace.
> - Windows 7, 8.1 i 10 obrazy Enterprise (x64) w portalu Azure Marketplace do subskrypcji MSDN, można znaleźć na stronie [klienta Windows na platformie Azure w scenariuszach tworzenia i testowania](client-images.md)
> - Licencjonowanie korzyści z systemu Windows Server, można znaleźć na stronie [hybrydowego platformy Azure, użyj korzyści dla obrazów systemu Windows Server](hybrid-use-benefit-licensing.md).
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Wdrażanie obrazu systemu Windows 10 z witryny Azure Marketplace 
Dla wdrożeń szablonu programu Powershell, interfejsu wiersza polecenia i usługi Azure Resource Manager można znaleźć obrazu systemu Windows 10 za pomocą następujących nazwa_wydawcy, oferty, jednostki sku.

| System operacyjny  |      PublisherName      |  Oferta | SKU |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS2-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2-ProN  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS3-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3-ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Przekazywanie systemu Windows 10 wirtualnego dysku twardego na platformie Azure
Podczas przekazywania wirtualnego dysku twardego uogólnionego systemu Windows 10, należy pamiętać, że system Windows 10 nie ma konta wbudowanego konta administratora, domyślnie włączone. Aby włączyć wbudowanego konta administratora, obejmują następujące polecenia jako część rozszerzenia niestandardowego skryptu.

```powershell
Net user <username> /active:yes
```

Poniższy fragment kodu programu powershell jest do oznaczania wszystkich kont administratora jako aktywny, włącznie z wbudowanym kontem administratora. W tym przykładzie jest przydatne, jeśli nazwa wbudowanego konta administratora jest nieznany.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Więcej informacji: 
* [Jak przekazać wirtualny dysk twardy na platformie Azure](upload-generalized-managed.md)
* [Jak przygotować wirtualny dysk twardy Windows do przekazania na platformę Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Wdrażanie systemu Windows 10 z praw hostingu wielodostępnego
Upewnij się, że masz [zainstalowany i skonfigurowany najnowszą wersję programu Azure PowerShell](/powershell/azure/overview). Po przygotowaniu wirtualnego dysku twardego przekazanie dysku VHD do konta usługi Azure Storage za pomocą `Add-AzVhd` polecenia cmdlet w następujący sposób:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Wdrażanie przy użyciu wdrażania usługi Azure Resource Manager szablon** w szablonach usługi Resource Manager, dodatkowy parametr dla `licenseType` może być określony. Przeczytaj więcej o [Tworzenie szablonów usługi Azure Resource Manager](../../resource-group-authoring-templates.md). Po utworzeniu wirtualnego dysku twardego przekazany na platformę Azure, Edytuj szablon usługi Resource Manager obejmują typ licencji w ramach dostawcy obliczeń i wdróż szablon, jak zwykle:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Wdrażanie przy użyciu programu PowerShell** podczas wdrażania maszyny Wirtualnej systemu Windows Server za pomocą programu PowerShell, ma dodatkowy parametr dla `-LicenseType`. Po utworzeniu wirtualnego dysku twardego przekazany na platformę Azure, możesz utworzyć maszynę Wirtualną przy użyciu `New-AzVM` i określić typ licencjonowania w następujący sposób:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Upewnij się, że Twoja maszyna wirtualna wykorzystywanych korzyść z licencjonowania
Po wdrożeniu maszyny Wirtualnej za pośrednictwem metody wdrażania programu PowerShell lub Menedżera zasobów Sprawdź typ licencji z `Get-AzVM` w następujący sposób:
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Rezultat jest podobny do poniższego przykładu w systemie Windows 10 z typem odpowiednią licencję:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

To różni się w danych wyjściowych za pomocą następujących maszyn wirtualnych wdrożyć bez Licencjonowanie korzyści z używania hybrydowej platformy Azure, takie jak maszyny Wirtualnej wdrożonej bezpośrednio z galerii platformy Azure:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Dodatkowe informacje na temat dołączania usługi Azure AD
>[!NOTE]
>Platforma Azure udostępnia wszystkie Windows maszyn wirtualnych przy użyciu wbudowanego konta administratora, którego nie można użyć do dołączenia do usługi AAD. Na przykład *Ustawienia > konta > dostęp do pracy lub nauki > i połącz* nie będzie działać. Należy utworzyć i zaloguj się jako drugiego konta administratora w celu ręcznego sprzężenia Azure AD. Można również skonfigurować usługi Azure AD przy użyciu pakietu aprowizacyjnego, użyj tego linku jest *następne kroki* sekcji, aby dowiedzieć się więcej.
>
>

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [Konfigurowanie VDA dla systemu Windows 10](https://docs.microsoft.com/windows/deployment/vda-subscription-activation)
- Dowiedz się więcej o [wielodostępnej Hosting w systemie Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)


