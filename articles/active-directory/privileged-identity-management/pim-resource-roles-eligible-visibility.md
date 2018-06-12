---
title: Kwalifikujące się zadania i widoczność zasobów platformy Azure w Privileged Identity Management | Dokumentacja firmy Microsoft
description: Zawiera opis sposobu przypisywania elementy członkowskie jako kwalifikujący się do ról zasobów, korzystając z usługi PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 205b4f3113f369279dbe18e75b5945a0498e7bbd
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260383"
---
# <a name="eligible-assignments-and-resource-visibility-with-privileged-identity-management"></a>Kwalifikujące się zadania i widoczność zasobów z Privileged Identity Management

Uprzywilejowane tożsamości zarządzania (PIM) dla ról zasobów platformy Azure udostępnia zwiększone zabezpieczenia dla organizacji, które są krytyczne zasoby platformy Azure. Zasób Administratorzy mogą używać usługi PIM można przypisać elementy członkowskie jako kwalifikujący się do zasobu ról. Dowiedz się więcej o przypisanie różnych typów i stanów przypisania ról zasobów platformy Azure w następujących sekcjach. 

## <a name="assignment-types"></a>Typy przypisania

PIM zasobów Azure oferuje dwa typy różne przypisania:

- Uprawniona
- Aktywne

Przydziały kwalifikujących się wymagają należącego do roli do wykonania akcji, aby użyć roli. Akcje mogą obejmować pomyślne wyboru uwierzytelnianie wieloskładnikowe, zapewniając biznesowego wyjaśnienia lub żądanie zatwierdzenia od osób zatwierdzających wyznaczonych.

Przydziały Active nie wymagają elementu członkowskiego do wykonywania dowolnych akcji, aby użyć roli. Elementy członkowskie przypisany jako aktywny mają uprawnienia przypisane do roli przez cały czas.

## <a name="assignment-duration"></a>Czas trwania przypisania

Administratorzy zasobów można wybrać z dwóch opcji dla każdego typu przydziału, gdy skonfigurowano ustawienia usługi PIM dla roli. Te opcje stają się domyślny maksymalny czas, kiedy element członkowski jest przypisany do roli w PIM. 

Administrator może wybrać jeden z następujących typów przypisania:

- Zezwalaj na przypisanie trwałego kwalifikowania się
- Zezwalaj na trwałe aktywne przypisanie

Lub administrator może wybrać jeden z następujących typów przypisania:

- Wygaszaj przypisania kwalifikowania się po
- Aktywne przypisania wygasają po

Jeśli administrator zasobów **Zezwalaj stałych przypisań kwalifikujących się** lub **Zezwalaj stałych przypisań active**, w przypadku wszystkich administratorów, które przypisania członków do zasobu można przypisać stałych członkostwa.

Jeśli administrator zasobów **wygaśnie przypisania kwalifikujących się po** lub **wygaśnie active przypisania po**, administrator zasobów kontroluje cykl życia przypisania, gdyż, który wszystkie przydziały mają określonej daty rozpoczęcia i zakończenia.

> [!NOTE] 
> Wszystkie przypisania z datą zakończenia mogą być odnawiane przez administratorów zasobów. Ponadto członkowie mogą inicjować żądań samoobsługi, aby [rozszerzać lub odnowić przypisania](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>Stany przypisania

PIM dla zasobów platformy Azure ma dwa stany przypisania unikatowych, które znajdują się w **aktywnych ról** karcie **Moje ról**, **ról**, i **członków**widoków PIM. Są następujące stany:

- Przypisany
- Aktywowano

Po wyświetleniu członkostwa, który znajduje się w **aktywnych ról**, możesz użyć wartości w **stanu** kolumny w celu rozróżnienia użytkowników, którzy są **przypisane** jako aktywne i Użytkownicy który **Activated** kwalifikujących się przypisanie i są obecnie aktywne.

## <a name="next-steps"></a>Następne kroki

[Przypisz role w Privileged Identity Manager](pim-resource-roles-assign-roles.md)
