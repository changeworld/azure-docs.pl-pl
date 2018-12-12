---
title: Przepływy użytkownika w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Temat na rozszerzalna struktura zasad usługi Azure Active Directory B2C i na temat tworzenia różnych typach przepływów użytkownika.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d4a93b04b8ad86a6a6d36a5bdaf3209b49e7a9dc
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877086"
---
# <a name="azure-active-directory-b2c-user-flows"></a>Usługa Azure Active Directory B2C: Przepływy użytkownika


Rozszerzalna struktura zasad usługi Azure Active Directory (Azure AD) B2C jest siły podstawowe usługi. Zasady pełni Opisuje funkcje tożsamości klientów takich jak rejestracji, logowania i edytowania profilu. Ułatwiające konfigurowanie typowych zadań tożsamości, w portalu usługi Azure AD B2C zawiera wstępnie zdefiniowane, można skonfigurować zasady o nazwie **przepływy użytkownika**. Na przykład przepływ rejestracji użytkownika pozwala na kontrolowanie zachowania, konfigurując następujące ustawienia:

* Typy kont (kont społecznościowych, takich jak Facebook) lub kont lokalnych, takich jak adresy e-mail, które klienci mogą korzystać w celu uzyskania aplikacji
* Atrybuty (na przykład imię, kod pocztowy i rozmiar buta) mają być zbierane od użytkownika podczas rejestracji
* Korzystanie z usługi Azure Multi-Factor Authentication
* Wygląd i działanie wszystkich stron rejestracji
* Informacje (które manifesty jako oświadczenia w tokenie), aplikacja otrzyma po przebiegu zakończeniu przepływu użytkownika

Można utworzyć wiele przepływów użytkownika w różnych typów w dzierżawie i używać ich w aplikacjach, zgodnie z potrzebami. Przepływy użytkownika mogą być ponownie używane w aplikacjach. Ta elastyczność umożliwia deweloperom definiowanie i modyfikowanie środowiska tożsamości konsumentów przy minimalnym lub jedynie minimalnych zmianach w ich kodu.

Przepływy użytkownika są dostępne za pośrednictwem interfejsu proste dla deweloperów. Wyzwala przepływ użytkownika przy użyciu standardowych żądania uwierzytelniania HTTP (przekazanie parametru przepływu użytkownika w żądaniu) i odbiera token dostosowanych jako odpowiedzi aplikacji. Na przykład jedyną różnicą między żądań, które wywołują przepływ tworzenia konta użytkownika i żądania, które wywołują przepływ logowania użytkownika jest przepływ użytkownika, który jest używany w "p" parametru ciągu zapytania:

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

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>Utwórz przepływ użytkownika rejestracji lub logowania

Ten przepływ użytkownika obsługuje zarówno konsumenta rejestracji i logowania środowiska za pomocą jednej konfiguracji. Odbiorcy są doprowadziło do prawego ścieżki (rejestracji lub logowania), w zależności od kontekstu. Omówiono także zawartość tokenów, które aplikacja otrzyma po pomyślnej rejestracji lub logowania.  Przykład kodu służącego do **rejestracji lub logowania** przepływ użytkownika to [dostępne tutaj](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  Zaleca się używać tego przepływu użytkownika za pośrednictwem **rejestracji** przepływ użytkownika lub **logowania** przepływu użytkownika.  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-user-flow"></a>Utwórz przepływ użytkownika rejestracji

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-user-flow"></a>Utwórz przepływ logowania użytkownika

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-user-flow"></a>Tworzenie profilu edycji przepływu użytkownika

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-user-flow"></a>Utwórz przepływ użytkownika resetowania hasła

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="preview-user-flows"></a>Przepływy użytkownika (wersja zapoznawcza)

Jak wydaniu nowych funkcji, niektóre z nich mogą nie być w niedostępne w przypadku istniejących zasad lub przepływy użytkownika.  Planujemy odłożyć starszych wersji za pomocą najnowszej wersji tego samego typu, gdy te przepływy użytkownika wprowadź GA.  Nie zmieni swoje istniejące zasady lub przepływy użytkownika, i aby można było korzystać z zalet tych nowych funkcjach trzeba będzie tworzenie nowych przepływów użytkownika.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-do-i-link-a-sign-up-or-sign-in-user-flow-with-a-password-reset-user-flow"></a>Jak połączyć przepływ użytkownika rejestracji lub logowania za pomocą przepływu użytkownika resetowania hasła?
Po utworzeniu **rejestracji lub logowania** przepływ użytkownika (za pomocą kont lokalnych), zostanie wyświetlony **nie pamiętasz hasła?** łącze na pierwszej stronie środowiska. Kliknięcie tego linku nie automatycznie wyzwalacza hasła resetuje przepływu użytkownika. 

Zamiast tego kod błędu: **`AADB2C90118`** jest zwracana do aplikacji. Twoja aplikacja powinna obsługiwać tego kodu błędu, wywołując przepływ użytkownika resetowania hasła określone. Aby uzyskać więcej informacji, zobacz [przykład demonstrujący podejście łączenie przepływy użytkownika](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

### <a name="should-i-use-a-sign-up-or-sign-in-user-flow-or-a-sign-up-user-flow-and-a-sign-in-user-flow"></a>Należy użyć przepływu rejestracji lub logowania użytkownika lub przepływu rejestracji użytkownika i przepływ logowania użytkownika?
Firma Microsoft zaleca użycie **rejestracji lub logowania** przepływ użytkownika za pośrednictwem **rejestracji** przepływ użytkownika i **logowania** przepływu użytkownika.  

**Rejestracji lub logowania** przepływ użytkownika ma więcej możliwości niż **logowania** przepływu użytkownika. Ponadto umożliwia dostosowywanie interfejsu użytkownika strony i ma lepsze wsparcie dla lokalizacji. 

**Logowania** przepływ użytkownika zaleca się jeśli nie potrzebujesz zlokalizować swoje przepływy użytkownika, tylko potrzebne możliwości dostosowywania pomocnicza znakowania i ma hasło resetowania wbudowanych.

## <a name="next-steps"></a>Kolejne kroki
* [Tokenów, sesji i konfiguracji rejestracji jednokrotnej](active-directory-b2c-token-session-sso.md)
* [Wyłączanie weryfikacji e-mail podczas rejestracji klienta](active-directory-b2c-reference-disable-ev.md)

