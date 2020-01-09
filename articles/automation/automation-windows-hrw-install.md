---
title: Hybrydowy proces roboczy elementu runbook usługi Azure Automation (Windows)
description: Ten artykuł zawiera informacje na temat instalowania Azure Automation hybrydowego procesu roboczego elementu Runbook, którego można użyć do uruchamiania elementów Runbook na komputerach z systemem Windows w lokalnym centrum danych lub w środowisku chmury.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 696885fa3e082ae7096954fb55b17da5b77788bc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75418896"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Wdrażanie hybrydowego procesu roboczego elementu Runbook systemu Windows

Za pomocą funkcji hybrydowego procesu roboczego elementu Runbook programu Azure Automation można uruchamiać elementy Runbook bezpośrednio na komputerze hostującym rolę i w odniesieniu do zasobów w środowisku, aby zarządzać tymi zasobami lokalnymi. Elementy Runbook są przechowywane i zarządzane w Azure Automation a następnie dostarczane do co najmniej jednego wyznaczenia komputerów. W tym artykule opisano sposób instalowania hybrydowego procesu roboczego elementu Runbook na komputerze z systemem Windows.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Instalowanie hybrydowego procesu roboczego elementu Runbook systemu Windows

Aby zainstalować i skonfigurować hybrydowy proces roboczy elementu Runbook systemu Windows, można użyć jednej z trzech następujących metod:

* W przypadku maszyn wirtualnych platformy Azure należy zainstalować agenta Log Analytics dla systemu Windows przy użyciu [rozszerzenia maszyny wirtualnej dla systemu Windows](../virtual-machines/extensions/oms-windows.md). Rozszerzenie instaluje agenta Log Analytics na maszynach wirtualnych platformy Azure i rejestruje maszyny wirtualne w istniejącym obszarze roboczym Log Analytics przy użyciu szablonu Azure Resource Manager lub programu PowerShell. Po zainstalowaniu agenta można dodać maszynę wirtualną do grupy hybrydowych procesów roboczych elementu Runbook na Twoim koncie usługi Automation, wykonując **krok 4** w sekcji [wdrażanie ręczne](#manual-deployment) poniżej.

* Użyj elementu Runbook usługi Automation, aby całkowicie zautomatyzować proces konfigurowania komputera z systemem Windows. Jest to zalecana metoda dla maszyn w centrum danych lub w innym środowisku chmury.

* Postępuj zgodnie z procedurą krok po kroku, aby ręcznie zainstalować i skonfigurować rolę hybrydowego procesu roboczego elementu Runbook na maszynie wirtualnej spoza platformy Azure.

> [!NOTE]
> Aby zarządzać konfiguracją serwerów obsługujących rolę hybrydowego procesu roboczego elementu Runbook z konfiguracją żądanego stanu (DSC), należy dodać je jako węzły DSC.

Minimalne wymagania dla hybrydowego procesu roboczego elementu Runbook systemu Windows to:

* System Windows Server 2012 lub nowszy.
* Program Windows PowerShell 5,1 lub nowszy ([pobieranie plików WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616)).
* .NET Framework 4.6.2 lub nowszy.
* Dwa rdzenie.
* 4 GB pamięci RAM.
* Port 443 (ruch wychodzący).

Aby uzyskać więcej wymagań sieci dla hybrydowego procesu roboczego elementu Runbook, zobacz [Konfigurowanie sieci](automation-hybrid-runbook-worker.md#network-planning).

Aby uzyskać więcej informacji na temat dołączania serwerów do zarządzania za pomocą DSC, zobacz [dołączanie maszyn w celu zarządzania przez Azure Automation DSC](automation-dsc-onboarding.md).
Po włączeniu [rozwiązania Update Management](../operations-management-suite/oms-solution-update-management.md)każdy komputer z systemem Windows połączony z obszarem roboczym log Analytics zostanie automatycznie skonfigurowany jako hybrydowy proces roboczy elementu Runbook w celu obsługi elementów Runbook zawartych w tym rozwiązaniu. Nie jest on jednak zarejestrowany dla żadnych grup hybrydowych procesów roboczych, które są już zdefiniowane w Twoim koncie usługi Automation. 

Komputer można dodać do grupy hybrydowych procesów roboczych elementu Runbook na koncie usługi Automation w celu obsługi elementów Runbook usługi Automation, o ile używasz tego samego konta zarówno dla tego rozwiązania, jak i dla członkostwa w grupie hybrydowych procesów roboczych elementu Runbook. Ta funkcjonalność została dodana do wersji 7.2.12024.0 hybrydowego procesu roboczego elementu Runbook.

Po pomyślnym wdrożeniu programu Runbook Worker przejrzyj temat [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md) , aby dowiedzieć się, jak skonfigurować elementy Runbook do automatyzowania procesów w lokalnym centrum danych lub w innym środowisku chmury.

### <a name="automated-deployment"></a>Wdrożenie zautomatyzowane

Wykonaj następujące kroki, aby zautomatyzować instalację i konfigurację roli hybrydowego procesu roboczego systemu Windows:

1. Pobierz skrypt New-OnPremiseHybridWorker. ps1 z [Galeria programu PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) bezpośrednio z komputera z uruchomioną rolą hybrydowego procesu roboczego elementu Runbook lub z innego komputera w środowisku. Skopiuj skrypt do procesu roboczego.

   Skrypt New-OnPremiseHybridWorker. ps1 wymaga następujących parametrów podczas wykonywania:

   * *AutomationAccountName* (obowiązkowy): nazwa konta usługi Automation.
   * *AAResourceGroupName* (obowiązkowy): Nazwa grupy zasobów, która jest skojarzona z kontem usługi Automation.
   * *OMSResourceGroupName* (opcjonalnie): Nazwa grupy zasobów dla obszaru roboczego log Analytics. Jeśli ta grupa zasobów nie jest określona, używana jest *AAResourceGroupName* .
   * *HybridGroupName* (obowiązkowy): Nazwa grupy hybrydowych procesów roboczych elementu Runbook, która została określona jako element docelowy dla elementów Runbook, które obsługują ten scenariusz.
   * Subskrypcja (obowiązkowa): Identyfikator subskrypcji platformy Azure, w którym znajduje się konto usługi Automation.
   * *WorkspaceName* (opcjonalnie): Nazwa obszaru roboczego log Analytics. Jeśli nie masz obszaru roboczego Log Analytics, skrypt tworzy i konfiguruje go.

   > [!NOTE]
   > W przypadku włączenia rozwiązań tylko w niektórych regionach jest obsługiwane łączenie obszaru roboczego usługi Log Analytics i konta usługi Automation.
   >
   > Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](how-to/region-mappings.md).

2. Na komputerze otwórz program **Windows PowerShell** na ekranie **startowym** w trybie administratora.
3. Z poziomu powłoki wiersza polecenia programu PowerShell przejdź do folderu zawierającego pobrany skrypt. Zmień wartości parametrów *-AutomationAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-subskrypcji*i *-WorkspaceName*. Następnie uruchom skrypt.

     > [!NOTE]
     > Po uruchomieniu skryptu zostanie wyświetlony monit o uwierzytelnienie przy użyciu platformy Azure. *Musisz* zalogować się przy użyciu konta, które jest członkiem roli Administratorzy subskrypcji i współadministratorem subskrypcji.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

4. Zostanie wyświetlony monit o zgodę na zainstalowanie narzędzia NuGet i wyświetlenie monitu o uwierzytelnienie przy użyciu poświadczeń platformy Azure.

5. Po zakończeniu działania skryptu na stronie **grupy hybrydowych procesów roboczych** zostanie wyświetlona nowa grupa i liczba członków. Jeśli jest to istniejąca Grupa, liczba członków jest zwiększana. Możesz wybrać grupę z listy na stronie **grupy hybrydowych procesów roboczych** i wybrać kafelek **hybrydowe procesy** robocze. Na stronie **hybrydowe procesy robocze** zobaczysz każdego członka grupy na liście.

### <a name="manual-deployment"></a>Wdrożenie ręczne

Wykonaj pierwsze dwa kroki w środowisku automatyzacji, a następnie powtórz pozostałe kroki dla każdego komputera podrzędnego.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

#### <a name="1-create-a-log-analytics-workspace"></a>1. Utwórz obszar roboczy Log Analytics

Jeśli nie masz jeszcze obszaru roboczego Log Analytics, przed utworzeniem obszaru roboczego zapoznaj się z tematem [wskazówki dotyczące projektowania dziennika Azure monitor](../azure-monitor/platform/design-logs-deployment.md) . 

#### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Dodaj rozwiązanie automatyzacji do obszaru roboczego Log Analytics

Rozwiązanie Automation dodaje funkcjonalność dla Azure Automation, w tym obsługę hybrydowego procesu roboczego elementu Runbook. Po dodaniu rozwiązania do obszaru roboczego Log Analytics automatycznie wypychanie składników procesu roboczego do komputera agenta, który zostanie zainstalowany w następnym kroku.

Aby dodać rozwiązanie **Automation** do obszaru roboczego, uruchom następujące polecenie programu PowerShell.

```powershell-interactive
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
```

#### <a name="3-install-the-log-analytics-agent-for-windows"></a>3. Zainstaluj agenta Log Analytics dla systemu Windows

Agent Log Analytics dla systemu Windows łączy komputery z obszarem roboczym Azure Monitor Log Analytics. Po zainstalowaniu agenta na komputerze i nawiązaniu połączenia z obszarem roboczym program automatycznie pobiera składniki wymagane dla hybrydowego procesu roboczego elementu Runbook.

Aby zainstalować agenta na komputerze, postępuj zgodnie z instrukcjami na stronie [łączenie komputerów z systemem Windows w celu Azure monitor dzienników](../log-analytics/log-analytics-windows-agent.md). Można powtórzyć ten proces dla wielu komputerów, aby dodać wielu procesów roboczych do środowiska.

Gdy Agent pomyślnie połączył się z obszarem roboczym Log Analytics, po kilku minutach można uruchomić następujące zapytanie, aby sprawdzić, czy wysyła dane pulsu do obszaru roboczego:

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

W zwróconych wynikach wyszukiwania powinny być widoczne rekordy pulsu dla komputera, który wskazuje, że jest on połączony i zgłaszany do usługi. Rekord pulsu jest domyślnie przekazywany z każdego agenta do przypisanego do niego obszaru roboczego. Można sprawdzić, czy Agent prawidłowo pobrał rozwiązanie automatyzacji, gdy ma folder o nazwie **AzureAutomationFiles** w folderze C:\Program Files\Microsoft monitoring Agent\Agent. Aby potwierdzić wersję hybrydowego procesu roboczego elementu Runbook, można przejść do katalogu C:\Program Files\Microsoft monitoring Agent\Agent\AzureAutomation\ i zanotować podfolder \\*wersji* .

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Zainstaluj środowisko Runbook i Połącz się z Azure Automation

W przypadku skonfigurowania agenta w celu raportowania do obszaru roboczego Log Analytics rozwiązanie Automation wypchnij moduł **HybridRegistration** PowerShell, który zawiera polecenie cmdlet **Add-HybridRunbookWorker** . To polecenie cmdlet służy do instalowania środowiska Runbook na komputerze i rejestrowania go w Azure Automation.

Otwórz sesję programu PowerShell w trybie administratora i uruchom następujące polecenia, aby zaimportować moduł:

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Następnie uruchom polecenie cmdlet **Add-HybridRunbookWorker** przy użyciu następującej składni:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Informacje wymagane dla tego polecenia cmdlet można uzyskać ze strony **Zarządzanie kluczami** w Azure Portal. Otwórz Tę stronę, wybierając opcję **klucze** na stronie **Ustawienia** na koncie usługi Automation.

![Strona "Zarządzanie kluczami"](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** to nazwa grupy hybrydowych procesów roboczych elementu Runbook. Jeśli ta grupa już istnieje na koncie usługi Automation, do niej zostanie dodany bieżący komputer. Jeśli ta grupa nie istnieje, jest dodawana.
* **Punkt końcowy** to wpis **adresu URL** na stronie **Zarządzanie kluczami** .
* **Token** to **podstawowy wpis klucza dostępu** na stronie **Zarządzanie kluczami** .

Aby uzyskać szczegółowe informacje na temat instalacji, należy użyć przełącznika **-verbose** z poleceniem **Add-HybridRunbookWorker**.

#### <a name="5-install-powershell-modules"></a>5. Zainstaluj moduły programu PowerShell

Elementy Runbook mogą korzystać z dowolnych działań i poleceń cmdlet zdefiniowanych w modułach zainstalowanych w środowisku Azure Automation. Te moduły nie są automatycznie wdrażane na komputerach lokalnych, dlatego należy je zainstalować ręcznie. Wyjątkiem jest moduł platformy Azure, który jest instalowany domyślnie i zapewnia dostęp do poleceń cmdlet dla wszystkich usług i działań platformy Azure dla Azure Automation.

Ponieważ głównym celem funkcji hybrydowego procesu roboczego elementu Runbook jest zarządzanie zasobami lokalnymi, najprawdopodobniej trzeba zainstalować moduły obsługujące te zasoby. Aby uzyskać informacje na temat instalowania modułów programu Windows PowerShell, zobacz [Instalowanie modułów](/powershell/scripting/developer/windows-powershell). 

Zainstalowane moduły muszą znajdować się w lokalizacji, do której odwołuje się zmienna środowiskowa **PSModulePath** , dzięki czemu hybrydowy proces roboczy może je automatycznie zaimportować. Aby uzyskać więcej informacji, zobacz [Modyfikowanie ścieżki instalacji PSModulePath](/powershell/scripting/developer/windows-powershell).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak skonfigurować elementy Runbook do automatyzowania procesów w lokalnym centrum danych lub w innym środowisku chmury, zobacz [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md).
* Aby uzyskać instrukcje dotyczące usuwania hybrydowych procesów roboczych elementów Runbook, zobacz [usuwanie Azure Automation hybrydowych procesów roboczych elementów Runbook](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Aby dowiedzieć się, jak rozwiązywać problemy dotyczące hybrydowych procesów roboczych elementów Runbook, zobacz [Rozwiązywanie problemów z hybrydowymi](troubleshoot/hybrid-runbook-worker.md#windows) procesami
* Aby uzyskać dodatkowe instrukcje dotyczące rozwiązywania problemów z Update Management, zobacz [Update Management: Rozwiązywanie problemów](troubleshoot/update-management.md).
