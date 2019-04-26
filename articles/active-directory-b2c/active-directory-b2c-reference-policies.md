---
title: Przepływy użytkownika w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat rozszerzalna struktura zasad usługi Azure Active Directory B2C oraz do tworzenia różnych przepływów użytkownika.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 67db13c8a36977f2614ba7b0e263919bd0405bc7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316956"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Przepływy użytkownika w usłudze Azure Active Directory B2C

Rozszerzalna struktura zasad usługi Azure Active Directory (Azure AD) B2C jest siły podstawowe usługi. Zasady pełni Opisuje funkcje tożsamości takich jak rejestracji, logowania i edytowania profilu. Ułatwiające konfigurowanie typowych zadań tożsamości, w portalu usługi Azure AD B2C zawiera wstępnie zdefiniowane, można skonfigurować zasady o nazwie **przepływy użytkownika**. 

## <a name="what-are-user-flows"></a>Co to są przepływy użytkownika?

Przepływ użytkownika umożliwia kontrolowanie zachowania w aplikacjach, konfigurując następujące ustawienia:

- Konto, które typy używane do logowania, takie jak kont społecznościowych, takich jak Facebook lub kont lokalnych
- Atrybutów zebranych od użytkownika, takie jak imię, kod pocztowy i uniesienia rozmiar
- Azure Multi-Factor Authentication
- Dostosowywanie interfejsu użytkownika
- Informacje, które aplikacja otrzyma jako oświadczenia w tokenie 

Można utworzyć wiele przepływów użytkownika o różnych typach w dzierżawie i używać ich w aplikacjach, zgodnie z potrzebami. Przepływy użytkownika mogą być ponownie używane w aplikacjach. Ta elastyczność umożliwia definiowanie i modyfikowanie środowiska tożsamości przy minimalnym lub jedynie minimalnych zmianach w kodzie. Aplikacja wyzwala przepływ użytkownika przy użyciu standardowych żądanie uwierzytelniania HTTP, które zawiera parametr przepływu użytkownika. Dostosowany [tokenu](active-directory-b2c-reference-tokens.md) została odebrana jako odpowiedzi. 

W poniższych przykładach pokazano parametr ciągu zapytania "p", który określa przepływ użytkownika, który ma być używany:

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow
```

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow
```

## <a name="user-flow-versions"></a>Wersje przepływu użytkownika

W witrynie Azure portal, nowy [wersje przepływy użytkownika](user-flow-versions.md) są dodawane przez cały czas. Gdy możesz rozpocząć pracę z usługą Azure AD B2C, należy przetestować użytkownika, którego przepływów są zalecane do użycia. Gdy tworzysz nowy przepływ użytkownika, wybierz przepływ użytkownika, które wymagają od **zalecane** kartę.

Obecnie zaleca się następujące przepływy użytkownika:

- **Zarejestruj się i zaloguj się** — obsługuje zarówno środowiska rejestracji i logowania za pomocą jednej konfiguracji. Użytkownicy są prowadzone w dół prawidłową ścieżkę, w zależności od kontekstu. Zaleca się używać tego przepływu użytkownika za pośrednictwem **rejestracji** przepływ użytkownika lub **logowania** przepływu użytkownika.
- **Edytowanie profilu** — umożliwia użytkownikom edytowanie ich informacji o profilu.
- **Resetowanie hasła** — umożliwia skonfigurowanie, czy i jak użytkownicy mogą zresetować swoje hasło.

## <a name="linking-user-flows"></a>Łączenie przepływy użytkownika

A **rejestracji lub logowania** obejmuje przepływ użytkownika z kont lokalnych **nie pamiętasz hasła?** łącze na pierwszej stronie doświadczenia. Kliknięcie tego linku nie automatycznie wyzwalacza hasła resetuje przepływu użytkownika. 

Zamiast tego kod błędu: `AADB2C90118` jest zwracana do aplikacji. Aplikacja musi obsłużyć tego kodu błędu, uruchamiając przepływu określonego użytkownika, która służy do resetowania hasła. Aby zobaczyć przykład, Przyjrzyj się [prostych przykładowych ASP.NET](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) demonstrujące łączenie przepływy użytkownika.

## <a name="email-address-storage"></a>Magazyn adres e-mail

Adres e-mail może być wymagany jako element przepływu użytkownika. Jeśli użytkownik jest uwierzytelniany przy użyciu dostawcy tożsamości społecznościowych, adres e-mail jest przechowywany w **otherMails** właściwości. Jeśli konto lokalne jest oparty na nazwę użytkownika, adres e-mail są przechowywane w właściwości szczegółu silnego uwierzytelniania. Jeśli konto lokalne jest oparty na adres e-mail, a następnie adres e-mail jest przechowywany w **signInNames** właściwości.
 
Adres e-mail nie jest gwarantowane do weryfikacji w dowolnej z tych przypadków. Administrator dzierżawy może wyłączyć weryfikację poczty e-mail w podstawowe zasady dla kont lokalnych. Nawet jeśli jest włączona Weryfikacja adresu e-mail, nie są zweryfikować adresy pochodzą z dostawcy tożsamości społecznościowych, jeśli ich nie zostały zmienione.
 
Tylko **otherMails** i **signInNames** właściwości są udostępniane za pośrednictwem Active Directory interfejsu API programu Graph. Adres e-mail w właściwości szczegółu silnego uwierzytelniania nie jest dostępna.

## <a name="next-steps"></a>Kolejne kroki

Aby utworzyć przepływy użytkownika zalecane, postępuj zgodnie z instrukcjami [samouczka: Utwórz przepływ użytkownika](tutorial-create-user-flows.md).


