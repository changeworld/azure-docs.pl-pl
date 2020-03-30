---
title: Stosowanie licencji systemu Windows do obsługi sesji maszyn wirtualnych — Azure
description: W tym artykule opisano sposób stosowania licencji systemu Windows dla maszyn wirtualnych pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: ChristianMontoya
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: 2543dd12e8a75a038a1fc04371b8c562ef696e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254237"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Stosowanie licencji systemu Windows do obsługi maszyn wirtualnych hosta sesji

Klienci, którzy mają odpowiednie licencje na uruchamianie obciążeń pulpitu wirtualnego systemu Windows, mogą zastosować licencję systemu Windows do swoich maszyn wirtualnych hosta sesji i uruchomić je bez płacenia za inną licencję. Aby uzyskać więcej informacji, zobacz [Ceny pulpitu wirtualnego systemu Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>Sposoby korzystania z licencji pulpitu wirtualnego systemu Windows
Licencjonowanie pulpitu wirtualnego systemu Windows umożliwia zastosowanie licencji do dowolnej maszyny wirtualnej systemu Windows lub systemu Windows Server, która jest zarejestrowana jako host sesji w puli hostów i odbiera połączenia z użytkownikami. Ta licencja nie ma zastosowania do maszyn wirtualnych, które są uruchomione jako serwery udziału plików, kontrolery domeny i tak dalej.

Licencja pulpitu wirtualnego systemu Windows jest kilka sposobów użycia:
- Za pomocą usługi Azure Marketplace można utworzyć pulę hostów i jej maszyny wirtualne [hosta](./create-host-pools-azure-marketplace.md)sesji. Maszyny wirtualne utworzone w ten sposób automatycznie mają zastosowaną licencję.
- Za pomocą [szablonu Usługi GitHub Azure Resource Manager](./create-host-pools-arm-template.md)można utworzyć pulę hostów i jej maszyny wirtualne hosta sesji. Maszyny wirtualne utworzone w ten sposób automatycznie mają zastosowaną licencję.
- Licencję można zastosować do istniejącej maszyny wirtualnej hosta sesji. Aby to zrobić, należy najpierw wykonać instrukcje w [Tworzenie puli hostów z programem PowerShell,](./create-host-pools-powershell.md) aby utworzyć pulę hostów i skojarzone maszyny wirtualne, a następnie wróć do tego artykułu, aby dowiedzieć się, jak zastosować licencję.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Stosowanie licencji systemu Windows do maszyny Wirtualnej hosta sesji
Upewnij się, że [zainstalowano i skonfigurowano najnowszą usługę Azure PowerShell](/powershell/azure/overview). Uruchom następujące polecenie cmdlet programu PowerShell, aby zastosować licencję systemu Windows:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Sprawdź, czy maszyna wirtualna hosta sesji korzysta z korzyści wynikających z licencjonowania
Po wdrożeniu maszyny Wirtualnej uruchom tę polecenie cmdlet ot sprawdź typ licencji:
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

Maszyna wirtualna hosta sesji z zastosowaną licencją systemu Windows pokaże coś takiego:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Maszyny wirtualne bez zastosowanej licencji systemu Windows pokażą coś takiego:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Uruchom następujące polecenie cmdlet, aby wyświetlić listę wszystkich maszyn wirtualnych hosta sesji, które mają licencję systemu Windows zastosowaną w ramach subskrypcji platformy Azure:

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```
