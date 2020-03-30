---
title: Przenoszenie konta usługi Azure Automation do innej subskrypcji
description: W tym artykule opisano sposób przenoszenia konta automatyzacji do innej subskrypcji
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1aa759a2984764169eb28935e095d0f7c0f90c08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969827"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Przenoszenie konta usługi Azure Automation do innej subskrypcji

Platforma Azure umożliwia przeniesienie niektórych zasobów do nowej grupy zasobów lub subskrypcji. Można przenieść zasoby za pośrednictwem witryny Azure portal, PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. Aby dowiedzieć się więcej o tym procesie, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Konta usługi Azure Automation są jednym z zasobów, które można przenieść. W tym artykule dowiesz się, jak przenieść konta automatyzacji do innego zasobu lub subskrypcji.

Kroki wysokiego poziomu do przenoszenia konta automatyzacji są:

1. Usuń swoje rozwiązania.
2. Odłącz obszar roboczy.
3. Przenoszenie konta automatyzacji.
4. Usuń i ponownie utwórz konta Uruchom jako.
5. Włącz ponownie swoje rozwiązania.

## <a name="remove-solutions"></a>Usuń roztwory

Aby odłączyć obszar roboczy od konta automatyzacji, te rozwiązania muszą zostać usunięte z obszaru roboczego:
- **Śledzenie zmian i spis**
- **Zarządzanie aktualizacjami**
- **Start/stop maszyn wirtualnych poza godzinami pracy**

W grupie zasobów znajdź każde rozwiązanie i wybierz pozycję **Usuń**. Na stronie **Usuwanie zasobów** potwierdź zasoby do usunięcia i wybierz pozycję **Usuń**.

![Usuwanie rozwiązań z witryny Azure portal](../media/move-account/delete-solutions.png)

Można wykonać to samo zadanie za pomocą polecenia cmdlet [Remove-AzureRmResource,](/powershell/module/azurerm.resources/remove-azurermresource) jak pokazano w poniższym przykładzie:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Dodatkowe kroki dla maszyn wirtualnych Start/Stop

Dla rozwiązania **Start/Stop maszyn wirtualnych,** należy również usunąć reguły alertów utworzone przez rozwiązanie.

W witrynie Azure portal przejdź do grupy zasobów i wybierz pozycję **Monitorowanie** > alertów > **Zarządzaj regułami****alertów**.

![Strona Alerty z wyborem reguł Zarządzania alertami](../media/move-account/alert-rules.png)

Na stronie **Reguły** powinna zostać wyświetlona lista alertów skonfigurowanych w tej grupie zasobów. Rozwiązanie **Start/Stop VMs** tworzy trzy reguły alertów:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Wybierz te trzy reguły alertów, a następnie wybierz pozycję **Usuń**. Ta akcja spowoduje usunięcie tych reguł alertów.

![Strona reguł z prośbą o potwierdzenie usunięcia dla wybranych reguł](../media/move-account/delete-rules.png)

> [!NOTE]
> Jeśli nie widzisz żadnych reguł alertów na stronie **Reguły,** zmień **stan,** aby wyświetlić **wyłączone** alerty, ponieważ możesz je wyłączyć.

Po usunięciu reguł alertów usuń grupę akcji utworzoną dla powiadomień o rozwiązaniu **Start/Stop VMs.**

W witrynie Azure portal wybierz pozycję **Monitor alerty** > **Zarządzaj****grupami** > akcji .

Wybierz **StartStop_VM_Notification** z listy. Na stronie grupy akcji wybierz pozycję **Usuń**.

![Strona grupy akcji, wybierz usuń](../media/move-account/delete-action-group.png)

Podobnie można usunąć grupę akcji przy użyciu programu PowerShell z poleceniem cmdlet [Remove-AzureRmActionGroup,](/powershell/module/azurerm.insights/remove-azurermactiongroup) jak pokazano w poniższym przykładzie:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Odłączanie obszaru roboczego

W witrynie Azure portal wybierz > **obszar roboczy Połączone****zasoby powiązane z kontem** >  **automatyzacji**. Wybierz **opcję Odłącz obszar roboczy,** aby odłączyć obszar roboczy od konta automatyzacji.

![Odłączanie obszaru roboczego od konta automatyzacji](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Przenoszenie konta automatyzacji

Po usunięciu poprzednich elementów można kontynuować usuwanie konta automatyzacji i jego elementów runbook. W witrynie Azure portal przejdź do grupy zasobów konta automatyzacji. Wybierz **pozycję Przenieś** > **przenieś do innej subskrypcji**.

![Strona grupy zasobów, przejście do innej subskrypcji](../media/move-account/move-resources.png)

Wybierz zasoby w grupie zasobów, które chcesz przenieść. Upewnij się, że dołączasz **swoje konto automatyzacji,** **runbook**i zasoby **obszaru roboczego usługi Log Analytics.**

Po zakończeniu przenoszenia wymagane są dodatkowe kroki, aby wszystko działało.

## <a name="re-create-run-as-accounts"></a>Odtwórz konta Uruchom jako

[Uruchom jako konta](../manage-runas-account.md) utworzyć jednostkę usługi w usłudze Azure Active Directory do uwierzytelniania przy użyciu zasobów platformy Azure. Po zmianie subskrypcji konto automatyzacji nie używa już istniejącego konta Uruchom jako.

Przejdź do swojego konta Automatyzacji w nowej subskrypcji i wybierz pozycję **Uruchom jako konta** w obszarze Ustawienia **konta**. Zobaczysz, że konta Uruchom jako są teraz wyświetlane jako niekompletne.

![Uruchom jako konta są niekompletne](../media/move-account/run-as-accounts.png)

Wybierz każde konto Uruchom jako. Na stronie **Właściwości** wybierz pozycję **Usuń,** aby usunąć konto Uruchom jako.

> [!NOTE]
> Jeśli nie masz uprawnień do tworzenia lub wyświetlania kont Uruchom jako, `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` zostanie wyświetlony następujący komunikat: Aby dowiedzieć się więcej o uprawnieniach wymaganych do skonfigurowania konta Uruchom jako, zobacz [Uprawnienia wymagane do skonfigurowania kont Uruchom jako](../manage-runas-account.md#permissions).

Po usunięciu konta Uruchom jako wybierz pozycję **Utwórz** w obszarze **Konto Azure Uruchom jako**. Na stronie **Dodaj konto Azure Run jako** wybierz pozycję **Utwórz,** aby utworzyć jednostkę konta i usługi Uruchom jako. Powtórz poprzednie kroki za pomocą **konta Azure Classic Run As**.

## <a name="enable-solutions"></a>Włączanie rozwiązań

Po ponownym utworzeniu konta Uruchom jako ponownie włączysz rozwiązania usunięte przed przeniesieniem. Aby włączyć **śledzenie zmian oraz zarządzanie zapasami** i **aktualizacjami,** wybierz odpowiednią funkcję na koncie automatyzacji. Wybierz obszar roboczy usługi Log Analytics, który został przeniesiony, a następnie wybierz pozycję **Włącz**.

![Ponowne włączanie rozwiązań na przeniesionym koncie automatyzacji](../media/move-account/reenable-solutions.png)

Maszyny, które są wbudowane w rozwiązania będą widoczne po podłączeniu istniejącego obszaru roboczego usługi Log Analytics.

Aby włączyć **maszyny wirtualne Start/Stop** podczas rozwiązania poza godzinami pracy, musisz ponownie wdrożyć rozwiązanie. W obszarze **Powiązane zasoby**wybierz **pozycję Start/Stop VMs** > **Dowiedz się więcej o rozwiązaniu** > **Tworzenie** i włączanie rozwiązania w celu uruchomienia wdrożenia.

Na stronie **Dodawanie rozwiązania** wybierz swoje konto robocze usługi Log Analytics i automatyzacja.

![Menu Dodaj rozwiązanie](../media/move-account/add-solution-vm.png)

Aby uzyskać szczegółowe instrukcje dotyczące konfigurowania rozwiązania, zobacz [Uruchamianie/zatrzymywanie maszyn wirtualnych podczas rozwiązania poza godzinami pracy w usłudze Azure Automation.](../automation-solution-vm-management.md)

## <a name="post-move-verification"></a>Weryfikacja po przeniesieniu

Po zakończeniu przenoszenia sprawdź następującą listę zadań, które powinny zostać zweryfikowane:

|Możliwości|Testy|Łącze rozwiązywania problemów|
|---|---|---|
|Elementy Runbook|Zestaw runbook można pomyślnie uruchomić i połączyć się z zasobami platformy Azure.|[Rozwiązywanie problemów z podręcznikami runbook](../troubleshoot/runbooks.md)
|Kontrola źródła|Synchronizację ręczną można uruchomić w repozytorium kontroli źródła.|[Integracja z kontrolą źródła](../source-control-integration.md)|
|Śledzenie zmian i zapasy|Sprawdź, czy widzisz bieżące dane spisu z twoich komputerów.|[Rozwiązywanie problemów ze śledzeniem zmian](../troubleshoot/change-tracking.md)|
|Zarządzanie aktualizacjami|Sprawdź, czy widzisz swoje maszyny i są one zdrowe.</br>Uruchom wdrożenie aktualizacji oprogramowania testowego.|[Rozwiązywanie problemów z zarządzaniem aktualizacjami](../troubleshoot/update-management.md)|
|Udostępnione zasoby|Sprawdź, czy widzisz wszystkie zasoby udostępnione, takie jak [poświadczenia,](../shared-resources/credentials.md) [zmienne](../shared-resources/variables.md)itp.|

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o przenoszeniu zasobów na platformie Azure, zobacz [Przenoszenie zasobów na platformie Azure](../../azure-resource-manager/management/move-support-resources.md).
