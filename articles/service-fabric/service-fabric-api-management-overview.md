---
title: Omówienie sieci szkieletowej usługi Azure z usługą API Management
description: Ten artykuł jest wprowadzeniem do korzystania z usługi Azure API Management jako bramy do aplikacji sieci szkieletowej usług.
author: vturecek
ms.topic: conceptual
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: 2a331715d4e4538cfdda8d958ff549a81b627b79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028554"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Usługi Service Fabric i Azure API Management — omówienie

Aplikacje w chmurze zwykle potrzebują bramy frontonu, aby udostępniać pojedynczy punkt danych przychodzących dla użytkowników, urządzeń lub innych aplikacji. W sieci szkieletowej usług brama może być dowolną usługą bezstanową, taką jak [aplikacja ASP.NET Core,](service-fabric-reliable-services-communication-aspnetcore.md)lub inną usługą przeznaczoną do transferu danych przychodzących, taką jak [Event Hubs,](https://docs.microsoft.com/azure/event-hubs/) [IoT Hub](https://docs.microsoft.com/azure/iot-hub/)lub Azure API [Management](https://docs.microsoft.com/azure/api-management/).

Ten artykuł jest wprowadzeniem do korzystania z usługi Azure API Management jako bramy do aplikacji sieci szkieletowej usług. Usługa API Management integruje się bezpośrednio z siecią szkieletową usług, umożliwiając publikowanie interfejsów API z bogatym zestawem reguł routingu do usług szkieletowej usług zaplecza.

## <a name="availability"></a>Dostępność

> [!IMPORTANT]
> Ta funkcja jest dostępna w warstwach **Premium** i **Developer** zarządzania interfejsami API ze względu na wymaganą obsługę sieci wirtualnej.

## <a name="architecture"></a>Architektura

Wspólna architektura sieci szkieletowej usług używa jednostronicowej aplikacji sieci web, która wywołuje http do usług zaplecza, które ujawniają interfejsy API HTTP. Przykładowa [aplikacja wprowadzenie sieci szkieletowej usług](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) pokazuje przykład tej architektury.

W tym scenariuszu bezstanowej usługi sieci web służy jako brama do aplikacji sieci szkieletowej usług. Takie podejście wymaga zapisu usługi sieci web, która może proxy żądań HTTP do usług zaplecza, jak pokazano na poniższym diagramie:

![Omówienie topologii usługi Sieci szkieletowej z usługą Azure API Management][sf-web-app-stateless-gateway]

W miarę jak aplikacje stają się coraz bardziej złożone, podobnie jak bramy, które muszą przedstawiać interfejs API przed niezliczonymi usługami zaplecza. Usługa Azure API Management jest przeznaczona do obsługi złożonych interfejsów API z regułami routingu, kontroli dostępu, ograniczania szybkości, monitorowania, rejestrowania zdarzeń i buforowania odpowiedzi przy minimalnej pracy z Twojej strony. Usługa Azure API Management obsługuje odnajdowanie, rozpoznawanie partycji i wybór replik sieci szkieletowej usług Azure, aby inteligentnie kierować żądania bezpośrednio do usług zaplecza w sieci szkieletowej usług, dzięki czemu nie trzeba pisać własnej bezstanowej bramy interfejsu API. 

W tym scenariuszu interfejs użytkownika sieci web jest nadal obsługiwany za pośrednictwem usługi sieci web, podczas gdy wywołania interfejsu API HTTP są zarządzane i kierowane za pośrednictwem usługi Azure API Management, jak pokazano na poniższym diagramie:

![Omówienie topologii usługi Sieci szkieletowej z usługą Azure API Management][sf-apim-web-app]

## <a name="application-scenarios"></a>Scenariusze aplikacji

Usługi w sieci szkieletowej usług mogą być bezstanowe lub stanowe i mogą być podzielone na partycje przy użyciu jednego z trzech schematów: singleton, int-64 i nazwane. Rozpoznawanie punktu końcowego usługi wymaga zidentyfikowania określonej partycji określonego wystąpienia usługi. Podczas rozpoznawania punktu końcowego usługi, zarówno nazwę wystąpienia usługi `fabric:/myapp/myservice`(na przykład), jak również określonej partycji usługi muszą być określone, z wyjątkiem przypadku partycji singleton.

Usługa Azure API Management może być używana z dowolną kombinacją usług bezstanowych, usług stanowych i dowolnego schematu partycjonowania.

## <a name="send-traffic-to-a-stateless-service"></a>Wysyłanie ruchu do usługi bezstanowej

W najprostszym przypadku ruch jest przekazywał do wystąpienia usługi bezstanowej. Aby to osiągnąć, operacja zarządzania interfejsami API zawiera przychodzące zasady przetwarzania z zaplecza sieci szkieletowej usług, który mapuje do określonego wystąpienia usługi bezstanowej w zapleczu sieci szkieletowej usług. Żądania wysyłane do tej usługi są wysyłane do losowego wystąpienia usługi.

**Przykład**

W poniższym scenariuszu aplikacja sieci szkieletowej `fabric:/app/fooservice`usług zawiera usługę bezstanową o nazwie , która udostępnia wewnętrzny interfejs API HTTP. Nazwa wystąpienia usługi jest dobrze znana i może być zakodowane bezpośrednio w zasadach przetwarzania przychodzącego usługi API Management. 

![Omówienie topologii usługi Sieci szkieletowej z usługą Azure API Management][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Wysyłanie ruchu do usługi stanowej

Podobnie jak w przypadku usługi bezstanowej, ruch można przekazać do wystąpienia usługi stanowej. W takim przypadku operacja zarządzania interfejsami API zawiera przychodzące zasady przetwarzania z zaplecza sieci szkieletowej usług, który mapuje żądanie do określonej partycji określonego wystąpienia usługi *stanowej.* Partycja do mapowania każdego żądania jest obliczana za pomocą metody lambda przy użyciu niektórych danych wejściowych z przychodzącego żądania HTTP, takich jak wartość w ścieżce adresu URL. Zasady mogą być skonfigurowane do wysyłania żądań tylko do repliki podstawowej lub do repliki losowej dla operacji odczytu.

**Przykład**

W poniższym scenariuszu aplikacja sieci szkieletowej usług `fabric:/app/userservice` zawiera partycjonowane usługi stanowe o nazwie, która udostępnia wewnętrzny interfejs API HTTP. Nazwa wystąpienia usługi jest dobrze znana i może być zakodowane bezpośrednio w zasadach przetwarzania przychodzącego usługi API Management.  

Usługa jest podzielona na partycje przy użyciu schematu partycji Int64 z dwiema partycjami i zakresem klucza, `Int64.MinValue` który obejmuje . `Int64.MaxValue` Zasady zaplecza oblicza klucz partycji w tym `id` zakresie, konwertując wartość podana w ścieżce żądania adresu URL do 64-bitowej liczby całkowitej, chociaż dowolny algorytm może być użyty w tym miejscu do obliczenia klucza partycji. 

![Omówienie topologii usługi Sieci szkieletowej z usługą Azure API Management][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Wysyłanie ruchu do wielu usług bezstanowych

W bardziej zaawansowanych scenariuszach można zdefiniować operację zarządzania interfejsem API, która mapuje żądania do więcej niż jednego wystąpienia usługi. W takim przypadku każda operacja zawiera zasadę, która mapuje żądania do określonego wystąpienia usługi na podstawie wartości z przychodzącego żądania HTTP, takich jak ścieżka adresu URL lub ciąg zapytania, a w przypadku usług stanowych partycji w wystąpieniu usługi.

Aby to osiągnąć, operacja zarządzania interfejsami API zawiera przychodzące zasady przetwarzania z zaplecza sieci szkieletowej usług, który mapuje do wystąpienia usługi bezstanowej w sieci szkieletowej usługi zaplecza na podstawie wartości pobranych z przychodzącego żądania HTTP. Żądania do usługi są wysyłane do losowego wystąpienia usługi.

**Przykład**

W tym przykładzie dla każdego użytkownika aplikacji o dynamicznie wygenerowanej nazwie jest tworzone nowe wystąpienie usługi bezstanowej przy użyciu następującej formuły:

- `fabric:/app/users/<username>`

  Każda usługa ma unikatową nazwę, ale nazwy nie są znane z góry, ponieważ usługi są tworzone w odpowiedzi na dane wejściowe użytkownika lub administratora i dlatego nie mogą być zakodowane na miejscu w zasadach APIM lub regułach routingu. Zamiast tego nazwa usługi, do której ma wysłać żądanie, jest generowana w definicji zasad zaplecza `name` z wartości podanej w ścieżce żądania adresu URL. Przykład:

  - Żądanie `/api/users/foo` jest kierowane do wystąpienia usługi`fabric:/app/users/foo`
  - Żądanie `/api/users/bar` jest kierowane do wystąpienia usługi`fabric:/app/users/bar`

![Omówienie topologii usługi Sieci szkieletowej z usługą Azure API Management][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Wysyłanie ruchu do wielu usług stanowych

Podobnie jak w przykładzie usługi bezstanowej, operacja zarządzania interfejsem API może mapować żądania do więcej niż jednego wystąpienia usługi **stanowej,** w którym to przypadku może być również konieczne wykonanie rozpoznawania partycji dla każdego wystąpienia usługi stanowej.

Aby to osiągnąć, operacja zarządzania interfejsami API zawiera przychodzące zasady przetwarzania z zaplecza sieci szkieletowej usług, który mapuje do wystąpienia usługi stanowej w sieci szkieletowej usługi zaplecza na podstawie wartości pobranych z przychodzącego żądania HTTP. Oprócz mapowania żądania do określonego wystąpienia usługi, żądanie można również mapować na określoną partycję w wystąpieniu usługi i opcjonalnie do repliki podstawowej lub losowej repliki pomocniczej w partycji.

**Przykład**

W tym przykładzie dla każdego użytkownika aplikacji jest tworzone nowe wystąpienie usługi stanowej o dynamicznie generowanej nazwie przy użyciu następującej formuły:

- `fabric:/app/users/<username>`

  Każda usługa ma unikatową nazwę, ale nazwy nie są znane z góry, ponieważ usługi są tworzone w odpowiedzi na dane wejściowe użytkownika lub administratora i dlatego nie mogą być zakodowane na miejscu w zasadach APIM lub regułach routingu. Zamiast tego nazwa usługi, do której ma wysłać żądanie, jest generowana w definicji zasad zaplecza `name` z wartości podanej w ścieżce żądania adresu URL. Przykład:

  - Żądanie `/api/users/foo` jest kierowane do wystąpienia usługi`fabric:/app/users/foo`
  - Żądanie `/api/users/bar` jest kierowane do wystąpienia usługi`fabric:/app/users/bar`

Każde wystąpienie usługi jest również podzielony na partycje przy użyciu schematu partycji Int64 z dwiema partycjami i zakresem klucza, `Int64.MinValue` który obejmuje `Int64.MaxValue`. Zasady zaplecza oblicza klucz partycji w tym `id` zakresie, konwertując wartość podana w ścieżce żądania adresu URL do 64-bitowej liczby całkowitej, chociaż dowolny algorytm może być użyty w tym miejscu do obliczenia klucza partycji. 

![Omówienie topologii usługi Sieci szkieletowej z usługą Azure API Management][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z [samouczka,](service-fabric-tutorial-deploy-api-management.md) aby skonfigurować pierwszy klaster sieci szkieletowej usług z zarządzania interfejsami API i żądania przepływu za pośrednictwem zarządzania interfejsami API do usług.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png
