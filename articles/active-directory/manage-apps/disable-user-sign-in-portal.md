---
title: Wyłączanie logów użytkowników dla aplikacji dla przedsiębiorstwa w usłudze Azure AD
description: Jak wyłączyć aplikację przedsiębiorstwa, aby żaden użytkownik nie mógł się do niej zalogować w usłudze Azure Active Directory
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
ms.openlocfilehash: 10553898376c4b9236ee62718fffccd45b12d70b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274087"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Wyłączanie logów użytkowników dla aplikacji dla przedsiębiorstwa w usłudze Azure Active Directory

Łatwo jest wyłączyć aplikację dla przedsiębiorstw, aby żaden użytkownik nie mógł się do niej zalogować w usłudze Azure Active Directory (Azure AD). Do zarządzania aplikacją dla przedsiębiorstw potrzebne są odpowiednie uprawnienia. I musisz być administratorem globalnym dla katalogu.

## <a name="how-do-i-disable-user-sign-ins"></a>Jak wyłączyć logowania użytkowników?

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
1. Wybierz **pozycję Wszystkie usługi**, wprowadź usługę Azure Active **Directory** w polu tekstowym, a następnie wybierz pozycję **Wprowadź**.
1. W okienku***nazwy katalogów*** **usługi Azure Active Directory** -  (czyli okienku usługi Azure AD dla katalogu, który zarządzasz), wybierz **pozycję Aplikacje przedsiębiorstwa**.
1. W okienku **Aplikacje dla przedsiębiorstw — wszystkie aplikacje** jest widoczna lista aplikacji, którymi można zarządzać. Wybierz aplikację.
1. W okienku ***appname*** (czyli okienku z nazwą wybranej aplikacji w tytule) wybierz pozycję **Właściwości**.
1. W okienku**Właściwości** ***aplikacji*** - wybierz pozycję **Nie** dla włączone dla użytkowników **do logowania?**.
1. Wybierz polecenie **Zapisz.**

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Wyłączanie aplikacji niepublicznej za pomocą programu Azure AD PowerShell

Jeśli znasz identyfikator app, który nie pojawia się na liście aplikacje przedsiębiorstwa (na przykład, ponieważ usunięto aplikację lub podmiot usługi nie został jeszcze utworzony z powodu wstępnie autoryzowanej aplikacji przez firmę Microsoft), możesz ręcznie utworzyć jednostkę usługi dla aplikacji, a następnie wyłączyć ją przy użyciu [polecenia cmdlet AzureAD PowerShell](https://docs.microsoft.com/powershell/module/azuread/New-AzureADServicePrincipal?view=azureadps-2.0).

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>Następne kroki

* [Zobacz wszystkie moje grupy](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](assign-user-or-group-access-portal.md)
* [Usuwanie przypisania użytkownika lub grupy z aplikacji przedsiębiorstwa](remove-user-or-group-access-portal.md)
* [Zmienianie nazwy lub logo aplikacji przedsiębiorstwa](change-name-or-logo-portal.md)
