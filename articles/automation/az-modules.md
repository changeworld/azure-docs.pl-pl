---
title: Korzystanie z modułów Az w usłudze Azure Automation
description: Ten artykuł zawiera informacje przy użyciu modułów Az w usłudze Azure Automation
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: dfbf54c19aef00cbda886a4531797cda7ef3a191
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986108"
---
# <a name="az-module-support-in-azure-automation"></a>Obsługa modułu Az w usłudze Azure Automation

Automatyzacja platformy Azure obsługuje możliwość korzystania z [modułu Azure Powershell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) w uruchomieniu. y. Moduł Az nie jest importowany automatycznie na żadnych nowych lub istniejących kontach automatyzacji. W tym artykule omówiono sposób korzystania z modułów Az z usługą Azure Automation.

## <a name="considerations"></a>Zagadnienia do rozważenia

Istnieje wiele rzeczy, które należy wziąć pod uwagę podczas korzystania z modułu Az w usłudze Azure Automation. Elementy runbook i moduły mogą być używane przez rozwiązania wyższego poziomu na koncie automatyzacji. Edytowanie ekscesów lub uaktualnianie modułów może potencjalnie powodować problemy z uruchomieniu. kamiłębień. Przed zaimportowaniem nowych `Az` modułów należy dokładnie przetestować wszystkie elementy runbook i rozwiązania na osobnym koncie automatyzacji. Wszelkie modyfikacje modułów mogą negatywnie wpłynąć na rozwiązanie [Start/Stop.](automation-solution-vm-management.md) Nie zaleca się zmieniania modułów i śmigieł w kontach automatyzacji, które zawierają żadnych rozwiązań. To zachowanie nie jest specyficzne dla modułów Az. To zachowanie należy wziąć pod uwagę przy wprowadzaniu jakichkolwiek zmian na koncie automatyzacji.

Importowanie `Az` modułu na koncie automatyzacji nie powoduje automatycznego zaimportowania modułu w sesji programu PowerShell używanej przez elementy runbook. Moduły są importowane do sesji programu PowerShell w następujących sytuacjach:

* Gdy polecenie cmdlet z modułu jest wywoływane z egoletu
* Gdy projekt runbook importuje go `Import-Module` jawnie z poleceniem cmdlet
* Gdy inny moduł w zależności od modułu jest importowany do sesji programu PowerShell

> [!IMPORTANT]
> Ważne jest, aby upewnić się, że elementy runbook `AzureRM` na koncie automatyzacji tylko import lub `Az` moduły do sesji programu PowerShell używane przez elementy runbook, a nie oba. Jeśli `Az` jest importowany wcześniej `AzureRM` w yjpotówku, element runbook zostanie ukończony, ale błąd [odwołujący się do metody get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) zostanie wyświetlony w strumieniach zadań i poleceniach cmdlet może nie zostały poprawnie wykonane. Jeśli `AzureRM` importujesz, `Az` a następnie element runbook będzie nadal kompletny, ale zobaczysz błąd `Az` `AzureRM` w strumieniach zadań stwierdzające, że zarówno i nie mogą być importowane w tej samej sesji lub używane w tym samym elementem runbook.

## <a name="migrating-to-az-modules"></a>Migracja do modułów Az

Zaleca się przetestowanie migracji do korzystania z modułów Az zamiast modułów AzureRM na koncie automatyzacji testów. Po utworzeniu konta automatyzacji można wykonać następujące czynności, aby upewnić się, że migracja przebiega sprawnie:

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>Zatrzymywać i odcinać wszystkie księgi runbook, która używa modułów AzureRM

Aby upewnić się, że nie uruchamiasz `AzureRM` żadnych istniejących śmięty, które używają poleceń cmdlet, należy zatrzymać i odsunąć wszystkie programy runbook korzystające z `AzureRM` modułów. Możesz zobaczyć, jakie harmonogramy istnieją i które harmonogramy muszą zostać usunięte, uruchamiając następujący przykład:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Ważne jest, aby przejrzeć każdy harmonogram oddzielnie, aby upewnić się, że można go zremisować w przyszłości dla śmiętków runbook, jeśli to konieczne.

### <a name="import-the-az-modules"></a>Importowanie modułów Az

Importuj tylko moduły Az, które są wymagane dla śmiętków. Nie importuj modułu zbiorczego, `Az` ponieważ `Az.*` zawiera on wszystkie moduły do zaimportowania. Ta wskazówka jest taka sama dla wszystkich modułów.

Moduł [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) jest zależnością dla `Az.*` innych modułów. Z tego powodu ten moduł musi zostać zaimportowany do konta automatyzacji przed zaimportowanie innych modułów.

Z konta automatyzacji wybierz **pozycję Moduły** w obszarze **Zasoby udostępnione**. Kliknij **pozycję Przeglądaj galerię,** aby otworzyć stronę **Przeglądaj galerię.**  Na pasku wyszukiwania wprowadź nazwę modułu `Az.Accounts`(np. ). Na stronie Moduł programu PowerShell kliknij przycisk **Importuj,** aby zaimportować moduł do konta automatyzacji.

![Importowanie modułów z konta automatyzacji](media/az-modules/import-module.png)

Ten proces importowania można również wykonać za pośrednictwem [galerii programu PowerShell,](https://www.powershellgallery.com) wyszukując moduł. Po znalezieniu modułu wybierz go i na karcie **Usługa Azure Automation** kliknij pozycję **Wdrażanie w usłudze Azure Automation**.

![Importowanie modułów bezpośrednio z galerii](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>Testowanie eks-ów

Po `Az` zaimportowaniu modułów na konto automatyzacji można teraz rozpocząć edycję śmięty, aby zamiast tego użyć modułu Az. Większość poleceń cmdlet ma tę samą nazwę, `Az`z wyjątkiem została zmieniona na `AzureRM` . Aby uzyskać listę modułów, które nie są zgodne z tym procesem, zobacz [listę wyjątków](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Jednym ze sposobów przetestowania śmięty przed zmodyfikowaniem śmiętkowego w celu użycia nowych poleceń cmdlet jest użycie `Enable-AzureRMAlias -Scope Process` na początku śmiętu. Dodając to do egobiegu, system runbook może działać bez zmian.

## <a name="after-migration-details"></a>Szczegóły po migracji

Po zakończeniu migracji nie uruchamiaj już śmiób przy użyciu `AzureRM` modułów na koncie. Zaleca się również, aby nie `AzureRM` importować ani nie aktualizować modułów na tym koncie. Począwszy od tego momentu, należy `Az`wziąć pod `Az` uwagę to konto przeniesione do , i działać tylko z modułami. Po utworzeniu nowego konta `AzureRM` automatyzacji istniejące moduły będą nadal instalowane, a `AzureRM` podręczniki runbook będą nadal tworzone za pomocą poleceń cmdlet. Te programy runbook nie powinny być uruchamiane.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o korzystaniu z modułów Az, zobacz [Wprowadzenie do modułu Az](/powershell/azure/get-started-azureps?view=azps-1.1.0).
