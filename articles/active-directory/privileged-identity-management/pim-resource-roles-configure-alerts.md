---
title: Zarządzanie alertami zabezpieczeń dla zasobów platformy Azure przy użyciu Privileged Identity Management | Dokumentacja firmy Microsoft
description: W tym artykule opisano alerty zabezpieczeń w usłudze PIM.
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
ms.openlocfilehash: 46b19866ec3f276c8337cf9501b779701377d0a5
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39620420"
---
# <a name="manage-security-alerts-for-azure-resources-by-using-privileged-identity-management"></a>Zarządzanie alertami zabezpieczeń dla zasobów platformy Azure przy użyciu Privileged Identity Management
Privileged Identity Management (PIM) dla zasobów platformy Azure generuje alerty w przypadku działania związane z niebezpieczne lub podejrzane w danym środowisku. Po wyzwoleniu alertu ona wyświetlona na stronie alertów. 

![Strona alertów](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>Przeglądanie alertów
Wybierz alert, aby wyświetlić raport zawierający listę użytkowników lub ról, które wyzwolony alert, wraz z porad korygowania.

![Raport o alertach](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>Alerty
| Alerty | Ważność | Wyzwalacz | Zalecenie |
| --- | --- | --- | --- |
| **Zbyt wielu właścicieli przypisanych do zasobu** |Medium |Zbyt wielu użytkowników korzysta z roli właściciela. |Przejrzyj użytkowników na liście, a następnie ponownie przypisać niektóre mniej uprzywilejowanych ról. |
| **Zbyt wiele trwałych właścicieli przypisanych do zasobu** |Medium |Zbyt wielu użytkowników są trwale przypisane do roli. |Przejrzyj użytkowników na liście, a następnie ponownie przypisać niektóre do wymagana aktywacja w celu użycia roli. |
| **Utworzono zduplikowaną rolę** |Medium |Wiele ról ma takich samych kryteriów. |Użyj tylko jednej z tych ról. |


### <a name="severity"></a>Ważność
* **Wysoka**: wymaga natychmiastowego działania z powodu naruszenia zasad. 
* **Średnia**: nie wymagać natychmiastowego działania, ale sygnalizuje potencjalne naruszenie zasad.
* **Niska**: nie wymagać natychmiastowego działania, ale sugeruje zmiana preferowanego zasad.

## <a name="configure-security-alert-settings"></a>Konfigurowanie ustawień alertów zabezpieczeń
Na stronie alertów, przejdź do **ustawienia**.
![Ustawienia](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

Dostosowywanie ustawień na różne alerty chcesz pracować w swoim środowisku i cele zabezpieczeń.
![Dostosowywanie ustawień](media/azure-pim-resource-rbac/rbac-alert-settings.png)
