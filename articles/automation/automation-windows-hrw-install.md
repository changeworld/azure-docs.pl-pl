---
title: Hybrydowy proces roboczy elementu runbook usługi Azure Automation (Windows)
description: Ten artykuł zawiera informacje na temat instalowania Azure hybrydowego procesu roboczego Runbook automatyzacji używanego do uruchamiania elementów runbook na komputerach z systemem Windows w lokalnym centrum danych lub w środowisku chmury.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e449e6e457c4fa568b5a4de5823014b4dcea82d0
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063951"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Wdrażanie systemu Windows hybrydowego Runbook Worker

Funkcja hybrydowy proces roboczy elementu Runbook usługi Automatyzacja Azure służy do uruchamiania elementów runbook bezpośrednio na komputerze, który jest hostem roli i względem zasobów w środowisku, aby zarządzać tymi zasobów lokalnych. Elementy Runbook są przechowywane i zarządzane w automatyzacji Azure i następnie dostarczany do co najmniej jednym komputerze wyznaczonym. W tym artykule opisano sposób instalowania hybrydowy proces roboczy elementu Runbook na komputerze z systemem Windows.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Instalowanie systemu Windows hybrydowy proces roboczy

Aby zainstalować i skonfigurować Runbook Worker hybrydowego systemu Windows, można użyć dwóch metod. Zalecaną metodą jest całkowicie zautomatyzować proces konfigurowania komputerów z systemem Windows przy użyciu element runbook usługi Automatyzacja. Druga metoda jest po procedurą krok po kroku, aby ręcznie zainstalować i skonfigurować rolę.

> [!NOTE]
> Aby zarządzać konfiguracji serwerów obsługujących roli hybrydowy proces roboczy elementu Runbook z konfiguracji żądanego stanu (DSC), należy je dodać jako węzły DSC.

Dostępne są następujące minimalne wymagania dotyczące systemu Windows hybrydowego Runbook Worker:

* Windows Server 2012 lub nowszym.
* Środowisko Windows PowerShell w wersji 4.0 lub nowszy ([Pobierz WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855)). Firma Microsoft zaleca Windows PowerShell w wersji 5.1 ([Pobierz WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)) do zwiększenia niezawodności.
* .NET framework 4.6.2 lub nowszym.
* Dwa rdzenie.
* 4 GB pamięci RAM.
* Port 443 (dla ruchu wychodzącego).

Aby uzyskać więcej wymagania sieciowe związane z hybrydowy proces roboczy elementu Runbook, zobacz [Konfigurowanie sieci](automation-hybrid-runbook-worker.md#network-planning).

Aby uzyskać więcej informacji o serwerach zarządzania w usłudze Konfiguracja DSC dołączania, zobacz [dołączania komputerów do zarządzania przez Konfiguracja DSC automatyzacji Azure](automation-dsc-onboarding.md).
Po włączeniu [rozwiązania do zarządzania aktualizacjami](../operations-management-suite/oms-solution-update-management.md), dowolnego komputera systemu Windows połączoną z obszaru roboczego analizy dzienników Azure zostanie automatycznie skonfigurowany jako hybrydowy proces roboczy elementu Runbook do obsługi elementy runbook zawarte w tym rozwiązaniu. Jednak nie jest on zarejestrowany na wszystkie grupy hybrydowego procesu roboczego już zdefiniowane na Twoim koncie automatyzacji. 

Komputer można dodać do grupy hybrydowego procesu roboczego elementu Runbook do Twojego konta automatyzacji do obsługi elementów runbook automatyzacji, tak długo, jak używasz tego samego konta dla rozwiązania i członkostwo w grupie hybrydowy proces roboczy elementu Runbook. Ta funkcjonalność została dodana do wersji 7.2.12024.0 hybrydowego procesu roboczego elementu Runbook.

Po pomyślnym wdrożeniu runbook worker, przejrzyj [uruchamiania elementów runbook na hybrydowy proces roboczy elementu Runbook](automation-hrw-run-runbooks.md) Aby dowiedzieć się, jak skonfigurować elementy runbook do automatyzacji procesów w lokalnym centrum danych lub w innym środowisku chmury.

### <a name="automated-deployment"></a>Wdrożenie zautomatyzowane

Wykonaj poniższe kroki, aby zautomatyzować instalację i konfigurację roli Windows hybrydowego procesu roboczego:

1. Pobierz skrypt OnPremiseHybridWorker.ps1 nowy [galerii programu PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/DisplayScript) bezpośrednio z komputera z uruchomionym roli hybrydowy proces roboczy elementu Runbook lub z innego komputera w danym środowisku. Skopiuj skrypt do pracownika.

   Skrypt New OnPremiseHybridWorker.ps1 wymaga następujących parametrów podczas wykonywania:

   * *AutomationAccountName* (wymagane): Nazwa konta automatyzacji.
   * *AAResourceGroupName* (wymagane): Nazwa grupy zasobów, który został skojarzony z Twoim kontem automatyzacji.
   * *OMSResourceGroupName* (opcjonalnie): Nazwa grupy zasobów dla obszaru roboczego usługi Operations Management Suite. Jeśli ta grupa zasobów nie jest określony, *AAResourceGroupName* jest używany.
   * *HybridGroupName* (wymagane): Nazwa grupy hybrydowego procesu roboczego elementu Runbook, określony jako element docelowy dla elementów runbook, który obsługuje ten scenariusz.
   * *Identyfikator subskrypcji* (wymagane): identyfikator subskrypcji Azure, których należy konto automatyzacji.
   * *WorkspaceName* (opcjonalnie): Nazwa obszaru roboczego analizy dzienników. Jeśli nie masz obszaru roboczego analizy dzienników skrypt tworzy i konfiguruje jeden.

     > [!NOTE]
     > Obecnie są tylko regiony automatyzacji obsługiwane integracji z usługą analizy dzienników **południowo-wschodnia Australia**, **wschodnie stany USA 2**, **Azja południowo-wschodnia**, i  **Europa Zachodnia**. Jeśli Twoje konto usługi Automatyzacja nie jest w jednym z tych regionów, skrypt tworzy obszaru roboczego analizy dzienników, ale ostrzega o tym, że jej nie może połączyć je razem.

1. Na komputerze, otwórz **programu Windows PowerShell** z **Start** ekranu w trybie administratora.
1. Z poziomu wiersza polecenia powłoki PowerShell przejdź do folderu, który zawiera skrypt, który został pobrany. Zmień wartości parametrów *- AutomationAccountName*, *- AAResourceGroupName*, *- OMSResourceGroupName*, *- HybridGroupName*, *- SubscriptionId*, i *- WorkspaceName*. Następnie uruchom skrypt.

     > [!NOTE]
     > Zostanie wyświetlony monit o uwierzytelniania w usłudze Azure po uruchomieniu skryptu. Możesz *musi* Zaloguj się przy użyciu konta należącego do roli Administratorzy subskrypcji i współadministratora subskrypcji.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

1. Zostanie wyświetlony monit o zgodę na instalowania NuGet i zostanie wyświetlony monit o uwierzytelniania przy użyciu poświadczeń platformy Azure.

1. Po zakończeniu działania skryptu **hybrydowego procesu roboczego grupy** strona zawiera nową grupę i liczby elementów członkowskich. Jeśli jest istniejącą grupę, do liczby elementów członkowskich jest zwiększany. Można zaznaczyć grupę z listy na **hybrydowego procesu roboczego grupy** i wybrać opcję **hybrydowych procesów roboczych** kafelka. Na **hybrydowych procesów roboczych** strony, zobacz każdego członka grupy na liście.

### <a name="manual-deployment"></a>Ręczne wdrażanie

Raz wykonać pierwsze dwa kroki dla danego środowiska automatyzacji, a następnie powtórz pozostałe kroki na każdym komputerze pracownika.

#### <a name="1-create-a-log-analytics-workspace"></a>1. Tworzenie obszaru roboczego analizy dzienników

Jeśli nie masz jeszcze obszaru roboczego analizy dzienników, utwórz ją, korzystając z instrukcji w [Zarządzanie obszaru roboczego](../log-analytics/log-analytics-manage-access.md). Istniejący obszar roboczy można użyć, jeśli już istnieje.

#### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Dodaj rozwiązanie automatyzacji do obszaru roboczego analizy dzienników

Rozwiązania powodują dodawanie funkcji do usługi Log Analytics. Rozwiązanie Automatyzacja dodaje funkcje automatyzacji Azure, w tym obsługi hybrydowy proces roboczy elementu Runbook. Dodaj rozwiązanie do swojego obszaru roboczego, automatycznie wypycha składników procesu roboczego na komputerze agenta, który zostanie zainstalowany w następnym kroku.

Aby dodać **automatyzacji** rozwiązania do swojego obszaru roboczego analizy dzienników, postępuj zgodnie z instrukcjami w [można dodać za pomocą galerii rozwiązań rozwiązania](../log-analytics/log-analytics-add-solutions.md).

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Zainstaluj program Microsoft Monitoring Agent

Microsoft Monitoring Agent łączy się analizy dzienników z komputerów. Po zainstalowaniu agenta na komputerze lokalnym i podłącz go do swojego obszaru roboczego, automatycznie pobiera składniki, które są wymagane dla hybrydowego procesu roboczego elementu Runbook.

Aby zainstalować agenta na komputerze lokalnym, postępuj zgodnie z instrukcjami w [połączyć komputery do analizy dzienników](../log-analytics/log-analytics-windows-agent.md). Można powtórzyć ten proces dla wielu komputerów do dodania wielu pracowników w danym środowisku.

Gdy agent połączył się pomyślnie analizy dzienników, jest wyświetlana na **połączonych źródeł** kartę analizy dzienników **ustawienia** strony. Możesz sprawdzić, czy agent poprawnie pobrał rozwiązania Automatyzacja po folder o nazwie **AzureAutomationFiles** w C:\Program Files\Microsoft Monitoring Agent\Agent. Aby sprawdzić wersję hybrydowy proces roboczy elementu Runbook, możesz przejść do C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ i zanotuj \\ *wersji* podfolderu.

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Zainstalowania środowiska elementu runbook i nawiązania połączenia usługi Automatyzacja Azure

Po dodaniu agenta do analizy dzienników rozwiązania Automatyzacja umieszcza w dół **HybridRegistration** moduł programu PowerShell, który zawiera **Add-HybridRunbookWorker** polecenia cmdlet. To polecenie cmdlet służy do zainstalowania środowiska elementu runbook na komputerze i zarejestrowania go w usłudze Automatyzacja Azure.

Otwórz sesję programu PowerShell w trybie administratora i uruchom następujące polecenia, aby zaimportować moduł:

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module HybridRegistration.psd1
```

Następnie uruchom **Add-HybridRunbookWorker** polecenia cmdlet, używając następującej składni:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Można uzyskać informacji wymaganych dla tego polecenia cmdlet z **zarządzanie kluczami** strony w portalu Azure. Otwórz tę stronę, wybierając **klucze** opcję **ustawienia** strony na Twoim koncie automatyzacji.

![Strona "Zarządzaj kluczami"](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** to nazwa grupy hybrydowego procesu roboczego elementu Runbook. Jeśli ta grupa już istnieje na koncie automatyzacji, bieżący komputer został dodany do niego. Jeśli ta grupa nie istnieje, jest dodawany.
* **Punkt końcowy** jest **adres URL** wpis na **zarządzanie kluczami** strony.
* **Token** jest **podstawowy klucz dostępu** wpis na **zarządzanie kluczami** strony.

Aby uzyskać szczegółowe informacje dotyczące instalacji, należy użyć **-Verbose** przełącznik z **Add-HybridRunbookWorker**.

#### <a name="5-install-powershell-modules"></a>5. Zainstaluj moduły programu PowerShell

Elementy Runbook można użyć dowolnego działania i poleceń cmdlet zdefiniowane w modułach zainstalowanych w środowisku usługi Automatyzacja Azure. Te moduły nie są automatycznie wdrażane na komputerach lokalnych, więc należy ją zainstalować ręcznie. Wyjątkiem jest moduł Azure, który jest instalowany domyślnie i zapewnia dostęp do poleceń cmdlet dla wszystkich usług platformy Azure i działań automatyzacji Azure.

Ponieważ jest podstawowym celem funkcji hybrydowego procesu roboczego elementu Runbook do zarządzania zasobami lokalnym, najprawdopodobniej musisz zainstalować moduły, które obsługują te zasoby. Aby uzyskać informacje na temat instalowania modułów programu Windows PowerShell, zobacz [instalowanie modułów](http://msdn.microsoft.com/library/dd878350.aspx). 

Moduły, które są zainstalowane musi znajdować się w lokalizacji odwołuje się **PSModulePath** zmiennej środowiskowej, aby hybrydowy proces roboczy mógł automatycznie zaimportować je. Aby uzyskać więcej informacji, zobacz [Modyfikowanie ścieżki instalacji PSModulePath](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx).

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Aby dowiedzieć się, jak rozwiązywać problemy z hybrydowych procesów roboczych elementu Runbook, zobacz [Rozwiązywanie problemów z systemem Windows hybrydowe procesy robocze elementu Runbook](troubleshoot/hybrid-runbook-worker.md#windows)

Dodatkowe kroki dotyczące rozwiązywania problemów z zarządzania aktualizacjami, zobacz [zarządzania aktualizacjami: Rozwiązywanie problemów z](troubleshoot/update-management.md).

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się, jak skonfigurować elementy runbook do automatyzacji procesów w lokalnym centrum danych lub w innym środowisku chmury, zobacz [uruchamiania elementów runbook na hybrydowy proces roboczy elementu Runbook](automation-hrw-run-runbooks.md).
* Aby uzyskać instrukcje dotyczące sposobu usuwania hybrydowych procesów roboczych elementu Runbook, zobacz [Usuń Azure Automation hybrydowe procesy robocze elementu Runbook](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).