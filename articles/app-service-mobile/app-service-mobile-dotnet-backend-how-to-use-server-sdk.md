---
title: Jak pracować z zestawem SDK serwera zaplecza platformy .NET dla aplikacji mobilnych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pracować z zestawem SDK serwera zaplecza platformy .NET dla usługi Azure App Service Mobile Apps.
keywords: Usługa App service, usłudze azure app service, aplikacji mobilnej, usługę mobilną, skalowania, wdrażania aplikacji w skalowalne, wdrożenia usługi azure app
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
ms.openlocfilehash: 195a2dd88f443120f337ba441358389f0dc290f8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62119524"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Praca z zestawem SDK serwera zaplecza platformy .NET na potrzeby usługi Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

W tym temacie dowiesz się, jak używać serwera zaplecza platformy .NET zestaw SDK w najważniejszych scenariuszy dla usługi Azure App Service Mobile Apps. Zestaw SDK usług Azure Mobile Apps pomaga w pracy z klientów mobilnych z poziomu aplikacji ASP.NET.

> [!TIP]
> [Serwera .NET SDK usługi Azure Mobile Apps] [ 2] jest typu open source w serwisie GitHub. Repozytorium zawiera całego kodu źródłowego, w tym zestaw testów jednostkowych SDK całego serwera i kilka przykładowych projektów.
>
>

## <a name="reference-documentation"></a>Dokumentacja referencyjna
Dokumentacja referencyjna zestaw SDK serwera znajduje się tutaj: [Dokumentacja platformy .NET w usłudze Azure Mobile Apps][1].

## <a name="create-app"></a>Jak: Tworzenie zaplecza aplikacji mobilnej platformy .NET
Jeśli rozpoczynasz nowy projekt, możesz utworzyć aplikację usługi App Service przy użyciu [Azure Portal] lub Visual Studio. Można uruchamiaj lokalnie aplikację usługi App Service lub opublikować projekt w aplikacji mobilnej usługi App Service opartej na chmurze.

W przypadku dodawania funkcji mobilnych do istniejącego projektu, zobacz [pobrać i zainicjuj zestaw SDK](#install-sdk) sekcji.

### <a name="create-a-net-backend-using-the-azure-portal"></a>Tworzenie zaplecza platformy .NET przy użyciu witryny Azure portal
Aby utworzyć zaplecze dla aplikacji mobilnych usługi App Service, albo wykonaj [samouczek szybkiego startu] [ 3] lub wykonaj następujące kroki:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Wstecz w *wprowadzenie* bloku, w obszarze **Utwórz tabelę interfejsu API**, wybierz **C#** jako swojej **język zaplecza**. Kliknij przycisk **Pobierz**, a następnie Wyodrębnij skompresowane pliki projektu do komputera lokalnego i otwórz rozwiązanie w programie Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2017"></a>Tworzenie zaplecza platformy .NET przy użyciu programu Visual Studio 2017

Zainstaluj obciążenie platformy Azure za pomocą Instalatora programu Visual Studio, aby opublikować projekt usługi Azure Mobile Apps w programie Visual Studio. Po zainstalowaniu zestawu SDK, tworzenie aplikacji ASP.NET wykonując następujące czynności:

1. Otwórz **nowy projekt** okna dialogowego (z **pliku** > **New** > **projektu...** ).
2. Rozwiń **Visual C#** i wybierz **Web**.
3. Wybierz **aplikacji sieci Web ASP.NET (.NET Framework)** .
4. Wprowadź nazwę projektu. Następnie kliknij przycisk **OK**.
5. Wybierz **usługi Azure Mobile App** z listy szablonów.
6. Kliknij przycisk **OK** do utworzenia rozwiązania.
7. Kliknij prawym przyciskiem myszy nad projektem w **Eksploratora rozwiązań** i wybierz polecenie **publikowania...** , następnie wybierz **usługi App Service** jako miejscem docelowym publikowania.
8. Postępuj zgodnie z monitami, aby uwierzytelnić i wybierz nowy lub istniejący usługi Azure App Service do opublikowania.

### <a name="create-a-net-backend-using-visual-studio-2015"></a>Tworzenie zaplecza platformy .NET przy użyciu programu Visual Studio 2015

Zainstaluj [zestawu Azure SDK dla platformy .NET] [ 4] (w wersji 2.9.0 lub nowszej) do tworzenia projektu usługi Azure Mobile Apps w programie Visual Studio. Po zainstalowaniu zestawu SDK, tworzenie aplikacji ASP.NET wykonując następujące czynności:

1. Otwórz **nowy projekt** okna dialogowego (z **pliku** > **New** > **projektu...** ).
2. Rozwiń **szablony** > **Visual C#** i wybierz **Web**.
3. Wybierz pozycję **Aplikacja internetowa platformy ASP.NET**.
4. Wprowadź nazwę projektu. Następnie kliknij przycisk **OK**.
5. W obszarze *ASP.NET 4.5.2 szablony*, wybierz opcję **usługi Azure Mobile App**. Sprawdź **Hostuj w chmurze** można utworzyć zaplecze aplikacji mobilnych w chmurze, w którym można opublikować tego projektu.
6. Kliknij przycisk **OK**.

## <a name="install-sdk"></a>Jak: Pobierz i zainicjuj zestaw SDK
Zestaw SDK jest dostępny w [NuGet.org]. Ten pakiet zawiera podstawowe funkcje, które są wymagane, aby rozpocząć korzystanie z zestawu SDK. Aby zainicjować zestaw SDK, musisz wykonać akcje na **HttpConfiguration** obiektu.

### <a name="install-the-sdk"></a>Instalacja zestawu SDK
Aby zainstalować zestaw SDK, kliknij prawym przyciskiem myszy projekt serwera w programie Visual Studio, wybierz **Zarządzaj pakietami NuGet**, wyszukaj [Microsoft.Azure.Mobile.Server] pakietu, a następnie kliknij przycisk **zainstalować** .

### <a name="server-project-setup"></a> Inicjowanie projektu serwera
Projekt serwera zaplecza platformy .NET jest inicjowany podobne do innych projektów programu ASP.NET, umieszczając klasy początkowej OWIN. Upewnij się, że masz odwołanie do pakietu NuGet `Microsoft.Owin.Host.SystemWeb`. Aby dodać tę klasę w programie Visual Studio, kliknij prawym przyciskiem myszy na projekt serwera, a następnie wybierz **Dodaj** >
**nowy element**, następnie **Web** > **Ogólne** > **Klasa początkowa OWIN**.  Klasa jest generowany przy użyciu następującego atrybutu:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

W `Configuration()` metody z klasy początkowej OWIN, użyj **HttpConfiguration** obiektu, aby skonfigurować środowisko usługi Azure Mobile Apps.
Poniższy przykład inicjuje Projekt serwera, żadne dodatkowe funkcje:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Aby włączać poszczególne funkcje, należy wywołać metody rozszerzenia na **MobileAppConfiguration** obiekt przed wywołaniem **ApplyTo**. Na przykład, poniższy kod dodaje trasy domyślne do wszystkich kontrolerów interfejsu API, które mają atrybut `[MobileAppController]` podczas inicjowania:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

Szybki Start serwera z usługi Azure portal wywołań **UseDefaultConfiguration()** . To odpowiednik następujące ustawienia:

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
* `MapApiControllers()` zawiera niestandardowe funkcje interfejsu API dla kontrolerów WebAPI ozdobione `[MobileAppController]` atrybutu.
* `AddTables()` udostępnia mapowanie `/tables` punktów końcowych do tabeli kontrolerów.
* `AddTablesWithEntityFramework()` jest skrótowym mapowania `/tables` punktów końcowych przy użyciu platformy Entity Framework na podstawie kontrolerów.
* `AddPushNotifications()` zapewnia prostą metodę rejestrowania urządzeń do usługi Notification Hubs.
* `MapLegacyCrossDomainController()` zawiera standardowe nagłówki CORS dla wdrożenia lokalnego.

### <a name="sdk-extensions"></a>Zestaw SDK rozszerzeń
Następujące pakiety rozszerzeń oparte na pakietach NuGet zapewniają różne funkcje mobilne, które mogą być używane przez aplikację. Włącz rozszerzenia podczas inicjowania przy użyciu **MobileAppConfiguration** obiektu.

* [Microsoft.Azure.Mobile.Server.Quickstart] obsługuje podstawowe ustawienia funkcji Mobile Apps. Dodany do konfiguracji przez wywołanie metody **UseDefaultConfiguration** — metoda rozszerzenia podczas inicjowania. To rozszerzenie zawiera następujące rozszerzenia: Powiadomienia, uwierzytelnianie, jednostka, tabele, między domenami i pakietów głównej. Ten pakiet jest używany przez Mobile Apps Quickstart dostępne w witrynie Azure portal.
* [Microsoft.Azure.Mobile.Server.Home](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) implementuje domyślny *ta aplikacja mobilna została uruchomiona i działa strony* dla katalogu głównego witryny sieci web. Dodaj do konfiguracji przez wywołanie metody **AddMobileAppHomeController** — metoda rozszerzenia.
* [Microsoft.Azure.Mobile.Server.Tables](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) zawiera klasy do pracy z danymi i zestawy potok danych przedstawiony w górę. Dodaj do konfiguracji przez wywołanie metody **AddTables** — metoda rozszerzenia.
* [Microsoft.Azure.Mobile.Server.Entity](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) umożliwia Entity Framework w celu dostępu do danych w bazie danych SQL. Dodaj do konfiguracji przez wywołanie metody **AddTablesWithEntityFramework** — metoda rozszerzenia.
* [Microsoft.Azure.Mobile.Server.Authentication] umożliwia uwierzytelnianie i zestawy w górę oprogramowania pośredniczącego OWIN służący do weryfikowania tokenów. Dodaj do konfiguracji przez wywołanie metody **AddAppServiceAuthentication** i **IAppBuilder**. **UseAppServiceAuthentication** metody rozszerzenia.
* [Microsoft.Azure.Mobile.Server.Notifications] włącza powiadomienia wypychane i definiuje punkt końcowy rejestracji wypychania. Dodaj do konfiguracji przez wywołanie metody **AddPushNotifications** — metoda rozszerzenia.
* [Microsoft.Azure.Mobile.Server.CrossDomain](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) tworzy kontroler, która obsługuje dane do przeglądarek sieci web w starszej wersji z aplikacją mobilną. Dodaj do konfiguracji przez wywołanie metody **MapLegacyCrossDomainController** — metoda rozszerzenia.
* [Microsoft.Azure.Mobile.Server.Login] udostępnia metodę AppServiceLoginHandler.CreateToken(), czyli statycznej metody używane podczas uwierzytelniania niestandardowego scenariuszy.

## <a name="publish-server-project"></a>Jak: Publikowanie projektu serwera
W tej sekcji pokazano, jak opublikować projekt zaplecza platformy .NET w programie Visual Studio. Można także wdrożyć projekt zaplecza przy użyciu [Git](../app-service/deploy-local-git.md) lub dowolny z dostępnych tam innych metod.

1. W programie Visual Studio należy ponownie skompilować projekt do przywrócenia pakietów NuGet.
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt, kliknij przycisk **Publikuj**. Przy pierwszym uruchomieniu publikowania, musisz zdefiniować profil publikowania. Jeśli masz już profil zdefiniowane, możesz zaznacz go i kliknij **Publikuj**.
3. Pojawi się monit wybierz lokalizację docelową publikowania, kliknij przycisk **Microsoft Azure App Service** > **dalej**, a następnie (jeśli jest to konieczne) Zaloguj się przy użyciu swoich poświadczeń platformy Azure.
   Program Visual Studio pobierze i OS x bezpiecznie przechowa Twoje ustawienia publikowania bezpośrednio z platformy Azure.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Wybierz swoje **subskrypcji**, wybierz opcję **typ zasobu** z **widoku**, rozwiń węzeł **aplikacji mobilnej**, polecenie zaplecza aplikacji mobilnej, a następnie kliknij przycisk **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Sprawdź informacje o profilu publikowania, a następnie kliknij przycisk **Publikuj**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Zaplecza aplikacji mobilnej został opublikowany pomyślnie, zobaczysz jest stroną docelową o powodzeniu.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a> Jak: Zdefiniuj kontrolera tabeli
Zdefiniuj kontrolera tabeli, aby udostępnić tabelę SQL do klientów mobilnych.  Konfigurowanie kontrolera tabeli wymaga wykonania trzech kroków:

1. Utwórz klasę obiektu transferu danych (DTO).
2. Konfigurowanie odwołania do tabeli w klasy Mobile DbContext.
3. Tworzenie kontrolera tabeli.

Data Transfer Object (obiekt DTO) jest to zwykły C# obiekt dziedziczący z `EntityData`.  Na przykład:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

Obiekt DTO służy do definiowania tabeli w bazie danych SQL.  Aby utworzyć wpis w bazie danych, Dodaj `DbSet<>` właściwość typu DbContext, którego używasz.  W domyślnym szablonie projektu usługi Azure Mobile Apps, nosi nazwę kontekstu DbContext `Models\MobileServiceContext.cs`:

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

Jeśli masz zainstalowany zestaw SDK platformy Azure, można teraz tworzyć kontrolera tabeli szablonu w następujący sposób:

1. Kliknij prawym przyciskiem myszy w folderze kontrolerów, a następnie wybierz pozycję **Dodaj** > **kontrolera...** .
2. Wybierz **Azure Mobile Apps tabeli kontrolera** opcji, a następnie kliknij przycisk **Dodaj**.
3. W **Dodaj kontroler** okno dialogowe:
   * W **klasa modelu** listy rozwijanej wybierz swoje nowe DTO.
   * W **DbContext** listy rozwijanej wybierz klasy DbContext usługi mobilnej.
   * Nazwa kontrolera jest tworzony.
4. Kliknij pozycję **Add** (Dodaj).

Projekt serwera Przewodnik Szybki Start zawiera przykład prostej **TodoItemController**.

### <a name="adjust-pagesize"></a>Jak: Dopasuj rozmiar stronicowania tabeli
Domyślnie usługa Azure Mobile Apps zwraca 50 rekordów na żądanie.  Stronicowanie gwarantuje, że klient nie blokuje ich wątku interfejsu użytkownika ani serwera zbyt długo, zapewnienie zajmującymi. Aby zmienić rozmiar tabeli stronicowania, zwiększyć "dozwolony rozmiar zapytania" po stronie serwera strony klienta jej rozmiaru i po stronie serwera jest dostosowywany "dozwolony rozmiar zapytania", przy użyciu `EnableQuery` atrybutu:

    [EnableQuery(PageSize = 500)]

Upewnij się, wartość PageSize jest taki sam lub większy niż rozmiar zażądane przez klienta.  Można znaleźć określonego klienta Porada dokumentacji, aby uzyskać szczegółowe informacje na temat zmieniania rozmiaru strony klienta.

## <a name="how-to-define-a-custom-api-controller"></a>Instrukcje: Definiowanie niestandardowego kontrolera interfejsu API
Niestandardowe Kontroler interfejsu API zawiera większość podstawowych funkcji do zaplecza aplikacji mobilnej uwidaczniając punktu końcowego. Możesz zarejestrować Kontroler interfejsu API specyficzne dla mobile, przy użyciu atrybutu [MobileAppController]. `MobileAppController` Atrybut rejestruje trasę, ustawia serializator JSON aplikacji mobilnych i włącza funkcję [sprawdzania wersji klienta](app-service-mobile-client-and-server-versioning.md).

1. W programie Visual Studio, kliknij prawym przyciskiem myszy folder kontrolerów, a następnie kliknij przycisk **Dodaj** > **kontrolera**, wybierz opcję **kontroler 2 internetowego interfejsu API&mdash;pusty** i Kliknij przycisk **Dodaj**.
2. Podaj **nazwy kontrolera**, takich jak `CustomController`i kliknij przycisk **Dodaj**.
3. W nowym pliku klasy kontroler, dodaj następującą instrukcję using:

        using Microsoft.Azure.Mobile.Server.Config;
4. Zastosuj **[MobileAppController]** atrybutu do definicji klasy kontrolera interfejsu API, jak w poniższym przykładzie:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. W pliku App_Start/Startup.MobileApp.cs, dodaj wywołanie **MapApiControllers** metodę rozszerzenia, jak w poniższym przykładzie:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Można również użyć `UseDefaultConfiguration()` rozszerzenia zamiast metody `MapApiControllers()`. Dowolny kontroler, który nie ma **MobileAppControllerAttribute** stosowany jest nadal dostępny dla klientów, ale jego mogą nie być prawidłowo wykorzystywane przez klientów przy użyciu zestawu SDK klienta aplikacji mobilnej.

## <a name="how-to-work-with-authentication"></a>Instrukcje: Praca z uwierzytelnianiem
Usługa Azure Mobile Apps używa uwierzytelnianie usługi App Service / autoryzacji, aby zabezpieczyć zapleczu mobilnym.  W tej sekcji dowiesz się, jak wykonywać następujące zadania związane z uwierzytelnianiem w projekcie serwera zaplecza platformy .NET:

* [Instrukcje: Dodawanie uwierzytelniania do projektu serwera](#add-auth)
* [Instrukcje: Użyj uwierzytelniania niestandardowego dla aplikacji](#custom-auth)
* [Instrukcje: Pobieranie informacji uwierzytelnionego użytkownika](#user-info)
* [Instrukcje: Ograniczanie dostępu do danych dla autoryzowanych użytkowników](#authorize)

### <a name="add-auth"></a>Jak: Dodawanie uwierzytelniania do projektu serwera
Można dodać uwierzytelnianie do projektu serwera, rozszerzając **MobileAppConfiguration** obiektu i konfigurowanie oprogramowania pośredniczącego OWIN. Po zainstalowaniu [Microsoft.Azure.Mobile.Server.Quickstart] pakietu i wywołania **UseDefaultConfiguration** metodę rozszerzenia, możesz przejść do kroku 3.

1. W programie Visual Studio, zainstaluj [Microsoft.Azure.Mobile.Server.Authentication] pakietu.
2. W pliku Startup.cs pliku projektu, Dodaj następujący wiersz kodu na początku **konfiguracji** metody:

        app.UseAppServiceAuthentication(config);

    Ten składnik oprogramowania pośredniczącego OWIN weryfikuje tokeny wystawione przez skojarzonej bramy usługi App Service.
3. Dodaj `[Authorize]` atrybutu do dowolnego kontrolera lub metody, która wymaga uwierzytelnienia.

Aby dowiedzieć się więcej o tym, jak do uwierzytelniania klientów do zaplecza funkcji Mobile Apps, zobacz [Dodawanie uwierzytelniania do aplikacji](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Jak: Użyj uwierzytelniania niestandardowego dla aplikacji
> [!IMPORTANT]
> Aby włączyć uwierzytelnianie niestandardowe, należy najpierw włączyć uwierzytelnianie usługi App Service bez zaznaczania dostawcy usługi App Service w witrynie Azure portal. Spowoduje to włączenie WEBSITE_AUTH_SIGNING_KEY zmienną środowiskową, w przypadku hostowania.
> 
> 
> Jeśli nie chcesz używać jednego z dostawców uwierzytelniania/autoryzacji dla aplikacji usługi, można zaimplementować własny system logowania. Zainstaluj [Microsoft.Azure.Mobile.Server.Login] pakietu na potrzeby generowania tokenu uwierzytelniania.  Podaj własny kod do zweryfikowania poświadczeń użytkownika. Na przykład sprawdzania solone i skrótu hasła w bazie danych. W poniższym przykładzie `isValidAssertion()` — metoda (zdefiniowany w innym miejscu) jest odpowiedzialny za te testy.

Niestandardowe uwierzytelnianie jest uwidaczniany przez utworzenie klasy ApiController i udostępnianie `register` i `login` akcji. Klient powinien używać niestandardowego interfejsu użytkownika do zbierania informacji od użytkownika.  Informacje następnie jest przesyłane do interfejsu API przy użyciu standardowego wywołania HTTP POST. Gdy serwer sprawdza potwierdzenia, token wystawiony za pomocą `AppServiceLoginHandler.CreateToken()` metody.  Klasy ApiController **nie powinien** użyj `[MobileAppController]` atrybutu.

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

W powyższym przykładzie LoginResult i LoginResultUser są obiekty możliwe do serializacji, udostępnianie wymaganych właściwości. Klient oczekuje odpowiedzi logowania będą zwracane jako obiekty JSON w postaci:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

`AppServiceLoginHandler.CreateToken()` Metoda zawiera *odbiorców* i *wystawcy* parametru. Oba te parametry można ustawić adres URL katalogu aplikacji przy użyciu schematu HTTPS. Podobnie należy ustawić *secretKey* jako klucz podpisywania wartości aplikacji. Nie przeprowadzaj dystrybucji klucza podpisywania w kliencie, ponieważ może służyć do trudniej zdobyć kluczy i personifikować użytkowników. Możesz uzyskać klucza podpisywania, podczas gdy hostowany w usłudze App Service, odwołując się do *witryny sieci Web\_uwierzytelniania\_podpisywanie\_klucz* zmiennej środowiskowej. Jeśli to konieczne w lokalnym kontekście debugowania, postępuj zgodnie z instrukcjami [debugowania lokalnego przy użyciu uwierzytelniania](#local-debug) sekcji, aby pobrać klucz i zapisz go jako ustawienie aplikacji.

Wystawiony token może również obejmować inne oświadczenia i datę wygaśnięcia.  Co najmniej wystawiony token musi zawierać podmiot (**sub**) oświadczenia.

Może obsługiwać standard klienta `loginAsync()` metoda przeciążając trasy uwierzytelniania.  Jeśli klient wywołuje `client.loginAsync('custom');` się zalogować, musi być trasy `/.auth/login/custom`.  Można ustawić trasy dla kontrolera niestandardowe uwierzytelnianie za pomocą `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> Za pomocą `loginAsync()` podejście zapewnia, że token uwierzytelniania jest podłączony do każde kolejne wywołanie do usługi.
>
>

### <a name="user-info"></a>Jak: Pobieranie informacji uwierzytelnionego użytkownika
Gdy użytkownik jest uwierzytelniany przez usługę App Service, dostęp identyfikator przypisany użytkownik i inne informacje w kodzie zaplecza platformy .NET. Informacje o użytkowniku mogą służyć do podejmowania decyzji dotyczących autoryzacji w wewnętrznej bazie danych. Poniższy kod pobiera identyfikator użytkownika skojarzony z żądaniem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

Identyfikator SID jest tworzony na podstawie Identyfikatora użytkownika właściwe dla dostawcy i jest element statyczny dla danego użytkownika i dostawcy logowania.  Identyfikator SID ma wartość null dla uwierzytelniania nieprawidłowe tokeny.

Usługa App Service umożliwia również żądać określonych oświadczeń od dostawcy logowania. Każdy dostawca tożsamości może zapewnić więcej informacji przy użyciu dostawcy tożsamości zestawu SDK.  Na przykład można użyć interfejsu API Graph serwisu Facebook dla znajomych informacji.  W bloku dostawcy w witrynie Azure portal, można określić oświadczenia, które są wymagane. Niektóre oświadczenia wymagają dodatkowej konfiguracji za pomocą dostawcy tożsamości.

Poniższy kod wywoła **GetAppServiceIdentityAsync** metodę rozszerzenia, aby uzyskać poświadczenia logowania, które obejmują dostęp do tokenu potrzebne do wysyłania żądań do interfejsu API Graph serwisu Facebook:

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

Dodaj instrukcję using poufności informacji dotyczące `System.Security.Principal` zapewnienie **GetAppServiceIdentityAsync** — metoda rozszerzenia.

### <a name="authorize"></a>Jak: Ograniczanie dostępu do danych dla autoryzowanych użytkowników
W poprzedniej sekcji pokazaliśmy, jak pobrać identyfikator użytkownika uwierzytelnionego użytkownika. Można ograniczyć dostęp do danych i innych zasobów na podstawie tej wartości. Na przykład dodawanie kolumny userId do tabel i filtrowanie wyników zapytania za pomocą Identyfikatora użytkownika jest to prosty sposób ograniczyć zwrócone dane tylko dla autoryzowanych użytkowników. Poniższy kod zwraca wiersze danych, tylko wtedy, gdy jest to identyfikator SID pasuje do wartości w kolumnie Identyfikator UserId w tabeli TodoItem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

`Query()` Metoda zwraca `IQueryable` mogą być zmieniane przez LINQ do obsługi filtrowania.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Instrukcje: Dodawanie powiadomień wypychanych do projektu serwera
Dodawanie powiadomień wypychanych do projektu serwera, rozszerzając **MobileAppConfiguration** obiektu i Tworzenie klienta usługi Notification Hubs.

1. W programie Visual Studio, kliknij prawym przyciskiem myszy projekt serwera, a następnie kliknij przycisk **Zarządzaj pakietami NuGet**, wyszukaj `Microsoft.Azure.Mobile.Server.Notifications`, następnie kliknij przycisk **zainstalować**.
2. Powtórz ten krok, aby zainstalować `Microsoft.Azure.NotificationHubs` pakiet, który zawiera bibliotekę klienta usługi Notification Hubs.
3. W App_Start/Startup.MobileApp.cs i dodaj wywołanie do **AddPushNotifications()** — metoda rozszerzenia podczas inicjowania:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Dodaj następujący kod, który tworzy klienta usługi Notification Hubs:

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

Można teraz używać klienta usługi Notification Hubs wysyłać powiadomienia wypychane do zarejestrowanych urządzeń. Aby uzyskać więcej informacji, zobacz [Dodawanie powiadomień wypychanych do aplikacji](app-service-mobile-ios-get-started-push.md). Aby dowiedzieć się więcej o usłudze Notification Hubs, zobacz [Notification Hubs — omówienie](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="tags"></a>Jak: Włącz ukierunkowanego wypychania za pomocą tagów
Usługa Notification Hubs umożliwia wysyłanie powiadomień ukierunkowanych do określonych rejestracji przy użyciu tagów. Kilka tagów są tworzone automatycznie:

* Identyfikator instalacji identyfikuje określonego urządzenia.
* Nazwa użytkownika, w oparciu o SID, uwierzytelnionego identyfikuje określonego użytkownika.

Instalacja identyfikator jest możliwy z **installationId** właściwość **MobileServiceClient**.  Poniższy przykład pokazuje, jak dodać tag do określonej instalacji usługi Notification Hubs za pomocą Identyfikatora instalacji:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Dowolne tagi dostarczonych przez klienta podczas rejestracji powiadomień wypychanych są ignorowane przez wewnętrznej bazy danych, tworząc instalacji. Aby włączyć klienta dodać tagi do instalacji, należy utworzyć niestandardowy interfejs API dodaje znaczniki za pomocą poprzedni wzorzec.

Zobacz [tagi powiadomień wypychanych dodany klient] [ 5] w przykładzie ukończone Szybki Start App Service Mobile Apps, na przykład.

## <a name="push-user"></a>Jak: Wysyłanie powiadomień wypychanych do uwierzytelnionego użytkownika
Gdy uwierzytelniony użytkownik rejestruje powiadomienia wypychane, tag identyfikator użytkownika jest automatycznie dodawany do rejestracji. Za pomocą tego tagu, można wysyłać powiadomienia wypychane do wszystkich urządzeń zarejestrowanych przez tę osobę. Poniższy kod pobiera identyfikator SID użytkownika zgłaszającego żądanie i wysyła powiadomienie wypychane szablonu do każdej rejestracji urządzeń dla tej osoby:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Podczas rejestrowania w celu powiadomienia wypychane z uwierzytelnionego klienta, upewnij się, że to uwierzytelnianie jest ukończone przed podjęciem próby wykonania rejestracji. Aby uzyskać więcej informacji, zobacz [wypychane do użytkowników] [ 6] w przykładzie ukończone Szybki Start usługi App Service Mobile Apps dla zaplecza platformy .NET.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Instrukcje: Debugowanie i rozwiązywanie problemów z zestawem SDK .NET Server
Usługa Azure App Service oferuje kilka debugowania i techniki rozwiązywania problemów dotyczące aplikacji ASP.NET:

* [Monitorowanie usługi Azure App Service](../app-service/web-sites-monitor.md)
* [Włączanie rejestrowania diagnostycznego w usłudze Azure App Service](../app-service/troubleshoot-diagnostic-logs.md)
* [Rozwiązywanie problemów z usługi Azure App Service w programie Visual Studio](../app-service/troubleshoot-dotnet-visual-studio.md)

### <a name="logging"></a>Rejestrowanie
Można napisać do dzienników diagnostycznych usługi App Service przy użyciu standardowych zapisu śledzenia ASP.NET. Zanim można zapisywać w dziennikach, należy włączyć diagnostyki w zaplecza aplikacji mobilnej.

Aby włączyć diagnostykę i zapisywać w dziennikach:

1. Postępuj zgodnie z instrukcjami w [włączania diagnostyki](../app-service/troubleshoot-diagnostic-logs.md#enablediag).
2. Dodaj następującą instrukcję using w pliku kodu:

        using System.Web.Http.Tracing;
3. Utwórz moduł zapisujący śledzenia do zapisania z zaplecza platformy .NET do dzienników diagnostycznych, w następujący sposób:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Ponowne opublikowanie projektu serwera i dostępu do zaplecza aplikacji mobilnej do wykonania w ścieżce kodu za pomocą funkcji rejestrowania.
5. Pobierz i ocenić dzienników, zgodnie z opisem w [jak: Pobieranie dzienników](../app-service/troubleshoot-diagnostic-logs.md#download).

### <a name="local-debug"></a>Lokalne debugowanie przy użyciu uwierzytelniania
Można uruchomić aplikację lokalnie, aby przetestować zmiany przed ich opublikowaniem w chmurze. Dla większości zaplecza usługi Azure Mobile Apps, naciśnij klawisz *F5* podczas gdy w programie Visual Studio. Istnieją pewne dodatkowe zagadnienia podczas uwierzytelniania.

Konieczne jest posiadanie oparte na chmurze aplikacji mobilnych za pomocą aplikacji usługi uwierzytelniania/autoryzacji skonfigurowano, a Twój klient musi mieć określony jako host alternatywnej nazwy logowania punkt końcowy w chmurze. Zobacz dokumentację dla używanej platformy klienta, aby poznać konkretne kroki wymagane.

Upewnij się, że w zapleczu mobilnym ma [Microsoft.Azure.Mobile.Server.Authentication] zainstalowane. Następnie w aplikacji klasy początkowej OWIN, Dodaj następujące polecenie, po `MobileAppConfiguration` została zastosowana do Twojej `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

W poprzednim przykładzie, należy skonfigurować *authAudience* i *authIssuer* ustawienia aplikacji w ramach Twojego pliku Web.config plików do każdego być adresem URL katalogu aplikacji przy użyciu schematu HTTPS. Podobnie należy ustawić *authSigningKey* jako klucz podpisywania wartości aplikacji.
Aby uzyskać klucz podpisywania:

1. Przejdź do aplikacji w ramach [Azure Portal]
2. Kliknij przycisk **narzędzia**, **Kudu**, **Przejdź**.
3. W witrynie programu Kudu zarządzania kliknij **środowiska**.
4. Znajdź wartość *witryny sieci Web\_uwierzytelniania\_podpisywanie\_klucz*.

Użyj klucza podpisywania dla *authSigningKey* parametrów w pliku config aplikacji lokalnej.  Usługi zaplecza aplikacji mobilnych jest teraz wyposażony w celu sprawdzania poprawności tokenów podczas uruchamiania lokalnego, które klient uzyskuje token z punktu końcowego oparte na chmurze.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Azure Portal]: https://portal.azure.com
[NuGet.org]: https://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
