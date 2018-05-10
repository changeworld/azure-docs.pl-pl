---
title: Hybrydowy proces roboczy elementu runbook usługi Azure Automation (Windows)
description: Ten artykuł zawiera informacje na temat instalowania Azure hybrydowego procesu roboczego Runbook automatyzacji, który służy do uruchamiania elementów runbook na komputerach z systemem Windows w lokalnym centrum danych lub w środowisku chmury.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 37f0786d1a24316d0a947fbaf89033dcd89918c6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-deploy-a-windows-hybrid-runbook-worker"></a>Wdrażanie systemu Windows hybrydowego Runbook Worker

Funkcja hybrydowy proces roboczy elementu Runbook usługi Automatyzacja Azure umożliwia uruchamianie elementów runbook, bezpośrednio na komputerze hostującym rolę i względem zasobów w środowisku, aby zarządzać tymi zasobów lokalnych. Elementy Runbook są przechowywane i zarządzane w automatyzacji Azure i następnie dostarczany do co najmniej jednym komputerze wyznaczonym. Ten artykuł decribes sposób instalowania hybrydowy proces roboczy elementu Runbook na komputerze z systemem Windows.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Instalowanie systemu Windows hybrydowy proces roboczy

Aby zainstalować i skonfigurować Windows hybrydowego Runbook Worker, istnieją dwie metody dostępne. Zalecaną metodą jest całkowicie zautomatyzować proces, należy skonfigurować komputer z systemem Windows przy użyciu element runbook usługi Automatyzacja. Druga metoda jest po procedurą krok po kroku, aby ręcznie zainstalować i skonfigurować rolę.

> [!NOTE]
> Aby zarządzać konfiguracji serwerów pomocniczych roli hybrydowy proces roboczy elementu Runbook z konfiguracji żądanego stanu (DSC), należy je dodać jako węzły DSC.

Poniżej przedstawiono minimalne wymagania dotyczące systemu Windows hybrydowego Runbook Worker.

* Windows Server 2012 lub nowszym.
* Windows PowerShell 4.0 lub nowszy jest wymagany ([Pobierz WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855)). Windows PowerShell w wersji 5.1 ([Pobierz 5.1 WMF](https://www.microsoft.com/download/details.aspx?id=54616)) jest zalecane w przypadku bardziej niezawodne.
* .NET framework 4.6.2 lub nowszym.
* Co najmniej dwa rdzenie.
* Co najmniej 4 GB pamięci RAM.
* Port 443 (dla ruchu wychodzącego)

Aby wyświetlić dodatkowe wymagania sieciowe związane z hybrydowy proces roboczy elementu Runbook, zobacz [Konfigurowanie sieci](automation-hybrid-runbook-worker.md#network-planning).

Aby uzyskać więcej informacji na temat dołączania ich do zarządzania za pomocą usługi Konfiguracja DSC, zobacz [dołączania komputerów do zarządzania przez Konfiguracja DSC automatyzacji Azure](automation-dsc-onboarding.md).
Po włączeniu [rozwiązania do zarządzania aktualizacjami](../operations-management-suite/oms-solution-update-management.md), dowolnego komputera z systemem Windows, podłączonego do obszaru roboczego analizy dzienników jest automatycznie konfigurowany jako hybrydowy proces roboczy elementu Runbook do obsługi elementy runbook zawarte w tym rozwiązaniu. Jednak nie jest zarejestrowany z wszystkich grup hybrydowy proces roboczy już zdefiniowane na Twoim koncie automatyzacji. Komputer można dodać do grupy hybrydowego procesu roboczego elementu Runbook do Twojego konta automatyzacji do obsługi elementów runbook automatyzacji, tak długo, jak używasz tego samego konta dla rozwiązania i członkostwa w grupie hybrydowy proces roboczy elementu Runbook. Ta funkcjonalność została dodana do wersji 7.2.12024.0 hybrydowego procesu roboczego elementu Runbook.

Po pomyślnym wdrożeniu runbook worker, przejrzyj [uruchamiania elementów runbook na hybrydowy proces roboczy elementu Runbook](automation-hrw-run-runbooks.md) Aby dowiedzieć się, jak skonfigurować elementy runbook do automatyzacji procesów w lokalnym centrum danych lub w innym środowisku chmury.

### <a name="automated-deployment"></a>Wdrożenie zautomatyzowane

Wykonaj poniższe kroki, aby zautomatyzować instalację i konfigurację roli Windows hybrydowy proces roboczy.

1. Pobierz *OnPremiseHybridWorker.ps1 nowy* skrypt z [galerii programu PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/DisplayScript) bezpośrednio z komputera z uruchomionym roli hybrydowy proces roboczy elementu Runbook lub z innego komputera w danym środowisku i skopiować go do pracownika.

   *OnPremiseHybridWorker.ps1 nowy* skrypt wymaga następujących parametrów podczas wykonywania:

   * *AutomationAccountName* (wymagane) — nazwa konta automatyzacji.
   * *AAResourceGroupName* (wymagane) — Nazwa grupy zasobów skojarzonych z Twoim kontem automatyzacji
   * *OMSResourceGroupName* (opcjonalnie) — Nazwa grupy zasobów w obszarze roboczym pakietu OMS. Jeśli nie zostanie określony, używany jest AAResourceGroupName.
   * *HybridGroupName* (wymagane) — Nazwa grupy hybrydowego procesu roboczego elementu Runbook, określony jako element docelowy dla elementów runbook, obsługa tego scenariusza.
   * *Identyfikator subskrypcji* (wymagane) — identyfikator subskrypcji platformy Azure, w których należy konto automatyzacji.
   * *WorkspaceName* (opcjonalnie) — nazwa obszaru roboczego analizy dzienników. Jeśli nie masz obszaru roboczego analizy dzienników, skrypt tworzy i konfiguruje jeden.

     > [!NOTE]
     > Obecnie są tylko regiony automatyzacji obsługiwane integracji z usługą analizy dzienników - **południowo-wschodnia Australia**, **wschodnie stany USA 2**, **Azja południowo-wschodnia**, i  **Europa Zachodnia**. Jeśli Twoje konto usługi Automatyzacja nie jest w jednym z tych regionów, skrypt tworzy obszaru roboczego analizy dzienników, ale jego ostrzega, że go nie może połączyć je razem.

1. Na komputerze, należy uruchomić **programu Windows PowerShell** z **Start** ekranu w trybie administratora.
1. Z poziomu wiersza polecenia powłoki PowerShell, przejdź do folderu, który zawiera skrypt pobrany i wykonaj go zmiana wartości parametrów *- AutomationAccountName*, *- AAResourceGroupName*, *- OMSResourceGroupName*, *- HybridGroupName*, *- SubscriptionId*, i *- WorkspaceName*.

     > [!NOTE]
     > Zostanie wyświetlony monit uwierzytelniania w usłudze Azure po wykonaniu skryptu. Możesz **musi** Zaloguj się przy użyciu konta należącego do roli Administratorzy subskrypcji i współadministratora subskrypcji.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

1. Zostanie wyświetlony monit o zgodę na instalowanie **NuGet** i zostanie wyświetlony monit o uwierzytelniania przy użyciu poświadczeń platformy Azure.

1. Po zakończeniu skrypt strony hybrydowego procesu roboczego grup wyświetli nową grupę i liczby elementów członkowskich lub jeśli istniejącą grupę, do liczby elementów członkowskich jest zwiększany. Można zaznaczyć grupę z listy na **hybrydowego procesu roboczego grupy** i wybrać opcję **hybrydowych procesów roboczych** kafelka. Na **hybrydowych procesów roboczych** strony, zobacz każdego członka grupy na liście.

### <a name="manual-deployment"></a>Ręczne wdrażanie

Raz wykonać pierwsze dwa kroki dla danego środowiska automatyzacji, a następnie powtórz pozostałe kroki na każdym komputerze pracownika.

#### <a name="1-create-log-analytics-workspace"></a>1. Tworzenie obszaru roboczego usługi Log Analytics

Jeśli nie masz już obszaru roboczego analizy dzienników, następnie utwórz ją przy użyciu instrukcji w [Zarządzanie obszaru roboczego](../log-analytics/log-analytics-manage-access.md). Istniejący obszar roboczy można użyć, jeśli już istnieje.

#### <a name="2-add-automation-solution-to-log-analytics-workspace"></a>2. Dodaj rozwiązanie do automatyzacji do obszaru roboczego analizy dzienników

Rozwiązania powodują dodawanie funkcji do usługi Log Analytics. Rozwiązanie Automatyzacja dodaje funkcje na tym obsługę hybrydowy proces roboczy elementu Runbook usługi Automatyzacja Azure. Po dodaniu rozwiązania do swojego obszaru roboczego on automatycznie wypycha dół składników procesu roboczego na komputerze agenta, który zostanie zainstalowany w następnym kroku.

Postępuj zgodnie z instrukcjami w [można dodać za pomocą galerii rozwiązań rozwiązania](../log-analytics/log-analytics-add-solutions.md) można dodać **automatyzacji** rozwiązania do swojego obszaru roboczego analizy dzienników.

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Zainstaluj program Microsoft Monitoring Agent

Microsoft Monitoring Agent łączy się analizy dzienników z komputerów. Po zainstalowaniu agenta na komputerze lokalnym i podłącz go do swojego obszaru roboczego, będzie automatycznie pobierał składniki wymagane dla hybrydowego procesu roboczego elementu Runbook.

Postępuj zgodnie z instrukcjami w [połączyć komputery do analizy dzienników](../log-analytics/log-analytics-windows-agent.md) do zainstalowania agenta na komputerze lokalnym. Można powtórzyć ten proces dla wielu komputerów do dodania wielu pracowników w danym środowisku.

Gdy agent połączył się pomyślnie analizy dzienników, jest wyświetlana na **połączonych źródeł** kartę analizy dzienników **ustawienia** strony. Możesz sprawdzić, czy agent poprawnie pobrał rozwiązania Automatyzacja po folder o nazwie **AzureAutomationFiles** w C:\Program Files\Microsoft Monitoring Agent\Agent. Aby sprawdzić wersję hybrydowy proces roboczy elementu Runbook, można przejść do C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ i zanotuj \\ *wersji* podfolderu.

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Zainstalowania środowiska elementu runbook i nawiązania połączenia usługi Automatyzacja Azure

Po dodaniu agenta do analizy dzienników rozwiązania Automatyzacja umieszcza w dół **HybridRegistration** moduł programu PowerShell, który zawiera **Add-HybridRunbookWorker** polecenia cmdlet. To polecenie cmdlet służy do zainstalowania środowiska elementu runbook na komputerze i zarejestrowania go w usłudze Automatyzacja Azure.

Otwórz sesję programu PowerShell w trybie administratora i uruchom następujące polecenia, aby zaimportować moduł.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module HybridRegistration.psd1
```

Następnie uruchom **Add-HybridRunbookWorker** polecenia cmdlet, używając następującej składni:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Można uzyskać informacji wymaganych dla tego polecenia cmdlet z **zarządzanie kluczami** strony w portalu Azure. Otwórz tę stronę, wybierając **klucze** opcję **ustawienia** strony na Twoim koncie automatyzacji.

![Omówienie procesu roboczego elementu Runbook hybrydowego](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** to nazwa grupy hybrydowych procesów roboczych elementu Runbook. Jeśli ta grupa już istnieje na koncie automatyzacji, bieżący komputer jest dodawany do niego. Jeśli go jeszcze nie istnieje, następnie jest dodawany.
* **Punkt końcowy** jest **adres URL** w **zarządzanie kluczami** strony.
* **Token** jest **podstawowy klucz dostępu** w **zarządzanie kluczami** strony.

Użyj **-Verbose** przełącznik z **Add-HybridRunbookWorker** uzyskać szczegółowe informacje o instalacji.

#### <a name="5-install-powershell-modules"></a>5. Zainstaluj moduły programu PowerShell

Elementy Runbook można użyć dowolnego działania i poleceń cmdlet zdefiniowane w modułach zainstalowanej w środowisku usługi Automatyzacja Azure. Te moduły nie są automatycznie wdrażane na komputerach lokalnych, więc należy ją zainstalować ręcznie. Wyjątkiem jest moduł Azure, który jest instalowany domyślnie, zapewniając dostęp do poleceń cmdlet dla wszystkich usług platformy Azure i działań automatyzacji Azure.

Ponieważ jest podstawowym celem funkcji hybrydowego procesu roboczego elementu Runbook do zarządzania zasobami lokalnym, prawdopodobnie musisz zainstalować moduły, które obsługują te zasoby. Można to sprawdzić [instalowanie modułów](http://msdn.microsoft.com/library/dd878350.aspx) informacje na temat instalowania modułów programu Windows PowerShell. Moduły, które są zainstalowane musi znajdować się w lokalizacji odwołuje PSModulePath zmiennej środowiskowej, dzięki czemu są automatycznie importowane przez hybrydowy proces roboczy. Aby uzyskać więcej informacji, zobacz [Modyfikowanie ścieżki instalacji PSModulePath](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx).

## <a name="next-steps"></a>Następne kroki

* Przegląd [uruchamiania elementów runbook na hybrydowy proces roboczy elementu Runbook](automation-hrw-run-runbooks.md) Aby dowiedzieć się, jak skonfigurować elementy runbook do automatyzacji procesów w lokalnym centrum danych lub w innym środowisku chmury.
* Aby uzyskać instrukcje dotyczące sposobu usuwania hybrydowych procesów roboczych elementu Runbook, zobacz [Usuń Azure Automation hybrydowe procesy robocze elementu Runbook](automation-hybrid-runbook-worker.md#removing-hybrid-runbook-worker)