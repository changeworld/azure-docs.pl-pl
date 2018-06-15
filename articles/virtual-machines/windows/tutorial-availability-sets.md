---
title: Samouczek — wysoka dostępność dla maszyn wirtualnych z systemem Windows na platformie Azure | Microsoft Docs
description: Z tego samouczka dowiesz się, jak za pomocą programu Azure PowerShell wdrażać maszyny wirtualne o wysokiej dostępności w zestawach dostępności
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: ca2c28a67b652631fc839a5445061ed89cc9197d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32190881"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-azure-powershell"></a>Samouczek: tworzenie i wdrażanie maszyn wirtualnych o wysokiej dostępności za pomocą programu Azure PowerShell

W tym samouczku dowiesz się, jak zwiększyć dostępność i niezawodność rozwiązań korzystających z maszyn wirtualnych na platformie Azure przy użyciu funkcji zestawów dostępności. Zestawy dostępności zapewniają rozproszenie maszyn wirtualnych wdrożonych na platformie Azure pomiędzy wieloma izolowanymi węzłami sprzętowymi w klastrze. Dzięki temu ewentualne awarie sprzętowe lub błędy oprogramowania na platformie Azure będą miały wpływ tylko na część maszyn wirtualnych, a całe rozwiązanie nadal będzie dostępne i funkcjonalne.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zestawu dostępności
> * Tworzenie maszyny wirtualnej w zestawie dostępności
> * Sprawdzanie dostępnych rozmiarów maszyn wirtualnych
> * Sprawdzanie usługi Azure Advisor

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek będzie wymagał modułu programu Azure PowerShell w wersji 5.7.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="availability-set-overview"></a>Zestaw dostępności — omówienie

Zestaw dostępności to dostępna na platformie Azure funkcja grupowania logicznego, zapewniająca izolację zawartych w tej grupie maszyn wirtualnych wdrożonych w centrum danych platformy Azure. Maszyny wirtualne platformy Azure umieszczone w zestawie dostępności korzystają z wielu serwerów fizycznych, regałów obliczeniowych, jednostek magazynowych i przełączników sieciowych. Ewentualne awarie sprzętowe lub błędy oprogramowania na platformie Azure będą miały wpływ tylko na część maszyn wirtualnych, a cała aplikacja nadal będzie działała i pozostanie dostępna dla klientów. Zestawy dostępności stanowią niezbędną funkcję podczas tworzenia niezawodnych rozwiązań w chmurze.

Rozważmy typowe rozwiązanie z użyciem maszyn wirtualnych, obejmujące cztery serwery internetowe frontonu oraz 2 maszyny wirtualne zaplecza, na których jest hostowana baza danych. Przed wdrożeniem maszyn wirtualnych na platformie Azure należałoby w takim przypadku zdefiniować dwa zestawy dostępności: jeden dla warstwy internetowej, a drugi dla warstwy bazy danych. Podczas tworzenia nowej maszyny wirtualnej można określić zestaw dostępności jako parametr polecenia az vm create, a platforma Azure automatycznie zapewni izolację maszyn wirtualnych tworzonych w ramach tego zestawu dostępności na wielu fizycznych zasobach sprzętowych. W przypadku problemu ze sprzętem fizycznym, na którym jest uruchomiona jedna z maszyn wirtualnych serwera internetowego lub serwera bazy danych, masz pewność, że pozostałe wystąpienia maszyn wirtualnych serwera internetowego i bazy danych będą nadal działać, ponieważ korzystają z innego sprzętu.

Zestawy dostępności umożliwiają wdrażanie niezawodnych rozwiązań z użyciem maszyn wirtualnych na platformie Azure.

## <a name="create-an-availability-set"></a>Tworzenie zestawu dostępności

Aby utworzyć zestaw dostępności, użyj polecenia [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). W tym przykładzie należy ustawić liczbę domen aktualizacji i domen błędów na *2* dla zestawu dostępności o nazwie *myAvailabilitySet* w grupie zasobów *myResourceGroupAvailability*.

Utwórz grupę zasobów.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAvailability -Location EastUS
```

Utwórz zarządzany zestaw dostępności przy użyciu polecenia [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) z parametrem `-sku aligned`.

```azurepowershell-interactive
New-AzureRmAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Tworzenie maszyn wirtualnych w zestawie dostępności
Aby zapewnić właściwe rozproszenie maszyn wirtualnych na sprzęcie, należy utworzyć je w ramach zestawu dostępności. Nie można dodać istniejącej, wcześniej utworzonej maszyny wirtualnej do zestawu dostępności. 

Sprzęt w jednej lokalizacji jest podzielony na wiele domen aktualizacji i domen błędów. **Domena aktualizacji** to grupa maszyn wirtualnych i używanego przez nie sprzętu fizycznego, które mogą być jednocześnie ponownie uruchamiane. Maszyny wirtualne w jednej **domenie błędów** korzystają ze wspólnego magazynu oraz ze wspólnego źródła zasilania i przełącznika sieciowego. 

Podczas tworzenia maszyny wirtualnej przy użyciu polecenia [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) parametr `-AvailabilitySetName` umożliwia określenie nazwy zestawu dostępności.

Najpierw ustaw nazwę użytkownika i hasło administratora maszyny wirtualnej przy użyciu polecenia [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Następnie utwórz dwie maszyny wirtualne w zestawie dostępności, używając polecenia [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzureRmVm `
        -ResourceGroupName "myResourceGroupAvailability" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -PublicIpAddressName "myPublicIpAddress$i" `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred
}
```

Parametr `-AsJob` umożliwia tworzenie maszyny wirtualnej w tle, co powoduje powrót do wiersza polecenia programu PowerShell. Możesz wyświetlić szczegóły zadań w tle, używając polecenia cmdlet `Job`. Utworzenie i skonfigurowanie obu maszyn wirtualnych może potrwać kilka minut. Po zakończeniu powstaną dwie maszyny wirtualne rozproszone między wiele elementów sprzętowych. 

Możesz wyświetlić zestaw dostępności w portalu, przechodząc do pozycji Grupy zasobów > myResourceGroupAvailability > myAvailabilitySet, aby zobaczyć, w jaki sposób maszyny wirtualne są rozmieszczone w dwóch domenach aktualizacji i błędów.

![Zestaw dostępności w portalu](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Sprawdzanie dostępnych rozmiarów maszyn wirtualnych 

Możesz później dodać więcej maszyn wirtualnych do zestawu dostępności, ale potrzebujesz do tego informacji o rozmiarach maszyn wirtualnych udostępnianych przez sprzęt. Użyj polecenia [Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize), aby wyświetlić listę wszystkich rozmiarów dostępnych w klastrze sprzętowym zestawu dostępności.

```azurepowershell-interactive
Get-AzureRmVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Sprawdzanie usługi Azure Advisor 

Możesz także uzyskać dodatkowe informacje na temat sposobów poprawy dostępności maszyn wirtualnych, korzystając z usługi Azure Advisor. Usługa Azure Advisor ułatwia stosowanie najlepszych rozwiązań w celu zoptymalizowania wdrożeń platformy Azure. Analizuje konfigurację zasobów i dane telemetryczne dotyczące użycia, a następnie zaleca rozwiązania, które mogą pomóc w zapewnieniu wysokiej dostępności, bezpieczeństwa, wydajności i efektywności kosztowej zasobów platformy Azure.

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com), wybierz pozycję **Wszystkie usługi**, a następnie wpisz **Advisor**. Pulpit nawigacyjny usługi Advisor będzie zawierał spersonalizowane zalecenia dotyczące wybranej subskrypcji. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Advisor](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zestawu dostępności
> * Tworzenie maszyny wirtualnej w zestawie dostępności
> * Sprawdzanie dostępnych rozmiarów maszyn wirtualnych
> * Sprawdzanie usługi Azure Advisor

Przejdź do następnego samouczka, aby poznać zestawy skalowania maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Tworzenie zestawu skalowania maszyn wirtualnych](tutorial-create-vmss.md)


