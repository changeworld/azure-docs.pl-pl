---
title: Hybrydowy proces roboczy elementu runbook usługi Azure Automation (Windows)
description: Ten artykuł zawiera informacje na temat instalowania Azure Automation hybrydowego procesu roboczego elementu Runbook, którego można użyć do uruchamiania elementów Runbook na komputerach z systemem Windows w lokalnym centrum danych lub w środowisku chmury.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 9f3e06f66996be4a2b43b64e6100c62a2fa41381
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649963"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Wdrażanie hybrydowego procesu roboczego elementu Runbook systemu Windows

Za pomocą funkcji hybrydowego procesu roboczego elementu Runbook programu Azure Automation można uruchamiać elementy Runbook bezpośrednio na komputerze hostującym rolę i w odniesieniu do zasobów w środowisku, aby zarządzać tymi zasobami lokalnymi. Elementy Runbook są przechowywane i zarządzane w Azure Automation a następnie dostarczane do co najmniej jednego wyznaczenia komputerów. W tym artykule opisano sposób wdrażania hybrydowego procesu roboczego elementu Runbook na komputerze z systemem Windows.

Po pomyślnym wdrożeniu programu Runbook Worker przejrzyj temat [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md) , aby dowiedzieć się, jak skonfigurować elementy Runbook do automatyzowania procesów w lokalnym centrum danych lub w innym środowisku chmury.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](automation-update-azure-modules.md).

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Instalacja i konfiguracja hybrydowego procesu roboczego elementu Runbook systemu Windows

Aby zainstalować i skonfigurować hybrydowy proces roboczy elementu Runbook systemu Windows, można użyć jednej z następujących metod.

* W przypadku maszyn wirtualnych platformy Azure należy zainstalować agenta Log Analytics dla systemu Windows przy użyciu [rozszerzenia maszyny wirtualnej dla systemu Windows](../virtual-machines/extensions/oms-windows.md). Rozszerzenie instaluje agenta Log Analytics na maszynach wirtualnych platformy Azure i rejestruje maszyny wirtualne w istniejącym obszarze roboczym Log Analytics przy użyciu szablonu Azure Resource Manager lub programu PowerShell. Po zainstalowaniu agenta można dodać maszynę wirtualną do grupy hybrydowych procesów roboczych elementu Runbook na Twoim koncie usługi Automation, wykonując **krok 4** w sekcji [wdrażanie ręczne](#manual-deployment) poniżej.

* Użyj elementu Runbook usługi Automation, aby całkowicie zautomatyzować proces konfigurowania komputera z systemem Windows. Jest to zalecana metoda dla maszyn w centrum danych lub w innym środowisku chmury.

* Postępuj zgodnie z procedurą krok po kroku, aby ręcznie zainstalować i skonfigurować rolę hybrydowego procesu roboczego elementu Runbook na maszynie wirtualnej spoza platformy Azure.

> [!NOTE]
> Aby zarządzać konfiguracją serwerów obsługujących rolę hybrydowego procesu roboczego elementu Runbook z konfiguracją żądanego stanu (DSC), należy dodać je jako węzły DSC.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Minimalne wymagania dla hybrydowego procesu roboczego elementu Runbook systemu Windows

Minimalne wymagania dla hybrydowego procesu roboczego elementu Runbook systemu Windows to:

* Windows Server 2012 lub nowszy
* Windows PowerShell 5,1 lub nowszy ([pobieranie plików WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 lub nowszy
* Dwa rdzenie
* 4 GB pamięci RAM
* Port 443 (ruch wychodzący)

### <a name="network-configuration"></a>Konfiguracja sieci

Aby uzyskać więcej wymagań sieci dla hybrydowego procesu roboczego elementu Runbook, zobacz [Konfigurowanie sieci](automation-hybrid-runbook-worker.md#network-planning).

### <a name="server-onboarding-for-management-with-automation-dsc"></a>Dołączanie serwera do zarządzania przy użyciu Automation DSC

Aby uzyskać informacje na temat dołączania serwerów do zarządzania za pomocą DSC, zobacz [dołączanie maszyn w celu zarządzania przez Azure Automation DSC](automation-dsc-onboarding.md).

Po włączeniu [rozwiązania Update Management](../operations-management-suite/oms-solution-update-management.md)każdy komputer z systemem Windows połączony z obszarem roboczym log Analytics zostanie automatycznie skonfigurowany jako hybrydowy proces roboczy elementu Runbook w celu obsługi elementów Runbook zawartych w tym rozwiązaniu. Nie jest on jednak zarejestrowany dla żadnych grup hybrydowych procesów roboczych, które są już zdefiniowane w Twoim koncie usługi Automation.

### <a name="adding-the-computer-to-a-hybrid-runbook-worker-group"></a>Dodawanie komputera do grupy hybrydowych procesów roboczych elementu Runbook

Komputer można dodać do grupy hybrydowych procesów roboczych elementu Runbook na koncie usługi Automation w celu obsługi elementów Runbook usługi Automation, o ile używasz tego samego konta zarówno dla tego rozwiązania, jak i dla członkostwa w grupie hybrydowych procesów roboczych elementu Runbook. Ta funkcjonalność została dodana do wersji 7.2.12024.0 hybrydowego procesu roboczego elementu Runbook.

## <a name="automated-deployment"></a>Automatyczne wdrażanie

Na maszynie docelowej wykonaj następujące kroki, aby zautomatyzować instalację i konfigurację roli hybrydowego procesu roboczego systemu Windows.

### <a name="1-download-the-powershell-script"></a>1. Pobierz skrypt programu PowerShell

Pobierz skrypt New-OnPremiseHybridWorker. ps1 z [Galeria programu PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) bezpośrednio z komputera z uruchomioną rolą hybrydowego procesu roboczego elementu Runbook lub z innego komputera w środowisku. Skopiuj skrypt do procesu roboczego. Skrypt New-OnPremiseHybridWorker. ps1 wymaga następujących parametrów podczas wykonywania:

* *AAResourceGroupName* (obowiązkowy): Nazwa grupy zasobów, która jest skojarzona z kontem usługi Automation.
* *OMSResourceGroupName* (opcjonalnie): Nazwa grupy zasobów dla obszaru roboczego log Analytics. Jeśli ta grupa zasobów nie jest określona, używana jest *AAResourceGroupName* .
* Subskrypcja (obowiązkowa): Identyfikator subskrypcji platformy Azure, w którym znajduje się konto usługi Automation.
* *TenantID* (opcjonalnie): identyfikator organizacji dzierżawy skojarzonej z kontem usługi Automation.
* *WorkspaceName* (opcjonalnie): Nazwa obszaru roboczego log Analytics. Jeśli nie masz obszaru roboczego Log Analytics, skrypt tworzy i konfiguruje go.
* *AutomationAccountName* (obowiązkowy): nazwa konta usługi Automation.
* *HybridGroupName* (obowiązkowy): Nazwa grupy hybrydowych procesów roboczych elementu Runbook, która została określona jako element docelowy dla elementów Runbook, które obsługują ten scenariusz.
* *Poświadczenie* (opcjonalnie): poświadczenia, które mają być używane podczas logowania w środowisku platformy Azure.
  
> [!NOTE]
> Podczas włączania rozwiązań tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i konta usługi Automation. Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](how-to/region-mappings.md).

### <a name="2-open-windows-powershell-command-line-shell"></a>2. Otwórz powłokę wiersza polecenia programu Windows PowerShell

Otwórz program **Windows PowerShell** na ekranie **startowym** w trybie administratora.

### <a name="3-run-the-powershell-script"></a>3. Uruchom skrypt programu PowerShell

Z poziomu powłoki wiersza polecenia programu PowerShell przejdź do folderu zawierającego pobrany skrypt. Zmień wartości parametrów *-AutomationAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-subskrypcji*i *-WorkspaceName*. Następnie uruchom skrypt.

Po uruchomieniu skryptu zostanie wyświetlony monit o uwierzytelnienie przy użyciu platformy Azure. Musisz zalogować się przy użyciu konta, które jest członkiem roli Administratorzy subskrypcji i współadministratorem subskrypcji.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="4-install-nuget"></a>4. Zainstaluj pakiet NuGet

Zostanie wyświetlony monit o zgodę na zainstalowanie narzędzia NuGet i uwierzytelnienie przy użyciu poświadczeń platformy Azure. Jeśli nie masz najnowszej wersji pakietu NuGet, możesz uzyskać ją z [dostępnych wersji dystrybucji NuGet](https://www.nuget.org/downloads).

### <a name="5-verify-the-deployment"></a>5. Sprawdź wdrożenie

Po zakończeniu działania skryptu na stronie **grupy hybrydowych procesów roboczych** zostanie wyświetlona nowa grupa i liczba członków. Jeśli jest to istniejąca Grupa, liczba członków jest zwiększana. Możesz wybrać grupę z listy na stronie **grupy hybrydowych procesów roboczych** i wybrać kafelek **hybrydowe procesy** robocze. Na stronie **hybrydowe procesy robocze** zobaczysz każdego członka grupy na liście.

## <a name="manual-deployment"></a>Wdrażanie ręczne

Na maszynie docelowej wykonaj dwa pierwsze kroki w środowisku automatyzacji. Następnie wykonaj pozostałe kroki dla każdego komputera podrzędnego.

### <a name="1-create-a-log-analytics-workspace"></a>1. Utwórz obszar roboczy Log Analytics

Jeśli nie masz jeszcze obszaru roboczego Log Analytics, przed utworzeniem obszaru roboczego zapoznaj się z tematem [wskazówki dotyczące projektowania dziennika Azure monitor](../azure-monitor/platform/design-logs-deployment.md) .

### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Dodaj rozwiązanie automatyzacji do obszaru roboczego Log Analytics

Rozwiązanie Automation dodaje funkcjonalność dla Azure Automation, w tym obsługę hybrydowego procesu roboczego elementu Runbook. Po dodaniu rozwiązania do obszaru roboczego Log Analytics automatycznie wypychanie składników procesu roboczego do komputera agenta, który zostanie zainstalowany w następnym kroku.

Aby dodać rozwiązanie **automatyzacji** do obszaru roboczego, uruchom następujące polecenie cmdlet programu PowerShell.

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="3-install-the-log-analytics-agent-for-windows"></a>3. Zainstaluj agenta Log Analytics dla systemu Windows

Agent Log Analytics dla systemu Windows łączy komputery z obszarem roboczym Azure Monitor Log Analytics. Po zainstalowaniu agenta na komputerze i nawiązaniu połączenia z obszarem roboczym program automatycznie pobiera składniki wymagane dla hybrydowego procesu roboczego elementu Runbook.

Aby zainstalować agenta na komputerze, postępuj zgodnie z instrukcjami na stronie [łączenie komputerów z systemem Windows w celu Azure monitor dzienników](../log-analytics/log-analytics-windows-agent.md). Można powtórzyć ten proces dla wielu komputerów, aby dodać wielu procesów roboczych do środowiska.

Gdy Agent pomyślnie połączył się z obszarem roboczym Log Analytics za kilka minut, możesz uruchomić następujące zapytanie, aby sprawdzić, czy wysyła dane pulsu do obszaru roboczego:

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

W wynikach wyszukiwania powinny być widoczne rekordy pulsu dla komputera, wskazujące, że są one połączone i raportowane do usługi. Domyślnie każdy agent przekazuje rekord pulsu do przypisanego im obszaru roboczego. Można sprawdzić, czy Agent prawidłowo pobrał rozwiązanie automatyzacji, gdy ma folder o nazwie AzureAutomationFiles w folderze C:\Program Files\Microsoft monitoring Agent\Agent. Aby potwierdzić wersję hybrydowego procesu roboczego elementu Runbook, przejdź do katalogu C:\Program Files\Microsoft monitoring Agent\Agent\AzureAutomation\ i zanotuj podfolder \\*wersji* .

### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Zainstaluj środowisko Runbook i Połącz się z Azure Automation

W przypadku skonfigurowania agenta w celu raportowania do obszaru roboczego Log Analytics rozwiązanie Automation wypchnij moduł HybridRegistration PowerShell, który zawiera polecenie cmdlet **Add-HybridRunbookWorker** . To polecenie cmdlet służy do instalowania środowiska Runbook na komputerze i rejestrowania go w Azure Automation.

Otwórz sesję programu PowerShell w trybie administratora i uruchom następujące polecenia, aby zaimportować moduł.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Następnie uruchom polecenie cmdlet **Add-HybridRunbookWorker** przy użyciu następującej składni.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Informacje wymagane dla tego polecenia cmdlet można uzyskać z strony "Zarządzanie kluczami" w Azure Portal. Otwórz Tę stronę, wybierając opcję **klucze** na stronie **Ustawienia** na koncie usługi Automation.

![Strona "Zarządzanie kluczami"](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Dla parametru *GroupName* Użyj nazwy grupy hybrydowych procesów roboczych elementu Runbook. Jeśli ta grupa już istnieje na koncie usługi Automation, do niej zostanie dodany bieżący komputer. Jeśli ta grupa nie istnieje, jest dodawana.
* Dla parametru *Endpoint* Użyj wpisu **adresu URL** na stronie Zarządzanie kluczami.
* Dla parametru *token* Użyj **podstawowego wpisu klucza dostępu** na stronie Zarządzanie kluczami.

Aby uzyskać szczegółowe informacje na temat instalacji, należy użyć przełącznika *-verbose* z poleceniem **Add-HybridRunbookWorker**.

### <a name="5-install-powershell-modules"></a>5. Zainstaluj moduły programu PowerShell

Elementy Runbook mogą korzystać z dowolnych działań i poleceń cmdlet zdefiniowanych w modułach zainstalowanych w środowisku Azure Automation. Te moduły nie są automatycznie wdrażane na komputerach lokalnych, dlatego należy je zainstalować ręcznie. Wyjątek jest modułem platformy Azure. Ten moduł jest instalowany domyślnie i zapewnia dostęp do poleceń cmdlet dla wszystkich usług i działań platformy Azure dla Azure Automation.

Ponieważ głównym celem funkcji hybrydowego procesu roboczego elementu Runbook jest zarządzanie zasobami lokalnymi, najprawdopodobniej trzeba zainstalować moduły obsługujące te zasoby, w szczególności moduł PowerShellGet. Aby uzyskać informacje na temat instalowania modułów programu Windows PowerShell, zobacz [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell).

Zainstalowane moduły muszą znajdować się w lokalizacji, do której odwołuje się zmienna środowiskowa PSModulePath, dzięki czemu hybrydowy proces roboczy może je automatycznie zaimportować. Aby uzyskać więcej informacji, zobacz [Install modules in PSModulePath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak skonfigurować elementy Runbook do automatyzowania procesów w lokalnym centrum danych lub w innym środowisku chmury, zobacz [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md).
* Aby uzyskać instrukcje dotyczące usuwania hybrydowych procesów roboczych elementów Runbook, zobacz [usuwanie Azure Automation hybrydowych procesów roboczych elementów Runbook](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Aby dowiedzieć się, jak rozwiązywać problemy dotyczące hybrydowych procesów roboczych elementów Runbook, zobacz [Rozwiązywanie problemów z hybrydowymi](troubleshoot/hybrid-runbook-worker.md#windows) procesami
* Aby uzyskać dodatkowe instrukcje dotyczące rozwiązywania problemów z Update Management, zobacz [Update Management: Rozwiązywanie problemów](troubleshoot/update-management.md).
