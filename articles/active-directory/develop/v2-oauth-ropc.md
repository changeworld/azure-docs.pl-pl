---
title: Korzystanie z platformy tożsamości firmy Microsoft do logowania użytkowników przy użyciu poświadczeń hasła właściciela zasobu (ROPC) | Azure
description: Obsługa przepływów uwierzytelniania bez przeglądarki przy użyciu hasła właściciela zasobu.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d5324aba5202abb76f07d1eaf43fe214e690393
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193205"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-resource-owner-password-credential"></a>Microsoft Identity platform oraz poświadczenia hasła właściciela zasobu OAuth 2,0

Platforma tożsamości firmy Microsoft obsługuje [przyznawanie poświadczeń hasła właściciela zasobu (ROPC)](https://tools.ietf.org/html/rfc6749#section-4.3), co umożliwia aplikacji Logowanie użytkownika przez bezpośrednią obsługę hasła. Przepływ ROPC wymaga wysokiego stopnia zaufania i narażenia użytkownika. tego przepływu należy używać tylko wtedy, gdy inne, bardziej bezpieczne, nie można używać przepływów.

> [!IMPORTANT]
>
> * Punkt końcowy platformy tożsamości firmy Microsoft obsługuje tylko ROPC dla dzierżawców usługi Azure AD, a nie konta osobiste. Oznacza to, że należy użyć punktu końcowego określonego dla dzierżawy`https://login.microsoftonline.com/{TenantId_or_Name}`() `organizations` lub punktu końcowego.
> * Konta osobiste, które są zapraszane do dzierżawy usługi Azure AD, nie mogą używać ROPC.
> * Konta, które nie mają haseł, nie mogą się zalogować za poorednictwem ROPC. W tym scenariuszu zalecamy użycie w zamian innego przepływu dla aplikacji.
> * Jeśli użytkownicy muszą korzystać z uwierzytelniania wieloskładnikowego (MFA) do logowania się do aplikacji, zostaną one zablokowane.

## <a name="protocol-diagram"></a>Diagram protokołu

Na poniższym diagramie przedstawiono przepływ ROPC.

![Diagram przedstawiający przepływ poświadczeń hasła właściciela zasobu](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Żądanie autoryzacji

Przepływ ROPC jest pojedynczym żądaniem: wysyła identyfikator klienta i poświadczenia użytkownika do dostawcy tożsamości, a następnie odbiera tokeny zwracane przez program. Przed wykonaniem tych czynności klient musi zażądać adresu e-mail użytkownika (UPN) i hasła. Natychmiast po pomyślnym żądaniu klient powinien bezpiecznie zwolnić poświadczenia użytkownika z pamięci. Nigdy nie należy ich zapisywać.

> [!TIP]
> Spróbuj wykonać to żądanie w programie Poster!
> [![Spróbuj uruchomić to żądanie w programie Poster](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```
// Line breaks and spaces are for legibility only.  This is a public client, so no secret is required. 

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| Parametr | Warunek | Opis |
| --- | --- | --- |
| `tenant` | Wymagane | Dzierżawa katalogu, w której ma być zalogowany użytkownik. Może to być w formacie identyfikatora GUID lub przyjaznej nazwy. Ten parametr nie może być ustawiony `common` na `consumers`wartość lub, ale może być `organizations`ustawiony na. |
| `client_id` | Wymagane | Identyfikator aplikacji (klienta), którą strona [Rejestracje aplikacji Azure Portala](https://go.microsoft.com/fwlink/?linkid=2083908) została przypisana do aplikacji. | 
| `grant_type` | Wymagane | Musi być równa `password`. |
| `username` | Wymagane | Adres e-mail użytkownika. |
| `password` | Wymagane | Hasło użytkownika. |
| `scope` | Zalecane | Rozdzielana spacjami lista [zakresów](v2-permissions-and-consent.md)lub uprawnień wymaganych przez aplikację. W przepływie interaktywnym administrator lub użytkownik musi wyrazić zgodę na te zakresy przed czasem. |
| `client_secret`| Czasami wymagane | Jeśli aplikacja jest klientem publicznym, a następnie `client_secret` `client_assertion` nie można jej uwzględnić.  Jeśli aplikacja jest klientem poufnym, należy ją uwzględnić. | 
| `client_assertion` | Czasami wymagane | Inna forma `client_secret`, wygenerowana przy użyciu certyfikatu.  Aby uzyskać więcej informacji, zobacz [poświadczenia certyfikatu](active-directory-certificate-credentials.md) . | 

### <a name="successful-authentication-response"></a>Pomyślna odpowiedź uwierzytelniania

W poniższym przykładzie przedstawiono Pomyślne odpowiedzi tokenu:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parametr | Format | Opis |
| --------- | ------ | ----------- |
| `token_type` | String | Zawsze ustawiaj `Bearer`na. |
| `scope` | Ciągi rozdzielone spacją | Jeśli został zwrócony token dostępu, ten parametr wyświetla listę zakresów, dla których token dostępu jest prawidłowy. |
| `expires_in`| int | Liczba sekund, przez jaką jest ważny włączony token dostępu. |
| `access_token`| Ciąg nieprzezroczysty | Wystawiony dla żądanych [zakresów](v2-permissions-and-consent.md) . |
| `id_token` | JWT | Wystawiony, `scope` Jeśli oryginalny parametr `openid` zawiera zakres. |
| `refresh_token` | Ciąg nieprzezroczysty | Wystawiony, `scope` Jeśli zostanie `offline_access`uwzględniony oryginalny parametr. |

Możesz użyć tokenu odświeżania, aby uzyskać nowe tokeny dostępu i odświeżać tokeny przy użyciu tego samego przepływu opisanego w [dokumentacji przepływu kodu OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Odpowiedź na błąd

Jeśli użytkownik nie podał prawidłowej nazwy użytkownika lub hasła lub klient nie otrzyma żądanej zgody, uwierzytelnienie zakończy się niepowodzeniem.

| Błąd | Opis | Akcja klienta |
|------ | ----------- | -------------|
| `invalid_grant` | Uwierzytelnianie nie powiodło się | Poświadczenia były nieprawidłowe lub klient nie ma zgody na żądane zakresy. Jeśli zakresy nie zostaną przyznane, `consent_required` zostanie zwrócony błąd. W takim przypadku klient powinien wysłać użytkownika do interakcyjnego monitu przy użyciu widoku WebView lub przeglądarki. |
| `invalid_request` | Żądanie zostało nieprawidłowo skonstruowane | Typ grantu nie jest obsługiwany w `/common` przypadku `/consumers` kontekstów uwierzytelniania lub.  Użyj `/organizations` zamiast tego identyfikatora dzierżawy. |

## <a name="learn-more"></a>Dowiedz się więcej

* Wypróbuj usługę ROPC za pomocą przykładowej [aplikacji konsolowej](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Aby określić, czy należy używać punktu końcowego v 2.0, przeczytaj temat [ograniczenia dotyczące platformy tożsamości firmy Microsoft](active-directory-v2-limitations.md).
