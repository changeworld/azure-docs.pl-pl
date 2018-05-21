---
title: Sieć szkieletowa usług Azure z zarządzanie interfejsami API — omówienie | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera wprowadzenie do korzystania z usługi Azure API Management jako bramy dla poszczególnych aplikacji sieci szkieletowej usług.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: 6bf7ea90bb5351411984110fd8fb05c2f8cb0650
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Sieć szkieletowa usług z usługi Azure API Management — omówienie

Aplikacje w chmurze zwykle potrzebują bramy frontonu, aby udostępniać pojedynczy punkt danych przychodzących dla użytkowników, urządzeń lub innych aplikacji. W sieci szkieletowej usług, brama może być dowolnym usługi bezstanowej takich jak [aplikacji platformy ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), lub inna usługa przeznaczone dla ruch przychodzący, takich jak [usługi Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [Centrum IoT](https://docs.microsoft.com/azure/iot-hub/), lub [Azure API Management](https://docs.microsoft.com/azure/api-management/).

Ten artykuł zawiera wprowadzenie do korzystania z usługi Azure API Management jako bramy dla poszczególnych aplikacji sieci szkieletowej usług. Zarządzanie interfejsami API integruje się bezpośrednio z usługi Service Fabric, co umożliwia publikowanie interfejsów API za pomocą bogaty zestaw reguł routingu do usług sieci szkieletowej usług zaplecza. 

## <a name="architecture"></a>Architektura
Architektura usługi Service Fabric korzysta z aplikacji jednej strony sieci web, która nawiązuje połączeń HTTP do usługi zaplecza, które udostępniają interfejsy API protokołu HTTP. [Usługi Service Fabric — wprowadzenie przykładowej aplikacji](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) przedstawiono przykład tej architektury.

W tym scenariuszu usługi bezstanowej sieci web służy jako bramy w aplikacji sieci szkieletowej usług. Ta metoda wymaga zapisu usługi sieci web można żądania HTTP serwera proxy do usługi zaplecza, jak pokazano na poniższym diagramie:

![Sieć szkieletowa usług Azure API Management topologii Przegląd][sf-web-app-stateless-gateway]

Wzrostem aplikacji złożonością tak zrobić bram, które musi przedstawić interfejsu API przed wiele usług zaplecza. Azure API Management przeznaczony do obsługi złożonych interfejsów API za pomocą reguły routingu, kontroli dostępu, limitów szybkości, monitorowanie, rejestrowanie zdarzeń i buforowania odpowiedzi z minimalnym nakładu pracy. Zarządzanie interfejsami API Azure obsługuje odnajdywania usług sieci szkieletowej usług, partycji rozwiązania i wybór repliki inteligentnie Przekierowanie żądań bezpośrednio z usługami zaplecza w sieci szkieletowej usług, więc nie trzeba napisać własne bezstanowych bramy interfejsu API. 

W tym scenariuszu sieci web interfejsu użytkownika jest nadal obsługiwane za pośrednictwem usługi sieci web, natomiast HTTP interfejsu API są zarządzane i kierowane za pomocą usługi Azure API Management, jak pokazano na poniższym diagramie:

![Sieć szkieletowa usług Azure API Management topologii Przegląd][sf-apim-web-app]

## <a name="application-scenarios"></a>Scenariusze aplikacji

Usługi w sieci szkieletowej usług mogą być bezstanowe i stanowe i mogą być partycjonowane, przy użyciu jednej z trzech schematy: pojedyncze, int-64 zakresu i nazwane. Rozpoznanie punktu końcowego usługi wymaga identyfikacji określonej partycji wystąpienia określonej usługi. W trakcie rozwiązywania punktu końcowego usługi, zarówno nazwę wystąpienia usługi (na przykład `fabric:/myapp/myservice`) również muszą być określone określonej partycji usługi, z wyjątkiem pojedynczych partycji.

Zarządzanie interfejsami API Azure może służyć z dowolną kombinację usług bezstanowych, stanowych usług i wszelkie schemat partycjonowania.

## <a name="send-traffic-to-a-stateless-service"></a>Wysyłaj ruch do usługi bezstanowej

Ogólnie rzecz biorąc ruch jest kierowany do wystąpienia usługi bezstanowej. Aby to osiągnąć, operacja interfejsu API zarządzania zawiera zasady przetwarzania przychodzącego z sieci szkieletowej usług zaplecza mapujący do wystąpienia określonej usługi bezstanowej w sieci szkieletowej usług zaplecza. Żądania wysyłane do tej usługi są wysyłane do losowe repliki wystąpienia usługi bezstanowej.

#### <a name="example"></a>Przykład
W poniższym scenariuszu aplikacji usługi Service Fabric zawiera usługi bezstanowej o nazwie `fabric:/app/fooservice`, który uwidacznia wewnętrzny API protokołu HTTP. Nazwa wystąpienia usługi jest dobrze znanych i może być ustalony bezpośrednio w zasadach zarządzanie interfejsami API przetwarzania przychodzącego. 

![Sieć szkieletowa usług Azure API Management topologii Przegląd][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Wysyłaj ruch do usługi stanowej

Podobnie jak w scenariuszu usługi bezstanowej, ruchu mogą być przekazywane do wystąpienia usługi stanowej. W takim przypadku operacja interfejsu API zarządzania zawiera zasady przetwarzania przychodzącego z sieci szkieletowej usług zaplecza mapujący żądanie na określoną partycję określony *stateful* wystąpienie usługi. Partycja do mapowania każdego żądania jest obliczana przy użyciu metody lambda za pomocą niektórych danych wejściowych z przychodzącego żądania HTTP, takie jak wartość w polu Ścieżka adresu URL. Zasady można skonfigurować do wysyłania żądań tylko replika podstawowa lub losowych repliki dla operacji odczytu.

#### <a name="example"></a>Przykład

W poniższym scenariuszu aplikacji usługi Service Fabric zawiera podzielonym na partycje usługi stanowej o nazwie `fabric:/app/userservice` który uwidacznia wewnętrzny API protokołu HTTP. Nazwa wystąpienia usługi jest dobrze znanych i może być ustalony bezpośrednio w zasadach zarządzanie interfejsami API przetwarzania przychodzącego.  

Usługa jest podzielona na partycje przy użyciu schematu partycji Int64 z dwóch partycji i zakres klucza, który obejmuje `Int64.MinValue` do `Int64.MaxValue`. Zasady zaplecza oblicza klucza partycji w ramach tego zakresu, konwertując `id` wartość podana w ścieżce żądanie adresu URL do 64-bitową liczbę całkowitą, chociaż dowolny algorytm można używać w tym miejscu można obliczyć klucza partycji. 

![Sieć szkieletowa usług Azure API Management topologii Przegląd][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Wysyłaj ruch do wielu usług bezstanowych

W bardziej zaawansowanych scenariuszy można zdefiniować operacji interfejsu API zarządzania, która mapuje żądania do więcej niż jedno wystąpienie usługi. W przypadku każdej operacji zawiera zasady, która mapuje żądania do wystąpienia określonej usługi na podstawie wartości z przychodzącego żądania HTTP, takie jak ciąg ścieżki lub zapytanie adresu URL i w przypadku stanowego usług, partycji w obrębie wystąpienia usługi. 

Aby to osiągnąć, operacja interfejsu API zarządzania zawiera zasady przetwarzania przychodzącego z sieci szkieletowej usług zaplecza mapowana na wystąpieniu usługi bezstanowej w sieci szkieletowej usług zaplecza na podstawie wartości pobierane z przychodzącego żądania HTTP. Żądania do wystąpienia usługi są wysyłane do losowe repliki wystąpienia usługi.

#### <a name="example"></a>Przykład

W tym przykładzie nowego wystąpienia usługi bezstanowej jest tworzone dla poszczególnych użytkowników aplikacji nazwą dynamicznie generowanym, za pomocą następującej formuły:
 
 - `fabric:/app/users/<username>`

 Każda usługa ma unikatową nazwę, ale nazwy nie są znane początkowych ponieważ usług są tworzone w odpowiedzi na użytkownika lub administratora danych wejściowych, jak i w związku z tym nie może być ustalony do zasad APIM lub reguły routingu. Zamiast tego nazwy usługi, do którego należy wysłać żądanie jest generowana w definicji zasad zaplecza z `name` wartość podana w ścieżkę adresu URL żądania. Na przykład:

  - Żądanie `/api/users/foo` jest kierowany do wystąpienia usługi `fabric:/app/users/foo`
  - Żądanie `/api/users/bar` jest kierowany do wystąpienia usługi `fabric:/app/users/bar`

![Sieć szkieletowa usług Azure API Management topologii Przegląd][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Wysyłaj ruch do wielu usług stanowych

Podobnie jak w przykładzie usługi bezstanowej, operacja interfejsu API zarządzania można mapować żądania do więcej niż jeden **stateful** wystąpienie usługi, w którym to przypadku należy również może być konieczne rozpoznawać partycji dla każdego wystąpienia usługi stanowej.

Aby to osiągnąć, operacja interfejsu API zarządzania zawiera zasady przetwarzania przychodzącego z sieci szkieletowej usług zaplecza mapujący do wystąpienia usługi stanowej w sieci szkieletowej usług zaplecza na podstawie wartości pobierane z przychodzącego żądania HTTP. Oprócz mapowania żądania do wystąpienia określonej usługi, żądanie również mogą być mapowane na określoną partycję w wystąpieniu usługi i opcjonalnie repliką podstawową lub losowych repliki pomocniczej w partycji.

#### <a name="example"></a>Przykład

W tym przykładzie nowe wystąpienie usługi stanowej jest tworzone dla poszczególnych użytkowników aplikacji nazwą dynamicznie generowanym, za pomocą następującej formuły:
 
 - `fabric:/app/users/<username>`

 Każda usługa ma unikatową nazwę, ale nazwy nie są znane początkowych ponieważ usług są tworzone w odpowiedzi na użytkownika lub administratora danych wejściowych, jak i w związku z tym nie może być ustalony do zasad APIM lub reguły routingu. Zamiast tego nazwy usługi, do którego należy wysłać żądanie jest generowana w definicji zasad zaplecza z `name` wartość podana ścieżka adresu URL żądania. Na przykład:

  - Żądanie `/api/users/foo` jest kierowany do wystąpienia usługi `fabric:/app/users/foo`
  - Żądanie `/api/users/bar` jest kierowany do wystąpienia usługi `fabric:/app/users/bar`

Każde wystąpienie usługi jest również podzielona na partycje przy użyciu schematu partycji Int64 z dwóch partycji i zakres klucza, który obejmuje `Int64.MinValue` do `Int64.MaxValue`. Zasady zaplecza oblicza klucza partycji w ramach tego zakresu, konwertując `id` wartość podana w ścieżce żądanie adresu URL do 64-bitową liczbę całkowitą, chociaż dowolny algorytm można używać w tym miejscu można obliczyć klucza partycji. 

![Sieć szkieletowa usług Azure API Management topologii Przegląd][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Kolejne kroki

Postępuj zgodnie z [samouczek](service-fabric-tutorial-deploy-api-management.md) do skonfigurowania pierwszego klastra sieci szkieletowej usług za pomocą interfejsu API zarządzania i przepływ żądań za pośrednictwem interfejsu API zarządzania do usług.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png