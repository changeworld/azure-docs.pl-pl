---
title: Obsługa modułu Az w usłudze Azure Automation
description: Ten artykuł zawiera informacje dotyczące korzystania z modułów Az w usłudze Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: a8d6d25a2ba7f0040b13982f14f3d6081ac32f15
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637991"
---
# <a name="az-module-support-in-azure-automation"></a>Obsługa modułu Az w usłudze Azure Automation

Usługa Azure Automation obsługuje korzystanie z [modułu Az programu Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) w uruchomieniu. 105. Moduł Az zestawienia nie jest importowany automatycznie na żadnych nowych lub istniejących kontach automatyzacji. 

## <a name="considerations"></a>Zagadnienia do rozważenia

Istnieje kilka rzeczy, które należy wziąć pod uwagę podczas korzystania z modułów Az w usłudze Azure Automation:

* Rozwiązania wyższego poziomu na koncie automatyzacji mogą używać życzek i modułów. W związku z tym edytowanie książek runbook lub uaktualnianie modułów może potencjalnie powodować problemy z rozwiązaniami. Przed zaimportowaniem nowych modułów Az należy dokładnie przetestować wszystkie elementy runbook i rozwiązania na osobnym koncie automatyzacji. 

* Wszelkie modyfikacje modułów mogą negatywnie wpłynąć na rozwiązanie [Start/Stop.](automation-solution-vm-management.md) 

* Importowanie modułu Az na koncie automatyzacji nie powoduje automatycznego importowania modułu w sesji programu PowerShell używanej przez elementy runbook. Moduły są importowane do sesji programu PowerShell w następujących sytuacjach:

    * Gdy projekt runbook wywołuje polecenie cmdlet z modułu
    * Gdy projekt runbook zaimportuje `Import-Module` moduł jawnie z poleceniem cmdlet
    * Gdy program runbook importuje inny moduł w zależności od modułu

> [!NOTE]
> Nie zaleca się zmieniania modułów i śmigieł na kontach automatyzacji, które zawierają rozwiązania. Ten przepis nie jest specyficzny dla modułów Az. Należy wziąć to pod uwagę przy wprowadzaniu jakichkolwiek zmian na koncie automatyzacji.

> [!IMPORTANT]
> Upewnij się, że elementy runbook na koncie automatyzacji importują moduły Az lub [moduły AzureRM,](https://www.powershellgallery.com/packages/AzureRM/6.13.1) ale nie oba, do sesji programu PowerShell. Jeśli system runbook importuje moduły Az przed modułami AzureRM, system runbook zostanie ukończony. Jednak błąd odwołujący się do polecenia cmdlet [Get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) pojawia się w strumieniach zadań i polecenia cmdlet może nie zostać poprawnie wykonane. Jeśli system runbook importuje moduły AzureRM przed modułami Az, system runbook również kończy. W takim przypadku jednak pojawia się błąd w strumieniach zadań stwierdzające, że nie można zaimportować zarówno usługi Az, jak i AzureRM w tej samej sesji lub użyć w tym samym uruchomieniu. Chybienia.

## <a name="migrating-to-az-modules"></a>Migracja do modułów Az

Zaleca się przetestowanie migracji do modułów Az na koncie automatyzacji testu. Po utworzeniu konta można użyć instrukcji w tej sekcji, aby pracować z modułami.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Zatrzymywać i odcinać wszystkie programy runbook korzystające z modułów AzureRM

Aby upewnić się, że nie uruchamiasz żadnych istniejących wiązek runbook, które używają modułów AzureRM, zatrzymaj i odeślij wszystkie, których dotyczy problem, wywiązują się ze wiązki uruchomieniu. Ścieleń. Możesz zobaczyć, jakie harmonogramy istnieją i które harmonogramy usunąć, uruchamiając kod podobny do tego przykładu:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Ważne jest, aby przejrzeć każdy harmonogram oddzielnie, aby upewnić się, że można go zremisować w przyszłości dla śmiętków runbook, jeśli to konieczne.

### <a name="import-the-az-modules"></a>Importowanie modułów Az

>[!NOTE]
>Zaimportowanie żyłaczek wymaga tylko wymaganych modułów Az. Nie importuj moduł Az zestawienia, ponieważ zawiera on wszystkie moduły Az. Ta wskazówka jest taka sama dla wszystkich modułów.

Moduł [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) jest zależnością dla innych modułów Az. Z tego powodu elementy runbook należy zaimportować ten moduł do konta automatyzacji przed zaimportowanie innych modułów.

Aby zaimportować moduły w witrynie Azure portal:

1. Na koncie automatyzacji wybierz pozycję **Moduły** w obszarze **Zasoby udostępnione**. 
2. Kliknij **pozycję Przeglądaj galerię,** aby otworzyć stronę Przeglądaj galerię.  
3. Na pasku wyszukiwania wprowadź nazwę modułu, `Az.Accounts`na przykład . 
4. Na stronie Moduł programu PowerShell kliknij przycisk **Importuj,** aby zaimportować moduł do konta automatyzacji.

![Importowanie modułów z konta automatyzacji](media/az-modules/import-module.png)

Ten proces importowania można również wykonać za pośrednictwem [galerii programu PowerShell,](https://www.powershellgallery.com) wyszukując moduł do importu. Po znalezieniu modułu wybierz go, wybierz kartę **Usługi Azure Automation** i kliknij pozycję **Wdrażanie w usłudze Azure Automation**.

![Importowanie modułów bezpośrednio z galerii](media/az-modules/import-gallery.png)

## <a name="testing-your-runbooks"></a>Testowanie eks-ów

Po zaimportowaniu modułów Az do konta Automatyzacja można rozpocząć edycję śmigierów ekwiomowych w celu użycia modułów. Większość poleceń cmdlet ma takie same nazwy jak dla modułu AzureRM, z tą różnicą, że prefiks AzureRM (lub AzureRm) został zmieniony na Az. Aby uzyskać listę modułów, które nie są zgodne z tą konwencją nazewnictwa, zobacz [listę wyjątków](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Jednym ze sposobów przetestowania modyfikacji śmiętu w celu `Enable-AzureRmAlias -Scope Process` użycia nowych poleceń cmdlet jest użycie na początku zestawu runbook. Dodając to polecenie do egobiegu, skrypt może działać bez zmian.

## <a name="after-migration-details"></a>Szczegóły po migracji

Po zakończeniu migracji nie próbuj uruchamiać uruchomieniu wykorzystaniem modułów AzureRM na koncie automatyzacji. Zaleca się również, aby nie importować ani nie aktualizować modułów usługi AzureRM na koncie. Należy wziąć pod uwagę konto zmigrowane do Az i działać tylko z modułami Az. 

Podczas tworzenia nowego konta automatyzacji istniejące moduły AzureRM są nadal instalowane. Nadal można zaktualizować podręczniki runbook za pomocą poleceń cmdlet azurerm. Jednak nie należy uruchamiać tych śmiętków runbook.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o korzystaniu z modułów Az, zobacz [Wprowadzenie do modułu Az](/powershell/azure/get-started-azureps?view=azps-1.1.0).
