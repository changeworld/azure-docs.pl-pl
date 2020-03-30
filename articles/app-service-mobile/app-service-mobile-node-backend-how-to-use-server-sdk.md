---
title: Praca z modułem SDK serwera zaplecza Node.js
description: Dowiedz się, jak pracować z zestawem SDK serwera zaplecza Node.js dla aplikacji mobilnych usługi Azure App Service.
ms.assetid: e7d97d3b-356e-4fb3-ba88-38ecbda5ea50
ms.tgt_pltfrm: mobile-multiple
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: 566b6db829c9694a7e5e83cc25695e71ada0bc8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249947"
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>Jak korzystać z SDK node.js aplikacji mobilnych

[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Ten artykuł zawiera szczegółowe informacje i przykłady, które pokazują, jak pracować z node.js zaplecza w aplikacji mobilnych funkcji usługi Azure App Service.

## <a name="introduction"></a><a name="Introduction"></a>Wprowadzenie

Aplikacje mobilne umożliwia dodawanie interfejsu API sieci Web zoptymalizowanego pod kątem urządzeń przenośnych do aplikacji sieci Web. SDK aplikacji mobilnych jest przeznaczony dla aplikacji internetowych ASP.NET i Node.js. SDK oferuje następujące operacje:

* Operacje tabeli (odczyt, wstawianie, aktualizowanie, usuwanie) w celu uzyskania dostępu do danych
* Niestandardowe operacje interfejsu API

Obie operacje zapewniają uwierzytelnianie we wszystkich dostawcach tożsamości, na które zezwala usługa Azure App Service. Dostawcy ci obejmują dostawców tożsamości społecznościowych, takich jak Facebook, Twitter, Google i Microsoft, a także usługi Azure Active Directory dla tożsamości przedsiębiorstwa.

Przykłady dla każdego przypadku użycia można znaleźć w [katalogu przykładów w usłudze GitHub].

## <a name="supported-platforms"></a>Obsługiwane platformy

Zestaw SDK aplikacji mobilnych Node.js obsługuje bieżącą wersję usługi LTS węzła i nowszych. Obecnie najnowsza wersja LTS to Node v4.5.0. Inne wersje node może działać, ale nie są obsługiwane.

Zestaw SDK aplikacji mobilnych Node.js obsługuje dwa sterowniki bazy danych: 

* Sterownik node-mssql obsługuje usługę Azure SQL Database i lokalne wystąpienia programu SQL Server.  
* Sterownik sqlite3 obsługuje bazy danych SQLite tylko w jednym wystąpieniu.

### <a name="create-a-basic-nodejs-back-end-by-using-the-command-line"></a><a name="howto-cmdline-basicapp"></a>Tworzenie podstawowego zaplecza node.js przy użyciu wiersza polecenia

Wszystkie aplikacje mobilne Node.js zaplecza rozpoczyna się jako aplikacja ExpressJS. ExpressJS jest najpopularniejszą platformą usług sieci web dostępną dla node.js. Można utworzyć podstawową aplikację [Express] w następujący sposób:

1. W oknie polecenia lub programu PowerShell utwórz katalog dla projektu:

        mkdir basicapp

1. Uruchom, `npm init` aby zainicjować strukturę pakietu:

        cd basicapp
        npm init

   Polecenie `npm init` zadaje zestaw pytań, aby zainicjować projekt. Zobacz przykładowe dane wyjściowe:

   ![Npm init wyjście][0]

1. Zainstaluj `express` biblioteki z `azure-mobile-apps` repozytorium npm:

        npm install --save express azure-mobile-apps

1. Utwórz plik app.js, aby zaimplementować podstawowy serwer mobilny:

    ```javascript
    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define a TodoItem table.
    mobile.tables.add('TodoItem');

    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);
    ```

Ta aplikacja tworzy interfejs API sieci Web zoptymalizowany`/tables/TodoItem`pod kątem urządzeń przenośnych z jednym punktem końcowym ( ), który zapewnia nieuwierzyfowany dostęp do podstawowego magazynu danych SQL przy użyciu schematu dynamicznego. Nadaje się do następujących szybki startów biblioteki klienta:

* [Szybki start klienta systemu Android]
* [Szybki start klienta Apache Cordova]
* [Szybki start klienta systemu iOS]
* [Szybki start klienta Sklepu Windows]
* [Szybki start klienta platformy Xamarin.iOS]
* [Szybki start klienta platformy Xamarin.Android]
* [Szybki start klienta platformy Xamarin.Forms]

Kod tej podstawowej aplikacji można znaleźć w [przykładzie basicapp na GitHub].

### <a name="create-a-nodejs-back-end-by-using-visual-studio-2015"></a><a name="howto-vs2015-basicapp"></a>Tworzenie zaplecza node.js przy użyciu programu Visual Studio 2015

Visual Studio 2015 wymaga rozszerzenia do tworzenia aplikacji Node.js w ramach IDE. Aby rozpocząć, należy zainstalować [node.js Tools 1.1 dla programu Visual Studio]. Po zakończeniu instalacji utwórz aplikację Express 4.x:

1. Otwórz okno dialogowe **Nowy projekt** (z **pliku** > **nowego** > **projektu).**
1. Rozwiń **szablony** > **JavaScript** > **Node.js**.
1. Wybierz **podstawową aplikację usługi Azure Node.js Express 4**.
1. Wpisz nazwę projektu. Kliknij przycisk **OK**.

   ![Nowy projekt programu Visual Studio 2015][1]
1. Kliknij prawym przyciskiem myszy węzeł **npm** i wybierz polecenie **Zainstaluj nowe pakiety npm**.
1. Może być konieczne odświeżenie katalogu npm po utworzeniu pierwszej aplikacji Node.js. W razie potrzeby **wybierz opcję Odśwież.**
1. Wprowadź w polu wyszukiwania **aplikacje azure-mobile.** Wybierz pakiet **azure-mobile-apps 2.0.0,** a następnie wybierz pozycję **Zainstaluj pakiet**.

   ![Instalowanie nowych pakietów npm][2]
1. Wybierz polecenie **Zamknij**.
1. Otwórz plik app.js, aby dodać obsługę SDK aplikacji mobilnych. W wierszu 6 u `require` dołu instrukcji biblioteki dodaj następujący kod:

    ```javascript
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

    W przybliżeniu wiersz 27 po innych `app.use` instrukcji, dodać następujący kod:

    ```javascript
    app.use('/users', users);

    // Mobile Apps initialization
    var mobile = azureMobileApps();
    mobile.tables.add('TodoItem');
    app.use(mobile);
    ```

    Zapisz plik.

1. Uruchom aplikację lokalnie (interfejs API jest `http://localhost:3000`obsługiwany na) lub opublikować na platformie Azure.

### <a name="create-a-nodejs-back-end-by-using-the-azure-portal"></a><a name="create-node-backend-portal"></a>Tworzenie zaplecza node.js przy użyciu portalu Azure

W [witrynie Azure Portal]można utworzyć zaplecze aplikacji mobilnych. Możesz wykonać następujące kroki lub utworzyć klienta i serwer razem, wykonując samouczek [Tworzenie aplikacji mobilnej.](app-service-mobile-ios-get-started.md) Samouczek zawiera uproszczoną wersję tych instrukcji i jest najlepszy dla projektów weryfikacji koncepcji.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

W okienku **Wprowadzenie** w obszarze **Tworzenie interfejsu API tabeli**wybierz pozycję **Node.js** jako język zaplecza.
Zaznacz pole **dla potwierdzam, że spowoduje to zastąpienie całej zawartości witryny,** a następnie wybierz pozycję **Utwórz tabelę TodoItem**.

### <a name="download-the-nodejs-back-end-quickstart-code-project-by-using-git"></a><a name="download-quickstart"></a>Pobierz projekt kodu szybkiego startu programu Node.js przy użyciu gita

Podczas tworzenia zaplecza aplikacji mobilnych Node.js przy użyciu okienka **Szybki start** portalu tworzony jest projekt Node.js i wdrażany w witrynie. W portalu można dodawać tabele i interfejsy API oraz edytować pliki kodu zaplecza Node.js. Można również użyć różnych narzędzi wdrażania, aby pobrać projekt zaplecza, dzięki czemu można dodawać lub modyfikować tabele i interfejsy API, a następnie ponownie opublikować projekt. Aby uzyskać więcej informacji, zobacz [przewodnik wdrażania usługi Azure App Service].

Poniższa procedura używa repozytorium Git do pobrania kodu projektu szybki start:

1. Zainstaluj Git, jeśli jeszcze tego nie zrobiłeś. Kroki wymagane do zainstalowania Git różnią się w zależności od systemu operacyjnego. Aby zapoznać się z instrukcjami dotyczącymi dystrybucji i instalacji specyficznych dla systemu operacyjnego, zobacz [Instalowanie gita](https://git-scm.com/book/en/Getting-Started-Installing-Git).
2. Zobacz [Przygotowywanie repozytorium,](../app-service/deploy-local-git.md#prepare-your-repository) aby włączyć repozytorium Git dla witryny zaplecza. Zanotuj nazwę użytkownika i hasło wdrożenia.
3. W okienku zaplecza aplikacji mobilnych zanotuj ustawienie **adresu URL klonu Git.**
4. Wykonaj `git clone` polecenie przy użyciu adresu URL klonu Git. Wprowadź hasło w razie potrzeby, jak w poniższym przykładzie:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Przejdź do katalogu lokalnego`/todolist` (w poprzednim przykładzie) i zwróć uwagę, że pliki projektu zostały pobrane. Znajdź plik todoitem.json `/tables` w katalogu. Ten plik definiuje uprawnienia do tabeli. Również znaleźć plik todoitem.js w tym samym katalogu. Definiuje skrypty operacji CRUD dla tabeli.
6. Po wdaniu zmian w plikach projektu uruchom następujące polecenia, aby dodać, zatwierdzić, a następnie przekazać zmiany do witryny:

        $ git commit -m "updated the table script"
        $ git push origin master

   Po dodaniu nowych plików do projektu należy `git add .` najpierw uruchomić polecenie.

Witryna jest ponownie publikowana za każdym razem, gdy nowy zestaw zatwierdzeń jest wypychany do witryny.

### <a name="publish-your-nodejs-back-end-to-azure"></a><a name="howto-publish-to-azure"></a>Publikowanie zaplecza node.js na platformie Azure

Platforma Microsoft Azure udostępnia wiele mechanizmów publikowania aplikacji mobilnych Node.js zaplecza do usługi Azure. Mechanizmy te obejmują narzędzia wdrażania zintegrowane z programem Visual Studio, narzędzia wiersza polecenia i opcje ciągłego wdrażania oparte na kontroli źródła. Aby uzyskać więcej informacji, zobacz [przewodnik wdrażania usługi Azure App Service].

Usługa Azure App Service ma konkretne porady dotyczące aplikacji Node.js, które należy przejrzeć przed opublikowaniem zaplecza:

* Jak [określić wersję węzła]
* Jak [korzystać z modułów node]

### <a name="enable-a-home-page-for-your-application"></a><a name="howto-enable-homepage"></a>Włączanie strony głównej aplikacji

Wiele aplikacji to połączenie aplikacji internetowych i mobilnych. Można użyć platformy ExpressJS, aby połączyć dwa aspekty. Czasami jednak można zaimplementować tylko interfejs mobilny. Warto udostępnić stronę główną, aby upewnić się, że usługa aplikacji jest uruchomiona. Możesz podać własną stronę główną lub włączyć tymczasową stronę główną. Aby włączyć tymczasową stronę główną, użyj następującego kodu do tworzenia wystąpienia aplikacji mobilnych:

```javascript
var mobile = azureMobileApps({ homePage: true });
```

Jeśli ta opcja ma być dostępna tylko podczas tworzenia lokalnie, możesz dodać to ustawienie do pliku azureMobile.js.

## <a name="table-operations"></a><a name="TableOperations"></a>Operacje na stole

Zestaw SDK serwera node.js w aplikacji azure-mobile zawiera mechanizmy służące udostępnianiu tabel danych przechowywanych w bazie danych SQL usługi Azure jako interfejsu API sieci Web. Zapewnia pięć operacji:

| Operacja | Opis |
| --- | --- |
| POBIERZ /tabele/*nazwa tabeli* |Pobierz wszystkie rekordy w tabeli. |
| POBIERZ /tabele/*nazwa tabeli*/:id |Pobierz określony rekord w tabeli. |
| POST /tabele/*nazwa tabeli* |Utwórz rekord w tabeli. |
| PATCH /tabele/*nazwa tabeli*/:id |Zaktualizuj rekord w tabeli. |
| USUŃ /tabele/*nazwa tabeli*/:id |Usuwanie rekordu w tabeli. |

Ten internetowy interfejs API obsługuje [odany] i rozszerza schemat tabeli, aby obsługiwać [synchronizację danych w trybie offline].

### <a name="define-tables-by-using-a-dynamic-schema"></a><a name="howto-dynamicschema"></a>Definiowanie tabel przy użyciu schematu dynamicznego

Przed użyciem tabeli należy ją zdefiniować. Tabele można definiować przy użyciu schematu statycznego (w którym definiuje się kolumny w schemacie) lub dynamicznie (gdzie SDK steruje schematem na podstawie żądań przychodzących). Ponadto można kontrolować określone aspekty interfejsu API sieci Web, dodając kod JavaScript do definicji.

Najlepszym rozwiązaniem jest zdefiniowanie każdej tabeli w pliku `tables` JavaScript w katalogu, a następnie użycie `tables.import()` tej metody do zaimportowania tabel. Rozszerzając przykład podstawowej aplikacji, należy dostosować plik app.js:

```javascript
var express = require('express'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Define the database schema that is exposed.
mobile.tables.import('./tables');

// Provide initialization of any tables that are statically defined.
mobile.tables.initialize().then(function () {
    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);
});
```

Zdefiniuj tabelę w ./tables/TodoItem.js:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here.

module.exports = table;
```

Tabele domyślnie używają schematu dynamicznego. Aby wyłączyć schemat dynamiczny globalnie, `MS_DynamicSchema` ustaw ustawienie aplikacji na false w witrynie Azure portal.

Pełny przykład można znaleźć w [przykładzie todo na GitHub].

### <a name="define-tables-by-using-a-static-schema"></a><a name="howto-staticschema"></a>Definiowanie tabel przy użyciu schematu statycznego

Można jawnie zdefiniować kolumny do udostępnienia za pośrednictwem interfejsu API sieci Web. Zestaw SDK node.js w aplikacji azure-mobile automatycznie dodaje do listy wszystkie dodatkowe kolumny wymagane do synchronizacji danych w trybie offline. Na przykład aplikacje klienckie szybki start `text` wymagają tabeli `complete` z dwiema kolumnami: (ciąg) i (wartość logiczna).  
Tabelę można zdefiniować w pliku JavaScript definicji tabeli (znajdującym `tables` się w katalogu) w następujący sposób:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

module.exports = table;
```

Jeśli tabele są definiowane statycznie, należy również wywołać `tables.initialize()` metodę tworzenia schematu bazy danych podczas uruchamiania. Metoda `tables.initialize()` zwraca [obietnicę,] dzięki czemu usługa sieci web nie obsługuje żądań przed zainicjowaniem bazy danych.

### <a name="use-sql-server-express-as-a-development-data-store-on-your-local-machine"></a><a name="howto-sqlexpress-setup"></a>Używanie programu SQL Server Express jako magazynu danych deweloperskich na komputerze lokalnym

Zestaw SDK aplikacji mobilnych Node.js udostępnia trzy opcje wyświetlania danych po wyjęciu z pudełka:

* Użyj sterownika **pamięci,** aby zapewnić nietrwałe magazyn przykładów.
* Użyj sterownika **mssql,** aby zapewnić magazyn danych PROGRAMU SQL Server Express w celu opracowania.
* Użyj sterownika **mssql,** aby zapewnić magazyn danych usługi Azure SQL Database dla produkcji.

Moduł SDK aplikacji mobilnych node.js używa [pakietu mssql Node.js] do nawiązania i używania połączenia z bazą danych SQL Server Express i SQL. Ten pakiet wymaga włączenia połączeń TCP w wystąpieniu programu SQL Server Express.

> [!TIP]
> Sterownik pamięci nie zapewnia pełnego zestawu urządzeń do testowania. Jeśli chcesz przetestować zaplecza lokalnie, zaleca się użycie magazynu danych programu SQL Server Express i sterownik mssql.

1. Pobierz i zainstaluj program [Microsoft SQL Server 2014 Express]. Upewnij się, że zainstalowano program SQL Server 2014 Express z tools edition. Jeśli jawnie nie wymagasz obsługi 64-bitowej, wersja 32-bitowa zużywa mniej pamięci podczas uruchamiania.
1. Uruchamianie programu SQL Server 2014 Configuration Manager:

   a. Rozwiń węzeł **Konfiguracja sieci programu SQL Server** w menu drzewa.

   b. Wybierz **protokoły dla SQLEXPRESS**.

   d. Kliknij prawym przyciskiem myszy pozycję **TCP/IP** i wybierz polecenie **Włącz**. Wybierz **przycisk OK** w wyskakującym oknie dialogowym.

   d. Kliknij prawym przyciskiem myszy pozycję **TCP/IP** i wybierz polecenie **Właściwości**.

   e. Wybierz kartę **Adresy IP.**

   f. Znajdź węzeł **IPAll.** W polu **Port TCP** wprowadź **1433**.

      ![Konfigurowanie programu SQL Server Express dla protokołu TCP/IP][3]

   g. Kliknij przycisk **OK**. Wybierz **przycisk OK** w wyskakującym oknie dialogowym.

   h. Wybierz **sql server services** w menu drzewa.

   i. Kliknij prawym przyciskiem myszy pozycję **SQL Server (SQLEXPRESS)** i wybierz polecenie **Uruchom ponownie**.

   j. Zamknij program SQL Server 2014 Configuration Manager.

1. Uruchom program SQL Server 2014 Management Studio i połącz się z lokalnym wystąpieniem programu SQL Server Express:

   1. Kliknij prawym przyciskiem myszy wystąpienie w Eksploratorze obiektów i wybierz polecenie **Właściwości**.
   1. Wybierz stronę **Zabezpieczenia.**
   1. Upewnij się, że wybrano **tryb uwierzytelniania programu SQL Server i systemu Windows.**
   1. Kliknij przycisk **OK**.

      ![Konfigurowanie uwierzytelniania SQL Server Express][4]
   1. Rozwiń**ustawienia logowania** **zabezpieczeń** > w Eksploratorze obiektów.
   1. Kliknij prawym przyciskiem myszy **pozycję Logins** i wybierz **pozycję Nowy login**.
   1. Wprowadź nazwę logowania. Wybierz pozycję **Uwierzytelnianie programu SQL Server**. Wprowadź hasło, a następnie wprowadź to samo hasło w **obszarze Potwierdź hasło**. Hasło musi spełniać wymagania dotyczące złożoności systemu Windows.
   1. Kliknij przycisk **OK**.

      ![Dodawanie nowego użytkownika do programu SQL Server Express][5]
   1. Kliknij prawym przyciskiem myszy nowy login i wybierz polecenie **Właściwości**.
   1. Wybierz stronę **Role serwera.**
   1. Zaznacz pole wyboru roli serwera **dbcreator.**
   1. Kliknij przycisk **OK**.
   1. Zamknij program SQL Server 2015 Management Studio.

Pamiętaj, aby zarejestrować wybraną nazwę użytkownika i hasło. W zależności od wymagań bazy danych może być konieczne przypisanie dodatkowych ról lub uprawnień serwera.

Aplikacja Node.js odczytuje zmienną `SQLCONNSTR_MS_TableConnectionString` środowiskową dla ciągu połączenia dla tej bazy danych. Tę zmienną można ustawić w twoim środowisku. Na przykład można użyć programu PowerShell, aby ustawić tę zmienną środowiskową:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Dostęp do bazy danych za pośrednictwem połączenia TCP/IP. Podaj nazwę użytkownika i hasło do połączenia.

### <a name="configure-your-project-for-local-development"></a><a name="howto-config-localdev"></a>Konfigurowanie projektu do rozwoju lokalnego

Aplikacje mobilne odczytują plik JavaScript o nazwie *azureMobile.js* z lokalnego systemu plików. Nie należy używać tego pliku do konfigurowania sdk aplikacji mobilnych w produkcji. Zamiast tego użyj **ustawień aplikacji** w [witrynie Azure portal].

Plik azureMobile.js powinien wyeksportować obiekt konfiguracji. Najczęstsze ustawienia to:

* Ustawienia bazy danych
* Ustawienia rejestrowania diagnostycznego
* Alternatywne ustawienia CORS

W tym przykładzie plik **azureMobile.js** implementuje poprzednie ustawienia bazy danych:

```javascript
module.exports = {
    cors: {
        origins: [ 'localhost' ]
    },
    data: {
        provider: 'mssql',
        server: '127.0.0.1',
        database: 'mytestdatabase',
        user: 'azuremobile',
        password: 'T3stPa55word'
    },
    logging: {
        level: 'verbose'
    }
};
```

Zaleca się dodanie **pliku azureMobile.js** do pliku **.gitignore** (lub innego pliku ignorowania kontroli kodu źródłowego), aby zapobiec przechowywaniu haseł w chmurze. Zawsze konfigurować ustawienia produkcji w **ustawieniach aplikacji** w [witrynie Azure portal].

### <a name="configure-app-settings-for-your-mobile-app"></a><a name="howto-appsettings"></a>Konfigurowanie ustawień aplikacji dla aplikacji mobilnej

Większość ustawień w pliku azureMobile.js ma równoważne ustawienie aplikacji w [witrynie Azure portal]. Użyj poniższej listy, aby skonfigurować aplikację w **ustawieniach aplikacji:**

| Ustawienie aplikacji | ustawienie azureMobile.js | Opis | Prawidłowe wartości |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |name |Nazwa aplikacji |ciąg |
| **MS_MobileLoggingLevel** |logging.level |Minimalny poziom dziennika wiadomości do zarejestrowania |błąd, ostrzeżenie, informacje, pełne, debugowanie, głupie |
| **MS_DebugMode** |debugowanie |Włącza lub wyłącza tryb debugowania |wartość true, false |
| **MS_TableSchema** |data.schema |Domyślna nazwa schematu dla tabel SQL |ciąg (domyślnie: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Włącza lub wyłącza tryb debugowania |wartość true, false |
| **MS_DisableVersionHeader** |wersja (ustawiona na niezdefiniowana) |Wyłącza nagłówek X-ZUMO-Server-Version |wartość true, false |
| **MS_SkipVersionCheck** |skipversioncheck (pomijanie) |Wyłącza sprawdzanie wersji interfejsu API klienta |wartość true, false |

Aby ustawić ustawienie aplikacji:

1. Zaloguj się do [Portalu Azure].
1. Wybierz **pozycję Wszystkie zasoby** lub usługi **aplikacji**, a następnie wybierz nazwę aplikacji mobilnej.
1. Domyślnie zostanie otwarte okienko **Ustawienia.** Jeśli tak nie jest, wybierz pozycję **Ustawienia**.
1. W menu **OGÓLNE** wybierz polecenie **Ustawienia aplikacji**.
1. Przewiń do sekcji **Ustawienia aplikacji.**
1. Jeśli ustawienie aplikacji już istnieje, wybierz wartość ustawienia aplikacji, aby edytować tę wartość.
   Jeśli ustawienie aplikacji nie istnieje, wprowadź ustawienie aplikacji w polu **Klucz** i wartość w polu **Wartość.**
1. Wybierz **pozycję Zapisz**.

Zmiana większości ustawień aplikacji wymaga ponownego uruchomienia usługi.

### <a name="use-sql-database-as-your-production-data-store"></a><a name="howto-use-sqlazure"></a>Używanie bazy danych SQL jako magazynu danych produkcyjnych

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Korzystanie z usługi Azure SQL Database jako magazynu danych jest identyczne we wszystkich typach aplikacji usługi Azure App Service. Jeśli nie zostało to jeszcze zrobione, wykonaj następujące kroki, aby utworzyć zaplecze aplikacji mobilnych:

1. Zaloguj się do [Portalu Azure].
1. W lewym górnym rogu okna wybierz przycisk **+NOWY** > **aplikacji sieci Web + mobilna,** > **Mobile App**a następnie podaj nazwę zaplecza aplikacji mobilnych.
1. W polu **Grupa zasobów** wprowadź tę samą nazwę co aplikacja.
1. Zostanie wybrany domyślny plan usługi aplikacji. Jeśli chcesz zmienić plan usługi App Service:

   a. Wybierz **pozycję Plan** > usługi aplikacji **+Utwórz nowy**.

   b. Podaj nazwę nowego planu usługi app service i wybierz odpowiednią lokalizację.

   d. Wybierz odpowiednią warstwę cenową dla usługi. Wybierz **pozycję Wyświetl wszystko,** aby wyświetlić więcej opcji cenowych, takich jak **Bezpłatne** i **Udostępnione**.

   d. Kliknij przycisk **Wybierz.**

   e. W okienku **planu usługi app service** wybierz przycisk **OK**.
1. Wybierz **pozycję Utwórz**.

Inicjowanie obsługi administracyjnej zaplecza aplikacji mobilnych może potrwać kilka minut. Po zainicjowaniu obsługi administracyjnej aplikacji mobilnych portal otworzy okienko **Ustawienia** zaplecza aplikacji mobilnych.

Można połączyć istniejącą bazę danych SQL z zapleczem aplikacji mobilnych lub utworzyć nową bazę danych SQL. W tej sekcji tworzymy bazę danych SQL.

> [!NOTE]
> Jeśli masz już bazę danych w tej samej lokalizacji co zaplecze aplikacji mobilnych, możesz zamiast tego wybrać opcję **Użyj istniejącej bazy danych,** a następnie wybrać tę bazę danych. Nie zaleca się korzystania z bazy danych w innej lokalizacji ze względu na większe opóźnienia.

1. W nowym zapleczu aplikacji mobilnych wybierz pozycję **Ustawienia** >  > **danych** > **aplikacji mobilnej****+Dodaj**.
1. W okienku **Dodawanie połączenia danych** wybierz pozycję Baza danych SQL — **konfigurowanie wymaganych ustawień** > **Utwórz nową bazę danych**. Wprowadź nazwę nowej bazy danych w polu **Nazwa.**
1. Wybierz **serwer**. W okienku **Nowy serwer** wprowadź unikatową nazwę serwera w polu **Nazwa serwera** i podaj odpowiedni login i hasło administratora serwera. Upewnij się, że wybrano **opcję Zezwalaj na dostęp do serwera usług platformy Azure.** Kliknij przycisk **OK**.

   ![Tworzenie bazy danych Azure SQL Database][6]
1. W okienku **Nowa baza danych** wybierz pozycję **OK**.
1. W okienku **Dodaj połączenie danych** wybierz pozycję Parametry **połączenia**i wprowadź login i hasło podane podczas tworzenia bazy danych. Jeśli używasz istniejącej bazy danych, podaj poświadczenia logowania dla tej bazy danych. Kliknij przycisk **OK**.
1. Ponownie w okienku **Dodawanie połączenia danych** wybierz przycisk **OK,** aby utworzyć bazę danych.

<!--- END OF ALTERNATE INCLUDE -->

Utworzenie bazy danych może potrwać kilka minut. Użyj **obszaru Powiadomienia,** aby monitorować postęp wdrożenia. Nie postępuj, dopóki baza danych nie zostanie pomyślnie wdrożona. Po wdrożeniu bazy danych dla wystąpienia bazy danych SQL w ustawieniach aplikacji zaplecza aplikacji aplikacji aplikacji mobilnych tworzony jest ciąg połączenia. Możesz zobaczyć to ustawienie aplikacji w **ustawieniach ustawień** > **Application settings** > aplikacji**Parametry połączenia**.

### <a name="require-authentication-for-access-to-tables"></a><a name="howto-tables-auth"></a>Wymagaj uwierzytelniania w celu uzyskania dostępu do tabel

Jeśli chcesz używać uwierzytelniania usługi `tables` app service z punktem końcowym, należy najpierw skonfigurować uwierzytelnianie usługi App Service w [portalu Azure.] Aby uzyskać więcej informacji, zobacz przewodnik konfiguracji dostawcy tożsamości, którego zamierzasz użyć:

* [Konfigurowanie uwierzytelniania usługi Azure Active Directory]
* [Konfigurowanie uwierzytelniania na Facebooku]
* [Konfigurowanie uwierzytelniania Google]
* [Konfigurowanie uwierzytelniania firmy Microsoft]
* [Konfigurowanie uwierzytelniania twitterowego]

Każda tabela ma właściwość dostępu, której można użyć do kontrolowania dostępu do tabeli. W poniższym przykładzie przedstawiono tabelę zdefiniowaną statycznie z wymaganym uwierzytelnianiem.

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

Właściwość access może przyjąć jedną z trzech wartości:

* *anonymous* wskazuje, że aplikacja kliencka może odczytywać dane bez uwierzytelniania.
* *uwierzytelniony* wskazuje, że aplikacja kliencka musi wysłać prawidłowy token uwierzytelniania z żądaniem.
* *oznacza,* że ta tabela jest obecnie wyłączona.

Jeśli właściwość dostępu jest niezdefiniowana, dozwolony jest nieuwierzytywy dostęp.

### <a name="use-authentication-claims-with-your-tables"></a><a name="howto-tables-getidentity"></a>Używanie oświadczeń uwierzytelniających z tabelami
Można skonfigurować różne oświadczenia, które są wymagane podczas konfigurowania uwierzytelniania. Oświadczenia te nie są zwykle `context.user` dostępne za pośrednictwem obiektu. Jednak można je pobrać przy `context.user.getIdentity()` użyciu metody. Metoda `getIdentity()` zwraca obietnicę, która jest rozpoznawana dla obiektu. Obiekt jest wpisany przez metodę`facebook` `google`uwierzytelniania `microsoftaccount`( `aad`, , `twitter`, , lub ).

Jeśli na przykład skonfigurowane jest uwierzytelnianie konta Microsoft i żądanie oświadczenia dotyczącego adresów e-mail, możesz dodać ten adres e-mail do rekordu za pomocą następującego kontrolera tabeli:

```javascript
var azureMobileApps = require('azure-mobile-apps');

// Create a new table definition.
var table = azureMobileApps.table();

table.columns = {
    "emailAddress": "string",
    "text": "string",
    "complete": "boolean"
};
table.dynamicSchema = false;
table.access = 'authenticated';

/**
* Limit the context query to those records with the authenticated user email address
* @param {Context} context the operation context
* @returns {Promise} context execution Promise
*/
function queryContextForEmail(context) {
    return context.user.getIdentity().then((data) => {
        context.query.where({ emailAddress: data.microsoftaccount.claims.emailaddress });
        return context.execute();
    });
}

/**
* Adds the email address from the claims to the context item - used for
* insert operations
* @param {Context} context the operation context
* @returns {Promise} context execution Promise
*/
function addEmailToContext(context) {
    return context.user.getIdentity().then((data) => {
        context.item.emailAddress = data.microsoftaccount.claims.emailaddress;
        return context.execute();
    });
}

// Configure specific code when the client does a request.
// READ: only return records that belong to the authenticated user.
table.read(queryContextForEmail);

// CREATE: add or overwrite the userId based on the authenticated user.
table.insert(addEmailToContext);

// UPDATE: only allow updating of records that belong to the authenticated user.
table.update(queryContextForEmail);

// DELETE: only allow deletion of records that belong to the authenticated user.
table.delete(queryContextForEmail);

module.exports = table;
```

Aby zobaczyć, jakie oświadczenia są dostępne, `/.auth/me` użyj przeglądarki sieci Web, aby wyświetlić punkt końcowy witryny.

### <a name="disable-access-to-specific-table-operations"></a><a name="howto-tables-disabled"></a>Wyłączanie dostępu do określonych operacji tabeli

Oprócz pojawiania się w tabeli właściwości access może służyć do kontrolowania poszczególnych operacji. Istnieją cztery operacje:

* `read`jest operacją RESTful GET na stole.
* `insert`jest operacją RESTful POST na stole.
* `update`jest operacją RESTful PATCH na stole.
* `delete`jest operacją RESTful DELETE w tabeli.

Na przykład można podać tabelę nieuwierzycowaną tylko do odczytu:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Read-only table. Only allow READ operations.
table.read.access = 'anonymous';
table.insert.access = 'disabled';
table.update.access = 'disabled';
table.delete.access = 'disabled';

module.exports = table;
```

### <a name="adjust-the-query-that-is-used-with-table-operations"></a><a name="howto-tables-query"></a>Dostosowywanie kwerendy używanej z operacjami tabeli

Typowym wymaganiem dla operacji tabeli jest zapewnienie ograniczonego widoku danych. Na przykład można podać tabelę, która jest oznaczona z uwierzytelnionym identyfikatorem użytkownika, dzięki czemu można tylko odczytywać lub aktualizować własne rekordy. Następująca definicja tabeli zawiera tę funkcję:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define a static schema for the table.
table.columns = {
    "userId": "string",
    "text": "string",
    "complete": "boolean"
};
table.dynamicSchema = false;

// Require authentication for this table.
table.access = 'authenticated';

// Ensure that only records for the authenticated user are retrieved.
table.read(function (context) {
    context.query.where({ userId: context.user.id });
    return context.execute();
});

// When adding records, add or overwrite the userId with the authenticated user.
table.insert(function (context) {
    context.item.userId = context.user.id;
    return context.execute();
});

module.exports = table;
```

Operacje, które zwykle uruchamiają kwerendę, mają właściwość kwerendy, którą można dostosować za pomocą `where` klauzuli. Właściwość query jest obiektem [QueryJS] używanym do konwertowania kwerendy OData na coś, co może przetwarzać zaplecze danych. W przypadku prostych przypadków równości (takich jak poprzednia) można użyć mapy. Można również dodać określone klauzule SQL:

```javascript
context.query.where('myfield eq ?', 'value');
```

### <a name="configure-a-soft-delete-on-a-table"></a><a name="howto-tables-softdelete"></a>Konfigurowanie usuwania nietrwałego w tabeli

Usunięcie nietrwałe w rzeczywistości nie powoduje usunięcia rekordów. Zamiast tego oznacza je jako usunięte w bazie danych, ustawiając usunięte kolumny na true. SDK aplikacji mobilnych automatycznie usuwa nietrwale usunięte rekordy z wyników, chyba że użyje `IncludeDeleted()`go SDK klienta mobilnego. Aby skonfigurować tabelę do usuwania `softDelete` nietrwałego, ustaw właściwość w pliku definicji tabeli:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Turn on soft delete.
table.softDelete = true;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

Należy ustanowić mechanizm usuwania rekordów: aplikacji klienckiej, usługi WebJob, funkcji platformy Azure lub niestandardowego interfejsu API.

### <a name="seed-your-database-with-data"></a><a name="howto-tables-seeding"></a>Rozsiew bazy danych danymi

Podczas tworzenia nowej aplikacji, można wyeksliować tabelę z danymi. Można to zrobić w pliku JavaScript definicji tabeli w następujący sposób:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};
table.seed = [
    { text: 'Example 1', complete: false },
    { text: 'Example 2', complete: true }
];

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

Rozmieszczanie danych odbywa się tylko wtedy, gdy do utworzenia tabeli użyto zestawu SDK aplikacji mobilnych. Jeśli tabela już istnieje w bazie danych, żadne dane nie są wstrzykiwane do tabeli. Jeśli schemat dynamiczny jest włączony, schemat jest wywnioskowany z danych rozstawionych.

Zaleca się jawnie wywołać `tables.initialize()` metodę, aby utworzyć tabelę, gdy usługa zaczyna działać.

### <a name="enable-swagger-support"></a><a name="Swagger"></a>Obsługa włącz swagger
Aplikacje mobilne są wyposażone we wbudowaną obsługę [Swagger.] Aby włączyć obsługę Swagger, najpierw zainstaluj swagger-ui jako zależność:

    npm install --save swagger-ui

Następnie można włączyć obsługę Swagger w konstruktorze aplikacji mobilnych:

```javascript
var mobile = azureMobileApps({ swagger: true });
```

Prawdopodobnie chcesz włączyć obsługę Swagger w wersjach rozwojowych. Można to zrobić za `NODE_ENV` pomocą ustawienia aplikacji:

```javascript
var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });
```

Punkt `swagger` końcowy znajduje się w http://*miejscu*.azurewebsites.net/swagger. Dostęp do interfejsu użytkownika swagger `/swagger/ui` można uzyskać za pośrednictwem punktu końcowego. Jeśli zdecydujesz się wymagać uwierzytelniania w całej aplikacji, Swagger generuje błąd. Aby uzyskać najlepsze wyniki, wybierz opcję zezwalania na nieuwierzyte żądania w ustawieniach `table.access` uwierzytelniania/autoryzacji usługi Azure App Service, a następnie kontroluj uwierzytelnianie przy użyciu właściwości.

Można również dodać opcję Swagger do pliku azureMobile.js, jeśli chcesz tylko Swagger wsparcia dla tworzenia lokalnie.

## <a name="push-notifications"></a><a name="push"/>Powiadomienia push

Aplikacje mobilne integrują się z centrum powiadomień platformy Azure, dzięki czemu można wysyłać ukierunkowane powiadomienia wypychane do milionów urządzeń na wszystkich głównych platformach. Korzystając z Centrów powiadomień, możesz wysyłać powiadomienia wypychane na urządzenia z systemami iOS, Android i Windows. Aby dowiedzieć się więcej o wszystkich, co można zrobić z Centrum powiadomień, zobacz [Centrum powiadomień — omówienie](../notification-hubs/notification-hubs-push-notification-overview.md).

### <a name="send-push-notifications"></a><a name="send-push"></a>Wysyłanie powiadomień push

Poniższy kod pokazuje, `push` jak używać obiektu do wysyłania powiadomienia wypychanego emisji do zarejestrowanych urządzeń z systemem iOS:

```javascript
// Create an APNS payload.
var payload = '{"aps": {"alert": "This is an APNS payload."}}';

// Only do the push if configured.
if (context.push) {
    // Send a push notification by using APNS.
    context.push.apns.send(null, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

Tworząc rejestrację wypychania szablonu z klienta, można zamiast tego wysłać wiadomość wypychaną szablonu do urządzeń na wszystkich obsługiwanych platformach. Poniższy kod pokazuje, jak wysłać powiadomienie szablonu:

```javascript
// Define the template payload.
var payload = '{"messageParam": "This is a template payload."}';

// Only do the push if configured.
if (context.push) {
    // Send a template notification.
    context.push.send(null, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

### <a name="send-push-notifications-to-an-authenticated-user-by-using-tags"></a><a name="push-user"></a>Wysyłanie powiadomień wypychanych do uwierzytelnionego użytkownika przy użyciu tagów
Gdy uwierzytelniony użytkownik rejestruje powiadomienia wypychane, tag identyfikatora użytkownika jest automatycznie dodawany do rejestracji. Za pomocą tego tagu można wysyłać powiadomienia wypychane do wszystkich urządzeń zarejestrowanych przez określonego użytkownika. Poniższy kod pobiera identyfikator SID użytkownika, który wysyła żądanie i wysyła powiadomienie wypychanie szablonu do każdej rejestracji urządzenia dla tego użytkownika:

```javascript
// Only do the push if configured.
if (context.push) {
    // Send a notification to the current user.
    context.push.send(context.user.id, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

Podczas rejestrowania powiadomień wypychanych od uwierzytelnionego klienta upewnij się, że uwierzytelnianie zostało zakończone przed podjęciem próby rejestracji.

## <a name="custom-apis"></a><a name="CustomAPI"></a>Niestandardowe interfejsy API

### <a name="define-a-custom-api"></a><a name="howto-customapi-basic"></a>Definiowanie niestandardowego interfejsu API

Oprócz interfejsu API dostępu do `/tables` danych za pośrednictwem punktu końcowego aplikacje mobilne mogą zapewnić niestandardowe pokrycie interfejsu API. Niestandardowe interfejsy API są definiowane w sposób podobny do definicji tabeli i mogą uzyskać dostęp do wszystkich tych samych obiektów, w tym uwierzytelniania.

Jeśli chcesz używać uwierzytelniania usługi app service przy użyciu niestandardowego interfejsu API, należy najpierw skonfigurować uwierzytelnianie usługi App Service w [portalu Azure.] Aby uzyskać więcej informacji, zobacz przewodnik konfiguracji dostawcy tożsamości, którego zamierzasz użyć:

* [Konfigurowanie uwierzytelniania usługi Azure Active Directory]
* [Konfigurowanie uwierzytelniania na Facebooku]
* [Konfigurowanie uwierzytelniania Google]
* [Konfigurowanie uwierzytelniania firmy Microsoft]
* [Konfigurowanie uwierzytelniania twitterowego]

Niestandardowe interfejsy API są definiowane w taki sam sposób jak interfejs API tabel:

1. Tworzenie `api` katalogu.
1. Utwórz plik JavaScript definicji interfejsu API w `api` katalogu.
1. Użyj metody importu, `api` aby zaimportować katalog.

Oto definicja prototypu interfejsu API oparta na przykładzie podstawowej aplikacji, który był używany wcześniej:

```javascript
var express = require('express'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Import the custom API.
mobile.api.import('./api');

// Add the Mobile API so it is accessible as a Web API.
app.use(mobile);

// Start listening on HTTP
app.listen(process.env.PORT || 3000);
```

Weźmy przykładowy interfejs API, który zwraca `Date.now()` datę serwera przy użyciu metody. Oto plik api/date.js:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};

module.exports = api;
```

Każdy parametr jest jednym ze standardowych czasowników RESTful: GET, POST, PATCH lub DELETE. Metoda jest standardową funkcją [oprogramowania pośredniczącego ExpressJS,] która wysyła wymagane dane wyjściowe.

### <a name="require-authentication-for-access-to-a-custom-api"></a><a name="howto-customapi-auth"></a>Wymagaj uwierzytelniania w celu uzyskania dostępu do niestandardowego interfejsu API

SDK aplikacji mobilnych implementuje uwierzytelnianie w `tables` taki sam sposób zarówno dla punktu końcowego, jak i niestandardowych interfejsów API. Aby dodać uwierzytelnianie do interfejsu API opracowanego w poprzedniej sekcji, dodaj właściwość: `access`

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};
// All methods must be authenticated.
api.access = 'authenticated';

module.exports = api;
```

Można również określić uwierzytelnianie w określonych operacjach:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    }
};
// The GET methods must be authenticated.
api.get.access = 'authenticated';

module.exports = api;
```

Ten sam token, który `tables` jest używany dla punktu końcowego musi być używany dla niestandardowych interfejsów API, które wymagają uwierzytelniania.

### <a name="handle-large-file-uploads"></a><a name="howto-customapi-auth"></a>Obsługa przekazywania dużych plików

Mobile Apps SDK używa [oprogramowania pośredniczącego analizatora treści](https://github.com/expressjs/body-parser) do akceptowania i dekodowania zawartości treści w przesłaniu. Można wstępnie skonfigurować analizator treści, aby zaakceptować większe przekazywanie plików:

```javascript
var express = require('express'),
    bodyParser = require('body-parser'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Set up large body content handling.
app.use(bodyParser.json({ limit: '50mb' }));
app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

// Import the custom API.
mobile.api.import('./api');

// Add the Mobile API so it is accessible as a Web API.
app.use(mobile);

// Start listening on HTTP.
app.listen(process.env.PORT || 3000);
```

Plik jest base-64 zakodowany przed transmisją. To kodowanie zwiększa rozmiar rzeczywistego przekazywania (i rozmiar, który należy uwzględnić).

### <a name="execute-custom-sql-statements"></a><a name="howto-customapi-sql"></a>Wykonywanie niestandardowych instrukcji SQL

Zestaw SDK aplikacji mobilnych umożliwia dostęp do całego kontekstu za pośrednictwem obiektu żądania. Parametryzowane instrukcje SQL można łatwo wykonać do dostawcy zdefiniowanych danych:

```javascript
var api = {
    get: function (request, response, next) {
        // Check for parameters. If not there, pass on to a later API call.
        if (typeof request.params.completed === 'undefined')
            return next();

        // Define the query. Anything that the mssql
        // driver can handle is allowed.
        var query = {
            sql: 'UPDATE TodoItem SET complete=@completed',
            parameters: [{
                completed: request.params.completed
            }]
        };

        // Execute the query. The context for Mobile Apps is available through
        // request.azureMobile. The data object contains the configured data provider.
        request.azureMobile.data.execute(query)
        .then(function (results) {
            response.json(results);
        });
    }
};

api.get.access = 'authenticated';
module.exports = api;
```

## <a name="debugging"></a><a name="Debugging"></a>Debugowanie

### <a name="debug-diagnose-and-troubleshoot-mobile-apps"></a><a name="howto-diagnostic-logs"></a>Debugowanie, diagnozowanie i rozwiązywanie problemów z aplikacjami mobilnymi

Usługa Azure App Service udostępnia kilka technik debugowania i rozwiązywania problemów dla aplikacji Node.js.
Aby rozpocząć rozwiązywanie problemów z zapleczem aplikacji mobilnych Node.js, zobacz następujące artykuły:

* [Monitorowanie usługi aplikacji platformy Azure]
* [Włączanie rejestrowania diagnostycznego w usłudze Azure App Service]
* [Rozwiązywanie problemów z usługą Azure App Service w programie Visual Studio]

Aplikacje Node.js mają dostęp do szerokiej gamy narzędzi dziennika diagnostycznego. Wewnętrznie mobile Apps Node.js SDK używa [Winston] do rejestrowania diagnostycznego. Rejestrowanie jest automatycznie włączane po włączeniu trybu `MS_DebugMode` debugowania lub ustawieniu ustawienia aplikacji na true w [witrynie Azure portal]. Wygenerowane dzienniki są wyświetlane w dziennikach diagnostycznych w [witrynie Azure portal].

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Szybki start klienta systemu Android]: app-service-mobile-android-get-started.md
[Apache Cordova Klient Szybki start]: app-service-mobile-cordova-get-started.md
[Szybki start klienta systemu iOS]: app-service-mobile-ios-get-started.md
[Szybki start klienta platformy Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
[Szybki start klienta platformy Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Szybki start klienta platformy Xamarin.Forms]: app-service-mobile-xamarin-forms-get-started.md
[Szybki start klienta sklepu Windows]: app-service-mobile-windows-store-dotnet-get-started.md
[synchronizacja danych w trybie offline]: app-service-mobile-offline-data-sync.md
[Konfigurowanie uwierzytelniania usługi Azure Active Directory]: ../app-service/configure-authentication-provider-aad.md
[Konfigurowanie uwierzytelniania na Facebooku]: ../app-service/configure-authentication-provider-facebook.md
[Konfigurowanie uwierzytelniania Google]: ../app-service/configure-authentication-provider-google.md
[Konfigurowanie uwierzytelniania firmy Microsoft]: ../app-service/configure-authentication-provider-microsoft.md
[Konfigurowanie uwierzytelniania twitterowego]: ../app-service/configure-authentication-provider-twitter.md
[Przewodnik po wdrażaniu usługi Azure App Service]: ../app-service/deploy-local-git.md
[Monitorowanie usługi aplikacji platformy Azure]: ../app-service/web-sites-monitor.md
[Włączanie rejestrowania diagnostycznego w usłudze Azure App Service]: ../app-service/troubleshoot-diagnostic-logs.md
[Rozwiązywanie problemów z usługą Azure App Service w programie Visual Studio]: ../app-service/troubleshoot-dotnet-visual-studio.md
[określanie wersji węzła]: ../nodejs-specify-node-version-azure-apps.md
[korzystanie z modułów węzłów]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: https://expressjs.com/
[Swagger]: https://swagger.io/

[Portal Azure]: https://portal.azure.com/
[OData]: https://www.odata.org
[Obietnica]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp próbki na GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[przykład todo na GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[katalog próbek w usłudze GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS (Pytania]: https://github.com/Azure/queryjs
[Narzędzia node.js 1.1 dla programu Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[Pakiet mssql Node.js]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[Oprogramowanie pośredniczące ExpressJS]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
