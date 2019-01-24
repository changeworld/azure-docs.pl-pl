---
title: Testowanie elementu runbook w usłudze Azure Automation
description: Przed opublikowaniem elementu runbook w usłudze Azure Automation, możesz przetestować, aby upewnić się, że to działa zgodnie z oczekiwaniami.  W tym artykule opisano, jak przetestować element runbook i wyświetlić dane wyjściowe.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 95e3f4426fab8ed3ff28877607dee8694962e79f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54422477"
---
# <a name="testing-a-runbook-in-azure-automation"></a>Testowanie elementu runbook w usłudze Azure Automation
Podczas testowania elementu runbook [wersję roboczą](automation-creating-importing-runbook.md#publishing-a-runbook) jest wykonywane i wykonywane są wszystkie akcje, które wykonuje. Historia zadań nie zostanie utworzony, ale [dane wyjściowe](automation-runbook-output-and-messages.md#output-stream) i [ostrzeżeń i błędów](automation-runbook-output-and-messages.md#message-streams) strumienie są wyświetlane w teście dane wyjściowe w okienku. Komunikaty dotyczące [pełne Stream](automation-runbook-output-and-messages.md#message-streams) są wyświetlane w okienku danych wyjściowych tylko wtedy, gdy [zmiennej $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) jest ustawiona, aby kontynuować.

Mimo że jest uruchamiana wersja robocza elementu runbook nadal normalnie wykonywania przepływu pracy i wykonuje wszystkie akcje dotyczące zasobów w środowisku. Z tego powodu należy przetestować tylko elementów runbook zasobów nieprodukcyjnych.

Procedury do przetestowania każdego [typ elementu runbook](automation-runbook-types.md) są takie same i występują nie różni się w zakresie testowania między Edytor tekstów, a edytor graficzny, w witrynie Azure portal.  

## <a name="to-test-a-runbook-in-the-azure-portal"></a>Aby przetestować element runbook w witrynie Azure portal
Można pracować z dowolnymi [typ elementu runbook](automation-runbook-types.md) w witrynie Azure portal.

1. Otwórz wersję roboczą elementu runbook albo [Edytor tekstów](automation-edit-textual-runbook.md) lub [edytor graficzny](automation-graphical-authoring-intro.md).
2. Kliknij przycisk **testu** przycisk, aby otworzyć blok testowania.
3. Jeśli element runbook ma parametry, będą one wyświetlane w okienku po lewej stronie, gdzie możesz podać wartości, które ma być używany dla testu.
4. Jeśli chcesz uruchomić test [hybrydowego procesu roboczego Runbook](automation-hybrid-runbook-worker.md), następnie zmienić **parametrów uruchomieniowych** do **hybrydowy proces roboczy** i wybierz nazwę grupy docelowej.  W przeciwnym razie Zachowaj domyślną **Azure** do uruchomienia testu w chmurze.
5. Kliknij przycisk **Start** przycisk, aby uruchomić test.
6. Jeśli element runbook ma [przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) lub [graficzny](automation-runbook-types.md#graphical-runbooks), a następnie można zatrzymać lub je zawiesić, gdy jest testowana za pomocą przycisków pod okienkiem danych wyjściowych. Po wstrzymaniu elementu runbook, kończy działanie bieżącego przed jego wstrzymaniem. Po wstrzymaniu elementu runbook można go zatrzymać lub uruchomić ją ponownie.
7. Sprawdź dane wyjściowe z elementu runbook w okienku danych wyjściowych.

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się, jak tworzenie lub importowanie elementu runbook, zobacz [Tworzenie lub importowanie elementu runbook w usłudze Azure Automation](automation-creating-importing-runbook.md)
* Aby dowiedzieć się więcej na temat tworzenia elementów graficznych, zobacz [Graphical authoring in Azure Automation](automation-graphical-authoring-intro.md) (Tworzenie elementów graficznych w usłudze Azure Automation).
* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz artykuł [My first PowerShell workflow runbook](automation-first-runbook-textual.md) (Mój pierwszy element Runbook przepływu pracy programu PowerShell).
* Aby dowiedzieć się więcej na temat konfigurowania elementów runbook w celu zwrócenia komunikaty o stanie i błędy, zobacz zalecane rozwiązania, w tym [elementu Runbook dane wyjściowe i komunikaty w usłudze Azure Automation](automation-runbook-output-and-messages.md)


