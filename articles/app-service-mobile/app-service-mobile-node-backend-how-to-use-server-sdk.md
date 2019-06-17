---
title: Jak pracować z zestawem SDK serwera zaplecza Node.js dla aplikacji mobilnych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pracować z zestawem SDK serwera zaplecza Node.js dla usługi Azure App Service Mobile Apps.
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: elamalani
editor: ''
ms.assetid: e7d97d3b-356e-4fb3-ba88-38ecbda5ea50
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 831f6b4bdc99e63859b390f8a9bb88d74301284e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128104"
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>Jak używać zestawu SDK środowiska Node.js w aplikacji mobilnej

[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Ten artykuł zawiera szczegółowe informacje i przykłady pokazujące, jak pracować w środowisku Node.js zaplecze funkcji Mobile Apps usługi Azure App Service.

## <a name="Introduction"></a>Wprowadzenie

Mobile Apps oferuje możliwość dodawania dane zoptymalizowane pod kątem mobile dostęp do interfejsu API sieci Web do aplikacji sieci web. Zestaw SDK usługi Mobile Apps jest udostępniana dla aplikacji sieci web platformy ASP.NET i Node.js. Zestaw SDK udostępnia następujące operacje:

* Operacje tabeli (Odczyt, wstawianie, aktualizowanie i usuwanie) uzyskać dostęp do danych
* Operacje niestandardowego interfejsu API

Obie operacje zapewnienia uwierzytelniania wszystkich dostawców tożsamości, które umożliwia usłudze Azure App Service. Ci dostawcy obejmują dostawców tożsamości społecznościowych, takich jak Facebook, Twitter, Google i Microsoft, a także usługi Azure Active Directory dla tożsamości organizacji.

Możesz znaleźć przykłady dla każdego przypadku użycia w [Katalog przykładów w witrynie GitHub].

## <a name="supported-platforms"></a>Obsługiwane platformy

Zestaw SDK usługi Mobile Apps Node.js obsługuje bieżącej wersji LTS węzła lub nowszy. Obecnie najnowszej wersji LTS jest v4.5.0 węzła. Inne wersje węzła może działać, ale nie są obsługiwane.

Zestaw SDK usługi Mobile Apps Node.js obsługuje dwa sterowniki bazy danych: 

* Sterownik mssql węzła obsługuje bazy danych SQL Azure i lokalnego wystąpienia programu SQL Server.  
* Sterownik sqlite3 obsługuje tylko jedno wystąpienie bazy danych SQLite.

### <a name="howto-cmdline-basicapp"></a>Tworzenie podstawowego środowiska Node.js zaplecza przy użyciu wiersza polecenia

Jako aplikacja ExpressJS rozpoczyna się co zaplecze środowiska Node.js z aplikacji mobilnej. ExpressJS jest najbardziej popularnych struktura usługi sieci web dostępnych dla środowiska Node.js. Możesz utworzyć podstawową [Express] aplikacji w następujący sposób:

1. Polecenie lub okno programu PowerShell Utwórz katalog projektu:

        mkdir basicapp

1. Uruchom `npm init` zainicjować struktury pakietu:

        cd basicapp
        npm init

   `npm init` Polecenia prosi zestaw pytań można zainicjować projektu. Zobacz przykładowe dane wyjściowe:

   ![Dane wyjściowe init npm][0]

1. Zainstaluj `express` i `azure-mobile-apps` biblioteki z repozytorium npm:

        npm install --save express azure-mobile-apps

1. Tworzenie pliku app.js do zaimplementowania podstawowego serwera przenośnych:

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

Ta aplikacja tworzy mobile są zoptymalizowane pod kątem interfejsu API sieci Web za pomocą pojedynczego punktu końcowego (`/tables/TodoItem`) zapewniającej nieuwierzytelniony dostęp do magazynu danych SQL przy użyciu schematu dynamicznego. Nadaje się do korzystania z biblioteki klienta, przewodniki Szybki Start:

* [Przewodnik Szybki Start kliencką dla systemu android]
* [Przewodnik Szybki Start klienta Apache Cordova]
* [iOS Client quickstart]
* [Przewodnik Szybki Start Windows Store klienta]
* [Xamarin.iOS Client quickstart]
* [Przewodnik Szybki Start klienta platformy Xamarin.Android]
* [Przewodnik Szybki Start klienta interfejsu Xamarin.Forms]

Możesz znaleźć kod dla tej aplikacji podstawowe w [przykład basicapp w witrynie GitHub].

### <a name="howto-vs2015-basicapp"></a>Tworzenie zaplecza Node.js przy użyciu programu Visual Studio 2015

Program Visual Studio 2015 wymaga rozszerzenia do tworzenia aplikacji Node.js w środowisku IDE. Aby rozpocząć, należy zainstalować [Node.js Tools 1.1 dla programu Visual Studio]. Po zakończeniu instalacji, należy utworzyć aplikację 4.x Express:

1. Otwórz **nowy projekt** okno dialogowe (z **pliku** > **New** > **projektu**).
1. Expand **Templates** > **JavaScript** > **Node.js**.
1. Wybierz **aplikacji podstawowe Azure node.js w środowisku Express 4**.
1. Wprowadź nazwę projektu. Kliknij przycisk **OK**.

   ![Nowy projekt programu Visual Studio 2015][1]
1. Kliknij prawym przyciskiem myszy **npm** a następnie wybierz węzeł **zainstalować nowe pakiety npm**.
1. Może być konieczne odświeżenie wykazu programu npm, po tworzenie pierwszej aplikacji Node.js. Wybierz **Odśwież** w razie potrzeby.
1. Wprowadź **azure-mobile-apps** w polu wyszukiwania. Wybierz **usługi azure mobile apps 2.0.0** pakietu, a następnie wybierz **zainstaluj pakiet**.

   ![Instaluj nowe pakiety npm][2]
1. Wybierz polecenie **Zamknij**.
1. Otwórz plik app.js, aby dodać obsługę zestaw SDK usługi Mobile Apps. W wierszu 6 at dolnej części biblioteki `require` instrukcji, Dodaj następujący kod:

    ```javascript
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

    Co około wiersza 27 po drugiej `app.use` instrukcji, Dodaj następujący kod:

    ```javascript
    app.use('/users', users);

    // Mobile Apps initialization
    var mobile = azureMobileApps();
    mobile.tables.add('TodoItem');
    app.use(mobile);
    ```

    Zapisz plik.

1. Albo uruchamianie aplikacji lokalnie (interfejs API jest obsługiwany na `http://localhost:3000`) lub opublikuj na platformie Azure.

### <a name="create-node-backend-portal"></a>Tworzenie zaplecza Node.js przy użyciu witryny Azure portal

Można utworzyć zaplecze funkcji Mobile Apps bezpośrednio w [Azure Portal]. Można albo wykonaj następujące czynności lub utworzyć klienta i serwera razem wykonując [tworzenie aplikacji mobilnej](app-service-mobile-ios-get-started.md) samouczka. Samouczek zawiera uproszczonej wersji tych instrukcji i sprawdza się najlepiej w projektach weryfikacji koncepcji.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Wstecz w **wprowadzenie** okienku w obszarze **Utwórz tabelę interfejsu API**, wybierz **Node.js** jako język zaplecza.
Zaznacz pole **potwierdzam, że spowoduje to zastąpienie całej zawartości witryny**, a następnie wybierz pozycję **Utwórz tabelę TodoItem**.

### <a name="download-quickstart"></a>Pobierz projekt kodu szybkiego startu zaplecza Node.js przy użyciu narzędzia Git

Podczas tworzenia zaplecza funkcji Mobile Apps Node.js za pomocą aplikacji portal firmy **— szybki start** okienku projekt Node.js zostaną utworzone i wdrożone dla witryny. W portalu można dodać tabele i interfejsów API i edytować pliki kodu dla zaplecza Node.js. Umożliwia także różne narzędzia wdrażania można pobrać projektu zaplecza, aby dodać lub zmodyfikować tabele i interfejsów API i ponownie opublikować projekt. Aby uzyskać więcej informacji, zobacz [Podręcznik wdrażania usługi Azure App Service].

W poniższej procedurze użyto repozytorium Git do pobierania kodu projektu Szybki Start:

1. Instalowanie programu Git, jeśli jeszcze tego nie zrobiłeś. Kroki wymagane do zainstalowania usługi Git różnią się między systemami operacyjnymi. Dla konkretnych systemów operacyjnych dystrybucji i wskazówki dotyczące instalacji, zobacz [instalacji usługi Git](https://git-scm.com/book/en/Getting-Started-Installing-Git).
2. Zobacz [przygotowanie repozytorium](../app-service/deploy-local-git.md#prepare-your-repository) włączyć repozytorium Git dla danej witryny zaplecza. Zanotuj nazwę wdrożenia użytkownika i hasło.
3. W okienku dla zaplecza usługi Mobile Apps Zanotuj **adres URL klonowania Git** ustawienie.
4. Wykonaj `git clone` polecenia przy użyciu adresu URL klonowania Git. Wprowadź swoje hasło, gdy jest to wymagane, jak w poniższym przykładzie:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Przejdź do katalogu lokalnego (`/todolist` w powyższym przykładzie) i zwróć uwagę, że pliki projektu zostały pobrane. Zlokalizuj plik todoitem.json w `/tables` katalogu. Ten plik definiuje uprawnienia w tabeli. Ponadto można znaleźć pliku todoitem.js, w tym samym katalogu. Definiuje skrypty operacji CRUD dla tabeli.
6. Po wprowadzeniu zmian w plikach projektu, uruchom następujące polecenia, aby dodać, Przekaż, a następnie przekazać zmiany do witryny:

        $ git commit -m "updated the table script"
        $ git push origin master

   Po dodaniu nowych plików do projektu, należy najpierw uruchomić `git add .` polecenia.

Witryna jest ponownie opublikować za każdym razem, gdy nowy zestaw zatwierdzenia są wypychane do lokacji.

### <a name="howto-publish-to-azure"></a>Publikowanie zaplecza Node.js na platformie Azure

Microsoft Azure udostępnia wiele mechanizmów publikowania usługi Mobile Apps Node.js zaplecze w usłudze platformy Azure. Te mechanizmy to m.in. narzędzia wdrażania zintegrowane w programie Visual Studio, narzędzia wiersza polecenia i opcji ciągłego wdrażania, w zależności od kontroli źródła. Aby uzyskać więcej informacji, zobacz [Podręcznik wdrażania usługi Azure App Service].

Usługa Azure App Service ma poradę dla aplikacji w technologii Node.js, które należy zapoznać się przed opublikowaniem zaplecza:

* Jak [Określ wersję węzła]
* Jak [Użyj moduły node.js]

### <a name="howto-enable-homepage"></a>Włącz stronę główną dla aplikacji

Wiele aplikacji jest kombinacją aplikacji internetowych i mobilnych. ExpressJS framework służy do łączenia dwóch zestawów reguł. Czasami jednak warto tylko implementacji interfejsu dla urządzeń przenośnych. Jest przydatne do zapewnienia strony głównej, aby upewnić się, że działa usługa app service i uruchomione. Możesz podać strony głównej lub Włącz tymczasowe strony głównej. Aby włączyć tymczasowe strony głównej, należy użyć następującego kodu do utworzenia wystąpienia Mobile Apps:

```javascript
var mobile = azureMobileApps({ homePage: true });
```

Jeśli chcesz tylko tej opcji, które są dostępne podczas tworzenia lokalnie, można dodać tego ustawienia do pliku azureMobile.js.

## <a name="TableOperations"></a>Operacje tabeli

Azure-mobile-apps Node.js Server SDK udostępnia mechanizmy do udostępnienia tabel danych przechowywanych w usłudze Azure SQL Database jako interfejsu API sieci Web. Oferuje ono pięciu działań:

| Operacja | Opis |
| --- | --- |
| Pobierz /tables/*tablename* |Pobierz wszystkie rekordy w tabeli. |
| Pobierz /tables/*tablename*/:id |Pobierz określony rekord w tabeli. |
| WPIS /tables/*tablename* |Utwórz rekord w tabeli. |
| POPRAWKA /tables/*tablename*/:id |Zaktualizuj rekord w tabeli. |
| Usuń /tables/*tablename*/:id |Usuwanie rekordu w tabeli. |

Ten interfejs API sieci Web obsługuje [OData] i rozszerza schemat tabeli w celu obsługi [synchronizacja danych offline].

### <a name="howto-dynamicschema"></a>Definiowanie tabel przy użyciu schematu dynamicznego

Zanim można użyć tabeli, należy zdefiniować. Tabele można zdefiniować przy użyciu statycznych schematu (gdzie definiuje się kolumn w schemacie) lub dynamicznie (tam, gdzie zestaw SDK kontroluje schematu oparte na przychodzące żądania). Ponadto możesz kontrolować konkretnych aspektów interfejsu API sieci Web, dodając kod JavaScript do definicji.

Najlepszym rozwiązaniem, należy zdefiniować w pliku JavaScript, w każdej tabeli `tables` katalogu, a następnie użyj `tables.import()` metody, aby zaimportować tabele. Rozszerzanie przykładu Podstawowa aplikacja, może dostosować pliku app.js:

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

Definiowanie w tabeli. / tables/TodoItem.js:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here.

module.exports = table;
```

Domyślnie tabele używają schematu dynamicznego. Aby wyłączyć globalnie schemat dynamiczny, ustaw `MS_DynamicSchema` ustawienia aplikacji na wartość false w witrynie Azure portal.

Możesz znaleźć pełny przykład w [przykład zadań do wykonania w witrynie GitHub].

### <a name="howto-staticschema"></a>Definiowanie tabel przy użyciu statycznych schematu

Można jawnie zdefiniować kolumn tak, aby ujawnić za pośrednictwem interfejsu API sieci Web. Zestaw SDK środowiska Node.js usługi azure mobile aplikacje automatycznie dodaje wszystkie dodatkowe kolumny wymagane w celu synchronizacji danych w trybie offline do listy, które należy podać. Na przykład aplikacje klienckie Szybki Start wymaga tabelę zawierającą dwie kolumny: `text` (ciąg) i `complete` (wartość logiczna).  
Można zdefiniować tabelę w pliku JavaScript w definicji tabeli (znajdujący się w `tables` katalogu) w następujący sposób:

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

Jeśli zdefiniujesz tabel statycznie, musisz również wywołać `tables.initialize()` metodę w celu utworzenia schematu bazy danych przy uruchamianiu. `tables.initialize()` Metoda zwraca [promise] tak, aby usługi sieci web nie obsługiwać żądań przed jest inicjowana.

### <a name="howto-sqlexpress-setup"></a>Użyj programu SQL Server Express do rozwoju przechowywania danych na komputerze lokalnym

Zestaw SDK usługi Mobile Apps Node.js zawiera trzy opcje do obsługi danych poza pole:

* Użyj **pamięci** sterownik udostępniają magazyn przykład nietrwałe.
* Użyj **mssql** sterownika w celu zapewnienia magazynu danych programu SQL Server Express do tworzenia aplikacji.
* Użyj **mssql** sterownika w celu zapewnienia magazynu danych Azure SQL Database w środowisku produkcyjnym.

Zestaw SDK usługi Mobile Apps Node.js używa [pakiet Node.js mssql] i użyć połączenia do programu SQL Server Express i SQL Database. Ten pakiet wymaga włączenia połączeń TCP w ramach wystąpienia programu SQL Server Express.

> [!TIP]
> Sterownik pamięci nie zapewniają kompletny zestaw urządzeń do testowania. Jeśli chcesz przetestować zaplecza lokalnie, firma Microsoft zaleca korzystanie z magazynu danych programu SQL Server Express i sterownik mssql.

1. Pobierz i zainstaluj [Microsoft SQL Server 2014 Express]. Upewnij się, zainstalowanie programu SQL Server 2014 Express przy użyciu wersji narzędzia. Chyba że jawnie wymagana jest Obsługa 64-bitowych, 32-bitowej wersji zużywa mniej pamięci podczas uruchamiania.
1. Uruchom Menedżera konfiguracji programu SQL Server 2014:

   a. Rozwiń **konfiguracja sieci programu SQL Server** węzła w menu drzewa.

   b. Wybierz **protokoły dla SQLEXPRESS**.

   c. Kliknij prawym przyciskiem myszy **TCP/IP** i wybierz **Włącz**. Wybierz **OK** w podręcznym oknie dialogowym.

   d. Kliknij prawym przyciskiem myszy **TCP/IP** i wybierz **właściwości**.

   e. Wybierz **adresów IP** kartę.

   f. Znajdź **IPWszystkie** węzła. W **TCP Port** wprowadź **1433**.

      ![Skonfiguruj program SQL Server Express protokołu TCP/IP][3]

   g. Kliknij przycisk **OK**. Wybierz **OK** w podręcznym oknie dialogowym.

   h. Wybierz **usług SQL Server** w menu drzewa.

   i. Kliknij prawym przyciskiem myszy **programu SQL Server (SQLEXPRESS)** i wybierz **ponowne uruchomienie**.

   j. Zamknij Menedżera konfiguracji programu SQL Server 2014.

1. Uruchom program SQL Server 2014 Management Studio, a następnie nawiązać połączenie z lokalnym wystąpieniem programu SQL Server Express:

   1. Kliknij prawym przyciskiem myszy wystąpienie w Eksploratorze obiektów, a następnie wybierz pozycję **właściwości**.
   1. Wybierz **zabezpieczeń** strony.
   1. Upewnij się, że **tryb programu SQL Server i Windows Authentication** jest zaznaczone.
   1. Kliknij przycisk **OK**.

      ![Konfigurowanie uwierzytelniania programu SQL Server Express][4]
   1. Rozwiń **zabezpieczeń** > **logowania** w Eksploratorze obiektów.
   1. Kliknij prawym przyciskiem myszy **logowania** i wybierz **nowy identyfikator logowania**.
   1. Wprowadź nazwę logowania. Wybierz pozycję **Uwierzytelnianie programu SQL Server**. Wprowadź hasło, a następnie wprowadź to samo hasło w **Potwierdź hasło**. Hasło musi spełniać wymagania co do złożoności Windows.
   1. Kliknij przycisk **OK**.

      ![Dodaj nowego użytkownika do programu SQL Server Express][5]
   1. Kliknij prawym przyciskiem myszy nowego identyfikatora logowania, a następnie wybierz pozycję **właściwości**.
   1. Wybierz **ról serwera** strony.
   1. Zaznacz pole wyboru dla **dbcreator** roli serwera.
   1. Kliknij przycisk **OK**.
   1. Zamknij program SQL Server 2015 Management Studio.

Pamiętaj zarejestrować nazwę użytkownika i hasło, które wybrano. Może być konieczne do przypisywania dodatkowych ról serwera lub uprawnienia, w zależności od wymagań bazy danych.

Odczyty aplikacji Node.js `SQLCONNSTR_MS_TableConnectionString` zmienna środowiskowa parametrów połączenia dla tej bazy danych. Można ustawić tę zmienną w środowisku. Na przykład można użyć programu PowerShell można ustawić tę zmienną środowiskową:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Dostęp do bazy danych za pośrednictwem połączenia TCP/IP. Podaj nazwę użytkownika i hasło dla połączenia.

### <a name="howto-config-localdev"></a>Skonfiguruj projekt do tworzenia aplikacji lokalnej

Aplikacje mobilne odczytuje plik JavaScript o nazwie *azureMobile.js* z lokalnego systemu plików. Aby skonfigurować zestaw SDK usługi Mobile Apps w środowisku produkcyjnym nie należy używać tego pliku. Zamiast tego należy użyć **ustawienia aplikacji** w [Azure Portal].

Plik azureMobile.js należy wyeksportować obiektu konfiguracji. Najczęściej używane ustawienia to:

* Ustawienia bazy danych
* Ustawienia rejestrowania diagnostycznego
* Alternatywne ustawienia specyfikacji CORS

W tym przykładzie **azureMobile.js** pliku implementuje powyższych ustawień bazy danych:

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

Firma Microsoft zaleca, aby dodać **azureMobile.js** do Twojej **.gitignore** pliku (lub inne kontroli kodu źródłowego, pliku ignorowania) aby zapobiec hasła są przechowywane w chmurze. Należy zawsze konfigurować tak środowiskach produkcyjnych w **ustawienia aplikacji** w ramach [Azure Portal].

### <a name="howto-appsettings"></a>Konfigurowanie ustawień aplikacji dla aplikacji mobilnej

Większość ustawień w pliku azureMobile.js ma ustawienia aplikacji równoważne [Azure Portal]. Aby skonfigurować aplikację, używając następującej listy **ustawienia aplikacji**:

| Ustawienia aplikacji | azureMobile.js setting | Opis | Prawidłowe wartości |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |name |Nazwa aplikacji |string |
| **MS_MobileLoggingLevel** |logging.level |Minimalny poziom rejestrowania komunikatów do zarejestrowania |błąd, ostrzeżenie, informacje o verbose, debug, niemądre |
| **MS_DebugMode** |Debugowanie |Włącza lub wyłącza tryb debugowania |wartość true, false |
| **MS_TableSchema** |data.schema |Domyślna nazwa schematu dla tabel programu SQL |ciąg (domyślne: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Włącza lub wyłącza tryb debugowania |wartość true, false |
| **MS_DisableVersionHeader** |Wersja (ustawione do niezdefiniowanego) |Wyłącza nagłówka X-ZUMO-Server-Version |wartość true, false |
| **MS_SkipVersionCheck** |skipversioncheck |Wyłącza sprawdzanie wersji interfejsu API klienta |wartość true, false |

Aby skonfigurować ustawienie aplikacji:

1. Zaloguj się w witrynie [Azure Portal].
1. Wybierz **wszystkie zasoby** lub **App Services**, a następnie wybierz nazwę swojej aplikacji mobilnej.
1. **Ustawienia** domyślnie zostanie otwarte okienko. Jeśli nie wybierz opcję **ustawienia**.
1. Na **ogólne** menu, wybierz opcję **ustawienia aplikacji**.
1. Przewiń do **ustawienia aplikacji** sekcji.
1. Jeśli aplikacja ustawienie już istnieje, wybierz wartość ustawienia aplikacji, aby edytować wartość.
   Jeśli Twoje ustawienia aplikacji nie istnieje, wprowadź ustawienie aplikacji **klucz** pola i wartość w **wartość** pole.
1. Wybierz pozycję **Zapisz**.

Zmiana ustawień większość aplikacji wymaga ponownego uruchomienia tej usługi.

### <a name="howto-use-sqlazure"></a>Użyj bazy danych SQL jako dane produkcji przechowywania

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Za pomocą usługi Azure SQL Database do przechowywania danych jest identyczna w przypadku wszystkich typów aplikacji w usłudze Azure App Service. Jeśli użytkownik nie zostało zrobione już, wykonaj następujące kroki, aby utworzyć zaplecze funkcji Mobile Apps:

1. Zaloguj się w witrynie [Azure Portal].
1. W lewym górnym rogu okna, wybierz **+ nowy** przycisk > **sieci Web i mobilność** > **aplikacji mobilnej**, a następnie podaj nazwę dla zaplecza usługi Mobile Apps.
1. W **grupy zasobów** wprowadź taką samą nazwę co aplikacja.
1. Wybrano domyślny plan usługi App Service. Jeśli chcesz zmienić plan usługi App Service:

   a. Wybierz **planu usługi App Service** >  **+ Utwórz nowe**.

   b. Podaj nazwę nowego planu usługi App Service, a następnie wybierz odpowiednią lokalizację.

   c. Wybierz odpowiednią warstwę cenową dla usługi. Wybierz **Wyświetl wszystkie** do widoku bardziej cennik, takich jak **bezpłatna** i **Shared**.

   d. Kliknij przycisk **wybierz** przycisku.

   e. Ponownie **planu usługi App Service** okienku wybierz **OK**.
1. Wybierz pozycję **Utwórz**.

Inicjowanie obsługi administracyjnej aplikacji mobilnych zaplecza może potrwać kilka minut. Po funkcji Mobile Apps kopii zaaprowizowaniu zakończenia, w portalu zostanie otwarty **ustawienia** okienka zaplecze funkcji Mobile Apps.

Można połączyć z istniejącej bazy danych SQL do zaplecza funkcji Mobile Apps lub Utwórz nową bazę danych SQL. W tej sekcji utworzymy bazy danych SQL.

> [!NOTE]
> Jeśli masz już bazę danych w tej samej lokalizacji jako zaplecze funkcji Mobile Apps, zamiast tego można wybrać **Użyj istniejącej bazy danych** i następnie wybrać tę bazę danych. Korzystanie z bazy danych w innej lokalizacji nie jest zalecane z powodu większych opóźnień.

1. Nowe zaplecze funkcji Mobile Apps, wybierz **ustawienia** > **aplikacji mobilnej** > **danych** >  **+ Dodaj**.
1. W **Dodaj połączenie danych** okienku wybierz **bazy danych SQL — Skonfiguruj wymagane ustawienia** > **Utwórz nową bazę danych**. Wprowadź nazwę nowej bazy danych w **nazwa** pole.
1. Wybierz **serwera**. W **nowy serwer** okienku, wprowadź unikatową nazwę serwera w **nazwy serwera** polu, a następnie podaj odpowiedni serwer oraz hasło logowania administratora. Upewnij się, że **Zezwalaj usługom platformy azure na dostęp do serwera** jest zaznaczone. Kliknij przycisk **OK**.

   ![Tworzenie bazy danych Azure SQL Database][6]
1. W **nową bazę danych** okienku wybierz **OK**.
1. Ponownie **Dodaj połączenie danych** okienku wybierz **parametry połączenia**, a następnie wprowadź identyfikator logowania i hasło podane podczas tworzenia bazy danych. Jeśli używasz istniejącej bazy danych, należy podać poświadczenia logowania dla tej bazy danych. Kliknij przycisk **OK**.
1. Ponownie **Dodaj połączenie danych** wybierz ponownie **OK** utworzyć bazę danych.

<!--- END OF ALTERNATE INCLUDE -->

Utworzenie bazy danych może potrwać kilka minut. Użyj **powiadomienia** obszar, aby monitorować postęp wdrażania. Brak postępu aż baza danych jest wdrożona pomyślnie. Po wdrożeniu bazy danych, ciąg połączenia jest tworzona dla wystąpienia bazy danych SQL w ustawieniach aplikacji zaplecza funkcji Mobile Apps. Można wyświetlić tego ustawienia aplikacji w **ustawienia** > **ustawienia aplikacji** > **parametry połączenia**.

### <a name="howto-tables-auth"></a>Wymaganie uwierzytelniania na potrzeby dostępu do tabel

Jeśli chcesz używać uwierzytelnianie usługi App Service przy użyciu `tables` punktu końcowego, należy skonfigurować uwierzytelnianie usługi App Service w [Azure Portal] pierwszy. Aby uzyskać więcej informacji zobacz Przewodnik po konfiguracji dla dostawcy tożsamości, które będą używane:

* [Konfigurowanie uwierzytelniania usługi Azure Active Directory]
* [Konfigurowanie uwierzytelniania serwisu Facebook]
* [Konfigurowanie uwierzytelniania serwisu Google]
* [Konfigurowanie uwierzytelniania firmy Microsoft]
* [Konfigurowanie uwierzytelniania usługi Twitter]

Każda tabela ma właściwość dostępu, którego można użyć do kontrolowania dostępu do tabeli. Poniższy przykład pokazuje tabelę statycznie zdefiniowanych za pomocą wymagane jest uwierzytelnianie.

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

Właściwość dostępu może mieć jedną z trzech wartości:

* *anonimowe* wskazuje, że aplikacja kliencka może odczytywać dane bez uwierzytelniania.
* *uwierzytelniony* wskazuje, że aplikacja kliencka musi wysłać ważny token uwierzytelniania z żądaniem.
* *wyłączone* wskazuje, że ta tabela jest obecnie wyłączona.

Jeśli zdefiniowano właściwość dostęp nieuwierzytelniony dostęp jest dozwolony.

### <a name="howto-tables-getidentity"></a>Oświadczenia uwierzytelniania za pomocą tabel
Można skonfigurować różne oświadczenia, które są wymagane podczas konfigurowania uwierzytelniania. Te oświadczenia nie są zwykle dostępne za pośrednictwem `context.user` obiektu. Jednak można je pobrać za pomocą `context.user.getIdentity()` metody. `getIdentity()` Metoda zwraca promise, który jest rozpoznawany jako obiekt. Obiekt z kluczami przez metodę uwierzytelniania (`facebook`, `google`, `twitter`, `microsoftaccount`, lub `aad`).

Na przykład jeśli skonfigurowano uwierzytelnianie za pomocą konta Microsoft i żądania, których adresy e-mail oświadczenia, można dodać adres e-mail do rekordu z następującym kontrolerem tabeli:

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

Aby zobaczyć, jakie oświadczenia są dostępne, użyj przeglądarki sieci web do wyświetlania `/.auth/me` punktu końcowego witryny.

### <a name="howto-tables-disabled"></a>Wyłącz dostęp do określonej tabeli operacji

Oprócz wymienionych w tabeli, właściwość dostępu może służyć do kontrolowania poszczególnych operacji. Istnieją cztery operacje:

* `read` jest operacji Pobierz RESTful w tabeli.
* `insert` jest operacją RESTful WPIS w tabeli.
* `update` jest operacja RESTful PATCH w tabeli.
* `delete` jest operacją RESTful usuwania w tabeli.

Na przykład możesz chcieć zapewnić tabelę nieuwierzytelnione tylko do odczytu:

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

### <a name="howto-tables-query"></a>Dostosuj zapytanie, które jest używane z operacjami tabeli

Typowym wymogiem dla operacji tabela ma na celu dostarczenie ograniczony widok danych. Na przykład można dostarczyć tabelę, która zostanie oznaczony przy użyciu Identyfikatora uwierzytelnionego użytkownika w taki sposób, że można tylko odczytu lub aktualizacji własnych rekordów. W poniższej definicji tabeli oferuje tę funkcję:

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

Operacje, które są zazwyczaj uruchamiane zapytanie ma właściwość zapytania, które można dostosować za pomocą `where` klauzuli. Właściwość kwerendy jest [QueryJS] może przetworzyć obiekt, który służy do konwertowania zapytanie OData na coś czy danych zaplecza. W przypadkach proste równości (takiego jak poprzedni) możesz użyć mapy. Można również dodać określonego klauzule SQL:

```javascript
context.query.where('myfield eq ?', 'value');
```

### <a name="howto-tables-softdelete"></a>Konfigurowanie usuwania nietrwałego w tabeli

Usuwanie nietrwałe faktycznie nie powoduje usunięcia rekordów. Zamiast tego oznacza je jako usunięty w bazie danych przez ustawienie usuniętą kolumnę na wartość true. Zestaw SDK usługi Mobile Apps automatycznie usuwa wszystkie usunięte nietrwale rekordy z wyników, chyba że używa zestaw SDK klienta usługi Mobile `IncludeDeleted()`. Aby skonfigurować tabelę dla usuwania nietrwałego, ustaw `softDelete` właściwość w pliku definicji tabeli:

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

Należy określić mechanizm służący do usuwania rekordów: aplikacja kliencka, zadania WebJob, funkcję platformy Azure lub niestandardowego interfejsu API.

### <a name="howto-tables-seeding"></a>Zapełnić bazę danych danymi

Podczas tworzenia nowej aplikacji, możesz chcieć umieszczenia tabelę z danymi. Można to zrobić w pliku JavaScript w definicji tabeli w następujący sposób:

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

Wstępne wypełnianie danych odbywa się tylko wtedy, gdy zestaw SDK usługi Mobile Apps został użyty do utworzenia tabeli. Jeśli tabela już istnieje w bazie danych, żadne dane nie są wstrzykiwane do tabeli. Jeśli włączono schemat dynamiczny schemat jest wnioskowany z wprowadzonych danych.

Zaleca się, że należy jawnie wywołać `tables.initialize()` metodę, aby utworzyć tabelę, podczas uruchamiania usługi.

### <a name="Swagger"></a>Włącz obsługę programu Swagger
Mobile Apps zawiera wbudowane funkcje [Swagger] pomocy technicznej. Aby włączyć obsługę struktury Swagger, należy najpierw zainstalować interfejs użytkownika struktury swagger jako zależność:

    npm install --save swagger-ui

Następnie można włączyć obsługę programu Swagger w Konstruktorze Mobile Apps:

```javascript
var mobile = azureMobileApps({ swagger: true });
```

Możesz prawdopodobnie tylko chcesz włączyć obsługę programu Swagger w wersjach rozwoju. Można to zrobić za pomocą `NODE_ENV` ustawienia aplikacji:

```javascript
var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });
```

`swagger` Punktu końcowego znajduje się w folderze http://*twoja_witryna*.azurewebsites.net/swagger. Możesz uzyskać dostęp interfejsu użytkownika programu Swagger za pośrednictwem `/swagger/ui` punktu końcowego. Struktury Swagger generuje błąd, jeśli chcesz wymagać uwierzytelniania na całej aplikacji. Aby uzyskać najlepsze wyniki, wybierz umożliwia nieuwierzytelnione żądania w ustawieniach usługi Azure App Service uwierzytelniania/autoryzacji i następnie kontrolować uwierzytelniania za pomocą `table.access` właściwości.

Można również dodać opcję struktury Swagger do pliku azureMobile.js, jeśli chcesz tylko Obsługa struktury Swagger do tworzenia lokalnie.

## <a name="a-namepushpush-notifications"></a><a name="push"/>Powiadomienia wypychane

Aplikacje mobilne integruje się z usługą Azure Notification Hubs, co umożliwia wysyłanie ukierunkowanych powiadomień wypychanych do milionów urządzeń we wszystkich głównych platform. Korzystając z usługi Notification Hubs można wysyłać powiadomienia wypychane do systemów iOS, Android i Windows urządzenia. Aby dowiedzieć się więcej na temat wszystko, co można zrobić z usługą Notification Hubs, zobacz [usługi Notification Hubs — omówienie](../notification-hubs/notification-hubs-push-notification-overview.md).

### <a name="send-push"></a>Wysyłanie powiadomień wypychanych

Poniższy kod przedstawia sposób użycia `push` obiektu, aby wysłać powiadomienie rozgłaszanie powiadomień wypychanych do urządzeń zarejestrowanych dla systemu iOS:

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

Tworząc szablon wypychania rejestracji od klienta, można zamiast tego wysłać szablon wiadomości wypychane do urządzeń na wszystkich obsługiwanych platformach. Poniższy kod przedstawia sposób wysłania powiadomienia szablonu:

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

### <a name="push-user"></a>Wysyłać powiadomienia wypychane do uwierzytelnionego użytkownika przy użyciu tagów
Gdy uwierzytelniony użytkownik rejestruje powiadomienia wypychane, tag identyfikator użytkownika jest automatycznie dodawany do rejestracji. Za pomocą tego tagu, można wysyłać powiadomienia wypychane do wszystkich urządzeń zarejestrowanych przez określonego użytkownika. Poniższy kod pobiera identyfikator SID użytkownika, który wysłał żądanie i wysyła powiadomienie wypychane szablonu do każdej rejestracji urządzeń dla tego użytkownika:

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

Podczas rejestracji w przypadku powiadomień wypychanych z uwierzytelnionego klienta upewnij się, że zakończeniem uwierzytelniania przed próbą rejestracji.

## <a name="CustomAPI"></a> Niestandardowe interfejsy API

### <a name="howto-customapi-basic"></a>Definiowanie niestandardowego interfejsu API

Oprócz interfejsu API dostępu do danych za pośrednictwem `/tables` punktu końcowego, Mobile Apps może zapewnić niestandardowego interfejsu API pokrycia. Niestandardowe interfejsy API są definiowane w sposób podobny do definicji tabeli i mogą uzyskiwać dostęp do tych samych urządzeń, w tym uwierzytelnianie.

Jeśli chcesz uwierzytelnianie usługi App Service za pomocą niestandardowego interfejsu API, należy skonfigurować uwierzytelnianie usługi App Service w [Azure Portal] pierwszy. Aby uzyskać więcej informacji zobacz Przewodnik po konfiguracji dla dostawcy tożsamości, które będą używane:

* [Konfigurowanie uwierzytelniania usługi Azure Active Directory]
* [Konfigurowanie uwierzytelniania serwisu Facebook]
* [Konfigurowanie uwierzytelniania serwisu Google]
* [Konfigurowanie uwierzytelniania firmy Microsoft]
* [Konfigurowanie uwierzytelniania usługi Twitter]

Niestandardowe interfejsy API są zdefiniowane w podobny sposób jak interfejsu API tabel:

1. Utwórz `api` katalogu.
1. Utwórz plik JavaScript definicji interfejsu API w `api` katalogu.
1. Importowanie przy użyciu metody importowania `api` katalogu.

W tym miejscu jest prototypem definicji interfejsu API oparte na przykład podstawowa aplikacja, której używaliśmy wcześniej:

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

Spójrzmy na przykład interfejsu API, który zwraca datę serwera przy użyciu `Date.now()` metody. Poniżej przedstawiono plik api/date.js:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};

module.exports = api;
```

Każdy parametr jest jednym z standardowych czasowników RESTful: Pobierz, POST, PATCH lub usuwanie. Metoda jest standardem [ExpressJS middleware] funkcja, która wysyła wymagane dane wyjściowe.

### <a name="howto-customapi-auth"></a>Wymaganie uwierzytelniania na potrzeby dostępu do niestandardowego interfejsu API

Zestaw SDK usługi Mobile Apps implementuje uwierzytelniania w taki sam sposób dla obu `tables` punktu końcowego i niestandardowych interfejsów API. Aby dodać uwierzytelnianie do interfejsu API, opracowane w poprzedniej sekcji, Dodaj `access` właściwości:

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

Można również określić uwierzytelniania na określonych operacji:

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

Ten sam token, który służy do `tables` punktu końcowego musi być używany dla niestandardowych interfejsów API, która wymaga uwierzytelniania.

### <a name="howto-customapi-auth"></a>Obsługi przekazywania dużych plików

Zestaw SDK usługi Mobile Apps używa [analizator treści, oprogramowanie pośredniczące](https://github.com/expressjs/body-parser) akceptować i zdekodować treść w swoje zgłoszenie. Można wstępnie skonfigurować analizator treści do przyjęcia przekazuje większy plik:

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

Plik jest kodowanie przed transmisją base-64. To kodowanie zwiększa rozmiar rzeczywisty przekazywania (i rozmiar, które należy uwzględnić).

### <a name="howto-customapi-sql"></a>Wykonywanie instrukcji SQL niestandardowe

Zestaw SDK usługi Mobile Apps umożliwia dostęp do całego kontekstu za pośrednictwem obiektu żądania. Można łatwo wykonywać sparametryzowanych instrukcji SQL do dostawcy danych:

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

## <a name="Debugging"></a>Debugowanie, łatwe tabele i łatwe interfejsy API

### <a name="howto-diagnostic-logs"></a>Debugowanie, diagnozowanie i rozwiązywanie problemów z aplikacji mobilnych

Usługa Azure App Service oferuje kilka debugowania i techniki rozwiązywania problemów dotyczące aplikacji Node.js.
Aby rozpocząć pracę przy rozwiązywaniu problemów z zapleczem aplikacji mobilnej Node.js, zobacz następujące artykuły:

* [Monitorowanie usługi Azure App Service]
* [Włącz rejestrowanie diagnostyczne w usłudze Azure App Service]
* [Rozwiązywanie problemów z usługi Azure App Service w programie Visual Studio]

Aplikacje node.js mają dostęp do szerokiej gamy narzędzi dzienników diagnostycznych. Zestaw SDK usługi Mobile Apps Node.js używa wewnętrznie, [Winston] rejestrowanie w celach diagnostycznych. Po włączeniu debugowania jest automatycznie włączone rejestrowanie tryb lub zestawu `MS_DebugMode` ustawienia aplikacji na wartość true w [Azure Portal]. Wygenerowany dzienników pojawia się w dziennikach diagnostycznych w [Azure Portal].

### <a name="in-portal-editing"></a><a name="work-easy-tables"></a>Praca z łatwych tabel w witrynie Azure portal

Łatwe tabele umożliwia tworzenie i Praca z tabelami bezpośrednio w portalu. Możesz przekazać zestawu danych do łatwych tabel w formacie CSV. Należy pamiętać, że nie można użyć nazwy właściwości (w zestawie danych CSV), będącymi w konflikcie z nazwami właściwości systemu zaplecze funkcji Mobile Apps. Nazwy właściwości systemu są następujące:
* createdAt
* updatedAt
* usunięte
* version

Operacje tabeli mogą edytować za pomocą Edytor usługi App Service. Po wybraniu **łatwych tabel** w ustawieniach witryny zaplecza może Dodawanie, modyfikowanie lub usuwanie tabeli. Można również zobaczyć dane w tabeli.

![Praca z łatwych tabel](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

Następujące polecenia są dostępne na pasku poleceń dla tabeli:

* **Zmień uprawnienia**: Zmodyfikować uprawnienia do odczytu, wstawianie, aktualizowanie i usuwanie operacji względem tabeli.
 Aby zezwolić na dostęp anonimowy, aby wymagać uwierzytelniania lub wyłącz dostęp do operacji są opcje.
* **Edytuj skrypt**: Plik skryptu w tabeli jest otwarty w Edytor usługi App Service.
* **Zarządzanie schematami**: Dodawanie lub usuwanie kolumn lub zmienić indeksu tabeli.
* **Wyczyść tabelę**: Obetnij istniejącej tabeli, usuwając wszystkie wiersze danych, ale pozostawiając schematu.
* **Usuń wiersze**: Usuń poszczególne wiersze danych.
* **Wyświetl podgląd dzienników przesyłanych strumieniowo**: Podłącz do przesyłania strumieniowego usługi rejestrowania dla witryny.

### <a name="work-easy-apis"></a>Praca z łatwe interfejsy API w witrynie Azure portal

Łatwe interfejsy API umożliwia tworzenie i Praca z niestandardowych interfejsów API po prawej stronie w portalu. Edytor usługi App Service można edytować skrypty interfejsu API.

Po wybraniu **łatwe interfejsy API** w ustawieniach witryny zaplecza może Dodawanie, modyfikowanie lub usuwanie niestandardowego punktu końcowego interfejsu API.

![Praca z łatwe interfejsy API](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

W portalu możesz zmienić uprawnienia dostępu dla akcji HTTP, Edytuj plik skryptu interfejsu API w Edytor usługi App Service lub Wyświetl dzienniki przesyłania strumieniowego.

### <a name="online-editor"></a>Edytowanie kodu w Edytor usługi App Service

Za pomocą witryny Azure portal, możesz edytować pliki skryptów zaplecza Node.js w Edytor usługi App Service bez konieczności pobierania projektu na komputerze lokalnym. Aby edytować pliki skryptów w edytorze online:

1. W okienku wybierz opcję dla zaplecza usługi Mobile Apps **wszystkie ustawienia** > albo **łatwych tabel** lub **łatwe interfejsy API**. Wybierz tabelę lub interfejsu API, a następnie wybierz **Przeprowadź edycję skryptu**. Plik skryptu zostanie otwarty w edytorze usługi aplikacji.

   ![App Service Editor](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)
1. Wprowadź zmiany do pliku kodu w edytorze online. Zmiany są zapisywane automatycznie podczas pisania.

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Przewodnik Szybki Start kliencką dla systemu android]: app-service-mobile-android-get-started.md
[Przewodnik Szybki Start klienta Apache Cordova]: app-service-mobile-cordova-get-started.md
[iOS Client quickstart]: app-service-mobile-ios-get-started.md
[Xamarin.iOS Client quickstart]: app-service-mobile-xamarin-ios-get-started.md
[Przewodnik Szybki Start klienta platformy Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Przewodnik Szybki Start klienta interfejsu Xamarin.Forms]: app-service-mobile-xamarin-forms-get-started.md
[Przewodnik Szybki Start Windows Store klienta]: app-service-mobile-windows-store-dotnet-get-started.md
[Synchronizacja danych offline]: app-service-mobile-offline-data-sync.md
[Konfigurowanie uwierzytelniania usługi Azure Active Directory]: ../app-service/configure-authentication-provider-aad.md
[Konfigurowanie uwierzytelniania serwisu Facebook]: ../app-service/configure-authentication-provider-facebook.md
[Konfigurowanie uwierzytelniania serwisu Google]: ../app-service/configure-authentication-provider-google.md
[Konfigurowanie uwierzytelniania firmy Microsoft]: ../app-service/configure-authentication-provider-microsoft.md
[Konfigurowanie uwierzytelniania usługi Twitter]: ../app-service/configure-authentication-provider-twitter.md
[Podręcznik wdrażania usługi Azure App Service]: ../app-service/deploy-local-git.md
[Monitorowanie usługi Azure App Service]: ../app-service/web-sites-monitor.md
[Włącz rejestrowanie diagnostyczne w usłudze Azure App Service]: ../app-service/troubleshoot-diagnostic-logs.md
[Rozwiązywanie problemów z usługi Azure App Service w programie Visual Studio]: ../app-service/troubleshoot-dotnet-visual-studio.md
[Określ wersję węzła]: ../nodejs-specify-node-version-azure-apps.md
[Użyj moduły node.js]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: https://expressjs.com/
[Swagger]: https://swagger.io/

[Azure Portal]: https://portal.azure.com/
[OData]: https://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[przykład basicapp w witrynie GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[przykład zadań do wykonania w witrynie GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[Katalog przykładów w witrynie GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 dla programu Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[Pakiet Node.js MSSQL]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS middleware]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
