---
title: Usługa Azure Service Fabric Diagnostyka zwrotnego serwera proxy | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować i diagnozować przetwarzania żądania na zwrotny serwer proxy.
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: c9c8c649208cff95f4ee515d39cc8cca3e2c64bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60726846"
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>Monitorowanie i diagnozowanie przetwarzania żądania na zwrotny serwer proxy

Począwszy od wersji 5.7 wersję usługi Service Fabric, zdarzenia zwrotnego serwera proxy są dostępne dla kolekcji. Zdarzenia są dostępne w dwóch kanałów, jeden z tylko zdarzenia błędu dotyczące niepowodzenia przetwarzania żądania zwrotnego serwera proxy i drugi kanał zawierający pełne zdarzenia z wpisy dla zarówno udane, jak i nieudane żądania.

Zapoznaj się [zbierania zdarzeń zwrotny serwer proxy](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) umożliwia zbieranie zdarzeń z tych kanałów w lokalnych i w klastrach usługi Azure Service Fabric.

## <a name="troubleshoot-using-diagnostics-logs"></a>Rozwiązywanie problemów przy użyciu dzienników diagnostycznych
Poniżej przedstawiono kilka przykładów na temat sposobu interpretacji do wspólnych dzienników błędów, napotykane przez jeden:

1. Zwrotny serwer proxy zwraca kod stanu odpowiedzi 504 (limit czasu).

    Jedną z przyczyn może być spowodowany usługi kończy się niepowodzeniem na udzielenie odpowiedzi przed upływem limitu czasu żądania.
   Pierwsze zdarzenie poniżej dzienniki szczegółów żądania otrzymane pod zwrotny serwer proxy. 
   Drugie zdarzenie wskazuje, że żądanie nie powiodło się podczas przekazywania danych do usługi, ze względu na "wewnętrzny błąd = ERROR_WINHTTP_TIMEOUT" 

    Ładunek zawiera:

   * **traceId**: Ten identyfikator GUID może służyć do skorelowania wszystkie zdarzenia odpowiadający pojedynczego żądania. W poniższych dwóch zdarzeń, traceId = **2f87b722-e254-4ac2-a802-fd315c1a0271**, co oznacza, że należą one do tego samego żądania.
   * **requestUrl**: Adres URL (adres URL zwrotny serwer proxy) z którym żądanie zostało wysłane.
   * **czasownik**: Czasownik HTTP.
   * **Obiekt remoteAddress**: Adres klienta wysyłającego żądanie.
   * **resolvedServiceUrl**: URL punktu końcowego usługi, do którego przychodzącego żądania został rozwiązany. 
   * **Szczegóły błędu**: Dodatkowe informacje o błędzie.

     ```
     {
     "Timestamp": "2017-07-20T15:57:59.9871163-07:00",
     "ProviderName": "Microsoft-ServiceFabric",
     "Id": 51477,
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Request url = https://localhost:19081/LocationApp/LocationFEService?zipcode=98052, verb = GET, remote (client) address = ::1, resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052, request processing start time =     15:58:00.074114 (745,608.196 MSec) ",
     "ProcessId": 57696,
     "Level": "Informational",
     "Keywords": "0x1000000000000021",
     "EventName": "ReverseProxy",
     "ActivityID": null,
     "RelatedActivityID": null,
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?zipcode=98052",
      "verb": "GET",
      "remoteAddress": "::1",
      "resolvedServiceUrl": "Https://localhost:8491/LocationApp/?zipcode=98052",
      "requestStartTime": "2017-07-20T15:58:00.0741142-07:00"
     }
     }

     {
     "Timestamp": "2017-07-20T16:00:01.3173605-07:00",
     ...
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Error while forwarding request to service: response status code = 504, description = Reverse proxy Timeout, phase = FinishSendRequest, internal error = ERROR_WINHTTP_TIMEOUT ",
     ...
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "statusCode": 504,
      "description": "Reverse Proxy Timeout",
      "sendRequestPhase": "FinishSendRequest",
      "errorDetails": "internal error = ERROR_WINHTTP_TIMEOUT"
     }
     }
     ```

2. Zwrotny serwer proxy zwraca kod stanu odpowiedzi 404 (nie znaleziono). 
    
    Oto przykład zdarzenia gdzie zwrotny serwer proxy zwraca 404, ponieważ nie udało się odnaleźć zgodnego punktu końcowego usługi.
    Wpisy ładunku zainteresowania w tym miejscu są:
   * **processRequestPhase**: Wskazuje fazy podczas przetwarzania żądania wystąpił błąd, ***TryGetEndpoint*** tj Podczas próby pobrania punktu końcowego usługi do przekazywania. 
   * **Szczegóły błędu**: Wyświetla listę kryteriów wyszukiwania punktu końcowego. Tutaj widać, że określony listenerName = **FrontEndListener** lista punktów końcowych repliki zawiera tylko odbiornik o nazwie **OldListener**.
    
     ```
     {
     ...
     "Message": "c1cca3b7-f85d-4fef-a162-88af23604343 Error while processing request, cannot forward to service: request url = https://localhost:19081/LocationApp/LocationFEService?ListenerName=FrontEndListener&zipcode=98052, verb = GET, remote (client) address = ::1, request processing start time = 16:43:02.686271 (3,448,220.353 MSec), error = FABRIC_E_ENDPOINT_NOT_FOUND, message = , phase = TryGetEndoint, SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"\":\"Https:\/\/localhost:8491\/LocationApp\/\"}} ",
     "ProcessId": 57696,
     "Level": "Warning",
     "EventName": "ReverseProxy",
     "Payload": {
      "traceId": "c1cca3b7-f85d-4fef-a162-88af23604343",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?ListenerName=NewListener&zipcode=98052",
      ...
      "processRequestPhase": "TryGetEndoint",
      "errorDetails": "SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Https:\/\/localhost:8491\/LocationApp\/\"}}"
     }
     }
     ```
     Inny przykład, w którym zwrotny serwer proxy może zwrócić kod 404 Nie znaleziono jest: Parametr konfiguracji ApplicationGateway\Http **SecureOnlyMode** ma wartość true, przy użyciu zwrotnego serwera proxy nasłuchuje **HTTPS**, ale wszystkie punkty końcowe repliki są niezabezpieczone (nasłuchiwania protokołu HTTP).
     Reverse proxy zwraca 404, ponieważ nie można odnaleźć punktu końcowego nasłuchiwanie przy użyciu protokołu HTTPS do przesyłania żądania. Analizowanie parametrów, w przypadku ładunku ułatwia zawęzić problemu:
    
     ```
      "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
     ```

3. Żądanie zwrotny serwer proxy kończy się niepowodzeniem z błędem przekroczenia limitu czasu. 
    Dzienniki zdarzeń zawierają zdarzenia ze szczegółami odebrane żądanie (nie pokazane tutaj).
    Następne zdarzenie pokazuje, że usługa zwrócił kod stanu 404 i zwrotny serwer proxy inicjuje ponownie rozwiązanie. 

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Request to service returned: status code = 404, status description = , Reresolving ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "statusCode": 404,
        "statusDescription": ""
      }
    }
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Re-resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052 ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "requestUrl": "Https://localhost:8491/LocationApp/?zipcode=98052"
      }
    }
    ```
    Podczas zbierania wszystkich zdarzeń, zostanie wyświetlony train zdarzeń przedstawiający każdego rozwiązania i próba do przodu.
    Ostatnie zdarzenie w serii pokazuje, że przetwarzanie żądania nie powiodło się z limitem czasu, oraz liczbę prób pomyślne rozwiązanie.
    
    > [!NOTE]
    > Zaleca się zachować zbierania zdarzeń pełne kanału, które są domyślnie wyłączone i włącz ją do rozwiązywania problemów w oparciu o potrzeby.

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Error while processing request: number of successful resolve attempts = 12, error = FABRIC_E_TIMEOUT, message = , phase = ResolveServicePartition,  ",
      "EventName": "ReverseProxy",
      ...
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "resolveCount": 12,
        "errorval": -2147017729,
        "errorMessage": "",
        "processRequestPhase": "ResolveServicePartition",
        "errorDetails": ""
      }
    }
    ```
    
    Jeśli kolekcja jest włączona tylko zdarzenia krytyczne/błąd, zostanie wyświetlony jedno zdarzenie ze szczegółowymi informacjami o limit czasu i liczby prób rozwiązania. 
    
    Usługi, które ma zostać wysłany kod stanu 404 do użytkownika, należy dodać nagłówek "X-ServiceFabric" w odpowiedzi. Po dodaniu do odpowiedzi nagłówek zwrotny serwer proxy przekazuje kod stanu do klienta.  

4. Przypadków, gdy klient rozłączył się żądanie.

    Następujące zdarzenie jest rejestrowane, gdy zwrotny serwer proxy jest przekazywania odpowiedzi do klienta, ale klient odłączy się:

    ```
    {
      ...
      "Message": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8 Unable to send response to client: phase = SendResponseHeaders, error = -805306367, internal error = ERROR_SUCCESS ",
      "ProcessId": 57696,
      "Level": "Warning",
      ...
      "EventName": "ReverseProxy",
      "Payload": {
        "traceId": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8",
        "sendResponsePhase": "SendResponseHeaders",
        "errorval": -805306367,
        "winHttpError": "ERROR_SUCCESS"
      }
    }
    ```
5. Zwrotny serwer Proxy zwraca 404 FABRIC_E_SERVICE_DOES_NOT_EXIST

    FABRIC_E_SERVICE_DOES_NOT_EXIST błąd jest zwracany, jeśli nie określono schemat identyfikatora URI punktu końcowego usługi w manifeście usługi.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    Aby rozwiązać ten problem, należy określić schemat identyfikatora URI w manifeście.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> Obecnie nie są rejestrowane zdarzenia związane z przetwarzania żądania protokołu websocket. Zostanie ona dodana w następnej wersji.

## <a name="next-steps"></a>Kolejne kroki
* [Zdarzenie agregacji i kolekcji przy użyciu Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) umożliwiające zbieranie danych dziennika w klastrach platformy Azure.
* Aby wyświetlić zdarzenia usługi Service Fabric w programie Visual Studio, zobacz [monitorowanie i diagnozowanie lokalnie](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
* Zapoznaj się [Konfigurowanie zwrotnego serwera proxy do nawiązywania bezpiecznej usługami](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) dla usługi Azure Resource Manager przykłady szablonów, aby skonfigurować bezpieczny zwrotny serwer proxy, za pomocą certyfikatu usługi różne opcje weryfikacji.
* Odczyt [usługi Service Fabric zwrotny serwer proxy](service-fabric-reverseproxy.md) Aby dowiedzieć się więcej.
