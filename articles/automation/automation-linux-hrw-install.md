---
title: Hybrydowy proces roboczy elementu runbook usługi Azure Automation (Linux)
description: Ten artykuł zawiera informacje na temat instalowania procesu roboczego elementu Runbook hybrydowego automatyzacji Azure tak elementów runbook można uruchomić na komputerach opartych na systemie Linux w lokalnym centrum danych lub w środowisku chmury.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8507cf99ea22b24aa3026565cb7c4139e4c3742d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268122"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Wdrażanie procesu roboczego elementu Runbook dla hybrydowych w systemie Linux

Funkcja hybrydowy proces roboczy elementu Runbook usługi Automatyzacja Azure służy do uruchamiania elementów runbook bezpośrednio na komputerze, który jest hostem roli i względem zasobów w środowisku, aby zarządzać tymi zasobów lokalnych. Linux hybrydowy proces roboczy jest wykonywany elementów runbook specjalnego użytkownika, który można z podwyższonym poziomem uprawnień do uruchamiania poleceń, które wymagają podniesienia uprawnień. Elementy Runbook są przechowywane i zarządzane w automatyzacji Azure i następnie dostarczany do co najmniej jednym komputerze wyznaczonym.

W tym artykule opisano sposób instalowania hybrydowy proces roboczy elementu Runbook na komputerze z systemem Linux.

## <a name="supported-linux-operating-systems"></a>Obsługiwane systemy operacyjne Linux

Funkcja hybrydowy proces roboczy elementu Runbook obsługuje następujące dystrybucji:

* Linux Amazon 2012.09 do 2015.09 (x86/x64)
* CentOS Linux 5, 6 i 7 (x86/x64)
* Oracle Linux 5, 6 i 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 i 7 (x86/x64)
* Debian GNU/Linux 6, 7 i 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS i 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 i 12 (x86/x64)

## <a name="installing-a-linux-hybrid-runbook-worker"></a>Instalowanie systemu Linux hybrydowego Runbook Worker

Aby zainstalować i skonfigurować hybrydowy proces roboczy elementu Runbook na komputerze z systemem Linux, należy wykonać dość proste, aby ręcznie zainstalować i skonfigurować rolę. Wymaga to włączenia **automatyzacji hybrydowy proces roboczy** rozwiązania w obszarze roboczym usługi Analiza dzienników Azure i ponownie uruchomić zestaw poleceń, aby zarejestrować komputer jako proces roboczy, a następnie dodaj go do grupy.

Dostępne są następujące minimalne wymagania dotyczące systemu Linux hybrydowego Runbook Worker:

* Dwa rdzenie
* 4 GB pamięci RAM
* Port 443 (dla ruchu wychodzącego)

### <a name="package-requirements"></a>Wymagania pakietu

| **Wymagany pakiet** | **Opis** | **Minimalna wersja**|
|--------------------- | --------------------- | -------------------|
|Glibc |Biblioteka C GNU| 2.5-12 |
|Biblioteki Openssl| Biblioteki OpenSSL | 0.9.8e lub 1.0|
|Narzędzie curl | cURL klienta sieci web | 7.15.5|
|Ctypes języka Python | |
|PAM | Podłączane moduły uwierzytelniania|
| **Opcjonalny pakiet** | **Opis** | **Minimalna wersja**|
| Podstawowe programu PowerShell | Do uruchamiania elementów runbook programu PowerShell, zobacz PowerShell musi być zainstalowany, [instalacji podstawowej programu PowerShell w systemie Linux](/powershell/scripting/setup/installing-powershell-core-on-linux) więcej informacji na temat go zainstalować.  | 6.0.0 |

### <a name="installation"></a>Instalacja

Przed kontynuowaniem należy zwrócić uwagę obszaru roboczego analizy dzienników, z którym powiązany jest Twoje konto usługi Automatyzacja. Należy również zauważyć, klucz podstawowy dla konta automatyzacji. Znajduje się zarówno z portalu Azure, wybierając automatyzacji z konta, wybierając **obszaru roboczego** dla identyfikator obszaru roboczego i wybierając **klucze** klucza podstawowego. Aby uzyskać informacje na porty i adresy, które należy uzyskać hybrydowy proces roboczy elementu Runbook, zobacz [Konfigurowanie sieci](automation-hybrid-runbook-worker.md#network-planning).

1. Włącz **automatyzacji hybrydowy proces roboczy** rozwiązania na platformie Azure przy użyciu jednej z następujących metod:

   * Dodaj **automatyzacji hybrydowy proces roboczy** rozwiązania do subskrypcji przy użyciu procedury na [rozwiązań do zarządzania dodać analizy dzienników do swojego obszaru roboczego](../log-analytics/log-analytics-add-solutions.md).
   * Uruchom następujące polecenie cmdlet:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Zainstaluj agenta pakietu OMS dla systemu Linux, uruchamiając następujące polecenie. Zastąp \<WorkspaceID\> i \<WorkspaceKey\> z odpowiednimi wartościami z obszaru roboczego.

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Uruchom następujące polecenie, zmiana wartości dla parametrów *-w*, *-k*, *-g*, i *-e*. Dla *-g* parametr i Zamień wartość nazwę grupy hybrydowego procesu roboczego elementu Runbook, w której powinien dołączyć nowy Linux hybrydowy proces roboczy. Jeśli nazwa nie istnieje na koncie automatyzacji, nowej grupy hybrydowego procesu roboczego elementu Runbook jest nawiązywane przy użyciu tej nazwy.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Po wykonaniu polecenia **hybrydowego procesu roboczego grupy** strony w portalu Azure zawiera nową grupę i liczby elementów członkowskich. Jeśli jest to istniejącą grupę, do liczby elementów członkowskich jest zwiększany. Można zaznaczyć grupę z listy na **hybrydowego procesu roboczego grupy** i wybrać opcję **hybrydowych procesów roboczych** kafelka. Na **hybrydowych procesów roboczych** strony, zobacz każdego członka grupy na liście.

## <a name="turning-off-signature-validation"></a>Wyłączenie sprawdzania poprawności podpisu

Domyślnie Linux hybrydowymi elementami roboczymi Runbook wymaga weryfikacji podpisu. Jeśli pracownik wykonywane bez znaku elementu runbook, zostanie wyświetlony komunikat o błędzie informujący, "Walidacja podpisu nie powiodło się." Aby wyłączyć sprawdzanie poprawności podpisu, uruchom następujące polecenie. Drugi parametr należy zastąpić identyfikatorem obszaru roboczego analizy dzienników

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Typy obsługiwane elementu runbook

Linux hybrydowymi elementami roboczymi Runbook nie obsługują pełny zestaw typy elementu runbook automatyzacji Azure.

Następujące typy elementów runbook działają na hybrydowy proces roboczy Linux:

* Python 2
* PowerShell

  > [!NOTE]
  > Elementy runbook programu PowerShell wymagają Core programu PowerShell do zainstalowania na komputerze z systemem Linux. Zobacz [instalacji podstawowej programu PowerShell w systemie Linux](/powershell/scripting/setup/installing-powershell-core-on-linux) więcej informacji na temat go zainstalować.

Następujące typy elementu runbook nie działają na hybrydowy proces roboczy Linux:

* Przepływ pracy programu PowerShell
* Graficzna
* Graficzny przepływ pracy programu PowerShell

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Linux hybrydowy proces roboczy jest zależna od agenta pakietu OMS dla systemu Linux do komunikowania się z Twoim kontem automatyzacji Zarejestruj pracownika, otrzymywanie zadania elementów runbook i zgłoszenia stanu. W przypadku niepowodzenia rejestracji pracownika poniżej przedstawiono niektóre możliwe przyczyny błędu.

### <a name="the-oms-agent-for-linux-isnt-running"></a>Nie jest uruchomiony Agent pakietu OMS dla systemu Linux

Jeśli nie jest uruchomiony Agent pakietu OMS dla systemu Linux, Linux hybrydowy proces roboczy nie może komunikować się z usługi Automatyzacja Azure. Sprawdź, czy agent jest uruchomiony przez wprowadzenie polecenia `ps -ef | grep python`. 

Powinny pojawić się dane wyjściowe podobne do następujących (Python przetwarza z **nxautomation** konta użytkownika). Jeśli rozwiązania zarządzania aktualizacjami lub usługi Automatyzacja Azure jest wyłączona, żadna z następujących procesów będzie działać.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Następujące procesy są uruchomione dla procesu roboczego elementu Runbook dla hybrydowych w systemie Linux. W przypadku wszystkich się one w `/var/opt/microsoft/omsagent/state/automationworker/` katalogu.

* **OMS.conf**: jest to proces roboczy Menedżera. Uruchomieniu bezpośrednio z żądanego stanu konfiguracji (DSC).

* **Worker.conf**: jest to proces roboczy hybrydowego zarejestrowana automatycznie. Jest ona uruchamiana przez Menedżera procesu roboczego. Ten proces jest używany przez zarządzanie aktualizacją i działa w sposób niewidoczny dla użytkownika. Ten proces występuje tylko wtedy, gdy rozwiązanie zarządzania aktualizacjami jest włączona na tym komputerze.

* **diy/Worker.conf**: jest to DIY hybrydowego procesu roboczego. DIY hybrydowy proces roboczy jest używany do wykonywania elementów runbook użytkownika na hybrydowy proces roboczy elementu Runbook. Go różni się od procesu roboczego hybrydowego zarejestrowana automatycznie tylko, gdyż używa innej konfiguracji. Ten proces jest obecny tylko wtedy, gdy rozwiązanie Automatyzacja Azure jest włączona i DIY hybrydowy proces roboczy Linux jest zarejestrowany.

Jeśli Agent pakietu OMS dla systemu Linux nie jest uruchomiona, uruchom następujące polecenie, aby uruchomić usługę: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="the-specified-class-doesnt-exist"></a>Określona klasa nie istnieje.

Jeśli zostanie wyświetlony błąd "określonej klasy nie istnieje" w `/var/opt/microsoft/omsconfig/omsconfig.log`, Agent pakietu OMS dla systemu Linux, musi zostać zaktualizowany. Uruchom następujące polecenie, aby ponownie zainstalować agenta pakietu OMS:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

Dodatkowe kroki dotyczące rozwiązywania problemów z zarządzania aktualizacjami, zobacz [zarządzania aktualizacjami: Rozwiązywanie problemów z](automation-update-management.md#troubleshooting).

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się, jak skonfigurować elementy runbook do automatyzacji procesów w lokalnym centrum danych lub w innym środowisku chmury, zobacz [uruchamiania elementów runbook na hybrydowy proces roboczy elementu Runbook](automation-hrw-run-runbooks.md).
* Aby uzyskać instrukcje dotyczące sposobu usuwania hybrydowych procesów roboczych elementu Runbook, zobacz [Usuń Azure Automation hybrydowe procesy robocze elementu Runbook](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
