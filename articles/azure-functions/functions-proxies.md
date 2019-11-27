---
title: Współdziałanie z serwerami proxy w Azure Functions
description: Omówienie sposobu użycia usługi Azure Functions Proxies
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: dffdffdfa80d940c4a50d0a6630c665164f24d5c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230454"
---
# <a name="work-with-azure-functions-proxies"></a>Praca z serwerów proxy usługi Azure Functions

W tym artykule wyjaśniono, jak skonfigurować i pracować z serwerów proxy usługi Azure Functions. Dzięki tej funkcji można określić punkty końcowe na aplikację funkcji, które są implementowane przez inny zasób. Te serwery proxy można użyć do dzielenia dużych interfejsu API na wiele aplikacji funkcyjnych (tak jak w architekturze mikrousług), przy jednoczesnym prezentowaniu jednej powierzchni interfejsu API dla klientów.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Standardowe funkcje rozliczeń mają zastosowanie do wykonania serwera proxy. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create"></a>Tworzenie serwera proxy

W tej sekcji dowiesz się, jak utworzyć serwer proxy w portalu usługi Functions.

1. Otwórz [Azure Portal], a następnie przejdź do swojej aplikacji funkcji.
2. W lewym okienku wybierz pozycję **nowy serwer proxy**.
3. Podaj nazwę serwera proxy.
4. Skonfiguruj punkt końcowy, który jest udostępniany w tej aplikacji funkcji, określając **szablon trasy** i **metody http**. Te parametry zachowują się zgodnie z regułami dla [Wyzwalacze HTTP].
5. Ustaw **adres URL zaplecza** na inny punkt końcowy. Ten punkt końcowy może być funkcją w innej aplikacji funkcji lub może być dowolnym interfejsem API. Wartość nie musi być statyczna i może odwoływać się do [Ustawienia aplikacji] i parametrów aplikacji [z oryginalnego żądania klienta].
6. Kliknij pozycję **Utwórz**.

Twój serwer proxy obecnie istnieje jako nowy punkt końcowy na aplikację funkcji. Z perspektywy klienta jest odpowiednikiem HttpTrigger w usłudze Azure Functions. Twój nowy serwer proxy można wypróbować, kopiując adres URL serwera Proxy i testowanie go za pomocą swojego ulubionego klienta HTTP.

## <a name="modify-requests-responses"></a>Modyfikuj żądania i odpowiedzi

Za pomocą usługi Azure Functions Proxies można modyfikować żądań i odpowiedzi z zapleczem. Te przekształcenia mogą używać zmiennych zdefiniowanych w [Użyj zmiennych].

### <a name="modify-backend-request"></a>Modyfikowanie żądania zaplecza

Domyślnie żądania zaplecza jest inicjowany jako kopię oryginalne żądanie. Oprócz skonfigurowania adresu URL zaplecza, można wprowadzić zmiany do metody HTTP, nagłówki i parametry ciągu zapytania. Zmodyfikowane wartości mogą odwoływać się do [Ustawienia aplikacji] i [z oryginalnego żądania klienta].

Żądania zaplecza można modyfikować w portalu, rozszerzając sekcję *przesłonięcie żądania* na stronie szczegółów serwera proxy. 

### <a name="modify-response"></a>Modyfikowanie odpowiedzi

Domyślnie odpowiedź klienta jest inicjowany jako kopię odpowiedzi zaplecza. Można wprowadzić zmiany, aby kod stanu odpowiedzi, frazę przyczyny, nagłówki i treść. Zmodyfikowane wartości mogą odwoływać się do [Ustawienia aplikacji], [z oryginalnego żądania klienta]i [parametrów z odpowiedzi zaplecza].

Żądania zaplecza można modyfikować w portalu, rozszerzając sekcję *przesłonięcie odpowiedzi* na stronie szczegółów serwera proxy. 

## <a name="using-variables"></a>Użyj zmiennych

Konfiguracja serwera proxy nie musi być statyczne. Można warunku go do korzystania ze zmiennych z oryginalnego żądania klienta, serwer zaplecza w odpowiedzi lub ustawień aplikacji.

### <a name="reference-localhost"></a>Odwołania do funkcji lokalnych
Za pomocą `localhost` można odwoływać się do funkcji w tej samej aplikacji funkcji bezpośrednio, bez żądania dwukierunkowego serwera proxy.

`"backendurl": "https://localhost/api/httptriggerC#1"` będzie odwoływać się do lokalnej funkcji wyzwalanej przez protokół HTTP w marszrucie `/api/httptriggerC#1`

 
>[!Note]  
>Jeśli funkcja używa poziomów autoryzacji *funkcji, administratora lub sys* , należy podać kod i clientId, zgodnie z oryginalnym adresem URL funkcji. W takim przypadku odwołanie będzie wyglądać następująco: `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` zalecamy przechowywanie tych kluczy w [Ustawienia aplikacji] i odwoływanie się do tych z serwerów proxy. Pozwala to uniknąć przechowywania wpisów tajnych w kodzie źródłowym. 

### <a name="request-parameters"></a>Parametry żądania odwołania

Parametry żądania można użyć jako danych wejściowych do właściwości adres URL zaplecza lub jako część modyfikowanie żądań i odpowiedzi. Niektóre parametry mogą być powiązane z szablonu trasy, który jest określony w konfiguracji podstawowej serwera proxy, a inne mogą pochodzić z właściwości żądania przychodzącego.

#### <a name="route-template-parameters"></a>Parametry szablonu trasy
Parametry, które są używane w szablonie trasy dostępnych może być przywoływane przez nazwę. Nazwy parametrów są ujęte w nawiasy klamrowe ({}).

Na przykład, jeśli serwer proxy ma szablon trasy, taki jak `/pets/{petId}`, adres URL zaplecza może zawierać wartość `{petId}`, jak w `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Jeśli szablon trasy kończy się w symbolu wieloznacznym, takim jak `/api/{*restOfPath}`, `{restOfPath}` wartość jest ciągiem reprezentującym pozostałe segmenty ścieżki z żądania przychodzącego.

#### <a name="additional-request-parameters"></a>Dodatkowe parametry żądania
Oprócz parametrów szablonu trasy następujące wartości może służyć w wartości konfiguracji:

* **{Request. Method}** : Metoda http, która jest używana w oryginalnym żądaniu.
* **{Request. Heads.\<headername\>}** : nagłówek, który można odczytać z oryginalnego żądania. Zastąp *\<headername\>* nazwą nagłówka, który ma zostać odczytany. Jeśli nagłówek nie jest uwzględniony w żądaniu, wartość będzie pusty ciąg.
* **{Request. QueryString.\<parametername\>}** : parametr ciągu zapytania, który można odczytać z oryginalnego żądania. Zastąp *parametr\<parametername\>* nazwą parametru, który ma zostać odczytany. Jeśli parametr nie jest uwzględniony w żądaniu, wartość będzie pusty ciąg.

### <a name="response-parameters"></a>Parametry odpowiedzi wewnętrznej bazy danych

Parametrów odpowiedzi może służyć jako część modyfikowania odpowiedzi do klienta. Następujące wartości może służyć w wartości konfiguracji:

* **{zaplecze. Response. StatusCode}** : kod stanu HTTP zwracany w odpowiedzi wewnętrznej.
* **{zaplecze. Response. statusReason}** : fraza przyczyny http, która jest zwracana w odpowiedzi wewnętrznej.
* **{zaplecze. Response. Heads.\<headername\>}** : nagłówek, który można odczytać z odpowiedzi wewnętrznej bazy danych. Zastąp *\<headername\>* nazwą nagłówka, który ma zostać odczytany. Jeśli nagłówek nie jest uwzględniony w odpowiedzi, wartość będzie pusty ciąg.

### <a name="use-appsettings"></a>Ustawienia aplikacji referencyjnych

Można także odwoływać się [do ustawień aplikacji zdefiniowanych dla aplikacji funkcji](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) , otaczając nazwę ustawienia ze znakami procentu (%).

Na przykład adres URL zaplecza *https://%ORDER_PROCESSING_HOST%/api/orders* zostałby zastąpiony przez wartość "% ORDER_PROCESSING_HOST%" wartością ustawienia ORDER_PROCESSING_HOST.

> [!TIP] 
> Użyj ustawień aplikacji dla hostów zaplecza, jeśli masz wielu wdrożeń i środowisk testowych. W ten sposób można upewnij się, że zawsze mówimy prawo zaplecze dla danego środowiska.

## <a name="debugProxies"></a>Rozwiązywanie problemów z serwerami proxy

Dodanie flagi `"debug":true` do dowolnego serwera proxy w `proxies.json` spowoduje włączenie rejestrowania debugowania. Dzienniki są przechowywane w `D:\home\LogFiles\Application\Proxies\DetailedTrace` i dostępne za pomocą narzędzi zaawansowanych (kudu). Wszystkie odpowiedzi HTTP także zawierają nagłówek `Proxy-Trace-Location` z adresem URL dostępu do pliku dziennika.

Można debugować serwer proxy po stronie klienta, dodając do `true`nagłówek `Proxy-Trace-Enabled`. To będzie również rejestrowania danych śledzenia w systemie plików, a jako nagłówek odpowiedzi na adres zwrotny URL śledzenia.

### <a name="block-proxy-traces"></a>Blok proxy śladów

Ze względów bezpieczeństwa może nie chcieć zezwala wszystkim podczas wywoływania usługi w celu wygenerowania śledzenia. Nie będą mogli uzyskać dostęp do zawartości śledzenia bez poświadczeń logowania, ale Trwa generowanie śladu wykorzystuje zasoby i ujawnia, że jest używana funkcja serwerów proxy.

Całkowicie wyłącz ślady, dodając `"debug":false` do każdego określonego serwera proxy w `proxies.json`.

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana

Konfigurowane serwery proxy są przechowywane w pliku *proxy. JSON* , który znajduje się w katalogu głównym katalogu aplikacji funkcji. Możesz ręcznie edytować ten plik i wdrożyć go w ramach aplikacji, korzystając z dowolnej z [metod wdrażania](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) obsługiwanych przez funkcje. 

> [!TIP] 
> Jeśli nie skonfigurowano jednej z metod wdrażania, można również współdziałać z plikiem *proxy. JSON* w portalu. Przejdź do aplikacji funkcji, wybierz pozycję **funkcje platformy**, a następnie wybierz pozycję **Edytor usługi App Service**. W ten sposób możesz wyświetlić strukturę cały plik aplikację funkcji, a następnie dokonaj zmian.

*Proxy. JSON* jest zdefiniowany przez obiekt proxy, który składa się z nazwanych serwerów proxy i ich definicji. Opcjonalnie, jeśli edytor obsługuje tę funkcję, można odwoływać się do [schematu JSON](http://json.schemastore.org/proxies) w celu uzupełniania kodu. Przykładowy plik może wyglądać następująco:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Każdy serwer proxy ma przyjazną nazwę, taką jak *Proxy1* w poprzednim przykładzie. Odpowiedni obiekt serwera proxy w definicji jest definiowany przez następujące właściwości:

* **matchCondition**: wymagane--obiekt definiujący żądania, które wyzwalają wykonywanie tego serwera proxy. Zawiera dwie właściwości, które są współużytkowane z [Wyzwalacze HTTP]:
    * _metody_: tablica metod http, na które odpowiada serwer proxy. Jeśli nie zostanie określony, serwer proxy odpowiada na wszystkich metod HTTP na trasie.
    * _trasa_: wymagane — definiuje szablon trasy, kontrolujący adresy URL żądań, na które odpowiada serwer proxy. W odróżnieniu od w wyzwalaczy HTTP ma wartości domyślnej.
* **backendUri**: adres URL zasobu zaplecza, do którego należy serwer proxy żądania. Ta wartość może przywoływać ustawień aplikacji i parametry, z oryginalnego żądania klienta. Jeśli ta właściwość nie jest dołączony, usługi Azure Functions odpowiada za pomocą protokołu HTTP 200 OK.
* **requestOverrides**: obiekt definiujący przekształcenia do żądania zaplecza. Zobacz [Zdefiniuj obiekt requestOverrides].
* **responseOverrides**: obiekt definiujący przekształcenia do odpowiedzi klienta. Zobacz [Zdefiniuj obiekt responseOverrides].

> [!NOTE] 
> Właściwość *Route* w serwery proxy usługi Azure Functions nie przestrzega właściwości *routePrefix* konfiguracji hosta aplikacja funkcji. Jeśli chcesz uwzględnić prefiks, taki jak `/api`, musi być uwzględniony we właściwości *trasy* .

### <a name="disableProxies"></a>Wyłącz indywidualne serwery proxy

Poszczególne serwery proxy można wyłączyć, dodając `"disabled": true` do serwera proxy w pliku `proxies.json`. Spowoduje to, że wszystkie żądania, które spełniają matchCondition, zwracają 404.
```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "Root": {
            "disabled":true,
            "matchCondition": {
                "route": "/example"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

### <a name="applicationSettings"></a>Ustawienia aplikacji

Zachowanie serwera proxy mogą być kontrolowane przez kilka ustawień aplikacji. Są one opisane w [dokumentacji dotyczącej ustawień aplikacji funkcji](./functions-app-settings.md)

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reservedChars"></a>Znaki zastrzeżone (formatowanie ciągu)

Serwery proxy odczytują wszystkie ciągi z pliku JSON przy użyciu znaku \ jako symbolu ucieczki. Serwery proxy interpretują również nawiasy klamrowe. Zapoznaj się z pełnym zestawem przykładów poniżej.

|Znak|Znak ucieczki|Przykład|
|-|-|-|
|{lub}|{{lub}}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|”|\\\"| `\"example\"` --> `"example"`

### <a name="requestOverrides"></a>Zdefiniuj obiekt requestOverrides

Obiekt requestOverrides definiuje zmiany wprowadzone do żądania, gdy wywoływana jest zasobów zaplecza. Obiekt jest zdefiniowany przez następujące właściwości:

* **zaplecze. Request. Method**: Metoda http, która jest używana do wywoływania zaplecza.
* **zaplecze. Request. QueryString.\<parametername\>** : parametr ciągu zapytania, który można ustawić dla wywołania zaplecza. Zastąp *parametr\<parametername\>* nazwą parametru, który ma zostać ustawiony. Jeśli podano pusty ciąg, parametr nie jest uwzględniony w żądaniu zaplecza.
* **zaplecze. Request. Headers.\<headername\>** : nagłówek, który można ustawić dla wywołania zaplecza. Zastąp *\<headername\>* nazwą nagłówka, który ma zostać ustawiony. Jeśli podasz pusty ciąg, nagłówka nie jest uwzględniony w żądaniu zaplecza.

Wartości może przywoływać ustawień aplikacji i parametry, z oryginalnego żądania klienta.

Przykładowa konfiguracja może wyglądać następująco:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>",
            "requestOverrides": {
                "backend.request.headers.Accept": "application/xml",
                "backend.request.headers.x-functions-key": "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="responseOverrides"></a>Zdefiniuj obiekt responseOverrides

Obiekt requestOverrides definiuje zmiany wprowadzone do odpowiedzi, który jest przekazywany z powrotem do klienta. Obiekt jest zdefiniowany przez następujące właściwości:

* **Response. StatusCode**: kod stanu HTTP do zwrócenia klientowi.
* **Response. statusReason**: fraza przyczyny http, która ma zostać zwrócona do klienta.
* **Response. Body**: ciąg reprezentujący treść, która ma zostać zwrócona do klienta.
* **Response. Headers.\<headername\>** : nagłówek, który można ustawić dla odpowiedzi dla klienta. Zastąp *\<headername\>* nazwą nagłówka, który ma zostać ustawiony. Jeśli podasz pusty ciąg, nagłówka nie znajduje się w odpowiedzi.

Wartości można odwoływać się ustawienia aplikacji, parametrami oryginalne żądanie klienta i parametry, z odpowiedzi zaplecza.

Przykładowa konfiguracja może wyglądać następująco:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "responseOverrides": {
                "response.body": "Hello, {test}",
                "response.headers.Content-Type": "text/plain"
            }
        }
    }
}
```
> [!NOTE] 
> W tym przykładzie treść odpowiedzi jest ustawiana bezpośrednio, więc nie jest wymagana żadna właściwość `backendUri`. W przykładzie pokazano, jak można wykorzystać usługi Azure Functions Proxies dla pozorowanie interfejsów API.

[Azure Portal]: https://portal.azure.com
[Wyzwalacze HTTP]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Zdefiniuj obiekt requestOverrides]: #requestOverrides
[Zdefiniuj obiekt responseOverrides]: #responseOverrides
[Ustawienia aplikacji]: #use-appsettings
[Użyj zmiennych]: #using-variables
[z oryginalnego żądania klienta]: #request-parameters
[parametrów z odpowiedzi zaplecza]: #response-parameters
