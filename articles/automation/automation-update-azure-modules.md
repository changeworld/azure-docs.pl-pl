---
title: Aktualizowanie modułów platformy Azure w Azure Automation
description: W tym artykule opisano, jak można teraz aktualizować typowe moduły Azure PowerShell udostępniane domyślnie w programie Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 06/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 76514e620f044b78b992db2b88733e69dbabf135
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850639"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Jak zaktualizować moduły Azure PowerShell w programie Azure Automation

Aby zaktualizować moduły platformy Azure na koncie usługi Automation, musisz użyć [elementu Runbook aktualizacji modułów platformy Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), który jest dostępny jako Open Source. Aby rozpocząć korzystanie z elementu Runbook **Update-AutomationAzureModulesForAccount** do aktualizowania modułów platformy Azure, Pobierz go z [repozytorium elementów Runbook aktualizacji modułów platformy Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) w witrynie GitHub. Następnie można zaimportować ją do konta usługi Automation lub uruchomić jako skrypt. Aby dowiedzieć się, jak zaimportować element Runbook na koncie usługi Automation, zobacz [Importowanie elementu Runbook](manage-runbooks.md#import-a-runbook).

Najpopularniejsze moduły AzureRM PowerShell są udostępniane domyślnie na poszczególnych kontach usługi Automation. Zespół platformy Azure regularnie aktualizuje moduły platformy Azure, dlatego w celu uzyskania Aktualności należy użyć elementu Runbook [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) w celu zaktualizowania modułów na kontach usługi Automation.

Ponieważ moduły są regularnie aktualizowane przez grupę produktów, zmiany mogą wystąpić z dołączonymi poleceniami cmdlet. Ta akcja może mieć negatywny wpływ na elementy Runbook, w zależności od typu zmiany, na przykład zmiana nazwy parametru lub całkowite wycofanie polecenia cmdlet.

Aby uniknąć wpływu elementów Runbook i procesów, które automatyzują, Testuj i Weryfikuj przed kontynuowaniem. Jeśli nie masz dedykowanego konta usługi Automation przeznaczonego do tego celu, rozważ utworzenie go, aby umożliwić testowanie wielu różnych scenariuszy podczas opracowywania elementów Runbook. Testowanie powinno obejmować iteracyjne zmiany, takie jak aktualizowanie modułów programu PowerShell.

W przypadku lokalnego tworzenia skryptów zaleca się, aby te same wersje modułów były lokalnie dostępne w ramach konta usługi Automation podczas testowania, aby upewnić się, że otrzymasz te same wyniki. Po sprawdzeniu poprawności wyników i zastosowaniu wymaganych zmian można przenieść zmiany do środowiska produkcyjnego.

> [!NOTE]
> Nowe konto usługi Automation może nie zawierać najnowszych modułów.

> [!NOTE]
> Nie będzie można usunąć modułów globalnych — moduły, które nie są dostępne dla usługi Automation.

## <a name="considerations"></a>Zagadnienia do rozważenia

Poniżej przedstawiono kilka kwestii, które należy wziąć pod uwagę w przypadku aktualizowania modułów platformy Azure przy użyciu tego procesu:

* Ten element Runbook domyślnie obsługuje aktualizowanie modułów **platformy Azure** i **AzureRm** . Ten element Runbook obsługuje również funkcję **AZ** modules. Więcej informacji na temat aktualizowania modułów `Az` za pomocą tego elementu Runbook można znaleźć w [pliku Readme aktualizacji elementu Runbook modułów platformy Azure](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) . Istnieją dodatkowe ważne czynniki, które należy wziąć pod uwagę podczas korzystania z modułów `Az` na koncie usługi Automation, aby dowiedzieć się więcej, zobacz [Używanie polecenia AZ modules na koncie usługi Automation](az-modules.md).

* Przed uruchomieniem tego elementu Runbook upewnij się, że konto usługi Automation ma utworzone [poświadczenie konta Uruchom jako platformy Azure](manage-runas-account.md) .

* Możesz użyć tego kodu jako zwykłego skryptu programu PowerShell zamiast elementu Runbook: wystarczy najpierw zalogować się do platformy Azure przy użyciu polecenia [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) , a następnie przekazać `-Login $false` do skryptu.

* Aby użyć tego elementu Runbook w przypadku suwerennych chmur, użyj parametru `AzureRmEnvironment`, aby przekazać poprawne środowisko do elementu Runbook.  Dopuszczalne wartości to **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**i **AzureUSGovernment**. Te wartości można pobrać z używania `Get-AzureRmEnvironment | select Name`. Jeśli wartość tego parametru nie zostanie przekazana, element Runbook będzie domyślnie **AzureCloud** chmurę publiczną platformy Azure.

* Jeśli chcesz użyć określonej Azure PowerShell wersji modułu zamiast najnowszej dostępnej w Galeria programu PowerShell, Przekaż te wersje do opcjonalnego parametru `ModuleVersionOverrides` elementu Runbook **Update-AutomationAzureModulesForAccount** . Aby zapoznać się z przykładami, zobacz element Runbook [Update-AutomationAzureModulesForAccount. ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) . Moduły Azure PowerShell, które nie są wymienione w parametrze `ModuleVersionOverrides`, są aktualizowane przy użyciu najnowszych wersji modułu w Galeria programu PowerShell. Jeśli wartość Nothing nie zostanie przekazana do parametru `ModuleVersionOverrides`, wszystkie moduły są aktualizowane przy użyciu najnowszych wersji modułu na Galeria programu PowerShell. Takie zachowanie jest takie samo, jak przycisk **Aktualizuj moduły platformy Azure** .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na ten temat, odwiedź [element Runbook modułu Update modules dla usługi Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) sources.
