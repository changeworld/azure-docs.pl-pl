---
title: Przenoszenie konta usługi Azure Automation do innej subskrypcji
description: W tym artykule opisano sposób przenoszenia konta usługi Automation do innej subskrypcji
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 59d433bfb888eaa41cc8f66bdf3ad28c16efbe5c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61302625"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Przenoszenie konta usługi Azure Automation do innej subskrypcji

Azure oferuje możliwość przenoszenia niektórych zasobów do nowej grupy zasobów lub subskrypcji. Można przenieść zasobów za pomocą witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. Aby dowiedzieć się więcej na temat procesu, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/resource-group-move-resources.md). 

Konta usługi Azure Automation to jeden z zasobów, które mogą zostać przeniesione. W tym artykule dowiesz się, instrukcje Przenoszenie konta usługi Automation do innego zasobu lub subskrypcji.

Dostępne są następujące ogólne kroki, aby Przenoszenie konta usługi Automation:

1. Usuń swoje rozwiązania.
2. Odłączanie obszaru roboczego.
3. Przenoszenie konta usługi Automation.
4. Usunięcie i ponowne utworzenie konta Uruchom jako.
5. Ponowne włączenie rozwiązania.

## <a name="remove-solutions"></a>Usuwanie rozwiązania

Aby odłączyć obszaru roboczego z konta usługi Automation, te rozwiązania muszą zostać usunięte z obszaru roboczego:
- **Śledzenie zmian i spis**
- **Zarządzanie aktualizacjami** 
- **Uruchamianie/zatrzymywanie maszyn wirtualnych poza godzinami poza godzinami pracy** 

W grupie zasobów, Znajdź każde z tych rozwiązań i wybierz **Usuń**. Na **Usuń zasoby** strony, upewnij się zasobów do usunięcia, a następnie wybierz pozycję **Usuń**.

![Usuwanie rozwiązania w witrynie Azure portal](../media/move-account/delete-solutions.png)

Można wykonać tego samego zadania za pomocą [Remove-AzureRmResource](/powershell/module/azurerm.resources/remove-azurermresource) polecenia cmdlet, jak pokazano w poniższym przykładzie:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Dodatkowe kroki uruchamianie/zatrzymywanie maszyn wirtualnych

Aby uzyskać **uruchamianie/zatrzymywanie maszyn wirtualnych** rozwiązania, należy również usunąć reguły alertów utworzone przez rozwiązanie.

W witrynie Azure portal przejdź do grupy zasobów i wybierz **monitorowanie** > **alerty** > **Zarządzaj regułami alertów**.

![Alerty wybór przedstawiający stronę reguł zarządzania alertów](../media/move-account/alert-rules.png)

Na **reguły** stronie powinien zostać wyświetlony Lista alertów skonfigurowanych w tej grupie zasobów. **Uruchamianie/zatrzymywanie maszyn wirtualnych** rozwiązanie tworzy trzy reguły alertu:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Wybierz tych trzech reguły alertów, a następnie wybierz pozycję **Usuń**. Ta akcja spowoduje usunięcie tych reguł alertów.

![Strona reguł z żądaniem potwierdzenia usunięcia dla zaznaczonych reguł](../media/move-account/delete-rules.png)

> [!NOTE]
> Jeśli nie widzisz żadnych reguł alertów na **reguły** strony, zmień **stan** pokazanie **wyłączone** alertów, ponieważ użytkownik może być wyłączona je.

Po usunięciu reguły alertów, Usuń grupę akcji, dla którego utworzono **uruchamianie/zatrzymywanie maszyn wirtualnych** powiadomienia rozwiązania.

W witrynie Azure portal wybierz **Monitor** > **alerty** > **Zarządzanie grupami działań**.

Wybierz **StartStop_VM_Notification** z listy. Na stronie grupy akcji wybierz **Usuń**.

![Strona grupy akcji, wybierz opcję Usuń](../media/move-account/delete-action-group.png)

Podobnie, można usunąć grupy akcji przy użyciu programu PowerShell przy użyciu [Remove-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) polecenia cmdlet, jak pokazano w poniższym przykładzie:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Odłączanie obszaru roboczego

W witrynie Azure portal wybierz **konta usługi Automation** > **powiązane zasoby** > **połączony obszar roboczy**. Wybierz **odłączanie obszaru roboczego** odłączanie obszaru roboczego z konta usługi Automation.

![Odłącz obszar roboczy z kontem usługi Automation](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Przenoszenie konta usługi Automation

Po usunięciu poprzednie elementy, możesz w dalszym ciągu usunąć swoje konto usługi Automation i jego elementy runbook. W witrynie Azure portal przejdź do grupy zasobów konta usługi Automation. Wybierz **przenieść** > **przenieść do innej subskrypcji**.

![Stronie grupy zasobów, Przenieś do innej subskrypcji](../media/move-account/move-resources.png)

Wybierz zasoby w grupie zasobów, którą chcesz przenieść. Upewnij się, możesz uwzględnić swoje **konta usługi Automation**, **Runbook**, i **obszaru roboczego usługi Log Analytics** zasobów.

Po zakończeniu przenoszenia istnieją dodatkowe kroki wymagane do wszystkich elementów.

## <a name="re-create-run-as-accounts"></a>Ponowne tworzenie kont Uruchom jako

[Konta Uruchom jako](../manage-runas-account.md) utworzyć nazwę główną usługi w usłudze Azure Active Directory do uwierzytelniania za pomocą zasobów platformy Azure. Po zmianie subskrypcji na koncie usługi Automation nie używa już istniejącego konta Uruchom jako.

Przejdź do konta usługi Automation w nowej subskrypcji i wybierz pozycję **konta Uruchom jako** w obszarze **ustawienia konta**. Zobaczysz, że konta Uruchom jako pokazują teraz jako ukończona.

![Konta Uruchom jako jest niekompletne](../media/move-account/run-as-accounts.png)

Wybierz każde konto Uruchom jako. Na **właściwości** wybierz opcję **Usuń** można usunąć konta Uruchom jako.

> [!NOTE]
> Jeśli nie masz uprawnień do utworzenia lub wyświetlać konta Uruchom jako, zostanie wyświetlony następujący komunikat: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` Aby dowiedzieć się więcej na temat uprawnień wymaganych do skonfigurowania konta Uruchom jako, zobacz [uprawnienia wymagane do skonfigurowania kont Uruchom jako](../manage-runas-account.md#permissions).

Po usunięciu konta Uruchom jako wybierz **Utwórz** w obszarze **konta Uruchom jako platformy**. Na **Dodaj konta Uruchom jako platformy** wybierz opcję **Utwórz** do utworzenia konta Uruchom jako i usługę podmiotu zabezpieczeń. Powtórz te czynności przy użyciu **klasycznego konta Uruchom jako platformy**.

## <a name="enable-solutions"></a>Włączanie rozwiązań

Po ponownym utworzeniu kont Uruchom jako, będzie ponownie włączyć rozwiązania, które zostały usunięte przed przeniesieniem. Aby włączyć **śledzenia zmian i spisu** i **rozwiązania Update Management**, wybierz odpowiednich funkcji na Twoim koncie usługi Automation. Wybierz obszar roboczy usługi Log Analytics, przeniesiony na **Włącz**.

![Ponowne włączenie rozwiązania na koncie usługi Automation przeniesiony](../media/move-account/reenable-solutions.png)

Maszyny, które są dołączone za pomocą rozwiązania będą widoczne po nawiązaniu połączenia istniejącego obszaru roboczego usługi Log Analytics.

Aby włączyć **uruchamianie/zatrzymywanie maszyn wirtualnych** podczas szczytu rozwiązania, należy ponownie wdrożyć rozwiązanie. W obszarze **powiązane zasoby**, wybierz opcję **uruchamianie/zatrzymywanie maszyn wirtualnych** > **Dowiedz się więcej o, a następnie włącz rozwiązanie** > **Utwórz** rozpocząć wdrażanie.

Na **Dodaj rozwiązanie** wybierz Twojego obszaru roboczego usługi Log Analytics i konto usługi Automation.  

![Dodawanie menu rozwiązania](../media/move-account/add-solution-vm.png)

Aby uzyskać szczegółowe instrukcje dotyczące konfigurowania rozwiązania, zobacz [uruchamianie/zatrzymywanie maszyn wirtualnych poza godzinami szczytu rozwiązania w usłudze Azure Automation](../automation-solution-vm-management.md).

## <a name="post-move-verification"></a>Weryfikacja przenoszenia po

Po zakończeniu przejścia Sprawdź poniższą listę zadań, które należy zweryfikować:

|Możliwości|Testy|Rozwiązywanie problemów z łącza|
|---|---|---|
|Elementy Runbook|Element runbook można pomyślnie uruchomić i łączyć się z zasobami platformy Azure.|[Rozwiązywanie problemów z elementami Runbook](../troubleshoot/runbooks.md)
| Kontrola źródła|Możesz uruchomić ręczną synchronizację w repozytorium kontroli źródła.|[Integracja kontroli źródła](../source-control-integration.md)|
|Śledzenie zmian i spisu|Sprawdź, czy widzisz bieżące dane spisu z maszyn.|[Rozwiązywanie problemów z śledzenia zmian](../troubleshoot/change-tracking.md)|
|Zarządzanie aktualizacjami|Sprawdź, zobaczysz maszyn i są one w dobrej kondycji.</br>Uruchom test wdrożenia aktualizacji oprogramowania.|[Rozwiązywanie problemów z rozwiązania update management](../troubleshoot/update-management.md)|

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat przenoszenia zasobów na platformie Azure, zobacz [przenoszenia zasobów na platformie Azure](../../azure-resource-manager/move-support-resources.md).
