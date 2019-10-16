---
title: Korzystanie z grup dynamicznych za pomocą usługi Azure Update Management
description: Opisuje sposób działania grup dynamicznych z Update Management
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: edc4384be0f1dc73f2e7e098114080d304d92ce8
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377748"
---
# <a name="use-dynamic-groups-with-update-management"></a>Używanie grup dynamicznych z Update Management

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

W przypadku maszyn spoza platformy Azure zapisane wyszukiwania są określane również jako grupy komputerów, które są używane do tworzenia grupy dynamicznej. Aby dowiedzieć się, jak utworzyć zapisane wyszukiwanie, zobacz [Tworzenie grupy komputerów](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Po utworzeniu grupy możesz wybrać ją z listy zapisanych wyszukiwań. Kliknij pozycję **Podgląd** , aby wyświetlić w tym momencie komputery z zapisanym wyszukiwaniem.

![Wybieranie grup](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Następne kroki

Po utworzeniu grupy dynamicznej można [utworzyć wdrożenie aktualizacji](automation-tutorial-update-management.md)
