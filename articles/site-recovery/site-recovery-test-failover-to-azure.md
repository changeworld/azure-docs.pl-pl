---
title: Uruchamianie testowej pracy awaryjnej (drążenia odzyskiwania po awarii) na platformie Azure w usłudze Azure Site Recovery
description: Dowiedz się więcej o uruchamianiu testowej pracy awaryjnej z lokalnego na platformę Azure przy użyciu usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 26c734b7a2e9f5592ee6d51dfee4650a3998ab1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257526"
---
# <a name="run-a-test-failover-disaster-recovery-drill-to-azure"></a>Uruchamianie testowej pracy awaryjnej (drążenia odzyskiwania po awarii) na platformie Azure 


W tym artykule opisano sposób uruchamiania wiertła odzyskiwania po awarii na platformie Azure przy użyciu testu odzyskiwania lokacji w pracy awaryjnej.  

Uruchom test pracy awaryjnej, aby sprawdzić poprawność strategii replikacji i odzyskiwania po awarii, bez utraty danych lub przestoju. Test pracy awaryjnej nie ma wpływu na trwającą replikację lub środowiska produkcyjnego. Można uruchomić test pracy awaryjnej na określonej maszynie wirtualnej (VM) lub na [planie odzyskiwania](site-recovery-create-recovery-plans.md) zawierającym wiele maszyn wirtualnych.


## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover
W tej procedurze opisano sposób uruchamiania testowego trybu failover dla planu odzyskiwania. Jeśli chcesz uruchomić test pracy awaryjnej dla pojedynczej maszyny Wirtualnej, wykonaj kroki opisane [w tym miejscu](tutorial-dr-drill-azure.md#run-a-test-failover-for-a-single-vm)

![Test pracy w trybie failover](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. W obszarze Odzyskiwanie witryny w witrynie w witrynie Azure portal kliknij pozycję **Plany** > odzyskiwania*recoveryplan_name* > **testowanie trybu failover**.
2. Wybierz **punkt odzyskiwania,** do którego ma być przejęty awaryjnie. Możesz użyć jednej z następujących opcji:
    - **Najnowsze przetworzone:** Ta opcja kończy się niepowodzeniem dla wszystkich maszyn wirtualnych w planie do najnowszego punktu odzyskiwania przetwarzanego przez odzyskiwanie witryny. Aby wyświetlić najnowszy punkt odzyskiwania dla określonej maszyny Wirtualnej, sprawdź **najnowsze punkty odzyskiwania** w ustawieniach maszyny Wirtualnej. Ta opcja zapewnia niską wartość celu czasu odzyskiwania, ponieważ nie wymaga przetwarzania nieprzetworzonych danych.
    - **Najnowsze spójne z aplikacjami:** Ta opcja kończy się niepowodzeniem we wszystkich maszynach wirtualnych w planie do najnowszego punktu odzyskiwania spójnego z aplikacją przetwarzanego przez odzysk witryny. Aby wyświetlić najnowszy punkt odzyskiwania dla określonej maszyny Wirtualnej, sprawdź **najnowsze punkty odzyskiwania** w ustawieniach maszyny Wirtualnej.
    - **Najnowsze:** Ta opcja najpierw przetwarza wszystkie dane, które zostały wysłane do usługi site recovery, aby utworzyć punkt odzyskiwania dla każdej maszyny Wirtualnej przed przepełnieniem jej po awarii. Ta opcja zapewnia najniższy cel punktu odzyskiwania (Cel punktu odzyskiwania), ponieważ maszyna wirtualna utworzona po przełączeniu w błąd będzie miała wszystkie dane replikowane do usługi Site Recovery po wyzwoleniu pracy awaryjnej.
    - **Najnowsze przetwarzane maszyny wielowęzłowe:** Ta opcja jest dostępna dla planów odzyskiwania z co najmniej jedną maszyną wirtualną, która ma włączoną spójność wielu maszyn wirtualnych. Maszyny wirtualne z włączoną włączoną funkcją fail over do najnowszego wspólnego wspólnego punktu odzyskiwania spójnego multi-VM. Inne maszyny wirtualne w pracy awaryjnej do ostatniego przetworzonego punktu odzyskiwania.  
    - **Najnowsze aplikacje z wieloma maszynami wirtualnymi spójne:** Ta opcja jest dostępna dla planów odzyskiwania z co najmniej jedną maszyną wirtualną, która ma włączoną spójność wielu maszyn wirtualnych. Maszyny wirtualne, które są częścią grupy replikacji w trybie fail over do najnowszego wspólnego wspólnego punktu odzyskiwania spójne aplikacji wielu maszyn wirtualnych. Inne maszyny wirtualne w pracy awaryjnej do ich ostatni punkt odzyskiwania spójne aplikacji.
    - **Niestandardowe:** Użyj tej opcji, aby przewinąć w stan faila dla określonej maszyny Wirtualnej do określonego punktu odzyskiwania.
3. Wybierz sieć wirtualną platformy Azure, w której będą tworzone testowe maszyny wirtualne.

    - Usługa Site Recovery próbuje utworzyć testowe maszyny wirtualne w podsieci o tej samej nazwie i tym samym adresie IP, co w ustawieniach **obliczeń i sieci** maszyny Wirtualnej.
    - Jeśli podsieć o tej samej nazwie nie jest dostępna w sieci wirtualnej platformy Azure używanej do testowania pracy awaryjnej, testowa maszyna wirtualna jest tworzona alfabetycznie w pierwszej podsieci.
    - Jeśli ten sam adres IP nie jest dostępny w podsieci, maszyna wirtualna odbiera inny dostępny adres IP w podsieci. [Dowiedz się więcej](#create-a-network-for-test-failover).
4. Jeśli po awarii na platformie Azure jest włączona i szyfrowanie danych jest włączone, w **klucz szyfrowania**wybierz certyfikat, który został wystawiony po włączeniu szyfrowania podczas instalacji dostawcy. Ten krok można zignorować, jeśli szyfrowanie nie jest włączone.
5. Śledzenie postępu pracy awaryjnej na karcie **Zadania.** Powinieneś być w stanie zobaczyć testową maszynę repliki w witrynie Azure portal.
6. Aby zainicjować połączenie RDP z maszyną wirtualną platformy Azure, należy dodać publiczny adres IP w interfejsie [sieciowym](https://aka.ms/addpublicip) maszyny wirtualnej, która uległa awarii.
7. Gdy wszystko działa zgodnie z oczekiwaniami, kliknij przycisk **Oczyszczanie testu pracy awaryjnej**. Spowoduje to usunięcie maszyn wirtualnych, które zostały utworzone podczas pracy awaryjnej testu.
8. W **uwagach**należy rejestrować i zapisywać wszystkie obserwacje związane z testem w wersji failover.


![Test pracy w trybie failover](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

Po wyzwoleniu pracy awaryjnej testu wystąpią następujące zdarzenia:

1. **Wymagania wstępne:** Sprawdzanie wymagań wstępnych jest uruchamiane, aby upewnić się, że spełnione są wszystkie warunki wymagane dla pracy awaryjnej.
2. **Przewijaniu w**błąd: procesy pracy awaryjnej i przygotowane dane, dzięki czemu można utworzyć z niej maszynę wirtualną platformy Azure.
3. **Najnowsze**: Jeśli wybrano najnowszy punkt odzyskiwania, punkt odzyskiwania jest tworzony na podstawie danych, które zostały wysłane do usługi.
4. **Start:** Ten krok tworzy maszynę wirtualną platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.

### <a name="failover-timing"></a>Czas pracy awaryjnej

W następujących scenariuszach praca awaryjna wymaga dodatkowego kroku pośredniego, który zwykle trwa około 8 do 10 minut, aby zakończyć:

* Maszyny wirtualne VMware z wersją usługi mobilności starszą niż 9.8
* Serwery fizyczne
* Maszyny wirtualne vmware z systemem Linux
* Maszyna wirtualna hyper-V chroniona jako serwery fizyczne
* VMware VM, gdzie następujące sterowniki nie są sterowniki rozruchowe:
    * storvsc ( storvsc )
    * Vmbus
    * storflt ( storflt )
    * intelide
    * Atapi
* VMware VMware VM, które nie mają włączonego protokołu DHCP, niezależnie od tego, czy używają dhcp lub statycznych adresów IP.

We wszystkich innych przypadkach nie jest wymagany żaden krok pośredni, a przeróbka awaryjna zajmuje znacznie mniej czasu.


## <a name="create-a-network-for-test-failover"></a>Tworzenie sieci do testowania pracy w trybie failover

Do testowania pracy w trybie failover zalecamy wybór sieci izolowanej od sieci produkcyjnej lokacji odzyskiwania, określonej w obszarze ustawień **Obliczenia i sieć** poszczególnych maszyn wirtualnych. Tworzone sieci wirtualne platformy Azure są domyślnie izolowane od innych sieci. Sieć testowa powinna odzwierciedlać sieć produkcyjną:

- Sieć testowa powinna mieć taką samą liczbę podsieci jak sieć produkcyjna. Podsieci powinny mieć takie same nazwy.
- W sieci testowej powinien być używany taki sam zakres adresów IP.
- Zaktualizuj system DNS sieci testowej, używając adresu IP określonego dla maszyny wirtualnej systemu DNS w obszarze ustawień **Obliczenia i sieć**. Aby uzyskać więcej informacji, zobacz sekcję [Zagadnienia dotyczące testowania trybu failover dla usługi Active Directory](site-recovery-active-directory.md#test-failover-considerations).


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Testowanie pracy awaryjnej w sieci produkcyjnej w lokacji odzyskiwania

Chociaż zaleca się użycie sieci testowej oddzielonej od sieci produkcyjnej, jeśli chcesz przetestować drążenie odzyskiwania po awarii w sieci produkcyjnej, należy zwrócić uwagę na następujące kwestie:

- Upewnij się, że podstawowa maszyna wirtualna jest zamykana po uruchomieniu pracy awaryjnej testu. W przeciwnym razie będą dwie maszyny wirtualne o tej samej tożsamości, działające w tej samej sieci w tym samym czasie. Może to prowadzić do nieoczekiwanych konsekwencji.
- Wszelkie zmiany maszyn wirtualnych utworzonych dla pracy awaryjnej testu są tracone podczas czyszczenia pracy awaryjnej. Te zmiany nie są replikowane z powrotem do podstawowej maszyny Wirtualnej.
- Testowanie w środowisku produkcyjnym prowadzi do przestoju aplikacji produkcyjnej. Użytkownicy nie powinni używać aplikacji uruchomionych na maszynach wirtualnych, gdy trwa testowanie pracy awaryjnej.  



## <a name="prepare-active-directory-and-dns"></a>Przygotowywanie usługi Active Directory i usługi DNS

Aby uruchomić test pracy awaryjnej do testowania aplikacji, należy wykonać kopię środowiska usługi Active Directory produkcji w środowisku testowym. Przeczytaj [zagadnienia dotyczące pracy awaryjnej testu dla usługi Active Directory,](site-recovery-active-directory.md#test-failover-considerations) aby dowiedzieć się więcej.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Jeśli chcesz połączyć się z maszynami wirtualnymi platformy Azure przy użyciu protokołu RDP/SSH po przejściu w stan failover, postępuj zgodnie z wymaganiami podsumowanym w tabeli.

**Tryb failover** | **Lokalizacja** | **Akcje**
--- | --- | ---
**Maszyna wirtualna platformy Azure z systemem Windows** | Komputer lokalny przed przejściem awaryjnym | Aby uzyskać dostęp do maszyny Wirtualnej platformy Azure przez Internet, włącz protokół RDP i upewnij się, że reguły protokołu TCP i UDP są dodawane dla **publicznych,** a protokół RDP jest dozwolony dla wszystkich profilów w > **aplikacjach dozwolonych zapory** **systemu Windows.**<br/><br/> Aby uzyskać dostęp do maszyny Wirtualnej platformy Azure za pośrednictwem połączenia lokacja lokacja, włącz prow na komputerze i upewnij się, że usługa RDP jest dozwolona w**aplikacjach i funkcjach dozwolonych przez** **Zaporę** -> systemu Windows dla sieci domen i sieci **prywatnych.**<br/><br/>  Upewnij się, że zasady sieci SAN systemu operacyjnego są ustawione na **OnlineAll**. [Dowiedz się więcej](https://support.microsoft.com/kb/3031135).<br/><br/> Upewnij się, że nie ma żadnych aktualizacji systemu Windows oczekujących na maszynie Wirtualnej podczas wyzwalania pracy awaryjnej. Aktualizacja systemu Windows może rozpocząć się po przełączeniu w pracę awaryjną i nie będzie można zalogować się na maszynę wirtualną, dopóki aktualizacja nie zostanie zakończona.
**Maszyna wirtualna platformy Azure z systemem Windows** | Maszyna wirtualna platformy Azure po przemijaniu awaryjnym |  [Dodaj publiczny adres IP](https://aka.ms/addpublicip) dla maszyny wirtualnej.<br/><br/> Reguły sieciowej grupy zabezpieczeń na maszynie wirtualnej po awarii (i podsieci platformy Azure, do której jest podłączona) muszą zezwalać na połączenia przychodzące z portem RDP.<br/><br/> Sprawdź **diagnostykę rozruchu,** aby zweryfikować zrzut ekranu maszyny Wirtualnej.<br/><br/> Jeśli nie możesz nawiązać połączenia, sprawdź, czy maszyna wirtualna jest uruchomiona, i zapoznaj się z tymi [wskazówkami dotyczącymi rozwiązywania problemów.](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)
**Maszyna wirtualna platformy Azure z systemem Linux** | Komputer lokalny przed przejściem awaryjnym | Upewnij się, że usługa Secure Shell na maszynie Wirtualnej jest ustawiona na automatyczne uruchamianie podczas rozruchu systemu.<br/><br/> Sprawdź, czy reguły zapory zezwalają na połączenie SSH.
**Maszyna wirtualna platformy Azure z systemem Linux** | Maszyna wirtualna platformy Azure po przemijaniu awaryjnym | Reguły sieciowej grupy zabezpieczeń na maszynie wirtualnej po awarii (i podsieci platformy Azure, do której jest podłączona) muszą zezwalać na połączenia przychodzące z portem SSH.<br/><br/> [Dodaj publiczny adres IP](https://aka.ms/addpublicip) dla maszyny wirtualnej.<br/><br/> Sprawdź **diagnostykę rozruchu,** aby uzyskać zrzut ekranu maszyny Wirtualnej.<br/><br/>

Wykonaj czynności opisane [tutaj](site-recovery-failover-to-azure-troubleshoot.md), aby rozwiązać wszystkie problemy z łącznością po przejściu do trybu failover.

## <a name="next-steps"></a>Następne kroki
Po zakończeniu ćwiczeń odzyskiwania po awarii dowiedz się więcej o innych typach [pracy awaryjnej.](site-recovery-failover.md)
