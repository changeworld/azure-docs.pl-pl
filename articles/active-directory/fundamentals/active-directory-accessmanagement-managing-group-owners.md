---
title: Dodawanie lub usuwanie właścicieli grup — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Instrukcje dotyczące dodawania lub usuwania właścicieli grup przy użyciu usługi Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f83a48ad13fe4ae217319b2aa85adf976aa6a7ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77149851"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Dodawanie lub usuwanie właścicieli grup w usłudze Azure Active Directory
Grupy usługi Azure Active Directory (Azure AD) są własnością właścicieli grup i są zarządzane przez właścicieli. Właściciele grup mogą być użytkownikami lub jednostkami usług i mogą zarządzać grupą, w tym członkostwem. Tylko obecni właściciele grup lub administratorzy zarządzający grupami mogą przypisywać właścicieli grup. Właściciele grup nie muszą być członkami grupy.

Gdy grupa nie ma właściciela, administratorzy zarządzający grupami nadal mogą zarządzać grupą. Zaleca się, aby każda grupa miała co najmniej jednego właściciela. Gdy właściciele zostaną przystyli do grupy, ostatniego właściciela grupy nie można usunąć. Przed usunięciem ostatniego właściciela z grupy należy wybrać innego właściciela.

## <a name="add-an-owner-to-a-group"></a>Dodawanie właściciela do grupy
Poniżej znajdują się instrukcje dotyczące dodawania użytkownika jako właściciela do grupy przy użyciu portalu usługi Azure AD. Aby dodać jednostkę usługi jako właściciela grupy, postępuj zgodnie z instrukcjami, aby to zrobić za pomocą [programu PowerShell](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0).

### <a name="to-add-a-group-owner"></a>Aby dodać właściciela grupy
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **pozycję Azure Active Directory**, wybierz **grupki**, a następnie wybierz grupę, dla której chcesz dodać właściciela (w tym przykładzie *zasady MDM — Zachód).*

3. Na stronie **Zasady MDM — Przegląd zachodni** wybierz pozycję **Właściciele**.

    ![Zasady MDM — strona Przegląd zachodu z wyróżnioną opcją Właściciele](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. Na stronie **Zasady MDM — Zachód - Właściciele** wybierz pozycję Dodaj **właścicieli**, a następnie wyszukaj i wybierz użytkownika, który będzie nowym właścicielem grupy, a następnie wybierz pozycję **Wybierz**.

    ![Zasady MDM — Zachód — strona Właściciele z wyróżnioną opcją Dodaj właścicieli](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Po wybraniu nowego właściciela można odświeżyć stronę **Właściciele** i wyświetlić nazwę dodaną do listy właścicieli.

## <a name="remove-an-owner-from-a-group"></a>Usuwanie właściciela z grupy
Usuń właściciela z grupy przy użyciu usługi Azure AD.

### <a name="to-remove-an-owner"></a>Aby usunąć właściciela
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **pozycję Azure Active Directory**, wybierz **grupki**, a następnie wybierz grupę, dla której chcesz usunąć właściciela (w tym przykładzie *zasady MDM — Zachód).*

3. Na stronie **Zasady MDM — Przegląd zachodni** wybierz pozycję **Właściciele**.

    ![Zasady MDM — strona Przegląd zachodu z wyróżnioną opcją Właściciele](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Na stronie **Zasady MDM — Zachód — Właściciele** wybierz użytkownika, którego chcesz usunąć jako właściciela grupy, wybierz pozycję **Usuń** ze strony z informacjami o użytkowniku i wybierz pozycję **Tak,** aby potwierdzić swoją decyzję.

    ![Strona informacyjna użytkownika z wyróżnioną opcją Usuń](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Po usunięciu właściciela możesz powrócić do strony **Właściciele** i zobaczyć, że nazwa została usunięta z listy właścicieli.

## <a name="next-steps"></a>Następne kroki
- [Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](active-directory-manage-groups.md)

- [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../users-groups-roles/groups-settings-cmdlets.md)

- [Przypisywanie dostępu do zintegrowanej aplikacji SaaS za pomocą grup](../users-groups-roles/groups-saasapps.md)

- [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

- [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../users-groups-roles/groups-settings-v2-cmdlets.md)
