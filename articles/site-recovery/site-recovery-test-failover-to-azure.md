---
title: Uruchamianie próbnego odzyskiwania po awarii na platformie Azure przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o uruchamianiu próbnego odzyskiwania po awarii ze środowiska lokalnego na platformę Azure za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: 6d8ad71894444b3759e506c50244b592ac1f8aac
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58904716"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Uruchamianie próbnego odzyskiwania na platformie Azure 


W tym artykule opisano sposób uruchamiania próbnego odzyskiwania po awarii na platformie Azure przy użyciu testowego trybu failover Usługa Site Recovery.  

Możesz uruchomić testowy tryb failover do zweryfikowania Twojej replikacji i strategii odzyskiwania po awarii, bez przestoju lub utraty danych. Testowanie trybu failover nie ma wpływu na trwającą replikację lub w środowisku produkcyjnym. Można uruchomić testowy tryb failover na określonej maszyny wirtualnej (VM) lub na [planu odzyskiwania](site-recovery-create-recovery-plans.md) zawierające wiele maszyn wirtualnych.


## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover
Ta procedura opisuje sposób testować tryb failover planu odzyskiwania. Jeśli chcesz uruchomić test trybu failover dla pojedynczej maszyny Wirtualnej, wykonaj czynności opisane [tutaj](tutorial-dr-drill-azure.md#run-a-test-failover-for-a-single-vm)

![Test pracy w trybie failover](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. W usłudze Site Recovery w witrynie Azure portal, kliknij przycisk **plany odzyskiwania** > *recoveryplan_name* > **testowy tryb Failover**.
2. Wybierz **punkt odzyskiwania** do którego ma zostać w trybie Failover. Możesz użyć jednej z następujących opcji:
    - **Najnowszy przetworzony**: Ta opcja nie powiedzie się za pośrednictwem wszystkich maszyn wirtualnych w planie do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Aby zobaczyć najnowszy punkt dla określonej maszyny Wirtualnej odzyskiwania, sprawdź **najnowsze punkty odzyskiwania** w ustawieniach maszyny Wirtualnej. Ta opcja zapewnia niską wartość celu czasu odzyskiwania, ponieważ nie wymaga przetwarzania nieprzetworzonych danych.
    - **Najnowszy spójny na poziomie aplikacji**: Ta opcja nie powiedzie się za pośrednictwem wszystkich maszyn wirtualnych w planie, aby najnowszy spójnych z aplikacją punkt przywracania przetworzone przez usługę Site Recovery. Aby zobaczyć najnowszy punkt dla określonej maszyny Wirtualnej odzyskiwania, sprawdź **najnowsze punkty odzyskiwania** w ustawieniach maszyny Wirtualnej.
    - **Najnowszy**: Ta opcja najpierw przetwarza wszystkie dane, które została wysłana do usługi Site Recovery, aby utworzyć punkt odzyskiwania dla każdej maszyny Wirtualnej przed przechodzenie w tryb failover do niego. Ta opcja zapewnia najniższy cel punktu odzyskiwania (cel punktu odzyskiwania), ponieważ maszyna wirtualna utworzona po pracy awaryjnej, będzie miał wszystkie dane, które są replikowane do usługi Site Recovery podczas pracy w trybie failover zostało wyzwolone.
    - **Najnowsze wieloma Maszynami wirtualnymi przetwarzane**: Ta opcja jest dostępna w przypadku planów odzyskiwania z co najmniej jeden maszyn wirtualnych, które mają włączoną spójnością wielu maszyn wirtualnych. Maszyny wirtualne z włączone ustawienie przełączyć w tryb failover do najnowszego punktu odzyskiwania spójnego na poziomie wielu maszyn wirtualnych wspólnej. Inne maszyny wirtualne przełączyć w tryb failover do najnowszego punktu odzyskiwania przetworzonego.  
    - **Najnowsze wieloma Maszynami wirtualnymi spójny na poziomie aplikacji**: Ta opcja jest dostępna w przypadku planów odzyskiwania z co najmniej jeden maszyn wirtualnych, które mają włączoną spójnością wielu maszyn wirtualnych. Maszyny wirtualne, które są częścią grupy replikacji przełączyć w tryb failover do najnowszego punktu odzyskiwania spójnego z aplikacją wspólnej wielu maszyn wirtualnych. Inne maszyny wirtualne Failover ich najnowszego punktu odzyskiwania spójnego na poziomie aplikacji.
    - **Niestandardowy**: Użyj tej opcji do określonej maszyny Wirtualnej do określonego punktu odzyskiwania w trybie Failover.
3. Wybierz opcję Usługa Azure virtual network, w której zostanie utworzony test maszyn wirtualnych.

    - Próby odzyskiwania lokacji, aby utworzyć testowe maszyny wirtualne w podsieci z taką samą nazwę i ten sam adres IP, jak ta podana w **obliczenia i sieć** ustawienia maszyny wirtualnej.
    - Jeśli podsieć o takiej samej nazwie, nie jest dostępny w sieci wirtualnej platformy Azure używane do testowania trybu failover, następnie test maszyny Wirtualnej jest tworzony w pierwszej podsieci alfabetycznie.
    - Jeśli ten sam adres IP jest niedostępna w tej podsieci, następnie maszyna wirtualna otrzyma kolejny dostępny adres IP w podsieci. [Dowiedz się więcej](#create-a-network-for-test-failover).
4. Jeśli użytkownik przechodzenia w tryb failover na platformie Azure i szyfrowanie danych jest włączone, w **klucza szyfrowania**, wybierz certyfikat, który został wystawiony, gdy włączone jest szyfrowanie podczas instalacji dostawcy. Można zignorować ten krok, jeśli nie jest włączone szyfrowanie.
5. Śledzić postęp trybu failover na **zadań** kartę. Można wyświetlić testową maszynę repliki w witrynie Azure portal.
6. Aby zainicjować połączenie RDP z maszyny Wirtualnej platformy Azure, musisz [dodać publiczny adres IP](https://aka.ms/addpublicip) interfejsu sieciowego w trybie Failover maszyny Wirtualnej.
7. Gdy wszystko działa zgodnie z oczekiwaniami, kliknij przycisk **wyczyść test pracy awaryjnej**. Spowoduje to usunięcie maszyn wirtualnych, które zostały utworzone podczas testowania trybu failover.
8. W obszarze **Uwagi** zarejestruj i zapisz wszelkie obserwacje związane z testem pracy w trybie failover.


![Test pracy w trybie failover](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

Podczas testowania trybu failover zostaje wyzwolona, zostaną wykonane następujące zadania:

1. **Wymagania wstępne**: Sprawdzanie wymagań wstępnych, przebiegów, aby upewnić się, że zostały spełnione wszystkie warunki wymagane dla trybu failover.
2. **Tryb failover**: Przełączenie w tryb failover przetwarza i przygotowane dane, tak, aby z niego można utworzyć Maszynę wirtualną platformy Azure.
3. **Najnowszy**: Jeśli wybrany najnowszy punkt odzyskiwania, punkt odzyskiwania jest tworzony z wysyłanym do usługi danych.
4. **Rozpocznij**: Ten krok umożliwia utworzenie maszyny wirtualnej platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.

### <a name="failover-timing"></a>Czas pracy awaryjnej

W następujących scenariuszach tryb failover wymaga dodatkowych pośredniego kroku, który zwykle trwa około 8 – 10 minut:

* Maszyny wirtualne programu VMware z wersją starszą niż 9,8 usługi mobilności
* Serwerów fizycznych
* Maszyny wirtualne VMware z systemem Linux
* Maszyna wirtualna funkcji Hyper-V chronionych jako serwerów fizycznych
* Maszyna wirtualna VMware gdzie następujące sterowniki nie są sterowników rozruchowych:
    * storvsc
    * vmbus
    * storflt
    * Intelide
    * ATAPI
* Maszyny Wirtualnej VMware, które nie mają włączony protokół DHCP, niezależnie od tego, czy użytkownicy korzystają z DHCP lub statyczne adresy IP.

We wszystkich innych przypadkach nie pośredni krok nie jest wymagany, a tryb failover trwa znacznie krócej.


## <a name="create-a-network-for-test-failover"></a>Tworzenie sieci do testowania pracy w trybie failover

Do testowania pracy w trybie failover zalecamy wybór sieci izolowanej od sieci produkcyjnej lokacji odzyskiwania, określonej w obszarze ustawień **Obliczenia i sieć** poszczególnych maszyn wirtualnych. Tworzone sieci wirtualne platformy Azure są domyślnie izolowane od innych sieci. Sieć testowa powinna odzwierciedlać sieć produkcyjną:

- Sieć testowa powinna mieć taką samą liczbę podsieci jak sieć produkcyjna. Podsieci powinny mieć takie same nazwy.
- W sieci testowej powinien być używany taki sam zakres adresów IP.
- Zaktualizuj system DNS sieci testowej, używając adresu IP określonego dla maszyny wirtualnej systemu DNS w obszarze ustawień **Obliczenia i sieć**. Aby uzyskać więcej informacji, zobacz sekcję [Zagadnienia dotyczące testowania trybu failover dla usługi Active Directory](site-recovery-active-directory.md#test-failover-considerations).


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Testowanie trybu failover do produkcyjnego środowiska sieciowego w lokacji odzyskiwania

Mimo że firma Microsoft zaleca się używanie sieci testowej, niezależnie od sieci produkcyjnej, jeśli chcesz przetestować odzyskiwanie po awarii w sieci produkcyjnej, pamiętaj o następujących kwestiach:

- Upewnij się, że podstawowa maszyna wirtualna jest zamknięty po uruchomieniu testu trybu failover. W przeciwnym razie nastąpi dwóch maszyn wirtualnych o tej samej tożsamości, uruchomione w tej samej sieci, w tym samym czasie. Może to prowadzić do nieoczekiwanych konsekwencji.
- Wszelkie zmiany maszyny wirtualne utworzone do testowania trybu failover zostaną utracone podczas czyszczenia przełączenie w tryb failover. Te zmiany nie są replikowane z powrotem do podstawowej maszyny Wirtualnej.
- Testowanie w środowisku produkcyjnym prowadzi do przestojów aplikacji produkcyjnych. Użytkownicy nie należy używać aplikacji działających na maszynach wirtualnych podczas testowania trybu failover jest w toku.  



## <a name="prepare-active-directory-and-dns"></a>Przygotowanie usługi Active Directory i DNS

Aby uruchomić testowy tryb failover do testowania aplikacji, potrzebujesz kopii środowiska produkcyjnego usługi Active Directory w środowisku testowym. Odczyt [testowanie trybu failover zagadnienia dotyczące usługi Active Directory](site-recovery-active-directory.md#test-failover-considerations) Aby dowiedzieć się więcej.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Jeśli chcesz połączyć się z maszyn wirtualnych platformy Azure przy użyciu protokołu RDP/SSH po włączeniu trybu failover, postępuj zgodnie z wymagań podsumowanych w tabeli.

**Tryb failover** | **Lokalizacja** | **Akcje**
--- | --- | ---
**Maszyny Wirtualnej platformy Azure z systemem Windows** | Na komputerze lokalnym przed włączeniem trybu failover | Dostęp do maszyny Wirtualnej platformy Azure za pośrednictwem Internetu, Włącz protokół RDP i upewnij się, że reguły TCP i UDP zostały dodane do **publicznych**, oraz że protokołu RDP jest dozwolona dla wszystkich profilów w **zapory Windows**  >  **Dozwolone aplikacje**.<br/><br/> Dostęp do maszyny Wirtualnej platformy Azure za pośrednictwem połączenia lokacja lokacja, Włącz protokół RDP na maszynie i upewnij się, że protokołu RDP jest dozwolona w **zapory Windows** -> **dozwolone aplikacje i funkcje**, aby uzyskać **Domena i prywatne** sieci.<br/><br/>  Upewnij się, że zasady sieci SAN systemu operacyjnego ustawiono **OnlineAll**. [Dowiedz się więcej](https://support.microsoft.com/kb/3031135).<br/><br/> Upewnij się, że nie ma żadnych oczekujących aktualizacji Windows na maszynie Wirtualnej podczas wyzwalania trybu failover. Windows update może uruchomić po przełączeniu w tryb failover, a nie będzie można zalogować się na maszynie Wirtualnej, do momentu ukończenia aktualizacji.
**Maszyny Wirtualnej platformy Azure z systemem Windows** | Maszyna wirtualna platformy Azure po włączeniu trybu failover |  [Dodaj publiczny adres IP](https://aka.ms/addpublicip) dla maszyny wirtualnej.<br/><br/> Reguły sieciowych grup zabezpieczeń w trybie Failover maszyny Wirtualnej (i podsieci platformy Azure, do którego jest podłączony) muszą zezwalać na połączenia przychodzące do portu RDP.<br/><br/> Sprawdź **diagnostykę rozruchu** Aby sprawdzić, zrzut ekranu maszyny wirtualnej.<br/><br/> Jeśli nie możesz się połączyć, sprawdź, czy maszyna wirtualna jest uruchomiona i przejrzyj te [wskazówki dotyczące rozwiązywania problemów](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Maszyna wirtualna platformy Azure z systemem Linux** | Na komputerze lokalnym przed włączeniem trybu failover | Upewnij się, że Usługa Secure Shell na maszynie Wirtualnej jest ustawiona na automatyczne uruchomienie przy rozruchu systemu.<br/><br/> Sprawdź, czy reguły zapory zezwalają na połączenie SSH.
**Maszyna wirtualna platformy Azure z systemem Linux** | Maszyna wirtualna platformy Azure po włączeniu trybu failover | Reguły sieciowych grup zabezpieczeń w trybie Failover maszyny Wirtualnej (i podsieci platformy Azure, do którego jest podłączony) muszą zezwalać na połączenia przychodzące do portu SSH.<br/><br/> [Dodaj publiczny adres IP](https://aka.ms/addpublicip) dla maszyny wirtualnej.<br/><br/> Sprawdź **diagnostykę rozruchu** dla zrzut ekranu maszyny wirtualnej.<br/><br/>

Wykonaj czynności opisane [tutaj](site-recovery-failover-to-azure-troubleshoot.md), aby rozwiązać wszystkie problemy z łącznością po przejściu do trybu failover.

## <a name="next-steps"></a>Kolejne kroki
Po ukończeniu odzyskiwania po awarii, więcej informacji na temat innych rodzajów [trybu failover](site-recovery-failover.md).
