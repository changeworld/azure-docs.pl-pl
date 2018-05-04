---
title: Kwalifikujące się zadania i widoczność zasobów platformy Azure w Privileged Identity Management | Dokumentacja firmy Microsoft
description: Zawiera opis sposobu przypisywania elementy członkowskie jako kwalifikujący się do ról zasobów, korzystając z usługi PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 4804d930a98192d64245784058920eeba7d30212
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
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

- Zezwalaj na stałych przypisań kwalifikujących się
- Zezwalaj na stałych przypisań active

Lub administrator może wybrać jeden z następujących typów przypisania:

- Przydziały kwalifikujących się po wygaśnie
- Ważność active przypisania po

Jeśli administrator zasobów **Zezwalaj stałych przypisań kwalifikujących się** lub **Zezwalaj stałych przypisań active**, w przypadku wszystkich administratorów, które przypisania członków do zasobu można przypisać stałych członkostwa.

Jeśli administrator zasobów **wygaśnie przypisania kwalifikujących się po** lub **wygaśnie active przypisania po**, administrator zasobów kontroluje cykl życia przypisania, gdyż, który wszystkie przydziały mają określonej daty rozpoczęcia i zakończenia.

> [!NOTE] 
> Wszystkie przypisania z datą zakończenia mogą być odnawiane przez administratorów zasobów. Ponadto członkowie mogą inicjować żądań samoobsługi, aby [rozszerzać lub odnowić przypisania](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>Stany przypisania

PIM dla zasobów platformy Azure ma dwa stany przypisania unikatowych, które znajdują się w **aktywnych ról** karcie **Moje ról**, **ról**, i **członków**widoków PIM. Są następujące stany:

- Przypisany
- Aktywowane

Po wyświetleniu członkostwa, który znajduje się w **aktywnych ról**, możesz użyć wartości w **stanu** kolumny w celu rozróżnienia użytkowników, którzy są **przypisane** jako aktywne i Użytkownicy który **Activated** kwalifikujących się przypisanie i są obecnie aktywne.

## <a name="next-steps"></a>Kolejne kroki

[Przypisz role w Privileged Identity Manager](pim-resource-roles-assign-roles.md)
