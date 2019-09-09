---
title: Konfigurowanie alertów zabezpieczeń dla ról zasobów platformy Azure w programie PIM-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak skonfigurować alerty zabezpieczeń dla ról zasobów platformy Azure w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 177f61392c3e441c891ba1b531301b3dae8c0db2
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804237"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Konfigurowanie alertów zabezpieczeń dla ról zasobów platformy Azure w usłudze PIM
Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) generuje alerty w przypadku podejrzanych lub niebezpiecznych działań w środowisku. Po wyzwoleniu alertu zostanie on wyświetlony na stronie alerty. 

![Zasoby platformy Azure — alert dotyczący wyświetlania na stronie alertów, poziom ryzyka i liczba](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Przegląd alertów
Wybierz Alert, aby wyświetlić raport zawierający listę użytkowników lub ról, które wyzwoliły alert, oraz porady dotyczące korygowania.

![Raport o alertach przedstawiający czas ostatniego skanowania, opis, kroki zaradcze, typ, ważność, wpływ na zabezpieczenia oraz sposób zapobiegania następnym](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Alerty
| Alerty | severity | Wyzwalacz | Zalecenie |
| --- | --- | --- | --- |
| **Zbyt wielu właścicieli przypisanych do zasobu** |Średni |Zbyt wielu użytkowników ma rolę właściciela. |Zapoznaj się z użytkownikami na liście i przypisz je do mniej uprzywilejowanych ról. |
| **Zbyt wiele stałych właścicieli przypisanych do zasobu** |Średni |Zbyt wielu użytkowników jest trwale przypisanych do roli. |Przejrzyj użytkowników na liście i przypisz go ponownie, aby wymagać aktywacji do użycia przez rolę. |
| **Utworzono zduplikowaną rolę** |Średni |Wiele ról ma takie same kryteria. |Użyj tylko jednej z tych ról. |


### <a name="severity"></a>severity
* **Wysoka**: Wymaga natychmiastowej akcji z powodu naruszenia zasad. 
* **Średni**: Nie wymaga natychmiastowego działania, ale sygnalizuje potencjalne naruszenie zasad.
* **Niska**: Nie wymaga natychmiastowego działania, ale sugeruje preferowaną zmianę zasad.

## <a name="configure-security-alert-settings"></a>Konfigurowanie ustawień alertów zabezpieczeń
Na stronie Alerty przejdź do pozycji **Ustawienia**.

![Strona alerty z wyróżnionymi ustawieniami](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Dostosuj ustawienia różnych alertów, aby współpracowały ze środowiskiem i celami zabezpieczeń.

![Ustawianie strony alertu do włączania i konfigurowania ustawień](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie ustawień roli zasobów platformy Azure w usłudze PIM](pim-resource-roles-configure-role-settings.md)
