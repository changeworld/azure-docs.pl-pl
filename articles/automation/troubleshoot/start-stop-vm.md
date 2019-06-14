---
title: Rozwiązywanie problemów, uruchamianie i zatrzymywanie maszyn wirtualnych przy użyciu usługi Azure Automation
description: Ten artykuł zawiera informacje na temat rozwiązywania problemów, uruchamianie i zatrzymywanie maszyn wirtualnych w usłudze Azure Automation
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 03bad12b7fcba5a247e05884aa0eb0493163a5c4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60579001"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Rozwiązywanie problemów z uruchamianie/zatrzymywanie maszyn wirtualnych poza godziny rozwiązania

## <a name="deployment-failure"></a>Scenariusz: Rozwiązanie uruchamianie/zatrzymywanie maszyny Wirtualnej nie powiedzie się prawidłowo wdrażania

### <a name="issue"></a>Problem

W przypadku wdrażania [uruchamianie/zatrzymywanie maszyn wirtualnych poza godziny rozwiązania](../automation-solution-vm-management.md), zostanie wyświetlony jeden z następujących błędów:

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]”.
```

```error
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```error
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```error
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

### <a name="cause"></a>Przyczyna

Wdrażanie może zakończyć się niepowodzeniem z jednego z następujących powodów:

1. Istnieje już konto usługi Automation o tej samej nazwie w wybranego regionu.
2. Zasady zostaną w miejscu, które nie zezwalają na wdrożenie rozwiązania uruchamianie/zatrzymywanie maszyn wirtualnych.
3. `Microsoft.OperationsManagement`, `Microsoft.Insights`, Lub `Microsoft.Automation` typów zasobów nie są zarejestrowane.
4. Obszar roboczy usługi Log Analytics jest zablokowany.

### <a name="resolution"></a>Rozwiązanie

Przejrzyj następującą listę potencjalne rozwiązania problemów lub miejsca do przeszukania:

1. Konta usługi Automation muszą być unikatowe w obrębie regionu Azure, nawet jeśli leżą one w różnych grupach zasobów. Sprawdź istniejące konta usługi Automation, w regionie docelowym.
2. Istniejące zasady zapobiega zasobów, która jest wymagana dla rozwiązania uruchamianie/zatrzymywanie maszyn wirtualnych do wdrożenia. Przejdź do przypisania zasad w witrynie Azure portal i sprawdź, czy masz przypisanie zasad, które nie zezwalają na wdrożenie tego zasobu. Aby dowiedzieć się więcej na ten temat, zobacz [RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md).
3. W celu wdrożenia rozwiązania uruchamianie/zatrzymywanie maszyn wirtualnych, Twoja subskrypcja musi zostać zarejestrowany do następujących przestrzeni nazw zasobów platformy Azure:
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Zobacz, [rozwiązać błędy rejestracji dostawcy zasobów](../../azure-resource-manager/resource-manager-register-provider-errors.md) Aby dowiedzieć się więcej na temat błędów podczas rejestrowania dostawców.
4. Jeśli masz blokadę w obszarze roboczym usługi Log Analytics, przejdź do obszaru roboczego w witrynie Azure portal i Usuń wszystkie blokady dla zasobu.

## <a name="all-vms-fail-to-startstop"></a>Scenariusz: Nie powiodło się uruchomienie/zatrzymanie wszystkich maszyn wirtualnych

### <a name="issue"></a>Problem

Skonfigurowano rozwiązanie uruchamianie/zatrzymywanie maszyn wirtualnych, ale go nie uruchomić lub zatrzymać wszystkie maszyny wirtualne skonfigurowane.

### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany przez jedną z następujących powodów:

1. Harmonogram nie jest poprawnie skonfigurowany.
2. Konto Uruchom jako może nie być poprawnie skonfigurowany
3. Element runbook może mieć napotkasz błędy
4. Maszyny wirtualne zostały wyłączone

### <a name="resolution"></a>Rozwiązanie

Przejrzyj następującą listę potencjalne rozwiązania problemów lub miejsca do przeszukania:

* Sprawdzanie zostało poprawnie skonfigurowane harmonogram dla rozwiązań uruchamianie/zatrzymywanie maszyn wirtualnych. Aby dowiedzieć się, jak skonfigurować harmonogram, zobacz [harmonogramy](../automation-schedules.md) artykułu.

* Sprawdź [zadania strumieni](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) sprawdzić pod kątem błędów. W portalu, przejdź na swoje konto usługi Automation, a następnie wybierz pozycję **zadania** w obszarze **automatyzacji procesów**. Z **zadań** wygląd strony dla zadań z jednego z następujących elementów runbook:

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* Sprawdź swoje [konto Uruchom jako](../manage-runas-account.md) ma odpowiednie uprawnienia do próbujesz uruchamianiem lub zatrzymywaniem maszyn wirtualnych. Aby dowiedzieć się, jak sprawdzić uprawnienia do zasobu, zobacz [Szybki Start: Wyświetlanie ról przypisanych do użytkownika za pomocą witryny Azure portal](../../role-based-access-control/check-access.md). Należy podać identyfikator aplikacji jednostki usługi używany przez konto Uruchom jako. Tę wartość można pobrać, przechodząc do konta usługi Automation w witrynie Azure portal, wybierając **konta Uruchom jako** w obszarze **ustawienia konta** i klikając odpowiednie konto Uruchom jako.

* Maszyny wirtualne nie może być uruchomiony lub zatrzymany, jeśli jest on jawnie wykluczone. Wykluczone maszyn wirtualnych w zestawie w **External_ExcludeVMNames** zmiennej w ramach konta usługi Automation wdrożeniu rozwiązania. Poniższy przykład pokazuje, jak możesz zbadać tę wartość przy użyciu programu PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="some-vms-fail-to-startstop"></a>Scenariusz: Niektóre z moich maszyn wirtualnych nie można uruchomić lub zatrzymać

### <a name="issue"></a>Problem

Skonfigurowano rozwiązanie uruchamianie/zatrzymywanie maszyn wirtualnych, ale go nie uruchomić lub zatrzymać niektóre maszyny wirtualne skonfigurowane.

### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany przez jedną z następujących powodów:

1. Jeśli używasz scenariusza sekwencji, tagu może być brakujące lub nieprawidłowe
2. Maszyna wirtualna może być wyłączony.
3. Konto Uruchom jako może nie mieć wystarczających uprawnień na maszynie Wirtualnej
4. Maszyna wirtualna może mieć coś, co jej uruchamianie lub zatrzymywanie zatrzymana

### <a name="resolution"></a>Rozwiązanie

Przejrzyj następującą listę potencjalne rozwiązania problemów lub miejsca do przeszukania:

* Korzystając z [scenariusza sekwencji](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags) uruchamianie/zatrzymywanie maszyny wirtualnej podczas poza godziny rozwiązania, musisz upewnić się, każda maszyna wirtualna, chcesz, aby rozpocząć lub zatrzymać ma odpowiedniego tagu. Upewnić się, że maszyny wirtualne, które chcesz uruchomić mają `sequencestart` tagu i maszyn wirtualnych, aby zatrzymać `sequencestop` tagu. Tagi oba wymagają dodatnią liczbą całkowitą. Podobnie jak w poniższym przykładzie zapytanie służy do wyszukania wszystkich maszyn wirtualnych z tagami i ich wartości.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Maszyny wirtualne nie może być uruchomiony lub zatrzymany, jeśli jest on jawnie wykluczone. Wykluczone maszyn wirtualnych w zestawie w **External_ExcludeVMNames** zmiennej w ramach konta usługi Automation wdrożeniu rozwiązania. Poniższy przykład pokazuje, jak możesz zbadać tę wartość przy użyciu programu PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Uruchamianie i zatrzymywanie maszyn wirtualnych, konto Uruchom jako dla konta usługi Automation musi mieć odpowiednie uprawnienia do maszyny Wirtualnej. Aby dowiedzieć się, jak sprawdzić uprawnienia do zasobu, zobacz [Szybki Start: Wyświetlanie ról przypisanych do użytkownika za pomocą witryny Azure portal](../../role-based-access-control/check-access.md). Należy podać identyfikator aplikacji jednostki usługi używany przez konto Uruchom jako. Tę wartość można pobrać, przechodząc do konta usługi Automation w witrynie Azure portal, wybierając **konta Uruchom jako** w obszarze **ustawienia konta** i klikając odpowiednie konto Uruchom jako.

* Jeśli maszyna wirtualna ma problem z uruchomienie lub cofnięcie przydziału, to zachowanie może być spowodowane problemu na samej maszyny Wirtualnej. Niektóre przykłady lub potencjalne problemy są, stosowania aktualizacji podczas próby zamknięcia, usługa zawiesza się i nie tylko). Przejdź do zasobu maszyny Wirtualnej, a następnie sprawdź **dzienników aktywności** aby zobaczyć, czy istnieją błędy w dziennikach. Również może próbować zalogować się do maszyny Wirtualnej, aby zobaczyć, czy istnieją błędy w dziennikach zdarzeń. Aby dowiedzieć się więcej na temat rozwiązywania problemów z maszyną Wirtualną, zobacz [Rozwiązywanie problemów z usługi Azure virtual machines](../../virtual-machines/troubleshooting/index.md)

* Sprawdź [zadania strumieni](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) sprawdzić pod kątem błędów. W portalu, przejdź na swoje konto usługi Automation, a następnie wybierz pozycję **zadania** w obszarze **automatyzacji procesów**.

## <a name="custom-runbook"></a>Scenariusz: Moje niestandardowe elementu runbook nie można rozpocząć lub zatrzymać moich maszyn wirtualnych

### <a name="issue"></a>Problem

Zostały utworzone z niestandardowych elementów runbook lub pobrany z galerii programu PowerShell, a nie działa prawidłowo.

### <a name="cause"></a>Przyczyna

Przyczyną błędu może być jedna z wielu czynności. Przejdź do konta usługi Automation w witrynie Azure portal i wybierz pozycję **zadania** w obszarze **automatyzacji procesów**. Z **zadań** strony, poszukaj zadań w elemencie runbook, aby wyświetlić Nieudane wykonanie zadań.

### <a name="resolution"></a>Rozwiązanie

Zaleca się używać [uruchamianie/zatrzymywanie maszyn wirtualnych poza godziny rozwiązania](../automation-solution-vm-management.md) uruchamianie i zatrzymywanie maszyn wirtualnych w usłudze Azure Automation. To rozwiązanie jest tworzone przez firmę Microsoft. Niestandardowe elementy runbook nie są obsługiwane przez firmę Microsoft. Może znaleźć rozwiązania dla Twojego niestandardowego elementu runbook, odwiedzając [Rozwiązywanie problemów z elementu runbook](runbooks.md) artykułu. Ten artykuł zawiera ogólne wskazówki i rozwiązywanie problemów dotyczących elementów runbook wszystkich typów. Sprawdź [zadania strumieni](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) sprawdzić pod kątem błędów. W portalu, przejdź na swoje konto usługi Automation, a następnie wybierz pozycję **zadania** w obszarze **automatyzacji procesów**.

## <a name="dont-start-stop-in-sequence"></a>Scenariusz: Maszyny wirtualne nie uruchomić lub zatrzymać w prawidłowej sekwencji

### <a name="issue"></a>Problem

Maszyny wirtualne, które zostały skonfigurowane w rozwiązaniu nie uruchomić lub zatrzymać w poprawnej kolejności.

### <a name="cause"></a>Przyczyna

Jest to spowodowane przez niepoprawne znakowanie na maszynach wirtualnych.

### <a name="resolution"></a>Rozwiązanie

Wykonaj następujące kroki, aby upewnić się, że rozwiązanie jest poprawnie skonfigurowany.

1. Upewnij się, wszystkie maszyny wirtualne, aby być uruchomiona lub zatrzymana `sequencestart` lub `sequencestop` znacznika, w zależności od konkretnej sytuacji. Te znaczniki wymagają dodatnią liczbę całkowitą jako wartość. Maszyny wirtualne są przetwarzane w kolejności rosnącej według tej wartości.
2. Upewnij się, grupy zasobów dla maszyn wirtualnych ma zostać uruchomiony lub zatrzymany znajdują się w `External_Start_ResourceGroupNames` lub `External_Stop_ResourceGroupNames` zmiennych, w zależności od konkretnej sytuacji.
3. Przetestować zmiany przez wykonanie `SequencedStartStop_Parent` elementu runbook za pomocą parametru WHATIF ustawiona na wartość True, aby wyświetlić podgląd zmian.

Aby uzyskać bardziej szczegółowe i dodatkowe instrukcje dotyczące sposobu rozwiązania umożliwia uruchamianie i zatrzymywanie maszyn wirtualnych w sekwencji, zobacz [uruchamianie/zatrzymywanie maszyn wirtualnych w sekwencji](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags).

## <a name="403"></a>Scenariusz: Zadanie uruchamiania/zatrzymywania maszyny Wirtualnej nie powiedzie się stanem 403 Zabronione 

### <a name="issue"></a>Problem

Znajdź zadania, które nie powiodło się z `403 forbidden` błąd uruchamianie/zatrzymywanie maszyn wirtualnych poza elementów runbook rozwiązań godzin.

### <a name="cause"></a>Przyczyna

Ten problem może być spowodowane nieprawidłowo skonfigurowany lub wygasł kontem Uruchom jako. Może to być również ze względu na niewystarczające uprawnienia do zasobów maszyny Wirtualnej przez konto usługi Automation konta Uruchom jako.

### <a name="resolution"></a>Rozwiązanie

Aby sprawdzić, konta Uruchom jako jest prawidłowo skonfigurowane, przejdź do konta usługi Automation w witrynie Azure portal i wybierz pozycję **konta Uruchom jako** w obszarze **ustawienia konta**. W tym miejscu zostanie wyświetlony stan uruchomienia usługi jako konta, jeśli konto Uruchom jako jest nieprawidłowo skonfigurowany lub wygasł stan będzie wyświetlany jako to.

Jeśli Twoje konto Uruchom jako jest [nieprawidłowo](../manage-runas-account.md#misconfiguration), należy usunąć i ponownie utworzyć Twoje konto Uruchom jako.

Jeśli certyfikat wygasł dla Twojego konta Uruchom jako, wykonaj kroki opisane w temacie [odnawiania certyfikatu z podpisem własnym](../manage-runas-account.md#cert-renewal) Aby odnowić certyfikat.

Ten problem może być spowodowane przez brak uprawnień. Aby dowiedzieć się, jak sprawdzić uprawnienia do zasobu, zobacz [Szybki Start: Wyświetlanie ról przypisanych do użytkownika za pomocą witryny Azure portal](../../role-based-access-control/check-access.md). Należy podać identyfikator aplikacji jednostki usługi używany przez konto Uruchom jako. Tę wartość można pobrać, przechodząc do konta usługi Automation w witrynie Azure portal, wybierając **konta Uruchom jako** w obszarze **ustawienia konta** i klikając odpowiednie konto Uruchom jako.

## <a name="other"></a>Scenariusz: Mojego problemu nie ma na liście powyżej

### <a name="issue"></a>Problem

Występują problemu lub nieoczekiwany wynik podczas za pomocą uruchamianie/zatrzymywanie maszyn wirtualnych podczas rozwiązanie po godzinach pracy, które nie są wymienione na tej stronie.

### <a name="cause"></a>Przyczyna

Wiele razy błędy mogą być spowodowane przy użyciu starego i przestarzałe wersji rozwiązania.

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać wiele błędów, zaleca się usuwanie i aktualizowanie rozwiązania. Aby dowiedzieć się, jak zaktualizować rozwiązania, zobacz [aktualizacji uruchamianie/zatrzymywanie maszyn wirtualnych poza godziny rozwiązania](../automation-solution-vm-management.md#update-the-solution). Ponadto można sprawdzić [zadania strumieni](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) sprawdzić pod kątem błędów. W portalu, przejdź na swoje konto usługi Automation, a następnie wybierz pozycję **zadania** w obszarze **automatyzacji procesów**.

## <a name="next-steps"></a>Kolejne kroki

Jeśli nie był widoczny problemu lub są w stanie rozwiązać problemu, odwiedź jedną z następujących kanałów obsługi więcej:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskiwanie pomocy technicznej**.
