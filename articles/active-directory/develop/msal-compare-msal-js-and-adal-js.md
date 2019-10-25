---
title: Różnice między MSAL. js i ADAL. js
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o różnicach między biblioteką uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL. js) i biblioteki uwierzytelniania usługi Azure AD dla języka JavaScript (ADAL. js) i sposobu wybierania, który ma być używany.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d831f9f1cf8dbf565d569f63ee6215fac80949d
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803173"
---
# <a name="differences-between-msal-js-and-adal-js"></a>Różnice między MSAL JS i ADAL JS

Zarówno Biblioteka uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL. js), jak i Biblioteka Azure AD Authentication Library dla języka JavaScript (ADAL. js) są używane do uwierzytelniania jednostek usługi Azure AD i żądania tokenów z usługi Azure AD. Do tej pory większość deweloperów pracowała z usługą Azure AD dla deweloperów (v 1.0) do uwierzytelniania tożsamości usługi Azure AD (konta służbowe), żądając tokenów przy użyciu biblioteki ADAL. Teraz korzystając z MSAL. js, można uwierzytelnić szerszy zbiór tożsamości firmy Microsoft (tożsamości usługi Azure AD i kont Microsoft, a także konta społecznościowe i lokalne za pośrednictwem Azure AD B2C) za pośrednictwem platformy tożsamości firmy Microsoft (v 2.0).

W tym artykule opisano sposób wybierania biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL. js) i biblioteki Azure AD Authentication Library dla języka JavaScript (ADAL. js) i porównywania dwóch bibliotek.

## <a name="choosing-between-adaljs-and-msaljs"></a>Wybieranie między bibliotekami ADAL. js i MSAL. js

W większości przypadków chcesz używać platformy tożsamości firmy Microsoft i MSAL. js, która jest najnowszą generacji bibliotek uwierzytelniania firmy Microsoft. Korzystając z MSAL. js, uzyskujesz tokeny dla użytkowników logujących się do aplikacji przy użyciu usługi Azure AD (konta służbowe), kont Microsoft (osobistych) lub Azure AD B2C.

Jeśli znasz już punkt końcowy v 1.0 (i ADAL. js), możesz zapoznać się z [informacjami o tym, co się dzieje w punkcie końcowym v 2.0?](active-directory-v2-compare.md).

Jednak nadal musisz użyć biblioteki ADAL. js, jeśli aplikacja musi zalogować się użytkowników przy użyciu wcześniejszych wersji [Active Directory Federation Services (AD FS)](/windows-server/identity/active-directory-federation-services).

## <a name="key-differences-in-authentication-with-msaljs"></a>Kluczowe różnice w uwierzytelnianiu za pomocą MSAL. js

### <a name="core-api"></a>Podstawowy interfejs API

* Biblioteka ADAL. js używa [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) jako reprezentacji wystąpienia połączenia aplikacji z serwerem autoryzacji lub dostawcą tożsamości za pośrednictwem adresu URL urzędu certyfikacji. W przeciwieństwie do interfejsu API MSAL. js jest przeznaczony dla aplikacji klienckiej agenta użytkownika (forma publicznej aplikacji klienckiej, w której kod klienta jest wykonywany w agencie użytkownika, takim jak przeglądarka sieci Web). Udostępnia klasę `UserAgentApplication` do reprezentowania wystąpienia kontekstu uwierzytelniania aplikacji z serwerem autoryzacji. Aby uzyskać więcej informacji, zobacz [Inicjowanie przy użyciu MSAL. js](msal-js-initializing-client-applications.md).

* W bibliotece ADAL. js metody uzyskiwania tokenów są skojarzone z pojedynczym zestawem uprawnień w `AuthenticationContext`. W MSAL. js żądania tokenu pozyskiwania mogą przyjmować różne wartości urzędu niż wartość ustawiona w `UserAgentApplication`. Dzięki temu MSAL. js może pobierać i buforować tokeny oddzielnie dla wielu dzierżawców i kont użytkowników w tej samej aplikacji.

* Metoda uzyskiwania i odnawiania tokenów dyskretnie bez monitowania użytkowników o nazwie `acquireToken` w bibliotece ADAL. js. W MSAL. js ta metoda ma nazwę `acquireTokenSilent`, aby mieć więcej opisów tej funkcji.

### <a name="authority-value-common"></a>Wartość urzędu `common`

W wersji 1.0 użycie urzędu `https://login.microsoftonline.com/common` umożliwi użytkownikom logowanie się przy użyciu dowolnego konta usługi Azure AD (dla dowolnej organizacji).

W wersji 2.0 przy użyciu urzędu `https://login.microsoftonline.com/common` użytkownicy będą mogli zalogować się przy użyciu dowolnego konta organizacji usługi Azure AD lub konta osobistego firmy Microsoft (MSA). Aby ograniczyć logowanie tylko do kont usługi Azure AD (takie samo zachowanie jak w przypadku biblioteki ADAL. js), należy użyć `https://login.microsoftonline.com/organizations`. Aby uzyskać szczegółowe informacje, zobacz opcję konfiguracji `authority` w artykule [Inicjowanie przy użyciu MSAL. js](msal-js-initializing-client-applications.md).

### <a name="scopes-for-acquiring-tokens"></a>Zakresy do pozyskiwania tokenów
* Zakres zamiast parametru zasobu w żądaniach uwierzytelnienia w celu uzyskania tokenów

    Protokół v 2.0 używa zakresów zamiast zasobów w żądaniach. Innymi słowy, gdy aplikacja musi zażądać tokenów mających uprawnienia do zasobu, takiego jak MS Graph, różnica wartości przesyłanych do metod biblioteki jest następująca:

    v 1.0: Resource = https\://graph.microsoft.com

    v 2.0: Scope = https\://graph.microsoft.com/User.Read

    Możesz zażądać zakresów dla dowolnego interfejsu API zasobów przy użyciu identyfikatora URI interfejsu API w tym formacie: appidURI/Scope na przykład: https: \//moja dzierżawca. onmicrosoft. com/myapi/API. Read

    W przypadku usługi MS interfejs API programu Graph wartość zakresu `user.read` mapuje do protokołu https: \//Graph. Microsoft. com/user. Read i może być używana zamiennie.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Zakresy dynamiczne na potrzeby przyrostowej zgody.

    Podczas kompilowania aplikacji przy użyciu wersji 1.0 należy zarejestrować pełen zestaw uprawnień (zakresy statyczne) wymagane przez aplikację, aby użytkownik mógł wyrazić zgodę na zalogowanie się. W wersji 2.0 można użyć parametru scope, aby zażądać uprawnień w odpowiednim czasie. Są one nazywane zakresami dynamicznymi. Dzięki temu użytkownik może zapewnić przyrostową zgodę na zakresy. Tak więc jeśli na początku użytkownik chce tylko zalogować się do aplikacji i nie ma żadnego rodzaju dostępu, możesz to zrobić. Jeśli później potrzebujesz możliwości odczytywania kalendarza użytkownika, możesz zażądać zakresu kalendarza w metodach acquireToken i uzyskać zgodę użytkownika. Na przykład:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Zakresy interfejsów API 1.0

    Podczas uzyskiwania tokenów dla interfejsów API języka V 1.0 przy użyciu MSAL. js można zażądać wszystkich statycznych zakresów zarejestrowanych w interfejsie API, dołączając `.default` do identyfikatora URI aplikacji interfejsu API jako zakresu. Na przykład:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz [porównanie v 1.0 i 2.0](active-directory-v2-compare.md).
