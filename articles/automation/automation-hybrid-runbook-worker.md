---
title: Hybrydowymi elementami roboczymi Runbook usługi Automatyzacja Azure
description: Ten artykuł zawiera informacje o instalowaniu i używaniu hybrydowy proces roboczy elementu Runbook, która jest funkcją automatyzacji Azure, która służy do uruchamiania elementów runbook na maszynach w lokalnym centrum danych lub dostawcy usług w chmurze.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 7065ec97e1e02dfb4ee873993caac584f6a63ba6
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="automate-resources-in-your-data-center-or-cloud-with-hybrid-runbook-worker"></a>Automatyzację zasobów w centrum danych lub w chmurze chronionej za pomocą hybrydowy proces roboczy elementu Runbook

Elementy Runbook automatyzacji Azure nie może uzyskać dostępu zasobów w innych chmur lub w środowisku lokalnym, ponieważ działają w chmurze Azure. Funkcja hybrydowy proces roboczy elementu Runbook usługi Automatyzacja Azure umożliwia uruchamianie elementów runbook, bezpośrednio na komputerze hostującym rolę i względem zasobów w środowisku, aby zarządzać tymi zasobów lokalnych. Elementy Runbook są przechowywane i zarządzane w automatyzacji Azure i następnie dostarczany do co najmniej jednym komputerze wyznaczonym.

Na poniższej ilustracji przedstawiono tę funkcję:

![Omówienie procesu roboczego elementu Runbook hybrydowego](media/automation-hybrid-runbook-worker/automation.png)

Informacje techniczne o kwestiach roli i wdrażania hybrydowego procesu roboczego elementu Runbook, zobacz [Przegląd architektury automatyzacji](automation-offering-get-started.md#automation-architecture-overview).

## <a name="hybrid-runbook-worker-groups"></a>Hybrydowego procesu roboczego elementu Runbook grup

Każdy hybrydowy proces roboczy elementu Runbook jest członkiem grupy hybrydowego procesu roboczego elementu Runbook, którą określono podczas instalacji agenta. Grupy mogą obejmować jednego agenta, ale można zainstalować wielu agentów w grupie wysokiej dostępności.

Po uruchomieniu elementu runbook na hybrydowy proces roboczy elementu Runbook można określić grupy, do której jest uruchamiany na. Członkowie grupy określają, które procesu roboczego obsługującym żądanie. Nie można określić określonego procesu roboczego.

## <a name="relationship-to-service-management-automation"></a>Relacja do automatyzacji zarządzania usługami

[Usługa zarządzania Strukturę automatyzacja](https://technet.microsoft.com/library/dn469260.aspx) służy do uruchamiania tej samej elementów runbook, które są obsługiwane przez usługi Automatyzacja Azure w centrum danych lokalnych. SMA jest wdrażany wraz z dodatkiem Service Pack systemu Windows Azure, zgodnie z interfejsem graficznym do zarządzania SMA zawiera pakietu Windows Azure Pack. W przeciwieństwie do automatyzacji Azure SMA wymaga instalacji lokalnej, zawierające serwerów sieci web do obsługi interfejsu API, bazy danych ma zawierać elementy runbook i SMA konfiguracji i procesy robocze elementu Runbook do wykonania zadania elementu runbook. Automatyzacja Azure udostępnia te usługi w chmurze i wymaga tylko do utrzymania hybrydowych procesów roboczych elementu Runbook w środowisku lokalnym.

W przypadku istniejącego użytkownika SMA można przenosić elementy runbook automatyzacji Azure ma być używany z hybrydowy proces roboczy elementu Runbook bez zmian, przy założeniu, że wykonują uwierzytelniania do zasobów, jak opisano w [uruchamiania elementów runbook na hybrydowy proces roboczy elementu Runbook](automation-hrw-run-runbooks.md). Elementy Runbook w programie SMA działają w kontekście konta usługi na serwerze procesu roboczego, co może zapewnić tym uwierzytelnianie elementów runbook.

Następujące kryteria służy do ustalenia, czy usługi Automatyzacja Azure z hybrydowy proces roboczy elementu Runbook lub Service Management Automation jest bardziej odpowiednie dla wymagań.

* SMA wymaga instalacji lokalnej jego podstawowych składników, które są podłączone do systemu Windows Azure Pack, jeśli wymagany jest interfejs zarządzania w trybie graficznym. Wyższe koszty utrzymania niż Automatyzacja Azure, w którym agent został zainstalowany na lokalnym programów runbook Worker musi tylko są potrzeby więcej zasobów lokalnych. Agenci są zarządzane przez platformę Azure, dalsze zmniejszenie kosztów obsługi.
* Automatyzacja Azure w chmurze są przechowywane jego elementy runbook i dostarcza je do lokalnej hybrydowymi elementami roboczymi Runbook. Zasady zabezpieczeń nie zezwalają na to zachowanie, należy użyć SMA.
* SMA jest dołączony do programu System Center; i dlatego wymaga licencji programu System Center 2012 R2. Automatyzacja Azure jest oparta na modelu warstwowych subskrypcji.
* Automatyzacja Azure udostępnia zaawansowane funkcje, takie jak graficznych elementów runbook, które nie są dostępne w programie SMA.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Instalowanie systemu Windows hybrydowy proces roboczy

Aby zainstalować i skonfigurować Windows hybrydowego Runbook Worker, istnieją dwie metody dostępne. Zalecaną metodą jest całkowicie zautomatyzować proces, należy skonfigurować komputer z systemem Windows przy użyciu element runbook usługi Automatyzacja. Druga metoda jest po procedurą krok po kroku, aby ręcznie zainstalować i skonfigurować rolę.

> [!NOTE]
> Aby zarządzać konfiguracji serwerów pomocniczych roli hybrydowy proces roboczy elementu Runbook z konfiguracji żądanego stanu (DSC), należy je dodać jako węzły DSC. Aby uzyskać więcej informacji na temat dołączania ich do zarządzania za pomocą usługi Konfiguracja DSC, zobacz [dołączania komputerów do zarządzania przez Konfiguracja DSC automatyzacji Azure](automation-dsc-onboarding.md).
>
>Po włączeniu [rozwiązania do zarządzania aktualizacjami](../operations-management-suite/oms-solution-update-management.md), dowolnego komputera z systemem Windows, podłączonego do obszaru roboczego analizy dzienników jest automatycznie konfigurowany jako hybrydowy proces roboczy elementu Runbook do obsługi elementy runbook zawarte w tym rozwiązaniu. Jednak nie jest zarejestrowany z wszystkich grup hybrydowy proces roboczy już zdefiniowane na Twoim koncie automatyzacji. Komputer można dodać do grupy hybrydowego procesu roboczego elementu Runbook do Twojego konta automatyzacji do obsługi elementów runbook automatyzacji, tak długo, jak używasz tego samego konta dla rozwiązania i członkostwa w grupie hybrydowy proces roboczy elementu Runbook. Ta funkcjonalność została dodana do wersji 7.2.12024.0 hybrydowego procesu roboczego elementu Runbook.

Przejrzyj następujące informacje dotyczące [wymagania sprzętowe i programowe](automation-offering-get-started.md#hybrid-runbook-worker) i [informacji do przygotowania sieci](automation-offering-get-started.md#network-planning) przed rozpoczęciem wdrażania hybrydowego procesu roboczego elementu Runbook. Po pomyślnym wdrożeniu runbook worker, przejrzyj [uruchamiania elementów runbook na hybrydowy proces roboczy elementu Runbook](automation-hrw-run-runbooks.md) Aby dowiedzieć się, jak skonfigurować elementy runbook do automatyzacji procesów w lokalnym centrum danych lub w innym środowisku chmury.

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

2. Na komputerze, należy uruchomić **programu Windows PowerShell** z **Start** ekranu w trybie administratora.
3. Z poziomu wiersza polecenia powłoki PowerShell, przejdź do folderu, który zawiera skrypt pobrany i wykonaj go zmiana wartości parametrów *- AutomationAccountName*, *- AAResourceGroupName*, *- OMSResourceGroupName*, *- HybridGroupName*, *- SubscriptionId*, i *- WorkspaceName*.

     > [!NOTE]
     > Zostanie wyświetlony monit uwierzytelniania w usłudze Azure po wykonaniu skryptu. Możesz **musi** Zaloguj się przy użyciu konta należącego do roli Administratorzy subskrypcji i współadministratora subskrypcji.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

4. Zostanie wyświetlony monit o zgodę na instalowanie **NuGet** i zostanie wyświetlony monit o uwierzytelniania przy użyciu poświadczeń platformy Azure.

5. Po zakończeniu skrypt bloku hybrydowego procesu roboczego grupy zostaną wyświetlone nowe grupy i liczby elementów członkowskich lub jeśli istniejącą grupę, do liczby elementów członkowskich jest zwiększany. Można zaznaczyć grupę z listy na **hybrydowego procesu roboczego grupy** bloku, a następnie wybierz **hybrydowych procesów roboczych** kafelka. Na **hybrydowych procesów roboczych** bloku, zobacz każdego członka grupy na liście.

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

Możesz sprawdzić, czy agent poprawnie pobrał rozwiązania Automatyzacja po folder o nazwie **AzureAutomationFiles** w C:\Program Files\Microsoft Monitoring Agent\Agent. Aby sprawdzić wersję hybrydowy proces roboczy elementu Runbook, można przejść do C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ i zanotuj \\ *wersji* podfolderu.  

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Zainstalowania środowiska elementu runbook i nawiązania połączenia usługi Automatyzacja Azure

Po dodaniu agenta do analizy dzienników rozwiązania Automatyzacja umieszcza w dół **HybridRegistration** moduł programu PowerShell, który zawiera **Add-HybridRunbookWorker** polecenia cmdlet. To polecenie cmdlet służy do zainstalowania środowiska elementu runbook na komputerze i zarejestrowania go w usłudze Automatyzacja Azure.

Aby zaimportować moduł, otwórz sesję programu PowerShell w trybie administratora i uruchom następujące polecenia:

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module HybridRegistration.psd1
```

Następnie uruchom **Add-HybridRunbookWorker** polecenia cmdlet, używając następującej składni:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Można uzyskać informacji wymaganych dla tego polecenia cmdlet, klikając **klucze** opcję w obszarze **ustawienia konta** na Twoim koncie automatyzacji.

* **GroupName** to nazwa grupy hybrydowych procesów roboczych elementu Runbook. Jeśli ta grupa już istnieje na koncie automatyzacji, bieżący komputer jest dodawany do niego. Jeśli go jeszcze nie istnieje, następnie jest dodawany.
* **Punkt końcowy** jest **adres URL** w **klucze** strony.
* **Token** jest **podstawowy klucz dostępu** w **klucze** strony.

Użyj **-Verbose** przełącznik z **Add-HybridRunbookWorker** uzyskać szczegółowe informacje o instalacji.

#### <a name="5-install-powershell-modules"></a>5. Zainstaluj moduły programu PowerShell

Elementy Runbook można użyć dowolnego działania i poleceń cmdlet zdefiniowane w modułach zainstalowanej w środowisku usługi Automatyzacja Azure. Te moduły nie są automatycznie wdrażane na komputerach lokalnych, więc należy ją zainstalować ręcznie. Wyjątkiem jest moduł Azure, który jest instalowany domyślnie, zapewniając dostęp do poleceń cmdlet dla wszystkich usług platformy Azure i działań automatyzacji Azure.

Ponieważ jest podstawowym celem funkcji hybrydowego procesu roboczego elementu Runbook do zarządzania zasobami lokalnym, prawdopodobnie musisz zainstalować moduły, które obsługują te zasoby. Można to sprawdzić [instalowanie modułów](http://msdn.microsoft.com/library/dd878350.aspx) informacje na temat instalowania modułów programu Windows PowerShell. Moduły, które są zainstalowane musi znajdować się w lokalizacji odwołuje PSModulePath zmiennej środowiskowej, dzięki czemu są automatycznie importowane przez hybrydowy proces roboczy. Aby uzyskać więcej informacji, zobacz [Modyfikowanie ścieżki instalacji PSModulePath](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx).

## <a name="removing-hybrid-runbook-worker"></a>Usuwanie hybrydowy proces roboczy elementu Runbook

Co najmniej jeden hybrydowymi elementami roboczymi Runbook można usunąć z grupy lub usunąć grupy, w zależności od wymagań. Aby usunąć hybrydowy proces roboczy elementu Runbook z komputera lokalnego, wykonaj następujące czynności:

1. W portalu Azure przejdź do swojego konta automatyzacji.
2. Z **ustawienia** bloku, wybierz opcję **klucze** i zanotuj wartości dla pola **adres URL** i **podstawowy klucz dostępu**. Te informacje są niezbędne do następnego kroku.
3. Otwórz sesję programu PowerShell w trybie administratora i uruchom następujące polecenie: `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`. Użyj **-Verbose** przełączać szczegółowy dziennik proces usuwania.

Aby usunąć stare maszyn z grupy hybrydowego procesu roboczego, Użyj opcjonalnego `machineName` parametru.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

> [!NOTE]
> Nie powoduje usunięcia programu Microsoft Monitoring Agent z komputera, tylko funkcje i konfiguracji roli hybrydowy proces roboczy elementu Runbook.

## <a name="remove-hybrid-worker-groups"></a>Usuń grupy hybrydowego procesu roboczego

Aby usunąć grupę, należy najpierw usunąć hybrydowy proces roboczy elementu Runbook z każdego komputera, który jest członkiem grupy, korzystając z procedury przedstawionej wcześniej, a następnie wykonaj następujące kroki, aby usunąć grupę.

1. Otwórz konto usługi Automatyzacja w portalu Azure.
1. W obszarze **automatyzacji procesu**, wybierz pozycję **hybrydowego procesu roboczego grupy**. Wybierz grupę, którą chcesz usunąć. Po wybraniu określonej grupy, **grupy hybrydowych procesów roboczych** bloku właściwości jest wyświetlany.

   ![Blok grupy hybrydowych procesów roboczych elementu Runbook](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. W bloku właściwości wybranej grupy, kliknij **usunąć**. Zostanie wyświetlony komunikat prośbą o potwierdzenie tej akcji, wybierz **tak** czy na pewno chcesz kontynuować.

   ![Okno dialogowe potwierdzenia usunięcia grupy](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Ten proces może potrwać kilka sekund. Możesz śledzić postęp w sekcji **Powiadomienia** z poziomu menu.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Hybrydowy proces roboczy elementu Runbook jest zależna od programu Microsoft Monitoring Agent, aby komunikować się z Twoim kontem automatyzacji Zarejestruj pracownika, otrzymywanie zadania elementów runbook i zgłoszenia stanu. W przypadku niepowodzenia rejestracji pracownika poniżej przedstawiono niektóre możliwe przyczyny błędu:

1. Hybrydowy proces roboczy jest związany z serwera proxy lub zapory.

   Sprawdź, czy komputer ma dostęp ruchu wychodzącego do *.azure automation.net na porcie 443.

2. Hybrydowy proces roboczy jest uruchomiona na komputerze są mniejsze niż minimalne wymagania dotyczące sprzętu [wymagania](automation-offering-get-started.md#hybrid-runbook-worker).

   Komputery z systemem hybrydowy proces roboczy elementu Runbook powinna spełniać minimalne wymagania sprzętowe przed oznaczeniem go do obsługi tej funkcji. W przeciwnym razie w zależności od innych procesów w tle i rywalizacji spowodowane przez elementy runbook podczas wykonywania na wykorzystanie zasobów komputera staje się nadmiernie wykorzystywany i spowodować opóźnienia zadania elementu runbook i przekroczeń limitu czasu.

   Upewnij się, że komputer wyznaczony do uruchomienia funkcji hybrydowego procesu roboczego elementu Runbook spełnia minimalne wymagania sprzętowe. Jeśli tak, monitorowanie wykorzystania procesora CPU i pamięci, aby określić korelacja wydajności procesów hybrydowy proces roboczy elementu Runbook i Windows. Brak pamięci lub dużego wykorzystania procesora CPU, może to oznaczać konieczność uaktualnienia lub dodać dodatkowych procesorów lub zwiększ ilość pamięci do adresów wąskie gardło zasobów i Usuń przyczynę błędu. Opcjonalnie zaznacz zasób różnych obliczeń, który może obsługiwać minimalne wymagania i skalowania, gdy wymaganego obciążenia wskazywać wzrost jest konieczne.

3. Usługa Microsoft Monitoring Agent nie jest uruchomiona.

   Jeśli usługa Microsoft Monitoring Agent Windows nie jest uruchomiona, zapobiega to hybrydowy proces roboczy elementu Runbook komunikację z usługi Automatyzacja Azure. Sprawdź, agent nie działa, wprowadzając następujące polecenie w programie PowerShell: `get-service healthservice`. Jeśli usługa zostanie zatrzymana, wprowadź następujące polecenie w programie PowerShell, aby uruchomić usługę: `start-service healthservice`.

4. W **aplikacji i usług Menedżera Logs\Operations** dziennika zdarzeń, zobacz zdarzenia 4502 i zawierający EventMessage **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**z następujący opis: *certyfikat przedstawiony przez usługę \<wsid\>. oms.opinsights.azure.com nie został wystawiony przez urząd certyfikacji używany dla usług firmy Microsoft. Skontaktuj się z administratorem sieci, aby sprawdzić, czy działają z serwera proxy przechwytującego komunikację TLS/SSL. Artykuł KB3126513 zawiera dodatkowe informacje dotyczące rozwiązywania problemów, które występują problemy dotyczące połączenia.*
    Może to być spowodowane serwera proxy lub sieci Zapora blokuje komunikację do systemu Microsoft Azure. Sprawdź, czy komputer ma dostęp ruchu wychodzącego do *.azure automation.net na porty 443.

Dzienniki są przechowywane lokalnie na każdym hybrydowy proces roboczy na C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Można sprawdzić, czy istnieją ostrzeżenia lub błędu zdarzeń zapisywanych w **aplikacji i usług Logs\Microsoft-SMA\Operations** i **aplikacji i usług Menedżera Logs\Operations** dziennika zdarzeń, który wskazuje z łącznością lub innego problemu dołączania roli do automatyzacji Azure lub problem podczas wykonywania normalnych operacji.

## <a name="next-steps"></a>Kolejne kroki

Przegląd [uruchamiania elementów runbook na hybrydowy proces roboczy elementu Runbook](automation-hrw-run-runbooks.md) Aby dowiedzieć się, jak skonfigurować elementy runbook do automatyzacji procesów w lokalnym centrum danych lub w innym środowisku chmury.
