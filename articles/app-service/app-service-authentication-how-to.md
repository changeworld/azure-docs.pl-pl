---
title: Zaawansowane zastosowania uwierzytelniania i autoryzacji — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Pokazuje, jak dostosować uwierzytelnianie i autoryzacja w usłudze App Service i uzyskać różne tokeny i oświadczenia użytkownika.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: b628f88e3d0d644cf5a9471be1d7a766c2b9575b
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67143946"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Zaawansowane zastosowania uwierzytelniania i autoryzacji w usłudze Azure App Service

W tym artykule przedstawiono sposób dostosowania wbudowanego [uwierzytelnianie i autoryzacja w usłudze App Service](overview-authentication-authorization.md)oraz do zarządzania tożsamościami z poziomu aplikacji. 

Aby szybko rozpocząć pracę, zobacz jeden z następujących samouczków:

* [Samouczek: Uwierzytelnianie i autoryzowanie użytkowników end-to-end w usłudze Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)
* [Samouczek: Uwierzytelnianie i autoryzowanie użytkowników end-to-end w usłudze Azure App Service dla systemu Linux](containers/tutorial-auth-aad.md)
* [Jak skonfigurować aplikację do używania logowania w usłudze Azure Active Directory](configure-authentication-provider-aad.md)
* [Jak skonfigurować aplikację do używania logowania usługi Facebook](configure-authentication-provider-facebook.md)
* [Jak skonfigurować aplikację do używania logowania usługi Google](configure-authentication-provider-google.md)
* [Jak skonfigurować aplikację do używania logowania za pomocą konta Microsoft](configure-authentication-provider-microsoft.md)
* [Jak skonfigurować aplikację do używania logowania usługi Twitter](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>Korzystanie z wielu dostawców logowania

Konfiguracja portalu nie oferują sposób setką kompleksowych istnieje wielu dostawców logowania dla użytkowników (takich jak Facebook i Twitter). Jednak nie jest trudne dodać funkcje do aplikacji. Kroki przedstawione poniżej:

Pierwszy w **uwierzytelniania / autoryzacji** stronie w witrynie Azure portal, skonfiguruj każdy z dostawcy tożsamości, aby włączyć.

W **akcji do wykonania w przypadku nieuwierzytelnionego żądania**, wybierz opcję **Zezwalaj na anonimowe żądania (Brak działania)** .

Strony logowania na pasku nawigacyjnym lub dowolnej innej lokalizacji w aplikacji, Dodaj Link umożliwiający zalogowanie się do każdego z dostawców włączono (`/.auth/login/<provider>`). Na przykład:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Gdy użytkownik kliknie na jeden z linków, odpowiednich strony logowania zostanie otwarta do logowania użytkownika.

Aby przekierować użytkownika po-konta logowania do niestandardowego adresu URL, należy użyć `post_login_redirect_url` (nie należy mylić z identyfikatora URI przekierowania w konfiguracji dostawcy tożsamości). parametr ciągu zapytania. Na przykład, aby użytkownik `/Home/Index` po zalogowaniu, użyj następującego kodu HTML:

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Sprawdzanie poprawności tokenów z dostawców

W skierowane do klienta logowania, ręcznie loguje użytkownika z dostawcą aplikacji, a następnie przesyła je token uwierzytelniania w usłudze App Service do sprawdzania poprawności (zobacz [przepływ uwierzytelniania](overview-authentication-authorization.md#authentication-flow)). Weryfikacji, ten sam faktycznie nie przyznać dostęp do zasobów żądaną aplikację, ale pomyślnej weryfikacji zapewni tokenu sesji, który umożliwia dostęp do zasobów aplikacji. 

Aby zweryfikować token dostawcy, aplikację usługi app Service należy najpierw skonfigurować przy użyciu wybranego dostawcy. W czasie wykonywania, po pobraniu tokenu uwierzytelniania za pośrednictwem swojego usługodawcy, Opublikuj token do `/.auth/login/<provider>` do sprawdzania poprawności. Na przykład: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Format tokenu nieznacznie się w zależności od dostawcy. Zobacz poniższą tabelę, aby uzyskać szczegółowe informacje:

| Dostawca wartości | W treści żądania wymagany | Komentarze |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | `expires_in` Właściwość jest opcjonalna. <br/>Podczas żądania tokenu z usługami Live, zawsze żądania `wl.basic` zakresu. |
| `google` | `{"id_token":"<id_token>"}` | `authorization_code` Właściwość jest opcjonalna. Jeśli zostanie określony, jego można również opcjonalnie towarzyszyć `redirect_uri` właściwości. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Użyj prawidłowego [token dostępu użytkownika](https://developers.facebook.com/docs/facebook-login/access-tokens) z usługi Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Jeśli token dostawcy zostanie pomyślnie zweryfikowana, interfejs API zwraca `authenticationToken` w treści odpowiedzi, czyli tokenu sesji. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Po utworzeniu tego tokenu sesji, mogą uzyskać dostęp zasobów chronionej aplikacji, dodając `X-ZUMO-AUTH` nagłówka do żądań HTTP. Na przykład: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Wyloguj się z sesji

Użytkownicy mogą inicjować wylogowania, wysyłając `GET` żądanie aplikacji `/.auth/logout` punktu końcowego. `GET` Żądania wykonuje następujące czynności:

- Powoduje wyczyszczenie plików cookie uwierzytelniania z bieżącej sesji.
- Usuwa tokenów bieżącego użytkownika z tokenu magazynu.
- Dla usługi Azure Active Directory i Google dokonuje po stronie serwera wylogowania dostawcy tożsamości.

Poniżej przedstawiono proste wylogowania łącze na stronie sieci Web:

```HTML
<a href="/.auth/logout">Sign out</a>
```

Domyślnie pomyślny wylogowania przekierowuje klienta do adresu URL `/.auth/logout/done`. Można zmienić na stronie przekierowania post-sign-out przez dodanie `post_logout_redirect_uri` parametr zapytania. Na przykład:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Zalecamy, aby użytkownik [kodowanie](https://wikipedia.org/wiki/Percent-encoding) wartość `post_logout_redirect_uri`.

Korzystając z w pełni kwalifikowaną adresy URL, adres URL musi być hostowane w tej samej domenie albo skonfigurowane jako dozwolone zewnętrznym adresem URL przekierowania dla aplikacji. W poniższym przykładzie, aby przekierować do `https://myexternalurl.com` nie jest obsługiwany w tej samej domenie:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Należy uruchomić następujące polecenie w [usługi Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Zachowanie fragmentów adresu URL

Po użytkownicy logują się do aplikacji, zwykle mają zostać przekierowane do tej samej sekcji w tej samej stronie, takich jak `/wiki/Main_Page#SectionZ`. Jednak ponieważ [fragmenty adresu URL](https://wikipedia.org/wiki/Fragment_identifier) (na przykład `#SectionZ`) nigdy nie są wysyłane do serwera, ich nie są zachowywane domyślnie po logowania OAuth kończy i przekierowuje wróć do aplikacji. Użytkownicy otrzymują nieoptymalne środowisko następnie, kiedy ich potrzebują przejść do żądanego zakotwiczenia ponownie. Powyższe ograniczenie ma zastosowanie do wszystkich rozwiązań po stronie serwera uwierzytelniania.

W przypadku uwierzytelniania usługi App Service można zachować fragmenty adresu URL w logowania OAuth. Aby to zrobić, należy ustawić aplikacji nosi nazwę `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` do `true`. Możesz to zrobić [witryny Azure portal](https://portal.azure.com), lub po prostu uruchom następujące polecenie [usługi Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Dostęp do oświadczenia użytkownika

Usługa App Service przekazuje oświadczenia użytkownika do aplikacji za pomocą specjalnych nagłówków. Zewnętrzne żądania nie są dozwolone do ustawiania tych nagłówków, dzięki czemu są one obecne tylko wtedy, gdy ustawiony przez usługę App Service. Niektóre nagłówki przykład obejmują:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Kod, który jest zapisywany w dowolnym języku lub framework można uzyskać informacji wymaganych z tych nagłówków. W przypadku aplikacji platformy ASP.NET 4.6 **ClaimsPrincipal** jest automatycznie ustawiana odpowiednimi wartościami.

Aplikację można również uzyskać więcej informacji na temat uwierzytelnionego użytkownika, wywołując `/.auth/me`. Serwer funkcji Mobile Apps SDK udostępnia metody pomocnika do pracy z tymi danymi. Aby uzyskać więcej informacji, zobacz [jak używać zestawu SDK usługi Azure Mobile Apps Node.js](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity), i [pracy z zestawem SDK serwera zaplecza platformy .NET dla usługi Azure Mobile Apps](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Pobieranie tokenów w kodzie aplikacji

W kodzie serwera tokenów właściwe dla dostawcy są wprowadzane w nagłówku żądania, dzięki czemu można łatwo z nich korzystać. W poniższej tabeli przedstawiono nazwy możliwe nagłówków tokenu:

| Dostawca | Nazwy nagłówków |
|-|-|
| Usługa Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook Token | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Konto Microsoft | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Z poziomu kodu klienta (np. aplikacji mobilnej lub JavaScript w przeglądarce) wysyłania HTTP `GET` limit czasu żądania `/.auth/me`. Zwrócone dane JSON ma właściwe dla dostawcy tokenów.

> [!NOTE]
> Tokeny dostępu służą do uzyskiwania dostępu do dostawcy zasobów, dzięki czemu są one występuje tylko w przypadku konfigurowania dostawcy z kluczem tajnym klienta. Aby zobaczyć, jak uzyskać tokeny odświeżania, zobacz Odświeżanie tokenów dostępu.

## <a name="refresh-identity-provider-tokens"></a>Tożsamość dostawcy tokenów odświeżania

Gdy token dostępu z dostawcą (nie [tokenu sesji](#extend-session-token-expiration-grace-period)) wygaśnie, należy ponownego uwierzytelnienia użytkownika, aby ponownie użyć tego tokenu. Możesz uniknąć wygaśnięcia tokenu, wprowadzając `GET` wywołanie `/.auth/refresh` punktu końcowego aplikacji. Po wywołaniu usługi App Service automatycznie odświeża tokenów dostępu w magazynie tokenów dla tego uwierzytelnionego użytkownika. Kolejnych żądań tokenów przez kod aplikacji uzyskiwać tokeny odświeżony. Jednak w wyniku odświeżenia tokenu do pracy, token magazynu musi zawierać [tokenów odświeżania](https://auth0.com/learn/refresh-tokens/) dla dostawcy. Opisano sposób uzyskania tokenów odświeżania przez każdego dostawcy, ale poniżej przedstawiono krótkie podsumowanie:

- **Google**: Dołącz `access_type=offline` parametr ciągu zapytania usługi `/.auth/login/google` wywołania interfejsu API. Jeśli używasz zestawu SDK aplikacji mobilnych, można dodać parametr do jednego z `LogicAsync` przeciążenia (zobacz [tokenów odświeżania Google](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: Nie zapewnia tokenów odświeżania. Długotrwałe tokenów wygaśnie po upływie 60 dni (zobacz [wygaśnięcia Facebook i rozszerzenie tokenów dostępu](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **W usłudze Twitter**: Tokeny dostępu nie wygasa (zobacz [często zadawane pytania dotyczące protokołu OAuth w usłudze Twitter](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Konto Microsoft**: Gdy [ustawienia uwierzytelniania konta Microsoft](configure-authentication-provider-microsoft.md), wybierz opcję `wl.offline_access` zakresu.
- **Azure Active Directory**: W [ https://resources.azure.com ](https://resources.azure.com), wykonaj następujące czynności:
    1. W górnej części strony wybierz **odczytu/zapisu**.
    2. W przeglądarce po lewej stronie przejdź do **subskrypcje** >  ** _\<subskrypcji\_nazwa_**   >  **resourceGroups** >  _ **\<zasobów\_grupy\_name >** _   >  **dostawców** > **Microsoft.Web** > **witryn** >  _ **\<aplikacji \_name >** _ > **config** > **authsettings**. 
    3. Kliknij pozycję **Edytuj**.
    4. Zmodyfikować następujące właściwości. Zastąp  _\<aplikacji\_id >_ identyfikator aplikacji usługi Azure Active Directory, usługi, którego chcesz uzyskać dostęp.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Kliknij przycisk **umieścić**. 

Po skonfigurowaniu dostawcy mogą [token odświeżania i czas wygaśnięcia tokenu dostępu](#retrieve-tokens-in-app-code) w magazynie tokenów. 

Aby odświeżyć tokenu dostępu w dowolnym momencie, po prostu Wywołaj `/.auth/refresh` w dowolnym języku. Poniższy fragment kodu używa jQuery odświeżanie tokenów dostępu z klientów JavaScript.

```JavaScript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Jeśli użytkownik odwołuje uprawnienia udzielone aplikacji, wywołania do `/.auth/me` może się nie powieść `403 Forbidden` odpowiedzi. Aby zdiagnozować błędy, sprawdź dzienniki aplikacji, aby uzyskać szczegółowe informacje.

## <a name="extend-session-token-expiration-grace-period"></a>Przedłużenie okresu prolongaty wygaśnięcia tokenu sesji

Sesja uwierzytelniona wygasa po upływie 8 godzin. Po wygaśnięciu uwierzytelnionej sesji jest domyślnie 72-godzinny okres prolongaty. W tym okresie prolongaty możesz odświeżyć tokenu sesji przy użyciu usługi App Service bez ponownego uwierzytelniania nazwy użytkownika. Można wywołać `/.auth/refresh` kiedy tokenu sesji staje się nieprawidłowy i nie trzeba samodzielnie śledzić wygaśnięcia tokenu. 72-godzinny okres prolongaty, po wygaśnięciu, użytkownik musi ponownie zaloguj można uzyskać tokenu prawidłową sesję.

72 godziny nie jest wystarczająco dużo czasu, można rozszerzyć tego okna wygaśnięcia. Rozszerzanie wygaśnięcia w długim okresie może mieć wpływ zabezpieczeń (na przykład gdy token uwierzytelniania jest ujawnione lub kradzieży). Dlatego należy pozostawić domyślne 72 godziny lub Ustaw okres rozszerzenia do najmniejszej wartości.

Aby rozszerzyć domyślny okna wygaśnięcia, uruchom następujące polecenie [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Okres prolongaty dotyczy wyłącznie do sesji uwierzytelniony w usłudze App Service nie tokeny od dostawcy tożsamości. Istnieje okres prolongaty, nie tokeny wygasły dostawcy. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Limit domeny konta logowania

Account Microsoft wraz z usługi Azure Active Directory pozwala zalogować się z wielu domen. Na przykład umożliwia Account Microsoft _outlook.com_, _live.com_, i _hotmail.com_ kont. Usługa Azure Active Directory umożliwia dowolną liczbę domen niestandardowych dla kont logowania. To działanie może być niepożądane dla wewnętrznych aplikacji, której nie chcesz upoważniać nikogo z _outlook.com_ konto dostępu. Aby ograniczyć nazwę domeny konta logowania, wykonaj następujące kroki.

W [ https://resources.azure.com ](https://resources.azure.com), przejdź do **subskrypcje** >  ** _\< subskrypcji\_ nazwa_**   >  **resourceGroups** >  _ **\< zasobów\_ grupy\_ name >** _   >  **dostawców** > **Microsoft.Web** > **witryn**  >    _ **\< aplikacji\_ name >** _ > **config** > **authsettings**. 

Kliknij przycisk **Edytuj**, zmodyfikować następujące właściwości, a następnie kliknij przycisk **umieścić**. Koniecznie Zastąp  _\<domeny\_name >_ domeny ma.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```
## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek: Uwierzytelnianie i autoryzowanie użytkowników end-to-end (Windows)](app-service-web-tutorial-auth-aad.md)
> [samouczka: Uwierzytelnianie i autoryzowanie użytkowników end-to-end (Linux)](containers/tutorial-auth-aad.md)
