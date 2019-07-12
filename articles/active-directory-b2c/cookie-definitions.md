---
title: Plik cookie definicje — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Zawiera definicje dla plików cookie używane w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: af3244a32e9d02a1ba5053da85547bf614053127
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67587410"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Pliki cookie definicje dla usługi Azure Active Directory B2C

W poniższej tabeli wymieniono pliki cookie używane w usłudze Azure Active Directory B2C.

| Name (Nazwa) | Domain | wygaśnięcie | Cel |
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

