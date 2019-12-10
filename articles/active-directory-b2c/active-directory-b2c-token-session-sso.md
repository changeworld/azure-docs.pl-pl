---
title: Konfiguracja sesji i logowania jednokrotnego
titleSuffix: Azure AD B2C
description: Konfiguracja sesji i logowania jednokrotnego (SSO) w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 736dd1f0490c2c9c7c4f526df96dd5ace6a1f819
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950072"
---
# <a name="session-and-single-sign-on-configuration-in-azure-active-directory-b2c"></a>Konfiguracja sesji i logowania jednokrotnego w Azure Active Directory B2C

Ta funkcja zapewnia precyzyjną kontrolę na [podstawie przepływu poszczególnych użytkowników](active-directory-b2c-reference-policies.md):

- Okresy istnienia sesji aplikacji sieci Web zarządzanych przez Azure AD B2C.
- Zachowanie logowania jednokrotnego w wielu aplikacjach i przepływach użytkowników w dzierżawie Azure AD B2C.

## <a name="session-behavior"></a>Zachowanie sesji

Azure AD B2C obsługuje [protokół uwierzytelniania OpenID Connect Connect](active-directory-b2c-reference-oidc.md) do włączania bezpiecznego logowania do aplikacji sieci Web. Aby zarządzać sesjami aplikacji sieci Web, można użyć następujących właściwości:

- **Okres istnienia sesji aplikacji sieci Web (w minutach)** — okres istnienia pliku cookie sesji usługi Azure AD B2C's w przeglądarce użytkownika po pomyślnym uwierzytelnieniu.
    - Wartość domyślna = 1440 min.
    - Minimum (włącznie) = 15 minut.
    - Maksimum (włącznie) = 1440 min.
- **Limit czasu sesji aplikacji sieci Web** — Jeśli ten przełącznik jest ustawiony na wartość **bezwzględną**, użytkownik jest zmuszony do uwierzytelnienia ponownie po upływie czasu określonego przez okres **istnienia sesji aplikacji sieci Web (w minutach)** . Jeśli ten przełącznik jest ustawiony na wartość **roll** (ustawienie domyślne), użytkownik pozostanie zalogowany, o ile użytkownik będzie stale aktywny w aplikacji sieci Web.

Następujące przypadki użycia są włączone przy użyciu tych właściwości:

- Zapoznaj się z wymaganiami dotyczącymi zabezpieczeń i zgodności w branży, ustawiając odpowiednie okresy istnienia sesji aplikacji sieci Web.
- Wymuś uwierzytelnianie po upływie określonego czasu podczas interakcji użytkownika z częścią aplikacji sieci Web o wysokim poziomie zabezpieczeń.

Te ustawienia nie są dostępne na potrzeby przepływów użytkownika resetowania hasła.

## <a name="single-sign-on-sso-configuration"></a>Konfiguracja logowania jednokrotnego (SSO)

Jeśli masz wiele aplikacji i przepływów użytkowników w dzierżawie usługi B2C, możesz zarządzać interakcjami użytkowników w nich przy użyciu właściwości **Konfiguracja logowania** jednokrotnego. Właściwość można ustawić na jedno z następujących ustawień:

- **Dzierżawca** — to ustawienie jest domyślne. Użycie tego ustawienia umożliwia wielu aplikacjom i przepływom użytkowników w dzierżawie B2C współużytkowanie tej samej sesji użytkownika. Na przykład, gdy użytkownik loguje się do aplikacji, użytkownik może również bezproblemowo zalogować się do innego, farmaceuty firmy Contoso, po uzyskaniu do niego dostępu.
- **Aplikacja** — to ustawienie umożliwia obsługę sesji użytkownika wyłącznie dla aplikacji, niezależnie od innych aplikacji. Na przykład jeśli chcesz, aby użytkownik logował się do usługi contoso apteki (z tymi samymi poświadczeniami), nawet jeśli użytkownik jest już zalogowany do kupowania w firmie Contoso, inna aplikacja w tej samej dzierżawie B2C.
- **Zasady** — to ustawienie umożliwia obsługę sesji użytkownika wyłącznie dla przepływu użytkownika niezależnie od aplikacji, z których korzysta. Na przykład jeśli użytkownik jest już zalogowany i ukończył krok uwierzytelniania wieloskładnikowego (MFA), użytkownik może uzyskać dostęp do części o wyższym poziomie zabezpieczeń wielu aplikacji, o ile sesja związana z przepływem użytkownika nie wygasa.
- **Wyłączone** — to ustawienie wymusza uruchomienie użytkownika przez cały przepływ użytkownika przy każdym wykonaniu zasad.

Te ustawienia nie są dostępne na potrzeby przepływów użytkownika resetowania hasła.

