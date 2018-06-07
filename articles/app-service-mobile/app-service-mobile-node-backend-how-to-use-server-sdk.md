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
ms.openlocfilehash: 33a447e2161925d23385cbd0c471e0f5babd618f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598083"
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>Jak używać zestawu Mobile Apps Node.js SDK
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Ten artykuł zawiera szczegółowe informacje i przykłady, które przedstawiają sposób pracy z Node.js zaplecza w funkcji Mobile Apps w usłudze Azure App Service.

## <a name="Introduction"></a>Wprowadzenie
Mobile Apps oferuje możliwość dodawania danych zoptymalizowanych pod kątem mobile dostępu do interfejsu API sieci Web do aplikacji sieci web. Zestaw SDK usługi Mobile Apps jest dostępna dla aplikacji sieci web ASP.NET i Node.js. Zestaw SDK udostępnia następujące operacje:

* Operacje tabeli (do odczytu, wstawianie, aktualizowanie i usuwanie) dla dostępu do danych
* Operacje niestandardowego interfejsu API

Obie operacje zapewnienia uwierzytelniania przez wszystkich dostawców tożsamości, które umożliwia usłudze Azure App Service. Tych dostawców obejmują dostawców tożsamości społecznościowych, takich jak Facebook, Twitter, Google i firmy Microsoft, a także usługi Azure Active Directory dla tożsamości organizacji.

Przykłady można znaleźć do każdego przypadku użycia w [Katalog przykładów w witrynie GitHub].

## <a name="supported-platforms"></a>Obsługiwane platformy
Zestaw SDK usługi Mobile Apps Node.js obsługuje bieżącej wersji LTS węzła i nowszych. Najnowszą wersję LTS jest obecnie v4.5.0 węzła. Inne wersje węzeł może działać, ale nie są obsługiwane.

Zestaw SDK usługi Mobile Apps Node.js obsługuje dwa sterowniki bazy danych: 

* Sterownik mssql węzła obsługuje bazy danych SQL Azure i lokalnego wystąpienia programu SQL Server.  
* Sterownik sqlite3 obsługuje tylko jedno wystąpienie bazy danych SQLite.

### <a name="howto-cmdline-basicapp"></a>Utwórz podstawowe zaplecze Node.js przy użyciu wiersza polecenia
Jako aplikacja ExpressJS rozpoczyna się co zaplecza Node.js aplikacji mobilnej. ExpressJS jest dostępna dla środowiska Node.js struktury usługi najpopularniejszych sieci web. Można utworzyć basic [Express] aplikacji w następujący sposób:

1. Polecenie lub oknie programu PowerShell należy utworzyć katalog projektu:

        mkdir basicapp
2. Uruchom `npm init` zainicjować struktury pakietu:

        cd basicapp
        npm init

   `npm init` Polecenie żąda zbiór pytania można zainicjować projektu. Zobacz przykład danych wyjściowych:

   ![Dane wyjściowe init npm][0]
3. Zainstaluj `express` i `azure-mobile-apps` bibliotek z repozytorium npm:

        npm install --save express azure-mobile-apps
4. Tworzenie pliku app.js do zaimplementowania podstawowego serwera przenośnych:

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

Ta aplikacja tworzy zoptymalizowanych pod kątem mobile interfejsu API sieci Web z jednym punktem końcowym (`/tables/TodoItem`) zapewniający nieuwierzytelniony dostęp do odpowiedni magazyn danych SQL za pomocą dynamicznej schematu. Jest ona odpowiednia dla następującej biblioteki klienta poradniki Szybki Start:

* [Szybki Start kliencką dla systemu android]
* [Szybki Start Apache Cordova klienta]
* [iOS — Szybki Start klienta]
* [Szybki Start klienta Sklepu Windows]
* [Szybki Start klienta platformy Xamarin.iOS]
* [Szybki Start klienta platformy Xamarin.Android]
* [Szybki Start klienta platformy Xamarin.Forms]

Możesz znaleźć kod dla tej aplikacji w warstwie podstawowa w [Przykładem basicapp w witrynie GitHub].

### <a name="howto-vs2015-basicapp"></a>Tworzenie zaplecza Node.js przy użyciu programu Visual Studio 2015
Visual Studio 2015 wymaga rozszerzenia do tworzenia aplikacji Node.js w środowisku IDE. Aby rozpocząć, zainstaluj [Narzędzia node.js Tools 1.1 dla programu Visual Studio]. Po zakończeniu instalacji, należy utworzyć aplikację Express 4.x:

1. Otwórz **nowy projekt** okno dialogowe (z **pliku** > **nowy** > **projektu**).
2. Rozwiń węzeł **szablony** > **JavaScript** > **Node.js**.
3. Wybierz **aplikacji podstawowe Azure Node.js Express 4**.
4. Wprowadź nazwę projektu. Kliknij przycisk **OK**.

   ![Nowy projekt programu Visual Studio 2015][1]
5. Kliknij prawym przyciskiem myszy **npm** a następnie wybierz węzeł **zainstalować nowych pakietów npm**.
6. Konieczne może być odświeżyć katalog npm po utworzeniu pierwszej aplikacji Node.js. Wybierz **Odśwież** w razie potrzeby.
7. Wprowadź **apps w usłudze azure-mobile** w polu wyszukiwania. Wybierz **usługi azure mobile apps 2.0.0** pakietu, a następnie wybierz **zainstaluj pakiet**.

   ![Zainstaluj nowych pakietów npm][2]
8. Wybierz **Zamknij**.
9. Otwórz plik app.js, aby dodać obsługę zestaw SDK usługi Mobile Apps. At wiersz 6 at dolnej części biblioteki `require` instrukcje, Dodaj następujący kod:

        var bodyParser = require('body-parser');
        var azureMobileApps = require('azure-mobile-apps');

   Co około wiersz 27 po drugim `app.use` instrukcje, Dodaj następujący kod:

        app.use('/users', users);

        // Mobile Apps initialization
        var mobile = azureMobileApps();
        mobile.tables.add('TodoItem');
        app.use(mobile);

   Zapisz plik.
10. Albo uruchamianie aplikacji lokalnie (interfejs API jest obsługiwana na http://localhost:3000) lub publikowanie na platformie Azure.

### <a name="create-node-backend-portal"></a>Tworzenie zaplecza Node.js przy użyciu portalu Azure
Można tworzyć aplikacje mobilne zaplecze bezpośrednio w [Azure Portal]. Możesz wykonać następujące czynności lub utworzyć klienta i serwera razem [tworzenie aplikacji mobilnej](app-service-mobile-ios-get-started.md) samouczka. Samouczek zawiera uproszczonej wersji tych instrukcji i najlepiej w projektach z koncepcji.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

W **wprowadzenie** okienku w obszarze **Utwórz tabelę interfejsu API**, wybierz **Node.js** język zaplecza.
Zaznacz pole **potwierdzam, że spowoduje to zastąpienie całej zawartości witryny**, a następnie wybierz **Utwórz tabelę TodoItem**.

### <a name="download-quickstart"></a>Pobieranie projektu kodu szybkiego startu zaplecza Node.js przy użyciu narzędzia Git
Podczas tworzenia zaplecza Node.js Mobile Apps, korzystając z portalu **szybki start** okienku projektu Node.js jest utworzony i wdrożone do tej witryny. W portalu Dodawanie tabel i interfejsów API i edytuj pliki kodu dla zaplecza Node.js. Aby pobrać projektu zaplecza, aby dodać lub zmodyfikować tabele i interfejsów API i ponownie opublikować projekt umożliwia także różne narzędzia wdrażania. Aby uzyskać więcej informacji, zobacz [Przewodnik wdrażania usługi aplikacji Azure]. 

Poniższa procedura wykorzystuje repozytorium Git do pobierania kodu projektu Szybki Start:

1. Zainstaluj usługę Git, jeśli jeszcze tego nie zrobiono. Kroki wymagane do zainstalowania Git się różnić między systemami operacyjnymi. Dystrybucje systemu operacyjnego i wskazówki dotyczące instalacji, zobacz [instalowanie Git](http://git-scm.com/book/en/Getting-Started-Installing-Git).
2. Zobacz [przygotowanie repozytorium](../app-service/app-service-deploy-local-git.md#prepare-your-repository) umożliwiające repozytorium Git dla witryny sieci wewnętrznej. Zanotuj wdrożenia nazwy użytkownika i hasła.
3. W okienku dla zaplecza aplikacji mobilnej Zanotuj **adres URL klonowania Git** ustawienie.
4. Wykonanie `git clone` polecenia przy użyciu adresu URL klonowania Git. Wprowadź hasło, gdy jest to wymagane, jak w poniższym przykładzie:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git
5. Przejdź do katalogu lokalnego (`/todolist` w poprzednim przykładzie) i zwróć uwagę, że pliki projektu zostały pobrane. Zlokalizuj plik todoitem.json w `/tables` katalogu. Ten plik definiuje uprawnienia w tabeli. Ponadto można znaleźć pliku todoitem.js w tym samym katalogu. Definiuje skrypty operacji CRUD dla tabeli.
6. Po wprowadzeniu zmian w plikach projektu, uruchom następujące polecenia, aby dodać, Przekaż, a następnie przekazać zmiany do witryny:

        $ git commit -m "updated the table script"
        $ git push origin master

   Po dodaniu nowych plików do projektu, najpierw należy uruchomić `git add .` polecenia.

Opublikowaniu lokacji za każdym razem, gdy nowy zestaw zatwierdzeń zostanie przypisany do lokacji.

### <a name="howto-publish-to-azure"></a>Publikowanie z zaplecza Node.js na platformie Azure
Microsoft Azure oferuje wiele mechanizmów publikowania programu Node.js aplikacji mobilnej zaplecza do usługi Azure. Obejmują one narzędzia wdrażania zintegrowane w programie Visual Studio, narzędzia wiersza polecenia i opcje ciągłego wdrażania w oparciu o kontroli źródła. Aby uzyskać więcej informacji, zobacz [Przewodnik wdrażania usługi aplikacji Azure].

Usługa aplikacji Azure ma poradę dla aplikacji programu Node.js, które należy przejrzeć przed opublikowaniem wewnętrznej:

* Jak [Określ wersję węzła]
* Jak [Użyj węzła modułów]

### <a name="howto-enable-homepage"></a>Włącz strony głównej aplikacji
Wiele aplikacji są kombinacją sieci web i aplikacji mobilnych. ExpressJS framework służy do łączenia dwóch zestawów reguł. Czasami jednak można tylko zaimplementować interfejs przenośnych. Jest przydatne do zawiera stronę główną, aby upewnić się, że działa usługa app i uruchomione. Można podać strony głównej lub włączyć tymczasowe strony głównej. Aby włączyć tymczasowe strony głównej, należy użyć poniższego kodu do utworzenia wystąpienia Mobile Apps:

    var mobile = azureMobileApps({ homePage: true });

Jeśli ta opcja dostępna mają tylko podczas opracowywania lokalnie to ustawienie można dodać do pliku azureMobile.js.

## <a name="TableOperations"></a>Operacje tabeli
Aplikacje azure-mobile Node.js Server SDK zawiera mechanizmy do udostępnienia tabel danych przechowywanych w bazie danych SQL Azure jako interfejs API sieci Web. Udostępnia ona pięć operacje:

| Operacja | Opis |
| --- | --- |
| GET /tables/*tablename* |Pobierz wszystkie rekordy w tabeli. |
| GET /tables/*tablename*/:id |Pobierz określonego rekordu w tabeli. |
| POST /tables/*tablename* |Utwórz rekord w tabeli. |
| POPRAWKA /tables/*tablename*/:id |Zaktualizuj rekord w tabeli. |
| Usuń /tables/*tablename*/:id |Usuwanie rekordu w tabeli. |

Ten interfejs API sieci Web obsługuje [OData] i rozszerza schemat tabeli w celu obsługi [Synchronizacja danych w trybie offline].

### <a name="howto-dynamicschema"></a>Definiowanie tabel za pomocą dynamicznej schematu
Przed użyciem tabeli, należy zdefiniować. Tabele można zdefiniować przy użyciu statycznych schematu (gdzie został zdefiniowany kolumn w schemacie) lub dynamicznie (gdy zestaw SDK kontroluje schematu oparte na przychodzące żądania). Ponadto można kontrolować określone aspekty interfejsu API sieci Web przez dodanie kodu JavaScript do definicji.

Najlepszym rozwiązaniem, należy zdefiniować w pliku JavaScript w każdej tabeli `tables` katalogu, a następnie użyć `tables.import()` metody, aby zaimportować tabele. Rozszerzanie przykładowej aplikacji basic, czy dostosować pliku app.js:

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

Zdefiniuj tabelę w. / tables/TodoItem.js:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Additional configuration for the table goes here.

    module.exports = table;

Tabele domyślnie używają dynamicznej schematu. Aby wyłączyć globalnie dynamiczne schematu, ustaw `MS_DynamicSchema` ustawienia aplikacji na wartość false w portalu Azure.

Znajduje się pełny przykład w [Przykładem ToDo w witrynie GitHub].

### <a name="howto-staticschema"></a>Definiowanie tabel za pomocą statycznego schematu
Można jawnie definiować kolumn do ujawnienia za pomocą interfejsu API sieci Web. Zestaw Node.js SDK apps w usłudze azure-mobile automatycznie dodaje wszystkie dodatkowe kolumny wymagane do synchronizacji danych w trybie offline do listy, który podasz. Na przykład wymagać tabeli z kolumnami przez aplikacje klienckie Szybki Start: `text` (ciąg) i `complete` (wartość logiczna).  
Tabeli mogą być zdefiniowane w pliku JavaScript definicji tabeli (znajdujące się w `tables` katalogu) w następujący sposób:

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

Po zdefiniowaniu statycznie tabel, musisz również wywołać `tables.initialize()` metodę w celu utworzenia schematu bazy danych podczas uruchamiania. `tables.initialize()` Metoda zwraca [promise] tak, aby usługi sieci web nie obsługiwać żądań przed zainicjowaniem bazy danych.

### <a name="howto-sqlexpress-setup"></a>Użyj programu SQL Server Express jako programowanie magazynu danych na komputerze lokalnym
Zestaw SDK usługi Mobile Apps Node.js zawiera trzy opcje do obsługi danych poza pola:

* Użyj **pamięci** sterownika do zapewnienia magazynu przykład trwałe.
* Użyj **mssql** sterownika do zapewnienia magazynu danych programu SQL Server Express do tworzenia aplikacji.
* Użyj **mssql** sterowników w celu zapewnienia magazynu danych bazy danych SQL Azure dla trybu produkcyjnego.

Zestaw SDK usługi Mobile Apps Node.js używa [MSSQL Node.js pakietu] i użycia połączenia do bazy danych SQL i programu SQL Server Express. Ten pakiet wymaga włączenia połączeń TCP w wystąpieniu programu SQL Server Express.

> [!TIP]
> Sterownik pamięci nie ma kompletnego zestawu urządzenia do testowania. Jeśli chcesz przetestować wewnętrzna baza lokalnie, firma Microsoft zaleca korzystanie z magazynu danych programu SQL Server Express i sterownik mssql.
>
>

1. Pobierz i zainstaluj [Program Microsoft SQL Server 2014 Express]. Pamiętaj, aby zainstalować program SQL Server 2014 Express z wersji narzędzia. Chyba że jawnie wymagana jest Obsługa 64-bitowy, 32-bitowej wersji zużywa mniej pamięci podczas uruchamiania.
2. Uruchom Menedżera konfiguracji programu SQL Server 2014:

   a. Rozwiń węzeł **konfigurację sieci programu SQL Server** węzła w menu drzewa.

   b. Wybierz **protokoły dla programu SQLEXPRESS**.

   c. Kliknij prawym przyciskiem myszy **TCP/IP** i wybierz **włączyć**. Wybierz **OK** w wyświetlonym oknie dialogowym.

   d. Kliknij prawym przyciskiem myszy **TCP/IP** i wybierz **właściwości**.

   e. Wybierz **adresów IP** kartę.

   f. Znajdź **IPWszystkie** węzła. W **TCP Port** wprowadź **1433**.

      ![Skonfiguruj program SQL Server Express protokołu TCP/IP][3]

   g. Kliknij przycisk **OK**. Wybierz **OK** w wyświetlonym oknie dialogowym.

   h. Wybierz **usług SQL Server** w menu drzewa.

   i. Kliknij prawym przyciskiem myszy **programu SQL Server (SQLEXPRESS)** i wybierz **ponownego uruchomienia**.

   j. Zamknij Menedżera konfiguracji programu SQL Server 2014.
3. Uruchom program SQL Server 2014 Management Studio i nawiąż połączenie lokalne wystąpienie programu SQL Server Express:

   1. Kliknij prawym przyciskiem myszy wystąpienie w Eksploratorze obiektów i wybierz **właściwości**.
   2. Wybierz **zabezpieczeń** strony.
   3. Upewnij się, że **programu SQL Server i uwierzytelniania systemu Windows tryb** jest zaznaczone.
   4. Kliknij przycisk **OK**.

      ![Konfigurowanie uwierzytelniania programu SQL Server Express][4]
   5. Rozwiń węzeł **zabezpieczeń** > **logowania** w Eksploratorze obiektów.
   6. Kliknij prawym przyciskiem myszy **logowania** i wybierz **nowe dane logowania**.
   7. Wprowadź nazwę logowania. Wybierz pozycję **Uwierzytelnianie programu SQL Server**. Wprowadź hasło, a następnie wprowadź to samo hasło w **Potwierdź hasło**. Hasło musi spełniać wymagania co do złożoności systemu Windows.
   8. Kliknij przycisk **OK**.

      ![Dodaj nowego użytkownika do programu SQL Server Express][5]
   9. Kliknij prawym przyciskiem myszy nazwę nowego użytkownika, a następnie wybierz **właściwości**.
   10. Wybierz **ról serwera** strony.
   11. Zaznacz pole wyboru dla **dbcreator** roli serwera.
   12. Kliknij przycisk **OK**.
   13. Zamknij program SQL Server 2015 Management Studio.

Pamiętaj zarejestrować nazwę użytkownika i hasło, które wybrano. Może być konieczne do przypisywania dodatkowych ról serwera lub uprawnieniami, w zależności od wymagań bazy danych.

Odczyty aplikacji Node.js `SQLCONNSTR_MS_TableConnectionString` zmiennej środowiskowej ciągu połączenia dla tej bazy danych. Można ustawić tę zmienną w danym środowisku. Na przykład aby ustawić tej zmiennej środowiskowej można Użyj programu PowerShell:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Dostęp do bazy danych przy użyciu połączenia TCP/IP. Podaj nazwę użytkownika i hasło dla połączenia.

### <a name="howto-config-localdev"></a>Skonfigurować projekt dla rozwoju lokalnych
Aplikacje mobilne odczytuje plik JavaScript o nazwie *azureMobile.js* z lokalnego systemu plików. Aby skonfigurować zestaw SDK usługi Mobile Apps w środowisku produkcyjnym nie należy używać tego pliku. Zamiast tego należy użyć **ustawień aplikacji** w [Azure Portal]. 

Plik azureMobile.js należy wyeksportować obiekt konfiguracji. Najczęściej używane ustawienia to:

* Ustawienia bazy danych
* Ustawienia rejestrowania diagnostycznego
* Alternatywne ustawienia mechanizmu CORS

Ten przykładowy plik azureMobile.js implementuje poprzedniego ustawienia bazy danych:

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

Firma Microsoft zaleca, Dodaj azureMobile.js do pliku .gitignore (lub innych kontroli kodu źródłowego Ignoruj pliku) aby zapobiec hasła są przechowywane w chmurze. Zawsze skonfigurować ustawienia produkcji w **ustawień aplikacji** w [Azure Portal].

### <a name="howto-appsettings"></a>Konfiguruj ustawienia aplikacji dla aplikacji mobilnej
Większość ustawień w pliku azureMobile.js ma ustawienia aplikacji odpowiednik [Azure Portal]. Skorzystaj z poniższej listy, aby skonfigurować aplikację w **ustawień aplikacji**:

| Ustawienia aplikacji | Ustawienie azureMobile.js | Opis | Prawidłowe wartości |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |name |Nazwa aplikacji |ciąg |
| **MS_MobileLoggingLevel** |logging.level |Poziom minimalna dziennika komunikatów do zarejestrowania |błąd, ostrzeżenie, informacje o verbose, debug, niemądre |
| **MS_DebugMode** |debuguj |Włącza lub wyłącza tryb debugowania |wartość true, false |
| **MS_TableSchema** |data.schema |Domyślna nazwa schematu dla tabel SQL |ciąg (domyślne: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Włącza lub wyłącza tryb debugowania |wartość true, false |
| **MS_DisableVersionHeader** |Wersja (Ustaw do niezdefiniowanego) |Wyłącza nagłówka X-ZUMO-Server-Version |wartość true, false |
| **MS_SkipVersionCheck** |skipversioncheck |Wyłącza sprawdzanie wersji klienta interfejsu API |wartość true, false |

Można skonfigurować ustawienie aplikacji:

1. Zaloguj się w [Azure Portal].
2. Wybierz **wszystkie zasoby** lub **usługi aplikacji**, a następnie wybierz nazwę aplikacji mobilnej.
3. **Ustawienia** okienko jest otwierany domyślnie. Jeśli nie wybierz opcję **ustawienia**.
4. Na **ogólne** menu, wybierz opcję **ustawienia aplikacji**.
5. Przewiń do **ustawień aplikacji** sekcji.
6. Jeśli aplikacja, ustawienia już istnieje, wybierz wartość ustawienia aplikacji, aby edytować wartość.
   Jeśli ustawienia aplikacji nie istnieje, wprowadź ustawienie aplikacji w **klucza** pola i wartość w **wartość** pole.
8. Wybierz pozycję **Zapisz**.

Zmiana większość ustawień aplikacji wymaga ponownego uruchomienia usługi.

### <a name="howto-use-sqlazure"></a>Użyj bazy danych SQL jako danych produkcyjnych przechowywania
<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Przy użyciu bazy danych SQL Azure jako magazynu danych jest identyczne we wszystkich typów aplikacji w usłudze Azure App Service. Jeśli użytkownik nie zostało zrobione już, wykonaj następujące kroki, aby utworzyć zaplecze aplikacji mobilnej:

1. Zaloguj się w [Azure Portal].
2. W lewym górnym rogu okna wybierz **+ nowy** przycisk > **sieci Web i mobilność** > **aplikacji mobilnej**, a następnie podaj nazwę dla zaplecza aplikacji mobilnej.
3. W **grupy zasobów** wprowadź takiej samej nazwie co aplikacja.
4. Wartość domyślna jest wybrany plan usługi aplikacji. Jeśli chcesz zmienić swój plan usługi aplikacji:

   a. Wybierz **planu usługi aplikacji** > **+ Utwórz nowe**. 
   
   b. Podaj nazwę nowego planu usługi aplikacji i wybierz odpowiednią lokalizację. 
   
   c. Wybierz odpowiednią warstwę cenową dla usługi. Wybierz **Wyświetl wszystkie** do widoku więcej cennik opcje, takie jak **wolne** i **Shared**. 
   
   d. Kliknij przycisk **wybierz** przycisku. 
   
   e. W **planu usługi aplikacji** okienku wybierz **OK**.
5. Wybierz pozycję **Utwórz**. 

Inicjowanie obsługi administracyjnej Mobile Apps zaplecza może potrwać kilka minut. Po kopii Mobile Apps zakończenia zostanie zainicjowana, otwiera portalu **ustawienia** okienko do zaplecza aplikacji mobilnej.

Można połączyć się z zaplecza aplikacji mobilnej z istniejącej bazy danych SQL lub Utwórz nową bazę danych SQL. W tej sekcji utworzymy bazy danych SQL.

> [!NOTE]
> Jeśli masz już bazę danych w tej samej lokalizacji jako zaplecza aplikacji mobilnej, zamiast tego można wybrać **Użyj istniejącej bazy danych** , a następnie wybrać tę bazę danych. Nie zaleca się korzystanie z bazy danych w innej lokalizacji z powodu większych opóźnień.
>
>

1. Nowego zaplecza aplikacji mobilnej, wybierz **ustawienia** > **aplikacji mobilnej** > **danych** > **+ Dodaj**.
2. W **Dodaj połączenie danych** okienku wybierz **baza danych SQL — Skonfiguruj wymagane ustawienia** > **Utwórz nową bazę danych**. Wprowadź nazwę nowej bazy danych w **nazwa** pole.
3. Wybierz **serwera**. W **nowy serwer** okienku, wprowadź unikatową nazwą serwera w **nazwy serwera** polu i podaj odpowiedni serwer logowania administratora i hasła. Upewnij się, że **Zezwalaj usługom platformy azure na dostęp do serwera** jest zaznaczone. Kliknij przycisk **OK**.

   ![Tworzenie bazy danych SQL Azure][6]
4. W **nową bazę danych** okienku wybierz **OK**.
5. W **Dodaj połączenie danych** okienku wybierz **ciąg połączenia**i wprowadź identyfikator logowania i hasło, podane podczas tworzenia bazy danych. Jeśli używasz istniejącej bazy danych, należy podać poświadczenia logowania dla tej bazy danych. Kliknij przycisk **OK**.
6. W **Dodaj połączenie danych** okienko ponownie, wybierz opcję **OK** utworzyć bazę danych.

<!--- END OF ALTERNATE INCLUDE -->

Utworzenie bazy danych może potrwać kilka minut. Użyj **powiadomienia** obszaru do monitorowania postępu wdrożenia. Nie postępu aż do bazy danych został wdrożony pomyślnie. Po wdrożeniu bazy danych, ciąg połączenia jest tworzony dla wystąpienia bazy danych SQL w ustawieniach aplikacji zaplecza aplikacji mobilnej. Można wyświetlić to ustawienie aplikacji w **ustawienia** > **ustawienia aplikacji** > **parametry połączenia**.

### <a name="howto-tables-auth"></a>Wymagaj uwierzytelniania dostępu do tabel
Jeśli chcesz użyć uwierzytelniania aplikacji usługi z `tables` punktu końcowego, należy skonfigurować uwierzytelnianie usługi aplikacji w [Azure Portal] pierwszy. Aby uzyskać więcej informacji zobacz przewodnik konfiguracji dostawcy tożsamości, który ma być używany:

* [Konfigurowanie uwierzytelniania usługi Azure Active Directory]
* [Konfigurowanie uwierzytelniania serwisu Facebook]
* [Konfigurowanie uwierzytelniania serwisu Google]
* [Konfigurowanie uwierzytelniania firmy Microsoft]
* [Konfigurowanie uwierzytelniania usługi Twitter]

Każda tabela ma dostęp do właściwości, która służy do kontrolowania dostępu do tabeli. Poniższy przykład przedstawia statycznie zdefiniowanych tabeli z wymagane uwierzytelnienie.

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

Właściwość dostępu można wykonać jedną z trzech wartości:

* *anonimowe* wskazuje, czy aplikacja kliencka może odczytać danych bez uwierzytelniania.
* *uwierzytelniony* oznacza, że aplikacja kliencka musi wysłany token uwierzytelniania prawidłowe z żądaniem.
* *wyłączone* wskazuje, że ta tabela jest obecnie wyłączona.

Jeśli zdefiniowano właściwość dostęp jest dozwolony dostęp nieuwierzytelniony.

### <a name="howto-tables-getidentity"></a>Użyj uwierzytelniania oświadczeń z tabel
Można skonfigurować różne oświadczenia, które są wymagane podczas konfigurowania uwierzytelniania. Te oświadczenia nie są zwykle dostępne za pośrednictwem `context.user` obiektu. Jednak można je odzyskać za pomocą `context.user.getIdentity()` metody. `getIdentity()` Metoda zwraca promise, który jest rozpoznawany jako obiekt. Obiekt jest wyznaczaną przez metodę uwierzytelniania (`facebook`, `google`, `twitter`, `microsoftaccount`, lub `aad`).

Na przykład jeśli skonfigurowano uwierzytelnianie konta Microsoft i żądania oświadczeń adresów e-mail, można dodać adres e-mail do rekordu o następujący kontroler tabeli:

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

Aby zobaczyć, jakie oświadczenia są dostępne, użyj przeglądarki sieci web, aby wyświetlić `/.auth/me` punktu końcowego witryny.

### <a name="howto-tables-disabled"></a>Wyłącz dostęp do operacji określonej tabeli
Oprócz wymienionych w tabeli, właściwości dostępu można można użyć do kontrolowania poszczególnych działań. Istnieją cztery operacje:

* `read` jest operacji RESTful GET, w tabeli.
* `insert` jest operacji RESTful POST w tabeli.
* `update` jest poprawka RESTful operacji w tabeli.
* `delete` jest operacja RESTful usuwania w tabeli.

Na przykład można podać nieuwierzytelnione tabeli tylko do odczytu:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Read-only table. Only allow READ operations.
    table.read.access = 'anonymous';
    table.insert.access = 'disabled';
    table.update.access = 'disabled';
    table.delete.access = 'disabled';

    module.exports = table;

### <a name="howto-tables-query"></a>Dostosuj kwerendę, która jest używana z operacjami tabeli
Typowe wymagania dotyczące operacji tabeli jest zapewnienie ograniczony widok danych. Na przykład można podać tabelę, która zostanie oznaczony przy użyciu Identyfikatora uwierzytelnionego użytkownika w taki sposób, że można tylko do odczytu lub aktualizacji własnych rekordów. Oferuje następujące definicji tabeli:

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

Operacje, które są zazwyczaj uruchamiane zapytania ma właściwość zapytania, który można dostosować za pomocą `where` klauzuli. Właściwość kwerendy jest [QueryJS] może przetwarzać obiekt, który służy do konwertowania zapytanie OData do coś czy danych zaplecza. W przypadku prostego równości (na przykład mieszanego) można użyć mapy. Możesz także dodać punkty SQL:

    context.query.where('myfield eq ?', 'value');

### <a name="howto-tables-softdelete"></a>Konfigurowanie usuwania nietrwałego dla tabeli
Faktycznie usuwania nietrwałego nie powoduje usunięcia rekordów. Zamiast tego oznacza je jako usunięte w bazie danych, ustawiając usuniętą kolumnę na wartość true. Zestaw SDK aplikacji usługi Mobile automatycznie usuwa wszystkie usunięte nietrwale rekordy z wyników, o ile nie korzysta z zestawu SDK klienta usługi Mobile `IncludeDeleted()`. Aby skonfigurować tabelę dla usuwania nietrwałego, ustaw `softDelete` właściwość w pliku definicji tabeli:

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

Należy określić mechanizm usuwania rekordów: aplikacji klienckiej, zadanie WebJob, funkcja Azure lub niestandardowego interfejsu API.

### <a name="howto-tables-seeding"></a>Inicjatora bazy danych z danymi
Podczas tworzenia nowej aplikacji, możesz chcieć inicjatora tabelę z danymi. Można to zrobić w pliku JavaScript definicji tabeli w następujący sposób:

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

Wstępne wypełnianie danych odbywa się tylko wtedy, gdy był używany zestaw SDK usługi Mobile Apps do tworzenia tabeli. Jeśli tabela już istnieje w bazie danych, bez danych jest dodane do tabeli. Jeśli włączono dynamiczne schematu schematu jest wywnioskowany na podstawie wprowadzonych danych.

Zaleca się, że można jawnie wywołać `tables.initialize()` metodę w celu utworzenia tabeli, gdy usługa zacznie działać.

### <a name="Swagger"></a>Włącz obsługę programu Swagger
Aplikacje mobilne jest dostarczany z wbudowanych [Swagger] obsługuje. Aby włączyć obsługę programu Swagger, należy najpierw zainstalować interfejsu użytkownika programu swagger jako zależność:

    npm install --save swagger-ui

Następnie można włączyć obsługę struktury Swagger w Konstruktorze Mobile Apps:

    var mobile = azureMobileApps({ swagger: true });

Możesz tylko prawdopodobnie chcesz włączyć obsługi struktury Swagger w wersjach programowanie. Można to zrobić za pomocą `NODE_ENV` ustawienie aplikacji:

    var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });

`swagger` Punktu końcowego znajduje się pod adresem http://*yoursite*.azurewebsites.net/swagger. Można uzyskać dostępu do interfejsu użytkownika programu Swagger za pomocą `/swagger/ui` punktu końcowego. Jeśli wybierzesz opcję Wymagaj uwierzytelniania na całej aplikacji, Swagger powoduje błąd. Aby uzyskać najlepsze wyniki, wybierz, aby zezwalać nieuwierzytelnione żądania w ustawieniach usługi aplikacji Azure uwierzytelniania/autoryzacji, a następnie kontrolować uwierzytelniania za pomocą `table.access` właściwości.

Można również dodać opcji programu Swagger do pliku azureMobile.js, jeśli chcesz tylko Swagger obsługę programowania lokalnie.

## <a name="a-namepushpush-notifications"></a><a name="push">Powiadomienia wypychane
Aplikacje mobilne integruje się z usługi Azure Notification Hubs, możesz wysłać powiadomienia wypychane docelowe do milionów urządzeń na wszystkich platformach głównych. Przy użyciu usługi Notification Hubs, można wysyłać powiadomienia wypychane do systemu iOS, Android i Windows urządzeń. Aby dowiedzieć się więcej na temat wszystkich, które można wykonać przy użyciu usługi Notification Hubs, zobacz [usługi Notification Hubs — omówienie](../notification-hubs/notification-hubs-push-notification-overview.md).

### </a><a name="send-push"></a>Wysyłanie powiadomień wypychanych
Poniższy kod przedstawia sposób użycia `push` obiekt do wysyłania powiadomień wypychanych emisji do urządzeń z systemem iOS zarejestrowanego:

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

Tworząc rejestracji wypychanych szablonu z klienta, można zamiast tego Wyślij wiadomość wypychania szablonu na urządzeniach, na wszystkich obsługiwanych platformach. Poniższy kod przedstawia sposób wysłania powiadomienia szablonu:

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


### <a name="push-user"></a>Wysyłanie powiadomień wypychanych do uwierzytelnionego użytkownika przy użyciu tagów
Uwierzytelniony użytkownik rejestrujący dla powiadomień wypychanych, tag identyfikator użytkownika jest automatycznie dodawany do rejestracji. Za pomocą tego tagu, można wysyłać powiadomienia wypychane do wszystkich urządzeń zarejestrowanych przez określonego użytkownika. Poniższy kod pobiera identyfikator SID użytkownika, który wysłał żądanie i wyśle powiadomienie wypychane szablonu do rejestracji urządzeń dla tego użytkownika:

    // Only do the push if configured.
    if (context.push) {
        // Send a notification to the current user.
        context.push.send(context.user.id, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

Podczas rejestrowania jest możesz dla powiadomień wypychanych z uwierzytelnionego klienta, upewnij się, że zakończeniem uwierzytelniania przed podjęciem próby rejestracji.

## <a name="CustomAPI"></a> Niestandardowych interfejsów API
### <a name="howto-customapi-basic"></a>Definiowanie niestandardowego interfejsu API
Oprócz interfejsu API dostępu do danych za pośrednictwem `/tables` punktu końcowego, Mobile Apps zapewniają niestandardowe pokrycia interfejsu API. Niestandardowe interfejsy API są definiowane w sposób podobny do definicji tabeli i mogą uzyskiwać dostęp do tych samych urządzeń, włącznie z uwierzytelnianiem.

Jeśli chcesz użyć uwierzytelniania usługi aplikacji z niestandardowego interfejsu API, należy skonfigurować uwierzytelnianie usługi aplikacji w [Azure Portal] pierwszy. Aby uzyskać więcej informacji zobacz przewodnik konfiguracji dostawcy tożsamości, który ma być używany:

* [Konfigurowanie uwierzytelniania usługi Azure Active Directory]
* [Konfigurowanie uwierzytelniania serwisu Facebook]
* [Konfigurowanie uwierzytelniania serwisu Google]
* [Konfigurowanie uwierzytelniania firmy Microsoft]
* [Konfigurowanie uwierzytelniania usługi Twitter]

Niestandardowych interfejsów API są zdefiniowane w znacznie taki sam sposób jak tabele interfejsu API:

1. Utwórz `api` katalogu.
2. Utwórz plik JavaScript definicji interfejsu API w `api` katalogu.
3. Metoda importu do importowania `api` katalogu.

Oto prototypu definicji interfejsu API na podstawie próbki basic aplikacji, które zostały użyte wcześniej:

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

Spójrzmy na przykład interfejs API, który zwraca datę serwera przy użyciu `Date.now()` metody. Oto plik api/date.js:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };

    module.exports = api;

Każdy parametr jest jednym z zleceń standardowych RESTful: GET, POST, PATCH lub usunąć. Metoda jest standardem [oprogramowanie pośredniczące ExpressJS] funkcja, która wysyła wymagane dane wyjściowe.

### <a name="howto-customapi-auth"></a>Wymagaj uwierzytelniania dostępu do niestandardowego interfejsu API
Zestaw SDK usługi Mobile Apps implementuje uwierzytelniania w taki sam sposób dla obu `tables` punktu końcowego i niestandardowych interfejsów API. Aby dodać uwierzytelniania interfejsu API w poprzedniej sekcji, Dodaj `access` właściwości:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };
    // All methods must be authenticated.
    api.access = 'authenticated';

    module.exports = api;

Można również określić uwierzytelniania na określonych operacji:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        }
    };
    // The GET methods must be authenticated.
    api.get.access = 'authenticated';

    module.exports = api;

Ten sam token, który służy do `tables` punktu końcowego muszą być używane do niestandardowych interfejsów API, która wymaga uwierzytelniania.

### <a name="howto-customapi-auth"></a>Obsługa wysyłania dużych plików
Korzysta z zestawu SDK aplikacji mobilnych [oprogramowanie pośredniczące treści analizator](https://github.com/expressjs/body-parser) do akceptowania i dekodowania zawartości w treści w Twoje zgłoszenie. Można wstępnie skonfigurować analizator treści do przyjęcia przekazywania plików większych:

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

Plik jest algorytmem przed transmisją base-64. Ten typ kodowania spowoduje zwiększenie rozmiaru rzeczywistego przekazywania (i rozmiar, który należy uwzględnić).

### <a name="howto-customapi-sql"></a>Wykonywanie instrukcji SQL niestandardowych
Zestaw SDK usługi Mobile Apps umożliwia dostęp do całego kontekstu za pośrednictwem obiektu żądania. Można łatwo wykonywania sparametryzowanych instrukcji SQL dla dostawcy danych:

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

## <a name="Debugging"></a>Debugowanie, łatwe tabel i łatwe interfejsów API
### <a name="howto-diagnostic-logs"></a>Debugowanie, diagnozowanie i rozwiązywanie problemów z Mobile Apps
Usługa aplikacji Azure zapewnia kilka debugowania i rozwiązywanie problemów z techniki aplikacji Node.js.
Aby rozpocząć pracę w rozwiązywaniu problemów z zaplecza Node.js Mobile Apps, zobacz następujące artykuły:

* [Monitorowanie usługi aplikacji Azure]
* [Włączanie rejestrowania diagnostyki w usłudze Azure App Service]
* [Rozwiązywanie problemów z usługi aplikacji Azure w programie Visual Studio]

Aplikacje środowiska node.js mają dostęp do szeroką gamę narzędzi diagnostycznych dziennika. Wewnętrznie, zestaw SDK usługi Mobile Apps Node.js używa [Winston] dla rejestrowania diagnostycznego. Po włączeniu debugowania jest automatycznie włączone rejestrowanie tryb lub zestawu `MS_DebugMode` ustawienia aplikacji na wartość true w [Azure Portal]. Wygenerowany dzienników pojawia się w dziennikach diagnostycznych w [Azure Portal].

### <a name="in-portal-editing"></a><a name="work-easy-tables"></a>Praca z tabelami łatwy w portalu Azure
Łatwe tabel umożliwia tworzenie i Praca z tabelami bezpośrednio w portalu. Możesz przekazać do tabel łatwe zestawu danych w formacie CSV. Należy pamiętać, że nie można użyć nazwy właściwości (w zestawie danych CSV), które są w konflikcie z nazwami właściwości systemu zaplecza aplikacji mobilnej. Nazwy właściwości systemu są:
* CreatedAt
* updatedAt
* usunięte
* wersja

Operacje tabeli nawet można edytować za pomocą edytora usługi aplikacji. Po wybraniu **łatwe tabel** w ustawieniach lokacji zaplecza może Dodawanie, modyfikowanie lub usuwanie tabeli. Można również wyświetlić dane w tabeli.

![Praca z tabelami łatwe](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

Poniższe polecenia są dostępne na pasku poleceń dla tabeli:

* **Zmienianie uprawnień**: modyfikować uprawnienia do odczytu, wstawiania, aktualizowania i usuwania operacji względem tabeli.
 Aby zezwolić na dostęp anonimowy, aby wymagać uwierzytelniania lub wyłącz dostęp do operacji są opcje.
* **Edytowanie skryptu**: plik skryptu dla tabeli jest otwarty w edytorze usługi aplikacji.
* **Zarządzanie schematem**: Dodawanie lub usuwanie kolumn lub zmiana indeksu tabeli.
* **Wyczyść tabeli**: obcięcia istniejącej tabeli przez usunięcie wszystkich wierszy danych, ale pozostawiając schematu.
* **Usuwanie wierszy**: Usuń poszczególne wiersze danych.
* **Wyświetl podgląd dzienników przesyłanych strumieniowo**: połączenie do przesyłania strumieniowego usługi rejestrowania dla witryny.

### <a name="work-easy-apis"></a>Praca z interfejsami API łatwy w portalu Azure
Łatwe interfejsów API umożliwia tworzenie i Praca z niestandardowych interfejsów API bezpośrednio w portalu. Skrypty interfejsu API można edytować za pomocą edytora usługi aplikacji.

Po wybraniu **łatwe interfejsów API** w ustawieniach lokacji zaplecza może Dodawanie, modyfikowanie lub usuwanie niestandardowych punkt końcowy interfejsu API.

![Praca z łatwe interfejsów API](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

W portalu można zmienić uprawnienia dostępu dla akcji HTTP, Edytuj plik skryptu interfejsu API w edytorze usługi aplikacji lub Wyświetl dzienniki przesyłania strumieniowego.

### <a name="online-editor"></a>Edytowanie kodu w edytorze usługi aplikacji
Za pomocą portalu Azure, można edytować pliki skryptów zaplecza Node.js w edytorze usługi aplikacji bez konieczności pobierania projektu na komputerze lokalnym. Aby edytować pliki skryptów w edytorze online:

1. W okienku wybierz dla zaplecza aplikacji mobilnej **wszystkie ustawienia** > albo **łatwe tabel** lub **łatwe interfejsów API**. Wybierz tabelę lub interfejsu API, a następnie wybierz **edytowanie skryptu**. Plik skryptu zostanie otwarty w edytorze usługi aplikacji.

   ![Edytor usługi App Service](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)
2. Wprowadź zmiany w pliku kodu w edytorze online. Zmiany zostaną zapisane automatycznie podczas pisania.

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Szybki Start kliencką dla systemu android]: app-service-mobile-android-get-started.md
[Szybki Start Apache Cordova klienta]: app-service-mobile-cordova-get-started.md
[iOS — Szybki Start klienta]: app-service-mobile-ios-get-started.md
[Szybki Start klienta platformy Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
[Szybki Start klienta platformy Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Szybki Start klienta platformy Xamarin.Forms]: app-service-mobile-xamarin-forms-get-started.md
[Szybki Start klienta Sklepu Windows]: app-service-mobile-windows-store-dotnet-get-started.md
[Synchronizacja danych w trybie offline]: app-service-mobile-offline-data-sync.md
[Konfigurowanie uwierzytelniania usługi Azure Active Directory]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Konfigurowanie uwierzytelniania serwisu Facebook]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[Konfigurowanie uwierzytelniania serwisu Google]: ../app-service/app-service-mobile-how-to-configure-google-authentication.md
[Konfigurowanie uwierzytelniania firmy Microsoft]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[Konfigurowanie uwierzytelniania usługi Twitter]: ../app-service/app-service-mobile-how-to-configure-twitter-authentication.md
[Przewodnik wdrażania usługi aplikacji Azure]: ../app-service/app-service-deploy-local-git.md
[Monitorowanie usługi aplikacji Azure]: ../app-service/web-sites-monitor.md
[Włączanie rejestrowania diagnostyki w usłudze Azure App Service]: ../app-service/web-sites-enable-diagnostic-log.md
[Rozwiązywanie problemów z usługi aplikacji Azure w programie Visual Studio]: ../app-service/web-sites-dotnet-troubleshoot-visual-studio.md
[Określ wersję węzła]: ../nodejs-specify-node-version-azure-apps.md
[Użyj węzła modułów]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: http://expressjs.com/
[Swagger]: http://swagger.io/

[Azure Portal]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[Przykładem basicapp w witrynie GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[Przykładem ToDo w witrynie GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[Katalog przykładów w witrynie GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Narzędzia node.js Tools 1.1 dla programu Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[MSSQL Node.js pakietu]: https://www.npmjs.com/package/mssql
[Program Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[Oprogramowanie pośredniczące ExpressJS]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
