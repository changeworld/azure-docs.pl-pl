---
title: Różnice między MSAL.js i ADAL.js | Azure
description: Dowiedz się więcej o różnicach między Biblioteka Microsoft Authentication Library dla języka JavaScript (MSAL.js) i Azure AD Authentication Library dla języka JavaScript (ADAL.js) oraz sposobu wybierania, której mają zostać użyte.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72da1130efc288c03229d990b75bc96c941265d7
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544297"
---
# <a name="differences-between-msal-js-and-adal-js"></a>Różnice między MSAL JS i JS biblioteki ADAL

Zarówno Biblioteka Microsoft Authentication Library dla języka JavaScript (MSAL.js) i Azure AD Authentication Library dla języka JavaScript (ADAL.js) są używane do uwierzytelniania jednostki usługi Azure AD i żądać tokenów z usługi Azure AD. Aż do chwili Większość programistów mający doświadczenie z usługą Azure AD dla deweloperów (1.0) do uwierzytelniania tożsamości usługi Azure AD (kont służbowych), żądając tokenów przy użyciu biblioteki ADAL. Teraz korzystając z MSAL.js, można uwierzytelniać szerszego zakresu tożsamości firmy Microsoft (tożsamości usługi Azure AD i konta Microsoft i kont społecznościowych i lokalnych przy użyciu usługi Azure AD B2C) dzięki platformie tożsamości firmy Microsoft (w wersji 2.0).

W tym artykule opisano, jak dokonać wyboru między Biblioteka Microsoft Authentication Library dla języka JavaScript (MSAL.js) i Azure AD Authentication Library dla języka JavaScript (ADAL.js) i porównuje dwie biblioteki.

## <a name="choosing-between-adaljs-and-msaljs"></a>Wybieranie między programami ADAL.js i MSAL.js

W większości przypadków, którego chcesz użyć z platformą Microsoft identity i MSAL.js, który jest najnowsza generacja bibliotek uwierzytelniania firmy Microsoft. Za pomocą MSAL.js, możesz uzyskać tokeny dla użytkowników, logowanie do aplikacji za pomocą usługi Azure AD (kont służbowych) konta Microsoft (osobistych) (MSA) lub usługi Azure AD B2C.

Jeśli już znasz z punktu końcowego w wersji 1.0 (i ADAL.js), warto przeczytać [czym się różni od punktu końcowego v2.0?](active-directory-v2-compare.md).

Nadal jednak należy używać ADAL.js, jeśli Twoja aplikacja potrzebuje do logowania użytkowników z wcześniejszymi wersjami programu [Active Directory Federation Services (ADFS)](/windows-server/identity/active-directory-federation-services).

## <a name="key-differences-in-authentication-with-msaljs"></a>Podstawowe różnice w przypadku uwierzytelniania za pomocą MSAL.js

### <a name="core-api"></a>Podstawowego interfejsu API

* Używa ADAL.js [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) jako reprezentacja wystąpienia aplikacji połączenia z serwerem autoryzacji lub dostawcy tożsamości za pomocą adresu URL urzędu. Przeciwnie, interfejs API MSAL.js zaprojektowany pod kątem aplikacji klienckiej agent użytkownika (formularz aplikacji publicznych klienta, w którym kod klienta jest wykonywane w agenta użytkownika, takie jak przeglądarki sieci web). Zapewnia `UserAgentApplication` klasy do reprezentowania wystąpienie kontekstu uwierzytelniania aplikacji za pomocą serwera autoryzacji. Aby uzyskać więcej informacji, zobacz [zainicjować przy użyciu MSAL.js](msal-js-initializing-client-applications.md).

* W ADAL.js, metody służące do uzyskania tokenów są skojarzone z urzędem pojedynczego w `AuthenticationContext`. MSAL.js, Uzyskaj token żądania może to zająć wartości urzędu innego niż ustawiony `UserAgentApplication`. Dzięki temu MSAL.js nabywania i tokeny pamięci podręcznej oddzielnie dla wielu dzierżawców i kont użytkowników w tej samej aplikacji.

* Metoda uzyskania i odnawianie tokenów w trybie dyskretnym bez monitowania użytkowników o nazwie `acquireToken` w ADAL.js. W MSAL.js, ta metoda ma nazwę `acquireTokenSilent` się bardziej opisowe tej funkcji.

### <a name="authority-value-common"></a>Wartość urzędu `common`

W wersji 1.0 za pomocą `https://login.microsoftonline.com/common` urzędu umożliwi użytkownikom logowanie się przy użyciu dowolnego konta usługi Azure AD (dla każdej organizacji).

W wersji 2.0 za pomocą `https://login.microsoftonline.com/common` urząd umożliwi użytkownikom logowanie się przy użyciu dowolnego konta organizacji usługi Azure AD lub osobiste konto Microsoft (MSA). Aby ograniczyć logowanie do tylko konta usługi Azure AD (takie samo zachowanie jako za pomocą ADAL.js), należy użyć `https://login.microsoftonline.com/organizations`. Aby uzyskać więcej informacji, zobacz `authority` opcji konfiguracji [zainicjować przy użyciu MSAL.js](msal-js-initializing-client-applications.md).

### <a name="scopes-for-acquiring-tokens"></a>Zakresy na potrzeby uzyskiwania tokenów
* Zakres zamiast parametru zasobów w żądaniach uwierzytelniania, można uzyskać tokenów

    Protokół v2.0 używa zakresów zamiast resource w żądaniach. Innymi słowy gdy aplikacja musi żądać tokenów przy użyciu uprawnień dla zasobów, takich jak MS Graph, różnica w wartości przekazane do metody biblioteki następująco:

    Wersja 1.0: zasobu =https://graph.microsoft.com

    w wersji 2.0: zakres = https://graph.microsoft.com/User.Read

    Możesz poprosić o zakresy dla dowolnego zasobu interfejsu API za pomocą identyfikatora URI interfejsu API w następującym formacie: appidURI/zakresu, na przykład: https://mytenant.onmicrosoft.com/myapi/api.read

    Tylko w przypadku interfejsu API programu Graph MS, wartość zakresu `user.read` mapuje https://graph.microsoft.com/User.Read i mogą być używane zamiennie.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Dynamiczne zakresów dla przyrostowych zgody.

    Podczas kompilowania aplikacji przy użyciu v1.0, potrzebne do zarejestrowania pełnego zestawu uprawnień (zakresy statyczne) wymagany przez aplikację dla użytkownika o zgodę na podczas logowania. W wersji 2.0 można użyć parametru zakresu, aby zażądać uprawnień w czasie, o których chcesz je. Są one nazywane dynamiczne zakresów. Dzięki temu użytkownikowi przyrostowe zgody do zakresów. Dlatego jeśli na początku chcesz użytkownikowi zalogowanie się do aplikacji, które nie potrzebują dostępu żadnego rodzaju, możesz to zrobić. Jeśli później konieczne odczytywanie kalendarza użytkownika, można żądania zakresu kalendarza w metodach acquireToken i uzyskać zgodę użytkownika. Na przykład:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Zakresy dla interfejsów API w wersji 1.0

    Podczas pobierania tokenów dla interfejsów API w wersji 1.0 przy użyciu MSAL.js, możesz poprosić o wszystkie zakresy statyczne zarejestrowany w interfejsie API, dodając `.default` do identyfikator URI Identyfikatora aplikacji interfejsu API jako zakres. Na przykład:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, zobacz [porównanie w wersji 1.0 i 2.0](active-directory-v2-compare.md).
