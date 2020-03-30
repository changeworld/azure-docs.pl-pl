---
title: Usuwanie przypisań użytkowników lub grup z aplikacji w usłudze Azure AD
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
ms.openlocfilehash: edf918b57212cf2adfbffb358a0257d9dbea85e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275882"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Usuwanie przypisania użytkownika lub grupy z aplikacji przedsiębiorstwa w usłudze Azure Active Directory

Łatwo jest usunąć użytkownika lub grupę z przypisanego dostępu do jednej z aplikacji dla przedsiębiorstw w usłudze Azure Active Directory (Azure AD). Do zarządzania aplikacją dla przedsiębiorstw potrzebne są odpowiednie uprawnienia. I musisz być administratorem globalnym dla katalogu.

> [!NOTE]
> W przypadku aplikacji Microsoft Applications (takich jak aplikacje usługi Office 365) użyj programu PowerShell, aby usunąć użytkowników do aplikacji dla przedsiębiorstw.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Jak usunąć przypisanie użytkownika lub grupy do aplikacji przedsiębiorstwa w witrynie Azure Portal?

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
1. Wybierz **pozycję Wszystkie usługi**, wprowadź usługę Azure Active **Directory** w polu tekstowym, a następnie wybierz pozycję **Wprowadź**.
1. Na stronie **Azure Active Directory — *nazwa katalogu* ** (czyli strona usługi Azure AD dla katalogu, który zarządzasz), wybierz pozycję **Aplikacje przedsiębiorstwa**.
1. Na stronie **Aplikacje dla przedsiębiorstw — wszystkie aplikacje** zostanie wyświetlona lista aplikacji, którymi możesz zarządzać. Wybierz aplikację.
1. Na stronie przegląd ***aplikacji*** (czyli stronie z nazwą wybranej aplikacji w tytule) wybierz pozycję **Użytkownicy & Grupy**.
1. Na stronie ***Appname*** **- User & Group Assignment (Przypisanie grupy** użytkownika) wybierz jedną z większej liczby użytkowników lub grup, a następnie wybierz polecenie **Usuń.** Potwierdź swoją decyzję w punkcie monitu.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Jak usunąć przypisanie użytkownika lub grupy do aplikacji przedsiębiorstwa przy użyciu programu PowerShell?

1. Otwórz wiersz polecenia programu Windows PowerShell z podwyższonym poziomem uprawnień.

   > [!NOTE]
   > Należy zainstalować moduł AzureAD (użyj `Install-Module -Name AzureAD`polecenia ). Jeśli zostanie wyświetlony monit o zainstalowanie modułu NuGet lub nowego modułu programu PowerShell usługi Azure Active Directory V2, wpisz Y i naciśnij klawisz ENTER.

1. Uruchamianie `Connect-AzureAD` konta użytkownika administratora globalnego i logowanie się do tego konta administratora globalnego.
1. Użyj następującego skryptu, aby usunąć użytkownika i rolę z aplikacji:

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

## <a name="next-steps"></a>Następne kroki

- [Zobacz wszystkie moje grupy](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](assign-user-or-group-access-portal.md)
- [Wyłączanie logów użytkowników dla aplikacji dla przedsiębiorstwa](disable-user-sign-in-portal.md)
- [Zmienianie nazwy lub logo aplikacji przedsiębiorstwa](change-name-or-logo-portal.md)
