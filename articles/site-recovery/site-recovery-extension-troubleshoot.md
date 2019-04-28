---
title: Rozwiązywanie problemów z agentami usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Zawiera informacje dotyczące objawy, przyczyny i rozwiązania błędów agenta usługi Azure Site Recovery.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 5ea701682c03370cea46f9126ecf78427a776371
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61280675"
---
# <a name="troubleshoot-issues-with-the-azure-site-recovery-agent"></a>Rozwiązywanie problemów z agentem usługi Azure Site Recovery

Ten artykuł zawiera kroki rozwiązywania problemów, które mogą pomóc Ci rozwiązać błędy usługi Azure Site Recovery, związane z agenta maszyny Wirtualnej i rozszerzenia.


## <a name="azure-site-recovery-extension-time-out"></a>Limit czasu rozszerzenia w usłudze Azure Site Recovery  

Komunikat o błędzie: "Wykonywania zadania został przekroczony podczas śledzenia operacji rozszerzenia do uruchomienia"<br>
Kod błędu: "151076"

 Usługa Azure Site Recovery Zainstaluj rozszerzenie na maszynie wirtualnej w ramach Zadanie włączania ochrony. Następujące warunki mogą uniemożliwić ochrony przed wyzwoleniem i niepowodzenie zadania. Wykonaj następujące kroki rozwiązywania problemów, a następnie ponów próbę wykonania operacji:

**Przyczyny 1: [Agent jest zainstalowany na maszynie wirtualnej, ale go nie odpowiada (dla maszyn wirtualnych Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Przyczyny 2: [Agent zainstalowany na maszynie wirtualnej jest nieaktualna (dla maszyn wirtualnych systemu Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Przyczyny 3: [Rozszerzenie usługi Site Recovery nie może zaktualizować lub obciążenia](#the-site-recovery-extension-fails-to-update-or-load)**  

Komunikat o błędzie: "Poprzedniego operacja rozszerzenia usługi site recovery trwa dłużej niż oczekiwano."<br>
Kod błędu: "150066"<br>

**Przyczyny 1: [Agent jest zainstalowany na maszynie wirtualnej, ale go nie odpowiada (dla maszyn wirtualnych Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Przyczyny 2: [Agent zainstalowany na maszynie wirtualnej jest nieaktualna (dla maszyn wirtualnych systemu Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Przyczyny 3: [Stan rozszerzenia usługi Site Recovery jest nieprawidłowa](#the-site-recovery-extension-fails-to-update-or-load)**  

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>Ochrona kończy się niepowodzeniem, ponieważ agent maszyny Wirtualnej nie odpowiada

Komunikat o błędzie: "Wykonywania zadania został przekroczony podczas śledzenia operacji rozszerzenia ma zostać uruchomiony."<br>
Kod błędu: "151099"<br>

Ten błąd może wystąpić, jeśli agent gościa platformy Azure na maszynie wirtualnej nie jest w stanie gotowe.
Możesz sprawdzić stan agenta gościa platformy Azure w [witryny Azure portal](https://portal.azure.com/). Przejdź do maszyny wirtualnej, które chcesz chronić i sprawdź stan "maszyna wirtualna > Ustawienia > Właściwości > Stan agenta". W większości przypadków stan agenta gotowość po jego ponownym uruchomieniu maszyny wirtualnej. Jednak jeśli ponowny rozruch nie jest możliwe opcji lub nadal pojawia się problem, wykonaj następujące kroki rozwiązywania problemów.

**Przyczyny 1: [Agent jest zainstalowany na maszynie wirtualnej, ale go nie odpowiada (dla maszyn wirtualnych Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Przyczyny 2: [Agent zainstalowany na maszynie wirtualnej jest nieaktualna (dla maszyn wirtualnych systemu Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  


Komunikat o błędzie: "Wykonywania zadania został przekroczony podczas śledzenia operacji rozszerzenia ma zostać uruchomiony."<br>
Kod błędu: "151095"<br>

Dzieje się tak, po starej wersji agenta na komputerze z systemem Linux. Wykonaj poniższe czynności dotyczące rozwiązywania problemów.<br>
  **Przyczyny 1: [Agent zainstalowany na maszynie wirtualnej jest nieaktualna (dla maszyn wirtualnych systemu Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
## <a name="causes-and-solutions"></a>Przyczyny i potencjalne rozwiązania

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agent jest zainstalowany na maszynie wirtualnej, ale go nie odpowiada (dla maszyn wirtualnych Windows)

#### <a name="solution"></a>Rozwiązanie
Agent maszyny Wirtualnej może ulec uszkodzeniu lub usługa została zatrzymana. Ponowne zainstalowanie agenta maszyny Wirtualnej pomaga uzyskać najnowszą wersję. Pomaga również ponownie uruchomić komunikuje się z usługą.

1. Określić, czy "Windows usługę agenta gościa platformy Azure" jest uruchomiona w usługach maszyny Wirtualnej (services.msc). Spróbuj uruchomić ponownie "Windows usługę agenta gościa platformy Azure".    
2. Jeśli usługa agenta gościa platformy Azure Windows nie jest widoczny w usługach, w Panelu sterowania, przejdź do strony **programy i funkcje** do określenia, czy Usługa agenta gościa Windows jest zainstalowana.
4. Jeśli agenta gościa platformy Azure Windows znajduje się w **programy i funkcje**, odinstaluj agenta gościa Windows.
5. Pobierz i zainstaluj [najnowszej wersji pliku MSI agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Musi mieć prawa administratora w celu ukończenia instalacji.
6. Sprawdź, czy usługi agenta gościa systemu Windows Azure znajduje się w usługach.
7. Uruchom ponownie zadanie ochrony.

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
4. Włącz ochronę maszyny wirtualnej.



### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Rozszerzenie usługi Site Recovery nie może zaktualizować lub obciążenia
Jeśli stan rozszerzeń jest "puste", "Niegotowe" lub Transitioning.

#### <a name="solution"></a>Rozwiązanie

Odinstaluj rozszerzenie, a następnie uruchom ponownie wykonać operację ponownie.

Aby odinstalować rozszerzenie:

1. W [witryny Azure portal](https://portal.azure.com/), przejdź do maszyny Wirtualnej, której dotyczy niepowodzenia wykonywania kopii zapasowej.
2. Wybierz **ustawienia**.
3. Wybierz pozycję **Rozszerzenia**.
4. Wybierz **odzyskiwania rozszerzenie witryny**.
5. Wybierz **odinstalować**.

Dla maszyny Wirtualnej systemu Linux, jeśli rozszerzenie VMSnapshot nie są wyświetlane w witrynie Azure portal [aktualizacja agenta systemu Linux platformy Azure](../virtual-machines/linux/update-agent.md), a następnie uruchom ochronę. 

Wykonanie tych kroków powoduje, że rozszerzenie, należy ponownie zainstalować podczas ochrony.


