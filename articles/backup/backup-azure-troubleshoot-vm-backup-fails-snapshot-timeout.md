---
title: 'Rozwiązywanie problemów z awarii usługi Azure Backup: niedostępny stan agenta gościa'
description: Objawy, przyczyny i rozwiązania usługi Azure Backup błędy związane z agenta, rozszerzeń i dysków.
services: backup
author: genlin
manager: cshepard
keywords: Usługa Azure backup; Agent maszyny Wirtualnej; Łączności sieciowej;
ms.service: backup
ms.topic: troubleshooting
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 496afab869d8cf1b7b00791913c3082e31b45327
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633924"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Rozwiązywanie problemów z awarii usługi Azure Backup: problemy z agentem lub rozszerzenia

Ten artykuł zawiera kroki rozwiązywania problemów, które mogą pomóc Ci rozwiązać błędy usługi Kopia zapasowa Azure dotyczące komunikacji z agentem maszyny Wirtualnej i rozszerzenia.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable — nie można nawiązać komunikacji z usługą Azure Backup agent maszyny Wirtualnej

**Kod błędu:**: UserErrorGuestAgentStatusUnavailable <br>
**Komunikat o błędzie**: nie można nawiązać komunikacji z usługą Azure Backup Agent maszyny Wirtualnej<br>

Po zarejestrowaniu się i zaplanować maszyny Wirtualnej dla usługi Kopia zapasowa, Backup Inicjuje zadania, komunikując się z agentem maszyny Wirtualnej, aby utworzyć migawkę punktu w czasie. Dowolne z następujących warunków może uniemożliwić migawki wyzwalane. Migawka nie zostanie wyzwolony, tworzenie kopii zapasowej może zakończyć się niepowodzeniem. Wykonaj następujące kroki w podanej kolejności, a następnie ponów próbę wykonania operacji:<br>
**Przyczyny 1: [agent jest zainstalowany w maszynie Wirtualnej, ale nie odpowiada (dla maszyn wirtualnych Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Przyczyny 2: [agent zainstalowany na maszynie wirtualnej jest nieaktualna (dla maszyn wirtualnych systemu Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Przyczyny 3: [nie można pobrać stanu migawki lub migawka nie może być przyjęty.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**    
**Przyczyna 4: [rozszerzenie kopii zapasowej nie powiedzie się zaktualizować lub załadować](#the-backup-extension-fails-to-update-or-load)**  
**Przyczyna 5: [maszyny Wirtualnej nie ma dostępu do Internetu](#the-vm-has-no-internet-access)**

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError — nie można nawiązać komunikacji z agentem maszyny Wirtualnej w celu uzyskania stanu migawki

**Kod błędu:**: GuestAgentSnapshotTaskStatusError<br>
**Komunikat o błędzie**: nie można nawiązać komunikacji z agentem maszyny Wirtualnej w celu uzyskania stanu migawki <br>

Po zarejestrowaniu i zaplanować maszyny Wirtualnej dla usługi Kopia zapasowa Azure Backup Inicjuje zadania, komunikując się z rozszerzenie kopii zapasowej maszyny Wirtualnej, aby utworzyć migawkę punktu w czasie. Dowolne z następujących warunków może uniemożliwić migawki wyzwalane. Jeśli nie zostanie wyzwolony, migawki, mogą wystąpić niepowodzenia wykonywania kopii zapasowej. Wykonaj następujące kroki w podanej kolejności, a następnie ponów próbę wykonania operacji:  
**Przyczyny 1: [agent jest zainstalowany w maszynie Wirtualnej, ale nie odpowiada (dla maszyn wirtualnych Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Przyczyny 2: [agent zainstalowany na maszynie wirtualnej jest nieaktualna (dla maszyn wirtualnych systemu Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Przyczyny 3: [maszyny Wirtualnej nie ma dostępu do Internetu](#the-vm-has-no-internet-access)**

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached - osiągnęła maksymalny limit kolekcji punktów przywracania

**Kod błędu:**: UserErrorRpCollectionLimitReached <br>
**Komunikat o błędzie**: osiągnęła maksymalny limit kolekcji punktów przywracania. <br>
* Ten problem może wystąpić, jeśli jest zablokowana w grupie zasobów punktu odzyskiwania, zapobiegając automatycznego czyszczenia punktu odzyskiwania.
* Ten problem może również się zdarzyć, jeśli wiele kopii zapasowych są wyzwalane na dzień. Obecnie firma Microsoft zaleca, aby tylko jedna kopia zapasowa dziennie jako natychmiastowe RPs są przechowywane przez 7 dni, a tylko 18 błyskawiczny RPs może być skojarzony z maszyną Wirtualną, w dowolnym momencie. <br>

Zalecana akcja:<br>
Aby rozwiązać ten problem, Usuń blokadę dla grupy zasobów i spróbuj ponownie wykonać operację w celu wyzwolenia oczyszczania.

> [!NOTE]
    > Usługa Backup tworzy oddzielnej grupie zasobów niż grupa zasobów maszyny wirtualnej, aby zapisać kolekcję punktów przywracania. Nie można zablokować grupy zasobów przeznaczone do użycia przez usługę Backup doradza się klientów. Format nazwy grupy zasobów, utworzone przez usługę kopia zapasowa jest: AzureBackupRG_`<Geo>`_`<number>` Eg: AzureBackupRG_northeurope_1

**Krok 1: [usunięcie blokady z grupy zasobów punkt przywracania](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Krok 2: [wyczyścić kolekcję punktów przywracania](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured — kopia zapasowa nie ma wystarczających uprawnień do usługi key vault dla kopii zapasowej zaszyfrowanych maszyn wirtualnych.

**Kod błędu:**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Komunikat o błędzie**: kopia zapasowa nie ma wystarczających uprawnień do magazynu kluczy dla kopii zapasowej zaszyfrowanych maszyn wirtualnych. <br>

Dla operacji tworzenia kopii zapasowej zakończyło się sukcesem w zaszyfrowanych maszyn wirtualnych musi mieć uprawnienia do dostępu do magazynu kluczy. Można to zrobić za pomocą [witryny Azure portal](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption#provide-permissions-to-backup) lub za pomocą [programu PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection)

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork — operacja migawki nie powiodło się z powodu braku łączności z siecią na maszynie wirtualnej

**Kod błędu:**: ExtensionSnapshotFailedNoNetwork<br>
**Komunikat o błędzie**: operacja nie powiodła się z powodu braku łączności z siecią na maszynie wirtualnej. Tworzenie migawki<br>

Po zarejestrowaniu i zaplanować maszyny Wirtualnej dla usługi Kopia zapasowa Azure Backup Inicjuje zadania, komunikując się z rozszerzenie kopii zapasowej maszyny Wirtualnej, aby utworzyć migawkę punktu w czasie. Dowolne z następujących warunków może uniemożliwić migawki wyzwalane. Jeśli nie zostanie wyzwolony, migawki, mogą wystąpić niepowodzenia wykonywania kopii zapasowej. Wykonaj następujące kroki w podanej kolejności, a następnie ponów próbę wykonania operacji:    
**Przyczyny 1: [nie można pobrać stanu migawki lub migawka nie może być przyjęty.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Przyczyny 2: [rozszerzenie kopii zapasowej nie powiedzie się zaktualizować lub załadować](#the-backup-extension-fails-to-update-or-load)**  
**Przyczyny 3: [maszyny Wirtualnej nie ma dostępu do Internetu](#the-vm-has-no-internet-access)**

## <a name="ExtentionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtentionOperationFailed - operacji rozszerzenia VMSnapshot nie powiodła się

**Kod błędu:**: ExtentionOperationFailed <br>
**Komunikat o błędzie**: operacja rozszerzenia VMSnapshot nie powiodła się.<br>

Po zarejestrowaniu i zaplanować maszyny Wirtualnej dla usługi Kopia zapasowa Azure Backup Inicjuje zadania, komunikując się z rozszerzenie kopii zapasowej maszyny Wirtualnej, aby utworzyć migawkę punktu w czasie. Dowolne z następujących warunków może uniemożliwić migawki wyzwalane. Jeśli nie zostanie wyzwolony, migawki, mogą wystąpić niepowodzenia wykonywania kopii zapasowej. Wykonaj następujące kroki w podanej kolejności, a następnie ponów próbę wykonania operacji:  
**Przyczyny 1: [nie można pobrać stanu migawki lub migawka nie może być przyjęty.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Przyczyny 2: [rozszerzenie kopii zapasowej nie powiedzie się zaktualizować lub załadować](#the-backup-extension-fails-to-update-or-load)**  
**Przyczyny 3: [agent jest zainstalowany w maszynie Wirtualnej, ale nie odpowiada (dla maszyn wirtualnych Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Przyczyna 4: [agent zainstalowany na maszynie wirtualnej jest nieaktualna (dla maszyn wirtualnych systemu Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 - kopii zapasowej nie powiedzie się, z powodu błędu wewnętrznego

**Kod błędu:**: BackUpOperationFailed / BackUpOperationFailedV2 <br>
**Komunikat o błędzie**: kopii zapasowej nie powiodło się z powodu błędu wewnętrznego — ponów próbę operacji za kilka minut <br>

Po zarejestrowaniu i zaplanować maszyny Wirtualnej dla usługi Kopia zapasowa Azure Backup Inicjuje zadania, komunikując się z rozszerzenie kopii zapasowej maszyny Wirtualnej, aby utworzyć migawkę punktu w czasie. Dowolne z następujących warunków może uniemożliwić migawki wyzwalane. Jeśli nie zostanie wyzwolony, migawki, mogą wystąpić niepowodzenia wykonywania kopii zapasowej. Wykonaj następujące kroki w podanej kolejności, a następnie ponów próbę wykonania operacji:  
**Przyczyny 1: [agent został zainstalowany w maszynie Wirtualnej, ale nie odpowiada (dla maszyn wirtualnych Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Przyczyny 2: [agent zainstalowany na maszynie wirtualnej jest nieaktualna (dla maszyn wirtualnych systemu Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Przyczyny 3: [nie można pobrać stanu migawki lub migawka nie może być przyjęty.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Przyczyna 4: [rozszerzenie kopii zapasowej nie powiedzie się zaktualizować lub załadować](#the-backup-extension-fails-to-update-or-load)**  
**Przyczyna 5: [usługi Backup nie ma uprawnień do usunięcia starych punktów przywracania z powodu blokady grupy zasobów](#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock)** <br>
**Przyczyna 6: [maszyny Wirtualnej nie ma dostępu do Internetu](#the-vm-has-no-internet-access)**

## <a name="usererrorunsupporteddisksize---currently-azure-backup-does-not-support-disk-sizes-greater-than-1023gb"></a>UserErrorUnsupportedDiskSize — obecnie usługa Azure Backup nie obsługuje dysków o rozmiarach większych niż 1023GB

**Kod błędu:**: UserErrorUnsupportedDiskSize <br>
**Komunikat o błędzie**: obecnie usługi Azure Backup nie obsługuje dysków o rozmiarach większych niż 1023 GB <br>

Operację tworzenia kopii zapasowej może zakończyć się niepowodzeniem, podczas wykonywania kopii zapasowej maszyny Wirtualnej o rozmiarze dysku jest większy niż 1023GB, ponieważ magazynu nie zostanie uaktualniona do stosu kopii zapasowej maszyny Wirtualnej platformy Azure w wersji 2. Uaktualnienie do kopii zapasowych maszyn wirtualnych platformy Azure stack, podane w wersji 2 obsługuje do 4 TB. Przejrzyj te [korzyści](backup-upgrade-to-vm-backup-stack-v2.md), [zagadnienia](backup-upgrade-to-vm-backup-stack-v2.md#considerations-before-upgrade), a następnie przejść do uaktualnienia, wykonując te [instrukcje](backup-upgrade-to-vm-backup-stack-v2.md#upgrade).  

## <a name="usererrorstandardssdnotsupported---currently-azure-backup-does-not-support-standard-ssd-disks"></a>UserErrorStandardSSDNotSupported — obecnie usługa Azure Backup nie obsługuje dysków SSD w warstwie standardowa

**Kod błędu:**: UserErrorStandardSSDNotSupported <br>
**Komunikat o błędzie**: obecnie usługi Azure Backup nie obsługuje dysków SSD w warstwie standardowa <br>

Obecnie usługa Azure Backup obsługuje dyski SSD w warstwie standardowa tylko w przypadku magazynów, które zostaną uaktualnione do stosu kopii zapasowych maszyn wirtualnych platformy Azure w wersji 2. Przejrzyj te [korzyści](backup-upgrade-to-vm-backup-stack-v2.md), [zagadnienia](backup-upgrade-to-vm-backup-stack-v2.md#considerations-before-upgrade), a następnie przejść do uaktualnienia, wykonując te [instrukcje](backup-upgrade-to-vm-backup-stack-v2.md#upgrade).


## <a name="causes-and-solutions"></a>Przyczyny i potencjalne rozwiązania

### <a name="the-vm-has-no-internet-access"></a>Maszyna wirtualna nie ma dostępu do Internetu
Na wymagania wdrożenia maszyny Wirtualnej nie ma dostępu do Internetu. Lub może mieć ograniczenia, które uniemożliwiają dostęp do infrastruktury platformy Azure.

Aby funkcjonowało poprawnie, zapasowy numer wewnętrzny wymaga połączenia z platformy Azure z publicznymi adresami IP. Rozszerzenie wysyła polecenia do punktu końcowego usługi Azure storage (adres URL HTTPs) do zarządzania migawki maszyny Wirtualnej. Jeśli rozszerzenie nie ma dostępu do publicznej sieci internet, niepowodzenie tworzenia kopii zapasowej po pewnym czasie.

Istnieje możliwość wdrożyć serwer proxy, aby przekierować ruch maszyny Wirtualnej.
##### <a name="create-a-path-for-https-traffic"></a>Tworzenie ścieżki dla ruchu HTTPs

1. Jeśli masz ograniczeń sieci w miejscu (na przykład, sieciowej grupy zabezpieczeń), należy wdrożyć serwer proxy HTTPs do kierowania ruchu.
2. Aby umożliwić dostęp do Internetu z serwera proxy protokołu HTTPs, należy dodać reguły do sieciowej grupy zabezpieczeń, jeśli nie masz.

Aby dowiedzieć się, jak skonfigurować serwer proxy HTTPs dla kopii zapasowych maszyn wirtualnych, zobacz [przygotowania środowiska do tworzenia kopii zapasowych maszyn wirtualnych platformy Azure](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

W kopii zapasowej maszyny Wirtualnej lub serwera proxy, przez który ruch jest kierowany wymaga dostępu do usługi Azure publicznych adresów IP

####  <a name="solution"></a>Rozwiązanie
Aby rozwiązać ten problem, wypróbuj jedną z następujących metod:

##### <a name="allow-access-to-azure-storage-that-corresponds-to-the-region"></a>Zezwalaj na dostęp do usługi Azure storage, która odnosi się do regionu

Możesz użyć [tagów usług](../virtual-network/security-overview.md#service-tags) zezwala na połączenia do magazynu w określonym regionie. Upewnij się, że reguła, która umożliwia uzyskanie dostępu do konta magazynu ma wyższy priorytet niż zasady które blokuje dostęp do Internetu.

![Sieciowa grupa zabezpieczeń z tagami magazynu dla regionu](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

Aby dowiedzieć się, procedury krok po kroku, aby skonfigurować tagi usługi, obejrzyj [ten film wideo](https://youtu.be/1EjLQtbKm1M).

> [!WARNING]
> Tagi usługi Storage są w wersji zapoznawczej. Są one dostępne tylko w określonych regionach. Aby uzyskać listę regionów, zobacz [tagów dla magazynu usług](../virtual-network/security-overview.md#service-tags).

Jeśli używasz usługi Azure Managed Disks, może być konieczne otwarcie dodatkowych portów (na porcie 8443) na zaporach.

Ponadto jeśli podsieć nie ma trasy dla ruchu wychodzącego z Internetem, należy dodać punktu końcowego usługi za pomocą tagu usługi "Microsoft.Storage" do podsieci.

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agent jest zainstalowany na maszynie wirtualnej, ale go nie odpowiada (dla maszyn wirtualnych Windows)

#### <a name="solution"></a>Rozwiązanie
Agent maszyny Wirtualnej może ulec uszkodzeniu lub usługa została zatrzymana. Ponowne zainstalowanie agenta maszyny Wirtualnej pomaga uzyskać najnowszą wersję. Pomaga również ponownie uruchomić komunikuje się z usługą.

1. Ustal, czy Usługa agenta gościa Windows jest uruchomiona w usługach (services.msc) maszyny Wirtualnej. Spróbuj ponownie uruchomić usługę agenta gościa Windows i zainicjuj tworzenie kopii zapasowej.    
2. Jeśli usługa agenta gościa Windows nie jest widoczny w usługach, w Panelu sterowania, przejdź do strony **programy i funkcje** do określenia, czy Usługa agenta gościa Windows jest zainstalowana.
4. Jeśli Agent gościa Windows znajduje się w **programy i funkcje**, odinstaluj agenta gościa Windows.
5. Pobierz i zainstaluj [najnowszej wersji pliku MSI agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Musi mieć prawa administratora w celu ukończenia instalacji.
6. Sprawdź, czy usługi agenta gościa Windows są wyświetlane w usługach.
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
| Maszyna wirtualna nie można pobrać adres hosta lub w sieci szkieletowej z serwera DHCP. | DHCP musi być włączona na gościu przeznaczonego na kopie zapasowe maszyn wirtualnych IaaS do pracy. Jeśli maszyna wirtualna nie może uzyskać adres hosta lub w sieci szkieletowej z odpowiedzi serwerów DHCP 245, go nie można pobrać lub uruchomić żadnych rozszerzeń. Jeśli potrzebujesz statycznego prywatnego adresu IP, należy go skonfigurować za pomocą platformy. Opcji protokołu DHCP w ramach maszyny Wirtualnej powinien być włączony w lewo. Aby uzyskać więcej informacji, zobacz [Ustaw wewnętrzny statyczny prywatny adres IP](../virtual-network/virtual-networks-reserved-private-ip.md). |

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
1. Zaloguj się w witrynie [Azure Portal](http://portal.azure.com/).
2. Przejdź do **opcja wszystkie zasoby**, wybierz grupę zasobów kolekcji punktów przywracania w następującym formacie AzureBackupRG_`<Geo>`_`<number>`.
3. W **ustawienia** zaznacz **blokad** do wyświetlenia blokad.
4. Aby usunąć blokadę, wybierz wielokropek, a następnie kliknij przycisk **Usuń**.

    ![Usuń blokady ](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a> Wyczyścić kolekcję punktów przywracania
Po usunięciu blokady, punkty przywracania zostały wyczyszczone. Aby wyczyścić punktów przywracania, wykonaj dowolną z metod:<br>
* [Wyczyścić kolekcję punktów przywracania przez uruchamianie zapytań ad-hoc kopii zapasowej.](#clean-up-restore-point-collection-by-running-ad-hoc-backup)<br>
* [Czyszczenie Przywracanie punktu kolekcji z witryny Azure portal](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-ad-hoc-backup"></a>Wyczyścić kolekcję punktów przywracania przez uruchamianie zapytań ad-hoc kopii zapasowej.
Po usunięciu blokady, wyzwalanie ad-hoc/ręcznego tworzenia kopii zapasowej. Pozwoli to zagwarantować, że punkty przywracania są automatycznie czyszczone. Ta operacja ad-hoc/ręcznie zakończyć się niepowodzeniem po raz pierwszy; oczekiwane jednak zapewni automatycznego czyszczenia zamiast ręczne usuwanie punktów przywracania. Po oczyszczaniu następnej zaplanowanej kopii zapasowej powinna zakończyć się pomyślnie.

> [!NOTE]
    > Automatyczne oczyszczanie nastąpi po kilku godzinach wyzwolenie tworzenia kopii zapasowej ad-hoc/ręczne. Jeśli zaplanowane tworzenie kopii zapasowej nadal kończy się niepowodzeniem, a następnie spróbuj ręcznie usunąć kolekcję punktów przywracania wykonując kroki wymienione [tutaj](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>Czyszczenie Przywracanie punktu kolekcji z witryny Azure portal <br>

Ręcznie wyczyścić przywracania wskazuje kolekcji, które nie są usuwane z powodu blokady na grupę zasobów, spróbuj wykonać następujące kroki:
1. Zaloguj się w witrynie [Azure Portal](http://portal.azure.com/).
2. Na **Centrum** menu, kliknij przycisk **wszystkie zasoby**, wybierz grupę zasobów o następującym formacie AzureBackupRG_`<Geo>`_`<number>` gdzie znajduje się maszyna wirtualna.

    ![Usuń blokady ](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Kliknij grupę zasobów, **Przegląd** zostanie wyświetlony blok.
4. Wybierz **Pokaż ukryte typy** opcję, aby wyświetlić wszystkie ukryte zasoby. Wybierz kolekcje punktów przywracania w następującym formacie AzureBackupRG_`<VMName>`_`<number>`.

    ![Usuń blokady ](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Kliknij przycisk **Usuń**, aby wyczyścić kolekcję punktów przywracania.
6. Ponów operację tworzenia kopii zapasowej.
