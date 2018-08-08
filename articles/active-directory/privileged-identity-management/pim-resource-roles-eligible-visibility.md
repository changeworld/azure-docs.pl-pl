---
title: Kwalifikujące się przypisania i widoczność zasobów platformy Azure w Privileged Identity Management | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak przypisać członków jako kwalifikuje się role zasobów, korzystając z usługi PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 336453c1ef6ef8d0295d00f31afc6a5e7e42e8b6
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621552"
---
# <a name="eligible-assignments-and-resource-visibility-with-privileged-identity-management"></a>Kwalifikujące się przypisania i widoczność zasobów Privileged Identity Management

Privileged Identity Management (PIM) dla ról zasobów platformy Azure zapewnia zwiększone zabezpieczenia dla organizacji, które mają zasoby platformy Azure o znaczeniu krytycznym. Zasób Administratorzy mogą używać usługi PIM, aby przypisać członków jako kwalifikuje się role zasobów. Dowiedz się więcej na temat przypisania różnych typów i Stany przypisania dla ról zasobów platformy Azure w poniższych sekcjach. 

## <a name="assignment-types"></a>Typy przypisania

Usługa PIM dla zasobów platformy Azure udostępnia dwa typy distinct przypisania:

- Uprawniona
- Aktywne

Kwalifikującymi się przypisaniami wymagają członek roli do wykonania działania, aby korzystać z roli. Akcje mogą obejmować sukcesy wyboru usługi Multi-Factor authentication, zapewniając uzasadnienie biznesowe lub żądanie zatwierdzenia od wyznaczone osoby zatwierdzające.

Aktywne przypisania nie wymagają elementu członkowskiego do wykonywania dowolnych akcji, aby użyć roli. Członkowie przypisani jako aktywny mają uprawnienia przypisane do roli przez cały czas.

## <a name="assignment-duration"></a>Czas trwania przypisania

Zasób Administratorzy mogą wybrać spośród dwóch opcji dla każdego typu przypisania, podczas konfigurowania ustawień usługi PIM dla roli. Te opcje stają się domyślny maksymalny czas, gdy członek jest przypisany do roli w usłudze PIM. 

Administrator może wybrać jeden z tych typów przypisania:

- Zezwalaj na przypisanie trwałego kwalifikowania się
- Zezwalaj na trwałe aktywne przypisanie

Lub administrator może wybrać jeden z następujących typów przypisania:

- Wygaszaj przypisania kwalifikowania się po
- Aktywne przypisania wygasają po

Jeśli administrator zasobów wybierze **Zezwalaj na trwałe kwalifikujące się przypisanie** lub **Zezwalaj na trwałe aktywne przypisanie**, wszystkich administratorów, Przypisz elementy członkowskie do zasobu, które można przypisać stałe w grupach.

Jeśli administrator zasobów wybierze **kwalifikujące się przypisania wygasają po** lub **aktywne przypisania wygasają po**, administrator zasobów kontroluje cykl życia przypisania poprzez wymaganie, wszystkie przydziały mają określoną datę początkową i końcową.

> [!NOTE] 
> Wszystkie przydziały, które mają określonej daty zakończenia, można ją odnawiać każdorazowo administratorom zasobów. Ponadto członkowie mogą inicjować żądań samoobsługi, aby [rozszerzanie lub odnawianie przypisania](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>Stany przypisania

Usługa PIM dla zasobów platformy Azure ma dwa stany distinct przypisania, które pojawiają się na **aktywnych ról** karcie **Moje role**, **role**, i **członków**widoków PIM. Te stany są następujące:

- Przypisany
- Aktywowano

Po wyświetleniu członkostwa, który znajduje się w **aktywnych ról**, możesz użyć wartości w **stanu** kolumny do rozróżniania użytkowników, którzy są **przypisane** jako aktywny i Użytkownicy, **aktywowano** kwalifikującego się przypisania i są obecnie aktywne.

## <a name="next-steps"></a>Kolejne kroki

[Przypisywanie ról w Privileged Identity Manager](pim-resource-roles-assign-roles.md)
