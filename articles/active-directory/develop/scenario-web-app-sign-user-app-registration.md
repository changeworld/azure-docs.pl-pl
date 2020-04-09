---
title: Rejestrowanie aplikacji sieci Web, która loguje się do użytkowników — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak zarejestrować aplikację internetową, która loguje się do użytkowników
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7d2eb5356b1abc54508fd6bf8d35fd9fc39d02ec
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881583"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>Aplikacja sieci Web, która loguje się do użytkowników: rejestracja aplikacji

W tym artykule opisano szczegóły rejestracji aplikacji dla aplikacji sieci web, która loguje się do użytkowników.

Aby zarejestrować aplikację, można użyć:

- Szybki [start aplikacji internetowej](#register-an-app-by-using-the-quickstarts). Oprócz tego, że jest to doskonałe pierwsze doświadczenie w tworzeniu aplikacji, przewodniki Szybki start w witrynie Azure portal zawierają przycisk o nazwie **Make this change for me**. Za pomocą tego przycisku można ustawić właściwości, których potrzebujesz, nawet dla istniejącej aplikacji. Musisz dostosować wartości tych właściwości do własnego przypadku. W szczególności adres URL interfejsu API sieci web dla aplikacji prawdopodobnie będzie się różnić od proponowanego domyślnego, co będzie również miało wpływ na identyfikator URI wylogowywania.
- Portal Azure, aby [zarejestrować aplikację ręcznie](#register-an-app-by-using-the-azure-portal).
- Narzędzia programu PowerShell i wiersza polecenia.

## <a name="register-an-app-by-using-the-quickstarts"></a>Rejestrowanie aplikacji przy użyciu przewodnika Szybki start

Możesz użyć tych linków do uruchamiania aplikacji sieci web:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Rejestrowanie aplikacji przy użyciu portalu Azure

> [!NOTE]
> Portal do użycia różni się w zależności od tego, czy aplikacja jest uruchamiana w chmurze publicznej platformy Microsoft Azure, czy w chmurze krajowej lub suwerennej. Aby uzyskać więcej informacji, zobacz [Chmury narodowe](./authentication-national-cloud.md#app-registration-endpoints).


1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft. Alternatywnie zaloguj się do witryny Azure portal wyboru dla chmury krajowej.
1. Jeśli twoje konto daje ci dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu. Następnie ustaw sesję portalu na żądaną dzierżawę usługi Azure Active Directory (Azure AD).
1. W lewym okienku wybierz usługę **Azure Active Directory,** a następnie wybierz pozycję **Rejestracje** > aplikacji**Nowa rejestracja**.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
   1. Wybierz typy obsługiwanych kont dla aplikacji. (Zobacz [Obsługiwane typy kont.)](./v2-supported-account-types.md)
   1. W sekcji **Nazwa** wprowadź znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji. Na przykład wpisz **AspNetCore-WebApp**.
   1. W przypadku **funkcji Przekierowanie identyfikatora URI**dodaj typ aplikacji i miejsce docelowe identyfikatora URI, które zaakceptuje zwrócone odpowiedzi tokenu po pomyślnym uwierzytelnieniu. Na przykład **https://localhost:44321**wprowadź . Następnie wybierz pozycję **Zarejestruj**.
1. Wybierz menu **Uwierzytelnianie**, a następnie dodaj następujące informacje:
   1. W przypadku adresu **https://localhost:44321/signin-oidc** URL **odpowiedzi**dodaj typ Sieci **Web**.
   1. W sekcji **Ustawienia zaawansowane** ustaw adres **URL wylogowywać** na **https://localhost:44321/signout-oidc**.
   1. W obszarze **Niejawne przyznanie** wybierz pozycję **Tokeny identyfikatorów**.
   1. Wybierz **pozycję Zapisz**.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. Po **wyświetleniu strony Zarejestruj zgłoszenie** wprowadź informacje rejestracyjne aplikacji:
   1. Wybierz typy obsługiwanych kont dla aplikacji. (Zobacz [Obsługiwane typy kont.)](./v2-supported-account-types.md)
   1. W sekcji **Nazwa** wprowadź znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji. Na przykład wprowadź **mailApp-openidconnect-v2**.
   1. W sekcji **Przekierowanie identyfikatora URI (opcjonalnie)** wybierz pozycję **Web** w **https://localhost:44326/** polu kombi i wprowadź następujący identyfikator URI przekierowania: .
1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
1. Wybierz menu **Uwierzytelnianie.**
1. W sekcji | **Niejawne przyznanie** **ustawień zaawansowanych**wybierz pozycję **Tokeny identyfikatorów**. Ten przykład wymaga [niejawnego przepływu dotacji,](v2-oauth2-implicit-grant-flow.md) aby włączyć logowanie użytkownika.
1. Wybierz **pozycję Zapisz**.

# <a name="java"></a>[Java](#tab/java)

1. Po **wyświetleniu strony Zarejestruj aplikację** wprowadź nazwę wyświetlaną aplikacji. Na przykład wprowadź **java-webapp**.
1. Wybierz **pozycję Konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft (np Outlook.com.** **Web app / API** **Application Type**
1. Wybierz **zarejestruj,** aby zarejestrować aplikację.
1. W menu po lewej stronie wybierz pozycję **Uwierzytelnianie**. W obszarze **Przekierowanie identyfikatorów URI**wybierz pozycję **Web**.

1. Wprowadź dwa identyfikatory URI przekierowania: jeden dla strony logowania i jeden dla strony wykresu. W obu tych pobycie użyj tego samego hosta i numeru portu, a następnie strony z **msal4jsample/graph/me** informacjami o **użytkowniku /msal4jsample/secure/aad.**

   Domyślnie w próbce użyto:

   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

  Następnie wybierz pozycję **Zapisz**.

1. Z menu **wybierz polecenie Certyfikaty & wpisy tajne.**
1. W sekcji **Wpisy tajne klienta** wybierz pozycję **Nowy klucz tajny klienta,** a następnie:

   1. Wprowadź opis klucza.
   1. Wybierz czas trwania **klucza W ciągu 1 roku**.
   1. Wybierz pozycję **Dodaj**.
   1. Gdy pojawi się wartość klucza, skopiuj ją na później. Ta wartość nie będzie wyświetlana ponownie ani może być pobierana w jakikolwiek inny sposób.

# <a name="python"></a>[Python](#tab/python)

1. Po **wyświetleniu strony Zarejestruj zgłoszenie** wprowadź informacje rejestracyjne aplikacji:
   1. W sekcji **Nazwa** wprowadź znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji. Na przykład wprowadź **python-webapp**.
   1. Zmień **obsługiwane typy kont** na Konta w dowolnym katalogu **organizacyjnym i osobistych kontach Microsoft (np Outlook.com.**
   1. W sekcji **Przekierowanie identyfikatora URI (opcjonalnie)** wybierz pozycję **Web** w **http://localhost:5000/getAToken**polu kombi i wprowadź następujący identyfikator URI przekierowania: .
1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
1. Na stronie **Przegląd** aplikacji znajdź wartość **identyfikatora aplikacji (klienta)** i zarejestruj ją na później. Będzie potrzebny do skonfigurowania pliku konfiguracji programu Visual Studio dla tego projektu.
1. W menu po lewej stronie wybierz pozycję **Certyfikaty & wpisy tajne**.
1. W sekcji **Wpisy klientów** wybierz pozycję **Nowy klucz tajny klienta,** a następnie:

   1. Wprowadź opis klucza.
   1. Wybierz kluczowy czas trwania **w ciągu 1 roku**.
   1. Wybierz pozycję **Dodaj**.
   1. Gdy pojawi się wartość klucza, skopiuj ją. Będzie on potrzebny później.
---

## <a name="register-an-app-by-using-powershell"></a>Rejestrowanie aplikacji przy użyciu programu PowerShell

> [!NOTE]
> Obecnie program Azure AD PowerShell tworzy aplikacje z następującymi obsługiwanymi typami kont:
>
> - MyOrg (tylko konta w tym katalogu organizacyjnym)
> - AnyOrg (konta w dowolnym katalogu organizacyjnym)
>
> Można utworzyć aplikację, która loguje się do użytkowników za pomocą ich osobistych kont Microsoft (na przykład Skype, Xbox lub Outlook.com). Najpierw utwórz aplikację wielodostępną. Obsługiwane typy kont to konta w dowolnym katalogu organizacji. Następnie zmień `signInAudience` właściwość w manifeście aplikacji z witryny Azure portal. Aby uzyskać więcej informacji, zobacz [krok 1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) w samouczku ASP.NET Core. Ten krok można uogólnić w aplikacjach sieci Web w dowolnym języku.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfiguracja kodu aplikacji](scenario-web-app-sign-user-app-configuration.md)
