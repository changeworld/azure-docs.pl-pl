---
title: Sesja i jednej konfiguracji logowania jednokrotnego — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Sesja i konfiguracji pojedynczego logowania jednokrotnego w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 674a20fc96cf5b86219222d746525a3559ae9d09
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681101"
---
# <a name="session-and-single-sign-on-configuration-in-azure-active-directory-b2c"></a>Sesja i konfiguracji pojedynczego logowania jednokrotnego w usłudze Azure Active Directory B2C

Ta funkcja umożliwia szczegółową kontrolę na [konkretnych użytkowników, przepływ](active-directory-b2c-reference-policies.md), programu:

- Okres istnienia sesji aplikacji sieci web zarządzanych przez usługę Azure AD B2C.
- Pojedynczy zachowanie logowania jednokrotnego (SSO) między wieloma aplikacje i przepływy użytkownika w dzierżawie usługi Azure AD B2C.

## <a name="session-behavior"></a>Działanie sesji

Usługa Azure AD B2C obsługuje [protokołu uwierzytelniania OpenID Connect](active-directory-b2c-reference-oidc.md) włączania bezpiecznego logowania do aplikacji sieci web. Do zarządzania sesjami aplikacji sieci web, można użyć następujących właściwości:

- **Aplikacja sieci Web okres istnienia sesji (w minutach)** — okres istnienia przechowywanych w przeglądarce użytkownika po pomyślnym uwierzytelnieniu pliku cookie sesji usługi Azure AD B2C.
    - Domyślne = 1440 minut.
    - Minimum (włącznie) = 15 minut.
    - Maksymalna (włącznie) = 1440 minut.
- **Limit czasu sesji aplikacji sieci Web** — w przypadku wybrania tej opcji **bezwzględne**, użytkownik jest zmuszony do uwierzytelniania ponownie po czasie określonym przez **aplikacji sieci Web okres istnienia sesji (w minutach)** upływa. Jeśli ten przełącznik jest równa **stopniowe** (ustawienie domyślne), użytkownik pozostaje zalogowany, tak długo, jak użytkownik jest stale aktywny w aplikacji sieci web.

Następujące przypadki użycia są włączone, przy użyciu tych właściwości:

- Spełnia wymagania dotyczące zabezpieczeń i zgodności w branży, ustawiając sesji aplikacji sieci web odpowiednią okresy istnienia.
- Wymuszanie uwierzytelniania ustalonym okresem podczas interakcji z części o wysokim poziomie zabezpieczeń aplikacji sieci web. 

Te ustawienia nie są dostępne w przypadku resetowania haseł przepływy użytkownika.

## <a name="single-sign-on-sso-configuration"></a>Konfiguracja logowania jednokrotnego (SSO)

Jeśli masz wiele aplikacji i przepływów użytkownika w dzierżawie B2C, możesz zarządzać interakcje użytkownika między nimi przy użyciu **konfiguracji rejestracji jednokrotnej** właściwości. Właściwość można ustawić na jeden z następujących ustawień:

- **Dzierżawy** — to ustawienie jest ustawieniem domyślnym. Przy użyciu tego ustawienia umożliwia wielu aplikacji i użytkownika przepływy w swojej dzierżawy usługi B2C do udostępniania tej samej sesji użytkownika. Na przykład po użytkownik zaloguje się do aplikacji, użytkownik może również bezproblemowo zalogować się do innego jeden, farmacji firmy Contoso, podczas uzyskiwania dostępu do jej.
- **Aplikacja** — to ustawienie pozwala zachować sesję użytkownika wyłącznie dla aplikacji, niezależnie od innych aplikacji. Na przykład jeśli chcesz to użytkownikowi logowanie się farmacji Contoso (przy użyciu tych samych poświadczeń), nawet wtedy, gdy użytkownik jest zalogowany w Contoso zakupów, inną aplikację na tym samym B2C dzierżawy. 
- **Zasady** — to ustawienie pozwala zachować sesję użytkownika wyłącznie na przepływ użytkownika, niezależnie od aplikacji korzystania z niego. Na przykład jeśli użytkownik ma już zalogowany i ukończyć krok multi factor authentication (MFA), użytkownik może mieć dostęp do lepsze zabezpieczenia części wielu aplikacji tak długo, jak sesji powiązane z przepływem użytkownika nie wygasa.
- **Wyłączone** — to ustawienie wymusza na użytkowniku, do uruchamiania za pośrednictwem przepływu całego użytkownika przy każdym wykonaniu zasad.

Te ustawienia nie są dostępne w przypadku resetowania haseł przepływy użytkownika. 

