---
title: Definicje plików cookie
titleSuffix: Azure AD B2C
description: Zawiera definicje plików cookie używanych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b984b75b3a12606aa0d82c7e7b399d5dce59df33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189518"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Definicje plików cookie dla usługi Azure AD B2C

Poniższe sekcje zawierają informacje o plikach cookie używanych w usłudze Azure Active Directory B2C (Azure AD B2C).

## <a name="samesite"></a>SameWitynie

Usługa Microsoft Azure AD B2C jest zgodna z konfiguracjami `SameSite=None` przeglądarki `Secure` SameSite, w tym z obsługą atrybutu.

Aby zabezpieczyć dostęp do witryn, przeglądarki internetowe wprowadzą nowy bezpieczny domyślny model, który zakłada, że wszystkie pliki cookie powinny być chronione przed dostępem zewnętrznym, chyba że określono inaczej. Przeglądarka Chrome jest pierwszą, która wprowadziła tę zmianę, począwszy od [Chrome 80 w lutym 2020](https://www.chromium.org/updates/same-site). Aby uzyskać więcej informacji na temat przygotowania do zmiany w Chrome, zobacz [Deweloperzy: Przygotuj się na nową te samewity=Brak; Bezpieczne ustawienia plików cookie](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html) na blogu Chromium.

Deweloperzy muszą użyć nowego `SameSite=None`ustawienia plików cookie, aby wyznaczyć pliki cookie do dostępu między witrynami. Gdy `SameSite=None` atrybut jest obecny, `Secure` dodatkowy atrybut musi być używany, aby pliki cookie między witrynami były dostępne tylko za pośrednictwem połączeń HTTPS. Sprawdzanie poprawności i testowanie wszystkich aplikacji, w tym tych aplikacji korzystających z usługi Azure AD B2C.

Aby uzyskać więcej informacji, zobacz:

* [Obsługa zmian plików cookie SameSite w przeglądarce Chrome](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [Wpływ na witryny klientów oraz usługi i produkty firmy Microsoft w wersji Chrome w wersji 80 lub nowszej](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>Pliki cookie

W poniższej tabeli wymieniono pliki cookie używane w usłudze Azure AD B2C.

| Nazwa | Domain | Wygaśnięcie | Przeznaczenie |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | Koniec [sesji przeglądarki](session-behavior.md) | Przechowuje dane członkostwa użytkowników w dzierżawach. Dzierżawcy użytkownik jest członkiem i poziom członkostwa (Administrator lub Użytkownik). |
| `x-ms-cpim-slice` | b2clogin.com, login.microsoftonline.com, domena markowa | Koniec [sesji przeglądarki](session-behavior.md) | Służy do kierowania żądań do odpowiedniego wystąpienia produkcji. |
| `x-ms-cpim-trans` | b2clogin.com, login.microsoftonline.com, domena markowa | Koniec [sesji przeglądarki](session-behavior.md) | Służy do śledzenia transakcji (liczba żądań uwierzytelniania do usługi Azure AD B2C) i bieżącej transakcji. |
| `x-ms-cpim-sso:{Id}` | b2clogin.com, login.microsoftonline.com, domena markowa | Koniec [sesji przeglądarki](session-behavior.md) | Służy do obsługi sesji SSO. |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com, login.microsoftonline.com, domena markowa | Koniec [sesji przeglądarki](session-behavior.md), pomyślne uwierzytelnianie | Służy do utrzymywania stanu żądania. |
| `x-ms-cpim-csrf` | b2clogin.com, login.microsoftonline.com, domena markowa | Koniec [sesji przeglądarki](session-behavior.md) | Token fałszerski żądania międzygłowieniem używany do ochrony CRSF. |
| `x-ms-cpim-dc` | b2clogin.com, login.microsoftonline.com, domena markowa | Koniec [sesji przeglądarki](session-behavior.md) | Używany do routingu sieciowego usługi Azure AD B2C. |
| `x-ms-cpim-ctx` | b2clogin.com, login.microsoftonline.com, domena markowa | Koniec [sesji przeglądarki](session-behavior.md) | Kontekst |
| `x-ms-cpim-rp` | b2clogin.com, login.microsoftonline.com, domena markowa | Koniec [sesji przeglądarki](session-behavior.md) | Służy do przechowywania danych członkostwa dla dzierżawy dostawcy zasobów. |
| `x-ms-cpim-rc` | b2clogin.com, login.microsoftonline.com, domena markowa | Koniec [sesji przeglądarki](session-behavior.md) | Służy do przechowywania pliku cookie przekaźnika. |
