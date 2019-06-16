---
title: Aktualizowanie modułów platformy Azure w usłudze Azure Automation
description: W tym artykule opisano, jak można teraz zaktualizować wspólnego moduły programu Azure PowerShell zapewniany domyślnie w usłudze Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/30/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c09c9df1b8af7adca5c0169cf31881121ba6cc99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427489"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation

Aby zaktualizować moduły platformy Azure na koncie usługi Automation, musisz użyć [elementu runbook usługi Azure aktualizowanie modułów](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), która jest dostępna jako oprogramowanie open source. Aby rozpocząć korzystanie z **AutomationAzureModulesForAccount aktualizacji** elementu runbook, aby zaktualizować moduły platformy Azure, pobierz go z [repozytorium runbook modułów Azure aktualizacji](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) w witrynie GitHub. Można następnie zaimportować go do konta usługi Automation lub uruchomić go jako skrypt. Aby dowiedzieć się zaimportować element runbook na Twoim koncie usługi Automation, zobacz [importowanie elementu runbook](manage-runbooks.md#import-a-runbook).

Najbardziej typowe moduły AzureRM PowerShell znajdują się domyślnie w ramach każdego konta usługi Automation. Zespół platformy Azure regularnie aktualizuje moduły platformy Azure, w związku z tym aby zapewnić aktualność można użyć [AutomationAzureModulesForAccount aktualizacji](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) elementu runbook, aby zaktualizować moduły na kontach usługi Automation.

Ponieważ moduły są regularnie aktualizowane przez grupę produktu, zmiany mogą być uwzględniane polecenia cmdlet. Ta akcja może niekorzystnie wpłynąć na Twoje elementy runbook w zależności od typu zmiany, takiej jak zmiana nazwy parametru lub całkowicie wycofano polecenie cmdlet.

Aby uniknąć wpływu na Twoje elementy runbook i procesy, które mogą zautomatyzować, przetestować i sprawdzić przed kontynuowaniem. Jeśli nie masz dedykowane konto usługi Automation, przeznaczone do tego celu, należy rozważyć utworzenie jednego, tak, aby podczas tworzenia elementów runbook można przetestować wielu różnych scenariuszach. Te testy powinny obejmować iteracyjne zmiany, takie jak aktualizowanie modułów programu PowerShell.

W przypadku tworzenia skryptów lokalnie, zaleca się mieć tej samej wersji modułu lokalnie, do których masz na koncie usługi Automation, podczas testowania, aby zagwarantować, że otrzymasz te same wyniki. Po zweryfikowaniu wyniki i wszelkie wymagane zmiany zostały zastosowane, zmiany można przenieść do środowiska produkcyjnego.

> [!NOTE]
> Nowe konto usługi Automation nie mogą zawierać najnowsze moduły.

## <a name="considerations"></a>Zagadnienia do rozważenia

Poniżej przedstawiono niektóre zagadnienia, weź pod uwagę podczas korzystania z tego procesu można zaktualizować moduły platformy Azure:

* Ten element runbook obsługuje aktualizowanie tylko **Azure** i **AzureRm** obecnie modułów. [Moduły platformy Azure PowerShell Az](/powershell/azure/new-azureps-module-az) są obsługiwane na kontach usługi Automation, ale nie można zaktualizować tego elementu runbook. Istnieją ważne czynniki, które należy wziąć pod uwagę w przypadku korzystania z `Az` modułów na koncie usługi Automation, aby dowiedzieć się więcej, zobacz [Az używanie modułów na koncie usługi Automation](az-modules.md).

* Należy unikać, uruchomienie tego elementu runbook konta usługi Automation, które zawierają moduły Az.

* Przed rozpoczęciem tego elementu runbook, upewnij się, Twoje konto usługi Automation ma [Uruchom jako platformy Azure poświadczeń konta](manage-runas-account.md) utworzone.

* Ten kod można użyć jako regularne skrypt programu PowerShell zamiast elementu runbook: tak. wystarczy zalogować się do platformy Azure za pomocą [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) polecenia najpierw, a następnie przekazać `-Login $false` do skryptu.

* Aby użyć tego elementu runbook w chmurach suwerennych, użyj `AzureRmEnvironment` parametr do przekazania odpowiednie środowisko do elementu runbook.  Dopuszczalne wartości to **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**, i **AzureUSGovernment**. Te wartości mogą być pobierane z przy użyciu `Get-AzureRmEnvironment | select Name`. Jeśli nie możesz przekazać wartość tego parametru, elementu runbook będą domyślnie chmury publicznej platformy Azure **AzureCloud**

* Jeśli chcesz użyć określonej wersji modułu Azure PowerShell zamiast najnowszy dostępny w galerii programu PowerShell, należy przekazać te wersje, opcjonalny `ModuleVersionOverrides` parametru **Update-AutomationAzureModulesForAccount**elementu runbook. Aby uzyskać przykłady, zobacz [AutomationAzureModulesForAccount.ps1 aktualizacji](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) elementu runbook. Moduły programu Azure PowerShell, które nie są wymienione w `ModuleVersionOverrides` parametru są aktualizowane przy użyciu najnowszej wersji modułu w galerii programu PowerShell. Jeśli nie ma niczego do przekazania `ModuleVersionOverrides` parametru, wszystkie moduły są aktualizowane przy użyciu najnowszej wersji modułu w galerii programu PowerShell. To zachowanie jest taka sama jak **aktualizowania modułów platformy Azure** przycisku.

## <a name="known-issues"></a>Znane problemy

Istnieje znany problem z aktualizowaniem moduł AzureRM na koncie usługi Automation, która znajduje się w grupie zasobów o nazwie numeryczne, która rozpoczyna się od 0. Aby zaktualizować moduły platformy Azure na koncie usługi Automation, musi być w grupie zasobów o nazwie alfanumeryczne. Nie można zaktualizować usługi AzureRM moduły w tym momencie są grupy zasobów o nazwach liczbowe od 0.

## <a name="next-steps"></a>Kolejne kroki

Odwiedź stronę "open source" [elementu runbook usługi Azure aktualizacji modułów](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) Aby dowiedzieć się więcej na ten temat.
