---
title: Korzystanie z polecenia AZ modules w Azure Automation
description: Ten artykuł zawiera informacje za pomocą polecenia AZ modules w Azure Automation
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 02/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2f81c0affb78d5944b8ba910cccfa0be655f1a6f
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097950"
---
# <a name="az-module-support-in-azure-automation"></a>Obsługa AZ module w Azure Automation

Usługa Azure Automation obsługuje możliwość korzystania z polecenia [AZ module programu Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) w Twoich elementach Runbook. Funkcja AZ module nie jest zaimportowana automatycznie na żadnym nowym lub istniejącym koncie usługi Automation. W tym artykule omówiono sposób używania AZ modułów z Azure Automation.

## <a name="considerations"></a>Zagadnienia do rozważenia

W przypadku korzystania z polecenia AZ module w Azure Automation należy wziąć pod uwagę wiele rzeczy. Elementy Runbook i moduły mogą być używane przez rozwiązania wyższego poziomu na koncie usługi Automation. Edytowanie elementów Runbook lub uaktualnianie modułów może potencjalnie powodować problemy z elementami Runbook. Przed zaimportowaniem nowych `Az` modułów należy uważnie przetestować wszystkie elementy Runbook i rozwiązania na osobnym koncie usługi Automation. Wszelkie modyfikacje modułów mogą negatywnie rozwiązaniu do [uruchamiania/zatrzymywania](automation-solution-vm-management.md) . Zaleca się, aby nie zmieniać modułów i elementów Runbook na kontach usługi Automation, które zawierają jakiekolwiek rozwiązania. To zachowanie nie jest specyficzne dla AZ modules. Takie zachowanie należy wziąć pod uwagę w przypadku wprowadzenia wszelkich zmian w Twoim koncie usługi Automation.

`Az` Importowanie modułu na koncie usługi Automation nie powoduje automatycznego importowania modułu w sesji programu PowerShell, którego używają elementy Runbook. Moduły są importowane do sesji programu PowerShell w następujących sytuacjach:

* Gdy polecenie cmdlet z modułu jest wywoływane z elementu Runbook
* Gdy element Runbook importuje go jawnie za `Import-Module` pomocą polecenia cmdlet
* Gdy inny moduł w zależności od modułu zostanie zaimportowany do sesji programu PowerShell

> [!IMPORTANT]
> Ważne jest, aby upewnić się, że elementy Runbook na koncie usługi Automation są `Az` tylko `AzureRM` importowane lub moduły do sesji programu PowerShell używanych przez elementy Runbook, a nie obu. Jeśli `Az` program zostanie zaimportowany przed `AzureRM` elementem Runbook, element Runbook zostanie ukończony, ale wystąpił [błąd odwołujący się do metody get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) w strumieniach zadań, a polecenia cmdlet mogą nie zostać prawidłowo wykonane. Jeśli zaimportujesz `AzureRM` program `Az` , a następnie element Runbook nadal zostanie ukończony, ale w strumieniach zadań zostanie wyświetlony błąd informujący o `Az` tym `AzureRM` , że oba elementy i nie mogą zostać zaimportowane w ramach tej samej sesji lub użyte w tym samym elemencie Runbook.

## <a name="migrating-to-az-modules"></a>Migrowanie do AZ modules

Zaleca się przetestowanie migracji w celu użycia polecenia AZ module zamiast modułów AzureRM na koncie usługi Automation. Po utworzeniu konta usługi Automation możesz wykonać następujące czynności, aby upewnić się, że migracja przebiega bezproblemowo:

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>Zatrzymaj i Anuluj harmonogram wszystkich elementów Runbook, które korzystają z modułów AzureRM

Aby upewnić się, że nie uruchomiono żadnych istniejących elementów Runbook `AzureRM` , które korzystają z poleceń cmdlet, należy zatrzymać i anulować planowanie wszystkich elementów `AzureRM` Runbook, które korzystają z modułów. Możesz zobaczyć, jakie harmonogramy istnieją i które harmonogramy muszą zostać usunięte, uruchamiając Poniższy przykład:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Ważne jest, aby osobno przejrzeć poszczególne harmonogramy, aby upewnić się, że w razie potrzeby będzie można zmienić ich harmonogram w przyszłości dla elementów Runbook.

### <a name="import-the-az-modules"></a>Zaimportuj AZ modules

Zaimportuj tylko te moduły, które są wymagane dla elementów Runbook. Nie należy importować modułu `Az` zbiorczego, ponieważ obejmuje on wszystkie `Az.*` moduły do zaimportowania. Te wskazówki są takie same dla wszystkich modułów.

Moduł [AZ. Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) jest zależnością dla innych `Az.*` modułów. Z tego powodu ten moduł musi zostać zaimportowany do konta usługi Automation przed zaimportowaniem innych modułów.

Z poziomu konta usługi Automation wybierz pozycję **moduły** w obszarze **zasoby udostępnione**. Kliknij pozycję **Przeglądaj Galerię** , aby otworzyć stronę **Przeglądaj Galerię** .  Na pasku wyszukiwania wprowadź nazwę modułu (na przykład `Az.Accounts`). Na stronie moduł programu PowerShell kliknij przycisk **Importuj** , aby zaimportować moduł do konta usługi Automation.

![Importuj moduły z konta usługi Automation](media/az-modules/import-module.png)

Ten proces importowania można wykonać za pomocą [Galeria programu PowerShell](https://www.powershellgallery.com) przez wyszukanie modułu. Po znalezieniu modułu zaznacz go, a następnie na karcie **Azure Automation** kliknij pozycję **wdróż, aby Azure Automation**.

![Importuj moduły bezpośrednio z galerii](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>Testowanie elementów Runbook

Po zaimportowaniu `Az` modułów na koncie usługi Automation możesz teraz rozpocząć edytowanie elementów Runbook, aby użyć polecenia AZ module. Większość poleceń cmdlet ma taką samą nazwę, z wyjątkiem programu `AzureRM` , została zmieniona na. `Az` Aby uzyskać listę modułów, które nie są zgodne z tym procesem, zobacz [Lista wyjątków](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Jednym ze sposobów na przetestowanie elementów Runbook Przed zmodyfikowaniem elementu Runbook do korzystania z nowych poleceń cmdlet `Enable-AzureRMAlias -Scope Process` jest użycie na początku elementu Runbook. Po dodaniu go do elementu Runbook, element Runbook można uruchomić bez wprowadzania zmian.

## <a name="after-migration-details"></a>Po zakończeniu migracji

Po zakończeniu migracji nie uruchamiaj dłużej elementów Runbook przy użyciu `AzureRM` modułów na koncie. Zalecane jest również, aby nie importować ani `AzureRM` aktualizować modułów na tym koncie. Od tego momentu należy wziąć pod uwagę to, `Az`że to konto zostało `Az` zmigrowane do i działa tylko z modułami. Po utworzeniu nowego konta usługi Automation nadal będą instalowane `AzureRM` istniejące moduły, a elementy Runbook samouczka nadal będą tworzone za pomocą `AzureRM` poleceń cmdlet. Nie należy uruchamiać tych elementów Runbook.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o korzystaniu z polecenia AZ [modules, zobacz Rozpoczynanie pracy z](/powershell/azure/get-started-azureps?view=azps-1.1.0)poleceniem AZ modułów.
