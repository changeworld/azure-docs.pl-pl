---
title: Aktualizowanie modułów platformy Azure w usłudze Azure Automation
description: W tym artykule opisano, jak można teraz zaktualizować wspólnego moduły programu Azure PowerShell zapewniany domyślnie w usłudze Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fbb57753117f3c60010fe910616b8d0af5178360
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434827"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation

Najbardziej typowe moduły programu Azure PowerShell znajdują się domyślnie w ramach każdego konta usługi Automation. Zespół platformy Azure regularnie, aktualizuje moduły platformy Azure, więc na koncie usługi Automation są dostarczane sposób aktualizowania modułów w ramach konta, gdy nowe wersje są dostępne z poziomu portalu.

Ponieważ moduły są regularnie aktualizowane przez grupę produktu, zmiany mogą być uwzględnione polecenia cmdlet, które może niekorzystnie wpłynąć na Twoje elementy runbook w zależności od typu zmiany, takiej jak zmiana nazwy parametru lub całkowicie wycofano polecenie cmdlet. Aby uniknąć wpływu na Twoje elementy runbook i procesy, które mogą zautomatyzować, zalecane jest, testowanie i sprawdzić przed kontynuowaniem. Jeśli nie masz dedykowane konto usługi Automation, przeznaczone do tego celu, należy rozważyć utworzenie jednego, więc, że możesz sprawdzić wielu różnych scenariuszy i permutacji podczas tworzenia elementów runbook, oprócz iteracyjne zmiany, takie jak aktualizowanie Moduły programu PowerShell. Po zweryfikowaniu wyniki i zastosowano wszelkie wymagane zmiany, kontynuuj koordynowanie migracji wszelkie elementy runbook wymagające modyfikacji i wykonaj następującą aktualizację, zgodnie z opisem w środowisku produkcyjnym.

> [!NOTE]
> Nowe konto usługi Automation nie mogą zawierać najnowsze moduły.

## <a name="updating-azure-modules"></a>Aktualizowanie modułów platformy Azure

1. Na stronie moduły konta usługi Automation jest dostępna opcja o nazwie **aktualizowania modułów platformy Azure**. Jest zawsze włączona.<br><br> ![Aktualizuj moduły platformy Azure opcja na stronie moduły](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

  > [!NOTE]
  > Przed zaktualizowaniem usługi moduły platformy Azure, zalecane jest, zaktualizuj je w teście upewnij się, że konto usługi Automation istniejące skrypty działają zgodnie z oczekiwaniami przed zaktualizowaniem moduły platformy Azure.
  >
  > **Aktualizowania modułów platformy Azure** przycisk jest dostępny tylko w chmurze publicznej. Nie jest dostępna w [niezależne regiony](https://azure.microsoft.com/global-infrastructure/). Zobacz [alternatywnych sposobów, aby zaktualizować moduły](#alternative-ways-to-update-your-modules) sekcji, aby dowiedzieć się więcej.


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

Jeśli używasz polecenia cmdlet z tych modułów programu Azure PowerShell w elementach runbook, chcesz, aby do uruchamiania tego procesu aktualizacji co miesiąc lub więc upewnij się, że najnowsze moduły. Usługa Azure Automation używa połączenia AzureRunAsConnection do uwierzytelniania podczas aktualizowania modułów, jeśli nazwy głównej usługi wygasł lub nie istnieje już na poziomie subskrypcji, aktualizacja modułu zakończy się niepowodzeniem.

## <a name="alternative-ways-to-update-your-modules"></a>Alternatywne sposoby, aby zaktualizować moduły

Jak wspomniano wcześniej, **aktualizowania modułów platformy Azure** przycisk nie jest dostępna w chmurach suwerennych, jest on dostępny tylko w chmurze globalnej platformy Azure. Jest to z faktu, że najnowsza wersja modułów programu Azure PowerShell z galerii programu PowerShell może nie działać z usługi Resource Manager aktualnie zaimplementowane w tych chmurach.

Aktualizowanie modułów, nadal możesz zrobić, importując [AzureModule.ps1 aktualizacji](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) elementu runbook do konta usługi Automation i uruchomiania go.

Użyj `AzureRmEnvironment` parametr do przekazania odpowiednie środowisko do elementu runbook.  Dopuszczalne wartości to **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**, i **AzureUSGovernmentCloud**. Jeśli wartość nie jest przekazywany do tego parametru, elementu runbook będą domyślnie chmury publicznej platformy Azure **AzureCloud**.

Jeśli chcesz użyć określonej wersji modułu Azure PowerShell zamiast najnowszy dostępny w galerii programu PowerShell, należy przekazać te wersje, opcjonalny `ModuleVersionOverrides` parametru **AzureModule aktualizacji** elementu runbook. Aby uzyskać przykłady, zobacz [AzureModule.ps1 aktualizacji](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) elementu runbook. Moduły programu Azure PowerShell, które nie są wymienione w `ModuleVersionOverrides` parametru są aktualizowane przy użyciu najnowszej wersji modułu w galerii programu PowerShell. Jeśli nic nie zostanie przekazana do `ModuleVersionOverrides` parametru, wszystkie moduły są aktualizowane przy użyciu najnowszej wersji modułu w galerii programu PowerShell, co jest zachowaniem **aktualizowania modułów platformy Azure** przycisku.

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat moduły integracji oraz sposób tworzenia niestandardowych modułów do dalszej integracji usługi Automation z innych systemów, usług lub rozwiązania, zobacz [moduły integracji](automation-integration-modules.md).

* Integracja kontroli źródła przy użyciu polecenia [GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) lub [DevOps platformy Azure](automation-scenario-source-control-integration-with-vsts.md) centralne zarządzanie i kontrolowanie wydań portfolio automatyzacji elementu runbook i konfiguracji.  
