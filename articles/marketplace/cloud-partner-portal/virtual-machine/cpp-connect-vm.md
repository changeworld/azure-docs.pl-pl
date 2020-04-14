---
title: Łączenie się z maszyną wirtualną opartą na platformie Microsoft Azure | Azure Marketplace
description: W tym artykule wyjaśniono, jak połączyć się z nową maszyną wirtualną utworzoną na platformie Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 6f2652a98aa081b62ba80951212ef3a3de513593
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272665"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Łączenie się z maszyną wirtualną opartą na platformie Azure

> [!IMPORTANT]
> Od 13 kwietnia 2020 r. rozpoczniemy przenoszenie zarządzania ofertami maszyny wirtualnej platformy Azure do centrum partnerskiego. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami w [Tworzenie zasobów technicznych maszyny wirtualnej platformy Azure](https://aka.ms/AzureVMTechAsset) do zarządzania zmigrowanych ofert.

W tym artykule wyjaśniono, jak połączyć się z maszynami wirtualnymi (VM) utworzonymi na platformie Azure i zalogować się do ich maszyn.  Po pomyślnym nawiązaniu połączenia można pracować z maszyną wirtualną tak, jakby użytkownik był zalogowany lokalnie na serwerze hosta. 

## <a name="connect-to-a-windows-based-vm"></a>Łączenie się z maszyną wirtualną z systemem Windows

Użyjesz klienta pulpitu zdalnego, aby połączyć się z maszyną wirtualną z systemem Windows hostowanym na platformie Azure.  Większość wersji systemu Windows natywnie zawiera obsługę protokołu pulpitu zdalnego (RDP).  W przypadku innych komputerów można znaleźć więcej informacji o klientach w [komputerach zdalnych.](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)  

W poniższym artykule opisano, jak połączyć się z [maszyną wirtualną i zalogować się na maszynie wirtualnej platformy Azure](../../../virtual-machines/windows/connect-logon.md)za pomocą wbudowanej obsługi protokołu RDP systemu Windows: Jak nawiązać połączenie i zalogować się na maszynie wirtualnej platformy Azure z systemem Windows .  

>[!TIP]
> Podczas procesu mogą pojawić się ostrzeżenia dotyczące zabezpieczeń, na przykład, że plik rdp pochodzi od nieznanego wydawcy lub że nie można zweryfikować poświadczeń użytkownika.  Można bezpiecznie zignorować te ostrzeżenia.


## <a name="connect-to-a-linux-based-vm"></a>Łączenie się z maszyną wirtualną z systemem Linux

Aby połączyć maszynę wirtualną z systemem Linux, potrzebny jest klient protokołu SSH (Secure Shell).  Ta dyskusja będzie korzystać z bezpłatnego terminalu [PuTTY](https://www.ssh.com/ssh/putty/) SHH.

1. Przejdź do [witryny Azure portal](https://ms.portal.azure.com). Wyszukaj i wybierz **maszyny wirtualne**. 
2. Wybierz maszynę wirtualną, z którą chcesz się połączyć.  
3. **Uruchom** maszynę wirtualną, jeśli nie jest jeszcze uruchomiona.
4. Kliknij nazwę maszyny Wirtualnej, aby otworzyć jej stronę **Przegląd.**
5. Zanotuj publiczny adres IP i nazwę DNS maszyny Wirtualnej.  (Jeśli te wartości nie są ustawione, należy [utworzyć interfejs sieciowy](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)

   ![Ustawienia przeglądu maszyny wirtualnej](./media/publishvm_019.png)
 
6. Otwórz aplikację PuTTY.  
7. W oknie dialogowym Konfiguracja PuTTY wprowadź adres IP lub nazwę DNS maszyny wirtualnej. 

   ![Ustawienia terminali PuTTY](./media/publishvm_020.png)
 
8. Kliknij **przycisk Otwórz,** aby otworzyć terminal PuTTY.  
9. Po wyświetleniu monitu wprowadź nazwę konta i hasło konta maszyny Wirtualnej z systemem Linux. 

Jeśli masz problemy z połączeniem, zapoznaj się z dokumentacją klienta SSH, na przykład [rozdział 10: Typowe komunikaty o błędach](https://www.ssh.com/ssh/putty/putty-manuals).

Aby uzyskać więcej informacji, w tym jak dodać pulpit do aprowizowanego urządzenia wirtualnego z systemem Linux, zobacz [Instalowanie i konfigurowanie pulpitu zdalnego w celu łączenia się z maszyną wirtualną z systemem Linux na platformie Azure](../../../virtual-machines/linux/use-remote-desktop.md).


## <a name="stop-unused-vms"></a>Zatrzymywać nieużywane maszyny wirtualne
Platforma Azure rozlicza hosting maszyn wirtualnych, gdy maszyna wirtualna jest uruchomiona *lub bezczynna*.  W związku z tym najlepszym rozwiązaniem jest zatrzymanie maszyn wirtualnych, które nie są obecnie używane.  Na przykład test, kopia zapasowa lub wycofane maszyny wirtualne są kandydatami do zamknięcia. Aby zamknąć maszynę wirtualną, wykonaj następujące czynności:

1. W bloku **Maszyny wirtualne** wybierz maszynę wirtualną, którą chcesz zatrzymać. 
2. Na pasku narzędzi u góry strony kliknij przycisk **Zatrzymaj.**

   ![Zatrzymywanie maszyny wirtualnej](./media/publishvm_018.png)

Platforma Azure szybko zatrzymuje maszynę wirtualną w procesie o nazwie *deallocation*, który nie tylko zamyka system operacyjny na maszynie Wirtualnej, ale także zwalnia sprzęt i zasoby sieciowe wcześniej aprowizowana dla niego.

Jeśli chcesz później ponownie aktywować zatrzymaną maszynę wirtualną, zaznacz ją i kliknij przycisk **Start.**


## <a name="next-steps"></a>Następne kroki

Po podłączeniu zdalnym można skonfigurować [maszynę wirtualną](./cpp-configure-vm.md).
