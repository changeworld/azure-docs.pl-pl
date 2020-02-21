---
title: Rozwiązywanie problemów z uruchamianiem i zatrzymywaniem maszyn wirtualnych — Azure Automation
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z uruchamianiem i zatrzymywaniem maszyn wirtualnych w programie Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 44de5878dcc39e09adf24f69b883a29370f00b48
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505729"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Rozwiązywanie problemów dotyczących uruchamiania/zatrzymywania maszyn wirtualnych poza godzinami

## <a name="deployment-failure"></a>Scenariusz: rozwiązanie uruchamiania/zatrzymywania maszyny wirtualnej nie zostało prawidłowo wdrożone

### <a name="issue"></a>Problem

Podczas wdrażania [maszyny wirtualnej uruchamiania/zatrzymywania w trakcie](../automation-solution-vm-management.md)korzystania z rozwiązania w ciągu kilku godzin otrzymujesz jeden z następujących błędów:

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]".
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

```error
A parameter cannot be found that matches parameter name 'TagName'
```

```error
Start-AzureRmVm : Run Login-AzureRmAccount to login
```

### <a name="cause"></a>Przyczyna

Wdrożenia mogą zakończyć się niepowodzeniem z jednego z następujących powodów:

1. W wybranym regionie istnieje już konto usługi Automation o tej samej nazwie.
2. Stosowane są zasady, które uniemożliwiają wdrożenie rozwiązania uruchamiania/zatrzymywania maszyn wirtualnych.
3. Typy zasobów `Microsoft.OperationsManagement`, `Microsoft.Insights`lub `Microsoft.Automation` nie są zarejestrowane.
4. Obszar roboczy Log Analytics ma blokadę.
5. Masz nieaktualną wersję modułów AzureRM lub rozwiązanie uruchamiania/zatrzymywania.

### <a name="resolution"></a>Rozwiązanie

Zapoznaj się z poniższą listą, aby zapoznać się z potencjalnymi rozwiązaniami problemu lub miejscami, aby

1. Konta usługi Automation muszą być unikatowe w obrębie regionu świadczenia usługi Azure, nawet jeśli znajdują się w różnych grupach zasobów. Sprawdź istniejące konta usługi Automation w regionie docelowym.
2. Istniejące zasady uniemożliwiają wdrożenie zasobu, który jest wymagany dla rozwiązania uruchamiania/zatrzymywania maszyny wirtualnej. Przejdź do przypisań zasad w Azure Portal i sprawdź, czy masz przypisanie zasad, które uniemożliwia wdrożenie tego zasobu. Aby dowiedzieć się więcej na ten temat, zobacz [RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
3. Aby wdrożyć rozwiązanie do uruchamiania/zatrzymywania maszyny wirtualnej, należy zarejestrować subskrypcję w następujących przestrzeniach nazw zasobów platformy Azure:
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Zobacz, [Rozwiązywanie problemów dotyczących rejestracji dostawcy zasobów](../../azure-resource-manager/templates/error-register-resource-provider.md) , aby dowiedzieć się więcej o błędach podczas rejestrowania dostawców.
4. Jeśli masz blokadę obszaru roboczego Log Analytics, przejdź do obszaru roboczego w obszarze Azure Portal i Usuń wszystkie blokady zasobu.
5. Jeśli powyższe rozwiązania nie rozwiążą problemu, postępuj zgodnie z instrukcjami w sekcji [Aktualizowanie rozwiązania](../automation-solution-vm-management.md#update-the-solution) , aby ponownie wdrożyć rozwiązanie uruchamiania/zatrzymywania.

## <a name="all-vms-fail-to-startstop"></a>Scenariusz: Uruchamianie/zatrzymywanie wszystkich maszyn wirtualnych nie powiodło się

### <a name="issue"></a>Problem

Skonfigurowano rozwiązanie uruchamiania/zatrzymywania maszyny wirtualnej, ale nie uruchomiono ani nie zatrzymano wszystkich skonfigurowanych maszyn wirtualnych.

### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany jedną z następujących przyczyn:

1. Harmonogram nie jest prawidłowo skonfigurowany
2. Konto Uruchom jako może nie być poprawnie skonfigurowane
3. W elemencie Runbook mogły wystąpić błędy
4. Maszyny wirtualne mogły zostać wykluczone

### <a name="resolution"></a>Rozwiązanie

Zapoznaj się z poniższą listą, aby zapoznać się z potencjalnymi rozwiązaniami problemu lub miejscami, aby

* Sprawdź, czy skonfigurowano harmonogram dla rozwiązania uruchamiania/zatrzymywania maszyny wirtualnej. Aby dowiedzieć się, jak skonfigurować harmonogram, zobacz artykuł dotyczący [harmonogramów](../automation-schedules.md) .

* Sprawdź [strumienie zadań](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) , aby wyszukać błędy. W portalu przejdź do konta usługi Automation i wybierz pozycję **zadania** w obszarze **Automatyzacja procesów**. Na stronie **zadania** Wyszukaj zadania z jednego z następujących elementów Runbook:

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* Sprawdź, czy [konto Uruchom jako](../manage-runas-account.md) ma odpowiednie uprawnienia do maszyn wirtualnych, które próbujesz uruchomić lub zatrzymać. Aby dowiedzieć się, jak sprawdzić uprawnienia do zasobu, zobacz [Szybki Start: Wyświetlanie ról przypisanych do użytkownika przy użyciu Azure Portal](../../role-based-access-control/check-access.md). Należy podać identyfikator aplikacji dla jednostki usługi używanej przez konto Uruchom jako. Możesz pobrać tę wartość, przechodząc do konta usługi Automation w Azure Portal, wybierając pozycję **konta Uruchom jako** w obszarze **Ustawienia konta** i klikając odpowiednie konto Uruchom jako.

* Maszyny wirtualne mogą nie zostać uruchomione lub zatrzymane, jeśli są jawnie wykluczone. Wykluczone maszyny wirtualne ustawione w zmiennej **External_ExcludeVMNames** na koncie usługi Automation, na którym wdrożono rozwiązanie. Poniższy przykład pokazuje, jak można zbadać tę wartość za pomocą programu PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="some-vms-fail-to-startstop"></a>Scenariusz: uruchamianie lub zatrzymywanie niektórych maszyn wirtualnych nie powiodło się

### <a name="issue"></a>Problem

Skonfigurowano rozwiązanie uruchamiania/zatrzymywania maszyny wirtualnej, ale nie uruchomiono lub nie zatrzymano niektórych skonfigurowanych maszyn wirtualnych.

### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany jedną z następujących przyczyn:

1. Jeśli jest używany scenariusz sekwencji, może brakować znacznika lub jest on nieprawidłowy
2. Maszyna wirtualna może zostać wykluczona
3. Konto Uruchom jako może nie mieć wystarczających uprawnień do maszyny wirtualnej
4. Maszyna wirtualna może znajdować się w stanie zatrzymania lub zatrzymywania

### <a name="resolution"></a>Rozwiązanie

Zapoznaj się z poniższą listą, aby zapoznać się z potencjalnymi rozwiązaniami problemu lub miejscami, aby

* W przypadku korzystania z [scenariusza kolejności](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags) uruchamiania/zatrzymywania maszyny wirtualnej poza godzinami należy upewnić się, że każda maszyna wirtualna, która ma zostać uruchomiona lub zatrzymana, ma odpowiedni tag. Upewnij się, że maszyny wirtualne, które chcesz uruchomić, mają tag `sequencestart` i maszyny wirtualne, które chcesz zatrzymać, mają tag `sequencestop`. Oba Tagi wymagają dodatniej wartości całkowitej. Można użyć zapytania podobnego do poniższego przykładu, aby wyszukać wszystkie maszyny wirtualne ze znacznikami i ich wartościami.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Maszyny wirtualne mogą nie zostać uruchomione lub zatrzymane, jeśli są jawnie wykluczone. Wykluczone maszyny wirtualne ustawione w zmiennej **External_ExcludeVMNames** na koncie usługi Automation, na którym wdrożono rozwiązanie. Poniższy przykład pokazuje, jak można zbadać tę wartość za pomocą programu PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Aby można było uruchamiać i zatrzymywać maszyny wirtualne, konto Uruchom jako dla konta usługi Automation musi mieć odpowiednie uprawnienia do maszyny wirtualnej. Aby dowiedzieć się, jak sprawdzić uprawnienia do zasobu, zobacz [Szybki Start: Wyświetlanie ról przypisanych do użytkownika przy użyciu Azure Portal](../../role-based-access-control/check-access.md). Należy podać identyfikator aplikacji dla jednostki usługi używanej przez konto Uruchom jako. Możesz pobrać tę wartość, przechodząc do konta usługi Automation w Azure Portal, wybierając pozycję **konta Uruchom jako** w obszarze **Ustawienia konta** i klikając odpowiednie konto Uruchom jako.

* Jeśli maszyna wirtualna ma problem z uruchamianiem lub cofaniem przydziału, to zachowanie może być spowodowane przez problem związany z maszyną wirtualną. Niektóre przykłady lub potencjalne problemy polegają na tym, że aktualizacja jest stosowana podczas próby zamknięcia usługi, a usługa zawiesza się i nie tylko. Przejdź do zasobu maszyny wirtualnej i sprawdź **dzienniki aktywności** , aby sprawdzić, czy w dziennikach znajdują się błędy. Możesz także próbować zalogować się do maszyny wirtualnej, aby sprawdzić, czy w dziennikach zdarzeń występują błędy. Aby dowiedzieć się więcej o rozwiązywaniu problemów dotyczących maszyny wirtualnej, zobacz [Rozwiązywanie problemów z maszynami wirtualnymi](../../virtual-machines/troubleshooting/index.yml)

* Sprawdź [strumienie zadań](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) , aby wyszukać błędy. W portalu przejdź do konta usługi Automation i wybierz pozycję **zadania** w obszarze **Automatyzacja procesów**.

## <a name="custom-runbook"></a>Scenariusz: mój niestandardowy element Runbook nie może uruchomić lub zatrzymać moich maszyn wirtualnych

### <a name="issue"></a>Problem

Utworzono niestandardowy element Runbook lub został on pobrany z Galeria programu PowerShell i nie działa prawidłowo.

### <a name="cause"></a>Przyczyna

Przyczyną niepowodzenia może być jedna z wielu rzeczy. Przejdź do konta usługi Automation w Azure Portal i wybierz pozycję **zadania** w obszarze **Automatyzacja procesów**. Na stronie **zadania** Znajdź zadania z elementu Runbook, aby wyświetlić wszystkie błędy zadań.

### <a name="resolution"></a>Rozwiązanie

Zaleca się, aby do uruchamiania i zatrzymywania maszyn wirtualnych w Azure Automation używać [rozwiązania do uruchamiania/zatrzymywania maszyn wirtualnych poza godzinami](../automation-solution-vm-management.md) . To rozwiązanie jest tworzone przez firmę Microsoft. Niestandardowe elementy Runbook nie są obsługiwane przez firmę Microsoft. Rozwiązanie dla niestandardowego elementu Runbook można znaleźć, odwiedzając artykuł dotyczący [rozwiązywania problemów](runbooks.md) z elementem Runbook. Ten artykuł zawiera ogólne wskazówki i rozwiązywanie problemów dotyczących elementów Runbook wszystkich typów. Sprawdź [strumienie zadań](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) , aby wyszukać błędy. W portalu przejdź do konta usługi Automation i wybierz pozycję **zadania** w obszarze **Automatyzacja procesów**.

## <a name="dont-start-stop-in-sequence"></a>Scenariusz: maszyny wirtualne nie uruchamiają się lub nie zatrzymują w poprawnej kolejności

### <a name="issue"></a>Problem

Maszyny wirtualne, które zostały skonfigurowane w rozwiązaniu, nie zaczynają się ani nie są zatrzymane w poprawnej kolejności.

### <a name="cause"></a>Przyczyna

Jest to spowodowane przez nieprawidłowe znakowanie na maszynach wirtualnych.

### <a name="resolution"></a>Rozwiązanie

Wykonaj następujące kroki, aby upewnić się, że rozwiązanie jest prawidłowo skonfigurowane.

1. Upewnij się, że wszystkie maszyny wirtualne, które mają zostać uruchomione lub zatrzymane, mają tag `sequencestart` lub `sequencestop`, w zależności od sytuacji. Tagi te wymagają dodatniej liczby całkowitej jako wartości. Maszyny wirtualne są przetwarzane w kolejności rosnącej na podstawie tej wartości.
2. Upewnij się, że grupy zasobów dla maszyn wirtualnych, które mają zostać uruchomione lub zatrzymane, znajdują się w zmiennych `External_Start_ResourceGroupNames` lub `External_Stop_ResourceGroupNames`, w zależności od sytuacji.
3. Przetestuj zmiany, wykonując `SequencedStartStop_Parent` element Runbook z parametrem WHATIF ustawionym na wartość true, aby wyświetlić podgląd zmian.

Aby uzyskać bardziej szczegółowe i dodatkowe instrukcje dotyczące sposobu używania rozwiązania do uruchamiania i zatrzymywania maszyn wirtualnych w sekwencji, zobacz [Uruchamianie/zatrzymywanie maszyn wirtualnych w sekwencji](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags).

## <a name="403"></a>Scenariusz: zadanie uruchamiania/zatrzymywania maszyny wirtualnej kończy się niepowodzeniem z niedozwolonym stanem 403

### <a name="issue"></a>Problem

Możesz znaleźć zadania, których nie powiodło się z powodu błędu `403 forbidden` w przypadku elementów Runbook uruchamiania/zatrzymywania maszyn wirtualnych poza godzinami.

### <a name="cause"></a>Przyczyna

Przyczyną tego problemu może być nieprawidłowo skonfigurowane lub wygasłe konto Uruchom jako. Może to być również spowodowane niewystarczającymi uprawnieniami do zasobów maszyny wirtualnej przy użyciu konta Uruchom jako konta usługi Automation.

### <a name="resolution"></a>Rozwiązanie

Aby sprawdzić, czy konto Uruchom jako jest prawidłowo skonfigurowane, przejdź do konta usługi Automation w Azure Portal i wybierz pozycję **konta Uruchom jako** w obszarze **Ustawienia konta**. W tym miejscu zobaczysz stan kont Uruchom jako, jeśli konto Uruchom jako jest nieprawidłowo skonfigurowane lub wygasło jego stan.

Jeśli konto Uruchom jako jest [nieprawidłowo skonfigurowane](../manage-runas-account.md#misconfiguration), należy usunąć i ponownie utworzyć konto Uruchom jako.

Jeśli certyfikat wygasł dla konta Uruchom jako, wykonaj czynności opisane w sekcji [odnowienie certyfikatu](../manage-runas-account.md#cert-renewal) z podpisem własnym, aby odnowić certyfikat.

Problem może być spowodowany brakiem uprawnień. Aby dowiedzieć się, jak sprawdzić uprawnienia do zasobu, zobacz [Szybki Start: Wyświetlanie ról przypisanych do użytkownika przy użyciu Azure Portal](../../role-based-access-control/check-access.md). Należy podać identyfikator aplikacji dla jednostki usługi używanej przez konto Uruchom jako. Możesz pobrać tę wartość, przechodząc do konta usługi Automation w Azure Portal, wybierając pozycję **konta Uruchom jako** w obszarze **Ustawienia konta** i klikając odpowiednie konto Uruchom jako.

## <a name="other"></a>Scenariusz: mój problem nie jest wymieniony powyżej

### <a name="issue"></a>Problem

Wystąpił problem lub nieoczekiwany wynik podczas korzystania z rozwiązania Start/Stop VMs during off-hours, które nie znajduje się na tej stronie.

### <a name="cause"></a>Przyczyna

Wiele błędów może być spowodowanych przez użycie starej i nieaktualnej wersji rozwiązania.

> [!NOTE]
> Rozwiązanie Start/Stop VMs during off-hours zostało przetestowane z modułami platformy Azure, które zostały zaimportowane do konta usługi Automation podczas wdrażania rozwiązania. Rozwiązanie nie działa obecnie z nowszymi wersjami modułu platformy Azure. Ma to wpływ tylko na konto usługi Automation używane do uruchamiania rozwiązania Start/Stop VMs during off-hours. Nadal można używać nowszych wersji modułu platformy Azure na innych kontach usługi Automation, zgodnie z opisem w artykule [jak zaktualizować moduły Azure PowerShell w Azure Automation](../automation-update-azure-modules.md)

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać wiele błędów, zaleca się usunięcie i zaktualizowanie rozwiązania. Aby dowiedzieć się, jak zaktualizować rozwiązanie, zobacz temat [Aktualizowanie uruchamiania/zatrzymywania maszyn wirtualnych poza godzinami](../automation-solution-vm-management.md#update-the-solution). Dodatkowo można sprawdzić [strumienie zadań](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) , aby wyszukać błędy. W portalu przejdź do konta usługi Automation i wybierz pozycję **zadania** w obszarze **Automatyzacja procesów**.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, możesz obsłużyć zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.
