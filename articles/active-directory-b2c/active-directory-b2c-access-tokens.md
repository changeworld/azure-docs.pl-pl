---
title: Żądania tokenów dostępu w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Ten artykuł będzie pokazują, jak skonfigurować aplikację klienta i uzyskiwanie tokenu dostępu.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/09/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9cd5789cd2ee6e167f3d3ed05c2fde077f7ec9a3
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344945"
---
# <a name="azure-ad-b2c-requesting-access-tokens"></a>Usługi Azure AD B2C: Tokeny dostępu żądania

Token dostępu (oznaczonego jako **dostępu\_tokenu** w odpowiedzi z usługi Azure AD B2C) jest formą token zabezpieczający, który klient może używać dostępu do zasobów, które są zabezpieczone przez [serwera autoryzacji](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-protocols#the-basics), takich jak interfejs API sieci web. Tokeny dostępu są reprezentowane jako [tokenów Jwt](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#types-of-tokens) i zawierają informacje o serwerze zamierzony zasobów i udzielone uprawnienia do serwera. Podczas wywoływania serwera zasobów, token dostępu musi być obecne w żądaniu HTTP.

W tym artykule omówiono sposób konfigurowania aplikacji klienckiej i interfejs API sieci web w celu uzyskania **dostępu\_tokenu**.

> [!NOTE]
> **Łańcuchy interfejsu API w sieci Web (w imieniu z) nie jest obsługiwana przez usługę Azure AD B2C.**
>
> Wiele architektur obejmuje internetowego interfejsu API, który musi wywołać inny podrzędny interfejsu API sieci web, zarówno zabezpieczone za pomocą usługi Azure AD B2C. Ten scenariusz jest typowy w klientach natywnych, mających interfejs API sieci web zaplecza, która z kolei wywołuje usługi online firmy Microsoft, takich jak interfejs API programu Graph usługi Azure AD.
>
> Ten scenariusz łańcuchowych interfejsu API sieci web mogą być obsługiwane przy użyciu przyznania poświadczeń elementu nośnego JWT OAuth w wersji 2.0, znanych także jako przepływu w imieniu z. Jednak przepływ w imieniu z nie jest obecnie wdrażany w usłudze Azure AD B2C.

## <a name="register-a-web-api-and-publish-permissions"></a>Rejestrowanie internetowego interfejsu API i uprawnienia do publikowania

Przed żądania tokenu dostępu, należy najpierw zarejestrować internetowy interfejs API, a następnie opublikować uprawnienia (zakresy), które mogą być udzielone w aplikacji klienckiej.

### <a name="register-a-web-api"></a>Rejestrowanie internetowego interfejsu API

1. W menu funkcji usługi Azure AD B2C w witrynie Azure portal kliknij **aplikacji**.
1. Kliknij przycisk **+ Dodaj** w górnej części menu.
1. Wprowadź wartość **Nazwa**, która będzie opisywać aplikację na potrzeby klientów. Na przykład można wprowadzić nazwę "Interfejs API Contoso".
1. Przestaw przełącznik **Uwzględnij aplikację internetową/internetowy interfejs API** na wartość **Tak**.
1. Wprowadź dowolną wartość w **adresy URL odpowiedzi**. Na przykład wprowadź wartość `https://localhost:44316/`. Wartość nie ma znaczenia, ponieważ interfejs API powinien nie otrzymywać tokenu bezpośrednio z usługi Azure AD B2C.
1. Wprowadź **identyfikator URI aplikacji**. Jest to identyfikator używany na potrzeby interfejsu API sieci Web. Na przykład wprowadź wartość w polu "Uwagi". **Identyfikator URI Identyfikatora aplikacji** będzie wówczas `https://{tenantName}.onmicrosoft.com/notes`.
1. Kliknij pozycję **Utwórz**, aby zarejestrować aplikację.
1. Kliknij nowo utworzoną aplikację i skopiuj globalnie unikatowy **identyfikator klienta aplikacji**, który będzie używany w dalszej części kodu.

### <a name="publishing-permissions"></a>Publikowanie uprawnień

Zakresy, które są analogiczne do uprawnień, są wymagane, gdy wywołuje aplikację interfejsu API. Niektóre przykłady zakresy są "Odczyt" lub "write". Załóżmy, że chcesz, aby usługi sieci web lub aplikacji natywnej do "Odczyt" z interfejsu API. Aplikacja może wywołać usługi Azure AD B2C i żądania tokenu dostępu, który zapewnia dostęp do zakresu od "Odczyt". Aby emitować token dostępu usługi Azure AD B2C aplikacja musi mieć uprawnienie do "Odczyt" z określonego interfejsu API. Aby to zrobić, Twój interfejs API najpierw musi opublikować zakresu "Odczyt".

1. W ramach usługi Azure AD B2C **aplikacje** menu Otwórz interfejsu API sieci web aplikacji ("interfejs API Contoso").
1. Kliknij pozycję **Opublikowane zakresy**. Jest to miejsce, w którym można zdefiniować uprawnienia (zakresy), które mogą być udzielone innym aplikacjom.
1. Dodaj **wartości zakresu** w razie (na przykład "Odczyt"). Domyślnie zostanie zdefiniowany zakres „user_impersonation”. W razie potrzeby można zignorować to. Wprowadź opis zakresu w **nazwa zakresu** kolumny.
1. Kliknij pozycję **Zapisz**.

> [!IMPORTANT]
> **Nazwa zakresu** znajduje się opis **wartość zakresu**. Korzystając z zakresu, upewnij się użyć **wartość zakresu**.

## <a name="grant-a-native-or-web-app-permissions-to-a-web-api"></a>Udziel natywny lub uprawnień aplikacji do internetowego interfejsu API w sieci web

Gdy interfejs API jest skonfigurowany do publikowania zakresów, aplikacja kliencka musi otrzymać te zakresy za pośrednictwem witryny Azure portal.

1. Przejdź do **aplikacji** w menu funkcji usługi Azure AD B2C.
1. Zarejestruj aplikację kliencką ([aplikacji sieci web](active-directory-b2c-app-registration.md#register-a-web-app) lub [klienta natywnego](active-directory-b2c-app-registration.md#register-a-mobile-or-native-app)) Jeśli nie masz jeszcze takiego. Jeśli korzystasz z tego przewodnika jako punkt początkowy, musisz zarejestrować aplikację kliencką.
1. Kliknij pozycję **dostęp do interfejsu API**.
1. Kliknij pozycję **Dodaj**.
1. Wybierz swój internetowy interfejs API oraz zakresy (uprawnienia), po którym chcesz udzielić.
1. Kliknij przycisk **OK**.

> [!NOTE]
> Usługa Azure AD B2C nie poproś swojego klienta aplikacji użytkowników o ich zgodę. Zamiast tego wszystkie zgody znajduje się przez administratora, na podstawie uprawnień skonfigurowana między aplikacjami opisanych powyżej. Po odebraniu przyznawania uprawnień dla aplikacji wszyscy użytkownicy, którzy wcześniej mogli uzyskać to uprawnienie już nie będzie można to zrobić.

## <a name="requesting-a-token"></a>Żądania tokenu

Podczas żądania tokenu dostępu, aplikacja kliencka musi określić odpowiednich uprawnień w **zakres** parametr żądania. Na przykład, aby określić **wartość zakresu** "read" dla interfejsu API, który ma **identyfikator URI Identyfikatora aplikacji** z `https://contoso.onmicrosoft.com/notes`, byłoby zakres `https://contoso.onmicrosoft.com/notes/read`. Poniżej znajduje się przykład wysłanie żądania kodu autoryzacji do `/authorize` punktu końcowego.

> [!NOTE]
> Obecnie domen niestandardowych nie są obsługiwane razem z tokenów dostępu. Należy użyć domeny tenantName.onmicrosoft.com w adresie URL żądania.

```
https://<tenantName>.b2clogin.com/tfp/<tenantName>.onmicrosoft.com/<yourPolicyId>/oauth2/v2.0/authorize?client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
```

Aby nabyć wiele uprawnień w tym samym żądaniu, można dodać wiele wpisów w jednej **zakres** parametru, rozdzielone spacjami. Na przykład:

Adres URL zdekodowany:

```
scope=https://contoso.onmicrosoft.com/notes/read openid offline_access
```

Zakodowane w adresie URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread%20openid%20offline_access
```

Dla zasobu niż co otrzymuje umożliwiające aplikacji klienta, może zażądać więcej zakresy (uprawnienia). W przypadku wywołanie zakończy się pomyślnie, jeśli co najmniej jednego uprawnienia. Wartość wynikowa **dostępu\_tokenu** będzie miał jego oświadczenia "punkt połączenia usługi", wypełnione przy użyciu uprawnień, które zostały pomyślnie udzielono.

> [!NOTE] 
> Nie obsługujemy żądaniami uprawnień w odniesieniu do dwóch zasobów innej witryny sieci web w tym samym żądaniu. Tego rodzaju żądanie zakończy się niepowodzeniem.

### <a name="special-cases"></a>Specjalne przypadki

Standardowa OpenID Connect określono kilka wartości specjalne "scope". Następujące zakresy specjalne reprezentują uprawnienie "dostęp profil użytkownika" do:

* **openid**: żąda tokenu Identyfikacyjnego
* **w trybie offline\_dostępu**: żąda tokenu odświeżania (przy użyciu [przepływu kodu autoryzacji](active-directory-b2c-reference-oauth-code.md)).

Jeśli `response_type` parametru w `/authorize` żądanie zawiera `token`, `scope` parametr musi zawierać co najmniej jeden zasób zakres (inne niż `openid` i `offline_access`), zostaną przyznane. W przeciwnym razie `/authorize` żądanie zostanie zakończona z błędem.

## <a name="the-returned-token"></a>Zwrócony token

W pomyślnie minted **dostępu\_tokenu** (albo `/authorize` lub `/token` punktu końcowego), następujące oświadczenia będą znajdować się:

| Nazwa | Oświadczenie | Opis |
| --- | --- | --- |
|Odbiorcy |`aud` |**Identyfikator aplikacji** z pojedynczego zasobu, którego token przyznaje dostęp. |
|Zakres |`scp` |Uprawnienia udzielone do zasobu. Wiele udzielone uprawnienia będą rozdzielone spacjami. |
|Autoryzowane innych firm |`azp` |**Identyfikator aplikacji** aplikacji klienta, który zainicjował żądanie. |

Gdy interfejs API odbiera **dostępu\_tokenu**, musi ona [zweryfikować token](active-directory-b2c-reference-tokens.md) udowodnić, że token jest autentyczny i ma oświadczenia poprawna.

Firma Microsoft zawsze są otwarte opinie i sugestie! Jeśli masz trudności z tym tematem, opublikuj wpis w witrynie Stack Overflow, używając tagu ["azure-ad-b2c"](https://stackoverflow.com/questions/tagged/azure-ad-b2c). Żądania funkcji, dodaj je do [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

## <a name="next-steps"></a>Kolejne kroki

* Tworzenie interfejsu API sieci web przy użyciu [platformy .NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)
* Tworzenie interfejsu API sieci web przy użyciu [środowiska Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)
