---
title: Uwierzytelnianie przy użyciu usługi Azure AD w chmurach krajowych
description: Więcej informacji na temat rejestracji oraz uwierzytelniania punktów końcowych aplikacji chmur krajowych.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: negoe
ms.reviewer: negoe,andret,saeeda
ms.custom: aaddev
ms.openlocfilehash: 866a86178d66b7b4af069d684e4eb56c12db47ca
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982005"
---
# <a name="national-clouds"></a>Chmury krajowe

Chmury krajowe (zwane również suwerenne chmury) są odizolowane fizycznie wystąpienia platformy Azure. Te regiony platformy Azure są przeznaczone do upewnij się, że wymagania dotyczące zgodności, niezależności i rezydencji danych są honorowane w granicach geograficznych.

W tym chmury globalnej usługi Azure Active Directory jest wdrażany w chmurach narodowych następujące:  

- Wersja platformy Azure dla administracji USA
- Azure (Niemcy)
- Azure w Chinach — 21Vianet

## <a name="app-registration-endpoints"></a>Punkty końcowe rejestracji aplikacji

Poniższa tabela zawiera listę podstawowych adresach URL używany do rejestrowania aplikacji w przypadku każdej chmury krajowe punktów końcowych usługi Azure Active Directory (Azure AD).

| Chmury krajowe | Usługi AD portalu punkt końcowy platformy Azure
| --- | --- |
| Usługa Azure AD dla instytucji rządowych USA |https://portal.azure.us
|Niemiecka wersja platformy Azure AD |https://portal.microsoftazure.de
|Usługi AD chińska wersja platformy Azure obsługiwane przez firmę 21Vianet |https://portal.azure.cn
|Usługa Azure AD (usługa globalne)|https://portal.azure.com

## <a name="azure-ad-authentication-endpoints"></a>Punkty końcowe uwierzytelniania usługi Azure AD

Poniższa tabela zawiera listę podstawowych adresów URL dla punktów końcowych usługi Azure Active Directory (Azure AD), używany do uzyskiwania tokenów do wywołania programu Microsoft Graph dla każdej chmury krajowe.

| Chmury krajowe | Punkt końcowy uwierzytelniania usługi Azure AD
| --- | --- |
| Usługa Azure AD dla instytucji rządowych USA |`https://login.microsoftonline.us`
|Niemiecka wersja platformy Azure AD| `https://login.microsoftonline.de`
|Usługi AD chińska wersja platformy Azure obsługiwane przez firmę 21Vianet | `https://login.chinacloudapi.cn`
|Usługa Azure AD (usługa globalne)|`https://login.microsoftonline.com`

Żądania do punktów końcowych autoryzacji lub tokenu usługi Azure AD można utworzyć przy użyciu odpowiednich specyficzne dla regionu podstawowego adresu URL. Na przykład w przypadku (Niemcy):

- Jest wspólnego punktu końcowego autoryzacji `https://login.microsoftonline.de/common/oauth2/authorize`
- Typowe punktu końcowego tokenu jest `https://login.microsoftonline.de/common/oauth2/token` 

Dla jednej dzierżawy aplikacji Zastąp rozpowszechnione w powyższych adresów URL z identyfikatorem dzierżawy lub nazwa, na przykład `https://login.microsoftonline.de/contoso.com`

>[!NOTE]
> [Usługi Azure AD v2.0 autoryzacji]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) i tokenu punkty końcowe są dostępne tylko dla usługi global service. Nie jest jeszcze obsługiwane w przypadku wdrożeń chmur krajowych.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [Azure dla instytucji rządowych](https://docs.microsoft.com/azure/azure-government/)
- Dowiedz się więcej o [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- Dowiedz się więcej o [Azure (Niemcy)](https://docs.microsoft.com/azure/germany/)
- Dowiedz się więcej o [podstawowe informacje o uwierzytelnianiu w usłudze Azure AD](authentication-scenarios.md)
- Dowiedz się więcej o [wdrożenie programu Microsoft Graph na platformie chmury krajowe](https://developer.microsoft.com/graph/docs/concepts/deployments)