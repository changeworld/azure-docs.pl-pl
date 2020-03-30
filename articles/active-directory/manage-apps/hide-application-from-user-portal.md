---
title: Ukrywanie aplikacji przed korzystaniem z usługi Azure AD
description: Jak ukryć aplikację przed korzystaniem z usług użytkownika w panelach dostępu usługi Azure Active Directory lub w programach uruchamiania usługi Office 365.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5718adf4fd76e2fbd0ff793dd2fa33ee08f7c0fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295053"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>Ukrywanie aplikacji przed użytkownikami końcowymi w usłudze Azure Active Directory

Instrukcje dotyczące ukrywania aplikacji przed panelem MyApps użytkowników końcowych lub programem uruchamiawki usługi Office 365. Gdy aplikacja jest ukryta, użytkownicy nadal mają uprawnienia do aplikacji. 

## <a name="prerequisites"></a>Wymagania wstępne

Uprawnienia administratora aplikacji są wymagane do ukrycia aplikacji w panelu MyApps i uruchomieniu usługi Office 365.

Uprawnienia administratora globalnego są wymagane do ukrycia wszystkich aplikacji usługi Office 365.


## <a name="hide-an-application-from-the-end-user"></a>Ukrywanie aplikacji przed użytkownikiem końcowym
Aby ukryć aplikację w panelu MyApps i w usłudze Office 365, należy wykonać następujące czynności.

1.  Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator globalny dla swojego katalogu.
2.  Wybierz pozycję **Azure Active Directory**.
3.  Wybierz **aplikacje enterprise**. Otwarte są aplikacje Enterprise — wszystkie **aplikacje.**
4.  W obszarze **Typ aplikacji**wybierz pozycję **Aplikacje przedsiębiorstwa**, jeśli nie jest jeszcze zaznaczona.
5.  Wyszukaj aplikację, którą chcesz ukryć, i kliknij aplikację.  Zostanie otwarty przegląd aplikacji.
6.  Kliknij pozycję **Właściwości**. 
7.  W przypadku pytania **Widoczne dla użytkowników** kliknij przycisk **Nie**.
8.  Kliknij przycisk **Zapisz**.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Ukrywanie aplikacji za pomocą programu Azure AD PowerShell

Aby ukryć aplikację w panelu MyApps, możesz ręcznie dodać tag HideApp do jednostki usługi dla aplikacji. Uruchom następujące polecenia [programu AzureAD PowerShell,](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#service_principals) aby ustawić właściwość **Visible** to Users aplikacji na **Nie**. 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Ukrywanie aplikacji usługi Office 365 z panelu MyApps

Aby ukryć wszystkie aplikacje usługi Office 365 w panelu MyApps, należy wykonać następujące czynności. Aplikacje są nadal widoczne w portalu usługi Office 365.

1.  Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator globalny dla swojego katalogu.
2.  Wybierz pozycję **Azure Active Directory**.
3.  Wybierz pozycję **Ustawienia użytkownika**.
4.  W obszarze **Aplikacje dla przedsiębiorstw**kliknij pozycję **Zarządzaj uruchamianiem i wyświetlaniem aplikacji przez użytkowników końcowych.**
5.  W **przypadku użytkowników mogą być widoczne tylko aplikacje usługi Office 365 w portalu usługi Office 365**, kliknij przycisk **Tak**.
6.  Kliknij przycisk **Zapisz**.

## <a name="next-steps"></a>Następne kroki
* [Zobacz wszystkie moje grupy](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](assign-user-or-group-access-portal.md)
* [Usuwanie przypisania użytkownika lub grupy z aplikacji przedsiębiorstwa](remove-user-or-group-access-portal.md)
* [Zmienianie nazwy lub logo aplikacji przedsiębiorstwa](change-name-or-logo-portal.md)

