---
title: Usługa usługi Azure AD do usługi Auth przy użyciu usługi OAuth2.0 | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób używania wiadomości HTTP do implementowania usługi do uwierzytelniania usługi przy użyciu przepływu dotacji poświadczeń klienta OAuth2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: ryanwi
ms.reviewer: nacanuma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: f2d1eaec80c8925eb7b38af848e29e944f1ebf69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154546"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Usługa obsługi wywołań przy użyciu poświadczeń klienta (wspólny klucz tajny lub certyfikat)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

OAuth 2.0 Przypowiania klienta Grant Flow umożliwia usłudze sieci web *(poufne klienta)* użycie własnych poświadczeń zamiast personifikacji użytkownika, aby uwierzytelnić się podczas wywoływania innej usługi sieci web. W tym scenariuszu klient jest zazwyczaj usługą sieci web warstwy środkowej, usługą demona lub witryną sieci web. Aby uzyskać wyższy poziom pewności, usługa Azure AD umożliwia również usługi wywołującej używać certyfikatu (zamiast udostępnionego klucza tajnego) jako poświadczenia.

## <a name="client-credentials-grant-flow-diagram"></a>Diagram przepływu dotacji poświadczeń klienta
Na poniższym diagramie wyjaśniono, jak działa przepływ poświadczeń klienta w usłudze Azure Active Directory (Azure AD).

![Przepływ dotacji poświadczeń poświadczeń klienta OAuth2.0](./media/v1-oauth2-client-creds-grant-flow/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. Aplikacja kliencka uwierzytelnia się w punkcie końcowym wystawienia tokenu usługi Azure AD i żąda tokenu dostępu.
2. Punkt końcowy wystawiania tokenu usługi Azure AD wystawia token.
3. Token dostępu służy do uwierzytelniania w zasobie zabezpieczonym.
4. Dane z zabezpieczonego zasobu są zwracane do aplikacji klienckiej.

## <a name="register-the-services-in-azure-ad"></a>Rejestrowanie usług w usłudze Azure AD
Zarejestruj zarówno usługę wywołującą, jak i usługę odbierającą w usłudze Azure Active Directory (Azure AD). Aby uzyskać szczegółowe instrukcje, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="request-an-access-token"></a>Żądanie tokenu dostępu
Aby zażądać tokenu dostępu, należy użyć wpisu HTTP do punktu końcowego usługi Azure AD specyficzne dla dzierżawy.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Żądanie tokenu dostępu usługi do usługi
Istnieją dwa przypadki w zależności od tego, czy aplikacja kliencka zdecyduje się być zabezpieczona przez wspólny klucz tajny, czy certyfikat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Pierwszy przypadek: żądanie tokenu dostępu z udostępnionym kluczem tajnym
Podczas korzystania z udostępnionego klucza tajnego żądanie tokenu dostępu usługi do usługi zawiera następujące parametry:

| Parametr |  | Opis |
| --- | --- | --- |
| grant_type |wymagany |Określa żądany typ dotacji. W przepływie grantu poświadczeń klienta wartość musi być **client_credentials**. |
| client_id |wymagany |Określa identyfikator klienta usługi Azure AD wywoływanej usługi sieci web. Aby znaleźć identyfikator klienta aplikacji wywołującej, w [witrynie Azure portal](https://portal.azure.com)kliknij pozycję **Usługa Azure Active Directory**, kliknij pozycję **Rejestracje aplikacji**, kliknij aplikację. client_id jest *identyfikatorem aplikacji* |
| client_secret |wymagany |Wprowadź klucz zarejestrowany dla wywołującej usługi sieci web lub aplikacji demona w usłudze Azure AD. Aby utworzyć klucz, w witrynie Azure Portal kliknij pozycję **Azure Active Directory**, kliknij pozycję **Rejestracje aplikacji**, kliknij aplikację, kliknij pozycję **Ustawienia**, kliknij pozycję **Klucze**i dodaj klucz.  Kodowanie adresu URL tego klucza tajnego podczas jego dostarczania. |
| zasób |wymagany |Wprowadź identyfikator URI identyfikatora aplikacji odbierającej usługi sieci web. Aby znaleźć identyfikator URI identyfikatora aplikacji, w witrynie Azure portal kliknij pozycję **Azure Active Directory**, kliknij pozycję **Rejestracje aplikacji**, kliknij aplikację usługi, a następnie kliknij pozycję **Ustawienia** i **właściwości**. |

#### <a name="example"></a>Przykład
Następujący protokół HTTP POST żąda `https://service.contoso.com/` [tokenu dostępu](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) dla usługi sieci web. Identyfikuje `client_id` usługę sieci web, która żąda tokenu dostępu.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Drugi przypadek: żądanie tokenu dostępu z certyfikatem
Żądanie tokenu dostępu usługi do usługi z certyfikatem zawiera następujące parametry:

| Parametr |  | Opis |
| --- | --- | --- |
| grant_type |wymagany |Określa żądany typ odpowiedzi. W przepływie grantu poświadczeń klienta wartość musi być **client_credentials**. |
| client_id |wymagany |Określa identyfikator klienta usługi Azure AD wywoływanej usługi sieci web. Aby znaleźć identyfikator klienta aplikacji wywołującej, w [witrynie Azure portal](https://portal.azure.com)kliknij pozycję **Usługa Azure Active Directory**, kliknij pozycję **Rejestracje aplikacji**, kliknij aplikację. client_id jest *identyfikatorem aplikacji* |
| client_assertion_type |wymagany |Wartość musi być`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |wymagany | Twierdzenie (token sieci Web JSON), które należy utworzyć i podpisać za pomocą certyfikatu zarejestrowanego jako poświadczenia dla aplikacji. Przeczytaj o [poświadczeniach certyfikatu,](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) aby dowiedzieć się, jak zarejestrować certyfikat i format potwierdzenia.|
| zasób | wymagany |Wprowadź identyfikator URI identyfikatora aplikacji odbierającej usługi sieci web. Aby znaleźć identyfikator URI identyfikatora aplikacji, w witrynie Azure portal kliknij pozycję **Azure Active Directory**, kliknij pozycję **Rejestracje aplikacji**, kliknij aplikację usługi, a następnie kliknij pozycję **Ustawienia** i **właściwości**. |

Należy zauważyć, że parametry są prawie takie same jak w przypadku żądania przez klucz udostępniony klucz tajny, z tą różnicą, że parametr client_secret jest zastępowany przez dwa parametry: client_assertion_type i client_assertion.

#### <a name="example"></a>Przykład
Następujący protokół HTTP POST żąda `https://service.contoso.com/` tokenu dostępu dla usługi sieci web z certyfikatem. Identyfikuje `client_id` usługę sieci web, która żąda tokenu dostępu.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Odpowiedź tokenu dostępu usługi do usługi

Odpowiedź na powodzenie zawiera odpowiedź JSON OAuth 2.0 z następującymi parametrami:

| Parametr | Opis |
| --- | --- |
| access_token |Żądany token dostępu. Wywołująca usługa sieci web może używać tego tokenu do uwierzytelniania w odbierającej usłudze sieci web. |
| token_type |Wskazuje wartość typu tokenu. Jedynym typem, który obsługuje usługę Azure AD jest **Bearer**. Aby uzyskać więcej informacji na temat tokenów okaziciela, zobacz [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |Jak długo token dostępu jest prawidłowy (w sekundach). |
| expires_on |Czas wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczba sekund od 1970-01-01T0:0:0Z UTC do czasu wygaśnięcia. Ta wartość jest używana do określenia okresu istnienia tokenów buforowanych. |
| not_before |Czas, z którego token dostępu staje się użyteczny. Data jest reprezentowana jako liczba sekund od 1970-01-01T0:0:0Z UTC do czasu ważności tokenu.|
| zasób |Identyfikator URI identyfikatora aplikacji odbierającej usługi sieci web. |

#### <a name="example-of-response"></a>Przykład odpowiedzi
W poniższym przykładzie pokazano odpowiedź na powodzenie żądania tokenu dostępu do usługi sieci web.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Zobacz też
* [OAuth 2.0 w usłudze Azure AD](v1-protocols-oauth-code.md)
* [Próbka w języku C# usługi do wywołania usługi z udostępnionym kluczem tajnym](https://github.com/Azure-Samples/active-directory-dotnet-daemon) i [Sample w języku C# usługi do wywołania usługi z certyfikatem](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
