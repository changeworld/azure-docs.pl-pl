---
title: Uruchamianie pracy awaryjnej podczas odzyskiwania po awarii za pomocą usługi Azure Site Recovery
description: Jak awaryjnie na platformie Azure za pomocą usługi Azure Site Recovery.
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: 99a197e8f5ebac8a3b0be1b567ee41b43a2c4476
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471272"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>Uruchamianie pracy awaryjnej z lokalnego na platformę Azure

W tym artykule opisano, jak awaryjnie obsługiwać komputery lokalne na platformie Azure w [usłudze Azure Site Recovery](site-recovery-overview.md)

## <a name="before-you-start"></a>Przed rozpoczęciem

- [Dowiedz się więcej](failover-failback-overview.md) o procesie pracy awaryjnej w odzyskiwaniu po awarii.
- Jeśli chcesz przejść w stan fail over wiele maszyn, [dowiedz się,](recovery-plan-overview.md) jak zbierać maszyny razem w planie odzyskiwania.
- Przed zakończeniem pełnego trybu failover uruchom [ćwiczenie odzyskiwania po awarii,](site-recovery-test-failover-to-azure.md) aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.

## <a name="prepare-to-connect-after-failover"></a>Przygotowanie do połączenia po przełączeniu awaryjnym

Aby upewnić się, że można połączyć się z maszyn wirtualnych platformy Azure, które są tworzone po przełączeniu w stan failover, oto kilka czynności, które należy wykonać lokalnie przed przejściem awaryjnym.


### <a name="prepare-on-premises-to-connect-after-failover"></a>Przygotowanie lokalnie do połączenia po przełączeniu awaryjnego

Jeśli chcesz połączyć się z maszynami wirtualnymi platformy Azure przy użyciu protokołu RDP/SSH po przełączeniu w stan failover, istnieje wiele czynności, które należy wykonać lokalnie przed przejściem w stan failover.

**Po pracy awaryjnej** | **Lokalizacja** | **Akcje**
--- | --- | ---
**Maszyna wirtualna platformy Azure z systemem Windows** | Komputer lokalny przed przejściem awaryjnym | Aby uzyskać dostęp do maszyny Wirtualnej platformy Azure przez Internet, włącz protokół RDP i upewnij się, że reguły protokołu TCP i UDP są dodawane dla **publicznych,** a protokół RDP jest dozwolony dla wszystkich profilów w > **aplikacjach dozwolonych zapory** **systemu Windows.**<br/><br/> Aby uzyskać dostęp do maszyny Wirtualnej platformy Azure za pośrednictwem połączenia lokacja lokacja, włącz prow na komputerze i upewnij się, że usługa RDP jest dozwolona w**aplikacjach i funkcjach dozwolonych przez** **Zaporę** -> systemu Windows dla sieci domen i sieci **prywatnych.**<br/><br/> <br/><br/> Usuń wszystkie statyczne trasy trwałe i serwer proxy WinHTTP. Upewnij się, że zasady sieci SAN systemu operacyjnego są ustawione na **OnlineAll**. [Dowiedz się więcej](https://support.microsoft.com/kb/3031135).<br/><br/> Upewnij się, że nie ma żadnych aktualizacji systemu Windows oczekujących na maszynie Wirtualnej podczas wyzwalania pracy awaryjnej. Aktualizacja systemu Windows może rozpocząć się po przełączeniu w pracę awaryjną i nie będzie można zalogować się na maszynę wirtualną, dopóki aktualizacja nie zostanie zakończona.
**Maszyna wirtualna platformy Azure z systemem Linux** | Komputer lokalny przed przejściem awaryjnym | Upewnij się, że usługa Secure Shell na maszynie Wirtualnej jest ustawiona na automatyczne uruchamianie podczas rozruchu systemu.<br/><br/> Sprawdź, czy reguły zapory zezwalają na połączenie SSH.


## <a name="run-a-failover"></a>Uruchamianie trybu failover

W tej procedurze opisano sposób uruchamiania pracy awaryjnej dla [planu odzyskiwania](site-recovery-create-recovery-plans.md). Jeśli chcesz uruchomić tryb failover dla pojedynczej maszyny Wirtualnej, postępuj zgodnie z instrukcjami dotyczącymi [maszyny wirtualnej VMware,](vmware-azure-tutorial-failover-failback.md) [serwera fizycznego](physical-to-azure-failover-failback.md)lub [maszyny wirtualnej funkcji Hyper-V.](hyper-v-azure-failover-failback-tutorial.md)


Uruchom plan odzyskiwania w pracy awaryjnej w następujący sposób:

1. W magazynie odzyskiwania witryny wybierz pozycję **Plany** > odzyskiwania*recoveryplan_name*.
2. Kliknij pozycję **Przetwórz awaryjnie**.

    ![Tryb failover](./media/site-recovery-failover/Failover.png)

3. W **trybie** > **awaryjnym trybu failover kierunek**, pozostaw domyślne, jeśli replikujesz na platformie Azure.
4. W **obszarze Przewijanie awaryjne**wybierz **punkt odzyskiwania,** do którego ma być przejęty awaryjnie.

    - **Najnowsze**: Użyj najnowszego punktu. Spowoduje to przetwarzanie wszystkich danych, które zostały wysłane do usługi site recovery i tworzy punkt odzyskiwania dla każdego komputera. Ta opcja zapewnia najniższy cel punktu odzyskiwania (Recovery Point Objective), ponieważ maszyna wirtualna utworzona po przełączeniu w błąd ma wszystkie dane, które zostały zreplikowane do odzyskiwania witryny po wyzwoleniu pracy awaryjnej.
   - **Ostatnio przetworzone:** Użyj tej opcji, aby w pracy awaryjnej maszyn wirtualnych do najnowszego punktu odzyskiwania już przetworzone przez usługi Site Recovery. Ostatni przetworzony punkt odzyskiwania można zobaczyć w **najnowszych punktach odzyskiwania**maszyny Wirtualnej . Ta opcja zapewnia niski cel działania, ponieważ nie poświęcono na przetwarzanie nieprzetwochanych danych
   - **Najnowsze spójne z aplikacjami:** Użyj tej opcji, aby zakończyć się niepowodzeniem maszyn wirtualnych do najnowszego punktu odzyskiwania spójnego aplikacji, który został przetworzony przez odzyskiwanie witryny.
   - **Najnowsze przetwarzanie wielu maszyn wirtualnych:** dzięki tej opcji maszyny wirtualne, które są częścią grupy replikacji w trybie failover do najnowszego wspólnego punktu odzyskiwania spójnego wielu maszyn wirtualnych. Inne maszyny wirtualne w trybie fail over do ich ostatni przetworzony punkt odzyskiwania. Ta opcja jest dostępna tylko dla planów odzyskiwania, które mają co najmniej jedną maszynę wirtualną z włączoną spójnością wielu maszyn wirtualnych.
   - **Najnowsze aplikacje wielu maszyn wirtualnych spójne:** Dzięki tej opcji maszyny wirtualne, które są częścią grupy replikacji w trybie fail over do najnowszego wspólnego punktu odzyskiwania spójne aplikacji wielu maszyn wirtualnych. Inne maszyny wirtualne pracy awaryjnej do ich najnowszych aplikacji spójne punktu odzyskiwania. Tylko dla planów odzyskiwania, które mają co najmniej jedną maszynę wirtualną z włączoną spójnością wielu maszyn wirtualnych.
   - **Niestandardowe:** Niedostępne dla planów odzyskiwania. Ta opcja jest dostępna tylko dla pracy awaryjnej poszczególnych maszyn wirtualnych.

5. Wybierz **zamknij komputer przed rozpoczęciem pracy awaryjnej,** jeśli chcesz, aby usługa Site Recovery zamknęła źródłowe maszyny wirtualne przed rozpoczęciem pracy awaryjnej. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się.  

    > [!NOTE]
    > Jeśli maszyny wirtualne funkcji Hyper-V są w trybie failover, zamknięcie systemu próbuje zsynchronizować i zreplikować dane lokalne, które nie zostały jeszcze wysłane do usługi, przed wyzwoleniem pracy awaryjnej. 

6. Postępuj pracy awaryjnej na stronie **Zadania.** Nawet jeśli wystąpią błędy, plan odzyskiwania jest uruchamiany, dopóki nie zostanie ukończony.
7. Po przemiń awaryjnych zaloguj się do maszyny Wirtualnej, aby sprawdzić jej poprawność. 
8. Jeśli chcesz przełączyć się do innego punktu odzyskiwania do użycia w pracy awaryjnej, użyj **zmień punktu odzyskiwania**.
9. Gdy będziesz gotowy, możesz zatwierdzić przemiebienie awaryjne. Akcja **Commit** usuwa wszystkie punkty odzyskiwania dostępne w usłudze. Opcja **Zmień punkt odzyskiwania** nie będzie już dostępna.

## <a name="run-a-planned-failover-hyper-v"></a>Uruchamianie planowanego trybu failover (funkcja Hyper-V)

Można uruchomić planowaną przełączę w stan failover dla maszyn wirtualnych funkcji Hyper-V.

- Planowana przewijanie awaryjne jest opcją trybu failover utraty danych.
- Po wyzwoleniu planowanej pracy awaryjnej najpierw źródłowe maszyny wirtualne są zamykane, synchronizowane są najnowsze dane, a następnie wyzwalana jest funkcja pracy awaryjnej.
- Planowane trybu failover można uruchomić przy użyciu opcji **Planowane trybu failover.** Działa w podobny sposób do zwykłej pracy awaryjnej.
 
## <a name="track-failovers"></a>Śledzenie przechyłów w niepowodzeniach

Istnieje wiele zadań skojarzonych z pracy awaryjnej.

![Tryb failover](./media/site-recovery-failover/FailoverJob.png)

- **Sprawdzanie wymagań wstępnych:** Zapewnia, że spełnione są wszystkie warunki wymagane do pracy awaryjnej.
- **Pracy awaryjnej:** przetwarza dane, tak aby można utworzyć maszynę wirtualną platformy Azure z niego. Jeśli wybrano **ostatni** punkt odzyskiwania, punkt odzyskiwania jest tworzony na podstawie danych, które zostały wysłane do usługi.
- **Start:** Tworzy maszynę wirtualną platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.

> [!WARNING]
> **Nie anuluj pracy awaryjnej w toku:** Przed rozpoczęciem pracy awaryjnej replikacja s zatrzymana dla maszyny Wirtualnej. Jeśli anulujesz zadanie w toku, praca awaryjna zostanie zatrzymana, ale maszyna wirtualna nie rozpocznie replikacji. Nie można ponownie uruchomić replikacji.


### <a name="extra-failover-time"></a>Dodatkowy czas pracy awaryjnej

W niektórych przypadkach przeróbka awaryjna maszyny Wirtualnej wymaga kroku pośredniego, który zwykle trwa około ośmiu do 10 minut. Są to maszyny, których dotyczy ten dodatkowy krok/czas:

* Maszyny wirtualne VMware z uruchomieniem wersji usługi mobilności starszej niż 9.8.
* Serwery fizyczne i maszyny wirtualne funkcji Hyper-V chronione jako serwery fizyczne.
* Maszyny wirtualne VMware z systemem Linux.
* Maszyny wirtualne VMware, na których te sterowniki nie są obecne jako sterowniki rozruchowe:
    * storvsc ( storvsc )
    * Vmbus
    * storflt ( storflt )
    * intelide
    * Atapi
* Maszyny wirtualne VMware, które nie mają włączonego protokołu DHCP, niezależnie od tego, czy używają adresów IP DHCP, czy statycznych.


## <a name="automate-actions-during-failover"></a>Automatyzacja akcji podczas pracy awaryjnej

Można zautomatyzować akcje podczas pracy awaryjnej. Aby to zrobić, można użyć skryptów lub uruchomieniu usługę Azure w planach odzyskiwania.

- [Dowiedz się więcej](site-recovery-create-recovery-plans.md) o tworzeniu i dostosowywaniu planów odzyskiwania, w tym dodawaniu skryptów.
- [Dowiedz się więcej](site-recovery-runbook-automation.md) o dodawaniu śmięty uruchomieniu usługi Azure Automation do planów odzyskiwania.


## <a name="configure-settings-after-failover"></a>Konfigurowanie ustawień po przemijeniu awaryjnym

### <a name="retain-drive-letters-after-failover"></a>Zachowywanie liter dysków po przemijaniu awaryjnym

Site Recovery obsługuje przechowywanie liter dysków. Jeśli wykluczasz dyski podczas replikacji maszyny [Wirtualnej, przejrzyj przykład](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) działania.

### <a name="prepare-in-azure-to-connect-after-failover"></a>Przygotowanie na platformie Azure do nawiązania połączenia po przełączeniu awaryjnym

Jeśli chcesz połączyć się z maszynami wirtualnymi platformy Azure, które są tworzone po przełączeniu w stan failover przy użyciu protokołu RDP lub SSH, postępuj zgodnie z wymaganiami podsumowanym w tabeli.

**Tryb failover** | **Lokalizacja** | **Akcje**
--- | --- | ---
**Maszyna wirtualna platformy Azure z systemem Windows** | Maszyna wirtualna platformy Azure po przemijaniu awaryjnym |  [Dodaj publiczny adres IP](https://aka.ms/addpublicip) dla maszyny wirtualnej.<br/><br/> Reguły sieciowej grupy zabezpieczeń na maszynie wirtualnej po awarii (i podsieci platformy Azure, do której jest podłączona) muszą zezwalać na połączenia przychodzące z portem RDP.<br/><br/> Sprawdź **diagnostykę rozruchu,** aby zweryfikować zrzut ekranu maszyny Wirtualnej.<br/><br/> Jeśli nie możesz nawiązać połączenia, sprawdź, czy maszyna wirtualna jest uruchomiona, i zapoznaj się z tymi [wskazówkami dotyczącymi rozwiązywania problemów.](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)
**Maszyna wirtualna platformy Azure z systemem Linux** | Maszyna wirtualna platformy Azure po przemijaniu awaryjnym | Reguły sieciowej grupy zabezpieczeń na maszynie wirtualnej po awarii (i podsieci platformy Azure, do której jest podłączona) muszą zezwalać na połączenia przychodzące z portem SSH.<br/><br/> [Dodaj publiczny adres IP](https://aka.ms/addpublicip) dla maszyny wirtualnej.<br/><br/> Sprawdź **diagnostykę rozruchu,** aby uzyskać zrzut ekranu maszyny Wirtualnej.<br/><br/>

Wykonaj czynności opisane [tutaj](site-recovery-failover-to-azure-troubleshoot.md), aby rozwiązać wszystkie problemy z łącznością po przejściu do trybu failover.

## <a name="set-up-ip-addressing"></a>Konfigurowanie adresowania IP

- **Wewnętrzne adresy IP:** Aby ustawić wewnętrzny adres IP maszyny Wirtualnej platformy Azure po przemijce awaryjnej, masz kilka opcji:
    - Zachowaj ten sam adres IP: można użyć tego samego adresu IP na maszynie Wirtualnej platformy Azure, jak adres przydzielony do komputera lokalnego.
    - Użyj innego adresu IP: można użyć innego adresu IP dla maszyny Wirtualnej platformy Azure.
    - [Dowiedz się więcej](concepts-on-premises-to-azure-networking.md#assign-an-internal-address) o konfigurowaniu wewnętrznych adresów IP.
- **Zewnętrzne adresy IP:** Publiczne adresy IP można zachować podczas pracy awaryjnej. Maszyny wirtualne platformy Azure utworzone w ramach procesu pracy awaryjnej muszą mieć przypisany publiczny adres IP platformy Azure dostępny w regionie platformy Azure. Publiczny adres IP można przypisać ręcznie lub automatyzując proces za pomocą planu odzyskiwania. [Dowiedz się więcej](concepts-public-ip-address-with-site-recovery.md).


## <a name="next-steps"></a>Następne kroki

Po zakończeniu pracy awaryjnej należy ponownie przejść do ponownego uruchomienia replikacji maszyn wirtualnych platformy Azure z powrotem do lokacji lokalnej. Po uruchomieniu replikacji można przywrócić po awarii w środowisku lokalnym, gdy będziesz gotowy.

- [Dowiedz się więcej](failover-failback-overview.md#reprotectionfailback) o reprotection i powrotu po awarii.
- [Przygotuj się](vmware-azure-reprotect.md) do ponownego wycofania vmware i powrotu po awarii.
- [Powrót po awarii](hyper-v-azure-failback.md) Maszyny wirtualne funkcji Hyper-V.
- [Dowiedz się więcej o](physical-to-azure-failover-failback.md) procesie pracy awaryjnej i powrotu po awarii dla serwerów fizycznych.

