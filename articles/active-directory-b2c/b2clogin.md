---
title: Przy użyciu b2clogin.com| Dokumentacja firmy Microsoft
description: Informacje o używaniu b2clogin.com zamiast login.microsoftonline.com.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.openlocfilehash: d2737e995b91caa2dcc55027baa2b552e64af23e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
#<a name="using-b2clogincom"></a>Korzystanie z usługi b2clogin.com

>[!IMPORTANT]
>Ta funkcja jest publicznej wersji zapoznawczej 
>

Masz teraz możliwość użycia usługi Azure AD B2C o `<YourTenantName>.b2clogin.com` zamiast `login.microsoftonline.com`.  Ma to wiele korzyści:
* Nie jest już udostępni sam limit rozmiaru nagłówka pliku cookie z innymi produktami firmy Microsoft
* W adresie URL można usunąć wszystkie odwołania do firmy Microsoft (może zastąpić `<YourTenantName>.onmicrosoft.com` za pomocą Identyfikatora dzierżawcy)

 Aby można było skorzystać z b2clogin.com, musisz ustawić niektóre z następujących czynności:

1. Dla Twojego **Uruchom teraz punktu końcowego** upewnij się, że używasz `<YourTenantName>.b2clogin.com` zamiast `login.microsoftonline.com`.
2. Jeśli używasz MSAL, musisz ustawić `validateauthority=false`.  Jest to spowodowane wystawcy tokenów staje się`<YourTenantName>.b2clogin.com`.