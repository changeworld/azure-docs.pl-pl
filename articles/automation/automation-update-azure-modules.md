---
title: Aktualizowanie modułów platformy Azure w usłudze Azure Automation
description: W tym artykule opisano, jak można teraz zaktualizować wspólnego moduły programu Azure PowerShell zapewniany domyślnie w usłudze Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 03174e6336589f8aa49a7fc7197da1301ff54400
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61304304"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation

Aby zaktualizować moduły platformy Azure na koncie usługi Automation zaleca możesz teraz użyj [elementu runbook usługi Azure aktualizowanie modułów](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), czyli "open source" teraz. Ponadto, możesz nadal używać **Aktualizuj moduły platformy Azure** przycisk w portalu, aby zaktualizować moduły platformy Azure. Aby dowiedzieć się więcej o korzystaniu z elementów runbook typu open source, zobacz [aktualizowania modułów platformy Azure przy użyciu elementów runbook typu open source](#open-source).

Najbardziej typowe moduły programu Azure PowerShell znajdują się domyślnie w ramach każdego konta usługi Automation. Zespół platformy Azure, które regularnie aktualizuje moduły platformy Azure. Na koncie usługi Automation otrzymasz sposób aktualizowania modułów w ramach konta, gdy nowe wersje są dostępne z poziomu portalu.

Ponieważ moduły są regularnie aktualizowane przez grupę produktu, zmiany mogą być uwzględniane polecenia cmdlet. Ta akcja może niekorzystnie wpłynąć na Twoje elementy runbook w zależności od typu zmiany, takiej jak zmiana nazwy parametru lub całkowicie wycofano polecenie cmdlet.

Aby uniknąć wpływu na Twoje elementy runbook i procesy, które mogą zautomatyzować, przetestować i sprawdzić przed kontynuowaniem. Jeśli nie masz dedykowane konto usługi Automation, przeznaczone do tego celu, należy rozważyć utworzenie jednego, tak, aby podczas tworzenia elementów runbook można przetestować wielu różnych scenariuszach. Te testy powinny obejmować iteracyjne zmiany, takie jak aktualizowanie modułów programu PowerShell. 

W przypadku tworzenia skryptów lokalnie, zaleca się mieć tej samej wersji modułu lokalnie, do których masz na koncie usługi Automation, podczas testowania, aby zagwarantować, że otrzymasz te same wyniki. Po zweryfikowaniu wyniki i wszelkie wymagane zmiany zostały zastosowane, zmiany można przenieść do środowiska produkcyjnego.

> [!NOTE]
> Nowe konto usługi Automation nie mogą zawierać najnowsze moduły.

## <a name="open-source"></a>Aktualizowanie modułów platformy Azure przy użyciu elementów runbook typu open-source

Aby rozpocząć korzystanie z **AutomationAzureModulesForAccount aktualizacji** elementu runbook, aby zaktualizować moduły platformy Azure, pobierz go z [repozytorium runbook modułów Azure aktualizacji](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) w witrynie GitHub. Można następnie zaimportować go do konta usługi Automation lub uruchomić go jako skrypt. Instrukcje dotyczące sposobu wykonania tego zadania znajdują się w [repozytorium runbook modułów Azure aktualizacji](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update).

### <a name="considerations"></a>Zagadnienia do rozważenia

Poniżej przedstawiono niektóre zagadnienia, weź pod uwagę podczas korzystania z tego procesu można zaktualizować moduły platformy Azure:

* W przypadku zaimportowania tego elementu runbook pod oryginalną nazwą `Update-AutomationAzureModulesForAccount`, zastąpi on wewnętrzny elementu runbook o tej nazwie. W rezultacie zaimportowany element runbook zostanie uruchomiony, jeśli **aktualizowania modułów platformy Azure** wypchnięciach przycisku, lub gdy ten element runbook jest wywoływany bezpośrednio za pośrednictwem interfejsu API usługi Azure Resource Manager dla tego konta usługi Automation.

* Ten element runbook obsługuje aktualizowanie tylko **Azure** i **AzureRm** obecnie modułów. [Moduły platformy Azure PowerShell Az](/powershell/azure/new-azureps-module-az) są obsługiwane na kontach usługi Automation, ale nie można zaktualizować tego elementu runbook.

* Należy unikać, uruchomienie tego elementu runbook konta usługi Automation, które zawierają moduły Az.

* Przed rozpoczęciem tego elementu runbook, upewnij się, Twoje konto usługi Automation ma [Uruchom jako platformy Azure poświadczeń konta](manage-runas-account.md) utworzone.

* Ten kod można użyć jako regularne skrypt programu PowerShell zamiast elementu runbook: po prostu Zaloguj się do platformy Azure za pomocą [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) polecenia najpierw, a następnie przekazać `-Login $false` do skryptu.

* Aby użyć tego elementu runbook w chmurach suwerennych, użyj `AzureRmEnvironment` parametr do przekazania odpowiednie środowisko do elementu runbook.  Dopuszczalne wartości to **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**, i **AzureUSGovernment**. Te wartości mogą być pobierane z przy użyciu `Get-AzureRmEnvironment | select Name`. Jeśli nie możesz przekazać wartość tego parametru, elementu runbook będą domyślnie chmury publicznej platformy Azure **AzureCloud**

* Jeśli chcesz użyć określonej wersji modułu Azure PowerShell zamiast najnowszy dostępny w galerii programu PowerShell, należy przekazać te wersje, opcjonalny `ModuleVersionOverrides` parametru **Update-AutomationAzureModulesForAccount**elementu runbook. Aby uzyskać przykłady, zobacz [AutomationAzureModulesForAccount.ps1 aktualizacji](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) elementu runbook. Moduły programu Azure PowerShell, które nie są wymienione w `ModuleVersionOverrides` parametru są aktualizowane przy użyciu najnowszej wersji modułu w galerii programu PowerShell. Jeśli nie ma niczego do przekazania `ModuleVersionOverrides` parametru, wszystkie moduły są aktualizowane przy użyciu najnowszej wersji modułu w galerii programu PowerShell. To zachowanie jest taka sama jak **aktualizowania modułów platformy Azure** przycisku.

## <a name="update-azure-modules-in-the-azure-portal"></a>Aktualizowanie modułów platformy Azure w witrynie Azure portal

1. Na stronie moduły konta usługi Automation jest dostępna opcja o nazwie **aktualizowania modułów platformy Azure**. Jest zawsze włączona.<br><br> ![Aktualizuj moduły platformy Azure opcja na stronie moduły](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

   > [!NOTE]
   > Przed zaktualizowaniem usługi moduły platformy Azure, zalecane jest, zaktualizuj je w teście upewnij się, że konto usługi Automation istniejące skrypty działają zgodnie z oczekiwaniami przed zaktualizowaniem moduły platformy Azure.
   >
   > **Aktualizowania modułów platformy Azure** przycisk jest dostępny tylko w chmurze publicznej. nie jest dostępna w [niezależne regiony](https://azure.microsoft.com/global-infrastructure/). Użyj **AutomationAzureModulesForAccount aktualizacji** elementu runbook, aby zaktualizować moduły platformy Azure. Możesz ją pobrać z [repozytorium runbook modułów Azure aktualizacji](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update). Aby dowiedzieć się więcej o korzystaniu z elementu runbook "open source", zobacz [aktualizowania modułów platformy Azure przy użyciu elementów runbook typu open source](#open-source).

2. Kliknij przycisk **aktualizowania modułów platformy Azure**, powiadomienie z potwierdzeniem jest wyświetlany, pytaniem, jeśli chcesz kontynuować.<br><br> ![Aktualizuj moduły platformy Azure powiadomień](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Kliknij przycisk **tak** i rozpocznie się proces aktualizacji modułu. Proces aktualizacji trwa około 15 – 20 minut, można zaktualizować następujących modułów:

   * Azure
   * Azure.Storage
   * AzureRm.Automation
   * AzureRm.Compute
   * AzureRm.Profile
   * AzureRm.Resources
   * AzureRm.Sql
   * AzureRm.Storage

     Jeśli moduły są już aktualne, proces zostanie zakończony w ciągu kilku sekund. Po ukończeniu procesu aktualizacji otrzymasz powiadomienie.<br><br> ![Aktualizuje stan aktualizacji modułów platformy Azure](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

     .NET core AzureRm modułów (AzureRm.*. -Rdzeniowe) nie są obsługiwane w usłudze Azure Automation i nie można go zaimportować.

> [!NOTE]
> Usługa Azure Automation używa najnowszych modułów na koncie usługi Automation, po uruchomieniu nowego zaplanowanego zadania.  

Jeśli używasz polecenia cmdlet z tych modułów programu Azure PowerShell w elementach runbook, chcesz, aby do uruchamiania tego procesu aktualizacji co miesiąc lub więc upewnij się, że najnowsze moduły. Usługa Azure Automation używa `AzureRunAsConnection` połączenia do uwierzytelniania podczas aktualizowania modułów. Nazwa główna usługi wygasł lub nie istnieje już na poziomie subskrypcji, aktualizacja modułu zakończy się niepowodzeniem.

## <a name="known-issues"></a>Znane problemy

Istnieje znany problem z aktualizowaniem moduł AzureRM na koncie usługi Automation, która znajduje się w grupie zasobów o nazwie numeryczne, która rozpoczyna się od 0. Aby zaktualizować moduły platformy Azure na koncie usługi Automation, musi być w grupie zasobów o nazwie alfanumeryczne. Nie można zaktualizować usługi AzureRM moduły w tym momencie są grupy zasobów o nazwach liczbowe od 0.

## <a name="next-steps"></a>Kolejne kroki

Odwiedź stronę "open source" [elementu runbook usługi Azure aktualizacji modułów](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) Aby dowiedzieć się więcej na ten temat.
