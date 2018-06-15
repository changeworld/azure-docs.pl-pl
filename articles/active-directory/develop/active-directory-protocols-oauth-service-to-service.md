---
title: Azure AD do usługi uwierzytelniania przy użyciu OAuth2.0 | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób użycia wiadomości HTTP do zaimplementowania usług uwierzytelniania za pomocą przepływ przyznania poświadczeń klienta OAuth2.0.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 1a4da1af61be4b632b51f5b8f921f6f48e925559
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158197"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Usługi w celu wywołania usługi przy użyciu poświadczeń klienta (wspólny klucz tajny lub certyfikatu)
OAuth 2.0 klienta poświadczenia Grant Flow pozwala usługi sieci web (*poufne klienta*) korzystać własne poświadczenia zamiast personifikacji użytkownika, do uwierzytelniania podczas wywoływania metody innej usługi sieci web. W tym scenariuszu klient jest zwykle usługi sieci web warstwy środkowej, usługa demona lub witryny sieci web. Wyższy poziom gwarancji usługi Azure AD umożliwia wywołanie usługi do korzystania z certyfikatu (zamiast wspólny klucz tajny) jako poświadczenie.

## <a name="client-credentials-grant-flow-diagram"></a>Diagram przepływu przyznania poświadczeń klienta
Poniższy diagram wyjaśniono, jak poświadczenia klienta umożliwiają działania przepływu w usłudze Azure Active Directory (Azure AD).

![Przepływ przyznania poświadczeń klienta OAuth2.0](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. Aplikacja kliencka uwierzytelnianie z punktem końcowym wystawiania tokenu usługi Azure AD i żądania tokenu dostępu.
2. Punkt końcowy wystawiania tokenu usługi Azure AD wystawia token dostępu.
3. Token dostępu jest używany do uwierzytelniania zabezpieczonych zasobów.
4. Dane z zabezpieczonych zasobów jest zwracana do aplikacji sieci web.

## <a name="register-the-services-in-azure-ad"></a>Zarejestruj usługi w usłudze Azure AD
Zarejestruj zarówno wywoływania usługi i odbieranie w usłudze Azure Active Directory (Azure AD). Aby uzyskać szczegółowe instrukcje, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](active-directory-integrating-applications.md).

## <a name="request-an-access-token"></a>Żądanie tokenu dostępu
Żądanie tokenu dostępu, użyj HTTP POST do specyficznego dla dzierżawy usługi Azure AD punktu końcowego.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Żądanie tokenu dostępu do usługi
Istnieją dwa przypadki, w zależności od tego, czy aplikacja kliencka zdecydował się były zabezpieczone przez Wspólny klucz tajny lub certyfikatu.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Najpierw przypadek: żądanie tokenu dostępu z wspólny klucz tajny
Korzystając z wspólny klucz tajny, żądania tokenu dostępu do usługi zawiera następujące parametry:

| Parametr |  | Opis |
| --- | --- | --- |
| Typ grant_type |wymagane |Określa typ żądanej przydziału. W przepływie przyznania poświadczeń klienta, wartość musi być **client_credentials**. |
| client_id |wymagane |Określa identyfikator klienta usługi Azure AD wywoływania usługi sieci web. Aby znaleźć identyfikator klienta aplikacji wywołującej, w [portalu Azure](https://portal.azure.com), kliknij przycisk **usługi Azure Active Directory**, kliknij przycisk **rejestracji aplikacji**, kliknij żądaną aplikację. Jest client_id *identyfikator aplikacji* |
| client_secret |wymagane |Wprowadź klucz, w zarejestrowany wywoływania aplikacji sieci web usługi lub demon w usłudze Azure AD. Aby utworzyć klucz, w portalu Azure, kliknij przycisk **usługi Azure Active Directory**, kliknij przycisk **rejestracji aplikacji**, kliknij aplikację, kliknij przycisk **ustawienia**, kliknij przycisk **kluczy** , i Dodaj klucz.|
| zasób |wymagane |Wprowadź identyfikator URI aplikacji na odbieranie usługi sieci web. Aby znaleźć identyfikator URI aplikacji w portalu Azure, kliknij przycisk **usługi Azure Active Directory**, kliknij przycisk **rejestracji aplikacji**, kliknij aplikacji usługi, a następnie kliknij przycisk **ustawienia** i  **Właściwości**. |

#### <a name="example"></a>Przykład
Token dostępu dla żądań następujące HTTP POST https://service.contoso.com/ usługi sieci web. `client_id` Identyfikuje żądania tokenu dostępu usługi sieci web.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>W drugim przypadku: żądanie tokenu dostępu przy użyciu certyfikatu
Żądanie tokenu dostępu do usługi przy użyciu certyfikatu zawiera następujące parametry:

| Parametr |  | Opis |
| --- | --- | --- |
| Typ grant_type |wymagane |Określa typ żądanych odpowiedzi. W przepływie przyznania poświadczeń klienta, wartość musi być **client_credentials**. |
| client_id |wymagane |Określa identyfikator klienta usługi Azure AD wywoływania usługi sieci web. Aby znaleźć identyfikator klienta aplikacji wywołującej, w [portalu Azure](https://portal.azure.com), kliknij przycisk **usługi Azure Active Directory**, kliknij przycisk **rejestracji aplikacji**, kliknij żądaną aplikację. Jest client_id *identyfikator aplikacji* |
| client_assertion_type |wymagane |Wartość musi być `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |wymagane | (JSON Web Token) potwierdzenia, że musisz utworzyć i podpisać przy użyciu certyfikatu został zarejestrowany jako poświadczeń dla aplikacji. Przeczytaj informacje o [certyfikatu poświadczeń](active-directory-certificate-credentials.md) informacje na temat rejestracji certyfikatu i format potwierdzenia.|
| zasób | wymagane |Wprowadź identyfikator URI aplikacji na odbieranie usługi sieci web. Aby znaleźć identyfikator URI aplikacji w portalu Azure, kliknij przycisk **usługi Azure Active Directory**, kliknij przycisk **rejestracji aplikacji**, kliknij aplikacji usługi, a następnie kliknij przycisk **ustawienia** i  **Właściwości**. |

Należy zauważyć, że parametry są prawie takie same jak w przypadku żądania przez Wspólny klucz tajny, z wyjątkiem tego, że parametr client_secret zostało zastąpione przez dwa parametry: client_assertion_type i client_assertion.

#### <a name="example"></a>Przykład
Token dostępu dla żądań następujące HTTP POST https://service.contoso.com/ sieci web usługi przy użyciu certyfikatu. `client_id` Identyfikuje żądania tokenu dostępu usługi sieci web.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Odpowiedzi tokenu dostępu do usługi

Odpowiedź sukcesu zawiera odpowiedź JSON OAuth 2.0 z następującymi parametrami:

| Parametr | Opis |
| --- | --- |
| access_token |Żądany dostęp token. Wywołanie usługi sieci web umożliwia ten token uwierzytelniania przyjęcia usługi sieci web. |
| token_type |Wskazuje wartość typ tokenu. Jedynym typem, który obsługuje usługę Azure AD jest **elementu nośnego**. Aby uzyskać więcej informacji dotyczących tokenów elementu nośnego, zobacz [OAuth 2.0 autoryzacji Framework: użycie tokenu elementu nośnego (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |Jak długo token dostępu jest nieprawidłowy (w sekundach). |
| expires_on |Czas wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczbę sekund z rokiem 1970-01-01T0:0:0Z UTC czasu wygaśnięcia. Ta wartość jest używana do określenia okres istnienia pamięci podręcznej tokenów. |
| not_before |Czas, w którym nadaje się tokenu dostępu. Data jest reprezentowana jako liczbę sekund z rokiem 1970-01-01T0:0:0Z UTC czasu ważności tokenu.|
| zasób |Identyfikator URI aplikacji odbierającej usługi sieci web. |

#### <a name="example-of-response"></a>Przykład odpowiedzi
W poniższym przykładzie przedstawiono Powodzenie odpowiedzi na żądanie tokenu dostępu do usługi sieci web.

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
* [OAuth 2.0 w usłudze Azure AD](active-directory-protocols-oauth-code.md)
* [Przykład w języku C# wywołania usług z wspólny klucz tajny](https://github.com/Azure-Samples/active-directory-dotnet-daemon) i [próbki w języku C# wywołania usług przy użyciu certyfikatu](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
