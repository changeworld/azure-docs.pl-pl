---
title: Dodawanie lub usuwanie właściciele grupy — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Instrukcje dotyczące sposobu dodawać i usuwać grupy właścicieli przy użyciu usługi Azure Active Directory.
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
ms.openlocfilehash: d259be0c04af0fcf3628a9f296730749404610cb
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562081"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Dodawanie lub usuwanie właściciele grupy w usłudze Azure Active Directory
Grupy usługi Azure Active Directory (Azure AD), są własnością i zarządzane przez właścicieli grupy. Właściciele grup mogą być użytkownikami lub jednostkami usługi i mogą zarządzać grupą, w tym członkostwem. Tylko istniejący właściciele grup lub Administratorzy grup mogą przypisywać grupę onwers. Właściciele grupy nie muszą być członkami grupy.

Gdy grupa nie ma właściciela, Administratorzy zarządzający grupami nadal mogą zarządzać tą grupą.

## <a name="add-an-owner-to-a-group"></a>Dodawanie właściciela do grupy
Poniżej znajdują się instrukcje dodawania użytkownika jako właściciela do grupy przy użyciu portalu usługi Azure AD. Aby dodać nazwę główną usługi jako właściciela grupy, postępuj zgodnie z instrukcjami, aby to zrobić przy użyciu [programu PowerShell](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0).

### <a name="to-add-a-group-owner"></a>Aby dodać właściciela grupy
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **usługi Azure Active Directory**, wybierz opcję **grup**, a następnie wybierz grupę, dla którego chcesz dodać jako właściciela (w tym przykładzie *zasad zarządzania urządzeniami Przenośnymi - Zachodnia*).

3. Na **zasady zarządzania urządzeniami Przenośnymi — omówienie zachodnie** wybierz opcję **właścicieli**.

    ![Zasady zarządzania urządzeniami Przenośnymi — strona przeglądu zachodnie z podświetloną opcją właścicieli](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. Na **właścicieli - Zachodnia — zasady zarządzania urządzeniami Przenośnymi** wybierz opcję **Dodawanie właścicieli**, a następnie wyszukaj i wybierz użytkownika, który będzie nowego właściciela grupy, a następnie wybierz **wybierz**.

    ![Zarządzania urządzeniami Przenośnymi zasad - zachód - właścicieli strony z podświetloną opcją właścicieli Dodaj](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Po zaznaczeniu nowego właściciela, możesz odświeżyć **właścicieli** strony, a nazwa dodane do listy właścicieli jest wyświetlana.

## <a name="remove-an-owner-from-a-group"></a>Usuwanie właściciela z grupy
Usuwanie właściciela z grupy przy użyciu usługi Azure AD.

### <a name="to-remove-an-owner"></a>Można usunąć właściciela
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **usługi Azure Active Directory**, wybierz opcję **grup**, a następnie wybierz grupę, dla którego chcesz usunąć właściciela (w tym przykładzie *zasad zarządzania urządzeniami Przenośnymi - Zachodnia*).

3. Na **zasady zarządzania urządzeniami Przenośnymi — omówienie zachodnie** wybierz opcję **właścicieli**.

    ![Zasady zarządzania urządzeniami Przenośnymi — strona przeglądu zachodnie z podświetloną opcją właścicieli](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Na **właścicieli - Zachodnia — zasady zarządzania urządzeniami Przenośnymi** wybierz użytkownika, aby Usuń jako właściciela grupy, wybierz **Usuń** z strona informacji o użytkownika i wybierz przycisk **tak** o potwierdzenie Twoją decyzję.

    ![Strona informacji użytkownika z podświetloną opcją Remove](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Po usunięciu właściciela możesz wrócić do **właścicieli** strony i zobacz, nazwa została usunięta z listy właścicieli.

## <a name="next-steps"></a>Kolejne kroki
- [Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](active-directory-manage-groups.md)

- [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../users-groups-roles/groups-settings-cmdlets.md)

- [Używanie grup do udzielania dostępu do zintegrowanych aplikacji SaaS](../users-groups-roles/groups-saasapps.md)

- [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

- [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../users-groups-roles/groups-settings-v2-cmdlets.md)
