---
title: Tworzenie przeglądu dostępu do ról zasobów platformy Azure w usłudze PIM — Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć przegląd dostępu ról zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae70b8386b1dc3ebd570d2651cded3eda75dfc53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847074"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Tworzenie przeglądu dostępu ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi

Dostęp do uprzywilejowanych ról zasobów platformy Azure dla pracowników zmienia się wraz z czasem. Aby zmniejszyć ryzyko związane z przestarzałymi przypisaniami ról, należy regularnie przeglądać dostęp. Za pomocą usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) można utworzyć przeglądy dostępu dla ról zasobów platformy Azure uprzywilejowanych. Można również skonfigurować cykliczne przeglądy dostępu, które występują automatycznie.

W tym artykule opisano sposób tworzenia co najmniej jednej recenzje dostępu dla ról zasobów platformy Azure uprzywilejowanych.

## <a name="prerequisites"></a>Wymagania wstępne

[Administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Przeglądy otwartego dostępu

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) z użytkownikiem, który jest członkiem roli administratora ról uprzywilejowanych.

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. W menu po lewej stronie wybierz pozycję **Zasoby platformy Azure**.

1. Wybierz zasób, który chcesz zarządzać, taki jak subskrypcja lub grupa zarządzania.

1. W obszarze Zarządzanie wybierz pozycję **Przeglądy programu Access**.

    ![Zasoby platformy Azure — lista opinii programu Access przedstawiająca stan wszystkich recenzji](./media/pim-resource-roles-start-access-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>Rozpoczynanie przeglądu dostępu

Po określeniu ustawień przeglądu dostępu kliknij przycisk **Start**. Przegląd dostępu pojawi się na liście ze wskaźnikiem jego stanu.

![Lista opinii programu Access przedstawiająca stan rozpoczętego przeglądu](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Domyślnie usługa Azure AD wysyła wiadomość e-mail do recenzentów wkrótce po rozpoczęciu przeglądu. Jeśli nie chcesz, aby usługa Azure AD wysyłała wiadomość e-mail, należy poinformować recenzentów, że przegląd dostępu czeka na ich ukończenie. Możesz pokazać im instrukcje dotyczące [sposobu przeglądania dostępu do ról zasobów platformy Azure.](pim-resource-roles-perform-access-review.md)

## <a name="manage-the-access-review"></a>Zarządzanie przeglądem dostępu

Postęp można śledzić, gdy recenzenci dokończą swoje opinie na stronie **Przegląd** przeglądu dostępu. Żadne prawa dostępu nie są zmieniane w katalogu, dopóki [przegląd nie zostanie zakończony.](pim-resource-roles-complete-access-review.md)

![Strona przeglądowa programu Access z informacjami o szczegółach recenzji](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Jeśli jest to przegląd jednorazowy, a następnie po zakończeniu okresu przeglądu dostępu lub administrator zatrzymuje przegląd dostępu, wykonaj kroki w [Wykonaj przegląd dostępu ról zasobów platformy Azure,](pim-resource-roles-complete-access-review.md) aby wyświetlić i zastosować wyniki.  

Aby zarządzać serią przeglądów dostępu, przejdź do przeglądu dostępu, a w zaplanowanych recenzjach znajdziesz nadchodzące wystąpienia, a także edytuj datę zakończenia lub odpowiednio dodaj/usuń recenzentów.

Na podstawie wybranych opcji w **sekcji Po zakończeniu ustawień**automatyczne stosowanie zostanie wykonane po dacie zakończenia recenzji lub po ręcznym zatrzymaniu recenzji. Stan przeglądu zmieni się z **Zakończone** przez stany **pośrednie,** takie jak Zastosowanie i na koniec stan **Zastosowane**. Należy się spodziewać, aby zobaczyć odmówiono użytkowników, jeśli w ogóle, są usuwane z ról w ciągu kilku minut.

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie dostępu do ról zasobów platformy Azure](pim-resource-roles-perform-access-review.md)
- [Ukończ przegląd ról zasobów platformy Azure](pim-resource-roles-complete-access-review.md)
- [Tworzenie przeglądu dostępu ról usługi Azure AD](pim-how-to-start-security-review.md)
