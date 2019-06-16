---
title: Rozpoczynanie pracy z zestawem SDK usługi Azure CDN dla środowiska Node.js | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć aplikacje Node.js, zarządzać usługą Azure CDN.
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
ms.openlocfilehash: 48a84520a61d19968b467091871459e21898dd5e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60564246"
---
# <a name="get-started-with-azure-cdn-development"></a>Rozpoczynanie pracy z wdrażaniem usługi Azure CDN
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Możesz użyć [zestawu SDK sieci CDN Azure dla środowiska Node.js](https://www.npmjs.com/package/azure-arm-cdn) zautomatyzować tworzenie i zarządzanie profilami usługi CDN i punktów końcowych.  Ten samouczek przedstawia kroki tworzenia prosta aplikacja konsolowa Node.js, który pokazuje niektóre z dostępnych operacji.  W tym samouczku nie jest przeznaczona do szczegółowego opisywania, wszystkie aspekty zestawu SDK usługi Azure CDN dla środowiska Node.js.

Do ukończenia tego samouczka, konto powinno mieć już [Node.js](https://www.nodejs.org) **4.x.x** lub nowszej zainstalowany i skonfigurowany.  Można użyć dowolnego edytora tekstów, w której chcesz utworzyć aplikację Node.js.  Do zapisu w tym samouczku, czy mogę używać [programu Visual Studio Code](https://code.visualstudio.com).  

> [!TIP]
> [Ukończone projektu z tego samouczka](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) jest dostępny do pobrania w witrynie MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Tworzenie projektu i Dodawanie zależności rozwiązania NPM
Teraz, gdy opracowaliśmy grupę zasobów dla naszych profilów usługi CDN i naszych uprawnienia aplikacji usługi Azure AD do zarządzania profilami usługi CDN i punkty końcowe w ramach tej grupy, Zaczniemy tworzenie naszej aplikacji.

Utwórz folder do przechowywania aplikacji.  Z poziomu konsoli za pomocą narzędzia Node.js w bieżącej ścieżce ustaw swoją bieżącą lokalizację ten nowy folder i inicjowanie projektu, wykonując:

    npm init

Następnie zobaczysz szereg pytań, aby zainicjować projektu.  Aby uzyskać **punktu wejścia**, w tym samouczku *app.js*.  Możesz zobaczyć moje inne opcje w poniższym przykładzie.

![Dane wyjściowe init NPM](./media/cdn-app-dev-node/cdn-npm-init.png)

Nasz projekt teraz jest inicjowany za pomocą *packages.json* pliku.  Nasz projekt będzie używać niektórych bibliotek platformy Azure zawartych w pakiety NPM.  Użyjemy środowiska uruchomieniowego klienta platformy Azure dla środowiska Node.js (ms-rest-azure) i biblioteki klienta usługi Azure CDN dla platformy Node.js (azure-arm-cd).  Dodajmy tych do projektu jako zależności.

    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

Po wykonaniu czynności są pakiety instalacji *package.json* plik powinien wyglądać podobnie do tego przykładu (wersja liczb może się różnić):

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

Na koniec za pomocą edytora tekstu, Utwórz pusty plik tekstowy i zapisz go w katalogu głównym folderu naszego projektu jako *app.js*.  Teraz jesteśmy gotowi do rozpoczęcia pisania kodu.

## <a name="requires-constants-authentication-and-structure"></a>Wymaga uwierzytelniania, stałe i struktury
Za pomocą *app.js* Otwórz w naszego edytora, Pozwól nam podstawowa struktura nasz program napisany.

1. Dodaj "wymaga" dla naszych pakietów NPM u góry z następujących czynności:
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. Należy zdefiniować kilka stałych, używanymi przez naszych metod.  Dodaj następujący fragment kodu.  Koniecznie Zastąp symbole zastępcze, w tym  **&lt;nawiasy kątowe&gt;** , przy użyciu własnych wartości, zgodnie z potrzebami.
   
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
3. Następnie utworzymy wystąpienia klienta zarządzania sieci CDN i Wypróbuj nasz poświadczeń.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Jeśli używasz uwierzytelniania użytkownika, te dwa wiersze będą różnić się nieznacznie.
   
   > [!IMPORTANT]
   > Ten przykładowy kod należy używać tylko, gdy decyduje się zastosować uwierzytelnianie poszczególnych użytkowników, zamiast nazwy głównej usługi.  Uważaj je przed nieprzewidzianymi poświadczenia użytkownika i nie należy ich ujawniać.
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Należy zastąpić elementy w **&lt;nawiasy kątowe&gt;** przy użyciu poprawnych informacji.  Aby uzyskać `<redirect URI>`, użyj wprowadzona podczas rejestrowania aplikacji w usłudze Azure AD identyfikator URI przekierowania.
4. Nasza Aplikacja konsoli Node.js zamierza wykonać niektóre parametry wiersza polecenia.  Sprawdźmy, że co najmniej jeden parametr został przekazany.
   
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
5. Który ma dla nas główną częścią naszego programu, gdzie możemy gałęzie do innych funkcji, w oparciu o parametry zostały przekazane.
   
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
6. W kilku miejscach w naszym programie będzie musimy upewnić się, że odpowiednią liczbę parametrów przekazano i wyświetlić pomocy, jeśli nie wygląda prawidłowe.  Utwórzmy funkcji, aby to zrobić.
   
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
7. Na koniec funkcje, których będziemy używać na kliencie zarządzania sieci CDN są asynchroniczne, związku z czym muszą metodę do wywołania po one gotowe.  Upewnijmy się, który można wyświetlić dane wyjściowe z klienta zarządzania sieci CDN (jeśli istnieje) i bezpiecznie zamknąć program.
   
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

Teraz, gdy podstawowa struktura nasz program został napisany, utworzymy powinien funkcji o nazwie oparte na naszych parametrów.

## <a name="list-cdn-profiles-and-endpoints"></a>Lista profilów usługi CDN i punktów końcowych
Zacznijmy od kodu, aby wyświetlić listę naszych istniejących profilów i punktów końcowych.  Komentarze w kodzie zapewniają oczekiwanego składni, aby było wiadomo, gdzie przejdzie każdego parametru.

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

## <a name="create-cdn-profiles-and-endpoints"></a>Tworzenie profilów CDN i punktów końcowych
Następnie będziemy pisać funkcje do tworzenia profilów i punktów końcowych.

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

## <a name="purge-an-endpoint"></a>Przeczyszczanie punktu końcowego usługi
Przy założeniu, że utworzono punkt końcowy, co typowe zadanie, które chcemy przeprowadzić w naszym programie jest przeczyszczanie zawartości w naszej punktu końcowego.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>Usuń profile CDN i punktów końcowych
Ostatniej funkcji, które firma Microsoft dołącza Usuwa punktów końcowych i profilów.

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

## <a name="running-the-program"></a>Uruchamianie programu
Firma Microsoft może teraz wykonać nasz program Node.js za pomocą naszych ulubionych debugera lub z konsoli.

> [!TIP]
> Jeśli używasz programu Visual Studio Code jako debuger, musisz skonfigurować swoje środowisko do przekazywania parametrów wiersza polecenia.  Robi to programie Visual Studio Code **launch.json** pliku.  Wyszukaj właściwość o nazwie **args** i Dodaj tablicę wartości ciągów dla parametry, dzięki czemu będzie wyglądać podobnie do tego: `"args": ["list", "profiles"]`.
> 
> 

Zacznijmy od listą naszych profilów.

![Lista profilów](./media/cdn-app-dev-node/cdn-list-profiles.png)

Firma Microsoft otrzymano pustą tablicę.  Ponieważ wszystkie profile nie istnieje w naszym grupie zasobów, jest to oczekiwane.  Teraz Utwórz profil.

![Utwórz profil](./media/cdn-app-dev-node/cdn-create-profile.png)

Teraz możemy dodać punkt końcowy.

![Tworzenie punktu końcowego](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Na koniec spróbujmy usunąć naszych profilu.

![Usuń profil](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Następne kroki
Aby wyświetlić ukończone projekt z tego przewodnika [pobrać przykład](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74).

Aby zobaczyć odwołania do zestawu SDK usługi Azure CDN dla środowiska Node.js, Wyświetl [odwołania](https://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

Aby znaleźć dodatkową dokumentację zestawu Azure SDK dla środowiska Node.js, Wyświetl [pełna dokumentacja](https://azure.github.io/azure-sdk-for-node/).

Zarządzanie zasobami sieci CDN, za pomocą [PowerShell](cdn-manage-powershell.md).

