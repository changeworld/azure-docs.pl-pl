---
title: Konfigurowanie sieci wirtualnej w laboratoriach devtest platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować istniejącą sieć wirtualną i podsieć i używać ich na maszynie Wirtualnej w laboratoriach devtest azure
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
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 6cf3d2f82c98a3caab47ff48a600316747932b72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70390032"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Konfigurowanie sieci wirtualnej w laboratorium devtest platformy Azure
Jak wyjaśniono w artykule [Dodaj maszynę wirtualną do laboratorium](devtest-lab-add-vm.md), podczas tworzenia maszyny Wirtualnej w laboratorium, można określić skonfigurowaną sieć wirtualną. Na przykład może być konieczne uzyskiwać dostęp do zasobów firmy corpnet z maszyn wirtualnych przy użyciu sieci wirtualnej, która została skonfigurowana za pomocą usługi ExpressRoute lub sieci VPN lokacja-lokacja.

W tym artykule wyjaśniono, jak dodać istniejącą sieć wirtualną do ustawień sieci wirtualnej laboratorium, aby była dostępna do wyboru podczas tworzenia maszyn wirtualnych.

> [!NOTE]
> Aby dowiedzieć się więcej o kosztach związanych z usługą Azure Virtual Network, zobacz [Cennik dla sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md#pricing).

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Konfigurowanie sieci wirtualnej dla laboratorium przy użyciu portalu Azure
Poniższe kroki przebiegają przez dodawanie istniejącej sieci wirtualnej (i podsieci) do laboratorium, dzięki czemu może być używana podczas tworzenia maszyny Wirtualnej w tym samym laboratorium. 

1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**
1. Z listy laboratoriów wybierz żądane laboratorium. 
1. W głównym okienku laboratorium wybierz pozycję **Konfiguracja i zasady**.

    ![Dostęp do konfiguracji i zasad laboratorium](./media/devtest-lab-configure-vnet/policies-menu.png)
1. W sekcji **ZASOBY ZEWNĘTRZNE** wybierz pozycję **Sieci wirtualne**. Zostanie wyświetlona lista sieci wirtualnych skonfigurowanych dla bieżącego laboratorium, a także domyślna sieć wirtualna utworzona dla laboratorium. 
1. Wybierz pozycję **+ Dodaj**.
   
    ![Dodawanie istniejącej sieci wirtualnej do laboratorium](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. W okienku **Sieć wirtualna** wybierz pozycję **[Wybierz sieć wirtualną]**.
   
    ![Wybieranie istniejącej sieci wirtualnej](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. W okienku **Wybierz sieć wirtualną** wybierz żądaną sieć wirtualną. Zostanie wyświetlona lista przedstawiająca wszystkie sieci wirtualne, które znajdują się w tym samym regionie w subskrypcji co laboratorium.
1. Po wybraniu sieci wirtualnej zostaniesz przywrócony do okienka **Sieć wirtualna.** Wybierz podsieć na liście u dołu.

    ![Lista podsieci](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    Zostanie wyświetlone okienko Podsieć laboratorium.

    ![Okienko podsieci Laboratorium](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Określ **nazwę podsieci Laboratorium**.
   - Aby zezwolić na użycie podsieci podczas tworzenia maszyn wirtualnych w laboratorium, wybierz opcję **Użyj w tworzeniu maszyny wirtualnej**.
   - Aby włączyć [udostępniony publiczny adres IP,](devtest-lab-shared-ip.md)wybierz pozycję Włącz udostępniony publiczny adres **IP**.
   - Aby zezwolić na publiczne adresy IP w podsieci, wybierz pozycję **Zezwalaj na publiczne tworzenie adresów IP**.
   - W polu **Maksymalna liczba maszyn wirtualnych na użytkownika** określ maksymalną liczbę maszyn wirtualnych na użytkownika dla każdej podsieci. Jeśli chcesz mieć nieograniczoną liczbę maszyn wirtualnych, pozostaw to pole puste.
1. Wybierz **przycisk OK,** aby zamknąć okienko Podsieć laboratorium.
1. Wybierz **pozycję Zapisz,** aby zamknąć okienko sieci wirtualnej.

Teraz, gdy sieć wirtualna jest skonfigurowana, można ją wybrać podczas tworzenia maszyny Wirtualnej. Aby zobaczyć, jak utworzyć maszynę wirtualną i określić sieć wirtualną, zapoznaj się z [artykułem Dodaj maszynę wirtualną do laboratorium](devtest-lab-add-vm.md). 

[Dokumentacja sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network) platformy Azure zawiera więcej informacji na temat korzystania z sieci wirtualnych, w tym sposobu konfigurowania sieci wirtualnej i zarządzania nią oraz łączenia jej z siecią lokalną.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Następne kroki
Po dodaniu żądanej sieci wirtualnej do laboratorium następnym krokiem jest [dodanie maszyny wirtualnej do laboratorium.](devtest-lab-add-vm.md)

