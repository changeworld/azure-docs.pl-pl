---
title: Użyj Microsoft Authentication Library (MSAL) w chmurach narodowych — platforma tożsamości firmy Microsoft
description: Microsoft Authentication Library (MSAL) umożliwia deweloperom aplikacji można uzyskać tokenów w celu wywołania interfejsów API zabezpieczonej sieci web. Te interfejsy API sieci web może być programu Microsoft Graph, inne APIS firmy Microsoft, interfejsy API sieci web innych firm lub własnego interfejsu API sieci web. Biblioteka MSAL obsługuje wielu architektury aplikacji i platform.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18eccd6b6d29a43cc3fa13d133d449bf0b9be657
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075793"
---
# <a name="use-msal-in-national-cloud-environment"></a>Użycia biblioteki MSAL w środowisku chmury krajowe

[Chmury krajowe](authentication-national-cloud.md) są odizolowane fizycznie wystąpienia platformy Azure. Te regiony platformy Azure są przeznaczone do upewnij się, że wymagania dotyczące zgodności, niezależności i rezydencji danych są honorowane w granicach geograficznych.

Oprócz na całym świecie chmury firmy Microsoft Microsoft Authentication Library (MSAL) umożliwia deweloperom aplikacji w chmurach narodowych uzyskanie tokenów w celu uwierzytelniania i wywołań interfejsów API zabezpieczonej sieci web. Te interfejsy API sieci web może być programu Microsoft Graph lub innych APIS firmy Microsoft.

W tym chmury globalnej usługi Azure Active Directory (Azure AD) jest wdrażany w chmurach narodowych następujące:  

- Wersja platformy Azure dla administracji USA
- Azure w Chinach — 21Vianet
- Azure (Niemcy)

Ten przewodnik pokazuje, jak zarejestrować się w oraz do kont służbowych, uzyskiwanie tokenu dostępu i wywoływanie interfejsu API programu Microsoft Graph w [Microsoft cloud dla instytucji rządowych USA](https://azure.microsoft.com/global-infrastructure/government/) środowiska.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że zostały spełnione następujące wymagania wstępne.

### <a name="choose-the-appropriate-identities"></a>Wybierz odpowiednie tożsamości

[Platforma Azure Government](https://docs.microsoft.com/azure/azure-government/) aplikacje mogą używać tożsamości platformy Azure Government AD, a także publiczny tożsamości usługi Azure AD do uwierzytelniania użytkowników. Ponieważ można użyć dowolnego z tych tożsamości, należy zrozumieć, i zdecyduj, które punkt końcowy urzędu należy wybrać dla danego scenariusza:

- Publicznej platformy Azure AD: Często używane, jeśli Twoja organizacja ma już dzierżawę usługi Azure AD publicznej do działu pomocy technicznej usługi Office 365 (publiczne lub GCC) lub innej aplikacji.
- Usługa Azure AD dla instytucji publicznych: Często używane, jeśli Twoja organizacja już ma dzierżawy usługi Azure AD Government do działu pomocy technicznej usługi Office 365 (GCC wysokiej lub DoD) lub tworzysz nową dzierżawę Azure Government usługi AD.

Postanowiono, szczególną uwagę po którym wykonywana jest rejestracja aplikacji. Jeśli wybierzesz tożsamości usługi Azure AD publiczne dla aplikacji platformy Azure Government musi zarejestrować aplikację w dzierżawie usługi Azure AD publicznego.

### <a name="get-an-azure-government-subscription"></a>Uzyskiwanie subskrypcji platformy Azure Government

- Aby uzyskać subskrypcję platformy Azure Government, zobacz [nawiązywania połączenia z subskrypcji usługi na platformie Azure dla instytucji rządowych i zarządzanie nimi](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).
- Jeśli nie masz subskrypcji platformy Azure Government, Utwórz [bezpłatne konto](https://azure.microsoft.com/global-infrastructure/government/request/) przed przystąpieniem do wykonywania.

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>Krok 1: Rejestrowanie aplikacji

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.us/), aby zarejestrować aplikację.
    1. Punkty końcowe portalu platformy Azure dla innych chmur krajowych, możesz znaleźć [punkty końcowe rejestracji aplikacji](authentication-national-cloud.md#app-registration-endpoints)

1. Jeśli dzierżawa usługi zapewnia konta dostępu do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu i ustaw sesję portalu do żądanej usługi Azure AD.
1. Przejdź do platforma tożsamości firmy Microsoft dla deweloperów [rejestracje aplikacji](https://aka.ms/ra/ff) strony.
1. Po wyświetleniu strony **Rejestrowanie aplikacji** wprowadź nazwę aplikacji.
1. W obszarze **obsługiwane typy kont**, wybierz opcję **kont w dowolnym katalogu organizacji**.
1. W obszarze **identyfikator URI przekierowania** zaznacz **Web** platformy i ustaw wartość adres URL aplikacji oparta na serwerze sieci web. Zobacz sekcje poniżej, aby uzyskać instrukcje na temat ustawiania i uzyskać adres URL przekierowania w programie Visual Studio i języka Node.
1. Po zakończeniu wybierz pozycję **Rejestruj**.
1. Na stronie **Przegląd** aplikacji zanotuj wartość **Identyfikator aplikacji (klienta)**.
1. Ten samouczek wymaga [niejawne udzielić przepływ](v2-oauth2-implicit-grant-flow.md) włączenia. W okienku nawigacji po lewej stronie w zarejestrowanej aplikacji wybierz pozycję **Uwierzytelnianie**.
1. Na stronie **Ustawienia zaawansowane** w obszarze **Niejawne udzielenie** zaznacz obydwa pola wyboru: **Tokeny identyfikatorów** i **Tokeny dostępu**. Tokeny identyfikatorów i tokeny dostępu są wymagane, ponieważ ta aplikacja musi zalogować użytkowników i wywołać interfejs API.
1. Wybierz pozycję **Zapisz**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Krok 2:  Konfigurowanie serwera sieci web lub projektu

- [Pobierz pliki projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) dla lokalnego serwera internetowego, takie jak węzeł.

  lub

- [Pobieranie projektu programu Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

A następnie przejdź do ["Konfigurowanie usługi SPA JavaScript"](#step-4-configure-your-javascript-spa) do skonfigurowania przykładowego kodu przed jej wykonanie.

### <a name="step-3-use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Krok 3: Biblioteka Microsoft Authentication Library (MSAL) umożliwia logowanie użytkownika

Wykonaj czynności opisane w [samouczek Javascript](tutorial-v2-javascript-spa.md#create-your-project) do utworzenia projektu i integracja z Microsoft Authentication Library (MSAL) do logowania użytkownika.

### <a name="step-4-configure-your-javascript-spa"></a>Krok 4: Konfigurowanie aplikacji JEDNOSTRONICOWEJ JavaScript

W `index.html` plik utworzony podczas konfiguracji projektu, Dodaj informacje o rejestracji aplikacji. Dodaj następujący kod u góry strony, w ramach `<script></script>` tagów w treści swoje `index.html` pliku:

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

Gdzie:

- `Enter_the_Application_Id_here` jest **identyfikatorem aplikacji (klienta)** dla zarejestrowanej aplikacji.
- `Enter_the_Tenant_Info_Here` to wartość ustawiana na jedną z następujących opcji:
    - Jeśli aplikacja obsługuje **kont w tym katalogu organizacji**, Zastąp tę wartość za pomocą **identyfikator dzierżawy** lub **nazwa dzierżawy** (np. contoso.microsoft.com)
    - Jeśli aplikacja obsługuje tryb**Konta w dowolnym katalogu organizacyjnym**, zastąp tę wartość za pomocą wartości `organizations`
    -  Punkty końcowe uwierzytelniania dla wszystkich chmur krajowych, możesz znaleźć [punkty końcowe uwierzytelniania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)

     > [!NOTE]
     > Osobiste scenariuszy kont (MSA) firmy Microsoft nie są obsługiwane w chmurach krajowych.
  
-   `graphEndpoint` — punkt końcowy programu Microsoft Graph w chmurze firmy Microsoft dla instytucji rządowych USA.
    -  Punkty końcowe programu Microsoft Graph dla wszystkich chmur krajowych, możesz znaleźć [punkty końcowe programu Microsoft Graph, chmury krajowe](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)

## <a name="net"></a>.NET

Biblioteka MSAL .NET umożliwia logowanie użytkowników, uzyskanie tokenu i wywołania interfejsu API Microsoft Graph w chmurach krajowych.

Następującego samouczka przedstawiono sposób tworzenia aplikacji sieci Web platformy .NET Core 2.2 MVC, która używa protokołu OpenID Connect w celu logowania użytkowników przy użyciu konta "pracy i służbowe" w swojej organizacji, należący do chmur krajowych.

- Aby logować użytkowników i uzyskania tokenu, postępuj zgodnie z [w tym samouczku](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Do wywołania interfejsu API Microsoft Graph, postępuj zgodnie z [w tym samouczku](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o usługach:

- [Platforma Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure (Niemcy)](https://docs.microsoft.com/azure/germany/)