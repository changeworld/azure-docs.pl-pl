---
title: Usuń przypisanie użytkownika lub grupy z aplikacji przedsiębiorstwa w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak usunąć przypisanie dostępu użytkownika lub grupy z aplikacji przedsiębiorstwa w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b6524a757d885e95637cb05480838db8ac37259
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67701943"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Usuń przypisanie użytkownika lub grupy z aplikacji przedsiębiorstwa w usłudze Azure Active Directory

To proste usunąć użytkownika lub grupę z przypisanego dostępu do jednej z Twoich aplikacjach firmowych w usłudze Azure Active Directory (Azure AD). Konieczne jest odpowiednie uprawnienia do zarządzania aplikacji przedsiębiorstwa. Ponadto musisz być administratorem globalnym katalogu.

> [!NOTE]
> For Applications firmy Microsoft (takich jak aplikacje usługi Office 365) usuwać użytkowników z aplikacji przedsiębiorstwa za pomocą programu PowerShell.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Jak usunąć użytkownika lub przypisania grupy do aplikacji przedsiębiorstwa w witrynie Azure portal?

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
1. Wybierz **wszystkich usług**, wprowadź **usługi Azure Active Directory** w polu tekstowym, a następnie wybierz pozycję **Enter**.
1. Na **usługi Azure Active Directory — *directoryname***  strony (czyli strony usługi Azure AD dla katalogu zarządzasz), wybierz **aplikacje dla przedsiębiorstw**.
1. Na **aplikacje w przedsiębiorstwie — wszystkie aplikacje** stronie zostaną wyświetlone listy aplikacje, którymi można zarządzać. Wybierz aplikację.
1. Na ***appname*** strony Przegląd (czyli strony o nazwie wybranej aplikacji w tytule), wybierz **użytkownikami i grupami**.
1. Na ***appname*** **— przypisanie do grupy & użytkownika** stronie, wybierz jedną z więcej użytkowników lub grup, a następnie wybierz **Usuń** polecenia. Potwierdź decyzję w wierszu.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Jak usunąć użytkownika lub przypisania grupy do aplikacji przedsiębiorstwa przy użyciu programu PowerShell?

1. Otwórz wiersz polecenia programu Windows PowerShell.

   > [!NOTE]
   > Musisz zainstalować moduł usługi Azure AD (Użyj polecenia `Install-Module -Name AzureAD`). Jeśli zostanie wyświetlony monit, aby zainstalować moduł NuGet lub nowego modułu programu PowerShell usługi Azure Active Directory w wersji 2, wpisz Y, a następnie naciśnij klawisz ENTER.

1. Uruchom `Connect-AzureAD` i zaloguj się przy użyciu konta użytkownika administratora globalnego.
1. Aby usunąć użytkownika i roli z aplikacji, użyj następującego skryptu:

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
