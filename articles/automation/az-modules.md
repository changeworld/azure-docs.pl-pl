---
title: Obsługa modułu Az w usłudze Azure Automation
description: Ten artykuł zawiera informacje dotyczące korzystania z modułów Az w usłudze Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: 776834937d81a3ba84e3c1db56496a7d951d7982
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548362"
---
# <a name="az-module-support-in-azure-automation"></a>Obsługa modułu Az w usłudze Azure Automation

Usługa Azure Automation obsługuje korzystanie z [modułu Azure PowerShell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) w twoich uruchomieniu. ysieci. Moduł Az nie jest importowany automatycznie na żadnych nowych lub istniejących kontach automatyzacji. 

## <a name="considerations"></a>Zagadnienia do rozważenia

Istnieje wiele rzeczy, które należy wziąć pod uwagę podczas korzystania z modułu Az w usłudze Azure Automation. Elementy runbook i moduły mogą być używane przez rozwiązania wyższego poziomu na koncie automatyzacji. Edytowanie ekscesów lub uaktualnianie modułów może potencjalnie powodować problemy z uruchomieniu. kamiłębień. Przed zaimportowaniem nowych `Az` modułów należy dokładnie przetestować wszystkie elementy runbook i rozwiązania na osobnym koncie automatyzacji. Wszelkie modyfikacje modułów mogą negatywnie wpłynąć na rozwiązanie [Start/Stop.](automation-solution-vm-management.md) Nie zaleca się zmieniania modułów i śmigieł na kontach automatyzacji, które zawierają rozwiązania. To zachowanie nie jest specyficzne dla modułów Az. To zachowanie należy wziąć pod uwagę podczas wprowadzania jakichkolwiek zmian do konta automatyzacji.

Importowanie `Az` modułu na koncie automatyzacji nie powoduje automatycznego importowania modułu w sesji programu PowerShell używanej przez elementy runbook. Moduły są importowane do sesji programu PowerShell w następujących sytuacjach:

* Gdy polecenie cmdlet z modułu jest wywoływane z egoletu
* Gdy projekt runbook importuje go `Import-Module` jawnie z poleceniem cmdlet
* Gdy inny moduł w zależności od modułu jest importowany do sesji programu PowerShell

> [!IMPORTANT]
> Ważne jest, aby upewnić się, że elementy runbook `AzureRM` na koncie automatyzacji tylko import lub `Az` moduły do sesji programu PowerShell używane przez elementy runbook, a nie oba. Jeśli `Az` jest importowany wcześniej `AzureRM` w wiązce uruchomieniu, element runbook kończy, ale błąd odwołujący się do polecenia cmdlet [Get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) pojawia się w strumieniach zadań i polecenia cmdlet może nie być poprawnie wykonane. Jeśli `AzureRM` importujesz, `Az`a następnie, element runbook nadal jest kompletny, ale w `Az` strumieniach zadań pojawia się błąd informujący, że oba i `AzureRM` nie można zaimportować w tej samej sesji lub użyć go w tym samym uruchomieniu. ścieleń.

## <a name="migrating-to-az-modules"></a>Migracja do modułów Az

Zaleca się przetestowanie migracji do modułów Az na koncie automatyzacji testu. Po utworzeniu tego konta automatyzacji, można użyć instrukcji w tej sekcji do pracy z modułami.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-cmdlets"></a>Zatrzymywać i odcinać wszystkie programy runbook korzystające z poleceń cmdlet azurerm

Aby upewnić się, że nie uruchamiasz `AzureRM` żadnych istniejących śmięty, które używają poleceń cmdlet, należy zatrzymać i odsunąć wszystkie programy runbook korzystające z `AzureRM` modułów. Można zobaczyć, jakie harmonogramy istnieją i które harmonogramy muszą zostać usunięte przez uruchomienie kodu podobnego do tego przykładu.

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Ważne jest, aby przejrzeć każdy harmonogram oddzielnie, aby upewnić się, że można go ponownie zaplanować w przyszłości dla śmiętków runbook, jeśli to konieczne.

### <a name="import-the-az-modules"></a>Importowanie modułów Az

Importuj tylko moduły Az, które są wymagane dla śmiętków. Nie importuj moduł `Az` zestawienia, ponieważ `Az.*` zawiera on wszystkie moduły. Ta wskazówka jest taka sama dla wszystkich modułów.

Moduł [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) jest zależnością dla `Az.*` innych modułów. Z tego powodu ten moduł musi zostać zaimportowany do konta automatyzacji przed zaimportowanie innych modułów.

Na koncie automatyzacji wybierz pozycję **Moduły** w obszarze **Zasoby udostępnione**. Kliknij **pozycję Przeglądaj galerię,** aby otworzyć stronę **Przeglądaj galerię.**  Na pasku wyszukiwania wprowadź nazwę modułu `Az.Accounts`(np. ). Na stronie Moduł programu PowerShell kliknij przycisk **Importuj,** aby zaimportować moduł do konta automatyzacji.

![Importowanie modułów z konta automatyzacji](media/az-modules/import-module.png)

Ten proces importowania można również wykonać za pośrednictwem [galerii programu PowerShell,](https://www.powershellgallery.com) wyszukując moduł do importu. Po znalezieniu modułu wybierz go i na karcie **Usługa Azure Automation** kliknij pozycję **Wdrażanie w usłudze Azure Automation**.

![Importowanie modułów bezpośrednio z galerii](media/az-modules/import-gallery.png)

## <a name="testing-your-runbooks"></a>Testowanie eks-ów

Po `Az` zaimportowaniu modułów do konta automatyzacji można rozpocząć edycję śmigierów ekwiwania, aby korzystać z modułów Az. Większość poleceń cmdlet ma te same `AzureRM` nazwy, `Az`z wyjątkiem tej samej nazwy, która została zmieniona na . Aby uzyskać listę modułów, które nie są zgodne z tą konwencją nazewnictwa, zobacz [listę wyjątków](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Jednym ze sposobów przetestowania modyfikacji śmiętu w celu `Enable-AzureRMAlias -Scope Process` użycia nowych poleceń cmdlet jest użycie na początku zestawu runbook. Dodając to polecenie do egobiegu, skrypt może działać bez zmian.

## <a name="after-migration-details"></a>Szczegóły po migracji

Po zakończeniu migracji nie próbuj już uruchamiać śmiób przy użyciu `AzureRM` modułów na koncie automatyzacji. Zaleca się również, aby nie `AzureRM` importować ani nie aktualizować modułów na koncie. Należy wziąć pod `Az`uwagę konto `Az` zmigrowane do , i działać tylko z modułami. 

Po utworzeniu nowego konta automatyzacji istniejące `AzureRM` moduły są nadal instalowane. Nadal można aktualizować podręcznikowe `AzureRM` programy runbook za pomocą poleceń cmdlet. Nie należy uruchamiać tych śmiętków.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o korzystaniu z modułów Az, zobacz [Wprowadzenie do modułu Az](/powershell/azure/get-started-azureps?view=azps-1.1.0).
