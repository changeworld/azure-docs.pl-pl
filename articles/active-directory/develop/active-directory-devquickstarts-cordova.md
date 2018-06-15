---
title: Azure AD oprogramowania Cordova wprowadzenie | Dokumentacja firmy Microsoft
description: Jak utworzyć aplikację Cordova, która integruje się z usługą Azure AD, logowania i wywołuje Azure interfejsów API, które są chronione przez usługi AD za pomocą uwierzytelniania OAuth.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: b1a8d7bd-7ad6-44d5-8ccb-5255bb623345
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 6d6d514875aa675bf160ee08a3e94b58944020ee
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156735"
---
# <a name="azure-ad-cordova-getting-started"></a>Azure AD oprogramowania Cordova wprowadzenie
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Apache Cordova służy do tworzenia aplikacji HTML5/JavaScript, które można uruchomić na urządzeniach przenośnych jako pełni funkcjonalnymi natywnych aplikacji. Usłudze Azure Active Directory (Azure AD) możesz dodać funkcje klasy korporacyjnej uwierzytelniania do aplikacji oprogramowania Cordova.

Wtyczka Cordova opakowuje Azure AD natywnych zestawów SDK w systemach iOS, Android, Sklep Windows i Windows Phone. Za pomocą, że dodatek plug-in, można rozszerzyć aplikacji do obsługi logowania przy użyciu kont usługi Active Directory systemu Windows Server użytkowników, uzyskiwanie dostępu do usługi Office 365 i interfejsów API usługi Azure, a nawet chronić wywołania do własnego niestandardowego interfejsu API w sieci web.

W tym samouczku Apache Cordova wtyczki dla Active Directory Authentication Library (ADAL) będą używane do poprawy prostej aplikacji przez dodanie następujących funkcji:

* Przy użyciu kilku wierszy kodu uwierzytelniania użytkownika i uzyskania tokenu.
* Użyj tego tokenu do wywołania interfejsu API programu Graph do tego katalogu zapytania i wyświetlić wyniki. 
* Użyj tokenu ADAL pamięci podręcznej, aby zminimalizować monitowania użytkownika o uwierzytelniania.

Aby wprowadzić te ulepszenia, musisz:

1. Zarejestrować aplikację w usłudze Azure AD.
2. Dodawanie kodu do aplikacji do żądania tokenów.
3. Dodaj kod, aby Użyj tokenu na potrzeby realizacji zapytania interfejsu API programu Graph i wyświetlić wyniki.
4. Utwórz projekt wdrożenia oprogramowania Cordova z platformami, które ma być docelowa, Dodaj Cordova ADAL wtyczki i przetestowania rozwiązania w emulatory.

## <a name="prerequisites"></a>Wymagania wstępne
Do ukończenia tego samouczka niezbędne są następujące elementy:

* Dzierżawa usługi Azure AD, gdzie masz konto z uprawnieniami rozwoju aplikacji.
* Środowisko deweloperskie, który jest skonfigurowany do używania oprogramowania Apache Cordova. 

Jeśli masz zarówno już skonfigurować, bezpośrednio przejść do kroku 1.

Jeśli nie masz dzierżawę usługi Azure AD, użyj [instrukcje dotyczące sposobu uzyskania jedną](active-directory-howto-tenant.md).

Jeśli nie masz Apache Cordova na komputerze, należy zainstalować następujące elementy:

* [Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [Node.js](https://nodejs.org/download/)
* [Interfejs Cordova CLI](https://cordova.apache.org/) (można łatwo zainstalować za pomocą Menedżera pakietów NPM: `npm install -g cordova`)

Poprzednie instalacje powinny działać zarówno na komputerze, jak i komputerów Mac.

Każda platforma docelowa ma inne wymagania wstępne:

* Aby skompilować i uruchomić aplikację dla komputerów typu Tablet z systemem Windows lub Windows Phone:
  * Zainstaluj [programu Visual Studio 2013 dla systemu Windows z Update 2 lub nowszym](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express lub inna wersja) lub [programu Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs#d-community).

* Aby skompilować i uruchomić aplikację dla systemu iOS:

  * Zainstaluj program Xcode 6.x lub nowszego. Pobierz go z [witryny dla deweloperów firmy Apple](http://developer.apple.com/downloads) lub [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?mt=12).
  * Zainstaluj [ios-sim](https://www.npmjs.org/package/ios-sim). Służy on do uruchomienia aplikacji systemu iOS w symulatora systemu iOS z wiersza polecenia. (Można go łatwo zainstalować przy użyciu terminala: `npm install -g ios-sim`.)
* Aby skompilować i uruchomić aplikację dla systemu Android:

  * Zainstaluj [Java Development Kit (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) lub nowszym. Upewnij się, że `JAVA_HOME` (zmienną środowiskową) jest poprawnie ustawiony odpowiednio do atrybutów JDK ścieżki instalacji (na przykład C:\Program Files\Java\jdk1.7.0_75).
  * Zainstaluj [zestawu SDK systemu Android](http://developer.android.com/sdk/installing/index.html?pkg=tools) i Dodaj `<android-sdk-location>\tools` lokalizację (na przykład C:\tools\Android\android-sdk\tools) do sieci `PATH` zmiennej środowiskowej.
  * Otwórz Menedżera zestawu SDK systemu Android (na przykład przy użyciu terminala: `android`) i zainstalować:
    * *5.0.1 systemu android (interfejs API 21)* zestawu SDK platformy
    * *Narzędzia kompilacji zestawu SDK dla systemu android* wersji 19.1.0 lub nowszej
    * *Obsługa systemu android repozytorium* (dodatki)

  Zestaw SDK systemu Android nie zapewnia żadnych domyślne wystąpienie emulatora. Utworzyć, uruchamiając `android avd` z terminala i wybierając **Utwórz**, jeśli chcesz uruchomić emulatora aplikacji systemu Android. Zaleca się poziom interfejsu API 19 lub nowszej. Aby uzyskać więcej informacji o opcji Android emulator i tworzenia, zobacz [Menedżera AVD](http://developer.android.com/tools/help/avd-manager.html) w witrynie systemu Android.

## <a name="step-1-register-an-application-with-azure-ad"></a>Krok 1: Rejestrowanie aplikacji w usłudze Azure AD
Ten krok jest opcjonalny. Ten samouczek zawiera wstępnie przygotowany wartości, których można użyć, aby zobaczyć przykładowy w akcji bez wykonywania żadnych alokacji w własną dzierżawę. Jednak zaleca się wykonać ten krok i zapoznać się z procesem, ponieważ będzie wymagane podczas tworzenia własnych aplikacji.

Usługi Azure AD wystawia tokeny tylko znane aplikacje. Zanim użyjesz usługi Azure AD z aplikacji, musisz utworzyć wpis dla niego w dzierżawie. Aby zarejestrować nową aplikację w dzierżawie:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na górnym pasku kliknij konto. W **katalogu** wybierz dzierżawy usługi Azure AD, które chcesz zarejestrować aplikację.
3. Kliknij przycisk **wszystkie usługi** w okienku po lewej stronie, a następnie wybierz **usługi Azure Active Directory**.
4. Kliknij przycisk **rejestracji aplikacji**, a następnie wybierz **Dodaj**.
5. Postępuj zgodnie z monitami i Utwórz **natywną aplikację kliencką**. (Chociaż aplikacje Cordova HTML na podstawie, tworzymy aplikację native client w tym miejscu. **Natywną aplikację kliencką** musi być zaznaczona opcja lub aplikacja nie będzie działać.)
  * **Nazwa** opisuje aplikacji dla użytkowników.
  * **Identyfikator URI przekierowania** jest identyfikator URI, który służy do zwracania tokenów do aplikacji. Wprowadź **http://MyDirectorySearcherApp**.

Po zakończeniu rejestracji usługi Azure AD przypisuje unikatowy identyfikator aplikacji do aplikacji. Będziesz potrzebować tę wartość w kolejnych sekcjach. Można go znaleźć na karcie aplikacji nowo utworzonej aplikacji.

Aby uruchomić `DirSearchClient Sample`, udziel uprawnienia nowo utworzoną aplikacją wykonać zapytania interfejsu API Azure AD Graph:

1. Z **ustawienia** wybierz pozycję **wymagane uprawnienia**, a następnie wybierz **Dodaj**. 
2. Dla aplikacji usługi Azure Active Directory wybierz **Microsoft Graph** jako interfejsu API i Dodaj **dostępu do katalogu jako zalogowany użytkownik** uprawnienie w obszarze **delegowane uprawnienia**. Dzięki temu aplikacja wykonać zapytania interfejsu API programu Graph dla użytkowników.

## <a name="step-2-clone-the-sample-app-repository"></a>Krok 2: Klonowanie repozytorium przykładowej aplikacji
Z powłoki lub wiersza polecenia wpisz następujące polecenie:

    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## <a name="step-3-create-the-cordova-app"></a>Krok 3: Tworzenie aplikacji Cordova
Istnieje wiele sposobów tworzenia aplikacji Cordova. W tym samouczku użyjemy Cordova interfejsu wiersza polecenia (CLI).

1. Z powłoki lub wiersza polecenia wpisz następujące polecenie:

        cordova create DirSearchClient

   To polecenie tworzy struktury folderów i funkcją szkieletów dla projektu oprogramowania Cordova.

2. Przenieś do nowego folderu DirSearchClient:

        cd .\DirSearchClient

3. Skopiuj zawartość projektu starter w podfolderze www przy użyciu Menedżera plików lub polecenie powłoki:

  * System Windows: `xcopy ..\NativeClient-MultiTarget-Cordova\DirSearchClient www /E /Y`
  * Mac: `cp -r  ../NativeClient-MultiTarget-Cordova/DirSearchClient/* www`

4. Dodaj wtyczkę dozwolonych. Jest to konieczne do wywoływania interfejsu API programu Graph.

        cordova plugin add cordova-plugin-whitelist

5. Dodaj wszystkie platformy, które mają być obsługiwane. Aby próbki roboczej, należy wykonać co najmniej jeden z następujących poleceń. Należy pamiętać, że nie będzie można emulować systemu iOS w systemie Windows lub emulacji systemu Windows na komputerach Mac.

        cordova platform add android
        cordova platform add ios
        cordova platform add windows

6. Dodawanie biblioteki ADAL dla wtyczki Cordova do projektu:

        cordova plugin add cordova-plugin-ms-adal

## <a name="step-4-add-code-to-authenticate-users-and-obtain-tokens-from-azure-ad"></a>Krok 4: Dodaj kod, aby uwierzytelniać użytkowników i uzyskać tokeny z usługi Azure AD
Aplikacja, która projektujesz w tym samouczku zapewni funkcji wyszukiwania prostego katalogu. Użytkownik może następnie wpisz alias każdego użytkownika w katalogu i wizualizacji niektóre podstawowe atrybuty. Projekt starter nie zawiera definicji podstawowy interfejs użytkownika aplikacji (w www/index.html) i funkcja szkieletów, który tworzącej zdarzeń Podstawowa aplikacja cykli powiązania interfejsu użytkownika i powoduje wyświetlanie logikę (www/js/index.js). Zadania po lewej dla Ciebie jest dodanie logiki, która implementuje zadania tożsamości.

W pierwszej kolejności należy wykonać w kodzie jest wprowadzenie wartości protokołu usługi Azure AD używane do identyfikowania Twojej aplikacji i zasobów przez docelowe. Te wartości ma być użyty do utworzenia tokenu żądania później. Wstaw następujący fragment kodu w górnej części pliku index.js:

```javascript
var authority = "https://login.microsoftonline.com/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

`redirectUri` i `clientId` wartości powinna być zgodna wartości, które opisują aplikacji w usłudze Azure AD. Można znaleźć od **Konfiguruj** karcie w portalu Azure, zgodnie z opisem w kroku 1 we wcześniejszej części tego samouczka.

> [!NOTE]
> Jeśli zostanie wybrana opcja nie rejestruje nową aplikację w dzierżawie własnych, możesz po prostu wkleić wstępnie skonfigurowane wartości, ponieważ jest. Możesz sprawdzić, przykładowe uruchomiona, że należy zawsze tworzyć własne wejścia dla aplikacji, które są przeznaczone do produkcji.

Następnie dodaj kod żądania tokenu. Wstaw następujący fragment kodu między `search` i `renderData` definicje:

```javascript
    // Shows the user authentication dialog box if required
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
Przeanalizujmy tej funkcji, dzieląc go w dwie główne części.
Ten przykład jest przeznaczona do pracy z dowolnej dzierżawy, a nie jest powiązana z jedna z nich. Używa "/ wspólnej" punktu końcowego, który umożliwia użytkownikowi wprowadzenie dowolne konto podczas uwierzytelniania i kieruje żądanie do dzierżawy, w którym należy.

To pierwsza część metoda sprawdza ADAL pamięci podręcznej, aby zobaczyć, czy token jest już zapisana. Jeśli tak, metoda korzysta z dzierżawcami gdzie token pochodzeniu dla ponowne zainicjowanie biblioteki ADAL. Jest to konieczne, aby uniknąć dodatkowych monitów, ponieważ użycie "/ wspólnej" zawsze powoduje pytaniem, użytkownik musi wprowadzić nowe konto.

```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
Druga część metoda wykonuje odpowiednie żądania tokenu. `acquireTokenSilentAsync` ADAL, aby zwrócić token dla określonego zasobu bez żadnych UX. przedstawiający zapyta — metoda Który może się zdarzyć, jeśli pamięć podręczna ma już token dostępu odpowiedniego przechowywane, lub Jeśli token odświeżania może służyć do Uzyskaj token dostępu nowe bez wyświetlania dowolnego wiersza. Jeśli kończy się niepowodzeniem, które firma Microsoft może wrócić `acquireTokenAsync`— które wizualnie monitują użytkownika do uwierzytelniania.

```javascript
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
Teraz, gdy mamy token możemy koniec wywołania interfejsu API programu Graph i wykonywać zapytania wyszukiwania, która ma. Wstaw następujący fragment kodu poniżej `authenticate` definicji:

```javascript
// Makes an API call to receive the user list
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
Pliki punktu początkowego dostarczona proste UX wprowadzania alias użytkownika w polu tekstowym. Ta metoda używa tej wartości do utworzenia kwerendy, połączyć ją z tokenu dostępu, wysyłają je do programu Microsoft Graph i przeanalizować wyniki. `renderData` Metody już istnieje w pliku punktu początkowego zajmuje się wizualizacja wyników.

## <a name="step-5-run-the-app"></a>Krok 5: Uruchamianie aplikacji
Aplikacja jest ostatecznie gotowy do uruchomienia. Jego działania jest prosty: po uruchomieniu aplikacji, wprowadź alias użytkownika, aby wyszukać, a następnie kliknij przycisk. Zostanie wyświetlony monit o uwierzytelnienie. Po pomyślnym uwierzytelnieniu i pomyślne wyszukiwania atrybutów wyszukiwanych użytkowników są wyświetlane.

Kolejnych uruchomieniach przeprowadzi wyszukiwanie bez wyświetlania dowolnego wiersza dzięki użyciu obecności wcześniej nabytych przez niego tokenu w pamięci podręcznej.

Konkretne kroki dotyczące uruchamiania aplikacji zależy od platformy.

### <a name="windows-10"></a>Windows 10
   Komputer typu Tablet: `cordova run windows --archs=x64 -- --appx=uap`

   Urządzenie przenośne (wymaga systemu Windows 10 Mobile urządzenie podłączone do komputera z systemem): `cordova run windows --archs=arm -- --appx=uap --phone`

   > [!NOTE]
   > Przy pierwszym uruchomieniu może zostać poproszona o logowania licencji dewelopera. Aby uzyskać więcej informacji, zobacz [licencji dewelopera](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).

### <a name="windows-81-tabletpc"></a>Komputer typu Tablet Windows 8.1
   `cordova run windows`

   > [!NOTE]
   > Przy pierwszym uruchomieniu może zostać poproszona o logowania licencji dewelopera. Aby uzyskać więcej informacji, zobacz [licencji dewelopera](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).

### <a name="windows-phone-81"></a>Windows Phone 8.1
   Aby uruchomić na połączonym urządzeniu: `cordova run windows --device -- --phone`

   Aby uruchomić emulatora domyślne: `cordova emulate windows -- --phone`

   Użyj `cordova run windows --list -- --phone` Aby wyświetlić wszystkie dostępne elementy docelowe i `cordova run windows --target=<target_name> -- --phone` do uruchamiania aplikacji dla określonego urządzenia lub emulatora (na przykład `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`).

### <a name="android"></a>Android
   Aby uruchomić na połączonym urządzeniu: `cordova run android --device`

   Aby uruchomić emulatora domyślne: `cordova emulate android`

   Upewnij się, że po utworzeniu wystąpienia emulatora za pomocą Menedżera AVD, zgodnie z wcześniejszym opisem w sekcji "Wymagania wstępne".

   Użyj `cordova run android --list` Aby wyświetlić wszystkie dostępne elementy docelowe i `cordova run android --target=<target_name>` do uruchamiania aplikacji dla określonego urządzenia lub emulatora (na przykład `cordova run android --target="Nexus4_emulator"`).

### <a name="ios"></a>iOS
   Aby uruchomić na połączonym urządzeniu: `cordova run ios --device`

   Aby uruchomić emulatora domyślne: `cordova emulate ios`

   > [!NOTE]
   > Upewnij się, że masz `ios-sim` zainstalowane do uruchamiania na emulatorze pakietu. Aby uzyskać więcej informacji zobacz sekcję "Wymagania wstępne".

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run the application on specific device or emulator (for example, `cordova run android --target="iPhone-6"`).

    Use `cordova run --help` to see additional build and run options.

## <a name="next-steps"></a>Kolejne kroki
Odwołanie, ukończonych próbka (bez wartości konfiguracji) są dostępne w [GitHub](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova/tree/complete/DirSearchClient).

Możesz teraz przejść do bardziej zaawansowanych (i bardziej interesującego) scenariuszy. Możesz spróbować: [zabezpieczyć interfejs API sieci Web Node.js w usłudze Azure AD](active-directory-devquickstarts-webapi-nodejs.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
