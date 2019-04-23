---
title: Konfigurowanie odzyskiwania po awarii dla usługi Active Directory i DNS przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak wdrożyć rozwiązanie odzyskiwania po awarii dla usługi Active Directory i DNS przy użyciu usługi Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 58e360bb355c7faf9608b00dd65b14f27aca4367
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790547"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Konfigurowanie odzyskiwania po awarii dla usługi Active Directory i DNS

Aplikacje dla przedsiębiorstw, takich jak SharePoint, Dynamics AX i SAP są zależne od usługi Active Directory i infrastruktury DNS, aby działo poprawnie. Podczas konfigurowania odzyskiwania po awarii dla aplikacji często trzeba odzyskać Active Directory i DNS, przed odzyskaniem inne składniki aplikacji w celu zapewnienia ich funkcjonalności właściwej aplikacji.

Możesz użyć [Site Recovery](site-recovery-overview.md) utworzyć plan odzyskiwania po awarii dla usługi Active Directory. W przypadku przerwy w działaniu można zainicjować trybu failover. Może mieć usługi Active Directory w górę i uruchamiania w ciągu kilku minut. Jeśli w lokacji głównej zostały wdrożone usługi Active Directory dla wielu aplikacji, na przykład dla programu SharePoint i SAP, możesz zechcieć do pracy awaryjnej w całej lokacji. Można najpierw przejścia w tryb failover do usługi Active Directory przy użyciu Site Recovery. Następnie w tryb failover inne aplikacje, korzystając z planów odzyskiwania specyficzne dla aplikacji.

W tym artykule wyjaśniono, jak utworzyć rozwiązanie odzyskiwania po awarii dla usługi Active Directory. Zawiera wymagania wstępne i instrukcje trybu failover. Należy zapoznać się z usługi Active Directory i usługi Site Recovery, przed przystąpieniem do wykonywania.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli przeprowadzasz replikację do platformy Azure, [przygotowywanie zasobów platformy Azure](tutorial-prepare-azure.md), w tym subskrypcji usługi Azure Virtual Network, konta magazynu i magazyn usługi Recovery Services.
* Zapoznaj się z [wymaganiami dotyczącymi obsługi](site-recovery-support-matrix-to-azure.md) wszystkich składników.

## <a name="replicate-the-domain-controller"></a>Replikowanie kontrolera domeny

- Należy zdefiniować replikacji usługi Site Recovery, na co najmniej jednej maszyny Wirtualnej, który hostuje kontrolera domeny lub systemu DNS.
- Jeśli masz wiele kontrolerów domeny w danym środowisku, można także skonfigurować dodatkowy kontroler domeny w lokacji docelowej. Dodatkowy kontroler domeny może być na platformie Azure lub w pomocniczej w lokalnym centrum danych.
- Jeśli masz tylko kilka aplikacji i jeden kontroler domeny, możesz chcieć razem przechodzą w całej lokacji. W tym przypadku zaleca się przy użyciu Site Recovery do replikacji kontrolera domeny w lokacji docelowej, (lub na platformie Azure w centrum danych z lokalnego, pomocniczego). Możesz użyć tego samego kontrolera domeny replikowanych lub DNS maszyny wirtualnej pod kątem [testowanie trybu failover](#test-failover-considerations).
- - Jeśli masz wiele aplikacji i więcej niż jeden kontroler domeny w środowisku lub jeśli planowane jest do trybu failover kilka aplikacji w czasie, oprócz replikowania maszyny wirtualnej kontrolera domeny z usługą Site Recovery, firma Microsoft zaleca skonfigurowanie dodatkowy kontroler domeny w lokacji docelowej (lub na platformie Azure w centrum danych z lokalnego, pomocniczego). Aby uzyskać [testowanie trybu failover](#test-failover-considerations), można użyć kontrolera domeny, które są replikowane przez usługę Site Recovery. Dla trybu failover można użyć dodatkowy kontroler domeny w lokacji docelowej.

## <a name="enable-protection-with-site-recovery"></a>Włącz ochronę za pomocą usługi Site Recovery

Usługa Site Recovery umożliwia ochronę maszyny wirtualnej, który hostuje kontrolera domeny lub DNS.

### <a name="protect-the-vm"></a>Ochrona maszyny Wirtualnej
Kontroler domeny, które są replikowane przy użyciu usługi Site Recovery jest używana do [testowanie trybu failover](#test-failover-considerations). Upewnij się, że spełnia następujące wymagania:

1. Kontroler domeny jest serwerem wykazu globalnego.
2. Kontroler domeny powinien być właścicielem roli FSMO, ról, które są wymagane podczas testowania trybu failover. W przeciwnym wypadku te role będą musiały mieć [zajętych](https://aka.ms/ad_seize_fsmo) po pracy w trybie failover.

### <a name="configure-vm-network-settings"></a>Konfigurowanie ustawień sieci maszyn wirtualnych
Dla maszyny wirtualnej, który hostuje kontrolera domeny lub DNS w usłudze Site Recovery, należy skonfigurować ustawienia sieciowe w ramach **obliczenia i sieć** ustawienia zreplikowanej maszyny wirtualnej. Daje to gwarancję, że maszyna wirtualna została dołączona do odpowiedniej sieci po włączeniu trybu failover.

## <a name="protect-active-directory"></a>Ochrona usługi Active Directory

### <a name="site-to-site-protection"></a>Ochrona lokacji do lokacji
Tworzenie kontrolera domeny w lokacji dodatkowej. W przypadku podwyższania poziomu serwera do roli kontrolera domeny, należy określić nazwę tej samej domenie, która jest używana w lokacji głównej. Możesz użyć **Lokacje usługi Active Directory i usługi** przystawki do konfigurowania ustawień na obiekt łącza lokacji, do którego dodawane są lokacje. Konfigurując ustawienia dla łącza lokacji, można kontrolować, podczas replikacji między dwoma lub większą liczbę lokacji i jak często występuje. Aby uzyskać więcej informacji, zobacz [planowanie replikacji między lokacjami](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Ochrona lokacji na platformie Azure
Najpierw należy utworzyć kontroler domeny w sieci wirtualnej platformy Azure. W przypadku podwyższania poziomu serwera do roli kontrolera domeny, należy określić tą samą nazwą domeny, która jest używana w lokacji głównej.

Następnie należy ponownie skonfigurować serwer DNS dla sieci wirtualnej użyć serwera DNS na platformie Azure.

![Azure Network](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Ochrona Azure – Azure
Najpierw należy utworzyć kontroler domeny w sieci wirtualnej platformy Azure. W przypadku podwyższania poziomu serwera do roli kontrolera domeny, należy określić tą samą nazwą domeny, która jest używana w lokacji głównej.

Następnie należy ponownie skonfigurować serwer DNS dla sieci wirtualnej użyć serwera DNS na platformie Azure.

## <a name="test-failover-considerations"></a>Testowanie trybu failover zagadnienia
Aby uniknąć wpływu na obciążenia produkcyjne, test przejścia do trybu failover w sieci, która jest odizolowana od sieci produkcyjnej.

Większość aplikacji wymaga obecności kontrolera domeny lub serwer DNS. W związku z tym zanim aplikacja zakończy się niepowodzeniem, za pośrednictwem, możesz utworzyć kontroler domeny w sieci izolowanej ma być używany do testowania trybu failover. Najprostszym sposobem na to jest usługa Site Recovery pozwala replikować maszynę wirtualną, który hostuje kontrolera domeny lub DNS. Następnie należy uruchomić test trybu failover maszyny wirtualnej kontrolera domeny, przed uruchomieniem testu trybu failover planu odzyskiwania dla aplikacji. Poniżej przedstawiono, jak to zrobić:

1. Usługa Site Recovery do [replikować](vmware-azure-tutorial.md) maszyny wirtualnej, który hostuje kontrolera domeny lub DNS.
2. Tworzenie sieci izolowanej. Sieci wirtualnej, który zostanie utworzony w usłudze Azure jest odizolowana od innych sieci domyślnie. Zaleca się używać tego samego zakresu adresów IP dla tej sieci, którego używasz w sieci produkcyjnej. Nie włączaj połączenia lokacja lokacja w tej sieci.
3. Podaj adres IP serwera DNS w sieci izolowanej. Użyj adresu IP, który oczekiwać DNS maszyny wirtualnej, aby pobrać. Jeśli przeprowadzasz replikację do platformy Azure, podaj adres IP maszyny wirtualnej, która jest używana w trybie failover. Aby wprowadzić adres IP w replikowanej maszyny wirtualnej w **obliczenia i sieć** ustawienia, wybierz opcję **docelowy adres IP** ustawienia.

    ![Sieć testowa platformy Azure](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > Usługa Site Recovery próbuje utworzyć testowych maszyn wirtualnych w podsieci o takiej samej nazwie, jak i za pomocą tego samego adresu IP, który znajduje się w **obliczenia i sieć** ustawienia maszyny wirtualnej. Jeśli podsieć o takiej samej nazwie, nie jest dostępny w sieci wirtualnej platformy Azure, który jest udostępniany dla testu trybu failover, testowej maszyny wirtualnej jest tworzony w kolejności alfabetycznej pierwszej podsieci.
    >
    > Jeśli docelowy adres IP jest częścią wybranej podsieci, Usługa Site Recovery próbuje utworzyć testową maszynę wirtualną w tryb failover przy użyciu docelowy adres IP. Jeśli docelowy adres IP nie jest częścią wybranej podsieci, testową maszynę wirtualną w tryb failover jest utworzone za pomocą następnego dostępnego adresu IP w wybranej podsieci.
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>Testowanie trybu failover do lokacji dodatkowej

1. Jeśli przeprowadzasz replikację do innej lokacji lokalnej i korzystania z protokołu DHCP [Konfigurowanie systemu DNS i DHCP do testowania trybu failover](hyper-v-vmm-test-failover.md#prepare-dhcp).
2. Wykonaj test trybu failover maszyny wirtualnej kontrolera domeny, która działa w sieci izolowanej. Użyj najnowszych dostępnych *spójne na poziomie aplikacji* punktu odzyskiwania maszyny wirtualnej kontrolera domeny w celu testowania trybu failover.
3. Uruchom testowanie trybu failover planu odzyskiwania, który zawiera maszyny wirtualne, na których działa aplikacja.
4. Po zakończeniu testowania, *oczyszczania testu trybu failover* na maszynie wirtualnej kontrolera domeny. Ten krok polega na usunięciu kontrolera domeny, który został utworzony do testowania trybu failover.


### <a name="remove-references-to-other-domain-controllers"></a>Usuń odwołania do innych kontrolerów domeny
Po zainicjowaniu testowy tryb failover nie uwzględniają wszystkich kontrolerów domeny w sieci testowej. Aby usunąć odwołania do innych kontrolerów domeny, które istnieją w środowisku produkcyjnym, konieczne może być [przejmowanie ról FSMO Active Directory](https://aka.ms/ad_seize_fsmo) i wykonaj [czyszczenie metadanych](https://technet.microsoft.com/library/cc816907.aspx) brakujących kontrolerów domeny .


### <a name="issues-caused-by-virtualization-safeguards"></a>Problemy spowodowane przez zabezpieczenia wirtualizacji

> [!IMPORTANT]
> Niektóre z konfiguracji opisanych w tej sekcji nie są standardowe lub wartości domyślnej konfiguracji kontrolera domeny. Jeśli nie chcesz wprowadzić te zmiany do poziomu kontrolera domeny w środowisku produkcyjnym, można utworzyć kontroler domeny, który jest przeznaczony dla usługi Site Recovery na potrzeby testowania trybu failover. Te zmiany tylko do tego kontrolera domeny.  
>
>

Począwszy od systemu Windows Server 2012, [dodatkowe zabezpieczenia są wbudowane w Active Directory Domain Services (AD DS)](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Te zabezpieczenia chronić zwirtualizowane kontrolery domeny przed wycofywania numerów USN, gdy podstawowej platformy funkcji hypervisor obsługuje **identyfikator VM-GenerationID**. Platforma Azure obsługuje **identyfikator VM-GenerationID**. W związku z tym kontrolerów domeny z systemem Windows Server 2012 lub nowszy na usłudze Azure virtual machines ma te dodatkowe zabezpieczenia.


Gdy **identyfikator VM-GenerationID** jest resetowany **InvocationID** wartości bazy danych usług AD DS jest także zresetować. Ponadto pulę identyfikatorów RID jest odrzucana i folderu sysvol jest oznaczony jako nieautorytatywnego. Aby uzyskać więcej informacji, zobacz [wprowadzenie do wirtualizacji usług Active Directory Domain Services](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) i [bezpiecznie wirtualizacja DFSR](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/).

Przechodzenie w tryb failover na platformie Azure może spowodować **identyfikator VM-GenerationID** do zresetowania. Resetowanie **identyfikator VM-GenerationID** wyzwala dodatkowe zabezpieczenia, po uruchomieniu maszyny wirtualnej kontrolera domeny, na platformie Azure. Może to spowodować *znaczne opóźnienie* jest możliwość Zaloguj się do maszyny wirtualnej kontrolera domeny.

Ponieważ ten kontroler domeny jest używany tylko w przypadku testowania trybu failover, zabezpieczenia wirtualizacji nie są niezbędne. Aby upewnić się, że **identyfikator VM-GenerationID** nie zmienia się wartość dla maszyny wirtualnej kontrolera domeny, można zmienić wartości następujących DWORD, aby **4** na kontrolerze domeny w środowisku lokalnym:


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>Objawy zabezpieczenia wirtualizacji

Po przejściu w tryb failover testów zostają uruchomione zabezpieczenia wirtualizacji, może zostać wyświetlony co najmniej jeden z następujących objawów:  

* **GenerationID** wartość zmienia się.

    ![Zmiany Identyfikatora generowania](./media/site-recovery-active-directory/Event2170.png)

* **InvocationID** wartość zmienia się.

    ![Zmiany Identyfikatora wywołania](./media/site-recovery-active-directory/Event1109.png)

* SYSVOL folder i NETLOGON udziały nie są dostępne.

    ![Udostępnij folder SYSVOL](./media/site-recovery-active-directory/sysvolshare.png)

    ![NtFrs sysvol folder](./media/site-recovery-active-directory/Event13565.png)

* Baz danych DFSR są usuwane.

    ![Baz danych DFSR zostaną usunięte.](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Rozwiązywanie problemów związanych z kontrolerami domeny podczas testowania trybu failover

> [!IMPORTANT]
> Niektóre z konfiguracji opisanych w tej sekcji nie są standardowe lub wartości domyślnej konfiguracji kontrolera domeny. Jeśli nie chcesz wprowadzić te zmiany do poziomu kontrolera domeny w środowisku produkcyjnym, można utworzyć kontroler domeny, który jest przeznaczony do odzyskiwania lokacji testowania trybu failover. Należy wprowadzić zmiany tylko do tego kontrolera domeny dedykowanego.  
>
>

1. W wierszu polecenia Uruchom następujące polecenie, aby sprawdzić, czy udostępnionego folderu sysvol i NETLOGON folderu:

    `NET SHARE`

2. W wierszu polecenia Uruchom następujące polecenie, aby upewnić się, że kontroler domeny działa prawidłowo:

    `dcdiag /v > dcdiag.txt`

3. Wyszukaj następujący tekst w dzienniku danych wyjściowych. Tekst potwierdza, że kontroler domeny działa poprawnie.

    * "Sukces test połączenia"
    * "testów zakończonych powodzeniem anonsowanie"
    * "testów zakończonych powodzeniem MachineAccount"

Jeśli powyższe warunki są spełnione, istnieje prawdopodobieństwo, że kontroler domeny działa poprawnie. Jeśli nie jest, wykonaj następujące czynności:

1. Wykonaj Przywracanie autorytatywne kontrolera domeny. Należy przestrzegać następujących informacji:
    * Chociaż nie jest zalecane [replikacji usługi FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/), jeśli używasz replikacji usługi FRS, postępuj zgodnie z instrukcjami dla przywracania autorytatywnego. Proces jest opisany w [Usługa replikacji plików za pomocą klucza rejestru BurFlags](https://support.microsoft.com/kb/290762).

        Aby uzyskać więcej informacji na temat BurFlags, zobacz wpis w blogu [D2 i D4: Co to jest dla? ](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Jeśli używasz replikacji DFSR, wykonaj kroki procedury przywracania autorytatywnego. Proces jest opisany w [wymusić synchronizację autorytatywne autorytatywną i nieautorytatywną folderu sysvol zreplikowanego DFSR folderu (na przykład "D4/D2" w przypadku usługi FRS)](https://support.microsoft.com/kb/2218556).

        Można również użyć funkcji programu PowerShell. Aby uzyskać więcej informacji, zobacz [funkcji PowerShell autorytatywne/przywracanie nieautorytatywne DFSR SYSVOL](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/).

2. Pominąć wymaganie synchronizacji początkowej, ustawiając klucz rejestru **0** na kontrolerze domeny w środowisku lokalnym. Jeśli wartości DWORD nie istnieje, możesz utworzyć je w obszarze **parametry** węzła.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z 4013 identyfikator zdarzenia DNS: Serwer DNS nie może załadować stref DNS zintegrowanych usługi AD](https://support.microsoft.com/kb/2001093).

3. Wyłącz wymagania serwera wykazu globalnego i udostępnienie do sprawdzania poprawności nazwy logowania użytkownika. W tym celu na kontrolerze domeny w środowisku lokalnym, należy ustawić następujący klucz rejestru **1**. Jeśli wartości DWORD nie istnieje, możesz utworzyć je w obszarze **Lsa** węzła.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    Aby uzyskać więcej informacji, zobacz [wyłączyć wymaganie serwera wykazu globalnego i udostępnienie do weryfikowania logowania użytkowników](https://support.microsoft.com/kb/241789).

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS i kontroler domeny na różnych maszynach

Jeśli korzystasz z kontrolerem domeny i DNs na tej samej maszyny Wirtualnej, możesz pominąć tę procedurę.


Jeśli DNS nie znajduje się w tej samej maszyny Wirtualnej jako kontroler domeny, należy utworzyć maszyny Wirtualnej z systemem DNS do testowania trybu failover. Możesz użyć świeże serwera DNS i utworzyć wymagane strefy. Na przykład jeśli domena usługi Active Directory to contoso.com, należy utworzyć strefę DNS o nazwie contoso.com. Wpisy, które odnoszą się do usługi Active Directory należy zaktualizować w systemie DNS w następujący sposób:

1. Upewnij się, że te ustawienia zostały spełnione przed rozpoczęciem żadną inną maszynę wirtualną w planie odzyskiwania:
   * Strefa musi mieć nazwę po nazwie katalogu głównego lasu.
   * Strefa musi być kopią zapasową w pliku.
   * Strefa musi być włączona bezpieczne jak i aktualizacje.
   * Mechanizm rozpoznawania maszyny wirtualnej, który hostuje kontrolera domeny powinien wskazywać adres IP maszyny wirtualnej DNS.

2. Na maszynie Wirtualnej, który hostuje kontrolera domeny, uruchom następujące polecenie:

    `nltest /dsregdns`

3. Uruchom następujące polecenia, aby dodać strefę na serwerze DNS, Zezwalaj na niezabezpieczone aktualizacje i dodać odpowiedni wpis dla strefy DNS:

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [ochrona obciążenia przedsiębiorstwa za pomocą usługi Azure Site Recovery](site-recovery-workload.md).
