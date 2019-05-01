---
title: Plik cookie definicje — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Zawiera definicje dla plików cookie używane w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7864320b71416d1b06661b8ae96c6113962250dd
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703976"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Pliki cookie definicje dla usługi Azure Active Directory B2C

W poniższej tabeli wymieniono pliki cookie używane w usłudze Azure Active Directory B2C.

| Name (Nazwa) | Domain | Wygaśnięcie | Przeznaczenie |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-admin | main.b2cadmin.Ext.Azure.com | Zakończenie [sesji przeglądarki](active-directory-b2c-token-session-sso.md) | Przechowuje dane członkostwa użytkownika dla dzierżaw. Dzierżawcy, użytkownika jest członkiem programu i poziom członkostwa (administratora lub użytkownika). |
| x-ms-cpim-slice | Login.microsoftonline.com, z usługi b2clogin.com, domeny na marki | Zakończenie [sesji przeglądarki](active-directory-b2c-token-session-sso.md) | Używane do kierowania żądań trasy do wystąpienia produkcyjnego. |
| x-ms-cpim-trans | Login.microsoftonline.com, z usługi b2clogin.com, domeny na marki | Zakończenie [sesji przeglądarki](active-directory-b2c-token-session-sso.md) | Używane do śledzenia transakcji (liczba żądań uwierzytelniania usługi Azure AD B2C) i bieżącej transakcji. |
| x-ms-cpim-sso:{Id} | Login.microsoftonline.com, z usługi b2clogin.com, domeny na marki | Zakończenie [sesji przeglądarki](active-directory-b2c-token-session-sso.md) | Używane do obsługiwania sesji logowania jednokrotnego. |
| x-ms-cpim-cache:{id}_n | Login.microsoftonline.com, z usługi b2clogin.com, domeny na marki | Zakończenie [sesji przeglądarki](active-directory-b2c-token-session-sso.md), pomyślne uwierzytelnienie | Używane do obsługiwania stan żądania. |
| x-ms-cpim-csrf | Login.microsoftonline.com, z usługi b2clogin.com, domeny na marki | Zakończenie [sesji przeglądarki](active-directory-b2c-token-session-sso.md) | Token Cross-Site Request Forgery używane do ochrony CRSF. |
| x-ms-cpim-dc | Login.microsoftonline.com, z usługi b2clogin.com, domeny na marki | Zakończenie [sesji przeglądarki](active-directory-b2c-token-session-sso.md) | Używać usługi Azure AD B2C routingu w sieci. |
| x-ms-cpim-ctx | Login.microsoftonline.com, z usługi b2clogin.com, domeny na marki | Zakończenie [sesji przeglądarki](active-directory-b2c-token-session-sso.md) | Kontekst |
| x-ms-cpim-rp | Login.microsoftonline.com, z usługi b2clogin.com, domeny na marki | Zakończenie [sesji przeglądarki](active-directory-b2c-token-session-sso.md) | Używane do przechowywania danych członkostwa dla dzierżawy dostawcy zasobów. |
| x-ms-cpim-rc | Login.microsoftonline.com, z usługi b2clogin.com, domeny na marki | Zakończenie [sesji przeglądarki](active-directory-b2c-token-session-sso.md) | Używane do przechowywania plików cookie przekazywania. |

