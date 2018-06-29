---
title: Hybrydowy proces roboczy elementu runbook usługi Azure Automation (Linux)
description: Ten artykuł zawiera informacje na temat instalowania procesu roboczego elementu Runbook hybrydowego automatyzacji Azure tak elementów runbook można uruchomić na komputerach opartych na systemie Linux w lokalnym centrum danych lub w środowisku chmury.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 06/28/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0c677b88228097efcaa30399160dfdafa1c01788
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096346"
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
|Biblioteki Openssl| Biblioteki OpenSSL | 1.0 (protokołu TLS 1.1 i TLS 1.2 są obsługiwane|
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

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Aby dowiedzieć się, jak rozwiązywać problemy z hybrydowych procesów roboczych elementu Runbook, zobacz [Rozwiązywanie problemów z systemem Linux hybrydowe procesy robocze elementu Runbook](troubleshoot/hybrid-runbook-worker.md#linux)

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się, jak skonfigurować elementy runbook do automatyzacji procesów w lokalnym centrum danych lub w innym środowisku chmury, zobacz [uruchamiania elementów runbook na hybrydowy proces roboczy elementu Runbook](automation-hrw-run-runbooks.md).
* Aby uzyskać instrukcje dotyczące sposobu usuwania hybrydowych procesów roboczych elementu Runbook, zobacz [Usuń Azure Automation hybrydowe procesy robocze elementu Runbook](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
