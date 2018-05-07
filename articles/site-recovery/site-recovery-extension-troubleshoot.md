---
title: 'Rozwiązywanie problemów z niepowodzenia agenta usług Azure Site Recovery: niedostępny stan agenta gościa | Dokumentacja firmy Microsoft'
description: Objawy, przyczyny i rozwiązania błędów usługi Azure Site Recovery związanych z agent i rozszerzenia
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: asgang
ms.openlocfilehash: 44f2016dacf1433cfe3a61058a167c42700e37d6
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="troubleshoot-azure-site-recovery-extension-failures-issues-with-the-agent-or-extension"></a>Rozwiązywanie problemów z błędami rozszerzenia usługi Azure Site Recovery: problemy z agentem lub rozszerzenia

Ten artykuł zawiera kroki rozwiązywania problemów, które mogą pomóc wyeliminować błędy usługi Azure Site Recovery związanych z agenta maszyny Wirtualnej i rozszerzenia.


## <a name="azure-site-recovery-extension-time-out"></a>Limit czasu rozszerzenia usługi Azure Site Recovery  

Komunikat o błędzie: "wykonywania zadania został przekroczony podczas śledzenia dla operacji rozszerzenia ma być uruchamiana"<br>
Kod błędu: "151076"

 Usługa Azure Site Recovery Zainstaluj rozszerzenie na maszynie wirtualnej w ramach Zadanie włączania ochrony. Jeden z następujących warunków mogą uniemożliwić ochronę z inicjowane i niepowodzenie zadania. Wykonaj poniższe kroki rozwiązywania problemów, a następnie ponów próbę wykonania operacji:

**Przyczyna 1: [agent jest zainstalowany na Maszynie wirtualnej, ale odpowiadać (dla maszyn wirtualnych systemu Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Przyczyny 2: [agent zainstalowany na maszynie wirtualnej są nieaktualne (dla maszyn wirtualnych systemu Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Przyczyny 3: [rozszerzenia usługi Site Recovery nie może zaktualizować lub załadować](#the-site-recovery-extension-fails-to-update-or-load)**  

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>Ochrona nie powiedzie się, ponieważ nie odpowiada, agent maszyny Wirtualnej

Komunikat o błędzie: "wykonywania zadania został przekroczony podczas śledzenia dla operacji rozszerzenia ma zostać uruchomiony."<br>
Kod błędu: "151099"<br>

Ten błąd może wystąpić, jeśli agent Azure gościa na maszynie wirtualnej nie jest w stanie gotowe.
Można sprawdzić stan agenta gościa Azure w [portalu Azure](https://portal.azure.com/). Przejdź do maszyny wirtualnej, które chcesz chronić i sprawdź stan "maszyny Wirtualnej > Ustawienia > Właściwości > Stan agenta". W większości przypadków stan agenta gotowość po jego ponownym uruchomieniu maszyny wirtualnej. Jednak jeśli ponowne uruchomienie komputera nie jest możliwe opcją lub nadal są ukierunkowane problem, wykonaj następujące kroki rozwiązywania problemów.

**Przyczyna 1: [agent jest zainstalowany na Maszynie wirtualnej, ale odpowiadać (dla maszyn wirtualnych systemu Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Przyczyny 2: [agent zainstalowany na maszynie wirtualnej są nieaktualne (dla maszyn wirtualnych systemu Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  



## <a name="causes-and-solutions"></a>Przyczyny i potencjalne rozwiązania

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agent jest zainstalowany na maszynie wirtualnej, ale go nie odpowiada (dla maszyn wirtualnych systemu Windows)

#### <a name="solution"></a>Rozwiązanie
Agent maszyny Wirtualnej może być uszkodzony lub może być zatrzymana usługa. Ponowne zainstalowanie agenta maszyny Wirtualnej pomaga uzyskać najnowszą wersję. Pomaga również ponowne uruchomienie komunikacji z usługą.

1. Określić, czy "systemu Windows Azure agenta gościa" działa w usługach maszyny Wirtualnej (services.msc). Spróbuj ponownie uruchomić usługę "systemu Windows Azure Agent gościa usługi".    
2. Jeśli usługa agenta gościa usługi Windows Azure nie jest widoczny w usługach, w Panelu sterowania, przejdź do **programy i funkcje** ustalenie, czy Usługa agenta gościa z systemem Windows jest zainstalowana.
4. Jeśli agenta gościa usługi Windows Azure znajduje się w **programy i funkcje**, odinstaluj agenta gościa z systemem Windows.
5. Pobierz i zainstaluj [najnowszej wersji pliku MSI agenta](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Musi mieć prawa administratora w celu ukończenia instalacji.
6. Sprawdź, czy usługi Windows Azure gościa Agent jest wyświetlany w usługach.
7. Uruchom ponownie zadanie ochrony.

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
4. Włącz ochronę maszyny wirtualnej.



### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Rozszerzenie usługi Site Recovery nie może zaktualizować lub załadować
Jeśli stan rozszerzeń jest "pusty", "NotReady" lub przechodzenie obsługującej.

#### <a name="solution"></a>Rozwiązanie

Odinstaluj rozszerzenie, a następnie uruchom ponownie wykonać operację ponownie.

Aby odinstalować rozszerzenia:

1. W [portalu Azure](https://portal.azure.com/), przejdź do maszyny Wirtualnej, w którym występuje niepowodzenia wykonywania kopii zapasowej.
2. Wybierz **ustawienia**.
3. Wybierz **rozszerzenia**.
4. Wybierz **lokacji odzyskiwania rozszerzenia**.
5. Wybierz **odinstalować**.

Dla maszyny Wirtualnej systemu Linux, jeśli rozszerzenie VMSnapshot nie są wyświetlane w portalu Azure [zaktualizować agenta systemu Linux Azure](../virtual-machines/linux/update-agent.md), a następnie uruchom ochronę. 

Wykonanie tych kroków powoduje, że rozszerzenie ponownego zainstalowania podczas ochrony.


