---
title: Korzystanie z grup dynamicznych za pomocą usługi Azure Update Management
description: W tym artykule opisano sposób działania grup dynamicznych z Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 678b3f361e4456a2c482896f7d7dc20d530b917b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75420394"
---
# <a name="use-dynamic-groups-with-update-management"></a>Używanie grup dynamicznych z rozwiązaniem Update Management

Update Management umożliwia kierowanie do dynamicznych grup platformy Azure lub maszyn wirtualnych innych niż platformy Azure na potrzeby wdrożeń aktualizacji. Te grupy są oceniane w czasie wdrażania, aby nie trzeba było edytować wdrożenia w celu dodania maszyn.

## <a name="azure-machines"></a>Maszyny platformy Azure

Te grupy są definiowane przez zapytanie, po rozpoczęciu wdrożenia aktualizacji są oceniane elementy członkowskie tej grupy. Grupy dynamiczne nie działają z klasycznymi maszynami wirtualnymi. Podczas definiowania zapytania następujące elementy mogą być używane razem do wypełniania grupy dynamicznej:

* Subskrypcja
* Grupy zasobów
* Lokalizacje
* Tagi

![Wybieranie grup](./media/automation-update-management/select-groups.png)

Aby wyświetlić podgląd wyników grupy dynamicznej, kliknij przycisk **Podgląd** . Ta wersja zapoznawcza pokazuje członkostwo w grupach, w tym przykładzie wyszukiwanie maszyn z **rolą** znacznika jest równe **BackendServer**. Jeśli więcej maszyn ma ten tag, zostanie dodany do wszystkich przyszłych wdrożeń względem tej grupy.

![Podgląd grup](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Maszyny spoza platformy Azure

W przypadku maszyn spoza platformy Azure zapisane wyszukiwania są również określane jako grupy komputerów, które są używane do tworzenia grupy dynamicznej. Aby dowiedzieć się, jak utworzyć zapisane wyszukiwanie, zobacz [Tworzenie grupy komputerów](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Po utworzeniu grupy możesz wybrać ją z listy zapisanych wyszukiwań. Kliknij pozycję **Podgląd** , aby wyświetlić w tym momencie komputery z zapisanym wyszukiwaniem.

![Wybieranie grup](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Następne kroki

Po utworzeniu grupy dynamicznej można [utworzyć wdrożenie aktualizacji](automation-tutorial-update-management.md)
