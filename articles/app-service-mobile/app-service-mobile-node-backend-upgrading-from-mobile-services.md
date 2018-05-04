---
title: Uaktualnienie z usług mobilnych Azure App Service - Node.js
description: Dowiedz się, jak łatwo przeprowadzić uaktualnienie aplikacji usługi Mobile Services do aplikacji usługi Mobile App Service
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
ms.openlocfilehash: 07ebf23270addc63793278d4e0510c187289b82c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="upgrade-your-existing-nodejs-azure-mobile-service-to-app-service"></a>Uaktualnij istniejącą usługę Mobile Node.js Azure App Service
Mobile App Service jest nowy sposób tworzenia aplikacji dla urządzeń przenośnych przy użyciu programu Microsoft Azure. Aby dowiedzieć się więcej, zobacz [co to są Mobile Apps?].

W tym artykule opisano sposób uaktualniania istniejącej aplikacji zaplecza Node.js z usług Azure Mobile Services do nowej aplikacji usługi Mobile Apps. Podczas wykonywania tego uaktualnienia istniejącej aplikacji usługi Mobile Services może kontynuować działanie.  Jeśli konieczne jest uaktualnienie aplikacji zaplecza Node.js, zapoznaj się [uaktualniania usług Mobile Services dla programu .NET](app-service-mobile-net-upgrading-from-mobile-services.md).

Po uaktualnieniu zaplecza mobilnego do usługi Azure App Service, ma dostęp do wszystkich funkcji usługi aplikacji i są rozliczane zgodnie ze standardem [cennik usługi aplikacji], nie Mobile Services cennik.

## <a name="migrate-vs-upgrade"></a>Migrowanie i uaktualnianie
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> Zaleca się że [przeprowadzenie migracji](app-service-mobile-migrating-from-mobile-services.md) przed rozpoczęciem uaktualnienia. W ten sposób można umieścić obie wersje aplikacji na tym samym planie usługi aplikacji i pociągnąć za sobą nie dodatkowych kosztów.
>
>

### <a name="improvements-in-mobile-apps-nodejs-server-sdk"></a>Ulepszenia w Mobile Apps Node.js server SDK
Uaktualnienie do nowej [zestaw SDK usługi Mobile Apps](https://www.npmjs.com/package/azure-mobile-apps) zawiera wiele ulepszeń, w tym:

* Na podstawie [Express framework](http://expressjs.com/en/index.html), nowego węzła zestawu SDK jest lekki i mające na celu nadąża z nowych wersji węzła, ponieważ zaczynają. Można dostosować zachowanie aplikacji z oprogramowania pośredniczącego Express.
* Znaczący wzrost wydajności w porównaniu do zestawu SDK usług mobilnych.
* Można hostować witrynę sieci Web wraz z zaplecza aplikacji mobilnych; Podobnie jest łatwo dodać do istniejącej aplikacji express.v4 zestaw SDK usługi Azure Mobile.
* Skompilowany dla rozwoju i platform i lokalnego, zestaw SDK usługi Mobile Apps można opracowany i uruchom lokalnie na platformach Windows, Linux lub OS x. Teraz jest łatwy w użyciu wspólnej węzła metody takie jak uruchamianie [Mocha](https://mochajs.org/) testy przed ich wdrożeniem.

## <a name="overview"></a>Ogólne omówienie uaktualnienia
W celu ułatwienia uaktualniania zaplecza Node.js, usługa aplikacji Azure udostępnił pakietu zgodności.  Po uaktualnieniu trzeba będzie nowej lokacji, które można wdrożyć do nowej lokacji z usługi aplikacji.

Są zestawy SDK klienta usługi Mobile Services **nie** zgodny z nowym serwerem Mobile Apps SDK. W celu zapewnienia ciągłości usługi dla aplikacji, nie należy opublikować zmian do lokacji aktualnie obsługujący opublikowanych klientów. Zamiast tego należy utworzyć nowej aplikacji mobilnej, która służy jako duplikat. Tej aplikacji można umieścić na tym samym planu usługi aplikacji, aby uniknąć ponoszenia dodatkowych kosztów finansowych.

Następnie będzie mieć dwie wersje aplikacji: symbole oraz inne, które następnie można uaktualnić i docelowych z nową wersją klienta, który pozostaje taki sam i służy opublikowane aplikacje. Można przenieść i testowanie kodu w tempie użytkownika, ale należy upewnić się, że wprowadzone wszystkie poprawki zastosowane do obu. Gdy uznasz, że odpowiednią liczbę klientów, które aplikacje w symbole zostały zaktualizowane do najnowszej wersji, można usunąć oryginalny zmigrowana aplikacja Jeśli jest to wymagane. Go nie pociągnąć za sobą dodatkowe koszty pieniężnego, jeśli obsługiwane w tym samym planie usługi aplikacji jako aplikacji mobilnej.

Pełna konspekt procesu uaktualniania przebiega następująco:

1. Pobierz istniejącej usługi mobilnej Azure (migrowanych).
2. Skonwertuj projekt do aplikacji mobilnej Azure przy użyciu pakietu zgodności.
3. Rozwiąż wszystkie problemy dotyczące różnic (na przykład ustawienia uwierzytelniania).
4. Wdróż przekonwertowanego projektu aplikacji mobilnej Azure do nowej usługi aplikacji.
5. Nowej wersji aplikacji klienta, który korzysta z nowej aplikacji mobilnej.
6. (Opcjonalnie) Usuń zmigrowane usługi mobilnej oryginalnej aplikacji.

Usuwanie może wystąpić, gdy nie jest widoczny cały ruch w oryginalnej zmigrowane usługi mobilnej.

## <a name="install-npm-package"></a> Zainstaluj wymagania wstępne
[Node] należy zainstalować na komputerze lokalnym.  Należy również zainstalować pakiet zgodności.  Po zainstalowaniu węzła, można uruchomić następujące polecenie z nowego cmd lub w wierszu polecenia programu PowerShell:

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a> Uzyskaj skrypty usług Azure Mobile Services
* Zaloguj się do [Azure Portal].
* Przy użyciu **wszystkie zasoby** lub **usługi aplikacji**, odnalezienie witryny usług Mobile Services.
* W ramach lokacji, kliknij **narzędzia** -> **Kudu** -> **Przejdź** otworzyć Kudu witryny.
* Polecenie **konsoli debugowania** -> **PowerShell** można otworzyć konsoli debugowania.
* Przejdź do `site/wwwroot/App_Data/config` , klikając kolejno w każdym katalogu
* Kliknij przycisk Pobierz ikonę `scripts` katalogu.

To spowoduje pobranie skryptów w formacie ZIP.  Utwórz nowy katalog na komputerze lokalnym i Rozpakuj `scripts.ZIP` pliku w katalogu.  Spowoduje to utworzenie `scripts` katalogu.

## <a name="scaffold-app"></a> Tworzenie szkieletu nowego zaplecza Azure Mobile Apps
Uruchom następujące polecenie z katalog zawierający katalogu skrypty:

```scaffold-mobile-app scripts out```

Spowoduje to utworzenie szkieletu zaplecza Azure Mobile Apps w `out` katalogu.  Mimo że nie jest to wymagane, to warto sprawdzić `out` katalogu do repozytorium kodu źródłowego, wybranych przez użytkownika.

## <a name="deploy-ama-app"></a> Wdrażanie z poziomu zaplecza Azure Mobile Apps
Podczas wdrażania należy wykonać następujące czynności:

1. Tworzenie nowej aplikacji mobilnej w [Azure Portal].
2. Uruchom `createViews.sql` skryptu połączenia bazy danych.
3. Połączenie bazy danych, która jest połączona z usługą Mobile do nowej usługi aplikacji.
4. Połącz żadnych innych zasobów (takich jak usługi Notification Hubs) do nowej usługi aplikacji.
5. Wdróż wygenerowany kod w nowej witryny.

### <a name="create-a-new-mobile-app"></a>Tworzenie nowej aplikacji mobilnej
1. Zaloguj się w [Azure Portal].
2. Kliknij kolejno opcje **+NOWE** > **Sieci Web i mobilność** > **Aplikacja mobilna**, a następnie podaj nazwę zaplecza aplikacji mobilnej.
3. W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów lub utwórz nową (używając tej samej nazwy, co aplikacja).

    Możesz wybrać inny plan usługi App Service lub utworzyć nowy. Aby uzyskać więcej informacji o usługach aplikacji planów i sposób tworzenia nowego planu w innej cennik warstwy oraz w preferowanej lokalizacji, zobacz [szczegółowe omówienie planów usługi aplikacji Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
4. W przypadku **planu usługi App Service** jest wybrany plan domyślny (w [warstwie standardowej](https://azure.microsoft.com/pricing/details/app-service/)). Możesz też wybrać inny plan lub [Utwórz nową](../app-service/app-service-plan-manage.md#create-an-app-service-plan). Ustawienia planu usługi aplikacji określają [lokalizację, funkcje, koszt i zasoby obliczeniowe](https://azure.microsoft.com/pricing/details/app-service/) skojarzone z aplikacją.

    Po wybraniu planu kliknij przycisk **Utwórz**. Spowoduje to utworzenie zaplecza aplikacji mobilnej.

### <a name="run-createviewssql"></a>Uruchom CreateViews.SQL
Ta aplikacja szkieletu zawiera plik o nazwie `createViews.sql`.  Ten skrypt musi zostać wykonana względem docelowej bazy danych.  Ciąg połączenia dla docelowej bazy danych można uzyskać z usługą mobilną zmigrowanych z **ustawienia** w obszarze **parametry połączenia**.  Szablon ma nazwę `MS_TableConnectionString`.

Możesz uruchomić ten skrypt w programie SQL Server Management Studio lub Visual Studio.

### <a name="link-the-database-to-your-app-service"></a>Połączyć bazy danych z usługi aplikacji
Połącz istniejącą bazę danych z usługi aplikacji:

* W [Azure Portal], Otwórz aplikację usługi.
* Wybierz **wszystkie ustawienia** -> **połączenia danych**.
* Polecenie **+ Dodaj**.
* Z listy rozwijanej wybierz **bazy danych SQL**
* W obszarze **bazy danych SQL**, wybierz istniejącą bazę danych, a następnie kliknij pozycję **wybierz**.
* W obszarze **ciąg połączenia**, wprowadź nazwę użytkownika i hasło dla bazy danych, a następnie kliknij pozycję **OK**.
* W **dodać połączenia danych** kliknij pozycję **OK**.

Wyświetlając ciąg połączenia dla docelowej bazy danych w usłudze Mobile migrowanych można znaleźć nazwy użytkownika i hasła.

### <a name="set-up-authentication"></a>Konfigurowanie uwierzytelniania
Aplikacje mobilne platformy Azure umożliwia konfigurowanie usługi Azure Active Directory, Facebook, Google, Microsoft i uwierzytelniania w usłudze Twitter.  Niestandardowe uwierzytelnianie będzie konieczne opracowywane oddzielnie.  Zapoznaj się [pojęć dotyczących uwierzytelniania] dokumentacji i [uwierzytelniania szybkiego startu] dokumentacji, aby uzyskać więcej informacji.  

## <a name="updating-clients"></a>Aktualizowanie klientów mobilnych
Po utworzeniu operacyjną zaplecza aplikacji mobilnej, można pracować na nowej wersji aplikacji klienta, która go wykorzystuje. Mobile Apps obejmuje również nową wersję klienta zestawy SDK i podobne do uaktualnienia serwera powyżej, należy usunąć wszystkie odwołania do zestawów SDK usługi Mobile przed zainstalowaniem wersji aplikacji mobilnej.

Jest jedną z głównych zmian między wersjami, że konstruktorów nie wymagają już klucz aplikacji.
Teraz po prostu są przekazywane w adresie URL aplikacji mobilnej. Na przykład na klientów platformy .NET `MobileServiceClient` Konstruktor jest teraz:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net" // URL of the Mobile App
        );

Możesz przeczytać o instalowaniu nowych zestawy SDK i przy użyciu nowej struktury za pośrednictwem łącza poniżej:

* [Wersja systemu android 2,2 lub nowszy](app-service-mobile-android-how-to-use-client-library.md)
* [System iOS w wersji 3.0.0 lub nowszej](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (system Windows/Xamarin) w wersji 2.0.0 lub nowszej](app-service-mobile-dotnet-how-to-use-client-library.md)
* [Apache Cordova w wersji 2.0 lub nowszej](app-service-mobile-cordova-how-to-use-client-library.md)

Czy aplikacja sprawia, że użycie powiadomień wypychanych, zanotuj instrukcje określonej rejestracji dla każdej platformy, jak nie zostały niektóre zmiany również.

Jeśli masz nową wersję klienta gotowa, wypróbuj go na uaktualnionym serwerze projektu. Po weryfikacji, czy działa, można zwolnić nowej wersji aplikacji dla klientów. Po pewnym czasie po klientów miało możliwość otrzymywać te aktualizacje, można usunąć wersji usług Mobile Services w aplikacji. W tym momencie całkowicie zostały uaktualnione do aplikacji mobilnej usługi dla aplikacji za pomocą najnowszej serwera Mobile Apps SDK.

<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[co to są Mobile Apps?]: app-service-mobile-value-prop.md
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
[cennik usługi aplikacji]: https://azure.microsoft.com/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[pojęć dotyczących uwierzytelniania]: ../app-service/app-service-authentication-overview.md
[uwierzytelniania szybkiego startu]: app-service-mobile-auth.md

[Azure Portal]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
