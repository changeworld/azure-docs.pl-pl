---
title: Wyświetlanie członków roli administratora w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Można teraz wyświetlić i członkowie roli administratora usługi Azure AD w portalu zarządzania. Dla tych, którzy często zarządzać przypisaniami ról.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 07/02/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 588e475a451ea00e52dd995edc3f1bf0e284044f
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37872330"
---
# <a name="view-all-members-of-an-administrator-role-in-azure-active-directory"></a>Wyświetlanie wszystkich elementów członkowskich w roli administratora w usłudze Azure Active Directory

Można teraz wyświetlić i Zarządzaj wszystkimi członkami ról administratora w portalu Azure Active Directory. Jeśli często zarządzać przypisaniami ról, będzie prawdopodobnie wolisz tego środowiska. A jeśli użytkownik nigdy nie "Co ewidencjonuj te role naprawdę robią?", można wyświetlić szczegółową listę uprawnień dla każdej z ról administratora usługi Azure AD.

To proste zobaczyć, jak również własnych uprawnień. Kliknij przycisk **Twojej roli** Uzyskaj szybki dostęp do strony użytkownika, aby uzyskać listę wszystkich aktywnych ról przypisanych. Kliknij przycisk wielokropka po prawej stronie każdego wiersza, aby otworzyć szczegółowy opis roli.

![Lista ról w portalu usługi Azure AD](./media/directory-manage-roles-portal/role-list.png)

Wybierz cały wiersz, aby wyświetlić listę przypisanych elementów członkowskich. Możesz wybrać **zarządzania w usłudze PIM** dla dodatkowe funkcje zarządzania. Administratorzy ról uprzywilejowanych mogą zmienić "Stałe" (zawsze aktywny w roli) przypisania na "Kwalifikujących" (w roli, tylko wtedy, gdy z podwyższonym poziomem uprawnień). Jeśli nie masz usługi PIM, możesz również zaznaczyć **zarządzania w usłudze PIM** się skorzystać z wersji próbnej. Privileged Identity Management wymaga [planu licencjonowania usługi Azure AD Premium P2](../privileged-identity-management/subscription-requirements.md).

![Lista członków roli administratora](./media/directory-manage-roles-portal/member-list.png)

Jeśli jesteś administratorem globalnym lub administratorem ról uprzywilejowanych, możesz łatwo dodać lub usunąć członków, filtrowanie listy lub zaznaczeniu elementu członkowskiego, aby przejść do strony użytkownika, aby zobaczyć ich aktywnym przypisane role. 

## <a name="role-details-in-the-portal"></a>Szczegóły roli w portalu

Podczas wyświetlania członków roli, wybierz **opis** aby zobaczyć pełną listę uprawnień udzielonych przez przypisanie roli. Strona zawiera linki do dodatkowej dokumentacji w celu ułatwienia zarządzania ról w katalogu.

![Lista uprawnień roli administratora](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>Kolejne kroki

* Możesz udostępnić w [forum ról administracyjnych usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Więcej informacji na temat ról i przypisywania ról administratora, zobacz [przypisywania ról administratora](directory-assign-admin-roles.md).
* Aby uzyskać domyślne uprawnienia użytkowników, zobacz [porównanie domyślne uprawnienia użytkownika gościa i element członkowski](../fundamentals/users-default-permissions.md).
