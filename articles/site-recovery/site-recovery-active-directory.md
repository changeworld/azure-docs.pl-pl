---
title: Konfigurowanie odzyskiwania po awarii usługi Active Directory/DNS za pomocą usługi Azure Site Recovery
description: W tym artykule opisano sposób implementowania rozwiązania do odzyskiwania po awarii dla usługi Active Directory i systemu DNS za pomocą usługi Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 8c1f85217db12b60cdcd8ea0bdb65792b8d02648
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257812"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Konfigurowanie odzyskiwania po awarii dla usługi Active Directory i systemu DNS

Aplikacje dla przedsiębiorstw, takie jak SharePoint, Dynamics AX i SAP, zależą od usługi Active Directory i infrastruktury DNS, aby działać poprawnie. Podczas konfigurowania odzyskiwania po awarii dla aplikacji, często trzeba odzyskać usługi Active Directory i DNS przed odzyskaniem innych składników aplikacji, aby zapewnić poprawne funkcje aplikacji.

Za pomocą [funkcji Odzysk lokacji](site-recovery-overview.md) można utworzyć plan odzyskiwania po awarii dla usługi Active Directory. W przypadku wystąpienia zakłócenia można zainicjować przewijaniu w stan failover. Usługę Active Directory można uruchomić w ciągu kilku minut. Jeśli wdrożono usługi Active Directory dla wielu aplikacji w lokacji głównej, na przykład dla programu SharePoint i SAP, można wykonać w trybie fail over pełną witrynę. Najpierw można przejmować w tryb failoranty usługi Active Directory przy użyciu usługi Site Recovery. Następnie w pracy awaryjnej innych aplikacji przy użyciu planów odzyskiwania specyficznych dla aplikacji.

W tym artykule wyjaśniono, jak utworzyć rozwiązanie do odzyskiwania po awarii dla usługi Active Directory. Zawiera wymagania wstępne i instrukcje pracy awaryjnej. Przed rozpoczęciem należy zapoznać się z usługą Active Directory i odzyskiwaniem witryn.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli replikujesz na platformę Azure, [przygotuj zasoby platformy Azure](tutorial-prepare-azure.md), w tym subskrypcję, sieć wirtualną platformy Azure, konto magazynu i magazyn usług odzyskiwania.
* Zapoznaj się z [wymaganiami dotyczącymi obsługi](site-recovery-support-matrix-to-azure.md) wszystkich składników.

## <a name="replicate-the-domain-controller"></a>Replikowanie kontrolera domeny

- Replikację usługi Site Recovery należy skonfigurować na co najmniej jednej maszynie wirtualnej, która obsługuje kontroler domeny lub usługę DNS.
- Jeśli w środowisku znajduje się wiele kontrolerów domeny, należy również skonfigurować dodatkowy kontroler domeny w lokacji docelowej. Dodatkowy kontroler domeny może znajdować się na platformie Azure lub w pomocniczym lokalnym centrum danych.
- Jeśli masz tylko kilka aplikacji i jeden kontroler domeny, możesz chcieć połączyć się w stan faila całej lokacji. W takim przypadku zaleca się użycie usługi Site Recovery do replikowania kontrolera domeny do lokacji docelowej (na platformie Azure lub w pomocniczym lokalnym centrum danych). Do [testowania trybu failover](#test-failover-considerations)można użyć tego samego replikowanego kontrolera domeny lub maszyny wirtualnej DNS .
- - Jeśli w środowisku znajduje się wiele aplikacji i więcej niż jeden kontroler domeny lub jeśli planujesz awaryjnie kilka aplikacji naraz, oprócz replikowania maszyny wirtualnej kontrolera domeny za pomocą funkcji Site Recovery, zalecamy skonfigurowanie dodatkowy kontroler domeny w lokacji docelowej (na platformie Azure lub w pomocniczym lokalnym centrum danych). W przypadku [testowania trybu failover](#test-failover-considerations)można użyć kontrolera domeny, który jest replikowany przez usługę Site Recovery. W przypadku pracy awaryjnej można użyć dodatkowego kontrolera domeny w lokacji docelowej.

## <a name="enable-protection-with-site-recovery"></a>Włącz ochronę dzięki odzyskiwaniu witryny

Za pomocą funkcji Site Recovery można chronić maszynę wirtualną hostującą kontroler domeny lub system DNS.

### <a name="protect-the-vm"></a>Ochrona maszyny wirtualnej
Kontroler domeny, który jest replikowany przy użyciu usługi Site Recovery jest używany do [testowania pracy awaryjnej](#test-failover-considerations). Upewnij się, że spełnia następujące wymagania:

1. Kontroler domeny jest serwerem wykazu globalnego.
2. Kontroler domeny powinien być właścicielem roli FSMO dla ról, które są potrzebne podczas pracy awaryjnej testu. W przeciwnym razie te role będą musiały zostać [przejęte](https://aka.ms/ad_seize_fsmo) po przemijaniu awaryjnym.

### <a name="configure-vm-network-settings"></a>Konfigurowanie ustawień sieciowej maszyny Wirtualnej
Dla maszyny wirtualnej, która obsługuje kontroler domeny lub dns, w uczynka Odzyskiwania lokacji, skonfiguruj ustawienia sieciowe w **ustawieniach obliczeniowych i sieciowych** replikowanej maszyny wirtualnej. Gwarantuje to, że maszyna wirtualna jest podłączona do odpowiedniej sieci po przełączeniu w tryb failover.

## <a name="protect-active-directory"></a>Ochrona usługi Active Directory

### <a name="site-to-site-protection"></a>Ochrona między lokacjami
Utwórz kontroler domeny w lokacji dodatkowej. Podczas podwyższenia poziomu serwera do roli kontrolera domeny należy określić nazwę tej samej domeny, która jest używana w lokacji głównej. Przystawki **Lokacje i usługi Active Directory** można użyć do skonfigurowania ustawień obiektu łącza lokacji, do którego są dodawane witryny. Konfigurując ustawienia w łączu lokacji, można kontrolować, kiedy replikacja odbywa się między dwiema lub więcej lokacjami i jak często występuje. Aby uzyskać więcej informacji, zobacz [Planowanie replikacji między lokacjami](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Ochrona między lokacjami a platformą Azure
Najpierw utwórz kontroler domeny w sieci wirtualnej platformy Azure. Podczas podwyższenia poziomu serwera do roli kontrolera domeny należy określić tę samą nazwę domeny, która jest używana w lokacji głównej.

Następnie ponownie skonfiguruj serwer DNS dla sieci wirtualnej, aby używała serwera DNS na platformie Azure.

![Azure Network](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Ochrona platformy Azure na platformie Azure
Najpierw utwórz kontroler domeny w sieci wirtualnej platformy Azure. Podczas podwyższenia poziomu serwera do roli kontrolera domeny należy określić tę samą nazwę domeny, która jest używana w lokacji głównej.

Następnie ponownie skonfiguruj serwer DNS dla sieci wirtualnej, aby używała serwera DNS na platformie Azure.

## <a name="test-failover-considerations"></a>Testowanie zagadnień dotyczących trybu failover
Aby uniknąć wpływu na obciążenia produkcyjne, test pracy awaryjnej występuje w sieci, która jest odizolowana od sieci produkcyjnej.

Większość aplikacji wymaga obecności kontrolera domeny lub serwera DNS. W związku z tym przed zakończeniem pracy aplikacji należy utworzyć kontroler domeny w izolowanej sieci, który będzie używany do testowania pracy awaryjnej. Najprostszym sposobem, aby to zrobić, jest użycie funkcji Site Recovery do replikowania maszyny wirtualnej, na której znajduje się kontroler domeny lub system DNS. Następnie uruchom testową przekładowanie awaryjne maszyny wirtualnej kontrolera domeny przed uruchomieniem testowej pracy awaryjnej planu odzyskiwania dla aplikacji. Oto jak to zrobić:

1. Funkcja Odzysk lokacji umożliwia [replikowanie](vmware-azure-tutorial.md) maszyny wirtualnej hostującej kontroler domeny lub usługę DNS.
2. Tworzenie izolowanej sieci. Każda sieć wirtualna utworzona na platformie Azure jest domyślnie odizolowana od innych sieci. Zaleca się używanie tego samego zakresu adresów IP dla tej sieci, który jest używany w sieci produkcyjnej. Nie włączaj łączności lokacja-lokacja w tej sieci.
3. Podaj adres IP DNS w izolowanej sieci. Użyj adresu IP, który można uzyskać na maszynie wirtualnej DNS. Jeśli replikujesz na platformę Azure, podaj adres IP maszyny wirtualnej, która jest używana w trybie failover. Aby wprowadzić adres IP, na zreplikowanej maszynie wirtualnej w **ustawieniach obliczeń i sieci** wybierz docelowe ustawienia **IP.**

    ![Sieć testowa platformy Azure](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > Usługa Site Recovery próbuje utworzyć test maszyn wirtualnych w podsieci o tej samej nazwie i przy użyciu tego samego adresu IP, który jest podany w **ustawieniach obliczeniowych i sieci** maszyny wirtualnej. Jeśli podsieć o tej samej nazwie nie jest dostępna w sieci wirtualnej platformy Azure, która jest dostępna do testowania pracy awaryjnej, testowa maszyna wirtualna jest tworzona w alfabetycznej pierwszej podsieci.
    >
    > Jeśli docelowy adres IP jest częścią wybranej podsieci, usługa Site Recovery próbuje utworzyć testową maszynę wirtualną trybu failover przy użyciu docelowego adresu IP. Jeśli docelowy adres IP nie jest częścią wybranej podsieci, testowa maszyna wirtualna trybu failover jest tworzona przy użyciu następnego dostępnego adresu IP w wybranej podsieci.
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>Testowanie pracy awaryjnej w lokacji dodatkowej

1. Jeśli replikujesz się do innej lokacji lokalnej i używasz usługi DHCP, [skonfiguruj system DNS i DHCP do testowania pracy awaryjnej](hyper-v-vmm-test-failover.md#prepare-dhcp).
2. Wykonaj testową przekład w tryb failover maszyny wirtualnej kontrolera domeny, która działa w sieci izolowanej. Użyj najnowszego dostępnego punktu odzyskiwania *spójnej aplikacji* maszyny wirtualnej kontrolera domeny, aby wykonać test pracy awaryjnej.
3. Uruchom test pracy awaryjnej dla planu odzyskiwania, który zawiera maszyny wirtualne, na których działa aplikacja.
4. Po zakończeniu testowania *należy wyczyścić test pracy awaryjnej* na maszynie wirtualnej kontrolera domeny. Ten krok powoduje usunięcie kontrolera domeny utworzonego w celu przetestowania trybu failover.


### <a name="remove-references-to-other-domain-controllers"></a>Usuwanie odwołań do innych kontrolerów domeny
Podczas inicjowania testowego trybu failover nie należy uwzględniać wszystkich kontrolerów domeny w sieci testowej. Aby usunąć odwołania do innych kontrolerów domeny, które istnieją w środowisku produkcyjnym, może być konieczne [przejęcie ról usługi FSMO Active Directory](https://aka.ms/ad_seize_fsmo) i [oczyszczanie metadanych](https://technet.microsoft.com/library/cc816907.aspx) z powodu brakujących kontrolerów domeny.


### <a name="issues-caused-by-virtualization-safeguards"></a>Problemy spowodowane zabezpieczeniami wirtualizacji

> [!IMPORTANT]
> Niektóre konfiguracje opisane w tej sekcji nie są standardowymi ani domyślnymi konfiguracjami kontrolera domeny. Jeśli nie chcesz wprowadzać tych zmian na produkcyjnym kontrolerze domeny, możesz utworzyć kontroler domeny przeznaczony do odzyskiwania witryny do użycia w celu przetestowania trybu failover. Te zmiany należy wprowadzać tylko na tym kontrolerze domeny.  
>
>

Począwszy od systemu Windows Server 2012, [dodatkowe zabezpieczenia są wbudowane w Usługi domenowe Active Directory (AD DS)](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Zabezpieczenia te pomagają chronić zwirtualizowane kontrolery domeny przed wycofywaniem nazw USN, jeśli podstawowa platforma hypervisor obsługuje **VM-GenerationID**. Platforma Azure obsługuje **moduł generacji maszyn wirtualnych.** W związku z tym kontrolery domeny, które uruchamiają system Windows Server 2012 lub nowsze na maszynach wirtualnych platformy Azure mają te dodatkowe zabezpieczenia.


Po zresetowaniu obiektu **VM-GenerationID** jest również resetowana wartość **InvocationID** bazy danych usług AD DS. Ponadto pula RID jest odrzucana, a folder sysvol jest oznaczony jako nieuzyskający. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do wirtualizacji usług domenowych Active Directory](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) i [bezpieczne wirtualizacja systemu plików DFSR](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/).

Niepowodzenie platformy Azure może spowodować zresetowanie funkcji **VM-GenerationID.** Resetowanie **VM-GenerationID** wyzwala dodatkowe zabezpieczenia po uruchomieniu maszyny wirtualnej kontrolera domeny na platformie Azure. Może to spowodować *znaczne opóźnienie* w możliwości logowania się do maszyny wirtualnej kontrolera domeny.

Ponieważ ten kontroler domeny jest używany tylko w testowej pracy awaryjnej, zabezpieczenia wirtualizacji nie są konieczne. Aby upewnić się, że wartość **identyfikatora generacji maszyny** wirtualnej maszyny kontrolera domeny nie ulegnie zmianie, można zmienić wartość następujących modułów DWORD na **4** w lokalnym kontrolerze domeny:


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>Objawy zabezpieczeń wirtualizacji

Jeśli zabezpieczenia wirtualizacji są wyzwalane po przejściu w tryb failover testu, może zostać wyświetlony co najmniej jeden z następujących symptomów:  

* Zmienia się wartość **GenerationID.**

    ![Zmiana identyfikatora generacji](./media/site-recovery-active-directory/Event2170.png)

* Zmienia się wartość **InvocationID.**

    ![Zmiana identyfikatora wywołania](./media/site-recovery-active-directory/Event1109.png)

* Folder Sysvol i udziały NETLOGON nie są dostępne.

    ![Udział folderów Sysvol](./media/site-recovery-active-directory/sysvolshare.png)

    ![Folder sysvol NtFrs](./media/site-recovery-active-directory/Event13565.png)

* Bazy danych DFSR są usuwane.

    ![Bazy danych DFSR są usuwane](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Rozwiązywanie problemów z kontrolerem domeny podczas pracy awaryjnej testu

> [!IMPORTANT]
> Niektóre konfiguracje opisane w tej sekcji nie są standardowe lub domyślne konfiguracje kontrolera domeny. Jeśli nie chcesz wprowadzać tych zmian na produkcyjnym kontrolerze domeny, możesz utworzyć kontroler domeny dedykowany do pracy awaryjnej testu odzyskiwania witryny. Wprowadzać zmiany tylko do tego dedykowanego kontrolera domeny.  
>
>

1. W wierszu polecenia uruchom następujące polecenie, aby sprawdzić, czy folder sysvol i folder NETLOGON są współużytkowane:

    `NET SHARE`

2. W wierszu polecenia uruchom następujące polecenie, aby upewnić się, że kontroler domeny działa poprawnie:

    `dcdiag /v > dcdiag.txt`

3. W dzienniku danych wyjściowych poszukaj następującego tekstu. Tekst potwierdza, że kontroler domeny działa poprawnie.

    * "przeszedł test Łączność"
    * "zdał test Reklama"
    * "zdał test MachineAccount"

Jeśli powyższe warunki są spełnione, jest prawdopodobne, że kontroler domeny działa poprawnie. Jeśli tak nie jest, wykonaj następujące czynności:

1. Wykonaj autorytatywne przywracanie kontrolera domeny. Należy pamiętać o następujących informacjach:
    * Chociaż nie zalecamy [replikacji usługi FRS,](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/)jeśli używasz replikacji usługi FRS, wykonaj kroki dla autorytatywnego przywracania. Proces jest opisany w [za pomocą klucza rejestru BurFlags do ponownego zainicjowania usługi replikacji plików](https://support.microsoft.com/kb/290762).

        Aby uzyskać więcej informacji na temat BurFlags, zobacz blogu [D2 i D4: Do czego służy?](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Jeśli używasz replikacji DFSR, wykonaj kroki dla autorytatywnego przywracania. Proces jest opisany w [Życie autorytatywnej i nieujawniałej synchronizacji dla folderu sysvol replikowanego na DFSR (np.](https://support.microsoft.com/kb/2218556)

        Można również użyć funkcji programu PowerShell. Aby uzyskać więcej informacji, zobacz [DFSR-SYSVOL autorytatywne/nieuzyskotające funkcje przywracania programu PowerShell](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/).

2. Pomiń wymóg synchronizacji początkowej, ustawiając następujący klucz rejestru na **0** na lokalnym kontrolerze domeny. Jeśli DWORD nie istnieje, można go utworzyć w węźle **Parametry.**

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z identyfikatorem zdarzenia DNS 4013: Serwer DNS nie może załadować zintegrowanych stref DNS usługi AD](https://support.microsoft.com/kb/2001093).

3. Wyłącz wymóg, aby serwer wykazu globalnego był dostępny do sprawdzania poprawności logowania użytkownika. Aby to zrobić, na lokalnym kontrolerze domeny ustaw następujący klucz rejestru na **1**. Jeśli DWORD nie istnieje, można go utworzyć w węźle **Lsa.**

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    Aby uzyskać więcej informacji, zobacz [Wyłączanie wymogu, aby serwer wykazu globalnego był dostępny do sprawdzania poprawności logowania użytkowników](https://support.microsoft.com/kb/241789).

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS i kontroler domeny na różnych komputerach

Jeśli korzystasz z kontrolera domeny i nazw DN na tej samej maszynie wirtualnej, możesz pominąć tę procedurę.


Jeśli usługa DNS nie znajduje się na tej samej maszynie wirtualnej co kontroler domeny, należy utworzyć maszynę wirtualną DNS dla testowej pracy awaryjnej. Można użyć świeżego serwera DNS i utworzyć wszystkie wymagane strefy. Jeśli na przykład domena usługi Active Directory jest contoso.com, można utworzyć strefę DNS o nazwie contoso.com. Wpisy odpowiadające usłudze Active Directory muszą być aktualizowane w systemie DNS w następujący sposób:

1. Upewnij się, że te ustawienia są w miejscu przed uruchomieniem innej maszyny wirtualnej w planie odzyskiwania:
   * Strefa musi być nazwana nazwą katalogu głównego lasu.
   * Strefa musi być wspierana przez pliki.
   * Strefa musi być włączona dla bezpiecznych i niezabezpieczonych aktualizacji.
   * Program rozpoznawania nazw maszyny wirtualnej hostującej kontroler domeny powinien wskazywać adres IP maszyny wirtualnej DNS.

2. Uruchom następujące polecenie na maszynie wirtualnej, która obsługuje kontroler domeny:

    `nltest /dsregdns`

3. Uruchom następujące polecenia, aby dodać strefę na serwerze DNS, zezwolić na niezabezpieczone aktualizacje i dodać wpis strefy do systemu DNS:

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [ochronie obciążeń korporacyjnych za pomocą usługi Azure Site Recovery](site-recovery-workload.md).
