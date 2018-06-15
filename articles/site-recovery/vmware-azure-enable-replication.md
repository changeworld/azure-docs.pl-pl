---
title: Włącz replikację do platformy Azure dla maszyn wirtualnych VMware z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób skonfigurowania replikacji maszyn wirtualnych VMware do platformy Azure, za pomocą usługi Azure Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: asgang
ms.openlocfilehash: 48d84f59a80ea14c2b40821d886fc410c7e3f2c3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
ms.locfileid: "30243339"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Włącz replikację maszyn wirtualnych VMware do platformy Azure


W tym artykule opisano sposób włączania replikacji lokalnych maszyn wirtualnych VMware do platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz:

1.  [Konfigurowanie środowiska źródłowego lokalnymi](vmware-azure-set-up-source.md).
2.  [Konfigurowanie środowiska docelowego na platformie Azure](vmware-azure-set-up-target.md).


## <a name="before-you-start"></a>Przed rozpoczęciem
Podczas replikowania maszyn wirtualnych VMware:

* Twoje konto użytkownika usługi Azure musi mieć niektórych [uprawnienia](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) Aby włączyć replikację nowej maszyny wirtualnej na platformie Azure.
* Maszyny wirtualne VMware odnalezieniu co 15 minut. Może zająć 15 minut lub dłużej, aby były widoczne w portalu Azure po odnajdywania. Podobnie odnajdowania może zająć 15 minut lub dłużej, po dodaniu nowego hosta serwera lub vSphere vCenter.
* Zmiany środowiska na maszynie wirtualnej (na przykład instalacji narzędzi VMware) może zająć 15 minut lub więcej do zaktualizowania w portalu.
* Można sprawdzić czas ostatniego odnalezionych w maszynach wirtualnych VMware w **ostatniego kontaktu w** w nagłówku hosta serwera/vSphere vCenter, **serwery konfiguracji** strony.
* Aby dodać maszyn do replikacji bez oczekiwania na zaplanowanego odnajdywania, zaznacz serwer konfiguracji (nie klikaj pozycji go) i kliknij przycisk **Odśwież** przycisku.
* Po włączeniu replikacji, jeśli komputer jest gotowy, serwer przetwarzania automatycznie instaluje usługi mobilności na nim.


## <a name="enable-replication"></a>Włączanie replikacji

1. Kliknij kolejno pozycje **Krok 2. Replikowanie aplikacji** > **Źródło**. Po włączeniu replikacji po raz pierwszy kliknij pozycję **+ Replikuj** w magazynie, aby włączyć replikację dla dodatkowych maszyn.
2. W **źródła** strony > **źródła**, wybierz serwer konfiguracji.
3. W **komputera typu**, wybierz pozycję **maszyn wirtualnych** lub **maszyn fizycznych**.
4. W obszarze **vCenter/vSphere Hypervisor** wybierz serwer vCenter zarządzający hostem vSphere lub wybierz tego hosta. To ustawienie nie jest istotne, Jeśli replikujesz maszyny fizyczne.
5. Wybierz serwer przetwarzania, który będzie to nazwa serwera konfiguracji, jeśli nie utworzono żadnych serwerów dodatkowych procesów. Następnie kliknij przycisk **OK**.

    ![Włącz źródła replikacji](./media/vmware-azure-enable-replication/enable-replication2.png)

6. W **docelowego**, wybierz subskrypcji i grupy zasobów, w której chcesz utworzyć przełączona w tryb failover maszyny wirtualnej. Wybierz model wdrażania, który ma być używany na platformie Azure dla maszyn wirtualnych przełączona w tryb failover.

7. Wybierz konto magazynu Azure, którego chcesz użyć do replikacji danych. 

    > [!NOTE]

    >   * Możesz wybrać premium lub konta standard storage. W przypadku wybrania konta premium, należy określić konto dodatkowego magazynu w warstwie standardowa dla dzienników trwającej replikacji. Konta muszą być w tym samym regionie co magazyn usług odzyskiwania.
    >   * Jeśli chcesz użyć innego konta magazynu, możesz [utworzyć](../storage/common/storage-create-storage-account.md). Aby utworzyć konto magazynu przy użyciu usługi Resource Manager, kliknij przycisk **Utwórz nowy**. 

8. Wybierz sieć platformy Azure i podsieć, z którą nawiążą połączenie maszyny wirtualne Azure, gdy zostaną uruchomione po przejściu do trybu failover. Sieć musi znajdować się w tym samym regionie co magazyn Usług odzyskiwania. Wybierz opcję **Konfiguruj teraz dla wybranych maszyn**, aby zastosować ustawienia sieci do wszystkich maszyn wybranych do ochrony. Wybierz opcję **Konfiguruj później**, aby wybrać sieć platformy Azure dla poszczególnych maszyn. Jeśli nie masz sieci, należy [utworzyć](#set-up-an-azure-network). Aby utworzyć sieć przy użyciu usługi Resource Manager, kliknij przycisk **Utwórz nowy**. Wybierz podsieć, jeśli ma to zastosowanie, a następnie kliknij przycisk **OK**.

    ![Włącz ustawienie docelowego replikacji](./media/vmware-azure-enable-replication/enable-rep3.png)
9. W pozycji **Maszyny wirtualne** > **Wybierz maszyny wirtualne** wybierz każdą maszynę, którą chcesz replikować. Możesz wybrać tylko te maszyny, dla których można włączyć replikację. Następnie kliknij przycisk **OK**.

    ![Włącz replikację wybierz maszyny wirtualne](./media/vmware-azure-enable-replication/enable-replication5.png)
10. W **właściwości** > **skonfigurować właściwości**, wybierz konto używane przez serwer przetwarzania, aby automatycznie zainstalować usługi mobilności na maszynie.  
11. Domyślnie wszystkie dyski są replikowane. Aby wykluczyć dysków z replikacji, kliknij przycisk **wszystkie dyski** i wyczyść wszystkie dyski, które nie mają być replikowane.  Następnie kliknij przycisk **OK**. Później możesz skonfigurować dodatkowe właściwości. [Dowiedz się więcej](vmware-azure-exclude-disk.md) o z wyjątkiem dysków.

    ![Włącz replikację skonfigurować właściwości](./media/vmware-azure-enable-replication/enable-replication6.png)

12. W obszarze **Ustawienia replikacji** > **Konfigurowanie ustawień replikacji** sprawdź, czy wybrano właściwe zasady replikacji. Można modyfikować ustawień zasad replikacji w **ustawienia** > **zasady replikacji** > (nazwa zasady) > **Edytuj ustawienia**. Dotyczą również zmiany dotyczą zasady replikacji i nowe maszyny.
13. Włącz **spójności wielu maszyn wirtualnych** Aby zebrać maszyny do grupy replikacji. Określ nazwę grupy, a następnie kliknij przycisk **OK**. 

    > [!NOTE]

    >    * Maszyny w grupie replikacji replikowane wspólnie i udostępnione punkty odzyskiwania spójna w razie awarii i spójności aplikacji podczas ich w tryb failover.
    >    * Zbiera maszyn wirtualnych i serwerów fizycznych, aby odzwierciedlały obciążeń. Włączenie spójności wielu maszyn wirtualnych może wpłynąć na wydajność obciążenia. Użyj tylko wtedy, gdy komputery są tego samego obciążenia i wymagana jest spójność.

    ![Włączanie replikacji](./media/vmware-azure-enable-replication/enable-replication7.png)
14. Kliknij pozycję **Włącz replikację**. Możesz śledzić postęp zadania **Włącz ochronę** w pozycji **Ustawienia** > **Zadania** > **Zadania usługi Site Recovery**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.

> [!NOTE]
> Jeśli komputer jest gotowy do instalacji w trybie wypychania, jest zainstalowany składnik usługi mobilności, po włączeniu ochrony. Po zainstalowaniu na komputerze składnika zadanie ochrony rozpoczyna się i nie powiedzie się. Po awarii musisz ręcznie ponownie uruchomić każdej maszyny. Po uruchomieniu zadania ochrony rozpoczyna się od nowa i następuje Replikacja początkowa.
>
>

## <a name="view-and-manage-vm-properties"></a>Wyświetlanie właściwości maszyny wirtualnej i zarządzanie nimi

Następnie możesz sprawdzić właściwości maszyny źródłowej. Należy pamiętać, że nazwa maszyny Wirtualnej Azure wymaga zgodnie z [wymagania maszyny wirtualnej platformy Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Kliknij przycisk **ustawienia** > **elementy replikowane** >, a następnie wybierz maszynę. **Essentials** strony zawiera informacje o ustawienia maszyny i stanu.
2. W obszarze **Właściwości** możesz wyświetlić informacje dotyczące replikacji i trybu failover dla danej maszyny wirtualnej.
3. W pozycji **Obliczenia i sieć** > **Właściwości obliczeń** możesz określić nazwę i docelowy rozmiar maszyny wirtualnej Azure. Zmodyfikuj nazwę, aby spełniać wymagania dotyczące usługi Azure, jeśli to konieczne.

    ![Właściwości sieci i obliczeń](./media/vmware-azure-enable-replication/vmproperties.png)

4.  Możesz wybrać [grupy zasobów](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines) z której maszynie staje się częścią po przejściu w tryb failover. Możesz zmienić to ustawienie dowolnym momencie przed trybu failover. Po pracy w trybie failover w przypadku migrowania komputera do innej grupie zasobów, ustawienia ochrony dla tego podziału maszyny.
5. Możesz wybrać [zestawu dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) Jeśli komputer musi być częścią po przejściu w tryb failover. Podczas zaznaczania zestawu dostępności, należy pamiętać, że:

    * Wyświetlane są tylko zestawy dostępności należących do określonej grupy zasobów.  
    * Komputery z różnych sieciach wirtualnych nie może być częścią tego samego zestawu dostępności.
    * Tylko maszyny wirtualne o tym samym rozmiarze może być częścią zestawu dostępności.
5. Można również wyświetlić i dodać informacje dotyczące sieci docelowej, podsieci i adresów IP przypisanych do maszyny Wirtualnej platformy Azure.
6. W **dysków**, możesz wyświetlać systemu operacyjnego i dysków z danymi na Maszynie wirtualnej powinny być replikowane.

### <a name="configure-networks-and-ip-addresses"></a>Konfigurowanie sieci i adresów IP

- Możesz ustawić docelowy adres IP. Jeśli nie podasz adresu, maszyna przełączona w tryb failover używa protokołu DHCP. Jeśli ustawisz adres, który nie jest dostępna w trybie failover, przejście w tryb failover nie działa. Jeśli adres jest dostępny w testowej sieci trybu failover, ten sam adres IP docelowego może służyć do testowania trybu failover.
- Liczba kart sieciowych zależy od rozmiaru określonego dla docelowej maszyny wirtualnej, zgodnie z poniższym:
    - Jeśli liczba kart sieciowych na maszynie źródłowej jest większa niż liczba kart sieciowych dozwolonych dla rozmiaru maszyny docelowej, następnie element docelowy ma taką samą liczbę kart jako źródło.
    - Jeśli liczba kart sieciowych dla źródłowej maszyny wirtualnej przekracza liczbę dozwolonych kart sieciowych dla rozmiaru maszyny docelowej, zostanie użyta maksymalna liczba kart dla rozmiaru maszyny docelowej.
    Na przykład jeśli maszyna źródłowa ma dwie karty sieciowe, a rozmiar maszyny docelowej obsługuje cztery, komputer docelowy ma dwie karty sieciowe. Jeśli maszyna źródłowa ma dwie karty sieciowe, ale rozmiar docelowy obsługiwanych obsługuje tylko jedną, maszyna docelowa ma tylko jedną kartę.
    - Jeśli maszyna wirtualna ma wiele kart sieciowych, wszystkie łączą się z tej samej sieci. Ponadto staje się pierwszą wyświetlane na liście *domyślne* karty sieciowej na maszynie wirtualnej platformy Azure.

### <a name="azure-hybrid-benefit"></a>Korzyść użycia hybrydowego platformy Azure

Microsoft Software Assurance klienci mogą użyć korzyści hybrydowe platformy Azure, do zapisania na licencjonowania kosztów maszyn systemu Windows Server, które są migrowane do platformy Azure lub do użycia podczas odzyskiwania systemu Azure. Jeśli prawo do korzystania z korzystać hybrydowe platformy Azure, można określić, że przypisane świadczenie maszyny wirtualnej jest tworzonych przez usługi Azure Site Recovery po przejściu w tryb failover. W tym celu:
- Przejdź do sekcji właściwości obliczania i sieci zreplikowanej maszyny wirtualnej.
- Odpowiedz na pytanie, z pytaniem, czy masz licencji systemu Windows Server dzięki możesz uprawnia do skorzystania z usługi Azure hybrydowego.
- Zaznacz pole wyboru, aby upewnić się, że masz uprawniających licencji systemu Windows Server z Software Assurance, w którym można zastosować korzyści hybrydowe platformy Azure na komputerze, który zostanie utworzony w tryb failover.
- Zapisz ustawienia dla zreplikowanej maszyny.

Dowiedz się więcej o [korzyści hybrydowego Azure](https://aka.ms/azure-hybrid-benefit-pricing).

## <a name="common-issues"></a>Typowe problemy

* Każdy dysk powinien być mniejszy niż 1 TB.
* Dysk systemu operacyjnego musi być dyskiem podstawowym i nie dysk dynamiczny.
* Generacji 2 lub UEFI — nie włączono maszyn wirtualnych rodziny systemów operacyjnych należy systemu Windows i dysku rozruchowego powinien być mniejszy niż 300 GB.

## <a name="next-steps"></a>Kolejne kroki

Po zakończeniu ochrony, komputera osiągnęła stan chronionych, możesz spróbować [pracy awaryjnej](site-recovery-failover.md) do sprawdzenia, czy aplikacja pojawia się na platformie Azure lub nie.

Aby wyłączyć ochronę, Dowiedz się jak [wyczyścić ustawienia rejestracji i ochrony](site-recovery-manage-registration-and-protection.md).
