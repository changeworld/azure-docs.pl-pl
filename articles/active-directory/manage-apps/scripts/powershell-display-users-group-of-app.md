---
title: Przykład programu PowerShell — lista użytkowników & grup dla aplikacji Proxy aplikacji
description: Przykład programu PowerShell zawierający listę wszystkich użytkowników i grup przypisanych do określonej aplikacji proxy aplikacji usługi Azure Active Directory (Azure AD).
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
ms.openlocfilehash: aab5ac5d4712dd9e72fc0b9665d740859b5a6775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483390"
---
# <a name="display-users-and-groups-assigned-to-an-application-proxy-application"></a>Wyświetlanie użytkowników i grup przypisanych do aplikacji proxy aplikacji

W tym przykładzie skryptu programu PowerShell wymieniono użytkowników i grupy przypisane do określonej aplikacji proxy aplikacji usługi Azure Active Directory (Azure AD).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ten przykład wymaga [modułu AzureAD V2 PowerShell for Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) lub [wersji preview modułu AzureAD V2 for Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/display-users-group-of-an-app.ps1 "Display users and groups assigned to an Application Proxy application")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

| Polecenie | Uwagi |
|---|---|
| [Get-AzureADUser](https://docs.microsoft.com/powershell/module/AzureAD/get-azureaduser?view=azureadps-2.0)| Pobiera użytkownika. |
| [Get-AzureADGroup](https://docs.microsoft.com/powershell/module/AzureAD/get-azureadgroup?view=azureadps-2.0)| Pobiera grupę. |
| [Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Pobiera jednostki usługi. |
| [Get-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/AzureAD/get-azureaduserapproleassignment?view=azureadps-2.0) | Pobierz przypisanie roli aplikacji użytkownika. |
| [Get-AzureADGroupAppRoleAssignment](https://docs.microsoft.com/powershell/module/AzureAD/get-azureadgroupapproleassignment?view=azureadps-2.0) | Pobierz przypisanie roli aplikacji grupy. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu programu Azure AD PowerShell, zobacz [omówienie modułu programu Azure AD PowerShell.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)

Aby zapoznać się z innymi przykładami programu PowerShell dla serwera proxy aplikacji, zobacz [przykłady programu Azure AD PowerShell dla serwera proxy aplikacji usługi Azure AD](../application-proxy-powershell-samples.md).