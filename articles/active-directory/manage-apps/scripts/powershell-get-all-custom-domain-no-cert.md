---
title: Przykład programu PowerShell — aplikacje proxy aplikacji bez certyfikatu
description: Przykład programu PowerShell, który zawiera listę wszystkich aplikacji proxy aplikacji usługi Azure Active Directory (Azure AD), które używają domen niestandardowych, ale nie mają prawidłowego certyfikatu TLS/SSL przekazanego.
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
ms.openlocfilehash: 97dc46a34e1bb5bb85dcb771a9720a19d356e6f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481215"
---
# <a name="get-all-azure-ad-proxy-application-apps-published-with-no-certificate-uploaded"></a>Pobierz wszystkie aplikacje usługi Azure AD Proxy opublikowane bez przekazywania certyfikatu

W tym przykładzie skryptu programu PowerShell wymieniono wszystkie aplikacje proxy aplikacji usługi Azure Active Directory (Azure AD), które używają domen niestandardowych, ale nie mają prawidłowego certyfikatu TLS/SSL przekazanego.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ten przykład wymaga [modułu AzureAD V2 PowerShell for Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) lub [wersji preview modułu AzureAD V2 for Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-custom-domain-no-cert.ps1 "Get all Azure AD Proxy application apps published with no certificate uploaded")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

| Polecenie | Uwagi |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Pobiera jednostki usługi. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Pobiera aplikację usługi Azure AD. |
|[Get-AzureADApplicationProxyAplikacja](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Pobiera aplikację skonfigurowaną dla serwera proxy aplikacji w usłudze Azure AD. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu programu Azure AD PowerShell, zobacz [omówienie modułu programu Azure AD PowerShell.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)

Aby zapoznać się z innymi przykładami programu PowerShell dla serwera proxy aplikacji, zobacz [przykłady programu Azure AD PowerShell dla serwera proxy aplikacji usługi Azure AD](../application-proxy-powershell-samples.md).