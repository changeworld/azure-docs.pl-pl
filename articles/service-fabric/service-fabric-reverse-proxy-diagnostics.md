---
title: Diagnostyka odwrotnego serwera proxy sieci szkieletowej usług Azure
description: Dowiedz się, jak monitorować i diagnozować przetwarzanie żądań na serwerze proxy odwrotnej dla aplikacji sieci szkieletowej usług Azure.
author: kavyako
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: bbc1fe5a76ecb5720bc49e0a082d5e9151b403d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645467"
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>Monitorowanie i diagnozowanie przetwarzania żądań na odwrotnym serwerze proxy

Począwszy od wersji 5.7 sieci szkieletowej usług, zdarzenia odwrotnego serwera proxy są dostępne do zbierania. Zdarzenia są dostępne w dwóch kanałach, jeden ze zdarzeniami błędu związanymi z niepowodzeniem przetwarzania żądania w odwrotnej serwera proxy i drugiego kanału zawierającego pełne zdarzenia z wpisami dla żądań pomyślnych i nieudanych.

Zapoznaj się [z collect odwrotnej zdarzeń proxy,](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) aby włączyć zbieranie zdarzeń z tych kanałów w klastrach lokalnych i usługi Azure Service Fabric.

## <a name="troubleshoot-using-diagnostics-logs"></a>Rozwiązywanie problemów z korzystaniem z dzienników diagnostycznych
Oto kilka przykładów interpretacji typowych dzienników błędów, które można napotkać:

1. Odwrotny serwer proxy zwraca kod stanu odpowiedzi 504 (Limit czasu).

    Jednym z powodów może być ze względu na usługę nie odpowiada w okresie limitu czasu żądania.
   Pierwsze zdarzenie poniżej rejestruje szczegóły żądania otrzymanego na odwrotnej serwera proxy. 
   Drugie zdarzenie wskazuje, że żądanie nie powiodło się podczas przekazywania do usługi z powodu "błąd wewnętrzny = ERROR_WINHTTP_TIMEOUT" 

    Ładowność obejmuje:

   * **traceId:** Ten identyfikator GUID może służyć do skorelowania wszystkich zdarzeń odpowiadających pojedynczemu żądaniu. W poniższych dwóch zdarzeń traceId = **2f87b722-e254-4ac2-a802-fd315c1a0271**, co oznacza, że należą one do tego samego żądania.
   * **requestUrl**: Adres URL (odwrotny adres URL serwera proxy), do którego wysłano żądanie.
   * **czasownik**: CZASOWNIK HTTP.
   * **remoteAddress**: Adres klienta wysyłającego żądanie.
   * **resolvedServiceUrl**: Adres URL punktu końcowego usługi, do którego zostało rozwiązane żądanie przychodzące. 
   * **errorDetails**: Dodatkowe informacje o awarii.

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

2. Odwrócony serwer proxy zwraca kod stanu odpowiedzi 404 (Nie znaleziono). 
    
    Oto przykładowe zdarzenie, w którym odwrotny serwer proxy zwraca 404, ponieważ nie można znaleźć pasującego punktu końcowego usługi.
    Wpisy ładunku zainteresowania tutaj są:
   * **procesRequestPhase**: Wskazuje fazę podczas przetwarzania żądania, gdy wystąpił błąd, ***TryGetEndpoint*** tj. podczas próby pobrania punktu końcowego usługi do przodu. 
   * **errorDetails**: Wyświetla listę kryteriów wyszukiwania punktów końcowych. W tym miejscu widać, że nazwa odbiornika określona = **FrontEndListener,** podczas gdy lista punktów końcowych repliki zawiera tylko odbiornik o nazwie **OldListener**.
    
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
     Innym przykładem, gdzie odwrotny serwer proxy może zwrócić 404 Nie znaleziono jest: ApplicationGateway\Http parametr konfiguracji **SecureOnlyMode** jest ustawiona na true z odwrotnego serwera proxy nasłuchiwania na **HTTPS,** jednak wszystkie punkty końcowe repliki są niezabezpieczone (nasłuchiwanie na HTTP).
     Odwrotny serwer proxy zwraca 404, ponieważ nie można znaleźć punktu końcowego nasłuchiwania na HTTPS do przekazania żądania. Analiza parametrów w ładunku zdarzenia pomaga zawęzić problem:
    
     ```
      "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
     ```

3. Żądanie do odwrotnego serwera proxy kończy się niepowodzeniem z błędem limitu czasu. 
    Dzienniki zdarzeń zawierają zdarzenie z informacjami o odebranym żądaniu (nie pokazano tutaj).
    Następne zdarzenie pokazuje, że usługa odpowiedziała kodem stanu 404 i odwrotnego serwera proxy inicjuje ponowne rozwiązanie. 

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
    Podczas zbierania wszystkich zdarzeń, widać pociąg zdarzeń pokazujący każdą próbę rozwiązania i do przodu.
    Ostatnie zdarzenie w serii pokazuje, że przetwarzanie żądania nie powiodło się z limitem czasu, wraz z liczbą pomyślnych prób rozwiązania.
    
    > [!NOTE]
    > Zaleca się, aby domyślnie wyłączyć kolekcję zdarzeń szczegółowego kanału i włączyć ją do rozwiązywania problemów na podstawie potrzeb.

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
    
    Jeśli kolekcja jest włączona tylko dla zdarzeń krytycznych/błędów, zostanie wyświetlone jedno zdarzenie ze szczegółami dotyczącymi limitu czasu i liczby prób rozwiązania. 
    
    Usługi, które zamierzają wysłać kod stanu 404 z powrotem do użytkownika, należy dodać nagłówek "X-ServiceFabric" w odpowiedzi. Po dodaniu nagłówka do odpowiedzi wstecznego serwera proxy przekazuje kod stanu z powrotem do klienta.  

4. Przypadki, gdy klient rozłączył żądanie.

    Następujące zdarzenie jest rejestrowane, gdy odwrotny serwer proxy jest przekazywanie odpowiedzi do klienta, ale klient rozłącza:

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
5. Odwrotny serwer proxy zwraca 404 FABRIC_E_SERVICE_DOES_NOT_EXIST

    FABRIC_E_SERVICE_DOES_NOT_EXIST błąd jest zwracany, jeśli schemat URI nie jest określony dla punktu końcowego usługi w manifeście usługi.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    Aby rozwiązać ten problem, należy określić schemat URI w manifeście.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> Zdarzenia związane z przetwarzaniem żądań websocket nie są obecnie rejestrowane. Zostanie to dodane w następnej wersji.

## <a name="next-steps"></a>Następne kroki
* [Agregacja zdarzeń i zbieranie przy użyciu diagnostyki systemu Windows Azure](service-fabric-diagnostics-event-aggregation-wad.md) do włączania kolekcji dzienników w klastrach platformy Azure.
* Aby wyświetlić zdarzenia sieci szkieletowej usług w programie Visual Studio, zobacz [Monitorowanie i diagnozowanie lokalnie](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
* Zapoznaj się [z pozycję Konfigurowanie odwrotnego serwera proxy, aby połączyć się z bezpiecznymi usługami](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services) dla przykładów szablonów usługi Azure Resource Manager, aby skonfigurować bezpieczny odwrotny serwer proxy z różnymi opcjami sprawdzania poprawności certyfikatu usługi.
* Przeczytaj [odwrotny serwer proxy sieci usług,](service-fabric-reverseproxy.md) aby dowiedzieć się więcej.
