---
title: Dowiedz się więcej o inny token, a typy obsługiwane przez usługę Azure AD | Dokumentacja firmy Microsoft
description: Przewodnik dla opis i ocena oświadczenia w tokeny SAML 2.0 i tokenów Web JSON (JWT), wystawiony przez usługę Azure Active Directory (AAD)
documentationcenter: na
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.assetid: 166aa18e-1746-4c5e-b382-68338af921e2
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/22/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: a8d4f2894a188ce83939180def65c5f5b058d215
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902216"
---
# <a name="azure-ad-token-reference"></a>Dokumentacja tokenu usługi Azure AD
Azure Active Directory (Azure AD) emituje kilka rodzajów tokenów zabezpieczających w czasie przetwarzania każdego przepływu uwierzytelniania. W tym dokumencie opisano format, zabezpieczenia właściwości i zawartość każdego typu tokenu. 

## <a name="types-of-tokens"></a>Typy tokenów
Usługa Azure AD obsługuje [Protokół autoryzacji OAuth 2.0](v1-protocols-oauth-code.md), co sprawia, że użycie access_tokens i refresh_tokens. Obsługuje również uwierzytelnianie i logowanie za pomocą [OpenID Connect](v1-protocols-openid-connect-code.md), która wprowadza trzeci typ tokenu, id_token. Każdy z tych tokenów jest reprezentowany jako "token elementu nośnego".

Token elementu nośnego jest tokenem zabezpieczającym uproszczone, która udziela dostępu "bearer" do chronionego zasobu. W tym sensie "bearer" to każda strona, która może powodować tokenu. Uwierzytelnianie za pomocą usługi Azure AD jest wymagane w celu odbierania token elementu nośnego, ale wykonania kroków, aby zabezpieczyć tokenu, aby uniemożliwić przechwycenie przez stronę niezamierzone. Ponieważ nie ma wbudowany mechanizm, aby uniemożliwić ich użycie przez osoby nieupoważnione tokenów elementu nośnego, musi być transportowane w bezpiecznego kanału, takie jak transport layer security (HTTPS). Jeśli token elementu nośnego jest przekazywane w zabezpieczeniu, ataków typu man-in środkowej atak może służyć do uzyskania tokenu i uzyskania nieautoryzowanego dostępu do chronionego zasobu. Te same zasady zabezpieczeń stosowane, gdy przechowywania lub buforowanie tokenów elementu nośnego do późniejszego użycia. Zawsze upewnij się, że Twoja aplikacja przesyła i przechowuje tokenów elementu nośnego w bezpieczny sposób. Aby uzyskać więcej zagadnienia dotyczące zabezpieczeń na tokenów elementu nośnego, zobacz [RFC 6750 sekcji 5](http://tools.ietf.org/html/rfc6750).

Wiele tokeny wystawione przez usługę Azure AD są implementowane jako tokenów sieci Web JSON lub tokenów Jwt. Token JWT jest compact, bezpieczny adres URL sposób przekazywania informacji między dwiema stronami. Informacje zawarte w tokenów Jwt są nazywane "oświadczenia" lub potwierdzenia informacji dotyczących elementu nośnego i podmiotu tokenu. Oświadczenia w tokenów Jwt są obiektami JSON, kodowanie i serializacji do przesłania. Ponieważ tokenów Jwt wystawione przez usługę Azure AD jest podpisany, ale nie są szyfrowane, można łatwo sprawdzić zawartość token JWT na potrzeby debugowania. Istnieją różne narzędzia dla tych czynności, takie jak [jwt.ms](https://jwt.ms/). Aby uzyskać więcej informacji na temat tokenów Jwt, mogą odwoływać się do [specyfikacji JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_tokens
Id_tokens są formą logowania w tokenie zabezpieczającym aplikacja odbiera podczas uwierzytelniania przy użyciu [OpenID Connect](v1-protocols-openid-connect-code.md). Są one reprezentowane jako [tokenów Jwt](#types-of-tokens)i zawiera oświadczenia, które służy do logowania się użytkownika do aplikacji. Można użyć oświadczeń w id_token, zgodnie z potrzebami — często są używane do wyświetlania informacji o koncie lub dokonywania decyzji dotyczących kontroli dostępu w aplikacji.

Id_tokens jest podpisany, ale nie są szyfrowane w tej chwili. Gdy aplikacja otrzymuje elementu id_token, musi ona [zweryfikować podpisu](#validating-tokens) potwierdzenia autentyczności tokenu i weryfikować kilka oświadczenia w tokenie, aby udowodnić, że jego ważności. Oświadczenia zweryfikowany przez aplikację różnią się w zależności od wymagań scenariusza, ale istnieją pewne [typowych roszczenie walidacji](#validating-tokens) , aplikacja musi wykonać w każdym scenariuszu.

Zobacz informacje w poniższej sekcji na id_tokens oświadczeń, a także id_token próbki. Należy pamiętać, że oświadczenia w id_tokens nie są zwracane w określonej kolejności. Ponadto nowe oświadczenia mogą być wprowadzane do id_tokens w dowolnym momencie w czasie — aplikacja nie powinna zostać podzielona na miarę wprowadzania nowych oświadczeń. Poniższa lista zawiera oświadczenia, które aplikacja może niezawodnie interpretować w momencie pisania tego dokumentu. Jeśli to konieczne, jeszcze więcej szczegółów można znaleźć w [specyfikacją z OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Id_token próbki
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [!TIP]
> Praktyki, spróbuj sprawdzanie oświadczenia w id_token przykładowe przez wklejenie go do [jwt.ms](https://jwt.ms/).
> 
> 

#### <a name="claims-in-idtokens"></a>Oświadczenia w id_tokens
> [!div class="mx-codeBreakAll"]
| Token JWT oświadczeń | Name (Nazwa) | Opis |
| --- | --- | --- |
| `aud` |Grupy odbiorców |Adresat tokenu. Aplikację, która odbiera token musi sprawdzić, czy wartość odbiorców jest poprawny i odrzucić wszystkie tokeny przeznaczone dla różnych odbiorców. <br><br> **Przykładowa wartość SAML**: <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **Przykładowa wartość tokenu JWT**: <br> `"aud":"https://contoso.com"` |
| `appidacr` |Application Authentication Context Class Reference |Wskazuje, jak klient został uwierzytelniony. Publicznych klienta wartość wynosi 0. Jeśli identyfikator klienta oraz klucz tajny klienta są używane, wartość to 1. Jeśli certyfikat klienta był używany do uwierzytelniania, wartość jest równa 2. <br><br> **Przykładowa wartość tokenu JWT**: <br> `"appidacr": "0"` |
| `acr` |Authentication Context Class Reference |Wskazuje, jak został uwierzytelniony podmiot, w przeciwieństwie do klientów w oświadczeniu Application Authentication Context Class Reference. Wartość "0" oznacza, że uwierzytelnianie użytkowników końcowych nie spełniał wymagań 29115 ISO/IEC. <br><br> **Przykładowa wartość tokenu JWT**: <br> `"acr": "0"` |
| Błyskawiczne uwierzytelnianie |Rejestruje datę i godzinę wystąpienia uwierzytelniania. <br><br> **Przykładowa wartość SAML**: <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` | |
| `amr` |Metoda uwierzytelniania |Określa, jak został uwierzytelniony podmiot tokenu. <br><br> **Przykładowa wartość SAML**: <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **Przykładowa wartość tokenu JWT**: `“amr”: ["pwd"]` |
| `given_name` |Imię |Zawiera pierwszy lub "" Nazwa użytkownika, według stawki ustalonej w obiekcie użytkownika usługi Azure AD. <br><br> **Przykładowa wartość SAML**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **Przykładowa wartość tokenu JWT**: <br> `"given_name": "Frank"` |
| `groups` |Grupy |Zawiera identyfikatory obiektów, reprezentujących podmiotu członkostw w grupie. Te wartości są unikatowe (patrz obiektu o identyfikatorze) i może być bezpiecznie stosowany w celu zarządzania dostępem, takie jak wymuszanie autoryzacji dostępu do zasobu. Grupy uwzględniane w oświadczenie grupy są skonfigurowane na podstawie poszczególnych aplikacji, za pomocą właściwości "groupMembershipClaims" manifestu aplikacji. Wartość null powoduje wyłączenie wszystkich grup, wartość "SecurityGroup" będzie zawierać tylko członkostwa grupy zabezpieczeń usługi Active Directory, a wartość "All" będzie zawierać grupy zabezpieczeń i listy dystrybucyjne usługi Office 365. <br><br> **Informacje o**: <br> Zobacz `hasgroups` oświadczenia poniżej, aby uzyskać szczegółowe informacje na temat korzystania z `groups` oświadczenia przyznawania niejawnego. <br> Dla innych przepływów awaria liczbę grup, których użytkownik ma przekracza limit (150 dla protokołu SAML, 200 dla tokenów JWT), a następnie nadwyżka oświadczenia zostaną dodane źródła oświadczeń, wskazujący na punkt końcowy programu Graph, zawierający listę grup dla użytkownika. (w. <br><br> **Przykładowa wartość SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **Przykładowa wartość tokenu JWT**: <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
|`hasgroups` | Token JWT niejawny przepływ grup nadwyżkowe wskaźnika| Jeśli jest obecne, zawsze `true`, oznaczające użytkownika znajduje się w co najmniej jedną grupę. Użyta zamiast `groups` oświadczenia dla elementów Jwt w przepływach przyznawanie niejawne, jeśli pełna grupy oświadczenia będzie rozszerzać składnik fragment identyfikatora URI poza granicami długość adresu URL (obecnie 6- lub więcej grup). Wskazuje, czy którego powinien używać klient programu Graph można określić grupy użytkowników (`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`). |
| `groups:src1` <br> `http://schemas.microsoft.com/claims/groups.link` | Wskaźnik użycia nadwyżkowego grup | Dla żądań tokenów, które nie są ograniczone długość (zobacz `hasgroups` powyżej), ale nadal zbyt duży dla tokenu, łącze do listy grup pełny dla użytkownika zostaną dołączone. Dla elementów Jwt jako roszczenie rozproszonej, SAML jako nowe oświadczenie zamiast `groups` oświadczenia. <br><br> **Przykładowa wartość SAML**: <br> `<Attribute Name=” http://schemas.microsoft.com/claims/groups.link”>`<br>`<AttributeValue>https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects<AttributeValue>` <br><br> **Przykładowa wartość tokenu JWT**: <br> `"groups":"src1` <br> `_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }`|
| `idp` |Dostawca tożsamości |Rejestruje dostawcę tożsamości, który uwierzytelnił podmiot tokenu. Ta wartość jest taka sama jak wartość oświadczenia wystawcy, chyba że konto użytkownika jest w innej dzierżawie niż wystawcy. <br><br> **Przykładowa wartość SAML**: <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **Przykładowa wartość tokenu JWT**: <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` |IssuedAt |Przechowuje czas, w którym został wystawiony token. Często służy do mierzenia świeżości tokenu. <br><br> **Przykładowa wartość SAML**: <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **Przykładowa wartość tokenu JWT**: <br> `"iat": 1390234181` |
| `iss` |Wystawca |Określa usługę tokenu zabezpieczającego (STS), który tworzy i zwraca token. W tokenach, które zwraca usługi Azure AD wystawca jest sts.windows.net. Identyfikator GUID w wartości wystawcy oświadczeń jest identyfikator dzierżawy katalogu usługi Azure AD. Identyfikator dzierżawy jest niezmienny i niezawodne identyfikator katalogu. <br><br> **Przykładowa wartość SAML**: <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **Przykładowa wartość tokenu JWT**: <br>  `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` |Nazwisko |Zawiera ostatni nazwę, nazwisko lub nazwę rodziny użytkownika, zgodnie z definicją w obiekcie użytkownika usługi Azure AD. <br><br> **Przykładowa wartość SAML**: <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **Przykładowa wartość tokenu JWT**: <br> `"family_name": "Miller"` |
| `unique_name` |Name (Nazwa) |Udostępnia zrozumiałą wartość identyfikującą podmiot tokenu. Ta wartość nie musi być unikatowa w ramach dzierżawy i jest przeznaczony do użycia tylko w celach wyświetlania. <br><br> **Przykładowa wartość SAML**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **Przykładowa wartość tokenu JWT**: <br> `"unique_name": "frankm@contoso.com"` |
| `oid` |Identyfikator obiektu |Zawiera unikatowy identyfikator obiektu w usłudze Azure AD. Ta wartość jest niezmienny i nie może być ponownie przypisywany ani ponownie. Użyj Identyfikatora obiektu, aby zidentyfikować obiekt w zapytaniach do usługi Azure AD. <br><br> **Przykładowa wartość SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **Przykładowa wartość tokenu JWT**: <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` |Role |Reprezentuje wszystkie role aplikacji, które temat ma przyznane bezpośrednio i pośrednio za pośrednictwem członkostwa w grupie i może służyć do wymuszania kontroli dostępu opartej na rolach. Role aplikacji są definiowane na podstawie poszczególnych aplikacji przy użyciu `appRoles` właściwości manifestu aplikacji. `value` Właściwości poszczególnych ról aplikacji jest wartość, która jest wyświetlana w oświadczenia ról. <br><br> **Przykładowa wartość SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **Przykładowa wartość tokenu JWT**: <br> `“roles”: ["Admin", … ]` |
| `scp` |Zakres |Wskazuje personifikacji uprawnienia do aplikacji klienckiej. Domyślne uprawnienia `user_impersonation`. Właściciel zabezpieczono zasób można zarejestrować dodatkowych wartości w usłudze Azure AD. <br><br> **Przykładowa wartość tokenu JWT**: <br> `"scp": "user_impersonation"` |
| `sub` |Podmiot |Identyfikuje jednostkę o tym, które token określa informacje, takie jak użytkownik aplikacji. Ta wartość jest niemodyfikowalna i nie może zostać przypisany, lub ponownie, dlatego może służyć do sprawdzania autoryzacji bezpiecznie. Ponieważ temat zawsze jest obecny w tokenach problemy dotyczące usługi Azure AD, zalecane jest korzystanie z tej wartości w systemie autoryzacji ogólnego przeznaczenia. <br> `SubjectConfirmation` nie jest oświadczenia. Opisuje sposób przedmiotem token jest weryfikowany. `Bearer` Wskazuje, że temat potwierdza zapoznały tokenu. <br><br> **Przykładowa wartość SAML**: <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **Przykładowa wartość tokenu JWT**: <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"` |
| `tid` |Identyfikator dzierżawy |Identyfikator niezmienne, jednorazowego, który identyfikuje dzierżawy katalogu, który wystawił token. Ta wartość umożliwia dostęp do zasobów katalogu specyficznym dla dzierżawy w aplikacji z wieloma dzierżawami. Na przykład można użyć tej wartości do identyfikowania dzierżawy w wywołaniu interfejsu API programu Graph. <br><br> **Przykładowa wartość SAML**: <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **Przykładowa wartość tokenu JWT**: <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"` |
| `nbf`, `exp` |Czas życia tokenu |Definiuje przedział czasu, przez który token jest prawidłowy. Usługa, która sprawdza poprawność tokenu należy sprawdzić, że bieżąca data mieści się okres istnienia tokenu, else powinien on odrzucenie tokena. Usługa może pozwalać na pięć minut poza zakresem okres istnienia tokenu na wszelkie różnice w zegarze ("niesymetryczność czasu") między usługą Azure AD i usługi. <br><br> **Przykładowa wartość SAML**: <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **Przykładowa wartość tokenu JWT**: <br> `"nbf":1363289634, "exp":1363293234` |
| `upn` |Nazwa główna użytkownika |Przechowuje nazwę głównej nazwy użytkownika.<br><br> **Przykładowa wartość tokenu JWT**: <br> `"upn": frankm@contoso.com` |
| `ver` |Wersja |Przechowuje numer wersji tokenu. <br><br> **Przykładowa wartość tokenu JWT**: <br> `"ver": "1.0"` |

## <a name="access-tokens"></a>Tokeny dostępu
Po pomyślnym uwierzytelnieniu usługa Azure AD zwraca token dostępu, którego można uzyskać dostęp do chronionych zasobów. Token dostępu jest base 64 zakodowane tokenu Web JSON (JWT) i jego zawartość może być kontrolowane, uruchamiając go przy użyciu dekodera.

Jeśli tylko aplikacja *używa* tokenów dostępu w celu uzyskania dostępu do interfejsów API, można i należy traktować tokenów dostępu jako całkowicie nieprzezroczysty — są one po prostu ciągów, które aplikacji mogą przejść do zasobów w żądaniach HTTP.

W przypadku żądania tokenu dostępu, usługi Azure AD zwraca także niektóre metadane dotyczące tokenu dostępu do użycia w Twojej aplikacji. Informacje te obejmują czas wygaśnięcia tokenu dostępu i zakresy, dla których jest on prawidłowy. Umożliwia to aplikacji, aby wykonać inteligentne buforowanie tokenów dostępu, bez konieczności przeanalizować Otwórz tokenu dostępu, sam.

Jeśli Twoja aplikacja jest chroniona za pomocą usługi Azure AD, który oczekuje, że tokeny dostępu w żądaniach HTTP interfejsu API, następnie należy wykonać sprawdzanie poprawności i inspekcji tokenów, które otrzymujesz. Aplikację należy wykonać sprawdzanie poprawności tokenu dostępu przed użyciem w celu dostępu do zasobów. Aby uzyskać więcej informacji na temat weryfikacji, zobacz [sprawdzania poprawności tokenów](#validating-tokens). Aby uzyskać szczegółowe informacje, jak to zrobić przy użyciu platformy .NET, zobacz [ochrona interfejsu API sieci Web za pomocą tokenów elementu nośnego z usługi Azure AD](quickstart-v1-dotnet-webapi.md).

## <a name="refresh-tokens"></a>Tokenów odświeżania

Odśwież tokeny są tokeny zabezpieczające, które Twoja aplikacja może użyć w celu uzyskania nowych tokenów dostępu w usłudze flow OAuth 2.0. Umożliwia aplikacji w celu osiągnięcia długoterminowe dostęp do zasobów w imieniu użytkownika bez konieczności interakcji użytkownika.

Tokeny odświeżania są wielu zasobów. To znaczy czy token odświeżania Odebrane żądania tokenu dla jednego zasobu można zrealizować dla tokenów dostępu do zupełnie innego zasobu. Aby to zrobić, należy ustawić `resource` parametru w żądaniu do zasobu docelowego.

Tokeny odświeżania są całkowicie nieprzezroczysty do swojej aplikacji. Są one długotrwałe, ale aplikacja nie powinna być zapisana można oczekiwać, że token odświeżania jest ważny dla dowolnego okresu czasu. Tokeny odświeżania może zostać unieważnione w dowolnym momencie w czasie z różnych przyczyn — zobacz [odwołanie tokenu](#token-revocation) tych powodów. Jedynym sposobem dla swojej aplikacji dowiedzieć się, czy token odświeżania jest prawidłowa jest próba Zrealizuj go, wprowadzając żądania tokenu do punktu końcowego tokenu usługi Azure AD.

Po wprowadzeniu token odświeżania, aby uzyskać nowy token dostępu, otrzymają nowy token odświeżania w odpowiedzi tokenu. Należy zapisać token odświeżania nowo wystawiane, zastępując ten, który zostanie użyty w żądaniu. Gwarantuje to tokenów odświeżania i ważność tak długo, jak to możliwe.

## <a name="validating-tokens"></a>Sprawdzanie poprawności tokenów

Aby można było zweryfikować id_token lub ' access_token ', aplikację należy zweryfikować podpisu tokenu i oświadczenia. Aby sprawdzić poprawność tokenów dostępu, aplikacja powinna również umożliwić weryfikację w wystawcy, odbiorców i podpisywania tokenów. Muszą one być weryfikowany pod kątem wartości w dokumencie odnajdywania protokołu OpenID. Na przykład, niezależnie od dzierżawcy wersję dokumentu znajduje się w [ https://login.microsoftonline.com/common/.well-known/openid-configuration ](https://login.microsoftonline.com/common/.well-known/openid-configuration). Usługa Azure AD oprogramowania pośredniczącego ma wbudowane funkcje sprawdzania poprawności tokenów dostępu i możesz przeglądać naszych [przykłady](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) można znaleźć w wybranym języku. Aby uzyskać więcej informacji na temat sposobu jawnie Weryfikacja tokenu JWT, zobacz [ręczne przykładowe sprawdzania poprawności tokenu JWT](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation). 

Firma Microsoft udostępnia biblioteki i przykłady kodu, które pokazują, jak i w łatwy sposób spełniaj walidacji tokenów — następujące informacje po prostu podać dla tych, którzy chcą poznać bazowego procesu. Dostępne są również kilka bibliotek typu open source innych firm dla walidacji tokenów JWT — istnieje co najmniej jedną z opcji prawie każdej platformy i języka tam. Aby uzyskać więcej informacji dotyczących bibliotek uwierzytelniania usługi Azure AD oraz przykładów kodu, zobacz [bibliotek uwierzytelniania usługi Azure AD](active-directory-authentication-libraries.md).

#### <a name="validating-the-signature"></a>Weryfikowanie podpisu

Token JWT zawiera trzy segmenty, które są oddzielone `.` znaków. Pierwszy segment jest znany jako **nagłówka**, drugi jako **treści**, a trzeci jako **podpisu**. Segment podpisu może służyć do sprawdzania autentyczności tokenu, dzięki czemu mogą być zaufane przez aplikację.

Tokeny wystawione przez usługę Azure AD są podpisywane przy użyciu branżowych standardowa asymetrycznych algorytmów, takich jak RSA 256. Nagłówek tokenu JWT zawiera informacje o metodzie klucza i szyfrowania używany do podpisywania tokenu:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

`alg` Oświadczenia wskazuje algorytm, który został użyty do podpisywania tokenu, while `x5t` oświadczenia wskazuje określonego klucza publicznego, który był używany do podpisywania tokenu.

W dowolnym danym momencie w czasie usługi Azure AD może podpisać id_token przy użyciu jednego zestawu pary kluczy publiczny prywatny. Usługa Azure AD obraca się możliwe zestaw kluczy w regularnych odstępach czasu, więc aplikacji mają być zapisywane automatycznie obsługiwać zmiany klucza. Uzasadnione częstotliwość, aby sprawdzić, czy są aktualizacje kluczy publicznych używane przez usługę Azure AD jest co 24 godziny.

Możesz uzyskać podpisywania danych klucza, który należy zweryfikować podpisu, za pomocą protokołu OpenID Connect dokumentów metadanych znajdujący się w:

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [!TIP]
> Wypróbuj ten adres URL w przeglądarce!

Ten dokument metadanych jest obiektem JSON zawierającym kilka rodzajów przydatne informacje, takie jak lokalizacja różne punkty końcowe, wymagane do wykonywania uwierzytelniania OpenID Connect. 

Obejmuje również `jwks_uri`, co daje lokalizacji zestawu kluczy publicznych, używany do podpisywania tokenów. Dokument JSON znajdujący się w `jwks_uri` zawiera wszystkie informacje o kluczu publicznym używany w tej chwili określonego czasu. Twoja aplikacja może używać `kid` oświadczenia w nagłówku JWT, aby wybrać, które klucza publicznego, w tym dokumencie został użyty do podpisania dany token. Następnie można wykonywać Weryfikacja podpisu przy użyciu prawidłowy klucz publiczny i wskazany algorytmu.

> [!NOTE]
> Zwraca punkt końcowy w wersji 1.0, zarówno `x5t` i `kid` oświadczeń. `x5t` Oświadczeń brakuje tokenów w wersji 2.0. Punkt końcowy v2.0 odpowiada za pomocą `kid` oświadczenia. Idąc dalej, firma Microsoft zaleca używanie `kid` oświadczeń można zweryfikować tokenu.

Wykonywanie weryfikacji podpisu wykracza poza zakres tego dokumentu — wiele bibliotek typu open source są dostępne, aby pomóc Ci w tym celu w razie potrzeby.

#### <a name="validating-the-claims"></a>Sprawdzanie poprawności oświadczenia

Kiedy aplikacja otrzymuje token (id_token podczas logowania użytkownika lub token dostępu jako token elementu nośnego w żądaniu HTTP) on również wykonać kilka kontroli względem oświadczenia w tokenie. Obejmują one nie są ograniczone do:

* **Odbiorców** oświadczeń — Aby sprawdzić, czy token jest przeznaczona do swojej aplikacji.
* **Nie wcześniej niż** i **czas wygaśnięcia** oświadczeń — Sprawdź, czy token nie wygasł.
* **Wystawcy** oświadczeń — w celu sprawdzenia, czy token rzeczywiście został wystawiony na aplikację przez usługę Azure AD.
* **Jednorazowego** — Aby uniknąć ataku powtarzania tokenu.
* i więcej...

Aby uzyskać pełną listę operacji sprawdzania poprawności oświadczenia aplikacji należy wykonać dla tokenów Identyfikatora dotyczą [specyfikacją z OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation). Szczegółowe informacje o oczekiwanej wartości te oświadczenia są uwzględnione w poprzednim [id_token](#id-tokens) sekcji.

## <a name="token-revocation"></a>Odwołanie tokenu

Odśwież tokeny mogą zostać unieważnione lub odwołać w dowolnym momencie i z różnych powodów. Te można podzielić na dwie główne kategorie: limity czasu i odwołania. 

**Token przekroczeń limitu czasu**

* MaxInactiveTime: Jeśli nie została użyta w czasie przez MaxInactiveTime token odświeżania, odświeżanie tokenu nie będzie już prawidłowy. 
* MaxSessionAge: Jeśli MaxAgeSessionMultiFactor lub MaxAgeSessionSingleFactor zostały ustawione na coś innego niż domyślne (aż do odwołane), to ponowne uwierzytelnianie będą wymagane po upływie tego czasu w MaxAgeSession *. 
* Przykłady:
  * Dzierżawa ma MaxInactiveTime 5 dni, użytkownik przeszedł na urlopie w każdym tygodniu i więc usługi AAD nie otrzymała żądanie nowego tokenu przez użytkownika w ciągu 7 dni. Następnym razem użytkownik zażąda nowego tokenu, znajdą ich odświeżyć Token został odwołany, a ich musi ponownie wprowadzić swoje poświadczenia. 
  * Poufnej aplikacji ma MaxAgeSessionSingleFactor 1 dzień. Jeśli użytkownik loguje się w poniedziałek i wtorek (po upływie 25 godzin), muszą zostać ponownie uwierzytelniony. 

**Odwołania**

|   | Plik cookie oparte na hasłach | Token oparte na hasłach | Na podstawie plików cookie bez hasła | Inne niż hasło na podstawie tokenu | Token poufne klienta| 
|---|-----------------------|----------------------|---------------------------|--------------------------|--------------------------|
|Hasło wygasło| Pozostanie aktywny|Pozostanie aktywny|Pozostanie aktywny|Pozostanie aktywny|Pozostanie aktywny|
|Hasło zostało zmienione przez użytkownika| Odwołany | Odwołany | Pozostanie aktywny|Pozostanie aktywny|Pozostanie aktywny|
|Użytkownik wykona samoobsługowego resetowania HASEŁ|Odwołany | Odwołany | Pozostanie aktywny|Pozostanie aktywny|Pozostanie aktywny|
|Administrator operacji resetowania hasła|Odwołany | Odwołany | Pozostanie aktywny|Pozostanie aktywny|Pozostanie aktywny|
|Użytkownik odwołuje tokeny odświeżania [za pośrednictwem programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Odwołany | Odwołany |Odwołany | Odwołany |Odwołany | Odwołany |
|Administrator odwołuje wszystkie tokeny odświeżania dla dzierżawy [za pośrednictwem programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Odwołany | Odwołany |Odwołany | Odwołany |Odwołany | Odwołany |
|[Pojedynczy znak w poziomie](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code#single-sign-out) w sieci web | Odwołany | Pozostanie aktywny |Odwołany | Pozostanie aktywny |Pozostanie aktywny |Pozostanie aktywny |

> [!NOTE]
> "Non-password na podstawie" nazwy logowania jest w jednym gdzie użytkownik nie wpisz hasło, aby pobrać go.  Na przykład przy użyciu usługi rozpoznawania twarzy Windows Hello, klucz FIDO lub numeru PIN. 
>
> Istnieje znany problem za pomocą Windows głównej odświeżanie tokenu.  Jeśli PRT uzyskuje się za pomocą hasła, a następnie użytkownik loguje się za pośrednictwem Witaj, nie ma to wpływu na inicjowanie PRT i zostanie odwołana, jeśli użytkownik zmieni swoje hasło. 

## <a name="sample-tokens"></a>Przykładowe tokenów

W tej sekcji przedstawia przykłady tokenów SAML i tokenów JWT, które zwraca usługi Azure AD. Te przykłady pozwalają zobaczyć oświadczeń w kontekście.

### <a name="saml-token"></a>SAML Token

To jest przykład typowego tokenu języka SAML.

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
Jest to przykład typowego tokenu web JSON (JWT) używane w przepływie przyznawania kodu autoryzacji.
Oprócz oświadczeń, token zawiera numer wersji w **ver** i **appidacr**, odwołania klasy kontekstu uwierzytelniania, który wskazuje, jak klient został uwierzytelniony. Publicznych klienta wartość wynosi 0. Jeśli użyto Identyfikatora klienta lub klucza tajnego klienta, wartość to 1.

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

## <a name="related-content"></a>Powiązana zawartość
* Zobacz program Azure AD Graph [operacje dotyczące zasad](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) i [jednostka zasad](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity), aby dowiedzieć się więcej o zarządzaniu zasady okres istnienia tokenu za pośrednictwem interfejsu API programu Graph usługi Azure AD.
* Aby uzyskać więcej informacji i przykłady dotyczące zarządzania zasad za pomocą poleceń cmdlet programu PowerShell, w tym przykłady, zobacz [okresów istnienia tokenu można skonfigurować w usłudze Azure AD](active-directory-configurable-token-lifetimes.md). 
* Dodaj [oświadczeń niestandardowych i opcjonalnie](active-directory-optional-claims.md) do tokenów dla aplikacji. 
