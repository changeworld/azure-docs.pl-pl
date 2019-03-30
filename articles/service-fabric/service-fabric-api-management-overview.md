---
title: Usługa Azure Service Fabric za pomocą usługi API Management — omówienie | Dokumentacja firmy Microsoft
description: Ten artykuł stanowi wprowadzenie do korzystania z usługi Azure API Management jako brama aplikacji usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: 0dac2730bcc13b979de6a8faaaa53c0aaf15e902
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669354"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Usługa Service Fabric za pomocą usługi Azure API Management — omówienie

Aplikacje w chmurze zwykle potrzebują bramy frontonu, aby udostępniać pojedynczy punkt danych przychodzących dla użytkowników, urządzeń lub innych aplikacji. W usłudze Service Fabric bramą może być dowolna usługa bezstanowa takich jak [aplikacji platformy ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), lub inna usługa przeznaczona dla ruchu przychodzącego, takich jak [usługi Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [usługi IoT Hub](https://docs.microsoft.com/azure/iot-hub/), lub [usługi Azure API Management](https://docs.microsoft.com/azure/api-management/).

Ten artykuł stanowi wprowadzenie do korzystania z usługi Azure API Management jako brama aplikacji usługi Service Fabric. Usługa API Management, integruje się bezpośrednio z usługą Service Fabric, umożliwiające publikowanie interfejsów API bogaty zestaw reguł routingu w celu usługi Service Fabric zaplecza. 

## <a name="availability"></a>Dostępność

> [!IMPORTANT]
> Ta funkcja jest dostępna w **Premium** i **Developer** warstw usługi API Management z powodu wymagane wirtualnych sieci pomocy technicznej.

## <a name="architecture"></a>Architektura

Architektura usługi Service Fabric używa aplikacji jednej strony sieci web, która sprawia, że wywołania HTTP do usługi zaplecza, które uwidaczniają interfejsów API protokołu HTTP. [Usługi Service Fabric — wprowadzenie do przykładowej aplikacji](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) przedstawiono przykład tej architektury.

W tym scenariuszu bezstanowej usługi internetowej służy jako bramy w aplikacji usługi Service Fabric. Takie podejście wymaga zapisu usługi sieci web, która może być żądań HTTP serwera proxy usług zaplecza, jak pokazano na poniższym diagramie:

![Usługa Service Fabric za pomocą usługi Azure API Management omówienie topologii][sf-web-app-stateless-gateway]

Jak aplikacje zwiększanie się stopnia skomplikowania, więc wykonaj bram, które musi przedstawić interfejsu API przed wiele usług zaplecza. Usługa Azure API Management jest przeznaczony do obsługi złożonych interfejsów API za pomocą reguł routingu, kontroli dostępu, ograniczania szybkości, monitorowanie, rejestrowanie zdarzeń i buforowanie odpowiedzi przy minimalnym nakładzie pracy ze strony użytkownika. Usługa Azure API Management obsługuje odnajdowania usługi Service Fabric, rozpoznawanie partycji i replik wybór inteligentnie kierować żądania bezpośrednio do usług zaplecza w usłudze Service Fabric, aby nie musieli napisać własny bezstanowe bramy interfejsu API. 

W tym scenariuszu sieci web interfejsu użytkownika jest nadal obsługiwany za pośrednictwem usługi sieci web podczas wywołania interfejsu API protokołu HTTP są zarządzane i kierowane za pośrednictwem usługi Azure API Management, jak pokazano na poniższym diagramie:

![Usługa Service Fabric za pomocą usługi Azure API Management omówienie topologii][sf-apim-web-app]

## <a name="application-scenarios"></a>Scenariusze aplikacji

Usługi w usłudze Service Fabric może być bezstanowe lub stanowe i mogą być dzielone, przy użyciu jednej z trzech schematów: pojedyncze, zakres int 64 i nazwanych. Rozpoznanie punktu końcowego usługi wymaga identyfikacji określoną partycję konkretnego wystąpienia usługi. Podczas rozpoznawania punktu końcowego usługi, obie nazwy wystąpienia usługi (na przykład `fabric:/myapp/myservice`) również muszą być określone określonej partycji usługi, z wyjątkiem pojedynczych partycji.

Usługa Azure API Management może służyć za pomocą dowolnej kombinacji usług bezstanowych, usługi stanowe i dowolnego schematu partycjonowania.

## <a name="send-traffic-to-a-stateless-service"></a>Wysyłaj ruch do usługi bezstanowej

W najprostszym przypadku ruch jest przekazywany do wystąpienia usługi bezstanowej. Aby to osiągnąć, operacja usługi API Management zawiera zasadę przychodzące przetwarzanie za pomocą usługi Service Fabric zaplecza mapujący wystąpienie określonej usługi bezstanowej usługi Service Fabric zaplecza. Żądania wysyłane do tej usługi są wysyłane do losowych repliki wystąpienia usługi bezstanowej.

#### <a name="example"></a>Przykład
W poniższym scenariuszu aplikacji usługi Service Fabric zawiera bezstanowa usługa o nazwie `fabric:/app/fooservice`, który uwidacznia wewnętrzny interfejsu API protokołu HTTP. Nazwa wystąpienia usługi jest dobrze znany i mogą być zakodowane bezpośrednio w ramach zasad usługi API Management przychodzące przetwarzanie. 

![Usługa Service Fabric za pomocą usługi Azure API Management omówienie topologii][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Wysyłaj ruch do usługi stanowej

Podobnie jak w scenariuszu usługę bezstanową, ruch mogą być przekazywane do wystąpienia usługi stanowej. W tym przypadku operacją usługi API Management zawiera zasadę przychodzące przetwarzanie za pomocą usługi Service Fabric zaplecza mapujący żądania do określonej partycji określonym *stanowa* wystąpienia usługi. Partycję, którą chcesz zamapować każde żądanie jest obliczana przy użyciu lambda — metoda, za pomocą dane wejściowe z przychodzącego żądania HTTP, takich jak wartość w polu Ścieżka adresu URL. Zasady można skonfigurować do wysyłania żądań do podstawową replikę lub replika losowych operacji odczytu.

#### <a name="example"></a>Przykład

W poniższym scenariuszu aplikacji usługi Service Fabric zawiera partycjonowane stanowej usługi o nazwie `fabric:/app/userservice` który uwidacznia wewnętrzny interfejsu API protokołu HTTP. Nazwa wystąpienia usługi jest dobrze znany i mogą być zakodowane bezpośrednio w ramach zasad usługi API Management przychodzące przetwarzanie.  

Usługa jest podzielona na partycje przy użyciu schematu partycji Int64 z dwóch partycji oraz zakres kluczy, która obejmuje `Int64.MinValue` do `Int64.MaxValue`. Zasad zaplecza oblicza klucza partycji, w tym zakresie, konwertując `id` wartość podana w ścieżce adresu URL żądania do 64-bitową liczbę całkowitą, mimo że dowolny algorytm może służyć w tym miejscu można obliczyć klucza partycji. 

![Usługa Service Fabric za pomocą usługi Azure API Management omówienie topologii][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Wysyłaj ruch do wielu usług bezstanowych

W bardziej zaawansowanych scenariuszy można zdefiniować operacji usługi API Management, która mapuje żądania do więcej niż jedno wystąpienie usługi. W takim przypadku każda operacja zawiera zasady, które mapuje żądania do konkretnego wystąpienia usługi na podstawie wartości z przychodzącego żądania HTTP, takie jak adres URL ścieżki lub ciągu zapytania, a w przypadku usług stanowych partycji w ramach wystąpienia usługi. 

Aby to osiągnąć, operacji usługi API Management zawiera zasadę przychodzące przetwarzanie za pomocą usługi Service Fabric zaplecza mapujący wystąpienie usługi bezstanowej usługi Service Fabric zaplecza na podstawie wartości pobierane z przychodzącego żądania HTTP. Żądania do wystąpienia usługi są wysyłane do losowych repliki wystąpienia usługi.

#### <a name="example"></a>Przykład

W tym przykładzie Usługa bezstanowa tworzone jest nowe wystąpienie dla każdego użytkownika aplikacji o nazwie generowanych dynamicznie, korzystając z następującego wzoru:
 
- `fabric:/app/users/<username>`

  Każda usługa ma unikatową nazwę, ale nazwy nie są znane z góry ponieważ usługi są tworzone w odpowiedzi na użytkownika lub administratora danych wejściowych i dlatego nie może być ustalone w zasady usługi APIM lub reguły routingu. Zamiast tego, nazwę usługi, do którego należy wysłać żądanie jest generowany w definicji zasad zaplecza z `name` wartość podana w ścieżce adresu URL żądania. Na przykład:

  - Żądanie `/api/users/foo` jest kierowany do wystąpienia usługi `fabric:/app/users/foo`
  - Żądanie `/api/users/bar` jest kierowany do wystąpienia usługi `fabric:/app/users/bar`

![Usługa Service Fabric za pomocą usługi Azure API Management omówienie topologii][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Wysyłaj ruch do wielu usług stanowych

Podobnie jak w przykładzie usługę bezstanową, operacji usługi API Management można mapować żądania do więcej niż jedną **stanowa** wystąpienie usługi, w którym to przypadku należy również może być konieczne wykonanie rozpoznawanie partycji dla każdego wystąpienia usługi stanowej.

Aby to osiągnąć, operacji usługi API Management zawiera zasadę przychodzące przetwarzanie za pomocą usługi Service Fabric zaplecza mapujący do wystąpienia usługi stanowej w usłudze Service Fabric zaplecza na podstawie wartości pobierane z przychodzącego żądania HTTP. Oprócz mapowania żądania do konkretnego wystąpienia usługi, żądanie również mogą być mapowane na określoną partycję w wystąpieniu usługi i opcjonalnie repliką podstawową lub losowych repliki pomocniczej w partycji.

#### <a name="example"></a>Przykład

W tym przykładzie stanowej usługi tworzone jest nowe wystąpienie dla każdego użytkownika aplikacji o nazwie generowanych dynamicznie, korzystając z następującego wzoru:
 
- `fabric:/app/users/<username>`

  Każda usługa ma unikatową nazwę, ale nazwy nie są znane z góry ponieważ usługi są tworzone w odpowiedzi na użytkownika lub administratora danych wejściowych i dlatego nie może być ustalone w zasady usługi APIM lub reguły routingu. Zamiast tego, nazwę usługi, do którego należy wysłać żądanie jest generowany w definicji zasad zaplecza z `name` podać wartość ścieżki adresu URL żądania. Na przykład:

  - Żądanie `/api/users/foo` jest kierowany do wystąpienia usługi `fabric:/app/users/foo`
  - Żądanie `/api/users/bar` jest kierowany do wystąpienia usługi `fabric:/app/users/bar`

Każde wystąpienie usługi są również podzielone na partycje przy użyciu schematu partycji Int64 z dwóch partycji oraz zakres kluczy, która obejmuje `Int64.MinValue` do `Int64.MaxValue`. Zasad zaplecza oblicza klucza partycji, w tym zakresie, konwertując `id` wartość podana w ścieżce adresu URL żądania do 64-bitową liczbę całkowitą, mimo że dowolny algorytm może służyć w tym miejscu można obliczyć klucza partycji. 

![Usługa Service Fabric za pomocą usługi Azure API Management omówienie topologii][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Kolejne kroki

Postępuj zgodnie z [samouczek](service-fabric-tutorial-deploy-api-management.md) skonfigurować swój pierwszy klaster usługi Service Fabric za pomocą usługi API Management i przepływ żądań za pomocą usługi API Management do usług.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png