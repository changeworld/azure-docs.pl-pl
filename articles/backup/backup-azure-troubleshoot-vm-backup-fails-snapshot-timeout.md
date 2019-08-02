---
title: 'Rozwiązywanie problemów z błędem Azure Backup: Stan agenta gościa jest niedostępny'
description: Objawy, przyczyny i rozwiązania błędów Azure Backup związanych z agentami, rozszerzeniem i dyskami.
ms.reviewer: saurse
author: dcurwin
manager: carmonm
keywords: Kopia zapasowa Azure; Agent maszyny wirtualnej; Łączność sieciowa;
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: 7fc288ad9e33088b1b5248c1b61ed439ac95a9c4
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688976"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Rozwiązywanie problemów z błędem Azure Backup: Problemy z agentem lub rozszerzeniem

W tym artykule opisano kroki rozwiązywania problemów, które mogą pomóc w rozwiązywaniu Azure Backup błędów związanych z komunikacją z agentem i rozszerzeniem maszyny wirtualnej.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]



## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable — Agent maszyny wirtualnej nie może komunikować się z Azure Backup

**Kod błędu**: UserErrorGuestAgentStatusUnavailable <br>
**Komunikat o błędzie**: Agent maszyny wirtualnej nie może komunikować się z Azure Backup<br>

Po zarejestrowaniu i zaplanowaniu maszyny wirtualnej dla usługi tworzenia kopii zapasowej program Backup inicjuje zadanie, komunikując się z agentem maszyny wirtualnej w celu przejęcia migawki określonego momentu. Jeden z następujących warunków może uniemożliwić wyzwolenie migawki. Gdy migawka nie zostanie wyzwolona, tworzenie kopii zapasowej może zakończyć się niepowodzeniem. Wykonaj następujące kroki rozwiązywania problemów w podanej kolejności, a następnie ponów próbę wykonania operacji:<br>
**Przyczyna 1: [Agent jest zainstalowany na maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Przyczyna 2: [Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Przyczyna 3: [Nie można pobrać stanu migawki lub nie można wykonać migawki](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**     
**Przyczyna 4: [Aktualizacja lub załadowanie rozszerzenia kopii zapasowej nie powiodło się](#the-backup-extension-fails-to-update-or-load)**  
**Przyczyna 5: [Maszyna wirtualna nie ma dostępu do Internetu](#the-vm-has-no-internet-access)**

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError — nie można skomunikować się z agentem maszyny wirtualnej w celu uzyskania stanu migawki

**Kod błędu**: GuestAgentSnapshotTaskStatusError<br>
**Komunikat o błędzie**: Nie można nawiązać komunikacji z agentem maszyny wirtualnej w celu uzyskania stanu migawki <br>

Po zarejestrowaniu i zaplanowaniu maszyny wirtualnej dla usługi Azure Backup backup inicjuje zadanie, komunikując się z rozszerzeniem kopii zapasowej maszyny wirtualnej w celu przeprowadzenia migawki w danym momencie. Jeden z następujących warunków może uniemożliwić wyzwolenie migawki. Jeśli migawka nie zostanie wyzwolona, może wystąpić błąd kopii zapasowej. Wykonaj następujące kroki rozwiązywania problemów w podanej kolejności, a następnie ponów próbę wykonania operacji:  
**Przyczyna 1: [Agent jest zainstalowany na maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Przyczyna 2: [Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Przyczyna 3: [Maszyna wirtualna nie ma dostępu do Internetu](#the-vm-has-no-internet-access)**

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached — Osiągnięto maksymalny limit kolekcji punktów przywracania

**Kod błędu**: UserErrorRpCollectionLimitReached <br>
**Komunikat o błędzie**: Osiągnięto maksymalny limit kolekcji punktów przywracania. <br>
* Ten problem może wystąpić, jeśli w grupie zasobów punktu odzyskiwania istnieje blokada uniemożliwiająca automatyczne czyszczenie punktu odzyskiwania.
* Ten problem może również wystąpić w przypadku wyzwolenia wielu kopii zapasowych dziennie. Obecnie zalecamy tylko jedną kopię zapasową dziennie, ponieważ natychmiastowe punkty przywracania są przechowywane przez 1-5 dni zgodnie ze skonfigurowanym przechowywaniem migawek, a tylko 18 RPS pliku można skojarzyć z maszyną wirtualną w danym momencie. <br>

Zalecana akcja:<br>
Aby rozwiązać ten problem, Usuń blokadę z grupy zasobów maszyny wirtualnej, a następnie ponów operację, aby wyzwolić czyszczenie.
> [!NOTE]
> Usługa Backup tworzy oddzielną grupę zasobów niż grupa zasobów maszyny wirtualnej do przechowywania kolekcji punktów przywracania. Klienci nie mogą zablokować grupy zasobów utworzonej do użycia przez usługę kopii zapasowej. Format nazewnictwa grupy zasobów utworzonej przez usługę kopii zapasowej to: AzureBackupRG_`<Geo>`_`<number>` Eg: AzureBackupRG_northeurope_1

**Krok 1. [Usuń blokadę z grupy zasobów punktu przywracania](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Krok 2. [Wyczyść kolekcję punktów przywracania](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured — kopia zapasowa nie ma wystarczających uprawnień do magazynu kluczy na potrzeby tworzenia kopii zapasowych zaszyfrowanych maszyn wirtualnych

**Kod błędu**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Komunikat o błędzie**: Kopia zapasowa nie ma wystarczających uprawnień do magazynu kluczy na potrzeby tworzenia kopii zapasowych zaszyfrowanych maszyn wirtualnych. <br>

Aby operacja tworzenia kopii zapasowej zakończyła się pomyślnie na zaszyfrowanych maszynach wirtualnych, musi mieć uprawnienia dostępu do magazynu kluczy. Można to zrobić za pomocą [Azure Portal](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) lub za pośrednictwem [programu PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection).

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork — operacja migawki nie powiodła się z powodu braku łączności sieciowej na maszynie wirtualnej

**Kod błędu**: ExtensionSnapshotFailedNoNetwork<br>
**Komunikat o błędzie**: Nie można wykonać operacji migawki z powodu braku łączności z siecią na maszynie wirtualnej<br>

Po zarejestrowaniu i zaplanowaniu maszyny wirtualnej dla usługi Azure Backup backup inicjuje zadanie, komunikując się z rozszerzeniem kopii zapasowej maszyny wirtualnej w celu przeprowadzenia migawki w danym momencie. Jeden z następujących warunków może uniemożliwić wyzwolenie migawki. Jeśli migawka nie zostanie wyzwolona, może wystąpić błąd kopii zapasowej. Wykonaj następujące kroki rozwiązywania problemów w podanej kolejności, a następnie ponów próbę wykonania operacji:    
**Przyczyna 1: [Nie można pobrać stanu migawki lub nie można wykonać migawki](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Przyczyna 2: [Aktualizacja lub załadowanie rozszerzenia kopii zapasowej nie powiodło się](#the-backup-extension-fails-to-update-or-load)**  
**Przyczyna 3: [Maszyna wirtualna nie ma dostępu do Internetu](#the-vm-has-no-internet-access)**

## <a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>Nie można wykonać operacji rozszerzenia ExtensionOperationFailedForManagedDisks-VMSnapshot

**Kod błędu**: ExtensionOperationFailedForManagedDisks <br>
**Komunikat o błędzie**: Operacja rozszerzenia VMSnapshot nie powiodła się<br>

Po zarejestrowaniu i zaplanowaniu maszyny wirtualnej dla usługi Azure Backup backup inicjuje zadanie, komunikując się z rozszerzeniem kopii zapasowej maszyny wirtualnej w celu przeprowadzenia migawki w danym momencie. Jeden z następujących warunków może uniemożliwić wyzwolenie migawki. Jeśli migawka nie zostanie wyzwolona, może wystąpić błąd kopii zapasowej. Wykonaj następujące kroki rozwiązywania problemów w podanej kolejności, a następnie ponów próbę wykonania operacji:  
**Przyczyna 1: [Nie można pobrać stanu migawki lub nie można wykonać migawki](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Przyczyna 2: [Aktualizacja lub załadowanie rozszerzenia kopii zapasowej nie powiodło się](#the-backup-extension-fails-to-update-or-load)**  
**Przyczyna 3: [Agent jest zainstalowany na maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Przyczyna 4: [Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed/BackUpOperationFailedV2 — tworzenie kopii zapasowej kończy się niepowodzeniem z powodu błędu wewnętrznego

**Kod błędu**: BackUpOperationFailed / BackUpOperationFailedV2 <br>
**Komunikat o błędzie**: Tworzenie kopii zapasowej nie powiodło się z powodu błędu wewnętrznego — ponów próbę wykonania operacji za kilka minut <br>

Po zarejestrowaniu i zaplanowaniu maszyny wirtualnej dla usługi Azure Backup backup inicjuje zadanie, komunikując się z rozszerzeniem kopii zapasowej maszyny wirtualnej w celu przeprowadzenia migawki w danym momencie. Jeden z następujących warunków może uniemożliwić wyzwolenie migawki. Jeśli migawka nie zostanie wyzwolona, może wystąpić błąd kopii zapasowej. Wykonaj następujące kroki rozwiązywania problemów w podanej kolejności, a następnie ponów próbę wykonania operacji:  
**Przyczyna 1: [Agent zainstalowany na maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Przyczyna 2: [Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Przyczyna 3: [Nie można pobrać stanu migawki lub nie można wykonać migawki](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Przyczyna 4: [Aktualizacja lub załadowanie rozszerzenia kopii zapasowej nie powiodło się](#the-backup-extension-fails-to-update-or-load)**  
**Przyczyna 5: Usługa kopii zapasowej nie ma uprawnień do usuwania starych punktów przywracania z powodu blokady grupy zasobów** <br>
**Przyczyna 6: [Maszyna wirtualna nie ma dostępu do Internetu](#the-vm-has-no-internet-access)**

## <a name="usererrorunsupporteddisksize---currently-azure-backup-does-not-support-disk-sizes-greater-than-4095gb"></a>UserErrorUnsupportedDiskSize — obecnie Azure Backup nie obsługuje dysków o rozmiarach większych niż 4095 GB

**Kod błędu**: UserErrorUnsupportedDiskSize <br>
**Komunikat o błędzie**: Obecnie Azure Backup nie obsługuje dysków o rozmiarach większych niż 4095 GB <br>

Operacja tworzenia kopii zapasowej może zakończyć się niepowodzeniem podczas tworzenia kopii zapasowej maszyny wirtualnej o rozmiarze dysku większym niż 4095 GB. Aby zarejestrować się w celu uzyskania prywatnej wersji zapoznawczej Azure Backup duże wsparcie dla dysków o rozmiarze większym niż 4 TB do 30TB, Wróć AskAzureBackupTeam@microsoft.comdo nas.

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress — nie można zainicjować kopii zapasowej, ponieważ trwa inna operacja tworzenia kopii zapasowej

**Kod błędu**: UserErrorBackupOperationInProgress <br>
**Komunikat o błędzie**: Nie można zainicjować kopii zapasowej, ponieważ inna operacja tworzenia kopii zapasowej jest obecnie w toku<br>

Ostatnie zadanie tworzenia kopii zapasowej nie powiodło się, ponieważ istnieje zadanie tworzenia kopii zapasowej w toku. Nie można rozpocząć nowego zadania tworzenia kopii zapasowej, dopóki nie zakończy się bieżące zadanie. Upewnij się, że operacja tworzenia kopii zapasowej jest w toku, przed wyzwoleniem lub zaplanowaniem innej operacji tworzenia kopii zapasowej. Aby sprawdzić stan zadań tworzenia kopii zapasowej, wykonaj poniższe kroki:

1. Zaloguj się do witryny Azure Portal, kliknij pozycję **Wszystkie usługi**. Wpisz ciąg Recovery Services i kliknij pozycję **Magazyny usługi Recovery Services**. Zostanie wyświetlona lista magazynów usługi Recovery Services.
2. Z listy magazynów usługi Recovery Services wybierz magazyn, w którym jest skonfigurowana kopia zapasowa.
3. W menu pulpitu nawigacyjnego magazynu kliknij pozycję **zadania tworzenia kopii zapasowej** , aby wyświetlić wszystkie zadania tworzenia kopii zapasowej.

    * Jeśli zadanie tworzenia kopii zapasowej jest w toku, poczekaj na jego zakończenie lub Anuluj zadanie tworzenia kopii zapasowej.
        * Aby anulować zadanie tworzenia kopii zapasowej, kliknij prawym przyciskiem myszy zadanie tworzenia kopii zapasowej, a następnie kliknij przycisk **Anuluj** lub Użyj [programu PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0).
    * Jeśli ponownie skonfigurowano kopię zapasową w innym magazynie, upewnij się, że w starym magazynie nie są uruchomione żadne zadania tworzenia kopii zapasowej. Jeśli istnieje, Anuluj zadanie tworzenia kopii zapasowej.
        * Aby anulować zadanie tworzenia kopii zapasowej, kliknij prawym przyciskiem myszy zadanie tworzenia kopii zapasowej i kliknij przycisk **Anuluj** lub Użyj [programu PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0) .
4. Spróbuj ponownie wykonać operację tworzenia kopii zapasowej.

Jeśli operacja zaplanowanej kopii zapasowej trwa dłużej w konflikcie z kolejną konfiguracją kopii zapasowej, [](backup-azure-vms-introduction.md#best-practices)należy zapoznać się z najlepszymi rozwiązaniami, [wydajnością kopii zapasowej](backup-azure-vms-introduction.md#backup-performance) i przywróceniu. [](backup-azure-vms-introduction.md#backup-and-restore-considerations)


## <a name="causes-and-solutions"></a>Przyczyny i rozwiązania

### <a name="the-vm-has-no-internet-access"></a>Maszyna wirtualna nie ma dostępu do Internetu
Na potrzeby wdrożenia maszyna wirtualna nie ma dostępu do Internetu. Mogą też istnieć ograniczenia, które uniemożliwiają dostęp do infrastruktury platformy Azure.

Aby prawidłowo działać, rozszerzenie kopii zapasowej wymaga łączności z publicznymi adresami IP platformy Azure. Rozszerzenie wysyła polecenia do punktu końcowego usługi Azure Storage (adres URL HTTPs) do zarządzania migawkami maszyny wirtualnej. Jeśli rozszerzenie nie ma dostępu do publicznego Internetu, wykonanie kopii zapasowej kończy się niepowodzeniem.

####  <a name="solution"></a>Rozwiązanie
Aby rozwiązać problem z siecią, zobacz [ustanawianie łączności sieciowej](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agent jest zainstalowany na maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)

#### <a name="solution"></a>Rozwiązanie
Agent maszyny wirtualnej mógł zostać uszkodzony lub usługa mogła zostać zatrzymana. Ponowne zainstalowanie agenta maszyny wirtualnej pomoże uzyskać najnowszą wersję. Pomaga również w ponownym uruchomieniu komunikacji z usługą.

1. Ustal, czy usługa agenta gościa systemu Windows Azure jest uruchomiona w usługach VM Services (Services. msc). Spróbuj ponownie uruchomić usługę agenta gościa systemu Windows Azure i zainicjuj wykonywanie kopii zapasowej.    
2. Jeśli usługa agenta gościa platformy Microsoft Azure nie jest widoczna w obszarze usługi, w panelu sterowania przejdź do pozycji **programy i funkcje** , aby określić, czy zainstalowano usługę agenta gościa systemu Windows Azure.
4. Jeśli w aplecie **programy i funkcje**zostanie wyświetlony Agent gościa platformy Microsoft Azure, Odinstaluj agenta gościa platformy Microsoft Azure.
5. Pobierz i zainstaluj [najnowszą wersję pliku msi agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Aby ukończyć instalację, musisz mieć uprawnienia administratora.
6. Sprawdź, czy usługi agenta gościa systemu Windows Azure są widoczne w obszarze usługi.
7. Uruchom kopię zapasową na żądanie:
    * W portalu wybierz pozycję **Utwórz kopię zapasową teraz**.

Sprawdź również, czy na maszynie wirtualnej zainstalowano [Microsoft .NET 4,5](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) . Do komunikacji agenta maszyny wirtualnej z usługą jest wymagany program .NET 4,5.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)

#### <a name="solution"></a>Rozwiązanie
Większość błędów związanych z agentami lub rozszerzeniami dla maszyn wirtualnych z systemem Linux jest spowodowana przez problemy, które mają wpływ na nieaktualny Agent maszyny wirtualnej. Aby rozwiązać ten problem, postępuj zgodnie z następującymi ogólnymi wskazówkami:

1. Postępuj zgodnie z instrukcjami dotyczącymi [aktualizowania agenta maszyny wirtualnej z systemem Linux](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > *Zdecydowanie zalecamy* , aby zaktualizować agenta tylko za pomocą repozytorium dystrybucji. Nie zalecamy pobierania kodu agenta bezpośrednio z usługi GitHub i aktualizowania go. Jeśli najnowszy Agent dystrybucji nie jest dostępny, skontaktuj się z pomocą techniczną, aby uzyskać instrukcje dotyczące sposobu jej instalacji. Aby sprawdzić najnowszego agenta, przejdź do strony [agenta systemu Linux platformy Microsoft Azure](https://github.com/Azure/WALinuxAgent/releases) w repozytorium GitHub.

2. Upewnij się, że na maszynie wirtualnej jest uruchomiony agent platformy Azure, uruchamiając następujące polecenie:`ps -e`

   Jeśli proces nie jest uruchomiony, uruchom go ponownie przy użyciu następujących poleceń:

   * Dla Ubuntu:`service walinuxagent start`
   * W przypadku innych dystrybucji:`service waagent start`

3. [Skonfiguruj agenta](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash)autostartu.
4. Uruchom nową testową kopię zapasową. Jeśli błąd będzie się utrzymywał, Zbierz następujące dzienniki z maszyny wirtualnej:

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

Jeśli wymagamy pełnego rejestrowania dla programu waagent, wykonaj następujące kroki:

1. W pliku/etc/waagent.conf znajdź następujący wiersz: **Włącz pełne rejestrowanie (t | n)**
2. Zmień wartość **dzienników. verbose** z *n* na *y*.
3. Zapisz zmiany, a następnie uruchom ponownie program waagent, wykonując kroki opisane wcześniej w tej sekcji.

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Nie można pobrać stanu migawki lub nie można wykonać migawki
Kopia zapasowa maszyny wirtualnej polega na wystawieniu polecenia migawki na bazowe konto magazynu. Tworzenie kopii zapasowej może zakończyć się niepowodzeniem, ponieważ nie ma dostępu do konta magazynu, lub ponieważ wykonywanie zadania migawki jest opóźnione.

#### <a name="solution"></a>Rozwiązanie
Następujące warunki mogą spowodować niepowodzenie zadania migawki:

| Przyczyna | Rozwiązanie |
| --- | --- |
| Stan maszyny wirtualnej jest raportowany niepoprawnie, ponieważ maszyna wirtualna jest zamknięta w Remote Desktop Protocol (RDP). | Jeśli wyłączysz maszynę wirtualną w protokole RDP, sprawdź Portal, aby określić, czy stan maszyny wirtualnej jest prawidłowy. Jeśli nie jest to poprawne, Zamknij maszynę wirtualną w portalu przy użyciu opcji **zamykania** na pulpicie NAWIGACYJNYM maszyny wirtualnej. |
| Maszyna wirtualna nie może pobrać adresu hosta ani sieci szkieletowej z serwera DHCP. | Aby tworzenie kopii zapasowej maszyny wirtualnej IaaS było możliwe, należy włączyć protokół DHCP wewnątrz gościa. Jeśli maszyna wirtualna nie może pobrać adresu hosta lub sieci szkieletowej z odpowiedzi DHCP 245, nie można pobrać ani uruchomić żadnych rozszerzeń. Jeśli potrzebujesz statycznego prywatnego adresu IP, należy go skonfigurować za pomocą **Azure Portal** lub **PowerShell** i upewnić się, że opcja DHCP wewnątrz maszyny wirtualnej jest włączona. [Dowiedz się więcej](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) o konfigurowaniu statycznego adresu IP za pomocą programu PowerShell.

### <a name="the-backup-extension-fails-to-update-or-load"></a>Aktualizacja lub załadowanie rozszerzenia kopii zapasowej nie powiodło się
Jeśli nie można załadować rozszerzeń, wykonywanie kopii zapasowej nie powiedzie się, ponieważ nie można wykonać migawki.

#### <a name="solution"></a>Rozwiązanie

Odinstaluj rozszerzenie, aby wymusić ponowne załadowanie rozszerzenia VMSnapshot. Kolejna próba wykonania kopii zapasowej ponownie ładuje rozszerzenie.

Aby odinstalować rozszerzenie:

1. W [Azure Portal](https://portal.azure.com/)przejdź do maszyny wirtualnej, na której występuje błąd kopii zapasowej.
2. Wybierz **ustawienia**.
3. Wybierz pozycję **Rozszerzenia**.
4. Wybierz **rozszerzenie VMSnapshot**.
5. Wybierz pozycję **Odinstaluj**.

W przypadku maszyny wirtualnej z systemem Linux, jeśli rozszerzenie VMSnapshot nie jest wyświetlane w Azure Portal, [zaktualizuj agenta systemu Azure Linux](../virtual-machines/linux/update-agent.md), a następnie uruchom kopię zapasową.

Wykonanie tych kroków powoduje ponowne zainstalowanie rozszerzenia podczas kolejnej kopii zapasowej.

### <a name="remove_lock_from_the_recovery_point_resource_group"></a>Usuń blokadę z grupy zasobów punktu odzyskiwania
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do **opcji wszystkie zasoby**, wybierz grupę zasobów kolekcji punktów przywracania w następującym formacie AzureBackupRG_`<Geo>`_.`<number>`
3. W sekcji **Ustawienia** wybierz pozycję **blokady** , aby wyświetlić blokady.
4. Aby usunąć blokadę, wybierz wielokropek, a następnie kliknij przycisk **Usuń**.

    ![Usuń blokadę](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a>Wyczyść kolekcję punktów przywracania
Po usunięciu blokady punkty przywracania muszą zostać oczyszczone. Aby wyczyścić punkty przywracania, wykonaj dowolną z następujących metod:<br>
* [Czyszczenie kolekcji punktów przywracania przez uruchomienie kopii zapasowej ad hoc](#clean-up-restore-point-collection-by-running-ad-hoc-backup)<br>
* [Wyczyść kolekcję punktów przywracania z Azure Portal](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-ad-hoc-backup"></a>Czyszczenie kolekcji punktów przywracania przez uruchomienie kopii zapasowej ad hoc
Po usunięciu blokady Wyzwól tworzenie kopii zapasowej ad hoc lub ręcznie. Dzięki temu punkty przywracania zostaną automatycznie oczyszczone. Należy oczekiwać, że ta operacja w trybie failover nie powiedzie się po raz pierwszy; jednak zapewni to automatyczne czyszczenie zamiast ręcznego usuwania punktów przywracania. Po oczyszczeniu Następna zaplanowana kopia zapasowa powinna zakończyć się powodzeniem.

> [!NOTE]
> Automatyczne czyszczenie zostanie wykonane po kilku godzinach wyzwalania tworzenia kopii zapasowej ad hoc/ręcznej. Jeśli zaplanowana kopia zapasowa nadal kończy się niepowodzeniem, spróbuj ręcznie usunąć kolekcję punktów przywracania, wykonując kroki opisane [tutaj](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>Wyczyść kolekcję punktów przywracania z Azure Portal <br>

Aby ręcznie wyczyścić kolekcję punktów przywracania, która nie została wyczyszczona ze względu na blokadę grupy zasobów, spróbuj wykonać następujące czynności:
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W menu **centrum** kliknij pozycję **wszystkie zasoby**, wybierz grupę zasobów o następującym formacie AzureBackupRG_`<Geo>`_`<number>` , gdzie znajduje się Twoja maszyna wirtualna.

    ![Usuń blokadę](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Kliknij pozycję Grupa zasobów, zostanie wyświetlony blok **Przegląd** .
4. Wybierz opcję **Pokaż ukryte typy** , aby wyświetlić wszystkie ukryte zasoby. Wybierz kolekcje punktów przywracania o następującym formacie AzureBackupRG_`<VMName>`_.`<number>`

    ![Usuń blokadę](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Kliknij przycisk **Usuń**, aby wyczyścić kolekcję punktów przywracania.
6. Spróbuj ponownie wykonać operację tworzenia kopii zapasowej.

> [!NOTE]
 >Jeśli zasób (kolekcja RP) ma dużą liczbę punktów przywracania, usunięcie tego samego z portalu może przekroczyć limit czasu i zakończyć się niepowodzeniem. Jest to znany problem CRP, w którym wszystkie punkty przywracania nie są usuwane w określonym czasie, a operacja przekracza limit czasu; Jednak operacja usuwania zazwyczaj kończy się powodzeniem po 2 lub 3 próbach.
