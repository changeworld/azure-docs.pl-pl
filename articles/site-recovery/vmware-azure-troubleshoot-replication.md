---
title: Rozwiązywanie problemów z replikacją w celu odzyskiwania po awarii maszyn wirtualnych vmware i serwerów fizycznych na platformie Azure przy użyciu usługi Azure Site Recovery | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące rozwiązywania typowych problemów z replikacją podczas odzyskiwania po awarii maszyn wirtualnych vmware i serwerów fizycznych na platformie Azure przy użyciu usługi Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: f91ee5654b4add37d3cce4f875be1f9c2b398ab9
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259497"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Rozwiązywanie problemów z replikacją maszyn wirtualnych VMware i serwerów fizycznych

W tym artykule opisano niektóre typowe problemy i specyficzne błędy, które mogą wystąpić podczas replikowania lokalnych maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure przy użyciu [usługi Site Recovery](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>Krok 1: Monitorowanie kondycji serwera procesu

Usługa Site Recovery używa [serwera przetwarzania](vmware-physical-azure-config-process-server-overview.md#process-server) do odbierania i optymalizowania replikowanych danych oraz wysyłania ich na platformę Azure.

Zaleca się monitorowanie kondycji serwerów procesów w portalu, aby upewnić się, że są one połączone i działają poprawnie, a replikacja postępuje dla maszyn źródłowych skojarzonych z serwerem przetwarzania.

- [Dowiedz się więcej o](vmware-physical-azure-monitor-process-server.md) monitorowaniu serwerów procesów.
- [Przeglądanie najlepszych rozwiązań](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Rozwiązywanie problemów ze](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) zdrowiem serwera procesów.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Krok 2: Rozwiązywanie problemów z łącznością i replikacją

Początkowe i trwające błędy replikacji często są spowodowane problemami z łącznością między serwerem źródłowym a serwerem przetwarzania lub między serwerem przetwarzania a platformą Azure.

Aby rozwiązać te problemy, [rozwiąż problem z łącznością i replikacją](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Krok 3: Rozwiązywanie problemów z maszynami źródłowymi, które nie są dostępne dla replikacji

Podczas próby wybrania komputera źródłowego w celu włączenia replikacji przy użyciu funkcji Site Recovery komputer może nie być dostępny z jednego z następujących powodów:

* **Dwie maszyny wirtualne o tym samym wystąpieniu UUID:** Jeśli dwie maszyny wirtualne w centrum vCenter mają ten sam identyfikator UUID wystąpienia, pierwsza maszyna wirtualna odnaleziona przez serwer konfiguracji jest wyświetlana w portalu Azure. Aby rozwiązać ten problem, upewnij się, że żadne dwie maszyny wirtualne mają ten sam uuid wystąpienia. Ten scenariusz jest często widoczna w przypadkach, gdy zapasowa maszyna wirtualna staje się aktywna i jest zalogowana do naszych rekordów odnajdywania. Zapoznaj się z [usługi Azure Site Recovery VMware-to-Azure: Jak wyczyścić zduplikowane lub przestarzałe wpisy](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) do rozwiązania.
* **Niepoprawne poświadczenia użytkownika vCenter**: Upewnij się, że podczas konfigurowania serwera konfiguracji przy użyciu szablonu OVF lub ujednoliconej konfiguracji dodano poprawne poświadczenia vCenter. Aby sprawdzić poświadczenia dodane podczas instalacji, zobacz [Modyfikowanie poświadczeń w celu automatycznego odnajdowania](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **vCenter niewystarczające uprawnienia:** Jeśli uprawnienia dostępu vCenter nie mają wymaganych uprawnień, może wystąpić niepowodzenie odnajdywanie maszyn wirtualnych. Upewnij się, że uprawnienia opisane w [przygotowanie konta do automatycznego odnajdowania](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) są dodawane do konta użytkownika vCenter.
* **Serwery zarządzania usługi Azure Site Recovery:** Jeśli maszyna wirtualna jest używana jako serwer zarządzania w ramach jednej lub więcej z następujących ról — serwer konfiguracji /scale-w poziomie serwera docelowego / główny serwer docelowy, nie będzie można wybrać maszyny wirtualnej z portalu. Nie można replikować serwerów zarządzania.
* **Już chronione/awaryjne za pośrednictwem usług Azure Site Recovery:** Jeśli maszyna wirtualna jest już chroniona lub przejęta awaryjnie za pośrednictwem usługi Site Recovery, maszyna wirtualna nie jest dostępna do wybrania do ochrony w portalu. Upewnij się, że maszyna wirtualna, której szukasz w portalu, nie jest już chroniona przez innego użytkownika lub w innej subskrypcji.
* **vCenter nie jest podłączony**: Sprawdź, czy vCenter jest w stanie połączenia. Aby zweryfikować, przejdź do magazynu usług odzyskiwania > infrastruktury odzyskiwania lokacji > serwerów konfiguracji > Kliknij odpowiedni serwer konfiguracji > otwiera się blok po prawej stronie ze szczegółami skojarzonych serwerów. Sprawdź, czy vCenter jest podłączony. Jeśli jest w stanie "Nie połączony", rozwiąż problem, a następnie [odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server) w portalu. Po tym, maszyna wirtualna zostanie wyświetlona w portalu.
* **EsXi wyłączony:** Jeśli host ESXi, w którym znajduje się maszyna wirtualna jest w stanie wyłączony, a następnie maszyna wirtualna nie zostanie wyświetlona lub nie będzie można wybrać w witrynie Azure portal. Włącz hosta ESXi, [odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server) w portalu. Po tym, maszyna wirtualna zostanie wyświetlona w portalu.
* **Oczekiwanie na ponowne uruchomienie:** Jeśli istnieje oczekujące ponowne uruchomienie na maszynie wirtualnej, nie będzie można wybrać komputera w witrynie Azure portal. Upewnij się, że należy wykonać oczekujące działania ponownego uruchomienia, [odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Po tym, maszyna wirtualna zostanie wyświetlona w portalu.
* **Nie znaleziono adresu IP:** Jeśli maszyna wirtualna nie ma skojarzonego z nią prawidłowego adresu IP, nie będzie można wybrać komputera w witrynie Azure portal. Upewnij się, że należy przypisać prawidłowy adres IP do maszyny wirtualnej, [odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Po tym, maszyna wirtualna zostanie wyświetlona w portalu.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Rozwiązywanie problemów z chronionymi maszynami wirtualnymi wyszarzonymi w portalu

Maszyny wirtualne, które są replikowane w obszarze Odzysk witryny nie są dostępne w witrynie Azure portal, jeśli istnieją zduplikowane wpisy w systemie. Aby dowiedzieć się, jak usunąć stare wpisy i rozwiązać ten problem, zapoznaj się z [witryną Azure Site Recovery VMware-to-Azure: Jak wyczyścić zduplikowane lub przestarzałe wpisy](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="no-crash-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Brak punktu odzyskiwania spójnego awarii dostępnego dla maszyny Wirtualnej w ostatnich minutach "XXX"

Niektóre z najczęstszych problemów są wymienione poniżej

### <a name="initial-replication-issues-error-78169"></a>Początkowe problemy z replikacją [błąd 78169]

Powyżej, zapewniając, że nie ma żadnych problemów związanych z łącznością, przepustowością lub synchronizacją czasu, upewnij się, że:

- Żadne oprogramowanie antywirusowe nie blokuje usługi Azure Site Recovery. Dowiedz się [więcej](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) o wykluczeniach folderów wymaganych dla usługi Azure Site Recovery.

### <a name="source-machines-with-high-churn-error-78188"></a>Maszyny źródłowe o wysokiej odpływie [błąd 78188]

Możliwe przyczyny:
- Szybkość zmiany danych (bajty zapisu/s) na wymienionych dyskach maszyny wirtualnej jest większa niż [limity obsługiwane](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) przez usługę Azure Site Recovery dla typu konta magazynu docelowego replikacji.
- Istnieje nagły skok współczynnika zmian, z powodu którego duża ilość danych jest w toku do przesłania.

Aby rozwiązać ten problem:
- Upewnij się, że typ konta magazynu docelowego (Standard lub Premium) jest aprowizowana zgodnie z wymaganiami dotyczących współczynnika zmian u źródła.
- Jeśli jesteś już replikowane do dysku zarządzanego w układzie premium (typ asrseeddisk), upewnij się, że rozmiar dysku obsługuje obserwowane współczynniki zmian zgodnie z limitami odzyskiwania witryny. W razie potrzeby można zwiększyć rozmiar asseedisk. Wykonaj poniższe czynności:
    - Przejdź do bloku Dyski, na które ma wpływ replikowany komputer i skopiuj nazwę dysku repliki
    - Przejdź do tego dysku zarządzanego repliki
    - Na bloku Przegląd może pojawić się baner z informacją, że został wygenerowany adres URL sygnatury dostępu Współdzielonego. Kliknij ten baner i anuluj eksport. Zignoruj ten krok, jeśli nie widzisz banera.
    - Po odwołaniu adresu URL sygnatury dostępu Współdzielonego przejdź do bloku Konfiguracja dysku zarządzanego i zwiększ rozmiar, tak aby asr obsługuje obserwowaną szybkość zmian na dysku źródłowym
- Jeśli obserwowane zmiany są tymczasowe, poczekaj kilka godzin, aż oczekujące dane będą się dogonić i utworzyć punkty odzyskiwania.
- Jeśli dysk zawiera dane niekrytyczne, takie jak dzienniki tymczasowe, dane testowe itp., rozważ przeniesienie tych danych w inne miejsce lub całkowite wykluczenie tego dysku z replikacji
- Jeśli problem nadal się powtarza, użyj [planisty wdrażania odzyskiwania lokacji,](site-recovery-deployment-planner.md#overview) aby zaplanować replikację.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Maszyny źródłowe bez bicia serca [błąd 78174]

Dzieje się tak, gdy agent mobilności odzyskiwania witryny Azure na komputerze źródłowym nie komunikuje się z serwerem konfiguracji (CS).

Aby rozwiązać ten problem, należy wykonać następujące czynności, aby zweryfikować łączność sieciową ze źródłowej maszyny Wirtualnej z serwerem config server:

1. Sprawdź, czy komputer źródłowy jest uruchomiony.
2. Zaloguj się na komputerze źródłowym przy użyciu konta z uprawnieniami administratora.
3. Sprawdź, czy następujące usługi są uruchomione, a jeśli nie ponownie uruchomić usługi:
   - Svagents (InMage Scout VX Agent)
   - InMage Scout Application Service
4. Na komputerze źródłowym sprawdź dzienniki w lokalizacji, aby uzyskać szczegółowe informacje o błędzie:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log

### <a name="process-server-with-no-heartbeat-error-806"></a>Serwer przetwarzania bez pulsu [błąd 806]
W przypadku braku pulsu z serwera przetwarzania (PS), należy sprawdzić, czy:
1. Maszyna wirtualna PS jest uruchomiona
2. Sprawdź następujące dzienniki na PS szczegóły błędu:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Główny serwer docelowy bez pulsu [błąd 78022]

Dzieje się tak, gdy agent mobilności odzyskiwania witryny Azure w głównym obiekcie docelowym nie komunikuje się z serwerem konfiguracji.

Aby rozwiązać ten problem, należy wykonać następujące czynności, aby zweryfikować stan usługi:

1. Sprawdź, czy główna maszyna wirtualna docelowa jest uruchomiona.
2. Zaloguj się do głównej maszyny Wirtualnej docelowej przy użyciu konta z uprawnieniami administratora.
    - Sprawdź, czy usługa svagents jest uruchomiona. Jeśli jest uruchomiona, uruchom ponownie usługę
    - Sprawdź dzienniki w lokalizacji, aby uzyskać szczegółowe informacje o błędzie:

          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
3. Aby zarejestrować główny obiekt docelowy na serwerze konfiguracji, przejdź do folderu **%PROGRAMDATA%\ASR\Agent**i uruchom w wierszu polecenia następujące polecenie:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Identyfikator błędu 78144 — brak punktu odzyskiwania spójnego z aplikacją dla maszyny Wirtualnej w ostatnich minutach "XXX"

Wprowadzono ulepszenia w wersjach programu mobility Agent [9.23](vmware-physical-mobility-service-overview.md#mobility-service-agent-version-923-and-higher) & [9.27](site-recovery-whats-new.md#update-rollup-39) w celu obsługi zachowań błędów instalacji usługi VSS. Upewnij się, że jesteś na najnowszych wersjach, aby uzyskać najlepsze wskazówki dotyczące rozwiązywania problemów z błędami usługi VSS.

Niektóre z najczęstszych problemów są wymienione poniżej

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Przyczyna 1: Znany problem w programie SQL Server 2008/2008 R2
**Jak naprawić:** Istnieje znany problem z programem SQL Server 2008/2008 R2. Zapoznaj się z tym artykułem KB [Agent odzyskiwania witryny platformy Azure lub inna nieskszła kopia zapasowa VSS kończy się niepowodzeniem dla serwera obsługującego program SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Przyczyna 2: Zadania odzyskiwania usługi Azure Site Recovery nie powiodą się na serwerach obsługujących dowolną wersję wystąpień programu SQL Server z AUTO_CLOSE DB
**Jak naprawić** : Zapoznaj się z [artykułem](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) kb


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Przyczyna 3: Znany problem w programie SQL Server 2016 i 2017
**Jak naprawić** : Zapoznaj się z [artykułem](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) kb


### <a name="more-causes-due-to-vss-related-issues"></a>Więcej przyczyn z powodu problemów związanych z VSS:

Aby rozwiązać dalsze problemy, sprawdź pliki na komputerze źródłowym, aby uzyskać dokładny kod błędu dla błędu:

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Jak zlokalizować błędy w pliku?
Wyszukaj ciąg "vacpError", otwierając plik vacp.log w edytorze

    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

W powyższym przykładzie **2147754994** jest kod błędu, który informuje o awarii, jak pokazano poniżej

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>Moduł zapisujący usługi VSS nie jest zainstalowany — błąd 2147221164

*Jak naprawić:* Aby wygenerować tag spójności aplikacji, usługa Azure Site Recovery używa usługi kopiowania woluminów microsoft volume copy Service (VSS). Instaluje dostawcę usługi VSS dla jego operacji do robienia migawek spójności aplikacji. Ten dostawca usługi VSS jest zainstalowany jako usługa. W przypadku, gdy usługa dostawcy usługi VSS nie jest zainstalowana, tworzenie migawki spójności aplikacji kończy się niepowodzeniem z identyfikatorem błędu 0x80040154 "Klasa nie zarejestrowana". </br>
Zapoznaj się [z artykułem rozwiązywania problemów z instalacją modułu zapisującego usługi VSS](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures)

#### <a name="vss-writer-is-disabled---error-2147943458"></a>Moduł zapisujący usługi VSS jest wyłączony — błąd 2147943458

**Jak naprawić:** Aby wygenerować tag spójności aplikacji, usługa Azure Site Recovery używa usługi kopiowania woluminów microsoft volume copy Service (VSS). Instaluje dostawcę usługi VSS dla jego operacji do robienia migawek spójności aplikacji. Ten dostawca usługi VSS jest zainstalowany jako usługa. W przypadku wyłączenia usługi dostawcy usługi VSS tworzenie migawki spójności aplikacji kończy się niepowodzeniem z identyfikatorem błędu "Określona usługa jest wyłączona i nie można jej uruchomić(0x80070422)". </br>

- Jeśli usługa VSS jest wyłączona,
    - Sprawdź, czy typ uruchamiania usługi dostawcy usługi VSS jest ustawiony na **Automatyczny**.
    - Uruchom ponownie następujące usługi:
        - Usługa USŁUGI VSS
        - Dostawca usług VSS usługi Azure Site Recovery
        - Usługa VDS

####  <a name="vss-provider-not_registered---error-2147754756"></a>NOT_REGISTERED dostawcy usługi VSS — błąd 2147754756

**Jak naprawić:** Aby wygenerować tag spójności aplikacji, usługa Azure Site Recovery używa usługi kopiowania woluminów microsoft volume copy Service (VSS).
Sprawdź, czy usługa dostawcy usługi Azure Site Recovery VSS jest zainstalowana, czy nie. </br>

- Ponów próbę instalacji dostawcy przy użyciu następujących poleceń:
- Odinstaluj istniejącego dostawcę: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- Ponowna instalacja: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd

Sprawdź, czy typ uruchamiania usługi dostawcy usługi VSS jest ustawiony na **Automatyczny**.
    - Uruchom ponownie następujące usługi:
        - Usługa USŁUGI VSS
        - Dostawca usług VSS usługi Azure Site Recovery
        - Usługa VDS

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz więcej pomocy, opublikuj swoje pytanie na [forum usługi Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Mamy aktywną społeczność, a jeden z naszych inżynierów może Ci pomóc.
