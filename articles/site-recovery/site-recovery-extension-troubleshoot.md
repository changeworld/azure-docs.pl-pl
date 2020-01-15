---
title: Rozwiązywanie problemów z rozszerzeniem maszyny wirtualnej platformy Azure na potrzeby odzyskiwania po awarii przy użyciu Azure Site Recovery
description: Rozwiązywanie problemów z rozszerzeniem maszyny wirtualnej platformy Azure na potrzeby odzyskiwania po awarii przy użyciu Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: 7f9ae32b95d629ef79f085ed590d9057b0414911
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941536"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Rozwiązywanie problemów z rozszerzeniem maszyny wirtualnej platformy Azure

W tym artykule opisano kroki rozwiązywania problemów, które mogą pomóc w rozwiązywaniu Azure Site Recovery błędów związanych z agentem maszyny wirtualnej i rozszerzeniem.


## <a name="azure-site-recovery-extension-time-out"></a>Przekroczono limit czasu rozszerzenia Azure Site Recovery  

Komunikat o błędzie: "Przekroczono limit czasu wykonywania zadania podczas śledzenia operacji rozszerzenia do uruchomienia"<br>
Kod błędu: "151076"

 Azure Site Recovery zainstalować rozszerzenie na maszynie wirtualnej w ramach zadania Włącz ochronę. Niektóre z poniższych warunków mogą uniemożliwić wyzwolenie ochrony, a zadanie kończy się niepowodzeniem. Wykonaj następujące kroki rozwiązywania problemów, a następnie ponów próbę wykonania operacji:

**Przyczyna 1: [Agent jest zainstalowany na maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Przyczyna 2: [Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Przyczyna 3: [Aktualizacja lub załadowanie rozszerzenia Site Recovery nie powiodło](#the-site-recovery-extension-fails-to-update-or-load) się**  

Komunikat o błędzie: "Poprzednia operacja rozszerzenia Site Recovery trwa dłużej niż oczekiwano".<br>
Kod błędu: "150066"<br>

**Przyczyna 1: [Agent jest zainstalowany na maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Przyczyna 2: [Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Przyczyna 3: [stan rozszerzenia Site Recovery jest nieprawidłowy](#the-site-recovery-extension-fails-to-update-or-load)**  

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>Ochrona nie powiedzie się, ponieważ agent maszyny wirtualnej nie odpowiada

Komunikat o błędzie: "Przekroczono limit czasu wykonywania zadania podczas śledzenia operacji rozszerzenia do uruchomienia".<br>
Kod błędu: "151099"<br>

Ten błąd może wystąpić, jeśli Agent gościa platformy Azure w maszynie wirtualnej nie jest w stanie gotowości.
Stan agenta gościa platformy Azure można sprawdzić w [Azure Portal](https://portal.azure.com/). Przejdź do maszyny wirtualnej, którą chcesz chronić, i sprawdź stan w pozycji "> ustawienia maszyny wirtualnej > Właściwości > status agenta". Większość czasu, gdy stan agenta stanie się gotowy po ponownym uruchomieniu maszyny wirtualnej. Jeśli jednak ponowne uruchomienie nie jest możliwe, a problem nadal występuje, wykonaj następujące kroki rozwiązywania problemów.

**Przyczyna 1: [Agent jest zainstalowany na maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Przyczyna 2: [Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  


Komunikat o błędzie: "Przekroczono limit czasu wykonywania zadania podczas śledzenia operacji rozszerzenia do uruchomienia".<br>
Kod błędu: "151095"<br>

Dzieje się tak, gdy wersja agenta na komputerze z systemem Linux jest stara. Wykonaj następujący krok rozwiązywania problemów.<br>
  **Przyczyna 1: [Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
## <a name="causes-and-solutions"></a>Przyczyny i rozwiązania

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agent jest zainstalowany na maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)

#### <a name="solution"></a>Rozwiązanie
Agent maszyny wirtualnej mógł zostać uszkodzony lub usługa mogła zostać zatrzymana. Ponowne zainstalowanie agenta maszyny wirtualnej pomoże uzyskać najnowszą wersję. Pomaga również w ponownym uruchomieniu komunikacji z usługą.

1. Ustal, czy "Usługa agenta gościa systemu Windows Azure" jest uruchomiona w usługach VM Services (Services. msc). Spróbuj ponownie uruchomić usługę "Agent gościa platformy Microsoft Azure".    
2. Jeśli usługa agenta gościa platformy Microsoft Azure nie jest widoczna w obszarze usługi, w panelu sterowania przejdź do pozycji **programy i funkcje** , aby określić, czy jest zainstalowana usługa agenta gościa systemu Windows.
4. Jeśli w aplecie **programy i funkcje**zostanie wyświetlony Agent gościa platformy Microsoft Azure, Odinstaluj agenta gościa systemu Windows.
5. Pobierz i zainstaluj [najnowszą wersję pliku msi agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Aby ukończyć instalację, musisz mieć uprawnienia administratora.
6. Sprawdź, czy w obszarze usługi są wyświetlane usługi agenta gościa platformy Microsoft Azure.
7. Uruchom ponownie zadanie ochrony.

Sprawdź również, czy na maszynie wirtualnej [zainstalowano Microsoft .NET 4,5](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) . Do komunikacji agenta maszyny wirtualnej z usługą jest wymagany program .NET 4,5.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)

#### <a name="solution"></a>Rozwiązanie
Większość błędów związanych z agentami lub rozszerzeniami dla maszyn wirtualnych z systemem Linux jest spowodowana przez problemy, które mają wpływ na nieaktualny Agent maszyny wirtualnej. Aby rozwiązać ten problem, postępuj zgodnie z następującymi ogólnymi wskazówkami:

1. Postępuj zgodnie z instrukcjami dotyczącymi [aktualizowania agenta maszyny wirtualnej z systemem Linux](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > *Zdecydowanie zalecamy* , aby zaktualizować agenta tylko za pomocą repozytorium dystrybucji. Nie zalecamy pobierania kodu agenta bezpośrednio z usługi GitHub i aktualizowania go. Jeśli najnowszy Agent dystrybucji nie jest dostępny, skontaktuj się z pomocą techniczną, aby uzyskać instrukcje dotyczące sposobu jej instalacji. Aby sprawdzić najnowszego agenta, przejdź do strony [agenta systemu Linux platformy Microsoft Azure](https://github.com/Azure/WALinuxAgent/releases) w repozytorium GitHub.

2. Upewnij się, że na maszynie wirtualnej jest uruchomiony agent platformy Azure, uruchamiając następujące polecenie: `ps -e`

   Jeśli proces nie jest uruchomiony, uruchom go ponownie przy użyciu następujących poleceń:

   * Dla Ubuntu: `service walinuxagent start`
   * W przypadku innych dystrybucji: `service waagent start`

3. [Skonfiguruj agenta autostartu](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Włącz ochronę maszyny wirtualnej.



### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Aktualizacja lub załadowanie rozszerzenia Site Recovery nie powiodło się
Jeśli rozszerzenia mają stan "puste", "nobieżniing" lub "przechodzenie".

#### <a name="solution"></a>Rozwiązanie

Odinstaluj rozszerzenie i ponownie uruchom operację.

Aby odinstalować rozszerzenie:

1. W [Azure Portal](https://portal.azure.com/)przejdź do maszyny wirtualnej, na której występuje błąd kopii zapasowej.
2. Wybierz **ustawienia**.
3. Wybierz pozycję **Rozszerzenia**.
4. Wybierz **Site Recovery rozszerzenie**.
5. Wybierz opcję **Odinstaluj**.

W przypadku maszyny wirtualnej z systemem Linux, jeśli rozszerzenie VMSnapshot nie jest wyświetlane w Azure Portal, [zaktualizuj agenta systemu Azure Linux](../virtual-machines/linux/update-agent.md), a następnie uruchom ochronę. 

Wykonanie tych kroków powoduje ponowne zainstalowanie rozszerzenia podczas ochrony.


