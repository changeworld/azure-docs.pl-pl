---
title: Hybrydowy proces roboczy elementu runbook usługi Azure Automation (Linux)
description: Ten artykuł zawiera informacje na temat instalowania Azure hybrydowego procesu roboczego Runbook automatyzacji, który służy do uruchamiania elementów runbook na komputerach opartych na systemie Linux w lokalnym centrum danych lub w środowisku chmury.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: e95f5d585fa97a62b709e73b6ed6eacafe69a2b3
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>Jak wdrożyć procesu roboczego elementu Runbook dla hybrydowych w systemie Linux

Funkcja hybrydowy proces roboczy elementu Runbook usługi Automatyzacja Azure umożliwia uruchamianie elementów runbook, bezpośrednio na komputerze hostującym rolę i względem zasobów w środowisku, aby zarządzać tymi zasobów lokalnych. Linux hybrydowy proces roboczy jest wykonywany elementów runbook specjalnego użytkownika, które można z podwyższonym poziomem uprawnień do uruchamiania poleceń, które wymagają podniesienia uprawnień. Elementy Runbook są przechowywane i zarządzane w automatyzacji Azure i następnie dostarczany do co najmniej jednym komputerze wyznaczonym. Ten artykuł decribes sposób instalowania hybrydowy proces roboczy elementu Runbook na komputerze z systemem Linux.

## <a name="supported-linux-operating-systems"></a>Obsługiwane systemy operacyjne Linux

Poniżej przedstawiono listę dystrybucje systemu Linux, które są obsługiwane:

* Amazon Linux 2012.09--> 2015.09 (x86/x64)
* CentOS Linux 5, 6 i 7 (x86/x64)
* Oracle Linux 5, 6 i 7 (x86/x64)
* Red Hat Enterprise Linux Server 5,6 i 7 (x86/x64)
* Debian GNU/Linux 6, 7 i 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux wpisywanie Server 11 i 12 (x86/x64)

## <a name="installing-linux-hybrid-runbook-worker"></a>Instalowanie systemu Linux hybrydowego Runbook Worker

Aby zainstalować i skonfigurować hybrydowy proces roboczy elementu Runbook na komputerze z systemem Linux, należy wykonać proces bezpośrednio do przodu, aby ręcznie zainstalować i skonfigurować rolę. Wymaga to włączenia **automatyzacji hybrydowy proces roboczy** rozwiązania w obszarze roboczym analizy dzienników i ponownie uruchomić zestaw poleceń, aby zarejestrować komputer jako proces roboczy, a następnie dodaj go do nowej lub istniejącej grupy.

Poniżej przedstawiono minimalne wymagania dotyczące systemu Linux hybrydowego Runbook Worker:

* Co najmniej dwa rdzenie
* Co najmniej 4 GB pamięci RAM
* Port 443 (dla ruchu wychodzącego)

### <a name="package-requirements"></a>Wymagania pakietu

| **Wymagany pakiet** | **Opis** | **Minimalna wersja**|
|--------------------- | --------------------- | -------------------|
|Glibc |Biblioteka C GNU| 2.5-12 |
|Biblioteki Openssl| Biblioteki OpenSSL | 0.9.8e lub 1.0|
|Narzędzie curl | cURL klienta sieci web | 7.15.5|
|Ctypes języka Python | |
|PAM | Podłączane moduły uwierzytelniania|

Przed kontynuowaniem należy pamiętać obszaru roboczego analizy dzienników, z którą połączony jest Twoje konto usługi Automatyzacja, a także klucz podstawowy dla konta automatyzacji. Oba z portalu można znaleźć konta automatyzacji, a zaznaczając **obszaru roboczego** dla identyfikator obszaru roboczego i wybierając **klucze** klucza podstawowego. Aby uzyskać informacje na porty i adresy, które są wymagane przez hybrydowy proces roboczy elementu Runbook, zobacz [Konfigurowanie sieci](automation-hybrid-runbook-worker.md#network-planning).

1. Włącz rozwiązanie "Automatyzacji hybrydowy proces roboczy" na platformie Azure. Można to zrobić przez:

   1. Dodaj **automatyzacji hybrydowy proces roboczy** rozwiązania do subskrypcji przy użyciu procedury na [rozwiązań do zarządzania dodać analizy dzienników do swojego obszaru roboczego](../log-analytics/log-analytics-add-solutions.md).
   1. Uruchom następujące polecenie cmdlet:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Zainstaluj agenta pakietu OMS dla systemu Linux, uruchamiając następujące polecenie, zastępując \<WorkspaceID\> i \<WorkspaceKey\> z odpowiednimi wartościami z obszaru roboczego.

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Uruchom następujące polecenie, zmiana wartości parametrów *-w*, *-k*, *-g*, i *-e*. Dla *-g* parametr i Zamień wartość nazwę grupy hybrydowego procesu roboczego elementu Runbook, w której powinien dołączyć nowy Linux hybrydowy proces roboczy. Jeśli nazwa nie istnieje już na Twoim koncie automatyzacji, nowej grupy hybrydowego procesu roboczego elementu Runbook jest nawiązywane o tej nazwie.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Po zakończeniu polecenia strony hybrydowego procesu roboczego grupy w portalu Azure opisano nową grupę i liczby elementów członkowskich lub jeśli istniejącą grupę, do liczby elementów członkowskich jest zwiększany. Można zaznaczyć grupę z listy na **hybrydowego procesu roboczego grupy** i wybrać opcję **hybrydowych procesów roboczych** kafelka. Na **hybrydowych procesów roboczych** strony, zobacz każdego członka grupy na liście.

## <a name="turning-off-signature-validation"></a>Wyłączenie sprawdzania poprawności podpisu

Domyślnie Linux hybrydowymi elementami roboczymi Runbook wymaga weryfikacji podpisu. Jeśli pracownik wykonywane bez znaku elementu runbook, zostanie wyświetlony błąd zawierający "Walidacja podpisu nie powiodła się". Aby wyłączyć sprawdzanie poprawności podpisu, uruchom następujące polecenie, zastępując drugi parametr swój identyfikator obszaru roboczego analizy dzienników:

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Typy obsługiwane elementu runbook

Linux hybrydowymi elementami roboczymi Runbook nie obsługują pełny zestaw typy elementu runbook, które zostały znalezione w automatyzacji Azure.

Następujące typy elementów runbook działają na hybrydowy proces roboczy Linux:

* Python 2
* PowerShell

Następujące typy elementu runbook nie działają na hybrydowy proces roboczy Linux:

* Przepływ pracy programu PowerShell
* Graficzna
* Graficzny przepływ pracy programu PowerShell

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Linux hybrydowy proces roboczy jest zależna od agenta pakietu OMS dla systemu Linux do komunikowania się z Twoim kontem automatyzacji Zarejestruj pracownika, otrzymywanie zadania elementów runbook i zgłoszenia stanu. W przypadku niepowodzenia rejestracji pracownika poniżej przedstawiono niektóre możliwe przyczyny błędu:

### <a name="the-oms-agent-for-linux-is-not-running"></a>Agent pakietu OMS dla systemu Linux nie jest uruchomiona.

Jeśli nie jest uruchomiony Agent pakietu OMS dla systemu Linux, zapobiega to Linux hybrydowy proces roboczy komunikację z usługi Automatyzacja Azure. Sprawdź, agent nie działa, wprowadzając następujące polecenie: `ps -ef | grep python`. Powinny pojawić się dane wyjściowe podobne do następujących procesów python z **nxautomation** konta użytkownika. Jeśli rozwiązania zarządzania aktualizacjami lub automatyzacji Azure nie są włączone, będzie uruchomiony żaden z poniższych procedur.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Na poniższej liście przedstawiono procesy, które są uruchamiane dla procesu roboczego elementu Runbook dla hybrydowych w systemie Linux. Znajdują się one w `/var/opt/microsoft/omsagent/state/automationworker/` katalogu.

* **OMS.conf** — jest to proces roboczy manager, to jest uruchamiany bezpośrednio z usługi Konfiguracja DSC.

* **Worker.conf** — ten proces jest automatycznie zarejestrowane hybrydowego procesu roboczego, jest uruchomiona przez Menedżera procesu roboczego. Ten proces jest używany przez zarządzanie aktualizacją i działa w sposób niewidoczny dla użytkownika. Ten proces nie jest obecny, jeśli rozwiązanie do zarządzania aktualizacji nie jest włączona na tym komputerze.

* **diy/Worker.conf** — ten proces jest DIY hybrydowego procesu roboczego. DIY hybrydowy proces roboczy jest używany do wykonywania elementów runbook użytkownika na hybrydowy proces roboczy elementu Runbook. Tylko różni się od automatycznie zarejestrowane hybrydowego procesu roboczego szczegółowo klucza, która jest używana z innej konfiguracji. Ten proces jest nie można obecnie czy automatyzacji Azure rozwiązania nie jest włączona i DIY hybrydowy proces roboczy systemu Linux nie jest zarejestrowany.

Jeśli Agent pakietu OMS dla systemu Linux nie jest uruchomiona, uruchom następujące polecenie, aby uruchomić usługę: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="the-specified-class-does-not-exist"></a>Określona klasa nie istnieje.

Jeśli zostanie wyświetlony błąd **określonej klasy nie istnieje.** w `/var/opt/microsoft/omsconfig/omsconfig.log` , a następnie Agent pakietu OMS dla systemu Linux, musi zostać zaktualizowany. Uruchom następujące polecenie, aby ponownie zainstalować agenta pakietu OMS:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

Dodatkowe kroki dotyczące rozwiązywania problemów z zarządzania aktualizacjami, zobacz [zarządzania aktualizacjami — Rozwiązywanie problemów](automation-update-management.md#troubleshooting)

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [uruchamiania elementów runbook na hybrydowy proces roboczy elementu Runbook](automation-hrw-run-runbooks.md) Aby dowiedzieć się, jak skonfigurować elementy runbook do automatyzacji procesów w lokalnym centrum danych lub w innym środowisku chmury.
* Aby uzyskać instrukcje dotyczące sposobu usuwania hybrydowych procesów roboczych elementu Runbook, zobacz [Usuń Azure Automation hybrydowe procesy robocze elementu Runbook](automation-hybrid-runbook-worker.md#removing-hybrid-runbook-worker)
