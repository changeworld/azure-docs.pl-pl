---
title: Jak współpracować z zestawem SDK serwera zaplecza platformy .NET dla Mobile Apps | Microsoft Docs
description: Dowiedz się, jak korzystać z zestawu SDK serwera zaplecza platformy .NET dla Azure App Service Mobile Apps.
keywords: App Service, Azure App Service, aplikacja mobilna, usługa mobilna, skalowanie, skalowalność, wdrażanie aplikacji, wdrażanie aplikacji platformy Azure
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
ms.openlocfilehash: 5372385bd4de0f5592a7fff3e4a78fbb5e1648dc
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388776"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Praca z zestawem SDK serwera zaplecza platformy .NET na potrzeby usługi Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

> [!NOTE]
> Usługa Visual Studio App Center obsługuje kompleksowe i zintegrowane usługi mające kluczowe znaczenie podczas tworzenia aplikacji mobilnych. Deweloperzy mogą używać usług do **tworzenia**, **testowania** i **dystrybuowania** w celu konfigurowania potoku ciągłej integracji i ciągłego wdrażania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie aplikacji za pomocą usług do **analizy** i **diagnostyki**, a także współpracować z użytkownikami za pomocą usługi do **wypychania**. Deweloperzy mogą również korzystać z usługi **uwierzytelniania** do uwierzytelniania użytkowników oraz usługi **danych** do utrwalania i synchronizowania danych aplikacji w chmurze.
>
> Jeśli chcesz zintegrować usługi w chmurze w aplikacji mobilnej, zarejestruj się w usłudze [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) już dziś.

W tym temacie pokazano, jak używać zestawu SDK serwera zaplecza platformy .NET w scenariuszach Mobile Apps Azure App Service. Zestaw Azure Mobile Apps SDK ułatwia współpracę z klientami mobilnymi z poziomu aplikacji ASP.NET.

> [!TIP]
> [Zestaw SDK programu .NET Server dla platformy Azure Mobile Apps][2] jest otwartym źródłem w serwisie GitHub. Repozytorium zawiera cały kod źródłowy obejmujący cały zestaw testów jednostkowych zestawu SDK serwera i niektóre przykładowe projekty.
>
>

## <a name="reference-documentation"></a>Dokumentacja referencyjna
Dokumentacja referencyjna dotycząca zestawu SDK serwera znajduje się tutaj: dokumentacja [platformy Azure Mobile Apps .NET][1].

## <a name="create-app"></a>Instrukcje: Tworzenie zaplecza aplikacji mobilnej platformy .NET
Jeśli uruchamiasz nowy projekt, możesz utworzyć aplikację App Service przy użyciu [Azure Portal] lub programu Visual Studio. Możesz uruchomić aplikację App Service lokalnie lub opublikować projekt w aplikacji mobilnej App Service opartej na chmurze.

Jeśli dodajesz możliwości mobilne do istniejącego projektu, zapoznaj się z sekcją [pobieranie i inicjowanie zestawu SDK](#install-sdk) .

### <a name="create-a-net-backend-using-the-azure-portal"></a>Tworzenie zaplecza platformy .NET przy użyciu Azure Portal
Aby utworzyć App Service zaplecza mobilnego, postępuj zgodnie z [samouczkiem szybki start][3] lub wykonaj następujące czynności:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Wróć do bloku *wprowadzenie* , w obszarze **Utwórz tabelę interfejsu API**wybierz **C#** jako **język zaplecza**. Kliknij pozycję **Pobierz**, Wyodrębnij skompresowane pliki projektu na komputer lokalny, a następnie otwórz rozwiązanie w programie Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2017"></a>Tworzenie zaplecza platformy .NET przy użyciu programu Visual Studio 2017

Zainstaluj obciążenie platformy Azure za pośrednictwem Instalator programu Visual Studio, aby publikować w projekcie Mobile Apps platformy Azure z poziomu programu Visual Studio. Po zainstalowaniu zestawu SDK Utwórz aplikację ASP.NET, wykonując następujące czynności:

1. Otwórz okno dialogowe **Nowy projekt** (z **pliku** > **nowym** > **projekcie...** ).
2. Rozwiń **pozycję C# Wizualizacja** i wybierz pozycję **Sieć Web**.
3. Wybierz pozycję **aplikacja sieci Web ASP.NET (.NET Framework)** .
4. Wypełnij pole Nazwa projektu. Następnie kliknij przycisk **OK**.
5. Z listy szablonów wybierz pozycję **aplikacja mobilna platformy Azure** .
6. Kliknij przycisk **OK** , aby utworzyć rozwiązanie.
7. Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Publikuj...** , a następnie wybierz **App Service** jako element docelowy publikowania.
8. Postępuj zgodnie z monitami, aby przeprowadzić uwierzytelnianie, i wybierz nową lub istniejącą Azure App Service do opublikowania.

### <a name="create-a-net-backend-using-visual-studio-2015"></a>Tworzenie zaplecza platformy .NET przy użyciu programu Visual Studio 2015

Zainstaluj [zestaw Azure SDK dla platformy .NET][4] (wersja 2.9.0 lub nowsza), aby utworzyć projekt usługi Azure Mobile Apps w programie Visual Studio. Po zainstalowaniu zestawu SDK Utwórz aplikację ASP.NET, wykonując następujące czynności:

1. Otwórz okno dialogowe **Nowy projekt** (z **pliku** > **nowym** > **projekcie...** ).
2. Rozwiń węzeł **Szablony** > **C#Wizualizacja**, a następnie wybierz pozycję **Sieć Web**.
3. Wybierz pozycję **Aplikacja internetowa platformy ASP.NET**.
4. Wypełnij pole Nazwa projektu. Następnie kliknij przycisk **OK**.
5. W obszarze *szablony ASP.NET 4.5.2*wybierz pozycję **aplikacja mobilna platformy Azure**. Sprawdź **host w chmurze** , aby utworzyć zaplecze mobilne w chmurze, w którym można opublikować ten projekt.
6. Kliknij przycisk **OK**.

## <a name="install-sdk"></a>Instrukcje: pobieranie i inicjowanie zestawu SDK
Zestaw SDK jest dostępny w witrynie [NuGet.org]. Ten pakiet zawiera podstawowe funkcje wymagane do rozpoczęcia korzystania z zestawu SDK. Aby zainicjować zestaw SDK, należy wykonać akcje na obiekcie **HttpConfiguration** .

### <a name="install-the-sdk"></a>Instalacja zestawu SDK
Aby zainstalować zestaw SDK, kliknij prawym przyciskiem myszy projekt serwera w programie Visual Studio, wybierz polecenie Zarządzaj pakietami **NuGet**, Wyszukaj pozycję [Microsoft.Azure.Mobile.Server] , a następnie kliknij przycisk **Instaluj**.

### <a name="server-project-setup"></a>Inicjowanie projektu serwera
Projekt serwera zaplecza platformy .NET jest zainicjowany podobnie jak w przypadku innych projektów ASP.NET, poprzez dołączenie klasy uruchomieniowej OWIN. Upewnij się, że przywoływano pakiet NuGet `Microsoft.Owin.Host.SystemWeb`. Aby dodać tę klasę w programie Visual Studio, kliknij prawym przyciskiem myszy na projekt serwera, a następnie wybierz **Dodaj** >
**nowy element**, następnie **Web** > **Ogólne** > **Klasa początkowa OWIN**.  Klasa jest generowana przy użyciu następującego atrybutu:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

W metodzie `Configuration()` klasy uruchomieniowej OWIN Użyj obiektu **HttpConfiguration** , aby skonfigurować środowisko Mobile Apps platformy Azure.
W poniższym przykładzie jest inicjowany projekt serwera bez dodatkowych funkcji:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Aby włączyć poszczególne funkcje, należy wywołać metody rozszerzania obiektu **MobileAppConfiguration** przed wywołaniem **ApplyTo**. Na przykład poniższy kod dodaje trasy domyślne do wszystkich kontrolerów interfejsu API, których atrybut `[MobileAppController]` podczas inicjalizacji:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

Serwer szybki start z Azure Portal wywołuje **UseDefaultConfiguration ()** . Ten odpowiednik do następującej konfiguracji:

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

Używane metody rozszerzające są następujące:

* `AddMobileAppHomeController()` udostępnia domyślną stronę główną usługi Azure Mobile Apps.
* `MapApiControllers()` udostępnia niestandardowe możliwości interfejsu API dla kontrolerów WebAPI z atrybutem `[MobileAppController]`.
* `AddTables()` zapewnia mapowanie punktów końcowych `/tables` do kontrolerów tabel.
* `AddTablesWithEntityFramework()` jest zwięzły do mapowania `/tables` punktów końcowych przy użyciu kontrolerów opartych na Entity Framework.
* `AddPushNotifications()` zapewnia prostą metodę rejestrowania urządzeń dla Notification Hubs.
* `MapLegacyCrossDomainController()` udostępnia standardowe nagłówki CORS na potrzeby lokalnego tworzenia.

### <a name="sdk-extensions"></a>Rozszerzenia zestawu SDK
Następujące pakiety rozszerzeń oparte na pakiecie NuGet udostępniają różne funkcje mobilne, które mogą być używane przez aplikację. Rozszerzenia są włączane podczas inicjowania przy użyciu obiektu **MobileAppConfiguration** .

* [Microsoft.Azure.Mobile.Server.Quickstart] obsługuje konfigurację Mobile Apps podstawowa. Dodano do konfiguracji przez wywołanie metody rozszerzenia **UseDefaultConfiguration** podczas inicjacji. To rozszerzenie obejmuje następujące rozszerzenia: powiadomienia, uwierzytelnianie, jednostki, tabele, wiele domen i pakiety domowe. Ten pakiet jest używany przez Mobile Apps Szybki Start dostępny w Azure Portal.
* [Microsoft. Azure. Mobile. Server. Home](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) implementuje domyślną *stronę aplikacji mobilnej* dla katalogu głównego witryny sieci Web. Dodaj do konfiguracji, wywołując metodę rozszerzenia **AddMobileAppHomeController** .
* [Microsoft. Azure. Mobile. Server. Tables](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) zawiera klasy umożliwiające pracę z danymi i konfiguracjami potoku danych. Dodaj do konfiguracji, wywołując metodę rozszerzenia **Addtables** .
* [Microsoft. Azure. Mobile. Server. Entity](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) umożliwia Entity Framework dostęp do danych w SQL Database. Dodaj do konfiguracji, wywołując metodę rozszerzenia **AddTablesWithEntityFramework** .
* [Microsoft.Azure.Mobile.Server.Authentication] umożliwia uwierzytelnianie i Konfigurowanie oprogramowania Owin używanego do weryfikowania tokenów. Dodaj do konfiguracji, wywołując **AddAppServiceAuthentication** i **IAppBuilder**. Metody rozszerzenia **UseAppServiceAuthentication** .
* [Microsoft.Azure.Mobile.Server.Notifications] Włącza powiadomienia wypychane i definiuje punkt końcowy rejestracji wypychanej. Dodaj do konfiguracji, wywołując metodę rozszerzenia **AddPushNotifications** .
* [Microsoft. Azure. Mobile. Server. CrossDomain](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) tworzy kontroler, który obsługuje dane do starszych przeglądarek sieci Web z aplikacji mobilnej. Dodaj do konfiguracji, wywołując metodę rozszerzenia **MapLegacyCrossDomainController** .
* [Microsoft.Azure.Mobile.Server.Login] udostępnia metodę AppServiceLoginHandler. istoken (), która jest metodą statyczną używaną podczas niestandardowych scenariuszy uwierzytelniania.

## <a name="publish-server-project"></a>Instrukcje: publikowanie projektu serwera
W tej sekcji przedstawiono sposób publikowania projektu zaplecza .NET z programu Visual Studio. Możesz również wdrożyć projekt zaplecza za pomocą [narzędzia Git](../app-service/deploy-local-git.md) lub dowolnej innej dostępnej metody.

1. W programie Visual Studio Skompiluj ponownie projekt, aby przywrócić pakiety NuGet.
2. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt, a następnie kliknij pozycję **Publikuj**. Przy pierwszym publikowaniu należy zdefiniować profil publikowania. Jeśli masz już zdefiniowany profil, możesz go zaznaczyć i kliknąć przycisk **Publikuj**.
3. Jeśli zostanie wyświetlony monit o wybranie elementu docelowego publikowania, kliknij **Microsoft Azure App Service** > **dalej**, a następnie (w razie potrzeby) Zaloguj się przy użyciu poświadczeń platformy Azure.
   Program Visual Studio pobiera i bezpiecznie przechowuje ustawienia publikowania bezpośrednio z platformy Azure.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Wybierz swoją **subskrypcję**, wybierz pozycję **Typ zasobu** z **widoku**, rozwiń węzeł **aplikacja mobilna**, a następnie kliknij pozycję zaplecze aplikacji mobilnej, a następnie kliknij przycisk **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Sprawdź informacje o profilu publikowania i kliknij przycisk **Publikuj**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Po pomyślnym opublikowaniu zaplecza aplikacji mobilnej zostanie wyświetlona strona docelowa wskazująca powodzenie.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a>Instrukcje: Definiowanie kontrolera tabeli
Zdefiniuj kontroler tabeli, aby udostępnić tabelę SQL klientom mobilnym.  Konfigurowanie kontrolera tabeli wymaga wykonania trzech kroków:

1. Utwórz klasę obiektu Transfer danych (DTO).
2. Skonfiguruj odwołanie do tabeli w klasie typu Mobile DbContext.
3. Utwórz kontroler tabeli.

Obiekt Transfer danych (DTO) to zwykły C# obiekt, który dziedziczy z `EntityData`.  Na przykład:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

DTO jest używany do definiowania tabeli w bazie danych SQL.  Aby utworzyć wpis bazy danych, Dodaj właściwość `DbSet<>` do używanego kontekstu DbContext.  W domyślnym szablonie projektu dla usługi Azure Mobile Apps, DbContext jest wywoływana `Models\MobileServiceContext.cs`:

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

1. Kliknij prawym przyciskiem myszy folder controllers i wybierz polecenie **Dodaj** **kontroler > ..** ..
2. Wybierz opcję **kontroler tabeli Mobile Apps Azure** , a następnie kliknij przycisk **Dodaj**.
3. W oknie dialogowym **Dodawanie kontrolera** :
   * Z listy rozwijanej **Klasa modelu** wybierz nowy DTO.
   * Z listy rozwijanej **DbContext** wybierz klasę usługi mobilnej DbContext.
   * Nazwa kontrolera zostanie utworzona.
4. Kliknij pozycję **Add** (Dodaj).

Projekt szybkiego startu serwera zawiera przykład dla prostego **TodoItemController**.

### <a name="adjust-pagesize"></a>Instrukcje: Dostosowywanie rozmiaru stronicowania tabeli
Domyślnie usługa Azure Mobile Apps zwraca rekordy 50 dla żądania.  Stronicowanie gwarantuje, że klient programu nie wiąże się z zbyt długim wątkiem interfejsu użytkownika ani z serwerem, zapewniając dobry komfort pracy użytkownika. Aby zmienić rozmiar stronicowania tabeli, zwiększ wartość po stronie serwera "dozwolony rozmiar zapytania" i rozmiar strony klienta po stronie serwera "dozwolony rozmiar zapytania" jest dostosowywany przy użyciu `EnableQuery` atrybutu:

    [EnableQuery(PageSize = 500)]

Upewnij się, że wartość PageSize jest taka sama lub większa od rozmiaru żądanego przez klienta.  Szczegółowe informacje na temat zmiany rozmiaru strony klienta znajdują się w dokumentacji porady klienta.

## <a name="how-to-define-a-custom-api-controller"></a>Instrukcje: definiowanie niestandardowego kontrolera interfejsu API
Niestandardowy kontroler interfejsu API zapewnia najbardziej podstawowe funkcje zaplecza aplikacji mobilnej przez udostępnienie punktu końcowego. Można zarejestrować kontroler interfejsu API specyficzny dla urządzeń przenośnych przy użyciu atrybutu [MobileAppController]. Atrybut `MobileAppController` rejestruje trasę, konfiguruje serializator JSON Mobile Apps i włącza [Sprawdzanie wersji klienta](app-service-mobile-client-and-server-versioning.md).

1. W programie Visual Studio kliknij prawym przyciskiem myszy folder controllers, a następnie kliknij pozycję **Dodaj** **kontroler** > , wybierz **kontroler sieci Web API 2&mdash;puste** i kliknij przycisk **Dodaj**.
2. Podaj **nazwę kontrolera**, taką jak `CustomController`, i kliknij przycisk **Dodaj**.
3. W nowym pliku klasy kontrolera Dodaj następującą instrukcję using:

        using Microsoft.Azure.Mobile.Server.Config;
4. Zastosuj atrybut **[MobileAppController]** do definicji klasy kontrolera interfejsu API, tak jak w poniższym przykładzie:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. W pliku App_Start/Startup.MobileApp.cs Dodaj wywołanie metody rozszerzenia **MapApiControllers** , jak w poniższym przykładzie:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Można również użyć metody rozszerzenia `UseDefaultConfiguration()` zamiast `MapApiControllers()`. Klienci, którzy nie mają zastosowanych **MobileAppControllerAttribute** , nadal mogą uzyskać dostęp do wszystkich kontrolerów, ale mogą nie być prawidłowo używani przez klientów przy użyciu dowolnego zestawu SDK klienta aplikacji mobilnej.

## <a name="how-to-work-with-authentication"></a>Instrukcje: korzystanie z uwierzytelniania
Usługa Azure Mobile Apps używa App Service uwierzytelniania/autoryzacji do zabezpieczania zaplecza mobilnego.  W tej sekcji przedstawiono sposób wykonywania następujących zadań związanych z uwierzytelnianiem w projekcie serwera zaplecza platformy .NET:

* [Instrukcje: Dodawanie uwierzytelniania do projektu serwera](#add-auth)
* [Instrukcje: korzystanie z uwierzytelniania niestandardowego dla aplikacji](#custom-auth)
* [Instrukcje: pobieranie informacji o uwierzytelnionym użytkowniku](#user-info)
* [Instrukcje: ograniczanie dostępu do danych dla autoryzowanych użytkowników](#authorize)

### <a name="add-auth"></a>Instrukcje: Dodawanie uwierzytelniania do projektu serwera
Możesz dodać uwierzytelnianie do projektu serwera, rozszerzając obiekt **MobileAppConfiguration** i konfigurując oprogramowanie pośredniczące Owin. Instalując pakiet [Microsoft.Azure.Mobile.Server.Quickstart] i Wywołaj metodę rozszerzenia **UseDefaultConfiguration** , możesz przejść do kroku 3.

1. W programie Visual Studio Zainstaluj pakiet [Microsoft.Azure.Mobile.Server.Authentication] .
2. W pliku projektu Startup.cs Dodaj następujący wiersz kodu na początku metody **konfiguracji** :

        app.UseAppServiceAuthentication(config);

    Ten składnik pośredniczący OWIN sprawdza poprawność tokenów wystawionych przez skojarzoną bramę App Service.
3. Dodaj atrybut `[Authorize]` do dowolnych kontrolerów lub metod, które wymagają uwierzytelniania.

Aby dowiedzieć się, jak uwierzytelniać klientów w zapleczu Mobile Apps, zobacz [Dodawanie uwierzytelniania do aplikacji](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Instrukcje: korzystanie z uwierzytelniania niestandardowego dla aplikacji
> [!IMPORTANT]
> Aby włączyć uwierzytelnianie niestandardowe, należy najpierw włączyć uwierzytelnianie App Service bez wybierania dostawcy App Service w Azure Portal. Spowoduje to włączenie zmiennej środowiskowej WEBSITE_AUTH_SIGNING_KEY, gdy jest hostowany.
> 
> 
> Jeśli nie chcesz używać jednego z App Service dostawcy uwierzytelniania/autoryzacji, możesz zaimplementować własny system logowania. Zainstaluj pakiet [Microsoft.Azure.Mobile.Server.Login] , aby pomóc w generowaniu tokenu uwierzytelniania.  Podaj własny kod służący do weryfikowania poświadczeń użytkownika. Można na przykład zaewidencjonować hasła z zasolonymi i skrótami w bazie danych. W poniższym przykładzie metoda `isValidAssertion()` (zdefiniowana w innym miejscu) jest odpowiedzialna za te kontrole.

Uwierzytelnianie niestandardowe jest uwidaczniane przez utworzenie ApiController i Uwidacznianie akcji `register` i `login`. Klient powinien użyć niestandardowego interfejsu użytkownika, aby zebrać informacje od użytkownika.  Informacje są następnie przesyłane do interfejsu API przy użyciu standardowego wywołania POST protokołu HTTP. Gdy serwer zweryfikuje potwierdzenie, token zostanie wystawiony przy użyciu metody `AppServiceLoginHandler.CreateToken()`.  ApiController **nie powinien** używać atrybutu `[MobileAppController]`.

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

W poprzednim przykładzie LoginResult i LoginResultUser są obiektami możliwymi do serializacji, które uwidaczniają wymagane właściwości. Klient oczekuje, że odpowiedzi logowania mają być zwracane jako obiekty JSON w postaci:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

Metoda `AppServiceLoginHandler.CreateToken()` obejmuje *odbiorców* i parametr *wystawcy* . Oba te parametry są ustawiane na adres URL katalogu głównego aplikacji przy użyciu schematu HTTPS. Podobnie należy ustawić *secretKey* jako wartość klucza podpisywania aplikacji. Nie należy rozpowszechniać klucza podpisywania na kliencie, ponieważ może służyć do mennic kluczy i personifikacji użytkowników. Możesz uzyskać klucz podpisywania, który jest hostowany w App Service, odwołując się do *witryny internetowej\_uwierzytelniania\_PODpisywanie\_kluczowej* zmiennej środowiskowej. Jeśli jest to konieczne w lokalnym kontekście debugowania, postępuj zgodnie z instrukcjami w sekcji [debugowanie lokalne z uwierzytelnianiem](#local-debug) w celu pobrania klucza i zapisania go jako ustawienia aplikacji.

Wystawiony token może również obejmować inne oświadczenia i datę wygaśnięcia.  W minimalnym stopniu wystawiony token musi zawierać zastrzeżenie subject (**Sub**).

Można obsługiwać standardową metodę `loginAsync()` klienta przez przeciążanie trasy uwierzytelniania.  Jeśli klient wywoła `client.loginAsync('custom');`, aby się zalogować, trasa musi być `/.auth/login/custom`.  Można ustawić trasę dla niestandardowego kontrolera uwierzytelniania przy użyciu `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> Użycie podejścia `loginAsync()` gwarantuje, że token uwierzytelniania jest dołączany do każdego kolejnego wywołania usługi.
>
>

### <a name="user-info"></a>Instrukcje: pobieranie informacji o uwierzytelnionym użytkowniku
Po uwierzytelnieniu użytkownika przez App Service można uzyskać dostęp do przypisanego identyfikatora użytkownika i innych informacji w kodzie zaplecza platformy .NET. Informacje o użytkowniku mogą służyć do podejmowania decyzji dotyczących autoryzacji w zapleczu. Poniższy kod uzyskuje identyfikator użytkownika skojarzony z żądaniem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

Identyfikator SID pochodzi od identyfikatora użytkownika określonego dla dostawcy i jest statyczny dla danego użytkownika i dostawcy logowania.  Identyfikator SID ma wartość null dla nieprawidłowych tokenów uwierzytelniania.

App Service również umożliwia żądanie określonych oświadczeń od dostawcy logowania. Każdy dostawca tożsamości może uzyskać więcej informacji przy użyciu zestawu SDK dostawcy tożsamości.  Na przykład możesz użyć interfejs API programu Graph w serwisie Facebook do uzyskania informacji o znajomych.  Można określić oświadczenia, które są żądane w bloku dostawcy w Azure Portal. Niektóre oświadczenia wymagają dodatkowej konfiguracji z dostawcą tożsamości.

Poniższy kod wywołuje metodę rozszerzenia **GetAppServiceIdentityAsync** , aby uzyskać poświadczenia logowania, w tym token dostępu wymagany do żądania do interfejs API programu Graph w serwisie Facebook:

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

Dodaj instrukcję using dla `System.Security.Principal`, aby zapewnić metodę rozszerzenia **GetAppServiceIdentityAsync** .

### <a name="authorize"></a>Instrukcje: ograniczanie dostępu do danych dla autoryzowanych użytkowników
W poprzedniej sekcji pokazano, jak pobrać identyfikator użytkownika uwierzytelnionego użytkownika. Można ograniczyć dostęp do danych i innych zasobów na podstawie tej wartości. Na przykład dodanie kolumny userId do tabel i filtrowanie wyników zapytania według identyfikatora użytkownika jest prostym sposobem na ograniczenie zwracanych danych tylko do autoryzowanych użytkowników. Poniższy kod zwraca wiersze danych tylko wtedy, gdy identyfikator SID jest zgodny z wartością w kolumnie UserId w tabeli TodoItem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

Metoda `Query()` zwraca `IQueryable`, który może być manipulowany przez LINQ w celu obsługi filtrowania.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Instrukcje: Dodawanie powiadomień wypychanych do projektu serwera
Dodaj powiadomienia wypychane do projektu serwera, rozszerzając obiekt **MobileAppConfiguration** i tworząc klienta Notification Hubs.

1. W programie Visual Studio kliknij prawym przyciskiem myszy projekt serwera, kliknij polecenie **Zarządzaj pakietami NuGet**, Wyszukaj `Microsoft.Azure.Mobile.Server.Notifications`, a następnie kliknij przycisk **Instaluj**.
2. Powtórz ten krok, aby zainstalować pakiet `Microsoft.Azure.NotificationHubs`, w którym znajduje się Notification Hubsa Biblioteka kliencka.
3. W App_Start/Startup.MobileApp.cs i Dodaj wywołanie metody rozszerzenia **AddPushNotifications ()** podczas inicjowania:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Dodaj następujący kod, który tworzy klienta Notification Hubs:

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

Za pomocą klienta Notification Hubs można teraz wysyłać powiadomienia wypychane do zarejestrowanych urządzeń. Aby uzyskać więcej informacji, zobacz [Dodawanie powiadomień wypychanych do aplikacji](app-service-mobile-ios-get-started-push.md). Aby dowiedzieć się więcej na temat Notification Hubs, zobacz [Omówienie usługi Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="tags"></a>Instrukcje: Włączanie wypychania skierowanego przy użyciu tagów
Notification Hubs umożliwia wysyłanie skierowanych powiadomień do określonych rejestracji przy użyciu tagów. Automatycznie tworzone są kilka tagów:

* Identyfikator instalacji identyfikuje określone urządzenie.
* Identyfikator użytkownika na podstawie uwierzytelnionego identyfikatora SID identyfikuje określonego użytkownika.

Do identyfikatora instalacji można uzyskać dostęp z właściwości **Identyfikator InstallationID** w **MobileServiceClient**.  Poniższy przykład pokazuje, jak używać identyfikatora instalacji, aby dodać tag do określonej instalacji w Notification Hubs:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Wszystkie Tagi dostarczone przez klienta podczas rejestracji powiadomień wypychanych są ignorowane przez zaplecze podczas tworzenia instalacji. Aby umożliwić klientowi Dodawanie tagów do instalacji, należy utworzyć niestandardowy interfejs API, który dodaje Tagi przy użyciu poprzedniego wzorca.

Zapoznaj się z przykładem [Dodawanie tagów powiadomień wypychanych do klienta][5] w App Service Mobile Apps zakończono przykładowego przewodnika Szybki Start.

## <a name="push-user"></a>Instrukcje: wysyłanie powiadomień wypychanych do uwierzytelnionego użytkownika
Gdy uwierzytelniony użytkownik rejestruje się w celu otrzymywania powiadomień wypychanych, tag identyfikatora użytkownika jest automatycznie dodawany do rejestracji. Za pomocą tego tagu można wysyłać powiadomienia wypychane do wszystkich urządzeń zarejestrowanych przez tę osobę. Poniższy kod pobiera identyfikator SID użytkownika wysyłającego żądanie i wysyła powiadomienie wypychane szablonu do każdej rejestracji urządzenia dla tej osoby:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Podczas rejestrowania powiadomień wypychanych z poziomu uwierzytelnionego klienta upewnij się, że przed próbą rejestracji zostanie wykonane uwierzytelnianie. Aby uzyskać więcej informacji, zobacz [wypychanie do użytkowników][6] w App Service Mobile Apps zakończono przykładowego przewodnika Szybki Start dla zaplecza platformy .NET.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Instrukcje: debugowanie i rozwiązywanie problemów z zestawem SDK programu .NET Server
Azure App Service oferuje kilka technik debugowania i rozwiązywania problemów dla aplikacji ASP.NET:

* [Monitorowanie Azure App Service](../app-service/web-sites-monitor.md)
* [Włączanie rejestrowania diagnostycznego w Azure App Service](../app-service/troubleshoot-diagnostic-logs.md)
* [Rozwiązywanie problemów z Azure App Service w programie Visual Studio](../app-service/troubleshoot-dotnet-visual-studio.md)

### <a name="logging"></a>Rejestrowanie
Możesz zapisywać w dziennikach diagnostycznych App Service przy użyciu standardowego zapisu śledzenia ASP.NET. Aby można było zapisywać w dziennikach, należy włączyć diagnostykę w zapleczu aplikacji mobilnej.

Aby włączyć diagnostykę i zapisywać w dziennikach:

1. Wykonaj kroki opisane w temacie [Włączanie rejestrowania aplikacji (Windows)](../app-service/troubleshoot-diagnostic-logs.md#enable-application-logging-windows).
2. Dodaj następującą instrukcję using w pliku kodu:

        using System.Web.Http.Tracing;
3. Utwórz moduł zapisujący śledzenia do zapisu z zaplecza platformy .NET do dzienników diagnostycznych w następujący sposób:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Opublikuj ponownie projekt serwera i uzyskaj dostęp do zaplecza aplikacji mobilnej, aby wykonać ścieżkę kodu z rejestrowaniem.
5. Pobierz i Oceń dzienniki, zgodnie z opisem w [pliku dziennika dostępu](../app-service/troubleshoot-diagnostic-logs.md#access-log-files).

### <a name="local-debug"></a>Debugowanie lokalne z uwierzytelnianiem
Możesz uruchomić aplikację lokalnie, aby przetestować zmiany przed opublikowaniem ich w chmurze. W przypadku większości punktów końcowych platformy Azure Mobile Apps naciśnij klawisz *F5* w programie Visual Studio. Podczas korzystania z uwierzytelniania należy jednak wziąć pod uwagę pewne dodatkowe zagadnienia.

Aplikacja mobilna oparta na chmurze musi mieć skonfigurowaną App Service uwierzytelnianie/autoryzację, a klient musi mieć punkt końcowy w chmurze określony jako alternatywny Host logowania. Zapoznaj się z dokumentacją platformy klienckiej, wykonując określone kroki.

Upewnij się, że zaplecze mobilne ma zainstalowany [Microsoft.Azure.Mobile.Server.Authentication] . Następnie w klasie uruchomieniowej OWIN aplikacji Dodaj następujące elementy, po zastosowaniu `MobileAppConfiguration` do `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

W poprzednim przykładzie należy skonfigurować ustawienia aplikacji *authAudience* i *AuthIssuer* w pliku Web. config w taki sposób, aby były adresami URL katalogu głównego aplikacji przy użyciu schematu https. Podobnie należy ustawić *authSigningKey* jako wartość klucza podpisywania aplikacji.
Aby uzyskać klucz podpisywania:

1. Przejdź do aplikacji w ramach [Azure Portal]
2. Kliknij pozycję **Narzędzia**, **kudu**, **Przejdź**.
3. W witrynie zarządzania kudu kliknij pozycję **środowisko**.
4. Znajdź wartość w obszarze *Witryna sieci web\_uwierzytelniania\_podpisywania klucza\_* .

Użyj klucza podpisywania dla parametru *authSigningKey* w konfiguracji aplikacji lokalnej.  Zaplecze mobilnej jest teraz wyposażone w sprawdzanie poprawności tokenów podczas uruchamiania lokalnego, który klient uzyskuje token z punktu końcowego opartego na chmurze.

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
