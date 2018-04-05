---
title: Wdrażanie systemu Windows 10 na platformie Azure z wielodostępnej Hosting uprawnień
description: Dowiedz się, jak zmaksymalizować korzyści programu Windows Software Assurance w celu przełączenia lokalnej licencji Azure
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: timlt
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: c157fd8fded40f08c997c29ef6761f3c09dbd282
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Wdrażanie systemu Windows 10 na platformie Azure z wielodostępnej Hosting uprawnień 
W przypadku klientów z systemem Windows 10 Enterprise E3/E5 dla poszczególnych użytkowników lub dostęp do pulpitu wirtualnego systemu Windows na użytkownika (licencji subskrypcji użytkownika lub licencji subskrypcji użytkownika dodatku) wielodostępnej Hosting praw dla systemu Windows 10 pozwala załadować licencji systemu Windows 10 do chmury i uruchom maszyny wirtualne systemu Windows 10 na platformie Azure, bez płatności dla innej licencji. Aby uzyskać więcej informacji, zobacz [wielodostępnej Hosting dla systemu Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> W tym artykule opisano Implementowanie asysty licencjonowania dla obrazów systemu Windows 10 Desktop Pro w portalu Azure Marketplace.
> - Windows 7, 8.1, 10 Enterprise (x64) obrazów w witrynie Azure Marketplace subskrypcji MSDN, można znaleźć na stronie [klienta systemu Windows na platformie Azure scenariusze tworzenia/testowania](client-images.md)
> - W systemie Windows Server licencjonowania korzyści, zapoznaj się [hybrydowego Azure użyć korzyści dla obrazów systemu Windows Server](hybrid-use-benefit-licensing.md).
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Wdrażanie obrazu systemu Windows 10 z portalu Azure Marketplace 
Dla wdrożenia szablonu programu Powershell, interfejsu wiersza polecenia i usługi Azure Resource Manager można znaleźć obrazu systemu Windows 10 z następujących publishername, oferty, jednostki sku.

| System operacyjny  |      PublisherName      |  Oferta | SKU |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS2-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2-ProN  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS3-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3-ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Przekazywanie systemu Windows 10 dysku VHD na platformę Azure
Podczas przekazywania wirtualnego dysku twardego uogólnionego systemu Windows 10, należy pamiętać, że system Windows 10 nie ma wbudowane konto administratora domyślnie włączone. Aby włączyć wbudowanego konta administratora, obejmują następujące polecenie w ramach rozszerzenia niestandardowego skryptu.

```powershell
Net user <username> /active:yes
```

Poniższy fragment kodu programu powershell jest oznaczenie wszystkich kont administratora jako aktywny, w tym wbudowane konto administratora. W tym przykładzie jest przydatne, jeśli nazwa wbudowanego konta administratora jest nieznany.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Więcej informacji: 
* [Sposób przekazywania wirtualnego dysku twardego na platformie Azure](upload-generalized-managed.md)
* [Jak przygotować dysku VHD systemu Windows, aby przekazać do usługi Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Wdrażanie systemu Windows 10 z prawami obsługi Wielodostępności
Upewnij się, że masz [zainstalowany i skonfigurowany najnowsze programu Azure PowerShell](/powershell/azure/overview). Po przygotowaniu dysk VHD, przekazanie dysku VHD do używania konta usługi Azure Storage `Add-AzureRmVhd` polecenia cmdlet w następujący sposób:

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Wdrażanie przy użyciu wdrażania usługi Azure Resource Manager szablonu** w szablonach usługi Resource Manager dodatkowy parametr dla `licenseType` można określić. Możesz przeczytać dodatkowe informacje [tworzenia szablonów usługi Azure Resource Manager](../../resource-group-authoring-templates.md). Po utworzeniu dysk VHD przekazany do platformy Azure, Edytuj szablon Menedżera zasobów, aby dołączyć typ licencji w ramach dostawcy obliczeń i wdrażania szablonu w zwykły:
```json
"properties": {  
   "licenseType": "Windows_Client",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

**Wdrażanie za pomocą programu PowerShell** podczas wdrażania maszyny Wirtualnej systemu Windows Server za pomocą programu PowerShell, masz dodatkowy parametr dla `-LicenseType`. Po utworzeniu dysk VHD przekazany do platformy Azure, utwórz maszynę Wirtualną przy użyciu `New-AzureRmVM` i określić typ licencjonowania w następujący sposób:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Sprawdź, czy maszyna wirtualna jest wykorzystania asysty licencjonowania
Po wdrożeniu maszyny Wirtualnej za pomocą metody wdrożenia programu PowerShell lub Menedżera zasobów Sprawdź typ licencji z `Get-AzureRmVM` w następujący sposób:
```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Wynik jest podobny do poniższego przykładu w systemie Windows 10 z typem odpowiednich licencji:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Output to różni się z maszyną Wirtualną następujące wdrożenie bez licencjonowania Azure hybrydowego Użyj korzyści, takich jak wdrożyć bezpośrednio z poziomu galerii Azure maszyny Wirtualnej:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Dodatkowe informacje na temat przyłączania usługi Azure AD
>[!NOTE]
>Azure udostępnia wszystkie maszyny wirtualne systemu Windows z wbudowanego konta administratora, którego nie można przyłączyć usługi AAD. Na przykład *Ustawienia > konta > dostępu pracy lub nauki > + Połącz* nie będzie działać. Należy utworzyć i zaloguj się jako drugiego konta administratora do przyłączenia ręcznie usługi Azure AD. Można również skonfigurować usługi Azure AD przy użyciu pakietu inicjowania obsługi administracyjnej, użyj tego linku jest *następne kroki* sekcji, aby dowiedzieć się więcej.
>
>

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [Konfigurowanie VDA dla systemu Windows 10](https://docs.microsoft.com/windows/deployment/vda-subscription-activation)
- Dowiedz się więcej o [wielodostępnej Hosting dla systemu Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)


