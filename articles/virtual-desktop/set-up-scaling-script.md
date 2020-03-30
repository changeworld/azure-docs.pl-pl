---
title: Skalowanie sesji hostuje usługę Azure Automation — Azure
description: Jak automatycznie skalować hosty sesji pulpitu wirtualnego systemu Windows za pomocą usługi Azure Automation.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3a853dc32f8716f3f2ba32896a7a4a239efcc5bd
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349881"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Skalowanie hostów sesji przy użyciu usługi Azure Automation

Całkowity koszt wdrożenia pulpitu wirtualnego systemu Windows można zmniejszyć, skalując maszyny wirtualne. Oznacza to zamykanie i rozdzielanie maszyn wirtualnych hosta sesji poza godzinami szczytu, a następnie włączanie ich i ponowne przydzielanie ich w godzinach szczytu.

W tym artykule dowiesz się o narzędziu skalowania utworzonym za pomocą usługi Azure Automation i usługi Azure Logic Apps, które automatycznie skaluje maszyny wirtualne hosta sesji w środowisku pulpitu wirtualnego systemu Windows. Aby dowiedzieć się, jak korzystać z narzędzia skalowania, przejdź do [wymagań wstępnych](#prerequisites).

## <a name="how-the-scaling-tool-works"></a>Jak działa narzędzie skalowania

Narzędzie skalowania zapewnia opcję automatyzacji o niskich kosztach dla klientów, którzy chcą zoptymalizować koszty maszyny wirtualnej hosta sesji.

Narzędzia skalowania można użyć do:
 
- Zaplanuj uruchamianie i zatrzymywania maszyn wirtualnych w zależności od godzin pracy szczytowych i poza szczytem.
- Skalowanie w poziomie maszyn wirtualnych na podstawie liczby sesji na rdzeń procesora CPU.
- Skalowanie w maszynach wirtualnych w godzinach poza szczytem, pozostawiając minimalną liczbę maszyn wirtualnych hosta sesji uruchomionych.

Narzędzie skalowania używa kombinacji elementów runbook programu Azure Automation PowerShell, elementów webhook i aplikacji logiki Azure Logic Apps do działania. Po uruchomieniu narzędzia usługa Azure Logic Apps wywołuje element webhook, aby uruchomić element runbook usługi Azure Automation. Element runbook następnie tworzy zadanie.

W szczytowym czasie użycia zadanie sprawdza bieżącą liczbę sesji i pojemność maszyny Wirtualnej bieżącego uruchomionego hosta sesji dla każdej puli hostów. Używa tych informacji do obliczenia, czy uruchomione maszyny wirtualne hosta sesji mogą obsługiwać istniejące sesje na podstawie *parametru SessionThresholdPerCPU* zdefiniowanego dla pliku **createazurelogicapp.ps1.** Jeśli maszyny wirtualne hosta sesji nie mogą obsługiwać istniejących sesji, zadanie uruchamia dodatkowe maszyny wirtualne hosta sesji w puli hostów.

>[!NOTE]
>*SessionThresholdPerCPU* nie ogranicza liczby sesji na maszynie Wirtualnej. Ten parametr określa tylko, kiedy nowe maszyny wirtualne muszą zostać uruchomione, aby zrównoważyć obciążenia połączeń. Aby ograniczyć liczbę sesji, należy postępować zgodnie z instrukcjami [Set-RdsHostPool,](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) aby odpowiednio skonfigurować parametr *MaxSessionLimit.*

W czasie użycia poza szczytem zadanie określa, które maszyny wirtualne hosta sesji powinny zostać zamknięte na podstawie *parametru MinimumNumberOfRDSH.* Zadanie ustawi maszyny wirtualne hosta sesji do trybu opróżniania, aby zapobiec nowym sesjam łączącym się z hostami. Jeśli ustawisz *parametr LimitSecondsToForceLogOffUser* na wartość dodatnią niezerową, zadanie powiadomi wszystkich aktualnie zalogowanych użytkowników, aby zapisali swoją pracę, odczekali skonfigurowaną ilość czasu, a następnie wymusili na użytkownikach wylogowanie się. Po wylogowanie wszystkich sesji użytkownika na maszynie wirtualnej hosta sesji zadanie zostanie zamknięte na maszynie Wirtualnej.

Jeśli ustawisz *parametr LimitSecondsToForceLogOffUser* na zero, zadanie zezwoli na ustawienie konfiguracji sesji w określonych zasadach grupy do obsługi podpisywania sesji użytkownika. Aby wyświetlić te zasady grupy, przejdź do **zasady konfiguracji** > **Policies** > komputera**Szablony administracyjne** > **Windows Składniki** > **Terminale Serwera** > **terminali** > **limity czasu sesji**. Jeśli istnieją aktywne sesje na maszynie wirtualnej hosta sesji, zadanie spowoduje pozostawienie uruchomionej maszyny wirtualnej hosta sesji. Jeśli nie ma żadnych aktywnych sesji, zadanie zostanie zamknięte na maszynie wirtualnej hosta sesji.

Zadanie jest uruchamiane okresowo na podstawie ustawionego interwału cyklu. Ten interwał można zmienić w zależności od rozmiaru środowiska pulpitu wirtualnego systemu Windows, ale należy pamiętać, że uruchamianie i zamykanie maszyn wirtualnych może zająć trochę czasu, więc pamiętaj, aby uwzględnić opóźnienie. Zalecamy ustawienie interwału cyklu na co 15 minut.

Jednak narzędzie ma również następujące ograniczenia:

- To rozwiązanie dotyczy tylko łączonych maszyn wirtualnych hosta sesji.
- To rozwiązanie zarządza maszynami wirtualnymi w dowolnym regionie, ale może być używane tylko w tej samej subskrypcji co konto usługi Azure Automation i usługi Azure Logic Apps.

>[!NOTE]
>Narzędzie skalowania steruje trybem równoważenia obciążenia puli hostów, który jest skalujący. Ustawia go do równoważenia obciążenia pierwszego szerokości zarówno w godzinach szczytu, jak i poza szczytem.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem konfigurowania narzędzia skalowania upewnij się, że masz gotowe następujące czynności:

- [Dzierżawa i host pulpitu wirtualnego systemu Windows](create-host-pools-arm-template.md)
- Maszyny wirtualne z puli hostów sesji skonfigurowane i zarejestrowane w usłudze pulpitu wirtualnego systemu Windows
- Użytkownik z [dostępem współautora w](../role-based-access-control/role-assignments-portal.md) ramach subskrypcji platformy Azure

Maszyna używana do wdrażania narzędzia musi mieć: 

- Program Windows PowerShell 5.1 lub nowszy
- Moduł Microsoft Az PowerShell

Jeśli masz wszystko gotowe, zacznijmy.

## <a name="create-an-azure-automation-account"></a>Tworzenie konta usługi Azure Automation

Najpierw musisz konto usługi Azure Automation do uruchomienia systemu runbook programu PowerShell. Aby skonfigurować konto:

1. Uruchom program Windows PowerShell jako administrator.
2. Uruchom następujące polecenie cmdlet, aby zalogować się do konta platformy Azure.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >Twoje konto musi mieć prawa współautora w ramach subskrypcji platformy Azure, na której chcesz wdrożyć narzędzie skalowania.

3. Uruchom następujące polecenie cmdlet, aby pobrać skrypt do tworzenia konta usługi Azure Automation:

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. Uruchom następujące polecenie cmdlet, aby wykonać skrypt i utworzyć konto usługi Azure Automation:

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. Dane wyjściowe polecenia cmdlet będą zawierać identyfikator URI elementu webhook. Upewnij się, aby zachować rekord identyfikatora URI, ponieważ użyjesz go jako parametru podczas konfigurowania harmonogramu wykonywania dla aplikacji logiki Azure.

6. Po skonfigurowaniu konta usługi Azure Automation zaloguj się do subskrypcji platformy Azure i sprawdź, czy twoje konto usługi Azure Automation i odpowiedni element runbook pojawiły się w określonej grupie zasobów, jak pokazano na poniższej ilustracji:

   ![Obraz strony przegląd platformy Azure przedstawiający nowo utworzone konto automatyzacji i element runbook.](media/automation-account.png)

  Aby sprawdzić, czy element webhook znajduje się tam, gdzie powinien być, wybierz nazwę elementu runbook. Następnie przejdź do sekcji Zasoby elementu runbook i wybierz pozycję **Webhooks**.

## <a name="create-an-azure-automation-run-as-account"></a>Tworzenie konta usługi Azure Automation Run As

Teraz, gdy masz konto usługi Azure Automation, musisz również utworzyć konto usługi Azure Automation Run As, aby uzyskać dostęp do zasobów platformy Azure.

[Konto Azure Automation Run As](../automation/manage-runas-account.md) zapewnia uwierzytelnianie do zarządzania zasobami na platformie Azure za pomocą poleceń cmdlet platformy Azure. Podczas tworzenia konta Uruchom jako tworzy nowego użytkownika jednostki usługi w usłudze Azure Active Directory i przypisuje rolę współautora do głównego użytkownika usługi na poziomie subskrypcji, usługa Azure Uruchom jako konto to świetny sposób bezpiecznego uwierzytelniania za pomocą certyfikaty i nazwę jednostki usługi bez konieczności przechowywania nazwy użytkownika i hasła w obiekcie poświadczeń. Aby dowiedzieć się więcej o uruchamianiu jako uwierzytelnianiu, zobacz [Ograniczanie uprawnień uruchamiania jako konta](../automation/manage-runas-account.md#limiting-run-as-account-permissions).

Każdy użytkownik, który jest członkiem roli Administratorzy subskrypcji i współadministrator subskrypcji można utworzyć konto Uruchom jako, postępując zgodnie z instrukcjami następnej sekcji.

Aby utworzyć konto Uruchom jako na koncie platformy Azure:

1. W witrynie Azure portal wybierz pozycję **Wszystkie usługi**. Na liście zasobów wprowadź i wybierz pozycję **Konta automatyzacji**.

2. Na stronie **Konta automatyzacji** wybierz nazwę konta automatyzacji.

3. W okienku po lewej stronie okna wybierz pozycję **Uruchom jako konta** w sekcji Ustawienia kont.

4. Wybierz **pozycję Azure Uruchom jako konto**. Po **wyświetleniu** okienka Dodaj usługę Azure Uruchom jako konto przejrzyj informacje omówienia, a następnie wybierz pozycję **Utwórz,** aby rozpocząć proces tworzenia konta.

5. Poczekaj kilka minut, aż platforma Azure utworzy konto Uruchom jako. Postęp tworzenia można śledzić w menu w obszarze Powiadomienia.

6. Po zakończeniu procesu utworzy zasób o nazwie AzureRunAsConnection na określonym koncie automatyzacji. Zasób połączenia przechowuje identyfikator aplikacji, identyfikator dzierżawy, identyfikator subskrypcji i odcisk palca certyfikatu. Zapamiętaj identyfikator aplikacji, ponieważ użyjesz go później.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Tworzenie przypisania roli na pulpicie wirtualnym systemu Windows

Następnie należy utworzyć przypisanie roli, aby usługa AzureRunAsConnection mogła wchodzić w interakcje z pulpitem wirtualnym systemu Windows. Upewnij się, że używasz programu PowerShell do logowania się przy użyciu konta, które ma uprawnienia do tworzenia przypisań ról.

Najpierw pobierz i zaimportuj [moduł programu Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) do użycia w sesji programu PowerShell, jeśli jeszcze tego nie zrobiłeś. Uruchom następujące polecenia cmdlet programu PowerShell, aby połączyć się z pulpitem wirtualnym systemu Windows i wyświetlić dzierżawców.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

Po znalezieniu dzierżawy z pulami hostów, które chcesz skalować, postępuj zgodnie z instrukcjami w [obszarze Tworzenie konta usługi Azure Automation](#create-an-azure-automation-account) i użyj nazwy dzierżawy, którą otrzymałeś z poprzedniego polecenia cmdlet w następującym połączeniu cmdlet, aby utworzyć przypisanie roli:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Tworzenie aplikacji logiki platformy Azure i harmonogram wykonywania

Na koniec musisz utworzyć aplikację logiki azure i skonfigurować harmonogram wykonywania dla nowego narzędzia skalowania.

1.  Otwieranie programu Windows PowerShell jako administrator

2.  Uruchom następujące polecenie cmdlet, aby zalogować się do konta platformy Azure.

     ```powershell
     Login-AzAccount
     ```

3. Uruchom następujące polecenie cmdlet, aby pobrać plik skryptu createazurelogicapp.ps1 na komputerze lokalnym.

     ```powershell
     Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1" -OutFile "your local machine path\ createazurelogicapp.ps1"
     ```

4. Uruchom następujące polecenie cmdlet, aby zalogować się do pulpitu wirtualnego systemu Windows przy za pomocą konta z uprawnieniami właściciela usług PULPITU ZDALNEGO lub współautora usług pulpitu zdalnego.

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. Uruchom następujący skrypt programu PowerShell, aby utworzyć aplikację logiki Azure i harmonogram wykonywania.

     ```powershell
     $resourceGroupName = Read-Host -Prompt "Enter the name of the resource group for the new Azure Logic App"
     
     $aadTenantId = Read-Host -Prompt "Enter your Azure AD tenant ID"

     $subscriptionId = Read-Host -Prompt "Enter your Azure Subscription ID"

     $tenantName = Read-Host -Prompt "Enter the name of your WVD tenant"

     $hostPoolName = Read-Host -Prompt "Enter the name of the host pool you'd like to scale"

     $recurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"

     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"

     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"

     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"

     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"

     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"

     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"

     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"

     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

     $location = Read-Host -Prompt "Enter the name of the Azure region where you will be creating the logic app"

     $connectionAssetName = Read-Host -Prompt "Enter the name of the Azure RunAs connection asset"

     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"

     $automationAccountName = Read-Host -Prompt "Enter the name of the Azure Automation Account"

     $maintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"

     .\createazurelogicapp.ps1 -ResourceGroupName $resourceGroupName `
       -AADTenantID $aadTenantId `
       -SubscriptionID $subscriptionId `
       -TenantName $tenantName `
       -HostPoolName $hostPoolName `
       -RecurrenceInterval $recurrenceInterval `
       -BeginPeakTime $beginPeakTime `
       -EndPeakTime $endPeakTime `
       -TimeDifference $timeDifference `
       -SessionThresholdPerCPU $sessionThresholdPerCPU `
       -MinimumNumberOfRDSH $minimumNumberOfRdsh `
       -LimitSecondsToForceLogOffUser $limitSecondsToForceLogOffUser `
       -LogOffMessageTitle $logOffMessageTitle `
       -LogOffMessageBody $logOffMessageBody `
       -Location $location `
       -ConnectionAssetName $connectionAssetName `
       -WebHookURI $webHookURI `
       -AutomationAccountName $automationAccountName `
       -MaintenanceTagName $maintenanceTagName
     ```

     Po uruchomieniu skryptu aplikacja logiki powinna pojawić się w grupie zasobów, jak pokazano na poniższej ilustracji.

     ![Obraz strony przeglądu dla przykładowej aplikacji logiki Azure.](media/logic-app.png)

Aby wprowadzić zmiany w harmonogramie wykonywania, takie jak zmiana interwału cyklu lub strefy czasowej, przejdź do harmonogramu skalowania automatycznego i wybierz pozycję **Edytuj,** aby przejść do Projektanta aplikacji logiki.

![Obraz projektanta aplikacji logiki. Menu cyklu i elementu webhook, które umożliwiają użytkownikowi edytowanie czasów cyklu i pliku elementu webhook są otwarte.](media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Zarządzanie narzędziem skalowania

Po utworzeniu narzędzia skalowania można uzyskać dostęp do jego danych wyjściowych. W tej sekcji opisano kilka funkcji, które mogą okazać się pomocne.

### <a name="view-job-status"></a>Wyświetlanie stanu zadania

Można wyświetlić stan podsumowania wszystkich zadań uruchomieniu lub wyświetlić bardziej szczegółowy stan określonego zadania umowości w witrynie Azure portal.

Po prawej stronie wybranego konta automatyzacji w obszarze "Statystyki zadań" można wyświetlić listę podsumowań wszystkich zadań elementu runbook. Otwarcie strony **Zadania** po lewej stronie okna zawiera aktualne stany zadań, godziny rozpoczęcia i godziny ukończenia.

![Zrzut ekranu strony stanu zadania.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Wyświetlanie dzienników i skalowanie danych wyjściowych narzędzia

Można wyświetlić dzienniki operacji skalowania w poziomie i skalowania w, otwierając swój projekt runbook i wybierając nazwę zadania.

Przejdź do systemu runbook (domyślną nazwą jest WVDAutoScaleRunbook) w grupie zasobów obsługującej konto usługi Azure Automation i wybierz **opcję Przegląd**. Na stronie przegląd wybierz zadanie w obszarze Ostatnie zadania, aby wyświetlić jego dane wyjściowe narzędzia skalowania, jak pokazano na poniższej ilustracji.

![Obraz okna danych wyjściowych dla narzędzia skalowania.](media/tool-output.png)

## <a name="report-issues"></a>Zgłaszanie problemów

Jeśli wystąpią jakiekolwiek problemy z narzędziem skalowania, możesz je zgłosić na [stronie RDS GitHub](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps).
