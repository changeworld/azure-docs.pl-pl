---
title: Przepływy użytkowników w usłudze Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Dowiedz się więcej o rozszerzalnej ramach zasad usługi Azure Active Directory B2C i jak tworzyć różne przepływy użytkowników.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c11bc48742c398d2048a236c7d00af044971f845
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185614"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Przepływy użytkownika w usłudze Azure Active Directory B2C

Rozszerzalna struktura zasad usługi Azure Active Directory B2C (Azure AD B2C) jest podstawową siłą usługi. Zasady w pełni opisują środowisko tożsamości, takie jak rejestracja, logowanie się lub edytowanie profilu. Aby ułatwić konfigurowanie najczęstszych zadań tożsamości, portal usługi Azure AD B2C zawiera wstępnie zdefiniowane, konfigurowalne zasady o nazwie **przepływy użytkowników**.

## <a name="what-are-user-flows"></a>Co to są przepływy użytkowników?

Przepływ użytkownika umożliwia kontrolowanie zachowań w aplikacjach przez skonfigurowanie następujących ustawień:

- Typy kont używane do logowania, takie jak konta społecznościowe, takie jak Facebook lub konta lokalne
- Atrybuty, które mają być zbierane od konsumenta, takie jak imię, kod pocztowy i rozmiar buta
- Azure Multi-Factor Authentication
- Dostosowywanie interfejsu użytkownika
- Informacje, które aplikacja otrzymuje jako oświadczenia w tokenie

Można utworzyć wiele przepływów użytkowników różnych typów w dzierżawie i używać ich w aplikacjach w razie potrzeby. Przepływy użytkownika mogą być ponownie zażywane w aplikacjach. Ta elastyczność umożliwia definiowanie i modyfikowanie środowiska tożsamości przy minimalnych lub żadnych zmian w kodzie. Aplikacja wyzwala przepływ użytkownika przy użyciu standardowego żądania uwierzytelniania HTTP, który zawiera parametr przepływu użytkownika. Dostosowany [token](tokens-overview.md) jest odbierany jako odpowiedź.

Poniższe przykłady przedstawiają parametr ciągu zapytania "p", który określa przepływ użytkownika, który ma być używany:

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

W witrynie Azure portal nowe [wersje przepływów użytkowników](user-flow-versions.md) są dodawane przez cały czas. Po rozpoczęciu korzystania z usługi Azure AD B2C, przetestowane przepływy użytkowników są zalecane do użycia. Podczas tworzenia nowego przepływu użytkownika, należy wybrać przepływ użytkownika, który jest potrzebny z **zalecanej** karty.

Obecnie zalecane są następujące przepływy użytkownika:

- **Zarejestruj się i zaloguj** — obsługuje zarówno środowisko rejestracji, jak i logowania za pomocą jednej konfiguracji. Użytkownicy są prowadzone w dół właściwą ścieżkę w zależności od kontekstu. Zaleca się, aby używać tego przepływu użytkownika przez przepływ użytkownika **rejestracji** lub przepływ użytkownika **logowania.**
- **Edycja profilu** — umożliwia użytkownikom edytowanie informacji o profilu.
- **Resetowanie hasła** — umożliwia skonfigurowanie, czy i w jaki sposób użytkownicy mogą zresetować swoje hasło.

## <a name="linking-user-flows"></a>Łączenie przepływów użytkowników

Przepływ użytkownika **rejestracji lub logowania** za pomocą kont lokalnych zawiera łącze **Zapomniałeś hasła?** na pierwszej stronie środowiska. Kliknięcie tego łącza nie powoduje automatycznego wyzwalania przepływu użytkownika resetowania hasła.

Zamiast tego kod `AADB2C90118` błędu jest zwracany do aplikacji. Aplikacja musi obsługiwać ten kod błędu, uruchamiając przepływ określonego użytkownika, który resetuje hasło. Aby zobaczyć przykład, spójrz na [prosty ASP.NET przykład,](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) który pokazuje łączenie przepływów użytkownika.

## <a name="email-address-storage"></a>Przechowywanie adresów e-mail

Adres e-mail może być wymagany jako część przepływu użytkownika. Jeśli użytkownik uwierzytelnia się za pomocą dostawcy tożsamości społecznościowej, adres e-mail jest przechowywany w **otherMails** właściwości. Jeśli konto lokalne jest oparte na nazwie użytkownika, adres e-mail jest przechowywany we właściwości szczegółów silnego uwierzytelniania. Jeśli konto lokalne jest oparte na adresie e-mail, adres e-mail jest przechowywany we właściwości **signInNames.**

W żadnym z tych przypadków nie ma gwarancji, że adres e-mail zostanie zweryfikowany. Administrator dzierżawy może wyłączyć weryfikację poczty e-mail w podstawowych zasadach dla kont lokalnych. Nawet jeśli weryfikacja adresu e-mail jest włączona, adresy nie są weryfikowane, jeśli pochodzą od dostawcy tożsamości społecznościowych i nie zostały zmienione.

Tylko **inneumaile** i **signInNames** właściwości są widoczne za pośrednictwem interfejsu API programu Microsoft Graph. Adres e-mail we właściwości szczegółów silnego uwierzytelniania jest niedostępny.

## <a name="next-steps"></a>Następne kroki

Aby utworzyć zalecane przepływy użytkownika, postępuj zgodnie z instrukcjami w [samouczku: Tworzenie przepływu użytkownika](tutorial-create-user-flows.md).
