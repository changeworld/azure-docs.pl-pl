---
title: Ochrona za pomocą protokołu OAuth 2.0 z usługą Azure Active Directory i zarządzanie interfejsami API interfejsu API | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zabezpieczyć interfejs API sieci Web wewnętrznej bazy danych z usługi Azure Active Directory i zarządzanie interfejsami API.
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
ms.openlocfilehash: 2c05407d761a8848f9e032aa219960cd7ea6fa93
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-protect-an-api-using-oauth-20-with-azure-active-directory-and-api-management"></a>Jak zabezpieczyć interfejs API z usługi Azure Active Directory i zarządzanie interfejsami API przy użyciu protokołu OAuth 2.0

W tym przewodniku przedstawiono sposób skonfigurować wystąpienie interfejsu API zarządzania (APIM) do ochrony interfejsu API przy użyciu protokołu OAuth 2.0 z usługi Azure Active Directory (AAD). 

## <a name="prerequisite"></a>Wymagania wstępne
Aby wykonać kroki opisane w tym artykule, musi być:
* Wystąpienie APIM
* Interfejs API publikowana przy użyciu wystąpienia APIM
* Dzierżawa usługi Azure AD

## <a name="overview"></a>Przegląd

W tym przewodniku przedstawiono sposób chronić interfejsu API za pomocą protokołu OAuth 2.0 w APIM. W tym artykule usługi Azure AD jako serwera autoryzacji (OAuth serwera) jest używany. Poniżej przedstawiono krótkie omówienie kroków:

1. Rejestrowanie aplikacji (aplikacji wewnętrznej bazy danych) w usłudze Azure AD do reprezentowania interfejsu API
2. Zarejestruj innej aplikacji (aplikacji klienta) w usłudze Azure AD do reprezentowania aplikacji klienckiej, która ma wywołać interfejs API
3. W usłudze Azure AD należy udzielić uprawnień umożliwia aplikacji klienta do wywołania wewnętrznej bazy danych aplikacji
4. Konfigurowanie konsoli Developer do używania uwierzytelniania użytkownika OAuth 2.0
5. Dodaj zasady sprawdzania poprawności jwt do zweryfikowania tokenu OAuth dla każdego żądania przychodzącego

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Rejestrowanie aplikacji w usłudze Azure AD do reprezentowania interfejsu API

Aby chronić interfejsu API za pomocą usługi Azure AD, pierwszym krokiem jest zarejestrować aplikację w usłudze Azure AD, która reprezentuje interfejsu API. 

Przejdź do dzierżawy usługi Azure AD, a następnie przejdź do **rejestracji aplikacji**.

Wybierz **nowej rejestracji aplikacji**. 

Podaj nazwę aplikacji. W tym przykładzie `backend-app` jest używany.  

Wybierz **aplikacji sieci Web / interfejs API** jako **typu aplikacji**. 

Aby uzyskać **adres URL logowania**, można użyć `https://localhost` jako symbol zastępczy.

Polecenie **utworzyć**.

Po utworzeniu aplikacji Zanotuj **identyfikator aplikacji** do użycia w kolejnym kroku. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Zarejestruj innej aplikacji w usłudze Azure AD do reprezentowania aplikacji klienta

Każda aplikacja klienta, który musi wywołać interfejsu API musi zostać zarejestrowany jako aplikacji w usłudze Azure AD oraz. W tym przewodniku używamy konsoli dewelopera w portalu dla deweloperów APIM jako przykładowej aplikacji klienta. 

Należy zarejestrować inną aplikację w usłudze Azure AD do reprezentowania konsoli dla deweloperów.

Polecenie **nowej rejestracji aplikacji** ponownie. 

Podaj nazwę aplikacji, a następnie wybierz **aplikacji sieci Web / interfejs API** jako **typu aplikacji**. W tym przykładzie `client-app` jest używany.  

Aby uzyskać **adres URL logowania**, można użyć `https://localhost` jako symbolu zastępczego lub użyj adresu URL logowania wystąpienia APIM. W tym przykładzie `https://contoso5.portal.azure-api.net/signin` jest używany.

Polecenie **utworzyć**.

Po utworzeniu aplikacji Zanotuj **identyfikator aplikacji** do użycia w kolejnym kroku. 

Teraz należy utworzyć klucz tajny klienta dla tej aplikacji do użycia w kolejnym kroku.

Polecenie **ustawienia** ponownie, przejdź do **klucze**.

W obszarze **hasła**, podaj **klucza opis**, wybierz po klucz powinien wygasa, a kliknięcie **zapisać**.

Zanotuj wartość klucza. 

## <a name="grant-permissions-in-aad"></a>Udzielanie uprawnień w usłudze AAD

Teraz możemy zarejestrowano dwie aplikacje do reprezentowania interfejsu API (to znaczy aplikacji zaplecza) i konsoli dewelopera (czyli aplikację klienta), należy udzielić uprawnień, aby umożliwić aplikacji klienckiej do wywołania aplikacji zaplecza.  

Przejdź do **rejestracji aplikacji** ponownie. 

Polecenie `client-app` i przejdź do **ustawienia**.

Polecenie **wymagane uprawnienia** , a następnie **Dodaj**.

Polecenie **wybierz interfejs API** i wyszukaj `backend-app`.

Sprawdź `Access backend-app` w obszarze **delegowane uprawnienia**. 

Polecenie **wybierz** , a następnie **gotowe**. 

> [!NOTE]
> Jeśli **Windows** **usługi Azure Active Directory** jest niewymienione w obszarze uprawnienia do innych aplikacji, kliknij przycisk **Dodaj** i dodaj go do listy.
> 
> 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Włącz uwierzytelnianie użytkownika OAuth 2.0 w konsoli dewelopera

Na tym etapie nasz utworzono nasze aplikacje w usłudze Azure AD i przyznano odpowiednie uprawnienia, aby umożliwić aplikacji klienckiej do wywołania aplikacji zaplecza. 

W tym przewodniku używamy konsoli dewelopera jako aplikacji klienckiej. Kroki opisują sposób włączania uwierzytelniania OAuth 2.0 użytkownika, w konsoli dewelopera 

Przejdź do swojego wystąpienia APIM.

Polecenie **OAuth 2.0** , a następnie **dodać**.

Podaj **Nazwa wyświetlana** i **opis**.

Rejestracja klienta adresu URL, strony ** wprowadź wartość symbolu zastępczego, takich jak `http://localhost`.  **Adres URL strony rejestracji klienta** wskazuje strona, do której użytkownicy mogą używać, aby utworzyć i skonfigurować swoje własne konta dla dostawcy OAuth 2.0, które obsługują zarządzanie kontami użytkowników. W tym przykładzie użytkowników nie utworzyć i skonfigurować swoje własne konta, aby używany jest symbol zastępczy.

Sprawdź **kod autoryzacji** jako **typy przydziałów autoryzacji**.

Następnie określ **adres URL punktu końcowego autoryzacji** i **adres URL punktu końcowego tokenu**.

Te wartości można pobrać z **punkty końcowe** strony w dzierżawie usługi Azure AD. Aby uzyskać dostęp do punktów końcowych, przejdź do **rejestracji aplikacji** ponownie i kliknij przycisk na **punkty końcowe**.

Kopiuj **punkt końcowy protokołu OAuth 2.0 autoryzacji** i wklej ją do **adres URL punktu końcowego autoryzacji** pola tekstowego.

Kopiuj **końcowym tokenów OAuth 2.0** i wklej ją do **adres URL punktu końcowego tokenu** pola tekstowego.

Oprócz wklejenie punktu końcowego tokenu, Dodaj parametr treści dodatkowe o nazwie **zasobów** i wykorzystanie wartości **identyfikator aplikacji** dla aplikacji zaplecza.

Następnie określ poświadczenia klienta. Są to poświadczenia dla aplikacji klienckiej.

Aby uzyskać **identyfikator klienta**, użyj **identyfikator aplikacji** dla aplikacji klienckiej.

Aby uzyskać **klucz tajny klienta**, Użyj klucza utworzonego wcześniej dla aplikacji klienckiej. 

Natychmiast po klucz tajny klienta jest **redirect_url** zezwolenia kodu przyznać typu.

Zanotuj adres URL.

Polecenie **utworzyć**.

Przejdź z powrotem do **ustawienia** strony aplikacji klienta.

Polecenie **adresy URL odpowiedzi** i Wklej **redirect_url** w pierwszym wierszu. W tym przykładzie mamy zastąpione `https://localhost` z adresem URL w pierwszym wierszu.  

Teraz możemy skonfigurowano serwera autoryzacji OAuth 2.0, w konsoli dewelopera powinno być możliwe uzyskanie tokenów dostępu z usługi Azure AD. 

Następnym krokiem jest umożliwienie autoryzację użytkownika OAuth 2.0 na interfejsie API, tak, aby w konsoli dewelopera zna go musi uzyskać token dostępu w imieniu użytkownika przed dokonaniem wywołań interfejsach API.

Przejdź do swojego wystąpienia APIM, przejdź do **interfejsów API**.

Polecenie interfejsu API, które chcesz chronić. W tym przykładzie używamy `Echo API`.

Przejdź do **ustawienia**.

W obszarze **zabezpieczeń**, wybierz **OAuth 2.0** i wybierz serwer protokołu OAuth 2.0 został wcześniej skonfigurowany. 

Kliknij przycisk **Zapisz**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Pomyślne wywołanie interfejsu API z portalu dla deweloperów

Teraz, gdy autoryzacja użytkownika OAuth 2.0 jest włączone na `Echo API`, konsoli dewelopera uzyska token dostępu w imieniu użytkownika przed wywołaniem interfejsu API.

Przejdź do operacji w ramach `Echo API` w portalu dla deweloperów i kliknij **wypróbuj**, które zostanie wyświetlone nam w konsoli dewelopera.

Należy pamiętać, nowy element **autoryzacji** sekcji odpowiadający właśnie dodano serwer autoryzacji.

Wybierz **kod autoryzacji** z Autoryzacja listy rozwijanej i zostanie wyświetlony monit o Zaloguj się do dzierżawy usługi Azure AD. Jeśli nastąpiło już zalogowanie się przy użyciu konta, może nie być wyświetlony monit.

Po pomyślnym zalogowaniu `Authorization` nagłówka zostaną dodane do żądania z tokenem dostępu z usługi Azure AD. 

Przykładowy token prawdopodobnie poniżej, jest kodowany w standardzie Base64.

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
```

Kliknij przycisk **wysyłania** i powinno być możliwe do wywołania interfejsu API pomyślnie.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Skonfiguruj zasady sprawdzania poprawności tokenu JWT wstępnie autoryzować żądania

W tym momencie, gdy użytkownik podejmuje próbę nawiązania połączenia z konsoli dla deweloperów, użytkownik otrzyma monit Zaloguj się i konsoli dewelopera uzyska tokenu dostępu w imieniu użytkownika. Wszystko działa zgodnie z oczekiwaniami. Jednak co zrobić, jeśli ktoś wywołuje interfejsach API bez tokenu lub o nieprawidłowym tokenie? Na przykład, spróbuj usunąć `Authorization` nagłówka i zostanie ustalone, nadal można wywołać interfejsu API. Dzieje się tak, ponieważ APIM nie można zweryfikować tokenu dostępu w tym momencie. Po prostu przekazuje `Auhtorization` nagłówka do zaplecza interfejsu API.

Możemy użyć [JWT do zweryfikowania](api-management-access-restriction-policies.md#ValidateJWT) zasad wstępnie autoryzować żądania w APIM weryfikując tokenów dostępu dla każdego żądania przychodzącego. Jeśli żądanie nie ma prawidłowy token, jest zablokowany przez interfejs API zarządzania i nie jest przekazywane do wewnętrznej bazy danych. Na przykład można dodać poniżej zasady `<inbound>` sekcji zasad `Echo API`. Sprawdza oświadczeń odbiorców tokenu dostępu, a zwraca komunikat o błędzie, jeśli token jest nieprawidłowy. Aby uzyskać informacje na temat sposobu konfigurowania zasad, zobacz [ustawić lub edytować zasady](set-edit-policies.md).

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

W tym przewodniku użyliśmy konsoli dewelopera w APIM jako przykładowej aplikacji klienta do wywołania `Echo API` chronione przez OAuth 2.0. Aby dowiedzieć się więcej na temat tworzenia aplikacji i implementacji przepływu OAuth 2.0, zobacz [przykłady kodu usługi Azure Active Directory](../active-directory/develop/active-directory-code-samples.md).

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [Azure Active Directory i OAuth2.0](../active-directory/develop/active-directory-authentication-scenarios.md)
* Zapoznaj się z kilku [wideo](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o zarządzanie interfejsami API.
* Aby uzyskać inne metody zabezpieczania usługi wewnętrznej bazy danych, zobacz [uwierzytelnianie wzajemne certyfikatu](api-management-howto-mutual-certificates.md).

[Create an API Management service instance]: get-started-create-service-instance.md
[Manage your first API]: import-and-publish.md
