---
title: Samouczek - zarejestrować swoje aplikację w celu włączenia zapisywania się i zaloguj się przy użyciu Azure Active Directory B2C | Dokumenty Microsoft
description: Azure umożliwia tworzenie dzierżawy Azure AD B2C i zarejestrowanie aplikacji z nim.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: patricka
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/08/2018
ms.author: davidmu
ms.openlocfilehash: 85e61f96ff829052e857d4823b3c944c6d981d2f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-register-an-application-to-enable-sign-up-and-sign-in-using-azure-active-directory-b2c"></a>Samouczek: Rejestrowania aplikacji umożliwienie zapisywania się i zaloguj się przy użyciu Azure Active Directory B2C

Ten samouczek pomaga utworzyć dzierżawcę B2C Microsoft Azure usługi Active Directory (Azure AD) i zarejestrowanie aplikacji z nim w ciągu kilku minut.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie dzierżawy usługi Azure AD B2C
> * Link do subskrypcji dzierżawy
> * Rejestrowanie aplikacji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Tworzenie dzierżawy usługi Azure AD B2C

Nie można włączyć funkcji B2C w istniejących dzierżawców. Musisz utworzyć dzierżawy usługi Azure AD B2C.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

Gratulacje, utworzono dzierżawy usługi Azure Active Directory B2C. Jesteś administratorem globalnym dzierżawcy. W razie potrzeby możesz dodawać innych administratorów globalnych. Aby przełączyć się do nowej dzierżawy, kliknij przycisk *Zarządzanie nowe łącze dzierżawy*.

![Zarządzanie nowe łącze dzierżawy](./media/active-directory-b2c-get-started/manage-new-b2c-tenant-link.png)

> [!IMPORTANT]
> Jeśli planujesz używać dzierżawy B2C do aplikacji produkcyjnej, przeczytaj artykuł dotyczący [skali produkcji a dzierżawcy usługi B2C w wersji zapoznawczej](active-directory-b2c-reference-tenant-type.md). Istnieją znane problemy podczas usuwania istniejącej dzierżawy B2C i utwórz go ponownie z tą samą nazwą domeny. Musisz utworzyć dzierżawy B2C o nazwie innej domeny.
>
>

## <a name="link-your-tenant-to-your-subscription"></a>Link do subskrypcji dzierżawy

Musisz połączyć dzierżawy usługi Azure AD B2C do subskrypcji platformy Azure, aby włączyć wszystkie funkcje B2C i opłacać opłaty za użycie. Aby dowiedzieć się więcej, przeczytaj [w tym artykule](active-directory-b2c-how-to-enable-billing.md). Jeśli nie możesz połączyć dzierżawy usługi Azure AD B2C do subskrypcji platformy Azure, niektóre funkcje jest zablokowany, a zostanie wyświetlony komunikat ostrzegawczy ("bez subskrypcji połączone z tej dzierżawy B2C lub na potrzeby subskrypcji uwagi.") w ustawieniach usługi B2C. Należy wykonać ten krok, przed wysłaniem aplikacji w środowisku produkcyjnym.


[!INCLUDE [active-directory-b2c-find-service-settings](../../includes/active-directory-b2c-find-service-settings.md)]

Można także przejść do bloku, wprowadzając `Azure AD B2C` w **wyszukiwania zasobów** w górnej części portalu. Na liście wyników wybierz **usługi Azure AD B2C** B2C blok ustawień dostępu do.

## <a name="register-your-application"></a>Rejestrowanie aplikacji

Aby utworzyć aplikację, która akceptuje tworzenie kont i logowanie użytkowników, musisz najpierw zarejestrować aplikację w dzierżawie usługi Azure Active Directory B2C. Aby utworzyć własną dzierżawę, wykonaj kroki opisane w temacie [Tworzenie dzierżawy usługi Azure AD B2C](active-directory-b2c-get-started.md).

Aplikacje utworzone w witrynie Azure Portal muszą być zarządzane z tej samej lokalizacji. Jeśli edytujesz aplikacje usługi Azure AD B2C przy użyciu programu PowerShell lub innego portalu, stają się one nieobsługiwane i przestają działać w usłudze Azure AD B2C. Szczegóły możesz znaleźć w sekcji [Uszkodzone aplikacje](#faulted-apps). 

W tym artykule są używane przykłady, które pomogą Ci rozpocząć pracę z naszymi przykładami. Więcej informacji o tych przykładach możesz znaleźć w kolejnych artykułach.

### <a name="navigate-to-b2c-settings"></a>Przechodzenie do ustawień usługi B2C

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi B2C. 

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](../../includes/active-directory-b2c-portal-navigate-b2c-service.md)]

### <a name="choose-next-steps-based-on-your-application-type"></a>Wybierz następne kroki na podstawie typu aplikacji

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

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób:

> [!div class="checklist"]
> * Tworzenie dzierżawy usługi Azure AD B2C
> * Link do subskrypcji dzierżawy
> * Rejestrowanie aplikacji

> [!div class="nextstepaction"]
> [Włączanie aplikacji sieci web do uwierzytelniania kont](active-directory-b2c-app-registration.md)