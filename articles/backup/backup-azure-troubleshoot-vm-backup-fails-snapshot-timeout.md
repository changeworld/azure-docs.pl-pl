---
title: Rozwiązywanie problemów z awarii usługi Azure Backup Stan agenta gościa jest niedostępny
description: Objawy, przyczyny i rozwiązania usługi Azure Backup błędy związane z agenta, rozszerzeń i dysków.
services: backup
author: saurabhsensharma
manager: saurabhsensharma
keywords: Usługa Azure backup; Agent maszyny Wirtualnej; Łączności sieciowej;
ms.service: backup
ms.topic: troubleshooting
ms.date: 12/03/2018
ms.author: saurabhsensharma
ms.openlocfilehash: 5abaa040b22000a300fba22590866846f6dbbd4a
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565686"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Rozwiązywanie problemów z awarii usługi Azure Backup Problemy związane z rozszerzenia lub agenta

Ten artykuł zawiera kroki rozwiązywania problemów, które mogą pomóc Ci rozwiązać błędy usługi Kopia zapasowa Azure dotyczące komunikacji z agentem maszyny Wirtualnej i rozszerzenia.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]



## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable — nie można nawiązać komunikacji z usługą Azure Backup agent maszyny Wirtualnej

**Kod błędu:** : UserErrorGuestAgentStatusUnavailable <br>
**Komunikat o błędzie**: Nie można nawiązać komunikacji z usługą Azure Backup Agent maszyny Wirtualnej<br>

Po zarejestrowaniu się i zaplanować maszyny Wirtualnej dla usługi Kopia zapasowa, Backup Inicjuje zadania, komunikując się z agentem maszyny Wirtualnej, aby utworzyć migawkę punktu w czasie. Dowolne z następujących warunków może uniemożliwić migawki wyzwalane. Migawka nie zostanie wyzwolony, tworzenie kopii zapasowej może zakończyć się niepowodzeniem. Wykonaj następujące kroki w podanej kolejności, a następnie ponów próbę wykonania operacji:<br>
**Przyczyny 1: [Agent jest zainstalowany na maszynie wirtualnej, ale go nie odpowiada (dla maszyn wirtualnych Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Przyczyny 2: [Agent zainstalowany na maszynie wirtualnej jest nieaktualna (dla maszyn wirtualnych systemu Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Przyczyny 3: [Nie można pobrać stanu migawki lub migawka nie może być przyjęty.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**     
**Przyczyna 4: [Rozszerzenie kopii zapasowej nie powiedzie się zaktualizować lub załadować](#the-backup-extension-fails-to-update-or-load)**  
**Przyczyna 5: [Maszyna wirtualna nie ma dostępu do Internetu](#the-vm-has-no-internet-access)**

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError — nie można nawiązać komunikacji z agentem maszyny Wirtualnej w celu uzyskania stanu migawki

**Kod błędu:** : GuestAgentSnapshotTaskStatusError<br>
**Komunikat o błędzie**: Nie można nawiązać komunikacji z agentem maszyny wirtualnej w celu uzyskania stanu migawki <br>

Po zarejestrowaniu i zaplanować maszyny Wirtualnej dla usługi Kopia zapasowa Azure Backup Inicjuje zadania, komunikując się z rozszerzenie kopii zapasowej maszyny Wirtualnej, aby utworzyć migawkę punktu w czasie. Dowolne z następujących warunków może uniemożliwić migawki wyzwalane. Jeśli nie zostanie wyzwolony, migawki, mogą wystąpić niepowodzenia wykonywania kopii zapasowej. Wykonaj następujące kroki w podanej kolejności, a następnie ponów próbę wykonania operacji:  
**Przyczyny 1: [Agent jest zainstalowany na maszynie wirtualnej, ale go nie odpowiada (dla maszyn wirtualnych Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Przyczyny 2: [Agent zainstalowany na maszynie wirtualnej jest nieaktualna (dla maszyn wirtualnych systemu Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Przyczyny 3: [Maszyna wirtualna nie ma dostępu do Internetu](#the-vm-has-no-internet-access)**

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached - osiągnęła maksymalny limit kolekcji punktów przywracania

**Kod błędu:** : UserErrorRpCollectionLimitReached <br>
**Komunikat o błędzie**: Osiągnięto maksymalny limit kolekcji punktów przywracania. <br>
* Ten problem może wystąpić, jeśli jest zablokowana w grupie zasobów punktu odzyskiwania, zapobiegając automatycznego czyszczenia punktu odzyskiwania.
* Ten problem może również się zdarzyć, jeśli wiele kopii zapasowych są wyzwalane na dzień. Obecnie firma Microsoft zaleca tylko jedna kopia zapasowa dziennie, jako punkty natychmiastowe Przywracanie są przechowywane przez 1 – 5 dni, zgodnie z migawki skonfigurowany okres przechowywania i tylko 18 błyskawiczny RPs może być skojarzony z maszyną Wirtualną, w dowolnym momencie. <br>

Zalecana akcja:<br>
Aby rozwiązać ten problem, Usuń blokadę grupę zasobów maszyny wirtualnej, a następnie spróbuj ponownie wykonać operację w celu wyzwolenia oczyszczania.
> [!NOTE]
> Usługa Backup tworzy oddzielnej grupie zasobów niż grupa zasobów maszyny wirtualnej, aby zapisać kolekcję punktów przywracania. Nie można zablokować grupy zasobów przeznaczone do użycia przez usługę Backup doradza się klientów. Format nazwy grupy zasobów, utworzone przez usługę kopia zapasowa jest: AzureBackupRG_`<Geo>`_`<number>` Eg: AzureBackupRG_northeurope_1

**Krok 1. [Usuń blokadę z grupy zasobów punkt przywracania](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Krok 2. [Wyczyścić kolekcję punktów przywracania](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured — kopia zapasowa nie ma wystarczających uprawnień do usługi key vault dla kopii zapasowej zaszyfrowanych maszyn wirtualnych

**Kod błędu:** : UserErrorKeyvaultPermissionsNotConfigured <br>
**Komunikat o błędzie**: Kopia zapasowa nie ma wystarczających uprawnień do usługi key vault dla kopii zapasowej zaszyfrowanych maszyn wirtualnych. <br>

Dla operacji tworzenia kopii zapasowej zakończyło się sukcesem w zaszyfrowanych maszyn wirtualnych musi mieć uprawnienia do dostępu do magazynu kluczy. Można to zrobić za pomocą [witryny Azure portal](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) lub za pomocą [PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection).

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork — operacja migawki nie powiodło się z powodu braku łączności z siecią na maszynie wirtualnej

**Kod błędu:** : ExtensionSnapshotFailedNoNetwork<br>
**Komunikat o błędzie**: Nie można wykonać operacji migawki z powodu braku łączności z siecią na maszynie wirtualnej<br>

Po zarejestrowaniu i zaplanować maszyny Wirtualnej dla usługi Kopia zapasowa Azure Backup Inicjuje zadania, komunikując się z rozszerzenie kopii zapasowej maszyny Wirtualnej, aby utworzyć migawkę punktu w czasie. Dowolne z następujących warunków może uniemożliwić migawki wyzwalane. Jeśli nie zostanie wyzwolony, migawki, mogą wystąpić niepowodzenia wykonywania kopii zapasowej. Wykonaj następujące kroki w podanej kolejności, a następnie ponów próbę wykonania operacji:    
**Przyczyny 1: [Nie można pobrać stanu migawki lub migawka nie może być przyjęty.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Przyczyny 2: [Rozszerzenie kopii zapasowej nie powiedzie się zaktualizować lub załadować](#the-backup-extension-fails-to-update-or-load)**  
**Przyczyny 3: [Maszyna wirtualna nie ma dostępu do Internetu](#the-vm-has-no-internet-access)**

## <a name="ExtentionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtentionOperationFailedForManagedDisks - operacji rozszerzenia VMSnapshot nie powiodła się.

**Kod błędu:** : ExtentionOperationFailedForManagedDisks <br>
**Komunikat o błędzie**: Operacja rozszerzenia VMSnapshot nie powiodła się<br>

Po zarejestrowaniu i zaplanować maszyny Wirtualnej dla usługi Kopia zapasowa Azure Backup Inicjuje zadania, komunikując się z rozszerzenie kopii zapasowej maszyny Wirtualnej, aby utworzyć migawkę punktu w czasie. Dowolne z następujących warunków może uniemożliwić migawki wyzwalane. Jeśli nie zostanie wyzwolony, migawki, mogą wystąpić niepowodzenia wykonywania kopii zapasowej. Wykonaj następujące kroki w podanej kolejności, a następnie ponów próbę wykonania operacji:  
**Przyczyny 1: [Nie można pobrać stanu migawki lub migawka nie może być przyjęty.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Przyczyny 2: [Rozszerzenie kopii zapasowej nie powiedzie się zaktualizować lub załadować](#the-backup-extension-fails-to-update-or-load)**  
**Przyczyny 3: [Agent jest zainstalowany na maszynie wirtualnej, ale go nie odpowiada (dla maszyn wirtualnych Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Przyczyna 4: [Agent zainstalowany na maszynie wirtualnej jest nieaktualna (dla maszyn wirtualnych systemu Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 - kopii zapasowej nie powiedzie się, z powodu błędu wewnętrznego

**Kod błędu:** : BackUpOperationFailed / BackUpOperationFailedV2 <br>
**Komunikat o błędzie**: Niepowodzenie wykonywania kopii zapasowej z powodu błędu wewnętrznego — ponów próbę operacji za kilka minut <br>

Po zarejestrowaniu i zaplanować maszyny Wirtualnej dla usługi Kopia zapasowa Azure Backup Inicjuje zadania, komunikując się z rozszerzenie kopii zapasowej maszyny Wirtualnej, aby utworzyć migawkę punktu w czasie. Dowolne z następujących warunków może uniemożliwić migawki wyzwalane. Jeśli nie zostanie wyzwolony, migawki, mogą wystąpić niepowodzenia wykonywania kopii zapasowej. Wykonaj następujące kroki w podanej kolejności, a następnie ponów próbę wykonania operacji:  
**Przyczyny 1: [Agent został zainstalowany w maszynie Wirtualnej, ale nie odpowiada (dla maszyn wirtualnych Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Przyczyny 2: [Agent zainstalowany na maszynie wirtualnej jest nieaktualna (dla maszyn wirtualnych systemu Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Przyczyny 3: [Nie można pobrać stanu migawki lub migawka nie może być przyjęty.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Przyczyna 4: [Rozszerzenie kopii zapasowej nie powiedzie się zaktualizować lub załadować](#the-backup-extension-fails-to-update-or-load)**  
**Przyczyna 5: Usługa Kopia zapasowa nie ma uprawnień do usunięcia starych punktów przywracania z powodu blokady grupy zasobów** <br>
**Przyczyna 6: [Maszyna wirtualna nie ma dostępu do Internetu](#the-vm-has-no-internet-access)**

## <a name="usererrorunsupporteddisksize---currently-azure-backup-does-not-support-disk-sizes-greater-than-4095gb"></a>UserErrorUnsupportedDiskSize — obecnie usługa Azure Backup nie obsługuje dysków o rozmiarach większych niż 4095GB

**Kod błędu:** : UserErrorUnsupportedDiskSize <br>
**Komunikat o błędzie**: Obecnie usługa Azure Backup nie obsługuje dysków o rozmiarach większych niż 4095GB <br>

Operację tworzenia kopii zapasowej może zakończyć się niepowodzeniem, podczas wykonywania kopii zapasowej maszyny Wirtualnej o rozmiarze dysku jest większy niż 4095GB. Obsługę dużych dysków będzie dostępna wkrótce.  

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress — nie można zainicjować kopii zapasowej, ponieważ trwa inna operacja tworzenia kopii zapasowej

**Kod błędu:** : UserErrorBackupOperationInProgress <br>
**Komunikat o błędzie**: Nie można zainicjować kopii zapasowej, ponieważ trwa inna operacja tworzenia kopii zapasowej<br>

Ostatnie zadanie tworzenia kopii zapasowej nie powiodło się, ponieważ ma istniejące zadanie tworzenia kopii zapasowej w toku. Nie można uruchomić nowe zadanie tworzenia kopii zapasowej, aż do zakończenia bieżącego zadania. Upewnij się, że przed wyzwoleniem lub planowania innej operacji tworzenia kopii zapasowej zostanie przeprowadzona operacji tworzenia kopii zapasowej w toku. Aby sprawdzić stan zadania tworzenia kopii zapasowych, wykonaj następujące czynności:

1. Zaloguj się do witryny Azure Portal, kliknij pozycję **Wszystkie usługi**. Wpisz ciąg Recovery Services i kliknij pozycję **Magazyny usługi Recovery Services**. Zostanie wyświetlona lista magazynów usługi Recovery Services.
2. Z listy magazynów usługi recovery services wybierz magazyn, w którym skonfigurowano kopii zapasowej.
3. W menu pulpitu nawigacyjnego magazynu kliknij **zadania tworzenia kopii zapasowej** Wyświetla wszystkie zadania tworzenia kopii zapasowej.

    * Jeśli zadanie tworzenia kopii zapasowej jest w toku, zaczekaj na jej ukończyć lub anulować zadanie tworzenia kopii zapasowej.
        * Aby anulować zadanie tworzenia kopii zapasowej kliknij prawym przyciskiem myszy zadanie tworzenia kopii zapasowej, a następnie kliknij przycisk **anulować** lub użyj [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0).
    * Jeśli konfiguracja została zmieniona kopii zapasowej w innym magazynie, następnie upewnij się, Brak zadań tworzenia kopii zapasowych działające w starym magazynie. Jeśli istnieje następnie anulować zadanie tworzenia kopii zapasowej.
        * Aby anulować zadanie tworzenia kopii zapasowej kliknij prawym przyciskiem myszy zadanie tworzenia kopii zapasowej, a następnie kliknij przycisk **anulować** lub użyj [programu PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)
4. Ponów próbę wykonania operacji tworzenia kopii zapasowej.

Jeśli zaplanowanych operacji tworzenia kopii zapasowej trwa dłużej powodującą konflikt z następnym konfiguracji kopii zapasowej następnie przejrzyj [najlepszych rozwiązań](backup-azure-vms-introduction.md#best-practices), [wydajności tworzenia kopii zapasowych](backup-azure-vms-introduction.md#backup-performance) i [przywrócić brany pod uwagę ](backup-azure-vms-introduction.md#backup-and-restore-considerations).


## <a name="causes-and-solutions"></a>Przyczyny i potencjalne rozwiązania

### <a name="the-vm-has-no-internet-access"></a>Maszyna wirtualna nie ma dostępu do Internetu
Na wymagania wdrożenia maszyny Wirtualnej nie ma dostępu do Internetu. Lub może mieć ograniczenia, które uniemożliwiają dostęp do infrastruktury platformy Azure.

Aby funkcjonowało poprawnie, zapasowy numer wewnętrzny wymaga połączenia z platformy Azure z publicznymi adresami IP. Rozszerzenie wysyła polecenia do punktu końcowego usługi Azure storage (adres URL HTTPs) do zarządzania migawki maszyny Wirtualnej. Jeśli rozszerzenie nie ma dostępu do publicznej sieci internet, niepowodzenie tworzenia kopii zapasowej po pewnym czasie.

####  <a name="solution"></a>Rozwiązanie
Aby rozwiązać problem z siecią, zobacz [ustanowienia połączenia z siecią](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agent jest zainstalowany na maszynie wirtualnej, ale go nie odpowiada (dla maszyn wirtualnych Windows)

#### <a name="solution"></a>Rozwiązanie
Agent maszyny Wirtualnej może ulec uszkodzeniu lub usługa została zatrzymana. Ponowne zainstalowanie agenta maszyny Wirtualnej pomaga uzyskać najnowszą wersję. Pomaga również ponownie uruchomić komunikuje się z usługą.

1. Ustal, czy Usługa agenta gościa platformy Azure Windows jest uruchomiona w usługach (services.msc) maszyny Wirtualnej. Spróbuj ponownie uruchomić usługę agenta gościa platformy Azure Windows i zainicjuj tworzenie kopii zapasowej.    
2. Jeśli usługa agenta gościa platformy Azure Windows nie jest widoczny w usługach, w Panelu sterowania, przejdź do strony **programy i funkcje** do określenia, czy Usługa agenta gościa platformy Azure Windows jest zainstalowana.
4. Jeśli agenta gościa platformy Azure Windows znajduje się w **programy i funkcje**, odinstaluj agenta gościa usługi Windows Azure.
5. Pobierz i zainstaluj [najnowszej wersji pliku MSI agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Musi mieć prawa administratora w celu ukończenia instalacji.
6. Sprawdź, czy usługi agenta gościa platformy Azure Windows są wyświetlane w usługach.
7. Uruchomienie w kopii zapasowej na żądanie:
    * W portalu, wybierz **Utwórz teraz kopię zapasową**.

Ponadto upewnij się, że [zainstalowano program Microsoft .NET 4.5](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) na maszynie wirtualnej. .NET 4.5 jest wymagany dla agenta maszyny wirtualnej do komunikacji z usługą.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agent zainstalowany na maszynie wirtualnej jest nieaktualna (dla maszyn wirtualnych systemu Linux)

#### <a name="solution"></a>Rozwiązanie
Najbardziej związane z agentem lub rozszerzenie awarii dla maszyn wirtualnych systemu Linux są spowodowane przez problemy, które mają wpływ na nieaktualne agenta maszyny Wirtualnej. Aby rozwiązać ten problem, wykonaj te ogólne wytyczne:

1. Postępuj zgodnie z instrukcjami dotyczącymi [aktualizowania agenta maszyny Wirtualnej systemu Linux](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > Firma Microsoft *zdecydowanie zaleca się* aktualizacji agenta wyłącznie za pośrednictwem repozytorium dystrybucji. Firma Microsoft nie zaleca się pobranie kodu agenta bezpośrednio z serwisu GitHub i aktualizowania. Jeśli najnowszą wersję agenta dla Twojej dystrybucji nie jest obsługiwana dystrybucja dostępnej, skontaktuj się z pomocą instrukcje dotyczące sposobu jego instalacji. Aby sprawdzić, czy najnowsze agenta, przejdź do [agenta systemu Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) strony w repozytorium GitHub.

2. Upewnij się, że agent platformy Azure działa na maszynie Wirtualnej, uruchamiając następujące polecenie: `ps -e`

   Jeśli proces nie jest uruchomiona, uruchom go ponownie przy użyciu następujących poleceń:

   * Aby uzyskać Ubuntu: `service walinuxagent start`
   * Dla innych dystrybucji: `service waagent start`

3. [Konfigurowanie automatycznego ponownego uruchomienia agenta](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Uruchom nową kopię zapasową testu. Jeśli błąd będzie się powtarzał, należy zebrać następujące dzienniki z maszyny Wirtualnej:

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

Jeśli firma Microsoft wymaga pełnego rejestrowania waagent, wykonaj następujące czynności:

1. W pliku /etc/waagent.conf Znajdź następujący wiersz: **Włącz pełne rejestrowanie (y | n)**
2. Zmiana **Logs.Verbose** wartość z *n* do *y*.
3. Zapisać zmiany, a następnie ponownie uruchom waagent, wykonując kroki opisane wcześniej w tej sekcji.

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Nie można pobrać stanu migawki lub migawka nie może być przyjęty.
Kopia zapasowa maszyny Wirtualnej, zależy od tego, wydanie polecenia migawki do podstawowego konta magazynu. Kopia zapasowa może zakończyć się niepowodzeniem, ponieważ nie ma dostępu do konta magazynu lub wykonanie zadania migawki jest opóźnione.

#### <a name="solution"></a>Rozwiązanie
Następujące warunki mogłyby spowodować zadania migawki nie powiedzie się:

| Przyczyna | Rozwiązanie |
| --- | --- |
| Stan maszyny Wirtualnej jest zgłaszany niepoprawnie, ponieważ maszyna wirtualna zostanie zamknięta w protokole RDP (Remote Desktop). | Wyłączenie maszyny Wirtualnej w protokole RDP sprawdzić w portalu w celu ustalenia, czy stan maszyny Wirtualnej jest poprawna. Jeśli nie jest poprawny, należy wyłączyć maszynę Wirtualną w portalu przy użyciu **zamknięcia** opcji na pulpicie nawigacyjnym maszyn wirtualnych. |
| Maszyna wirtualna nie można pobrać adres hosta lub w sieci szkieletowej z serwera DHCP. | DHCP musi być włączona na gościu przeznaczonego na kopie zapasowe maszyn wirtualnych IaaS do pracy. Jeśli maszyna wirtualna nie może uzyskać adres hosta lub w sieci szkieletowej z odpowiedzi serwerów DHCP 245, go nie można pobrać lub uruchomić żadnych rozszerzeń. Jeśli potrzebujesz statycznego prywatnego adresu IP, należy skonfigurować ją przy użyciu **witryny Azure portal** lub **PowerShell** i upewnij się, że jest włączona opcja DHCP wewnątrz maszyny Wirtualnej. [Dowiedz się więcej](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) o konfigurowaniu statyczny adres IP przy użyciu programu PowerShell.

### <a name="the-backup-extension-fails-to-update-or-load"></a>Rozszerzenie kopii zapasowej nie powiedzie się zaktualizować lub załadować
Jeśli nie można załadować rozszerzenia kopii zapasowej nie powiedzie się, ponieważ migawka nie może być przyjęty.

#### <a name="solution"></a>Rozwiązanie

Odinstaluj rozszerzenie Aby wymusić rozszerzenie VMSnapshot, aby załadować ponownie. Następnej próby tworzenia kopii zapasowej ponownie ładuje rozszerzenie.

Aby odinstalować rozszerzenie:

1. W [witryny Azure portal](https://portal.azure.com/), przejdź do maszyny Wirtualnej, której dotyczy niepowodzenia wykonywania kopii zapasowej.
2. Wybierz **ustawienia**.
3. Wybierz pozycję **Rozszerzenia**.
4. Wybierz **rozszerzenie Vmsnapshot**.
5. Wybierz **odinstalować**.

Dla maszyny Wirtualnej systemu Linux, jeśli rozszerzenie VMSnapshot nie są wyświetlane w witrynie Azure portal [aktualizacja agenta systemu Linux platformy Azure](../virtual-machines/linux/update-agent.md), a następnie uruchomić tworzenie kopii zapasowej.

Wykonanie tych kroków powoduje, że rozszerzenie, należy ponownie zainstalować podczas następnej kopii zapasowej.

### <a name="remove_lock_from_the_recovery_point_resource_group"></a>Usuń blokadę z grupy zasobów punktu odzyskiwania
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do **opcja wszystkie zasoby**, wybierz grupę zasobów kolekcji punktów przywracania w następującym formacie AzureBackupRG_`<Geo>`_`<number>`.
3. W **ustawienia** zaznacz **blokad** do wyświetlenia blokad.
4. Aby usunąć blokadę, wybierz wielokropek, a następnie kliknij przycisk **Usuń**.

    ![Usuń blokady](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a> Wyczyścić kolekcję punktów przywracania
Po usunięciu blokady, punkty przywracania zostały wyczyszczone. Aby wyczyścić punktów przywracania, wykonaj dowolną z metod:<br>
* [Czyszczenie przywracania kolekcję punktów, uruchamiając kopii zapasowej ad-hoc](#clean-up-restore-point-collection-by-running-ad-hoc-backup)<br>
* [Czyszczenie Przywracanie punktu kolekcji z witryny Azure portal](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-ad-hoc-backup"></a>Czyszczenie przywracania kolekcję punktów, uruchamiając kopii zapasowej ad-hoc
Po usunięciu blokady, Wyzwól kopię zapasową ad hoc/ręczne. Pozwoli to zagwarantować, że punkty przywracania są automatycznie czyszczone. Oczekiwane operacja ad hoc/ręczne się niepowodzeniem po raz pierwszy; jednak zapewni automatycznego czyszczenia zamiast ręczne usuwanie punktów przywracania. Po oczyszczaniu następnej zaplanowanej kopii zapasowej powinna zakończyć się pomyślnie.

> [!NOTE]
> Automatyczne oczyszczanie nastąpi po kilku godzinach wyzwalanie tworzenia kopii zapasowej ad hoc/ręczne. Jeśli zaplanowane tworzenie kopii zapasowej nadal kończy się niepowodzeniem, a następnie spróbuj ręcznie usunąć kolekcję punktów przywracania wykonując kroki wymienione [tutaj](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>Czyszczenie Przywracanie punktu kolekcji z witryny Azure portal <br>

Ręcznie wyczyścić przywracania wskazuje kolekcji, które nie są usuwane z powodu blokady na grupę zasobów, spróbuj wykonać następujące kroki:
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Na **Centrum** menu, kliknij przycisk **wszystkie zasoby**, wybierz grupę zasobów o następującym formacie AzureBackupRG_`<Geo>`_`<number>` gdzie znajduje się maszyna wirtualna.

    ![Usuń blokady](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Kliknij grupę zasobów, **Przegląd** zostanie wyświetlony blok.
4. Wybierz **Pokaż ukryte typy** opcję, aby wyświetlić wszystkie ukryte zasoby. Wybierz kolekcje punktów przywracania w następującym formacie AzureBackupRG_`<VMName>`_`<number>`.

    ![Usuń blokady](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Kliknij przycisk **Usuń**, aby wyczyścić kolekcję punktów przywracania.
6. Ponów operację tworzenia kopii zapasowej.

> [!NOTE]
 >Jeśli zasób (RP kolekcji) ma duże liczba punktów przywracania, usuwanie takie same w portalu może limitu czasu i zakończyć się niepowodzeniem. Jest to znany problem CRP, gdzie wszystkie punkty przywracania nie są usuwane w określonym czasie, i kończą się operacji; Jednak operacja usuwania powiedzie się zwykle 2 lub 3 ponowne próby.
