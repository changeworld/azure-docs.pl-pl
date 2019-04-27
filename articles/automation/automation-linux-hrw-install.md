---
title: Hybrydowy proces roboczy elementu runbook usługi Azure Automation (Linux)
description: Ten artykuł zawiera informacje na temat instalowania usługi Azure Automation hybrydowego procesu roboczego Runbook, aby można było uruchomić elementy runbook na komputerach opartych na systemie Linux w środowisku chmury lub lokalnym centrum danych.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 06/28/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cc07aa9c1b2c540c33949a8c591bd98f91b04666
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60738863"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Wdrażanie procesu roboczego elementu Runbook dla hybrydowych w systemie Linux

Funkcja hybrydowego procesu roboczego Runbook usługi Azure Automation umożliwia uruchamianie elementów runbook bezpośrednio na komputerze, który jest hostem roli i w odniesieniu do zasobów w środowisku w celu zarządzania tymi zasobami lokalnymi. Linux hybrydowego procesu roboczego Runbook jest wykonywany elementów runbook specjalnego użytkownika, który zostanie podniesiony do uruchamiania poleceń, które wymagają podniesienia uprawnień. Elementy Runbook są przechowywane i zarządzane w usłudze Azure Automation i następnie dostarczane do co najmniej jeden komputer wyznaczonym.

W tym artykule opisano sposób instalowania hybrydowy proces roboczy elementu Runbook na maszynie z systemem Linux.

## <a name="supported-linux-operating-systems"></a>Obsługiwane systemy operacyjne Linux

Funkcja hybrydowego procesu roboczego Runbook obsługuje poniższe dystrybucje:

* Linux Amazon 2012.09 do 2015.09 — x86/x64 64
* CentOS Linux 5, 6 i 7 — x86/x64 64
* Oracle Linux 5, 6 i 7 — x86/x64 64
* Red Hat Enterprise Linux Server 5, 6 i 7 — x86/x64 64
* Debian GNU/Linux 6, 7 i 8 — x86/x64 64
* Ubuntu 12.04 LTS, 14.04 LTS i 16.04 LTS — x86/x64 64
* SUSE Linux Enterprise Server 11 i 12 — x86/x64 64

## <a name="installing-a-linux-hybrid-runbook-worker"></a>Instalowanie systemu Linux hybrydowego procesu roboczego Runbook

Aby zainstalować i skonfigurować hybrydowy proces roboczy elementu Runbook na komputerze z systemem Linux, należy wykonać prosty proces ręcznego instalowania i konfigurowania roli. Wymaga, włączanie **automatyzacji hybrydowy proces roboczy** rozwiązania w obszarze roboczym usługi Azure Log Analytics, a następnie uruchamiając zestaw poleceń, aby zarejestrować komputer jako procesu roboczego i dodać go do grupy.

Dostępne są następujące minimalne wymagania dotyczące procesu roboczego elementu Runbook dla hybrydowych w systemie Linux:

* Dwa rdzenie
* 4 GB pamięci RAM
* Port wyjściowy 443)

### <a name="package-requirements"></a>Wymagania dotyczące pakietu

| **Wymagany pakiet** | **Opis** | **Minimalna wersja**|
|--------------------- | --------------------- | -------------------|
|Glibc |Biblioteka C GNU| 2.5-12 |
|Openssl| Biblioteki OpenSSL | 1.0 (protokół TLS 1.1 i TLS 1.2 są obsługiwane|
|Curl | Klient sieci web programu cURL | 7.15.5|
|Ctypes języka Python | |
|PAM | Podłączane moduły uwierzytelniania|
| **Opcjonalny pakiet** | **Opis** | **Minimalna wersja**|
| Program PowerShell Core | Aby uruchomić elementy runbook programu PowerShell, zobacz PowerShell musi zostać zainstalowany, [Instalowanie programu PowerShell Core w systemie Linux](/powershell/scripting/setup/installing-powershell-core-on-linux) dowiesz się, jak go zainstalować.  | 6.0.0 |

### <a name="installation"></a>Instalacja

Przed kontynuowaniem należy pamiętać, Twoje konto usługi Automation jest połączone z obszaru roboczego usługi Log Analytics. Należy również zauważyć klucza podstawowego dla konta usługi Automation. Możesz znaleźć, zarówno z poziomu witryny Azure portal, wybierając automatyzacji konta, wybierając opcję **obszaru roboczego** dla Identyfikatora obszaru roboczego i wybierając polecenie **klucze** dla klucza podstawowego. Aby uzyskać informacje na temat portów i adresów, które muszą uzyskać hybrydowy proces roboczy elementu Runbook, zobacz [konfigurowania sieci](automation-hybrid-runbook-worker.md#network-planning).

1. Włącz **automatyzacji hybrydowy proces roboczy** rozwiązanie na platformie Azure przy użyciu jednej z następujących metod:

   * Dodaj **automatyzacji hybrydowy proces roboczy** rozwiązań do subskrypcji, wykonując procedurę podaną w [Dodawanie usługi Azure Monitor dzienników rozwiązań do obszaru roboczego](../log-analytics/log-analytics-add-solutions.md).
   * Uruchom następujące polecenie cmdlet:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Zainstaluj agenta usługi Log Analytics dla systemu Linux, uruchamiając następujące polecenie. Zastąp \<WorkspaceID\> i \<klucz WorkspaceKey\> odpowiednimi wartościami z obszaru roboczego.

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)] 

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Uruchom następujące polecenie, zmieniając wartości parametrów *-w*, *-k*, *-g*, i *-e*. Aby uzyskać *-g* parametru, zastąp wartość nazwą grupy hybrydowego procesu roboczego Runbook, do której powinien dołączyć nowy Linux hybrydowego procesu roboczego elementu Runbook. Jeśli nazwa nie istnieje na koncie usługi Automation, nowej grupy hybrydowego procesu roboczego Runbook składa się z tą nazwą.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Po wykonaniu polecenia **grupy hybrydowych procesów roboczych** strona w witrynie Azure portal zawiera nową grupę i liczba elementów członkowskich. Jeśli jest to istniejącą grupę, liczba elementów członkowskich jest zwiększany. Możesz wybrać grupę, z listy na **grupy hybrydowych procesów roboczych** strony i wybierz **hybrydowych procesów roboczych** kafelka. Na **hybrydowych procesów roboczych** stronie zobaczysz każdy członek grupy na liście.

> [!NOTE]
> Jeśli używane są rozszerzenia maszyny wirtualnej usługi Azure Monitor dla systemu Linux na Maszynie wirtualnej platformy Azure, firma Microsoft zaleca ustawienie `autoUpgradeMinorVersion` o wartości false jako automatyczne uaktualnianie wersji może spowodować problemy z hybrydowego procesu roboczego elementu Runbook. Aby dowiedzieć się, jak ręcznie uaktualnić rozszerzenia, zobacz [wdrożenia wiersza polecenia platformy Azure ](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turning-off-signature-validation"></a>Wyłączenie sprawdzania poprawności podpisu

Domyślnie Linux hybrydowych procesów roboczych Runbook wymagają weryfikacji podpisu. Jeśli uruchamiasz procesu roboczego elementu runbook bez znaku, zostanie wyświetlony komunikat o błędzie informujący o tym, "Weryfikacja podpisu nie powiodło się." Aby wyłączyć sprawdzanie poprawności podpisu, uruchom następujące polecenie. Zamień drugi parametr identyfikatora obszaru roboczego analizy dzienników

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Typy obsługiwane elementów runbook

Linux hybrydowych procesów roboczych Runbook nie obsługuje ona pełnego zestawu typów elementów runbook w usłudze Azure Automation.

Następujące typy elementów runbook działają w systemie Linux hybrydowego procesu roboczego:

* Python 2
* PowerShell

  > [!NOTE]
  > Elementy runbook programu PowerShell wymagają programu PowerShell Core można zainstalować na komputerze systemu Linux. Zobacz [Instalowanie programu PowerShell Core w systemie Linux](/powershell/scripting/setup/installing-powershell-core-on-linux) dowiesz się, jak go zainstalować.

Następujące typy elementów runbook nie działa w systemie Linux hybrydowego procesu roboczego:

* Przepływ pracy programu PowerShell
* Graficzne
* Graficzny przepływ pracy programu PowerShell

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się, jak skonfigurować elementy runbook do automatyzacji procesów w lokalnym centrum danych lub w innym środowisku chmury, zobacz [uruchamianie elementów runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md).
* Aby uzyskać instrukcje dotyczące sposobu usuwania hybrydowych procesów roboczych Runbook, zobacz [Usuń usługi Azure Automation hybrydowych procesów roboczych Runbook](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Aby dowiedzieć się, jak rozwiązywać problemy z hybrydowych procesów roboczych Runbook, zobacz [Rozwiązywanie problemów z systemem Linux hybrydowych procesów roboczych Runbook](troubleshoot/hybrid-runbook-worker.md#linux)