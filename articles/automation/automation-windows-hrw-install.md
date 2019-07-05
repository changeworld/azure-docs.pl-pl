---
title: Hybrydowy proces roboczy elementu runbook usługi Azure Automation (Windows)
description: Ten artykuł zawiera informacje na temat instalowania usługi Azure Automation hybrydowego procesu roboczego Runbook używanego do uruchamiania elementów runbook na komputerach z systemem Windows w środowisku chmury lub lokalnym centrum danych.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 05/21/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a8f6d46b8db6761204e39f14bbb51a493445ad26
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477912"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Wdrażanie Windows hybrydowego procesu roboczego Runbook

Funkcja hybrydowego procesu roboczego Runbook usługi Azure Automation umożliwia uruchamianie elementów runbook bezpośrednio na komputerze, który jest hostem roli i w odniesieniu do zasobów w środowisku w celu zarządzania tymi zasobami lokalnymi. Elementy Runbook są przechowywane i zarządzane w usłudze Azure Automation i następnie dostarczane do co najmniej jeden komputer wyznaczonym. W tym artykule opisano sposób instalowania hybrydowy proces roboczy elementu Runbook na komputerze Windows.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Instalowanie Windows hybrydowego procesu roboczego Runbook

Aby zainstalować i skonfigurować Windows hybrydowego procesu roboczego Runbook, można użyć dwóch metod. Zalecaną metodą jest całkowicie zautomatyzować proces konfigurowania komputerów Windows przy użyciu elementu runbook usługi Automation. Druga metoda jest następujące procedury krok po kroku, aby ręcznie zainstalować i skonfigurować rolę.

> [!NOTE]
> Aby zarządzać konfiguracji serwerów, które obsługują roli hybrydowego procesu roboczego Runbook za pomocą Desired State Configuration (DSC), należy dodać je jako węzłów DSC.

Dostępne są następujące minimalne wymagania dotyczące Windows hybrydowego procesu roboczego Runbook:

* System Windows Server 2012 lub nowszy.
* Program Windows PowerShell w wersji 5.1 lub nowszej ([Pobierz program WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)).
* .NET framework 4.6.2 lub nowszy.
* Dwa rdzenie.
* 4 GB pamięci RAM.
* Port 443 (ruch wychodzący).

Aby uzyskać więcej wymagania sieciowe związane z hybrydowego procesu roboczego elementu Runbook, zobacz [konfigurowania sieci](automation-hybrid-runbook-worker.md#network-planning).

Aby uzyskać więcej informacji na temat dołączania serwery do zarządzania za pomocą DSC, zobacz [dołączanie maszyn w celu zarządzania przez usługi Azure Automation DSC](automation-dsc-onboarding.md).
Po włączeniu [rozwiązanie do zarządzania aktualizacjami](../operations-management-suite/oms-solution-update-management.md), dowolnego komputera Windows, która jest połączona z obszarem roboczym usługi Azure Log Analytics zostanie automatycznie skonfigurowany jako hybrydowy proces roboczy elementu Runbook w celu obsługi elementów runbook zawartych w tym rozwiązaniu. Jednak nie jest zarejestrowana przy użyciu żadnych grupach hybrydowych procesów roboczych, które już zdefiniowane na koncie usługi Automation. 

Komputer można dodać do grupy hybrydowego procesu roboczego Runbook na Twoim koncie usługi Automation w celu obsługi elementów runbook usługi Automation, tak długo, jak długo używasz tego samego konta zarówno dla rozwiązania i hybrydowego procesu roboczego Runbook członkostwa w grupie. Ta funkcjonalność została dodana do wersji 7.2.12024.0 hybrydowego procesu roboczego elementu Runbook.

Po pomyślnym wdrożeniu procesu roboczego elementu runbook, przejrzyj [uruchamianie elementów runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md) informacje na temat konfigurowania elementów runbook do automatyzacji procesów w lokalnym centrum danych lub w innym środowisku chmury.

### <a name="automated-deployment"></a>Zautomatyzowane wdrażanie

Wykonaj poniższe kroki, aby zautomatyzować instalację i konfigurację roli Windows hybrydowego procesu roboczego:

1. Pobierz skrypt New OnPremiseHybridWorker.ps1 z [galerii programu PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) bezpośrednio z komputera z uruchomionym roli hybrydowego procesu roboczego Runbook lub z innego komputera w danym środowisku. Skopiuj skrypt do pracownika.

   Skrypt New OnPremiseHybridWorker.ps1 wymaga następujących parametrów podczas wykonywania:

   * *AutomationAccountName* (obowiązkowe): Nazwa konta usługi Automation.
   * *AAResourceGroupName* (obowiązkowe): Nazwa grupy zasobów, która jest skojarzona z kontem usługi Automation.
   * *OMSResourceGroupName* (opcjonalnie): Nazwa grupy zasobów dla obszaru roboczego usługi Log Analytics. Jeśli ta grupa zasobów nie zostanie określony, *AAResourceGroupName* jest używany.
   * *HybridGroupName* (obowiązkowe): Nazwa grupy hybrydowego procesu roboczego Runbook, którą można określić jako miejsce docelowe dla elementów runbook, który obsługuje ten scenariusz.
   * *SubscriptionID* (obowiązkowe): Identyfikator subskrypcji platformy Azure, która znajduje się konto usługi Automation.
   * *WorkspaceName* (opcjonalnie): Nazwa obszaru roboczego usługi Log Analytics. Jeśli nie masz obszaru roboczego usługi Log Analytics, skrypt tworzy i konfiguruje jeden.

   > [!NOTE]
   > W przypadku włączenia rozwiązań tylko w niektórych regionach jest obsługiwane łączenie obszaru roboczego usługi Log Analytics i konta usługi Automation.
   >
   > Aby uzyskać listę par mapowania obsługiwanych, zobacz [mapowania Region dla obszaru roboczego z konta usługi Automation i Log Analytics](how-to/region-mappings.md).

2. Na komputerze, należy otworzyć **programu Windows PowerShell** z **Start** ekranu w trybie administratora.
3. Z powłoki wiersza polecenia programu PowerShell przejdź do folderu, który zawiera skrypt, który został pobrany. Zmienianie wartości parametrów *- AutomationAccountName*, *- AAResourceGroupName*, *- OMSResourceGroupName*, *- HybridGroupName*, *- SubscriptionId*, i *Nazwa_obszaru_roboczego -* . Następnie uruchom skrypt.

     > [!NOTE]
     > Zostanie wyświetlony monit uwierzytelnienia za pomocą platformy Azure po uruchomieniu skryptu. Możesz *musi* Zaloguj się przy użyciu konta będącego członkiem roli Administratorzy subskrypcji i współadministratorem subskrypcji.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

4. Zostanie wyświetlony monit o zgodę na instalowania Menedżera NuGet, a następnie zostanie wyświetlony monit o uwierzytelnianie przy użyciu swoich poświadczeń platformy Azure.

5. Po zakończeniu działania skryptu **grupy hybrydowych procesów roboczych** strona zawiera nową grupę i liczba elementów członkowskich. Jeśli jest istniejącą grupę, liczba elementów członkowskich jest zwiększany. Możesz wybrać grupę, z listy na **grupy hybrydowych procesów roboczych** strony i wybierz **hybrydowych procesów roboczych** kafelka. Na **hybrydowych procesów roboczych** stronie zobaczysz każdy członek grupy na liście.

### <a name="manual-deployment"></a>Ręczne wdrażanie

Wykonaj pierwsze dwa kroki, jeden raz dla własnego środowiska automatyzacji, a następnie powtórz pozostałe kroki dla każdego komputera, procesu roboczego.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

#### <a name="1-create-a-log-analytics-workspace"></a>1. Utwórz obszar roboczy usługi Log Analytics

Jeśli nie masz jeszcze obszaru roboczego usługi Log Analytics, utwórz ją przy użyciu instrukcji w [Zarządzanie obszarem roboczym](../azure-monitor/platform/manage-access.md). Jeśli już masz, możesz użyć istniejącego obszaru roboczego.

#### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Dodaj rozwiązanie do automatyzacji do obszaru roboczego usługi Log Analytics

Monitorowanie usługi Azure Automation rozwiązania dzienniki dodaje funkcje usługi Azure Automation, takie jak obsługa hybrydowego procesu roboczego Runbook. Po dodaniu rozwiązania do obszaru roboczego, automatycznie wypycha składniki proces roboczy na komputerze agenta, który zostanie zainstalowany w następnym kroku.

Aby dodać **automatyzacji** usługi Azure Monitor dzienniki rozwiązania do obszaru roboczego, uruchom następujące polecenie programu PowerShell.

```powershell-interactive
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
```

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Zainstaluj program Microsoft Monitoring Agent

Program Microsoft Monitoring Agent łączy komputery do dzienników usługi Azure Monitor. Podczas instalowania agenta na komputerze w środowisku lokalnym i połączyć ją z obszarem roboczym, automatycznie pobiera składniki, które są wymagane dla hybrydowego procesu roboczego Runbook.

Aby zainstalować agenta na komputerze lokalnym, postępuj zgodnie z instrukcjami w artykule [Windows łączenie komputerów do dzienników usługi Azure Monitor](../log-analytics/log-analytics-windows-agent.md). Możesz powtórzyć ten proces dla wielu komputerów do dodania wielu procesów roboczych dla danego środowiska.

Gdy agent pomyślnie połączył się z dziennikami usługi Azure Monitor, ta opcja jest wyświetlana na **połączone źródła** kartę usługi log analytics **ustawienia** strony. Możesz sprawdzić, czy agent poprawnie pobrał rozwiązanie do automatyzacji gdy ma ona folder o nazwie **AzureAutomationFiles** w C:\Program Files\Microsoft Monitoring Agent\Agent. Aby sprawdzić wersję hybrydowy proces roboczy elementu Runbook, możesz przejść do C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ i zanotuj \\ *wersji* podfolderu.

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Instalowanie środowiska elementu runbook i nawiązać połączenie z usługi Azure Automation

Po dodaniu agenta usługi Azure Monitor dzienniki rozwiązania Automation umieszcza **HybridRegistration** moduł programu PowerShell, który zawiera **Add-HybridRunbookWorker** polecenia cmdlet. To polecenie cmdlet umożliwia zainstalowanie środowiska elementu runbook na komputerze i zarejestrowanie go za pomocą usługi Azure Automation.

Otwórz sesję programu PowerShell w trybie administratora i uruchom następujące polecenia, aby zaimportować moduł:

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Następnie uruchom **Add-HybridRunbookWorker** polecenia cmdlet przy użyciu następującej składni:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Można uzyskać informacji wymaganych dla tego polecenia cmdlet z **zarządzanie kluczami** strony w witrynie Azure portal. Otwórz tę stronę, wybierając **klucze** opcję **ustawienia** strony na Twoim koncie usługi Automation.

![Strona "Zarządzaj kluczami"](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **Nazwa grupy** to nazwa grupy hybrydowego procesu roboczego Runbook. Jeśli ta grupa już istnieje na koncie usługi Automation, bieżący komputer zostanie dodany do niego. Jeśli ta grupa nie istnieje, zostanie dodany.
* **Punkt końcowy** jest **adresu URL** wpisu na **zarządzanie kluczami** strony.
* **Token** jest **podstawowy klucz dostępu** wpisu na **zarządzanie kluczami** strony.

Aby uzyskać szczegółowe informacje dotyczące instalacji, należy użyć **-Verbose** przełącznik z **Add-HybridRunbookWorker**.

#### <a name="5-install-powershell-modules"></a>5. Zainstaluj moduły programu PowerShell

Elementy Runbook można użyć dowolnego działania i poleceń cmdlet zdefiniowane w modułach, które są zainstalowane w środowisku usługi Azure Automation. Te moduły nie są automatycznie wdrażane na komputerach w środowisku lokalnym, więc należy je zainstalować ręcznie. Wyjątkiem jest moduł platformy Azure, który jest instalowany domyślnie i zapewnia dostęp do poleceń cmdlet dla wszystkich usług platformy Azure i działań dla usługi Azure Automation.

Ponieważ podstawowym celem funkcji hybrydowego procesu roboczego Runbook do zarządzania zasobami lokalnymi, najprawdopodobniej musisz zainstalować moduły, które obsługują te zasoby. Aby uzyskać informacje na temat instalowania modułów programu Windows PowerShell, zobacz [instalowanie modułów](/powershell/developer/windows-powershell). 

Moduły, które są zainstalowane musi znajdować się w lokalizacji, odwołuje się **PSModulePath** zmiennej środowiskowej, aby można je automatycznie zaimportować do hybrydowego procesu roboczego. Aby uzyskać więcej informacji, zobacz [Modyfikowanie ścieżki instalacji PSModulePath](/powershell/developer/windows-powershell).

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się, jak skonfigurować elementy runbook do automatyzacji procesów w lokalnym centrum danych lub w innym środowisku chmury, zobacz [uruchamianie elementów runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md).
* Aby uzyskać instrukcje dotyczące sposobu usuwania hybrydowych procesów roboczych Runbook, zobacz [Usuń usługi Azure Automation hybrydowych procesów roboczych Runbook](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Aby dowiedzieć się, jak rozwiązywać problemy z hybrydowych procesów roboczych Runbook, zobacz [Rozwiązywanie problemów z Windows hybrydowych procesów roboczych Runbook](troubleshoot/hybrid-runbook-worker.md#windows)
* Aby uzyskać dodatkowe instrukcje dotyczące sposobu rozwiązywania problemów z zarządzaniem aktualizacjami, zobacz [rozwiązania Update Management: Rozwiązywanie problemów z](troubleshoot/update-management.md).
