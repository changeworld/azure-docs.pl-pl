---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 32f533d06b7db0284459951e65f9c04fe0bb0285
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485362"
---
Zestaw dostępności, pomaga zachować maszynach wirtualnych, które jest dostępne podczas przestojów, takich jak podczas konserwacji. Wprowadzenie do co najmniej dwie podobnie skonfigurowane maszyny wirtualne w zestawie dostępności tworzy nadmiarowość niezbędne do zapewnienia dostępności aplikacji lub usług, których maszyna wirtualna działa. Aby uzyskać szczegółowe informacje o tym, jak to działa, zobacz [Zarządzanie dostępnością maszyn wirtualnych][Manage the availability of virtual machines].

Jest najlepszym rozwiązaniem jest do użycia zarówno punkty końcowe równoważenia obciążenia, jak i zestawy dostępności, aby mieć pewność, że aplikacja jest zawsze dostępna i działa wydajnie. Aby uzyskać szczegółowe informacje na temat punktów końcowych z równoważeniem obciążenia, zobacz [równoważenia obciążenia dla usług infrastruktury platformy Azure][Load balancing for Azure infrastructure services].

Możesz dodać klasycznych maszyn wirtualnych w zestawie przy użyciu jednej z dwóch opcji dostępności:

* [Opcja 1: Tworzenie maszyny wirtualnej i zestawu dostępności w tym samym czasie][Option 1: Create a virtual machine and an availability set at the same time]. Następnie należy dodać nowe maszyny wirtualne do zestawu, podczas tworzenia tych maszyn wirtualnych.
* [Opcja 2: Dodaj istniejącą maszynę wirtualną do zestawu dostępności][Option 2: Add an existing virtual machine to an availability set].

> [!NOTE]
> W modelu klasycznym maszyn wirtualnych, które chcesz umieścić w tym samym zestawie dostępności muszą należeć do tej samej usługi w chmurze.
> 
> 

## <a id="createset"> </a>Opcja 1: Tworzenie maszyny wirtualnej i zestawu dostępności w tym samym czasie
W tym celu można użyć witryny Azure portal lub poleceń programu Azure PowerShell.

Aby użyć witryny Azure portal:

1. Jeśli jeszcze tego nie zrobiono, zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij przycisk **Utwórz zasób** > **obliczenia**.
3. Wybierz obraz maszyny wirtualnej portalu Marketplace, którego chcesz użyć. Można utworzyć maszyny wirtualnej z systemem Linux lub Windows.
4. Dla wybranej maszyny wirtualnej, sprawdź, czy w modelu wdrażania jest równa **klasycznego** a następnie kliknij przycisk **Create**
   
    ![Alternatywny tekst obrazu](./media/virtual-machines-common-classic-configure-availability/ChooseClassicModel.png)
5. Wprowadź nazwę maszyny wirtualnej, nazwa użytkownika i hasło (dla maszyn Windows) lub klucza publicznego SSH (w przypadku maszyn z systemem Linux). 
6. Wybieranie rozmiaru maszyny Wirtualnej, a następnie kliknij przycisk **wybierz** aby kontynuować.
7. Wybierz **opcjonalna konfiguracja > zestaw dostępności**, i chcesz dodać maszyny wirtualnej wybierz zestawu dostępności.
   
    ![Alternatywny tekst obrazu](./media/virtual-machines-common-classic-configure-availability/ChooseAvailabilitySet.png) 
8. Przejrzyj ustawienia konfiguracji. Gdy wszystko będzie gotowe, kliknij przycisk **Utwórz**.
9. Gdy platforma Azure tworzy maszynę wirtualną, możesz śledzić postęp w obszarze **maszyn wirtualnych** w menu Centrum.

Aby utworzyć maszynę wirtualną platformy Azure i dodać go do nowego lub istniejącego dostupnosti, należy użyć poleceń programu Azure PowerShell, zobacz [Użyj programu Azure PowerShell do tworzenia i wstępne konfigurowanie maszyn wirtualnych z systemem Windows](../articles/virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a id="addmachine"> </a>Opcja 2: Dodaj istniejącą maszynę wirtualną do zestawu dostępności
W witrynie Azure portal możesz dodać istniejące klasyczne maszyny wirtualne do istniejących dostępności zestawu lub utworzyć nową dla nich. (Należy pamiętać, że maszyny wirtualne w tym samym zestawie dostępności muszą należeć do tej samej usługi w chmurze). Kroki są prawie takie same. Za pomocą programu Azure PowerShell można dodać maszyny wirtualnej do istniejącego zestawu dostępności.

1. Jeśli jeszcze tego nie zrobiono, zaloguj się do [witryny Azure portal](https://portal.azure.com).
2. W menu po lewej stronie, kliknij polecenie **maszyny wirtualne (klasyczne)**.
   
    ![Alternatywny tekst obrazu](./media/virtual-machines-common-classic-configure-availability/ChooseClassicVM.png)
3. Z listy maszyn wirtualnych wybierz nazwę maszyny wirtualnej, która ma zostać dodany do zestawu.
4. Wybierz **zestawu dostępności** z maszyny wirtualnej **ustawienia**.
   
    ![Alternatywny tekst obrazu](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetSettings.png)
5. Wybierz zestaw dostępności, który chcesz dodać maszynę wirtualną w celu. Maszyna wirtualna musi należeć do tej samej usługi w chmurze jako zestawu dostępności.
   
    ![Alternatywny tekst obrazu](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetPicker.png)
6. Kliknij pozycję **Zapisz**.

Aby użyć poleceń programu Azure PowerShell, otwórz sesję programu PowerShell Azure na poziomie administratora i uruchom następujące polecenie. Dla symboli zastępczych (takie jak &lt;VmCloudServiceName&gt;), Zamień wszystko w ramach oferty, w tym < a > znaków długości, poprawne nazwy.

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

> [!NOTE]
> Maszyna wirtualna może dysponować ponownego uruchomienia, aby zakończyć dodawanie go do zestawu dostępności.
> 
> 

<!-- LINKS -->
[Option 1: Create a virtual machine and an availability set at the same time]: #createset
[Option 2: Add an existing virtual machine to an availability set]: #addmachine

[Load balancing for Azure infrastructure services]: ../articles/virtual-machines/virtual-machines-linux-load-balance.md
[Manage the availability of virtual machines]:../articles/virtual-machines/linux/manage-availability.md

[Create a virtual machine running Windows]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md
[Virtual Network overview]: ../articles/virtual-network/virtual-networks-overview.md

