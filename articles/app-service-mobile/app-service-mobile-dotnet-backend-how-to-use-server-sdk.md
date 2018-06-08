---
title: Jak pracować z serwera wewnętrznej bazy danych .NET SDK dla aplikacji mobilnych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pracować z serwera wewnętrznej bazy danych .NET SDK usługi Azure App Service Mobile Apps.
keywords: usługi aplikacji, usługa aplikacji azure, aplikacji mobilnej, usługi mobilnej, wdrażanie aplikacji wdrożenia usługi azure app skali, skalowalna,
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 0620554f-9590-40a8-9f47-61c48c21076b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 8216dafdd846f10ca1c8fc33b710a093aca20c7b
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "31589632"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Praca z zestawem SDK serwera zaplecza platformy .NET na potrzeby usługi Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

W tym temacie przedstawiono sposób użycia serwera wewnętrznej bazy danych .NET SDK w różnych kluczowych scenariuszy Azure App Service Mobile Apps. Zestaw SDK usługi Azure Mobile Apps pomaga w pracy z klientów mobilnych, z poziomu aplikacji ASP.NET.

> [!TIP]
> [Serwer .NET SDK usługi Azure Mobile Apps] [ 2] jest typu open source w witrynie GitHub. Repozytorium zawiera wszystkie kodu źródłowego, w tym zestaw testów jednostkowych SDK całego serwera i kilka przykładowych projektach.
>
>

## <a name="reference-documentation"></a>Dokumentacja referencyjna
Dokumentacja referencyjna dla serwera zestawu SDK znajduje się tutaj: [Azure Mobile Apps .NET Reference][1].

## <a name="create-app"></a>Porady: tworzenie zaplecza aplikacji mobilnej platformy .NET
Jeśli uruchamiasz nowego projektu można utworzyć aplikację usługi aplikacji za pomocą [Azure Portal] lub Visual Studio. Można uruchomić aplikację usługi aplikacji — lokalnie lub publikowania projektu na chmurze aplikacji mobilnej usługi aplikacji.

Jeśli dodajesz funkcji mobilnych do istniejącego projektu, zobacz [pobrać i zainicjuj zestaw SDK](#install-sdk) sekcji.

### <a name="create-a-net-backend-using-the-azure-portal"></a>Tworzenie zaplecza .NET przy użyciu portalu Azure
Aby utworzyć zaplecze aplikacji mobilnej usługi aplikacji, albo wykonaj [samouczek Szybki Start] [ 3] lub wykonaj następujące kroki:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

W *wprowadzenie* bloku, w obszarze **Utwórz tabelę interfejsu API**, wybierz **C#** jako sieci **język wewnętrznej bazy danych**. Kliknij przycisk **Pobierz**, a następnie Wyodrębnij skompresowane pliki projektu na komputerze lokalnym i otwórz rozwiązanie w programie Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2017"></a>Tworzenie zaplecza .NET przy użyciu programu Visual Studio 2017 r.

Zainstaluj Azure obciążenia za pomocą Instalatora programu Visual Studio do publikowania do projektu usługi Azure Mobile Apps w programie Visual Studio. Po zainstalowaniu zestawu SDK, należy utworzyć aplikację ASP.NET, wykonując następujące czynności:

1. Otwórz **nowy projekt** okna dialogowego (z **pliku** > **nowy** > **projektu...** ).
2. Rozwiń węzeł **Visual C#** i wybierz **Web**.
3. Wybierz **aplikacji sieci Web platformy ASP.NET (.NET Framework)**.
4. Wprowadź nazwę projektu. Następnie kliknij przycisk **OK**.
5. Wybierz **aplikacji mobilnej Azure** z listy szablonów.
6. Kliknij przycisk **OK** do utworzenia rozwiązania.
7. Kliknij prawym przyciskiem myszy projekt w **Eksploratora rozwiązań** i wybierz polecenie **publikowania...** , a następnie wybierz **usługi aplikacji** jako miejsce docelowe publikowania.
8. Postępuj zgodnie z monitami, aby uwierzytelniać i wybierz usługę aplikacji Azure nowego lub istniejącego do publikowania.

### <a name="create-a-net-backend-using-visual-studio-2015"></a>Tworzenie zaplecza .NET przy użyciu programu Visual Studio 2015

Zainstaluj [zestawu Azure SDK dla platformy .NET] [ 4] (wersja 2.9.0 lub nowszym) do tworzenia projektu usługi Azure Mobile Apps w programie Visual Studio. Po zainstalowaniu zestawu SDK, należy utworzyć aplikację ASP.NET, wykonując następujące czynności:

1. Otwórz **nowy projekt** okna dialogowego (z **pliku** > **nowy** > **projektu...** ).
2. Rozwiń węzeł **szablony** > **Visual C#** i wybierz **Web**.
3. Wybierz **aplikacji sieci Web ASP.NET**.
4. Wprowadź nazwę projektu. Następnie kliknij przycisk **OK**.
5. W obszarze *ASP.NET 4.5.2 szablony*, wybierz pozycję **aplikacji mobilnej Azure**. Sprawdź **Hostuj w chmurze** utworzyć przenośnych wewnętrznej bazy danych w chmurze, do którego można opublikować tego projektu.
6. Kliknij przycisk **OK**.

## <a name="install-sdk"></a>Porady: pobieranie i zainicjuj zestaw SDK
Zestaw SDK jest dostępny na [NuGet.org]. Ten pakiet zawiera podstawowe funkcje wymagane, aby rozpocząć korzystanie z zestawu SDK. Aby zainicjować zestawu SDK, należy wykonywać działania na **HttpConfiguration** obiektu.

### <a name="install-the-sdk"></a>Instalacja zestawu SDK
Aby zainstalować zestaw SDK, kliknij prawym przyciskiem myszy projekt serwera w programie Visual Studio, wybierz **Zarządzaj pakietami NuGet**, wyszukaj [Microsoft.Azure.Mobile.Server] pakietu, a następnie kliknij przycisk **zainstalować** .

### <a name="server-project-setup"></a> Inicjowanie projektu serwera
Projekt serwera zaplecza .NET zainicjowano podobne do innych projektów programu ASP.NET, umieszczając klasę początkową OWIN. Upewnij się, że ma odwołanie do pakietu NuGet `Microsoft.Owin.Host.SystemWeb`. Aby dodać do tej klasy w Visual Studio, kliknij prawym przyciskiem myszy projekt serwera, a następnie wybierz **Dodaj** >
**nowy element**, następnie **Web**  >  ** Ogólne** > **klasy początkowej OWIN**.  Klasa jest generowana za pomocą następującego atrybutu:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

W `Configuration()` metoda z klasy początkowej OWIN, użyj **HttpConfiguration** obiekt, aby skonfigurować środowisko usługi Azure Mobile Apps.
W poniższym przykładzie inicjowane Projekt serwera z nie dodatkowe funkcje:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Aby włączyć poszczególne funkcje, należy wywołać metody rozszerzenia na **MobileAppConfiguration** obiekt przed wywołaniem **metody ApplyTo**. Na przykład następujący kod dodaje tras domyślnych do wszystkich kontrolerów interfejsu API, które mają atrybut `[MobileAppController]` podczas inicjowania:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

Szybki Start serwera z portalu Azure wywołań **UseDefaultConfiguration()**. To odpowiednik następujące ustawienia:

        new MobileAppConfiguration()
            .AddMobileAppHomeController()             // from the Home package
            .MapApiControllers()
            .AddTables(                               // from the Tables package
                new MobileAppTableConfiguration()
                    .MapTableControllers()
                    .AddEntityFramework()             // from the Entity package
                )
            .AddPushNotifications()                   // from the Notifications package
            .MapLegacyCrossDomainController()         // from the CrossDomain package
            .ApplyTo(config);

Metody rozszerzenia używane są:

* `AddMobileAppHomeController()` udostępnia domyślną stronę główną usługi Azure Mobile Apps.
* `MapApiControllers()` zapewnia niestandardowego interfejsu API funkcji WebAPI kontrolerów ozdobione `[MobileAppController]` atrybutu.
* `AddTables()` zapewnia mapowanie `/tables` punktów końcowych do tabeli kontrolerów.
* `AddTablesWithEntityFramework()` jest skrótowym mapowania `/tables` kontrolerów na podstawie punktów końcowych przy użyciu programu Entity Framework.
* `AddPushNotifications()` zapewnia prostą metodę rejestrowania urządzeń do usługi Notification Hubs.
* `MapLegacyCrossDomainController()` zawiera standardowe nagłówki CORS dla rozwoju lokalnych.

### <a name="sdk-extensions"></a>Rozszerzenia zestawu SDK
Następujące pakiety NuGet, na podstawie rozszerzenia zawierają różne funkcje mobilne, które mogą być używane przez aplikację. Włącz rozszerzenia podczas inicjowania przy użyciu **MobileAppConfiguration** obiektu.

* [Microsoft.Azure.Mobile.Server.Quickstart] obsługuje podstawowa konfiguracja aplikacji mobilnej. Dodany do konfiguracji przez wywołanie metody **UseDefaultConfiguration** — metoda rozszerzenia podczas inicjowania. To rozszerzenie zawiera następujące rozszerzenia: powiadomienia, uwierzytelnianie, jednostki, tabel, między domenami i pakiety głównej. Ten pakiet jest używany przez Szybki Start aplikacji mobilnej, które są dostępne w portalu Azure.
* [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) implementuje domyślny *tej aplikacji mobilnej jest uruchomiona strona* dla katalogu głównego witryny sieci web. Dodaj do konfiguracji przez wywołanie metody **AddMobileAppHomeController** — metoda rozszerzenia.
* [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) zawiera klasy do pracy z danymi i zestawy danych potoku w górę. Dodaj do konfiguracji przez wywołanie metody **AddTables** — metoda rozszerzenia.
* [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) umożliwia programu Entity Framework w celu dostępu do danych w bazie danych SQL. Dodaj do konfiguracji przez wywołanie metody **AddTablesWithEntityFramework** — metoda rozszerzenia.
* [Microsoft.Azure.Mobile.Server.Authentication] umożliwia uwierzytelnianie i zestawy oprogramowanie pośredniczące OWIN służący do weryfikowania tokenów w górę. Dodaj do konfiguracji przez wywołanie metody **AddAppServiceAuthentication** i **IAppBuilder**. **UseAppServiceAuthentication** metody rozszerzenia.
* [Microsoft.Azure.Mobile.Server.Notifications] umożliwia wypychanie powiadomień i definiuje punktu końcowego rejestracji wypychania. Dodaj do konfiguracji przez wywołanie metody **AddPushNotifications** — metoda rozszerzenia.
* [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) tworzy kontroler, który służy danych do starszej wersji przeglądarki z aplikacji mobilnej. Dodaj do konfiguracji przez wywołanie metody **MapLegacyCrossDomainController** — metoda rozszerzenia.
* [Microsoft.Azure.Mobile.Server.Login] udostępnia metody AppServiceLoginHandler.CreateToken(), która jest metodą statyczną, używane podczas uwierzytelniania niestandardowego scenariuszy.

## <a name="publish-server-project"></a>Porady: publikowanie projektu serwera
W tej sekcji przedstawiono sposób publikowania projektu zaplecza .NET z programu Visual Studio. Można także wdrożyć, używając projektu zaplecza [Git](../app-service/app-service-deploy-local-git.md) lub dowolny z dostępnych dostępne inne metody.

1. W programie Visual Studio Skompiluj ponownie projekt pod kątem przywracania pakietów NuGet.
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt, kliknij przycisk **publikowania**. Przy pierwszym uruchomieniu publikowania, musisz zdefiniować profil publikowania. Jeśli istnieje już profil zdefiniowane, możesz wybrać go i kliknij przycisk **publikowania**.
3. Jeśli prośba o wybierz cel, publikowania, kliknij przycisk **Microsoft Azure App Service** > **dalej**, a następnie (w razie potrzeby) logowania przy użyciu poświadczeń platformy Azure.
   Program Visual Studio pobierze i bezpiecznie przechowywane są Twoje ustawienia publikowania bezpośrednio z platformy Azure.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Wybierz użytkownika **subskrypcji**, wybierz pozycję **typu zasobu** z **widoku**, rozwiń węzeł **aplikacji mobilnej**i kliknij przycisk zaplecza aplikacji mobilnej, a następnie kliknij przycisk **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Sprawdź informacje o profilu publikowania i kliknij przycisk **publikowania**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Po zaplecza aplikacji mobilnej został pomyślnie opublikowany, pojawi się Strona początkowa, informujący o powodzeniu.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a> Porady: Definiowanie kontrolera tabeli
Zdefiniuj kontrolera tabeli do udostępnienia tabeli SQL do klientów mobilnych.  Konfigurowanie kontrolera tabeli wymaga trzy kroki:

1. Utwórz klasę obiektu transferu danych (DTO).
2. Skonfiguruj odwołania do tabeli w klasy Mobile DbContext.
3. Tworzenie kontrolera tabeli.

Skanowania danych Transfer obiektu (DTO) jest to zwykły C# obiekt dziedziczący z `EntityData`.  Na przykład:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

DTO służy do definiowania tabeli w bazie danych SQL.  Aby utworzyć wpis w bazie danych, należy dodać `DbSet<>` właściwości DbContext używasz.  W domyślnym szablonie projektu usługi Azure Mobile Apps, nosi nazwę kontekstu DbContext `Models\MobileServiceContext.cs`:

    public class MobileServiceContext : DbContext
    {
        private const string connectionStringName = "Name=MS_TableConnectionString";

        public MobileServiceContext() : base(connectionStringName)
        {

        }

        public DbSet<TodoItem> TodoItems { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceColumnTable", (property, attributes) => attributes.Single().ColumnType.ToString()));
        }
    }

Jeśli masz zainstalowany zestaw SDK platformy Azure, można teraz utworzyć kontroler tabeli szablonu w następujący sposób:

1. Kliknij prawym przyciskiem myszy folder kontrolery, a następnie wybierz **Dodaj** > **kontrolera...** .
2. Wybierz **Azure Mobile Apps tabeli kontrolera** opcji, a następnie kliknij przycisk **Dodaj**.
3. W **Dodaj kontroler** okna dialogowego:
   * W **klasa modelu** listy rozwijanej wybierz Twoje nowe DTO.
   * W **DbContext** listy rozwijanej wybierz klasę DbContext usługi mobilnej.
   * Nazwa kontrolera jest tworzona.
4. Kliknij pozycję **Add** (Dodaj).

Projekt serwera szybkiego startu zawiera przykład prostą **TodoItemController**.

### <a name="adjust-pagesize"></a>Porady: dopasowywanie rozmiaru stronicowania tabeli
Domyślnie program Azure Mobile Apps zwraca 50 rekordów na żądanie.  Stronicowanie gwarantuje, że klient nie blokuje ich wątku interfejsu użytkownika ani server zbyt długo, zapewniając uzyskać komfortowe środowisko użytkownika. Aby zmienić rozmiar tabeli stronicowania, Zwiększ "dozwolony rozmiar zapytania" po stronie serwera i po stronie serwera rozmiaru strony po stronie klienta "dozwolony rozmiar zapytania" jest dostosowywana przy użyciu `EnableQuery` atrybutu:

    [EnableQuery(PageSize = 500)]

Upewnij się, wartość właściwości PageSize jest taki sam lub większy niż rozmiar żądanej przez klienta.  Odnoszą się do określonego klienta Porada dokumentacji, aby uzyskać więcej informacji na temat zmieniania rozmiaru strony klienta.

## <a name="how-to-define-a-custom-api-controller"></a>Porady: Definiowanie niestandardowego kontrolera interfejsu API
Kontrolera niestandardowego interfejsu API zapewnia najbardziej podstawowe funkcje do zaplecza aplikacji mobilnej w przypadku wystawianego punktu końcowego. Można zarejestrować kontrolera interfejsu API specyficzne dla mobilnych za pomocą atrybutu [MobileAppController]. `MobileAppController` Atrybut rejestruje trasę, konfiguruje serializator JSON aplikacji mobilnych i włącza [Sprawdzanie wersji klienta](app-service-mobile-client-and-server-versioning.md).

1. W programie Visual Studio, kliknij prawym przyciskiem myszy folder kontrolery, a następnie kliknij przycisk **Dodaj** > **kontrolera**, wybierz pozycję **kontroler 2 interfejsu API sieci Web&mdash;pusty** i Kliknij przycisk **Dodaj**.
2. Podaj **nazwy kontrolera**, takich jak `CustomController`i kliknij przycisk **Dodaj**.
3. W nowym pliku klasy kontroler, dodaj następującą instrukcję using:

        using Microsoft.Azure.Mobile.Server.Config;
4. Zastosuj **[MobileAppController]** atrybutu w definicji klasy kontrolera interfejsu API, jak w poniższym przykładzie:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. W pliku App_Start/Startup.MobileApp.cs, dodaj wywołanie do **MapApiControllers** — metoda rozszerzenia, jak w poniższym przykładzie:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Można również użyć `UseDefaultConfiguration()` — metoda rozszerzenia zamiast `MapApiControllers()`. Każdego kontrolera, który nie ma **MobileAppControllerAttribute** stosowane nadal mogą być używane przez klientów, ale go może nie być poprawnie używane przez klientów przy użyciu dowolnego klienta aplikacji mobilnej zestawu SDK.

## <a name="how-to-work-with-authentication"></a>Porady: Praca z uwierzytelnianiem
Aplikacje mobilne platformy Azure korzysta z aplikacji usługi uwierzytelniania / autoryzacji do zabezpieczania sieci zaplecza aplikacji mobilnych.  W tej sekcji pokazano, jak wykonywać następujące zadania związane z uwierzytelnianiem w projekcie serwera zaplecza .NET:

* [Porady: Dodawanie uwierzytelniania do server project](#add-auth)
* [Porady: użycie uwierzytelniania niestandardowego dla aplikacji](#custom-auth)
* [Porady: pobieranie uwierzytelniony informacje o użytkowniku](#user-info)
* [Porady: ograniczanie autoryzowanym użytkownikom dostęp do danych](#authorize)

### <a name="add-auth"></a>Porady: Dodawanie uwierzytelniania do server project
Uwierzytelnianie można dodać do projektu serwera, rozszerzając **MobileAppConfiguration** obiektu i konfigurowanie oprogramowania pośredniczącego OWIN. Po zainstalowaniu [Microsoft.Azure.Mobile.Server.Quickstart] pakietu i wywołanie **UseDefaultConfiguration** — metoda rozszerzenia, możesz przejść do kroku 3.

1. W programie Visual Studio należy zainstalować [Microsoft.Azure.Mobile.Server.Authentication] pakietu.
2. W pliku Startup.cs projekt, Dodaj następujący wiersz kodu na początku **konfiguracji** metody:

        app.UseAppServiceAuthentication(config);

    Ten składnik oprogramowania pośredniczącego OWIN weryfikuje tokeny wystawione przez skojarzone bramy usługi aplikacji.
3. Dodaj `[Authorize]` atrybutu kontrolera lub metody, która wymaga uwierzytelnienia.

Aby uzyskać informacje dotyczące sposobu uwierzytelniania klientów z wewnętrzną bazą danych Mobile Apps, zobacz [Dodawanie uwierzytelniania do aplikacji](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Porady: użycie uwierzytelniania niestandardowego dla aplikacji
> [!IMPORTANT]
> Aby włączyć uwierzytelnianie niestandardowe, należy najpierw włączyć uwierzytelnianie usługi aplikacji bez zaznaczania dostawcy usługi aplikacji w portalu Azure. Spowoduje to włączenie zmiennej środowiskowej WEBSITE_AUTH_SIGNING_KEY podczas udostępniania.
> 
> 
Jeśli nie chcesz używać jednego z dostawców uwierzytelniania/autoryzacji dla aplikacji usługi, można zaimplementować systemu logowania. Zainstaluj [Microsoft.Azure.Mobile.Server.Login] pakietu z generowania tokenu uwierzytelniania.  Sprawdzanie poprawności poświadczeń użytkownika Podaj własny kod. Na przykład sprawdzić przed solone i skrótu hasła w bazie danych. W poniższym przykładzie `isValidAssertion()` — metoda (zdefiniowany w innym miejscu) jest odpowiedzialny za kontrole.

Niestandardowe uwierzytelnianie jest udostępniany przez tworzenie klasy ApiController i udostępnianie `register` i `login` akcje. Niestandardowy interfejs użytkownika powinien używać klient do zbiera informacje o użytkowniku.  Informacje jest następnie przesłane do interfejsu API przy użyciu standardowych wywołania metody POST protokołu HTTP. Gdy serwer sprawdza to potwierdzenie, token jest wystawiane przy użyciu `AppServiceLoginHandler.CreateToken()` metody.  Klasy ApiController **nie powinien** użyj `[MobileAppController]` atrybutu.

Przykład `login` akcji:

        public IHttpActionResult Post([FromBody] JObject assertion)
        {
            if (isValidAssertion(assertion)) // user-defined function, checks against a database
            {
                JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
                    mySigningKey,
                    myAppURL,
                    myAppURL,
                    TimeSpan.FromHours(24) );
                return Ok(new LoginResult()
                {
                    AuthenticationToken = token.RawData,
                    User = new LoginResultUser() { UserId = userName.ToString() }
                });
            }
            else // user assertion was not valid
            {
                return this.Request.CreateUnauthorizedResponse();
            }
        }

W powyższym przykładzie LoginResult i LoginResultUser są obiekty możliwe do serializacji udostępnianie wymagane właściwości. Klient oczekuje odpowiedzi logowania, które są zwracane w postaci obiektów JSON w postaci:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

`AppServiceLoginHandler.CreateToken()` Metoda zawiera *odbiorców* i *wystawcy* parametru. Oba te parametry są ustawione na adres URL katalogu aplikacji za pomocą schematu HTTPS. Podobnie należy ustawić *secretKey* jako klucza do podpisywania wartości aplikacji. Nie rozpowszechniaj klucza podpisywania w kliencie jako mogą być używane do trudniej zdobyć kluczy i personifikować użytkowników. Można uzyskać klucza podpisywania, podczas gdy hostowanych w usłudze App Service za pomocą odwołań do *witryny sieci Web\_uwierzytelniania\_podpisywanie\_klucza* zmiennej środowiskowej. W razie potrzeby w kontekście lokalnego debugowania, postępuj zgodnie z instrukcjami [debugowania lokalnego z uwierzytelnianiem](#local-debug) sekcji, aby pobrać klucz i zapisz go jako ustawienie aplikacji.

Wystawiony token mogą również obejmować inne oświadczenia oraz datę wygaśnięcia.  Minimalny, wystawionego tokenu musi zawierać temat (**sub**) oświadczeń.

Standardowa klienta może obsługiwać `loginAsync()` metoda przeciążenia trasy uwierzytelniania.  Jeśli klient wywołuje `client.loginAsync('custom');` się zalogować, Twoje trasy nie może być `/.auth/login/custom`.  Można ustawić trasy dla kontrolera niestandardowego uwierzytelniania za pomocą `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> Przy użyciu `loginAsync()` podejście zapewnia, że token uwierzytelniania jest dołączona do każdego kolejne wywołania do usługi.
>
>

### <a name="user-info"></a>Porady: pobieranie uwierzytelniony informacje o użytkowniku
Gdy użytkownik jest uwierzytelniany przez usługę App Service, są dostępne identyfikator użytkownika przypisany i inne informacje w kodzie zaplecza .NET. Informacje o użytkowniku może służyć do podejmowania decyzji dotyczących autoryzacji w wewnętrznej bazie danych. Poniższy kod pobiera identyfikator użytkownika skojarzony z żądaniem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

Identyfikator SID jest określana na podstawie Identyfikatora użytkownika specyficznych dla dostawcy i jest statyczny dla danego użytkownika i dostawcy logowania.  Identyfikator SID ma wartość null dla nieprawidłowego uwierzytelniania tokenów.

Usługa App Service umożliwia również żądania określonych oświadczeń od dostawcy logowania. Każdy dostawca tożsamości Aby uzyskać więcej informacji, przy użyciu dostawcy tożsamości zestawu SDK.  Na przykład można użyć interfejsu API programu Graph usługi Facebook informacji znajomych.  W bloku dostawcy w portalu Azure, można określić oświadczenia, które są wymagane. Niektóre oświadczenia wymagają dodatkowej konfiguracji z dostawcy tożsamości.

Poniższy kod wywołania **GetAppServiceIdentityAsync** — metoda rozszerzenia można pobrać tokenu poświadczenia logowania, które obejmują dostęp potrzebne do podejmowania żądania kierowane do interfejsu Graph API usługi Facebook:

    // Get the credentials for the logged-in user.
    var credentials =
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token="
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

Dodaj using instrukcji dla `System.Security.Principal` zapewnienie **GetAppServiceIdentityAsync** — metoda rozszerzenia.

### <a name="authorize"></a>Porady: ograniczanie autoryzowanym użytkownikom dostęp do danych
W poprzedniej sekcji możemy pokazano, jak uzyskać identyfikator użytkownika uwierzytelnionego użytkownika. Można ograniczyć dostęp do danych i innych zasobów na podstawie tej wartości. Na przykład dodać kolumnę userId do tabel i filtrowanie wyników zapytania za pomocą Identyfikatora użytkownika jest prosty sposób, aby ograniczyć liczbę zwracanych danych tylko do autoryzowanych użytkowników. Poniższy kod zwraca wiersze danych tylko wtedy, gdy identyfikator SID jest zgodna z wartością w kolumnie identyfikator użytkownika w tabeli TodoItem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

`Query()` Metoda zwraca `IQueryable` który można manipulować LINQ do obsługi filtrowania.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Porady: Dodawanie wypychania powiadomień do projektu serwera
Dodawanie powiadomień wypychanych do projektu serwera przez rozszerzenie **MobileAppConfiguration** obiektu i Tworzenie klienta usługi Notification Hubs.

1. W programie Visual Studio, kliknij prawym przyciskiem myszy projekt serwera, a następnie kliknij przycisk **Zarządzaj pakietami NuGet**, wyszukaj `Microsoft.Azure.Mobile.Server.Notifications`, następnie kliknij przycisk **zainstalować**.
2. Powtórz ten krok, aby zainstalować `Microsoft.Azure.NotificationHubs` pakiet, który zawiera Biblioteka klienta usługi Notification Hubs.
3. W App_Start/Startup.MobileApp.cs i dodaj wywołanie do **AddPushNotifications()** — metoda rozszerzenia podczas inicjowania:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Dodaj następujący kod, który tworzy centra powiadomień klienta:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

Klienta usługi Notification Hubs można teraz używać do wysyłania powiadomień wypychanych do zarejestrowanych urządzeń. Aby uzyskać więcej informacji, zobacz [Dodawanie powiadomień wypychanych do aplikacji](app-service-mobile-ios-get-started-push.md). Aby dowiedzieć się więcej o usłudze Notification Hubs, zobacz [omówienie centra powiadomień](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="tags"></a>Porady: Włączanie docelowe wypychanych przy użyciu tagów
Usługi Notification Hubs umożliwia wysyłanie powiadomień docelowe do określonych rejestracji przy użyciu tagów. Kilka tagi są tworzone automatycznie:

* Identyfikator instalacji identyfikuje określonego urządzenia.
* Nazwa użytkownika, oparte na uwierzytelniony identyfikator SID identyfikuje określonego użytkownika.

Identyfikator jest możliwy z instalacji **installationId** właściwość **MobileServiceClient**.  Poniższy przykład pokazuje, jak dodać tag do instalacji określonych w usłudze Notification Hubs za pomocą Identyfikatora instalacji:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Wszystkie znaczniki dostarczany przez klienta podczas rejestracji powiadomień wypychanych są ignorowane przez zaplecze, podczas tworzenia instalacji. Aby włączyć klienta dodać tagi do instalacji, należy utworzyć niestandardowego interfejsu API, który dodaje znaczniki przy użyciu poprzedniego wzorca.

Zobacz [tagi powiadomień wypychanych dodać klienta] [ 5] w przykładowym ukończone szybkiego startu App Service Mobile Apps przykład.

## <a name="push-user"></a>Porady: wysyłanie powiadomień wypychanych do uwierzytelnionego użytkownika
Uwierzytelniony użytkownik rejestrujący dla powiadomień wypychanych, tag identyfikator użytkownika jest automatycznie dodawany do rejestracji. Za pomocą tego tagu, można wysyłać powiadomienia wypychane do wszystkich urządzeń zarejestrowanych przez tę osobę. Poniższy kod pobiera identyfikator SID użytkownika wysyłającego żądanie i wyśle powiadomienie wypychane szablonu do rejestracji urządzeń dla tej osoby:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Podczas rejestrowania dla powiadomień wypychanych z uwierzytelnionego klienta, upewnij się, że przed podjęciem próby wykonania rejestracji zakończeniem uwierzytelniania. Aby uzyskać więcej informacji, zobacz [Push użytkownikom] [ 6] w próbce App Service Mobile Apps ukończone szybkiego startu dla zaplecza .NET.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Porady: debugowanie i rozwiązywanie problemów z serwerem zestawu SDK .NET
Usługa aplikacji Azure zapewnia kilka debugowania i rozwiązywanie problemów z techniki dla aplikacji ASP.NET:

* [Monitorowanie usługi aplikacji Azure](../app-service/web-sites-monitor.md)
* [Włączanie rejestrowania diagnostycznego w usłudze aplikacji Azure](../app-service/web-sites-enable-diagnostic-log.md)
* [Rozwiązywanie problemów z usługi aplikacji Azure w programie Visual Studio](../app-service/web-sites-dotnet-troubleshoot-visual-studio.md)

### <a name="logging"></a>Rejestrowanie
Możesz zapisywać do dzienników diagnostycznych App Service przy użyciu standardowych zapisywanie śledzenia ASP.NET. Zanim można zapisywać w dziennikach, należy włączyć diagnostyki w zaplecza aplikacji mobilnej.

Aby włączyć diagnostykę i zapisywać w dziennikach:

1. Postępuj zgodnie z instrukcjami [włączania diagnostyki](../app-service/web-sites-enable-diagnostic-log.md#enablediag).
2. Dodaj następującą instrukcję using w pliku kodu:

        using System.Web.Http.Tracing;
3. Utwórz moduł zapisujący śledzenia do zapisania z zaplecza .NET do dzienników diagnostycznych w następujący sposób:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Ponownie opublikować projekt serwera, a dostęp do zaplecza aplikacji mobilnej na wykonanie ścieżki kodu z rejestrowaniem.
5. Pobierz i ocenić dzienniki, zgodnie z opisem w [porady: pobieranie dzienników](../app-service/web-sites-enable-diagnostic-log.md#download).

### <a name="local-debug"></a>Debugowanie za pomocą uwierzytelniania lokalnego
Możesz uruchomić aplikację lokalnie, aby przetestować zmiany przed ich opublikowaniem w chmurze. Dla większości zapleczy Azure Mobile Apps, naciśnij klawisz *F5* while w programie Visual Studio. Istnieją pewne dodatkowe kwestie przy użyciu uwierzytelniania.

Musi być oparta na chmurze aplikacji mobilnej z aplikacji usługi uwierzytelniania/autoryzacji skonfigurowany, a klient musi mieć określony jako host alternatywnego końcowego w chmurze. W dokumentacji platformy klienta, aby poznać konkretne kroki wymagane.

Upewnij się, że Twoje zaplecze aplikacji mobilnej ma [Microsoft.Azure.Mobile.Server.Authentication] zainstalowane. Następnie, w aplikacji klasy początkowej OWIN, Dodaj następujące polecenie, po `MobileAppConfiguration` zostały zastosowane do Twojej `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

W poprzednim przykładzie, należy skonfigurować *authAudience* i *authIssuer* ustawienia aplikacji w pliku Web.config plików do każdej być adresem URL katalogu aplikacji przy użyciu schematu HTTPS. Podobnie należy ustawić *authSigningKey* jako klucza do podpisywania wartości aplikacji.
Aby uzyskać klucza podpisywania:

1. Przejdź do aplikacji w ramach [Azure Portal]
2. Kliknij przycisk **narzędzia**, **Kudu**, **Przejdź**.
3. W witrynie zarządzania Kudu kliknij **środowiska**.
4. Znajdź wartość *witryny sieci Web\_uwierzytelniania\_podpisywanie\_klucza*.

Użyj klucza podpisywania dla *authSigningKey* parametru w pliku config aplikacji lokalnej.  Przenośne zaplecza teraz jest przystosowany do sprawdzania poprawności tokenów podczas uruchamiania lokalnego, które klient otrzymuje token z chmurowego punktu końcowego.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Azure Portal]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
