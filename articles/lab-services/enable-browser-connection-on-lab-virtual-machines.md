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
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 86304fc7776f49c999924b8609f2d26120cee372
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549079"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Włączanie połączenia przeglądarki na maszynach wirtualnych w laboratorium 

DevTest Labs integruje się z [usługą Azure Bastion](https://docs.microsoft.com/azure/bastion/), która umożliwia łączenie się z maszynami wirtualnymi za pośrednictwem przeglądarki. Najpierw należy włączyć połączenie przeglądarki na maszynach wirtualnych laboratorium.

Jako właściciel laboratorium możesz włączyć dostęp do wszystkich maszyn wirtualnych w laboratorium za pośrednictwem przeglądarki. Nie potrzebujesz dodatkowego klienta, agenta ani oprogramowania. Usługa Azure Bastion zapewnia bezpieczną i bezproblemową łączność RDP/SSH z maszynami wirtualnymi bezpośrednio w witrynie Azure portal za pomocą protokołu TLS. Po nawiązaniu połączenia za pośrednictwem usługi Azure Bastion maszyny wirtualne nie potrzebują publicznego adresu IP. Aby uzyskać więcej informacji, zobacz [Co to jest Bastion platformy Azure?](../bastion/bastion-overview.md)

> [!NOTE]
> Włączanie połączenia przeglądarki na maszynach wirtualnych laboratorium jest w wersji zapoznawczej.

W tym artykule pokazano, jak włączyć połączenie przeglądarki na maszynach wirtualnych laboratorium.

## <a name="prerequisites"></a>Wymagania wstępne 
Albo wdrożyć hosta bastionu w istniejącej sieci wirtualnej laboratorium **(OR)** połączyć laboratorium z bastionu skonfigurowane sieci wirtualnej. 

Aby dowiedzieć się, jak wdrożyć host bastionu w sieci wirtualnej, zobacz [Tworzenie hosta bastionu platformy Azure (wersja zapoznawcza)](../bastion/bastion-create-host-portal.md). Podczas tworzenia hosta bastionu wybierz sieć wirtualną laboratorium. 

Aby dowiedzieć się, jak połączyć laboratorium z siecią wirtualną skonfigurowaną w bastionie, zobacz [Konfigurowanie sieci wirtualnej w laboratoriach devtest platformy Azure.](devtest-lab-configure-vnet.md) Wybierz w sieci wirtualnej, która ma host bastion wdrożony i **AzureBastionSubnet** w nim. Oto szczegółowe kroki: 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz **pozycję Wszystkie usługi** w lewym menu nawigacyjnym. 
1. Wybierz z listy pozycję **DevTest Labs**. 
1. Z listy laboratoriów wybierz *laboratorium*. 

    > [!NOTE]
    > Bastion platformy Azure jest obecnie w wersji zapoznawczej. Jest ograniczona do następujących regionów: Zachodnie stany USA, Wschodnie stany USA, Europa Zachodnia, Południowo-Środkowe STANY USA, Australia Wschodnia i Japonia Wschodnia. Utwórz laboratorium w jednym z tych regionów, jeśli laboratorium nie jest w jednym z nich. 
1. Wybierz **pozycję Konfiguracja i zasady** w sekcji **Ustawienia** w menu po lewej stronie. 
1. Wybierz **opcję Sieci wirtualne**.
1. Wybierz **pozycję Dodaj** z paska narzędzi. 
1. Wybierz w sieci **wirtualnej,** która ma host bastionu wdrożony. 
1. Wybierz podsieć: **AzureBastionSubnet**. 

    ![Podsieć](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
1. Wybierz **opcję Użyj w tworzeniu maszyny wirtualnej.** 
1. Wybierz pozycję **Zapisz** na pasku narzędzi. 
1. Jeśli masz starąnetę wirtualną dla laboratorium, usuń ją, wybierając **...*  i **Usuń**. 

## <a name="enable-browser-connection"></a>Włączanie połączenia z przeglądarką 

Po skonfigurowaniu sieci wirtualnej bastionu wewnątrz laboratorium, jako właściciel laboratorium, można włączyć połączenie przeglądarki na maszynach wirtualnych laboratorium.

Aby włączyć łączenie się z przeglądarką na maszynach wirtualnych w laboratorium, wykonaj następujące kroki:

1. W witrynie Azure portal przejdź do *laboratorium*.
1. Wybierz **opcję Konfiguracja i zasady**.
1. W **obszarze Ustawienia**wybierz pozycję **Połącz przeglądarkę (Podgląd)**.

![Włączanie połączenia z przeglądarką](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Następne kroki
Zobacz następujący artykuł, aby dowiedzieć się, jak połączyć się z maszynami wirtualnymi za pomocą przeglądarki: [Łączenie się z maszynami wirtualnymi za pośrednictwem przeglądarki](connect-virtual-machine-through-browser.md)