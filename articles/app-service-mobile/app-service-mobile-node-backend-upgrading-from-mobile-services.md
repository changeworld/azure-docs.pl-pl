---
title: Uaktualnianie z usług Mobile Services w usłudze Azure App Service — Node.js
description: Dowiedz się, jak łatwo przeprowadzić uaktualnienie aplikacji usługi Mobile Services do aplikacji mobilnej usługi App Service
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: yochayk
editor: ''
ms.assetid: c58f6df0-5aad-40a3-bddc-319c378218e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: e54ed6c526182cea57e2d40f356ad9236510d82c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128070"
---
# <a name="upgrade-your-existing-nodejs-azure-mobile-service-to-app-service"></a>Uaktualnianie istniejącej usługi mobilnej Azure Node.js w usłudze App Service
Mobile App Service jest nowy sposób na tworzenie aplikacji mobilnych przy użyciu Microsoft Azure. Aby dowiedzieć się więcej, zobacz [co to jest usługa Mobile Apps?].

W tym artykule opisano sposób uaktualniania istniejącej aplikacji zaplecza Node.js z usług Azure Mobile Services do nowego App Service Mobile Apps. Podczas wykonywania tego uaktualnienia istniejącej aplikacji usługi Mobile Services będą działać.  Jeśli musisz uaktualnić aplikację Node.js do wewnętrznej bazy danych, zapoznaj się [uaktualnianie usługi Mobile Services .NET](app-service-mobile-net-upgrading-from-mobile-services.md).

Po uaktualnieniu zaplecza aplikacji mobilnych w usłudze Azure App Service, ma dostęp do wszystkich funkcji usługi App Service i są rozliczane zgodnie z opisem w [Cennik usługi aplikacji], nie cennika usług Mobile Services.

## <a name="migrate-vs-upgrade"></a>Migrowanie i uaktualnianie
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> Zalecane jest, [przeprowadzić migrację](app-service-mobile-migrating-from-mobile-services.md) przed przechodzi uaktualnienie. W ten sposób można umieścić obie wersje aplikacji na tym samym planie usługi App Service i pociągnąć za sobą żadnych dodatkowych kosztów.
>
>

### <a name="improvements-in-mobile-apps-nodejs-server-sdk"></a>Ulepszenia Mobile Apps Node.js server SDK
Uaktualnianie do nowej [zestawu SDK usługi Mobile Apps](https://www.npmjs.com/package/azure-mobile-apps) oferuje wiele udoskonaleń, w tym:

* Na podstawie [Express framework](https://expressjs.com/en/index.html), nowy węzeł zestawu SDK jest lekkie i jest przeznaczona na bieżąco do nowych wersji węzła, ponieważ zaczynają. Można dostosować zachowanie aplikacji za pomocą oprogramowania pośredniczącego Express.
* Znaczne ulepszenia wydajności w porównaniu z zestaw SDK usług Mobile Services.
* Teraz można już hostować witrynę sieci Web wraz z usługi mobilnej zaplecza; Podobnie jest łatwe do dodania do istniejącej aplikacji express.v4, zestaw SDK usług Azure Mobile.
* Skompilowany dla wielu platform i lokalny rozwój, zestaw SDK usługi Mobile Apps można opracowane i uruchom lokalnie na platformach Windows, Linux i OS x. Teraz jest łatwy w użyciu wspólnego węzła rozwoju technik, takich jak uruchamianie [Mocha](https://mochajs.org/) testów przed ich wdrożeniem.

## <a name="overview"></a>Omówienie uaktualnienia
Ułatwiające do uaktualniania zaplecza środowiska Node.js usługi Azure App Service udostępnia pakiet zgodności.  Po uaktualnieniu trzeba będzie nowej lokacji, które można wdrożyć do nowej lokacji usługi App Service.

Są zestawy SDK klienta usługi Mobile Services **nie** zgodny z nowym serwerem funkcji Mobile Apps SDK. W celu zapewnienia ciągłości usługi dla aplikacji, nie należy opublikować zmiany do lokacji, obsługujący opublikowanych klientów. Zamiast tego należy utworzyć nowa aplikacja mobilna, która służy jako duplikat. Ta aplikacja można umieścić w ten sam plan usługi App Service, aby uniknąć ponoszenia dodatkowych kosztów finansowych.

Użytkownik będzie miał dwie wersje aplikacji: aplikacji, który pozostaje taka sama i służy opublikowanych w dzikie i inne, które następnie można uaktualnić i element docelowy z nowej wersji klienta. Możesz przenieść i przetestować kod własnym tempie, ale należy się upewnić, że wszelkie poprawki wprowadzone stosowane do obu. Gdy uznasz, że odpowiednią liczbę klientów, które aplikacje w środowisku naturalnym zostały zaktualizowane do najnowszej wersji, możesz usunąć oryginalny zmigrowanej aplikacji w razie potrzeby. Go nie wiązać ponoszenia dodatkowych kosztów pieniężnych hostowanych w ramach tego samego planu usługi App Service jako aplikacji mobilnej.

Pełne konspekt procesu uaktualniania jest następująca:

1. Pobierz istniejącej usługi mobilnej Azure (zmigrowane).
2. Skonwertowaniu projektu do aplikacji mobilnej platformy Azure przy użyciu pakietu zgodności.
3. Popraw wszelkie różnice (na przykład ustawienia uwierzytelniania).
4. Wdróż przekonwertowanego projektu usługi Azure Mobile App do nowej usługi App Service.
5. Zwolnij nową wersję aplikacji klienta, który używa nowej aplikacji mobilnej.
6. (Opcjonalnie) Usuwanie oryginalnej aplikacji zmigrowane usługi mobilnej.

Usuwanie może wystąpić, gdy cały ruch nie jest widoczny w oryginalnej zmigrowane usługi mobilnej.

## <a name="install-npm-package"></a> Instalowanie warunków wstępnych
[Node] należy zainstalować na komputerze lokalnym.  Należy także zainstalować pakiet zgodności.  Po zainstalowaniu węzła można uruchomić następujące polecenie, z nowego polecenia lub w wierszu polecenia programu PowerShell:

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a> Uzyskaj skryptów usług Azure Mobile Services
* Zaloguj się do [Azure Portal].
* Za pomocą **wszystkie zasoby** lub **App Services**, Odkryj usługi Mobile Services.
* W ramach lokacji, kliknij pozycję **narzędzia** -> **Kudu** -> **Przejdź** można otworzyć witryny Kudu.
* Kliknij pozycję **konsoli debugowania** -> **PowerShell** można otworzyć konsoli debugowania.
* Przejdź do `site/wwwroot/App_Data/config` , klikając na każdy katalog z kolei
* Kliknij ikonę pobierania obok `scripts` katalogu.

Spowoduje to pobranie skryptów w formacie ZIP.  Utwórz nowy katalog na komputerze lokalnym i rozpakowywanie `scripts.ZIP` pliku w katalogu.  Spowoduje to utworzenie `scripts` katalogu.

## <a name="scaffold-app"></a> Tworzenie szkieletu nowego zaplecza usługi Azure Mobile Apps
Uruchom następujące polecenie z katalogu zawierającego katalogu skrypty:

```scaffold-mobile-app scripts out```

Spowoduje to utworzenie szkieletu zaplecza usługi Azure Mobile Apps w `out` katalogu.  Chociaż nie jest to wymagane, to dobry pomysł, aby sprawdzić `out` katalogu do wybranego przez użytkownika repozytorium kodu źródłowego.

## <a name="deploy-ama-app"></a> Wdrażanie zaplecza usługi Azure Mobile Apps
Podczas wdrażania należy wykonać następujące czynności:

1. Tworzenie nowej aplikacji mobilnej w [Azure Portal].
2. Uruchom `createViews.sql` skryptu w podłączonej bazie danych.
3. Łączenie bazy danych, która jest połączona z usługą mobilną do nowej usługi App Service.
4. Łączenie wszystkich innych zasobów (np. usługi Notification Hubs) do nowej usługi App Service.
5. Wdróż wygenerowany kod do nowej witryny.

### <a name="create-a-new-mobile-app"></a>Tworzenie nowej aplikacji mobilnej
1. Zaloguj się w [Azure Portal].
2. Kliknij kolejno opcje **+NOWE** > **Sieci Web i mobilność** > **Aplikacja mobilna**, a następnie podaj nazwę zaplecza aplikacji mobilnej.
3. W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów lub utwórz nową (używając tej samej nazwy, co aplikacja).

    Możesz wybrać inny plan usługi App Service lub utworzyć nowy. Aby uzyskać więcej informacji na temat planów usług App Services oraz sposobu tworzenia nowego planu w innej warstwie cenowej oraz w preferowanej lokalizacji, zobacz temat [Szczegółowe omówienie planów usługi Azure App Service](../app-service/overview-hosting-plans.md).
4. W przypadku **planu usługi App Service** jest wybrany plan domyślny (w [warstwie standardowej](https://azure.microsoft.com/pricing/details/app-service/)). Możesz również wybrać inny plan lub [utworzyć nowy](../app-service/app-service-plan-manage.md#create-an-app-service-plan). Ustawienia planu usługi App Service określają [lokalizację, funkcje, koszt i zasoby obliczeniowe](https://azure.microsoft.com/pricing/details/app-service/) skojarzone z aplikacją.

    Po wybraniu planu kliknij przycisk **Utwórz**. Spowoduje to utworzenie zaplecza aplikacji mobilnej.

### <a name="run-createviewssql"></a>Uruchom CreateViews.SQL
Utworzony szkielet aplikacji zawiera plik o nazwie `createViews.sql`.  Ten skrypt musi zostać wykonany w docelowej bazie danych.  Parametry połączenia dla docelowej bazy danych można uzyskać z poziomu usługi mobilnej zmigrowanych z **ustawienia** strony w obszarze **parametry połączenia**.  Jest on nazwany `MS_TableConnectionString`.

Można uruchomić ten skrypt z w ramach programu SQL Server Management Studio lub Visual Studio.

### <a name="link-the-database-to-your-app-service"></a>Łączenie bazy danych do usługi App Service
Połącz istniejącą bazę danych do usługi App Service:

* W [Azure Portal], Otwórz swoją usługę App Service.
* Wybierz **wszystkie ustawienia** -> **połączeń danych**.
* Kliknij pozycję **+ Dodaj**.
* Z listy rozwijanej wybierz **bazy danych SQL**
* W obszarze **bazy danych SQL**, wybierz istniejącą bazę danych, a następnie kliknij pozycję **wybierz**.
* W obszarze **parametry połączenia**, wprowadź nazwę użytkownika i hasło dla bazy danych, a następnie kliknij pozycję **OK**.
* W **Dodawanie połączenia danych** kliknij na **OK**.

Nazwa użytkownika i hasło można znaleźć, wyświetlając ciąg połączenia dla docelowej bazy danych w usłudze mobilnej zmigrowane.

### <a name="set-up-authentication"></a>Konfigurowanie uwierzytelniania
Usługa Azure Mobile Apps pozwala na konfigurowanie usługi Azure Active Directory, Facebook, Google, Microsoft i uwierzytelniania w usłudze Twitter.  Uwierzytelnianie niestandardowe, będzie konieczne opracowywane oddzielnie.  Zapoznaj się [Pojęcia dotyczące uwierzytelniania] dokumentacji i [Przewodnik Szybki Start uwierzytelniania] dokumentacji, aby uzyskać więcej informacji.  

## <a name="updating-clients"></a>Aktualizowanie klientów mobilnych
Po utworzeniu operacyjnej zaplecza aplikacji mobilnej, można pracować na nową wersję aplikacji klienta, który ich używa. Mobile Apps również zawiera nową wersję zestawów SDK klienta, i podobnie jak uaktualnienie serwera powyżej, musisz usunąć wszystkie odwołania do zestawów SDK usługi Mobile przed zainstalowaniem wersji funkcji Mobile Apps.

Jedną z najważniejszych zmian między wersjami jest, że konstruktory nie wymagają już klucza aplikacji.
Możesz teraz po prostu przekazać adres URL aplikacji mobilnej. Na przykład na klientach .NET `MobileServiceClient` Konstruktor jest obecnie:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net" // URL of the Mobile App
        );

Możesz przeczytać o zainstalowanie nowych zestawów SDK i korzystanie z nowej struktury za pomocą poniższych linków:

* [Wersja systemu android 2,2 lub nowszy](app-service-mobile-android-how-to-use-client-library.md)
* [System iOS w wersji 3.0.0 lub nowszej](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (Windows/Xamarin) w wersji 2.0.0 lub nowszej](app-service-mobile-dotnet-how-to-use-client-library.md)
* [Apache Cordova w wersji 2.0 lub nowszej](app-service-mobile-cordova-how-to-use-client-library.md)

Jeśli aplikacja podejmuje użycie powiadomień wypychanych, zanotuj instrukcje rejestracji określonych dla każdej platformy, ponieważ było tam zmiany także.

Jeśli masz nową wersję klienta gotowy, wypróbuj działanie rozwiązania na uaktualnionym serwerze projektu. Po upewnieniu się, że działa, można zwolnić nową wersję aplikacji dla klientów. Po pewnym czasie po klienci mieli Państwo otrzymywać te aktualizacje, można usunąć usługi Mobile Services wersję aplikacji. W tym momencie możesz całkowicie uaktualniono do aplikacji usługi Mobile App przy użyciu najnowszy zestaw SDK serwera funkcji Mobile Apps.

<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[Co to jest usługa Mobile Apps?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[Web Job]: https://github.com/Azure/azure-webjobs-sdk/wiki
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Cennik usługi aplikacji]: https://azure.microsoft.com/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Pojęcia dotyczące uwierzytelniania]: ../app-service/overview-authentication-authorization.md
[Przewodnik Szybki Start uwierzytelniania]: app-service-mobile-auth.md

[Azure Portal]: https://portal.azure.com/
[OData]: https://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
