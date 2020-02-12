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
ms.openlocfilehash: f83a48ad13fe4ae217319b2aa85adf976aa6a7ae
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149851"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Dodawanie lub usuwanie właściciele grupy w usłudze Azure Active Directory
Grupy usługi Azure Active Directory (Azure AD), są własnością i zarządzane przez właścicieli grupy. Właściciele grup mogą być użytkownikami lub jednostkami usługi i mogą zarządzać grupą, w tym członkostwem. Tylko istniejący właściciele grup lub Administratorzy grup mogą przypisywać właścicieli grup. Właściciele grupy nie muszą być członkami grupy.

Gdy grupa nie ma właściciela, Administratorzy zarządzający grupami nadal mogą zarządzać tą grupą. Jest to zalecane dla każdej grupy, aby mieć co najmniej jednego właściciela. Gdy właściciele są przypisać do grupy, nie można usunąć ostatniego właściciela grupy. Przed usunięciem ostatniego właściciela z grupy upewnij się, że wybrano innego właściciela.

## <a name="add-an-owner-to-a-group"></a>Dodawanie właściciela do grupy
Poniżej znajdują się instrukcje dodawania użytkownika jako właściciela do grupy przy użyciu portalu usługi Azure AD. Aby dodać nazwę główną usługi jako właściciela grupy, postępuj zgodnie z instrukcjami, aby to zrobić przy użyciu [programu PowerShell](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0).

### <a name="to-add-a-group-owner"></a>Aby dodać właściciela grupy
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz pozycję **Azure Active Directory**, wybierz pozycję **grupy**, a następnie wybierz grupę, dla której chcesz dodać właściciela (na przykład *zasady zarządzania urządzeniami przenośnymi — zachodni*).

3. Na stronie **Przegląd zasad zarządzania urządzeniami przenośnymi** wybierz pozycję **właściciele**.

    ![Zasady zarządzania urządzeniami Przenośnymi — strona przeglądu zachodnie z podświetloną opcją właścicieli](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. Na stronie **zasady zarządzania urządzeniami przenośnymi-zachodni-właściciele** wybierz pozycję **Dodaj właścicieli**, a następnie wyszukaj i wybierz użytkownika, który będzie nowym właścicielem grupy, a następnie wybierz **pozycję Wybierz**.

    ![Zarządzania urządzeniami Przenośnymi zasad - zachód - właścicieli strony z podświetloną opcją właścicieli Dodaj](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Po wybraniu nowego właściciela można odświeżyć stronę **właściciele** i zobaczyć nazwę dodaną do listy właścicieli.

## <a name="remove-an-owner-from-a-group"></a>Usuwanie właściciela z grupy
Usuwanie właściciela z grupy przy użyciu usługi Azure AD.

### <a name="to-remove-an-owner"></a>Można usunąć właściciela
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz pozycję **Azure Active Directory**, wybierz pozycję **grupy**, a następnie wybierz grupę, dla której chcesz usunąć właściciela (na przykład *zasady zarządzania urządzeniami przenośnymi — zachodni*).

3. Na stronie **Przegląd zasad zarządzania urządzeniami przenośnymi** wybierz pozycję **właściciele**.

    ![Zasady zarządzania urządzeniami Przenośnymi — strona przeglądu zachodnie z podświetloną opcją właścicieli](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Na stronie **zasady zarządzania urządzeniami przenośnymi-zachodni-właściciele** wybierz użytkownika, którego chcesz usunąć jako właściciela grupy, wybierz pozycję **Usuń** ze strony informacje o użytkowniku, a następnie wybierz pozycję **tak** , aby potwierdzić decyzję.

    ![Strona informacji użytkownika z podświetloną opcją Remove](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Po usunięciu właściciela można wrócić do strony **właściciele** i zobaczyć, że nazwa została usunięta z listy właścicieli.

## <a name="next-steps"></a>Następne kroki
- [Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](active-directory-manage-groups.md)

- [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../users-groups-roles/groups-settings-cmdlets.md)

- [Przypisywanie dostępu do zintegrowanej aplikacji SaaS przy użyciu grup](../users-groups-roles/groups-saasapps.md)

- [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

- [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../users-groups-roles/groups-settings-v2-cmdlets.md)
