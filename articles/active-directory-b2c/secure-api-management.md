---
title: Zabezpieczanie interfejsu API usługi Azure API przy użyciu usługi Azure Active Directory B2C
description: Dowiedz się, jak używać tokenów dostępu wystawionych przez usługę Azure Active Directory B2C w celu zabezpieczenia punktu końcowego interfejsu API usługi Azure API.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 00938d831e70289b24acb599b81016aa6e564d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186934"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Zabezpieczanie interfejsu API usługi Azure API za pomocą usługi Azure AD B2C

Dowiedz się, jak ograniczyć dostęp do interfejsu APIM usługi Azure APIM do klientów uwierzytelnionych za pomocą usługi Azure Active Directory B2C (Azure AD B2C). Wykonaj kroki opisane w tym artykule, aby utworzyć i przetestować zasady przychodzące w interfejsie APIM, która ogranicza dostęp tylko do tych żądań, które zawierają prawidłowy token dostępu wystawiony przez usługę Azure AD B2C.

## <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem czynności określonych w tym artykule potrzebne są następujące zasoby:

* [Dzierżawa usługi Azure AD B2C](tutorial-create-tenant.md)
* [Aplikacja zarejestrowana](tutorial-register-applications.md) w dzierżawie
* [Przepływy użytkownika utworzone](tutorial-create-user-flows.md) w dzierżawie
* [Opublikowany interfejs API](../api-management/import-and-publish.md) w usłudze Azure API Management
* [Listonosz](https://www.getpostman.com/) do testowania zabezpieczonego dostępu (opcjonalnie)

## <a name="get-azure-ad-b2c-application-id"></a>Uzyskaj identyfikator aplikacji usługi Azure AD B2C

Po zabezpieczeniu interfejsu API w usłudze Azure API Management za pomocą usługi Azure AD B2C, potrzebujesz kilku wartości dla [zasad ruchu przychodzącego,](../api-management/api-management-howto-policies.md) które można utworzyć w usłudze APIM. Najpierw należy zarejestrować identyfikator aplikacji utworzonej wcześniej w dzierżawie usługi Azure AD B2C. Jeśli używasz aplikacji utworzonej w wymaganiach wstępnych, użyj identyfikatora aplikacji dla *webbapp1*.

Aby uzyskać identyfikator aplikacji, można użyć bieżącego środowiska **aplikacji** lub naszego nowego środowiska **rejestracji aplikacji (Wersja zapoznawcza).** [Dowiedz się więcej na temat nowego środowiska](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz filtr **subskrypcja katalog +** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Możesz też wybrać **wszystkie usługi** i wyszukać i wybrać pozycję Azure **AD B2C**.
1. W obszarze **Zarządzanie**wybierz pozycję **Aplikacje**.
1. Zapisz wartość w kolumnie **Identyfikator aplikacji** dla *aplikacji webapp1* lub innej aplikacji, która została wcześniej utworzona.

#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz filtr **subskrypcja katalog +** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Możesz też wybrać **wszystkie usługi** i wyszukać i wybrać pozycję Azure **AD B2C**.
1. Wybierz **pozycję Rejestracje aplikacji (Wersja zapoznawcza),** a następnie wybierz kartę **Posiadane aplikacje.**
1. Zapisz wartość w kolumnie **Identyfikator aplikacji (klienta)** dla *aplikacji webapp1* lub innej aplikacji, która została wcześniej utworzona.

* * *

## <a name="get-token-issuer-endpoint"></a>Pobierz punkt końcowy wystawcy tokenu

Następnie pobierz dobrze znany adres URL konfiguracji dla jednego z przepływów użytkownika usługi Azure AD B2C. Potrzebujesz również identyfikatora URI punktu końcowego wystawcy tokenu, który chcesz obsługiwać w usłudze Azure API Management.

1. Przejdź do dzierżawy usługi Azure AD B2C w [witrynie Azure portal.](https://portal.azure.com)
1. W obszarze **Zasady**wybierz **pozycję Przepływy użytkownika (zasady)**.
1. Wybierz istniejącą zasadę, na przykład *B2C_1_signupsignin1*, a następnie wybierz pozycję **Uruchom przepływ użytkownika**.
1. Nagraj adres URL w hiperłączu wyświetlany w nagłówku **Przepływ użytkownika Uruchom** u góry strony. Ten adres URL jest openid connect dobrze znany punkt końcowy odnajdywania dla przepływu użytkownika i używasz go w następnej sekcji podczas konfigurowania zasad ruchu przychodzącego w usłudze Azure API Management.

    ![Dobrze znane hiperłącze identyfikatora URI na stronie Uruchom teraz w witrynie Azure portal](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Wybierz hiperłącze, aby przejść do dobrze znanej strony konfiguracji OpenID Connect.
1. Na stronie, która zostanie otwarta `issuer` w przeglądarce, zapisz wartość, na przykład:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    Ta wartość jest używana w następnej sekcji podczas konfigurowania interfejsu API w usłudze Azure API Management.

Teraz powinny być rejestrowane dwa adresy URL do użycia w następnej sekcji: adres URL punktu końcowego openid connect dobrze znany i identyfikator URI wystawcy. Przykład:

```
https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1
https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Konfigurowanie zasad ruchu przychodzącego w usłudze Azure API Management

Teraz możesz dodać zasady przychodzące w usłudze Azure API Management, która sprawdza poprawność wywołań interfejsu API. Dodając zasady [sprawdzania poprawności JWT,](../api-management/api-management-access-restriction-policies.md#ValidateJWT) która weryfikuje odbiorców i wystawcy w tokenie dostępu, można zapewnić, że akceptowane są tylko wywołania interfejsu API z prawidłowym tokenem.

1. Przejdź do wystąpienia usługi Azure API Management w [witrynie Azure portal](https://portal.azure.com).
1. Wybierz pozycję **Interfejsy API**.
1. Wybierz interfejs API, który chcesz zabezpieczyć za pomocą usługi Azure AD B2C.
1. Wybierz kartę **Projekt**.
1. W obszarze Przetwarzanie ** \< / ** **przychodzące**wybierz, aby otworzyć edytor kodu zasad.
1. Umieść następujący `<validate-jwt>` znacznik `<inbound>` wewnątrz zasad.

    1. `url` Zaktualizuj `<openid-config>` wartość w elemencie za pomocą dobrze znanego adresu URL konfiguracji zasad.
    1. Zaktualizuj `<audience>` element za pomocą identyfikatora aplikacji utworzonej wcześniej w dzierżawie B2C (na przykład *webapp1).*
    1. Zaktualizuj `<issuer>` element za pomocą punktu końcowego wystawcy tokenu, który został wcześniej zarejestrowany.

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>Sprawdzanie poprawności bezpiecznego dostępu do interfejsu API

Aby upewnić się, że tylko uwierzytelnione wywołujące mogą uzyskać dostęp do interfejsu API, można sprawdzić poprawność konfiguracji usługi Azure API Management, wywołując interfejs API za pomocą [programu Postman](https://www.getpostman.com/).

Aby wywołać interfejs API, potrzebujesz zarówno tokenu dostępu wystawionego przez usługę Azure AD B2C, jak i klucza subskrypcji interfejsu APIM.

### <a name="get-an-access-token"></a>Pobranie tokenu dostępu

Najpierw potrzebujesz tokenu wystawionego przez usługę Azure AD `Authorization` B2C do użycia w nagłówku w postmanie. Można go uzyskać za pomocą funkcji **Uruchom teraz** przepływu użytkownika rejestracji/logowania, który powinien zostać utworzony jako jeden z wymagań wstępnych.

1. Przejdź do dzierżawy usługi Azure AD B2C w [witrynie Azure portal.](https://portal.azure.com)
1. W obszarze **Zasady**wybierz **pozycję Przepływy użytkownika (zasady)**.
1. Wybierz istniejący przepływ użytkownika rejestracji/logowania, na przykład *B2C_1_signupsignin1*.
1. W przypadku **aplikacji**wybierz *webapp1*.
1. W przypadku adresu `https://jwt.ms`URL **odpowiedzi**wybierz opcję .
1. Wybierz **pozycję Uruchom przepływ użytkownika**.

    ![Uruchom stronę przepływu użytkownika, aby zarejestrować się w przepływie użytkowników w witrynie Azure portal](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Zakończ proces logowania. Powinieneś zostać przekierowany do `https://jwt.ms`.
1. Zapisz zakodowaną wartość tokenu wyświetlaną w przeglądarce. Ta wartość tokenu jest używana dla nagłówka Autoryzacja w postmanie.

    ![Zakodowana wartość tokenu wyświetlana na jwt.ms](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>Pobierz klucz subskrypcji interfejsu API

Aplikacja kliencka (w tym przypadku Postman), która wywołuje opublikowany interfejs API, musi zawierać prawidłowy klucz subskrypcji usługi API Management w żądaniach HTTP do interfejsu API. Aby uzyskać klucz subskrypcji do uwzględnienia w żądaniu HTTP listonosza:

1. Przejdź do wystąpienia usługi Azure API Management w [witrynie Azure portal](https://portal.azure.com).
1. Wybierz pozycję **Subskrypcje**.
1. Wybierz wielokropek dla **produktu: Nieograniczony**, a następnie wybierz **pokaż /ukryj klawisze**.
1. Zapisz **klucz podstawowy** dla produktu. Ten klucz jest `Ocp-Apim-Subscription-Key` używany dla nagłówka w żądaniu HTTP w postman.

![Strona klucza subskrypcji z wybranymi kluczami Pokaż/ukryj w witrynie Azure Portal](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Testowanie bezpiecznego wywołania interfejsu API

Po zarejestrować token dostępu i klucz subskrypcji INTERFEJSU APIM, możesz teraz sprawdzić, czy poprawnie skonfigurowano bezpieczny dostęp do interfejsu API.

1. Utwórz `GET` nowe żądanie w [postman](https://www.getpostman.com/). Dla adresu URL żądania określ punkt końcowy listy prelegentów interfejsu API opublikowanego jako jeden z wymagań wstępnych. Przykład:

    `https://contosoapim.azure-api.net/conference/speakers`

1. Następnie dodaj następujące nagłówki:

    | Klucz | Wartość |
    | --- | ----- |
    | `Authorization` | Zakodowana wartość tokenu, którą wcześniej `Bearer ` zarejestrowałeś, poprzedzone (uwzględnij spację po "Nośniku") |
    | `Ocp-Apim-Subscription-Key` | Klucz subskrypcji APIM zarejestrowany wcześniej |

    Adres URL żądania **GET** i **nagłówki** powinny być podobne do:

    ![Interfejs użytkownika poczty z adresem URL żądania GET i nagłówkami](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Wybierz przycisk **Wyślij** w postman, aby wykonać żądanie. Jeśli wszystko jest poprawnie skonfigurowane, powinieneś przedstawić odpowiedź JSON z kolekcją prelegentów konferencji (pokazaną tutaj obciętą):

    ```JSON
    {
      "collection": {
        "version": "1.0",
        "href": "https://conferenceapi.azurewebsites.net:443/speakers",
        "links": [],
        "items": [
          {
            "href": "https://conferenceapi.azurewebsites.net/speaker/1",
            "data": [
              {
                "name": "Name",
                "value": "Scott Guthrie"
              }
            ],
            "links": [
              {
                "rel": "http://tavis.net/rels/sessions",
                "href": "https://conferenceapi.azurewebsites.net/speaker/1/sessions"
              }
            ]
          },
    [...]
    ```

### <a name="test-an-insecure-api-call"></a>Testowanie niezabezpieczonych wywołań interfejsu API

Teraz, gdy zostało wykonane pomyślne żądanie, przetestuj przypadek awarii, aby upewnić się, że wywołania interfejsu API z *nieprawidłowym* tokenem zostaną odrzucone zgodnie z oczekiwaniami. Jednym ze sposobów wykonania testu jest dodanie lub zmiana kilku znaków w `GET` wartości tokenu, a następnie wykonanie tego samego żądania, co poprzednio.

1. Dodaj kilka znaków do wartości tokenu, aby symulować nieprawidłowy token. Na przykład dodaj "INVALID" do wartości tokenu:

    ![Sekcja Nagłówki interfejsu użytkownika listonosza z nieprawidłowym dodatkiem do tokenu](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. Wybierz przycisk **Wyślij,** aby wykonać żądanie. Z nieprawidłowym tokenem oczekiwany `401` wynik to nieautoryzowany kod stanu:

    ```JSON
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Jeśli zostanie `401` wyświetlony kod stanu, zweryfikowano, że tylko osoby wywołujące z prawidłowym tokenem dostępu wystawionym przez usługę Azure AD B2C mogą składać pomyślne żądania do interfejsu API usługi Azure API Management.

## <a name="support-multiple-applications-and-issuers"></a>Obsługa wielu aplikacji i wystawców

Kilka aplikacji zazwyczaj współdziałają z jednym interfejsem API REST. Aby włączyć interfejs API do akceptowania tokenów przeznaczonych dla `<audiences>` wielu aplikacji, dodaj ich identyfikatory aplikacji do elementu w zasadach przychodzących interfejsu APIM.

```XML
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

Podobnie do obsługi wielu wystawców tokenu, dodaj ich `<issuers>` identyfikatory URI punktu końcowego do elementu w zasadach przychodzących interfejsu APIM.

```XML
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>Migracja do b2clogin.com

Jeśli masz apim API, który sprawdza poprawność `login.microsoftonline.com` tokenów wystawionych przez starszy punkt końcowy, należy przeprowadzić migrację interfejsu API i aplikacji, które go wywołują, aby używać tokenów wystawionych przez [b2clogin.com](b2clogin.md).

Można wykonać ten ogólny proces, aby przeprowadzić migrację etapową:

1. Dodaj obsługę w zasadach przychodzących interfejsu APIM dla tokenów wystawionych zarówno przez b2clogin.com, jak i login.microsoftonline.com.
1. Aktualizowanie aplikacji po jednym na raz, aby uzyskać tokeny z punktu końcowego b2clogin.com.
1. Gdy wszystkie aplikacje są poprawnie uzyskiwania tokenów z b2clogin.com, usuń obsługę tokenów wydanych login.microsoftonline.com z interfejsu API.

Poniższe przykładowe zasady przychodzące interfejsu APIM ilustrują sposób akceptowania tokenów wystawionych przez b2clogin.com i login.microsoftonline.com. Ponadto obsługuje żądania interfejsu API z dwóch aplikacji.

```XML
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
            </issuers>
        </validate-jwt>
        <base />
    </inbound>
    <backend> <base /> </backend>
    <outbound> <base /> </outbound>
    <on-error> <base /> </on-error>
</policies>
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje na temat zasad usługi Azure API Management, zobacz [indeks odwołania do zasad interfejsu APIM](../api-management/api-management-policies.md).

Informacje na temat migracji internetowych interfejsów API opartych na OWIN i ich aplikacji do b2clogin.com można znaleźć w aplikacji [Migrate an OWIN-based web API to b2clogin.com](multiple-token-endpoints.md).
