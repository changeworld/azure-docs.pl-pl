---
title: Używanie grup dynamicznych w usłudze Azure Update Management
description: W tym artykule opisano, jak grupy dynamiczne współpracują z usługą Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 678b3f361e4456a2c482896f7d7dc20d530b917b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75420394"
---
# <a name="use-dynamic-groups-with-update-management"></a>Używanie grup dynamicznych z rozwiązaniem Update Management

Usługa Zarządzania aktualizacjami umożliwia kierowanie na dynamiczną grupę maszyn wirtualnych platformy Azure lub maszyn wirtualnych innych niż Azure w celu wdrożenia aktualizacji. Te grupy są oceniane w czasie wdrażania, więc nie trzeba edytować wdrożenia, aby dodać maszyny.

## <a name="azure-machines"></a>Maszyny platformy Azure

Te grupy są definiowane przez kwerendę, po rozpoczęciu wdrażania aktualizacji członkowie tej grupy są oceniane. Grupy dynamiczne nie działają z klasycznymi maszynami wirtualnymi. Podczas definiowania kwerendy można użyć następujących elementów razem do wypełniania grupy dynamicznej:

* Subskrypcja
* Grupy zasobów
* Lokalizacje
* Tagi

![Zaznaczanie grup](./media/automation-update-management/select-groups.png)

Aby wyświetlić podgląd wyników grupy dynamicznej, kliknij przycisk **Podgląd.** Ta wersja zapoznawcza pokazuje członkostwo w grupie w tym czasie, w tym przykładzie wyszukujemy maszyny z tagiem **Rola** jest równa **BackendServer**. Jeśli więcej maszyn ma ten tag dodany, zostaną one dodane do przyszłych wdrożeń przeciwko tej grupie.

![grupy w wersji zapoznawczej](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Maszyny nienawiązane z platformą Azure

W przypadku maszyn innych niż azure zapisane wyszukiwania, nazywane również grupami komputerów, są używane do tworzenia grupy dynamicznej. Aby dowiedzieć się, jak utworzyć zapisane wyszukiwanie, zobacz [Tworzenie grupy komputerów](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Po utworzeniu grupy można ją wybrać z listy zapisanych wyszukiwań. Kliknij **pozycję Podgląd,** aby wyświetlić podgląd komputerów w zapisanym wyszukiwaniu w tym czasie.

![Zaznaczanie grup](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Następne kroki

Po utworzeniu grupy dynamicznej można [utworzyć wdrożenie aktualizacji](automation-tutorial-update-management.md)
