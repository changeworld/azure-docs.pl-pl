---
title: Praca z serwerów proxy w usługi Azure Functions | Dokumentacja firmy Microsoft
description: Omówienie sposobu korzystania z serwerów proxy funkcji platformy Azure
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: 0e7fe474c3b247baa6550770c661af62e83b3737
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
ms.locfileid: "29933779"
---
# <a name="work-with-azure-functions-proxies"></a>Praca z serwerów proxy Azure Functions

W tym artykule opisano sposób konfigurowania i pracować z serwerów proxy funkcji platformy Azure. Dzięki tej funkcji można określić punkty końcowe na aplikację funkcji, które zostały zaimplementowane przez inny zasób. Umożliwia te serwery proxy Podziel dużych interfejsu API na wiele aplikacji funkcji (tak jak to architektura mikrousługi), prezentując pojedynczą powierzchnię interfejsu API dla klientów jednocześnie.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Standardowe funkcje rozliczeń dotyczy wykonaniami serwera proxy. Aby uzyskać więcej informacji, zobacz [cennik usługi Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create"></a>Utwórz serwer proxy

W tej sekcji przedstawiono sposób tworzenia serwera proxy w portalu funkcji.

1. Otwórz [portalu Azure], a następnie przejdź do aplikacji funkcji.
2. W okienku po lewej stronie wybierz **nowego serwera proxy**.
3. Podaj nazwę serwera proxy.
4. Skonfiguruj punktu końcowego, która jest widoczna w tym funkcji aplikacji, określając **szablon trasy** i **metod HTTP**. Parametry te działają zgodnie z regułami dla [wyzwalaczy HTTP].
5. Ustaw **URL wewnętrznej bazy danych** do innego punktu końcowego. Ten punkt końcowy może być funkcją w innej aplikacji funkcji lub może być jakiegokolwiek interfejsu API. Wartość musi być statyczne i mogą się odwoływać do [ustawienia aplikacji] i [parametrów z żądania klienta oryginalnym].
6. Kliknij przycisk **Utwórz**.

Serwer proxy obecnie istnieje jako nowy punkt końcowy w aplikacji funkcji. Z perspektywy klienta jest odpowiednikiem HttpTrigger w funkcji platformy Azure. Można wypróbować nowego serwera proxy, kopiując adres URL serwera Proxy i testowanie go z ulubionych klienta HTTP.

## <a name="modify-requests-responses"></a>Modyfikowanie żądań i odpowiedzi

Za pomocą proxy funkcji platformy Azure można modyfikować żądania i odpowiedzi z zaplecza. Przekształcenia te można używać zmiennych, zgodnie z definicją w [używać zmiennych].

### <a name="modify-backend-request"></a>Modyfikowanie żądania zaplecza

Domyślnie żądania zaplecza został zainicjowany jako kopia oryginalnego żądania. Oprócz skonfigurowania adresu URL zaplecza, można wprowadzić zmiany do metody HTTP, nagłówki i parametrów ciągu zapytania. Zmodyfikowane wartości może się odwoływać [ustawienia aplikacji] i [parametrów z żądania klienta oryginalnym].

Żądania zaplecza może być modyfikowany w portalu przez expading *żądania zastąpienie* części strony szczegółów serwera proxy. 

### <a name="modify-response"></a>Modyfikować odpowiedzi

Domyślnie odpowiedzi klienta zostanie zainicjowany jako kopię odpowiedzi zaplecza. Kod stanu odpowiedzi, frazę przyczyny, nagłówki i treści, można wprowadzić zmiany. Zmodyfikowane wartości może się odwoływać [ustawienia aplikacji], [parametrów z żądania klienta oryginalnym], i [parametry z odpowiedzi zaplecza].

Żądania zaplecza może być modyfikowany w portalu przez expading *zastąpienie odpowiedzi* części strony szczegółów serwera proxy. 

## <a name="using-variables"></a>Używać zmiennych

Konfiguracja serwera proxy dla muszą być statyczne. Można warunku, aby używać zmiennych z oryginalnego żądania klienta, zaplecza odpowiedzi lub ustawień aplikacji.

### <a name="reference-localhost"></a>Odwołanie do funkcji lokalnej
Można użyć `localhost` do odwołania funkcji w tej samej aplikacji funkcja bezpośrednio, bez przesyłania żądania serwera proxy.

`"backendurl": "https://localhost/api/httptriggerC#1"` będzie odwoływać się do funkcji lokalnej HTTP wyzwalane w trasy `/api/httptriggerC#1`

 
>[!Note]  
>Jeśli korzysta z funkcji *funkcję, administrator lub sys* poziomy autoryzacji, konieczne będzie podanie kodu i clientId, zgodnie z harmonogramem oryginalny adres URL funkcji. W takim przypadku będzie wyglądać odwołania: `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"`

### <a name="request-parameters"></a>Parametry żądania odwołania

Parametry żądania można użyć jako dane wejściowe, aby właściwość URL zaplecza lub w ramach modyfikowania żądania i odpowiedzi. Niektóre parametry mogą być powiązane z szablonu trasy, który został określony w konfiguracji podstawowej serwera proxy, a inne osoby mogą pochodzić z właściwości z żądania przychodzącego.

#### <a name="route-template-parameters"></a>Parametry szablonu trasy
Parametry, które są używane w szablonie trasy dostępnych może odwoływać się do nazwy. Nazwy parametrów są ujęte w nawiasy klamrowe ({}).

Na przykład, jeśli serwer proxy ma szablon trasy, takich jak `/pets/{petId}`, adres URL zaplecza może zawierać wartości `{petId}`, jak w `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Jeśli szablon trasy kończy w symboli wieloznacznych, takich jak `/api/{*restOfPath}`, wartość `{restOfPath}` jest reprezentację ciągu pozostałych segmentów ścieżki z żądania przychodzącego.

#### <a name="additional-request-parameters"></a>Dodatkowe parametry żądania
Oprócz parametrów szablonu trasy można używać następujących wartości w wartości konfiguracji:

* **{Request.method wartość}** : Metoda HTTP, która jest używana na oryginalne żądanie.
* **{request.headers. \<HeaderName\>}**: nagłówek, który może zostać odczytany z oryginalnego żądania. Zastąp *\<HeaderName\>* o nazwie nagłówka, który chcesz odczytać. Jeśli żądanie nie zawiera nagłówka, wartość będzie pusty ciąg.
* **{request.querystring. \<ParameterName\>}**: parametr ciągu zapytania, który może zostać odczytany z oryginalnego żądania. Zastąp *\<ParameterName\>* o nazwę parametru, który chcesz odczytać. Jeśli parametr nie jest dostępna na żądanie, wartość będzie pusty ciąg.

### <a name="response-parameters"></a>Parametry zaplecza odpowiedzi odwołania

Parametry odpowiedzi mogą być używane jako część modyfikowania odpowiedzi do klienta. W wartości konfiguracji można używać następujących wartości:

* **{backend.response.statusCode}** : Kod stanu HTTP, który jest zwracany w odpowiedzi zaplecza.
* **{backend.response.statusReason}** : Fraza przyczyny protokołu HTTP, który jest zwracany w odpowiedzi zaplecza.
* **{backend.response.headers. \<HeaderName\>}**: nagłówek, który może zostać odczytany z odpowiedzi zaplecza. Zastąp *\<HeaderName\>* o nazwie nagłówka, który chcesz odczytać. Jeśli nie ma nagłówka odpowiedzi, wartość będzie pusty ciąg.

### <a name="use-appsettings"></a>Odwołanie do ustawienia aplikacji

Można także odwoływać [ustawienia aplikacji określone dla aplikacji funkcja](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#develop) przez wpisywania nazwy ustawienia w znaki procentu (%).

Na przykład adres URL zaplecza z *https://%ORDER_PROCESSING_HOST%/api/orders* byłyby "% ORDER_PROCESSING_HOST %" zastąpione przez ustawienie ORDER_PROCESSING_HOST.

> [!TIP] 
> Użyj ustawienia aplikacji dla hostów zaplecza w przypadku wielu wdrożeń lub środowisk testowych. W ten sposób można upewnić się, że zawsze mówimy do prawej zaplecza dla tego środowiska.

## <a name="debugProxies"></a>Rozwiązywanie problemów z serwerów proxy

Dodając flagi `"debug":true` do dowolnego serwera proxy w sieci `proxies.json` spowoduje włączenie rejestrowania debugowania. Dzienniki są przechowywane w `D:\home\LogFiles\Application\Proxies\DetailedTrace` i jest dostępny za pośrednictwem zaawansowanych narzędzi (kudu). Wszystkie odpowiedzi HTTP będzie również zawierać `Proxy-Trace-Location` nagłówka o adresie URL dostępu do pliku dziennika.

Serwer proxy po stronie klienta można debugować przez dodanie `Proxy-Trace-Enabled` wartość nagłówka `true`. Spowoduje to również rejestrowania danych śledzenia w systemie plików i zwraca adres URL śledzenia jako nagłówka w odpowiedzi.

### <a name="block-proxy-traces"></a>Blok serwera proxy śledzenia

Ze względów bezpieczeństwa możesz nie pozwala nikomu wywoływania usługi do generowania śledzenia. Nie będą oni mogli uzyskać dostępu do zawartości śledzenia bez poświadczeń logowania, ale generowania śledzenia wykorzystuje zasoby i ujawnia używasz funkcji serwerów proxy.

Całkowicie wyłączyć śladów, dodając `"debug":false` do dowolnego określonego serwera proxy w sieci `proxies.json`.

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana

Serwery proxy, które można skonfigurować są przechowywane w *proxies.json* pliku, który znajduje się w folderze głównym katalogiem aplikacji funkcji. Można ręcznie edytować ten plik i wdrożyć go jako część aplikacji przy użyciu jednej z [metody wdrażania](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) obsługiwane przez funkcje. 

> [!TIP] 
> Jeśli nie zdefiniowano jednej z metod wdrażania, możesz także pracować z *proxies.json* pliku w portalu. Przejdź do funkcji aplikacji, wybierz opcję **funkcji platformy**, a następnie wybierz **Edytor usług aplikacji**. W ten sposób można wyświetlić strukturę całego pliku aplikacji funkcji, a następnie wprowadź zmiany.

*Proxies.JSON* jest definiowana za pomocą obiektu serwery proxy, który składa się z nazwanym proxy oraz ich definicje. Opcjonalnie, jeśli edytor obsługuje tę funkcję, możesz odwoływać się do [schematu JSON](http://json.schemastore.org/proxies) dla uzupełniania kodu. Przykładowy plik może wyglądać następująco:

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

Każdy serwer proxy ma przyjazną nazwę, takich jak *proxy1* w poprzednim przykładzie. Odpowiedni obiekt serwera proxy w definicji jest zdefiniowane przez następujących właściwościach:

* **matchCondition**: wymagany — obiekt definiujący żądań, które wyzwalają wykonanie tego serwera proxy. Zawiera dwie właściwości, które są udostępniane [wyzwalaczy HTTP]:
    * _metody_: Tablica metod HTTP, które odpowiada serwera proxy. Jeśli nie zostanie określony, serwer proxy reaguje na wszystkie metody HTTP na trasie.
    * _trasy_: wymagany — definiuje szablon trasy, kontrolowanie, które adresów URL żądań proxy odpowiada. W odróżnieniu od w HTTP wyzwalaczy, nie ma wartości domyślnej.
* **backendUri**: adres URL zasobu zaplecza, do którego powinien być serwerem proxy żądania. Ta wartość może odwoływać się parametry i ustawienia aplikacji z oryginalnego żądania klienta. Jeśli ta właściwość nie jest uwzględniona, usługi Azure Functions odpowiada HTTP 200 OK.
* **requestOverrides**: obiekt, który definiuje przekształcenia na żądanie zaplecza. Zobacz [zdefiniować obiekt requestOverrides].
* **responseOverrides**: obiekt, który definiuje przekształcenia odpowiedzi klienta. Zobacz [zdefiniować obiekt responseOverrides].

> [!NOTE] 
> *Trasy* właściwości w proxy funkcji Azure honoruje *routePrefix* właściwości konfiguracji hostów funkcji aplikacji. Jeśli chcesz obejmują takie jak prefiks `/api`, muszą być zawarte w *trasy* właściwości.

### <a name="disableProxies"></a>Wyłącz poszczególnych serwerów proxy

Można wyłączyć poszczególne serwery proxy, dodając `"disabled": true` do serwera proxy w `proxies.json` pliku. To spowoduje, że wszystkie żądania spotkania matchCondidtion do zwrócenia 404.
```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "Root": {
            "disabled":true,
            "matchCondition": {
                "route": "/example"
            },
            "backendUri": "www.example.com"
        }
    }
}
```

### <a name="requestOverrides"></a>Zdefiniuj obiektu requestOverrides

Obiekt requestOverrides definiuje zmiany wprowadzone do żądania wywołanego zasobów wewnętrznych. Obiekt jest zdefiniowane przez następujących właściwościach:

* **backend.Request.Method**: metody HTTP, które są używane do wywoływania zaplecza.
* **backend.Request.QueryString. \<ParameterName\>**: parametr ciągu zapytania, które można ustawić dla wywołania zaplecza. Zastąp *\<ParameterName\>* o nazwę parametru, który chcesz ustawić. W przypadku pustego ciągu parametru nie jest uwzględniony w żądaniu zaplecza.
* **backend.Request.headers. \<HeaderName\>**: nagłówek, który można ustawić dla wywołania zaplecza. Zastąp *\<HeaderName\>* o nazwie nagłówka, którą chcesz ustawić. Jeśli podasz pustym ciągiem nagłówka nie znajduje się na żądanie zaplecza.

Ustawienia aplikacji i parametrów wartości można odwoływać się z oryginalnego żądania klienta.

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

### <a name="responseOverrides"></a>Zdefiniuj obiektu responseOverrides

Obiekt requestOverrides definiuje zmiany wprowadzone do odpowiedzi, która jest przekazywane z powrotem do klienta. Obiekt jest zdefiniowane przez następujących właściwościach:

* **response.statusCode**: kod stanu HTTP ma zostać zwrócona do klienta.
* **response.statusReason**: fraza przyczyny HTTP ma zostać zwrócona do klienta.
* **Response.body**: reprezentację ciągu treści, która ma zostać zwrócona do klienta.
* **Response.headers. \<HeaderName\>**: nagłówek, który można ustawić dla odpowiedzi do klienta. Zastąp *\<HeaderName\>* o nazwie nagłówka, którą chcesz ustawić. Jeśli podasz pustym ciągiem nagłówka nie jest uwzględniony w odpowiedzi.

Ustawienia aplikacji, parametrów z żądania klienta oryginalnym i parametry wartości można odwoływać się z odpowiedzi zaplecza.

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
> W tym przykładzie treść odpowiedzi jest ustawiony bezpośrednio, więc nie `backendUri` właściwość jest wymagana. W przykładzie pokazano, jak można użyć do mocking interfejsów API Azure funkcji z serwerów proxy.

[portalu Azure]: https://portal.azure.com
[wyzwalaczy HTTP]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#http-trigger
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[zdefiniować obiekt requestOverrides]: #requestOverrides
[zdefiniować obiekt responseOverrides]: #responseOverrides
[ustawienia aplikacji]: #use-appsettings
[używać zmiennych]: #using-variables
[parametrów z żądania klienta oryginalnym]: #request-parameters
[parametry z odpowiedzi zaplecza]: #response-parameters
