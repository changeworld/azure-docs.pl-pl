---
title: Uaktualnienie z usług mobilnych Azure App Service
description: Dowiedz się, jak łatwo przeprowadzić uaktualnienie aplikacji usługi Mobile Services do aplikacji usługi Mobile App Service
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 9c0ac353-afb6-462b-ab94-d91b8247322f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 05041637aa5cbb044e6731208825f75edec83352
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32157048"
---
# <a name="upgrade-your-existing-net-azure-mobile-service-to-app-service"></a>Uaktualnij istniejącą usługę Mobile .NET Azure App Service
Mobile App Service jest nowy sposób tworzenia aplikacji dla urządzeń przenośnych przy użyciu programu Microsoft Azure. Aby dowiedzieć się więcej, zobacz [co to są Mobile Apps?].

W tym temacie opisano sposób uaktualnienia istniejącej aplikacji zaplecza .NET z usług Azure Mobile Services do nowej aplikacji usługi Mobile Apps. Podczas wykonywania tego uaktualnienia istniejącej aplikacji usługi Mobile Services może kontynuować działanie.   Jeśli konieczne jest uaktualnienie aplikacji zaplecza Node.js, zapoznaj się [uaktualniania usług Mobile Node.js](app-service-mobile-node-backend-upgrading-from-mobile-services.md).

Po uaktualnieniu zaplecza mobilnego do usługi Azure App Service, ma dostęp do wszystkich funkcji usługi aplikacji i są rozliczane zgodnie ze standardem [cennik usługi aplikacji], nie Mobile Services cennik.

## <a name="migrate-vs-upgrade"></a>Migrowanie i uaktualnianie
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> Zaleca się że [przeprowadzenie migracji](app-service-mobile-migrating-from-mobile-services.md) przed rozpoczęciem uaktualnienia. W ten sposób można umieścić obie wersje aplikacji na tym samym planie usługi aplikacji i pociągnąć za sobą nie dodatkowych kosztów.
>
>

### <a name="improvements-in-mobile-apps-net-server-sdk"></a>Ulepszenia serwera Mobile Apps .NET SDK
Uaktualnienie do nowej [zestaw SDK usługi Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) zapewnia następujące korzyści:

* Większa elastyczność w zależności NuGet. Środowisko macierzyste nie zawiera już własną wersje pakietów NuGet, dzięki czemu można użyć alternatywnej wersji zgodne. Jednak jeśli istnieją nowe bugfixes krytycznych lub aktualizacje zabezpieczeń, zestaw SDK usługi Mobile serwera lub zależności, należy ręcznie zaktualizować usługę.
* Większą elastyczność w zestawie SDK usług mobilnych. Jawnie można kontrolować, które funkcje i trasy są skonfigurowane, takich jak uwierzytelnianie, tabeli interfejsów API i punktu końcowego rejestracji wypychania. Aby dowiedzieć się więcej, zobacz [jak używać serwera .NET SDK usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).
* Obsługa innych typów projektów programu ASP.NET i trasy. Można hostować kontrolerów MVC i interfejsu API sieci Web w tej samej projektu jako projektu zaplecza aplikacji mobilnych.
* Obsługa nowych funkcji uwierzytelniania usługi aplikacji, które umożliwiają używanie typowych konfiguracji uwierzytelniania w sieci web i aplikacji mobilnych.

## <a name="overview"></a>Ogólne omówienie uaktualnienia
W wielu przypadkach uaktualniania będzie najprostszą przełączenie na nowy serwer Mobile Apps SDK i ponownego publikowania kodu na nowe wystąpienie aplikacji mobilnej. Istnieją, jednak niektóre scenariusze, które będą wymagać dodatkowej konfiguracji, takich jak uwierzytelnianie zaawansowanych scenariuszy i Praca z zaplanowanych zadań. Każdy z tych zostało opisane w kolejnych sekcjach.

> [!TIP]
> Zaleca się, że należy dokładnie zapoznać się pozostałej części tego tematu całkowicie przed rozpoczęciem uaktualniania. Zwróć uwagę na każdej funkcji Użyj wymienione poniżej.
>
>

Są zestawy SDK klienta usługi Mobile Services **nie** zgodny z nowym serwerem Mobile Apps SDK. W celu zapewnienia ciągłości usługi dla aplikacji, nie należy opublikować zmian do lokacji aktualnie obsługujący opublikowanych klientów. Zamiast tego należy utworzyć nowej aplikacji mobilnej, która służy jako duplikat. Tej aplikacji można umieścić na tym samym planu usługi aplikacji, aby uniknąć ponoszenia dodatkowych kosztów finansowych.

Następnie będzie mieć dwie wersje aplikacji: taki, który pozostaje taki sam i służy opublikowane aplikacje symbole oraz inne, które następnie można uaktualnić i docelowych z nową wersją klienta. Można przenieść i testowanie kodu w tempie użytkownika, ale należy upewnić się, że wprowadzone wszystkie poprawki zastosowane do obu. Gdy uznasz, że odpowiednią liczbę klientów, które aplikacje w symbole zostały zaktualizowane do najnowszej wersji, można usunąć oryginalny zmigrowana aplikacja Jeśli jest to wymagane.

Pełna konspekt procesu uaktualniania przebiega następująco:

1. Tworzenie nowej aplikacji mobilnej
2. Aktualizowanie projektów korzystać z nowych zestawów SDK serwera
3. Zwolnij nowej wersji aplikacji klienta
4. (Opcjonalnie) Usuń zmigrowane oryginalnego wystąpienia

## <a name="mobile-app-version"></a>Tworzenie drugiego wystąpienia aplikacji
Pierwszym krokiem podczas uaktualniania jest Utwórz zasób aplikacji mobilnej, który będzie hostował nową wersję aplikacji. Już po przeprowadzeniu migracji istniejącej usługi mobilnej, można utworzyć tej wersji na ten sam plan hostingu. Otwórz [portalu Azure] i przejdź do migrowanych aplikacji. Zanotuj planu usługi App Service jest uruchomiona na.

Następnie należy utworzyć drugiego wystąpienia aplikacji, postępując [instrukcje dotyczące tworzenia zaplecza .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app). Po wyświetleniu monitu można wybrać planu usługi aplikacji lub "plan hostingu" Wybierz plan migrowanych aplikacji.

Prawdopodobnie można korzystać z tej samej bazy danych i Centrum powiadomień, tak jak w usłudze Mobile Services. Możesz skopiować te wartości, otwierając [portalu Azure] , a następnie przejść do oryginalnej aplikacji, kliknij przycisk **ustawienia** > **ustawienia aplikacji**. W obszarze **parametry połączenia**, kopiowania `MS_NotificationHubConnectionString` i `MS_TableConnectionString`. Przejdź do nowej witryny uaktualnienia i wklej je w, zastępując istniejące wartości. Powtórz te kroki dla innych ustawień aplikacji potrzeb aplikacji. Jeśli nie używa zmigrowanej usługi, można znaleźć parametrów połączenia i ustawień aplikacji z **Konfiguruj** kartę sekcji usług Mobile Services [klasycznego portalu Azure].

Utwórz kopię projektu ASP.NET dla aplikacji i opublikować go do nowej witryny. Przy użyciu kopii zaktualizowany o nowy adres URL aplikacji klienckiej, sprawdź, czy wszystko działa zgodnie z oczekiwaniami.

## <a name="updating-the-server-project"></a>Aktualizowanie projektu serwera
Mobile Apps udostępnia nową [zestawu SDK serwera aplikacji mobilnych] która oferuje wiele funkcji środowiska uruchomieniowego usługi Mobile Services. Najpierw należy usunąć wszystkie odwołania do pakietów usług Mobile Services. W Menedżerze pakietów NuGet, wyszukaj `WindowsAzure.MobileServices.Backend`. Większość aplikacji zostanie wyświetlony kilka pakietów, w tym `WindowsAzure.MobileServices.Backend.Tables` i `WindowsAzure.MobileServices.Backend.Entity`. W takim przypadku rozpoczynać najniższy pakietu w drzewie zależności, takich jak `Entity`i usuń go. Po wyświetleniu monitu, nie należy usuwać wszystkie pakiety zależne. Powtórz ten proces, aż zostaną usunięte `WindowsAzure.MobileServices.Backend` samej siebie.

Na tym etapie należy projektu, który nie odwołuje się do zestawów SDK usługi Mobile.

Następnie dodać odwołania do zestawu SDK aplikacji mobilnych. Dla tego uaktualnienia większość deweloperów należy pobrać i zainstalować `Microsoft.Azure.Mobile.Server.Quickstart` pakietów, jak to będzie pobierać z całego zestawu wymagane.

Będzie kilka błędów wynikających z różnic między zestawy SDK, ale są łatwe do adresu i zostały omówione w dalszej części tej sekcji.

### <a name="base-configuration"></a>Konfiguracja podstawowa
Następnie w WebApiConfig.cs, można zastąpić:

        // Use this class to set configuration options for your mobile service
        ConfigOptions options = new ConfigOptions();

        // Use this class to set WebAPI configuration options
        HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

z 

        HttpConfiguration config = new HttpConfiguration();
        new MobileAppConfiguration()
            .UseDefaultConfiguration()
        .ApplyTo(config);

> [!NOTE]
> Jeśli chcesz dowiedzieć się więcej na temat nowego serwera .NET SDK oraz sposób Dodaj/Usuń funkcje z aplikacji, zapoznaj się z artykułem [jak używać serwera .NET SDK] tematu.
>
>

Jeśli aplikacja sprawia, że korzystanie z funkcji uwierzytelniania, należy również zarejestrować oprogramowania pośredniczącego OWIN. W takim przypadku należy przenieść powyżej kod konfiguracji do nowej klasy uruchamiania OWIN.

1. Dodaj pakiet NuGet `Microsoft.Owin.Host.SystemWeb` Jeśli nie jest już zawarty w projekcie.
2. W programie Visual Studio, kliknij prawym przyciskiem myszy projekt i wybierz **Dodaj** -> **nowy element**. Wybierz **Web** -> **ogólne** -> **klasy początkowej OWIN**.
3. Przenoszenie kodu powyżej MobileAppConfiguration z `WebApiConfig.Register()` do `Configuration()` metody nowej klasy uruchamiania.

Upewnij się, że `Configuration()` metoda kończy się na:

        app.UseWebApi(config)
        app.UseAppServiceAuthentication(config);

Istnieją dodatkowe zmiany związane z uwierzytelnianiem, które opisano w poniższej sekcji pełnego uwierzytelniania.

### <a name="working-with-data"></a>Praca z danymi
W usłudze Mobile Services nazwa aplikacji mobilnej są obsługiwane jako domyślną nazwę schematu w ustawieniach programu Entity Framework.

Aby upewnić się, że mają ten sam schemat odwołuje się jak przed, użyj następujących czynności można ustawić schematu w DbContext aplikacji:

        string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");

Upewnij się, że masz ustawiony po wykonaniu powyższych MS_MobileServiceName. Jeśli aplikacja dostosowany to wcześniej, można też podać inną nazwę schematu.

### <a name="system-properties"></a>Właściwości systemu
#### <a name="naming"></a>Nazewnictwo
Na serwerze usług Azure Mobile Services SDK właściwości systemu zawsze zawiera podwójne podkreślenia (`__`) prefiks dla właściwości:

* __createdAt
* __updatedAt
* __deleted
* __version

Klient usług Mobile Services SDK ma specjalną logikę do analizowania właściwości systemu w tym formacie.

W usłudze Azure Mobile Apps właściwości systemu nie mają specjalne format i ma następujące nazwy:

* CreatedAt
* updatedAt
* usunięte
* wersja

Zestawów SDK klienta Mobile Apps Użyj nowych nazw właściwości systemu, więc zmiany nie są wymagane do kodu klienta. Jednak jeśli bezpośrednio wykonywania wywołań REST z usługą następnie należy zmienić zapytań odpowiednio.

#### <a name="local-store"></a>Lokalny magazyn
Zmiany nazwy właściwości systemu oznacza dla usług Mobile Services lokalnej bazy danych synchronizacji w trybie offline nie jest zgodny z usługą Mobile Apps. Jeśli to możliwe należy unikać uaktualniania klienta, które zostały wysłane do serwera aplikacji usługi Mobile Services do aplikacji mobilnej dopiero po oczekujące zmiany. Następnie uaktualnionego aplikacji należy używać nową nazwę pliku bazy danych.

Aplikacja kliencka jest uaktualnienia z usługi Mobile Services do aplikacji mobilnej podczas, gdy istnieją oczekujące zmiany w trybie offline w kolejce operacji, systemowej bazy danych trzeba zaktualizować do nowych nazw kolumn. W systemach iOS można to osiągnąć przy użyciu lekkie migracji można zmienić nazwy kolumny. W systemach Android i klient zarządzany .NET należy zapisać niestandardowe SQL można zmienić nazwy kolumny tabel obiektu danych.

W systemach iOS należy zmienić schemat danych podstawowych dla jednostek danych odpowiadające poniżej. Należy pamiętać, że właściwości `createdAt`, `updatedAt` i `version` nie będzie już `ms_` prefiksu:

| Atrybut | Typ | Uwaga |
| --- | --- | --- |
| id |Ciąg, oznaczona jako wymagana |klucz podstawowy w magazynie zdalnym |
| CreatedAt |Date |(opcjonalnie) mapowany do właściwości systemu createdAt |
| updatedAt |Date |(opcjonalnie) mapowany do właściwości systemu updatedAt |
| wersja |Ciąg |(opcjonalnie) używane do wykrywania konfliktów, mapy wersji |

#### <a name="querying-system-properties"></a>Badanie właściwości systemu
W usłudze Azure Mobile Services właściwości systemu nie są wysyłane domyślnie, ale tylko wtedy, gdy żądanie przy użyciu ciągu zapytania `__systemProperties`. Z kolei w systemie Azure Mobile Apps właściwości są **zawsze zaznaczone** ponieważ są one częścią modelu obiektów serwera zestawu SDK.

Ta zmiana wpływa głównie implementacji niestandardowych menedżerów domeny, takie jak rozszerzenia `MappedEntityDomainManager`. W usłudze Mobile Services, jeśli klient nie zażąda żadnych właściwości systemu jest możliwość użycia `MappedEntityDomainManager` który nie jest rzeczywiście mapowany wszystkich właściwości. Jednak w usłudze Azure Mobile Apps, te właściwości Niemapowane spowoduje błąd w zapytaniach GET.

Najprostszym sposobem, aby rozwiązać ten problem jest zmodyfikować DTOs Twojego dziedziczą z `ITableData` zamiast `EntityData`. Następnie należy dodać `[NotMapped]` atrybutów pola, które powinny być pominięte.

Na przykład poniższe definicje `TodoItem` bez właściwości systemu:

    using System.ComponentModel.DataAnnotations.Schema;

    public class TodoItem : ITableData
    {
        public string Text { get; set; }

        public bool Complete { get; set; }

        public string Id { get; set; }

        [NotMapped]
        public DateTimeOffset? CreatedAt { get; set; }

        [NotMapped]
        public DateTimeOffset? UpdatedAt { get; set; }

        [NotMapped]
        public bool Deleted { get; set; }

        [NotMapped]
        public byte[] Version { get; set; }
    }

Uwaga: Jeśli występują błędy `NotMapped`, Dodaj odwołanie do zestawu `System.ComponentModel.DataAnnotations`.

### <a name="cors"></a>CORS
Mobile Services zawarte niektórych obsługę mechanizmu CORS zawijania rozwiązania ASP.NET CORS. Ta warstwa zawijania została usunięta umożliwiają dewelopera większą kontrolę, można wykorzystać bezpośrednio [obsługi mechanizmu CORS ASP.NET](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

Główne obszary zainteresowania, jeśli przy użyciu mechanizmu CORS są, który `eTag` i `Location` może nagłówków w kolejności dla zestawów SDK klienta do poprawnego działania.

### <a name="push-notifications"></a>Powiadomienia wypychane
Do wypychania głównego elementu, który może się okazać Brak z zestawu SDK serwera jest klasą PushRegistrationHandler. Nieco inaczej obsługi rejestracji w aplikacji mobilnej, a tagless rejestracji są domyślnie włączone. Zarządzanie tagów można osiągnąć za pomocą niestandardowych interfejsów API. Zobacz [rejestrowanie tagów](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) instrukcje, aby uzyskać więcej informacji.

### <a name="scheduled-jobs"></a>Zaplanowane zadania
Zaplanowane zadania nie są wbudowane w Mobile Apps, więc wszystkie istniejące zadania, które ma w zapleczu swojej .NET należy uaktualnić pojedynczo. Jedną z opcji jest utworzenie zaplanowanego [zadanie sieci Web] w witrynie kodu aplikacji mobilnej. Można także skonfigurować kontroler, który zawiera kod zadania i skonfigurować [Harmonogram systemu Azure] trafienie tego punktu końcowego oczekiwanego zgodnie z harmonogramem.

### <a name="miscellaneous-changes"></a>Inne zmiany
Teraz musi mieć wszystkie ApiControllers, które będą używane przez klienta mobilnego `[MobileAppController]` atrybutu. To nie jest już włączone domyślnie ApiControllers, aby przejść dotknięta przenośnych elementy formatujące.

`ApiServices` Obiekt nie jest już częścią zestawu SDK. Aby uzyskać dostęp do ustawień aplikacji mobilnej, można użyć następujących czynności:

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

Podobnie rejestrowanie teraz odbywa się przy użyciu standardowych zapisywanie śledzenia ASP.NET:

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

## <a name="authentication"></a>Zagadnienia dotyczące uwierzytelniania
Składniki uwierzytelniania usług Mobile zostały przeniesione do funkcji uwierzytelniania/autoryzacji dla aplikacji usługi. Informacje na temat włączenie tej witryny, odczytując [Dodawanie uwierzytelniania do aplikacji mobilnej](app-service-mobile-ios-get-started-users.md) tematu.

Dla niektórych dostawców, takich jak usługi AAD i Facebook, Google można wykorzystać istniejące rejestracji z poziomu aplikacji kopii. Należy po prostu przejdź do portalu dostawcy tożsamości, a następnie dodaj nowy adres URL przekierowania do rejestracji. Następnie można skonfigurować uwierzytelniania/autoryzacji dla aplikacji usługi z Identyfikatorem klienta i klucz tajny.

### <a name="controller-action-authorization"></a>Autoryzacji akcji kontrolera
Wszystkie wystąpienia `[AuthorizeLevel(AuthorizationLevel.User)]` teraz można zmienić atrybutu przy użyciu standardowego środowiska ASP.NET `[Authorize]` atrybutu. Ponadto kontrolery są teraz anonimowe domyślnie, tak jak inne aplikacje ASP.NET.
Jeśli były używane inne opcje AuthorizeLevel, takie jak administrator lub aplikacji, należy pamiętać, że są one usunięte. Zamiast tego można skonfigurować AuthorizationFilters dla wspólne klucze tajne lub skonfigurować nazwy głównej usługi AAD umożliwia bezpieczne wywołania do usługi.

### <a name="getting-additional-user-information"></a>Otrzymanie informacji dodatkowych użytkowników
Możesz uzyskać dodatkowe informacje dotyczące użytkownika, łącznie z tokenów dostępu za pośrednictwem `GetAppServiceIdentityAsync()` metody:

        FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();

Ponadto jeśli aplikacja przejmuje zależności identyfikatory, takie jak przechowywania ich w bazie danych, jest należy pamiętać, że identyfikatory użytkowników między usług Mobile Services i aplikacji usługi Mobile Apps są różne. Identyfikator użytkownika usługi mobilnej, może nadal otrzymywać jednak. Wszystkie podklasy ProviderCredentials mieć właściwość identyfikatora użytkownika. Dlatego kontynuowanie z przykładu przed:

        string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

Jeśli aplikacja podejmij wszelkie zależności nazwy użytkownika, należy wykorzystać tę samą rejestrację przy użyciu dostawcy tożsamości, jeśli to możliwe. Identyfikatory użytkowników zazwyczaj ograniczone do rejestracji aplikacji, który był używany, więc wprowadzenie nowej rejestracji można tworzyć problemy z pasującymi użytkowników do swoich danych.

### <a name="custom-authentication"></a>Niestandardowe uwierzytelnianie
Jeśli aplikacja używa uwierzytelniania niestandardowego rozwiązania, należy się upewnić, że uaktualniony lokacji ma dostęp do systemu. Postępuj zgodnie z instrukcjami nowe niestandardowe uwierzytelnianie w [Omówienie zestawu SDK serwera .NET] integrację rozwiązania. Należy pamiętać, że składniki uwierzytelniania niestandardowego są nadal w wersji zapoznawczej.

## <a name="updating-clients"></a>Aktualizowanie klientów
Po utworzeniu operacyjną zaplecza aplikacji mobilnej, można pracować na nowej wersji aplikacji klienta, która go wykorzystuje. Mobile Apps obejmuje również nową wersję klienta zestawy SDK i podobne do uaktualnienia serwera powyżej, należy usunąć wszystkie odwołania do zestawów SDK usługi Mobile przed zainstalowaniem wersji aplikacji mobilnej.

Jest jedną z głównych zmian między wersjami, że konstruktorów nie wymagają już klucz aplikacji. Teraz po prostu są przekazywane w adresie URL aplikacji mobilnej. Na przykład na klientów platformy .NET `MobileServiceClient` Konstruktor jest teraz:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Możesz przeczytać o instalowaniu nowych zestawy SDK i przy użyciu nowej struktury za pośrednictwem łącza poniżej:

* [System iOS w wersji 3.0.0 lub nowszej](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (system Windows/Xamarin) w wersji 2.0.0 lub nowszej](app-service-mobile-dotnet-how-to-use-client-library.md)

Czy aplikacja sprawia, że użycie powiadomień wypychanych, zanotuj instrukcje określonej rejestracji dla każdej platformy, jak nie zostały niektóre zmiany również.

Jeśli masz nową wersję klienta gotowa, wypróbuj go na uaktualnionym serwerze projektu. Po weryfikacji, czy działa, można zwolnić nowej wersji aplikacji dla klientów. Po pewnym czasie po klientów miało możliwość otrzymywać te aktualizacje, można usunąć wersji usług Mobile Services w aplikacji. W tym momencie całkowicie zostały uaktualnione do aplikacji mobilnej usługi dla aplikacji za pomocą najnowszej serwera Mobile Apps SDK.

<!-- URLs. -->

[portalu Azure]: https://portal.azure.com/
[klasycznego portalu Azure]: https://manage.windowsazure.com/
[co to są Mobile Apps?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[zestawu SDK serwera aplikacji mobilnych]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Harmonogram systemu Azure]: /en-us/documentation/services/scheduler/
[zadanie sieci Web]: https://github.com/Azure/azure-webjobs-sdk/wiki
[jak używać serwera .NET SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[cennik usługi aplikacji]: https://azure.microsoft.com/pricing/details/app-service/
[Omówienie zestawu SDK serwera .NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
