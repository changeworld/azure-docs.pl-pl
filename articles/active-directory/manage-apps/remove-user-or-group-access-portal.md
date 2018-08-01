---
title: Usuń przypisanie użytkownika lub grupy z aplikacji przedsiębiorstwa w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak usunąć przypisanie dostępu użytkownika lub grupy z aplikacji przedsiębiorstwa w usłudze Azure Active Directory
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
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: fde6d5fa2488d86af542f409df7c5b76d2510f08
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39369156"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Usuń przypisanie użytkownika lub grupy z aplikacji przedsiębiorstwa w usłudze Azure Active Directory
To proste usunąć użytkownika lub grupy z przypisania dostępu do jednej z Twoich aplikacjach firmowych w usłudze Azure Active Directory (Azure AD). Musi mieć odpowiednie uprawnienia do zarządzania aplikacji przedsiębiorstwa, a musi być administratorem globalnym katalogu.

> [!NOTE]
> For Applications firmy Microsoft (takich jak aplikacje usługi Office 365) usuwać użytkowników z aplikacji przedsiębiorstwa za pomocą programu PowerShell.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Jak usunąć użytkownika lub przypisania grupy do aplikacji przedsiębiorstwa w witrynie Azure portal?
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2. Wybierz **więcej usług**, wprowadź **usługi Azure Active Directory** w polu tekstowym, a następnie wybierz pozycję **Enter**.
3. Na **usługi Azure Active Directory — *directoryname***  strony (oznacza to, że strony usługi Azure AD dla katalogu zarządzasz), wybierz **aplikacje dla przedsiębiorstw**.

    ![Otwieranie aplikacji dla przedsiębiorstw](./media/remove-user-or-group-access-portal/open-enterprise-apps.png)
4. Na **aplikacje dla przedsiębiorstw** wybierz opcję **wszystkie aplikacje**. Zobaczysz listę aplikacji, którymi można zarządzać.
5. Na **aplikacje w przedsiębiorstwie — wszystkie aplikacje** wybierz aplikację.
6. Na ***appname*** strony (czyli strony o nazwie wybranej aplikacji w tytule), wybierz **użytkownikami i grupami**.

    ![Wybieranie użytkowników lub grup](./media/remove-user-or-group-access-portal/remove-app-users.png)
7. Na ***appname*** **— przypisanie do grupy & użytkownika** stronie, wybierz jedną z więcej użytkowników lub grup, a następnie wybierz **Usuń** polecenia. Potwierdź decyzję w wierszu.

    ![Wybierając polecenie Remove](./media/remove-user-or-group-access-portal/remove-users.png)

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Jak usunąć użytkownika lub przypisania grupy do aplikacji przedsiębiorstwa przy użyciu programu PowerShell?
1. Otwórz wiersz polecenia programu Windows PowerShell.

    >[!NOTE] 
    > Musisz zainstalować moduł usługi Azure AD (Użyj polecenia `Install-Module -Name AzureAD`). Jeśli zostanie wyświetlony monit, aby zainstalować moduł NuGet lub nowego modułu programu PowerShell usługi Azure Active Directory w wersji 2, wpisz Y, a następnie naciśnij klawisz ENTER.

2. Uruchom `Connect-AzureAD` i zaloguj się przy użyciu konta użytkownika administratora globalnego.
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

- [Zobacz wszystkie moje grupy](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](assign-user-or-group-access-portal.md)
- [Wyłączanie logowania użytkowników dla aplikacji przedsiębiorstwa](disable-user-sign-in-portal.md)
- [Zmiana nazwy lub logo aplikacji przedsiębiorstwa](change-name-or-logo-portal.md)
