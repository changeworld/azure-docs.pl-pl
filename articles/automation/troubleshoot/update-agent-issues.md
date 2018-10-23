---
title: Zrozumienie wyniki sprawdzania agenta w usłudze Azure Update Management
description: Dowiedz się, jak rozwiązywać problemy z agentem rozwiązania Update Management.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/10/2018
ms.topic: conceptual
ms.service: automation
ms.component: update-management
manager: carmonm
ms.openlocfilehash: 2a70893c3e879b2cecb4d39d538b0df44b1ced15
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49651571"
---
# <a name="understand-the-agent-check-results-in-update-management"></a>Zrozumienie wyniki sprawdzania agenta w zarządzania aktualizacjami

Może istnieć wiele funkcji przemawiających swoje maszyny spoza platformy Azure nie jest widoczny **gotowe** w zarządzania aktualizacjami. W zarządzania aktualizacjami można sprawdzić kondycję agenta hybrydowy proces roboczy, aby określić pierwotny problem. W tym artykule omówiono sposób uruchomić narzędzie do rozwiązywania problemów w witrynie Azure portal i w scenariuszach w trybie offline.

## <a name="start-the-troubleshooter"></a>Uruchom narzędzie do rozwiązywania problemów

Klikając **Rozwiązywanie problemów** łącze w obszarze **Aktualizuj gotowość agenta** kolumny w portalu, możesz uruchomić **Rozwiązywanie problemów z usługi Windows Update Agent** strony. Ta strona pokazuje problemy z agenta i łącze do tego artykułu, aby pomóc w rozwiązywaniu problemów związanych z.

![Strona listy maszyn wirtualnych](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Kontrole wymagają na maszynie Wirtualnej i działać. Jeśli maszyna wirtualna nie działa, zostanie wyświetlony przycisk, aby **Uruchom maszynę Wirtualną**.

Na **Rozwiązywanie problemów z usługi Windows Update Agent** kliknij **uruchomienia sprawdza**, aby uruchomić narzędzie do rozwiązywania problemów. Narzędzie do rozwiązywania problemów używa [Uruchom polecenie](../../virtual-machines/windows/run-command.md) do uruchamiania skryptu na maszynie, aby zweryfikować zależności, które ma agenta. Po zakończeniu rozwiązywania problemów, zwraca wynik kontroli.

![Rozwiązywanie problemów z strony](../media/update-agent-issues/troubleshoot-page.png)

Po zakończeniu wyniki są zwracane w oknie. [Zaznacz sekcje](#pre-requisistes-checks) zawierają informacje na temat każdego wyboru poszukuje programu.

![Usługi Windows Update agent sprawdza, czy strony](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Sprawdzanie wymagań wstępnych

### <a name="operating-system"></a>System operacyjny

Sprawdzanie systemu operacyjnego, sprawdza, czy hybrydowego procesu roboczego Runbook działa jeden z następujących systemów operacyjnych:

|System operacyjny  |Uwagi  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Obsługuje tylko zaktualizować oceny.         |
|Windows Server 2008 R2 z dodatkiem SP1 lub nowszy     |.NET framework 4.5 lub nowszy jest wymagany. ([Pobierz program .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 4.0 lub nowszy jest wymagany. ([Pobierz platformę WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Programu Windows PowerShell 5.1 jest zalecane w celu zwiększenia niezawodności.  ([Pobierz platformę WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) i 7 (x64)      | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji. Oparte na klasyfikacji poprawek wymaga "yum", aby zwrócić dane zabezpieczeń, która CentOS nie ma gotowych.         |
|Red Hat Enterprise 6 (x86/x64) i 7 (x64)     | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.        |
|SUSE Linux Enterprise Server 11 (x86/x64) i 12 (x64)     | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.        |
|Ubuntu 14.04 LTS i 16.04 LTS — x86/x64 64      |Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.         |

### <a name="net-45"></a>.NET 4.5

Sprawdzenie platformy .NET, sprawdza, czy system ma co najmniej [.NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653) obecne.

### <a name="wmf-51"></a>WMF 5.1

Sprawdzanie WMF, sprawdza, czy system ma wymaganą wersję Windows Management Framework. [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) jest najniższa wersja obsługiwana. Zalecane jest, że instalujesz program [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616) celu zwiększenia niezawodności hybrydowy proces roboczy elementu Runbook.

### <a name="tls-12"></a>TLS 1.2

To sprawdzenie decyduje o tym, jeśli używasz protokołu TLS 1.2, do szyfrowania komunikacji. Protokół TLS 1.0 jest już obsługiwany przez platformę, i zaleca się, że klienci używają protokołu TLS 1.2, do komunikowania się z rozwiązania Update Management.

## <a name="connectivity-checks"></a>Sprawdzanie łączności

### <a name="registration-endpoint"></a>Punkt końcowy rejestracji

To sprawdzenie decyduje o tym, jeśli agent prawidłowo może komunikować się z usługą agenta.

Konfiguracji serwera proxy i zapory muszą zezwalać na agenta hybrydowego procesu roboczego Runbook do komunikowania się z punktu końcowego rejestracji. Aby uzyskać listę adresów i portów, otwieranych zobacz [sieci, planowanie hybrydowych procesów roboczych](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Punkt końcowy operacji

To sprawdzenie decyduje o tym, jeśli agent prawidłowo może komunikować się z usługą danych czasu wykonywania zadania.

Konfiguracji serwera proxy i zapory muszą zezwalać na agenta hybrydowego procesu roboczego Runbook do komunikacji z usługą danych czasu wykonywania zadania. Aby uzyskać listę adresów i portów, otwieranych zobacz [sieci, planowanie hybrydowych procesów roboczych](../automation-hybrid-runbook-worker.md#network-planning)

## <a name="vm-service-health-checks"></a>Kontrole kondycji usługi maszyny Wirtualnej

### <a name="monitoring-agent-service-status"></a>Stan usługi agenta monitorowania

Określa to sprawdzenie, czy program Microsoft Monitoring Agent, `HealthService` jest uruchomiona na maszynie.

Aby dowiedzieć się więcej na temat rozwiązywania problemów z usługi, zobacz [nie jest uruchomiony program Microsoft Monitoring Agent](hybrid-runbook-worker.md#mma-not-running).

Aby ponownie zainstalować program Microsoft Monitoring Agent, zobacz [Instalowanie i konfigurowanie programu Microsoft Monitoring Agent](/log-analytics/log-analytics-concept-hybrid.md#install-and-configure-agent)

### <a name="monitoring-agent-service-events"></a>Zdarzenia usługi agenta monitorowania

To sprawdzenie Określa, czy zostały dowolne `4502` dziennik zdarzeń w programie Operations Manager na komputerze, w ciągu ostatnich 24 godzin.

Aby dowiedzieć się więcej na temat tego zdarzenia, zobacz [przewodnik rozwiązywania problemów z](hybrid-runbook-worker.md#event-4502) dla tego zdarzenia.

## <a name="access-permissions-checks"></a>Sprawdzanie uprawnień dostępu

### <a name="machinekeys-folder-access"></a>Dostęp do folderu MachineKeys

Sprawdź dostęp do folderów Crypto Określa, czy konto systemu lokalnego ma dostęp do `C:\ProgramData\Microsoft\Crypto\RSA`

## <a name="troubleshoot-offline"></a>Rozwiązywanie problemów w trybie offline

Narzędzie do rozwiązywania problemów w trybie offline można użyć w hybrydowym procesie roboczym elementu Runbook, uruchamiając skrypt lokalnie. Skrypt, [WindowsUpdateAgentRegistration rozwiązywanie](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration) można znaleźć w galerii programu PowerShell. W poniższym przykładzie przedstawiono przykładowy wynik tego skryptu:

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
RuleName                    : .Net Framework 4.5+
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

Aby rozwiązać problemy dodatkowe problemy za pomocą hybrydowych procesów roboczych Runbook, zobacz [Rozwiązywanie problemów — hybrydowych procesów roboczych Runbook](hybrid-runbook-worker.md)
