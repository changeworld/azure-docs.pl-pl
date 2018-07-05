---
title: Korzystanie z usługi b2clogin.com | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat korzystania z usługi b2clogin.com zamiast login.microsoftonline.com.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1d42d9a97244eeff501b9d02b0f143d6ef0c91b2
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440634"
---
# <a name="using-b2clogincom"></a>Korzystanie z usługi b2clogin.com

>[!IMPORTANT]
>Ta funkcja jest w publicznej wersji zapoznawczej 
>

Masz teraz możliwość korzystania z usługi Azure AD B2C z `<YourTenantName>.b2clogin.com` zamiast `login.microsoftonline.com`.  To ma wiele zalet:
* Nie możesz udostępniać ten sam limit rozmiaru nagłówka pliku cookie z innymi produktami firmy Microsoft.
* Możesz usunąć wszystkie odwołania do firmy Microsoft w adresie URL (można zastąpić `<YourTenantName>.onmicrosoft.com` identyfikatorem dzierżawy). Na przykład: `https://<tenantname>.b2clogin.com/tfp/<tenantname>/<policyname>/v2.0/.well-known/openid-configuration`.

 Aby móc korzystać z usługi b2clogin.com, musisz ustawić niektóre z następujących czynności:

1. Dla Twojej **Uruchom punkt końcowy do natychmiastowego** upewnij się, że używasz `<YourTenantName>.b2clogin.com` zamiast `login.microsoftonline.com`.
2. Jeśli używasz biblioteki MSAL, musisz ustawić `validateauthority=false`.  Jest to spowodowane staje się wystawcą tokenu`<YourTenantName>.b2clogin.com`.