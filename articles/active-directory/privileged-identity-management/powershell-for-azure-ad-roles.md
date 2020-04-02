---
title: Role usługi PowerShell dla usługi Azure AD w usłudze PIM — usługa Azure AD | Dokumenty firmy Microsoft
description: Zarządzanie rolami usługi Azure AD przy użyciu poleceń cmdlet programu PowerShell w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fa241a261b8dcb21dd39b5dacacac9aa4889304
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519648"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>Role usługi PowerShell dla usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi

Ten artykuł zawiera instrukcje dotyczące używania poleceń cmdlet programu PowerShell usługi Azure Active Directory (Azure AD) do zarządzania rolami usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi (PIM). Informuje również, jak skonfigurować moduł programu Azure AD PowerShell.

> [!Note]
> Nasz oficjalny program PowerShell jest obsługiwany tylko wtedy, gdy korzystasz z nowej wersji uprzywilejowanego zarządzania tożsamościami usługi Azure AD. Przejdź do uprzywilejowanego zarządzania tożsamościami i upewnij się, że masz następujący baner na bloku szybkiego startu.
> [![sprawdź wersję uprzywilejowanego zarządzania tożsamościami, którą](media/pim-how-to-add-role-to-user/pim-new-version.png "Wybieranie zarządzania tożsamościami > uprzywilejowanych usługi Azure AD")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox) Jeśli nie masz tego banera, poczekaj, jak jesteśmy obecnie w trakcie wprowadzania tego zaktualizowanego doświadczenia w ciągu najbliższych kilku tygodni.
> Polecenia cmdlet usługi PowerShell zarządzania tożsamościami uprzywilejowanymi są obsługiwane za pośrednictwem modułu usługi Azure AD Preview. Jeśli używasz innego modułu, a ten moduł zwraca teraz komunikat o błędzie, zacznij używać tego nowego modułu. Jeśli masz jakieś systemy produkcyjne zbudowane na innym module, skontaktuj się zpim_preview@microsoft.com

## <a name="installation-and-setup"></a>Instalacja i konfiguracja

1. Instalowanie modułu usługi Azure AD Preview

        Install-module AzureADPreview

1. Upewnij się, że masz wymagane uprawnienia roli przed kontynuowaniem. Jeśli próbujesz wykonywać zadania zarządzania, takie jak nadanie przypisania roli lub aktualizowanie ustawienia roli, upewnij się, że masz rolę administratora globalnego lub administratora ról uprzywilejowanych. Jeśli po prostu próbujesz aktywować własne przypisanie, nie są wymagane żadne uprawnienia wykraczające poza domyślne uprawnienia użytkownika.

1. Połącz się z usługą Azure AD.

        $AzureAdCred = Get-Credential  
        Connect-AzureAD -Credential $AzureAdCred

1. Znajdź swój identyfikator **dzierżawy,** > przechodząc do identyfikatora katalogu**właściwości** > usługi Azure Active**Directory**. W sekcji polecenia cmdlets użyj tego identyfikatora, gdy trzeba podać identyfikator resourceId.

    ![Znajdowanie identyfikatora dzierżawy we właściwościach organizacji usługi Azure AD](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> Poniższe sekcje są prostymi przykładami, które mogą pomóc Ci w uruchomieniu. Bardziej szczegółową dokumentację dotyczącą następujących poleceń https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_managementcmdlet można znaleźć na poziomie . Jednak należy zastąpić "azureResources" w providerID parametr z "aadRoles". Należy również pamiętać, aby użyć identyfikatora dzierżawy dla organizacji usługi Azure AD jako parametr resourceId.

## <a name="retrieving-role-definitions"></a>Pobieranie definicji ról

Użyj następującego polecenia cmdlet, aby uzyskać wszystkie wbudowane i niestandardowe role usługi Azure AD w twojej organizacji usługi Azure AD (dzierżawie). Ten ważny krok zapewnia mapowanie między nazwą roli a roląDefinitionId. RolaDefinitionId jest używany w tych poleceń cmdlet w celu odwołania się do określonej roli.

Obiekt roleDefinitionId jest specyficzny dla organizacji usługi Azure AD i różni się od funkcjiDefinitionId zwracany przez interfejs API zarządzania rolami.

    Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26

Wynik:

![Pobierz wszystkie role dla organizacji usługi Azure AD](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>Pobieranie przypisań ról

Użyj następującego polecenia cmdlet, aby pobrać wszystkie przypisania ról w organizacji usługi Azure AD.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"

Użyj następującego polecenia cmdlet, aby pobrać wszystkie przypisania ról dla określonego użytkownika. Ta lista jest również znana jako "Moje role" w portalu usługi Azure AD. Jedyną różnicą jest to, że dodano filtr dla identyfikatora tematu. Identyfikator tematu w tym kontekście jest identyfikatorem użytkownika lub identyfikatorem grupy.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 

Użyj następującego polecenia cmdlet, aby pobrać wszystkie przypisania ról dla określonej roli. RolaDefinitionId w tym miejscu jest identyfikator, który jest zwracany przez poprzednie polecenie cmdlet.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"

Polecenia cmdlet powodują wyświetlenia listy obiektów przypisania ról pokazanych poniżej. Identyfikator tematu jest identyfikatorem użytkownika użytkownika, do którego przypisana jest rola. Stan przypisania może być aktywny lub kwalifikowalny. Jeśli użytkownik jest aktywny i istnieje identyfikator w polu LinkedEligibleRoleAssignmentId, oznacza to, że rola jest aktualnie aktywowana.

Wynik:

![Pobieranie wszystkich przypisań ról dla organizacji usługi Azure AD](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>Przypisywanie roli

Użyj następującego polecenia cmdlet, aby utworzyć odpowiednie przypisanie.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 

Harmonogram, który definiuje czas rozpoczęcia i zakończenia przypisania, jest obiektem, który można utworzyć w następującym przykładzie:

    $schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
    $schedule.Type = "Once"
    $schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
    $schedule.endDateTime = "2020-07-25T20:49:11.770Z"
> [!Note]
> Jeśli wartość endDateTime jest ustawiona na wartość null, wskazuje stałe przypisanie.

## <a name="activate-a-role-assignment"></a>Uaktywnianie przypisania roli

Użyj następującego polecenia cmdlet, aby aktywować kwalifikujące się przypisanie.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas" 

To polecenie cmdlet jest prawie identyczne z poleceniem cmdlet do tworzenia przypisania roli. Kluczową różnicą między poleceniami cmdlet jest to, że dla parametru –Type aktywacja jest "userAdd" zamiast "adminAdd". Inną różnicą jest to, że parametr –AssignmentState to "Aktywny" zamiast "Kwalifikujący się".

> [!Note]
> Istnieją dwa scenariusze ograniczające aktywację roli za pośrednictwem programu PowerShell.
> 1. Jeśli potrzebujesz systemu biletu / numeru biletu w ustawieniach roli, nie ma możliwości podania ich jako parametru. W związku z tym nie będzie można aktywować rolę poza witryną Azure portal. Ta funkcja jest wdrażana w programie PowerShell w ciągu najbliższych kilku miesięcy.
> 1. Jeśli do aktywacji roli wymagane jest uwierzytelnianie wieloskładnikowe, obecnie program PowerShell nie może wyzywać użytkownika po aktywowaniu roli. Zamiast tego użytkownicy będą musieli wyzwolić wyzwanie usługi MFA, gdy łączą się z usługą Azure AD, wykonując [ten wpis w blogu](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html) od jednego z naszych inżynierów. Jeśli tworzysz aplikację dla usługi PIM, jedną z możliwych implementacji jest wyzwanie dla użytkowników i ponownie połączyć ich z modułem po otrzymaniu błędu "MfaRule".

## <a name="retrieving-and-updating-role-settings"></a>Pobieranie i aktualizowanie ustawień roli

Użyj następującego polecenia cmdlet, aby uzyskać wszystkie ustawienia roli w organizacji usługi Azure AD.

    Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'" 

W ustawieniach znajdują się cztery główne obiekty. Tylko trzy z tych obiektów są obecnie używane przez pim. Ustawienia użytkowników to ustawienia aktywacji, Ustawienia administracyjne są ustawieniami przydziałów dla kwalifikujących się przydziałów, a ustawienia administracji są ustawieniami przypisania dla aktywnych przypisań.

[![](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png "Get and update role settings")](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

Aby zaktualizować ustawienie roli, należy najpierw zdefiniować obiekt ustawień w następujący sposób:

    $setting = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting 
    $setting.RuleIdentifier = "JustificationRule"
    $setting.Setting = "{'required':false}"

Następnie można przejść do przodu i zastosować ustawienie do jednego z obiektów dla określonej roli, jak pokazano poniżej. Identyfikator w tym miejscu jest identyfikator ustawienia roli, które mogą być pobierane z wyniku polecenia cmdlet ustawień roli listy.

    Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $setting 

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie roli niestandardowej usługi Azure AD](azure-ad-custom-roles-assign.md)
- [Usuwanie lub aktualizowanie przypisania niestandardowego zadania usługi Azure AD](azure-ad-custom-roles-update-remove.md)
- [Konfigurowanie przypisania niestandardowego zadania usługi Azure AD](azure-ad-custom-roles-configure.md)
- [Definicje ról w usłudze Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
