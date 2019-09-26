---
title: Aplikacja internetowa, która loguje użytkowników (Rejestracja aplikacji) — platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację internetową, która loguje się do użytkowników (Rejestracja aplikacji)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ab7c3699abdd5c094b1b14cd53f76023fa8c1ac
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309603"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>Aplikacja sieci Web, która loguje się do użytkowników — Rejestracja aplikacji

Ta strona zawiera wyjaśnienia dotyczące rejestracji aplikacji dla aplikacji sieci Web, które logują się do użytkowników.

Aby zarejestrować aplikację, możesz użyć:

- Funkcja [szybkiego startu aplikacji sieci Web](#register-an-app-using-the-quickstarts) — Oprócz tworzenia aplikacji, Przewodniki Szybki start w Azure Portal zawierają przycisk o nazwie **Utwórz tę zmianę dla mnie**. Ten przycisk służy do ustawiania właściwości, które są potrzebne, nawet w przypadku istniejącej aplikacji. Należy dostosować wartości tych właściwości do własnego przypadku. W szczególności adres URL internetowego interfejsu API dla aplikacji prawdopodobnie będzie inny niż proponowana wartość domyślna, która również wpłynie na identyfikator URI wylogowania.
- Azure Portal, aby [ręcznie zarejestrować aplikację](#register-an-app-using-azure-portal)
- Program PowerShell i narzędzia wiersza polecenia

## <a name="register-an-app-using-the-quickstarts"></a>Rejestrowanie aplikacji przy użyciu przewodników Szybki Start

Jeśli przejdziesz do tego linku, możesz utworzyć uruchomienie aplikacji sieci Web przy użyciu narzędzia Bootstrap:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-using-azure-portal"></a>Rejestrowanie aplikacji przy użyciu Azure Portal

> [!NOTE]
> Portal, który ma być używany, różni się w zależności od tego, czy aplikacja działa w Microsoft Azureej chmurze publicznej czy w chmurze krajowej lub suwerennej. Aby uzyskać więcej informacji, zobacz [chmury narodowe](./authentication-national-cloud.md#app-registration-endpoints)


1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft. Alternatywnie możesz zalogować się do Azure Portal w chmurze krajowej.
1. Jeśli Twoje konto zapewnia dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu, a następnie ustaw sesję portalu na żądaną dzierżawę usługi Azure AD.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** a następnie wybierz pozycję **rejestracje aplikacji** > **Nowa rejestracja**.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

4. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
   1. Wybierz obsługiwane typy kont dla aplikacji (zobacz [obsługiwane typy kont](./v2-supported-account-types.md))
   1. W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `AspNetCore-WebApp`.
   1. W obszarze **Identyfikator URI przekierowania**Dodaj typ aplikacji oraz miejsce docelowe identyfikatora URI, które będą akceptować zwrócone odpowiedzi na tokeny po pomyślnym uwierzytelnieniu. Na przykład `https://localhost:44321/`.  Wybierz pozycję **Zarejestruj**.
1. Wybierz menu **Uwierzytelnianie**, a następnie dodaj następujące informacje:
   1. W polu **adres URL odpowiedzi**Dodaj `https://localhost:44321/signin-oidc` typ "Web".
   1. W sekcji **Ustawienia zaawansowane** ustaw pole **Adres URL wylogowywania** na wartość `https://localhost:44321/signout-oidc`.
   1. W obszarze **Niejawne przyznanie** zaznacz pole wyboru **Tokeny Identyfikatorów**.
   1. Wybierz pozycję **Zapisz**.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

4. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
   1. Wybierz obsługiwane typy kont dla aplikacji (zobacz [obsługiwane typy kont](./v2-supported-account-types.md))
   - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `MailApp-openidconnect-v2`.
   - W sekcji identyfikator URI przekierowania (opcjonalnie) wybierz pozycję **Sieć Web** w polu kombi i wprowadź następujące identyfikatory URI przekierowania `https://localhost:44326/`:.
1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
1. Wybierz menu **Uwierzytelnianie**, a następnie dodaj następujące informacje:
   - W sekcji **Zaawansowane ustawienia** | **niejawnego** przypisywania Sprawdź **tokeny identyfikatora** , ponieważ ten przykład wymaga, aby [przepływ niejawnego przydzielenia](v2-oauth2-implicit-grant-flow.md) był włączony do logowania użytkownika.
1. Wybierz pozycję **Zapisz**.

# <a name="javatabjava"></a>[Java](#tab/java)

4. Gdy zostanie wyświetlona **Strona Zarejestruj aplikację** , wprowadź przyjazną nazwę aplikacji, na przykład "Java-webapp", wybierz pozycję "konta w dowolnym katalogu organizacyjnym i osobiste konta Microsoft (np. Skype, Xbox, Outlook.com)" i wybierz pozycję "aplikacja sieci Web" /API "jako *Typ aplikacji*.
1. Kliknij pozycję **zarejestruj** , aby zarejestrować aplikację.
1. W menu po lewej stronie kliknij pozycję **uwierzytelnianie**, a następnie w obszarze *identyfikatory URI przekierowania*wybierz pozycję "Web". Należy wprowadzić dwa różne identyfikatory URI przekierowania: jeden dla strony logowania i jeden dla strony użytkownicy programu Graph. W obu przypadkach należy używać tego samego hosta i numeru portu, a następnie "/msal4jsample/Secure/AAD" dla strony logowania i "msal4jsample/Graph/users" na stronie użytkownicy.
 Domyślnie użycie przykładu: 

    - `http://localhost:8080/msal4jsample/secure/aad`. 
    - `http://localhost:8080/msal4jsample/graph/users`

Kliknij pozycję **Zapisz**.

# <a name="pythontabpython"></a>[Python](#tab/python)

4. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
   - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `python-webapp`.
   - Zmień **obsługiwane typy kont** na **konta w dowolnym katalogu organizacyjnym i osobiste konta Microsoft (np. Skype, Xbox, Outlook.com)** .
   - W sekcji identyfikator URI przekierowania (opcjonalnie) wybierz pozycję **Sieć Web** w polu kombi i wprowadź następujące identyfikatory URI przekierowania `http://localhost:5000/getAToken`:.
1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
1. Na stronie **Przegląd** aplikacji Znajdź wartość **Identyfikator aplikacji (klienta)** i Zapisz ją jako nowszą. Będzie ona potrzebna do skonfigurowania pliku konfiguracji programu Visual Studio dla tego projektu.
1. Na stronie Przegląd aplikacji wybierz sekcję **uwierzytelnianie** .
   - W sekcji **Ustawienia zaawansowane** Ustaw **adres URL wylogowywania** na`http://localhost:5000/logout`
1. Wybierz pozycję **Zapisz**.

---

## <a name="register-an-app-using-powershell"></a>Rejestrowanie aplikacji przy użyciu programu PowerShell

> [!NOTE]
> Obecnie program PowerShell usługi Azure AD tworzy tylko aplikacje z następującymi obsługiwanymi typami kont:
>
> - MyOrg (konta tylko w tym katalogu organizacji)
> - AnyOrg (konta w dowolnym katalogu organizacyjnym).
>
> Jeśli chcesz utworzyć aplikację, która umożliwia użytkownikom logowanie się przy użyciu osobistych kont Microsoft (np. Skype, XBox, Outlook.com), można najpierw utworzyć aplikację wielodostępną (obsługiwane typy kont = konta w dowolnym katalogu organizacyjnym), a następnie zmienić `signInAudience` właściwość w manifeście aplikacji z Azure Portal. Opisano to szczegółowo w kroku [1,3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) samouczka ASP.NET Core (i może być uogólniony do aplikacji sieci Web w dowolnym języku).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfiguracja kodu aplikacji](scenario-web-app-sign-user-app-configuration.md)
