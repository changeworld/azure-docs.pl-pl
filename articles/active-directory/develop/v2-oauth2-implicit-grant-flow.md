---
title: Zabezpieczanie jednostronicowych aplikacji przy użyciu niejawnego przepływu platformy tożsamości firmy Microsoft | Azure
description: Tworzenie aplikacji sieci Web przy użyciu implementacji platformy tożsamości firmy Microsoft dla niejawnego przepływu dla aplikacji jednostronicowych.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab5ba4fde7469854954ed19d2e643f2b8a23f34f
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193259"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft Identity platform i niejawny przepływ dotacji

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Za pomocą punktu końcowego platformy tożsamości firmy Microsoft możesz zalogować użytkowników do aplikacji jednostronicowych zarówno z kontami osobistymi, jak i służbowymi firmy Microsoft. Jednostronicowe i inne aplikacje JavaScript, które są uruchamiane głównie w przeglądarce, mają kilka interesujących wyzwań podczas uwierzytelniania:

* Charakterystyki zabezpieczeń tych aplikacji różnią się znacznie od tradycyjnych aplikacji sieci Web opartych na serwerze.
* Wiele serwerów autoryzacji i dostawców tożsamości nie obsługuje żądań CORS.
* Przeglądarka pełnej strony przekieruje się do środowiska użytkownika.

W przypadku tych aplikacji (AngularJS, wpływ. js, reaguje. js itd.) platforma Microsoft Identity platform obsługuje niejawny przepływ OAuth 2,0. Niejawny przepływ jest opisany w [specyfikacji OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-4.2). Główną korzyścią jest umożliwienie aplikacji uzyskiwania tokenów z platformy tożsamości firmy Microsoft bez konieczności wymiany poświadczeń serwera wewnętrznej bazy danych. Dzięki temu aplikacja może logować się do użytkownika, obsługiwać sesję i uzyskiwać tokeny do innych interfejsów API sieci Web w kodzie JavaScript klienta. Istnieje kilka ważnych zagadnień związanych z zabezpieczeniami, które należy wziąć pod uwagę podczas korzystania z niejawnego przepływu w odróżnieniu od personifikacji [klientów](https://tools.ietf.org/html/rfc6749#section-10.3) i [użytkowników](https://tools.ietf.org/html/rfc6749#section-10.3).

Jeśli chcesz użyć niejawnego przepływu i platformy tożsamości firmy Microsoft w celu dodania uwierzytelniania do aplikacji JavaScript, zalecamy użycie biblioteki języka JavaScript Open Source, [msal. js](https://github.com/AzureAD/microsoft-authentication-library-for-js).

Jeśli jednak wolisz zrezygnować z używania biblioteki w aplikacji jednostronicowej i wysyłać komunikaty protokołu samodzielnie, wykonaj poniższe czynności.

> [!NOTE]
> Nie wszystkie scenariusze i funkcje Azure Active Directory (Azure AD) są obsługiwane przez punkt końcowy platformy tożsamości firmy Microsoft. Aby określić, czy należy używać punktu końcowego platformy tożsamości firmy Microsoft, przeczytaj artykuł [ograniczenia dotyczące platformy tożsamości firmy Microsoft](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagram protokołu

Na poniższym diagramie przedstawiono, jak wygląda cały niejawny przepływ logowania, a kolejne sekcje opisują każdy krok bardziej szczegółowo.

![Diagram przedstawiający niejawny przepływ logowania](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Wyślij żądanie logowania

Aby najpierw podpisać użytkownika w aplikacji, możesz wysłać żądanie uwierzytelniania [OpenID Connect Connect](v2-protocols-oidc.md) i uzyskać dostęp `id_token` do punktu końcowego platformy tożsamości firmy Microsoft.

> [!IMPORTANT]
> Aby pomyślnie zażądać tokenu identyfikatora, Rejestracja aplikacji na stronie [Azure Portal-rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) musi mieć niejawnie włączony przepływ, wybierając **tokeny dostępu** i **tokeny identyfikatora** w ramach niejawnego **udzielenia** Paragraf. Jeśli nie jest włączona, `unsupported_response` zostanie zwrócony błąd: **Podana wartość parametru wejściowego "response_type" nie jest dozwolona dla tego klienta. Oczekiwana wartość to "Code"**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Aby przetestować Logowanie przy użyciu przepływu niejawnego, kliknij pozycję <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> Po zalogowaniu przeglądarka powinna zostać przekierowana do `https://localhost/myapp/` programu `id_token` przy użyciu na pasku adresu.
>

| Parametr |  | Opis |
| --- | --- | --- |
| `tenant` | wymagane |`{tenant}` Wartość w ścieżce żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to `common`, `organizations`, `consumers`i identyfikator dzierżawy. Aby uzyskać więcej informacji, zobacz temat [podstawowe informacje](active-directory-v2-protocols.md#endpoints)o protokole. |
| `client_id` | wymagane | Identyfikator aplikacji (klienta), którą strona [Rejestracje aplikacji Azure Portala](https://go.microsoft.com/fwlink/?linkid=2083908) została przypisana do aplikacji. |
| `response_type` | wymagane |Musi zawierać `id_token` do logowania za OpenID Connect Connect. Może również zawierać response_type `token`. Użycie `token` w tym miejscu umożliwi aplikacji otrzymywanie tokenu dostępu od razu od autoryzowanego punktu końcowego bez konieczności podawania drugiego żądania do autoryzowanego punktu końcowego. Jeśli używasz `token` response_type `scope` , parametr musi zawierać zakres wskazujący zasób, dla którego ma zostać wystawiony token. |
| `redirect_uri` | Rekomendowane |Redirect_uri aplikacji, gdzie odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Musi dokładnie odpowiadać jednemu z redirect_uris zarejestrowanych w portalu, z wyjątkiem tego, że musi on być zakodowany w adresie URL. |
| `scope` | wymagane |Rozdzielana spacjami lista [zakresów](v2-permissions-and-consent.md). W przypadku programu OpenID Connect Connect musi zawierać zakres `openid`, który jest tłumaczy na uprawnienie "Logowanie użytkownika" w interfejsie użytkownika wyrażania zgody. Opcjonalnie możesz również uwzględnić zakresy lub `email` `profile` , aby uzyskać dostęp do dodatkowych danych użytkownika. Do żądania zgody na różne zasoby mogą również zawierać inne zakresy w tym żądaniu. |
| `response_mode` | opcjonalne |Określa metodę, która ma zostać użyta do wysłania zwróconego tokenu z powrotem do aplikacji. Wartość domyślna to zapytanie dla tokenu dostępu, ale fragment, jeśli żądanie zawiera id_token. |
| `state` | Rekomendowane |Wartość uwzględniona w żądaniu, która również zostanie zwrócona w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Losowo wygenerowana unikatowa wartość jest zwykle używana w celu [zapobiegania atakom na fałszerstwo żądań między witrynami](https://tools.ietf.org/html/rfc6749#section-10.12). Ten stan jest również używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takiego jak strona lub widok. |
| `nonce` | wymagane |Wartość uwzględniona w żądaniu wygenerowanym przez aplikację, która zostanie uwzględniona w id_token jako roszczeń. Następnie aplikacja może zweryfikować tę wartość, aby zmniejszyć ataki metodą powtórzeń tokenu. Wartość jest zazwyczaj losowo losowym, unikatowym ciągiem, który może służyć do identyfikowania pochodzenia żądania. Wymagane tylko w przypadku żądania id_token. |
| `prompt` | opcjonalne |Wskazuje typ interakcji użytkownika, która jest wymagana. Jedyne prawidłowe wartości w tym momencie to "login'", "none", "select_account" i "zgody". `prompt=login`wymusić użytkownikowi wprowadzanie poświadczeń na to żądanie, negację logowania jednokrotnego. `prompt=none`jest przeciwieństwem do siebie, upewnia się, że użytkownik nie jest wyświetlany z żadnym interaktywnym monitem. Jeśli żądanie nie może zostać zakończone dyskretnie przy użyciu logowania jednokrotnego, punkt końcowy platformy tożsamości firmy Microsoft zwróci błąd. `prompt=select_account`wysyła użytkownika do selektora konta, w którym zostaną wyświetlone wszystkie konta zapamiętane w sesji. `prompt=consent`spowoduje wyzwolenie okna dialogowego zgody na uwierzytelnianie OAuth po zalogowaniu się użytkownika, z prośbą o udzielenie uprawnień do aplikacji. |
| `login_hint`  |opcjonalne |Może służyć do wstępnego wypełniania pola Nazwa użytkownika/adres e-mail na stronie logowania dla użytkownika, jeśli znana jest jego nazwa użytkownika przed czasem. Często aplikacje będą używać tego parametru podczas ponownego uwierzytelniania, ponieważ już wyodrębnili nazwę użytkownika z poprzedniego logowania przy użyciu tego `preferred_username` żądania.|
| `domain_hint` | opcjonalne |Może być jedną z `consumers` lub `organizations`. W przypadku pominięcia zostanie pominięty proces odnajdywania na podstawie poczty e-mail, który użytkownik przejdzie na stronie logowania, co prowadzi do nieco bardziej usprawnionego środowiska użytkownika. Często aplikacje będą używać tego parametru podczas ponownego uwierzytelniania przez wyodrębnienie `tid` roszczeń z id_token. Jeśli wartość `9188040d-6c67-4c5b-b112-36a304b66dad`żądaniato (dzierżawa odbiorcy konta Microsoft), należy użyć `domain_hint=consumers`. `tid` W przeciwnym razie można użyć `domain_hint=organizations` podczas ponownego uwierzytelniania. |

W tym momencie użytkownik zostanie poproszony o wprowadzenie poświadczeń i zakończenie uwierzytelniania. Punkt końcowy platformy tożsamości firmy Microsoft sprawdzi również, czy użytkownik wyraził zgodę na uprawnienia wskazane w `scope` parametrze zapytania. Jeśli użytkownik wyraził zgodę na **żadne** z tych uprawnień, skontaktuje się z użytkownikiem, aby zalecić wymagane uprawnienia. Aby uzyskać więcej informacji, zobacz [uprawnienia, zgoda i aplikacje z wieloma dzierżawcami](v2-permissions-and-consent.md).

Gdy użytkownik uwierzytelni się i udzieli zgody, punkt końcowy platformy tożsamości firmy Microsoft zwróci odpowiedź do aplikacji we wskazanym `redirect_uri`miejscu przy użyciu metody określonej `response_mode` w parametrze.

#### <a name="successful-response"></a>Pomyślna odpowiedź

Pomyślna odpowiedź `response_mode=fragment` przy `response_type=id_token+token` użyciu i wygląda następująco (z podziałami wierszy na potrzeby czytelności):

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fuser.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parametr | Opis |
| --- | --- |
| `access_token` |Uwzględnione, `response_type` Jeśli `token`zawiera. Token dostępu, którego żądał aplikacja, w tym przypadku Microsoft Graph. Token dostępu nie powinien być zdekodowany lub w inny sposób sprawdzany, powinien być traktowany jako ciąg nieprzezroczysty. |
| `token_type` |Uwzględnione, `response_type` Jeśli `token`zawiera. Zawsze `Bearer`będzie. |
| `expires_in`|Uwzględnione, `response_type` Jeśli `token`zawiera. Wskazuje liczbę sekund ważności tokenu na potrzeby buforowania. |
| `scope` |Uwzględnione, `response_type` Jeśli `token`zawiera. Wskazuje zakresy, dla których access_token będzie prawidłowy. Nie może zawierać wszystkich żądanych zakresów, jeśli nie mają zastosowania do użytkownika (w przypadku zakresów tylko w usłudze Azure AD, które są wymagane w przypadku logowania za pomocą konta osobistego). |
| `id_token` | Podpisany token sieci Web JSON (JWT). Aplikacja może zdekodować segmenty tego tokenu, aby zażądać informacji o użytkowniku, który się zalogował. Aplikacja może buforować wartości i wyświetlać je, ale nie należy polegać na nich dla żadnych ograniczeń autoryzacji lub zabezpieczeń. Aby uzyskać więcej informacji na temat id_tokens, [`id_token reference`](id-tokens.md)Zobacz. <br> **Uwaga:** Dostępne tylko wtedy `openid` , gdy żądany został zakres. |
| `state` |Jeśli w żądaniu zostanie uwzględniony parametr stanu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy wartości stanu w żądaniu i odpowiedzi są identyczne. |

#### <a name="error-response"></a>Odpowiedź na błąd

Odpowiedzi na błędy mogą być również wysyłane do `redirect_uri` , aby aplikacja mogła je odpowiednio obsłużyć:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| `error` |Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i mogą być używane do reagowania na błędy. |
| `error_description` |Konkretny komunikat o błędzie, który może ułatwić deweloperom zidentyfikowanie głównej przyczyny błędu uwierzytelniania. |

## <a name="validate-the-id_token"></a>Weryfikowanie id_token

Tylko otrzymanie id_token nie wystarcza do uwierzytelnienia użytkownika; należy również zweryfikować podpis id_token's i zweryfikować oświadczenia w tokenie na podstawie wymagań aplikacji. Punkt końcowy platformy tożsamości firmy Microsoft korzysta z [tokenów sieci Web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) i kryptografii klucza publicznego w celu podpisania tokenów i sprawdzenia, czy są one prawidłowe.

Można sprawdzić poprawność `id_token` kodu klienta, ale typowe rozwiązanie polega na `id_token` wysłaniu do serwera wewnętrznej bazy danych i przeprowadzenia weryfikacji. Po sprawdzeniu poprawności podpisu id_token istnieje kilka oświadczeń, które będą wymagane do zweryfikowania. Zobacz odwołanie, aby uzyskać więcej informacji, w tym [Sprawdzanie poprawności tokenów](id-tokens.md#validating-an-id_token) i [ważnych informacji na temat przerzucania klucza podpisywania](active-directory-signing-key-rollover.md). [ `id_token` ](id-tokens.md) Zalecamy korzystanie z biblioteki do analizowania i weryfikowania tokenów — istnieje co najmniej jedna dostępna dla większości języków i platform.

Możesz również chcieć sprawdzić poprawność dodatkowych oświadczeń w zależności od danego scenariusza. Niektóre typowe walidacje obejmują:

* Upewnienie się, że użytkownik/organizacja zarejestrowali się w aplikacji.
* Upewnienie się, że użytkownik ma odpowiednie uprawnienia/autoryzacja.
* Upewnienie się, że wystąpiło pewne silne uwierzytelnianie, takie jak uwierzytelnianie wieloskładnikowe.

Po sprawdzeniu poprawności id_token można rozpocząć sesję z użytkownikiem i użyć oświadczeń w id_token, aby uzyskać informacje o użytkowniku w aplikacji. Te informacje mogą być używane do wyświetlania, rekordów, personalizacji i innych.

## <a name="get-access-tokens"></a>Uzyskaj tokeny dostępu

Teraz, gdy użytkownik został zalogowany do aplikacji jednostronicowej, można uzyskać tokeny dostępu do wywoływania interfejsów API sieci Web zabezpieczonych przez platformę tożsamości firmy Microsoft, np. [Microsoft Graph](https://developer.microsoft.com/graph). Nawet jeśli token został już odebrany przy użyciu `token` response_type, można użyć tej metody, aby uzyskać tokeny dla dodatkowych zasobów bez konieczności przekierowania użytkownika w celu ponownego zalogowania.

W normalnym przepływie OpenID Connect Connect/OAuth należy to zrobić, wysyłając żądanie do punktu końcowego platformy `/token` tożsamości firmy Microsoft. Jednak punkt końcowy platformy tożsamości firmy Microsoft nie obsługuje żądań CORS, dlatego w celu uzyskania i odświeżenia tokenów nie można wykonać wywołań AJAX. Zamiast tego można użyć niejawnego przepływu w ukrytym elemencie iframe, aby uzyskać nowe tokeny dla innych interfejsów API sieci Web: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

Aby uzyskać szczegółowe informacje na temat parametrów zapytania w adresie URL, zobacz [Wysyłanie żądania logowania](#send-the-sign-in-request).

> [!TIP]
> Spróbuj skopiować & wklejając poniższe żądanie do karty przeglądarki! (Nie zapomnij zastąpić `login_hint` wartości poprawną wartością dla użytkownika)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2user.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint=your-username`
>

Za pomocą `prompt=none` tego parametru żądanie zakończy się pomyślnie lub natychmiast zakończy się niepowodzeniem i wróci do aplikacji. Pomyślna odpowiedź zostanie wysłana do aplikacji w wskazanym `redirect_uri`miejscu przy użyciu metody określonej `response_mode` w parametrze.

#### <a name="successful-response"></a>Pomyślna odpowiedź

Pomyślna odpowiedź `response_mode=fragment` przy użyciu wygląda następująco:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parametr | Opis |
| --- | --- |
| `access_token` |Uwzględnione, `response_type` Jeśli `token`zawiera. Token dostępu, którego żądał aplikacja, w tym przypadku Microsoft Graph. Token dostępu nie powinien być zdekodowany lub w inny sposób sprawdzany, powinien być traktowany jako ciąg nieprzezroczysty. |
| `token_type` | Zawsze `Bearer`będzie. |
| `expires_in` | Wskazuje liczbę sekund ważności tokenu na potrzeby buforowania. |
| `scope` | Wskazuje zakresy, dla których access_token będzie prawidłowy. Nie może zawierać wszystkich żądanych zakresów, jeśli nie mają zastosowania do użytkownika (w przypadku zakresów tylko w usłudze Azure AD, które są wymagane w przypadku logowania za pomocą konta osobistego). |
| `id_token` | Podpisany token sieci Web JSON (JWT). Uwzględnione, `response_type` Jeśli `id_token`zawiera. Aplikacja może zdekodować segmenty tego tokenu, aby zażądać informacji o użytkowniku, który się zalogował. Aplikacja może buforować wartości i wyświetlać je, ale nie należy polegać na nich dla żadnych ograniczeń autoryzacji lub zabezpieczeń. Aby uzyskać więcej informacji na temat id_tokens, zobacz [ `id_token` odwołanie](id-tokens.md). <br> **Uwaga:** Dostępne tylko wtedy `openid` , gdy żądany został zakres. |
| `state` |Jeśli w żądaniu zostanie uwzględniony parametr stanu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy wartości stanu w żądaniu i odpowiedzi są identyczne. |

#### <a name="error-response"></a>Odpowiedź na błąd

Odpowiedzi na błędy mogą być również wysyłane do `redirect_uri` , aby aplikacja mogła je odpowiednio obsłużyć. W przypadku `prompt=none`, oczekiwany błąd to:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametr | Opis |
| --- | --- |
| `error` |Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i mogą być używane do reagowania na błędy. |
| `error_description` |Konkretny komunikat o błędzie, który może ułatwić deweloperom zidentyfikowanie głównej przyczyny błędu uwierzytelniania. |

Jeśli ten błąd wystąpi w żądaniu iframe, użytkownik musi interaktywnie zalogować się ponownie, aby pobrać nowy token. Możesz obsłużyć ten przypadek w dowolny sposób, który ma sens dla aplikacji.

## <a name="refreshing-tokens"></a>Odświeżanie tokenów

Niejawne przyznanie nie zapewnia tokenów odświeżania. Oba `id_token`elementy s `access_token`i s wygasną po krótkim czasie, więc należy przygotować aplikację do okresowego odświeżania tych tokenów. Aby odświeżyć każdy typ tokenu, można wykonać te same ukryte żądanie iframe z powyżej przy użyciu parametru `prompt=none` , aby kontrolować zachowanie platformy tożsamości. Jeśli `id_token`chcesz otrzymać nową, pamiętaj, aby użyć `response_type=id_token` , a `scope=openid` `nonce` także parametru.

## <a name="send-a-sign-out-request"></a>Wyślij żądanie wylogowania

OpenID Connect Connect `end_session_endpoint` umożliwia aplikacji wysyłanie żądania do punktu końcowego platformy tożsamości firmy Microsoft w celu zakończenia sesji użytkownika i czyszczenie plików cookie ustawionych przez punkt końcowy platformy tożsamości firmy Microsoft. Aby w pełni podpisać użytkownika z aplikacji sieci Web, aplikacja powinna zakończyć własną sesję użytkownika (zazwyczaj przez wyczyszczenie pamięci podręcznej tokenów lub usunięcie plików cookie), a następnie przekierować przeglądarkę do:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parametr |  | Opis |
| --- | --- | --- |
| `tenant` |wymagane |`{tenant}` Wartość w ścieżce żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to `common`, `organizations`, `consumers`i identyfikator dzierżawy. Aby uzyskać więcej informacji, zobacz temat [podstawowe informacje](active-directory-v2-protocols.md#endpoints)o protokole. |
| `post_logout_redirect_uri` | Rekomendowane | Adres URL, do którego użytkownik powinien zostać zwrócony po zakończeniu wylogowywania. Ta wartość musi być zgodna z jednym z identyfikatorów URI przekierowania zarejestrowanych dla aplikacji. Jeśli ta wartość nie jest uwzględniona, użytkownik zostanie pokazany jako komunikat ogólny przez punkt końcowy platformy tożsamości firmy Microsoft. |

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć kodowanie, przejdź do [przykładów MSAL js](sample-v2-code.md) .
