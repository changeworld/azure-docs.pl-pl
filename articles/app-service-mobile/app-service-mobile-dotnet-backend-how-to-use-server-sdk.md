---
title: Praca z sdk serwera wewnętrznej bazy danych .NET
description: Dowiedz się, jak pracować z zestawem SDK serwera wewnętrznej bazy danych .NET dla aplikacji mobilnych usługi Azure App Service.
keywords: usługa aplikacji, usługa aplikacji platformy Azure, aplikacja mobilna, usługa mobilna, skalowanie, skalowalne, wdrażanie aplikacji, wdrażanie aplikacji platformy Azure
author: conceptdev
ms.assetid: 0620554f-9590-40a8-9f47-61c48c21076b
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: cafb0a7e2bf0fbce82448236a2da98079144121e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250142"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Praca z zestawem SDK serwera wewnętrznej bazy danych .NET dla aplikacji mobilnych platformy Azure
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

W tym temacie pokazano, jak używać sdk serwera wewnętrznej bazy danych .NET w kluczowych scenariuszach aplikacji mobilnych usługi Azure App Service. Zestaw SDK aplikacji mobilnych platformy Azure ułatwia pracę z klientami mobilnymi z aplikacji ASP.NET.

> [!TIP]
> Zestaw [SDK serwera .NET dla aplikacji mobilnych platformy Azure][2] jest open source w usłudze GitHub. Repozytorium zawiera cały kod źródłowy, w tym cały zestaw testów jednostkowych zestawu SDK serwera i niektóre przykładowe projekty.
>
>

## <a name="reference-documentation"></a>Dokumentacja referencyjna
Dokumentacja referencyjna dla sdk serwera znajduje się tutaj: [Usługi Azure Mobile Apps .NET Reference][1].

## <a name="how-to-create-a-net-mobile-app-backend"></a><a name="create-app"></a>Jak: Tworzenie zaplecza aplikacji .NET Mobile
Jeśli rozpoczynasz nowy projekt, możesz utworzyć aplikację usługi App Service przy użyciu [portalu Azure] lub programu Visual Studio. Aplikację usługi App Service można uruchomić lokalnie lub opublikować projekt w aplikacji mobilnej usługi App Service w chmurze.

Jeśli dodajesz funkcje mobilne do istniejącego projektu, zobacz [sekcję Pobieranie i inicjowanie sdk.](#install-sdk)

### <a name="create-a-net-backend-using-the-azure-portal"></a>Tworzenie zaplecza platformy .NET przy użyciu portalu Azure
Aby utworzyć zaplecze sieciowej usługi App Service, wykonaj [samouczek szybki start][3] lub wykonaj następujące czynności:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

W obszarze *Tworzenie* interfejsu **API tabeli**wybierz pozycję **C#** jako **język wewnętrznej bazy danych**. Kliknij **przycisk Pobierz**, wyodrębnij skompresowane pliki projektu na komputer lokalny i otwórz rozwiązanie w programie Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2017"></a>Tworzenie zaplecza platformy .NET przy użyciu programu Visual Studio 2017

Zainstaluj obciążenie platformy Azure za pośrednictwem Instalatora programu Visual Studio, aby opublikować w projekcie usługi Azure Mobile Apps z programu Visual Studio. Po zainstalowaniu sdk należy utworzyć aplikację ASP.NET, wykonując następujące czynności:

1. Otwórz okno dialogowe **Nowy projekt** (z **pliku** > **nowego** > **projektu...**).
2. Rozwiń **pozycję Visual C#** i wybierz pozycję **Web**.
3. Wybierz **ASP.NET aplikacji sieci Web (.NET Framework)**.
4. Wpisz nazwę projektu. Następnie kliknij przycisk **OK**.
5. Wybierz **aplikację mobilną platformy Azure** z listy szablonów.
6. Kliknij **przycisk OK,** aby utworzyć rozwiązanie.
7. Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz pozycję **Publikuj...**, a następnie wybierz pozycję **App Service** jako miejsce docelowe publikowania.
8. Postępuj zgodnie z instrukcjami, aby uwierzytelnić i wybrać nową lub istniejącą usługę Azure App Service do opublikowania.

### <a name="create-a-net-backend-using-visual-studio-2015"></a>Tworzenie zaplecza platformy .NET przy użyciu programu Visual Studio 2015

Zainstaluj [zestaw Azure SDK dla platformy .NET][4] (wersja 2.9.0 lub nowsza), aby utworzyć projekt usługi Azure Mobile Apps w programie Visual Studio. Po zainstalowaniu sdk należy utworzyć aplikację ASP.NET, wykonując następujące czynności:

1. Otwórz okno dialogowe **Nowy projekt** (z **pliku** > **nowego** > **projektu...**).
2. Rozwiń **pozycję Szablony** > **Visual C#** i wybierz pozycję **Sieć Web**.
3. Wybierz pozycję **Aplikacja internetowa platformy ASP.NET**.
4. Wpisz nazwę projektu. Następnie kliknij przycisk **OK**.
5. W *obszarze ASP.NET szablonów 4.5.2*wybierz pozycję **Aplikacja mobilna Azure**. Sprawdź **hosta w chmurze,** aby utworzyć zaplecze mobilne w chmurze, do którego można opublikować ten projekt.
6. Kliknij przycisk **OK**.

## <a name="how-to-download-and-initialize-the-sdk"></a><a name="install-sdk"></a>Jak: Pobieranie i inicjowanie sdk
SDK jest dostępny w [NuGet.org]. Ten pakiet zawiera podstawowe funkcje wymagane do rozpoczęcia korzystania z zestawu SDK. Aby zainicjować SDK, należy wykonać akcje na **httpConfiguration** obiektu.

### <a name="install-the-sdk"></a>Instalacja zestawu SDK
Aby zainstalować zestaw SDK, kliknij prawym przyciskiem myszy projekt serwera w programie Visual Studio, wybierz pozycję **Zarządzaj pakietami NuGet**, wyszukaj pakiet [Microsoft.Azure.Mobile.Server,] a następnie kliknij polecenie **Zainstaluj**.

### <a name="initialize-the-server-project"></a><a name="server-project-setup"></a>Inicjowanie projektu serwera
Projekt serwera wewnętrznej bazy danych .NET jest inicjowany podobnie jak inne projekty ASP.NET, dołączając klasę uruchamiania OWIN. Upewnij się, że odwołujesz `Microsoft.Owin.Host.SystemWeb`się do pakietu NuGet . Aby dodać tę klasę w programie Visual Studio, kliknij prawym przyciskiem myszy projekt serwera i wybierz polecenie **Dodaj** >
**nowy element,** a następnie klasę**uruchamiania OWIN****w języku** >  **Web** > General .  Klasa jest generowana z następującym atrybutem:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

W `Configuration()` metodzie klasy uruchamiania OWIN użyj obiektu **HttpConfiguration,** aby skonfigurować środowisko usługi Azure Mobile Apps.
Poniższy przykład inicjuje projekt serwera bez dodatkowych funkcji:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Aby włączyć poszczególne funkcje, należy wywołać metody rozszerzenia na obiekcie **MobileAppConfiguration** przed wywołaniem **ApplyTo**. Na przykład poniższy kod dodaje trasy domyślne do wszystkich `[MobileAppController]` kontrolerów interfejsu API, które mają atrybut podczas inicjowania:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

Szybki start serwera z portalu Azure wywołuje **program UseDefaultConfiguration().** Odpowiada to następującej konfiguracji:

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

Stosowane metody rozszerzenia to:

* `AddMobileAppHomeController()`udostępnia domyślną stronę główną usługi Azure Mobile Apps.
* `MapApiControllers()`zapewnia niestandardowe możliwości interfejsu API dla kontrolerów `[MobileAppController]` WebAPI ozdobione atrybutem.
* `AddTables()`zapewnia mapowanie `/tables` punktów końcowych do kontrolerów tabel.
* `AddTablesWithEntityFramework()`jest skrótem do mapowania punktów `/tables` końcowych przy użyciu kontrolerów opartych na platformie Entity Framework.
* `AddPushNotifications()`zapewnia prostą metodę rejestrowania urządzeń dla centrów powiadomień.
* `MapLegacyCrossDomainController()`zapewnia standardowe nagłówki CORS dla rozwoju lokalnego.

### <a name="sdk-extensions"></a>Rozszerzenia SDK
Następujące pakiety rozszerzeń oparte na NuGet zapewniają różne funkcje mobilne, które mogą być używane przez aplikację. Rozszerzenia można włączyć podczas inicjowania przy użyciu **MobileAppConfiguration** obiektu.

* [Microsoft.Azure.Mobile.Server.Quickstart] Obsługuje podstawową konfigurację aplikacji mobilnych. Dodano do konfiguracji, wywołując **UseDefaultConfiguration** metody rozszerzenia podczas inicjowania. To rozszerzenie obejmuje następujące rozszerzenia: powiadomienia, uwierzytelnianie, jednostki, tabele, międzydomenowe i pakiety home. Ten pakiet jest używany przez szybki start aplikacji mobilnych dostępny w witrynie Azure portal.
* [Microsoft.Azure.Mobile.Server.Home](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) Implementuje domyślną *tę aplikację mobilną jest uruchomiona strona* dla katalogu głównego witryny sieci Web. Dodaj do konfiguracji, wywołując **AddMobileAppHomeController** metody rozszerzenia.
* [Microsoft.Azure.Mobile.Server.Tables](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) zawiera klasy do pracy z danymi i konfiguruje potok danych. Dodaj do konfiguracji, wywołując **AddTables** metody rozszerzenia.
* [Microsoft.Azure.Mobile.Server.Entity](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) Umożliwia entity Framework dostęp do danych w bazie danych SQL. Dodaj do konfiguracji, wywołując **AddTablesWithEntityFramework** metody rozszerzenia.
* [Microsoft.Azure.Mobile.Server.Authentication] Umożliwia uwierzytelnianie i konfiguruje oprogramowanie pośredniczące OWIN używane do sprawdzania poprawności tokenów. Dodaj do konfiguracji, wywołując **AddAppServiceAuthentication** i **IAppBuilder**. **UseAppServiceAuthentication** metody rozszerzenia.
* [Microsoft.Azure.Mobile.Server.Notifications] Umożliwia powiadomienia wypychane i definiuje punkt końcowy rejestracji wypychania. Dodaj do konfiguracji, wywołując **AddPushNotifications** rozszerzenie metody.
* [Microsoft.Azure.Mobile.Server.CrossDomain](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) Tworzy kontroler, który obsługuje dane do starszych przeglądarek internetowych z aplikacji mobilnej. Dodaj do konfiguracji, wywołując **MapLegacyCrossDomainController** metody rozszerzenia.
* [Microsoft.Azure.Mobile.Server.Login] udostępnia metodę AppServiceLoginHandler.CreateToken(), która jest metodą statyczną używaną podczas scenariuszy uwierzytelniania niestandardowego.

## <a name="how-to-publish-the-server-project"></a><a name="publish-server-project"></a>Jak: Publikowanie projektu serwera
W tej sekcji pokazano, jak opublikować projekt zaplecza platformy .NET z programu Visual Studio. Można również wdrożyć projekt wewnętrznej bazy danych przy użyciu [git](../app-service/deploy-local-git.md) lub inne metody dostępne tam.

1. W programie Visual Studio odbuduj projekt, aby przywrócić pakiety NuGet.
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt, kliknij polecenie **Publikuj**. Przy pierwszym publikowaniu należy zdefiniować profil publikowania. Jeśli masz już zdefiniowany profil, możesz go zaznaczyć i kliknąć przycisk **Publikuj**.
3. Jeśli zostaniesz poproszony o wybranie miejsca docelowego publikowania, kliknij przycisk **Microsoft Azure App Service** > **Next**, a następnie (w razie potrzeby) zaloguj się przy użyciu poświadczeń platformy Azure.
   Program Visual Studio pobiera i bezpiecznie przechowuje ustawienia publikowania bezpośrednio z platformy Azure.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Wybierz **subskrypcję**, wybierz **typ zasobu** z **widoku**, rozwiń pozycję **Aplikacja mobilna**i kliknij zaplecze aplikacji mobilnej, a następnie kliknij przycisk **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Sprawdź informacje o profilu publikowania i kliknij przycisk **Publikuj**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Po pomyślnym opublikowaniu zaplecza aplikacji mobilnej zostanie wyświetlona strona docelowa wskazująca sukces.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="how-to-define-a-table-controller"></a><a name="define-table-controller"></a>Jak: Definiowanie kontrolera tabel
Zdefiniuj kontroler tabel, aby udostępnić tabelę SQL klientom przenośnym.  Konfigurowanie kontrolera tabel wymaga trzech kroków:

1. Utwórz klasę obiektu transferu danych (DTO).
2. Skonfiguruj odwołanie do tabeli w klasie Mobile DbContext.
3. Utwórz kontroler tabeli.

Obiekt transferu danych (DTO) jest zwykłym obiektem `EntityData`języka C#, który dziedziczy po pliku .  Przykład:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

DTO służy do definiowania tabeli w bazie danych SQL.  Aby utworzyć wpis bazy `DbSet<>` danych, dodaj właściwość do używanego DbContext.  W domyślnym szablonie projektu dla aplikacji mobilnych `Models\MobileServiceContext.cs`platformy Azure DbContext jest wywoływana:

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

Jeśli masz zainstalowany zestaw Azure SDK, możesz teraz utworzyć kontroler tabeli szablonów w następujący sposób:

1. Kliknij prawym przyciskiem myszy folder Kontrolery i wybierz polecenie **Dodaj** > **kontroler...**.
2. Wybierz opcję **Kontroler tabel aplikacji mobilnych platformy Azure,** a następnie kliknij przycisk **Dodaj**.
3. W oknie dialogowym **Dodawanie kontrolera:**
   * Z **listy** rozwijanej Klasa Model wybierz nowy dto.
   * W **DbContext** rozwijanej wybierz Mobile Service DbContext klasy.
   * Nazwa kontrolera jest tworzona dla Ciebie.
4. Kliknij przycisk **Dodaj**.

Projekt serwera Szybki start zawiera przykład prostego **todoItemController**.

### <a name="how-to-adjust-the-table-paging-size"></a><a name="adjust-pagesize"></a>Jak: Dopasowywanie rozmiaru stronicowania tabeli
Domyślnie usługa Azure Mobile Apps zwraca 50 rekordów na żądanie.  Stronicowanie zapewnia, że klient nie wiąże ich wątku interfejsu użytkownika ani serwera zbyt długo, zapewniając dobre środowisko użytkownika. Aby zmienić rozmiar stronicowania tabeli, należy zwiększyć po stronie serwera "dozwolony rozmiar zapytania" i rozmiar `EnableQuery` strony po stronie klienta Po stronie serwera "dozwolony rozmiar zapytania" jest korygowany za pomocą atrybutu:

    [EnableQuery(PageSize = 500)]

Upewnij się, że Rozmiar strony jest taki sam lub większy niż rozmiar wymagany przez klienta.  Szczegółowe informacje na temat zmiany rozmiaru strony klienta można znaleźć w dokumentacji howto określonego klienta.

## <a name="how-to-define-a-custom-api-controller"></a>Jak: Definiowanie niestandardowego kontrolera interfejsu API
Niestandardowy kontroler interfejsu API zapewnia najbardziej podstawowe funkcje zaplecza aplikacji mobilnej, udostępniając punkt końcowy. Kontroler interfejsu API specyficzny dla urządzeń przenośnych można zarejestrować przy użyciu atrybutu [MobileAppController]. Atrybut `MobileAppController` rejestruje trasę, konfiguruje serializator aplikacji mobilnych JSON i włącza [sprawdzanie wersji klienta](app-service-mobile-client-and-server-versioning.md).

1. W programie Visual Studio kliknij prawym przyciskiem myszy folder Kontrolery, a następnie kliknij polecenie **Dodaj** > **kontroler**, wybierz pozycję Kontroler **&mdash;interfejsu API sieci Web 2 Pusty** i kliknij polecenie **Dodaj**.
2. Podaj **nazwę kontrolera**, na przykład `CustomController`, a następnie kliknij przycisk **Dodaj**.
3. W nowym pliku klasy kontrolera dodaj następującą instrukcję using:

        using Microsoft.Azure.Mobile.Server.Config;
4. Zastosuj atrybut **[MobileAppController]** do definicji klasy kontrolera interfejsu API, jak w poniższym przykładzie:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. W pliku App_Start/Startup.MobileApp.cs dodaj wywołanie metody rozszerzenia **MapApiControllers,** jak w poniższym przykładzie:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Można również użyć `UseDefaultConfiguration()` metody rozszerzenia `MapApiControllers()`zamiast . Każdy kontroler, który nie ma **MobileAppControllerAttribute** stosowane nadal mogą być dostępne dla klientów, ale nie może być poprawnie używane przez klientów przy użyciu dowolnego SDK klienta aplikacji mobilnej.

## <a name="how-to-work-with-authentication"></a>Jak: Praca z uwierzytelnianiem
Usługa Azure Mobile Apps używa uwierzytelniania / autoryzacji usługi app service do zabezpieczania zaplecza mobilnego.  W tej sekcji pokazano, jak wykonać następujące zadania związane z uwierzytelnianiem w projekcie serwera wewnętrznej bazy danych .NET:

* [Jak: Dodawanie uwierzytelniania do projektu serwera](#add-auth)
* [Jak: Używanie uwierzytelniania niestandardowego dla aplikacji](#custom-auth)
* [Jak: Pobieranie uwierzytelnionych informacji o użytkowniku](#user-info)
* [Jak: Ograniczanie dostępu do danych dla autoryzowanych użytkowników](#authorize)

### <a name="how-to-add-authentication-to-a-server-project"></a><a name="add-auth"></a>Jak: Dodawanie uwierzytelniania do projektu serwera
Uwierzytelnianie można dodać do projektu serwera, rozszerzając obiekt **MobileAppConfiguration** i konfigurując oprogramowanie pośredniczące OWIN. Po zainstalowaniu pakietu [Microsoft.Azure.Mobile.Server.Quickstart] i wywołaniu metody rozszerzenia **UseDefaultConfiguration** można przejść do kroku 3.

1. W programie Visual Studio zainstaluj pakiet [Microsoft.Azure.Mobile.Server.Authentication.]
2. W pliku projektu Startup.cs dodaj następujący wiersz kodu na początku **metody Konfiguracji:**

        app.UseAppServiceAuthentication(config);

    Ten składnik oprogramowania pośredniczącego OWIN sprawdza poprawność tokenów wystawionych przez skojarzoną bramę usługi App Service.
3. Dodaj `[Authorize]` atrybut do dowolnego kontrolera lub metody, która wymaga uwierzytelniania.

Aby dowiedzieć się, jak uwierzytelnić klientów w wewnętrznej cemie aplikacji mobilnych, zobacz [Dodawanie uwierzytelniania do aplikacji](app-service-mobile-ios-get-started-users.md).

### <a name="how-to-use-custom-authentication-for-your-application"></a><a name="custom-auth"></a>Jak: Używanie uwierzytelniania niestandardowego dla aplikacji
> [!IMPORTANT]
> Aby włączyć uwierzytelnianie niestandardowe, należy najpierw włączyć uwierzytelnianie usługi App Service bez wybierania dostawcy usługi App Service w witrynie Azure portal. Spowoduje to włączenie zmiennej środowiskowej WEBSITE_AUTH_SIGNING_KEY podczas hosta.
> 
> 
> Jeśli nie chcesz używać jednego z dostawców uwierzytelniania/autoryzacji usługi aplikacji, możesz zaimplementować własny system logowania. Zainstaluj pakiet [Microsoft.Azure.Mobile.Server.Login,] aby ułatwić generowanie tokenu uwierzytelniania.  Podaj własny kod do sprawdzania poprawności poświadczeń użytkownika. Na przykład można sprawdzić pod kątem solone i hashed haseł w bazie danych. W poniższym przykładzie `isValidAssertion()` metoda (zdefiniowana gdzie indziej) jest odpowiedzialna za te kontrole.

Uwierzytelnianie niestandardowe jest uwidaczniane przez `register` utworzenie `login` ApiController i uwidaczniania i akcje. Klient powinien używać niestandardowego interfejsu użytkownika do zbierania informacji od użytkownika.  Informacje są następnie przesyłane do interfejsu API za pomocą standardowego wywołania HTTP POST. Gdy serwer sprawdza poprawność potwierdzenia, token jest `AppServiceLoginHandler.CreateToken()` wystawiany przy użyciu metody.  ApiController **nie należy** `[MobileAppController]` używać atrybutu.

Przykładowa `login` akcja:

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

W poprzednim przykładzie LoginResult i LoginResultUser są serializable obiektów uwidaczniania wymaganych właściwości. Klient oczekuje, że odpowiedzi logowania mają być zwracane jako obiekty JSON formularza:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

Metoda `AppServiceLoginHandler.CreateToken()` obejmuje *odbiorcę* i parametr *wystawcy.* Oba te parametry są ustawione na adres URL katalogu głównego aplikacji przy użyciu schematu HTTPS. Podobnie należy ustawić *secretKey* być wartością klucza podpisywania aplikacji. Nie należy rozpowszechniać klucza podpisywania w kliencie, ponieważ może on służyć do kluczy miętowych i personifikacji użytkowników. Klucz podpisywania można uzyskać podczas hostowania w usłudze App Service, odwołując się do zmiennej środowiskowej *\_\_klucza podpisywania witryny\_sieci Web.* W razie potrzeby w kontekście debugowania lokalnego postępuj zgodnie z instrukcjami w [lokalnym debugowaniu z uwierzytelniania](#local-debug) sekcji, aby pobrać klucz i przechowywać go jako ustawienie aplikacji.

Wystawiony token może również zawierać inne roszczenia i datę ważności.  Minimalnie wystawiony token musi zawierać oświadczenie podmiotu (**sub).**

Można obsługiwać standardową metodę klienta `loginAsync()` przez przeciążenie trasy uwierzytelniania.  Jeśli klient `client.loginAsync('custom');` wywołuje logowanie, trasa `/.auth/login/custom`musi być .  Trasę dla niestandardowego kontrolera uwierzytelniania można ustawić za pomocą: `MapHttpRoute()`

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> Przy `loginAsync()` użyciu metody zapewnia, że token uwierzytelniania jest dołączony do każdego kolejnego wywołania usługi.
>
>

### <a name="how-to-retrieve-authenticated-user-information"></a><a name="user-info"></a>Jak: Pobieranie uwierzytelnionych informacji o użytkowniku
Gdy użytkownik jest uwierzytelniany przez usługę app service, można uzyskać dostęp do przypisanego identyfikatora użytkownika i innych informacji w kodzie wewnętrznej bazy danych .NET. Informacje o użytkowniku mogą służyć do podejmowania decyzji dotyczących autoryzacji w wewnętrznej bazy danych. Poniższy kod uzyskuje identyfikator użytkownika skojarzony z żądaniem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

Identyfikator SID pochodzi od identyfikatora użytkownika specyficznego dla dostawcy i jest statyczny dla danego użytkownika i dostawcy logowania.  Identyfikator SID ma wartość null dla nieprawidłowych tokenów uwierzytelniania.

Usługa App Service umożliwia również żądanie określonych oświadczeń od dostawcy logowania. Każdy dostawca tożsamości może dostarczyć więcej informacji przy użyciu sdk dostawcy tożsamości.  Możesz na przykład użyć interfejsu API wykresu Facebooka, aby uzyskać informacje o znajomych.  Można określić oświadczenia, które są wymagane w bloku dostawcy w witrynie Azure portal. Niektóre oświadczenia wymagają dodatkowej konfiguracji z dostawcą tożsamości.

Następujący kod wywołuje **getappserviceidentityAsync** metody rozszerzenia, aby uzyskać poświadczenia logowania, które obejmują token dostępu potrzebne do żądania względem interfejsu API wykresu Facebook:

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

Dodaj using `System.Security.Principal` instrukcji, aby zapewnić **GetAppServiceIdentityAsync** metody rozszerzenia.

### <a name="how-to-restrict-data-access-for-authorized-users"></a><a name="authorize"></a>Jak: Ograniczanie dostępu do danych dla autoryzowanych użytkowników
W poprzedniej sekcji pokazano, jak pobrać identyfikator użytkownika uwierzytelnionego użytkownika. Można ograniczyć dostęp do danych i innych zasobów na podstawie tej wartości. Na przykład dodanie kolumny userId do tabel i filtrowanie wyników kwerendy według identyfikatora użytkownika jest prostym sposobem ograniczenia zwracanych danych tylko do autoryzowanych użytkowników. Poniższy kod zwraca wiersze danych tylko wtedy, gdy identyfikator SID pasuje do wartości w kolumnie Identyfikator użytkownika w tabeli TodoItem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

Metoda `Query()` `IQueryable` zwraca, które mogą być manipulowane przez LINQ do obsługi filtrowania.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Jak: Dodawanie powiadomień wypychanych do projektu serwera
Dodaj powiadomienia wypychania do projektu serwera, rozszerzając obiekt **MobileAppConfiguration** i tworząc klienta centrum powiadomień.

1. W programie Visual Studio kliknij prawym przyciskiem myszy projekt `Microsoft.Azure.Mobile.Server.Notifications`serwera i kliknij polecenie **Zarządzaj pakietami NuGet**, wyszukaj , a następnie kliknij polecenie **Zainstaluj**.
2. Powtórz ten krok, `Microsoft.Azure.NotificationHubs` aby zainstalować pakiet, który zawiera bibliotekę klienta Centrów powiadomień.
3. W App_Start/Startup.MobileApp.cs i dodać wywołanie do **AddPushNotifications()** metody rozszerzenia podczas inicjowania:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Dodaj następujący kod, który tworzy klienta Centrum powiadomień:

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

Teraz można użyć klienta Centrum powiadomień do wysyłania powiadomień wypychanych do zarejestrowanych urządzeń. Aby uzyskać więcej informacji, zobacz [Dodawanie powiadomień wypychanych do aplikacji](app-service-mobile-ios-get-started-push.md). Aby dowiedzieć się więcej o Centrach powiadomień, zobacz [Centrum powiadomień — omówienie](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="how-to-enable-targeted-push-using-tags"></a><a name="tags"></a>Jak: Włączanie ukierunkowanego wypychania za pomocą tagów
Centra powiadomień umożliwiają wysyłanie ukierunkowanych powiadomień do określonych rejestracji przy użyciu tagów. Kilka tagów jest tworzonych automatycznie:

* Identyfikator instalacji identyfikuje określone urządzenie.
* Identyfikator użytkownika na podstawie uwierzytelnionego identyfikatora SID identyfikuje określonego użytkownika.

Identyfikator instalacji można uzyskać z **installationId** właściwości na **MobileServiceClient**.  W poniższym przykładzie pokazano, jak użyć identyfikatora instalacji, aby dodać znacznik do określonej instalacji w Centrach powiadomień:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Wszystkie tagi dostarczone przez klienta podczas rejestracji powiadomień wypychanych są ignorowane przez wewnętrznej bazy danych podczas tworzenia instalacji. Aby umożliwić klientowi dodawanie tagów do instalacji, należy utworzyć niestandardowy interfejs API, który dodaje znaczniki przy użyciu poprzedniego wzorca.

Zobacz [Tagi powiadomień wypychanych dodane][5] przez klienta w aplikacjach mobilnych usługi App Service ukończonych przykład szybki start na przykład.

## <a name="how-to-send-push-notifications-to-an-authenticated-user"></a><a name="push-user"></a>Jak: Wysyłanie powiadomień wypychanych do uwierzytelnionego użytkownika
Gdy uwierzytelniony użytkownik rejestruje powiadomienia wypychane, tag identyfikatora użytkownika jest automatycznie dodawany do rejestracji. Korzystając z tego tagu, możesz wysyłać powiadomienia wypychane do wszystkich urządzeń zarejestrowanych przez tę osobę. Poniższy kod pobiera identyfikator SID użytkownika składającego żądanie i wysyła powiadomienie wypychanie szablonu do każdej rejestracji urządzenia dla tej osoby:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Podczas rejestrowania powiadomień wypychanych od uwierzytelnionego klienta upewnij się, że uwierzytelnianie zostało zakończone przed podjęciem próby rejestracji. Aby uzyskać więcej informacji, zobacz [Wypychanie do użytkowników][6] w aplikacjach mobilnych usługi App Service ukończono próbkę szybkiego startu dla zaplecza platformy .NET.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Jak: Debugowanie i rozwiązywanie problemów z sdkem serwera .NET
Usługa Azure App Service udostępnia kilka technik debugowania i rozwiązywania problemów dla ASP.NET aplikacji:

* [Monitorowanie usługi aplikacji platformy Azure](../app-service/web-sites-monitor.md)
* [Włącz rejestrowanie diagnostyczne w usłudze Azure App Service](../app-service/troubleshoot-diagnostic-logs.md)
* [Rozwiązywanie problemów z usługą Azure App Service w programie Visual Studio](../app-service/troubleshoot-dotnet-visual-studio.md)

### <a name="logging"></a>Rejestrowanie
Można zapisywać w dziennikach diagnostycznych usługi App Service przy użyciu standardowego zapisu śledzenia ASP.NET. Przed zapisaniem w dziennikach należy włączyć diagnostykę w wewnętrznej cemie aplikacji mobilnej.

Aby włączyć diagnostykę i zapis w dziennikach:

1. Wykonaj kroki opisane w aplikacji [Włącz rejestrowanie aplikacji (Windows).](../app-service/troubleshoot-diagnostic-logs.md#enable-application-logging-windows)
2. Dodaj następującą instrukcję przy użyciu w pliku kodu:

        using System.Web.Http.Tracing;
3. Utwórz moduł zapisujący śledzenia do zapisu z zaplecza .NET do dzienników diagnostycznych, w następujący sposób:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Ponownie opublikuj projekt serwera i uzyskaj dostęp do zaplecza aplikacji mobilnej, aby wykonać ścieżkę kodu za pomocą rejestrowania.
5. Pobierz i oceń dzienniki, zgodnie z opisem w [plikach dziennika programu Access](../app-service/troubleshoot-diagnostic-logs.md#access-log-files).

### <a name="local-debugging-with-authentication"></a><a name="local-debug"></a>Debugowanie lokalne za pomocą uwierzytelniania
Aplikację można uruchomić lokalnie, aby przetestować zmiany przed opublikowaniem ich w chmurze. W przypadku większości zaplecza aplikacji mobilnych platformy Azure naciśnij klawisz *F5* w programie Visual Studio. Istnieją jednak pewne dodatkowe zagadnienia podczas korzystania z uwierzytelniania.

Musisz mieć chmurową aplikację mobilną z skonfigurowaną autoryzacją/autoryzacją usługi app service, a klient musi mieć punkt końcowy w chmurze określony jako alternatywny host logowania. Zobacz dokumentację platformy klienta, aby uzyskać określone kroki.

Upewnij się, że zaplecze mobilne ma zainstalowany [system Microsoft.Azure.Mobile.Server.Authentication.] Następnie w klasie uruchamiania OWIN aplikacji dodaj następujące, po `MobileAppConfiguration` zastosowaniu `HttpConfiguration`do:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

W poprzednim przykładzie należy skonfigurować ustawienia aplikacji *authAudience* i *authIssuer* w pliku Web.config tak, aby były adresem URL katalogu głównego aplikacji przy użyciu schematu HTTPS. Podobnie należy ustawić *authSigningKey* być wartością klucza podpisywania aplikacji.
Aby uzyskać klucz podpisywania:

1. Przechodzenie do aplikacji w [witrynie Azure portal]
2. Kliknij pozycję **Narzędzia**, **Kudu**, **Go**.
3. W witrynie Zarządzania Kudu kliknij pozycję **Środowisko**.
4. Znajdź wartość *klucza podpisywania\_\_\_strony internetowej AUTH*.

Użyj klucza podpisywania dla *parametru authSigningKey* w konfiguracji aplikacji lokalnej.  Zaplecze mobilne jest teraz wyposażone do sprawdzania poprawności tokenów podczas uruchamiania lokalnie, co klient uzyskuje token z punktu końcowego opartego na chmurze.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Portal Azure]: https://portal.azure.com
[NuGet.org]: https://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Szybki start]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
