---
title: Korzystanie z modułów Az w usłudze Azure Automation
description: Ten artykuł zawiera informacje przy użyciu modułów Az w usłudze Azure Automation
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 02/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c722dc6af2b98adb60045d530bb38de7762027d5
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477895"
---
# <a name="az-module-support-in-azure-automation"></a>Obsługa modułu az w usłudze Azure Automation

Usługa Azure automation obsługuje możliwość używania [modułu Azure Powershell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) w elementach runbook. Moduł Az nie jest importowany automatycznie w nowych lub istniejących kont usługi Automation. W tym artykule omówiono sposób używania modułów Az za pomocą usługi Azure Automation.

## <a name="considerations"></a>Zagadnienia do rozważenia

Istnieje wiele rzeczy, aby wziąć pod uwagę podczas korzystania z modułu Az w usłudze Azure Automation. Elementy Runbook i modułów może służyć przez wyższego poziomu rozwiązania na koncie usługi Automation. Edytowanie elementów runbook lub uaktualnienie modułów potencjalnie mogą powodować problemy z poziomu elementów runbook. Należy przetestować wszystkich elementów runbook i rozwiązań dokładnie w oddzielne konta usługi Automation przed zaimportowaniem nowy `Az` modułów. Wszelkie modyfikacje moduły może negatywnie wpłynąć na wyższym poziomie rozwiązań, takich jak rozwiązanie Update Management i uruchamianie/zatrzymywanie maszyn wirtualnych poza godzinami poza godzinami pracy. Zaleca się nie zmieniać modułów i elementami runbook w ramach kont usługi Automation, które zawiera wszystkie rozwiązania. To zachowanie nie jest specyficzne dla modułów Az. To zachowanie powinien brana pod uwagę w przypadku wprowadzenia zmian do konta usługi Automation.

Importowanie `Az` modułu na koncie usługi Automation nie automatycznie zaimportować moduł w sesji programu PowerShell używanego przez elementy runbook. Moduły są importowane do sesji programu PowerShell w następujących sytuacjach:

* Po użyciu polecenia cmdlet z modułu jest wywoływana z poziomu elementu runbook
* Gdy element runbook importuje go jawnie `Import-Module` polecenia cmdlet
* Gdy inny moduł, w zależności od tego, moduł są importowane do sesji programu PowerShell

> [!IMPORTANT]
> Jest ważne upewnić się, że elementy runbook w ramach konta usługi Automation, albo importować tylko `Az` lub `AzureRM` moduły do sesji programu PowerShell, używane przez elementy runbook, a nie w obu. Jeśli `Az` jest importowany przed `AzureRM` w elemencie runbook, element runbook zostanie ukończone, ale [błąd odwoływaniu się do metody get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) będzie widoczna w strumieni zadań oraz poleceń cmdlet może nie zostały prawidłowo wykonany. Jeśli importujesz `AzureRM` i następnie `Az` element runbook będzie nadal czynności, ale zostanie wyświetlony błąd informujący, że strumienie zadania zarówno `Az` i `AzureRM` nie mogą być importowane w tej samej sesji ani używane w ten sam element runbook.

## <a name="migrating-to-az-modules"></a>Migrowanie do modułów Az

Zalecane jest przetestowanie migracji przy użyciu modułów Az zamiast modułów AzureRM w teście konta usługi Automation. Po utworzeniu tego konta usługi Automation umożliwia następujące czynności upewnij się, że migracja przechodzi sprawnie:

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>Zatrzymaj i Usuń z harmonogramu wszystkich elementu runbook, które są używane moduły AzureRM

Aby upewnić się, nie zostanie uruchomione istniejące elementy runbook, używanego przez `AzureRM` poleceń cmdlet, należy zatrzymać i Usuń wszystkie elementy runbook, które korzystają z harmonogramu element `AzureRM` modułów. Możesz zobaczyć, jakie harmonogramy istnieje i które harmonogramy muszą być usunięte, uruchamiając poniższy przykład:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Jest ważne zapoznać się z każdym harmonogramie oddzielnie, aby upewnić się, że można ponownie zaplanować go w przyszłości dla elementów runbook w razie potrzeby.

### <a name="import-the-az-modules"></a>Importowanie modułów Az

Importuj tylko moduły Az, które są wymagane dla elementów runbook. Nie Importuj pakiet zbiorczy `Az` modułu w postaci, w jakiej znajdują się wszystkie `Az.*` modułów do zaimportowania. Niniejsze wskazówki są takie same dla wszystkich modułów.

[Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) modułu jest zależnością dla siebie `Az.*` modułów. Z tego powodu ten moduł musi zostać zaimportowany do konta usługi Automation, przed zaimportowaniem inne moduły.

Z poziomu konta usługi Automation wybierz **modułów** w obszarze **zasoby udostępnione**. Kliknij przycisk **Przeglądaj galerię** otworzyć **Przeglądaj galerię** strony.  Na pasku wyszukiwania wprowadź nazwę modułu (takie jak `Az.Accounts`). Na stronie modułu programu PowerShell, kliknij **zaimportować** Aby zaimportować moduł do konta usługi Automation.

![Importuj moduły z konta usługi Automation](media/az-modules/import-module.png)

Ten proces importowania można również wykonać za pomocą [galerii programu PowerShell](https://www.powershellgallery.com) , wyszukując moduł. Po znalezieniu moduł, zaznacz go i w obszarze **usługi Azure Automation** kliknij pozycję **wdrażanie w usłudze Azure Automation**.

![Zaimportować moduły bezpośrednio z galerii](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>Testowanie elementów runbook

Raz `Az` moduły są importowane na koncie usługi Automation, możesz teraz rozpocząć edycję Twoje elementy runbook, aby zamiast tego użyj modułu Az. Większość poleceń cmdlet mają taką samą nazwę, z wyjątkiem `AzureRM` został zmieniony na `Az`. Aby uzyskać listę modułów, które nie podlegają tym procesie, zobacz [listy wyjątków](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Jednym ze sposobów, do testowania elementów runbook przed Modyfikowanie elementu runbook za pomocą nowych poleceń cmdlet przy użyciu `Enable-AzureRMAlias -Scope Process` na początku elementu runbook. Dodając to do elementu runbook, element runbook można uruchomić bez konieczności wprowadzania zmian.

## <a name="after-migration-details"></a>Po szczegóły migracji

Po zakończeniu migracji nie uruchamiaj elementy runbook przy użyciu `AzureRM` modułów na koncie dłużej. Zalecane jest również nie importować lub zaktualizuj `AzureRM` modułów na tym koncie. Począwszy od tej chwili należy wziąć pod uwagę migracji do tego konta `Az`i działają z `Az` tylko moduły. Nowe konto usługi Automation utworzenia istniejące `AzureRM` modułów pozostaną zainstalowane, i elementów runbook z samouczka, nadal będzie można utworzone przy użyciu `AzureRM` polecenia cmdlet. Nie można uruchamiać tych elementów runbook.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat używania modułów Az, zobacz [rozpoczęcie korzystania z modułu Az](/powershell/azure/get-started-azureps?view=azps-1.1.0).
