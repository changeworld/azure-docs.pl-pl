---
title: Wywoływanie ASP.NET internetowego interfejsu API chronionego przez usługę Azure AD — platforma tożsamości firmy Microsoft
description: W tym przewodniku szybki start dowiesz się, jak wywołać interfejs API sieci Web ASP.NET chroniony przez Azure Active Directory z aplikacji klasycznej systemu Windows (WPF). Klient WPF uwierzytelnia użytkownika, żąda tokenu dostępu i wywołuje internetowy interfejs API.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: c558d45702498e6c1164d7ee1731e80ff195349e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328416"
---
# <a name="build-a-multi-tenant-daemon-with-the-microsoft-identity-platform-endpoint"></a>Tworzenie demona z wieloma dzierżawcami przy użyciu punktu końcowego platformy tożsamości firmy Microsoft

W ramach tego samouczka nauczysz się używać platformy tożsamości firmy Microsoft do uzyskiwania dostępu do danych klientów firmy Microsoft w długotrwałym, nieinteraktywnym procesie. Demona Przykładowa używa [przydzielenia poświadczeń klienta OAuth2](v2-oauth2-client-creds-grant-flow.md) w celu uzyskania tokenu dostępu, który następnie używa do wywołania [Microsoft Graph](https://graph.microsoft.io) i dostępu do danych organizacji.

Aplikacja została skompilowana jako aplikacja ASP.NET MVC i używa programu OWIN OpenID Connect Connecter do logowania użytkowników.  Jego składnik "Demon" w tym przykładzie jest kontrolerem interfejsu API, który po wywołaniu wywołuje listę użytkowników w dzierżawie usługi Azure AD klienta z Microsoft Graph.  Ta `SyncController.cs` jest wyzwalana przez wywołanie AJAX w aplikacji sieci Web i używa [biblioteki Microsoft Authentication Library (MSAL) dla platformy .NET](msal-overview.md) do uzyskiwania tokenu dostępu dla Microsoft Graph.

W przypadku prostszej aplikacji demona konsoli zapoznaj się z [przewodnikiem Szybki Start dla programu .NET Core](quickstart-v2-netcore-daemon.md).

## <a name="scenario"></a>Scenariusz

Ponieważ aplikacja jest aplikacją z wieloma dzierżawcami, która jest przeznaczona do użytku przez dowolnego klienta firmy Microsoft, musi zapewnić klientom możliwość "rejestracji" lub "łączenia" aplikacji z danymi firmowymi.  Podczas przepływu połączenia administrator firmy najpierw przyznaje **uprawnienia aplikacji** bezpośrednio do aplikacji, dzięki czemu może uzyskiwać dostęp do danych firmowych w sposób nieinteraktywny, bez obecności zalogowanego użytkownika.  Większość logiki w tym przykładzie przedstawia sposób osiągnięcia tego przepływu połączenia przy użyciu punktu końcowego [zgody administratora](v2-permissions-and-consent.md#using-the-admin-consent-endpoint) platformy tożsamości.

![Topologia](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Aby uzyskać więcej informacji na temat pojęć użytych w tym przykładzie, należy zapoznać się z [dokumentacją protokołu poświadczeń klienta usługi Identity platform](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić przykład w tym przewodniku Szybki Start, potrzebne są:

- [Visual Studio 2017 lub 2019](https://visualstudio.microsoft.com/downloads/)
- Dzierżawa usługi Azure Active Directory (Azure AD). Aby uzyskać więcej informacji na temat uzyskiwania dzierżawy usługi Azure AD, zobacz [jak uzyskać dzierżawę usługi Azure AD](quickstart-create-new-tenant.md) .
- Co najmniej jedno konto użytkownika w dzierżawie usługi Azure AD. Ten przykład nie będzie działał z konto Microsoft (dawniej konto usługi Windows Live). W związku z tym, jeśli zalogowano się do [Azure Portal](https://portal.azure.com) za pomocą konto Microsoft i nigdy wcześniej nie utworzono konta użytkownika w katalogu, należy to zrobić teraz.

## <a name="clone-or-download-this-repository"></a>Klonuj lub Pobierz to repozytorium

Z poziomu powłoki lub wiersza polecenia:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Lub [Pobierz przykład w pliku zip](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-the-sample-application-with-your-azure-ad-tenant"></a>Rejestrowanie przykładowej aplikacji przy użyciu dzierżawy usługi Azure AD

W tym przykładzie istnieje jeden projekt. Aby go zarejestrować, możesz:

- Wykonaj kroki, aby [zarejestrować przykład w dzierżawie Azure Active Directory](#register-the-sample-application-with-your-azure-ad-tenant) i [skonfigurować przykład do korzystania z dzierżawy usługi Azure AD](#choose-the-azure-ad-tenant-for-the-applications)
- Lub użyj skryptów programu PowerShell, które:
  - **Automatycznie** tworzy aplikacje usługi Azure AD i powiązane obiekty (hasła, uprawnienia, zależności)
  - Zmodyfikuj pliki konfiguracyjne projektów programu Visual Studio.

Jeśli chcesz użyć tej automatyzacji:

1. W systemie Windows uruchom program PowerShell i przejdź do katalogu głównego sklonowanego katalogu
1. W programie PowerShell uruchom:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Uruchom skrypt, aby utworzyć aplikację usługi Azure AD i odpowiednio skonfigurować kod przykładowej aplikacji.
1. W programie PowerShell uruchom:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   > Inne sposoby uruchamiania skryptów są opisane w [skryptach tworzenia aplikacji](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md)

1. Otwórz rozwiązanie Visual Studio i kliknij przycisk Uruchom, aby uruchomić kod.

Jeśli nie chcesz używać tej automatyzacji, postępuj zgodnie z poniższymi instrukcjami.

### <a name="choose-the-azure-ad-tenant-for-the-applications"></a>Wybierz dzierżawę usługi Azure AD dla aplikacji

Pierwszym krokiem jest:

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. Jeśli Twoje konto jest obecne w więcej niż jednej dzierżawie usługi Azure AD, wybierz swój profil w prawym górnym rogu menu w górnej części strony, a następnie **Przełącz katalog**.
   Zmień sesję portalu na żądaną dzierżawę usługi Azure AD.

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Rejestrowanie aplikacji klienckiej (dotnet-Web-DAEMON-v2)

1. Przejdź do strony Microsoft Identity Platform for Developers [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Wybierz pozycję **Nowa rejestracja**.
1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
   - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `dotnet-web-daemon-v2`.
   - W sekcji **obsługiwane typy kont** wybierz pozycję **konta w dowolnym katalogu organizacyjnym**.
   - W sekcji identyfikator URI przekierowania (opcjonalnie) wybierz pozycję **Sieć Web** w polu kombi i wprowadź następujące identyfikatory URI przekierowania:
       - `https://localhost:44316/`
       - `https://localhost:44316/Account/GrantPermissions` Jeśli istnieje więcej niż jeden identyfikator URI przekierowania, należy dodać je z karty **uwierzytelnianie** później po pomyślnym utworzeniu aplikacji.
1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
1. Na stronie **Przegląd** aplikacji Znajdź wartość **Identyfikator aplikacji (klienta)** i Zapisz ją jako nowszą. Będzie ona potrzebna do skonfigurowania pliku konfiguracji programu Visual Studio dla tego projektu.
1. Na liście stron dla aplikacji wybierz pozycję **Uwierzytelnianie**.
   - W sekcji **Ustawienia zaawansowane** Ustaw **adres URL wylogowywania** na `https://localhost:44316/Account/EndSession`
   - W sekcji **Ustawienia zaawansowane** | **niejawne przyznanie** Sprawdź **tokeny dostępu** i **tokeny identyfikatorów** , ponieważ ten przykład wymaga włączenia [przepływu niejawnego](v2-oauth2-implicit-grant-flow.md) , aby zalogować użytkownika i wywołać interfejs API.
1. Wybierz pozycję **Zapisz**.
1. Na stronie **certyfikaty & wpisy tajne** w sekcji **klucze tajne klienta** wybierz pozycję **Nowy wpis tajny klienta**:

   - Wpisz opis klucza (`app secret`wystąpienia),
   - Wybierz kluczowy okres trwania z przedziału **1 roku**, **w ciągu 2 lat**lub **nigdy nie wygasa**.
   - Po naciśnięciu przycisku **Dodaj** zostanie wyświetlona wartość klucza, skopiuj ją i Zapisz wartość w bezpiecznej lokalizacji.
   - Ten klucz będzie potrzebny później, aby skonfigurować projekt w programie Visual Studio. Ta wartość klucza nie będzie ponownie wyświetlana ani nie można jej pobrać z innych metod, dlatego Zapisz ją tak szybko, jak to będzie widoczne w Azure Portal.
1. Na liście stron dla aplikacji wybierz pozycję **uprawnienia interfejsu API**
   - Kliknij przycisk **Dodaj uprawnienia** , a następnie
   - Upewnij się, że karta **Microsoft API** została wybrana
   - W sekcji *najczęściej używane interfejsy API firmy Microsoft* kliknij pozycję **Microsoft Graph**
   - W sekcji **uprawnienia aplikacji** upewnij się, że są zaznaczone odpowiednie uprawnienia: **User. Read. All**
   - Wybierz przycisk **Dodaj uprawnienia**

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Konfigurowanie przykładu do korzystania z dzierżawy usługi Azure AD

W poniższych krokach "ClientID" jest taki sam jak "Identyfikator aplikacji" lub "AppId".

Otwórz rozwiązanie w programie Visual Studio, aby skonfigurować projekty

### <a name="configure-the-client-project"></a>Konfigurowanie projektu klienta

W przypadku użycia skryptów Instalatora zostaną zastosowane następujące zmiany.

1. Otwórz plik `UserSync\Web.Config`
1. Znajdź klucz aplikacji `ida:ClientId` i Zastąp istniejącą wartość IDENTYFIKATORem aplikacji (clientId) `dotnet-web-daemon-v2` aplikacji skopiowanej z Azure Portal.
1. Znajdź klucz aplikacji `ida:ClientSecret` i Zastąp istniejącą wartość kluczem zapisanym podczas tworzenia aplikacji `dotnet-web-daemon-v2` w Azure Portal.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Oczyść rozwiązanie, Skompiluj ponownie rozwiązanie i uruchom aplikację UserSync i zacznij od zalogowania się jako administrator w dzierżawie usługi Azure AD.  Jeśli nie masz dzierżawy usługi Azure AD do testowania, możesz [wykonać te instrukcje](quickstart-create-new-tenant.md) , aby je uzyskać.

Po zalogowaniu aplikacja najpierw poprosił Cię o zgodę na zalogowanie się w & odczytania profilu użytkownika.  Dzięki temu aplikacja może mieć pewność, że jesteś użytkownikiem biznesowym.

![Wyrażanie zgody użytkownika](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

Następnie aplikacja spróbuje zsynchronizować listę użytkowników z dzierżawy usługi Azure AD za pośrednictwem Microsoft Graph.  Jeśli nie można tego zrobić, zostanie wyświetlony monit (Administrator dzierżawy) o połączenie dzierżawy z aplikacją.

Następnie aplikacja wyświetli żądanie uprawnienia do odczytu listy użytkowników w Twojej dzierżawie.

![Zgoda administratora](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

**Po udzieleniu uprawnienia użytkownik zostanie wylogowany z aplikacji**. W tym celu upewnij się, że wszystkie istniejące tokeny dostępu dla programu Graph zostaną usunięte z pamięci podręcznej tokenu. Po ponownym zalogowaniu uzyskany świeży token będzie miał odpowiednie uprawnienia do wykonywania wywołań do programu MS Graph.
Po udzieleniu uprawnienia aplikacja będzie mogła wysyłać zapytania do użytkowników w dowolnym momencie.  Można to sprawdzić, klikając przycisk **Synchronizuj użytkowników** na stronie użytkownicy, odświeżając listę użytkowników.  Spróbuj dodać lub usunąć użytkownika i ponownie zsynchronizować listę (ale należy pamiętać, że synchronizuje tylko pierwszą stronę użytkowników).

## <a name="about-the-code"></a>Informacje o kodzie

Odpowiedni kod dla tego przykładu znajduje się w następujących plikach:

- Początkowe Logowanie: `App_Start\Startup.Auth.cs`, `Controllers\AccountController.cs`.  W szczególności akcje na kontrolerze mają atrybut Autoryzuj, który wymusza Logowanie użytkownika. Aplikacja używa [przepływu kodu autoryzacji](v2-oauth2-auth-code-flow.md) do logowania użytkownika.
- Synchronizowanie listy użytkowników z lokalnym magazynem w pamięci: `Controllers\SyncController.cs`
- Wyświetlanie listy użytkowników z lokalnego magazynu w pamięci: `Controllers\UserController.cs`
- Uzyskiwanie uprawnień od administratora dzierżawy przy użyciu punktu końcowego zgody administratora: `Controllers\AccountController.cs`

## <a name="recreate-this-sample-app"></a>Utwórz ponownie tę przykładową aplikację

1. W programie Visual Studio Utwórz nowy projekt `Visual C#` `ASP.NET Web Application (.NET Framework)`. Na następnym ekranie wybierz szablon projektu `MVC`. Dodaj również foldery i podstawowe odwołania dla `Web API` jak później dodać kontroler interfejsu API sieci Web.  Pozostaw domyślny tryb uwierzytelniania wybranego projektu, czyli `No Authentication`".
2. Wybierz projekt w oknie **Eksplorator rozwiązań** i naciśnij klawisz **F4** , aby przełączyć właściwości projektu. We właściwościach projektu ustaw opcję **SSL z włączonym** `True`. Zanotuj **adres URL protokołu SSL**. Będzie on potrzebny podczas konfigurowania rejestracji tej aplikacji w Azure Portal.
3. Dodaj następujące NuGet ASP.Net OWIN oprogramowania pośredniczącego: `Microsoft.Owin.Security.ActiveDirectory`, `Microsoft.Owin.Security.Cookies` i `Microsoft.Owin.Host.SystemWeb`, `Microsoft.IdentityModel.Protocol.Extensions`, `Microsoft.Owin.Security.OpenIdConnect` i `Microsoft.Identity.Client`. 
4. W folderze `App_Start` Utwórz `Startup.Auth.cs`klasy. Usuń `.App_Start` z nazwy przestrzeni nazw.  Zastąp kod klasy `Startup` kodem z tego samego pliku przykładowej aplikacji.  Upewnij się, że cała definicja klasy została zadana!  Definicja zmieni się z `public class Startup` na `public partial class Startup`
5. W `Startup.Auth.cs` rozwiązać brakujące odwołania przez dodanie instrukcji `using` zgodnie z sugestią w programie Visual Studio IntelliSense.
6. Kliknij prawym przyciskiem myszy projekt, wybierz pozycję Dodaj, wybierz pozycję "Klasa" i w polu wyszukiwania wprowadź ciąg "OWIN".  "Klasa startowa OWIN" zostanie wyświetlona jako zaznaczenie; zaznacz ją i nadaj jej nazwę `Startup.cs`.
7. W `Startup.cs`Zastąp kod klasy `Startup` kodem z tego samego pliku przykładowej aplikacji.  Zanotuj zmiany definicji z `public class Startup`, aby `public partial class Startup`.
8. W folderze Dodaj nową klasę o nazwie `MsGraphUser.cs`.  Zastąp implementację zawartością pliku o tej samej nazwie z przykładu.
9. Dodaj nowy **kontroler MVC 5 — pusty** nazwa `AccountController`. Zastąp implementację zawartością pliku o tej samej nazwie z przykładu.
10. Dodaj nowy **kontroler MVC 5 — pusty** nazwa `UserController`. Zastąp implementację zawartością pliku o tej samej nazwie z przykładu.
11. Dodaj nowy **kontroler Web API 2 — pusty** nazwa `SyncController`. Zastąp implementację zawartością pliku o tej samej nazwie z przykładu.
12. Dla interfejsu użytkownika w folderze `Views\Account` Dodaj trzy **puste (bez modelu) widoki** o nazwach `GrantPermissions`, `Index` i `UserMismatch` oraz jeden nazwany `Index` w folderze `Views\User`. Zastąp implementację zawartością pliku o tej samej nazwie z przykładu.
13. Zaktualizuj `Shared\_Layout.cshtml` i `Home\Index.cshtml` w celu poprawnego łączenia różnych widoków.

## <a name="deploy-this-sample-to-azure"></a>Wdróż ten przykład na platformie Azure

Ten projekt zawiera WebApp/projekty interfejsu API sieci Web. Aby wdrożyć je w witrynach sieci Web systemu Azure, należy dla każdej z nich potrzebować:

- Tworzenie witryny sieci Web systemu Azure
- Opublikuj aplikacje sieci Web/interfejsy API sieci Web w witrynie sieci Web i
- Zaktualizuj klientów, aby wywoływać witrynę sieci Web, a nie IIS Express.

### <a name="create-and-publish-the-dotnet-web-daemon-v2-to-an-azure-web-site"></a>Tworzenie i publikowanie `dotnet-web-daemon-v2` w witrynie sieci Web systemu Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Kliknij pozycję `Create a resource` w lewym górnym rogu, wybierz pozycję **aplikacja**sieci Web --> **sieci** Web i nadaj witrynie sieci Web nazwę, na przykład `dotnet-web-daemon-v2-contoso.azurewebsites.net`.
1. Następnie wybierz `Subscription`, `Resource Group`, `App service plan and Location`. `OS` będzie w **systemie Windows** , a `Publish` będzie **kodem**.
1. Kliknij przycisk `Create` i poczekaj na utworzenie App Service.
1. Po otrzymaniu powiadomienia `Deployment succeeded` kliknij pozycję `Go to resource`, aby przejść do nowo utworzonej usługi App Service.
1. Po utworzeniu witryny sieci Web Znajdź ją na **pulpicie nawigacyjnym** i kliknij ją, aby otworzyć ekran **Przegląd** **App Services** .
1. Na karcie **omówienie** App Service Pobierz profil publikowania, klikając link **Pobierz profil publikowania** i Zapisz go.  Można również użyć innych mechanizmów wdrażania, takich jak z kontroli źródła.
1. Przełącz się do programu Visual Studio i przejdź do projektu dotnet-Web-demo-v2.  Kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań i wybierz polecenie **Publikuj**.  Kliknij przycisk **Importuj profil** na dolnym pasku i zaimportuj wcześniej pobrany profil publikacji.
1. Kliknij pozycję **Konfiguruj** i w `Connection tab`zaktualizuj docelowy adres URL, tak aby był `https` w adresie URL strony głównej, na przykład [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net). Kliknij przycisk **Dalej**.
1. Na karcie Ustawienia upewnij się, że `Enable Organizational Authentication` nie jest zaznaczone.  Kliknij pozycję **Zapisz**. Kliknij pozycję **Publikuj** na ekranie głównym.
1. Program Visual Studio opublikuje projekt i automatycznie otworzy w przeglądarce adres URL projektu.  Jeśli zostanie wyświetlona domyślna strona sieci Web projektu, publikacja zakończyła się pomyślnie.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>Aktualizowanie rejestracji aplikacji dzierżawy usługi Azure AD dla `dotnet-web-daemon-v2`

1. Przejdź z powrotem do [Azure Portal](https://portal.azure.com).
W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** a następnie wybierz pozycję **rejestracje aplikacji**.
1. Na ekranie wynikowym wybierz aplikację `dotnet-web-daemon-v2`.
1. W obszarze **uwierzytelnianie** | na stronie aplikacji zaktualizuj pola adresu URL wylogowywania z adresem usługi, na przykład [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)
1. Z menu *znakowanie* , zaktualizuj **adres URL strony głównej**na adres usługi, na przykład [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net). Zapisz konfigurację.
1. Dodaj ten sam adres URL na liście wartości w menu *przekierowania identyfikatorów uri >* . Jeśli masz wiele adresów URL przekierowania, upewnij się, że nowy wpis używa identyfikatora URI usługi App Service dla każdego adresu URL przekierowania.

## <a name="community-help-and-support"></a>Pomoc i obsługa techniczna społeczności

Użyj [Stack Overflow](http://stackoverflow.com/questions/tagged/msal) , aby uzyskać pomoc techniczną dla społeczności.
Zadawaj pytania na Stack Overflow pierwsze i Przeglądaj istniejące problemy, aby zobaczyć, czy ktoś zażądał wcześniej pytania.
Upewnij się, że Twoje pytania lub komentarze są oznaczone za pomocą [`adal` `msal` `dotnet`].

Jeśli znajdziesz i usterkę w przykładzie, zgłoś problem w usłudze [GitHub](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Jeśli znajdziesz błąd w MSAL.NET, zgłoś problem w usłudze [MSAL.NET GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues).

Aby uzyskać zalecenie, odwiedź następującą [stronę głosu użytkownika](https://feedback.azure.com/forums/169401-azure-active-directory).

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat różnych [przepływów uwierzytelniania i scenariuszy aplikacji](authentication-flows-app-scenarios.md) obsługiwanych przez platformę tożsamości firmy Microsoft.

Aby uzyskać więcej informacji, zobacz następującą dokumentację koncepcyjną:

- [Dzierżawa w Azure Active Directory](single-and-multi-tenant-apps.md)
- [Opis środowisk zgody dla aplikacji usługi Azure AD](application-consent-experience.md)
- [Instrukcje: Logowanie dowolnego Azure Active Directory użytkownika przy użyciu wzorca aplikacji wielodostępnych](howto-convert-app-to-be-multi-tenant.md)
- [Zrozumienie zgody użytkownika i administratora](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Obiekty główne aplikacji i usług w Azure Active Directory](app-objects-and-service-principals.md)
- [Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](quickstart-register-app.md)
- [Szybki Start: Konfigurowanie aplikacji klienckiej w celu uzyskiwania dostępu do interfejsów API sieci Web](quickstart-configure-app-access-web-apis.md)
- [Uzyskiwanie tokenu dla aplikacji za pomocą przepływów poświadczeń klienta](msal-client-applications.md)

Aby uprościć wielodostępną aplikację demona konsoli, zapoznaj się z tematem [szybkiego startu demona platformy .NET Core](quickstart-v2-netcore-daemon.md).
