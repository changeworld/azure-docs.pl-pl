---
title: Ochrona usługi Active Directory i DNS z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak wdrożyć rozwiązanie odzyskiwania po awarii dla usługi Active Directory przy użyciu usługi Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: manayar
ms.openlocfilehash: 73562fee58b38999fd3bb281b5be32917f6a3394
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="use-azure-site-recovery-to-protect-active-directory-and-dns"></a>Użyj usługi Azure Site Recovery do ochrony usługi Active Directory i DNS

Aplikacje przedsiębiorstwa, takich jak SharePoint, Dynamics AX i SAP są zależne od usługi Active Directory i infrastruktury DNS, aby mógł działać poprawnie. Podczas odzyskiwania po awarii dla aplikacji, należy często odzyskać usługi Active Directory i DNS wcześniej inne składniki aplikacji w celu zapewnienia ich funkcjonalności właściwej aplikacji.

Można użyć [usługi Site Recovery](site-recovery-overview.md) umożliwia utworzenie planu odzyskiwania po awarii dla usługi Active Directory. W przypadku wystąpienia przerw w działaniu można zainicjować trybu failover. Masz usługi Active Directory w górę i uruchomiony w ciągu kilku minut. Jeśli w lokacji głównej zostały wdrożone usługi Active Directory dla wielu aplikacji, na przykład dla programu SharePoint i SAP, możesz do pracy awaryjnej w całej lokacji. Możesz pierwszy w trybie Failover usługi Active Directory przy użyciu mentu odzyskiwania. Następnie należy przełączyć inne aplikacje, przy użyciu planów odzyskiwania specyficzne dla aplikacji.

W tym artykule opisano sposób tworzenia rozwiązanie odzyskiwania po awarii dla usługi Active Directory. Zawiera wymagania wstępne i instrukcje pracy awaryjnej. Należy zapoznać się z usługą Active Directory i usługi Site Recovery, przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli przeprowadzasz replikację do platformy Azure, [przygotowanie zasobów Azure](tutorial-prepare-azure.md), włącznie z subskrypcji, sieci wirtualnej platformy Azure, konta magazynu i magazynu usług odzyskiwania.
* Zapoznaj się z [wymaganiami dotyczącymi obsługi](site-recovery-support-matrix-to-azure.md) wszystkich składników.

## <a name="replicate-the-domain-controller"></a>Replikowanie kontrolera domeny

Należy zdefiniować [replikacji usługi Site Recovery](#enable-protection-using-site-recovery), na co najmniej jedną maszynę Wirtualną, która obsługuje kontrolera domeny lub DNS. Jeśli masz [wielu kontrolerów domeny](#environment-with-multiple-domain-controllers) w danym środowisku, należy również skonfigurować [dodatkowy kontroler domeny](#protect-active-directory-with-active-directory-replication) w lokacji docelowej. Dodatkowy kontroler domeny może być na platformie Azure lub lokalnego pomocniczego centrum danych.

### <a name="single-domain-controller"></a>Kontroler domeny pojedynczej
Jeśli masz tylko kilka aplikacji i jeden kontroler domeny, możesz chcieć razem ulegają awarii w całej lokacji. W takim przypadku zaleca się przy użyciu usługi Site Recovery do replikacji kontrolera domeny do lokacji docelowej (albo w Azure lub lokalnego pomocniczego centrum danych). Możesz użyć tego samego kontrolera domeny replikowanych lub maszyny wirtualnej DNS dla [testowanie trybu failover](#test-failover-considerations).

### <a name="multiple-domain-controllers"></a>Wiele kontrolerów domeny
Jeśli masz wiele aplikacji i więcej niż jeden kontroler domeny w środowisku, lub jeśli planujesz do trybu failover kilka aplikacji w czasie, oprócz replikacji maszyny wirtualnej kontrolera domeny z usługą Site Recovery, zaleca się konfigurowanie [dodatkowy kontroler domeny](#protect-active-directory-with-active-directory-replication) w docelowej lokacji (albo w Azure lub lokalnego pomocniczego centrum danych). Aby uzyskać [testowanie trybu failover](#test-failover-considerations), można użyć kontrolera domeny, który jest replikowany przez usługę Site Recovery. Dla trybu failover można użyć dodatkowego kontrolera domeny w lokacji docelowej.

## <a name="enable-protection-with-site-recovery"></a>Włącz ochronę za pomocą usługi Site Recovery

Site Recovery można użyć do ochrony maszyny wirtualnej, która obsługuje kontrolera domeny lub DNS.

### <a name="protect-the-vm"></a>Ochrona maszyny Wirtualnej
Kontroler domeny, który jest replikowany za pomocą usługi Site Recovery jest używana do [testowanie trybu failover](#test-failover-considerations). Upewnij się, że spełnia następujące wymagania:

1. Kontroler domeny jest serwerem wykazu globalnego.
2. Kontroler domeny powinien być właścicielem roli FSMO dla ról, które są wymagane podczas testowania trybu failover. W przeciwnym wypadku te role musi być [zajęte](http://aka.ms/ad_seize_fsmo) po przejściu w tryb failover.

### <a name="configure-vm-network-settings"></a>Konfigurowanie ustawień sieciowych maszyny Wirtualnej
Dla maszyny wirtualnej, która obsługuje kontrolera domeny lub DNS w usłudze Site Recovery, skonfigurować ustawienia sieciowe w obszarze **obliczenia i sieć** ustawienia zreplikowanej maszyny wirtualnej. Dzięki temu, że maszyna wirtualna jest dołączona do odpowiedniej sieci po pracy awaryjnej.

## <a name="protect-active-directory"></a>Ochrona usługi Active Directory

### <a name="site-to-site-protection"></a>Ochrona lokacja lokacja
Tworzenie kontrolera domeny w lokacji dodatkowej. Podczas podwyższania poziomu serwera do roli kontrolera domeny, określ nazwę tej samej domeny, który jest używany w lokacji głównej. Można użyć **Lokacje usługi Active Directory i usługi** przystawki można skonfigurować ustawienia na obiekt łącza lokacji, do której są dodawane witryny. Konfigurując ustawienia dla łącza lokacji, możesz kontrolować termin replikacji między co najmniej dwóch lokacji, i jak często występuje. Aby uzyskać więcej informacji, zobacz [planowanie replikacji między lokacjami](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Ochrona lokacji do platformy Azure
Najpierw należy utworzyć kontroler domeny w sieci wirtualnej platformy Azure. Podczas podwyższania poziomu serwera do roli kontrolera domeny, określ tej samej nazwy domeny, który jest używany w lokacji głównej.

Następnie zmienić konfigurację serwera DNS dla sieci wirtualnej do używania serwera DNS w systemie Azure.

![Azure Network](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Ochrona Azure do platformy Azure
Najpierw należy utworzyć kontroler domeny w sieci wirtualnej platformy Azure. Podczas podwyższania poziomu serwera do roli kontrolera domeny, określ tej samej nazwy domeny, który jest używany w lokacji głównej.

Następnie zmienić konfigurację serwera DNS dla sieci wirtualnej do używania serwera DNS w systemie Azure.

## <a name="test-failover-considerations"></a>Zagadnienia dotyczące trybu failover testu
Aby uniknąć wpływu na obciążeń produkcyjnych, test pracy awaryjnej w sieci, która jest odizolowana od sieci produkcyjnej.

Większość aplikacji wymaga obecności kontrolera domeny lub serwer DNS. Dlatego przed aplikacji wirtualnej w tryb failover, należy utworzyć kontroler domeny w sieci izolowanej służący do testowania trybu failover. Najprostszym sposobem, w tym celu jest użycie usługi Site Recovery do replikacji maszyny wirtualnej, która obsługuje kontrolera domeny lub DNS. Następnie uruchom test trybu failover maszyny wirtualnej kontrolera domeny przed uruchomieniem testowy tryb failover planu odzyskiwania dla aplikacji. Oto jak to zrobić:

1. Użyj usługi Site Recovery do [replikować](vmware-azure-tutorial.md) maszynę wirtualną, która obsługuje kontrolera domeny lub DNS.
2. Utwórz izolowaną sieć. Sieci wirtualnej, które są tworzone w usłudze Azure jest odizolowany od innych sieci domyślnie. Zaleca się, że używasz tego samego zakresu adresów IP dla tej sieci używanej w sieci produkcyjnej. Nie włączaj połączenie lokacja lokacja w tej sieci.
3. Podaj adres IP serwera DNS w sieci izolowanej. Użyj adresu IP, który spodziewasz się maszyny wirtualnej DNS, aby uzyskać. Jeśli przeprowadzasz replikację do platformy Azure, podaj adres IP dla maszyny wirtualnej, który jest używany w tryb failover. Aby wprowadzić adres IP w maszynie wirtualnej zreplikowanej w **obliczenia i sieć** ustawienia, wybierz opcję **docelowy adres IP** ustawienia.

    ![Sieci testowej Azure](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > Usługa Site Recovery próbuje utworzyć testowych maszyn wirtualnych w podsieci o takiej samej nazwie i przy użyciu tego samego adresu IP, który znajduje się w **obliczenia i sieć** ustawienia maszyny wirtualnej. Jeśli podsieć o tej samej nazwie nie jest dostępny w sieci wirtualnej platformy Azure jest dostępne w celu testowania trybu failover, testowej maszyny wirtualnej jest tworzony w kolejności alfabetycznej pierwszej podsieci.
    >
    > Jeśli docelowy adres IP jest częścią wybranej podsieci, Usługa Site Recovery próbuje utworzyć testowej maszyny wirtualnej w tryb failover przy użyciu docelowy adres IP. Jeśli docelowy adres IP nie jest częścią wybranej podsieci, testową maszynę wirtualną w tryb failover jest tworzony przy użyciu dalej IP dostępne w wybranej podsieci.
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>Testowanie trybu failover do lokacji dodatkowej

1. Jeśli przeprowadzasz replikację do innej lokacji lokalnej i korzystania z protokołu DHCP [Konfigurowanie systemu DNS i DHCP do testowania trybu failover](hyper-v-vmm-test-failover.md#prepare-dhcp).
2. Wykonaj test trybu failover maszyny wirtualnej kontrolera domeny z systemem w sieci izolowanej. Użyj najnowszych dostępnych *aplikacji spójne* punktu odzyskiwania maszyny wirtualnej kontrolera domeny do testowania trybu failover.
3. Uruchom test trybu failover planu odzyskiwania, który zawiera maszyny wirtualne, których aplikacja jest uruchamiana na.
4. Po zakończeniu testowania, *oczyszczania testowy tryb failover* na maszynie wirtualnej kontrolera domeny. Ten krok powoduje usunięcie kontrolera domeny, który został utworzony do testowania trybu failover.


### <a name="remove-references-to-other-domain-controllers"></a>Usuń odwołania do innych kontrolerów domeny
Po zainicjowaniu testowy tryb failover nie zawierają wszystkich kontrolerów domeny w sieci testowej. Aby usunąć odwołania do innych kontrolerów domeny, które istnieją w środowisku produkcyjnym, konieczne może być [przejmowanie ról FSMO usługi Active Directory](http://aka.ms/ad_seize_fsmo) i [czyszczenie metadanych](https://technet.microsoft.com/library/cc816907.aspx) brakujących kontrolerów domeny .


### <a name="issues-caused-by-virtualization-safeguards"></a>Problemy spowodowane przez zabezpieczenia wirtualizacji

> [!IMPORTANT]
> Niektóre z konfiguracji opisanych w tej sekcji są standard lub domyślnej konfiguracji kontrolera domeny. Jeśli nie chcesz wprowadzić te zmiany do poziomu kontrolera domeny w środowisku produkcyjnym, można utworzyć kontroler domeny, który jest przeznaczony dla usługi Site Recovery na potrzeby testowania trybu failover. Te zmiany tylko do tego kontrolera domeny.  
>
>

Począwszy od systemu Windows Server 2012, [dodatkowe zabezpieczenia są wbudowane w usługach domenowych w usłudze Active Directory (AD DS)](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Te zabezpieczenia chronić zwirtualizowanych kontrolerów domeny przed wycofywania numerów USN, gdy platforma podstawowa funkcja hypervisor obsługuje **identyfikatora VM-GenerationID**. Azure obsługuje **identyfikatora VM-GenerationID**. W związku z tym kontrolerów domeny z systemem Windows Server 2012 lub nowszego Azure maszyny wirtualne mają te dodatkowe zabezpieczenia.


Gdy **identyfikatora VM-GenerationID** zostanie zresetowana, **InvocationID** wartość bazy danych usług AD DS jest również zresetować. Ponadto puli identyfikatorów RID jest odrzucany i folderu SYSVOL jest oznaczony jako nieautorytatywne. Aby uzyskać więcej informacji, zobacz [wprowadzenie do wirtualizacji usług domenowych w usłudze Active Directory](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) i [bezpiecznie wirtualizacja usługi DFSR](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/).

Awarii na platformie Azure może spowodować **identyfikatora VM-GenerationID** do zresetowania. Resetowanie **identyfikatora VM-GenerationID** dodatkowe zabezpieczenia jest wyzwalane po uruchomieniu maszyny wirtualnej kontrolera domeny na platformie Azure. Może to spowodować *znaczne opóźnienie* w będą mogli logować się do maszyny wirtualnej kontrolera domeny.

Ponieważ ten kontroler domeny jest używany tylko w przypadku testowego trybu failover, zabezpieczenia wirtualizacji nie są konieczne. Aby upewnić się, że **identyfikatora VM-GenerationID** nie zmienia wartość dla maszyny wirtualnej kontrolera domeny, można zmienić wartość DWORD następujące do **4** na lokalnym kontrolerze domeny:


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>Objawy zabezpieczenia wirtualizacji

Jeśli po przejściu w tryb failover testu zostają uruchomione zabezpieczenia wirtualizacji, można napotkać co najmniej jeden z następujących objawów:  

* **GenerationID** wartość zmienia się.

    ![Zmiany Identyfikatora generacji](./media/site-recovery-active-directory/Event2170.png)

* **InvocationID** wartość zmienia się.

    ![Zmiany Identyfikatora wywołania](./media/site-recovery-active-directory/Event1109.png)

* Udział SYSVOL i NETLOGON nie są dostępne.

    ![Udział SYSVOL](./media/site-recovery-active-directory/sysvolshare.png)

    ![NtFrs SYSVOL](./media/site-recovery-active-directory/Event13565.png)

* Bazy danych DFSR są usuwane.

    ![Bazy danych DFSR są usuwane.](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Rozwiązywanie problemów z kontrolera domeny podczas testowania trybu failover

> [!IMPORTANT]
> Niektóre z konfiguracji opisanych w tej sekcji są standard lub domyślnej konfiguracji kontrolera domeny. Jeśli nie chcesz wprowadzić te zmiany do poziomu kontrolera domeny w środowisku produkcyjnym, można utworzyć kontroler domeny, który jest przeznaczony dla usługi Site Recovery testowy tryb failover. Wprowadź zmiany tylko do tego kontrolera domeny dedykowanego.  
>
>

1. W wierszu polecenia Uruchom następujące polecenie, aby sprawdzić, czy folder SYSVOL i usługa NETLOGON foldery są udostępnione:

    `NET SHARE`

2. W wierszu polecenia Uruchom następujące polecenie, aby upewnić się, że kontroler domeny działa prawidłowo:

    `dcdiag /v > dcdiag.txt`

3. Wyszukaj następujący tekst w dzienniku danych wyjściowych. Tekst potwierdza, że kontroler domeny działa poprawnie.

    * "przekazany test łączności"
    * "anonsowanie przekazany test"
    * "test przekazany MachineAccount"

Jeśli powyższe warunki są spełnione, istnieje prawdopodobieństwo, że kontroler domeny działa poprawnie. Jeśli nie, wykonaj następujące czynności:

1. Czy przywracania autorytatywnego kontrolera domeny. Należy pamiętać, następujące informacje:
    * Chociaż nie zalecamy [replikacji usługi FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/), korzystając z replikacji usługi FRS, wykonaj kroki procedury przywracania autorytatywnego. Proces został opisany w [usługi replikacji plików za pomocą klucza rejestru BurFlags](https://support.microsoft.com/kb/290762).

        Aby uzyskać więcej informacji na temat BurFlags, zobacz we wpisie blogu [D2 i D4: co to jest dla?](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Jeśli używasz replikacji DFSR, wykonaj kroki procedury przywracania autorytatywnego. Proces został opisany w [wymusić synchronizację autorytatywną i nieautorytatywną dla folderu SYSVOL zreplikowanego DFSR (na przykład "D4/D2" w przypadku usługi FRS)](https://support.microsoft.com/kb/2218556).

        Możesz również użyć funkcji programu PowerShell. Aby uzyskać więcej informacji, zobacz [funkcji programu PowerShell autorytatywne/przywracanie nieautorytatywne DFSR SYSVOL](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/).

2. Pominąć wymaganie synchronizacji początkowej przez ustawienie następujący klucz rejestru **0** na lokalnym kontrolerze domeny. Jeśli wartość typu DWORD nie istnieje, można utworzyć go w **parametry** węzła.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z 4013 identyfikator zdarzenia DNS: serwer DNS nie może załadować strefy DNS zintegrowane AD](https://support.microsoft.com/kb/2001093).

3. Wyłącz wymagania serwera wykazu globalnego i można do sprawdzania poprawności nazwy logowania użytkownika. W tym celu na lokalnym kontrolerze domeny, należy ustawić następujący klucz rejestru **1**. Jeśli wartość typu DWORD nie istnieje, można utworzyć go w **Lsa** węzła.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    Aby uzyskać więcej informacji, zobacz [wyłączyć wymagania serwera wykazu globalnego i można do sprawdzania poprawności logowania użytkowników](http://support.microsoft.com/kb/241789).

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS i kontrolera domeny na kilka różnych maszyn
Jeśli DNS nie znajduje się w tej samej maszyny wirtualnej jako kontroler domeny, należy utworzyć maszynę wirtualną DNS do testowania trybu failover. Jeśli DNS i kontrolera domeny nie ma na tej samej maszyny wirtualnej, możesz pominąć tę sekcję.

Można użyć nowego serwera DNS, a następnie utwórz wymagane strefy. Na przykład domena usługi Active Directory to contoso.com, można utworzyć strefę DNS o nazwie contoso.com. Wpisów, które odnoszą się do usługi Active Directory muszą zostać zaktualizowane w systemie DNS w następujący sposób:

1. Upewnij się, że te ustawienia są na miejscu przed rozpoczęciem inne maszyny wirtualne w planie odzyskiwania:
   * Strefa musi nosi nazwę katalogu głównego lasu.
   * Strefa musi być kopią zapasową w pliku.
   * Strefa musi być włączona bezpieczne jak i aktualizacje.
   * Mechanizm rozpoznawania maszynę wirtualną, która znajduje się na kontrolerze domeny powinny wskazywać adres IP DNS maszyny wirtualnej.

2. Na maszynie Wirtualnej, która znajduje się na kontrolerze domeny, uruchom następujące polecenie:

    `nltest /dsregdns`

3. Uruchom następujące polecenia, aby dodać strefę na serwerze DNS, Zezwalaj na niezabezpieczone aktualizacje i Dodaj wpis dla strefy DNS:

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [ochrony obciążeń przedsiębiorstwa z usługą Azure Site Recovery](site-recovery-workload.md).
