---
title: Usługami uwierzytelnianie usługi Azure AD za pomocą OAuth 2.0 | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak używać wiadomości HTTP do zaimplementowania uwierzytelniania usług przy użyciu przepływie przyznawania poświadczeń klienta OAuth 2.0.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74c79dbfb397a8e6d87de75b5468414f2b7adf2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60251721"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Wywołania Usługa do usługi przy użyciu poświadczeń klienta (wspólne hasło lub certyfikat)

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

OAuth 2.0 przepływ przyznawania poświadczeń klienta pozwala usługi sieci web (*poufne klienta*) na potrzeby własne poświadczenia, zamiast personifikacji użytkownika, uwierzytelniania podczas wywoływania innej usługi sieci web. W tym scenariuszu klient jest zazwyczaj usługą sieci web warstwy środkowej, usługą demona lub witryny sieci web. Wyższy poziom gwarancji usługa Azure AD umożliwia również wywoływania usługi do użycia certyfikatu (zamiast wspólny klucz tajny) jako poświadczenie.

## <a name="client-credentials-grant-flow-diagram"></a>Diagram przepływu przyznawanie poświadczeń klienta
Poniższy diagram wyjaśnia, jak poświadczenia klienta umożliwiają przepływ działa w usłudze Azure Active Directory (Azure AD).

![Przepływie przyznawania poświadczeń klienta OAuth 2.0](./media/v1-oauth2-client-creds-grant-flow/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. Aplikacja kliencka jest uwierzytelniany punktu końcowego wystawiania tokenu usługi Azure AD i żąda tokenu dostępu.
2. Punkt końcowy wystawiania tokenu usługi Azure AD wystawia token dostępu.
3. Token dostępu jest używany do uwierzytelniania zabezpieczonych zasobów.
4. Dane z zabezpieczono zasób jest zwracana do aplikacji klienckiej.

## <a name="register-the-services-in-azure-ad"></a>Rejestrowanie usługi w usłudze Azure AD
Zarejestruj zarówno wywoływania usługi i odbieranie w usłudze Azure Active Directory (Azure AD). Aby uzyskać szczegółowe instrukcje, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="request-an-access-token"></a>Żądanie tokenu dostępu
Aby żądania tokenu dostępu, należy użyć metody POST protokołu HTTP do konkretnej dzierżawy punktu końcowego usługi Azure AD.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Żądanie tokenu dostępu do usługi
Istnieją dwa przypadki, w zależności od tego, czy aplikacja kliencka zdecyduje się na chronione przez wspólne hasło lub certyfikat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Pierwszy przypadek: Żądanie tokenu dostępu za pomocą wspólny klucz tajny
Korzystając z wspólny klucz tajny, żądania tokenu dostępu do usługi zawiera następujące informacje:

| Parametr |  | Opis |
| --- | --- | --- |
| grant_type |wymagane |Określa typ żądany przydział. W przepływie przyznawania poświadczeń klienta, wartość musi być **client_credentials**. |
| client_id |wymagane |Określa identyfikator klienta usługi Azure AD, wywoływania usługi sieci web. Aby znaleźć identyfikator klienta aplikacji wywołującej, w [witryny Azure portal](https://portal.azure.com), kliknij przycisk **usługi Azure Active Directory**, kliknij przycisk **rejestracje aplikacji**, kliknij żądaną aplikację. Jest client_id *Identyfikatora aplikacji* |
| client_secret |wymagane |Wprowadź klucz zarejestrowany dla sieci web usługi lub demona aplikacja wywołująca w usłudze Azure AD. Aby utworzyć klucz w witrynie Azure portal, kliknij przycisk **usługi Azure Active Directory**, kliknij przycisk **rejestracje aplikacji**, kliknij aplikację, kliknij przycisk **ustawienia**, kliknij przycisk **kluczy** , i Dodaj klucz.  Adres URL zakodować w tym klucza tajnego, podczas jego tworzenia. |
| zasób |wymagane |Wprowadź identyfikator URI aplikacji na odbieranie usługi sieci web. Aby znaleźć identyfikator URI aplikacji w witrynie Azure portal, kliknij **usługi Azure Active Directory**, kliknij przycisk **rejestracje aplikacji**, kliknij aplikacji usługi, a następnie kliknij przycisk **ustawienia** i  **Właściwości**. |

#### <a name="example"></a>Przykład
Następujące żądania HTTP POST [token dostępu](access-tokens.md) dla https://service.contoso.com/ usługi sieci web. `client_id` Identyfikuje usługę sieci web, który żąda tokenu dostępu.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Drugi przypadek: Żądanie tokenu dostępu przy użyciu certyfikatu
Żądanie tokenu dostępu do usługi, za pomocą certyfikatu zawiera następujące parametry:

| Parametr |  | Opis |
| --- | --- | --- |
| grant_type |wymagane |Określa żądany typ odpowiedzi. W przepływie przyznawania poświadczeń klienta, wartość musi być **client_credentials**. |
| client_id |wymagane |Określa identyfikator klienta usługi Azure AD, wywoływania usługi sieci web. Aby znaleźć identyfikator klienta aplikacji wywołującej, w [witryny Azure portal](https://portal.azure.com), kliknij przycisk **usługi Azure Active Directory**, kliknij przycisk **rejestracje aplikacji**, kliknij żądaną aplikację. Jest client_id *Identyfikatora aplikacji* |
| client_assertion_type |wymagane |Wartość musi być `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |wymagane | Potwierdzenie (JSON Web Token), musisz utworzyć i podpisać za pomocą certyfikatu rejestracji w charakterze poświadczenia dla aplikacji. Przeczytaj o [certyfikatu poświadczeń](active-directory-certificate-credentials.md) informacje na temat rejestracji certyfikatu i format potwierdzenia.|
| zasób | wymagane |Wprowadź identyfikator URI aplikacji na odbieranie usługi sieci web. Aby znaleźć identyfikator URI aplikacji w witrynie Azure portal, kliknij **usługi Azure Active Directory**, kliknij przycisk **rejestracje aplikacji**, kliknij aplikacji usługi, a następnie kliknij przycisk **ustawienia** i  **Właściwości**. |

Należy zauważyć, że parametry są prawie takie same jak w przypadku żądania przez Wspólny klucz tajny, z tą różnicą, że parametr client_secret zostaje zastąpiona przez dwa parametry: client_assertion_type i client_assertion.

#### <a name="example"></a>Przykład
W następującym WPISIE HTTP żądania tokenu dostępu dla https://service.contoso.com/ Usługa sieci web z certyfikatem. `client_id` Identyfikuje usługę sieci web, który żąda tokenu dostępu.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Odpowiedzi tokenu dostępu do usługi

Odpowiedź sukcesu zawiera odpowiedź JSON OAuth 2.0, z następującymi parametrami:

| Parametr | Opis |
| --- | --- |
| access_token |Token żądanego dostępu. Wywoływania usługi sieci web można użyć tego tokenu do uwierzytelnienia w usłudze sieci web odbierania. |
| token_type |Wskazuje typ tokenu. Jedynym typem, który obsługuje usługi Azure AD jest **elementu nośnego**. Aby uzyskać więcej informacji na temat tokenów elementu nośnego, zobacz [OAuth 2.0 autoryzacji Framework: Użycie tokenu elementu nośnego (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |Jak długo token dostępu jest prawidłowy (w sekundach). |
| expires_on |Czas wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczbę sekund od 1970-01-01T0:0:0Z UTC do czasu wygaśnięcia. Ta wartość jest używana do określenia okres istnienia tokenów buforowanych. |
| not_before |Czas, z którego token dostępu nadaje się do wykorzystania. Data jest reprezentowana jako liczbę sekund od 1970-01-01T0:0:0Z UTC do czasu ważności tokenu.|
| zasób |Identyfikator URI Identyfikatora aplikacji odbierającej usługi sieci web. |

#### <a name="example-of-response"></a>Przykład odpowiedzi
Poniższy przykład przedstawia odpowiedź sukcesu na żądanie tokenu dostępu do usługi sieci web.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Zobacz także
* [Uwierzytelnianie OAuth 2.0 w usłudze Azure AD](v1-protocols-oauth-code.md)
* [Przykładowy w języku C# z wywołaniem usługa-Usługa z wspólny klucz tajny](https://github.com/Azure-Samples/active-directory-dotnet-daemon) i [przykładowy w języku C# z wywołaniem usługa-Usługa przy użyciu certyfikatu](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
