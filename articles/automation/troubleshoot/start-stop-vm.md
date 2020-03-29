---
title: Rozwiązywanie problemów z uruchamianiem i zatrzymywaniem maszyn wirtualnych — automatyzacja platformy Azure
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z uruchamianiem i zatrzymywaniem maszyn wirtualnych w usłudze Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 97ea98fc38fc8d06dc1bc65ee057241da6f15488
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851378"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Rozwiązywanie problemów z maszynami wirtualnymi start/stop w godzinach wolnych od pracy

## <a name="scenario-the-startstop-vm-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Scenariusz: Rozwiązanie maszyny Wirtualnej Start/Stop nie można poprawnie wdrożyć

### <a name="issue"></a>Problem

Podczas wdrażania [maszyny wirtualne Start/Stop w godzinach wolnych od pracy](../automation-solution-vm-management.md)pojawia się jeden z następujących błędów:

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

1. Istnieje już konto automatyzacji o tej samej nazwie w wybranym regionie.
2. Zasada jest w miejscu, które nie zezwala na wdrożenie rozwiązania Start/Stop maszyn wirtualnych.
3. Typy `Microsoft.OperationsManagement`zasobów `Microsoft.Automation` nie są rejestrowane. `Microsoft.Insights`
4. Obszar roboczy usługi Log Analytics ma blokadę.
5. Masz przestarzałą wersję modułów usługi AzureRM lub rozwiązanie Start/Stop.

### <a name="resolution"></a>Rozwiązanie

Przejrzyj następującą listę potencjalnych rozwiązań problemu lub miejsc, w których warto szukać:

1. Konta automatyzacji muszą być unikatowe w regionie platformy Azure, nawet jeśli znajdują się w różnych grupach zasobów. Sprawdź istniejące konta automatyzacji w regionie docelowym.
2. Istniejąca zasada uniemożliwia zasób, który jest wymagany do wdrożenia rozwiązania maszyny Wirtualnej Start/Stop. Przejdź do przypisań zasad w witrynie Azure portal i sprawdź, czy masz przypisanie zasad, które nie zezwala na wdrożenie tego zasobu. Aby dowiedzieć się więcej na ten temat, zobacz [RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
3. Aby wdrożyć rozwiązanie Start/Stop VM, subskrypcja musi być zarejestrowana w następujących obszarach nazw zasobów platformy Azure:
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Zobacz [Rozwiązywanie problemów z rejestracją dostawcy zasobów,](../../azure-resource-manager/templates/error-register-resource-provider.md) aby dowiedzieć się więcej o błędach podczas rejestrowania dostawców.
4. Jeśli masz blokadę w obszarze roboczym usługi Log Analytics, przejdź do obszaru roboczego w witrynie Azure portal i usuń wszelkie blokady zasobu.
5. Jeśli powyższe rozwiązania nie rozwiążą problemu, postępuj zgodnie z instrukcjami w obszarze [Aktualizuj rozwiązanie,](../automation-solution-vm-management.md#update-the-solution) aby ponownie wdrożyć rozwiązanie Start/Stop.

## <a name="scenario-all-vms-fail-to-startstop"></a><a name="all-vms-fail-to-startstop"></a>Scenariusz: Nie można uruchomić/zatrzymać wszystkich maszyn wirtualnych

### <a name="issue"></a>Problem

Skonfigurowano rozwiązanie Start/Stop VM, ale nie uruchamia się ani nie zatrzymuje wszystkich skonfigurowanych maszyn wirtualnych.

### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany jedną z następujących przyczyn:

1. Harmonogram nie jest poprawnie skonfigurowany
2. Konto RunAs może nie być poprawnie skonfigurowane
3. Identyfikator runbooka mógł napotkać błędy
4. Maszyny wirtualne mogły zostać wykluczone

### <a name="resolution"></a>Rozwiązanie

Przejrzyj następującą listę potencjalnych rozwiązań problemu lub miejsc, w których warto szukać:

* Sprawdź, czy poprawnie skonfigurowano harmonogram rozwiązania start/stop maszyny wirtualnej. Aby dowiedzieć się, jak skonfigurować harmonogram, zobacz artykuł [Harmonogramy.](../automation-schedules.md)

* Sprawdź [strumienie zadań,](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) aby wyszukać błędy. W portalu przejdź do konta automatyzacji i wybierz pozycję **Zadania** w obszarze **Automatyzacja procesów**. Na stronie **Zadania** poszukaj zadań z jednego z następujących elementów runbook:

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* Sprawdź, czy [twoje konto RunAs](../manage-runas-account.md) ma odpowiednie uprawnienia do maszyn wirtualnych, które próbujesz uruchomić lub zatrzymać. Aby dowiedzieć się, jak sprawdzić uprawnienia do zasobu, zobacz [Szybki start: Wyświetlanie ról przypisanych do użytkownika za pomocą portalu Azure](../../role-based-access-control/check-access.md). Musisz podać identyfikator aplikacji dla jednostki usługi używanej przez uruchom jako konto. Tę wartość można pobrać, przechodząc do konta automatyzacji w witrynie Azure portal, wybierając **pozycję Uruchom jako konta** w obszarze Ustawienia **konta** i klikając odpowiednie konto Uruchom jako.

* Maszyny wirtualne nie mogą być uruchamiane lub zatrzymywane, jeśli są jawnie wykluczone. Wykluczone maszyny wirtualne w ustawieniu w **zmiennej External_ExcludeVMNames** w kontie automatyzacji, do na które jest wdrażane rozwiązanie. Poniższy przykład pokazuje, jak można zbadać tę wartość za pomocą programu PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Scenariusz: nie można uruchomić lub zatrzymać niektórych moich maszyn wirtualnych

### <a name="issue"></a>Problem

Skonfigurowano rozwiązanie Start/Stop VM, ale nie uruchamia się ani nie zatrzymuje niektórych skonfigurowanych maszyn wirtualnych.

### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany jedną z następujących przyczyn:

1. W przypadku korzystania ze scenariusza sekwencji może brakować znacznika lub
2. Maszyna wirtualna może być wykluczona
3. Konto RunAs może nie mieć wystarczających uprawnień na maszynie wirtualnej
4. Maszyna wirtualna może mieć coś, co powstrzymało ją przed uruchomieniem lub zatrzymaniem

### <a name="resolution"></a>Rozwiązanie

Przejrzyj następującą listę potencjalnych rozwiązań problemu lub miejsc, w których warto szukać:

* Podczas korzystania ze [scenariusza sekwencji](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags) Start/Stop VM w godzinach pracy rozwiązania, należy upewnić się, że każda maszyna wirtualna, którą chcesz uruchomić lub zatrzymać ma odpowiedni tag. Upewnij się, że maszyny wirtualne, `sequencestart` które chcesz uruchomić, mają tag i `sequencestop` maszyny wirtualne, które chcesz zatrzymać, mają tag. Oba znaczniki wymagają dodatniej wartości całkowitej. Można użyć kwerendy podobne do poniższego przykładu, aby wyszukać wszystkie maszyny wirtualne z tagów i ich wartości.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Maszyny wirtualne nie mogą być uruchamiane lub zatrzymywane, jeśli są jawnie wykluczone. Wykluczone maszyny wirtualne w ustawieniu w **zmiennej External_ExcludeVMNames** w kontie automatyzacji, do na które jest wdrażane rozwiązanie. Poniższy przykład pokazuje, jak można zbadać tę wartość za pomocą programu PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Aby uruchomić i zatrzymać maszyny wirtualne, konto RunAs dla konta automatyzacji musi mieć odpowiednie uprawnienia do maszyny Wirtualnej. Aby dowiedzieć się, jak sprawdzić uprawnienia do zasobu, zobacz [Szybki start: Wyświetlanie ról przypisanych do użytkownika za pomocą portalu Azure](../../role-based-access-control/check-access.md). Musisz podać identyfikator aplikacji dla jednostki usługi używanej przez uruchom jako konto. Tę wartość można pobrać, przechodząc do konta automatyzacji w witrynie Azure portal, wybierając **pozycję Uruchom jako konta** w obszarze Ustawienia **konta** i klikając odpowiednie konto Uruchom jako.

* Jeśli maszyna wirtualna ma problem z uruchamianiem lub alokacją, to zachowanie może być spowodowane przez problem na samej maszynie wirtualnej. Niektóre przykłady lub potencjalne problemy są, aktualizacja jest stosowana podczas próby zamknięcia, usługa zawiesza się i więcej). Przejdź do zasobu maszyny Wirtualnej i sprawdź **dzienniki aktywności,** aby sprawdzić, czy w dziennikach występują błędy. Można również spróbować zalogować się do maszyny Wirtualnej, aby sprawdzić, czy są jakieś błędy w dziennikach zdarzeń. Aby dowiedzieć się więcej na temat rozwiązywania problemów z maszyną wirtualną, zobacz [Rozwiązywanie problemów z maszynami wirtualnymi platformy Azure](../../virtual-machines/troubleshooting/index.yml)

* Sprawdź [strumienie zadań,](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) aby wyszukać błędy. W portalu przejdź do konta automatyzacji i wybierz pozycję **Zadania** w obszarze **Automatyzacja procesów**.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Scenariusz: Uruchomienie lub zatrzymanie maszyn wirtualnych jest uruchamiane lub zatrzymywać mój niestandardowy eks-orp.

### <a name="issue"></a>Problem

Zostałeś autorem niestandardowego eksmisu lub pobrano go z galerii programu PowerShell i nie działa poprawnie.

### <a name="cause"></a>Przyczyna

Przyczyną awarii może być jedna z wielu rzeczy. Przejdź do konta automatyzacji w witrynie Azure portal i wybierz pozycję **Zadania** w obszarze **Automatyzacja procesów**. Na stronie **Zadania** poszukaj zadań z ego księgi runbook, aby wyświetlić wszelkie błędy zadań.

### <a name="resolution"></a>Rozwiązanie

Zaleca się użycie [rozwiązania start/stop VMs w godzinach poza godzinami,](../automation-solution-vm-management.md) aby uruchomić i zatrzymać maszyny wirtualne w usłudze Azure Automation. To rozwiązanie jest autorstwa firmy Microsoft. Niestandardowe programy runbook nie są obsługiwane przez firmę Microsoft. Rozwiązanie dla niestandardowego wiązka uruchomieniu można znaleźć, odwiedzając artykuł dotyczący rozwiązywania problemów z systemem [runbook.](runbooks.md) Ten artykuł zawiera ogólne wskazówki i rozwiązywanie problemów dla elementów runbook wszystkich typów. Sprawdź [strumienie zadań,](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) aby wyszukać błędy. W portalu przejdź do konta automatyzacji i wybierz pozycję **Zadania** w obszarze **Automatyzacja procesów**.

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Scenariusz: maszyny wirtualne nie uruchamiają się ani nie zatrzymują w odpowiedniej kolejności

### <a name="issue"></a>Problem

Maszyny wirtualne skonfigurowane w rozwiązaniu nie uruchamiają się ani nie zatrzymują w odpowiedniej kolejności.

### <a name="cause"></a>Przyczyna

Jest to spowodowane nieprawidłowym tagowaniem na maszynach wirtualnych.

### <a name="resolution"></a>Rozwiązanie

Należy wykonać następujące kroki, aby upewnić się, że rozwiązanie jest poprawnie skonfigurowane.

1. Upewnij się, że wszystkie maszyny wirtualne, które mają zostać uruchomione lub zatrzymane, mają `sequencestart` lub `sequencestop` tag, w zależności od sytuacji. Te tagi potrzebują dodatniej liczby całkowitej jako wartości. Maszyny wirtualne są przetwarzane w porządku rosnącym na podstawie tej wartości.
2. Upewnij się, że grupy zasobów dla maszyn wirtualnych, `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` które mają zostać uruchomione lub zatrzymane, znajdują się w zmiennych lub w zależności od sytuacji.
3. Przetestuj zmiany, `SequencedStartStop_Parent` wykonując runbook z parametrem WHATIF ustawionym na True, aby wyświetlić podgląd zmian.

Aby uzyskać bardziej szczegółowe i dodatkowe instrukcje dotyczące używania rozwiązania do uruchamiania i zatrzymywania maszyn wirtualnych w [sekwencji, zobacz Uruchamianie/zatrzymywania maszyn wirtualnych w kolejności](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags).

## <a name="scenario-startstop-vm-job-fails-with-403-forbidden-status"></a><a name="403"></a>Scenariusz: zadanie uruchamiania/zatrzymywania maszyny wirtualnej kończy się niepowodzeniem z 403 zabronionym stanem

### <a name="issue"></a>Problem

Można znaleźć zadania, które `403 forbidden` nie powiodły się z powodu błędu dla start/stop maszyn wirtualnych w godzinach nieobiegowych wiązki śmięty rozwiązania.

### <a name="cause"></a>Przyczyna

Ten problem może być spowodowany nieprawidłowo skonfigurowanym lub wygasłym kontem Uruchom jako konto. Może to być również z powodu niewystarczających uprawnień do zasobów maszyny Wirtualnej przez konta automatyzacji uruchom jako konto.

### <a name="resolution"></a>Rozwiązanie

Aby sprawdzić, czy konto Uruchom jako jest poprawnie skonfigurowane, przejdź do konta automatyzacji w witrynie Azure portal i wybierz pozycję **Uruchom jako konta** w obszarze Ustawienia **konta**. W tym miejscu zobaczysz stan uruchomienia jako konta, jeśli konto Uruchom jako jest nieprawidłowo skonfigurowane lub wygasło, stan to pokaże.

Jeśli twoje konto Uruchom jako jest nieprawidłowo skonfigurowane, należy usunąć i ponownie utworzyć konto Uruchom jako. Zobacz [Zarządzanie programem Azure Automation Run jako kontami](../manage-runas-account.md).

Jeśli certyfikat wygasł dla twojego konta Uruchom jako konto, wykonaj kroki wymienione w [instrukcji odnowienia certyfikatu z podpisem własnym,](../manage-runas-account.md#cert-renewal) aby odnowić certyfikat.

Problem może być spowodowany brakującymi uprawnieniami. Aby dowiedzieć się, jak sprawdzić uprawnienia do zasobu, zobacz [Szybki start: Wyświetlanie ról przypisanych do użytkownika za pomocą portalu Azure](../../role-based-access-control/check-access.md). Musisz podać identyfikator aplikacji dla jednostki usługi używanej przez uruchom jako konto. Tę wartość można pobrać, przechodząc do konta automatyzacji w witrynie Azure portal, wybierając **pozycję Uruchom jako konta** w obszarze Ustawienia **konta** i klikając odpowiednie konto Uruchom jako.

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>Scenariusz: Mój problem nie jest wymieniony powyżej

### <a name="issue"></a>Problem

Wystąpi problem lub nieoczekiwany wynik podczas korzystania z maszyn wirtualnych Start/Stop podczas rozwiązania poza godzinami pracy, którego nie ma na tej stronie.

### <a name="cause"></a>Przyczyna

Wiele razy błędy mogą być spowodowane przy użyciu starej i przestarzałej wersji rozwiązania.

> [!NOTE]
> Maszyny wirtualne Start/Stop podczas poza godzinami pracy zostały przetestowane przy testach modułów platformy Azure, które są importowane do konta automatyzacji podczas wdrażania rozwiązania. Rozwiązanie obecnie nie działa z nowszymi wersjami modułu platformy Azure. Dotyczy to tylko konta automatyzacji, którego używasz do uruchamiania maszyn wirtualnych Start/Stop podczas rozwiązania poza godzinami pracy. Nadal można używać nowszych wersji modułu Platformy Azure na innych kontach automatyzacji, zgodnie z opisem w [jak zaktualizować moduły usługi Azure PowerShell w usłudze Azure Automation](../automation-update-azure-modules.md)

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać wiele błędów, zaleca się usunięcie i zaktualizowanie rozwiązania. Aby dowiedzieć się, jak zaktualizować rozwiązanie, zobacz [Aktualizowanie maszyn wirtualnych Start/Stop w godzinach pracy.](../automation-solution-vm-management.md#update-the-solution) Ponadto można sprawdzić [strumienie zadań, aby wyszukać](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) wszelkie błędy. W portalu przejdź do konta automatyzacji i wybierz pozycję **Zadania** w obszarze **Automatyzacja procesów**.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, możesz zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.
