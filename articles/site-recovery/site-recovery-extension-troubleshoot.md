---
title: Rozwiązywanie problemów z rozszerzeniem maszyny Wirtualnej platformy Azure w celu odzyskiwania po awarii za pomocą usługi Azure Site Recovery
description: Rozwiązywanie problemów z rozszerzeniem maszyny Wirtualnej platformy Azure w celu odzyskiwania po awarii za pomocą usługi Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: a780a42179a0bacf0e4a12ba1e75ae84943539b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190722"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Rozwiązywanie problemów z rozszerzeniem maszyny Wirtualnej platformy Azure

Ten artykuł zawiera kroki rozwiązywania problemów, które mogą pomóc w rozwiązaniu błędów usługi Azure Site Recovery związanych z agentem i rozszerzeniem maszyny Wirtualnej.


## <a name="azure-site-recovery-extension-time-out"></a>Limit czasu rozszerzenia usługi Azure Site Recovery  

Komunikat o błędzie: "Limit czasu wykonania zadania został przesunął się podczas śledzenia operacji rozszerzenia do rozpoczęcia"<br>
Kod błędu: "151076"

 Usługa Azure Site Recovery zainstalowała rozszerzenie na maszynie wirtualnej w ramach zadania ochrony włączania. Każdy z następujących warunków może uniemożliwić wyzwolenie ochrony i spowodować niepowodzenie zadania. Wykonaj następujące kroki rozwiązywania problemów, a następnie ponów próbę wykonania operacji:

- [Agent jest zainstalowany na maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Rozszerzenie odzyskiwania witryny nie może zaktualizować ani załadować](#the-site-recovery-extension-fails-to-update-or-load)

Komunikat o błędzie: "Poprzednia operacja rozszerzenia odzyskiwania witryny zajmuje więcej czasu niż oczekiwano."<br>
Kod błędu: "150066"

- [Agent jest zainstalowany na maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Stan rozszerzenia odzyskiwania witryny jest niepoprawny](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>Ochrona kończy się niepowodzeniem, ponieważ agent maszyny Wirtualnej nie odpowiada

Komunikat o błędzie: "Limit czasu wykonania zadania został przesunął się podczas śledzenia operacji rozszerzenia do rozpoczęcia."<br>
Kod błędu: "151099"

Ten błąd może się zdarzyć, jeśli agent gościa platformy Azure na maszynie wirtualnej nie jest w stanie gotowości.

Możesz sprawdzić stan agenta gościa platformy Azure w [witrynie Azure portal](https://portal.azure.com/). Przejdź do maszyny wirtualnej, którą próbujesz chronić, i sprawdź stan w**stanie agenta****właściwości** > **ustawień** >  **maszyny wirtualnej** > . W większości przypadków stan agenta jest gotowy po ponownym uruchomieniu maszyny wirtualnej. Jeśli jednak nie możesz ponownie uruchomić komputera lub nadal występuje problem, wykonaj następujące kroki rozwiązywania problemów:

- [Agent jest zainstalowany na maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


Komunikat o błędzie: "Limit czasu wykonania zadania został przesunął się podczas śledzenia operacji rozszerzenia do rozpoczęcia."<br>
Kod błędu: "151095"

Ten błąd występuje, gdy wersja agenta na komputerze z systemem Linux jest nieaktualna. Wykonaj następujący krok rozwiązywania problemów:

- [Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>Przyczyny i rozwiązania

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agent jest zainstalowany na maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)

#### <a name="solution"></a>Rozwiązanie
Agent maszyny Wirtualnej mógł zostać uszkodzony lub usługa mogła zostać zatrzymana. Ponowna instalacja agenta maszyny Wirtualnej pomaga uzyskać najnowszą wersję. Pomaga również ponownie uruchomić komunikację z usługą.

1. Określ, czy usługa Agenta gościa systemu Windows Azure jest uruchomiona w usługach maszyny Wirtualnej (services.msc). Uruchom ponownie usługę Agent gościa systemu Windows Azure.    
1. Jeśli usługa Agent gościa systemu Windows Azure nie jest widoczna w usługach, otwórz Panel sterowania. Przejdź do **programu i funkcji,** aby sprawdzić, czy jest zainstalowana usługa Agent gościa systemu Windows.
1. Jeśli agent gościa systemu Windows Azure pojawi się w **programie Programy i funkcje,** odinstaluj agenta gościa systemu Windows Azure.
1. Pobierz i zainstaluj [najnowszą wersję agenta MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Aby ukończyć instalację, potrzebne są uprawnienia administratora.
1. Sprawdź, czy usługa Agent gościa systemu Windows Azure jest wyświetlana w usługach.
1. Uruchom ponownie zadanie ochrony.

Ponadto sprawdź, czy [program Microsoft .NET 4.5 jest zainstalowany](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) na maszynie wirtualnej. Aby agent maszyn wirtualnych komunikował się z usługą, musisz mieć program .NET 4.5.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)

#### <a name="solution"></a>Rozwiązanie
Większość błędów związanych z agentem lub rozszerzeniem dla maszyn wirtualnych z systemem Linux są spowodowane przez problemy, które wpływają na przestarzałego agenta maszyny Wirtualnej. Aby rozwiązać ten problem, postępuj zgodnie z poniższymi ogólnymi wskazówkami:

1. Postępuj zgodnie z instrukcjami [aktualizacji agenta maszyny Wirtualnej systemu Linux](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > *Zdecydowanie zaleca się* aktualizowanie agenta tylko za pośrednictwem repozytorium dystrybucji. Nie zaleca się pobierania kodu agenta bezpośrednio z usługi GitHub i aktualizowania go. Jeśli najnowszy agent dystrybucji nie jest dostępny, skontaktuj się z pomocą techniczną dystrybucji, aby uzyskać instrukcje dotyczące instalowania go. Aby sprawdzić, czy najnowszy agent, przejdź do strony [agenta systemu Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) w repozytorium GitHub.

1. Upewnij się, że agent platformy Azure jest uruchomiony na maszynie wirtualnej, uruchamiając następujące polecenie:`ps -e`

   Jeśli proces nie jest uruchomiony, uruchom go ponownie za pomocą następujących poleceń:

   - Dla Ubuntu:`service walinuxagent start`
   - W przypadku innych dystrybucji:`service waagent start`

1. [Skonfiguruj agenta automatycznego ponownego uruchamiania](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
1. Włącz ochronę maszyny wirtualnej.

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Rozszerzenie odzyskiwania witryny nie może zaktualizować ani załadować

Stan rozszerzenia jest wyświetlany jako "Pusty", "NotReady" lub "Transitioning".

#### <a name="solution"></a>Rozwiązanie

Odinstaluj rozszerzenie i uruchom ponownie operację.

Aby odinstalować rozszerzenie:

1. W [witrynie Azure portal](https://portal.azure.com/)przejdź do maszyny Wirtualnej, na którą występuje błąd kopii zapasowej.
1. Wybierz **pozycję Ustawienia**.
1. Wybierz pozycję **Rozszerzenia**.
1. Wybierz **rozszerzenie odzyskiwania witryny**.
1. Wybierz **pozycję Odinstaluj**.

Jeśli rozszerzenie VMSnapshot nie jest wyświetlane w witrynie Azure Portal, [należy zaktualizować agenta systemu Azure Linux.](../virtual-machines/linux/update-agent.md) Następnie uruchom ochronę.

Po wykonaniu tych kroków powoduje, że rozszerzenie ma zostać ponownie zainstalowany podczas ochrony.
