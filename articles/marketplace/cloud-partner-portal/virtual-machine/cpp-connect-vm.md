---
title: Połącz się z maszyną wirtualną systemu Azure firmy Microsoft | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak połączyć się z nowej maszyny wirtualnej utworzonej na platformie Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: fd68846b9144c3efcc71dec369d64119427758a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744562"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Połącz się z maszyną wirtualną opartych na platformie Azure

W tym artykule wyjaśniono, jak nawiązać połączenie i zaloguj się do maszyn wirtualnych (VM), został utworzony na platformie Azure.  Po pomyślnym nawiązaniu połączenia, można pracować z maszyną Wirtualną tak, jakby był lokalnie zalogowany do swojego serwera hosta. 

## <a name="connect-to-a-windows-based-vm"></a>Łączenie z maszyną Wirtualną z systemem Windows

Klient usług pulpitu zdalnego użyje połączyć się z maszyny Wirtualnej systemu Windows, hostowane na platformie Azure.  Większość wersji systemu Windows zawierają natywną obsługę protokołu remote desktop protocol (RDP).  Dla innych maszyn, możesz znaleźć więcej informacji o klientach w [klientów usług pulpitu zdalnego](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).  

Następujący artykuł szczegółowo opisuje sposób użyć wbudowanej obsługi Windows protokołu RDP, aby nawiązać połączenie z maszyną Wirtualną: [Jak połączyć i zaloguj się na maszynie wirtualnej platformy Azure, systemem Windows](../../../virtual-machines/windows/connect-logon.md).  

>[!TIP]
> Mogą być wyświetlane ostrzeżenia o zabezpieczeniach podczas procesu, na przykład, plik RDP pochodzi od nieznanego wydawcy lub że nie można zweryfikować poświadczeń użytkownika.  Jest ona bezpiecznie zignorować te ostrzeżenia.


## <a name="connect-to-a-linux-based-vm"></a>Łączenie z maszyną Wirtualną opartą na systemie Linux

Aby połączyć maszynę Wirtualną opartą na systemie Linux, konieczne będzie klienta protokołu secure shell (SSH) protokołu.  Tej dyskusji, będą korzystać z BEZPŁATNEJ [PuTTY](https://www.ssh.com/ssh/putty/) terminalu SSH.

1. W **maszyn wirtualnych** bloku [witryny Azure portal](https://ms.portal.azure.com), wybierz maszynę Wirtualną, aby nawiązać połączenie.  
2. **Rozpocznij** maszyny Wirtualnej, jeśli nie jest już uruchomiona.
3. Kliknij nazwę maszyny Wirtualnej, aby otworzyć jego **Przegląd** strony.
4. Zanotuj publiczny adres IP i nazwę DNS maszyny wirtualnej.  (Jeśli te wartości nie są ustawione, a następnie należy [Utwórz interfejs sieciowy](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)

   ![Ustawienia maszyny Wirtualnej — omówienie](./media/publishvm_019.png)
 
5. Otwórz aplikację programu PuTTY.  
6. W oknie dialogowym konfiguracji programu PuTTY wprowadź adres IP lub nazwę DNS maszyny wirtualnej. 

   ![Ustawienia terminalu programu puTTY](./media/publishvm_020.png)
 
7. Kliknij przycisk **Otwórz** aby otworzyć terminal programu PuTTY.  
8. Po wyświetleniu monitu wprowadź nazwę konta i hasło konta maszyny Wirtualnej systemu Linux. 

   Jeśli występują problemy z połączeniem, zapoznaj się z dokumentacją używanego klienta SSH, na przykład [rozdział 10: Typowe komunikaty o błędach](https://www.ssh.com/ssh/putty/putty-manuals/0.68/Chapter10.html#errors).

Aby uzyskać więcej informacji, w tym sposobu dodawania komputera stacjonarnego do aprowizowanej maszyny Wirtualnej systemu Linux, zobacz [Instalowanie i konfigurowanie pulpitu zdalnego, aby nawiązać połączenie z maszyny Wirtualnej z systemem Linux na platformie Azure](../../../virtual-machines/linux/use-remote-desktop.md).


## <a name="stop-unused-vms"></a>Zatrzymywanie nieużywane maszyn wirtualnych
W ramach platformy Azure do hostowania maszyny Wirtualnej, gdy maszyna wirtualna jest uruchomiona *lub bezczynnych*.  Dlatego jest najlepszym rozwiązaniem jest zatrzymanie maszyny wirtualne, które nie są obecnie używane.  Na przykład przetestować, tworzenia kopii zapasowej lub wycofane maszyny wirtualne są kandydatami do zamknięcia. Aby zamknąć Maszynę wirtualną, wykonaj następujące czynności:

1. Na **maszyn wirtualnych** bloku wybierz maszynę Wirtualną, do którego ma zostać zatrzymane. 
2. Na pasku narzędzi w górnej części strony kliknij **zatrzymać** przycisku.

   ![Zatrzymywanie maszyny wirtualnej](./media/publishvm_018.png)

Platforma Azure szybko zatrzymuje maszyny Wirtualnej w procesie nazywanym *dezalokacji*, który nie tylko zamykania systemu operacyjnego na maszynie Wirtualnej, ale także powoduje zwolnienie zasobów sprzętowych i sieciowych, wcześniej zainicjowano obsługę administracyjną dla niego.

Jeśli chcesz później ponownie uaktywnić zatrzymanej maszyny Wirtualnej, zaznacz go i kliknij przycisk **Start** przycisku.


## <a name="next-steps"></a>Kolejne kroki

Po nawiązaniu zdalnie, można przystąpić do [skonfigurować swoją maszynę Wirtualną](./cpp-configure-vm.md).
