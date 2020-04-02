---
title: Włączanie połączenia przeglądarki na maszynach wirtualnych usługi Azure DevTest Labs | Dokumenty firmy Microsoft
description: DevTest Labs integruje się teraz z usługą Azure Bastion, jako właściciel laboratorium można włączyć dostęp do wszystkich maszyn wirtualnych laboratorium za pośrednictwem przeglądarki.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: 074d512767769fe434cd4a6d4f4e8e6a88f7ed2a
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549118"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Włączanie połączenia przeglądarki na maszynach wirtualnych w laboratorium 
DevTest Labs integruje się z [usługą Azure Bastion](https://docs.microsoft.com/azure/bastion/), która umożliwia łączenie się z maszynami wirtualnymi za pośrednictwem przeglądarki. Najpierw należy włączyć połączenie przeglądarki na maszynach wirtualnych laboratorium.

Jako właściciel laboratorium możesz włączyć dostęp do wszystkich maszyn wirtualnych w laboratorium za pośrednictwem przeglądarki. Nie potrzebujesz dodatkowego klienta, agenta ani oprogramowania. Usługa Azure Bastion zapewnia bezpieczną i bezproblemową łączność RDP/SSH z maszynami wirtualnymi bezpośrednio w witrynie Azure portal za pomocą protokołu TLS. Po nawiązaniu połączenia za pośrednictwem usługi Azure Bastion maszyny wirtualne nie potrzebują publicznego adresu IP. Aby uzyskać więcej informacji, zobacz [Co to jest Bastion platformy Azure?](../bastion/bastion-overview.md)


W tym artykule pokazano, jak włączyć połączenie przeglądarki na maszynach wirtualnych laboratorium.

## <a name="prerequisites"></a>Wymagania wstępne 
Albo wdrożyć hosta bastionu w istniejącej sieci wirtualnej laboratorium **(OR)** połączyć laboratorium z bastionu skonfigurowane sieci wirtualnej. 

Aby dowiedzieć się, jak wdrożyć host bastionu w sieci wirtualnej, zobacz [Tworzenie hosta bastionu platformy Azure](../bastion/bastion-create-host-portal.md). Podczas tworzenia hosta bastionu wybierz sieć wirtualną laboratorium. 

Najpierw należy utworzyć drugą podsieć w sieci wirtualnej Bastion, ponieważ AzureBastionSubnet nie zezwala na tworzenie zasobów innych niż Bastion w nim. 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>Tworzenie drugiej sieci podrzędnej w sieci wirtualnej Bastion
Nie można tworzyć maszyn wirtualnych w laboratorium w podsieci Bastion platformy Azure. Utwórz inną podsieć w sieci wirtualnej Bastion, jak pokazano na poniższej ilustracji:

![Druga podsieć w sieci wirtualnej Bastion platformy Azure](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>Włączanie tworzenia maszyn wirtualnych w podsieci
Teraz włącz tworzenie maszyn wirtualnych w tej podsieci, wykonując następujące kroki: 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz **pozycję Wszystkie usługi** w lewym menu nawigacyjnym. 
1. Wybierz z listy pozycję **DevTest Labs**. 
1. Z listy laboratoriów wybierz *laboratorium*. 

    > [!NOTE]
    > Usługa Azure Bastion jest teraz ogólnie dostępna w następujących regionach: Zachodnie stany USA, wschodnie stany USA, Europa Zachodnia, Południowo-środkowe stany USA, Australia Wschodnia i Japonia Wschodnia. Utwórz laboratorium w jednym z tych regionów, jeśli laboratorium nie jest w jednym z nich. 
    
1. Wybierz **pozycję Konfiguracja i zasady** w sekcji **Ustawienia** w menu po lewej stronie. 
1. Wybierz **opcję Sieci wirtualne**.
1. Wybierz **pozycję Dodaj** z paska narzędzi. 
1. Wybierz **sieć wirtualną,** w którą wdrożono host bastionu. 
1. Wybierz podsieć dla maszyn wirtualnych, a nie **AzureBastionSubnet**, drugi, który został utworzony wcześniej. Zamknij stronę i otwórz ją ponownie, jeśli nie widzisz podsieci na liście u dołu. 

    ![Włączanie tworzenia maszyn wirtualnych w podsieci](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. Wybierz **opcję Użyj w tworzeniu maszyny wirtualnej.** 
1. Wybierz pozycję **Zapisz** na pasku narzędzi. 
1. Jeśli masz starą sieć wirtualną dla laboratorium, usuń ją, wybierając **...*  i **Usuń**. 

## <a name="enable-browser-connection"></a>Włączanie połączenia z przeglądarką 

Po skonfigurowaniu sieci wirtualnej bastionu wewnątrz laboratorium, jako właściciel laboratorium, można włączyć połączenie przeglądarki na maszynach wirtualnych laboratorium.

Aby włączyć łączenie się z przeglądarką na maszynach wirtualnych w laboratorium, wykonaj następujące kroki:

1. W witrynie Azure portal przejdź do *laboratorium*.
1. Wybierz **opcję Konfiguracja i zasady**.
1. W **obszarze Ustawienia**wybierz pozycję **Połącz przeglądarkę**. Jeśli ta opcja nie jest widoczna, zamknij stronę **Zasady konfiguracji** i otwórz ją ponownie. 

    ![Włączanie połączenia z przeglądarką](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Następne kroki
Zobacz następujący artykuł, aby dowiedzieć się, jak połączyć się z maszynami wirtualnymi za pomocą przeglądarki: [Łączenie się z maszynami wirtualnymi za pośrednictwem przeglądarki](connect-virtual-machine-through-browser.md)
