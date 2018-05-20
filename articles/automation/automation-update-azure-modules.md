---
title: Zaktualizuj Azure modułów w automatyzacji Azure
description: W tym artykule opisano, jak można teraz zaktualizować typowymi modułami programu Azure PowerShell domyślne automatyzacji Azure.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fe263346d8794b3dc85b6420d8b9b02efa5f9684
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Jak zaktualizować modułów programu Azure PowerShell w usłudze Automatyzacja Azure

Najbardziej typowe moduły programu PowerShell systemu Azure znajdują się domyślnie każdego konta automatyzacji. Zespół Azure aktualizuje Azure modułów regularnie, więc w ramach konta automatyzacji podano sposób aktualizowania modułów w ramach konta, gdy nowe wersje są dostępne w portalu.  

Ponieważ moduły są regularnie aktualizowane przez grupę, zmiany mogą być uwzględnione polecenia cmdlet, które może niekorzystnie wpłynąć na elementach runbook, w zależności od rodzaju zmiany, takie jak zmiana nazwy parametru lub całkowicie wycofano polecenia cmdlet. Aby uniknąć wpływu na elementy runbook i ich automatyzacji procesów, zaleca się testowanie i zweryfikować przed kontynuowaniem. Jeśli nie masz dedykowane konto automatyzacji przeznaczone do tego celu, należy rozważyć utworzenie jednego, dzięki czemu można przetestować wiele różnych scenariuszy i permutacji podczas tworzenia elementów runbook, oprócz do iteracji zmian, takich jak aktualizowanie Moduły programu PowerShell. Po zweryfikowaniu wyniki i wszelkie wymagane zmiany zostały zastosowane, kontynuować koordynowanie migracji wszystkie elementy runbook, który wymagany modyfikacji i wykonać następującą aktualizację, zgodnie z opisem w środowisku produkcyjnym.

## <a name="updating-azure-modules"></a>Aktualizowanie Azure modułów

1. Na stronie moduły konto automatyzacji jest opcję o nazwie **modułów Azure aktualizacji**. Jest zawsze włączone.<br><br> ![Zaktualizuj opcja modułów Azure na stronie moduły](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Kliknij przycisk **modułów Azure aktualizacji**, powiadomienie z potwierdzeniem jest wyświetlany, który zapyta, czy chcesz kontynuować.<br><br> ![Zaktualizuj powiadomień Azure modułów](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Kliknij przycisk **tak** i rozpocznie się proces aktualizacji modułu. Proces aktualizacji trwa około 15-20 minut, można zaktualizować następujących modułów:

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    Jeśli moduły są już aktualne, proces zostanie zakończony w ciągu kilku sekund. Po zakończeniu procesu aktualizacji, użytkownik jest powiadamiany.<br><br> ![Zaktualizuj stan aktualizacji modułów Azure](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

> [!NOTE]
> Po uruchomieniu nowe zadanie zaplanowane usługi Automatyzacja Azure korzysta z modułów najnowszego na Twoim koncie automatyzacji.    

Jeśli używasz poleceń cmdlet z tych modułów programu Azure PowerShell w elementach runbook chcesz uruchomić ten proces aktualizacji, co miesiąc lub tak upewnij się, że masz najnowszą modułów.

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej o moduły integracji oraz sposobu tworzenia niestandardowych modułów do dalszej integracji z innymi systemami, usług lub rozwiązań automatyzacji, zobacz [moduły integracji](automation-integration-modules.md).

* Należy rozważyć użycie integracji kontroli źródła [GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) lub [Visual Studio Team Services](automation-scenario-source-control-integration-with-vsts.md) centralne zarządzanie i sterowanie wersjach portfela automatyzacji elementu runbook i konfiguracji.  