---
title: Zaawansowane użytkowanie AuthN/AuthO
description: Dowiedz się, aby dostosować funkcję uwierzytelniania i autoryzacji w usłudze App Service dla różnych scenariuszy i uzyskać oświadczenia użytkowników i różne tokeny.
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: d57b196bf95ebdf31bc459ad4b9d718fd32ca495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280835"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Zaawansowane użycie uwierzytelniania i autoryzacji w usłudze Azure App Service

W tym artykule pokazano, jak dostosować wbudowane [uwierzytelnianie i autoryzację w usłudze App Service](overview-authentication-authorization.md)oraz zarządzać tożsamością z aplikacji. 

Aby szybko rozpocząć, zobacz jeden z następujących samouczków:

* [Samouczek: Uwierzytelnij i autoryzuj użytkowników end-to-end w usłudze Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)
* [Samouczek: Uwierzytelnij i autoryzuj użytkowników end-to-end w usłudze Azure App Service dla systemu Linux](containers/tutorial-auth-aad.md)
* [Jak skonfigurować aplikację do używania logowania w usłudze Azure Active Directory](configure-authentication-provider-aad.md)
* [Jak skonfigurować aplikację do używania logowania usługi Facebook](configure-authentication-provider-facebook.md)
* [Jak skonfigurować aplikację do używania logowania usługi Google](configure-authentication-provider-google.md)
* [Jak skonfigurować aplikację do używania logowania za pomocą konta Microsoft](configure-authentication-provider-microsoft.md)
* [Jak skonfigurować aplikację do używania logowania usługi Twitter](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>Korzystanie z wielu dostawców logowania

Konfiguracja portalu nie oferuje pod klucz sposobu prezentowania użytkownikom wielu dostawców logowania (takich jak Facebook i Twitter). Jednak nie jest trudno dodać funkcje do aplikacji. Kroki są opisane w następujący sposób:

Najpierw na stronie **Uwierzytelnianie / Autoryzacja** w witrynie Azure Portal skonfiguruj każdego dostawcy tożsamości, który chcesz włączyć.

W **akcji do podjęcia, gdy żądanie nie jest uwierzytelnione,** wybierz **opcję Zezwalaj na żądania anonimowe (bez działania).**

Na stronie logowania, na pasku nawigacyjnym lub w innej lokalizacji aplikacji dodaj łącze logowania do`/.auth/login/<provider>`każdego z włączonych dostawców ( ). Przykład:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Gdy użytkownik kliknie jeden z łączy, zostanie otwarta odpowiednia strona logowania, aby zalogować się do użytkownika.

Aby przekierować użytkownika po zalogowaniu się do `post_login_redirect_url` niestandardowego adresu URL, należy użyć parametru ciągu zapytania (nie należy mylić z identyfikatorem URI przekierowania w konfiguracji dostawcy tożsamości). Na przykład, aby przejść `/Home/Index` do użytkownika po zalogowaniu się, użyj następującego kodu HTML:

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Sprawdzanie poprawności tokenów od dostawców

W logowaniu kierowanym przez klienta aplikacja loguje się ręcznie do dostawcy, a następnie przesyła token uwierzytelniania do usługi App Service w celu weryfikacji (zobacz [przepływ uwierzytelniania).](overview-authentication-authorization.md#authentication-flow) Ta weryfikacja sama w rzeczywistości nie daje dostępu do zasobów aplikacji żądanej, ale pomyślne sprawdzanie poprawności zapewni token sesji, który można użyć do uzyskania dostępu do zasobów aplikacji. 

Aby sprawdzić poprawność tokenu dostawcy, aplikacja usługi App Service musi najpierw być skonfigurowana z żądanym dostawcą. W czasie wykonywania po pobraniu tokenu uwierzytelniania od `/.auth/login/<provider>` dostawcy należy zaksięgować token do sprawdzania poprawności. Przykład: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Format tokenu różni się nieznacznie w zależności od dostawcy. Szczegółowe informacje można znaleźć w poniższej tabeli:

| Wartość dostawcy | Wymagane w treści żądania | Komentarze |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | Właściwość `expires_in` jest opcjonalna. <br/>Żądając tokenu z usług Live, `wl.basic` zawsze żądaj zakresu. |
| `google` | `{"id_token":"<id_token>"}` | Właściwość `authorization_code` jest opcjonalna. Po określeniu, może również opcjonalnie `redirect_uri` towarzyszyć właściwości. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Użyj prawidłowego [tokenu dostępu użytkownika](https://developers.facebook.com/docs/facebook-login/access-tokens) z Facebooka. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Jeśli token dostawcy zostanie pomyślnie zweryfikowany, `authenticationToken` interfejs API zwraca z treścią odpowiedzi, która jest tokenem sesji. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Po uzyskaniu tego tokenu sesji można uzyskać dostęp `X-ZUMO-AUTH` do chronionych zasobów aplikacji, dodając nagłówek do żądań HTTP. Przykład: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Wylogowywanie się z sesji

Użytkownicy mogą zainicjować wylogowanie, wysyłając `GET` żądanie `/.auth/logout` do punktu końcowego aplikacji. Żądanie `GET` wykonuje następujące czynności:

- Czyści pliki cookie uwierzytelniania z bieżącej sesji.
- Usuwa tokeny bieżącego użytkownika z magazynu tokenów.
- W przypadku usługi Azure Active Directory i Google wylogowa się z usług dostawcy tożsamości po stronie serwera.

Oto prosty link na stronie internetowej służący do wylogowywania:

```HTML
<a href="/.auth/logout">Sign out</a>
```

Domyślnie pomyślne wylogowanie przekierowuje `/.auth/logout/done`klienta do adresu URL . Stronę przekierowania po wyloguj się `post_logout_redirect_uri` można zmienić, dodając parametr kwerendy. Przykład:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Zaleca się [zakodowanie](https://wikipedia.org/wiki/Percent-encoding) wartości . `post_logout_redirect_uri`

W przypadku korzystania z w pełni kwalifikowanych adresów URL adres URL musi być hostowany w tej samej domenie lub skonfigurowany jako dozwolony zewnętrzny adres URL przekierowania dla aplikacji. W poniższym przykładzie, `https://myexternalurl.com` aby przekierować do tego nie jest obsługiwany w tej samej domenie:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Uruchom następujące polecenie w [usłudze Azure Cloud Shell:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Zachowywanie fragmentów adresów URL

Po zalogowaniu się użytkowników do aplikacji zazwyczaj chcą zostać przekierowani do tej `/wiki/Main_Page#SectionZ`samej sekcji tej samej strony, takiej jak . Jednak ponieważ [fragmenty adresów](https://wikipedia.org/wiki/Fragment_identifier) `#SectionZ`URL (na przykład) nigdy nie są wysyłane do serwera, nie są domyślnie zachowywane po zakończeniu logowania OAuth i przekierowuje z powrotem do aplikacji. Użytkownicy następnie uzyskać nieoptymalne doświadczenie, gdy trzeba przejść do żądanej kotwicy ponownie. To ograniczenie dotyczy wszystkich rozwiązań uwierzytelniania po stronie serwera.

W uwierzytelnianiu usługi app service można zachować fragmenty adresów URL w całym logowanie OAuth. Aby to zrobić, ustaw `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` ustawienie `true`aplikacji wywoływane na . Można to zrobić w [witrynie Azure portal](https://portal.azure.com)lub po prostu uruchomić następujące polecenie w [usłudze Azure Cloud Shell:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Dostęp do oświadczeń użytkowników

Usługa app service przekazuje oświadczenia użytkowników do aplikacji przy użyciu specjalnych nagłówków. Żądania zewnętrzne nie mogą ustawić tych nagłówków, więc są one obecne tylko wtedy, gdy są ustawione przez usługę App Service. Niektóre przykładowe nagłówki obejmują:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Kod, który jest napisany w dowolnym języku lub ramach można uzyskać informacje, które potrzebuje z tych nagłówków. W przypadku aplikacji ASP.NET 4.6 **claimsprincipal** jest automatycznie ustawiany z odpowiednimi wartościami. ASP.NET Core nie udostępnia jednak oprogramowania pośredniczącego uwierzytelniania, które integruje się z oświadczeniami użytkowników usługi App Service. Aby uzyskać obejście, zobacz [MaximeRouiller.Azure.AppService.EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth).

Aplikacja może również uzyskać dodatkowe informacje na `/.auth/me`temat uwierzytelnionego użytkownika, dzwoniąc . Zestawy SDK serwera aplikacji mobilnych zapewniają metody pomocnicze do pracy z tymi danymi. Aby uzyskać więcej informacji, zobacz [Jak używać sdk węzła węzła azure mobile apps.js](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)i [pracować z zestawem SDK serwera wewnętrznej bazy danych .NET dla aplikacji mobilnych platformy Azure](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Pobieranie tokenów w kodzie aplikacji

Z kodu serwera tokeny specyficzne dla dostawcy są wstrzykiwane do nagłówka żądania, dzięki czemu można łatwo uzyskać do nich dostęp. W poniższej tabeli przedstawiono możliwe nazwy nagłówków tokenu:

| Dostawca | Nazwy nagłówków |
|-|-|
| Usługa Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Token Facebooka | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Konto Microsoft | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Z kodu klienta (takiego jak aplikacja mobilna lub javascript `GET` w `/.auth/me`przeglądarce) wyślij żądanie HTTP do . Zwrócony JSON ma tokeny specyficzne dla dostawcy.

> [!NOTE]
> Tokeny dostępu służą do uzyskiwania dostępu do zasobów dostawcy, więc są one obecne tylko wtedy, gdy skonfigurowano dostawcę z kluczem tajnym klienta. Aby zobaczyć, jak uzyskać tokeny odświeżania, zobacz Odśwież tokeny dostępu.

## <a name="refresh-identity-provider-tokens"></a>Odśwież tokeny dostawcy tożsamości

Po wygaśnięciu tokenu dostępu dostawcy (nie [tokenu sesji),](#extend-session-token-expiration-grace-period)należy ponownie uwierzytelnić użytkownika przed ponownym użyciem tego tokenu. Można uniknąć wygaśnięcia tokenu, `GET` wywołując `/.auth/refresh` punkt końcowy aplikacji. Po wywołaniu usługa App Service automatycznie odświeża tokeny dostępu w magazynie tokenów dla uwierzytelnionego użytkownika. Kolejne żądania tokenów według kodu aplikacji uzyskać odświeżone tokeny. Jednak aby odświeżanie tokenu działało, magazyn tokenów musi zawierać [tokeny odświeżania](https://auth0.com/learn/refresh-tokens/) dla dostawcy. Sposób uzyskania tokenów odświeżania są dokumentowane przez każdego dostawcę, ale poniższa lista jest krótkie podsumowanie:

- **Google**: Dołącz `access_type=offline` parametr ciągu `/.auth/login/google` zapytania do wywołania interfejsu API. W przypadku korzystania z SDK aplikacji mobilnych można `LogicAsync` dodać ten parametr do jednego z przeciążeń (patrz [Tokeny odświeżania Google](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: Nie zapewnia tokenów odświeżania. Długowieczne tokeny tracą ważność w ciągu 60 dni (patrz [Wygaśnięcie Facebooka i rozszerzenie tokenów dostępu).](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)
- **Twitter**: Tokeny dostępu nie wygasają (zobacz [Twitter OAuth FAQ](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Konto Microsoft:** Podczas [konfigurowania ustawień uwierzytelniania konta Microsoft](configure-authentication-provider-microsoft.md)wybierz `wl.offline_access` zakres.
- **Usługa Azure Active Directory**: W [https://resources.azure.com](https://resources.azure.com)obszarze wykonywania następujących czynności wykonaj następujące czynności:
    1. U góry strony wybierz pozycję **Odczyt/Zapis**.
    2. W lewej przeglądarce przejdź do **subskrypcji** > **_\<nazwa\__** > **sites** > **_\<\_ _** > **_\<\_\_ _** > subskrypcji**resourceGroups**nazwa grupy zasobów> >  **dostawców** > **Microsoft.Web**sites nazwa aplikacji> >  **konfiguracje** > **authsettings**. 
    3. Kliknij pozycję **Edytuj**.
    4. Zmodyfikuj następującą właściwość. Zamień _ \<identyfikator aplikacji\_>_ identyfikatorem aplikacji usługi Azure Active Directory usługi, do której chcesz uzyskać dostęp.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Kliknij **pozycję Umieść**. 

Po skonfigurowaniu dostawcy można [znaleźć token odświeżania i czas wygaśnięcia tokenu dostępu](#retrieve-tokens-in-app-code) w magazynie tokenów. 

Aby odświeżyć token dostępu w `/.auth/refresh` dowolnym momencie, wystarczy zadzwonić w dowolnym języku. Poniższy fragment kodu używa jQuery do odświeżenia tokenów dostępu z klienta JavaScript.

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

Jeśli użytkownik odwoła uprawnienia przyznane aplikacji, wywołanie może `/.auth/me` zakończyć `403 Forbidden` się niepowodzeniem z odpowiedzią. Aby zdiagnozować błędy, sprawdź dzienniki aplikacji, aby uzyskać szczegółowe informacje.

## <a name="extend-session-token-expiration-grace-period"></a>Przedłużenie okresu prolongaty wygaśnięcia tokenu sesji

Uwierzytelniona sesja wygasa po 8 godzinach. Po wygaśnięciu uwierzytelnionej sesji domyślnie występuje 72-godzinny okres prolongaty. W tym okresie prolongaty możesz odświeżyć token sesji za pomocą usługi App Service bez ponownego uwierzytelniania użytkownika. Możesz po `/.auth/refresh` prostu wywołać, gdy token sesji staje się nieprawidłowy i nie musisz samodzielnie śledzić wygaśnięcia tokenu. Po upływie 72-godzinnego okresu prolongaty użytkownik musi zalogować się ponownie, aby uzyskać prawidłowy token sesji.

Jeśli 72 godziny to za mało czasu, możesz przedłużyć to okno wygaśnięcia. Przedłużenie wygaśnięcia w długim okresie może mieć istotne konsekwencje dla bezpieczeństwa (na przykład gdy token uwierzytelniania zostanie przeciekły lub skradziony). Dlatego należy pozostawić go w domyślnym 72 godzin lub ustawić okres rozszerzenia na najmniejszą wartość.

Aby rozszerzyć domyślne okno wygaśnięcia, uruchom następujące polecenie w [usłudze Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Okres prolongaty dotyczy tylko usługi app service uwierzytelnionej sesji, a nie tokeny od dostawców tożsamości. Nie ma okresu prolongaty dla wygasłych tokenów dostawcy. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Ograniczanie domeny kont logowania

Zarówno konto Microsoft, jak i usługa Azure Active Directory umożliwiają logowanie się z wielu domen. Na przykład konto Microsoft umożliwia _outlook.com_kont _live.com_i _hotmail.com._ Usługa Azure AD umożliwia dowolną liczbę domen niestandardowych dla kont logowania. Warto jednak przyspieszyć użytkowników bezpośrednio do własnej, markowej strony logowania usługi `contoso.com`Azure AD (np. Aby zasugerować nazwę domeny kont logowania, wykonaj następujące kroki.

W [https://resources.azure.com](https://resources.azure.com)programie Przejdź do pozycji Nazwa**sites** >  **subscriptions** > **_\<\_subskrypcji_** >  > **resourceGroups**  >  **providers** > **_\<\_nazwa grupy\_zasobów>_** dostawców Nazwy**_\<aplikacji\_ _****Witryny firmy Microsoft.Web** > > > **konfiguracje** **config** > . 

Kliknij **pozycję Edytuj**, zmodyfikuj następującą właściwość, a następnie kliknij przycisk **Umieść**. Pamiętaj, aby _ \<\__ zastąpić nazwę domeny>odpowiednią domeną.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

To ustawienie dołącza `domain_hint` parametr ciągu zapytania do adresu URL przekierowania logowania. 

> [!IMPORTANT]
> Klient może usunąć `domain_hint` parametr po otrzymaniu adresu URL przekierowania, a następnie zalogować się przy innej domenie. Tak więc, chociaż ta funkcja jest wygodna, nie jest to funkcja zabezpieczeń.
>

## <a name="authorize-or-deny-users"></a>Autoryzowanie lub odmawianie użytkownikom

Usługa App Service zajmuje się najprostszym przypadkiem autoryzacji (tj. odrzucaniem nieuwierzytezowanych żądań), ale aplikacja może wymagać bardziej szczegółowych zachowań autoryzacji, takich jak ograniczanie dostępu tylko do określonej grupy użytkowników. W niektórych przypadkach należy napisać kod aplikacji niestandardowej, aby zezwolić lub odmówić dostępu do zalogowany użytkownik. W innych przypadkach usługa App Service lub dostawca tożsamości mogą być w stanie pomóc bez konieczności zmiany kodu.

- [Poziom serwera](#server-level-windows-apps-only)
- [Poziom dostawcy tożsamości](#identity-provider-level)
- [Poziom aplikacji](#application-level)

### <a name="server-level-windows-apps-only"></a>Poziom serwera (tylko aplikacje systemu Windows)

W przypadku dowolnej aplikacji systemu Windows można zdefiniować zachowanie autoryzacji serwera sieci Web usług IIS, edytując plik *Web.config.* Aplikacje systemu Linux nie korzystają z usług IIS i nie można ich skonfigurować za pośrednictwem *pliku Web.config*.

1. Przejdź do`https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. W eksploratorze przeglądarki plików usługi App Service przejdź do *witryny/wwwroot*. Jeśli *plik Web.config* nie istnieje, utwórz **+**  > go, wybierając pozycję **Nowy plik**. 

1. Wybierz ołówek dla *witryny Web.config,* aby go edytować. Dodaj następujący kod konfiguracji i kliknij przycisk **Zapisz**. Jeśli *Web.config* już istnieje, `<authorization>` wystarczy dodać element ze wszystkim w nim. Dodaj konta, na które `<allow>` chcesz zezwolić w elemencie.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>Poziom dostawcy tożsamości

Dostawca tożsamości może zapewnić pewne autoryzacje pod klucz. Przykład:

- W przypadku [usługi Azure App Service](configure-authentication-provider-aad.md)możesz zarządzać [dostępem na poziomie przedsiębiorstwa](../active-directory/manage-apps/what-is-access-management.md) bezpośrednio w usłudze Azure AD. Aby uzyskać instrukcje, zobacz [Jak usunąć dostęp użytkownika do aplikacji](../active-directory/manage-apps/methods-for-removing-user-access.md).
- W przypadku [Google](configure-authentication-provider-google.md)projekty interfejsu API Google należące do [organizacji](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) można skonfigurować tak, aby zezwalały na dostęp tylko użytkownikom w organizacji (zobacz [Strona pomocy technicznej Google **Konfigurowanie OAuth 2.0).** ](https://support.google.com/cloud/answer/6158849?hl=en)

### <a name="application-level"></a>Poziom aplikacji

Jeśli którykolwiek z pozostałych poziomów nie zapewnia wymaganej autoryzacji lub jeśli platforma lub dostawca tożsamości nie jest obsługiwany, należy napisać niestandardowy kod, aby autoryzować użytkowników na podstawie [oświadczeń użytkownika.](#access-user-claims)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Uwierzytelnij i autoryzuj użytkowników end-to-end (Windows)](app-service-web-tutorial-auth-aad.md)
> [Tutorial: Uwierzytelnij i autoryzuj użytkowników end-to-end (Linux)](containers/tutorial-auth-aad.md)
