---
title: 'Rozwiązywanie problemów z usługi Kopia zapasowa Azure awarii: niedostępny stan agenta gościa | Dokumentacja firmy Microsoft'
description: Objawy, przyczyny i rozwiązania błędów kopia zapasowa Azure powiązane z agenta, rozszerzenia i dysków.
services: backup
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
keywords: Kopia zapasowa Azure; Agent maszyny Wirtualnej; Łączności sieciowej;
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: genli;markgal;sogup;
ms.openlocfilehash: 194b8237ce1bff6ac18878bc7eca6e0d3891aa33
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Rozwiązywanie problemów z usługi Kopia zapasowa Azure awarii: problemy z agentem lub rozszerzenia

Ten artykuł zawiera kroki rozwiązywania problemów, które mogą pomóc rozwiązać kopia zapasowa Azure błędy dotyczące komunikacji z agentem maszyny Wirtualnej i rozszerzenia.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="vm-agent-unable-to-communicate-with-azure-backup"></a>Agent maszyny Wirtualnej nie można nawiązać połączenia z usługą kopia zapasowa Azure

Komunikat o błędzie: "Agent maszyny Wirtualnej nie można nawiązać połączenia z kopii zapasowej systemu Azure"<br>
Kod błędu: "UserErrorGuestAgentStatusUnavailable"

Po zarejestrować i zaplanować maszyny Wirtualnej dla usługi tworzenia kopii zapasowej, kopia zapasowa inicjuje zadania komunikując się z migawki w chwili agenta maszyny Wirtualnej. Jeden z następujących warunków może uniemożliwić migawki są wyzwalane. Po wyzwoleniu nie jest migawka kopii zapasowej może zakończyć się niepowodzeniem. Wykonaj następujące kroki rozwiązywania problemów w podanej kolejności, a następnie ponów próbę wykonania operacji:

**Przyczyna 1: [maszyny Wirtualnej nie ma dostępu do Internetu](#the-vm-has-no-internet-access)**  
**Przyczyny 2: [agent jest zainstalowany na Maszynie wirtualnej, ale odpowiadać (dla maszyn wirtualnych systemu Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Przyczyny 3: [agent zainstalowany na maszynie wirtualnej są nieaktualne (dla maszyn wirtualnych systemu Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Przyczyna 4: [nie można pobrać stanu migawki lub migawka nie można pobrać](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**    
**Przyczyny 5: [zapasowy numer wewnętrzny nie może zaktualizować lub załadować](#the-backup-extension-fails-to-update-or-load)**  

## <a name="snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>Operacja migawki nie powiedzie się, ponieważ maszyna wirtualna nie jest połączony z siecią

Komunikat o błędzie: "Migawki: operacja nie powiodła się ze względu na brak łączności sieciowej na maszynie wirtualnej"<br>
Kod błędu: "ExtensionSnapshotFailedNoNetwork"

Po zarejestrować i zaplanować maszyny Wirtualnej dla usługi Kopia zapasowa Azure kopii zapasowej inicjuje zadania komunikując się z rozszerzenia kopii zapasowej maszyny Wirtualnej do tworzenia migawki punktu w czasie. Jeden z następujących warunków może uniemożliwić migawki są wyzwalane. Jeśli migawka nie jest wyzwalany, może wystąpić błąd tworzenia kopii zapasowej. Wykonaj następujące kroki rozwiązywania problemów w podanej kolejności, a następnie ponów próbę wykonania operacji:    
**Przyczyna 1: [maszyny Wirtualnej nie ma dostępu do Internetu](#the-vm-has-no-internet-access)**  
**Przyczyny 2: [nie można pobrać stanu migawki lub migawka nie można pobrać](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Przyczyny 3: [zapasowy numer wewnętrzny nie może zaktualizować lub załadować](#the-backup-extension-fails-to-update-or-load)**  

## <a name="vmsnapshot-extension-operation-failed"></a>Operacja rozszerzenia VMSnapshot kończy się niepowodzeniem

Komunikat o błędzie: "VMSnapshot rozszerzenia operacja nie powiodła się."<br>
Kod błędu: "ExtentionOperationFailed"

Po zarejestrować i zaplanować maszyny Wirtualnej dla usługi Kopia zapasowa Azure kopii zapasowej inicjuje zadania komunikując się z rozszerzenia kopii zapasowej maszyny Wirtualnej do tworzenia migawki punktu w czasie. Jeden z następujących warunków może uniemożliwić migawki są wyzwalane. Jeśli migawka nie jest wyzwalany, może wystąpić błąd tworzenia kopii zapasowej. Wykonaj następujące kroki rozwiązywania problemów w podanej kolejności, a następnie ponów próbę wykonania operacji:  
**Przyczyna 1: [nie można pobrać stanu migawki lub migawka nie można pobrać](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Przyczyny 2: [zapasowy numer wewnętrzny nie może zaktualizować lub załadować](#the-backup-extension-fails-to-update-or-load)**  
**Przyczyny 3: [agent jest zainstalowany na Maszynie wirtualnej, ale odpowiadać (dla maszyn wirtualnych systemu Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Przyczyna 4: [agent zainstalowany na maszynie wirtualnej są nieaktualne (dla maszyn wirtualnych systemu Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backup-fails-because-the-vm-agent-is-unresponsive"></a>Kopii zapasowej nie powiedzie się, ponieważ nie odpowiada, agent maszyny Wirtualnej

Komunikat o błędzie: "Nie można wykonać operacji, ponieważ Agent maszyny Wirtualnej nie jest elastyczny"

Po zarejestrować i zaplanować maszyny Wirtualnej dla usługi Kopia zapasowa Azure kopii zapasowej inicjuje zadania komunikując się z rozszerzenia kopii zapasowej maszyny Wirtualnej do tworzenia migawki punktu w czasie. Jeden z następujących warunków może uniemożliwić migawki są wyzwalane. Jeśli migawka nie jest wyzwalany, może wystąpić błąd tworzenia kopii zapasowej. Wykonaj następujące kroki rozwiązywania problemów w podanej kolejności, a następnie ponów próbę wykonania operacji:  
**Przyczyna 1: [agent jest zainstalowany na Maszynie wirtualnej, ale odpowiadać (dla maszyn wirtualnych systemu Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Przyczyny 2: [agent zainstalowany na maszynie wirtualnej są nieaktualne (dla maszyn wirtualnych systemu Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Przyczyny 3: [maszyny Wirtualnej nie ma dostępu do Internetu](#the-vm-has-no-internet-access)**  

## <a name="backup-fails-with-an-internal-error"></a>Kopii zapasowej kończy się niepowodzeniem z powodu błędu wewnętrznego

Komunikat o błędzie: "kopii zapasowej nie powiodło się z powodu wewnętrznego błędu — spróbuj ponownie wykonać operację w ciągu kilku minut."

Po zarejestrować i zaplanować maszyny Wirtualnej dla usługi Kopia zapasowa Azure kopii zapasowej inicjuje zadania komunikując się z rozszerzenia kopii zapasowej maszyny Wirtualnej do tworzenia migawki punktu w czasie. Jeden z następujących warunków może uniemożliwić migawki są wyzwalane. Jeśli migawka nie jest wyzwalany, może wystąpić błąd tworzenia kopii zapasowej. Wykonaj następujące kroki rozwiązywania problemów w podanej kolejności, a następnie ponów próbę wykonania operacji:  
**Przyczyna 1: [maszyny Wirtualnej nie ma dostępu do Internetu](#the-vm-has-no-internet-access)**  
**Przyczyny 2: [agenta zainstalowanego w Maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych systemu Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Przyczyny 3: [agent zainstalowany na maszynie wirtualnej są nieaktualne (dla maszyn wirtualnych systemu Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Przyczyna 4: [nie można pobrać stanu migawki lub migawka nie można pobrać](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Przyczyny 5: [zapasowy numer wewnętrzny nie może zaktualizować lub załadować](#the-backup-extension-fails-to-update-or-load)**  
**Przyczyna 6: [usługa kopii zapasowej nie ma uprawnienia do usuwania starych punktów przywracania z powodu blokady grupy zasobów](#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock)**

## <a name="causes-and-solutions"></a>Przyczyny i potencjalne rozwiązania

### <a name="the-vm-has-no-internet-access"></a>Maszyna wirtualna nie ma dostępu do Internetu
Na wymagania wdrożenia maszyny Wirtualnej nie ma dostępu do Internetu. Lub może mieć ograniczenia, które uniemożliwiają dostęp do infrastruktury platformy Azure.

Prawidłowe rozszerzenie usługi Backup wymaga połączenia z Azure publicznych adresów IP. Rozszerzenie wysyła polecenia do punktu końcowego usługi Azure storage (adres URL protokołu HTTP) do zarządzania migawki maszyny wirtualnej. Jeśli rozszerzenie nie ma dostępu do publicznej sieci internet, wykonaj kopię zapasową ostatecznie zakończy się niepowodzeniem.

####  <a name="solution"></a>Rozwiązanie
Aby rozwiązać ten problem, wypróbuj jedną z następujących metod:

##### <a name="allow-access-to-azure-storage-that-corresponds-to-the-region"></a>Zezwalaj na dostęp do magazynu Azure, umożliwiająca regionu

Można użyć [usługi tagi](../virtual-network/security-overview.md#service-tags) Aby zezwolić na połączenia z magazynem określonego regionu. Upewnij się, że reguła, która umożliwia uzyskanie dostępu do konta magazynu ma wyższy priorytet niż zasady tego blokuje dostęp do Internetu. 

![Grupy zabezpieczeń sieci przy użyciu tagów magazynu dla regionu](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

> [!WARNING]
> Tagi usługi magazynowania są w wersji zapoznawczej. Są one dostępne tylko w określonych regionach. Aby uzyskać listę regionów, zobacz [usługi tagi dla magazynu](../virtual-network/security-overview.md#service-tags).

##### <a name="create-a-path-for-http-traffic"></a>Tworzenie ścieżki dla ruchu HTTP

1. Jeśli masz ograniczeń sieci w miejscu (na przykład grupa zabezpieczeń sieci), wdrażania serwera proxy HTTP do kierowania ruchem.
2. Aby umożliwić dostęp do Internetu z serwera proxy HTTP, należy dodać reguły do grupy zabezpieczeń sieci, jeśli istnieje.

Aby dowiedzieć się, jak skonfigurować serwer proxy HTTP dla kopii zapasowych maszyn wirtualnych, zobacz [przygotowania środowiska do tworzenia kopii zapasowych maszyn wirtualnych platformy Azure](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

Dysków Azure zarządzanych, może być konieczne otwierający dodatkowy port (na porcie 8443) na zapory.

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agent jest zainstalowany na maszynie wirtualnej, ale go nie odpowiada (dla maszyn wirtualnych systemu Windows)

#### <a name="solution"></a>Rozwiązanie
Agent maszyny Wirtualnej może być uszkodzony lub może być zatrzymana usługa. Ponowne instalowanie agenta maszyny Wirtualnej pomaga uzyskać najnowszą wersję. Pomaga również ponowne uruchomienie komunikacji z usługą.

1. Ustal, czy Usługa agenta gościa z systemem Windows jest uruchomiona w usługach maszyny Wirtualnej (services.msc). Spróbuj uruchomić ponownie usługę agenta gościa z systemem Windows i zainicjować kopii zapasowej.    
2. Jeśli usługa agenta gościa z systemem Windows nie jest widoczny w usługach, w Panelu sterowania, przejdź do **programy i funkcje** ustalenie, czy Usługa agenta gościa z systemem Windows jest zainstalowana.
4. Jeśli Agent gościa Windows znajduje się w **programy i funkcje**, odinstaluj agenta gościa z systemem Windows.
5. Pobierz i zainstaluj [najnowszej wersji pliku MSI agenta](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Musi mieć prawa administratora w celu ukończenia instalacji.
6. Sprawdź, czy usługi agenta gościa z systemem Windows jest wyświetlany w usługach.
7. Uruchom kopię zapasową na żądanie: 
    * W portalu, wybierz **Utwórz kopię zapasową teraz**.

Ponadto upewnij się, że [jest zainstalowany program Microsoft .NET 4.5](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) w maszynie Wirtualnej. .NET 4.5 jest wymagany dla agenta maszyny Wirtualnej do komunikowania się z usługą.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agent zainstalowany na maszynie wirtualnej są nieaktualne (dla maszyn wirtualnych systemu Linux)

#### <a name="solution"></a>Rozwiązanie
Najbardziej związane z agenta lub rozszerzenie błędów dla maszyn wirtualnych systemu Linux są spowodowane przez problemy, które mają wpływ na nieaktualne agenta maszyny Wirtualnej. Aby rozwiązać ten problem, wykonaj następujące ogólne wytyczne:

1. Postępuj zgodnie z instrukcjami dotyczącymi [aktualizacja agenta maszyny Wirtualnej systemu Linux](../virtual-machines/linux/update-agent.md).

 > [!NOTE]
 > Firma Microsoft *zdecydowanie zaleca się* zaktualizowanie agenta tylko za pośrednictwem repozytorium dystrybucji. Nie zaleca się pobierania kodu agenta bezpośrednio z witryny GitHub i zaktualizowaniem go. Jeśli najnowsza wersja agenta dla dystrybucji nie jest dostępny, skontaktuj się z dystrybucji obsługę instrukcje dotyczące sposobu jego instalacji. Aby sprawdzić najnowsze agenta, przejdź do [agenta Windows Azure w systemie Linux](https://github.com/Azure/WALinuxAgent/releases) strony w repozytorium GitHub.

2. Upewnij się, że agent programu Azure działa na maszynie Wirtualnej, uruchamiając następujące polecenie: `ps -e`

 Jeśli nie jest uruchomiony proces, uruchom go ponownie za pomocą następujących poleceń:

 * Dla Ubuntu: `service walinuxagent start`
 * Inne dystrybucji: `service waagent start`

3. [Konfigurowanie automatycznego ponownego uruchomienia agenta](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Uruchom nową kopię zapasową testu. Jeśli błąd będzie się powtarzać, zbierz dzienniki następujące maszyny Wirtualnej:

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

Jeśli wymagane pełne rejestrowanie dla agenta waagent, wykonaj następujące kroki:

1. W pliku /etc/waagent.conf Znajdź następujący wiersz: **Włącz pełne rejestrowanie (y | n)**
2. Zmień **Logs.Verbose** wartość z *n* do *y*.
3. Zapisz zmiany, a następnie ponownie uruchom agenta waagent, wykonując kroki opisane wcześniej w tej sekcji.

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Nie można pobrać stanu migawki lub migawka nie można pobrać
Kopii zapasowej maszyny Wirtualnej zależy od wydawania polecenia migawki podstawowym konta magazynu. Kopia zapasowa może nie powieść, ponieważ go nie ma dostępu do konta magazynu lub wykonanie zadania migawki jest opóźnione.

#### <a name="solution"></a>Rozwiązanie
Poniższe warunki może spowodować awarię zadania migawki:

| Przyczyna | Rozwiązanie |
| --- | --- |
| Stan maszyny Wirtualnej jest zgłaszana niepoprawnie, ponieważ maszyna wirtualna jest zamknięta w protokołu RDP (Remote Desktop). | Jeśli wyłączysz maszyny Wirtualnej w ramach protokołu RDP, sprawdź portalu, aby ustalić, czy stan maszyny Wirtualnej jest prawidłowa. Jeśli nie jest poprawny, należy wyłączyć maszynę Wirtualną w portalu przy użyciu **zamknięcia** opcji na pulpicie maszyny Wirtualnej. |
| Maszyna wirtualna nie można pobrać adresu hosta lub z sieci szkieletowej z serwera DHCP. | DHCP musi być włączona na gościu przeznaczonego na kopie zapasowe maszyn wirtualnych IaaS do pracy. Jeśli maszyna wirtualna nie może pobrać adres hosta lub z sieci szkieletowej z odpowiedzi DHCP 245, go nie można pobrać lub uruchomić wszystkich rozszerzeń. Jeśli potrzebujesz statycznego prywatnego adresu IP, należy skonfigurować za pomocą platformy. Opcja DHCP w ramach maszyny Wirtualnej powinna być włączona w lewo. Aby uzyskać więcej informacji, zobacz [ustawić wewnętrzny statycznego prywatnego adresu IP](../virtual-network/virtual-networks-reserved-private-ip.md). |

### <a name="the-backup-extension-fails-to-update-or-load"></a>Zapasowy numer wewnętrzny nie może zaktualizować lub załadować
Jeśli nie można załadować rozszerzeń, tworzenia kopii zapasowej nie powiedzie się, ponieważ nie można pobrać migawek.

#### <a name="solution"></a>Rozwiązanie

Odinstaluj rozszerzenie wymuszenie rozszerzenia VMSnapshot, aby załadować ponownie. Następnej próbie kopii zapasowej ponowne załadowanie rozszerzenia.

Aby odinstalować rozszerzenia:

1. W [portalu Azure](https://portal.azure.com/), przejdź do maszyny Wirtualnej, w którym występuje niepowodzenia wykonywania kopii zapasowej.
2. Wybierz **ustawienia**.
3. Wybierz **rozszerzenia**.
4. Wybierz **Vmsnapshot rozszerzenia**.
5. Wybierz **odinstalować**.

Dla maszyny Wirtualnej systemu Linux, jeśli rozszerzenie VMSnapshot nie są wyświetlane w portalu Azure [zaktualizować agenta systemu Linux Azure](../virtual-machines/linux/update-agent.md), a następnie uruchomić tworzenie kopii zapasowej. 

Wykonanie tych kroków powoduje, że rozszerzenie, należy zainstalować ponownie podczas następnej kopii zapasowej.

### <a name="backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock"></a>Usługa kopii zapasowej nie ma uprawnienia do usuwania starych punktów przywracania z powodu blokady grupy zasobów
Ten problem dotyczy zarządzanych maszyn wirtualnych, w których użytkownik zablokuje grupy zasobów. W takim przypadku usługi tworzenia kopii zapasowej nie można usunąć starsze punkty przywracania. Ponieważ ma limitu 18 punktów przywracania, nowe kopie zapasowe będą kończyć się niepowodzeniem.

#### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, wykonaj następujące kroki, aby usunąć kolekcję punkt przywracania: <br>
 
1. Usunięcie blokady w grupie zasobów, w którym znajduje się maszyna wirtualna. 
2. Zainstaluj ARMClient przy użyciu Chocolatey: <br>
   https://github.com/projectkudu/ARMClient
3. Zaloguj się do ARMClient: <br>
    `.\armclient.exe login`
4. Pobierz kolekcję punkt przywracania, do której odnosi się do maszyny Wirtualnej: <br>
    `.\armclient.exe get https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30`

    Przykład: `.\armclient.exe get https://management.azure.com/subscriptions/f2edfd5d-5496-4683-b94f-b3588c579006/resourceGroups/winvaultrg/providers/Microsoft.Compute/restorepointcollections/AzureBackup_winmanagedvm?api-version=2017-03-30`
5. Usuń kolekcję punkt przywracania: <br>
    `.\armclient.exe delete https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30` 
6. Następnego zaplanowanego tworzenia kopii zapasowej automatycznie tworzy kolekcję punktu przywracania i nowych punktów przywracania.

 
Problem zostanie wystąpi w przypadku zablokowania grupy zasobów. 

