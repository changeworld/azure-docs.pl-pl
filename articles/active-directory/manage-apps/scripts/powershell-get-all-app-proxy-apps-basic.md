---
title: Przykład programu PowerShell — lista podstawowych informacji dotyczących aplikacji proxy aplikacji
description: Przykład programu PowerShell zawierający listę aplikacji proxy aplikacji usługi Azure Active Directory (Azure AD) wraz z identyfikatorem aplikacji (Identyfikator aplikacji), nazwą (DisplayName) i identyfikatorem obiektu (ObjId).
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
ms.openlocfilehash: 009aabb8b742a752a29d775efbd11b153588c09b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482220"
---
# <a name="get-all-application-proxy-apps-and-list-basic-information"></a>Pobierz wszystkie aplikacje proxy aplikacji i listę podstawowych informacji

W tym przykładzie skryptu programu PowerShell są wyświetlane informacje dotyczące wszystkich aplikacji proxy aplikacji usługi Azure Active Directory (Azure AD), w tym identyfikatora aplikacji (identyfikatora aplikacji), nazwy (DisplayName) i identyfikatora obiektu (ObjId).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ten przykład wymaga [modułu AzureAD V2 PowerShell for Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) lub [wersji preview modułu AzureAD V2 for Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).
## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-basic.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

| Polecenie | Uwagi |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Pobiera jednostki usługi. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu programu Azure AD PowerShell, zobacz [omówienie modułu programu Azure AD PowerShell.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)

Aby zapoznać się z innymi przykładami programu PowerShell dla serwera proxy aplikacji, zobacz [przykłady programu Azure AD PowerShell dla serwera proxy aplikacji usługi Azure AD](../application-proxy-powershell-samples.md).