---
title: Ochrona interfejsu API przy użyciu protokołu OAuth 2.0 z usługi Azure Active Directory i zarządzanie interfejsami API | Dokumentacja firmy Microsoft
description: Dowiedz się, jak chronić zaplecza interfejsu API sieci web, z usługą Azure Active Directory i zarządzanie interfejsami API.
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
ms.openlocfilehash: f5662a4082487137dfd642cc3264a90f8ab19054
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Ochrona interfejsu API przy użyciu protokołu OAuth 2.0 z usługi Azure Active Directory i zarządzanie interfejsami API

W tym przewodniku przedstawiono sposób skonfigurować wystąpienie usługi Azure API Management do interfejsu API jest chroniony przy użyciu protokołu OAuth 2.0 z usługą Azure Active Directory (Azure AD). 

## <a name="prerequisites"></a>Wymagania wstępne
Aby wykonać kroki opisane w tym artykule, musi być:
* Wystąpienie interfejsu API zarządzania
* Interfejs API publikowaną używającym wystąpienia interfejsu API zarządzania
* Dzierżawa usługi Azure AD

## <a name="overview"></a>Przegląd

Poniżej przedstawiono szybki przegląd kroków:

1. Rejestrowanie aplikacji (aplikacji wewnętrznej bazy danych) w usłudze Azure AD do reprezentowania interfejsu API.
2. Zarejestruj innej aplikacji (aplikacji klienta) w usłudze Azure AD do reprezentowania aplikacji klienta, który musi wywołać interfejsu API.
3. W usłudze Azure AD należy udzielić uprawnień umożliwi aplikacji klienta do wywołania aplikacji zaplecza.
4. Konfigurowanie konsoli Developer do używania uwierzytelniania użytkownika OAuth 2.0.
5. Dodaj **jwt zweryfikować** zasad, aby sprawdzić poprawność token OAuth dla każdego żądania przychodzącego.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Rejestrowanie aplikacji w usłudze Azure AD do reprezentowania interfejsu API

Aby chronić interfejsu API za pomocą usługi Azure AD, pierwszym krokiem jest zarejestrować aplikację w usłudze Azure AD, która reprezentuje interfejsu API. 

1. Przejdź do dzierżawy usługi Azure AD, a następnie przejdź do **rejestracji aplikacji**.

2. Wybierz pozycję **Rejestrowanie nowej aplikacji**. 

3. Podaj nazwę aplikacji. (Na przykład nazwa jest `backend-app`.)  

4. Wybierz **aplikacji sieci Web / interfejs API** jako **typu aplikacji**. 

5. Aby uzyskać **adres URL logowania**, można użyć `https://localhost` jako symbol zastępczy.

6. Wybierz pozycję **Utwórz**.

Po utworzeniu aplikacji Zanotuj **identyfikator aplikacji**, do użycia w kolejnym kroku. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Zarejestruj innej aplikacji w usłudze Azure AD do reprezentowania aplikacji klienta

Każda aplikacja klienta, która wywołuje interfejs API musi zostać zarejestrowany jako aplikacji w usłudze Azure AD oraz. Na przykład przykładowa aplikacja klienta jest konsoli dewelopera w portalu dla deweloperów usługi API Management. Oto jak zarejestrować inną aplikację w usłudze Azure AD do reprezentowania konsoli dla deweloperów.

1. Wybierz pozycję **Rejestrowanie nowej aplikacji**. 

2. Podaj nazwę aplikacji. (Na przykład nazwa jest `client-app`.)

3. Wybierz **aplikacji sieci Web / interfejs API** jako **typu aplikacji**.  

4. Aby uzyskać **adres URL logowania**, można użyć `https://localhost` jako symbolu zastępczego lub adres URL używany przy logowaniu wystąpienia interfejsu API zarządzania. (Na przykład adres URL jest `https://contoso5.portal.azure-api.net/signin`.)

5. Wybierz pozycję **Utwórz**.

Po utworzeniu aplikacji Zanotuj **identyfikator aplikacji**, do użycia w kolejnym kroku. 

Teraz Utwórz klucz tajny klienta dla tej aplikacji do użycia w kolejnym kroku.

1. Wybierz **ustawienia** ponownie, a następnie przejdź do **klucze**.

2. W obszarze **hasła**, podaj **klucza opis**. Wybierz po klucz powinien wygaśnie i wybierz **zapisać**.

Zanotuj wartość klucza. 

## <a name="grant-permissions-in-azure-ad"></a>Udzielanie uprawnień w usłudze Azure AD

Teraz, gdy zarejestrowano dwie aplikacje do reprezentowania interfejsu API i konsoli dla deweloperów, należy udzielić uprawnień, aby umożliwić aplikacji klienckiej do wywołania aplikacji zaplecza.  

1. Przejdź do **rejestracji aplikacji**. 

2. Wybierz `client-app`, a następnie przejdź do **ustawienia**.

3. Wybierz **wymagane uprawnienia** > **Dodaj**.

4. Wybierz **wybierz interfejs API**i wyszukaj `backend-app`.

5. W obszarze **delegowane uprawnienia**, wybierz pozycję `Access backend-app`. 

6. Wybierz **wybierz**, a następnie wybierz **gotowe**. 

> [!NOTE]
> Jeśli **usługi Azure Active Directory** nie znajduje się w obszarze uprawnienia do innych aplikacji, wybierz opcję **Dodaj** dodać ją z listy.
> 
> 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Włącz uwierzytelnianie użytkownika OAuth 2.0 w konsoli dewelopera

W tym momencie zostały utworzone aplikacji w usłudze Azure AD, a przyznano odpowiednie uprawnienia, aby umożliwić aplikacji klienckiej do wywołania aplikacji zaplecza. 

W tym przykładzie konsoli dewelopera jest aplikacji klienckiej. W poniższych krokach opisano sposób włączania uwierzytelniania użytkownika OAuth 2.0 w konsoli dewelopera. 

1. Przejdź do Twojego wystąpienia usługi API Management.

2. Wybierz **OAuth 2.0** > **dodać**.

3. Podaj **Nazwa wyświetlana** i **opis**.

4. Aby uzyskać **adres URL strony rejestracji klienta**, wprowadź wartość symbolu zastępczego, takich jak `http://localhost`. **Adres URL strony rejestracji klienta** wskazuje strona, do której użytkownicy mogą używać, aby utworzyć i skonfigurować swoje własne konta dla dostawcy OAuth 2.0, które obsługują to. W tym przykładzie użytkowników nie utworzyć i skonfigurować swoje własne konta, aby zamiast tego użyj symbol zastępczy.

5. Aby uzyskać **typy przydziałów autoryzacji**, wybierz pozycję **kod autoryzacji**.

6. Określ **adres URL punktu końcowego autoryzacji** i **adres URL punktu końcowego tokenu**. Pobierania tych wartości z **punkty końcowe** strony w dzierżawie usługi Azure AD. Przejdź do **rejestracji aplikacji** ponownie i wybrać opcję **punkty końcowe**.

7. Kopiuj **punkt końcowy protokołu OAuth 2.0 autoryzacji**i wklej ją do **adres URL punktu końcowego autoryzacji** pola tekstowego.

8. Kopiuj **końcowym tokenów OAuth 2.0**i wklej ją do **adres URL punktu końcowego tokenu** pola tekstowego. Oprócz wklejenie punktu końcowego tokenu, Dodaj parametr treści o nazwie **zasobów**. Wartość tego parametru należy użyć **identyfikator aplikacji** dla aplikacji zaplecza.

9. Następnie określ poświadczenia klienta. Są to poświadczenia dla aplikacji klienckiej.

10. Aby uzyskać **identyfikator klienta**, użyj **identyfikator aplikacji** dla aplikacji klienckiej.

11. Aby uzyskać **klucz tajny klienta**, Użyj klucza utworzonego wcześniej dla aplikacji klienckiej. 

12. Natychmiast po klucz tajny klienta jest **redirect_url** zezwolenia kodu przyznać typu. Zanotuj adres URL.

13. Wybierz pozycję **Utwórz**.

14. Wróć do **ustawienia** strony aplikacji klienta.

15. Wybierz **adresy URL odpowiedzi**i Wklej **redirect_url** w pierwszym wierszu. W tym przykładzie zostanie zastąpiony `https://localhost` z adresem URL w pierwszym wierszu.  

Teraz, gdy skonfigurowano serwera autoryzacji OAuth 2.0, w konsoli dewelopera mogą uzyskać tokeny dostępu z usługi Azure AD. 

Następnym krokiem jest umożliwienie autoryzacji użytkownika OAuth 2.0 dla interfejsu API. Dzięki temu konsoli dewelopera wiedzieć, że należy uzyskać token dostępu w imieniu użytkownika, przed wykonaniem wywołań interfejsu API.

1. Przejdź do Twojego wystąpienia interfejsu API zarządzania, a następnie przejdź do **interfejsów API**.

2. Wybierz interfejs API, który chcesz chronić. W tym przykładzie używamy `Echo API`.

3. Przejdź do **ustawienia**.

4. W obszarze **zabezpieczeń**, wybierz **OAuth 2.0**i wybierz serwer protokołu OAuth 2.0 skonfigurowane wcześniej. 

5. Wybierz pozycję **Zapisz**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Pomyślne wywołanie interfejsu API z portalu dla deweloperów

Teraz, gdy autoryzacja użytkownika OAuth 2.0 jest włączone na `Echo API`, konsoli dewelopera uzyskuje token dostępu w imieniu użytkownika, przed wywołaniem interfejsu API.

1. Przejdź do operacji w ramach `Echo API` w developer portalu i wybierz pozycję **wypróbuj**. Zostanie oferuje konsoli dla deweloperów.

2. Należy pamiętać, nowy element **autoryzacji** sekcji odpowiadający właśnie dodano serwer autoryzacji.

3. Wybierz **kod autoryzacji** z Autoryzacja listy rozwijanej, a monit, aby zalogować się do dzierżawy usługi Azure AD. Jeśli nastąpiło już zalogowanie się przy użyciu konta, może nie być wyświetlany monit.

4. Po pomyślnym zalogowaniu `Authorization` nagłówek został dodany do wniosku, token dostępu z usługi Azure AD. Poniżej przedstawiono przykładowy token (kodowanie Base64):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Wybierz **wysyłania**, i pomyślnie można wywołać interfejsu API.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Skonfiguruj zasady sprawdzania poprawności tokenu JWT wstępnie autoryzować żądania

W tym momencie gdy użytkownik podejmuje próbę nawiązania połączenia z konsoli dla deweloperów, użytkownik jest monitowany o Zaloguj się. W konsoli dewelopera uzyskuje token dostępu w imieniu użytkownika.

Ale co zrobić, jeśli ktoś wywołuje interfejs API bez tokenu lub o nieprawidłowym tokenie? Na przykład możesz nadal wywołać interfejs API nawet po usunięciu `Authorization` nagłówka. Przyczyną jest to, że interfejs API zarządzania nie można zweryfikować tokenu dostępu w tym momencie. Po prostu przekazuje `Authorization` nagłówka do interfejsu API zaplecza.

Można użyć [JWT do zweryfikowania](api-management-access-restriction-policies.md#ValidateJWT) zasad wstępnie autoryzować żądania interfejsu API zarządzania, sprawdzając poprawność tokenów dostępu dla każdego żądania przychodzącego. Jeśli żądanie nie ma prawidłowy token, zarządzanie interfejsami API blokuje ją. Na przykład można dodać następujące zasady `<inbound>` sekcji zasad `Echo API`. Sprawdza odbiorców oświadczenia w tokenie dostępu i zwraca komunikat o błędzie, jeśli token jest nieprawidłowy. Aby uzyskać informacje na temat sposobu konfigurowania zasad, zobacz [ustawić lub edytować zasady](set-edit-policies.md).

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

W tym przewodniku użyto konsoli dewelopera w usłudze API Management przykładowej aplikacji klienta do wywołania `Echo API` chronione przez OAuth 2.0. Aby dowiedzieć się więcej na temat tworzenia aplikacji i implementacji protokołu OAuth 2.0, zobacz [przykłady kodu usługi Azure Active Directory](../active-directory/develop/active-directory-code-samples.md).

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [Azure Active Directory i OAuth2.0](../active-directory/develop/active-directory-authentication-scenarios.md).
* Zapoznaj się z kilku [wideo](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o zarządzanie interfejsami API.
* Aby uzyskać inne metody zabezpieczania usługi zaplecza, zobacz [uwierzytelnianie wzajemne certyfikatu](api-management-howto-mutual-certificates.md).

* [Utwórz wystąpienie usługi Zarządzanie interfejsami API](get-started-create-service-instance.md).

* [Pierwszy interfejs API zarządzania](import-and-publish.md).
