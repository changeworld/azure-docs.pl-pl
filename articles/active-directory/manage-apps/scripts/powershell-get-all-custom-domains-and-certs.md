---
title: Przykład programu PowerShell — aplikacje proxy aplikacji przy użyciu domen niestandardowych
description: Przykład programu PowerShell zawierający listę wszystkich aplikacji proxy aplikacji usługi Azure Active Directory (Azure AD), które używają domen niestandardowych i informacji o certyfikatach.
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
ms.openlocfilehash: 9722178011c3c0411f2e0e888dd9ab5962b933b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482051"
---
# <a name="get-all-application-proxy-apps-using-custom-domains-and-certificate-information"></a>Pobierz wszystkie aplikacje proxy aplikacji przy użyciu domen niestandardowych i informacji o certyfikatach

W tym przykładzie skryptu programu PowerShell wymieniono wszystkie aplikacje proxy aplikacji usługi Azure Active Directory (Azure AD), które używają domen niestandardowych i zawiera listę informacji o certyfikatach skojarzonych z domenami niestandardowymi.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ten przykład wymaga [modułu AzureAD V2 PowerShell for Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) lub [wersji preview modułu AzureAD V2 for Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-custom-domains-and-certs.ps1 "Get all Application Proxy apps using custom domains and certificate information")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

| Polecenie | Uwagi |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Pobiera jednostki usługi. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Pobiera aplikację usługi Azure AD. |
|[Get-AzureADApplicationProxyAplikacja](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Pobiera aplikację skonfigurowaną dla serwera proxy aplikacji w usłudze Azure AD. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu programu Azure AD PowerShell, zobacz [omówienie modułu programu Azure AD PowerShell.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)

Aby zapoznać się z innymi przykładami programu PowerShell dla serwera proxy aplikacji, zobacz [przykłady programu Azure AD PowerShell dla serwera proxy aplikacji usługi Azure AD](../application-proxy-powershell-samples.md).