---
title: 'Szybki start: tworzenie indeksu wyszukiwania w pliku Node.js przy użyciu interfejsów API REST'
titleSuffix: Azure Cognitive Search
description: W tym przewodniku Szybki start node.js dowiedz się, jak utworzyć indeks, załadować dane i uruchomić kwerendy w usłudze Azure Cognitive Search przy użyciu języka JavaScript i interfejsów API REST.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.devlang: nodejs
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/25/2020
ms.openlocfilehash: cbef6029b93f134f95ee54aa87ce0dd65bcdf50d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77623995"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-nodejs-using-rest-apis"></a>Szybki start: tworzenie indeksu usługi Azure Cognitive Search w pliku Node.js przy użyciu interfejsów API REST
> [!div class="op_single_selector"]
> * [Javascript](search-get-started-nodejs.md)
> * [C #](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [Powershell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Utwórz aplikację Node.js, która tworzy, ładuje i wysyła kwerendy do indeksu usługi Azure Cognitive Search. W tym artykule pokazano, jak utworzyć aplikację krok po kroku. Alternatywnie można [pobrać kod źródłowy i dane](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/) i uruchomić aplikację z wiersza polecenia.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Do stworzenia i przetestowania tego przewodnika Szybki start użyliśmy następującego oprogramowania i usług:

+ [Node.js](https://nodejs.org)

+ [SERWER NPM](https://www.npmjs.com) powinien być instalowany przez node.js

+ Przykładowa struktura indeksu i pasujące dokumenty znajdują się w tym artykule lub z katalogu [ **szybkiego startu** repozytorium](https://github.com/Azure-Samples/azure-search-javascript-samples/)

+ [Utwórz usługę Azure Cognitive Search](search-create-service-portal.md) lub znajdź [istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. Możesz skorzystać z bezpłatnej usługi dla tego szybkiego startu.

Zalecane:

* [Kod programu Visual Studio](https://code.visualstudio.com)

* [Ładniejsze](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) i [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) rozszerzenia vscode.

<a name="get-service-info"></a>

## <a name="get-keys-and-urls"></a>Uzyskaj klucze i adresy URL

Wywołania usługi wymagają punktu końcowego adresu URL i klucza dostępu przy każdym żądaniu. Usługa wyszukiwania jest tworzona z obu, więc jeśli dodano usługę Azure Cognitive Search do subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania uzyskaj nazwę usługi wyszukiwania. Nazwę usługi można potwierdzić, przeglądając adres URL punktu końcowego. Jeśli adres URL `https://mydemo.search.windows.net`punktu końcowego był, nazwa usługi będzie `mydemo`.

2. W **ustawieniach** > **klawiszy**pobierz klucz administratora, aby uzyskać pełne prawa do usługi. Istnieją dwa wymienne klucze administracyjne, przewidziane dla ciągłości biznesowej w przypadku, gdy trzeba przewrócić jeden. Klucz podstawowy lub pomocniczy można używać w żądaniach dodawania, modyfikowania i usuwania obiektów.

    Pobierz klucz zapytania, jak również. Jest najlepszym rozwiązaniem do wystawiania żądań zapytań z dostępem tylko do odczytu.

![Pobierz nazwę usługi i klucze administratora i zapytania](media/search-get-started-nodejs/service-name-and-keys.png)

Wszystkie żądania wymagają klucza interfejsu API w nagłówku każdego żądania wysłanego do usługi. Prawidłowy klucz ustanawia zaufanie, na podstawie żądania, między aplikacją wysyłającą żądanie a usługą, która go obsługuje.

## <a name="set-up-your-environment"></a>Konfigurowanie środowiska

Rozpocznij od otwarcia konsoli programu Powershell lub innego środowiska, w którym zainstalowano plik Node.js.

1. Utwórz katalog programistyczny, nadając mu nazwę: `quickstart`

    ```powershell
    mkdir quickstart
    cd quickstart
    ```

2. Zainicjować pusty projekt z NPM, uruchamiając `npm init`plik . Zaakceptuj wartości domyślne, z wyjątkiem licencji, które należy ustawić na "MIT". 

1. Dodaj pakiety, które będą zależały od kodu i pomocy w rozwoju:

    ```powershell
    npm install nconf node-fetch
    npm install --save-dev eslint eslint-config-prettier eslint-config-airbnb-base eslint-plugin-import prettier
    ```

4. Upewnij się, że skonfigurowano projekty i ich zależności, sprawdzając, czy plik **package.json** wygląda podobnie do następującego:

    ```json
    {
      "name": "quickstart",
      "version": "1.0.0",
      "description": "Azure Cognitive Search Quickstart",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "keywords": [
        "Azure",
        "Azure_Search"
      ],
      "author": "Your Name",
      "license": "MIT",
      "dependencies": {
        "nconf": "^0.10.0",
        "node-fetch": "^2.6.0"
      },
      "devDependencies": {
        "eslint": "^6.1.0",
        "eslint-config-airbnb-base": "^13.2.0",
        "eslint-config-prettier": "^6.0.0",
        "eslint-plugin-import": "^2.18.2",
        "prettier": "^1.18.2"
      }
    }
    ```

5. Utwórz plik **azure_search_config.json,** aby przechowywać dane usługi wyszukiwania:

    ```json
    {
        "serviceName" : "[SEARCH_SERVICE_NAME]",
        "adminKey" : "[ADMIN_KEY]",
        "queryKey" : "[QUERY_KEY]",
        "indexName" : "hotels-quickstart"
    }
    ```

Zastąp `[SERVICE_NAME]` wartość nazwą usługi wyszukiwania. Zastąp `[ADMIN_KEY]` i `[QUERY_KEY]` wartościami kluczy nagranymi wcześniej. 

## <a name="1---create-index"></a>1 - Tworzenie indeksu 

Tworzenie pliku **hotels_quickstart_index.json**.  Ten plik definiuje, jak usługa Azure Cognitive Search współpracuje z dokumentami, które będą ładowane w następnym kroku. Każde pole zostanie zidentyfikowane `name` przez a `type`i będzie mieć określony plik . Każde pole ma również serię atrybutów indeksu, które określają, czy usługa Azure Cognitive Search może wyszukiwać, filtrować, sortować i aspektować pole. Większość pól to proste typy danych, `AddressType` ale niektóre, takie jak złożone typy, które umożliwiają tworzenie struktur danych rozszerzonych w indeksie.  Więcej informacji na temat [obsługiwanych typów danych](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) i [atrybutów indeksu](https://docs.microsoft.com/azure/search/search-what-is-an-index#index-attributes)można przeczytać. 

Dodaj następujące elementy do **hotels_quickstart_index.json** lub [pobierz plik](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels_quickstart_index.json). 

```json
{
    "name": "hotels-quickstart",
    "fields": [
        {
            "name": "HotelId",
            "type": "Edm.String",
            "key": true,
            "filterable": true
        },
        {
            "name": "HotelName",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": true,
            "facetable": false
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "fr.lucene"
        },
        {
            "name": "Category",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Tags",
            "type": "Collection(Edm.String)",
            "searchable": true,
            "filterable": true,
            "sortable": false,
            "facetable": true
        },
        {
            "name": "ParkingIncluded",
            "type": "Edm.Boolean",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "LastRenovationDate",
            "type": "Edm.DateTimeOffset",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Rating",
            "type": "Edm.Double",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Address",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true
                },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "PostalCode",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "Country",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        }
    ],
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "HotelName"
            ]
        }
    ]
}
```
    

Jest dobrą praktyką, aby oddzielić specyfikę określonego scenariusza od kodu, który będzie szeroko stosowany. Klasa `AzureSearchClient` zdefiniowana w pliku **AzureSearchClient.js** będzie wiedzieć, jak konstruować adresy URL żądań, żądać przy użyciu interfejsu API pobierania i reagować na kod stanu odpowiedzi.

Rozpocznij pracę nad **usługą AzureSearchClient.js,** importując pakiet **pobierania węzłów** i tworząc prostą klasę. Wyizolować zmienne części klasy, `AzureSearchClient` przekazując do konstruktora różne wartości konfiguracji:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    constructor(searchServiceName, adminKey, queryKey, indexName) {
        this.searchServiceName = searchServiceName;
        this.adminKey = adminKey;
        // The query key is used for read-only requests and so can be distributed with less risk of abuse.
        this.queryKey = queryKey;
        this.indexName = indexName;
        this.apiVersion = '2019-05-06';
    }

    // All methods go inside class body here!
}

module.exports = AzureSearchClient;
```

Pierwszym obowiązkiem klasy jest wiedzieć, jak konstruować adresy URL, do których mają być wysyłane różne żądania. Skompiluj te adresy URL za pomocą metod wystąpienia, które używają danych konfiguracji przekazanych do konstruktora klasy. Należy zauważyć, że adres URL, który konstruują, jest specyficzny dla wersji `2019-05-06`interfejsu API i musi mieć argument określający tę wersję (w tej aplikacji). 

Pierwsza z tych metod zwróci adres URL dla samego indeksu. Dodaj następującą metodę wewnątrz treści klasy:

```javascript
getIndexUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}?api-version=${this.apiVersion}`; }

```

Następną `AzureSearchClient` odpowiedzialnością jest wykonanie asynchroniiowego żądania za pomocą interfejsu API pobierania. Asynchroniczna metoda `request` statyczna przyjmuje adres URL, ciąg określający metodę HTTP ("GET", "PUT", "POST", "DELETE"), klucz, który ma być używany w żądaniu, oraz opcjonalny obiekt JSON. Zmienna `headers` mapuje `queryKey` (czy klucz administratora lub klucz kwerendy tylko do odczytu) do nagłówka żądania HTTP "api-key". Opcje żądania zawsze `method` zawierają używane i `headers`. Jeśli `bodyJson` nie, `null`treść żądania HTTP jest ustawiona na `bodyJson`reprezentację ciągu . Metoda `request` zwraca obietnicę interfejsu API pobierania do wykonania żądania HTTP.

```javascript
static async request(url, method, apiKey, bodyJson = null) {
    // Uncomment the following for request details:
    /*
    console.log(`\n${method} ${url}`);
    console.log(`\nKey ${apiKey}`);
    if (bodyJson !== null) {
        console.log(`\ncontent: ${JSON.stringify(bodyJson, null, 4)}`);
    }
    */

    const headers = {
        'content-type' : 'application/json',
        'api-key' : apiKey
    };
    const init = bodyJson === null ?
        { 
            method, 
            headers
        }
        : 
        {
            method, 
            headers,
            body : JSON.stringify(bodyJson)
        };
    return fetch(url, init);
}
```

W przypadku celów demonstracyjnych po prostu zgłosić wyjątek, jeśli żądanie HTTP nie jest sukcesem. W rzeczywistej aplikacji prawdopodobnie zrobić niektóre rejestrowania i diagnozowania `response` kodu stanu HTTP w z żądania usługi wyszukiwania. 
    
```javascript
static throwOnHttpError(response) {
    const statusCode = response.status;
    if (statusCode >= 300){
        console.log(`Request failed: ${JSON.stringify(response, null, 4)}`);
        throw new Error(`Failure in request. HTTP Status was ${statusCode}`);
    }
}
```

Na koniec dodaj metody wykrywania, usuwania i tworzenia indeksu usługi Azure Cognitive Search. Wszystkie te metody mają tę samą strukturę:

* Pobierz punkt końcowy, do którego zostanie złożony wniosek.
* Wygeneruj żądanie z odpowiednim punktem końcowym, zleceniem HTTP, kluczem interfejsu API i, w razie potrzeby, treścią JSON. `indexExistsAsync()`i `deleteIndexAsync()` nie mają ciała JSON, ale `createIndexAsync(definition)` nie.
* `await`odpowiedzi na wniosek.  
* Działaj zgodnie z kodem stanu odpowiedzi.
* Zwraca obietnicę o odpowiedniej wartości (wyniki logiczne `this`lub wyniki kwerendy). 

```javascript
async indexExistsAsync() { 
    console.log("\n Checking if index exists...");
    const endpoint = this.getIndexUrl();
    const response = await AzureSearchClient.request(endpoint, "GET", this.adminKey);
    // Success has a few likely status codes: 200 or 204 (No Content), but accept all in 200 range...
    const exists = response.status >= 200 && response.status < 300;
    return exists;
}

async deleteIndexAsync() {
    console.log("\n Deleting existing index...");
    const endpoint = this.getIndexUrl();
    const response = await AzureSearchClient.request(endpoint, "DELETE", this.adminKey);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}

async createIndexAsync(definition) {
    console.log("\n Creating index...");
    const endpoint = this.getIndexUrl();
    const response = await AzureSearchClient.request(endpoint, "PUT", this.adminKey, definition);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

Upewnij się, że metody znajdują się wewnątrz klasy i że eksportujesz klasę. Najbardziej zewnętrzny zakres **azuresearchclient.js** powinny być:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    // ... code here ...
}

module.exports = AzureSearchClient;
```

Klasa obiektowa była dobrym wyborem dla potencjalnie wielokrotnego użytku **modułu AzureSearchClient.js,** ale nie jest konieczna dla głównego programu, który należy umieścić w pliku o nazwie **index.js**. 

Utwórz **plik index.js** i zacznij od wprowadzenia:

* Pakiet **nconf,** który zapewnia elastyczność określania konfiguracji za pomocą JSON, zmiennych środowiskowych lub argumentów wiersza polecenia.
* Dane z pliku **hotels_quickstart_index.json.**
* Moduł `AzureSearchClient`.

```javascript
const nconf = require('nconf');

const indexDefinition = require('./hotels_quickstart_index.json');
const AzureSearchClient = require('./AzureSearchClient.js');
```

Pakiet [ **nconf** ](https://github.com/indexzero/nconf) umożliwia określenie danych konfiguracyjnych w różnych formatach, takich jak zmienne środowiskowe lub wiersz polecenia. Ten przykład używa **nconf** w sposób podstawowy, aby odczytać plik **azure_search_config.json** i zwrócić zawartość tego pliku jako słownika. Za pomocą funkcji `get(key)` **nconf's,** można zrobić szybkie sprawdzenie, czy informacje o konfiguracji zostały prawidłowo dostosowane. Na koniec funkcja zwraca konfigurację:

```javascript
function getAzureConfiguration() {
    const config = nconf.file({ file: 'azure_search_config.json' });
    if (config.get('serviceName') === '[SEARCH_SERVICE_NAME]' ) {
        throw new Error("You have not set the values in your azure_search_config.json file. Change them to match your search service's values.");
    }
    return config;
}
```

Funkcja `sleep` `Promise` tworzy, który jest rozpoznawany po określonym czasie. Za pomocą tej funkcji umożliwia aplikacji wstrzymanie podczas oczekiwania na operacje indeksu asynchroniiowego, aby zakończyć i stać się dostępne. Dodawanie takiego opóźnienia jest zazwyczaj konieczne tylko w pokazach, testach i przykładowych aplikacjach.

```javascript
function sleep(ms) {
    return(
        new Promise(function(resolve, reject) {
            setTimeout(function() { resolve(); }, ms);
        })
    );
}
```

Na koniec określ i wywołaj główną funkcję asynchroniczna. `run` Ta funkcja wywołuje inne funkcje w kolejności, `Promise`oczekując w razie potrzeby, aby rozwiązać s.

* Pobierz konfigurację z `getAzureConfiguration()` wcześniej naliężone wcześniej
* Tworzenie nowego `AzureSearchClient` wystąpienia, przekazywanie wartości z konfiguracji
* Sprawdź, czy indeks istnieje, a jeśli tak, usuń go
* Tworzenie indeksu `indexDefinition` przy użyciu załadowanego **hotels_quickstart_index.json**

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
    } catch (x) {
        console.log(x);
    }
}

run();
```

Nie zapomnij, że ostatnie `run()`wezwanie do! Jest to punkt wejścia do programu `node index.js` po uruchomieniu w następnym kroku.

Należy `AzureSearchClient.indexExistsAsync()` zauważyć, że i `AzureSearchClient.deleteIndexAsync()` nie należy przyjmować parametry. Te funkcje `AzureSearchClient.request()` `bodyJson` wywołać bez argumentu. W `AzureSearchClient.request()`obrębie `bodyJson === null` `true`, `init` ponieważ jest , struktura jest ustawiona na `indexExistsAsync()` tylko zlecenie HTTP `deleteIndexAsync()`("GET" for i "DELETE" for) i nagłówki, które określają klucz żądania.  

W przeciwieństwie `AzureSearchClient.createIndexAsync(indexDefinition)` do metody _przyjmuje_ parametr. Funkcja `run` w `index.js`programie przekazuje zawartość pliku **hotels_quickstart_index.json** do `AzureSearchClient.createIndexAsync(indexDefinition)` metody. Metoda `createIndexAsync()` przekazuje tę `AzureSearchClient.request()`definicję do . W `AzureSearchClient.request()`, `bodyJson === null` ponieważ `false`jest `init` teraz, struktura zawiera nie tylko czasownik HTTP ("PUT") i nagłówki, ale ustawia `body` dane definicji indeksu.

### <a name="prepare-and-run-the-sample"></a>Przygotowanie i uruchomienie próbki

Użyj okna terminala dla następujących poleceń.

1. Przejdź do folderu zawierającego plik **package.json** i pozostałą część kodu.
1. Zainstaluj pakiety dla `npm install`próbki za pomocą pliku .  To polecenie pobierze pakiety, od których zależy kod.
1. Uruchom program `node index.js`z programem .

Powinna zostać wyświetlona seria komunikatów opisujących akcje podejmowane przez program. Jeśli chcesz zobaczyć więcej szczegółów żądań, możesz odkomentować [wiersze `AzureSearchClient.request()` nahttps://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/AzureSearchClient.js#L21-L27) początku metody] w **witrynie AzureSearchClient.js**. 

Otwórz **przegląd** usługi wyszukiwania w witrynie Azure portal. Wybierz kartę **Indeksy.** Powinieneś zobaczyć coś takiego jak:

![Zrzut ekranu przedstawiający witrynę Azure portal, omówienie usługi wyszukiwania, kartę Indeksy](media/search-get-started-nodejs/create-index-no-data.png)

W następnym kroku dodasz dane do indeksu. 

## <a name="2---load-documents"></a>2 - Ładowanie dokumentów 

W usłudze Azure Cognitive Search dokumenty są strukturami danych, które są zarówno dane wejściowe do indeksowania i dane wyjściowe z zapytań. Musisz wysłać takie dane do indeksu. Używa innego punktu końcowego niż operacje wykonane w poprzednim kroku. Otwórz **witrynę AzureSearchClient.js** i `getIndexUrl()`dodaj następującą metodę po:

```javascript
 getPostDataUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs/index?api-version=${this.apiVersion}`;  }
```

Jak `AzureSearchClient.createIndexAsync(definition)`, trzeba funkcję, `AzureSearchClient.request()` która wywołuje i przekazuje w danych hotelu, aby być jego ciała. W **witrynie AzureSearchClient.js** dodaj `postDataAsync(hotelsData)` po: `createIndexAsync(definition)`

```javascript
async postDataAsync(hotelsData) {
    console.log("\n Adding hotel data...");
    const endpoint = this.getPostDataUrl();
    const response = await AzureSearchClient.request(endpoint,"POST", this.adminKey, hotelsData);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

 Dane wejściowe dokumentu mogą być wierszami w bazie danych, obiektami blob w magazynie obiektów Blob lub, jak w tym przykładzie, dokumentami JSON na dysku. Możesz pobrać [plik hotels.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels.json) lub utworzyć własny plik **hotels.json** z następującą zawartością:

```json
{
    "value": [
        {
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
            "Category": "Boutique",
            "Tags": ["pool", "air conditioning", "concierge"],
            "ParkingIncluded": false,
            "LastRenovationDate": "1970-01-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "677 5th Ave",
                "City": "New York",
                "StateProvince": "NY",
                "PostalCode": "10022"
            }
        },
        {
            "HotelId": "2",
            "HotelName": "Twin Dome Motel",
            "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Boutique",
            "Tags": ["pool", "free wifi", "concierge"],
            "ParkingIncluded": "false",
            "LastRenovationDate": "1979-02-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "140 University Town Center Dr",
                "City": "Sarasota",
                "StateProvince": "FL",
                "PostalCode": "34243"
            }
        },
        {
            "HotelId": "3",
            "HotelName": "Triple Landscape Hotel",
            "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Resort and Spa",
            "Tags": ["air conditioning", "bar", "continental breakfast"],
            "ParkingIncluded": "true",
            "LastRenovationDate": "2015-09-20T00:00:00Z",
            "Rating": 4.8,
            "Address": {
                "StreetAddress": "3393 Peachtree Rd",
                "City": "Atlanta",
                "StateProvince": "GA",
                "PostalCode": "30326"
            }
        },
        {
            "HotelId": "4",
            "HotelName": "Sublime Cliff Hotel",
            "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
            "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
            "Category": "Boutique",
            "Tags": ["concierge", "view", "24-hour front desk service"],
            "ParkingIncluded": true,
            "LastRenovationDate": "1960-02-06T00:00:00Z",
            "Rating": 4.6,
            "Address": {
                "StreetAddress": "7400 San Pedro Ave",
                "City": "San Antonio",
                "StateProvince": "TX",
                "PostalCode": "78216"
            }
        }
    ]
}

```

Aby załadować te dane do programu, zmodyfikuj `hotelData` **plik index.js,** dodając wiersz odnoszący się do górnej części:

```javascript
const nconf = require('nconf');

const hotelData = require('./hotels.json');
const indexDefinition = require('./hotels_quickstart_index.json');
```

Teraz zmodyfikuj `run()` funkcję w **index.js**. Może upłynąć kilka sekund, zanim indeks stanie się dostępny, więc `AzureSearchClient.postDataAsync(hotelData)`dodaj 2-sekundową pauzę przed wywołaniem:

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName"));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
        // Index availability can take a few seconds
        await sleep(2000);
        await client.postDataAsync(hotelData);
    } catch (x) {
        console.log(x);
    }
}
```

Uruchom program ponownie `node index.js`za pomocą pliku . Powinien zostać wyświetlony nieco inny zestaw wiadomości od tych, które widziałeś w kroku 1. Tym razem indeks _istnieje_ i powinien zostać wyświetlony komunikat o usunięciu go, zanim aplikacja utworzy nowy indeks i opublikuje do niego dane. 

## <a name="3---search-an-index"></a>3 — Przeszukiwanie indeksu

Wróć do karty **Indeksy** w **przeglądzie** usługi wyszukiwania w witrynie Azure portal. Indeks zawiera teraz cztery dokumenty i zużywa pewną ilość magazynu (może upłynąć kilka minut dla interfejsu użytkownika, aby poprawnie odzwierciedlić podstawowy stan indeksu). Kliknij nazwę indeksu, która ma zostać pobrana do **Eksploratora wyszukiwania**. Ta strona umożliwia eksperymentowanie z zapytaniami o dane. Spróbuj wyszukać ciąg `*&$count=true` zapytania i powinieneś odzyskać wszystkie dokumenty i liczbę wyników. Spróbuj z ciągiem `historic&highlight=Description&$filter=Rating gt 4` zapytania i powinieneś odzyskać pojedynczy dokument, ze `<em></em>` słowem "historyczny" zawiniętym w znaczniki. Dowiedz się więcej o [tym, jak skomponować kwerendę w usłudze Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-query-overview). 

Odtwórz te zapytania w kodzie, otwierając **plik index.js** i dodając ten kod u góry:

```javascript
const queries = [
    "*&$count=true",
    "historic&highlight=Description&$filter=Rating gt 4&"
];
```

W tym samym pliku **index.js** napisz funkcję pokazaną `doQueriesAsync()` poniżej. Ta funkcja `AzureSearchClient` przyjmuje obiekt i `AzureSearchClient.queryAsync` stosuje metodę do każdej `queries` z wartości w tablicy. Używa `Promise.all()` funkcji do zwrócenia pojedynczego, `Promise` który jest rozpoznawany tylko wtedy, gdy wszystkie kwerendy zostały rozwiązane. Wywołanie `JSON.stringify(body, null, 4)` formatów wynik kwerendy, aby być bardziej czytelne.

```javascript
async function doQueriesAsync(client) {
    return Promise.all(
        queries.map( async query => {
            const result = await client.queryAsync(query);
            const body = await result.json();
            const str = JSON.stringify( body, null, 4);
            console.log(`Query: ${query} \n ${str}`);
        })
    );
}
```

Zmodyfikuj funkcję, `run()` aby wstrzymać wystarczająco długo, aby indeksator działał, a następnie wywołać `doQueriesAsync(client)` funkcję:

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName"));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
        // Index availability can take a few seconds
        await sleep(2000);
        await client.postDataAsync(hotelData);
        // Data availability can take a few seconds
        await sleep(5000);
        await doQueriesAsync(client);
    } catch (x) {
        console.log(x);
    }
}
```

Aby `AzureSearchClient.queryAsync(query)`zaimplementować, edytuj plik **AzureSearchClient.js**. Wyszukiwanie wymaga innego punktu końcowego, a wyszukiwane terminy `getSearchUrl(searchTerm)` stają `getIndexUrl()` się `getPostDataUrl()` argumentami adresu URL, więc dodaj funkcję obok i metod, które zostały już napisane.

```javascript
getSearchUrl(searchTerm) { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs?api-version=${this.apiVersion}&search=${searchTerm}&searchMode=all`; }
 ```

Funkcja `queryAsync(searchTerm)` również idzie w **AzureSearchClient.js** i `postDataAsync(data)` następuje tej samej struktury, jak i innych funkcji wykonywania zapytań: 

```javascript
async queryAsync(searchTerm) {
    console.log("\n Querying...")
    const endpoint = this.getSearchUrl(searchTerm);
    const response = await AzureSearchClient.request(endpoint, "GET", this.queryKey);
    AzureSearchClient.throwOnHttpError(response);
    return response;
}
```

Wyszukiwanie odbywa się za pomocą czasownika "GET" i bez treści, ponieważ wyszukiwany termin jest częścią adresu URL. Należy `queryAsync(searchTerm)` zauważyć, `this.queryKey`że używa , w przeciwieństwie do innych funkcji, które używały klucza administratora. Klucze kwerendy, jak sama nazwa wskazuje, mogą być używane tylko do wykonywania zapytań o indeks i nie mogą być używane do modyfikowania indeksu w jakikolwiek sposób. Klucze zapytań są zatem bezpieczniejsze do dystrybucji do aplikacji klienckich.

Uruchom program `node index.js`z plikiem . Teraz, oprócz poprzednich kroków, kwerendy zostaną wysłane, a wyniki zapisane w konsoli.

### <a name="about-the-sample"></a>Informacje o próbce

W przykładzie użyto niewielkiej ilości danych hotelu, wystarczające do wykazania podstaw tworzenia i wykonywania zapytań indeksu usługi Azure Cognitive Search.

**Klasa AzureSearchClient** hermetyzuje konfigurację, adresy URL i podstawowe żądania HTTP dla usługi wyszukiwania. Plik **index.js** ładuje dane konfiguracyjne usługi Azure Cognitive Search, dane hotelu, które zostaną `run` przekazane do indeksowania, a w jego funkcji zamówienia i wykonuje różne operacje.

Ogólne zachowanie `run` funkcji jest usunięcie indeksu usługi Azure Cognitive Search, jeśli istnieje, utworzyć indeks, dodać niektóre dane i wykonać niektóre kwerendy.  

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli pracujesz w ramach własnej subskrypcji, dobrym pomysłem po zakończeniu projektu jest sprawdzenie, czy dalej potrzebujesz utworzonych zasobów. Nadal uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć i zarządzać nimi w portalu, korzystając z łącza **Wszystkie zasoby** lub **Grupy zasobów** w lewym okienku nawigacji.

Jeśli korzystasz z bezpłatnej usługi, należy pamiętać, że są ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby pozostać poniżej limitu. 

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start node.js przepracowano serię zadań w celu utworzenia indeksu, załadowania go z dokumentami i uruchomienia kwerend. Wykonaliśmy pewne kroki, takie jak odczytywanie konfiguracji i definiowanie zapytań w najprostszy możliwy sposób. W rzeczywistej aplikacji, należy umieścić te obawy w oddzielnych modułów, które zapewniają elastyczność i hermetyzacji. 
 
Jeśli masz już pewne tło w usłudze Azure Cognitive Search, możesz użyć tego przykładu jako trampoliny do wypróbowania sugestów (kwerendy typu z wyprzedzeniem lub autouzupełniania), filtrów i nawigacji aspektowej. Jeśli jesteś nowy w usłudze Azure Cognitive Search, zalecamy wypróbowanie innych samouczków w celu zrozumienia, co można utworzyć. Aby znaleźć więcej zasobów, odwiedź naszą [stronę dokumentacji](https://azure.microsoft.com/documentation/services/search/). 

> [!div class="nextstepaction"]
> [Wywoływanie usługi Azure Cognitive Search ze strony WebPage przy użyciu języka JavaScript](https://github.com/liamca/azure-search-javascript-samples)
