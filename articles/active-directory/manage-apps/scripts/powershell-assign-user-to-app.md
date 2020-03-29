---
title: Przykład programu PowerShell — przypisywanie użytkownika do aplikacji proxy aplikacji
description: Przykład programu PowerShell, który przypisuje użytkownika do aplikacji proxy aplikacji usługi Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42667ebdfc37b679d56421e3a82d4a6aaaed8fdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483338"
---
# <a name="assign-a-user-to-a-specific-azure-ad-application-proxy-application"></a>Przypisywanie użytkownika do określonej aplikacji serwera proxy aplikacji usługi Azure AD

Ten przykład skryptu programu PowerShell umożliwia przypisanie użytkownika do określonej aplikacji serwera proxy aplikacji usługi Azure AD.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ten przykład wymaga [modułu AzureAD V2 PowerShell for Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) lub [wersji preview modułu AzureAD V2 for Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-user-to-app.ps1 "Assign a user to an application")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

| Polecenie | Uwagi |
|---|---|
| [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/AzureAD/new-azureaduserapproleassignment?view=azureadps-2.0) | Przypisuje użytkownika do roli aplikacji. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu programu Azure AD PowerShell, zobacz [omówienie modułu programu Azure AD PowerShell.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)

Aby zapoznać się z innymi przykładami programu PowerShell dla serwera proxy aplikacji, zobacz [przykłady programu Azure AD PowerShell dla serwera proxy aplikacji usługi Azure AD](../application-proxy-powershell-samples.md).