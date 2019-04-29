---
title: Uaktualnianie z usług Mobile Services w usłudze Azure App Service
description: Dowiedz się, jak łatwo przeprowadzić uaktualnienie aplikacji usługi Mobile Services do aplikacji mobilnej usługi App Service
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
ms.openlocfilehash: f5ffc795e6469971d1eaf335d6683f94d05f0807
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122442"
---
# <a name="upgrade-your-existing-net-azure-mobile-service-to-app-service"></a>Uaktualnianie istniejącej usługi mobilnej Azure .NET w usłudze App Service
Mobile App Service jest nowy sposób na tworzenie aplikacji mobilnych przy użyciu Microsoft Azure. Aby dowiedzieć się więcej, zobacz [co to jest usługa Mobile Apps?].

W tym temacie opisano sposób uaktualniania istniejącej aplikacji .NET wewnętrznej bazy danych z usług Azure Mobile Services do nowego App Service Mobile Apps. Podczas wykonywania tego uaktualnienia istniejącej aplikacji usługi Mobile Services będą działać.   Jeśli musisz uaktualnić aplikację Node.js do wewnętrznej bazy danych, zapoznaj się [uaktualnianie Twojej usługi Mobile Services dla środowiska Node.js](app-service-mobile-node-backend-upgrading-from-mobile-services.md).

Po uaktualnieniu zaplecza aplikacji mobilnych w usłudze Azure App Service, ma dostęp do wszystkich funkcji usługi App Service i są rozliczane zgodnie z opisem w [Cennik usługi aplikacji], nie cennika usług Mobile Services.

## <a name="migrate-vs-upgrade"></a>Migrowanie i uaktualnianie
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> Zalecane jest, [przeprowadzić migrację](app-service-mobile-migrating-from-mobile-services.md) przed przechodzi uaktualnienie. W ten sposób można umieścić obie wersje aplikacji na tym samym planie usługi App Service i pociągnąć za sobą żadnych dodatkowych kosztów.
>
>

### <a name="improvements-in-mobile-apps-net-server-sdk"></a>Ulepszenia w zestawie SDK serwera platformy .NET usług Mobile Apps
Uaktualnianie do nowej [zestawu SDK usługi Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) zapewnia następujące korzyści:

* Większą elastyczność na zależności NuGet. Środowisko hostingu nie zawiera już własnej wersji pakietów NuGet, aby można było używać alternatywnego zgodne wersje. Jednak w przypadku nowych poprawek krytyczne lub aktualizacje zabezpieczeń do zestawu SDK usługi Mobile serwera lub zależności, należy ręcznie zaktualizować usługę.
* Większą elastyczność w zestawie SDK mobilnych. Jawnie można kontrolować, które funkcje i trasy zostały skonfigurowane, takie jak uwierzytelnianie, tabeli interfejsów API i punkt końcowy rejestracji wypychania. Aby dowiedzieć się więcej, zobacz [jak używać zestawu SDK serwera .NET dla usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).
* Wsparcie dla innych typów projektów platformy ASP.NET i trasy. Teraz można już hostować kontrolerów MVC i interfejs API sieci Web, w tym samym projekcie jako projekt zaplecza aplikacji mobilnych.
* Obsługa nowych funkcji uwierzytelniania usługi App Service, które pozwalają na korzystanie z typowych konfiguracji uwierzytelniania w aplikacji sieci web i aplikacji mobilnych.

## <a name="overview"></a>Omówienie uaktualnienia
W wielu przypadkach uaktualnienie będzie proste i polega na przełączenie do nowego serwera funkcji Mobile Apps SDK i ponownego publikowania kodu na nowe wystąpienie aplikacji mobilnej. Istnieją jednak sytuacje, w których będzie wymagać dodatkowej konfiguracji, takich jak scenariusze zaawansowane uwierzytelnianie i Praca z zaplanowanych zadań. Każdy z nich zostało opisane w kolejnych sekcjach.

> [!TIP]
> Zaleca się, że należy przeczytać i sprawdzić pozostałej części tego tematu w całości, przed rozpoczęciem uaktualniania. Zanotuj wszystkie funkcje, użyj wymienione poniżej.
>
>

Są zestawy SDK klienta usługi Mobile Services **nie** zgodny z nowym serwerem funkcji Mobile Apps SDK. W celu zapewnienia ciągłości usługi dla aplikacji, nie należy opublikować zmiany do lokacji, obsługujący opublikowanych klientów. Zamiast tego należy utworzyć nowa aplikacja mobilna, która służy jako duplikat. Ta aplikacja można umieścić w ten sam plan usługi App Service, aby uniknąć ponoszenia dodatkowych kosztów finansowych.

Użytkownik będzie miał dwie wersje aplikacji: aplikacji, która pozostaje taka sama i służy opublikowanych w dzikie i inne, które następnie można uaktualnić i element docelowy z nowej wersji klienta. Możesz przenieść i przetestować kod własnym tempie, ale należy się upewnić, że wszelkie poprawki wprowadzone stosowane do obu. Gdy uznasz, że odpowiednią liczbę klientów, które aplikacje w środowisku naturalnym zostały zaktualizowane do najnowszej wersji, możesz usunąć oryginalny zmigrowanej aplikacji w razie potrzeby.

Pełne konspekt procesu uaktualniania jest następująca:

1. Tworzenie nowej aplikacji mobilnej
2. Aktualizowanie projektu, aby użyć nowych zestawów SDK serwera
3. Wydać jej nową wersję aplikacji klienckiej
4. (Opcjonalnie) Usuń zmigrowane oryginalnego wystąpienia

## <a name="mobile-app-version"></a>Tworzenie drugiego wystąpienia aplikacji
Pierwszym krokiem podczas uaktualniania jest Utwórz zasób aplikacji mobilnej, który będzie hostował nową wersję aplikacji. Jeśli już przeprowadzono istniejącej usługi mobilnej, można utworzyć tej wersji na tym samym planie hostingu. Otwórz [Azure Portal] i przejdź do migrowanych aplikacji. Zanotuj planu usługi App Service jest uruchomiona na.

Następnie Utwórz drugie wystąpienie aplikacji, postępując zgodnie z [instrukcje dotyczące tworzenia zaplecza .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app). Po wyświetleniu monitu można wybrać Plan usługi App Service lub "planu hostingu" Wybierz plan migrowanych aplikacji.

Prawdopodobnie można korzystać z tej samej bazy danych i Centrum powiadomień, tak jak w usłudze Mobile Services. Możesz skopiować te wartości, otwierając [Azure Portal] a następnie przechodząc do oryginalnej aplikacji, kliknij przycisk **ustawienia** > **ustawienia aplikacji**. W obszarze **parametry połączenia**, kopia `MS_NotificationHubConnectionString` i `MS_TableConnectionString`. Przejdź do nowej witryny uaktualniania, a następnie wklej je w programie, zastępując wszystkie istniejące wartości. Powtórz ten proces dla innych ustawień aplikacji potrzebom aplikacji.

Utwórz kopię projekt platformy ASP.NET dla aplikacji i opublikować ją do nowej witryny. Przy użyciu kopii aplikacji klienckiej aktualizowane przy użyciu nowego adresu URL, zweryfikuj, że wszystko działa zgodnie z oczekiwaniami.

## <a name="updating-the-server-project"></a>Aktualizowanie projektu serwera
Mobile Apps oferuje nową [Zestaw SDK serwera aplikacji mobilnej] który zapewnia dostęp do większości funkcji środowiska uruchomieniowego usług Mobile Services. Najpierw należy usunąć wszystkie odwołania do pakietów usług Mobile Services. W Menedżerze pakietów NuGet, wyszukaj `WindowsAzure.MobileServices.Backend`. Większość aplikacji zostanie wyświetlony w tym miejscu kilka pakietów, łącznie z `WindowsAzure.MobileServices.Backend.Tables` i `WindowsAzure.MobileServices.Backend.Entity`. W takim przypadku rozpoczynać najniższy pakietu w drzewie zależności, takich jak `Entity`i usuń go. Po wyświetleniu monitu, nie usuwaj wszystkich pakietów zależnych. Powtórz ten proces, dopóki nie usuniesz `WindowsAzure.MobileServices.Backend` sam.

W tym momencie masz projekt, który nie jest już odwołuje się do zestawów SDK usług mobilnych.

Następnie należy dodać odwołania do zestawu SDK aplikacji mobilnych. Dla tego uaktualnienia będzie większość deweloperów do pobrania i zainstalowania `Microsoft.Azure.Mobile.Server.Quickstart` pakietu, ponieważ zostanie ściągnięty cały wymagany zestaw.

Będzie kilka błędów kompilatora wynikające z różnic między zestawy SDK, ale są to można z łatwością rozwiązać które zostały omówione w dalszej części tej sekcji.

### <a name="base-configuration"></a>Konfiguracja podstawowa
Następnie w WebApiConfig.cs, można zastąpić:

```csharp
// Use this class to set configuration options for your mobile service
ConfigOptions options = new ConfigOptions();

// Use this class to set WebAPI configuration options
HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));
```

z

```csharp
HttpConfiguration config = new HttpConfiguration();
new MobileAppConfiguration()
    .UseDefaultConfiguration()
.ApplyTo(config);

```

> [!NOTE]
> Jeśli chcesz dowiedzieć się więcej na temat nowego zestawu SDK serwera .NET i Dodaj/Usuń funkcje z aplikacji, zapoznaj się z artykułem [jak używać zestawu SDK serwera .NET] tematu.
>
>

Jeśli aplikacja sprawia, że korzystać z funkcji uwierzytelniania, należy również zarejestrować oprogramowania pośredniczącego OWIN. W takim przypadku należy przenieść powyższy kod konfiguracji do nowego Klasa początkowa OWIN.

1. Dodaj pakiet NuGet `Microsoft.Owin.Host.SystemWeb` Jeśli nie jest już uwzględniony w projekcie.
2. W programie Visual Studio, kliknij prawym przyciskiem myszy projekt i wybierz **Dodaj** -> **nowy element**. Wybierz **Web** -> **ogólne** -> **Klasa początkowa OWIN**.
3. Przenoszenie kodu powyżej MobileAppConfiguration z `WebApiConfig.Register()` do `Configuration()` metoda nowej klasie uruchamiania.

Upewnij się, że `Configuration()` metoda kończy się na:

```csharp
app.UseWebApi(config)
app.UseAppServiceAuthentication(config);
```

Istnieją dodatkowe zmiany związane z uwierzytelnianiem, które opisano w poniższej sekcji pełnego uwierzytelniania.

### <a name="working-with-data"></a>Praca z danymi
W usługach Mobile Services nazwa aplikacji mobilnej są obsługiwane jako domyślna nazwa schematu w ustawieniach platformy Entity Framework.

Aby upewnić się, że mają ten sam schemat, który odwołuje się zgodnie z wcześniej, użyj następujących można ustawić schematu w DbContext aplikacji:

```csharp
string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");
```

Upewnij się, że masz MS_MobileServiceName ustawić po wykonaniu powyższych. Możesz także podać inną nazwę schematu, jeśli aplikacja dostosowane to wcześniej.

### <a name="system-properties"></a>Właściwości systemu
#### <a name="naming"></a>Nazewnictwo
Na serwerze usług Azure Mobile Services SDK właściwości systemu zawsze zawierać podwójnym podkreśleniem (`__`) prefiks dla właściwości:

* __createdAt
* __updatedAt
* __deleted
* __version

Usługi Mobile Services zestaw SDK zawiera logikę specjalną do analizowania właściwości systemu, w tym formacie.

W usłudze Azure Mobile Apps właściwości systemu nie jest już mieć specjalne format i mają następujące nazwy:

* createdAt
* updatedAt
* usunięte
* version

Zestawów SDK klienta funkcji Mobile Apps Użyj nowych nazw właściwości systemu, więc żadne zmiany nie są wymagane dla kodu klienta. Jednak jeśli wykonujesz bezpośrednio wywołania REST z usługą następnie należy zmieniać zapytań odpowiednio.

#### <a name="local-store"></a>Magazyn lokalny
Zmiany nazwy właściwości systemu oznacza dla usług Mobile Services lokalnej bazy danych synchronizacji w trybie offline nie jest zgodny z funkcją Mobile Apps. Jeśli to możliwe należy unikać uaktualniania klienta, które aplikacje z usług Mobile Services do funkcji Mobile Apps, aż po oczekujące zmiany zostały wysłane do serwera. Następnie uaktualnionego aplikacja powinna używać nową nazwę pliku bazy danych.

Jeśli aplikacja kliencka zostanie uaktualniony z usług Mobile Services do funkcji Mobile Apps, gdy istnieją oczekujące zmiany w trybie offline w kolejce operacji, systemowej bazy danych, musi zostać zaktualizowany do korzystania z nowych nazw kolumn. W systemach iOS można to osiągnąć za pomocą uproszczonego migracji można zmienić nazwy kolumny. W systemach Android i zarządzanego klienta platformy .NET należy wpisać niestandardowe SQL można zmienić nazwy kolumny dla tabel obiektu danych.

W systemach iOS należy zmienić schematu danych podstawowych dla Twojego jednostek danych dopasować następujące czynności. Należy pamiętać, że właściwości `createdAt`, `updatedAt` i `version` nie będzie już `ms_` prefiksu:

| Atrybut | Type | Uwaga |
| --- | --- | --- |
| id |Ciąg, oznaczone jako wymagane |klucz podstawowy w magazynie zdalnym |
| createdAt |Date |mapy właściwości systemu createdAt i (opcjonalnie) |
| updatedAt |Date |mapy właściwości systemu updatedAt i (opcjonalnie) |
| version |String |(opcjonalnie) służącą do wykrywania konfliktów, mapy do wersji |

#### <a name="querying-system-properties"></a>Tworzenie zapytań właściwości systemu
W usługach Azure Mobile Services właściwości systemu nie są wysyłane domyślnie, ale tylko wtedy, gdy żądanie przy użyciu ciągu zapytania `__systemProperties`. Z kolei w systemie Azure Mobile Apps właściwości są **zawsze zaznaczony** ponieważ są one częścią modelu obiektów serwera zestawu SDK.

Ta zmiana dotyczy głównie niestandardowych implementacji menedżerów domeny, np. rozszerzenia `MappedEntityDomainManager`. W usługach Mobile Services, gdy klient zażąda nigdy nie wszystkie właściwości systemu jest możliwość użycia `MappedEntityDomainManager` , nie jest faktycznie mapowany wszystkich właściwości. Jednak w usłudze Azure Mobile Apps, te właściwości niezamapowane spowoduje, że wystąpił błąd podczas kwerendy GET.

Najprostszym sposobem, aby rozwiązać ten problem jest zmodyfikowanie swoje dto, tak aby dziedziczą z `ITableData` zamiast `EntityData`. Następnie należy dodać `[NotMapped]` atrybutu do pola, które powinny zostać pominięty.

Na przykład definiuje następujące `TodoItem` bez właściwości systemu:

```csharp
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
```

Uwaga: Jeśli występują błędy `NotMapped`, Dodaj odwołanie do zestawu `System.ComponentModel.DataAnnotations`.

### <a name="cors"></a>CORS
Usługi Mobile Services uwzględnione niektóre obsługę mechanizmu CORS opakowując rozwiązania ASP.NET CORS. Ta warstwa zawijania została usunięta zapewnienie Deweloper uzyskać większą kontrolę, dzięki czemu można korzystać bezpośrednio [obsługi mechanizmu CORS ASP.NET](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

Główne obszary zainteresowania, jeśli przy użyciu mechanizmu CORS są, który `eTag` i `Location` nagłówki muszą być dozwolone w kolejności dla zestawów SDK klienta, aby zapewnić prawidłowe działanie.

### <a name="push-notifications"></a>Powiadomienia wypychane
Do wypychania element główny, który może się okazać Brak z zestawu SDK serwera jest klasa PushRegistrationHandler. Rejestracje są obsługiwane inaczej w aplikacjach mobilnych i tagless rejestracje są domyślnie włączone. Zarządzanie tagów można osiągnąć przy użyciu niestandardowych interfejsów API. Zobacz [rejestrowanie tagów](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) instrukcje, aby uzyskać więcej informacji.

### <a name="scheduled-jobs"></a>Zaplanowane zadania
Zaplanowane zadania nie są wbudowane w funkcji Mobile Apps, dzięki czemu wszystkie istniejące zadania, które masz w swoim zapleczem platformy .NET, musisz uaktualnić pojedynczo. Jednym rozwiązaniem jest utworzenie zaplanowanego [zadanie Web Job] w witrynie kodu aplikacji mobilnej. Możesz również skonfigurować kontroler, który zawiera kod zadania i skonfigurować [Azure Scheduler] aby trafić punkt końcowy zgodnie z harmonogramem oczekiwane.

### <a name="miscellaneous-changes"></a>Różne zmiany
Teraz jest posiadanie wszystkich ApiControllers, które będą używane przez klienta mobilnego `[MobileAppController]` atrybutu. To nie jest już domyślnie ApiControllers, aby przejść niezależny od mobilnej elementy formatujące.

`ApiServices` Obiekt nie jest już częścią zestawu SDK. Aby uzyskać dostęp ustawienia aplikacji mobilnej, można użyć następujących czynności:

```csharp
MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
```

Podobnie rejestrowanie teraz odbywa się przy użyciu standardowych zapisu śledzenia ASP.NET:

```csharp
ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
traceWriter.Info("Hello, World");  
```

## <a name="authentication"></a>Zagadnienia dotyczące uwierzytelniania
Składniki uwierzytelniania usług Mobile Services zostały przeniesione do funkcji uwierzytelniania/autoryzacji dla aplikacji usługi. Możesz dowiedzieć się o włączenie tej witryny, czytając [Dodawanie uwierzytelniania do aplikacji mobilnej](app-service-mobile-ios-get-started-users.md) tematu.

W przypadku niektórych dostawców, takich jak usługi AAD i Facebook, Google można wykorzystać rejestracja istniejącej aplikacji kopiowania. Po prostu musisz przejść do portalu dostawcy tożsamości i Dodaj nowy adres URL przekierowania do rejestracji. Następnie można skonfigurować uwierzytelniania/autoryzacji dla aplikacji usługi z Identyfikatorem klienta i klucz tajny.

### <a name="controller-action-authorization"></a>Autoryzacja akcji kontrolera
Wszystkie wystąpienia `[AuthorizeLevel(AuthorizationLevel.User)]` teraz można zmienić atrybutu przy użyciu standardowego środowiska ASP.NET `[Authorize]` atrybutu. Ponadto kontrolery są teraz anonimowe domyślnie, tak jak inne aplikacje platformy ASP.NET.
Jeśli używano inne opcje AuthorizeLevel, takie jak administrator lub aplikacji, należy pamiętać, że są one usunięte. Możesz zamiast tego ustawienia dla wspólne klucze tajne AuthorizationFilters lub skonfigurować nazwy głównej usługi AAD umożliwiające bezpieczne wywołania do usługi.

### <a name="getting-additional-user-information"></a>Uzyskiwanie dodatkowych informacji dotyczących użytkowników
Można uzyskać dodatkowe informacje dotyczące użytkownika, łącznie z tokenów dostępu za pośrednictwem `GetAppServiceIdentityAsync()` metody:

```csharp
FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();
```

Ponadto jeśli aplikacja przejmuje zależności użytkownika identyfikatorów, takich jak przechowywanie ich w bazie danych, jest należy pamiętać, że identyfikatory użytkowników między usługami Mobile Services a App Service Mobile Apps są różne. Identyfikator użytkownika usługi mobilnych, nadal można jednak uzyskać. Wszystkie podklasy ProviderCredentials ma właściwości identyfikatora użytkownika. Więc kontynuowanie z przykładu przed:

```csharp
string mobileServicesUserId = creds.Provider + ":" + creds.UserId;
```

Jeśli aplikacja wszelkie zależności na nazwy użytkownika, należy wykorzystać tę samą rejestrację za pomocą dostawcy tożsamości, jeśli jest to możliwe. Identyfikatory użytkowników zwykle są ograniczone do rejestracji aplikacji, który został użyty, dzięki czemu wprowadzenie nowej rejestracji można tworzyć problemy z pasujących użytkowników do ich danych.

### <a name="custom-authentication"></a>Uwierzytelnianie niestandardowe
Jeśli aplikacja używa uwierzytelniania niestandardowego rozwiązania, należy upewnić się, że uaktualniony lokacji ma dostęp do systemu. Postępuj zgodnie z instrukcjami nowego niestandardowego uwierzytelniania [Omówienie zestawu SDK serwera .NET] integrować rozwiązania. Należy pamiętać, że składniki uwierzytelniania niestandardowego są nadal w wersji zapoznawczej.

## <a name="updating-clients"></a>Aktualizowanie klientów
Po utworzeniu operacyjnej zaplecza aplikacji mobilnej, można pracować na nową wersję aplikacji klienta, który ich używa. Mobile Apps również zawiera nową wersję zestawów SDK klienta, i podobnie jak uaktualnienie serwera powyżej, musisz usunąć wszystkie odwołania do zestawów SDK usługi Mobile przed zainstalowaniem wersji funkcji Mobile Apps.

Jedną z najważniejszych zmian między wersjami jest, że konstruktory nie wymagają już klucza aplikacji. Możesz teraz po prostu przekazać adres URL aplikacji mobilnej. Na przykład na klientach .NET `MobileServiceClient` Konstruktor jest obecnie:

```csharp
public static MobileServiceClient MobileService = new MobileServiceClient(
    "https://contoso.azurewebsites.net", // URL of the Mobile App
);
```

Możesz przeczytać o zainstalowanie nowych zestawów SDK i korzystanie z nowej struktury za pomocą poniższych linków:

* [System iOS w wersji 3.0.0 lub nowszej](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (Windows/Xamarin) w wersji 2.0.0 lub nowszej](app-service-mobile-dotnet-how-to-use-client-library.md)

Jeśli aplikacja podejmuje użycie powiadomień wypychanych, zanotuj instrukcje rejestracji określonych dla każdej platformy, ponieważ było tam zmiany także.

Jeśli masz nową wersję klienta gotowy, wypróbuj działanie rozwiązania na uaktualnionym serwerze projektu. Po upewnieniu się, że działa, można zwolnić nową wersję aplikacji dla klientów. Po pewnym czasie po klienci mieli Państwo otrzymywać te aktualizacje, można usunąć usługi Mobile Services wersję aplikacji. W tym momencie możesz całkowicie uaktualniono do aplikacji usługi Mobile App przy użyciu najnowszy zestaw SDK serwera funkcji Mobile Apps.

<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[Co to jest usługa Mobile Apps?]: app-service-mobile-value-prop.md
[Zestaw SDK serwera aplikacji mobilnej]: https://www.nuget.org/packages/microsoft.azure.mobile.server
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /azure/scheduler/
[Zadanie Web Job]: https://github.com/Azure/azure-webjobs-sdk/wiki
[Jak używać zestawu SDK serwera .NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Cennik usługi aplikacji]: https://azure.microsoft.com/pricing/details/app-service/
[Omówienie zestawu SDK serwera .NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
