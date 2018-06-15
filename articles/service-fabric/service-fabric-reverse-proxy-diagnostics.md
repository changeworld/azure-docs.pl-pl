---
title: Sieć szkieletowa usług Azure odwrotna diagnostyki serwera proxy | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorowanie i diagnozowanie przetwarzania żądania na zwrotnego serwera proxy.
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
ms.openlocfilehash: 662fc124af71c1ce976037a3544f59e3cea54ef0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207635"
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>Monitorowanie i diagnozowanie przetwarzania żądania na zwrotnego serwera proxy

Począwszy od wersji 5.7 wersji platformy Service Fabric, zdarzenia zwrotnego serwera proxy są dostępne dla kolekcji. Zdarzenia są dostępne w dwóch kanałów, jeden z tylko zdarzenia błędów związanych z niepowodzenia przetwarzania żądania na zwrotnego serwera proxy i drugi kanał zawierający zdarzenia pełne wpisy dla żądań pomyślnie i niepomyślnie.

Zapoznaj się [zbierania zdarzeń zwrotnego serwera proxy](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) umożliwia zbieranie zdarzeń z tych kanałów w klastrach sieć szkieletowa usług Azure i lokalnego.

## <a name="troubleshoot-using-diagnostics-logs"></a>Rozwiązywanie problemów przy użyciu dzienników diagnostycznych
Oto kilka przykładów na temat sposobu interpretacji Wspólnej dzienniki awarii napotykane przez jedną:

1. Zwrotny serwer proxy zwraca kod stanu odpowiedzi 504 (limit czasu).

    Jedną z przyczyn może wynikać z awarii odpowiedzi przed upływem limitu czasu żądania usługi.
Pierwsze zdarzenie poniżej dzienniki szczegóły żądania odebraniu zwrotnego serwera proxy. Drugie zdarzenie wskazuje, że żądanie nie powiodło się podczas przekazywania danych do usługi, z powodu "Błąd wewnętrzny = ERROR_WINHTTP_TIMEOUT" 

    Ładunek zawiera:

    *  **traceId**: ten identyfikator GUID może służyć do skorelowania wszystkie zdarzenia odpowiadające pojedyncze żądanie. W poniższych dwóch zdarzeń, traceId = **2f87b722-e254-4ac2-a802-fd315c1a0271**, co oznacza należą do tego samego żądania.
    *  **Adres_url_żądania**: adres URL (adres URL zwrotny serwer proxy), do której wysłano żądanie.
    *  **zlecenie**: Zlecenie HTTP.
    *  **remoteAddress**: adres wysyłania żądania klienta.
    *  **resolvedServiceUrl**: Usługa końcowego adresu URL, do którego żądania przychodzącego, jeśli został rozwiązany. 
    *  **Szczegóły błędu**: dodatkowe informacje o awarii.

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
    
    Poniżej przedstawiono zdarzenia przykład gdzie zwrotny serwer proxy zwraca 404, ponieważ go nie można odnaleźć pasującego punktu końcowego usługi.
    Wpisy ładunku zainteresowania w tym miejscu są:
    *  **processRequestPhase**: wskazuje fazy podczas przetwarzania żądania wystąpił błąd, ***TryGetEndpoint*** tj Podczas próby pobrania do przekazywania do punktu końcowego usługi. 
    *  **Szczegóły błędu**: Wyświetla listę kryteriów wyszukiwania punktu końcowego. Tutaj można zobaczyć, czy określony listenerName = **FrontEndListener** listy punktów końcowych repliki zawiera tylko odbiornik o nazwie **OldListener**.
    
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
    Innym przykładem, gdzie zwrotny serwer proxy może zwrócić 404 Nie znaleziono jest: parametr konfiguracji ApplicationGateway\Http **SecureOnlyMode** nasłuchuje ustawioną wartość true z zwrotnego serwera proxy na **HTTPS**, ale wszystkie punkty końcowe repliki są niezabezpieczone (nasłuchiwanie HTTP).
    Odtwarzanie zwraca proxy 404, ponieważ nie można odnaleźć punktu końcowego nasłuchiwania protokołu HTTPS, aby przesłać żądanie. Analizowanie parametrów w przypadku ładunku umożliwia zawężenie problemu:
    
    ```
        "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
    ```

3. Żądanie zwrotny serwer proxy kończy się niepowodzeniem z błędem przekroczenia limitu czasu. 
    Dzienniki zdarzeń zawierają zdarzenia ze szczegółami odebrane żądanie (nie jest tutaj widoczny).
    Następne zdarzenie pokazuje, że usługa zwrócił kod stanu 404 i inicjuje zwrotnego serwera proxy, ponownie rozwiązanie. 

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
    Podczas zbierania wszystkich zdarzeń, zostanie wyświetlony train zdarzeń przedstawiający co rozwiązanie, a następnie spróbuj do przodu.
    Ostatnie zdarzenie z tej serii przedstawiono przetwarzania żądania nie powiodła się z limitem czasu, wraz z liczbą prób rozpoznania powiodło się.
    
    > [!NOTE]
    > Zaleca się zachować zbierania zdarzeń kanału pełne domyślnie wyłączona i włącz ją do rozwiązywania problemów na podstawie potrzeby.

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
    
    Jeśli kolekcja jest włączony dla zdarzeń krytyczny błąd tylko, zobaczysz jednego zdarzenia o szczegółowe informacje dotyczące limitu czasu i liczby prób rozpoznania. 
    
    Usługi, które ma zostać wysłany kod stanu 404 do użytkownika, należy dodać nagłówka "X-ServiceFabric" w odpowiedzi. Po dodaniu do odpowiedzi nagłówek zwrotny serwer proxy przekazuje kod stanu do klienta.  

4. Przypadków, gdy klient rozłączył się żądanie.

    Następujące zdarzenie jest rejestrowane, gdy zwrotnego serwera proxy jest przekazywania odpowiedzi do klienta, ale klient odłączy się:

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

    Aby rozwiązać ten problem, określ schemat identyfikatora URI w manifeście.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> Zdarzenia związane z przetwarzania żądania websocket nie jest aktualnie zalogowany. Zostanie ona dodana w następnej wersji.

## <a name="next-steps"></a>Kolejne kroki
* [Agregacja zdarzeń i kolekcji przy użyciu systemu Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) umożliwiający zbieranie danych dziennika w klastrach platformy Azure.
* Aby wyświetlić zdarzenia platformy Service Fabric w programie Visual Studio, zobacz [monitorowanie i diagnozowanie lokalnie](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
* Zapoznaj się [Konfiguruj zwrotnego serwera proxy do nawiązania bezpiecznego usług](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) dla usługi Azure Resource Manager przykłady szablonu, aby skonfigurować zabezpieczenia zwrotny serwer proxy przy użyciu certyfikatu innej usługi opcji weryfikacji.
* Odczyt [sieci szkieletowej usług odwrotny serwer proxy](service-fabric-reverseproxy.md) Aby dowiedzieć się więcej.
