---
title: Użyj Microsoft Authentication Library (MSAL) w chmurach narodowych — platforma tożsamości firmy Microsoft
description: Microsoft Authentication Library (MSAL) umożliwia deweloperom aplikacji można uzyskać tokenów w celu wywołania interfejsów API zabezpieczonej sieci web. Te interfejsy API sieci web może być programu Microsoft Graph, inne APIs firmy Microsoft, partnerów interfejsów API sieci web lub własnego interfejsu API sieci web. Biblioteka MSAL obsługuje wielu architektury aplikacji i platform.
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
ms.openlocfilehash: f9958356cae3c486ecf68e280f33d63c6a537b14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235263"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Użycia biblioteki MSAL w środowisku chmury krajowe

[Chmury krajowe](authentication-national-cloud.md) są odizolowane fizycznie wystąpienia platformy Azure. Te regiony platformy Azure pomagają, upewnij się, że wymagania dotyczące zgodności, niezależności i rezydencji danych są honorowane w granicach geograficznych.

Oprócz chmurze na całym świecie firmy Microsoft Microsoft Authentication Library (MSAL) umożliwia deweloperom w chmurach narodowych uzyskiwanie tokenów w celu uwierzytelniania i wywołań interfejsów API zabezpieczonej sieci web. Te interfejsy API sieci web może być lub Microsoft Graph APIs inne firmy Microsoft.

W tym chmury globalnej usługi Azure Active Directory (Azure AD) jest wdrażany w chmurach narodowych następujące:  

- Azure Government
- Azure w Chinach — 21Vianet
- Azure (Niemcy)

Ten przewodnik pokazuje, jak mają logować się do pracy i kont służbowych, Uzyskaj token dostępu i wywoływanie interfejsu API programu Microsoft Graph w [chmury Azure Government](https://azure.microsoft.com/global-infrastructure/government/) środowiska.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że spełniają te wymagania wstępne.

### <a name="choose-the-appropriate-identities"></a>Wybierz odpowiednie tożsamości

[Platforma Azure Government](https://docs.microsoft.com/azure/azure-government/) aplikacje mogą używać usługi Azure AD publiczny tożsamości i tożsamości platformy Azure Government AD do uwierzytelniania użytkowników. Można użyć dowolnego z tych tożsamości, dlatego musisz zdecydować, które punkt końcowy urzędu należy wybrać dla danego scenariusza:

- Publicznej platformy Azure AD: Często używane, jeśli Twoja organizacja ma już dzierżawę usługi Azure AD publicznej do działu pomocy technicznej usługi Office 365 (publiczne lub GCC) lub innej aplikacji.
- Usługa Azure AD dla instytucji publicznych: Często używane, jeśli Twoja organizacja już dzierżawą usługi Azure Government AD do działu pomocy technicznej usługi Office 365 (GCC wysokiej lub DoD) lub tworzy nową dzierżawę Azure Government usługi AD.

Po podjęciu decyzji, szczególną uwagę to, gdzie wykonać rejestrację aplikacji. Jeśli wybierzesz tożsamości usługi Azure AD publiczne dla aplikacji platformy Azure Government musi zarejestrować aplikację w dzierżawie usługi Azure AD publicznego.

### <a name="get-an-azure-government-subscription"></a>Uzyskiwanie subskrypcji platformy Azure Government

Aby uzyskać subskrypcję platformy Azure Government, zobacz [nawiązywania połączenia z subskrypcji usługi na platformie Azure dla instytucji rządowych i zarządzanie nimi](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).

Jeśli nie masz subskrypcji platformy Azure Government, Utwórz [bezpłatne konto](https://azure.microsoft.com/global-infrastructure/government/request/) przed przystąpieniem do wykonywania.

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>Krok 1: Rejestrowanie aplikacji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.us/).
    
   Punkty końcowe portalu platformy Azure dla innych chmur krajowych, możesz znaleźć [punkty końcowe rejestracji aplikacji](authentication-national-cloud.md#app-registration-endpoints).

1. Jeśli dzierżawa usługi zapewnia konta dostępu do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu i ustaw sesję portalu do żądanej usługi Azure AD.
1. Przejdź do [rejestracje aplikacji](https://aka.ms/ra/ff) strony na platformie tożsamości firmy Microsoft dla deweloperów.
1. Po wyświetleniu strony **Rejestrowanie aplikacji** wprowadź nazwę aplikacji.
1. W obszarze **obsługiwane typy kont**, wybierz opcję **kont w dowolnym katalogu organizacji**.
1. W **identyfikator URI przekierowania** zaznacz **Web** platformy i ustaw wartość adres URL aplikacji oparta na serwerze sieci web. Zobacz w kolejnych sekcjach, aby uzyskać instrukcje na temat ustawiania i uzyskać adres URL przekierowania w programie Visual Studio i języka Node.
1. Wybierz pozycję **Zarejestruj**.
1. Na stronie **Przegląd** aplikacji zanotuj wartość **Identyfikator aplikacji (klienta)** .
1. Ten samouczek wymaga włączenia [przepływie przyznawania niejawnego](v2-oauth2-implicit-grant-flow.md). W okienku po lewej stronie zarejestrowanej aplikacji wybierz **uwierzytelniania**.
1. W **Zaawansowane ustawienia**w obszarze **przyznawanie niejawne**, wybierz opcję **tokeny Identyfikatora** i **tokeny dostępu** pola wyboru. Identyfikator tokenów i tokenów dostępu są wymagane, ponieważ ta aplikacja wymaga logowania użytkowników i wywoływać interfejs API.
1. Wybierz pozycję **Zapisz**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Krok 2:  Konfigurowanie serwera sieci web lub projektu

- [Pobierz pliki projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) dla lokalnego serwera internetowego, takie jak węzeł.

  lub

- [Pobieranie projektu programu Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Następnie przejdź do [skonfigurować swoje SPA JavaScript](#step-4-configure-your-javascript-spa) do skonfigurowania przykładowego kodu przed jego uruchomieniem.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Krok 3: Loguje użytkownika za pomocą biblioteki uwierzytelniania firmy Microsoft

Wykonaj czynności opisane w [samouczek JavaScript](tutorial-v2-javascript-spa.md#create-your-project) do utworzenia projektu i integracji z biblioteki MSAL do logowania użytkownika.

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

W tym kodem:

- `Enter_the_Application_Id_here` jest **identyfikator aplikacji (klienta)** wartości dla aplikacji, która została zarejestrowana.
- `Enter_the_Tenant_Info_Here` jest ustawiony na jedną z następujących opcji:
    - Jeśli aplikacja obsługuje **kont w tym katalogu organizacji**, Zastąp tę wartość z Identyfikatorem dzierżawy lub nazwa (na przykład contoso.microsoft.com) dzierżawcy.
    - Jeśli aplikacja obsługuje **kont w dowolnym katalogu organizacji**, Zastąp tę wartość za pomocą `organizations`.
    
    Punkty końcowe uwierzytelniania dla wszystkich chmur krajowych, możesz znaleźć [punkty końcowe uwierzytelniania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Osobistych kont Microsoft nie są obsługiwane w chmurach krajowych.
  
- `graphEndpoint` jest punktem końcowym programu Microsoft Graph w chmurze firmy Microsoft dla instytucji rządowych USA.

   Punkty końcowe programu Microsoft Graph dla wszystkich chmur krajowych, możesz znaleźć [punktów końcowych z programu Microsoft Graph w chmurach narodowych](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="net"></a>.NET

Platformy MSAL.NET służy do logowania użytkowników, uzyskiwanie tokenów i wywołania interfejsu API programu Microsoft Graph w chmurach krajowych.

Następujące samouczki pokazują, jak utworzyć aplikację sieci Web platformy .NET Core 2.2 MVC. Aplikacja używa do logowania użytkowników przy użyciu kont służbowych w organizacji, która należy do chmury krajowe OpenID Connect.

- Aby logować użytkowników i uzyskać tokeny, postępuj zgodnie z [w tym samouczku](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Do wywołania interfejsu API programu Microsoft Graph, postępuj zgodnie z [w tym samouczku](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o usługach:

- [Platforma Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure (Niemcy)](https://docs.microsoft.com/azure/germany/)