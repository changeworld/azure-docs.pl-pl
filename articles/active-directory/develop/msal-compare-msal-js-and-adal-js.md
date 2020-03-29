---
title: Różnice między MSAL.js i ADAL.js | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o różnicach między biblioteką uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js) a biblioteką uwierzytelniania usługi Azure AD dla języka JavaScript (ADAL.js) oraz o tym, jak wybrać, którego chcesz użyć.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 7238a78279528b4522d09178d00bf916f14bad88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696422"
---
# <a name="differences-between-msal-js-and-adal-js"></a>Różnice między MSAL JS i ADAL JS

Zarówno Biblioteka uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js), jak i biblioteka uwierzytelniania usługi Azure AD dla języka JavaScript (ADAL.js) są używane do uwierzytelniania jednostek usługi Azure AD i żądania tokenów z usługi Azure AD. Do tej pory większość deweloperów pracowała z usługą Azure AD dla deweloperów (w wersji 1.0) w celu uwierzytelnienia tożsamości usługi Azure AD (kont pracy i szkoły) przez żądanie tokenów przy użyciu usługi ADAL. Teraz za pomocą msal.js można uwierzytelnić szerszy zestaw tożsamości firmy Microsoft (tożsamości usługi Azure AD i kont Microsoft oraz kont społecznościowych i lokalnych za pośrednictwem usługi Azure AD B2C) za pośrednictwem platformy tożsamości firmy Microsoft (wersja 2.0).

W tym artykule opisano sposób wyboru między biblioteką uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js) a biblioteką uwierzytelniania usługi Azure AD dla języka JavaScript (ADAL.js) i porównuje dwie biblioteki.

## <a name="choosing-between-adaljs-and-msaljs"></a>Wybór między ADAL.js i MSAL.js

W większości przypadków chcesz użyć platformy tożsamości firmy Microsoft i pliku MSAL.js, który jest najnowszą generacją bibliotek uwierzytelniania firmy Microsoft. Za pomocą pliku MSAL.js można uzyskać tokeny dla użytkowników logujących się do aplikacji za pomocą usługi Azure AD (konta służbowe i szkolne), kont Microsoft (osobistych) (MSA) lub usługi Azure AD B2C.

Jeśli znasz już punkt końcowy w wersji 1.0 (i plik ADAL.js), możesz przeczytać [Co innego różni się od punktu końcowego w wersji 2.0?](active-directory-v2-compare.md).

Jednak nadal trzeba używać ADAL.js, jeśli aplikacja musi zalogować się użytkowników z wcześniejszych wersji [Usług Feds Active Directory (ADFS)](/windows-server/identity/active-directory-federation-services).

## <a name="key-differences-in-authentication-with-msaljs"></a>Kluczowe różnice w uwierzytelnianiu za pomocą pliku MSAL.js

### <a name="core-api"></a>Podstawowy interfejs API

* Plik ADAL.js używa [authenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) jako reprezentacji wystąpienia połączenia aplikacji z serwerem autoryzacji lub dostawcą tożsamości za pośrednictwem adresu URL urzędu. Wręcz przeciwnie, interfejs API msal.js jest zaprojektowany wokół aplikacji klienckiej agenta użytkownika (forma publicznej aplikacji klienckiej, w której kod klienta jest wykonywany w agencie użytkownika, takim jak przeglądarka internetowa). Zapewnia `UserAgentApplication` klasę do reprezentowania wystąpienia kontekstu uwierzytelniania aplikacji z serwerem autoryzacji. Aby uzyskać więcej informacji, zobacz [Inicjowanie przy użyciu pliku MSAL.js](msal-js-initializing-client-applications.md).

* W pliku ADAL.js metody pozyskiwania tokenów są skojarzone z `AuthenticationContext`jednym urzędem ustawionym w pliku . W pliku MSAL.js żądania tokenu nabycia mogą przyjmować `UserAgentApplication`wartości innych uprawnień niż te, które są ustawione w pliku . Dzięki temu msal.js do nabycia i pamięci podręcznej tokenów oddzielnie dla wielu dzierżawców i kont użytkowników w tej samej aplikacji.

* Metoda uzyskiwania i odnawiania tokenów po `acquireToken` cichu bez monitowania użytkowników jest nazwany w ADAL.js. W pliku MSAL.js ta `acquireTokenSilent` metoda jest nazywana bardziej opisową dla tej funkcji.

### <a name="authority-value-common"></a>Wartość organu`common`

W wersji 1.0 `https://login.microsoftonline.com/common` użycie urzędu umożliwi użytkownikom logowanie się przy użyciu dowolnego konta usługi Azure AD (dla dowolnej organizacji).

W wersji 2.0, przy użyciu `https://login.microsoftonline.com/common` urzędu, umożliwi użytkownikom zalogować się za pomocą dowolnego konta organizacji usługi Azure AD lub konta osobistego Microsoft (MSA). Aby ograniczyć logowanie tylko do kont usługi Azure AD (takie samo `https://login.microsoftonline.com/organizations`zachowanie jak w przypadku pliku ADAL.js), należy użyć . Aby uzyskać szczegółowe `authority` informacje, zobacz opcję konfiguracji w [inicjuj przy użyciu pliku MSAL.js](msal-js-initializing-client-applications.md).

### <a name="scopes-for-acquiring-tokens"></a>Zakresy pozyskiwania tokenów
* Zakres zamiast parametru zasobu w żądaniach uwierzytelniania w celu uzyskania tokenów

    Protokół w wersji 2.0 używa zakresów zamiast zasobów w żądaniach. Innymi słowy, gdy aplikacja musi zażądać tokenów z uprawnieniami dla zasobu, takiego jak MS Graph, różnica w wartościach przekazanych do metod biblioteki jest następująca:

    v1.0: zasób\:= https //graph.microsoft.com

    wersja 2.0: zakres\:= https //graph.microsoft.com/User.Read

    Można zażądać zakresów dla dowolnego interfejsu API zasobów przy użyciu identyfikatora URI interfejsu API\/w tym formacie: appidURI/scope Na przykład: https: /mytenant.onmicrosoft.com/myapi/api.read

    Tylko dla interfejsu API programu MS `user.read` Graph wartość\/zakresu jest mapowana na https: /graph.microsoft.com/User.Read i może być używana zamiennie.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Zakresy dynamiczne dla przyrostowej zgody.

    Podczas tworzenia aplikacji przy użyciu wersji 1.0, trzeba zarejestrować pełny zestaw uprawnień (zakresy statyczne) wymagane przez aplikację dla użytkownika do zgody w momencie logowania. W wersji 2.0 można użyć parametru scope, aby zażądać uprawnień w odpowiednim czasie. Są to tak zwane zakresy dynamiczne. Dzięki temu użytkownik może zapewnić przyrostową zgodę na zakresy. Jeśli więc na początku chcesz, aby użytkownik zalogował się do aplikacji i nie potrzebujesz żadnego dostępu, możesz to zrobić. Jeśli później potrzebujesz możliwość odczytu kalendarza użytkownika, można następnie zażądać zakresu kalendarza w metody acquireToken i uzyskać zgodę użytkownika. Przykład:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Zakresy interfejsów API systemu Windows 1.0

    Podczas uzyskiwania tokenów dla interfejsów API w wersji 1.0 przy użyciu pliku MSAL.js `.default` można zażądać wszystkich zakresów statycznych zarejestrowanych w interfejsie API, dołączając do identyfikatora URI identyfikatora aplikacji interfejsu API jako zakres. Przykład:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zapoznaj się z [porównaniem wersji 1.0 i v2.0](active-directory-v2-compare.md).
