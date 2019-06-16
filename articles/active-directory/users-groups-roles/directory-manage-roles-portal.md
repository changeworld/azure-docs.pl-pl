---
title: Wyświetlić i przypisać administratora z uprawnienia roli — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Można teraz wyświetlić i członkowie roli administratora usługi Azure AD w portalu zarządzania. Dla tych, którzy często zarządzać przypisaniami ról.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20842c8c644a351b59322569661abf82bb524449
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60472591"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Wyświetlanie i przypisywania ról administratora w usłudze Azure Active Directory

Można teraz wyświetlić i Zarządzaj wszystkimi członkami ról administratora w portalu Azure Active Directory. Jeśli często zarządzać przypisaniami ról, będzie prawdopodobnie wolisz tego środowiska. A jeśli użytkownik nigdy nie "Co ewidencjonuj te role naprawdę robią?", można wyświetlić szczegółową listę uprawnień dla każdej z ról administratora usługi Azure AD.

## <a name="view-all-roles"></a>Wyświetl wszystkie role

W usłudze Azure Active Directory, zaznacz **role i Administratorzy** Aby wyświetlić listę wszystkich dostępnych ról. 

Kliknij przycisk wielokropka po prawej stronie każdego wiersza, aby otworzyć szczegółowy opis roli.

![Lista ról w portalu usługi Azure AD](./media/directory-manage-roles-portal/role-list.png)

## <a name="view-my-roles"></a>Wyświetl moje role

Jest łatwe wyświetlić także własnych uprawnień. Wybierz **Twoja rola** na **role i Administratorzy** strony, aby zobaczyć ról, które są aktualnie przypisane do użytkownika.

## <a name="view-assignments-for-a-role"></a>Wyświetl przypisania roli

Kliknij rolę, aby wyświetlić użytkowników przypisanych do roli. Możesz wybrać **zarządzania w usłudze PIM** dla dodatkowe funkcje zarządzania. Administratorzy ról uprzywilejowanych mogą zmienić "Stałe" (zawsze aktywny w roli) przypisania na "Kwalifikujących" (w roli, tylko wtedy, gdy z podwyższonym poziomem uprawnień). Jeśli nie masz usługi PIM, możesz również zaznaczyć **zarządzania w usłudze PIM** się skorzystać z wersji próbnej. Privileged Identity Management wymaga [planu licencjonowania usługi Azure AD Premium P2](../privileged-identity-management/subscription-requirements.md).

![Lista członków roli administratora](./media/directory-manage-roles-portal/member-list.png)

Jeśli jesteś administratorem globalnym lub administratorem ról uprzywilejowanych, możesz łatwo dodać lub usunąć członków, filtrowanie listy lub zaznaczeniu elementu członkowskiego, aby zobaczyć ich aktywnym przypisane role.

## <a name="view-a-users-role-permissions"></a>Wyświetlanie uprawnień roli użytkownika

Podczas wyświetlania członków roli, wybierz **opis** aby zobaczyć pełną listę uprawnień udzielonych przez przypisanie roli. Strona zawiera linki do dodatkowej dokumentacji w celu ułatwienia zarządzania ról w katalogu.

![Lista uprawnień roli administratora](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>Kolejne kroki

* Możesz udostępnić w [forum ról administracyjnych usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Więcej informacji na temat ról i przypisywania ról administratora, zobacz [przypisywania ról administratora](directory-assign-admin-roles.md).
* Aby uzyskać domyślne uprawnienia użytkowników, zobacz [porównanie domyślne uprawnienia użytkownika gościa i element członkowski](../fundamentals/users-default-permissions.md).
