---
title: Wbudowane zasady w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Temat na rozszerzalna struktura zasad usługi Azure Active Directory B2C i jak tworzyć różne typy zasad.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5c89f39b2f94309ea3d99230f5265d834c7093d9
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444826"
---
# <a name="azure-active-directory-b2c-built-in-policies"></a>Usługi Azure Active Directory B2C: Wbudowane zasady


Rozszerzalna struktura zasad usługi Azure Active Directory (Azure AD) B2C jest siły podstawowe usługi. Zasady pełni Opisuje funkcje tożsamości klientów takich jak rejestracji, logowania i edytowania profilu. Na przykład zasady rejestracji pozwala na kontrolowanie zachowania, konfigurując następujące ustawienia:

* Typy kont (kont społecznościowych, takich jak Facebook) lub kont lokalnych, takich jak adresy e-mail, które klienci mogą korzystać w celu uzyskania aplikacji
* Atrybuty (na przykład imię, kod pocztowy i rozmiar buta) mają być zbierane od użytkownika podczas rejestracji
* Korzystanie z usługi Azure Multi-Factor Authentication
* Wygląd i działanie wszystkich stron rejestracji
* Informacje (które manifesty jako oświadczenia w tokenie), aplikacja otrzyma po zasad uruchamiania zostanie zakończone

Można utworzyć wiele zasad o różnych typach w dzierżawie i używać ich w aplikacjach, zgodnie z potrzebami. Zasady mogą być ponownie używane w aplikacjach. Ta elastyczność umożliwia deweloperom definiowanie i modyfikowanie środowiska tożsamości konsumentów przy minimalnym lub jedynie minimalnych zmianach w ich kodu.

Zasady są dostępne do użycia przy użyciu interfejsu dewelopera proste. Wyzwala zasadę przy użyciu standardowych żądania uwierzytelniania HTTP (przekazywania parametru zasad w żądaniu) i odbiera token dostosowanych jako odpowiedzi aplikacji. Na przykład jedyną różnicą między żądań, które wywołują zasady rejestracji i żądań, które wywołują zasad logowania jest nazwa zasad, który jest używany w "p" parametru ciągu zapytania:

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

## <a name="create-a-sign-up-or-sign-in-policy"></a>Tworzenie zasad rejestracji lub logowania

Ta zasada obsługuje zarówno konsumenta rejestracji i logowania środowiska za pomocą jednej konfiguracji. Odbiorcy są doprowadziło do prawego ścieżki (rejestracji lub logowania), w zależności od kontekstu. Omówiono także zawartość tokenów, które aplikacja otrzyma po pomyślnej rejestracji lub logowania.  Przykład kodu służącego do **rejestracji lub logowania** zasady [dostępne tutaj](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  Zaleca się używać tych zasad za pośrednictwem **rejestracji** zasad lub **logowania** zasad.  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-policy"></a>Tworzenie zasad rejestracji

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-policy"></a>Utwórz zasady logowania

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-policy"></a>Tworzenie zasad edytowania profilów

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-policy"></a>Tworzenie zasad resetowania haseł

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="preview-policies"></a>Zasady (wersja zapoznawcza)

Jak wydaniu nowych funkcji, niektóre z nich mogą nie można na istniejące zasady.  Planujemy odłożyć starszych wersji za pomocą najnowszej wersji tego samego typu, gdy te zasady należy wprowadzić po ogólnym udostępnieniu produktu  Nie zmieni swoje istniejące zasady i aby można było korzystać z zalet nowych funkcji, należy utworzyć nowe zasady.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-do-i-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>Jak połączyć zasad rejestracji lub logowania za pomocą zasad resetowania haseł?
Podczas tworzenia **rejestracji lub logowania** zasad (przy użyciu kont lokalnych), zostanie wyświetlony **nie pamiętasz hasła?** łącze na pierwszej stronie środowiska. Kliknięcie tego linku nie automatycznie wyzwalacza hasła zasad resetowania. 

Zamiast tego kod błędu: **`AADB2C90118`** jest zwracana do aplikacji. Twoja aplikacja powinna obsługiwać tego kodu błędu, wywołując zasad resetowania haseł określonych. Aby uzyskać więcej informacji, zobacz [przykład demonstrujący podejście łączenie zasad](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

### <a name="should-i-use-a-sign-up-or-sign-in-policy-or-a-sign-up-policy-and-a-sign-in-policy"></a>Należy użyć zasad rejestracji lub logowania lub zasady tworzenia konta i zasady logowania?
Firma Microsoft zaleca użycie **rejestracji lub logowania** zasady przez **rejestracji** zasad i **logowania** zasad.  

**Rejestracji lub logowania** zasad ma więcej możliwości niż **logowania** zasad. Ponadto umożliwia dostosowywanie interfejsu użytkownika strony i ma lepsze wsparcie dla lokalizacji. 

**Logowania** Jeśli nie potrzebujesz zlokalizować zasad, tylko potrzebne możliwości dostosowywania pomocnicza znakowania i ma hasła, zalecane jest zasad resetowania wbudowanych.

## <a name="next-steps"></a>Kolejne kroki
* [Tokenów, sesji i konfiguracji rejestracji jednokrotnej](active-directory-b2c-token-session-sso.md)
* [Wyłączanie weryfikacji e-mail podczas rejestracji klienta](active-directory-b2c-reference-disable-ev.md)

