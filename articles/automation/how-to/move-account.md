---
title: Przenoszenie konta Azure Automation do innej subskrypcji
description: W tym artykule opisano sposób przenoszenia konta usługi Automation do innej subskrypcji
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2d1c747a52a1e8dedd0b5ba411b673eee463a2b6
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849585"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Przenoszenie konta Azure Automation do innej subskrypcji

Platforma Azure oferuje możliwość przenoszenia niektórych zasobów do nowej grupy zasobów lub subskrypcji. Zasoby można przenosić za pomocą Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. Aby dowiedzieć się więcej o tym procesie, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/resource-group-move-resources.md).

Konta Azure Automation są jednym z zasobów, które można przenieść. W tym artykule omówiono procedurę przenoszenia kont usługi Automation do innego zasobu lub subskrypcji.

Poniżej przedstawiono ogólne kroki służące do przeniesienia konta usługi Automation:

1. Usuwanie rozwiązań.
2. Odłącz obszar roboczy.
3. Przenieś konto usługi Automation.
4. Usuń i ponownie Utwórz konta Uruchom jako.
5. Ponownie Włącz swoje rozwiązania.

## <a name="remove-solutions"></a>Usuwanie rozwiązań

Aby odłączyć obszar roboczy od konta usługi Automation, te rozwiązania muszą zostać usunięte z obszaru roboczego:
- **Change Tracking i spis**
- **Zarządzanie aktualizacjami**
- **Uruchamianie/zatrzymywanie maszyn wirtualnych poza godzinami pracy**

W grupie zasobów Znajdź każde rozwiązanie i wybierz pozycję **Usuń**. Na stronie **usuwanie zasobów** Potwierdź zasoby, które mają zostać usunięte, a następnie wybierz pozycję **Usuń**.

![Usuwanie rozwiązań z Azure Portal](../media/move-account/delete-solutions.png)

To samo zadanie można wykonać za pomocą polecenia cmdlet [Remove-AzureRmResource](/powershell/module/azurerm.resources/remove-azurermresource) , jak pokazano w następującym przykładzie:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Dodatkowe kroki dla uruchamiania/zatrzymywania maszyn wirtualnych

W przypadku rozwiązania do **uruchamiania/zatrzymywania maszyn wirtualnych** należy również usunąć reguły alertów utworzone przez rozwiązanie.

W Azure Portal przejdź do grupy zasobów i wybierz pozycję **monitorowanie** > **alerty** > **Zarządzanie regułami alertów**.

![Strona alertów przedstawiająca wybór opcji Zarządzaj regułami alertów](../media/move-account/alert-rules.png)

Na stronie **reguły** powinna zostać wyświetlona lista alertów skonfigurowanych w tej grupie zasobów. Rozwiązanie **uruchamiania/zatrzymywania maszyn wirtualnych** tworzy trzy reguły alertów:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Wybierz te trzy reguły alertów, a następnie wybierz pozycję **Usuń**. Ta akcja spowoduje usunięcie tych reguł alertów.

![Strona reguł żąda potwierdzenia usunięcia dla wybranych reguł](../media/move-account/delete-rules.png)

> [!NOTE]
> Jeśli nie widzisz żadnych reguł alertów na stronie **reguły** , Zmień **stan** , aby wyświetlić **wyłączone** alerty, ponieważ mogły zostać wyłączone.

Po usunięciu reguł alertów Usuń grupę akcji utworzoną dla powiadomień o rozwiązaniu **uruchamiania/zatrzymywania maszyn wirtualnych** .

W Azure Portal wybierz kolejno pozycje **monitorowanie** > **alerty** > **Zarządzanie grupami akcji**.

Z listy wybierz pozycję **StartStop_VM_Notification** . Na stronie Grupa akcji wybierz pozycję **Usuń**.

![Strona grupy akcji, wybierz pozycję Usuń.](../media/move-account/delete-action-group.png)

Podobnie można usunąć grupę akcji przy użyciu programu PowerShell za pomocą polecenia cmdlet [Remove-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) , jak pokazano w następującym przykładzie:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Odłącz obszar roboczy

W Azure Portal wybierz pozycję **konto usługi Automation** > **powiązane zasoby** > **połączony obszar roboczy**. Wybierz opcję **Odłącz obszar roboczy** , aby odłączyć obszar roboczy od konta usługi Automation.

![Odłączanie obszaru roboczego od konta usługi Automation](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Przenoszenie konta usługi Automation

Po usunięciu poprzednich elementów możesz kontynuować usuwanie konta usługi Automation i jego elementów Runbook. W Azure Portal przejdź do grupy zasobów konta usługi Automation. Wybierz pozycję **przenieś** > **Przenieś do innej subskrypcji**.

![Strona grupy zasobów, Przenieś do innej subskrypcji](../media/move-account/move-resources.png)

Wybierz zasoby w grupie zasobów, które chcesz przenieść. Upewnij się, że dołączysz Twoje **konto usługi Automation**, **element Runbook**i **log Analytics zasoby obszaru roboczego** .

Po zakończeniu przenoszenia trzeba wykonać dodatkowe czynności, aby wszystko działało.

## <a name="re-create-run-as-accounts"></a>Ponowne tworzenie kont Uruchom jako

[Konta Uruchom jako](../manage-runas-account.md) tworzą nazwę główną usługi w Azure Active Directory do uwierzytelniania w zasobach platformy Azure. Gdy zmienisz subskrypcje, konto usługi Automation nie używa już istniejącego konta Uruchom jako.

Przejdź do konta usługi Automation w nowej subskrypcji i wybierz pozycję **konta Uruchom jako** w obszarze **Ustawienia konta**. Zobaczysz, że konta Uruchom jako są wyświetlane jako niekompletne.

![Konta Uruchom jako są niekompletne](../media/move-account/run-as-accounts.png)

Wybierz każde konto Uruchom jako. Na stronie **Właściwości** wybierz pozycję **Usuń** , aby usunąć konto Uruchom jako.

> [!NOTE]
> Jeśli nie masz uprawnień do tworzenia lub wyświetlania kont Uruchom jako, zobaczysz następujący komunikat: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` aby dowiedzieć się więcej o uprawnieniach wymaganych do skonfigurowania konta Uruchom jako, zobacz [uprawnienia wymagane do skonfigurowania kont Uruchom jako](../manage-runas-account.md#permissions).

Po usunięciu kont Uruchom jako wybierz pozycję **Utwórz** w obszarze **konto Uruchom jako platformy Azure**. Na stronie **Dodawanie konta Uruchom jako platformy Azure** wybierz pozycję **Utwórz** , aby utworzyć konto Uruchom jako i nazwę główną usługi. Powtórz powyższe kroki przy użyciu **klasycznego konta Uruchom jako platformy Azure**.

## <a name="enable-solutions"></a>Włączanie rozwiązań

Po ponownym utworzeniu konta Uruchom jako należy ponownie włączyć rozwiązania, które zostały usunięte przed przeniesieniem. Aby włączyć **Change Tracking i spis** i **Update Management**, wybierz odpowiednią możliwość na koncie usługi Automation. Wybierz obszar roboczy Log Analytics przesunięty, a następnie wybierz pozycję **Włącz**.

![Ponowne włączanie rozwiązań na przenoszonym koncie usługi Automation](../media/move-account/reenable-solutions.png)

Maszyny, które są dołączone do Twoich rozwiązań, będą widoczne po połączeniu istniejącego obszaru roboczego Log Analytics.

Aby włączyć **Uruchamianie/zatrzymywanie maszyn wirtualnych** w ramach rozwiązania off-godzinnego, należy ponownie wdrożyć rozwiązanie. W obszarze **powiązane zasoby**wybierz pozycję **Start/zatrzymaj maszyny wirtualne** > **Dowiedz się więcej o tym, jak włączyć rozwiązanie** > **Utwórz** , aby rozpocząć wdrażanie.

Na stronie **Dodawanie rozwiązania** wybierz obszar roboczy log Analytics i konto usługi Automation.

![Menu Dodaj rozwiązanie](../media/move-account/add-solution-vm.png)

Aby uzyskać szczegółowe instrukcje dotyczące konfigurowania rozwiązania, zobacz [Start/Stop VMS during off-hours rozwiązanie w Azure Automation](../automation-solution-vm-management.md).

## <a name="post-move-verification"></a>Weryfikacja po przeniesieniu

Po zakończeniu przenoszenia Sprawdź poniższą listę zadań, które powinny zostać zweryfikowane:

|Możliwość|Testy|Link do rozwiązywania problemów|
|---|---|---|
|Elementy Runbook|Pomyślnie można uruchomić element Runbook i połączyć się z zasobami platformy Azure.|[Rozwiązywanie problemów z elementami Runbook](../troubleshoot/runbooks.md)
|Kontrola źródła|Możesz uruchomić ręczną synchronizację w repozytorium kontroli źródła.|[Integracja kontroli źródła](../source-control-integration.md)|
|Śledzenie zmian i spis|Sprawdź, czy na maszynach są widoczne bieżące dane spisu.|[Rozwiązywanie problemów ze śledzeniem zmian](../troubleshoot/change-tracking.md)|
|Zarządzanie aktualizacjami|Sprawdź, czy Twoje maszyny są widoczne i są w dobrej kondycji.</br>Uruchom testowe wdrożenie aktualizacji oprogramowania.|[Rozwiązywanie problemów z zarządzaniem aktualizacjami](../troubleshoot/update-management.md)|
|Współdzielone zasoby|Sprawdź, czy są widoczne wszystkie zasoby udostępnione, takie jak [poświadczenia](../shared-resources/credentials.md), [zmienne](../shared-resources/variables.md)itp.|

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat przenoszenia zasobów na platformie Azure, zobacz [przenoszenie zasobów na platformie Azure](../../azure-resource-manager/move-support-resources.md).
