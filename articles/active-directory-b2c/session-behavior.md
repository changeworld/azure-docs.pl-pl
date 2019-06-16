---
title: Konfigurowanie zachowania sesji — w przypadku usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Skonfiguruj zachowanie sesji w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3167a63be46e0d777fbec0c6a3301684fb7636bd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66512562"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Skonfiguruj zachowanie sesji w usłudze Azure Active Directory B2C

Ta funkcja umożliwia szczegółową kontrolę na [konkretnych użytkowników, przepływ](active-directory-b2c-reference-policies.md), programu:

- Okres istnienia sesji aplikacji sieci web zarządzanych przez usługę Azure AD B2C.
- Pojedynczy zachowanie logowania jednokrotnego (SSO) między wieloma aplikacje i przepływy użytkownika w dzierżawie usługi Azure AD B2C.

Te ustawienia nie są dostępne w przypadku resetowania haseł przepływy użytkownika.

Usługa Azure AD B2C obsługuje [protokołu uwierzytelniania OpenID Connect](active-directory-b2c-reference-oidc.md) włączania bezpiecznego logowania do aplikacji sieci web. Do zarządzania sesjami aplikacji sieci web, można użyć następujących właściwości:

## <a name="session-behavior-properties"></a>Właściwości zachowanie sesji

- **Aplikacja sieci Web okres istnienia sesji (w minutach)** — okres istnienia przechowywanych w przeglądarce użytkownika po pomyślnym uwierzytelnieniu pliku cookie sesji usługi Azure AD B2C.
    - Domyślne = 1440 minut.
    - Minimum (włącznie) = 15 minut.
    - Maksymalna (włącznie) = 1440 minut.
- **Limit czasu sesji aplikacji sieci Web** — w przypadku wybrania tej opcji **bezwzględne**, użytkownik jest zmuszony do uwierzytelniania ponownie po czasie określonym przez **aplikacji sieci Web okres istnienia sesji (w minutach)** upływa. Jeśli ten przełącznik jest równa **stopniowe** (ustawienie domyślne), użytkownik pozostaje zalogowany, tak długo, jak użytkownik jest stale aktywny w aplikacji sieci web.
- **Konfiguracja rejestracji jednokrotnej** w przypadku wielu aplikacji i przepływów użytkownika w dzierżawie B2C, możesz zarządzać interakcje użytkownika między nimi przy użyciu **konfiguracji rejestracji jednokrotnej** właściwości. Właściwość można ustawić na jeden z następujących ustawień:
    - **Dzierżawy** — to ustawienie jest ustawieniem domyślnym. Przy użyciu tego ustawienia umożliwia wielu aplikacji i użytkownika przepływy w swojej dzierżawy usługi B2C do udostępniania tej samej sesji użytkownika. Na przykład po użytkownik zaloguje się do aplikacji, użytkownik może również bezproblemowo zalogować się do innego jeden, farmacji firmy Contoso, podczas uzyskiwania dostępu do jej.
    - **Aplikacja** — to ustawienie pozwala zachować sesję użytkownika wyłącznie dla aplikacji, niezależnie od innych aplikacji. Na przykład jeśli chcesz to użytkownikowi logowanie się farmacji Contoso (przy użyciu tych samych poświadczeń), nawet wtedy, gdy użytkownik jest zalogowany w Contoso zakupów, inną aplikację na tym samym B2C dzierżawy. 
    - **Zasady** — to ustawienie pozwala zachować sesję użytkownika wyłącznie na przepływ użytkownika, niezależnie od aplikacji korzystania z niego. Na przykład jeśli użytkownik ma już zalogowany i ukończyć krok multi factor authentication (MFA), użytkownik może mieć dostęp do lepsze zabezpieczenia części wielu aplikacji tak długo, jak sesji powiązane z przepływem użytkownika nie wygasa.
    - **Wyłączone** — to ustawienie wymusza na użytkowniku, do uruchamiania za pośrednictwem przepływu całego użytkownika przy każdym wykonaniu zasad.

Następujące przypadki użycia są włączone, przy użyciu tych właściwości:

- Spełnia wymagania dotyczące zabezpieczeń i zgodności w branży, ustawiając sesji aplikacji sieci web odpowiednią okresy istnienia.
- Wymuszanie uwierzytelniania ustalonym okresem podczas interakcji z części o wysokim poziomie zabezpieczeń aplikacji sieci web. 

## <a name="configure-the-properties"></a>Skonfiguruj właściwości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera dzierżawy usługi Azure AD B2C.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **przepływy użytkownika (zasady)** .
5. Otwórz przepływ użytkownika, która została wcześniej utworzona. 
6. Wybierz **właściwości**.
7. Konfigurowanie **aplikacji sieci Web okres istnienia sesji (w minutach)** , **limit czasu sesji aplikacji sieci Web**, **konfiguracji rejestracji jednokrotnej**, i **wymaga tokenu Identyfikatora w żądaniach wylogowania**  zgodnie z potrzebami.

    ![Konfigurowanie zachowania sesji](./media/session-behavior/session-behavior.png)
    
8. Kliknij pozycję **Zapisz**.



