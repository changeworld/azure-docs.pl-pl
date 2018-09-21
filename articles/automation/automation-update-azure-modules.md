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
ms.openlocfilehash: bee1c5c48242b69ee33fedd358a83e0580d19942
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2018
ms.locfileid: "46498055"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation

Najbardziej typowe moduły programu Azure PowerShell znajdują się domyślnie w ramach każdego konta usługi Automation. Zespół platformy Azure regularnie, aktualizuje moduły platformy Azure, więc na koncie usługi Automation są dostarczane sposób aktualizowania modułów w ramach konta, gdy nowe wersje są dostępne z poziomu portalu.

Ponieważ moduły są regularnie aktualizowane przez grupę produktu, zmiany mogą być uwzględnione polecenia cmdlet, które może niekorzystnie wpłynąć na Twoje elementy runbook w zależności od typu zmiany, takiej jak zmiana nazwy parametru lub całkowicie wycofano polecenie cmdlet. Aby uniknąć wpływu na Twoje elementy runbook i procesy, które mogą zautomatyzować, zalecane jest, testowanie i sprawdzić przed kontynuowaniem. Jeśli nie masz dedykowane konto usługi Automation, przeznaczone do tego celu, należy rozważyć utworzenie jednego, więc, że możesz sprawdzić wielu różnych scenariuszy i permutacji podczas tworzenia elementów runbook, oprócz iteracyjne zmiany, takie jak aktualizowanie Moduły programu PowerShell. Po zweryfikowaniu wyniki i zastosowano wszelkie wymagane zmiany, kontynuuj koordynowanie migracji wszelkie elementy runbook wymagające modyfikacji i wykonaj następującą aktualizację, zgodnie z opisem w środowisku produkcyjnym.

> [!NOTE]
> Nowe konto usługi Automation nie mogą zawierać najnowsze moduły.

## <a name="updating-azure-modules"></a>Aktualizowanie modułów platformy Azure

1. Na stronie moduły konta usługi Automation jest dostępna opcja o nazwie **aktualizowania modułów platformy Azure**. Jest zawsze włączona.<br><br> ![Aktualizuj moduły platformy Azure opcja na stronie moduły](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

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

    .NET core AzureRm modułów (AzureRm.*. -Rdzeniowe) nie są obsługiwane w usłudze Azure Automation i nie można zaimportować.

> [!NOTE]
> Usługa Azure Automation używa najnowszych modułów na koncie usługi Automation, po uruchomieniu nowego zaplanowanego zadania.  

Jeśli używasz polecenia cmdlet z tych modułów programu Azure PowerShell w elementach runbook, chcesz, aby do uruchamiania tego procesu aktualizacji co miesiąc lub więc upewnij się, że najnowsze moduły. Usługa Azure Automation używa połączenia AzureRunAsConnection do uwierzytelniania podczas aktualizowania modułów, jeśli nazwy głównej usługi wygasł lub nie istnieje już na poziomie subskrypcji, aktualizacja modułu zakończy się niepowodzeniem.

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat moduły integracji oraz sposób tworzenia niestandardowych modułów do dalszej integracji usługi Automation z innych systemów, usług lub rozwiązania, zobacz [moduły integracji](automation-integration-modules.md).

* Integracja kontroli źródła przy użyciu polecenia [GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) lub [DevOps platformy Azure](automation-scenario-source-control-integration-with-vsts.md) centralne zarządzanie i kontrolowanie wydań portfolio automatyzacji elementu runbook i konfiguracji.  
