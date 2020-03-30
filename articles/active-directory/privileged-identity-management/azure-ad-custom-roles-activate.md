---
title: Aktywowanie roli niestandardowej usługi Azure AD — zarządzanie tożsamościami uprzywilejowanymi (PIM)
description: Jak aktywować rolę niestandardową usługi Azure AD dla zarządzania tożsamościami uprzywilejowanymi (PRZYDZIAŁ)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbd60d1311bd84adb303a0d329ab4e42f4d61525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77498735"
---
# <a name="activate-an-azure-ad-custom-role-in-privileged-identity-management"></a>Aktywowanie roli niestandardowej usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi

Zarządzanie tożsamościami uprzywilejowanymi w usłudze Azure Active Directory (Azure AD) obsługuje teraz przypisanie tylko w czasie i czasie do ról niestandardowych utworzonych dla zarządzania aplikacjami w środowiskach administracyjnych usługi Identity and Access Management. Aby uzyskać więcej informacji na temat tworzenia ról niestandardowych do delegowania zarządzania aplikacjami w usłudze Azure AD, zobacz [Role administratora niestandardowego w usłudze Azure Active Directory (wersja zapoznawcza)](../users-groups-roles/roles-custom-overview.md).

> [!NOTE]
> Role niestandardowe usługi Azure AD nie są zintegrowane z wbudowanymi rolami katalogów podczas podglądu. Gdy możliwości są ogólnie dostępne, zarządzanie rolami odbędzie się w doświadczeniu wbudowanych ról. Jeśli widzisz następujący baner, te role powinny być zarządzane [w doświadczeniu wbudowanych ról](pim-how-to-activate-role.md) i ten artykuł nie ma zastosowania:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="activate-a-role"></a>Aktywowanie roli

Jeśli musisz aktywować rolę niestandardową usługi Azure AD, zażądaj aktywacji, wybierając opcję Nawigacji moje role w zarządzania tożsamościami uprzywilejowanymi.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com).
1. Otwórz [zarządzanie tożsamościami uprzywilejowanymi usługą](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart)Azure AD .

1. Wybierz **role niestandardowe usługi Azure AD,** aby wyświetlić listę kwalifikujących się przypisań ról niestandardowych usługi Azure AD.

   ![Zobacz listę kwalifikujących się przypisań ról niestandardowych usługi Azure AD](./media/azure-ad-custom-roles-activate/view-preview-roles.png)

> [!Note] 
>  Przed przypisaniem roli należy utworzyć/skonfigurować rolę. Aby uzyskać więcej informacji dotyczących konfigurowania ról niestandardowych usługi AAD, zobacz [tutaj] (https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-ad-custom-roles-configure)

1. Na stronie **Role niestandardowe usługi Azure AD (Wersja zapoznawcza)** znajdź potrzebne przypisanie.
1. Wybierz **pozycję Aktywuj swoją rolę,** aby otworzyć stronę **Aktywuj.**
1. Jeśli twoja rola wymaga uwierzytelniania wieloskładnikowego, wybierz pozycję **Zweryfikuj swoją tożsamość przed kontynuowaniem**. Musisz uwierzytelnić tylko raz na sesję.
1. Wybierz **pozycję Zweryfikuj moją tożsamość** i postępuj zgodnie z instrukcjami, aby zapewnić dodatkową weryfikację zabezpieczeń.
1. Aby określić niestandardowy zakres aplikacji, wybierz **opcję Zakres,** aby otworzyć okienko filtru. Należy zażądać dostępu do roli w minimalnym zakresie wymagane. Jeśli przypisanie znajduje się w zakresie aplikacji, można aktywować tylko w tym zakresie.

   ![Przypisywanie zakresu zasobów usługi Azure AD do przypisania roli](./media/azure-ad-custom-roles-activate/assign-scope.png)

1. W razie potrzeby określ niestandardowy czas rozpoczęcia aktywacji. Gdy jest używany, element członkowski roli jest aktywowany w określonym czasie.
1. W polu **Przyczyna** wprowadź przyczynę żądania aktywacji. Mogą one być wymagane lub nie w ustawieniu roli.
1. Wybierz **pozycję Aktywuj**.

Jeśli rola nie wymaga zatwierdzenia, jest aktywowana zgodnie z ustawieniami i jest dodawana do listy aktywnych ról. Jeśli chcesz użyć aktywowanej roli, zacznij od kroków w [obszarze Przypisywanie roli niestandardowej usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi](azure-ad-custom-roles-assign.md).

Jeśli rola wymaga zatwierdzenia do aktywacji, otrzymasz powiadomienie platformy Azure z informacją, że żądanie oczekuje na zatwierdzenie.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie roli niestandardowej usługi Azure AD](azure-ad-custom-roles-assign.md)
- [Usuwanie lub aktualizowanie przypisania niestandardowego zadania usługi Azure AD](azure-ad-custom-roles-update-remove.md)
- [Konfigurowanie przypisania niestandardowego zadania usługi Azure AD](azure-ad-custom-roles-configure.md)
- [Definicje ról w usłudze Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
