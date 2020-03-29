---
title: Wprowadzenie do zestawu SDK usługi Azure CDN dla pliku Node.js | Dokumenty firmy Microsoft
description: Dowiedz się, jak pisać aplikacje Node.js do zarządzania usługą Azure CDN.
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 18dbcbf93947306334ccc2c156d9266884198e19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594117"
---
# <a name="get-started-with-azure-cdn-development"></a>Rozpoczynanie pracy z wdrażaniem usługi Azure CDN
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Za pomocą zestawu [SDK usługi Azure CDN dla node.js](https://www.npmjs.com/package/azure-arm-cdn) można zautomatyzować tworzenie profili i punktów końcowych sieci CDN i zarządzanie nimi.  W tym samouczku przedstawiono utworzenie prostej aplikacji konsoli Node.js, która pokazuje kilka dostępnych operacji.  Ten samouczek nie jest przeznaczony do szczegółowego opisu wszystkich aspektów zestawu SDK usługi Azure CDN dla pliku Node.js.

Aby ukończyć ten samouczek, należy już mieć [node.js](https://www.nodejs.org) **4.x.x lub nowsze** zainstalowane i skonfigurowane.  Można użyć dowolnego edytora tekstu, który ma zostać utworzony przez aplikację Node.js.  Aby napisać ten samouczek, użyłem [Visual Studio Code](https://code.visualstudio.com).  

> [!TIP]
> [Ukończony projekt z tego samouczka](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) jest dostępny do pobrania na MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Tworzenie projektu i dodawanie zależności npm
Teraz, gdy utworzyliśmy grupę zasobów dla naszych profili usługi CDN i mamy uprawnienia aplikacji usługi Azure AD do zarządzania profilami i punktami końcowymi usługi CDN w tej grupie, możemy rozpocząć tworzenie naszej aplikacji.

Utwórz folder do przechowywania aplikacji.  Na konsoli z narzędziami Node.js w bieżącej ścieżce ustaw bieżącą lokalizację na ten nowy folder i zainiólj projekt, wykonując:

    npm init

Następnie zostanie przedstawiona seria pytań, aby zainicjować projekt.  Dla **punktu wejścia,** ten samouczek używa *app.js*.  Inne opcje można zobaczyć w poniższym przykładzie.

![Wyjście init NPM](./media/cdn-app-dev-node/cdn-npm-init.png)

Nasz projekt jest teraz inicjowany za pomocą pliku *packages.json.*  Nasz projekt będzie używać niektórych bibliotek platformy Azure zawartych w pakietach NPM.  Użyjemy środowiska uruchomieniowego klienta platformy Azure dla node.js (ms-rest-azure) i biblioteki klienta usługi Azure CDN dla node.js (azure-arm-cd).  Dodajmy je do projektu jako zależności.

    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

Po zakończeniu instalacji pakietów plik *package.json* powinien wyglądać podobnie do tego przykładu (numery wersji mogą się różnić):

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

Na koniec, za pomocą edytora tekstu, utwórz pusty plik tekstowy i zapisz go w katalogu głównym naszego folderu projektu jako *app.js*.  Teraz jesteśmy gotowi, aby rozpocząć pisanie kodu.

## <a name="requires-constants-authentication-and-structure"></a>Wymaga, stałych, uwierzytelniania i struktury
Z *app.js* otwarte w naszym edytorze, let's get podstawową strukturę naszego programu napisane.

1. Dodaj "wymaga" dla naszych pakietów NPM u góry, z następującymi:
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. Musimy zdefiniować pewne stałe, z których będą korzystać nasze metody.  Dodaj następujące elementy.  Pamiętaj, aby w razie potrzeby zastąpić symbole zastępcze, w tym ** &lt;wsporniki kątowe,&gt;** własnymi wartościami.
   
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
3. Następnie będziemy utworzyć wystąpienie klienta zarządzania siecią CDN i nadać mu nasze poświadczenia.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Jeśli używasz uwierzytelniania poszczególnych użytkowników, te dwa wiersze będą wyglądać nieco inaczej.
   
   > [!IMPORTANT]
   > Użyj tego przykładu kodu tylko wtedy, gdy zdecydujesz się na uwierzytelnianie poszczególnych użytkowników zamiast jednostki usługi.  Należy zachować ostrożność, aby chronić poświadczenia poszczególnych użytkowników i zachować je w tajemnicy.
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Pamiętaj, aby zastąpić elementy w ** &lt;nawiasach kątowych&gt; ** poprawnymi informacjami.  W `<redirect URI>`przypadku programu , użyj identyfikatora URI przekierowania wprowadzonego podczas rejestracji aplikacji w usłudze Azure AD.
4. Nasza aplikacja konsoli Node.js będzie przyjmować pewne parametry wiersza polecenia.  Sprawdźmy, czy co najmniej jeden parametr został przekazany.
   
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
5. To prowadzi nas do głównej części naszego programu, gdzie rozgałęziamy się na inne funkcje w oparciu o parametry, które zostały przekazane.
   
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
6. W kilku miejscach naszego programu musimy upewnić się, że odpowiednia liczba parametrów została przekazana i wyświetlić pomoc, jeśli nie wyglądają poprawnie.  Utwórzmy do tego funkcje.
   
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
7. Na koniec funkcje, które będą używane na kliencie zarządzania siecią CDN są asynchroniczne, więc potrzebują metody, aby oddzwonić po zakończeniu.  Zróbmy taki, który może wyświetlać dane wyjściowe z klienta zarządzania CDN (jeśli istnieje) i bezpiecznie wyjść z programu.
   
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

Teraz, gdy podstawowa struktura naszego programu jest napisana, powinniśmy tworzyć funkcje wywoływane w oparciu o nasze parametry.

## <a name="list-cdn-profiles-and-endpoints"></a>Wyświetlanie profilu i punktów końcowych sieci CDN
Zacznijmy od kodu, aby wyświetlić listę naszych istniejących profili i punktów końcowych.  Moje komentarze kodu zawierają oczekiwaną składnię, dzięki czemu wiemy, gdzie każdy parametr idzie.

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

## <a name="create-cdn-profiles-and-endpoints"></a>Tworzenie profili i punktów końcowych sieci CDN
Następnie napiszemy funkcje do tworzenia profili i punktów końcowych.

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

## <a name="purge-an-endpoint"></a>Czyszczenie punktu końcowego
Zakładając, że punkt końcowy został utworzony, jednym z typowych zadań, które możemy chcieć wykonać w naszym programie, jest czyszczenie zawartości w naszym punkcie końcowym.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>Usuwanie profili i punktów końcowych sieci CDN
Ostatnia funkcja, którą dołączymy, usuwa punkty końcowe i profile.

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
Możemy teraz wykonać nasz program Node.js za pomocą naszego ulubionego debugera lub na konsoli.

> [!TIP]
> Jeśli używasz visual studio kod jako debuger, należy skonfigurować środowisko do przekazywania w parametrach wiersza polecenia.  Visual Studio Code robi to w pliku **launch.json.**  Poszukaj właściwości o nazwie **args** i dodaj tablicę wartości ciągów `"args": ["list", "profiles"]`dla parametrów, tak aby wyglądała podobnie do następującego: .
> 
> 

Zacznijmy od umieszczenia na liście naszych profili.

![Profile listy](./media/cdn-app-dev-node/cdn-list-profiles.png)

Wróciliśmy do pustej tablicy.  Ponieważ nie mamy żadnych profili w naszej grupie zasobów, jest to oczekiwane.  Utwórzmy teraz profil.

![Tworzenie profilu](./media/cdn-app-dev-node/cdn-create-profile.png)

Teraz dodajmy punkt końcowy.

![Tworzenie punktu końcowego](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Na koniec usuńmy nasz profil.

![Usuń profil](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Następne kroki
Aby zobaczyć ukończony projekt z tego [przewodnika, pobierz przykład .](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74)

Aby wyświetlić odwołanie do sdk usługi Azure CDN dla node.js, wyświetl [odwołanie](https://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

Aby znaleźć dodatkową dokumentację dotyczącą narzędzia Azure SDK dla pliku Node.js, wyświetl [pełne odwołanie](https://azure.github.io/azure-sdk-for-node/).

Zarządzanie zasobami sieci CDN za pomocą programu [PowerShell](cdn-manage-powershell.md).

