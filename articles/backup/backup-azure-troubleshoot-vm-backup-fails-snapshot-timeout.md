---
title: 'Rozwiązywanie problemów z awarii usługi Azure Backup: niedostępny stan agenta gościa'
description: Objawy, przyczyny i rozwiązania usługi Azure Backup błędy związane z agenta, rozszerzeń i dysków.
services: backup
author: genlin
manager: cshepard
keywords: Usługa Azure backup; Agent maszyny Wirtualnej; Łączności sieciowej;
ms.service: backup
ms.topic: troubleshooting
ms.date: 06/25/2018
ms.author: genli
ms.openlocfilehash: ce4a889cae852d333ea9862138f4d44471677c26
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2018
ms.locfileid: "45544017"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Rozwiązywanie problemów z awarii usługi Azure Backup: problemy z agentem lub rozszerzenia

Ten artykuł zawiera kroki rozwiązywania problemów, które mogą pomóc Ci rozwiązać błędy usługi Kopia zapasowa Azure dotyczące komunikacji z agentem maszyny Wirtualnej i rozszerzenia.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="vm-agent-unable-to-communicate-with-azure-backup"></a>Nie można nawiązać komunikacji z usługą Azure Backup agent maszyny Wirtualnej

Komunikat o błędzie: "Nie można nawiązać komunikacji z usługą Azure Backup Agent maszyny Wirtualnej"<br>
Kod błędu: "UserErrorGuestAgentStatusUnavailable"

Po zarejestrowaniu się i zaplanować maszyny Wirtualnej dla usługi Kopia zapasowa, Backup Inicjuje zadania, komunikując się z agentem maszyny Wirtualnej, aby utworzyć migawkę punktu w czasie. Dowolne z następujących warunków może uniemożliwić migawki wyzwalane. Migawka nie zostanie wyzwolony, tworzenie kopii zapasowej może zakończyć się niepowodzeniem. Wykonaj następujące kroki w podanej kolejności, a następnie ponów próbę wykonania operacji:

**Przyczyny 1: [maszyny Wirtualnej nie ma dostępu do Internetu](#the-vm-has-no-internet-access)**  
**Przyczyny 2: [agent jest zainstalowany w maszynie Wirtualnej, ale nie odpowiada (dla maszyn wirtualnych Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Przyczyny 3: [agent zainstalowany na maszynie wirtualnej jest nieaktualna (dla maszyn wirtualnych systemu Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Przyczyna 4: [nie można pobrać stanu migawki lub migawka nie może być przyjęty.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**    
**Przyczyna 5: [rozszerzenie kopii zapasowej nie powiedzie się zaktualizować lub załadować](#the-backup-extension-fails-to-update-or-load)**  

## <a name="snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>Operacja migawki nie powiedzie się, ponieważ maszyna wirtualna nie jest podłączona do sieci

Komunikat o błędzie: "Migawki: operacja nie powiodła się z powodu braku łączności z siecią na maszynie wirtualnej"<br>
Kod błędu: "ExtensionSnapshotFailedNoNetwork"

Po zarejestrowaniu i zaplanować maszyny Wirtualnej dla usługi Kopia zapasowa Azure Backup Inicjuje zadania, komunikując się z rozszerzenie kopii zapasowej maszyny Wirtualnej, aby utworzyć migawkę punktu w czasie. Dowolne z następujących warunków może uniemożliwić migawki wyzwalane. Jeśli nie zostanie wyzwolony, migawki, mogą wystąpić niepowodzenia wykonywania kopii zapasowej. Wykonaj następujące kroki w podanej kolejności, a następnie ponów próbę wykonania operacji:    
**Przyczyny 1: [maszyny Wirtualnej nie ma dostępu do Internetu](#the-vm-has-no-internet-access)**  
**Przyczyny 2: [nie można pobrać stanu migawki lub migawka nie może być przyjęty.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Przyczyny 3: [rozszerzenie kopii zapasowej nie powiedzie się zaktualizować lub załadować](#the-backup-extension-fails-to-update-or-load)**  

## <a name="vmsnapshot-extension-operation-failed"></a>Operacja rozszerzenia VMSnapshot nie powiedzie się.

Komunikat o błędzie: "Operacja rozszerzenia VMSnapshot nie powiodła się."<br>
Kod błędu: "ExtentionOperationFailed"

Po zarejestrowaniu i zaplanować maszyny Wirtualnej dla usługi Kopia zapasowa Azure Backup Inicjuje zadania, komunikując się z rozszerzenie kopii zapasowej maszyny Wirtualnej, aby utworzyć migawkę punktu w czasie. Dowolne z następujących warunków może uniemożliwić migawki wyzwalane. Jeśli nie zostanie wyzwolony, migawki, mogą wystąpić niepowodzenia wykonywania kopii zapasowej. Wykonaj następujące kroki w podanej kolejności, a następnie ponów próbę wykonania operacji:  
**Przyczyny 1: [nie można pobrać stanu migawki lub migawka nie może być przyjęty.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Przyczyny 2: [rozszerzenie kopii zapasowej nie powiedzie się zaktualizować lub załadować](#the-backup-extension-fails-to-update-or-load)**  
**Przyczyny 3: [agent jest zainstalowany w maszynie Wirtualnej, ale nie odpowiada (dla maszyn wirtualnych Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Przyczyna 4: [agent zainstalowany na maszynie wirtualnej jest nieaktualna (dla maszyn wirtualnych systemu Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backup-fails-because-the-vm-agent-is-unresponsive"></a>Niepowodzenie tworzenia kopii zapasowej, ponieważ agent maszyny Wirtualnej nie odpowiada

Komunikat o błędzie: "Nie można nawiązać komunikacji z agentem maszyny Wirtualnej w celu uzyskania stanu migawki" <br>
Kod błędu: "GuestAgentSnapshotTaskStatusError"

Po zarejestrowaniu i zaplanować maszyny Wirtualnej dla usługi Kopia zapasowa Azure Backup Inicjuje zadania, komunikując się z rozszerzenie kopii zapasowej maszyny Wirtualnej, aby utworzyć migawkę punktu w czasie. Dowolne z następujących warunków może uniemożliwić migawki wyzwalane. Jeśli nie zostanie wyzwolony, migawki, mogą wystąpić niepowodzenia wykonywania kopii zapasowej. Wykonaj następujące kroki w podanej kolejności, a następnie ponów próbę wykonania operacji:  
**Przyczyny 1: [agent jest zainstalowany w maszynie Wirtualnej, ale nie odpowiada (dla maszyn wirtualnych Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Przyczyny 2: [agent zainstalowany na maszynie wirtualnej jest nieaktualna (dla maszyn wirtualnych systemu Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Przyczyny 3: [maszyny Wirtualnej nie ma dostępu do Internetu](#the-vm-has-no-internet-access)**  

## <a name="backup-fails-with-an-internal-error"></a>Niepowodzenie tworzenia kopii zapasowej z powodu błędu wewnętrznego

Komunikat o błędzie: "kopii zapasowej nie powiodło się z powodu błędu wewnętrznego — ponów próbę operacji za kilka minut" <br>
Kod błędu: "BackUpOperationFailed" / "BackUpOperationFailedV2"

Po zarejestrowaniu i zaplanować maszyny Wirtualnej dla usługi Kopia zapasowa Azure Backup Inicjuje zadania, komunikując się z rozszerzenie kopii zapasowej maszyny Wirtualnej, aby utworzyć migawkę punktu w czasie. Dowolne z następujących warunków może uniemożliwić migawki wyzwalane. Jeśli nie zostanie wyzwolony, migawki, mogą wystąpić niepowodzenia wykonywania kopii zapasowej. Wykonaj następujące kroki w podanej kolejności, a następnie ponów próbę wykonania operacji:  
**Przyczyny 1: [maszyny Wirtualnej nie ma dostępu do Internetu](#the-vm-has-no-internet-access)**  
**Przyczyny 2: [agent został zainstalowany w maszynie Wirtualnej, ale nie odpowiada (dla maszyn wirtualnych Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Przyczyny 3: [agent zainstalowany na maszynie wirtualnej jest nieaktualna (dla maszyn wirtualnych systemu Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Przyczyna 4: [nie można pobrać stanu migawki lub migawka nie może być przyjęty.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Przyczyna 5: [rozszerzenie kopii zapasowej nie powiedzie się zaktualizować lub załadować](#the-backup-extension-fails-to-update-or-load)**  
**Przyczyna 6: [usługi Backup nie ma uprawnień do usunięcia starych punktów przywracania z powodu blokady grupy zasobów](#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock)**

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
5. Pobierz i zainstaluj [najnowszej wersji pliku MSI agenta](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Musi mieć prawa administratora w celu ukończenia instalacji.
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
3. Wybierz **rozszerzenia**.
4. Wybierz **rozszerzenie Vmsnapshot**.
5. Wybierz **odinstalować**.

Dla maszyny Wirtualnej systemu Linux, jeśli rozszerzenie VMSnapshot nie są wyświetlane w witrynie Azure portal [aktualizacja agenta systemu Linux platformy Azure](../virtual-machines/linux/update-agent.md), a następnie uruchomić tworzenie kopii zapasowej. 

Wykonanie tych kroków powoduje, że rozszerzenie, należy ponownie zainstalować podczas następnej kopii zapasowej.

### <a name="backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock"></a>Usługa Backup nie ma uprawnień do usunięcia starych punktów przywracania z powodu blokady grupy zasobów
Ten problem dotyczy zarządzane maszyny wirtualne, w których użytkownik zablokuje grupy zasobów. W tym przypadku usługi kopii zapasowej nie można usunąć starsze punkty przywracania. Ponieważ istnieje limit punktów przywracania 18, nowych kopii zapasowych będzie kończyć się niepowodzeniem.

#### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, Usuń blokadę z grupy zasobów i wykonaj następujące kroki, aby usunąć kolekcję punktów przywracania: 
 
1. Usuń blokadę, w grupie zasobów, w którym znajduje się maszyna wirtualna. 
2. Zainstaluj ARMClient przy użyciu narzędzia Chocolatey: <br>
   https://github.com/projectkudu/ARMClient
3. Zaloguj się do ARMClient: <br>
    `.\armclient.exe login`
4. Pobierz kolekcję punktów przywracania, która odnosi się do maszyny Wirtualnej: <br>
    `.\armclient.exe get https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30`

    Przykład: `.\armclient.exe get https://management.azure.com/subscriptions/f2edfd5d-5496-4683-b94f-b3588c579006/resourceGroups/winvaultrg/providers/Microsoft.Compute/restorepointcollections/AzureBackup_winmanagedvm?api-version=2017-03-30`
5. Usuń kolekcję punktów przywracania: <br>
    `.\armclient.exe delete https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30` 
6. Następnej zaplanowanej kopii zapasowej automatycznie tworzy kolekcję punktów przywracania i nowe punkty przywracania.

Po zakończeniu możesz ponownie umieścić ponownie blokady w grupie zasobów, maszyny Wirtualnej. 
