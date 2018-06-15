---
title: Konfigurowanie sieci wirtualnej w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować istniejącą sieć wirtualną i podsieć, a następnie używać ich na maszynie wirtualnej z platformy Azure DevTest Labs
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
ms.openlocfilehash: 0141ea8a88c0322e6f56cbea56d3a43c923769af
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787409"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Konfigurowanie sieci wirtualnej w usłudze Azure DevTest Labs
Jak wyjaśniono w artykule [dodać maszyny Wirtualnej do laboratorium](devtest-lab-add-vm.md), podczas tworzenia maszyn wirtualnych w laboratorium, można określić skonfigurowanej sieci wirtualnej. Na przykład może być konieczne dostęp do zasobów sieci corpnet z maszyn wirtualnych przy użyciu sieci wirtualnej, który został skonfigurowany za pomocą usługi ExpressRoute i sieci VPN typu lokacja lokacja.

W tym artykule opisano sposób dodawania istniejącej sieci wirtualnej do ustawień sieci wirtualnej laboratorium, aby była ona dostępna do wyboru podczas tworzenia maszyn wirtualnych.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Skonfiguruj sieć wirtualną dla laboratorium przy użyciu portalu Azure
W poniższych krokach objaśniono przez procedurę dodawania istniejącej sieci wirtualnej (i podsieci) do laboratorium, dzięki czemu można użyć podczas tworzenia maszyny Wirtualnej w tym samym laboratorium. 

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **wszystkie usługi**, a następnie wybierz **DevTest Labs** z listy.
1. Z listy labs wybierz żądany laboratorium. 
1. W okienku głównym laboratorium, wybierz **konfiguracji i zasadach**.

    ![Dostęp do konfiguracji i zasadach laboratorium](./media/devtest-lab-configure-vnet/policies-menu.png)
1. W **zasobów zewnętrznych** zaznacz **sieci wirtualnych**. Zostanie wyświetlona lista skonfigurowane dla bieżącego laboratorium sieci wirtualnych oraz sieci wirtualnej domyślny utworzony dla laboratorium. 
1. Wybierz pozycję **+ Dodaj**.
   
    ![Dodawanie istniejącej sieci wirtualnej do laboratorium](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. Na **sieci wirtualnej** okienku wybierz **[Wybierz sieć wirtualną]**.
   
    ![Wybierz istniejącą sieć wirtualną](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. Na **sieci wirtualnej wybierz** okienku, wybierz odpowiednią sieć wirtualną. Zostanie wyświetlona lista przedstawiająca wszystkie sieci wirtualne, które są w tym samym regionie, w ramach subskrypcji jako laboratorium.
1. Po wybraniu sieci wirtualnej, nastąpi powrót do **sieci wirtualnej** okienka. Wybierz z listy u dołu tej podsieci.

    ![Lista podsieci](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    W okienku podsieci laboratorium jest wyświetlany.

    ![Okienko podsieci laboratorium](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Określ **Nazwa podsieci laboratorium**.
   - Aby zezwolić na podsieci do użycia w laboratorium tworzenia maszyny Wirtualnej, wybierz **używany podczas tworzenia maszyny wirtualnej**.
   - Aby włączyć [udostępnionych publicznego adresu IP](devtest-lab-shared-ip.md), wybierz pozycję **Włącz udostępnione publicznego adresu IP**.
   - Aby zezwolić na publiczne adresy IP w podsieci, wybierz **pozwala publicznego adresu IP tworzenie**.
   - W **maksymalna maszyn wirtualnych dla użytkownika** pola, określ maksymalną maszyn wirtualnych dla poszczególnych użytkowników, dla każdej podsieci. Jeśli chcesz nieograniczoną liczbę maszyn wirtualnych, pozostaw to pole puste.
1. Wybierz **OK** aby zamknąć okienko podsieci laboratorium.
1. Wybierz **zapisać** aby zamknąć okienko sieci wirtualnej.

Teraz, gdy skonfigurowano sieci wirtualnej, można wybrać podczas tworzenia maszyny Wirtualnej. Aby sprawdzić, jak utworzyć Maszynę wirtualną i określ sieć wirtualną, zapoznaj się z artykułem [dodać maszyny Wirtualnej do laboratorium](devtest-lab-add-vm.md). 

Azure [dokumentacji sieci wirtualnych](https://docs.microsoft.com/azure/virtual-network) zawiera więcej informacji o sposobie używania sieci wirtualnych, w tym sposób konfigurowania i zarządzania nimi sieci wirtualnej i połącz ją z sieci lokalnej.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Kolejne kroki
Po dodaniu żądanej sieci wirtualnej do laboratorium, następnym krokiem jest [Dodaj Maszynę wirtualną do laboratorium](devtest-lab-add-vm.md).

