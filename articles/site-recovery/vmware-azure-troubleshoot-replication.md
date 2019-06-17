---
title: Rozwiązywanie problemów z replikacją na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów dla typowych problemów z replikacją podczas odzyskiwania po awarii maszyn wirtualnych programu VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/14/2019
ms.author: mayg
ms.openlocfilehash: 3221b2bd18b8b0756f280d88fffc6016d0498b8f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64924823"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Rozwiązywanie problemów z replikacją dla maszyn wirtualnych VMware i serwerów fizycznych

W tym artykule opisano niektóre typowe problemy i określone błędy mogą występować w przypadku replikowania lokalnych maszyn wirtualnych z programu VMware i serwerów fizycznych do platformy Azure za pomocą [Site Recovery](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>Krok 1: Monitorowanie kondycji serwera przetwarzania

Site Recovery używa [serwera przetwarzania](vmware-physical-azure-config-process-server-overview.md#process-server) odbierania i zoptymalizować replikowanych danych oraz wysyłać je do platformy Azure.

Zaleca się, że monitorowanie kondycji serwerów przetwarzania w portalu, aby upewnić się, że są one połączone i działa prawidłowo, a replikacja postępuje maszyna źródłowa skojarzona z serwerem przetwarzania.

- [Dowiedz się więcej o](vmware-physical-azure-monitor-process-server.md) monitorowanie serwerów przetwarzania.
- [Przeglądanie najlepszych rozwiązań](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Rozwiązywanie problemów z](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) Kondycja serwera process server.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Krok 2: Rozwiązywanie problemów z łącznością i replikacji

Niepowodzenia replikacji początkowej i bieżących często są spowodowane przez problemy z łącznością między serwerem źródłowym i serwerem przetwarzania lub między serwerem przetwarzania a platformą Azure. 

Aby rozwiązać te problemy [Rozwiązywanie problemów z łącznością i replikacji](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Krok 3: Rozwiązywanie problemów z maszyn źródłowych, które nie są dostępne dla replikacji

Podczas próby wybierz maszyny źródłowej, aby włączyć replikację przy użyciu usługi Site Recovery maszyny może nie być dostępne dla jednego z następujących powodów:

* **Dwie maszyny wirtualne z tym samym wystąpieniu UUID**: Jeśli dwie maszyny wirtualne w ramach programu vCenter to samo wystąpienie identyfikatora UUID, pierwszej maszyny wirtualnej, wykrytych przez serwer konfiguracji jest wyświetlany w witrynie Azure portal. Aby rozwiązać ten problem, upewnij się, że nie dwie maszyny wirtualne mają tego samego wystąpienia identyfikatora UUID. Ten scenariusz jest częsta w wystąpieniach, gdzie staje się aktywny kopii zapasowej maszyny Wirtualnej, a jest zalogowany do naszych danych odnajdywania. Zapoznaj się [usługi Azure Site Recovery VMware na platformę Azure: Jak wyczyścić zduplikowane lub nieaktualne wpisy](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) do rozwiązania.
* **VCenter niepoprawne poświadczenia użytkownika**: Nie zapomnij dodać poświadczenia poprawne vCenter, po skonfigurowaniu serwera konfiguracji za pomocą szablonu pakietu OVF lub ujednoliconej konfiguracji. Aby zweryfikować poświadczenia, które dodałeś podczas instalacji, zobacz [zmodyfikowania poświadczeń do automatycznego odnajdowania](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **niewystarczające uprawnienia vCenter**: Jeśli uprawnienia podany dostępu vCenter do nie ma wymaganych uprawnień, może wystąpić błąd, aby odnaleźć maszyny wirtualne. Upewnij się, że uprawnienia opisane w [przygotowywanie konta do automatycznego odnajdowania](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) są dodawane do konta użytkownika vCenter.
* **Serwery zarządzania w usłudze Azure Site Recovery**: Jeśli maszyna wirtualna jest używany jako serwer zarządzania w co najmniej jeden z następujących ról — serwer przetwarzania /scale-out serwera konfiguracji / głównym serwerze docelowym, nie będzie mógł wybrać maszynę wirtualną z portalu. Nie można zreplikować serwery zarządzania.
* **Już chronione/przełączone w tryb failover za pomocą usługi Azure Site Recovery**: Jeśli maszyna wirtualna jest już chronione lub przełączone w tryb failover przez usługi Site Recovery, maszyna wirtualna nie jest dostępny dla wybranych do ochrony w portalu. Upewnij się, czy maszyny wirtualnej, którego szukasz w portalu nie jest już chroniony przez żadnego innego użytkownika lub innej subskrypcji.
* **vCenter niepołączony**: Sprawdź, czy vCenter jest w stanie połączonym. Aby sprawdzić, przejdź do magazynu usługi Recovery Services > infrastruktura usługi Site Recovery > Serwery konfiguracji > kliknij na serwerze konfiguracji odpowiednich > zostanie otwarty blok z prawej strony ze szczegółami skojarzonych serwerów. Sprawdź, czy vCenter jest połączony. Jeśli jest w stanie "Niepodłączone", rozwiąż problem, a następnie [Odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server) w portalu. Po tym maszyna wirtualna będzie wyświetlane w portalu.
* **Wyłączone ESXi**: Jeśli host ESXi, pod którą znajduje się maszyna wirtualna znajduje się w stanie, wyłączenia następnie maszyna wirtualna nie będą wyświetlane lub nie będzie możliwe w witrynie Azure portal. Zasilania na hoście ESXi [Odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server) w portalu. Po tym maszyna wirtualna będzie wyświetlane w portalu.
* **Oczekujące na ponowny rozruch**: W przypadku oczekuje na ponowny rozruch na maszynie wirtualnej, następnie nie będzie możliwe wybranie maszyny w witrynie Azure portal. Upewnij się, że ukończenie działania oczekiwanie na ponowny rozruch [Odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Po tym maszyna wirtualna będzie wyświetlane w portalu.
* **Nie można odnaleźć adresu IP**: Jeśli maszyna wirtualna nie ma prawidłowego adresu IP skojarzone z nim, następnie nie będzie możliwe wybranie maszyny w witrynie Azure portal. Upewnij się, aby przypisać prawidłowego adresu IP do maszyny wirtualnej [Odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Po tym maszyna wirtualna będzie wyświetlane w portalu.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Rozwiązywanie problemów z chronionych maszyn wirtualnych na szaro w portalu

Maszyny wirtualne, które są replikowane w ramach odzyskiwania lokacji nie są dostępne w witrynie Azure portal, jeśli istnieją zduplikowane wpisy w systemie. Aby dowiedzieć się, jak usunąć przestarzałych wpisów i rozwiązać problem, zapoznaj się [usługi Azure Site Recovery VMware na platformę Azure: Jak wyczyścić zduplikowane lub nieaktualne wpisy](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="common-errors-and-solutions"></a>Typowe błędy i rozwiązania

### <a name="initial-replication-issues-error-78169"></a>Problemy dotyczące replikacji początkowej [błąd 78169]

W powyższym zapewnienie, że istnieją nie łączności, przepustowości lub czasu synchronizacji problemy związane z, upewnij się, że:

- Oprogramowanie antywirusowe nie blokuje usługi Azure Site Recovery. Dowiedz się, [więcej](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) na wykluczenia folderów wymagane dla usługi Azure Site Recovery.

### <a name="missing-app-consistent-recovery-points-error-78144"></a>Brak punktów odzyskiwania zapewniających spójność aplikacji [błąd 78144]

 Dzieje się tak z powodu problemów z kopiowania woluminów w tle Service (VSS). Aby rozwiązać: 
 
- Sprawdź, czy zainstalowana wersja agenta usługi Azure Site Recovery jest co najmniej 9.22.2. 
- Sprawdź, czy dostawca usługi VSS jest zainstalowany jako usługa na platformie usług Windows, a także sprawdzić MMC usługi składnika, aby sprawdzić, czy dostawca VSS usługi Azure Site Recovery znajduje się.
- Jeśli nie zainstalowano dostawcy usługi VSS, zapoznaj się [niepowodzenia instalacji w artykule dotyczącym rozwiązywania problemów](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

- Wyłączenie usługi VSS
    - Sprawdź, czy typ uruchamiania usługi dostawcy usługi VSS jest równa **automatyczne**.
    - Uruchom ponownie następujące usługi:
        - Usługa VSS
        - Dostawcy usługi VSS programu usługi Azure Site Recovery
        - Usługę VDS

### <a name="source-machines-with-high-churn-error-78188"></a>Maszyny źródłowe z dużą ilością zmian [błąd 78188]

Możliwe przyczyny:
- Współczynnik zmian danych (zapisane bajty/s) na liście dysków maszyny wirtualnej wynosi więcej niż [usługi Azure Site Recovery obsługiwane limity](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) dla typu konta replikacji magazynu docelowego.
- Współczynnik zmian danych jest nagłym skokiem z powodu której dużej ilości danych jest w stanie oczekiwania do przekazania.

Aby rozwiązać ten problem:
- Upewnij się, że docelowy typ konta magazynu (standardowa / Premium) zostanie zainicjowana dla każdego wymagania szybkość zmian w źródle.
- W przypadku tymczasowego obserwowanych zmian, poczekaj kilka godzin w celu przekazania danych oczekujące, zapoznać się z nimi, a także tworzenie punktów odzyskiwania.
- Jeśli problem nie ustąpi, używać usługi Site Recovery [planista wdrażania](site-recovery-deployment-planner.md#overview) ułatwiające zaplanowanie replikacji.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Maszyny źródłowe z Brak pulsu [błąd 78174]

Dzieje się tak, gdy agent usługi Azure Site Recovery Mobility na maszynie źródłowej nie komunikuje się za pomocą serwera konfiguracji (CS).

Aby rozwiązać ten problem, należy użyć do weryfikowania łączności sieciowej ze źródłowej maszyny Wirtualnej serwera konfiguracji następujące czynności:

1. Sprawdź, czy na maszynie źródłowej jest uruchomiony.
2. Zaloguj się na maszynie źródłowej przy użyciu konta z uprawnieniami administratora.
3. Sprawdź, czy następujące usługi są uruchomione i ponownego uruchamiania usług, gdy:
   - Svagents (Agent InMage Scout VX Agent)
   - Usługa aplikacji InMage Scout
4. Na maszynie źródłowej Sprawdź dzienniki w lokalizacji, aby uzyskać szczegóły błędu:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>Serwer przetwarzania za pomocą Brak pulsu [błąd 806]
W przypadku, gdy jest brak pulsu z serwera przetwarzania (PS), sprawdź, czy:
1. PS Maszyna wirtualna jest uruchomiona
2. Sprawdź następujące dzienniki na PS, aby uzyskać szczegóły błędu:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Główny serwer docelowy z Brak pulsu [błąd 78022]

Dzieje się tak, gdy agent usługi Azure Site Recovery Mobility na główny element docelowy nie komunikuje się z serwerem konfiguracji.

Aby rozwiązać ten problem, należy użyć następujące kroki, aby sprawdzić stan usługi:

1. Sprawdź, czy wzorzec docelowej maszyny Wirtualnej jest uruchomiony.
2. Zaloguj się do wzorca docelowej maszyny Wirtualnej przy użyciu konta z uprawnieniami administratora.
    - Sprawdź, czy usługa svagents jest uruchomiona. Jeśli jest uruchomiona, uruchom ponownie usługę
    - Sprawdź dzienniki w lokalizacji, aby uzyskać szczegóły błędu:
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log



## <a name="next-steps"></a>Kolejne kroki

Jeśli potrzebujesz więcej pomocy, opublikuj swoje pytanie w [forum usługi Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Mamy aktywnej społeczności użytkowników, a jeden z naszych inżynierów może być pomocny.
