---
title: Współpraca z zestawem SDK serwera zaplecza Node. js
description: Dowiedz się, jak współpracować z zestawem SDK serwera zaplecza Node. js dla Azure App Service Mobile Apps.
ms.assetid: e7d97d3b-356e-4fb3-ba88-38ecbda5ea50
ms.tgt_pltfrm: mobile-multiple
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: 566b6db829c9694a7e5e83cc25695e71ada0bc8d
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461457"
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>Jak używać zestawu SDK Mobile Apps Node. js

[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Ten artykuł zawiera szczegółowe informacje i przykłady pokazujące, jak korzystać z zaplecza Node. js w funkcji Mobile Apps Azure App Service.

## <a name="Introduction"></a>Wprowadzenie

Mobile Apps oferuje możliwość dodawania interfejsu API sieci Web dostępu do danych zoptymalizowanego pod kątem urządzeń przenośnych do aplikacji sieci Web. Zestaw Mobile Apps SDK jest dostępny dla aplikacji sieci Web ASP.NET i Node. js. Zestaw SDK udostępnia następujące operacje:

* Operacje tabeli (odczyt, wstawianie, aktualizowanie, usuwanie) na potrzeby dostępu do danych
* Niestandardowe operacje interfejsu API

Obie operacje zapewniają uwierzytelnianie między wszystkimi dostawcami tożsamości, których Azure App Service zezwala. Ci dostawcy obejmują dostawców tożsamości społecznościowych, takich jak Facebook, Twitter, Google i Microsoft, a także Azure Active Directory na potrzeby tożsamości przedsiębiorstwa.

Przykłady dla każdego przypadku użycia można znaleźć w [Katalog przykładów w witrynie GitHub].

## <a name="supported-platforms"></a>Obsługiwane platformy

Zestaw SDK Mobile Apps Node. js obsługuje bieżącą wersję LTS węzła lub nowszą. Obecnie Najnowsza wersja LTS to Node v 4.5.0. Inne wersje węzła mogą funkcjonować, ale nie są obsługiwane.

Zestaw SDK Mobile Apps Node. js obsługuje dwa sterowniki baz danych: 

* Sterownik MSSQL w węźle obsługuje Azure SQL Database i lokalne wystąpienia SQL Server.  
* Sterownik SQLITE3 obsługuje bazy danych programu SQLite tylko w jednym wystąpieniu.

### <a name="howto-cmdline-basicapp"></a>Tworzenie podstawowego zaplecza Node. js przy użyciu wiersza polecenia

Każdy Mobile Apps zaplecza Node. js jest uruchamiany jako aplikacja ExpressJS. ExpressJS jest najpopularniejszą strukturą usług sieci Web dostępną dla środowiska Node. js. Podstawową aplikację [Express] można utworzyć w następujący sposób:

1. W oknie polecenia lub programu PowerShell Utwórz katalog dla projektu:

        mkdir basicapp

1. Uruchom `npm init`, aby zainicjować strukturę pakietu:

        cd basicapp
        npm init

   Polecenie `npm init` żąda zestawu pytań do zainicjowania projektu. Zobacz przykładowe dane wyjściowe:

   ![Dane wyjściowe init npm][0]

1. Zainstaluj biblioteki `express` i `azure-mobile-apps` z repozytorium npm:

        npm install --save express azure-mobile-apps

1. Utwórz plik App. js w celu zaimplementowania podstawowego serwera mobilnego:

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

Ta aplikacja tworzy interfejs API sieci Web zoptymalizowany pod kątem urządzeń przenośnych z jednym punktem końcowym (`/tables/TodoItem`), który zapewnia nieuwierzytelniony dostęp do podstawowego magazynu danych SQL przy użyciu schematu dynamicznego. Jest to odpowiednie dla przewodników szybki start dotyczących biblioteki klienta:

* [Klient systemu Android — szybki start]
* [Apache Cordova — Szybki Start]
* [Klient systemu iOS — Szybki Start]
* [Klient sklepu Windows — szybki start]
* [Klient platformy Xamarin. iOS — Szybki Start]
* [Klient platformy Xamarin. Android — szybki start]
* [Samouczek szybki start dla klienta Xamarin. Forms]

Kod tej aplikacji podstawowej można znaleźć w [przykład basicapp w witrynie GitHub].

### <a name="howto-vs2015-basicapp"></a>Tworzenie zaplecza Node. js przy użyciu programu Visual Studio 2015

Program Visual Studio 2015 wymaga rozszerzenia do tworzenia aplikacji node. js w środowisku IDE. Aby rozpocząć, zainstaluj [Narzędzia Node. js Tools 1,1 dla programu Visual Studio]. Po zakończeniu instalacji Utwórz aplikację Express 4. x:

1. Otwórz okno dialogowe **Nowy projekt** (z **pliku** > **nowym** > **projekcie**).
1. Rozwiń **szablon** > **JavaScript** > **Node. js**.
1. Wybierz **podstawową aplikację Azure Node. js Express 4**.
1. Wypełnij pole Nazwa projektu. Kliknij przycisk **OK**.

   ![Nowy projekt programu Visual Studio 2015][1]
1. Kliknij prawym przyciskiem myszy węzeł **npm** i wybierz pozycję **Instaluj nowe pakiety npm**.
1. Może być konieczne odświeżenie wykazu npm po utworzeniu pierwszej aplikacji node. js. W razie potrzeby wybierz pozycję **Odśwież** .
1. W polu wyszukiwania wprowadź wartość **Azure-Mobile-Apps** . Wybierz pakiet **2.0.0 Azure-Mobile-Apps** , a następnie wybierz pozycję **Zainstaluj pakiet**.

   ![Instalowanie nowych pakietów npm][2]
1. Wybierz polecenie **Zamknij**.
1. Otwórz plik App. js, aby dodać obsługę zestawu SDK Mobile Apps. W wierszu 6 w dolnej części instrukcji `require` biblioteki Dodaj następujący kod:

    ```javascript
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

    W około wierszu 27 po innych instrukcjach `app.use` Dodaj następujący kod:

    ```javascript
    app.use('/users', users);

    // Mobile Apps initialization
    var mobile = azureMobileApps();
    mobile.tables.add('TodoItem');
    app.use(mobile);
    ```

    Zapisz plik.

1. Uruchom aplikację lokalnie (interfejs API jest obsługiwany w `http://localhost:3000`) lub Opublikuj na platformie Azure.

### <a name="create-node-backend-portal"></a>Tworzenie zaplecza środowiska Node. js przy użyciu Azure Portal

W [Azure Portal]można utworzyć Mobile Apps zaplecza. Możesz wykonać następujące kroki lub utworzyć klienta oraz serwer, wykonując instrukcje opisane w samouczku [Tworzenie aplikacji mobilnej](app-service-mobile-ios-get-started.md) . Samouczek zawiera uproszczoną wersję tych instrukcji i jest najlepszy dla projektów sprawdzających koncepcję.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Wróć do okienka **wprowadzenie** , w obszarze **Utwórz tabelę interfejsu API**wybierz pozycję **Node. js** jako język zaplecza.
Zaznacz pole wyboru **potwierdzam, że spowoduje to zastąpienie całej zawartości witryny**, a następnie wybierz pozycję **Utwórz tabelę TodoItem**.

### <a name="download-quickstart"></a>Pobieranie projektu kodu szybkiego startu środowiska Node. js przy użyciu narzędzia Git

Podczas tworzenia programu Node. js Mobile Apps zaplecza przy użyciu okienka **szybkiego uruchamiania** portalu, projekt node. js zostanie utworzony dla Ciebie i wdrożony w witrynie. W portalu można dodawać tabele i interfejsy API oraz edytować pliki kodu dla zaplecza Node. js. Można również użyć różnych narzędzi wdrażania do pobrania projektu zaplecza, aby można było dodać lub zmodyfikować tabele i interfejsy API, a następnie ponownie opublikować projekt. Aby uzyskać więcej informacji, zobacz [Przewodnik wdrażania Azure App Service].

Poniższa procedura używa repozytorium git do pobrania kodu projektu szybkiego startu:

1. Zainstaluj program git, jeśli jeszcze tego nie zrobiono. Kroki wymagane do zainstalowania usługi git różnią się w zależności od systemów operacyjnych. Aby poznać dystrybucje i wskazówki dotyczące systemu operacyjnego, zobacz [Instalowanie usługi git](https://git-scm.com/book/en/Getting-Started-Installing-Git).
2. Zobacz [Przygotowywanie repozytorium](../app-service/deploy-local-git.md#prepare-your-repository) , aby włączyć repozytorium git dla swojej lokacji zaplecza. Zanotuj nazwę użytkownika i hasło do wdrożenia.
3. W okienku dla Mobile Apps zaplecza Zanotuj ustawienie **adresu URL klonowania git** .
4. Wykonaj `git clone` polecenie przy użyciu adresu URL klonowania git. Wprowadź hasło, jeśli jest to wymagane, tak jak w poniższym przykładzie:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Przejdź do katalogu lokalnego (`/todolist` w poprzednim przykładzie) i zwróć uwagę na to, że pliki projektu zostały pobrane. Znajdź plik TodoItem. JSON w katalogu `/tables`. Ten plik definiuje uprawnienia do tabeli. Znajdź także plik TodoItem. js w tym samym katalogu. Definiuje ona skrypty operacji CRUD dla tabeli.
6. Po wprowadzeniu zmian w plikach projektu uruchom następujące polecenia, aby dodać, zatwierdzić, a następnie przekazać zmiany do lokacji:

        $ git commit -m "updated the table script"
        $ git push origin master

   Po dodaniu nowych plików do projektu należy najpierw uruchomić polecenie `git add .`.

Lokacja jest ponownie publikowana za każdym razem, gdy nowy zestaw zatwierdzeń jest wypychany do lokacji.

### <a name="howto-publish-to-azure"></a>Publikowanie zaplecza Node. js na platformie Azure

Microsoft Azure udostępnia wiele mechanizmów publikowania zaplecza Mobile Apps Node. js w usłudze platformy Azure. Te mechanizmy obejmują narzędzia wdrażania zintegrowane z Visual Studio, narzędzia wiersza polecenia i opcje ciągłego wdrażania na podstawie kontroli źródła. Aby uzyskać więcej informacji, zobacz [Przewodnik wdrażania Azure App Service].

Azure App Service ma specjalne porady dotyczące aplikacji node. js, które należy przejrzeć przed opublikowaniem zaplecza:

* Jak [Określ wersję węzła]
* Jak [używanie modułów Node]

### <a name="howto-enable-homepage"></a>Włączanie strony głównej aplikacji

Wiele aplikacji jest kombinacją aplikacji internetowych i mobilnych. Możesz użyć struktury ExpressJS, aby połączyć dwa zestawy reguł. Czasami jednak może zajść potrzeba zaimplementowania interfejsu mobilnego. Warto podać stronę główną, aby upewnić się, że usługa App Service jest uruchomiona. Możesz udostępnić własną stronę główną lub włączyć tymczasową stronę główną. Aby włączyć tymczasową stronę główną, użyj następującego kodu, aby utworzyć wystąpienie Mobile Apps:

```javascript
var mobile = azureMobileApps({ homePage: true });
```

Jeśli chcesz, aby ta opcja była dostępna tylko podczas tworzenia lokalnie, możesz dodać to ustawienie do pliku azureMobile. js.

## <a name="TableOperations"></a>Operacje tabeli

Zestaw SDK serwera platformy Azure-Mobile-Apps udostępnia mechanizmy umożliwiające uwidocznienie tabel danych przechowywanych w Azure SQL Database jako interfejs API sieci Web. Zawiera pięć operacji:

| Operacja | Opis |
| --- | --- |
| Pobierz/Tables/*tabeli* |Pobierz wszystkie rekordy z tabeli. |
| Pobierz/Tables/*TableName*/: ID |Pobierz konkretny rekord w tabeli. |
| Ogłoś/Tables/*tabeli* |Utwórz rekord w tabeli. |
| Poprawka/Tables/*tabeli*/: ID |Zaktualizuj rekord w tabeli. |
| Usuń/Tables/*TableName*/: ID |Usuń rekord z tabeli. |

Ten internetowy interfejs API obsługuje protokół [OData] i rozszerza schemat tabeli w celu obsługi [Synchronizacja danych w trybie offline].

### <a name="howto-dynamicschema"></a>Definiowanie tabel przy użyciu schematu dynamicznego

Aby można było użyć tabeli, należy ją zdefiniować. Tabele można definiować przy użyciu schematu statycznego (w którym można definiować kolumny w schemacie) lub dynamicznie (gdzie zestaw SDK kontroluje schemat w oparciu o przychodzące żądania). Ponadto można kontrolować określone aspekty interfejsu API sieci Web, dodając kod JavaScript do definicji.

Najlepszym rozwiązaniem jest zdefiniowanie każdej tabeli w pliku JavaScript w katalogu `tables`, a następnie użycie metody `tables.import()` w celu zaimportowania tabel. Rozszerzając przykład podstawowej aplikacji, należy dostosować plik App. js:

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

Zdefiniuj tabelę w./tables/TodoItem.js:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here.

module.exports = table;
```

Domyślnie tabele używają schematu dynamicznego. Aby globalnie wyłączyć schemat dynamiczny, w Azure Portal należy ustawić wartość false dla ustawienia aplikacji `MS_DynamicSchema`.

Pełny przykład można znaleźć w przykładowym [przykład do zrobienia w serwisie GitHub].

### <a name="howto-staticschema"></a>Definiowanie tabel przy użyciu schematu statycznego

Można jawnie zdefiniować kolumny, które mają zostać ujawnione za pośrednictwem internetowego interfejsu API. Zestaw SDK platformy Azure-Mobile-Apps automatycznie dodaje wszelkie dodatkowe kolumny wymagane do synchronizacji danych w trybie offline z podaną listą. Na przykład aplikacje klienta szybkiego startu wymagają tabeli z dwiema kolumnami: `text` (ciąg) i `complete` (wartość logiczna).  
Tabelę można zdefiniować w pliku języka JavaScript definicji tabeli (znajdującym się w katalogu `tables`) w następujący sposób:

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

Jeśli tabele są definiowane statycznie, należy również wywołać metodę `tables.initialize()`, aby utworzyć schemat bazy danych przy uruchamianiu. Metoda `tables.initialize()` zwraca [Firmy] , aby usługa sieci Web nie obsługiwała żądań przed zainicjowaniem bazy danych.

### <a name="howto-sqlexpress-setup"></a>Użyj SQL Server Express jako magazynu danych programistycznych na komputerze lokalnym

Zestaw SDK Mobile Apps Node. js oferuje trzy opcje obsługi danych z usługi Box:

* Użyj sterownika **pamięci** , aby dostarczyć nietrwały magazyn przykładów.
* Użyj sterownika **MSSQL** , aby zapewnić SQL Server Express magazyn danych do programowania.
* Użyj sterownika **MSSQL** , aby dostarczyć Azure SQL Database magazynu danych dla środowiska produkcyjnego.

Zestaw SDK Mobile Apps Node. js używa [Pakiet MSSQL Node. js] do nawiązywania połączenia z SQL Server Express i SQL Database. Ten pakiet wymaga włączenia połączeń TCP w wystąpieniu SQL Server Express.

> [!TIP]
> Sterownik pamięci nie oferuje kompletnego zestawu obiektów do testowania. Jeśli chcesz przetestować zaplecze lokalnie, zalecamy korzystanie z SQL Server Express magazynu danych i sterownika MSSQL.

1. Pobierz i zainstaluj [Microsoft SQL Server 2014 Express]. Upewnij się, że zainstalowano SQL Server 2014 Express with Tools Edition. O ile nie jest jawnie wymagana obsługa 64-bitowego, wersja 32-bitowa zużywa mniejszą ilość pamięci podczas uruchamiania.
1. Uruchom SQL Server 2014 Configuration Manager:

   a. Rozwiń węzeł **Konfiguracja sieci SQL Server** w menu drzewa.

   b. Wybierz **Protokoły dla programu SQLExpress**.

   c. Kliknij prawym przyciskiem myszy pozycję **TCP/IP** , a następnie wybierz pozycję **Włącz**. W podręcznym oknie dialogowym wybierz pozycję **OK** .

   d. Kliknij prawym przyciskiem myszy pozycję **TCP/IP** , a następnie wybierz polecenie **Właściwości**.

   e. Wybierz kartę **adresy IP** .

   f. Znajdź węzeł **IPAll** . W polu **port TCP** wprowadź **1433**.

      ![Konfigurowanie SQL Server Express protokołu TCP/IP][3]

   g. Kliknij przycisk **OK**. W podręcznym oknie dialogowym wybierz pozycję **OK** .

   h. W menu drzewa wybierz pozycję **usługi SQL Server** .

   i. Kliknij prawym przyciskiem myszy pozycję **SQL Server (SQLExpress)** i wybierz pozycję **Uruchom ponownie**.

   j. Zamknij Configuration Manager SQL Server 2014.

1. Uruchom SQL Server 2014 Management Studio i Połącz się z lokalnym wystąpieniem SQL Server Express:

   1. Kliknij prawym przyciskiem myszy wystąpienie w Eksplorator obiektów i wybierz polecenie **Właściwości**.
   1. Wybierz stronę **zabezpieczenia** .
   1. Upewnij się, że wybrano **SQL Server i tryb uwierzytelniania systemu Windows** .
   1. Kliknij przycisk **OK**.

      ![Konfigurowanie uwierzytelniania SQL Server Express][4]
   1. Rozwiń węzeł **zabezpieczenia** > **nazwy logowania** w Eksplorator obiektów.
   1. Kliknij prawym przyciskiem myszy pozycję **logowania** i wybierz pozycję **Nowa nazwa logowania**.
   1. Wprowadź nazwę logowania. Wybierz pozycję **Uwierzytelnianie programu SQL Server**. Wprowadź hasło, a następnie wprowadź to samo hasło w polu **Potwierdź hasło**. Hasło musi spełniać wymagania dotyczące złożoności systemu Windows.
   1. Kliknij przycisk **OK**.

      ![Dodaj nowego użytkownika do SQL Server Express][5]
   1. Kliknij prawym przyciskiem myszy nową nazwę logowania i wybierz pozycję **Właściwości**.
   1. Wybierz stronę **role serwera** .
   1. Zaznacz pole wyboru dla roli serwera **dbcreator** .
   1. Kliknij przycisk **OK**.
   1. Zamknij Management Studio SQL Server 2015.

Pamiętaj, aby zapisać wybraną nazwę użytkownika i hasło. W zależności od wymagań bazy danych może być konieczne przypisanie dodatkowych ról lub uprawnień serwera.

Aplikacja Node. js odczytuje zmienną środowiskową `SQLCONNSTR_MS_TableConnectionString` dla parametrów połączenia dla tej bazy danych. Tę zmienną można ustawić w środowisku. Na przykład można użyć programu PowerShell do ustawienia tej zmiennej środowiskowej:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Dostęp do bazy danych za pośrednictwem połączenia TCP/IP. Podaj nazwę użytkownika i hasło dla połączenia.

### <a name="howto-config-localdev"></a>Konfigurowanie projektu na potrzeby lokalnego projektowania

Mobile Apps odczytuje plik języka JavaScript o nazwie *azureMobile. js* z lokalnego systemu plików. Nie należy używać tego pliku do konfigurowania zestawu SDK Mobile Apps w środowisku produkcyjnym. Zamiast tego użyj **ustawień aplikacji** w [Azure Portal].

Plik azureMobile. js powinien eksportować obiekt konfiguracji. Najczęściej spotykane są następujące ustawienia:

* Ustawienia bazy danych
* Ustawienia rejestrowania diagnostycznego
* Alternatywne ustawienia CORS

Ten przykładowy plik **azureMobile. js** implementuje poprzednie ustawienia bazy danych:

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

Zalecamy dodanie pliku **azureMobile. js** do pliku **. gitignore** (lub innej kontroli kodu źródłowego Ignoruj plik), aby uniemożliwić przechowywanie haseł w chmurze. Zawsze Konfiguruj ustawienia produkcyjne w **ustawieniach aplikacji** w ramach [Azure Portal].

### <a name="howto-appsettings"></a>Konfigurowanie ustawień aplikacji dla aplikacji mobilnej

Większość ustawień w pliku azureMobile. js ma odpowiednie ustawienie aplikacji w [Azure Portal]. Aby skonfigurować aplikację w **ustawieniach aplikacji**, Skorzystaj z następującej listy:

| Ustawienie aplikacji | ustawienie azureMobile. js | Opis | Prawidłowe wartości |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |name |Nazwa aplikacji |ciąg |
| **MS_MobileLoggingLevel** |logging.level |Minimalny poziom rejestrowania komunikatów do zarejestrowania |błąd, ostrzeżenie, informacje, pełne, debugowanie, parapet |
| **MS_DebugMode** |debugowanie |Włącza lub wyłącza tryb debugowania |wartość true, false |
| **MS_TableSchema** |data.schema |Domyślna nazwa schematu dla tabel SQL |ciąg (domyślnie: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Włącza lub wyłącza tryb debugowania |wartość true, false |
| **MS_DisableVersionHeader** |wersja (ustawiona na wartość undefined) |Wyłącza nagłówek X-ZUMO-Server-version |wartość true, false |
| **MS_SkipVersionCheck** |skipversioncheck |Wyłącza sprawdzanie wersji interfejsu API klienta |wartość true, false |

Aby ustawić ustawienie aplikacji:

1. Zaloguj się do [Azure portal].
1. Wybierz pozycję **wszystkie zasoby** lub **App Services**a następnie wybierz nazwę aplikacji mobilnej.
1. Domyślnie zostanie otwarte okienko **ustawień** . Jeśli nie, wybierz pozycję **Ustawienia**.
1. W menu **Ogólne** wybierz pozycję **Ustawienia aplikacji**.
1. Przewiń do sekcji **Ustawienia aplikacji** .
1. Jeśli ustawienie aplikacji już istnieje, wybierz wartość ustawienia aplikacji, aby edytować wartość.
   Jeśli ustawienie aplikacji nie istnieje, wprowadź ustawienie aplikacji w polu **klucz** i wartość w polu **wartość** .
1. Wybierz pozycję **Zapisz**.

Zmiana większości ustawień aplikacji wymaga ponownego uruchomienia usługi.

### <a name="howto-use-sqlazure"></a>Użyj SQL Database jako magazynu danych produkcyjnych

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Używanie Azure SQL Database jako magazynu danych jest identyczne we wszystkich typach aplikacji Azure App Service. Jeśli jeszcze tego nie zrobiono, wykonaj następujące kroki, aby utworzyć Mobile Apps zaplecza:

1. Zaloguj się do [Azure portal].
1. W lewym górnym rogu okna wybierz przycisk **+ nowy** > **Sieć Web + aplikacje mobilne** > **aplikacji mobilnej**, a następnie podaj nazwę Mobile Apps zaplecza.
1. W polu **Grupa zasobów** Wprowadź taką samą nazwę jak aplikacja.
1. Wybrano domyślny plan App Service. Jeśli chcesz zmienić plan App Service:

   a. Wybierz pozycję **App Service Plan** >  **+ Utwórz nowy**.

   b. Podaj nazwę nowego planu App Service i wybierz odpowiednią lokalizację.

   c. Wybierz odpowiednią warstwę cenową dla usługi. Wybierz pozycję **Wyświetl wszystko** , aby wyświetlić więcej opcji cenowych, takich jak **bezpłatne** i **udostępnione**.

   d. Kliknij przycisk **Wybierz** .

   e. W okienku **App Service plan** wybierz pozycję **OK**.
1. Wybierz pozycję **Utwórz**.

Inicjowanie obsługi Mobile Apps zaplecza może potrwać kilka minut. Po zainicjowaniu obsługi wewnętrznej bazy Mobile Apps w portalu zostanie otwarte okienko **Ustawienia** dla Mobile Apps zaplecza.

Możesz połączyć istniejącą bazę danych SQL z zaplecem Mobile Apps lub utworzyć nową bazę danych SQL. W tej sekcji utworzysz bazę danych SQL.

> [!NOTE]
> Jeśli masz już bazę danych w tej samej lokalizacji co Mobile Apps zaplecza, zamiast tego wybierz opcję **Użyj istniejącej bazy danych** , a następnie wybierz tę bazę danych. Nie zalecamy korzystania z bazy danych w innej lokalizacji z powodu wyższych opóźnień.

1. Na stronie nowy Mobile Apps zaplecza wybierz pozycję **ustawienia** > **aplikacja mobilna** > **dane** >  **+ Dodaj**.
1. W okienku **Dodawanie połączenia danych** wybierz pozycję **SQL Database — skonfiguruj wymagane ustawienia** , > **utworzyć nową bazę danych**. Wprowadź nazwę nowej bazy danych w polu **Nazwa** .
1. Wybierz pozycję **serwer**. W okienku **nowy serwer** wprowadź unikatową nazwę serwera w polu **Nazwa serwera** , a następnie podaj odpowiedni identyfikator logowania i hasło administratora serwera. Upewnij się, że wybrano opcję **Zezwalaj usługom platformy Azure na dostęp do serwera** . Kliknij przycisk **OK**.

   ![Tworzenie bazy danych Azure SQL Database][6]
1. W okienku **Nowa baza danych** wybierz **przycisk OK**.
1. Wróć do okienka **Dodawanie połączenia danych** , wybierz pozycję **Parametry połączenia**, a następnie wprowadź nazwę logowania i hasło podane podczas tworzenia bazy danych. W przypadku korzystania z istniejącej bazy danych podaj poświadczenia logowania dla tej bazy danych. Kliknij przycisk **OK**.
1. Ponownie w okienku **Dodaj połączenie danych** wybierz pozycję **OK** , aby utworzyć bazę danych.

<!--- END OF ALTERNATE INCLUDE -->

Tworzenie bazy danych może potrwać kilka minut. Za pomocą obszaru **powiadomienia** Monitoruj postęp wdrożenia. Nie wykonuj postępów, dopóki baza danych nie zostanie pomyślnie wdrożona. Po wdrożeniu bazy danych są tworzone parametry połączenia dla wystąpienia SQL Database w ustawieniach aplikacji zaplecza Mobile Apps. To ustawienie aplikacji można zobaczyć w obszarze **ustawienia** > **Ustawienia aplikacji** > **Parametry połączenia**.

### <a name="howto-tables-auth"></a>Wymagaj uwierzytelniania w celu uzyskania dostępu do tabel

Jeśli chcesz użyć uwierzytelniania App Service przy użyciu punktu końcowego `tables`, najpierw należy skonfigurować uwierzytelnianie App Service w [Azure Portal] . Aby uzyskać więcej informacji, zobacz Przewodnik dotyczący konfiguracji dostawcy tożsamości, którego zamierzasz użyć:

* [Konfigurowanie uwierzytelniania Azure Active Directory]
* [Konfigurowanie uwierzytelniania w usłudze Facebook]
* [Skonfiguruj uwierzytelnianie Google]
* [Konfigurowanie uwierzytelniania firmy Microsoft]
* [Konfigurowanie uwierzytelniania w usłudze Twitter]

Każda tabela ma właściwość dostępu, której można użyć do kontrolowania dostępu do tabeli. Poniższy przykład pokazuje statycznie zdefiniowaną tabelę z wymaganym uwierzytelnianiem.

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

Właściwość dostępu może przyjmować jedną z trzech wartości:

* wartość *anonimowa* wskazuje, że aplikacja kliencka może odczytywać dane bez uwierzytelniania.
* *uwierzytelnianie* wskazuje, że aplikacja kliencka musi wysłać prawidłowy token uwierzytelniania z żądaniem.
* *wyłączone* wskazuje, że ta tabela jest aktualnie wyłączona.

Jeśli właściwość dostępu jest niezdefiniowana, dozwolony jest nieuwierzytelniony dostęp.

### <a name="howto-tables-getidentity"></a>Używanie oświadczeń uwierzytelniania z tabelami
Można skonfigurować różne oświadczenia, które są wymagane podczas konfigurowania uwierzytelniania. Te oświadczenia nie są zwykle dostępne za pomocą obiektu `context.user`. Można je jednak pobrać przy użyciu metody `context.user.getIdentity()`. Metoda `getIdentity()` zwraca obietnicę, która jest rozpoznawana jako obiekt. Obiekt jest poddany przez metodę uwierzytelniania (`facebook`, `google`, `twitter`, `microsoftaccount`lub `aad`).

Na przykład jeśli skonfigurujesz konto Microsoft uwierzytelnianie i zażądasz wniosku e-mail, możesz dodać adres e-mail do rekordu z następującym kontrolerem tabel:

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

Aby zobaczyć, jakie oświadczenia są dostępne, użyj przeglądarki sieci Web, aby wyświetlić punkt końcowy `/.auth/me` witryny.

### <a name="howto-tables-disabled"></a>Wyłącz dostęp do określonych operacji w tabeli

Oprócz wyświetlania w tabeli Właściwość dostępu może służyć do kontrolowania poszczególnych operacji. Istnieją cztery operacje:

* `read` jest operacją pobierania RESTful na tabeli.
* `insert` jest operacją RESTful POST w tabeli.
* `update` jest operacją RESTful PATCH w tabeli.
* `delete` jest operacją usuwania RESTful z tabeli.

Na przykład możesz chcieć podać nieuwierzytelnioną tabelę tylko do odczytu:

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

Typowym wymaganiem dla operacji w tabeli jest zapewnienie ograniczonego widoku danych. Można na przykład podać tabelę z IDENTYFIKATORem uwierzytelnionego użytkownika, tak aby można było tylko odczytywać lub aktualizować własne rekordy. W poniższej definicji tabeli przedstawiono tę funkcję:

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

Operacje, które zwykle uruchamiają zapytanie, mają właściwość zapytania, którą można dostosować przy użyciu klauzuli `where`. Właściwość zapytania jest obiektem [QueryJS] , który jest używany do konwertowania zapytania OData na element, który może przetwarzać zaplecze danych. W przypadku prostych przypadków równości (takich jak poprzednia) można użyć mapy. Można również dodać określone klauzule SQL:

```javascript
context.query.where('myfield eq ?', 'value');
```

### <a name="howto-tables-softdelete"></a>Konfigurowanie usuwania nietrwałego w tabeli

Usuwanie nietrwałe nie usuwa rekordów. Zamiast tego oznacza je jako usunięte w bazie danych przez ustawienie kolumny usunięte na wartość true. Zestaw Mobile Apps SDK automatycznie usuwa nietrwałe rekordy usunięte z wyników, chyba że zestaw SDK klienta mobilnego używa `IncludeDeleted()`. Aby skonfigurować tabelę do usuwania nietrwałego, ustaw właściwość `softDelete` w pliku definicji tabeli:

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

Należy ustanowić mechanizm usuwania rekordów: aplikacja kliencka, zadanie WebJob, funkcja platformy Azure lub niestandardowy interfejs API.

### <a name="howto-tables-seeding"></a>Wypełnianie bazy danych danymi

Podczas tworzenia nowej aplikacji może być konieczne wypełnianie tabeli danymi. Można to zrobić w pliku języka JavaScript definicji tabeli w następujący sposób:

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

Wypełnianie danych odbywa się tylko wtedy, gdy do utworzenia tabeli użyto zestawu SDK Mobile Apps. Jeśli tabela już istnieje w bazie danych, żadne dane nie są wstrzykiwane do tabeli. Jeśli schemat dynamiczny jest włączony, schemat jest wywnioskowany na podstawie danych z inicjatora.

Zalecamy jawnie wywołać metodę `tables.initialize()`, aby utworzyć tabelę, gdy usługa zostanie uruchomiona.

### <a name="Swagger"></a>Włącz obsługę struktury Swagger
Mobile Apps zawiera wbudowaną obsługę [Swagger] . Aby włączyć obsługę struktury Swagger, najpierw zainstaluj strukturę Swagger — interfejs użytkownika jako zależność:

    npm install --save swagger-ui

Następnie można włączyć obsługę struktury Swagger w konstruktorze Mobile Apps:

```javascript
var mobile = azureMobileApps({ swagger: true });
```

Prawdopodobnie chcesz włączyć obsługę struktury Swagger tylko w wersjach deweloperskich. Można to zrobić za pomocą ustawienia aplikacji `NODE_ENV`:

```javascript
var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });
```

Punkt końcowy `swagger` znajduje się w http://*yoursite*. azurewebsites.NET/Swagger. Dostęp do interfejsu użytkownika struktury Swagger można uzyskać za pośrednictwem punktu końcowego `/swagger/ui`. W przypadku wybrania opcji wymagania uwierzytelniania w całej aplikacji struktura Swagger generuje błąd. Aby uzyskać najlepsze wyniki, wybierz opcję zezwalania na nieuwierzytelnione żądania w Azure App Service ustawieniach uwierzytelniania/autoryzacji, a następnie kontrolowanie uwierzytelniania przy użyciu właściwości `table.access`.

Możesz również dodać opcję Swagger do pliku azureMobile. js, jeśli chcesz, aby obsługiwały strukturę Swagger lokalnie.

## <a name="a-namepushpush-notifications"></a><a name="push"/>powiadomień wypychanych

Mobile Apps integrują się z usługą Azure Notification Hubs, dzięki czemu możesz wysyłać skierowane do milionów urządzeń w ramach wszystkich głównych platform. Za pomocą Notification Hubs można wysyłać powiadomienia wypychane do urządzeń z systemem iOS, Android i Windows. Aby dowiedzieć się więcej na temat wszystkich informacji, które można wykonać za pomocą Notification Hubs, zobacz [omówienie Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

### <a name="send-push"></a>Wysyłanie powiadomień wypychanych

Poniższy kod pokazuje, jak za pomocą obiektu `push` wysyłać powiadomienia wypychane do zarejestrowanych urządzeń z systemem iOS:

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

Tworząc szablon wypychanej rejestracji z poziomu klienta, można zamiast tego wysłać wiadomość wypychaną szablonu do urządzeń na wszystkich obsługiwanych platformach. Poniższy kod przedstawia sposób wysyłania powiadomienia szablonu:

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

### <a name="push-user"></a>Wysyłanie powiadomień wypychanych do uwierzytelnionego użytkownika przy użyciu tagów
Gdy uwierzytelniony użytkownik rejestruje się w celu otrzymywania powiadomień wypychanych, tag identyfikatora użytkownika jest automatycznie dodawany do rejestracji. Za pomocą tego tagu można wysyłać powiadomienia wypychane do wszystkich urządzeń zarejestrowanych przez określonego użytkownika. Poniższy kod pobiera identyfikator SID użytkownika, który wysłał żądanie, i wysyła powiadomienie wypychane szablonu do każdej rejestracji urządzenia dla tego użytkownika:

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

Po zarejestrowaniu się w celu otrzymywania powiadomień wypychanych z poziomu uwierzytelnionego klienta upewnij się, że przed próbą rejestracji zostanie wykonane uwierzytelnianie.

## <a name="CustomAPI"></a>Niestandardowe interfejsy API

### <a name="howto-customapi-basic"></a>Definiowanie niestandardowego interfejsu API

Oprócz interfejsu API dostępu do danych za pośrednictwem punktu końcowego `/tables`, Mobile Apps może zapewnić niestandardowe pokrycie interfejsu API. Niestandardowe interfejsy API są zdefiniowane w podobny sposób do definicji tabeli i mogą uzyskać dostęp do wszystkich tych samych obiektów, w tym uwierzytelniania.

Jeśli chcesz użyć uwierzytelniania App Service z niestandardowym interfejsem API, najpierw należy skonfigurować uwierzytelnianie App Service w [Azure Portal] . Aby uzyskać więcej informacji, zobacz Przewodnik dotyczący konfiguracji dostawcy tożsamości, którego zamierzasz użyć:

* [Konfigurowanie uwierzytelniania Azure Active Directory]
* [Konfigurowanie uwierzytelniania w usłudze Facebook]
* [Skonfiguruj uwierzytelnianie Google]
* [Konfigurowanie uwierzytelniania firmy Microsoft]
* [Konfigurowanie uwierzytelniania w usłudze Twitter]

Niestandardowe interfejsy API są zdefiniowane w taki sam sposób, jak w przypadku interfejsu API tabel:

1. Utwórz katalog `api`.
1. Utwórz plik języka JavaScript definicji interfejsu API w katalogu `api`.
1. Użyj metody Import, aby zaimportować katalog `api`.

Oto definicja prototypu interfejsu API oparta na przykładowej aplikacji używanej wcześniej:

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

Użyjmy przykładowego interfejsu API, który zwraca datę serwera przy użyciu metody `Date.now()`. Oto plik API/Date. js:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};

module.exports = api;
```

Każdy parametr jest jednym ze standardowych czasowników RESTful: GET, POST, PATCH lub DELETE. Metoda jest standardową funkcją [Oprogramowanie pośredniczące ExpressJS] , która wysyła wymagane dane wyjściowe.

### <a name="howto-customapi-auth"></a>Wymagaj uwierzytelniania w celu uzyskania dostępu do niestandardowego interfejsu API

Zestaw SDK Mobile Apps implementuje uwierzytelnianie w taki sam sposób dla punktu końcowego `tables` i niestandardowych interfejsów API. Aby dodać uwierzytelnianie do interfejsu API opracowanego w poprzedniej sekcji, Dodaj `access` Właściwość:

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

Można również określić uwierzytelnianie dla określonych operacji:

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

Ten sam token, który jest używany przez punkt końcowy `tables`, musi być używany dla niestandardowych interfejsów API, które wymagają uwierzytelniania.

### <a name="howto-customapi-auth"></a>Obsługa dużych operacji przekazywania plików

Zestaw SDK Mobile Apps używa [oprogramowania pośredniczącego Analizator treści](https://github.com/expressjs/body-parser) do akceptowania i dekodowania zawartości treści w ramach przesłania. Można wstępnie skonfigurować Analizator treści, aby akceptował większe operacje przekazywania plików:

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

Plik ma Base-64 zakodowany przed przesłaniem. To kodowanie zwiększa rozmiar rzeczywistego przekazywania (i rozmiar, który należy uwzględnić).

### <a name="howto-customapi-sql"></a>Wykonaj niestandardowe instrukcje SQL

Zestaw SDK Mobile Apps umożliwia dostęp do całego kontekstu za pomocą obiektu request. Można łatwo wykonać sparametryzowane instrukcje SQL do zdefiniowanego dostawcy danych:

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

## <a name="Debugging"></a>Debugera

### <a name="howto-diagnostic-logs"></a>Debugowanie, diagnozowanie i rozwiązywanie problemów Mobile Apps

Azure App Service zapewnia kilka technik debugowania i rozwiązywania problemów dla aplikacji node. js.
Aby rozpocząć rozwiązywanie problemów z zapleczem środowiska Node. js Mobile Apps, zobacz następujące artykuły:

* [Azure App Service monitorowania]
* [Włączanie rejestrowania diagnostycznego w Azure App Service]
* [Rozwiązywanie problemów Azure App Service w programie Visual Studio]

Aplikacje Node. js mają dostęp do szerokiego zakresu narzędzi dzienników diagnostycznych. Wewnętrznie zestaw SDK Mobile Apps Node. js używa [Winston] do rejestrowania diagnostycznego. Rejestrowanie jest włączane automatycznie po włączeniu trybu debugowania lub ustawieniu dla ustawienia aplikacji `MS_DebugMode` wartość true w [Azure Portal]. Wygenerowane dzienniki są wyświetlane w dziennikach diagnostycznych w [Azure Portal].

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Klient systemu Android — szybki start]: app-service-mobile-android-get-started.md
[Apache Cordova — Szybki Start]: app-service-mobile-cordova-get-started.md
[Klient systemu iOS — Szybki Start]: app-service-mobile-ios-get-started.md
[Klient platformy Xamarin. iOS — Szybki Start]: app-service-mobile-xamarin-ios-get-started.md
[Klient platformy Xamarin. Android — szybki start]: app-service-mobile-xamarin-android-get-started.md
[Samouczek szybki start dla klienta Xamarin. Forms]: app-service-mobile-xamarin-forms-get-started.md
[Klient sklepu Windows — szybki start]: app-service-mobile-windows-store-dotnet-get-started.md
[Synchronizacja danych w trybie offline]: app-service-mobile-offline-data-sync.md
[Konfigurowanie uwierzytelniania Azure Active Directory]: ../app-service/configure-authentication-provider-aad.md
[Konfigurowanie uwierzytelniania w usłudze Facebook]: ../app-service/configure-authentication-provider-facebook.md
[Skonfiguruj uwierzytelnianie Google]: ../app-service/configure-authentication-provider-google.md
[Konfigurowanie uwierzytelniania firmy Microsoft]: ../app-service/configure-authentication-provider-microsoft.md
[Konfigurowanie uwierzytelniania w usłudze Twitter]: ../app-service/configure-authentication-provider-twitter.md
[Przewodnik wdrażania Azure App Service]: ../app-service/deploy-local-git.md
[Azure App Service monitorowania]: ../app-service/web-sites-monitor.md
[Włączanie rejestrowania diagnostycznego w Azure App Service]: ../app-service/troubleshoot-diagnostic-logs.md
[Rozwiązywanie problemów Azure App Service w programie Visual Studio]: ../app-service/troubleshoot-dotnet-visual-studio.md
[Określ wersję węzła]: ../nodejs-specify-node-version-azure-apps.md
[używanie modułów Node]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: https://expressjs.com/
[Swagger]: https://swagger.io/

[Azure Portal]: https://portal.azure.com/
[OData]: https://www.odata.org
[Firmy]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[przykład basicapp w witrynie GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[przykład do zrobienia w serwisie GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[Katalog przykładów w witrynie GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Narzędzia Node. js Tools 1,1 dla programu Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[Pakiet MSSQL Node. js]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[Oprogramowanie pośredniczące ExpressJS]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
