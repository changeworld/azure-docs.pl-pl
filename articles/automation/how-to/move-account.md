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
ms.openlocfilehash: 01f3995a80375f2deada13c36c48600243a17623
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2019
ms.locfileid: "57733368"
---
# <a name="move-your-automation-account-to-another-subscription"></a>Przenieś swoje konto usługi Automation do innej subskrypcji

Azure oferuje możliwość przenoszenia niektórych zasobów do nowej grupy zasobów lub subskrypcji o tej samej dzierżawie, natywnie za pośrednictwem witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. Aby dowiedzieć się więcej na temat procesu, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/resource-group-move-resources.md). Konta usługi Automation to jeden z zasobów, które można przenosić, ale istnieją specjalne kroki potrzebne podczas przenoszenia konta usługi Automation.

Dostępne są następujące ogólne kroki, aby Przenoszenie konta usługi Automation w taki sposób, aby:

* Usuwanie rozwiązania
* Odłączanie obszaru roboczego
* Przenoszenie konta usługi Automation
* Usunięcie i ponowne tworzenie kont Uruchom jako
* Ponowne włączenie rozwiązania

## <a name="remove-solutions"></a>Usuwanie rozwiązania

Aby odłączyć obszar roboczy z kontem usługi Automation, zmian i spisu, rozwiązanie Update Management i uruchamianie/zatrzymywanie maszyn wirtualnych poza godziny rozwiązania należy usunąć z obszaru roboczego.

W grupie zasobów, wybierz poszczególne **rozwiązania** i kliknij przycisk **Usuń**. Na **Usuń zasoby** strony, upewnij się zasobów do usunięcia, a następnie kliknij przycisk **Usuń**.

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

W witrynie Azure portal przejdź do grupy zasobów i wybierz **alerty** w obszarze **monitorowanie**. Na **alerty** wybierz opcję **Zarządzaj regułami alertów**

![Przedstawiający stronę alertów, kliknij pozycję reguły zarządzania alertów](../media/move-account/alert-rules.png)

Na **reguły** stronie powinien zostać wyświetlony lista wszystkich alertów skonfigurowanych w tej grupie zasobów. **Uruchamianie/zatrzymywanie maszyn wirtualnych** rozwiązanie tworzy 3 reguły alertów

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Wybierz te 3 reguły alertu, a następnie kliknij przycisk **Usuń**. Ta akcja spowoduje usunięcie tych reguł alertów.

![Reguły strony za pomocą reguł zaznaczone i są usuwane](../media/move-account/delete-rules.png)

> [!NOTE]
> Jeśli nie widzisz żadnych reguł alertów na **reguły** strony, zmień **stan** pokazanie **wyłączone** powiadamia o użytkownik wyłączył je.

Po usunięciu reguły alertów, którą chcesz usunąć grupę akcji, który został utworzony dla powiadomień dla rozwiązania uruchamianie/zatrzymywanie maszyn wirtualnych.

W witrynie Azure portal przejdź do **Monitor**, wybierz opcję **alerty**i kliknij przycisk **Zarządzanie grupami działań**.

Wybierz grupę akcji z listy, będzie miał nazwę **StartStop_VM_Notification**. Na stronie grup akcji kliknij **Usuń**

![Strona grupy akcji, klikając polecenie Usuń](../media/move-account/delete-action-group.png)

Podobnie można usunąć grupy akcji przy użyciu programu PowerShell. Ta akcja jest przeprowadzane za pomocą [Remove-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) polecenia cmdlet, jak pokazano w poniższym przykładzie:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Odłączanie obszaru roboczego

W witrynie Azure portal przejdź do swojej **konta usługi Automation**. W obszarze **powiązane zasoby**, kliknij przycisk **połączony obszar roboczy**. Kliknij przycisk **odłączanie obszaru roboczego** odłączanie obszaru roboczego z konta usługi Automation.

![Odłączanie obszaru roboczego z kontem usługi Automation](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Przenoszenie konta usługi Automation

Po usunięciu wszystkich poprzednich elementów można usunąć konta usługi Automation i jego elementy runbook. W witrynie Azure portal przejdź do grupy zasobów konta usługi Automation. Wybierz **przenieść** i następnie **przenieść do innej subskrypcji**.

![Stronie grupy zasobów, wybierając przeniesienie do innej subskrypcji](../media/move-account/move-resources.png)

Wybierz zasoby w grupie zasobów, którą chcesz przenieść. Upewnij się, możesz uwzględnić swoje **konta usługi Automation**, **Runbook**, i **obszaru roboczego usługi Log Analytics** zasobów.

Po zakończeniu przenoszenia, istnieją dodatkowe kroki, które należy podjąć, aby pobrać wszystkie elementy pracy.

## <a name="recreate-run-as-accounts"></a>Utwórz ponownie uruchom jako konta

[Konta Uruchom jako](../manage-runas-account.md) utworzyć nazwę główną usługi w usłudze Azure Active Directory do uwierzytelniania za pomocą zasobów platformy Azure. Po zmianie subskrypcji istniejące konto Uruchom jako nie jest już używana przez konto usługi Automation.

Przejdź do swojego konta usługi Automation w nowej subskrypcji, a następnie wybierz pozycję **konta Uruchom jako** w obszarze **ustawienia konta**. Zobaczysz, że konta Uruchom jako pokazują teraz jako ukończona.

![Konta Uruchom jako wyświetlane jako niekompletne](../media/move-account/run-as-accounts.png)

Kliknij każde uruchomienie jako konto i na **właściwości** kliknij **Usuń** można usunąć konta Uruchom jako.

> ! [UWAGA] Jeśli nie masz uprawnień do utworzenia lub wyświetlać konta Uruchom jako zostanie wyświetlony następujący komunikat `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.`. Aby dowiedzieć się więcej na temat uprawnień wymaganych do skonfigurowania konta Uruchom jako, zobacz [uprawnienia wymagane do skonfigurowania kont Uruchom jako](../manage-runas-account.md#permissions).

Po usunięciu konta Uruchom jako kliknij **Utwórz** na **Azure konto Uruchom jako**. Na **Dodawanie konta platformy Azure Uruchom jako** kliknij **Utwórz** do utworzenia konta Uruchom jako i nazwę główną usługi. Powtórz te czynności przy użyciu **Azure klasycznego konta Uruchom jako**.

## <a name="enable-solutions"></a>Włączanie rozwiązań

Po utworzeniu konta Uruchom jako będzie ponownie włączyć rozwiązania, które można usunąć przed przeniesieniem. Aby włączyć **śledzenia zmian i spisu** i **rozwiązania Update Management**, wybierz odpowiednich funkcji na Twoim koncie usługi Automation. Wybierz obszar roboczy usługi Log Analytics, przeniesiony na, a następnie kliknij przycisk **Włącz**.

![Ponowne włączenie rozwiązania na koncie usługi Automation przeniesiony](../media/move-account/reenable-solutions.png)

Z maszynami, które są dołączone za pomocą rozwiązania będą widoczne ponownie, ponieważ łączysz istniejącego obszaru roboczego usługi Log Analytics.

Aby ponownie włączyć uruchamianie/zatrzymywanie maszyn wirtualnych podczas szczytu rozwiązania, należy ponownie wdrożyć rozwiązanie. W obszarze **powiązane zasoby**, wybierz opcję **uruchamianie/zatrzymywanie maszyn wirtualnych**. Kliknij przycisk **Dowiedz się więcej o, a następnie włącz rozwiązanie** i kliknij przycisk **Utwórz** rozpocząć wdrażanie.

Na **Dodaj rozwiązanie** stronie, wybierz obszar roboczy usługi Log Analytics i konto usługi Automation.  

![Konta Uruchom jako wyświetlane jako niekompletne](../media/move-account/add-solution-vm.png)

Aby uzyskać szczegółowe instrukcje dotyczące konfigurowania rozwiązania, zobacz [uruchamianie/zatrzymywanie maszyn wirtualnych poza godzinami szczytu rozwiązania w usłudze Azure Automation](../automation-solution-vm-management.md)

## <a name="post-move-verification"></a>Weryfikacja przeniesienia wpis

Po zakończeniu przejścia upewnij się, że różne scenariusze na koncie usługi Automation, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami. W poniższej tabeli przedstawiono listę zadań, które powinny być zweryfikowane, po zakończeniu przenoszenia:

|Możliwości|Testy|Rozwiązywanie problemów z łącza|
|---|---|---|
|Elementy Runbook|Element Runbook można pomyślnie uruchomić i łączyć się z zasobami platformy Azure.|[Rozwiązywanie problemów z elementami Runbook](../troubleshoot/runbooks.md)
| Kontrola źródła|Możesz uruchomić ręczną synchronizację w repozytorium kontroli źródła.|[Integracja kontroli źródła](../source-control-integration.md)|
|Śledzenie zmian i spis|Sprawdź, czy widzisz bieżące dane spisu z maszyn.|[Rozwiązywanie problemów z śledzenia zmian](../troubleshoot/change-tracking.md)|
|Zarządzanie aktualizacjami|Sprawdź, zobacz maszyn i są one w dobrej kondycji</br>Uruchom test wdrożenia aktualizacji oprogramowania.|[Rozwiązywanie problemów z rozwiązania Update Management](../troubleshoot/update-management.md)|

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat przenoszenia zasobów na platformie Azure, zobacz [przenoszenia zasobów na platformie Azure](../../azure-resource-manager/move-support-resources.md)
