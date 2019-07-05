---
title: Zrozumienie wyniki sprawdzania agenta Windows w usłudze Azure Update Management
description: Dowiedz się, jak rozwiązywać problemy z agentem rozwiązania Update Management.
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 3555ec74b7e7c8a0f7606f24f8c6f2c4fe36b52d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477097"
---
# <a name="understand-the-windows-agent-check-results-in-update-management"></a>Zrozumienie wyniki wyboru agenta Windows do zarządzania aktualizacjami

Może istnieć wiele przyczyn, na tym komputerze nie jest wyświetlany **gotowe** w zarządzania aktualizacjami. W zarządzania aktualizacjami można sprawdzić kondycję agenta hybrydowy proces roboczy, aby określić pierwotny problem. W tym artykule omówiono sposób uruchamiania narzędzia do rozwiązywania problemów dla maszyn platformy Azure z witryny Azure portal i maszyny spoza platformy Azure w [scenariusza w trybie offline](#troubleshoot-offline).

Poniżej przedstawiono stanów gotowości trzy, maszyna może być w:

* **Gotowe** — usługi Windows update agent jest wdrażany i ostatnio zaobserwowano mniej niż 1 godzinę temu.
* **Odłączony** — usługi Windows update agent jest wdrażany i ostatnio zaobserwowano ponad 1 godzinę temu.
* **Nieskonfigurowane** — usługi Windows update agent nie zostanie odnaleziony lub nie zakończono dołączania.

> [!NOTE]
> Może to być niewielkie opóźnienie między pokazuje witryny Azure portal i bieżący stan maszyny.

## <a name="start-the-troubleshooter"></a>Uruchom narzędzie do rozwiązywania problemów

Dla maszyn, klikając **Rozwiązywanie problemów** łącze w obszarze **Aktualizuj gotowość agenta** kolumny w portalu zostanie uruchomiona **Rozwiązywanie problemów z usługi Windows Update Agent** strony. W przypadku komputerów spoza platformy Azure linku oferuje do tego artykułu. Zobacz [instrukcje w trybie offline](#troubleshoot-offline) rozwiązywać maszyny spoza platformy Azure.

![Aktualizowanie listy maszyn wirtualnych zarządzania](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Aby sprawdzić kondycję agenta, musi być uruchomiona maszyna wirtualna. Jeśli maszyna wirtualna nie jest uruchomiona, **Uruchom maszynę Wirtualną** pojawi się przycisk.

Na **Rozwiązywanie problemów z usługi Windows Update Agent** wybierz opcję **Uruchom testy** można uruchomić narzędzie do rozwiązywania problemów. Narzędzie do rozwiązywania problemów używa [Uruchom polecenie](../../virtual-machines/windows/run-command.md) do uruchamiania skryptu na komputerze, aby zweryfikować agenta zależności. Po zakończeniu rozwiązywania problemów zwraca wynik kontroli.

![Rozwiązywanie problemów z usługi Windows Update Agent strony](../media/update-agent-issues/troubleshoot-page.png)

Wyniki są wyświetlane na stronie, gdy będą gotowe. Sekcje kontroli Pokaż zawartość każdego wyboru.

![Rozwiązywanie problemów z usługi Windows Update Agent kontroli](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Sprawdzanie wymagań wstępnych

### <a name="operating-system"></a>System operacyjny

Sprawdzanie systemu operacyjnego sprawdza, czy hybrydowy proces roboczy elementu Runbook jest uruchomiony jeden z następujących systemów operacyjnych:

|System operacyjny  |Uwagi  |
|---------|---------|
|Windows Server 2008 R2 RTM, Windows Server 2008 | Obsługuje tylko zaktualizować oceny.         |
|Windows Server 2008 R2 z dodatkiem SP1 lub nowszy |.NET framework 4.5.1 lub nowszy jest wymagany. ([Pobrać program .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 4.0 lub nowszy jest wymagany. ([Pobierz Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Programu Windows PowerShell 5.1 jest zalecane w celu zwiększenia niezawodności.  ([Pobierz Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-451"></a>.NET 4.5.1

Sprawdzanie środowiska .NET Framework sprawdza, czy system ma co najmniej [programu .NET Framework 4.5.1](https://www.microsoft.com/download/details.aspx?id=30653) zainstalowane.

### <a name="wmf-51"></a>WMF 5.1

Sprawdzanie WMF sprawdza, czy system ma wymaganą wersję narzędzia Windows Management Framework (WMF). [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) najwcześniejsze obsługiwaną wersją. Zaleca się zainstalowanie [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616) zwiększyć niezawodność hybrydowy proces roboczy elementu Runbook.

### <a name="tls-12"></a>TLS 1.2

To sprawdzenie Określa, czy używasz protokołu TLS 1.2, do szyfrowania komunikacji. Protokół TLS 1.0 nie jest już obsługiwana przez platformę. Zaleca się, że klienci używają protokołu TLS 1.2, do komunikowania się z rozwiązania Update Management.

## <a name="connectivity-checks"></a>Sprawdzanie łączności

### <a name="registration-endpoint"></a>Punkt końcowy rejestracji

To sprawdzenie Określa, czy agent prawidłowo może komunikować się z usługą agenta.

Konfiguracji serwera proxy i zapory muszą zezwalać na agenta hybrydowego procesu roboczego Runbook, aby komunikował się z punktem końcowym rejestracji. Aby uzyskać listę adresów i portów, otwieranych zobacz [sieci, planowanie hybrydowych procesów roboczych](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Punkt końcowy operacji

To sprawdzenie Określa, czy agent prawidłowo może komunikować się z usługą danych czasu wykonywania zadania.

Konfiguracji serwera proxy i zapory muszą zezwalać na agenta hybrydowego procesu roboczego Runbook do komunikacji z usługą danych czasu wykonywania zadania. Aby uzyskać listę adresów i portów, otwieranych zobacz [sieci, planowanie hybrydowych procesów roboczych](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Kontrole kondycji usługi maszyny Wirtualnej

### <a name="monitoring-agent-service-status"></a>Stan usługi agenta monitorowania

To sprawdzenie Określa, czy `HealthService`, programie Microsoft Monitoring Agent jest uruchomiony na komputerze.

Aby dowiedzieć się więcej na temat rozwiązywania problemów z usługi, zobacz [nie jest uruchomiony program Microsoft Monitoring Agent](hybrid-runbook-worker.md#mma-not-running).

Aby ponownie zainstalować program Microsoft Monitoring Agent, zobacz [Instalowanie i konfigurowanie programu Microsoft Monitoring Agent](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Zdarzenia usługi agenta monitorowania

Określa to sprawdzenie, czy którekolwiek `4502` zdarzenia są wyświetlane w Dzienniku programu Operations Manager dla platformy Azure na maszynie w ciągu ostatnich 24 godzin.

Aby dowiedzieć się więcej na temat tego zdarzenia, zobacz [przewodnik rozwiązywania problemów z](hybrid-runbook-worker.md#event-4502) dla tego zdarzenia.

## <a name="access-permissions-checks"></a>Sprawdzanie uprawnień dostępu

### <a name="machinekeys-folder-access"></a>Dostęp do folderu MachineKeys

Kontroli dostępu usług kryptograficznych folderu określa, czy konto systemu lokalnego ma dostęp do C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a>Rozwiązywanie problemów w trybie offline

Narzędzie do rozwiązywania problemów można użyć na hybrydowego procesu roboczego elementu Runbook w trybie offline, uruchamiając skrypt lokalnie. Możesz uzyskać skrypt, [WindowsUpdateAgentRegistration rozwiązywanie](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration), w galerii programu PowerShell. Dane wyjściowe tego skryptu wygląda następująco:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.1.7601 (Windows Server 2008 R2 SP1) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             : 
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Microsoft Monitoring Agent, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Microsoft Monitoring Agent, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>Kolejne kroki

Aby rozwiązać więcej problemów za pomocą hybrydowych procesów roboczych Runbook, zobacz [Rozwiązywanie problemów z hybrydowych procesów roboczych Runbook](hybrid-runbook-worker.md).

