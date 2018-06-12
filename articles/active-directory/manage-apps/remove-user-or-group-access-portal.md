---
title: Usuń przypisanie użytkownika lub grupy z aplikacji przedsiębiorstwa w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak usunąć przypisanie dostęp użytkownika lub grupy z aplikacji przedsiębiorstwa w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: b6da8eed16b67db098ceb90079b7da7dfadcd5e3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303624"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Usuń przypisanie użytkownika lub grupy z aplikacji przedsiębiorstwa w usłudze Azure Active Directory
To proste usunąć użytkownika lub grupy z przypisaniem dostęp do jednej z aplikacji przedsiębiorstwa w usłudze Azure Active Directory (Azure AD). Musi mieć odpowiednie uprawnienia do zarządzania aplikacjami przedsiębiorstwa, a musi być administratorem globalnym katalogu.

> [!NOTE]
> For Applications firmy Microsoft (takich jak aplikacje pakietu Office 365) usuwać użytkowników z aplikacjami za pomocą programu PowerShell.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Jak usunąć użytkownika lub grupy przypisania do aplikacji w przedsiębiorstwie w portalu Azure?
1. Zaloguj się do [portalu Azure](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2. Wybierz **więcej usług**, wprowadź **usługi Azure Active Directory** w polu tekstowym, a następnie wybierz **Enter**.
3. Na **usługi Azure Active Directory - *directoryname***  strona (oznacza to, że usługi Azure AD dla katalogu są używane do zarządzania), wybierz **aplikacje dla przedsiębiorstw**.

    ![Otwieranie aplikacji przedsiębiorstwa](./media/remove-user-or-group-access-portal/open-enterprise-apps.png)
4. Na **aplikacje dla przedsiębiorstw** wybierz pozycję **wszystkie aplikacje**. Zobaczysz listę aplikacji, którymi można zarządzać.
5. Na **aplikacje przedsiębiorstwa — wszystkie aplikacje** wybierz aplikację.
6. Na ***appname*** (to znaczy strony o nazwie wybranej aplikacji w tytule), wybierz **użytkownicy i grupy**.

    ![Wybieranie użytkowników lub grup](./media/remove-user-or-group-access-portal/remove-app-users.png)
7. Na ***appname*** **— przypisanie do grupy & użytkownika** strony, wybierz jedną więcej użytkowników lub grup, a następnie wybierz **Usuń** polecenia. Potwierdź decyzję w wierszu.

    ![Polecenie Remove](./media/remove-user-or-group-access-portal/remove-users.png)

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Jak usunąć użytkownika lub grupy przypisania do aplikacji przedsiębiorstwa przy użyciu programu PowerShell
1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień programu Windows PowerShell.

    >[!NOTE] 
    > Musisz zainstalować moduł AzureAD (za pomocą polecenia `Install-Module -Name AzureAD`). Jeśli zostanie wyświetlony monit, aby zainstalować moduł NuGet lub nowego modułu programu PowerShell usługi Azure Active Directory w wersji 2, typ T, a następnie naciśnij klawisz ENTER.

2. Uruchom `Connect-AzureAD` i zaloguj się przy użyciu konta administratora globalnego.
3. Aby przypisać użytkownika i roli aplikacji, użyj następującego skryptu:

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ``` 
## <a name="next-steps"></a>Kolejne kroki

- [Wyświetl wszystkie moje grupy](../active-directory-groups-view-azure-portal.md)
- [Przypisanie użytkownika lub grupę do aplikacji w przedsiębiorstwie](assign-user-or-group-access-portal.md)
- [Wyłącz logowania użytkowników dla aplikacji przedsiębiorstwa](disable-user-sign-in-portal.md)
- [Zmiana nazwy lub logo aplikacji przedsiębiorstwa](change-name-or-logo-portal.md)
