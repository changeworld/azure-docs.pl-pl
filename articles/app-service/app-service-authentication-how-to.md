---
title: Dostosowywanie uwierzytelnianie i autoryzację w usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Pokazuje, jak dostosować uwierzytelniania i autoryzacji w usłudze App Service i Pobierz oświadczenia użytkowników i różnych tokenów.
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
ms.date: 03/14/2018
ms.author: cephalin
ms.openlocfilehash: 10decbd5291e2054e373bfef266b64eae36ea1cf
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="customize-authentication-and-authorization-in-azure-app-service"></a>Dostosowywanie uwierzytelnianie i autoryzację w usłudze Azure App Service

W tym artykule przedstawiono sposób dostosowywania [uwierzytelniania i autoryzacji w usłudze App Service](app-service-authentication-overview.md)i zarządzania tożsamościami z poziomu aplikacji. 

Aby szybko rozpocząć pracę, zobacz jedną z następujących samouczków:

* [Samouczek: Uwierzytelniania i autoryzacji użytkowników end-to-end w usłudze Azure App Service](app-service-web-tutorial-auth-aad.md)
* [Jak skonfigurować aplikację do używania logowania w usłudze Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md)
* [Jak skonfigurować aplikację do używania logowania usługi Facebook](app-service-mobile-how-to-configure-facebook-authentication.md)
* [Jak skonfigurować aplikację do używania logowania usługi Google](app-service-mobile-how-to-configure-google-authentication.md)
* [Jak skonfigurować aplikację do używania logowania za pomocą konta Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md)
* [Jak skonfigurować aplikację do używania logowania usługi Twitter](app-service-mobile-how-to-configure-twitter-authentication.md)

## <a name="configure-multiple-sign-in-options"></a>Skonfiguruj wiele opcji logowania

Konfiguracja portalu nie oferuje sposób gotowe do prezentowania wiele opcji logowania użytkowników (takich jak Facebook i Twitter). Jednak nie jest trudne dodać funkcje do aplikacji sieci web. Kroki przedstawione poniżej:

Pierwszy w **uwierzytelniania / autoryzacji** w portalu Azure, skonfiguruj każdy dostawca tożsamości, aby umożliwić.

W **działania należy podjąć w przypadku nieuwierzytelnionego żądania**, wybierz pozycję **Zezwalaj na anonimowe żądania (żadnej akcji)**.

Strona logowania lub na pasku nawigacyjnym lub w innej lokalizacji aplikacji sieci web, dodanie logowania łącza do każdego z dostawców włączono (`/.auth/login/<provider>`). Na przykład:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoft">Log in with Microsoft Account</a> 
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Gdy użytkownik kliknie na jeden z linków, odpowiednich strony logowania zostanie otwarty do logowania użytkownika.

## <a name="access-user-claims"></a>Dostęp do oświadczeń użytkowników

Usługa aplikacji przekazuje oświadczeń użytkowników do aplikacji za pomocą specjalnych nagłówków. Zewnętrzne żądania nie są dozwolone można ustawić te nagłówki, dzięki czemu są one obecne tylko wtedy, gdy ustawiony przez usługę App Service. Niektóre nagłówki przykład obejmują:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Kod napisany w dowolnego języka lub platformy można uzyskać informacji wymaganych z tych nagłówków. Dla aplikacji platformy ASP.NET 4.6 **ClaimsPrincipal** jest ustawiany automatycznie z odpowiednimi wartościami.

Aplikację można również uzyskać więcej informacji na temat uwierzytelnionego użytkownika przez wywołanie metody `/.auth/me`. Serwer Mobile Apps SDK zapewnia metody pomocnicze do pracy z tych danych. Aby uzyskać więcej informacji, zobacz [sposobu korzystania z zestawem Azure Mobile Apps Node.js SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity), i [pracować z serwera wewnętrznej bazy danych .NET SDK usługi Azure Mobile Apps](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Pobranie tokenów w kodzie aplikacji

W kodzie serwera specyficznego dla dostawcy tokenów są wstrzykiwane do nagłówka żądania, użytkownik może łatwo uzyskiwać do nich dostęp. W poniższej tabeli przedstawiono możliwe tokenu nagłówka nazwy:

| | |
|-|-|
| Usługa Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Token usługi Facebook | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Konto Microsoft | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

W kodzie klienta (na przykład aplikacji mobilnej lub JavaScript w przeglądarce) wysyłania HTTP `GET` żądanie `/.auth/me`. Zwrócone dane JSON ma tokenów specyficznego dla dostawcy.

> [!NOTE]
> Tokeny dostępu są do uzyskiwania dostępu do dostawcy zasobów, dzięki czemu są one występuje tylko w przypadku konfiguracji dostawcy usługi przy użyciu klucza tajnego klienta. Aby zobaczyć, jak uzyskać tokeny odświeżania, zobacz [tokenów dostępu odświeżania](#refresh-access-tokens).

## <a name="refresh-access-tokens"></a>Odśwież tokeny dostępu

Po wygaśnięciu tokenu dostępu użytkownika dostawcy musisz ponownego uwierzytelnienia użytkownika. Można uniknąć wygaśnięcia tokenu dokonując `GET` wywołanie `/.auth/refresh` punkt końcowy aplikacji. Po wywołaniu usługi aplikacji — automatycznie odświeża tokenów dostępu w magazynie tokenu dla tego uwierzytelnionego użytkownika. Kolejnych żądań tokenów przez kod aplikacji uzyskiwać tokeny odświeżony. Jednak dla tokenu odświeżania, aby pracować, Magazyn tokenu musi zawierać [tokenów odświeżania](https://auth0.com/learn/refresh-tokens/) dla dostawcy. Opisano sposób uzyskiwania tokenów odświeżania przez każdego dostawcy, ale poniżej przedstawiono krótkie podsumowanie:

- **Google**: Dołącz `access_type=offline` parametr ciągu do zapytania z `/.auth/login/google` wywołanie interfejsu API. Jeśli przy użyciu zestawu SDK aplikacji mobilnych, możesz dodać parametr do jednego z `LogicAsync` przeciążenia (zobacz [tokeny odświeżania usługi Google](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: nie zapewnia tokenów odświeżania. Tokeny długotrwałe wygaśnie w ciągu 60 dni (zobacz [wygaśnięcia Facebook i rozszerzenia tokenów dostępu](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **W usłudze Twitter**: tokenów dostępu nie wygasa (zobacz [— często zadawane pytania OAuth Twitter](https://developer.twitter.com/docs/basics/authentication/guides/oauth-faq)).
- **Account Microsoft**: gdy [ustawienia uwierzytelniania konta Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md), wybierz pozycję `wl.offline_access` zakresu.
- **Usługa Azure Active Directory**: W [ https://resources.azure.com ](https://resources.azure.com), wykonaj następujące czynności:
    1. W górnej części strony, wybierz **odczytu/zapisu**.
    1. W przeglądarce po lewej stronie, przejdź do **subskrypcje** > **_\<subskrypcji\_nazwa_**   >  **resourceGroups** > _**\<zasobów\_grupy\_name >**_   >  **dostawców** > **Microsoft.Web** > **witryny** > _**\<aplikacji \_name >**_ > **config** > **authsettings**. 
    1. Kliknij pozycję **Edytuj**.
    1. Zmodyfikuj następujące właściwości. Zastąp  _\<aplikacji\_id >_ identyfikator aplikacji usługi Azure Active Directory ma dostęp do usługi.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    1. Kliknij przycisk **Put**. 

Po skonfigurowaniu dostawcy można sprawdzić, czy tokeny odświeżania są w magazynie tokenu, wywołując `/.auth/me`. 

Aby odświeżyć tokenu dostępu w dowolnym momencie, po prostu Wywołaj `/.auth/refresh` w dowolnym języku. Poniższy fragment kodu używa jQuery odświeżyć tokenów dostępu z klienta języka JavaScript.

```JavaScript
function refreshTokens() {
  var refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Jeśli użytkownik odwołuje uprawnienia do aplikacji, wywołania do `/.auth/me` może zakończyć się niepowodzeniem z `403 Forbidden` odpowiedzi. Aby diagnozowanie błędów, sprawdź szczegóły w dziennikach aplikacji.

## <a name="extend-session-expiration-grace-period"></a>Przedłużyć okres prolongaty wygaśnięcia sesji

Po wygaśnięciu uwierzytelnionej sesji jest domyślnie okres prolongaty 72 godziny. W ramach tego okresu prolongaty możesz odświeżyć plik cookie sesji lub tokenu sesji z usługi aplikacji bez ponownego uwierzytelniania użytkownika. Można wywołać `/.auth/refresh` po z pliku cookie sesji lub tokenu sesji staje się nieprawidłowa i nie trzeba samodzielnie śledzić wygaśnięcia tokenu. Okres prolongaty 72 godziny po upłynięcia użytkownika należy się zalogować w ponownie Pobierz plik cookie sesji prawidłowe lub tokenu sesji.

Jeśli 72 godziny nie jest wystarczająco dużo czasu, można rozszerzyć tego okna wygaśnięcia. Rozszerzanie wygaśnięcia w długim okresie może mieć wpływ zabezpieczeń (na przykład jeśli tokenu uwierzytelniania jest ujawnione lub kradzieży). Należy więc pozostaw wartość domyślną 72 godzin lub Ustaw okres rozszerzenia na najmniejszą wartość.

Aby rozszerzyć domyślnego okna wygaśnięcia, uruchom następujące polecenie [powłoki chmury](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Okres prolongaty ma zastosowanie tylko do sesji usługi aplikacji uwierzytelniony, nie tokeny od dostawców tożsamości. Brak okres prolongaty nie wygasły dostawcy tokenów. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Limit domeny kont logowania

Account Microsoft wraz z usługą Azure Active Directory pozwala zalogować się w kilku domenach. Na przykład umożliwia Account Microsoft _outlook.com_, _live.com_, i _hotmail.com_ kont. Usługa Azure Active Directory umożliwia dowolną liczbę domen niestandardowych dla kont logowania. To działanie może być niepożądane wewnętrznych aplikacji, której nie chcesz, każda osoba mająca _outlook.com_ konto dostępu. Aby ograniczyć nazwę domeny konta logowania, wykonaj następujące kroki.

W [ https://resources.azure.com ](https://resources.azure.com), przejdź do **subskrypcje** > **_\<subskrypcji\_nazwa_**   >  **resourceGroups** > _**\<zasobów\_grupy\_name >**_   >  **dostawców** > **Microsoft.Web** > **witryny**  >    _**\<aplikacji\_name >**_ > **config** > **authsettings**. 

Kliknij przycisk **Edytuj**, zmodyfikuj następującą właściwość, a następnie kliknij przycisk **Put**. Pamiętaj zastąpić  _\<domeny\_name >_ z domeny ma.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```
## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek: Uwierzytelniania i autoryzacji użytkowników end-to-end](app-service-web-tutorial-auth-aad.md)
