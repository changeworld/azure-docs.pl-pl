---
title: Rejestrowanie aplikacji w usłudze Azure Active Directory B2C | Microsoft Docs
description: Jak zarejestrować aplikację w usłudze Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: get-started-article
ms.date: 6/13/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 354c5da815f19e4366a04c8c031fd9cbca6f060b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711862"
---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: rejestrowanie aplikacji

Ten samouczek szybkiego startu pomaga zarejestrować aplikację w dzierżawie usługi Microsoft Azure Active Directory (Azure AD) B2C w ciągu kilku minut. Kiedy skończysz, Twoja aplikacja będzie zarejestrowana do użycia w dzierżawie usługi Azure AD B2C.

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć aplikację, która akceptuje tworzenie kont i logowanie użytkowników, musisz najpierw zarejestrować aplikację w dzierżawie usługi Azure Active Directory B2C. Aby utworzyć własną dzierżawę, wykonaj kroki opisane w temacie [Tworzenie dzierżawy usługi Azure AD B2C](active-directory-b2c-get-started.md).

Aplikacje utworzone w witrynie Azure Portal muszą być zarządzane z tej samej lokalizacji. Jeśli edytujesz aplikacje usługi Azure AD B2C przy użyciu programu PowerShell lub innego portalu, stają się one nieobsługiwane i przestają działać w usłudze Azure AD B2C. Szczegóły możesz znaleźć w sekcji [Uszkodzone aplikacje](#faulted-apps). 

W tym artykule są używane przykłady, które pomogą Ci rozpocząć pracę z naszymi przykładami. Więcej informacji o tych przykładach możesz znaleźć w kolejnych artykułach.

## <a name="navigate-to-b2c-settings"></a>Przechodzenie do ustawień usługi B2C

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi B2C. 

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](../../includes/active-directory-b2c-portal-navigate-b2c-service.md)]

## <a name="choose-next-steps-based-on-your-application-type"></a>Wybierz następne kroki na podstawie typu aplikacji

* [Rejestrowanie aplikacji internetowej](#register-a-web-app)
* [Rejestrowanie internetowego interfejsu API](#register-a-web-api)
* [Rejestrowanie aplikacji mobilnej lub natywnej](#register-a-mobile-or-native-app)
 
### <a name="register-a-web-app"></a>Rejestrowanie aplikacji internetowej

[!INCLUDE [active-directory-b2c-register-web-app](../../includes/active-directory-b2c-register-web-app.md)]

### <a name="create-a-web-app-client-secret"></a>Tworzenie klucza tajnego klienta aplikacji internetowej

Jeśli Twoja aplikacja internetowa wywołuje internetowy interfejs API zabezpieczony przez usługę Azure AD B2C, wykonaj następujące kroki:
   1. Utwórz wpis tajny aplikacji, przechodząc do bloku **Klucze** i klikając przycisk **Wygeneruj klucz**. Zanotuj wartość pola **Klucz aplikacji**. Użyj tej wartości jako wpisu tajnego aplikacji w kodzie Twojej aplikacji.
   2. Kliknij pozycję **Dostęp do interfejsu API**, kliknij pozycję **Dodaj** i wybierz swój internetowy interfejs API oraz zakresy (uprawnienia).

> [!NOTE]
> **Klucz tajny aplikacji** jest ważnym poświadczeniem zabezpieczeń i powinien być odpowiednio zabezpieczony.
> 

[Przejdź do **następnych kroków**](#next-steps)

### <a name="register-a-web-api"></a>Rejestrowanie internetowego interfejsu API

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

Kliknij przycisk **Opublikowane zakresy**, aby w razie potrzeby dodać więcej zakresów. Domyślnie jest definiowany zakres „user_impersonation”. Zakres user_impersonation daje innym aplikacjom możliwość dostępu do tego interfejsu API w imieniu zalogowanego użytkownika. Jeśli chcesz, możesz usunąć zakres user_impersonation.

[Przejdź do **następnych kroków**](#next-steps)

### <a name="register-a-mobile-or-native-app"></a>Rejestrowanie aplikacji mobilnej lub natywnej

[!INCLUDE [active-directory-b2c-register-mobile-native-app](../../includes/active-directory-b2c-register-mobile-native-app.md)]

[Przejdź do **następnych kroków**](#next-steps)

## <a name="limitations"></a>Ograniczenia

### <a name="choosing-a-web-app-or-api-reply-url"></a>Wybieranie aplikacji internetowej lub adresu URL odpowiedzi interfejsu API

Obecnie aplikacje, które są zarejestrowane w usłudze Azure AD B2C, mają wartości adresów URL odpowiedzi ograniczone do określonego zestawu. Adres URL odpowiedzi dla aplikacji i usług internetowych musi zaczynać się od schematu `https` i wartości wszystkich adresów URL odpowiedzi muszą współużytkować jedną domenę DNS. Na przykład nie można zarejestrować aplikacji internetowej z jednym z następujących adresów URL odpowiedzi:

`https://login-east.contoso.com`

`https://login-west.contoso.com`

System rejestracji porównuje całą nazwę DNS istniejącego adresu URL odpowiedzi z nazwą DNS dodawanego adresu URL odpowiedzi. Żądanie dodania nazwy DNS zakończy się niepowodzeniem, jeśli będzie spełniony jeden z następujących warunków:

* Cała nazwa DNS nowego adresu URL odpowiedzi nie będzie zgodna z nazwą DNS istniejącego adresu URL odpowiedzi.
* Cała nazwa DNS nowego adresu URL odpowiedzi nie jest poddomeną istniejącego adresu URL odpowiedzi.

Na przykład jeśli aplikacja ma następujący adres URL odpowiedzi:

`https://login.contoso.com`

Można dodać do niego adres w następujący sposób:

`https://login.contoso.com/new`

W takim przypadku nazwa DNS jest idealnie zgodna. Można też zrobić tak:

`https://new.login.contoso.com`

W takim przypadku przywoływana jest poddomena DNS domeny login.contoso.com. Jeśli chcesz mieć aplikację z adresami URL odpowiedzi login-east.contoso.com i login-west.contoso.com, musisz dodać następujące adresy URL w podanej kolejności:

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Dwa ostatnie adresy można dodać, ponieważ są poddomenami pierwszego adresu URL odpowiedzi, contoso.com.

### <a name="choosing-a-native-app-redirect-uri"></a>Wybieranie identyfikatora URI przekierowania aplikacji natywnej

Istnieją dwie ważne kwestie, które należy wziąć pod uwagę podczas wybierania identyfikatora URI przekierowania dla aplikacji mobilych/natywnych:

* **Unikatowość**: schemat identyfikatora URI przekierowania powinien być unikatowy dla każdej aplikacji. W tym przykładzie (com.onmicrosoft.contoso.appname://przekierowanie/ścieżka) schematem jest fragment com.onmicrosoft.contoso.appname. Zalecamy stosowanie się do tego wzorca. Jeśli dwie aplikacje mają ten sam schemat, użytkownik zobaczy okno dialogowe „Wybór aplikacji”. Jeśli użytkownik dokona nieprawidłowego wyboru, logowanie nie powiedzie się.
* **Kompletność**: identyfikator URI przekierowania musi mieć schemat i ścieżkę. Ścieżka musi zawierać co najmniej jeden ukośnik po nazwie domeny (na przykład identyfikator //contoso/ jest prawidłowy, ale identyfikator //contoso jest błędny).

Upewnij się, że w identyfikatorze URI przekierowywania nie ma żadnych znaków specjalnych, takich jak podkreślenia.

### <a name="faulted-apps"></a>Uszkodzone aplikacje

NIE NALEŻY edytować aplikacji B2C:

* W innych portalach zarządzania aplikacjami, takich jak [portal rejestracji aplikacji](https://apps.dev.microsoft.com/).
* Korzystanie z interfejsu API programu Graph lub programu PowerShell

Jeśli poddasz edycji aplikację usługi Azure AD B2C w opisany sposób i spróbujesz ponownie edytować ją za pomocą funkcji usługi Azure AD B2C w witrynie Azure Portal, stanie się ona uszkodzoną aplikacją i nie będzie można z niej korzystać w usłudze Azure AD B2C. Musisz usunąć taką aplikację i utworzyć ją ponownie.

Aby usunąć aplikację, przejdź do [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/) i usuń ją tam. Aby aplikacja była widoczna, musisz być jej właścicielem (a nie tylko administratorem dzierżawy).

## <a name="next-steps"></a>Następne kroki

Po zarejestrowaniu aplikacji w usłudze Azure AD B2C możesz wykonać czynności opisane w jednym z [samouczków szybkiego startu](active-directory-b2c-overview.md), aby rozpocząć pracę.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej platformy ASP.NET z rejestrowaniem, logowaniem i resetowaniem hasła](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
