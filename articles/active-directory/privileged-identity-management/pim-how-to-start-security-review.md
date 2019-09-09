---
title: Tworzenie przeglądu dostępu ról usługi Azure AD w programie PIM-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak utworzyć przegląd dostępu ról usługi Azure AD w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/27/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2069f0161ea2600a62ab123206d0ebe3c00642c3
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804367"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-pim"></a>Tworzenie przeglądu dostępu ról usługi Azure AD w usłudze PIM

Dostęp do ról uprzywilejowanych usługi Azure AD dla pracowników zmienia się w czasie. Aby zmniejszyć ryzyko związane ze starymi przypisaniami ról, należy regularnie przeglądać dostęp. Za pomocą usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) można tworzyć przeglądy dostępu dla ról uprzywilejowanych usługi Azure AD. Istnieje również możliwość skonfigurowania cyklicznych przeglądów dostępu, które są wykonywane automatycznie.

W tym artykule opisano sposób tworzenia co najmniej jednej oceny dostępu dla uprzywilejowanych ról usługi Azure AD.

## <a name="prerequisites"></a>Wymagania wstępne

- [Administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Otwarte przeglądy dostępu

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu użytkownika, który jest członkiem roli administrator ról uprzywilejowanych.

1. Otwórz **Azure AD Privileged Identity Management**.

1. W menu po lewej stronie kliknij pozycję **role usługi Azure AD** , a następnie kliknij pozycję **przeglądy dostępu**.

1. W obszarze Zarządzanie kliknij pozycję **przeglądy dostępu**.

    ![Role usługi Azure AD — lista przeglądów dostępu przedstawiających stan wszystkich przeglądów](./media/pim-how-to-start-security-review/access-reviews.png)


[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]


## <a name="start-the-access-review"></a>Rozpocznij przegląd dostępu

Po określeniu ustawień przeglądu dostępu kliknij przycisk **Uruchom**. Przegląd dostępu zostanie wyświetlony na liście ze wskaźnikiem jego stanu.

![Lista przeglądów dostępu pokazująca stan rozpoczęte przeglądy](./media/pim-how-to-start-security-review/access-reviews-list.png)

Domyślnie usługa Azure AD wysyła wiadomość e-mail do recenzentów wkrótce po rozpoczęciu przeglądu. Jeśli nie chcesz, aby usługa Azure AD wysłała wiadomość e-mail, pamiętaj, aby poinformować recenzentów, że przegląd dostępu oczekuje na jego zakończenie. Można wyświetlić instrukcje dotyczące sposobu [przeglądania dostępu do ról usługi Azure AD](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Zarządzanie przeglądem dostępu

Postęp można śledzić, ponieważ recenzenci ukończyją przeglądy na stronie **Przegląd** przeglądu dostępu. Żadne prawa dostępu nie są zmieniane w katalogu, dopóki nie [zostanie ukończona Recenzja](pim-how-to-complete-review.md).

![Strona przeglądu dostępu przegląd przedstawia szczegóły przeglądu](./media/pim-how-to-start-security-review/access-review-overview.png)

Jeśli jest to jednorazowe przegląd, po przekroczeniu okresu kontroli dostępu lub przejściu przez administratora do przeglądu dostępu wykonaj kroki opisane w temacie [Kończenie przeglądu dostępu ról usługi Azure AD](pim-how-to-complete-review.md) , aby zobaczyć i zastosować wyniki.  

Aby zarządzać serią przeglądów dostępu, przejdź do przeglądu dostępu i znajdziesz nadchodzące wystąpienia w zaplanowanych recenzjach, a następnie edytuj datę końcową lub Dodaj/Usuń recenzentów.

Po dokonaniu wyboru w **ustawieniach ukończenia**funkcja automatycznego zastosowania zostanie wykonana po zakończeniu przeglądu lub po ręcznym zatrzymaniu przeglądu. Stan przeglądu zmieni się z **zakończone** przez Stany pośrednie, takie jak **stosowanie** i ostatecznie do stanu **zastosowania**. Należy się spodziewać, że w ciągu kilku minut będzie widocznych odrzuconych użytkowników (jeśli istnieją).

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie dostępu do ról usługi Azure AD](pim-how-to-perform-security-review.md)
- [Kończenie przeglądu dostępu do ról usługi Azure AD](pim-how-to-complete-review.md)
- [Tworzenie przeglądu dostępu do ról zasobów platformy Azure](pim-resource-roles-start-access-review.md)
