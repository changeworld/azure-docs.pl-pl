---
title: Konfigurowanie alertów zabezpieczeń dla ról zasobów platformy Azure w usłudze PIM | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować alerty zabezpieczeń dla ról zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 2fa63cf2fa05f2cde4558f0bea38bfd7f17df3ae
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43342066"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Konfigurowanie alertów zabezpieczeń dla ról zasobów platformy Azure w usłudze PIM
Privileged Identity Management (PIM) dla zasobów platformy Azure generuje alerty w przypadku działania związane z niebezpieczne lub podejrzane w danym środowisku. Po wyzwoleniu alertu ona wyświetlona na stronie alertów. 

![Strona alertów](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>Przeglądanie alertów
Wybierz alert, aby wyświetlić raport zawierający listę użytkowników lub ról, które wyzwolony alert, wraz z porad korygowania.

![Raport o alertach](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>Alerty
| Alert | Ważność | Wyzwalacz | Zalecenie |
| --- | --- | --- | --- |
| **Zbyt wielu właścicieli przypisanych do zasobu** |Średnie |Zbyt wielu użytkowników korzysta z roli właściciela. |Przejrzyj użytkowników na liście, a następnie ponownie przypisać niektóre mniej uprzywilejowanych ról. |
| **Zbyt wiele trwałych właścicieli przypisanych do zasobu** |Średnie |Zbyt wielu użytkowników są trwale przypisane do roli. |Przejrzyj użytkowników na liście, a następnie ponownie przypisać niektóre do wymagana aktywacja w celu użycia roli. |
| **Utworzono zduplikowaną rolę** |Średnie |Wiele ról ma takich samych kryteriów. |Użyj tylko jednej z tych ról. |


### <a name="severity"></a>Ważność
* **Wysoka**: wymaga natychmiastowego działania z powodu naruszenia zasad. 
* **Średnia**: nie wymagać natychmiastowego działania, ale sygnalizuje potencjalne naruszenie zasad.
* **Niska**: nie wymagać natychmiastowego działania, ale sugeruje zmiana preferowanego zasad.

## <a name="configure-security-alert-settings"></a>Konfigurowanie ustawień alertów zabezpieczeń
Na stronie alertów, przejdź do **ustawienia**.
![Ustawienia](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

Dostosowywanie ustawień na różne alerty chcesz pracować w swoim środowisku i cele zabezpieczeń.
![Dostosowywanie ustawień](media/azure-pim-resource-rbac/rbac-alert-settings.png)

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie alertów zabezpieczeń dla ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-configure-alerts.md)
