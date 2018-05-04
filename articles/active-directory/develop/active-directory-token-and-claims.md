---
title: Dowiedz się więcej o inny token i typy obsługiwane przez usługę Azure AD | Dokumentacja firmy Microsoft
description: Przewodnik dla opis i ocena oświadczenia w tokenach SAML 2.0 i tokenów sieci Web JSON (JWT), wystawione przez usługi Azure Active Directory (AAD)
documentationcenter: na
author: hpsin
services: active-directory
manager: mtillman
editor: ''
ms.assetid: 166aa18e-1746-4c5e-b382-68338af921e2
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/22/2018
ms.author: hirsin
ms.custom: aaddev
ms.openlocfilehash: 627b5bf39c066cd974b70f9db974fcf3fd73b251
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-ad-token-reference"></a>Odwołania do usługi Azure AD tokenu
Azure Active Directory (Azure AD) emituje kilka typów tokenów zabezpieczających do przetworzenia każdy przepływ uwierzytelniania. Ten dokument zawiera opis format właściwości zabezpieczeń i zawartości każdego typu tokenu. 

## <a name="types-of-tokens"></a>Typy tokenów
Azure AD obsługuje [protokół OAuth 2.0](active-directory-protocols-oauth-code.md), który wykorzystuje access_tokens i refresh_tokens.  Obsługuje ona również uwierzytelniania i logowania za pomocą [OpenID Connect](active-directory-protocols-openid-connect-code.md), który został wprowadzony trzeci typ tokenu, żądaniu.  Każdy z tych tokenów jest reprezentowany jako "bearer token".

Token elementu nośnego jest tokenem zabezpieczającym lekkie, która udziela dostępu "bearer" do chronionego zasobu. W tym sensie "bearer" jest każda strona, która może ona powodować tokenu. Uwierzytelnianie za pomocą usługi Azure AD jest wymagane w celu odbierania tokenu elementu nośnego, ale czynności należy zabezpieczyć token, aby zapobiec przechwyceniu przez stronę niezamierzone. Ponieważ nie ma wbudowany mechanizm uniemożliwić nieupoważnione ich użycie tokenów elementu nośnego, musi być transportowane w bezpiecznego kanału, takie jak zabezpieczeń warstwy transportu (HTTPS). Jeśli token elementu nośnego są przesyłane bez zabezpieczeń, man-in środkowej ataków można uzyskać tokenu i uzyskania nieautoryzowanego dostępu do chronionego zasobu. Te same zasady zabezpieczeń mają zastosowanie po zapisaniu lub buforowanie tokenów elementu nośnego do późniejszego użycia. Zawsze upewnij się, że aplikacja przesyła i przechowuje tokenów elementu nośnego w bezpieczny sposób. Aby uzyskać więcej zagadnienia dotyczące zabezpieczeń na tokenów elementu nośnego, zobacz [RFC 6750 sekcji 5](http://tools.ietf.org/html/rfc6750).

Wiele tokenów wystawiony przez usługę Azure AD są zaimplementowane jako tokenów sieci Web JSON lub tokenów Jwt.  Token JWT jest compact, adres URL bezpiecznego sposób przekazywania informacji między dwiema stronami.  Informacje zawarte w Jwt są nazywane "oświadczenia" lub twierdzeniom informacji o elementu nośnego i temat tokenu.  Oświadczenia w Jwt są obiektów JSON, kodowania i serializację do transmisji.  Ponieważ Jwt wystawione przez usługę Azure AD jest podpisany, ale nie są szyfrowane, możesz łatwo sprawdzić zawartość token JWT na potrzeby debugowania.  Istnieje kilka narzędzi w ten sposób, takich jak [jwt.ms](https://jwt.ms/). Aby uzyskać więcej informacji dotyczących tokenów Jwt, może się odwoływać do [specyfikacji JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_tokens
Id_tokens są w formie tokenów zabezpieczających logowania aplikacji otrzymuje podczas uwierzytelniania przy użyciu [OpenID Connect](active-directory-protocols-openid-connect-code.md).  Są one reprezentowane jako [Jwt](#types-of-tokens)i może zawierać oświadczenia, które służy do podpisywania użytkownika w aplikacji.  Zgodnie z własnymi potrzebami — zwykle są używane do wyświetlania informacji o koncie lub podejmowania decyzji dotyczących kontroli dostępu w aplikacji, można użyć oświadczeń w żądaniu.

Id_tokens jest podpisany, ale nie są szyfrowane w tej chwili.  Gdy aplikacja odbiera żądaniu, musi ona [weryfikuje podpisu](#validating-tokens) potwierdzenia autentyczności tokenu i sprawdzić poprawności kilku oświadczenia w tokenie potwierdzenie jego ważności.  Oświadczenia zweryfikowany przez aplikację się różnić w zależności od wymagań scenariusza, ale w przypadku niektórych [typowych operacji sprawdzania poprawności oświadczeń](#validating-tokens) który aplikacji należy wykonać w każdym scenariuszu.

Zobacz sekcję poniżej informacje na id_tokens oświadczeń, a także żądaniu próbki.  Należy pamiętać, że oświadczenia w id_tokens nie są zwracane w określonej kolejności.  Ponadto nowych oświadczeń może być wprowadzane do id_tokens w dowolnym momencie w czasie — aplikacji nie powinna zostać podzielona na miarę wprowadzania nowych oświadczeń.  Poniższa lista zawiera oświadczenia, które aplikacji można niezawodnie interpretacji w momencie pisania tego dokumentu.  Jeśli to konieczne, nawet więcej szczegółów można znaleźć w [OpenID Connect specyfikacji](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Przykładowe żądaniu
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [!TIP]
> Praktyki, spróbuj sprawdzania oświadczeń w żądaniu próbki przez wklejenie go do [jwt.ms](https://jwt.ms/).
> 
> 

#### <a name="claims-in-idtokens"></a>Oświadczenia w id_tokens
> [!div class="mx-codeBreakAll"]
| Token JWT oświadczeń | Name (Nazwa) | Opis |
| --- | --- | --- |
| `aud` |Grupy odbiorców |Zamierzonym odbiorcą tokenu. Aplikacja, która odbiera token należy sprawdzić, czy wartość odbiorców jest poprawny i odrzucić wszystkie tokeny przeznaczona dla różnych użytkowników. <br><br> **Przykładowa wartość SAML**: <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **Przykładowa wartość JWT**: <br> `"aud":"https://contoso.com"` |
| `appidacr` |Application Authentication Context Class Reference |Wskazuje, jak klient został uwierzytelniony. Dla publicznych klienta wartość wynosi 0. Jeśli identyfikator klienta i klucz tajny klienta są używane, wartość to 1. <br><br> **Przykładowa wartość JWT**: <br> `"appidacr": "0"` |
| `acr` |Authentication Context Class Reference |Wskazuje, jak został uwierzytelniony podmiot, w przeciwieństwie do klienta z oświadczeń Application Authentication Context Class Reference. Wartość "0" oznacza, że uwierzytelnianie użytkownika końcowego nie spełnia wymagań z normą ISO/IEC 29115. <br><br> **Przykładowa wartość JWT**: <br> `"acr": "0"` |
| Błyskawiczne uwierzytelniania |Rejestruje datę i godzinę po przeprowadzeniu uwierzytelnienia. <br><br> **Przykładowa wartość SAML**: <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` | |
| `amr` |Metoda uwierzytelniania |Określa, jak został uwierzytelniony podmiot tokenu. <br><br> **Przykładowa wartość SAML**: <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **Przykładowa wartość JWT**: `“amr”: ["pwd"]` |
| `given_name` |Imię |Udostępnia pierwszy lub "" Nazwa użytkownika, zgodnie z ustaleniami na obiekt użytkownika usługi Azure AD. <br><br> **Przykładowa wartość SAML**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **Przykładowa wartość JWT**: <br> `"given_name": "Frank"` |
| `groups` |Grupy |Zawiera identyfikatory obiektów, które reprezentują członkostwa w grupach podmiotu. Wartości te są unikatowe (patrz obiektu o identyfikatorze) i bezpiecznie umożliwia zarządzanie dostępem, takie jak wymuszanie autoryzacji dostępu do zasobu. Grup objętych oświadczenia grupy są konfigurowane na poszczególnych aplikacji, za pośrednictwem właściwości "groupMembershipClaims" manifest aplikacji. Wartość null powoduje wyłączenie wszystkich grup, wartość "SecurityGroup" będzie zawierać tylko członkostwa grupy zabezpieczeń usługi Active Directory, a wartość "All" będzie zawierać grup zabezpieczeń i listami dystrybucyjnymi usługi Office 365. <br><br> **Uwagi dotyczące**: <br> Zobacz `hasgroups` oświadczeń poniżej, aby uzyskać więcej informacji na temat używania `groups` oświadczenie z niejawne Przyznaj.  <br> Dla innych przepływów Jeśli liczba grup, do których użytkownik ma przekracza limit (150 dla SAML, 200 JWT), a następnie nadwyżkowe oświadczenia zostaną dodane źródła oświadczeń wskazującego punkt końcowy wykres zawierający listę grup dla użytkownika. (w. <br><br> **Przykładowa wartość SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **Przykładowa wartość JWT**: <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
|`hasgroups` | Token JWT przepływu niejawnego grup nadwyżkowe wskaźnika| Jeśli jest obecny, zawsze `true`, określające użytkownika znajduje się w co najmniej jedną grupę.  Użyta zamiast `groups` oświadczenie dla tokenów Jwt przepływów niejawne Przyznaj, jeśli pełna grup oświadczeń może rozszerzyć fragmentu identyfikatora URI poza granicami długość adresu URL (obecnie 6 lub więcej grup).  Wskazuje, że powinien używać klient do wykresu można określić grupy użytkowników (`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`). |
| `groups:src1` <br> `http://schemas.microsoft.com/claims/groups.link` | Wskaźnik nadwyżkowe grup | Token żądań, które nie są ograniczone długość (zobacz `hasgroups` powyżej), ale nadal za duży dla tokenu, łącze do listy grup pełny dla użytkownika będzie uwzględniony.  Dla tokenów Jwt rozproszonej oświadczenia, dla SAML jako nowe oświadczenie zamiast `groups` oświadczeń. <br><br> **Przykładowa wartość SAML**: <br> `<Attribute Name=” http://schemas.microsoft.com/claims/groups.link”>`<br>`<AttributeValue>https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects<AttributeValue>` <br><br> **Przykładowa wartość JWT**: <br> `"groups":"src1` <br> `_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }`|
| `idp` |Dostawca tożsamości |Rejestruje dostawcę tożsamości, który uwierzytelniony podmiot tokenu. Ta wartość jest taka sama jak wartość oświadczenia wystawcy, chyba, że konto użytkownika jest w innej dzierżawie niż wystawcy. <br><br> **Przykładowa wartość SAML**: <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **Przykładowa wartość JWT**: <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` |IssuedAt |Przechowuje czasu, jaką token został wystawiony. Często służy do pomiaru świeżości tokenu. <br><br> **Przykładowa wartość SAML**: <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **Przykładowa wartość JWT**: <br> `"iat": 1390234181` |
| `iss` |Wystawca |Identyfikuje usługę tokenu zabezpieczającego (STS), które tworzy i zwraca token. W tokenach, które zwraca usługi Azure AD wystawca jest sts.windows.net. Identyfikator GUID w wartości wystawcy oświadczeń jest identyfikator dzierżawcy katalogu usługi Azure AD. Identyfikator dzierżawy jest niezmienne i niezawodne identyfikator katalogu. <br><br> **Przykładowa wartość SAML**: <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **Przykładowa wartość JWT**: <br>  `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` |Nazwisko |Zgodnie z definicją w obiekcie użytkownika usługi Azure AD zapewnia ostatniego nazwy, nazwisko lub nazwisko użytkownika. <br><br> **Przykładowa wartość SAML**: <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **Przykładowa wartość JWT**: <br> `"family_name": "Miller"` |
| `unique_name` |Name (Nazwa) |Udostępnia człowieka wartość do odczytu, która identyfikuje podmiotu tokenu. Wartość ta nie musi być unikatowa w ramach dzierżawcy i jest przeznaczona do użycia tylko do wyświetlania. <br><br> **Przykładowa wartość SAML**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **Przykładowa wartość JWT**: <br> `"unique_name": "frankm@contoso.com"` |
| `oid` |Identyfikator obiektu |Zawiera unikatowy identyfikator obiektu w usłudze Azure AD. Ta wartość jest niezmienne i nie można ponownie przypisać lub ponownie. Identyfikator obiektu umożliwia zidentyfikowanie obiektu w zapytaniach do usługi Azure AD. <br><br> **Przykładowa wartość SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **Przykładowa wartość JWT**: <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` |Role |Reprezentuje wszystkie role aplikacji, które podmiot zostały przyznane bezpośrednio i pośrednio przez przynależność do grupy i umożliwia wymuszanie kontroli dostępu opartej na rolach. Role aplikacji są zdefiniowane na poszczególnych aplikacji, co za pośrednictwem `appRoles` właściwości manifest aplikacji. `value` Właściwości każdej roli aplikacji jest wartością wyświetlaną w oświadczeń ról. <br><br> **Przykładowa wartość SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **Przykładowa wartość JWT**: <br> `“roles”: ["Admin", … ]` |
| `scp` |Zakres |Wskazuje personifikacji uprawnienia do aplikacji klienckiej. Domyślne uprawnienia `user_impersonation`. Właściciel zasobu zabezpieczonych rejestrować dodatkowych wartości w usłudze Azure AD. <br><br> **Przykładowa wartość JWT**: <br> `"scp": "user_impersonation"` |
| `sub` |Temat |Identyfikuje podmiot zabezpieczeń, o której token potwierdzeń informacje, takie jak użytkownik aplikacji. Ta wartość jest niezmienne i nie może zostać przypisany lub używane ponownie, więc może służyć do wykonywania sprawdzeń autoryzacji bezpiecznie. Ponieważ podmiot zawsze jest obecny w tokenach problemy z usługą Azure AD, zaleca się korzystanie z tej wartości w systemie autoryzacji ogólnego przeznaczenia. <br> `SubjectConfirmation` nie jest oświadczenie. Opisuje sposób przedmiotem token jest weryfikowany. `Bearer` Wskazuje, czy podmiot potwierdza zapoznały tokenu. <br><br> **Przykładowa wartość SAML**: <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **Przykładowa wartość JWT**: <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"` |
| `tid` |Identyfikator dzierżawy |Identyfikator niezmienne, jednorazowego, który identyfikuje dzierżawy katalogu, który wystawił token. Ta wartość umożliwia dostęp do zasobów dzierżawy katalog w aplikacji wielodostępnej. Na przykład tej wartości można użyć, aby zidentyfikować dzierżawy w wywołaniu interfejsu API programu Graph. <br><br> **Przykładowa wartość SAML**: <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **Przykładowa wartość JWT**: <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"` |
| `nbf`, `exp` |Okres istnienia tokenu |Określa przedział czasu, w którym token jest prawidłowy. Usługa, która weryfikuje token powinien Sprawdź bieżącą datę w okres istnienia tokenu, else powinien odrzucenie tokenu. Usługi mogą zezwalać na maksymalnie pięć minut poza zakresem okres istnienia tokenu na dowolnym różnice w zegarze ("czas zegara") między usługami Azure AD i usługi. <br><br> **Przykładowa wartość SAML**: <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **Przykładowa wartość JWT**: <br> `"nbf":1363289634, "exp":1363293234` |
| `upn` |Nazwa główna użytkownika |Przechowuje nazwę głównej nazwy użytkownika.<br><br> **Przykładowa wartość JWT**: <br> `"upn": frankm@contoso.com` |
| `ver` |Wersja |Przechowuje numer wersji tokenu. <br><br> **Przykładowa wartość JWT**: <br> `"ver": "1.0"` |

## <a name="access-tokens"></a>Tokeny dostępu
Po pomyślnym uwierzytelnieniu usługi Azure AD zwraca token dostępu, który może służyć do dostępu do chronionych zasobów. Token dostępu jest base 64 zakodowane tokenu Web JSON (JWT) i jego zawartość można sprawdzić, uruchamiając go przy użyciu dekodera.

Jeśli aplikacja tylko *używa* tokenów dostępu, aby uzyskać dostęp do interfejsów API, można i powinien traktują tokenów dostępu jako całkowicie nieprzezroczyste — są tylko ciągi, które aplikacji można przekazać do zasobów w żądaniach HTTP.

W przypadku żądania tokenu dostępu, usługi Azure AD zwraca także niektóre metadane dotyczące token dostępu do aplikacji przez.  Informacje te obejmują czas wygaśnięcia tokenu dostępu i zakresów, dla których jest prawidłowy.  Dzięki temu aplikację, aby wykonać inteligentne buforowanie tokenów dostępu bez konieczności przeanalizować otwórz sam token dostępu.

Jeśli aplikacja jest chroniony za pomocą usługi Azure AD, która oczekuje tokenów dostępu w żądaniach HTTP interfejsu API, następnie należy wykonać sprawdzanie poprawności i kontroli tokenów, który pojawi się. Aplikację należy wykonać sprawdzanie poprawności tokenu dostępu przed użyciem jej do dostępu do zasobów. Aby uzyskać więcej informacji dotyczących sprawdzania poprawności, zobacz [sprawdzania poprawności tokenów](#validating-tokens).  
Aby uzyskać więcej informacji na temat w tym .NET, zobacz [chronić interfejsu API sieci Web za pomocą tokenów elementu nośnego z usługi Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

## <a name="refresh-tokens"></a>Tokenów odświeżania

Odśwież tokeny to tokeny zabezpieczające, których może używać aplikacja do uzyskania nowe tokeny dostępu przepływu OAuth 2.0.  Umożliwia aplikacji w celu osiągnięcia długoterminowej dostęp do zasobów w imieniu użytkownika bez konieczności interakcji przez użytkownika.

Tokeny odświeżania są wielu zasobów.  To znaczy czy token odświeżania otrzymał podczas żądania tokenu dla jednego zasobu można wykorzystać tokenów dostępu na zupełnie innego zasobu. Aby to zrobić, ustaw `resource` parametru w żądaniu do zasobu docelowego.

Tokeny odświeżania są całkowicie nieprzezroczysta dla aplikacji. Są one długotrwałe, ale aplikacji nie powinna być zapisana można oczekiwać, że token odświeżania będą trwać przez dowolnego okresu.  Tokeny odświeżania może być nieważne w dowolnej chwili z różnych przyczyn — zobacz [tokenu odwołania](#token-revocation) tych powodów.  Jedynym sposobem na aplikację, aby sprawdzić, czy token odświeżania jest prawidłowy jest próba Zrealizuj go, wykonując żądania tokenu do punktu końcowego tokenu usługi Azure AD.

Gdy zrealizować token odświeżania, aby uzyskać nowy token dostępu, otrzymasz nowy token odświeżania w odpowiedzi tokenu.  Należy zapisać tokenu odświeżania nowo wystawione zastąpienie tego, który zostanie użyty w żądaniu.  Gwarantuje to, że tokenów odświeżania ważność tak długo, jak to możliwe.

## <a name="validating-tokens"></a>Sprawdzanie poprawności tokenów

Aby sprawdzić poprawność żądaniu lub ' access_token ', aplikacji powinni sprawdzać zarówno podpisu tokenu i oświadczenia. Aby sprawdzić poprawność tokenów dostępu, aplikacji należy zweryfikować wystawcy, odbiorców i podpisywania tokenów. Te muszą być weryfikowany pod kątem wartości w dokumencie odnajdywania OpenID. Na przykład wersji niezależnie od dzierżawcy dokumentu znajduje się w [ https://login.microsoftonline.com/common/.well-known/openid-configuration ](https://login.microsoftonline.com/common/.well-known/openid-configuration). Azure AD oprogramowanie pośredniczące ma wbudowane funkcje sprawdzania poprawności tokenów dostępu i można przeglądać naszych [przykłady](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) można znaleźć w wybranym języku. Aby uzyskać więcej informacji na temat sposobu jawnie sprawdzania poprawności tokenu JWT, zobacz [ręczne próbki sprawdzania poprawności tokenu JWT](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation).  

Firma Microsoft udostępnia bibliotek i przykładów kodu, które pokazują, jak łatwo obsługi sprawdzania poprawności tokenu — poniżej informacje po prostu podane dla tych, którzy chcą poznać w podstawowym procesie.  Dostępne są również kilka innych firm bibliotek typu open source dla weryfikacji JWT — Brak co najmniej jedną opcję dla prawie wszystkich platform i tam języka. Aby uzyskać więcej informacji na temat biblioteki uwierzytelniania usługi Azure AD i przykładów kodu, zobacz [biblioteki uwierzytelniania usługi Azure AD](active-directory-authentication-libraries.md).

#### <a name="validating-the-signature"></a>Sprawdzanie poprawności podpisu

Token JWT zawiera trzy segmentów, które są rozdzielone `.` znaków.  Pierwszy segment nosi nazwę **nagłówka**, drugi jako **treści**, a trzeci jako **podpisu**.  Segment podpisu umożliwia sprawdzenie oryginalności tokenu, dzięki czemu mogą być zaufane przez aplikację.

Tokenów wystawionych przez usługę Azure AD są podpisane za pomocą branżowy standard szyfrowanie asymetryczne algorytmy, takie jak RSA 256. Nagłówek tokenu JWT zawiera informacje o metodzie kluczy i szyfrowania używany do podpisywania tokenu:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "kriMPdmBvx68skT8-mPAB3BseeA"
}
```

`alg` Oświadczeń wskazuje algorytm używany do podpisywania tokenu, podczas `x5t` oświadczeń wskazuje określonego klucz publiczny, który został użyty do podpisania tokenu.

Na dowolnym etapie w czasie usługi Azure AD mogą zarejestrować żądaniu przy użyciu jednego z określone pary kluczy publiczno prywatnych. Usługi Azure AD obraca możliwe zestaw kluczy w regularnych odstępach czasu, więc aplikacja powinna być zapisana automatycznie obsługiwać te zmiany klucza.  Uzasadnione częstotliwości, aby wyszukać aktualizacje do kluczy publicznych używane przez usługę Azure AD jest co 24 godziny.

Można uzyskać podpisywania danych klucza niezbędne do zweryfikowania podpisu za pomocą dokumentów metadanych OpenID Connect w lokalizacji:

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [!TIP]
> Sprawdź ten adres URL w przeglądarce!
> 
> 

Ten dokument metadanych jest obiekt JSON zawierający kilku przydatne informacje, takie jak lokalizacja różne punkty końcowe wymagane do wykonywania uwierzytelniania OpenID Connect.  

Zawiera także `jwks_uri`, co daje lokalizacji zestawu kluczy publicznych używane do podpisywania tokenów.  Dokument JSON znajdujący się w `jwks_uri` zawiera wszystkie informacje o kluczu publicznym używany w tej chwili określonego czasu.  Może używać aplikacja `kid` oświadczeń w nagłówku JWT do wybrania, którego klucz publiczny w tym dokumencie został użyty do podpisania określonej tokenu.  Następnie można przeprowadzić weryfikacji podpisu za pomocą prawidłowy klucz publiczny i algorytm wskazany.

Zostanie wykonana Walidacja podpisu wykracza poza zakres tego dokumentu — Brak dostępnych wiele bibliotek typu open source za pomoc, możesz to zrobić w razie potrzeby.

#### <a name="validating-the-claims"></a>Sprawdzanie poprawności oświadczenia

Gdy aplikacja odbiera token (żądaniu podczas logowania użytkownika lub token dostępu jako tokenu elementu nośnego w żądaniu HTTP) on również wykonywać kilka kontroli względem oświadczenia w tokenie.  Te obejmują, ale nie są ograniczone do:

* **Odbiorców** oświadczeń — Aby sprawdzić, czy token jest przeznaczona do aplikacji.
* **Nie wcześniej niż** i **czas wygaśnięcia** oświadczeń — Aby sprawdzić, czy token nie wygasł.
* **Wystawcy** oświadczeń — Aby sprawdzić, czy token został rzeczywiście wystawiony do aplikacji przez usługę Azure AD.
* **Identyfikator jednorazowy** — Aby ograniczyć atak powtórzeń tokenów.
* i więcej...

Pełną listę operacji sprawdzania poprawności oświadczenia aplikacji należy wykonać tokeny Identyfikatora, można znaleźć w [OpenID Connect specyfikacji](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation). Szczegóły oczekiwanych wartości tych oświadczeń są zawarte w poprzednim [żądaniu](#id-tokens) sekcji.

## <a name="token-revocation"></a>Token odwołania

Odśwież tokeny można unieważnione lub odwołać w dowolnym momencie z różnych przyczyn.  Te można podzielić na dwie główne kategorie: przekroczeń limitu czasu i odwołania. 
* Token limitów czasu
  * MaxInactiveTime: Jeśli nie został użyty w czasie ustawieniem MaxInactiveTime token odświeżania, Token odświeżania nie będzie już prawidłowy. 
  * MaxSessionAge: Jeśli MaxAgeSessionMultiFactor lub MaxAgeSessionSingleFactor zostały ustawione na inną niż domyślne (do odwołane), następnie ponowne uwierzytelnianie będzie wymagane po upływie tego czasu w MaxAgeSession *.  
  * Przykłady:
    * Dzierżawca ma MaxInactiveTime 5 dni, użytkownik przeszedł na urlopie na tydzień i dlatego usługi AAD nie otrzymała nowego żądania tokenu od użytkownika w ciągu 7 dni.  Następnym razem, użytkownik żąda nowy token znajdą ich odświeżyć Token został odwołany, a ich musi ponownie wprowadzić swoje poświadczenia. 
    * Poufnej aplikacji ma MaxAgeSessionSingleFactor 1 dzień.  Jeśli użytkownik loguje się w poniedziałek i wtorek (po upływie 25 godzin), muszą zostać ponownie uwierzytelniony.  
* Odwołania
  * Dobrowolny zmiany hasła: Jeśli użytkownik zmieni swoje hasło, może mają ponownego uwierzytelnienia w niektórych aplikacjach, w zależności od sposobu token został osiągnięty.  Zobacz uwagi poniżej, aby wyjątki. 
  * Niedobrowolnego zmiany hasła: Jeśli administrator wymusza na użytkowniku, aby zmienić swoje hasło lub Ponadto resetuje go, następnie tokeny użytkownika są unieważniona jeśli zostały osiągnięte, przy użyciu hasła.  Zobacz uwagi poniżej, aby wyjątki. 
  * Naruszenie zabezpieczeń: W przypadku naruszenia zabezpieczeń (np. z lokalnym magazynem hasła jest naruszony) Administrator można odwołać wszystkie obecnie wystawione tokeny odświeżania.  Spowoduje to wymuszenie ponownego uwierzytelnienia wszystkim użytkownikom. 

> [!NOTE]
>Jeśli hasło z systemem innym niż metoda uwierzytelniania używana (Windows Hello, aplikacji uwierzytelniającej, biometrycznych, takich jak twarzy na obrazie lub linii papilarnych) do osiągnięcia token, zmiana hasła nie spowoduje wymuszenie ponownego uwierzytelnienia użytkownika (ale zostanie wymuszone ich aplikacji uwierzytelniania Aby ponownie uwierzytelnić).  Jest to spowodowane uwierzytelniania wybranych danych wejściowych (a krój, np.) nie został zmieniony i w związku z tym można ponownie ponownego uwierzytelnienia.

## <a name="sample-tokens"></a>Tokeny próbki

Ta sekcja Wyświetla przykłady tokeny SAML i JWT, które zwraca usługi Azure AD. Te przykłady umożliwiają sprawdzanie oświadczenia w kontekście.

### <a name="saml-token"></a>SAML Token

To jest przykład typowego tokenu SAML.

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

### <a name="jwt-token---user-impersonation"></a>Token JWT - personifikacji użytkownika
To jest przykładowy typowe tokenu web JSON (JWT) używane w grant przepływu kodu autoryzacji.
Oprócz oświadczenia, token zawiera numeru wersji w **ver** i **appidacr**, odwołania klasy kontekstu uwierzytelniania, który wskazuje, jak klient został uwierzytelniony. Dla publicznych klienta wartość wynosi 0. Jeśli użyto Identyfikatora klienta lub klucz tajny klienta, wartość to 1.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

## <a name="related-content"></a>Zawartość pokrewna
* Zobacz Azure AD Graph [operacji zasad](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) i [jednostki zasady](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity), aby dowiedzieć się więcej na temat zarządzania zasadami okres istnienia tokenu przy użyciu interfejsu API Azure AD Graph.
* Aby uzyskać więcej informacji i przykłady dotyczące zarządzania zasadami za pomocą poleceń cmdlet programu PowerShell, zawierająca próbek, zobacz [można skonfigurować tokenu okresy istnienia w usłudze Azure AD](../active-directory-configurable-token-lifetimes.md). 
* Dodaj [niestandardowych i opcjonalnych oświadczeń](active-directory-optional-claims.md) na tokeny dla aplikacji. 
