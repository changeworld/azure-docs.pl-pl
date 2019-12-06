---
title: Współdziałanie z serwerami proxy w Azure Functions
description: Omówienie sposobu użycia usługi Azure Functions Proxies
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: 09e4616bc7cbb4361ad067ed64984ed95e9a20c5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849194"
---
# <a name="work-with-azure-functions-proxies"></a>Praca z serwerów proxy usługi Azure Functions

W tym artykule wyjaśniono, jak skonfigurować i pracować z serwerów proxy usługi Azure Functions. Dzięki tej funkcji można określić punkty końcowe na aplikację funkcji, które są implementowane przez inny zasób. Te serwery proxy można użyć do dzielenia dużych interfejsu API na wiele aplikacji funkcyjnych (tak jak w architekturze mikrousług), przy jednoczesnym prezentowaniu jednej powierzchni interfejsu API dla klientów.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Standardowe funkcje rozliczeń mają zastosowanie do wykonania serwera proxy. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create"></a>Tworzenie serwera proxy

W tej sekcji dowiesz się, jak utworzyć serwer proxy w portalu usługi Functions.

1. Otwórz [Azure Portal], a następnie przejdź do aplikacji funkcji.
2. W okienku po lewej stronie wybierz **nowego serwera proxy**.
3. Podaj nazwę serwera proxy.
4. Skonfiguruj punkt końcowy, który jest udostępniany w tej aplikacji funkcji, określając **szablon trasy** i **metod HTTP**. Parametry te zachowują się zgodnie z regułami dla [wyzwalaczy HTTP].
5. Ustaw **URL wewnętrznej bazy danych** do innego punktu końcowego. Ten punkt końcowy może być funkcją w innej aplikacji funkcji lub może być dowolnym interfejsem API. Wartość nie musi być statyczna i może się odwoływać do [ustawienia aplikacji] i [Parametry z oryginalne żądanie klienta].
6. Kliknij przycisk **Utwórz**.

Twój serwer proxy obecnie istnieje jako nowy punkt końcowy na aplikację funkcji. Z perspektywy klienta jest odpowiednikiem HttpTrigger w usłudze Azure Functions. Twój nowy serwer proxy można wypróbować, kopiując adres URL serwera Proxy i testowanie go za pomocą swojego ulubionego klienta HTTP.

## <a name="modify-requests-responses"></a>Modyfikowanie żądań i odpowiedzi

Za pomocą usługi Azure Functions Proxies można modyfikować żądań i odpowiedzi z zapleczem. Te przekształcenia można używać zmiennych, zgodnie z definicją w [Używanie zmiennych].

### <a name="modify-backend-request"></a>Zmodyfikować żądanie zaplecza

Domyślnie żądania zaplecza jest inicjowany jako kopię oryginalne żądanie. Oprócz skonfigurowania adresu URL zaplecza, można wprowadzić zmiany do metody HTTP, nagłówki i parametry ciągu zapytania. Zmodyfikowane wartości może odwoływać się [ustawienia aplikacji] i [Parametry z oryginalne żądanie klienta].

Żądania zaplecza można modyfikować w portalu, rozszerzając sekcję *przesłonięcie żądania* na stronie szczegółów serwera proxy. 

### <a name="modify-response"></a>Modyfikowanie odpowiedzi

Domyślnie odpowiedź klienta jest inicjowany jako kopię odpowiedzi zaplecza. Można wprowadzić zmiany, aby kod stanu odpowiedzi, frazę przyczyny, nagłówki i treść. Zmodyfikowane wartości może odwoływać się [ustawienia aplikacji], [Parametry z oryginalne żądanie klienta], i [Parametry z odpowiedzi zaplecza].

Żądania zaplecza można modyfikować w portalu, rozszerzając sekcję *przesłonięcie odpowiedzi* na stronie szczegółów serwera proxy. 

## <a name="using-variables"></a>Używanie zmiennych

Konfiguracja serwera proxy nie musi być statyczne. Można warunku go do korzystania ze zmiennych z oryginalnego żądania klienta, serwer zaplecza w odpowiedzi lub ustawień aplikacji.

### <a name="reference-localhost"></a>Dokumentacja funkcji lokalnych
Możesz użyć `localhost` k odkazu funkcji wewnątrz ta sama aplikacja funkcji bezpośrednio, bez żądania serwera proxy w obie strony.

`"backendurl": "https://localhost/api/httptriggerC#1"` będzie odwoływać się do funkcji wyzwalanej przez protokół HTTP lokalnej na trasie `/api/httptriggerC#1`

 
>[!Note]  
>Jeśli korzysta z funkcji *funkcji, administratora lub sys* poziomów dostępu, należy podać kod i clientId, zgodnie z oryginalny adres URL funkcji. W takim przypadku odwołanie będzie wyglądać następująco: `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` zalecamy przechowywanie tych kluczy w [Ustawienia aplikacji] i odwoływanie się do tych z serwerów proxy. Pozwala to uniknąć przechowywania wpisów tajnych w kodzie źródłowym. 

### <a name="request-parameters"></a>Parametry żądania odwołania

Parametry żądania można użyć jako danych wejściowych do właściwości adres URL zaplecza lub jako część modyfikowanie żądań i odpowiedzi. Niektóre parametry mogą być powiązane z szablonu trasy, który jest określony w konfiguracji podstawowej serwera proxy, a inne mogą pochodzić z właściwości żądania przychodzącego.

#### <a name="route-template-parameters"></a>Parametry szablonu trasy
Parametry, które są używane w szablonie trasy dostępnych może być przywoływane przez nazwę. Nazwy parametrów są ujęte w nawiasy klamrowe ({}).

Na przykład, jeśli serwer proxy ma szablon trasy, takie jak `/pets/{petId}`, adres URL zaplecza może zawierać wartości `{petId}`, jak w `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Jeśli szablon trasy przerywa w symbolu wieloznacznego, takich jak `/api/{*restOfPath}`, wartość `{restOfPath}` jest ciąg reprezentujący pozostałe segmenty ścieżki z żądania przychodzącego.

#### <a name="additional-request-parameters"></a>Dodatkowe parametry żądania
Oprócz parametrów szablonu trasy następujące wartości może służyć w wartości konfiguracji:

* **{Request.method wartość}** : Metoda HTTP, która jest używana na oryginalne żądanie.
* **{request.headers. \<HeaderName\>}** : nagłówek, który może zostać odczytany z oryginalnego żądania. Zastąp *\<HeaderName\>* o nazwie nagłówka, który chcesz odczytać. Jeśli nagłówek nie jest uwzględniony w żądaniu, wartość będzie pusty ciąg.
* **{request.querystring. \<ParameterName\>}** : parametr ciągu zapytania, który może zostać odczytany z oryginalnego żądania. Zastąp *\<ParameterName\>* o nazwie parametru, który chcesz odczytać. Jeśli parametr nie jest uwzględniony w żądaniu, wartość będzie pusty ciąg.

### <a name="response-parameters"></a>Parametry odpowiedzi zaplecza odwołania

Parametrów odpowiedzi może służyć jako część modyfikowania odpowiedzi do klienta. Następujące wartości może służyć w wartości konfiguracji:

* **{backend.response.statusCode}** : Kod stanu HTTP, który jest zwracany w odpowiedzi zaplecza.
* **{backend.response.statusReason}** : Fraza przyczyny HTTP, który jest zwracany w odpowiedzi zaplecza.
* **{backend.response.headers. \<HeaderName\>}** : nagłówek, który może zostać odczytany z odpowiedzi zaplecza. Zastąp *\<HeaderName\>* o nazwie nagłówka chcesz odczytać. Jeśli nagłówek nie jest uwzględniony w odpowiedzi, wartość będzie pusty ciąg.

### <a name="use-appsettings"></a>Dokumentacja ustawień aplikacji

Możesz też przywołać [ustawienia aplikacji określone dla aplikacji funkcji](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) wpisując nazwę ustawienia w znaki procentu (%).

Na przykład serwer zaplecza w adresie URL *https://%ORDER_PROCESSING_HOST%/api/orders* miałby "% ORDER_PROCESSING_HOST %" zastąpione przez ustawienie ORDER_PROCESSING_HOST.

> [!TIP] 
> Użyj ustawień aplikacji dla hostów zaplecza, jeśli masz wielu wdrożeń i środowisk testowych. W ten sposób można upewnij się, że zawsze mówimy prawo zaplecze dla danego środowiska.

## <a name="debugProxies"></a>Rozwiązywanie problemów z serwerów proxy

Dodając flagi `"debug":true` do dowolnego serwera proxy w swojej `proxies.json` spowoduje włączenie rejestrowania debugowania. Dzienniki są przechowywane w `D:\home\LogFiles\Application\Proxies\DetailedTrace` i jest dostępny za pośrednictwem narzędzia zaawansowane (kudu). Wszystkie odpowiedzi HTTP będzie również zawierać `Proxy-Trace-Location` nagłówka przy użyciu adresu URL dostępu do pliku dziennika.

Serwer proxy po stronie klienta można debugować, dodając `Proxy-Trace-Enabled` wartość nagłówka `true`. To będzie również rejestrowania danych śledzenia w systemie plików, a jako nagłówek odpowiedzi na adres zwrotny URL śledzenia.

### <a name="block-proxy-traces"></a>Blok proxy śladów

Ze względów bezpieczeństwa może nie chcieć zezwala wszystkim podczas wywoływania usługi w celu wygenerowania śledzenia. Nie będą mogli uzyskać dostęp do zawartości śledzenia bez poświadczeń logowania, ale Trwa generowanie śladu wykorzystuje zasoby i ujawnia, że jest używana funkcja serwerów proxy.

Całkowicie wyłączyć ślady, dodając `"debug":false` dowolnego określonego serwera proxy w swojej `proxies.json`.

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana

Serwery proxy, które można skonfigurować, są przechowywane w *proxies.json* pliku, który znajduje się w folderze głównym katalogu aplikacji funkcji. Można ręcznie edytować ten plik i wdrożyć go jako część aplikacji przy użyciu jednej z [metody wdrażania](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) obsługiwanych przez funkcje. 

> [!TIP] 
> Jeśli nie zdefiniowano jednej z metod wdrażania, można również pracować *proxies.json* pliku w portalu. Przejdź do aplikacji funkcji, wybierz opcję **funkcje platformy**, a następnie wybierz pozycję **Edytor usługi App Service**. W ten sposób możesz wyświetlić strukturę cały plik aplikację funkcji, a następnie dokonaj zmian.

*Plik proxies.JSON* jest definiowany przez obiekt serwery proxy, który składa się z o nazwie proxy i ich definicje. Opcjonalnie, jeśli edytor obsługuje tę funkcję, możesz odwoływać się do [schematu JSON](http://json.schemastore.org/proxies) do uzupełniania kodu. Przykładowy plik może wyglądać następująco:

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

Każdy serwer proxy ma przyjazną nazwę, taką jak *proxy1* w poprzednim przykładzie. Odpowiedni obiekt serwera proxy w definicji jest definiowany przez następujące właściwości:

* **matchCondition**: wymagane — obiekt definiujący żądań, które wywołać ich wykonanie tego serwera proxy. Zawiera on dwie właściwości, które są współużytkowane z [wyzwalaczy HTTP]:
    * _metody_: szereg metod HTTP, które odpowiada serwera proxy. Jeśli nie zostanie określony, serwer proxy odpowiada na wszystkich metod HTTP na trasie.
    * _trasy_: wymagane — definiuje szablon trasy kontrolowanie, które adresów URL żądań na serwerze proxy odpowiada. W odróżnieniu od w wyzwalaczy HTTP ma wartości domyślnej.
* **backendUri**: adres URL zasobu zaplecza, do której żądanie powinno być serwerem proxy. Ta wartość może przywoływać ustawień aplikacji i parametry, z oryginalnego żądania klienta. Jeśli ta właściwość nie jest dołączony, usługi Azure Functions odpowiada za pomocą protokołu HTTP 200 OK.
* **requestOverrides**: obiekt, który definiuje przekształcenia na żądanie zaplecza. Zobacz [Zdefiniuj obiekt requestOverrides].
* **responseOverrides**: obiekt, który definiuje przekształcenia odpowiedzi klienta. Zobacz [Zdefiniuj obiekt responseOverrides].

> [!NOTE] 
> *Trasy* właściwości w usłudze Azure Functions Proxies nie uznaje *routePrefix* właściwość konfiguracji hosta aplikacji funkcji. Jeśli chcesz uwzględnić prefiks takie jak `/api`, muszą być zawarte w *trasy* właściwości.

### <a name="disableProxies"></a> Wyłączyć poszczególne serwery proxy

Można wyłączyć poszczególne serwery proxy, dodając `"disabled": true` serwer proxy w `proxies.json` pliku. Spowoduje to, że wszystkie żądania, które spełniają matchCondition, zwracają 404.
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

### <a name="applicationSettings"></a> Ustawienia aplikacji

Zachowanie serwera proxy mogą być kontrolowane przez kilka ustawień aplikacji. Są to wszystkie opisane w [informacje dotyczące ustawień aplikacji funkcji](./functions-app-settings.md)

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reservedChars"></a> Zastrzeżone znaki (formatowanie ciągu)

Serwery proxy odczytują wszystkie ciągi z pliku JSON przy użyciu znaku \ jako symbolu ucieczki. Serwery proxy interpretują również nawiasy klamrowe. Zapoznaj się z pełnym zestawem przykładów poniżej.

|Znak|Znak ucieczki|Przykład|
|-|-|-|
|{lub}|{{lub}}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|”|\\\"| `\"example\"` --> `"example"`

### <a name="requestOverrides"></a>Zdefiniuj obiekt requestOverrides

Obiekt requestOverrides definiuje zmiany wprowadzone do żądania, gdy wywoływana jest zasobów zaplecza. Obiekt jest zdefiniowany przez następujące właściwości:

* **backend.Request.Method**: metoda HTTP, który służy do wywoływania zaplecza.
* **backend.Request.QueryString. \<ParameterName\>** : parametr ciągu zapytania, który można ustawić dla wywołania do zaplecza. Zastąp *\<ParameterName\>* o nazwie parametru, który chcesz ustawić. Należy pamiętać, że jeśli podano pusty ciąg, parametr nadal jest zawarty w żądaniu zaplecza.
* **backend.Request.headers. \<HeaderName\>** : nagłówek, który można ustawić dla wywołania do zaplecza. Zastąp *\<HeaderName\>* o nazwie nagłówka, który chcesz ustawić. Jeśli podasz pusty ciąg, nagłówka nie jest uwzględniony w żądaniu zaplecza.

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

* **response.statusCode**: kod stanu HTTP zwracany do klienta.
* **response.statusReason**: fraza przyczyny HTTP do zwrócenia do klienta.
* **Response.body**: ciąg reprezentujący treść do zwrócenia do klienta.
* **Response.headers. \<HeaderName\>** : nagłówek, który można ustawić dla odpowiedzi do klienta. Zastąp *\<HeaderName\>* o nazwie nagłówka, który chcesz ustawić. Jeśli podasz pusty ciąg, nagłówka nie znajduje się w odpowiedzi.

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
> W tym przykładzie treść odpowiedzi jest ustawiony bezpośrednio, więc nie `backendUri` właściwość jest wymagana. W przykładzie pokazano, jak można wykorzystać usługi Azure Functions Proxies dla pozorowanie interfejsów API.

[Azure Portal]: https://portal.azure.com
[Wyzwalaczy HTTP]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Zdefiniuj obiekt requestOverrides]: #requestOverrides
[Zdefiniuj obiekt responseOverrides]: #responseOverrides
[Ustawienia aplikacji]: #use-appsettings
[Używanie zmiennych]: #using-variables
[Parametry z oryginalne żądanie klienta]: #request-parameters
[Parametry z odpowiedzi zaplecza]: #response-parameters
