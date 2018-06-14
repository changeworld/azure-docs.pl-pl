---
title: Zmień rozmiar maszyny Wirtualnej systemu Windows w klasycznym modelu wdrażania - Azure | Dokumentacja firmy Microsoft
description: Zmień rozmiar maszyny wirtualnej systemu Windows tworzone w klasycznym modelu wdrażania, przy użyciu programu Azure Powershell.
services: virtual-machines-windows
documentationcenter: ''
author: Drewm3
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: e3038215-001c-406e-904d-e0f4e326a4c7
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/19/2016
ms.author: drewm
ms.openlocfilehash: 4277bc8394c7ba140291e9dc776162e87deab96b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23928244"
---
# <a name="resize-a-windows-vm-created-in-the-classic-deployment-model"></a>Zmień rozmiar maszyny Wirtualnej systemu Windows tworzone w klasycznym modelu wdrażania
W tym artykule przedstawiono sposób zmiany rozmiaru maszyny Wirtualnej systemu Windows, utworzone w klasycznym modelu wdrażania przy użyciu programu Azure Powershell.

Rozważając możliwość zmiany rozmiaru maszyny Wirtualnej, istnieją dwa pojęcia, które kontrolę zakresu rozmiary można zmienić rozmiaru maszyny wirtualnej. Pierwszy koncepcja jest regionu, w którym wdrożonej maszyny Wirtualnej. Listę dostępnych rozmiarów maszyny Wirtualnej w regionie jest na karcie usług strony sieci web, regiony platformy Azure. Drugi koncepcja jest sprzętem fizycznym, w obecnie udostępnia maszyny Wirtualnej. Serwery fizyczne hostingu maszyn wirtualnych są grupowane w klastrach wspólnej sprzętu fizycznego. Metoda zmiany rozmiaru maszyny Wirtualnej różni się w zależności od, jeśli wymagany nowy rozmiar maszyny Wirtualnej jest obsługiwana przez sprzęt klaster obecnie udostępnia maszyny Wirtualnej.

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Możesz również [Zmień rozmiar maszyny Wirtualnej utworzonej w modelu wdrażania usługi Resource Manager](../resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="add-your-account"></a>Dodaj swoje konto
Należy skonfigurować programu Azure PowerShell do pracy z klasycznym zasobów platformy Azure. Wykonaj poniższe kroki, aby skonfigurować programu Azure PowerShell do zarządzania zasobami klasycznego.

1. W wierszu polecenia programu PowerShell wpisz `Add-AzureAccount` i kliknij przycisk **Enter**. 
2. Wpisz adres e-mail skojarzony z subskrypcją platformy Azure i kliknij przycisk **Kontynuuj**. 
3. Wpisz hasło dla swojego konta. 
4. Kliknij przycisk **Zaloguj**. 

## <a name="resize-in-the-same-hardware-cluster"></a>Zmiana rozmiaru w tym samym klastrze sprzętu
Aby zmienić rozmiar maszyny Wirtualnej na rozmiar dostępne w klastrze sprzętu obsługującego maszyny Wirtualnej, wykonaj następujące kroki.

1. Uruchom następujące polecenie programu PowerShell, aby wyświetlić listę dostępnych w klastrze sprzętu obsługującego usługę w chmurze, która zawiera maszynę Wirtualną rozmiarów maszyn wirtualnych.
   
    ```powershell
    Get-AzureService | where {$_.ServiceName -eq "<cloudServiceName>"}
    ```
2. Uruchom następujące polecenia, aby zmienić rozmiar maszyny Wirtualnej.
   
    ```powershell
    Get-AzureVM -ServiceName <cloudServiceName> -Name <vmName> | Set-AzureVMSize -InstanceSize <newVMSize> | Update-AzureVM
    ```

## <a name="resize-on-a-new-hardware-cluster"></a>Zmiana rozmiaru w nowym klastrze sprzętu
Aby zmienić rozmiar maszyny Wirtualnej na rozmiar nie jest dostępna w klastrze sprzętu obsługującego maszynę Wirtualną, chmury usługi i wszystkich maszyn wirtualnych w usłudze w chmurze muszą zostać ponownie utworzone. Każdą usługę w chmurze jest hostowanych w klastrze pojedynczego sprzętu, więc wszystkie maszyny wirtualne w usłudze w chmurze musi mieć rozmiar, który jest obsługiwany w klastrze sprzętu. Poniższe kroki opisano sposób zmiany rozmiaru maszyny Wirtualnej przez usunięcie i ponowne utworzenie usługi w chmurze.

1. Uruchom następujące polecenie programu PowerShell, aby wyświetlić listę dostępnych rozmiarów maszyny Wirtualnej w regionie. 
   
    ```powershell
    Get-AzureLocation | where {$_.Name -eq "<locationName>"}
    ```
2. Zanotuj wszystkie ustawienia konfiguracji dla każdej maszyny Wirtualnej usługi w chmurze, który zawiera można zmienić rozmiaru maszyny Wirtualnej. 
3. Usuń wszystkie maszyny wirtualne w usłudze w chmurze wybranie opcji, aby zachować dyski dla każdej maszyny Wirtualnej.
4. Utwórz ponownie maszynę Wirtualną można zmienić za pomocą żądany rozmiar maszyny Wirtualnej.
5. Utwórz ponownie wszystkich innych maszyn wirtualnych, które były w usłudze w chmurze przy użyciu rozmiaru maszyny Wirtualnej dostępne w klastrze sprzętu teraz obsługującego usługę w chmurze.

Przykładowy skrypt do usuwania i ponowne utworzenie usługi w chmurze przy użyciu nowego rozmiaru maszyny Wirtualnej można znaleźć [tutaj](https://github.com/Azure/azure-vm-scripts). 

## <a name="next-steps"></a>Następne kroki
* [Zmień rozmiar maszyny Wirtualnej utworzonej w modelu wdrażania usługi Resource Manager](../resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

