---
title: Wyświetlanie i przypisywanie uprawnień roli administratora — usługa Azure AD | Dokumenty firmy Microsoft
description: Teraz możesz wyświetlać i zarządzać członkami roli administratora usługi Azure AD w portalu. Dla tych, którzy często zarządzają przypisaniami ról.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2815b496545ca5f920c00df7b2b5b7efe15c12a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900911"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Wyświetlanie i przypisywanie ról administratora w usłudze Azure Active Directory

Teraz możesz wyświetlać i zarządzać wszystkimi członkami ról administratora w portalu usługi Azure Active Directory. Jeśli często zarządzasz przypisaniami ról, prawdopodobnie wolisz to doświadczenie. A jeśli kiedykolwiek zastanawiałeś się "Co do cholery zrobić te role naprawdę?", można wyświetlić szczegółową listę uprawnień dla każdej z ról administratora usługi Azure AD.

## <a name="view-all-roles"></a>Wyświetlanie wszystkich ról

Wyszukaj i wybierz pozycję **Azure Active Directory**. Wybierz **pozycję Role i administratorzy,** aby wyświetlić listę wszystkich dostępnych ról.

Kliknij wielokropek po prawej stronie każdego wiersza, aby otworzyć szczegółowy opis roli.

![lista ról w portalu usługi Azure AD](./media/directory-manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>Wyświetlanie moich ról

Łatwo jest również wyświetlić własne uprawnienia. Wybierz **pozycję Rola** na stronie Role i **administratorzy,** aby wyświetlić role, które są aktualnie przypisane do Ciebie.

## <a name="view-assignments-for-a-role"></a>Wyświetlanie przypisań dla roli

Kliknij rolę, aby wyświetlić użytkowników przypisanych do roli. Aby uzyskać dodatkowe możliwości zarządzania, można wybrać **opcję Zarządzaj w pliku PIM.** Administratorzy ról uprzywilejowanych mogą zmieniać przypisania "Stałe" (zawsze aktywne w roli) na "Kwalifikujące się" (w roli tylko wtedy, gdy są podwyższone). Jeśli nie masz identyfikatora PIM, nadal możesz wybrać **pozycję Zarządzaj w ułowieniu w ułowieniu,** aby zarejestrować się w wersji próbnej. Zarządzanie tożsamościami uprzywilejowanymi wymaga [planu licencji usługi Azure AD Premium P2.](../privileged-identity-management/subscription-requirements.md)

![lista członków roli administratora](./media/directory-manage-roles-portal/member-list.png)

Jeśli jesteś administratorem globalnym lub administratorem ról uprzywilejowanych, możesz łatwo dodawać lub usuwać członków, filtrować listę lub wybierać członka, aby zobaczyć ich aktywne przypisane role.

## <a name="view-a-users-role-permissions"></a>Wyświetlanie uprawnień roli użytkownika

Podczas wyświetlania członków roli wybierz pozycję **Opis,** aby wyświetlić pełną listę uprawnień przyznanych przez przypisanie roli. Strona zawiera łącza do odpowiedniej dokumentacji, która pomoże Ci w zarządzaniu rolami w katalogu.

![lista uprawnień dla roli administratora](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>Następne kroki

* Zapraszam do dzielenia się z nami na [forum ról administracyjnych usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Aby uzyskać więcej informacji o rolach i przypisywaniu ról administratora, zobacz [Przypisywanie ról administratora](directory-assign-admin-roles.md).
* Aby uzyskać domyślne uprawnienia użytkownika, zobacz [porównanie domyślnych uprawnień gościa i użytkownika.](../fundamentals/users-default-permissions.md)
