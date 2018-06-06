---
title: Żądania tokenów dostępu w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: W tym artykule opisano jak Instalator aplikacji klienckiej i uzyskać token dostępu.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/09/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 92c4544a0dee4c875b1c802a8c4d77d48bfb94ef
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711067"
---
# <a name="azure-ad-b2c-requesting-access-tokens"></a>Usługa Azure AD B2C: Żądanie tokeny dostępu

Token dostępu (oznaczonego jako **dostępu\_tokenu** w odpowiedzi z usługi Azure AD B2C) jest formą tokenów zabezpieczających klienta umożliwiają dostęp do zasobów, które są zabezpieczone przez [serwera autoryzacji](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-protocols#the-basics), np. interfejsu API sieci web. Tokeny dostępu są reprezentowane jako [Jwt](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#types-of-tokens) i zawierają informacje dotyczące serwera danego zasobu i przyznanych uprawnień na serwerze. Podczas wywoływania metody serwer zasobów, token dostępu musi być obecny w żądaniu HTTP.

W tym artykule opisano sposób konfigurowania aplikacji klienckiej i interfejs API sieci web w celu uzyskania **dostępu\_tokenu**.

> [!NOTE]
> **Łańcuchy interfejsu API w sieci Web (w imieniu-z) nie jest obsługiwana przez usługę Azure AD B2C.**
>
> Wiele architektur obejmują składnika web API, który musi wywołać inny podrzędny interfejs API sieci web, zarówno zabezpieczonej przez usługi Azure AD B2C. Ten scenariusz jest często używane w klientach natywnych, którzy mają interfejsu API sieci web zaplecze, która z kolei wywołuje usługi online firmy Microsoft, takich jak Azure AD Graph API.
>
> Ten scenariusz łańcuchowa interfejsu API sieci web mogą być obsługiwane przez przy użyciu przyznania poświadczeń elementu nośnego JWT 2.0, znanej także jako przepływ w imieniu-z. Jednak przepływ w imieniu — z nie jest obecnie zaimplementowana w usłudze Azure AD B2C.

## <a name="register-a-web-api-and-publish-permissions"></a>Zarejestruj interfejsu API sieci web i uprawnienia do publikowania

Przed wysłaniem żądania tokenu dostępu, należy najpierw zarejestrować interfejsu API sieci web i publikowanie uprawnienia (zakresy), które może zostać przydzielony do aplikacji klienckiej.

### <a name="register-a-web-api"></a>Rejestrowanie internetowego interfejsu API

1. W menu funkcje usługi Azure AD B2C w portalu Azure kliknij **aplikacji**.
1. Kliknij przycisk **+ Dodaj** w górnej części menu.
1. Wprowadź wartość **Nazwa**, która będzie opisywać aplikację na potrzeby klientów. Na przykład można wprowadzić "Interfejsu API firmy Contoso".
1. Przestaw przełącznik **Uwzględnij aplikację sieci Web/interfejs API sieci Web** na wartość **Tak**.
1. Należy wprowadzić dowolną wartość dla **adresy URL odpowiedzi**. Na przykład wprowadź wartość `https://localhost:44316/`. Wartość nie ma znaczenia, ponieważ interfejs API powinni nie otrzymywać token bezpośrednio z usługi Azure AD B2C.
1. Wprowadź **identyfikator URI aplikacji**. Jest to identyfikator używany na potrzeby interfejsu API sieci Web. Na przykład wprowadź "informacje" w polu. **Identyfikator URI aplikacji** będzie wówczas `https://{tenantName}.onmicrosoft.com/notes`.
1. Kliknij pozycję **Utwórz**, aby zarejestrować aplikację.
1. Kliknij nowo utworzoną aplikację i skopiuj globalnie unikatowy **identyfikator klienta aplikacji**, który będzie używany w dalszej części kodu.

### <a name="publishing-permissions"></a>Uprawnienia publikowania

Zakresy, które są odpowiednikiem uprawnienia, jest konieczne, gdy aplikacja jest wywoływanie interfejsu API. Przykłady zakresy są "do odczytu" lub "write". Załóżmy, że chcesz sieci web lub aplikacji natywnej "odczytać" z interfejsem API. Aplikacji spowoduje wywołanie usługi Azure AD B2C i żądania tokenu dostępu, która zapewnia dostęp do zakresu "Odczyt". Dla usługi Azure AD B2C emitować tokenu dostępu, aplikacja musi mieć uprawnienie "odczytać" z interfejsu API. Aby to zrobić, interfejsu API musi najpierw opublikować zakresu "Przeczytaj".

1. W ramach usługi Azure AD B2C **aplikacji** menu, Otwórz interfejs API sieci web aplikacji ("interfejsu API firmy Contoso").
1. Kliknij pozycję **Opublikowane zakresy**. Jest to miejsce, w którym można zdefiniować uprawnienia (zakresy), które mogą być udzielone innym aplikacjom.
1. Dodaj **wartości zakresu** niezbędne (na przykład "do odczytu"). Domyślnie zostanie zdefiniowany zakres „user_impersonation”. W razie potrzeby można zignorować to. Wprowadź opis zakresu w **nazwa zakresu** kolumny.
1. Kliknij pozycję **Zapisz**.

> [!IMPORTANT]
> **Nazwa zakresu** opis **wartość zakresu**. Korzystając z zakresu, upewnij się, że użyj **wartość zakresu**.

## <a name="grant-a-native-or-web-app-permissions-to-a-web-api"></a>Udziel natywny lub sieci web aplikacji uprawnienia do interfejsu API sieci web

Interfejs API skonfigurowane do publikowania zakresy aplikacja kliencka musi otrzymać tych zakresów za pośrednictwem portalu Azure.

1. Przejdź do **aplikacji** w menu funkcje usługi Azure AD B2C.
1. Zarejestrować aplikację klienta ([aplikacji sieci web](active-directory-b2c-app-registration.md#register-a-web-app) lub [klientami](active-directory-b2c-app-registration.md#register-a-mobile-or-native-app)) Jeśli nie masz już. Jeśli wykonujesz w tym przewodniku jako punktu początkowego, musisz zarejestrować aplikację klienta.
1. Polecenie **dostępu do interfejsu API**.
1. Kliknij pozycję **Dodaj**.
1. Wybierz interfejs API sieci web i chcesz udzielić zakresów (uprawnień).
1. Kliknij przycisk **OK**.

> [!NOTE]
> Usługa Azure AD B2C nie Poproś klienta aplikacji użytkowników o ich zgodę. Zamiast tego wszystkie zgody są udostępniane przez administratora, na podstawie uprawnień skonfigurowanych od aplikacje opisane powyżej. Po odebraniu Udziel uprawnień dla aplikacji, wszyscy użytkownicy, którzy wcześniej mogli uzyskać te uprawnienia nie będzie w tym celu.

## <a name="requesting-a-token"></a>Żądania tokenu

Podczas żądania tokenu dostępu, aplikacja kliencka musi określać odpowiednie uprawnienia w **zakres** parametr żądania. Na przykład, aby określić **wartość zakresu** "Przeczytaj" dla interfejsu API, który ma **identyfikator URI aplikacji** z `https://contoso.onmicrosoft.com/notes`, zakres będzie `https://contoso.onmicrosoft.com/notes/read`. Poniżej przedstawiono przykładowy kod żądania autoryzacji do `/authorize` punktu końcowego.

> [!NOTE]
> Obecnie domen niestandardowych nie są obsługiwane razem z tokenów dostępu. Należy użyć domeny tenantName.onmicrosoft.com w adresie URL żądania.

```
https://login.microsoftonline.com/<tenantName>.onmicrosoft.com/<yourPolicyId>/oauth2/v2.0/authorize?client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
```

Uzyskanie wiele uprawnień w jednym żądaniu, można dodać wiele wpisów w pojedynczej **zakres** parametru rozdzielone spacjami. Na przykład:

Adres URL zdekodować:

```
scope=https://contoso.onmicrosoft.com/notes/read openid offline_access
```

Zakodowane w adresie URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread%20openid%20offline_access
```

Dla zasobów niż co otrzymuje aplikacji klienta, aby zażądać więcej zakresów (uprawnień). W przypadku wywołania powiedzie się, jeśli co najmniej jednego uprawnienia. Powstałe w ten sposób **dostępu\_tokenu** będą miały swoje roszczenie "punkt połączenia usługi" wypełniane przy użyciu uprawnień, które pomyślnie zostały przyznane.

> [!NOTE] 
> Żądania uprawnień w odniesieniu do dwóch zasobów innej witryny sieci web w jednym żądaniu nie jest obsługiwana. Tego rodzaju żądanie zakończy się niepowodzeniem.

### <a name="special-cases"></a>Specjalne przypadki

Standard OpenID Connect określa kilka wartości specjalne 'scope'. Następujące zakresy specjalne reprezentują uprawnienia do "dostępu do profilu użytkownika":

* **openid**: żąda identyfikator tokenu
* **w trybie offline\_dostępu**: żąda token odświeżania (przy użyciu [przepływu kodu autoryzacji](active-directory-b2c-reference-oauth-code.md)).

Jeśli `response_type` parametru w `/authorize` żądanie zawiera `token`, `scope` parametr musi zawierać co najmniej jeden zasób zakres (inne niż `openid` i `offline_access`) który zostaną przyznane. W przeciwnym razie `/authorize` żądanie zakończy się niepowodzeniem.

## <a name="the-returned-token"></a>Zwrócony tokenu

W pomyślnie minted **dostępu\_tokenu** (przy użyciu dowolnego `/authorize` lub `/token` punktu końcowego), poniższe oświadczenia będą znajdować się:

| Name (Nazwa) | Claim | Opis |
| --- | --- | --- |
|Grupy odbiorców |`aud` |**Identyfikator aplikacji** pojedynczego tokenu udziela dostępu do zasobu. |
|Zakres |`scp` |Uprawnienia do tego zasobu. Wiele uprawnienia nadanego będzie rozdzielone spacjami. |
|Strona autoryzowanych |`azp` |**Identyfikator aplikacji** aplikacji klienta, który zainicjował żądanie. |

Gdy odbierze interfejsu API **dostępu\_tokenu**, musi [sprawdzania poprawności tokenu](active-directory-b2c-reference-tokens.md) udowodnić, że token jest autentyczny i ma poprawne oświadczeń.

Firma Microsoft zawsze są otwarte na opinie i sugestie! Jeśli masz trudności w tym temacie, Opublikuj w witrynie Stack Overflow tagu ["azure-ad b2c"](https://stackoverflow.com/questions/tagged/azure-ad-b2c). Funkcja żądań, dodaj je do [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

## <a name="next-steps"></a>Kolejne kroki

* Tworzenie interfejsu API sieci web przy użyciu [.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)
* Tworzenie interfejsu API sieci web przy użyciu [Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)
