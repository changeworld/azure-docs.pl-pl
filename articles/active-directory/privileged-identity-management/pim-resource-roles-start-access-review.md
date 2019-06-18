---
title: Tworzenie przeglądu dostępu dla ról zasobów platformy Azure w usłudze PIM — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć Przegląd dostępu dla ról zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/29/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78e4de5bbc56f95c0e903b1dac4e8481373716f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65143497"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-pim"></a>Tworzenie przeglądu dostępu dla ról zasobów platformy Azure w usłudze PIM

Dostęp do ról uprzywilejowanych zasób platformy Azure dla pracowników zmienia się wraz z upływem czasu. Zmniejszenie ryzyka związanego z przypisań ról starych, należy regularnie przejrzeć dostępu. Azure Active Directory (Azure AD) Privileged Identity Management (PIM) służy do tworzenia przeglądów dostępu na potrzeby zasobów platformy Azure ról uprzywilejowanych. Można również skonfigurować cykliczny przeglądów dostępu, które są wykonywane automatycznie.

W tym artykule opisano sposób tworzenia co najmniej jeden przeglądów dostępu dla ról uprzywilejowanych zasób platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- [Administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Przeglądy dostępu w otwartych

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) z użytkownikiem, który jest członkiem roli Administrator ról uprzywilejowanych.

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. W menu po lewej stronie kliknij **zasobów platformy Azure**.

1. Kliknij zasób, który chcesz zarządzać, takich jak subskrypcję lub grupę zarządzania.

1. W obszarze Zarządzanie, kliknij przycisk **przeglądów dostępu**.

    ![Przeglądy dostępu do zasobów platformy Azure —](./media/pim-resource-roles-start-access-review/access-reviews.png)


[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]


## <a name="start-the-access-review"></a>Rozpocznij Przegląd dostępu

Po określeniu ustawienia przeglądu dostępu, kliknij przycisk **Start**. Przegląd dostępu będą wyświetlane na liście ze wskaźnikiem jego stan.

![Lista przeglądów dostępu](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Domyślnie usługa Azure AD wysyła wiadomość e-mail do recenzentów, wkrótce, po uruchomieniu przeglądu. Jeśli użytkownik chce nie ma wysyłać wiadomości e-mail z usługi Azure AD, pamiętaj poinformować osób dokonujących przeglądu, które przeglądu dostępu oczekuje na ich zakończenie. Można pokazać im instrukcje dotyczące sposobu [Przegląd dostępu wszystkich użytkowników do ról zasobów platformy Azure](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Zarządzanie przeglądu dostępu

Recenzenci Wypełnij przeglądów ich na postęp można śledzić **Przegląd** strony Przegląd dostępu. Nie prawa dostępu zostaną zmienione w katalogu, dopóki [zakończeniu przeglądu](pim-resource-roles-complete-access-review.md).

![Przeglądy dostępu w toku](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Jeśli jest to jednorazowa przeglądu, następnie po umieszczeniu okres przeglądu dostępu lub administrator zatrzymania przeglądu dostępu postępuj zgodnie z instrukcjami w [Kończenie przeglądu dostępu dla ról zasobów platformy Azure](pim-resource-roles-complete-access-review.md) aby zobaczyć i zastosować wyniki.  

Aby zarządzać szereg dostępu recenzje, przejdź do przeglądu dostępu i będą Znajdź nadchodzące zdarzenia w przeglądach zaplanowane i Edytuj datę zakończenia lub Dodawanie/usuwanie recenzentów w związku z tym.

Na podstawie dokonanego wyboru w **ustawienia działań po zakończeniu**, automatycznie Zastosuj będzie wykonywany po Data zakończenia przeglądu lub jeśli ręcznie zatrzymaj przeglądu. Stan przeglądu ulegnie zmianie z **Ukończono** za pośrednictwem pośrednich stanów, takie jak **stosowanie** a na koniec do stanu **zastosowano**. Należy się spodziewać się zablokowani użytkownicy ewentualnej usuwana z ról w ciągu kilku minut.

## <a name="next-steps"></a>Kolejne kroki

- [Przegląd dostępu wszystkich użytkowników do ról zasobów platformy Azure](pim-resource-roles-perform-access-review.md)
- [Kończenie przeglądu dostępu dla ról zasobów platformy Azure](pim-resource-roles-complete-access-review.md)
- [Tworzenie przeglądu dostępu ról usługi Azure AD](pim-how-to-start-security-review.md)
