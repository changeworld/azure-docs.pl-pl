---
title: Rozpoczynanie pracy z zestawem SDK usługi Azure CDN dla środowiska Node.js | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pisanie aplikacji Node.js do zarządzania usługi Azure CDN.
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 46ae8cd9775432d126cbde856c1fb06ea319297e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
ms.locfileid: "23843196"
---
# <a name="get-started-with-azure-cdn-development"></a>Rozpoczynanie pracy z wdrażaniem usługi Azure CDN
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Można użyć [Azure CDN SDK dla środowiska Node.js](https://www.npmjs.com/package/azure-arm-cdn) można zautomatyzować tworzenie i zarządzanie profilami sieci CDN i punktów końcowych.  Ten samouczek przeprowadza przez tworzenie prostej aplikacji konsoli Node.js, która przedstawia niektóre z dostępnych operacji.  W tym samouczku nie ma na celu opis wszystkich aspektów zestaw SDK usługi Azure CDN dla środowiska Node.js szczegółowo.

Do ukończenia tego samouczka, powinien zostać [Node.js](http://www.nodejs.org) **4.x.x** lub nowszy zainstalowany i skonfigurowany.  Można użyć dowolnego edytora tekstów, aby utworzyć aplikację Node.js.  Można zapisać w tym samouczku, używany [Visual Studio Code](https://code.visualstudio.com).  

> [!TIP]
> [Ukończone projektu z tego samouczka](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) jest dostępny do pobrania w witrynie MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Tworzenie projektu i Dodawanie zależności NPM
Teraz, opracowaliśmy grupę zasobów dla naszych profilów CDN i naszych uprawnienia aplikacji usługi Azure AD do zarządzania profilami sieci CDN i punkty końcowe w ramach tej grupy, możemy rozpocząć tworzenie aplikacji.

Utwórz folder do przechowywania aplikacji.  Z poziomu konsoli przy użyciu narzędzi Node.js w bieżącej ścieżce Ustaw bieżącą lokalizację tego nowego folderu, a inicjowanie projektu, wykonując:

    npm init

Zostanie następnie wyświetlona szereg pytań, można zainicjować projektu.  Aby uzyskać **punktu wejścia**, w tym samouczku używana *app.js*.  Moje innych wyborów w poniższym przykładzie jest widoczny.

![Dane wyjściowe init NPM](./media/cdn-app-dev-node/cdn-npm-init.png)

Nasze projektu teraz jest inicjowany z *packages.json* pliku.  Nasze projektu będzie korzystać z niektórych bibliotek Azure zawartych w pakietach NPM.  Użyjemy środowiska uruchomieniowego klienta Azure dla środowiska Node.js (ms-rest-azure) i biblioteki klienta usługi Azure CDN dla środowiska Node.js (azure-arm-cd).  Dodajmy tych do projektu jako zależności.

    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

Po zakończeniu pakiety instalowania, *package.json* pliku powinna wyglądać podobnie jak w tym przykładzie (wersja numery może różnić się):

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "azure-arm-cdn": "^0.2.1",
    "ms-rest-azure": "^1.14.4"
  }
}
```

Na koniec za pomocą edytora tekstu, Utwórz pusty plik tekstowy i zapisz go w katalogu głównym folderu naszych projektu jako *app.js*.  Jest teraz gotowy do rozpoczęcia pisania kodu.

## <a name="requires-constants-authentication-and-structure"></a>Wymaga uwierzytelniania, stałe i struktury
Z *app.js* Otwórz w edytorze naszych, załóż podstawowej struktury nasz program zapisywane.

1. Dodaj "wymaga" naszych pakietów NPM u góry z następujących czynności:
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. Musimy zdefiniować niektóre stałe, używanego przez naszych metod.  Dodaj następujące czynności.  Koniecznie Zastąp symbole zastępcze w tym  **&lt;nawiasy&gt;**, z własne wartości zgodnie z potrzebami.
   
    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";
   
    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Następnie firma Microsoft będzie wystąpienia klienta zarządzania w sieci CDN i nadaj naszych poświadczeń.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Jeśli używasz uwierzytelniania użytkownika, te dwa wiersze będą różnić się nieznacznie.
   
   > [!IMPORTANT]
   > Ten przykładowy kod należy używać, tylko jeśli wybierzesz do uwierzytelniania indywidualnych użytkowników zamiast nazwy głównej usługi.  Należy zachować ostrożność zabezpieczyć poświadczenia użytkownika i nie należy ich ujawniać.
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Należy zastąpić elementy w  **&lt;nawiasy&gt;**  przy użyciu prawidłowych informacji.  Aby uzyskać `<redirect URI>`, użyj przekierowania URI wprowadzona podczas rejestrowania aplikacji w usłudze Azure AD.
4. Nasze aplikacja konsolowa Node.js ma wykonać niektóre parametry wiersza polecenia.  Teraz zweryfikować, że co najmniej jedna został przekazany parametr.
   
   ```javascript
   //Collect command-line parameters
   var parms = process.argv.slice(2);
   
   //Do we have parameters?
   if(parms == null || parms.length == 0)
   {
       console.log("Not enough parameters!");
       console.log("Valid commands are list, delete, create, and purge.");
       process.exit(1);
   }
   ```
5. Które nam wprowadzono do głównej części nasz program, w którym firma Microsoft gałęzie na inne funkcje oparte na jakie parametry zostały przekazane.
   
    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;
   
        case "create":
            cdnCreate();
            break;
   
        case "delete":
            cdnDelete();
            break;
   
        case "purge":
            cdnPurge();
            break;
   
        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```
6. W kilku miejscach nasz program potrzebujemy upewnij się, że prawa liczba parametrów zostały przekazane i wyświetlić Pomoc, jeśli nie przynosi poprawne.  Funkcje, w tym celu Utwórz.
   
   ```javascript
   function requireParms(parmCount) {
       if(parms.length < parmCount) {
           usageHelp(parms[0].toLowerCase());
           process.exit(1);
       }
   }
   
   function usageHelp(cmd) {
       console.log("Usage for " + cmd + ":");
       switch(cmd)
       {
           case "list":
               console.log("list profiles");
               console.log("list endpoints <profile name>");
               break;
   
           case "create":
               console.log("create profile <profile name>");
               console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
               break;
   
           case "delete":
               console.log("delete profile <profile name>");
               console.log("delete endpoint <profile name> <endpoint name>");
               break;
   
           case "purge":
               console.log("purge <profile name> <endpoint name> <path>");
               break;
   
           default:
               console.log("Invalid command.");
       }
   }
   ```
7. Na koniec funkcje, których będziemy używać w sieci CDN w warstwie klienta zarządzania są asynchroniczne, więc potrzebują metody do wywołania po ich wszystko gotowe.  Upewnij się, który można wyświetlić dane wyjściowe z sieci CDN w warstwie klienta zarządzania (jeśli istnieje) i bezpiecznie zamknąć program.
   
    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

Teraz, gdy podstawowa struktura nasz program napisano, utworzymy powinien funkcji o nazwie oparte na naszych parametrów.

## <a name="list-cdn-profiles-and-endpoints"></a>Lista profilów CDN i punkty końcowe
Zacznijmy od kodu do listy naszych istniejących profilów i punktów końcowych.  Komentarze w kodzie Podaj oczekiwanego składni, aby było wiadomo, gdzie każdy parametr przejdzie.

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Tworzenie profilów sieci CDN i punkty końcowe
Firma Microsoft będzie następnie należy napisać funkcje do tworzenia profilów i punktów końcowych.

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create(parms[2], standardCreateParameters, resourceGroupName, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(parms[3], endpointProperties, parms[2], resourceGroupName, callback);
}
```

## <a name="purge-an-endpoint"></a>Przeczyszczanie punktu końcowego
Zakładając, że utworzono punkt końcowy, jeden typowe zadania, które firma Microsoft może być wykonanie w nasz program jest przeczyszczanie zawartości w naszym punktu końcowego.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>Usuwanie punktów końcowych i profilów usługi CDN
Ostatniej funkcji, które firma Microsoft będzie zawierać usuwa punkty końcowe i profilów.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteIfExists(parms[2], resourceGroupName, callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteIfExists(parms[3], parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>Uruchomienie programu
Teraz możemy wykonywać naszego programu Node.js za pomocą naszych ulubionych debugera lub za pomocą konsoli.

> [!TIP]
> Jeśli używasz programu Visual Studio Code jako debuger, należy skonfigurować środowisko do przekazania parametrów wiersza polecenia.  Robi to programie Visual Studio Code **lanuch.json** pliku.  Wyszukaj właściwość o nazwie **argumentów** i Dodaj tablicę wartości ciągu parametry, aby wyglądały one podobnie do poniższego: `"args": ["list", "profiles"]`.
> 
> 

Zacznijmy od wyświetlania naszych profilów.

![Lista profilów](./media/cdn-app-dev-node/cdn-list-profiles.png)

Firma Microsoft otrzymano pustą tablicę.  Ponieważ nie mamy żadnych profilów w naszej grupy zasobów, która oczekuje.  Teraz Utwórzmy profilu.

![Utwórz profil](./media/cdn-app-dev-node/cdn-create-profile.png)

Teraz możemy dodać punkt końcowy.

![Tworzenie punktu końcowego](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Ponadto umożliwia usunięcie naszych profilu.

![Usuwanie profilu](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Następne kroki
Aby wyświetlić ukończone projektu z tego przewodnika [Pobierz przykład](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74).

Odwołanie do zestawu SDK usługi Azure CDN dla środowiska Node.js, przejrzeć [odwołania](http://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

Aby znaleźć dodatkową dokumentację zestawu Azure SDK dla środowiska Node.js, Wyświetl [pełne odwołanie](http://azure.github.io/azure-sdk-for-node/).

Zarządzanie zasobami CDN za pomocą [PowerShell](cdn-manage-powershell.md).

