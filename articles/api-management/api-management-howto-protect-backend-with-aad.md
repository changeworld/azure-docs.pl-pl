---
title: Ochrona interfejsu API przy użyciu funkcji OAuth 2.0 za pomocą usługi AAD i zarządzania interfejsami API
titleSuffix: Azure API Management
description: Dowiedz się, jak chronić zaplecze interfejsu API sieci Web za pomocą usługi Azure Active Directory i usługi API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/21/2019
ms.author: apimpm
ms.openlocfilehash: 300f44daeeea5e8a774575dabcb00686906bb5de
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804371"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Ochrona interfejsu API przy użyciu protokołu OAuth 2.0 za pomocą usługi Azure Active Directory i usługi API Management

W tym przewodniku pokazano, jak skonfigurować wystąpienie usługi Azure API Management w celu ochrony interfejsu API przy użyciu protokołu OAuth 2.0 z usługą Azure Active Directory (Azure AD). 

> [!NOTE]
> Ta funkcja jest dostępna w warstwach **Deweloper,** **Standard** i **Premium** zarządzania interfejsami API.

## <a name="prerequisites"></a>Wymagania wstępne
Aby wykonać kroki opisane w tym artykule, musisz mieć:
* Wystąpienie usługi API Management
* Publikowany interfejs API, który używa wystąpienia usługi API Management
* Dzierżawa usługi Azure AD

## <a name="overview"></a>Omówienie

Oto krótki przegląd kroków:

1. Zarejestruj aplikację (aplikację zaplecza) w usłudze Azure AD do reprezentowania interfejsu API.
2. Zarejestruj inną aplikację (aplikację kliencką) w usłudze Azure AD, aby reprezentować aplikację kliencką, która musi wywołać interfejs API.
3. W usłudze Azure AD udziel uprawnień, aby umożliwić aplikacji klienckiej do wywołania aplikacji wewnętrznej bazy danych.
4. Skonfiguruj konsolę deweloperów, aby wywołać interfejs API przy użyciu autoryzacji użytkownika OAuth 2.0.
5. Dodaj zasady **validate-jwt,** aby sprawdzić poprawność tokenu OAuth dla każdego żądania przychodzącego.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Rejestrowanie aplikacji w usłudze Azure AD w celu reprezentowania interfejsu API

Aby chronić interfejs API za pomocą usługi Azure AD, pierwszym krokiem jest zarejestrowanie aplikacji w usłudze Azure AD reprezentującej interfejs API. 

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby zarejestrować aplikację. Wyszukaj i wybierz **rejestracje aplikacji**.

1. Wybierz **pozycję Nowa rejestracja**. 

1. Po **wyświetleniu strony Zarejestruj zgłoszenie** wprowadź informacje rejestracyjne aplikacji: 
    - W sekcji **Nazwa** wprowadź znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład *aplikacja zaplecza*. 
    - W sekcji **Obsługiwane typy kont** wybierz opcję, która pasuje do Twojego scenariusza. 

1. Pozostaw sekcję **Przekierowanie identyfikatora URI** pustą.

1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację. 

1. Na stronie **Przegląd** aplikacji znajdź wartość **identyfikatora aplikacji (klienta)** i zarejestruj ją na później.

1. Wybierz **opcję Uwidaczniaj interfejs API** i ustaw identyfikator **URI identyfikatora aplikacji** z wartością domyślną. Zapisz tę wartość na później.

1. Wybierz przycisk **Dodaj zakres,** aby wyświetlić stronę **Dodaj zakres.** Następnie utwórz nowy zakres, który jest obsługiwany przez `Files.Read`interfejs API (na przykład ). Na koniec wybierz przycisk **Dodaj zakres,** aby utworzyć zakres. Powtórz ten krok, aby dodać wszystkie zakresy obsługiwane przez interfejs API.

1. Po utworzeniu zakresów zanotuj je do użycia w kolejnym kroku. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Rejestrowanie innej aplikacji w usłudze Azure AD w celu reprezentowania aplikacji klienckiej

Każda aplikacja kliencka, która wywołuje interfejs API musi być zarejestrowana jako aplikacja w usłudze Azure AD, jak również. W tym przykładzie aplikacja kliencka jest Konsola deweloperów w portalu dewelopera zarządzania interfejsami API. Poniżej opisano, jak zarejestrować inną aplikację w usłudze Azure AD do reprezentowania Konsoli dewelopera.

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby zarejestrować aplikację. Wyszukaj i wybierz **rejestracje aplikacji**.

1. Wybierz **pozycję Nowa rejestracja**.

1. Po **wyświetleniu strony Zarejestruj zgłoszenie** wprowadź informacje rejestracyjne aplikacji: 
    - W sekcji **Nazwa** wprowadź znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład *aplikacja klient-aplikacja*. 
    - W sekcji **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym (dowolny katalog usługi Azure AD — Multitenant)**. 

1. W sekcji **Przekierowanie identyfikatora URI** wybierz `Web` i wprowadź adres URL `https://contoso5.portal.azure-api.net/signin`.

1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację. 

1. Na stronie **Przegląd** aplikacji znajdź wartość **identyfikatora aplikacji (klienta)** i zarejestruj ją na później.

Teraz utwórz klucz tajny klienta dla tej aplikacji do użycia w następnym kroku.

1. Z listy stron aplikacji klienckiej wybierz pozycję **Certyfikaty & wpisy tajne**i wybierz pozycję **Nowy klucz tajny klienta**.

1. W obszarze **Dodaj klucz tajny klienta**podaj **opis**. Wybierz, kiedy klucz ma wygasnąć, a następnie wybierz pozycję **Dodaj**.

Podczas tworzenia klucza tajnego należy zwrócić uwagę na wartość klucza do użycia w kolejnym kroku. 

## <a name="grant-permissions-in-azure-ad"></a>Udziel uprawnień w usłudze Azure AD

Teraz, gdy zostały zarejestrowane dwie aplikacje do reprezentowania interfejsu API i Konsoli dewelopera, należy udzielić uprawnień, aby umożliwić aplikacji klient-aplikacja do wywołania aplikacji wewnętrznej bazy danych.  

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby udzielić uprawnień do aplikacji klienckiej. Wyszukaj i wybierz **rejestracje aplikacji**.

1. Wybierz aplikację kliencką. Następnie na liście stron aplikacji wybierz pozycję **Uprawnienia interfejsu API**.

1. Wybierz **pozycję Dodaj uprawnienie**.

1. W obszarze **Wybierz interfejs API**wybierz pozycję Moje **interfejsy API**, a następnie znajdź i wybierz aplikację wewnętrznej bazy danych.

1. W obszarze **Uprawnienia delegowane**wybierz odpowiednie uprawnienia do wewnętrznej bazy danych aplikacji, a następnie wybierz pozycję **Dodaj uprawnienia**.

1. Opcjonalnie na stronie **uprawnień interfejsu API** wybierz pozycję **Udzielaj zgody \<administratora dla nazwy dzierżawcy>** udzielić zgody w imieniu wszystkich użytkowników w tym katalogu. 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Włącz autoryzację użytkownika OAuth 2.0 w Konsoli dewelopera

W tym momencie utworzono aplikacje w usłudze Azure AD i udzielono odpowiednich uprawnień, aby umożliwić aplikacji klienta do wywołania aplikacji wewnętrznej bazy danych. 

W tym przykładzie Konsola dewelopera jest aplikacją kliencką. W poniższych krokach opisano sposób włączania autoryzacji użytkownika OAuth 2.0 w Konsoli dewelopera. 

1. W witrynie Azure portal przejdź do wystąpienia usługi API Management.

1. Wybierz **pozycję OAuth 2.0** > **Dodaj**.

1. Podaj **nazwę wyświetlaną** i **opis**.

1. W przypadku **adresu URL strony rejestracji klienta**wprowadź `http://localhost`wartość zastępczą, taką jak . **Adres URL strony rejestracji klienta** wskazuje stronę, której użytkownicy mogą używać do tworzenia i konfigurowania własnych kont dla dostawców OAuth 2.0, którzy to obsługują. W tym przykładzie użytkownicy nie tworzą i nie konfigurują własnych kont, więc zamiast tego należy użyć symbolu zastępczego.

1. W przypadku **typów dotacji autoryzacji**wybierz **kod autoryzacji**.

1. Określ **adres URL punktu końcowego autoryzacji** i adres URL punktu **końcowego tokenu**. Pobierz te wartości ze strony **Punkty końcowe** w dzierżawie usługi Azure AD. Ponownie przejdź do strony **Rejestracje aplikacji** i wybierz pozycję **Punkty końcowe**.


1. Skopiuj **punkt końcowy autoryzacji OAuth 2.0**i wklej go do pola tekstowego **URL punktu końcowego autoryzacji.** Wybierz **pozycję KSIĘGUj** w obszarze Metoda żądania autoryzacji.

1. Skopiuj **punkt końcowy tokenu OAuth 2.0**i wklej go do pola tekstowego **adresu URL punktu końcowego tokenu.** 

    >[!IMPORTANT]
    > Można użyć punktów końcowych **w wersji 1** lub **v2.** Jednak w zależności od wersji, którą wybierzesz, poniższy krok będzie inny. Zaleca się używanie punktów końcowych w wersji 2. 

1. Jeśli używasz punktów końcowych **w wersji 1,** dodaj parametr treści o nazwie **zasób**. Dla wartości tego parametru należy użyć **identyfikatora aplikacji** zaplecza. 

1. Jeśli używasz punktów końcowych **w wersji 2,** użyj zakresu utworzonego dla aplikacji wewnętrznej bazy danych w polu **Zakres domyślny.** Ponadto upewnij się, aby ustawić [`accessTokenAcceptedVersion`](/azure/active-directory/develop/reference-app-manifest#accesstokenacceptedversion-attribute) wartość `2` właściwości w [manifeście aplikacji](/azure/active-directory/develop/reference-app-manifest).

1. Następnie określ poświadczenia klienta. Są to poświadczenia dla aplikacji klienckiej.

1. W przypadku **identyfikatora klienta**użyj **identyfikatora aplikacji** aplikacji.

1. W przypadku **klucza tajnego klienta**użyj klucza utworzonego wcześniej dla aplikacji klient-klient. 

1. Natychmiast po klucz tajny klienta jest **redirect_url** dla typu udzielenia kodu autoryzacji. Zanotuj ten adres URL.

1. Wybierz **pozycję Utwórz**.

1. Wróć do aplikacji klienckiej i wybierz pozycję **Uwierzytelnianie**.

1. W obszarze **Przekierowanie identyfikatorów URI**wybierz typ jako **Sieć Web**, wklej **redirect_url** w obszarze **Przekieruj identyfikator URI**, a następnie zapisz.

Teraz, gdy skonfigurowano serwer autoryzacji OAuth 2.0, Konsola deweloperów może uzyskać tokeny dostępu z usługi Azure AD. 

Następnym krokiem jest włączenie autoryzacji użytkownika OAuth 2.0 dla interfejsu API. Dzięki temu Konsola dewelopera wiedzieć, że musi uzyskać token dostępu w imieniu użytkownika, przed wykonaniem wywołań interfejsu API.

1. Przejdź do wystąpienia zarządzania interfejsami API i przejdź do **interfejsów API**.

2. Wybierz interfejs API, który chcesz chronić. Można na przykład użyć `Echo API`pliku .

3. Przejdź do obszaru **Settings** (Ustawienia).

4. W obszarze **Zabezpieczenia**wybierz pozycję **OAuth 2.0**i wybierz serwer OAuth 2.0 skonfigurowany wcześniej. 

5. Wybierz **pozycję Zapisz**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Pomyślne wywołanie interfejsu API z portalu dla deweloperów

> [!NOTE]
> Ta sekcja nie ma zastosowania do **warstwy Zużycie,** która nie obsługuje portalu dewelopera.

Teraz, gdy autoryzacja użytkownika OAuth 2.0 jest włączona w interfejsie API, konsola dewelopera uzyska token dostępu w imieniu użytkownika przed wywołaniem interfejsu API.

1. Przejdź do dowolnej operacji w interfejsie API w portalu dla deweloperów i wybierz pozycję **Wypróbuj**. Spowoduje to wyświetlenie konsoli dewelopera.

2. Zanotuj nowy element w sekcji **Autoryzacja,** odpowiadający właśnie dodanemu serwerowi autoryzacji.

3. Wybierz **kod autoryzacji** z listy rozwijanej autoryzacji i zostanie wyświetlony monit o zalogowanie się do dzierżawy usługi Azure AD. Jeśli użytkownik jest już zalogowany za pomocą konta, może nie zostać wyświetlony monit.

4. Po pomyślnym `Authorization` zalogowaniu nagłówek jest dodawany do żądania z tokenem dostępu z usługi Azure AD. Poniżej znajduje się przykładowy token (zakodowany base64):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Wybierz **pozycję Wyślij**i możesz pomyślnie wywołać interfejs API.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Konfigurowanie zasad sprawdzania poprawności JWT w celu preeuwaluj żądania

W tym momencie, gdy użytkownik próbuje nawiązać połączenie z Konsoli dewelopera, użytkownik jest monitowany o zalogowanie się. Konsola dewelopera uzyskuje token dostępu w imieniu użytkownika i zawiera token w żądaniu złożonym do interfejsu API.

Co jednak zrobić, jeśli ktoś wywołuje interfejs API bez tokenu lub z nieprawidłowym tokenem? Na przykład spróbuj wywołać interfejs `Authorization` API bez nagłówka, wywołanie będzie nadal przechodzić. Powodem jest to, że usługa API Management nie sprawdza poprawności tokenu dostępu w tym momencie. Po prostu `Authorization` przekazuje nagłówek do interfejsu API zaplecza.

Zasady sprawdzania [poprawności JWT](api-management-access-restriction-policies.md#ValidateJWT) służy do pre-authorize żądań w usłudze API Management, sprawdzając tokeny dostępu każdego żądania przychodzącego. Jeśli żądanie nie ma prawidłowego tokenu, usługa API Management blokuje go. Na przykład dodaj następujące zasady `<inbound>` do sekcji `Echo API`zasad . Sprawdza oświadczenie odbiorców w tokenie dostępu i zwraca komunikat o błędzie, jeśli token jest nieprawidłowy. Aby uzyskać informacje dotyczące konfigurowania zasad, zobacz [Ustawianie lub edytowanie zasad](set-edit-policies.md).

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```
> [!NOTE]
> Ten `openid-config` adres URL odpowiada punktowi końcowemu w wersji 1. W przypadku `openid-config`punktu końcowego `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`v2 należy użyć pliku .

## <a name="build-an-application-to-call-the-api"></a>Tworzenie aplikacji do wywoływania interfejsu API

W tym przewodniku użyto Konsoli dewelopera w zarządzaniu interfejsem API jako przykładowej aplikacji klienckiej do wywołania `Echo API` chronionego przez OAuth 2.0. Aby dowiedzieć się więcej o tworzeniu aplikacji i implementowaniu usługi OAuth 2.0, zobacz [przykłady kodu usługi Azure Active Directory.](../active-directory/develop/sample-v2-code.md)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [usłudze Azure Active Directory i OAuth2.0](../active-directory/develop/authentication-scenarios.md).
* Obejrzyj więcej [filmów](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o zarządzaniu interfejsami API.
* Aby uzyskać inne sposoby zabezpieczenia usługi zaplecza, zobacz [Uwierzytelnianie wzajemnego certyfikatu](api-management-howto-mutual-certificates.md).

* [Tworzenie wystąpienia usługi zarządzania interfejsami API](get-started-create-service-instance.md).

* [Zarządzaj pierwszym interfejsem API](import-and-publish.md).
