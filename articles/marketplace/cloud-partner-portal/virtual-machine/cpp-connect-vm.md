---
title: Nawiązywanie połączenia z maszyną wirtualną opartą na Microsoft Azure | Portal Azure Marketplace
description: Wyjaśnia, jak nawiązać połączenie z nową maszyną wirtualną utworzoną na platformie Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: 256952d06e8b417be9ab2bc3c6c8c6857d50e2ea
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73817369"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną opartą na platformie Azure

W tym artykule wyjaśniono, jak nawiązać połączenie z maszynami wirtualnymi utworzonymi na platformie Azure i zalogować się do nich.  Po pomyślnym nawiązaniu połączenia możesz współpracować z maszyną wirtualną tak, jakby była ona zalogowana lokalnie na serwerze hosta. 

## <a name="connect-to-a-windows-based-vm"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Windows

Klient usług pulpitu zdalnego będzie używany do nawiązywania połączenia z maszyną wirtualną opartą na systemie Windows hostowaną na platformie Azure.  Większość wersji systemu Windows natywnie zawiera obsługę protokołu RDP (Remote Desktop Protocol).  W przypadku innych maszyn można znaleźć więcej informacji o klientach w [pulpit zdalny klientach](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).  

W poniższym artykule szczegółowo opisano sposób używania wbudowanej obsługi protokołu RDP systemu Windows do nawiązywania połączenia z maszyną wirtualną: [jak nawiązać połączenie i zalogować się do maszyny wirtualnej platformy Azure z systemem Windows](../../../virtual-machines/windows/connect-logon.md).  

>[!TIP]
> Podczas procesu mogą pojawić się ostrzeżenia dotyczące zabezpieczeń, na przykład że plik RDP pochodzi od nieznanego wydawcy lub nie można zweryfikować poświadczeń użytkownika.  Można bezpiecznie zignorować te ostrzeżenia.


## <a name="connect-to-a-linux-based-vm"></a>Nawiązywanie połączenia z maszyną wirtualną opartą na systemie Linux

Aby można było połączyć maszynę wirtualną z systemem Linux, potrzebny jest klient protokołu Secure Shell Protocol (SSH).  W tej dyskusji zostanie [użyty bezpłatny prywatnego](https://www.ssh.com/ssh/putty/) terminala.

1. W bloku **maszyny wirtualne** [Azure Portal](https://ms.portal.azure.com)wybierz maszynę wirtualną, z którą chcesz nawiązać połączenie.  
2. **Uruchom** maszynę wirtualną, jeśli nie została jeszcze uruchomiona.
3. Kliknij nazwę maszyny wirtualnej, aby otworzyć jej stronę **przeglądu** .
4. Zanotuj publiczny adres IP i nazwę DNS maszyny wirtualnej.  (Jeśli te wartości nie są ustawione, należy [utworzyć interfejs sieciowy](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)

   ![Ustawienia przeglądu maszyny wirtualnej](./media/publishvm_019.png)
 
5. Otwórz aplikację.  
6. W oknie dialogowym Konfiguracja konfiguracji wprowadź adres IP lub nazwę DNS maszyny wirtualnej. 

   ![Podano ustawienia terminalu](./media/publishvm_020.png)
 
7. Kliknij przycisk **Otwórz** , aby otworzyć wystawcy Terminal.  
8. Po wyświetleniu monitu wprowadź nazwę konta i hasło do konta maszyny wirtualnej z systemem Linux. 

   Jeśli występują problemy z połączeniem, zapoznaj się z dokumentacją klienta SSH, na przykład [rozdział 10: typowe komunikaty o błędach](https://www.ssh.com/ssh/putty/putty-manuals/0.68/Chapter10.html#errors).

Aby uzyskać więcej informacji, w tym o sposobach dodawania pulpitu do maszyny wirtualnej z systemem Linux z obsługą administracyjną, zobacz [Instalowanie i konfigurowanie pulpit zdalny do nawiązywania połączenia z maszyną wirtualną z systemem Linux na platformie Azure](../../../virtual-machines/linux/use-remote-desktop.md).


## <a name="stop-unused-vms"></a>Zatrzymaj nieużywane maszyny wirtualne
Weksle platformy Azure dla hostingu maszyn wirtualnych, gdy maszyna wirtualna jest uruchomiona lub jest w *stanie bezczynności*.  W związku z tym najlepszym rozwiązaniem jest zatrzymanie maszyn wirtualnych, które nie są obecnie używane.  Na przykład, test, kopia zapasowa lub wycofane maszyny wirtualne są kandydatami do zamknięcia. Aby zamknąć maszynę wirtualną, wykonaj następujące czynności:

1. W bloku **maszyny wirtualne** wybierz maszynę wirtualną, która ma zostać zatrzymana. 
2. Na pasku narzędzi u góry strony kliknij przycisk **Zatrzymaj** .

   ![Zatrzymywanie maszyny wirtualnej](./media/publishvm_018.png)

Platforma Azure szybko zatrzyma maszynę wirtualną w procesie nazywanym *cofnięciem alokacji*, który nie tylko zamknie system operacyjny na maszynie wirtualnej, ale również zwalnia zasoby sprzętowe i sieciowe, które wcześniej zainicjowano dla niego.

Jeśli chcesz później ponownie uaktywnić zatrzymaną maszynę wirtualną, zaznacz ją i kliknij przycisk **Uruchom** .


## <a name="next-steps"></a>Następne kroki

Po nawiązaniu połączenia zdalnego możesz [skonfigurować maszynę wirtualną](./cpp-configure-vm.md).
