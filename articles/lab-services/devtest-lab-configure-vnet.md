---
title: Konfigurowanie sieci wirtualnej w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować istniejącą sieć wirtualną i podsieć, a następnie używać ich na maszynie wirtualnej w usłudze Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 8fb3b4ac748fcae2e3aad5b3bfb2a893340dc61a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60694818"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Konfigurowanie sieci wirtualnej w usłudze Azure DevTest Labs
Zgodnie z opisem w artykule [Dodawanie maszyny Wirtualnej do laboratorium](devtest-lab-add-vm.md), podczas tworzenia maszyny Wirtualnej w laboratorium, można określić skonfigurowanej sieci wirtualnej. Na przykład może być konieczne dostępu do zasobów sieci firmowej z maszynami wirtualnymi przy użyciu sieci wirtualnej, która została skonfigurowana przy użyciu usługi ExpressRoute lub sieci VPN typu lokacja lokacja.

W tym artykule opisano sposób dodawania istniejącej sieci wirtualnej do ustawień sieci wirtualnej laboratorium, aby były one dostępne do wyboru podczas tworzenia maszyn wirtualnych.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Konfigurowanie sieci wirtualnej dla laboratorium przy użyciu witryny Azure portal
W poniższych krokach objaśniono przez proces dodawania istniejącej sieci wirtualnej (i podsieci) do laboratorium, dzięki czemu można używać podczas tworzenia maszyny Wirtualnej, w tym samym środowisku laboratoryjnym. 

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.
1. Z listy labs wybierz żądane laboratorium. 
1. W okienku głównym laboratorium, wybierz **konfiguracji i zasad**.

    ![Dostęp do konfiguracji i zasad laboratorium](./media/devtest-lab-configure-vnet/policies-menu.png)
1. W **zasobów zewnętrznych** zaznacz **sieci wirtualne**. A także domyślnej sieci wirtualnej, utworzyć dla laboratorium, zostanie wyświetlona lista sieci wirtualnych, skonfigurowany w bieżącym środowisku laboratoryjnym. 
1. Wybierz pozycję **+ Dodaj**.
   
    ![Dodaj istniejącą sieć wirtualną do środowiska laboratoryjnego](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. Na **sieć wirtualna** okienku wybierz **[Wybierz sieć wirtualną]** .
   
    ![Wybierz istniejącą sieć wirtualną](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. Na **wybierz sieć wirtualną** okienku, wybierz odpowiednią sieć wirtualną. Zostanie wyświetlona lista przedstawiająca wszystkie sieci wirtualne, które znajdują się w tym samym regionie, w ramach subskrypcji, jako laboratorium.
1. Po wybraniu sieci wirtualnej, nastąpi powrót do **sieć wirtualna** okienka. Wybierz podsieć, na liście u dołu.

    ![Lista podsieci](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    Zostanie wyświetlone okienko podsieci laboratorium.

    ![Okienko podsieci laboratorium](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Określ **Nazwa podsieci laboratorium**.
   - Aby zezwolić na podsieci do użycia w laboratorium tworzenia maszyny Wirtualnej, wybierz **użycia podczas tworzenia maszyny wirtualnej**.
   - Aby włączyć [udostępnionego publicznego adresu IP](devtest-lab-shared-ip.md), wybierz opcję **Włącz udostępnionego publicznego adresu IP**.
   - Aby zezwolić na publiczne adresy IP w podsieci, wybierz **zezwolić na publiczny IP tworzenie**.
   - W **maksymalna maszyn wirtualnych na użytkownika** pola, określ maksymalną maszyn wirtualnych dla poszczególnych użytkowników dla każdej podsieci. Jeśli chcesz, aby nieograniczona liczba maszyn wirtualnych, pozostaw to pole puste.
1. Wybierz **OK** aby zamknąć okienko podsieci laboratorium.
1. Wybierz **Zapisz** aby zamknąć okienko sieci wirtualnej.

Teraz, gdy sieć wirtualna jest skonfigurowany, może ona zostać wybrana podczas tworzenia maszyny Wirtualnej. Aby zobaczyć, jak utworzyć Maszynę wirtualną i określ sieć wirtualną, zapoznaj się z artykułem [Dodawanie maszyny Wirtualnej do laboratorium](devtest-lab-add-vm.md). 

Azure [dokumentacja usługi Virtual Network](https://docs.microsoft.com/azure/virtual-network) zawiera więcej informacji o sposobie używania sieci wirtualnych, w tym sposób konfigurowania i zarządzania siecią wirtualną i połączyć ją z siecią lokalną.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Kolejne kroki
Po dodaniu żądanej sieci wirtualnej do środowiska laboratoryjnego, następnym krokiem jest [Dodaj Maszynę wirtualną do środowiska laboratoryjnego](devtest-lab-add-vm.md).

