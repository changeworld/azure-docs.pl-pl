---
title: Testowanie trybu failover na platformie Azure w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o uruchamianiu test trybu failover z lokalnej na platformie Azure, przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/08/2018
ms.author: raynew
ms.openlocfilehash: bdbeee0e0caaa0e6db7249c2f4aeaa19d5d2ed0d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643655"
---
# <a name="test-failover-to-azure-in-site-recovery"></a>Testowanie trybu failover na platformie Azure w usłudze Site Recovery


W tym artykule opisano sposób uruchamiania wyszczególniania odzyskiwania po awarii na platformę Azure przy użyciu usługi Site Recovery test trybu failover.  

Możesz uruchomić test trybu failover do weryfikowanie replikacji i strategii odzyskiwania po awarii, bez utraty danych lub przestoju. Test trybu failover bez wpływu na trwającej replikacji lub w środowisku produkcyjnym. Można uruchomić testowy tryb failover na określonej maszyny wirtualnej (VM) lub na [planu odzyskiwania](site-recovery-create-recovery-plans.md) zawierający wiele maszyn wirtualnych. 


## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover
Ta procedura opisuje sposób testować tryb failover planu odzyskiwania. 

![Test pracy w trybie failover](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. W usłudze Site Recovery w portalu Azure, kliknij przycisk **plany odzyskiwania** > *recoveryplan_name* > **testowy tryb Failover**.
2. Wybierz **punkt odzyskiwania** któremu do pracy awaryjnej. Możesz użyć jednej z następujących opcji:
    - **Najnowsze przetworzone**: Ta opcja awaryjnie wszystkie maszyny wirtualne w planie do ostatniego punktu odzyskiwania przetworzone przez usługę Site Recovery. Aby wyświetlić najnowsze punktu dla określonej maszyny Wirtualnej odzyskiwania, należy sprawdzić **najnowsze punkty odzyskiwania** w ustawieniach maszyny Wirtualnej. Ta opcja zapewnia niską wartość celu czasu odzyskiwania, ponieważ nie wymaga przetwarzania nieprzetworzonych danych.
    - **Najnowsza wersja aplikacji spójne**: Ta opcja awaryjnie wszystkie maszyny wirtualne w planie punktowi najnowszych odzyskiwania zapewniających spójność aplikacji przetworzone przez usługę Site Recovery. Aby wyświetlić najnowsze punktu dla określonej maszyny Wirtualnej odzyskiwania, należy sprawdzić **najnowsze punkty odzyskiwania** w ustawieniach maszyny Wirtualnej. 
    - **Najnowsze**: tę opcję, najpierw przetwarza wszystkie dane, które zostało wysłane do usługi Site Recovery, aby utworzyć punkt odzyskiwania dla każdej maszyny Wirtualnej przed jej awarii. Ta opcja umożliwia najniższy RPO (cel punktu odzyskiwania), ponieważ maszyna wirtualna utworzona po trybu failover ma wszystkie dane, które są replikowane do usługi Site Recovery pracę w trybie failover zostało wyzwolone.
    - **Najnowsze wielu maszyn wirtualnych przetwarzane**: Ta opcja jest dostępna dla planów odzyskiwania z przynajmniej jednej maszyny wirtualnej, które mają włączoną spójnością wielu maszyn wirtualnych. Maszyny wirtualne z ustawieniem włączone przełączyć najnowsze wspólnych punktów odzyskiwania zapewniających spójność wielu maszyn wirtualnych. Innych maszyn wirtualnych w trybie Failover do najnowszego punktu odzyskiwania przetworzone.  
    - **Najnowsze wielu maszyn wirtualnych całej aplikacji**: Ta opcja jest dostępna dla planów odzyskiwania z przynajmniej jednej maszyny wirtualnej, które mają włączoną spójnością wielu maszyn wirtualnych. Maszyny wirtualne, które są częścią grupy replikacji w trybie Failover do najnowszego punktu odzyskiwania zapewniających spójność aplikacji wspólnej wielu maszyn wirtualnych. Pozostałe maszyny wirtualne przełączone awaryjnie do ich najnowszy punkt odzyskiwania zapewniających spójność aplikacji. 
    - **Niestandardowe**: Użyj tej opcji do określonej maszyny Wirtualnej do określonego punktu w tryb failover.
3. Wybierz sieć wirtualną platformy Azure w którym zostanie utworzona testu maszyn wirtualnych.

    - Prób odzyskania lokacji można utworzyć testu maszyn wirtualnych w podsieci o tej samej nazwie i tego samego adresu IP, jak te zawarte w **obliczenia i sieć** ustawienia maszyny wirtualnej.
    - Jeśli podsieć o takiej samej nazwie nie jest dostępny w sieci wirtualnej platformy Azure, używane do testowania trybu failover, następnie testu zostanie utworzona maszyna wirtualna w pierwszej podsieci alfabetycznie.
    - Jeśli tego samego adresu IP nie jest dostępny w podsieci, następnie maszyna wirtualna odbiera kolejny dostępny adres IP w podsieci. [Dowiedz się więcej](#create-a-network-for-test-failover).
4. Jeśli użytkownik jest przechodzenie w tryb failover na platformie Azure i szyfrowanie danych jest włączone, w **klucza szyfrowania**, wybierz certyfikat, który został wystawiony, włączenie szyfrowania podczas instalacji dostawcy. Możesz zignorować ten krok nie jest włączone szyfrowanie.
5. Śledzić postęp trybu failover na **zadania** kartę. Można wyświetlić testową maszynę repliki w portalu Azure.
6. Aby zainicjować połączenie RDP z maszyny Wirtualnej Azure, musisz [Dodaj publiczny adres IP](https://aka.ms/addpublicip) dla interfejsu sieci w trybie Failover maszyny Wirtualnej. 
7. Gdy wszystko działa zgodnie z oczekiwaniami, kliknij przycisk **oczyszczania testowy tryb failover**. Spowoduje to usunięcie maszyny wirtualne, które zostały utworzone podczas testowania trybu failover.
8. W obszarze **Uwagi** zarejestruj i zapisz wszelkie obserwacje związane z testem pracy w trybie failover. 


![Test pracy w trybie failover](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

Po wyzwoleniu test trybu failover, są następujące operacje:

1. **Wymagania wstępne**: uruchomieniu, aby upewnić się, że spełniono wszystkie warunki wymagane do trybu failover sprawdzania wymagań wstępnych.
2. **Tryb failover**: tryb failover przetwarza i przygotowane danych, dzięki czemu można z niej utworzyć maszyny Wirtualnej platformy Azure.
3. **Najnowsze**: Jeśli wybrano najnowszy punkt odzyskiwania, punkt odzyskiwania jest utworzone na podstawie danych wysyłanym do usługi.
4. **Uruchom**: ten krok umożliwia utworzenie maszyny wirtualnej platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.

### <a name="failover-timing"></a>Czas pracy awaryjnej

W następujących scenariuszach pracy awaryjnej wymaga bardzo pośredniego kroku, który zazwyczaj trwa około 8 do 10 minut na zakończenie:

* Maszyny wirtualne VMware z wersją starszą niż 9,8 usługi mobilności
* Serwerów fizycznych
* Maszyny wirtualne VMware systemu Linux
* Maszyna wirtualna funkcji Hyper-V chronione jako serwerów fizycznych
* Gdzie następujące sterowniki nie są sterowniki rozruchu maszyny Wirtualnej VMware:
    * storvsc
    * vmbus
    * storflt
    * Intelide
    * ATAPI
* Maszyna wirtualna oprogramowania VMware który DHCP nie jest włączone, rrespective z tego, czy używasz DHCP lub statyczne adresy IP.

We wszystkich innych przypadkach nie jest wymagane nie pośredniego kroku i trybu failover trwa znacznie krócej.


## <a name="create-a-network-for-test-failover"></a>Tworzenie sieci do testowania trybu failover

Zaleca się do testowania trybu failover, wybranie sieci, która jest odizolowana od określonych w sieci lokacji odzyskiwania środowiska produkcyjnego **obliczenia i sieć** ustawienia dla każdej maszyny Wirtualnej. Domyślnie podczas tworzenia sieci wirtualnej platformy Azure jest odizolowany od innych sieci. Sieci produkcyjnej, powinien naśladować sieci testowej:

- Sieci testowej powinien mieć takiej samej liczby podsieci jak sieci produkcyjnej. Podsieci powinny mieć tej samej nazwy.
- Test sieci należy używać tego samego zakresu adresów IP.
- Zaktualizuj DNS w sieci testowej przy użyciu adresu IP określonej dla maszyny Wirtualnej DNS w **obliczenia i sieć** ustawienia. Odczyt [test pracy awaryjnej zagadnienia dotyczące usługi Active Directory](site-recovery-active-directory.md#test-failover-considerations) więcej szczegółów.


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Testowanie trybu failover do środowiska produkcyjnego w lokacji odzyskiwania

Mimo że zalecane jest użycie sieci testowej oddzielne od produkcyjnego środowiska sieciowego, jeśli chcesz przetestować wyszczególniania odzyskiwania po awarii, w sieci produkcyjnej, należy uwzględnić następujące informacje: 

- Upewnij się, że podstawowej maszyny Wirtualnej jest zamknięta podczas uruchamiania testowania trybu failover. W przeciwnym razie będzie dwóch maszyn wirtualnych o tej samej tożsamości, uruchomiony w tej samej sieci, w tym samym czasie. Może to spowodować nieoczekiwane problemy.
- Wszelkie zmiany, aby maszyny wirtualne utworzone do testowania trybu failover zostaną utracone podczas czyszczenia pracy awaryjnej. Te zmiany nie są replikowane do podstawowej maszyny Wirtualnej.
- Testowanie w środowisku produkcyjnym prowadzi do przestojów aplikacji produkcyjnej. Użytkownicy nie powinien używać aplikacji działających na maszynach wirtualnych podczas testowania trybu failover jest w toku.  



## <a name="prepare-active-directory-and-dns"></a>Przygotowanie usługi Active Directory i DNS

Aby uruchomić test trybu failover do testowania aplikacji, należy kopię w środowisku produkcyjnym usługi Active Directory w środowisku testowym. Odczyt [test pracy awaryjnej zagadnienia dotyczące usługi Active Directory](site-recovery-active-directory.md#test-failover-considerations) Aby dowiedzieć się więcej.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Aby nawiązać połączenie przy użyciu protokołu RDP po pracy awaryjnej maszyn wirtualnych platformy Azure, należy wykonać wymagania podsumowane w tabeli.

**Tryb failover** | **Lokalizacja** | **Akcje**
--- | --- | ---
**Maszyna wirtualna platformy Azure, systemem Windows** | Na lokalnej maszynie przed trybu failover | Aby uzyskać dostęp do maszyny Wirtualnej platformy Azure za pośrednictwem Internetu, Włącz protokół RDP i upewnij się, że reguły TCP i UDP są dodawane do **publicznego**, oraz że RDP jest dozwolona dla wszystkich profilów w **zapory systemu Windows**  >  **Dozwolone aplikacje**.<br/><br/> Aby uzyskać dostęp do maszyny Wirtualnej platformy Azure za pośrednictwem połączenia lokacja lokacja, Włącz protokół RDP na maszynie i upewnij się, że RDP jest dozwolony w **zapory systemu Windows** -> **dozwolone aplikacje i funkcje**, dla **Domeny i prywatnej** sieci.<br/><br/>  Upewnij się, że system operacyjny zasad sieci SAN jest ustawiony **OnlineAll**. [Dowiedz się więcej](https://support.microsoft.com/kb/3031135).<br/><br/> Upewnij się, że nie ma żadnych aktualizacji systemu Windows oczekujących na maszynie Wirtualnej, gdy użytkownik zainicjuje tryb failover. Usługi Windows update może być uruchamiany po przejścia w tryb failover i będzie niemożliwe do logowania się do maszyny Wirtualnej do momentu ukończenia aktualizacji. 
**Maszyna wirtualna platformy Azure, systemem Windows** | Maszyna wirtualna platformy Azure po trybu failover |  [Dodaj publiczny adres IP](https://aka.ms/addpublicip) dla maszyny wirtualnej.<br/><br/> Reguły grupy zabezpieczeń sieci w trybie Failover maszyny Wirtualnej (i podsieci platformy Azure, do którego jest podłączony) muszą zezwalać na połączenia przychodzące na portach protokołu RDP.<br/><br/> Sprawdź **diagnostyki rozruchu** można zweryfikować zrzut ekranu maszyny wirtualnej.<br/><br/> Jeśli nie można połączyć, sprawdź, czy maszyna wirtualna jest uruchomiona i przejrzyj te [porady dotyczące rozwiązywania problemów](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Maszyna wirtualna platformy Azure, systemem Linux** | Na lokalnej maszynie przed trybu failover | Upewnij się, że Usługa Secure Shell na maszynie Wirtualnej jest ustawiona na automatyczne uruchomienie przy rozruchu systemu.<br/><br/> Sprawdź, czy reguły zapory zezwalają na połączenie SSH.
**Maszyna wirtualna platformy Azure, systemem Linux** | Maszyna wirtualna platformy Azure po trybu failover | Reguły grupy zabezpieczeń sieci w trybie Failover maszyny Wirtualnej (i podsieci platformy Azure, do którego jest podłączony) muszą zezwalać na połączenia przychodzące do portu SSH.<br/><br/> [Dodaj publiczny adres IP](https://aka.ms/addpublicip) dla maszyny wirtualnej.<br/><br/> Sprawdź **diagnostyki rozruchu** dla zrzut ekranu maszyny wirtualnej.<br/><br/>



## <a name="next-steps"></a>Kolejne kroki
Po zakończeniu wyszczególniania odzyskiwania po awarii, więcej informacji na temat innych typów [pracy awaryjnej](site-recovery-failover.md).
