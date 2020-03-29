---
title: Konfigurowanie pracy awaryjnej i powrotu po awarii dla serwerów fizycznych za pomocą usługi Site Recovery
description: Dowiedz się, jak awaryjnie przeczesywać serwery fizyczne na platformie Azure i wrócić do lokacji lokalnej w celu odzyskiwania po awarii za pomocą usługi Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2019
ms.author: raynew
ms.openlocfilehash: ea5893f45962d67f4b6f3e9a261c65aa0ec926bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75497859"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Serwery fizyczne replikowane w sieci Fail over i po awarii z replikowane na platformie Azure

W tym samouczku opisano sposób pracy awaryjnej lokalnych serwerów fizycznych, które są replikowane na platformę Azure za pomocą [usługi Azure Site Recovery.](site-recovery-overview.md) Po zakończeniu pracy awaryjnej powrót z platformy Azure po awarii do witryny lokalnej, gdy jest dostępna.

## <a name="before-you-start"></a>Przed rozpoczęciem

- [Dowiedz się więcej](failover-failback-overview.md) o procesie pracy awaryjnej w odzyskiwaniu po awarii.
- Jeśli chcesz przejść w stan fail over wiele maszyn, [dowiedz się,](recovery-plan-overview.md) jak zbierać maszyny razem w planie odzyskiwania.
- Przed zakończeniem pełnego trybu failover uruchom [ćwiczenie odzyskiwania po awarii,](site-recovery-test-failover-to-azure.md) aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
- Postępuj zgodnie [z tymi instrukcjami,](site-recovery-failover.md#prepare-to-connect-after-failover) aby przygotować się do łączenia się z maszynami wirtualnymi platformy Azure po przełączeniu w stan failover.



## <a name="run-a-failover"></a>Uruchamianie trybu failover

### <a name="verify-server-properties"></a>Weryfikowanie właściwości serwera

Sprawdź właściwości serwera i upewnij się, że jest on zgodny z [wymaganiami platformy Azure](vmware-physical-azure-support-matrix.md#replicated-machines) dla maszyn wirtualnych platformy Azure.

1. W **obszarze Elementy chronione**kliknij pozycję Elementy **replikowane**i wybierz maszynę.
2. W okienku **Zreplikowany element** zawiera podsumowanie informacji o komputerze, stanu kondycji i najnowszych dostępnych punktów odzyskiwania. Kliknij przycisk **Właściwości**, aby wyświetlić więcej szczegółów.
3. W **obszarze Obliczenia i Sieć**można zmodyfikować nazwę platformy Azure, grupę zasobów, rozmiar obiektu docelowego, zestaw [dostępności](../virtual-machines/windows/tutorial-availability-sets.md)i ustawienia dysku zarządzanego
4. Możesz wyświetlać i modyfikować ustawienia sieciowe, w tym sieć/podsieć, w której zlokalizowana będzie maszyna wirtualna na platformie Azure po wejściu w tryb failover, oraz adres IP, który będzie do niej przypisany.
5. W **programach Dyski**można wyświetlić informacje o systemie operacyjnym komputera i dyskach z danymi.

### <a name="fail-over-to-azure"></a>Przełączenie do trybu failover na platformie Azure

1. W **ustawieniach** > **replikowane elementy** kliknij komputer > pracy **awaryjnej**.
2. W **obszarze Przewijanie awaryjne** wybierz **punkt odzyskiwania,** do który chcesz awaryjnie. Możesz użyć jednej z następujących opcji:
   - **Najnowszy**: ta opcja najpierw przetwarza wszystkie dane wysyłane do usługi Site Recovery. Zapewnia najniższą wartość celu puntu odzyskiwania, ponieważ maszyna wirtualna platformy Azure utworzona po przejściu do trybu failover zawiera wszystkie dane, które zostały zreplikowane w usłudze Site Recovery do momentu włączenia trybu failover.
   - **Najnowsze przetworzone:** Ta opcja działa w czasie pracy komputera do najnowszego punktu odzyskiwania przetwarzanego przez odzysk witryny. Ta opcja zapewnia niską wartość celu czasu odzyskiwania, ponieważ nie wymaga przetwarzania nieprzetworzonych danych.
   - **Najnowsze spójne z aplikacjami:** Ta opcja działa w czasie pracy komputera w najnowszym punkcie odzyskiwania zgodnym z aplikacją przetwarzanym przez program Site Recovery.
   - **Niestandardowy**: umożliwia określenie punktu odzyskiwania.

3. Wybierz **zamknij komputer przed rozpoczęciem pracy awaryjnej,** jeśli chcesz, aby usługa Site Recovery próbowała zamknąć komputer źródłowy przed wyzwoleniem pracy awaryjnej. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Postęp pracy awaryjnej można śledzić na stronie **Zadania.**
4. Jeśli przeprowadzono przygotowanie do nawiązania połączenia z maszyną wirtualną platformy Azure, należy po przełączeniu w tryb failover nawiązać połączenie w celu weryfikacji.
5. Po weryfikacji należy **Zatwierdzić** tryb failover. To działanie usuwa wszystkie dostępne punkty odzyskiwania.

> [!WARNING]
> Nie anuluj pracy awaryjnej w toku. Przed rozpoczęciem pracy awaryjnej replikacja maszyny zostanie zatrzymana. Jeśli anulujesz przemiebienia awaryjne, zostanie ono zatrzymane, ale urządzenie nie będzie replikowane ponownie.
> W przypadku serwerów fizycznych dodatkowe przetwarzanie pracy awaryjnej może potrwać około ośmiu do dziesięciu minut.

## <a name="automate-actions-during-failover"></a>Automatyzacja akcji podczas pracy awaryjnej

Można zautomatyzować akcje podczas pracy awaryjnej. Aby to zrobić, można użyć skryptów lub uruchomieniu usługę Azure w planach odzyskiwania.

- [Dowiedz się więcej](site-recovery-create-recovery-plans.md) o tworzeniu i dostosowywaniu planów odzyskiwania, w tym dodawaniu skryptów.
- [Dowiedz się,](site-recovery-runbook-automation.md) jak dodawać elementy runbook usługi Azure Automation do planów odzyskiwania.

## <a name="configure-settings-after-failover"></a>Konfigurowanie ustawień po przemijeniu awaryjnym

Po przełączeniu w błąd należy [skonfigurować ustawienia platformy Azure,](site-recovery-failover.md#prepare-in-azure-to-connect-after-failover) aby połączyć się z replikowanymi maszynami wirtualnymi platformy Azure. Ponadto należy skonfigurować [wewnętrzne i publiczne](site-recovery-failover.md#set-up-ip-addressing) adresy IP.

## <a name="prepare-for-reprotection-and-failback"></a>Przygotuj się na ponowne nadruekcję i powrót awaryjny

Po przeżycia po awarii na platformie Azure, można ponownie przesłać maszyny wirtualne platformy Azure, replikując je do lokacji lokalnej. Następnie po ich replikacji, można zakończyć ich niepowodzeniem z powrotem do lokalnego, uruchamiając pracy awaryjnej z platformy Azure do lokacji lokalnej.

1. Serwery fizyczne replikowane na platformie Azure przy użyciu usługi Site Recovery mogą być używane tylko jako maszyny wirtualne VMware. Potrzebujesz infrastruktury VMware, aby wrócić awaryjnie. Wykonaj kroki opisane w [tym artykule,](vmware-azure-prepare-failback.md) aby przygotować się do ponownego tworzenia i powrotu po awarii, w tym konfigurowania serwera przetwarzania na platformie Azure i lokalnego głównego serwera docelowego oraz konfigurowania sieci VPN między lokacjami lub prywatnej komunikacji równorzędnej usługi ExpressRoute w celu powrotu po awarii.
2. Upewnij się, że lokalny serwer konfiguracji jest uruchomiony i połączony z platformą Azure. Podczas pracy awaryjnej na platformie Azure lokacja lokalna może nie być dostępna, a serwer konfiguracji może być niedostępny lub zamknięty. Podczas powrotu po awarii maszyna wirtualna musi istnieć w bazie danych serwera konfiguracji. W przeciwnym razie powrót po awarii nie powiedzie się.
3. Usuń wszystkie migawki na lokalnym głównym serwerze docelowym. Reprotection nie będzie działać, jeśli istnieją migawki.  Migawki na maszynie Wirtualnej są automatycznie scalane podczas zadania ponownego ceł.
4. Jeśli ponownie wdrażasz maszyny wirtualne zebrane w grupie replikacji w celu zapewnienia spójności wielu maszyn wirtualnych, upewnij się, że wszystkie mają ten sam system operacyjny (Windows lub Linux) i upewnij się, że wdrażany serwer docelowy ma ten sam typ systemu operacyjnego. Wszystkie maszyny wirtualne w grupie replikacji muszą używać tego samego głównego serwera docelowego.
5. Otwórz [wymagane porty](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) do powrotu po awarii.
6. Upewnij się, że serwer vCenter jest połączony przed powrotem po awarii. W przeciwnym razie odłączanie dysków i dołączanie ich z powrotem do maszyny wirtualnej kończy się niepowodzeniem.
7. Jeśli serwer vCenter zarządza maszynami wirtualnymi, do których powrócisz po awarii, upewnij się, że masz wymagane uprawnienia. Jeśli użytkownik tylko do odczytu odnajduje i chroni maszyny wirtualne, ochrona powiedzie się i pracy awaryjnej. Jednak podczas ponownego przekazywania, pracy awaryjnej kończy się niepowodzeniem, ponieważ nie można odnajdyć magazynów danych i nie są wymienione podczas ponownego przekazywania. Aby rozwiązać ten problem, można zaktualizować poświadczenia vCenter za pomocą [odpowiedniego konta/uprawnień,](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)a następnie ponowić próbę wykonania zadania. 
8. Jeśli użyto szablonu do utworzenia maszyn wirtualnych, upewnij się, że każda maszyna wirtualna ma swój własny identyfikator UUID dla dysków. Jeśli lokalny identyfikator UUID maszyny Wirtualnej ma konflikt z identyfikatorem UUID głównego serwera docelowego, ponieważ oba zostały utworzone na podstawie tego samego szablonu, ponowne odrzucenie nie powiedzie się. Wdrażanie z innego szablonu.
9. Jeśli powrót do alternatywnego serwera vCenter nie powiódł się, upewnij się, że nowy serwer vCenter i serwer docelowy wzorcowy zostaną odnalezione. Zazwyczaj, jeśli nie są one magazyny danych nie są dostępne lub nie są widoczne w **Reprotect**.
10. Sprawdź następujące scenariusze, w których nie można przywrócić po awarii:
    - Jeśli korzystasz z bezpłatnej edycji ESXi 5.5 lub bezpłatnej edycji vSphere 6 Hypervisor. Uaktualnienie do innej wersji.
    - Jeśli masz serwer fizyczny z dodatku SP1 dla systemu Windows Server 2008 R2.
    - Maszyny wirtualne, które [zostały zmigrowane](migrate-overview.md#what-do-we-mean-by-migration).
    - Maszyna wirtualna, która została przeniesiona do innej grupy zasobów.
    - Replika maszyny Wirtualnej platformy Azure, która została usunięta.
    - Replika maszyny Wirtualnej platformy Azure, która nie jest chroniona (replikowanie do lokacji lokalnej).
10. [Przejrzyj typy powrotu po awarii,](concepts-types-of-failback.md) których można użyć — odzyskiwanie oryginalnej lokalizacji i odzyskiwanie lokalizacji alternatywnej.


## <a name="reprotect-azure-vms-to-an-alternate-location"></a>Ponowne przecenianie maszyn wirtualnych platformy Azure do lokalizacji alternatywnej

Ta procedura zakłada, że lokalna maszyna wirtualna nie jest dostępna.

1. W magazynie > **Ustawienia replikowane** > **elementy**kliknij prawym przyciskiem myszy komputer, który został przejęty po awarii > **Re-Protect**.
2. W obszarze **Włącz ponownie ochronę** sprawdź, czy wybrano opcję **Z platformy Azure do serwera lokalnego**.
3. Określ lokalny główny serwer docelowy oraz serwer przetwarzania.
4. W obszarze **Magazyn danych** wybierz główny docelowy magazyn danych, do którego chcesz odzyskać dyski w środowisku lokalnym.
       - Użyj tej opcji, jeśli lokalna maszyna wirtualna została usunięta lub nie istnieje i musisz utworzyć nowe dyski.
       - To ustawienie jest ignorowane, jeśli dyski już istnieją, ale należy określić wartość.
5. Wybierz główny docelowy dysk przechowywania. Zasady powrotu po awarii są wybierane automatycznie.
6. Kliknij przycisk **OK**, aby rozpocząć ponowne włączanie ochrony. Zadanie rozpoczyna replikowanie maszyny Wirtualnej platformy Azure do lokacji lokalnej. Na karcie **Zadania** można śledzić postęp.

> [!NOTE]
> Jeśli chcesz odzyskać maszynę wirtualną platformy Azure do istniejącej lokalnej maszyny wirtualnej, zainstaluj magazyn danych lokalnej maszyny wirtualnej z dostępem odczytu/zapisu na hoście ESXi głównego serwera docelowego.


## <a name="fail-back-from-azure"></a>Powrót po awarii z platformy Azure

Uruchom tryb failover w następujący sposób:

1. Na stronie **Zreplikowane elementy** kliknij prawym przyciskiem myszy maszynę, a następnie wybierz opcję **Nieplanowana praca w trybie failover**.
2. W obszarze **Potwierdź tryb failover** sprawdź, czy wybrano kierunek trybu failover Z platformy Azure.
3.Wybierz punkt odzyskiwania, który ma być używany do pracy awaryjnej.
    - Zaleca się użycie punktu odzyskiwania **Najnowsze.** Punkt spójny z aplikacją znajduje się za najnowszym punktem w czasie i powoduje utratę danych.
    - **Najnowszy** jest punktem odzyskiwania u danych awaryjnych.
    - Podczas przełączania do trybu failover usługa Site Recovery wyłącza maszyny wirtualne platformy Azure i uruchamia lokalną maszynę wirtualną. Wiąże się to z przestojem, należy więc wybrać odpowiedni moment.
4. Kliknij prawym przyciskiem myszy maszynę, a następnie kliknij przycisk **Zatwierdź**. Spowoduje to wyzwolenie zadania, które usunie maszyny wirtualne platformy Azure.
5. Sprawdź, czy maszyna wirtualna platformy Azure została prawidłowo wyłączona.


## <a name="reprotect-on-premises-machines-to-azure"></a>Ponowne włączanie ochrony lokalnych maszyn na platformie Azure

Dane powinny teraz ponownie znajdować się w lokacji lokalnej, ale nie są replikowane na platformie Azure. Możesz ponownie rozpocząć replikowanie na platformie Azure w następujący sposób:

1. W magazynie w obszarze **Ustawienia** >**Zreplikowane elementy** wybierz maszyny wirtualne, które przywrócono po awarii, i kliknij przycisk **Włącz ponownie ochronę**.
2. Wybierz serwer przetwarzania używany do wysyłania replikowanych danych do platformy Azure, a następnie kliknij przycisk **OK**.


## <a name="next-steps"></a>Następne kroki

Po zakończeniu zadania ponownego ceł lokalnej maszyny Wirtualnej jest replikowanie na platformie Azure. W razie potrzeby można [uruchomić inną funkcję failover](site-recovery-failover.md) na platformie Azure.
