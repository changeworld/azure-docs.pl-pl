---
title: Aktualizowanie modułów platformy Azure w usłudze Azure Automation
description: W tym artykule opisano, jak można teraz aktualizować typowe moduły programu Azure PowerShell dostarczane domyślnie w usłudze Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 721bb3579fec10df88be471d67f68c0846aa9432
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632115"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation

Aby zaktualizować moduły platformy Azure na koncie automatyzacji, należy użyć [identyfikatora runbooka update azure modules](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), który jest dostępny jako open source. Aby rozpocząć korzystanie z **update-AutomationAzureModulesForAccount** runbook, aby zaktualizować moduły platformy Azure, pobierz go z [repozytorium umulatek umownych modułów platformy Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) w usłudze GitHub. Następnie można zaimportować go do konta automatyzacji lub uruchomić go jako skrypt. Aby dowiedzieć się, jak zaimportować system runbook na konto automatyzacji, zobacz [Importowanie ekwidujnika](manage-runbooks.md#importing-a-runbook).

Najczęściej moduły programu AzureRM Programu PowerShell są dostarczane domyślnie na każdym koncie automatyzacji. Zespół platformy Azure aktualizuje moduły platformy Azure regularnie, w związku z tym, aby być na bieżąco, należy użyć [update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbook, aby zaktualizować moduły na kontach automatyzacji.

Ponieważ moduły są regularnie aktualizowane przez grupę produktów, mogą wystąpić zmiany w dołączonych poleceniach cmdlet. Ta akcja może negatywnie wpłynąć na śmigieł w zależności od typu zmiany, takich jak zmiana nazwy parametru lub przestarzałe polecenie cmdlet całkowicie.

Aby uniknąć wpływu na swoje programy runbook i procesy, które automatyzują, testują i weryfikują przed kontynuowaniem. Jeśli nie masz dedykowanego konta automatyzacji przeznaczonego do tego celu, należy rozważyć utworzenie jednego, aby można było przetestować wiele różnych scenariuszy podczas tworzenia śmiób ekustrych. Ta testowanie powinna zawierać zmiany iteracyjne, takie jak aktualizowanie modułów programu PowerShell.

Jeśli skrypty są rozwijane lokalnie, zaleca się, aby mieć te same wersje modułów lokalnie, które masz na koncie automatyzacji podczas testowania, aby upewnić się, że otrzymasz te same wyniki. Po sprawdzeniu poprawności wyników i zastosowaniu wszelkich wymaganych zmian można przenieść zmiany do produkcji.

> [!NOTE]
> Nowe konto automatyzacji może nie zawierać najnowszych modułów.

> [!NOTE]
> Nie będzie można usunąć modułów globalnych - modułów, które automatyzacja udostępnia po wyjęciu z pudełka.

## <a name="considerations"></a>Zagadnienia do rozważenia

Poniżej przedstawiono kilka kwestii, które należy wziąć pod uwagę podczas korzystania z tego procesu w celu zaktualizowania modułów platformy Azure:

* Ten element runbook obsługuje domyślnie aktualizowanie modułów **platformy Azure** i **AzureRm.** Ten projekt runbook obsługuje również aktualizowanie modułów **Az.** Zapoznaj się z [aktualizacjami modułów platformy Azure README, aby](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) uzyskać więcej informacji na temat aktualizowania `Az` modułów za pomocą tego systemu runbook. Istnieją dodatkowe ważne czynniki, które należy wziąć pod `Az` uwagę podczas korzystania z modułów na koncie automatyzacji, aby dowiedzieć się więcej, zobacz [Korzystanie z modułów Az na koncie automatyzacji](az-modules.md).

* Przed uruchomieniem tego systemu runbook upewnij się, że konto automatyzacji ma utworzone [poświadczenia konta usługi Azure Uruchom jako.](manage-runas-account.md)

* Ten kod można użyć jako zwykłego skryptu programu PowerShell zamiast przewodniczącego: wystarczy zalogować się na platformę `-Login $false` Azure przy użyciu polecenia [Connect-AzureRmAccount,](/powershell/module/azurerm.profile/connect-azurermaccount) a następnie przekazać do skryptu.

* Aby użyć tego śmigieł `AzureRmEnvironment` w chmurach suwerennych, należy użyć parametru, aby przekazać poprawne środowisko do egonatu.  Dopuszczalne wartości to **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**i **AzureUSGovernment**. Te wartości można pobrać `Get-AzureRmEnvironment | select Name`z za pomocą . Jeśli wartość nie zostanie przekażena do tego parametru, element runbook domyślnie przejdzie do chmury publicznej azure **azurecloud** azure

* Jeśli chcesz użyć określonej wersji modułu programu Azure PowerShell zamiast najnowszych dostępnych w Galerii programu PowerShell, przekaż te wersje do opcjonalnego `ModuleVersionOverrides` **parametru systemu Update-AutomationAzureModulesForAccount.** Na przykład zobacz [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) runbook. Moduły programu Azure PowerShell, które `ModuleVersionOverrides` nie są wymienione w parametrze są aktualizowane o najnowsze wersje modułów w galerii programu PowerShell. Jeśli nic nie `ModuleVersionOverrides` przekażesz do parametru, wszystkie moduły są aktualizowane o najnowsze wersje modułów w Galerii programu PowerShell. To zachowanie jest takie samo jak przycisk **Aktualizuj moduły platformy Azure.**

## <a name="next-steps"></a>Następne kroki

Odwiedź system [uruchamiania modułów aktualizacji platformy Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) typu open source, aby dowiedzieć się więcej na ten temat.
