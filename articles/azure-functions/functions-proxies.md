---
title: Praca z serwerami proxy w usłudze Azure Functions
description: Omówienie korzystania z serwerów proxy usługi Azure Functions
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: 09e4616bc7cbb4361ad067ed64984ed95e9a20c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849194"
---
# <a name="work-with-azure-functions-proxies"></a>Praca z serwerami proxy usług Azure Functions

W tym artykule wyjaśniono, jak skonfigurować i pracować z serwerami proxy usługi Azure Functions. Za pomocą tej funkcji można określić punkty końcowe w aplikacji funkcji, które są implementowane przez inny zasób. Za pomocą tych serwerów proxy można podzielić duży interfejs API na wiele aplikacji funkcji (jak w architekturze mikrousług), podczas gdy nadal przedstawia jedną powierzchnię interfejsu API dla klientów.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Rozliczanie funkcji standardowych dotyczy wykonań serwera proxy. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create-a-proxy"></a><a name="create"></a>Tworzenie serwera proxy

W tej sekcji pokazano, jak utworzyć serwer proxy w portalu funkcji.

1. Otwórz [witrynę Azure portal], a następnie przejdź do aplikacji funkcji.
2. W lewym okienku wybierz pozycję **Nowy serwer proxy**.
3. Podaj nazwę serwera proxy.
4. Skonfiguruj punkt końcowy, który jest widoczna w tej aplikacji funkcji, określając **szablon trasy** i **metody HTTP**. Parametry te zachowują się zgodnie z regułami [wyzwalaczy HTTP].
5. Ustaw **adres URL wewnętrznej bazy danych** na inny punkt końcowy. Ten punkt końcowy może być funkcją w innej aplikacji funkcji lub może być inny interfejs API. Wartość nie musi być statyczna i może odwoływać się do [ustawień aplikacji] i [parametrów z oryginalnego żądania klienta].
6. Kliknij przycisk **Utwórz**.

Serwer proxy istnieje teraz jako nowy punkt końcowy w aplikacji funkcji. Z punktu widzenia klienta jest odpowiednikiem httpTrigger w usłudze Azure Functions. Możesz wypróbować nowy serwer proxy, kopiując adres URL serwera proxy i testując go z ulubionym klientem HTTP.

## <a name="modify-requests-and-responses"></a><a name="modify-requests-responses"></a>Modyfikowanie żądań i odpowiedzi

Za pomocą serwerów proxy usługi Azure Functions można modyfikować żądania i odpowiedzi z zaplecza. Przekształcenia te mogą używać zmiennych zdefiniowanych w [użyj zmiennych].

### <a name="modify-the-back-end-request"></a><a name="modify-backend-request"></a>Modyfikowanie żądania zaplecza

Domyślnie żądanie zaplecza jest inicjowane jako kopia oryginalnego żądania. Oprócz ustawiania adresu URL zaplecza można wprowadzać zmiany w parametrach metody HTTP, nagłówków i ciągu zapytania. Zmodyfikowane wartości mogą odwoływać się do [ustawień aplikacji] i [parametrów z oryginalnego żądania klienta].

Żądania zaplecza można modyfikować w portalu, rozwiń sekcję *zastępowania żądania* na stronie szczegółów serwera proxy. 

### <a name="modify-the-response"></a><a name="modify-response"></a>Modyfikowanie odpowiedzi

Domyślnie odpowiedź klienta jest inicjowana jako kopia odpowiedzi zaplecza. Można wprowadzić zmiany w kodzie stanu odpowiedzi, frazie przyczyny, nagłówkach i treści. Zmodyfikowane wartości mogą odwoływać się do [ustawień aplikacji,] [parametrów z oryginalnego żądania klienta]i [parametrów z odpowiedzi zaplecza.]

Żądania zaplecza można modyfikować w portalu, rozszerzając sekcję *zastępowania odpowiedzi* na stronie szczegółów serwera proxy. 

## <a name="use-variables"></a><a name="using-variables"></a>Używanie zmiennych

Konfiguracja serwera proxy nie musi być statyczna. Można go warunkować, aby użyć zmiennych z oryginalnego żądania klienta, odpowiedzi zaplecza lub ustawień aplikacji.

### <a name="reference-local-functions"></a><a name="reference-localhost"></a>Odwoływanie się do funkcji lokalnych
Można użyć `localhost` do odwołania się do funkcji wewnątrz tej samej aplikacji funkcji bezpośrednio, bez żądania serwera proxy w obie strony.

`"backendurl": "https://localhost/api/httptriggerC#1"`odwołuje się do lokalnej funkcji wyzwalania HTTP na trasie`/api/httptriggerC#1`

 
>[!Note]  
>Jeśli funkcja używa *funkcji, admin lub sys* poziomów autoryzacji, należy podać kod i clientId, zgodnie z oryginalnym adresem URL funkcji. W takim przypadku odwołanie będzie `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` wyglądać: Zaleca się przechowywanie tych kluczy w [ustawieniach aplikacji] i odwoływanie się do tych w serwerach proxy. Pozwala to uniknąć przechowywania wpisów tajnych w kodzie źródłowym. 

### <a name="reference-request-parameters"></a><a name="request-parameters"></a>Parametry żądania referencyjnego

Parametry żądania można używać jako danych wejściowych do właściwości adresu URL zaplecza lub jako część modyfikowania żądań i odpowiedzi. Niektóre parametry mogą być powiązane z szablonem trasy, który jest określony w konfiguracji podstawowego serwera proxy, a inne mogą pochodzić z właściwości żądania przychodzącego.

#### <a name="route-template-parameters"></a>Parametry szablonu trasy
Parametry używane w szablonie trasy są dostępne, aby odwoływać się za pomocą nazwy. Nazwy parametrów są ujęte{}w nawiasy klamrowe ( ).

Na przykład, jeśli serwer proxy ma `/pets/{petId}`szablon trasy, na przykład , `{petId}`adres URL `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`zaplecza może zawierać wartość , jak w . Jeśli szablon marszruty kończy się na `/api/{*restOfPath}`symbolu `{restOfPath}` wieloznacznym, na przykład, wartość jest reprezentacją ciągu pozostałych segmentów ścieżki z żądania przychodzącego.

#### <a name="additional-request-parameters"></a>Dodatkowe parametry żądania
Oprócz parametrów szablonu trasy w wartościach konfiguracyjnych można używać następujących wartości:

* **{request.method}**: Metoda HTTP używana w oryginalnym żądaniu.
* **{request.headers.\< HeaderName\>}**: Nagłówek, który można odczytać z oryginalnego żądania. Zamień * \<\> nazwę nagłówka* nazwą nagłówka, który chcesz odczytać. Jeśli nagłówek nie jest uwzględniony w żądaniu, wartość będzie pustym ciągiem.
* **{request.querystring.\< ParameterName\>}**: Parametr ciągu zapytania, który można odczytać z oryginalnego żądania. Zamień * \<\> nazwę parametru* nazwą parametru, który chcesz odczytać. Jeśli parametr nie jest uwzględniony w żądaniu, wartość będzie pustym ciągiem.

### <a name="reference-back-end-response-parameters"></a><a name="response-parameters"></a>Odwołanie do parametrów odpowiedzi zaplecza

Parametry odpowiedzi mogą być używane jako część modyfikowania odpowiedzi do klienta. W wartościach konfiguracjowych można używać następujących wartości:

* **{backend.response.statusCode}**: Kod stanu HTTP, który jest zwracany w odpowiedzi zaplecza.
* **{backend.response.statusReason}**: Fraza przyczyny HTTP, która jest zwracana w odpowiedzi zaplecza.
* **{backend.response.headers.\< HeaderName\>}**: Nagłówek, który można odczytać z odpowiedzi zaplecza. Zamień * \<\> nazwę nagłówka* nazwą nagłówka, który chcesz odczytać. Jeśli nagłówek nie jest uwzględniony w odpowiedzi, wartość będzie pusty ciąg.

### <a name="reference-application-settings"></a><a name="use-appsettings"></a>Odwołanie do ustawień aplikacji

Można również odwoływać się do [ustawień aplikacji zdefiniowanych dla aplikacji funkcyjnej,](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) otaczając nazwę ustawienia znakami procentowymi (%).

Na przykład adres URL zaplecza *https://%ORDER_PROCESSING_HOST%/api/orders* miałby "%ORDER_PROCESSING_HOST%" zastąpiony wartością ustawienia ORDER_PROCESSING_HOST.

> [!TIP] 
> Użyj ustawień aplikacji dla hostów zaplecza, gdy masz wiele wdrożeń lub środowisk testowych. W ten sposób możesz upewnić się, że zawsze rozmawiasz z prawym zapleczem dla tego środowiska.

## <a name="troubleshoot-proxies"></a><a name="debugProxies"></a>Rozwiązywanie problemów z serwerami proxy

Dodając flagę `"debug":true` do dowolnego `proxies.json` serwera proxy w swoim można włączyć rejestrowanie debugowania. Dzienniki są `D:\home\LogFiles\Application\Proxies\DetailedTrace` przechowywane i dostępne za pośrednictwem zaawansowanych narzędzi (kudu). Wszelkie odpowiedzi HTTP będą `Proxy-Trace-Location` również zawierać nagłówek z adresem URL, aby uzyskać dostęp do pliku dziennika.

Serwer proxy można debugować od strony `Proxy-Trace-Enabled` klienta, `true`dodając nagłówek ustawiony na . Spowoduje to również rejestrowanie śledzenia do systemu plików i zwraca adres URL śledzenia jako nagłówek w odpowiedzi.

### <a name="block-proxy-traces"></a>Blokowanie śladów serwera proxy

Ze względów bezpieczeństwa możesz nie chcieć zezwolić osobom dzwoniącym do usługi na wygenerowanie śledzenia. Nie będą mogli uzyskać dostępu do zawartości śledzenia bez poświadczeń logowania, ale generowanie śledzenia zużywa zasoby i udostępnia, że używasz serwerów proxy funkcji.

Całkowicie wyłącz ślady, `"debug":false` dodając do dowolnego `proxies.json`konkretnego serwera proxy w pliku .

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana

Skonfigurowane serwery proxy są przechowywane w pliku *proxyies.json,* który znajduje się w katalogu głównym aplikacji funkcji. Można ręcznie edytować ten plik i wdrożyć go jako część aplikacji, korzystając z dowolnej [z metod wdrażania,](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) które obsługuje funkcje. 

> [!TIP] 
> Jeśli nie skonfigurowano jednej z metod wdrażania, można również pracować z plikiem *proxyies.json* w portalu. Przejdź do aplikacji funkcji, wybierz pozycję **Funkcje platformy**, a następnie wybierz pozycję **Edytor usług aplikacji**. W ten sposób można wyświetlić całą strukturę plików aplikacji funkcji, a następnie wprowadzić zmiany.

*Plik Proxy.json* jest definiowany przez obiekt proxy, który składa się z nazwanych serwerów proxy i ich definicji. Opcjonalnie, jeśli edytor obsługuje go, można odwoływać się do [schematu JSON](http://json.schemastore.org/proxies) do uzupełniania kodu. Przykładowy plik może wyglądać następująco:

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

Każdy serwer proxy ma przyjazną nazwę, taką jak *proxy1* w poprzednim przykładzie. Odpowiedni obiekt definicji serwera proxy jest definiowany przez następujące właściwości:

* **matchCondition**: Wymagane — obiekt definiujący żądania, które wyzwalają wykonanie tego serwera proxy. Zawiera dwie właściwości, które są współużytkowane za pomocą [wyzwalaczy HTTP:]
    * _metody_: Tablica metod HTTP, na które odpowiada serwer proxy. Jeśli nie zostanie określony, serwer proxy odpowiada na wszystkie metody HTTP w marszruty.
    * _trasa_: Wymagane — definiuje szablon trasy, kontrolując, na które żądania adresów URL odpowiada serwer proxy. W przeciwieństwie do wyzwalaczy HTTP, nie ma wartości domyślnej.
* **backendUri**: Adres URL zasobu zaplecza, do którego żądanie powinno być bliższe. Ta wartość może odwoływać się do ustawień aplikacji i parametrów z oryginalnego żądania klienta. Jeśli ta właściwość nie jest uwzględniona, usługa Azure Functions odpowiada za pomocą protokołu HTTP 200 OK.
* **requestOverrides**: Obiekt definiujący przekształcenia do żądania zaplecza. Zobacz [Definiowanie obiektu requestOverrides].
* **responseOverrides**: Obiekt definiujący przekształcenia do odpowiedzi klienta. Zobacz [Definiowanie obiektu responseOverrides].

> [!NOTE] 
> Właściwość *trasy* w usługach Azure Functions Proxies nie jest honorować właściwość *routePrefix* konfiguracji hosta aplikacji funkcji. Jeśli chcesz dołączyć prefiks, `/api`taki jak , musi być uwzględniony we właściwości *trasy.*

### <a name="disable-individual-proxies"></a><a name="disableProxies"></a>Wyłączanie poszczególnych serwerów proxy

Można wyłączyć poszczególne serwery `"disabled": true` proxy, dodając `proxies.json` go do serwera proxy w pliku. Spowoduje to, że wszystkie żądania spełniające matchCondition do zwrócenia 404.
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

### <a name="application-settings"></a><a name="applicationSettings"></a>Ustawienia aplikacji

Zachowanie serwera proxy może być kontrolowane przez kilka ustawień aplikacji. Wszystkie są opisane w [odwołaniu Do ustawień aplikacji funkcji](./functions-app-settings.md)

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reserved-characters-string-formatting"></a><a name="reservedChars"></a>Znaki zastrzeżone (formatowanie ciągów)

Serwery proxy odczytują wszystkie ciągi z pliku JSON, używając \ jako symbolu ucieczki. Serwery proxy interpretują również nawiasy klamrowe. Zobacz pełny zestaw przykładów poniżej.

|Znak|Znak ucieczki|Przykład|
|-|-|-|
|{ lub }|{{ lub }}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="define-a-requestoverrides-object"></a><a name="requestOverrides"></a>Definiowanie obiektu requestOverrides

Obiekt requestOverrides definiuje zmiany wprowadzone do żądania, gdy wywoływany jest zasób zaplecza. Obiekt jest definiowany przez następujące właściwości:

* **backend.request.method**: Metoda HTTP używana do wywoływania zaplecza.
* **backend.request.querystring. ParameterName\>: Parametr ciągu zapytania, który można ustawić dla wywołania zaplecza. \<** Zamień * \<\> nazwę parametru* nazwą parametru, który chcesz ustawić. Należy pamiętać, że jeśli podany jest pusty ciąg, parametr jest nadal uwzględniany w żądaniu zaplecza.
* **backend.request.headers. HeaderName\>: Nagłówek, który można ustawić dla wywołania do zaplecza. \<** Zamień * \<\> nazwę nagłówka* nazwą nagłówka, który chcesz ustawić. Jeśli podasz pusty ciąg, nagłówek nie jest uwzględniony w żądaniu zaplecza.

Wartości mogą odwoływać się do ustawień aplikacji i parametrów z oryginalnego żądania klienta.

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

### <a name="define-a-responseoverrides-object"></a><a name="responseOverrides"></a>Definiowanie obiektu responseOverrides

RequestOverrides obiekt definiuje zmiany, które są wprowadzane do odpowiedzi, która jest przekazywana z powrotem do klienta. Obiekt jest definiowany przez następujące właściwości:

* **response.statusCode**: Kod stanu HTTP, który ma zostać zwrócony do klienta.
* **response.statusReason**: Wyrażenie przyczyny HTTP, które mają zostać zwrócone do klienta.
* **response.body**: Reprezentacja ciągu ciała, który ma zostać zwrócony do klienta.
* **response.headers. HeaderName\>: Nagłówek, który można ustawić dla odpowiedzi na klienta. \<** Zamień * \<\> nazwę nagłówka* nazwą nagłówka, który chcesz ustawić. Jeśli podasz pusty ciąg, nagłówek nie jest uwzględniony w odpowiedzi.

Wartości mogą odwoływać się do ustawień aplikacji, parametrów z oryginalnego żądania klienta i parametrów z odpowiedzi zaplecza.

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
> W tym przykładzie treść odpowiedzi jest ustawiana bezpośrednio, więc nie jest wymagana żadna `backendUri` właściwość. W przykładzie pokazano, jak można użyć usługi Azure Functions Serwery proxy do szyfrowania interfejsów API.

[Portal Azure]: https://portal.azure.com
[Wyzwalacze HTTP]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Definiowanie obiektu requestOverrides]: #requestOverrides
[Definiowanie obiektu responseOverrides]: #responseOverrides
[ustawienia aplikacji]: #use-appsettings
[Używanie zmiennych]: #using-variables
[parametry z oryginalnego żądania klienta]: #request-parameters
[parametry z odpowiedzi zaplecza]: #response-parameters
