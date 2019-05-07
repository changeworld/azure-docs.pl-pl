---
title: Ochrona interfejsu API przy użyciu protokołu OAuth 2.0 przy użyciu usługi Azure Active Directory i usługi API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ochrona zaplecza interfejsu API sieci web, za pomocą usługi Azure Active Directory i usługi API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2018
ms.author: apimpm
ms.openlocfilehash: b5467711f06380ca61b4a9d5150b66c3f945c08c
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141075"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Ochrona interfejsu API przy użyciu protokołu OAuth 2.0 przy użyciu usługi Azure Active Directory i usługi API Management

Ten przewodnik pokazuje, jak skonfigurować wystąpienie usługi Azure API Management, aby chronić interfejs API przy użyciu protokołu OAuth 2.0 przy użyciu usługi Azure Active Directory (Azure AD). 

## <a name="prerequisites"></a>Wymagania wstępne
Aby wykonać kroki opisane w tym artykule, musisz mieć:
* Wystąpienia usługi API Management
* Interfejs API publikowane używającym wystąpienia usługi API Management
* Dzierżawa usługi Azure AD

## <a name="overview"></a>Omówienie

Poniżej przedstawiono krótkie omówienie kroków:

1. Rejestrowanie aplikacji (aplikacji zaplecza) w usłudze Azure AD do reprezentowania interfejsu API.
2. Inna aplikacja (aplikacja kliencka) należy zarejestrować w usłudze Azure AD do reprezentowania aplikacji klienckiej, która ma wywołać interfejs API.
3. W usłudze Azure AD należy udzielić uprawnień, aby umożliwić aplikacji klienckiej do wywoływania aplikacji zaplecza.
4. Konfigurowanie konsoli dla deweloperów, aby użyć autoryzacji użytkownika OAuth 2.0.
5. Dodaj **weryfikacji tokenu jwt** zasady, aby zweryfikować token OAuth dla każdego żądania przychodzącego.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Rejestrowanie aplikacji w usłudze Azure AD do reprezentowania interfejsu API

Aby chronić interfejs API z usługą Azure AD, pierwszym krokiem jest zarejestrowanie aplikacji w usłudze Azure AD, który reprezentuje interfejs API. 

1. Przejdź do dzierżawy usługi Azure AD, a następnie przejdź do **rejestracje aplikacji (starsza wersja)**.

2. Wybierz pozycję **Rejestrowanie nowej aplikacji**. 

3. Podaj nazwę aplikacji. (W tym przykładzie nazwa to `backend-app`.)  

4. Wybierz **aplikacji sieci Web / interfejs API** jako **typ aplikacji**. 

5. Aby uzyskać **adres URL logowania**, możesz użyć `https://localhost` jako symbol zastępczy.

6. Wybierz pozycję **Utwórz**.

Po utworzeniu aplikacji Zanotuj **identyfikator aplikacji**, do użycia w kolejnym kroku. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Zarejestruj innej aplikacji w usłudze Azure AD do reprezentowania aplikacji klienckiej

Każda aplikacja kliencka, która wywołuje interfejs API musi zostać zarejestrowana jako aplikację w usłudze Azure AD oraz. W tym przykładzie Przykładowa aplikacja kliencka jest konsoli dla deweloperów w portalu dla deweloperów usługi API Management. Oto jak można zarejestrować inną aplikację w usłudze Azure AD do reprezentowania konsoli dla deweloperów.

1. W **rejestracje aplikacji (starsza wersja)**, wybierz opcję **rejestrowanie nowej aplikacji**. 

2. Podaj nazwę aplikacji. (W tym przykładzie nazwa to `client-app`.)

3. Wybierz **aplikacji sieci Web / interfejs API** jako **typ aplikacji**.  

4. Aby uzyskać **adres URL logowania**, możesz użyć `https://localhost` jako symbolu zastępczego lub adres URL użyj logowania wystąpienia usługi API Management. (W tym przykładzie adres URL jest `https://contoso5.portal.azure-api.net/signin`.)

5. Wybierz pozycję **Utwórz**.

Po utworzeniu aplikacji Zanotuj **identyfikator aplikacji**, do użycia w kolejnym kroku. 

Teraz utworzymy klucz tajny klienta dla tej aplikacji, do użycia w kolejnym kroku.

1. Wybierz **ustawienia** ponownie, a następnie przejdź do **klucze**.

2. W obszarze **haseł**, podaj **Opis klucza**. Wybierz po klucz powinien wygasają i wybraniu **Zapisz**.

Zanotuj wartość klucza. 

## <a name="grant-permissions-in-azure-ad"></a>Udziel uprawnień w usłudze Azure AD

Po zarejestrowaniu dwie aplikacje do reprezentowania konsoli dla deweloperów i interfejsu API, należy udzielić uprawnień, aby umożliwić aplikacji klienckiej do wywoływania aplikacji zaplecza.  

1. Przejdź do **rejestracje aplikacji (starsza wersja)**. 

2. Wybierz `client-app`, a następnie przejdź do **ustawienia**.

3. Wybierz **wymagane uprawnienia** > **Dodaj**.

4. Wybierz **wybierz interfejs API**i wyszukaj `backend-app`.

5. W obszarze **delegowane uprawnienia**, wybierz opcję `Access backend-app`. 

6. Wybierz **wybierz**, a następnie wybierz pozycję **gotowe**. 

> [!NOTE]
> Jeśli **usługi Azure Active Directory** nie znajduje się w obszarze uprawnień dotyczących innych aplikacji, wybierz opcję **Dodaj** Aby dodać go z listy.

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Włączanie autoryzacji użytkownika OAuth 2.0, w konsoli dla deweloperów

Na tym etapie utworzono aplikacji w usłudze Azure AD, a przyznano odpowiednie uprawnienia, aby umożliwić aplikacji klienckiej do wywoływania aplikacji zaplecza. 

W tym przykładzie konsoli dla deweloperów jest aplikacją kliencką. Poniżej opisano sposób włączania autoryzacji użytkownika OAuth 2.0, w konsoli dla deweloperów. 

1. W witrynie Azure Portal przejdź do swojego wystąpienia usługi API Management.

2. Wybierz **protokołu OAuth 2.0** > **Dodaj**.

3. Podaj **nazwę wyświetlaną** i **opis**.

4. Aby uzyskać **adres URL strony rejestracji klienta**, takich jak Podaj wartość symbolu zastępczego `http://localhost`. **Adres URL strony rejestracji klienta** wskazuje stronę która umożliwia użytkownikom tworzenie i konfigurowanie własnych kont dla dostawcy OAuth 2.0, które obsługują to. W tym przykładzie użytkownicy nie tworzyć i konfigurować własne konta, więc zamiast tego użyj symbolu zastępczego.

5. Aby uzyskać **typy przydziałów autoryzacji**, wybierz opcję **kod autoryzacji**.

6. Określ **adresu URL punktu końcowego autoryzacji** i **adresu URL punktu końcowego tokenu**. Pobierania tych wartości z **punktów końcowych** strony w dzierżawie usługi Azure AD. Przejdź do **rejestracje aplikacji** strony, a następnie wybierz **punktów końcowych**.

    >[!NOTE]
    > Użyj **v1** punktów końcowych w tym miejscu

7. Kopiuj **punkt końcowy protokołu OAuth 2.0 autoryzacji**i wklej go w **adresu URL punktu końcowego autoryzacji** pola tekstowego.

8. Kopiuj **punkt końcowy protokołu OAuth 2.0 Token**i wklej go w **adresu URL punktu końcowego tokenu** pola tekstowego. Oprócz wklejeniem punktu końcowego tokenu, Dodaj parametr treści, nazwie **zasobów**. Wartość tego parametru można użyć **identyfikator aplikacji** dla aplikacji zaplecza.

9. Następnie określ poświadczenia klienta. Są to poświadczenia dla aplikacji klienckiej.

10. Aby uzyskać **identyfikator klienta**, użyj **identyfikator aplikacji** dla aplikacji klienckiej.

11. Aby uzyskać **klucz tajny klienta**, Użyj klucza utworzonego dla aplikacji klienckiej wcześniej. 

12. Natychmiast po klucz tajny klienta jest **redirect_url** zezwolenia typ udzielania kodu. Zanotuj ten adres URL.

13. Wybierz pozycję **Utwórz**.

14. Wróć do **ustawienia** strony aplikacji klienckiej.

15. Wybierz **adresy URL odpowiedzi**i Wklej **redirect_url** w pierwszym wierszu. W tym przykładzie został zastąpiony `https://localhost` za pomocą adresu URL w pierwszym wierszu.  

Teraz, gdy skonfigurowano serwer autoryzacji OAuth 2.0, konsolę dla deweloperów można uzyskać tokenów dostępu z usługi Azure AD. 

Następnym krokiem jest umożliwienie autoryzacji użytkownika OAuth 2.0 dla interfejsu API. Dzięki temu konsoli dla deweloperów dowiedzieć się, że musi uzyskać token dostępu w imieniu użytkownika, przed wykonaniem wywołania interfejsu API.

1. Przejdź do swojego wystąpienia usługi API Management, a następnie przejdź do **interfejsów API**.

2. Wybierz interfejs API, który chcesz chronić. W tym przykładzie używamy `Echo API`.

3. Przejdź do obszaru **Settings** (Ustawienia).

4. W obszarze **zabezpieczeń**, wybierz **OAuth 2.0**i wybierz skonfigurowane wcześniej serwer OAuth 2.0. 

5. Wybierz pozycję **Zapisz**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Pomyślne wywołanie interfejsu API z portalu dla deweloperów

> [!NOTE]
> Ta sekcja nie ma zastosowania do **zużycie** warstwę, która nie obsługuje portalu dla deweloperów.

Teraz, gdy włączona jest funkcja autoryzacji użytkownika OAuth 2.0 `Echo API`, konsolę dla deweloperów uzyskuje token dostępu w imieniu użytkownika, przed wywołaniem interfejsu API.

1. Przejdź do każdej operacji w ramach `Echo API` w developer portal i wybierz pozycję **wypróbuj**. Zostanie zapewnia konsolę dla deweloperów.

2. Należy pamiętać, nowego elementu w **autoryzacji** sekcji odpowiadający serwera autoryzacji, właśnie został dodany.

3. Wybierz **kod autoryzacji** z autoryzacją listy rozwijanej, a monit, aby zalogować się do dzierżawy usługi Azure AD. Jeśli już zalogowano się przy użyciu konta, może nie być monit.

4. Po pomyślnym zalogowaniu `Authorization` nagłówek jest dodawana do żądania, przy użyciu tokenu dostępu z usługi Azure AD. Poniżej przedstawiono przykładowy token (kodowanie Base64):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Wybierz **wysyłania**, można pomyślnie wywołać interfejs API.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Skonfiguruj zasady walidacji tokenów JWT do wstępnej autoryzacji żądania

W tym momencie gdy użytkownik próbuje nawiązać połączenie z konsoli dla deweloperów, użytkownik jest monitowany do logowania. W konsoli dla deweloperów uzyskuje token dostępu w imieniu użytkownika.

Ale co zrobić, jeśli ktoś wywołuje interfejs API bez tokenu lub nieprawidłowy token? Na przykład użytkownik może wciąż wywołać interfejs API nawet wtedy, gdy usuniesz `Authorization` nagłówka. Przyczyną jest to, że usługa API Management nie sprawdza poprawność tokenu dostępu w tym momencie. Po prostu przekazuje `Authorization` nagłówka do interfejsu API zaplecza.

Możesz użyć [weryfikacji tokenu JWT](api-management-access-restriction-policies.md#ValidateJWT) zasad do wstępnej autoryzacji żądania w usłudze API Management, sprawdzając poprawność tokenów dostępu dla każdego żądania przychodzącego. Jeśli żądanie nie ma prawidłowego tokenu, API Management blokuje ją. Na przykład można dodać następujące zasady `<inbound>` sekcji zasady `Echo API`. Sprawdza, czy oświadczenia odbiorców w tokenie dostępu i zwraca komunikat o błędzie, jeśli token jest nieprawidłowy. Aby uzyskać informacje na temat sposobu konfigurowania zasad, zobacz [Ustawianie lub edytowanie zasad](set-edit-policies.md).

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

## <a name="build-an-application-to-call-the-api"></a>Tworzenie aplikacji do wywołania interfejsu API

W tym przewodniku użyto konsoli dla deweloperów w usłudze API Management jako Przykładowa aplikacja kliencka do wywołania `Echo API` chronione przez OAuth 2.0. Aby dowiedzieć się więcej na temat tworzenia aplikacji i wdrażania protokołu OAuth 2.0, zobacz [przykładów kodu usługi Azure Active Directory](../active-directory/develop/sample-v1-code.md).

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [Azure Active Directory i OAuth 2.0](../active-directory/develop/authentication-scenarios.md).
* Zapoznaj się z kilku [wideo](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o usłudze API Management.
* Aby uzyskać inny sposób zabezpieczyć usługi zaplecza, zobacz [wzajemne uwierzytelnianie certyfikatów](api-management-howto-mutual-certificates.md).

* [Tworzenie wystąpienia usługi API Management](get-started-create-service-instance.md).

* [Zarządzanie pierwszym interfejsem API](import-and-publish.md).
