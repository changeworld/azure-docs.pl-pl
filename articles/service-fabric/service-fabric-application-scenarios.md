---
title: Scenariusze aplikacji i projekt | Dokumentacja firmy Microsoft
description: Przegląd kategorie aplikacji w sieci szkieletowej usług w chmurze. W tym artykule omówiono projekt aplikacji, która korzysta z usług stanowe i bezstanowe.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 3a8ca6ea-b8e9-4bc3-9e20-262437d2528e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/02/2017
ms.author: mfussell
ms.openlocfilehash: c0a9b24704a91d6a6893937b4ee03765fb05f092
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207540"
---
# <a name="service-fabric-application-scenarios"></a>Scenariusze aplikacji sieci szkieletowej usług
Sieć szkieletowa usług Azure oferuje niezawodny i elastyczny platformę umożliwiającą zapis i uruchomić wiele typów usług i aplikacji biznesowych. Te aplikacje i mikrousług może być bezstanowe i stanowe i są równoważenia zasobów między maszynami wirtualnymi, aby zmaksymalizować wydajność. Unikatowa architektura sieci szkieletowej usług umożliwia przeprowadzanie analizy danych w czasie rzeczywistym, obliczeń w pamięci, równoległych transakcji i przetwarzania zdarzeń w aplikacjach w pobliżu. Można łatwo skalować aplikacji w górę lub w dół (naprawdę przychodzący lub wychodzący), w zależności od zmieniających się wymagań dotyczących zasobów.

Platforma Service Fabric w Azure jest idealnym rozwiązaniem w ramach następujących kategorii aplikacji:

* **Wysoką dostępność usług**: usługi sieci szkieletowej usług zapewniają szybki trybu failover, tworząc wiele replik pomocniczych usługi. Jeśli węzeł, proces lub pojedynczej usługi przestanie działać z powodu sprzętu lub inne niepowodzenia, jednej repliki pomocniczej jest podwyższany do repliki podstawowej przy minimalnej utracie usługi.
* **Skalowalnych usług**: poszczególnych usług mogą być partycjonowane, co pozwala na stanie, aby być skalowana w poziomie w klastrze. Ponadto poszczególnych usług można tworzyć i usunąć na bieżąco. Usługi można szybko i łatwo skalować w poziomie z kilku wystąpień na kilku węzłów do tysięcy wystąpień na wielu węzłach, a następnie wyświetlane w ponownie, w zależności od potrzeb zasobów. Sieć szkieletowa usług służy do tworzenia tych usług i zarządzania ich pełne cykle.
* **Obliczenia na danych Niestatyczne**: sieci szkieletowej usług umożliwia tworzenie danych, we/wy i obliczeniowe znacznym aplikacji stanowych. Sieć szkieletowa usług umożliwia kolokacji przetwarzania (obliczeń) i danych w aplikacjach. Zwykle, gdy aplikacja wymaga dostępu do danych, jest skojarzone z warstwą pamięci podręcznej lub magazynu danych zewnętrznych opóźnienia sieci. Za pomocą usług sieci szkieletowej usług stanowych tego opóźnienia została pominięta, włączanie więcej wydajności operacji odczytu i zapisu. Na przykład załóżmy, że masz aplikację, która wykonuje obok opcji zalecenia w czasie rzeczywistym dla klientów z wymaganiem czasu Rundy mniej niż 100 milisekund. Właściwości opóźnienia i wydajności usługi sieć szkieletowa usług (gdzie obliczenia wybór zalecenia są zawsze umieszczane przy użyciu danych i zasady) zapewnia obsługę reakcji użytkownika, w porównaniu z model standardowej implementacji konieczności pobrania niezbędnych danych z magazynu zdalnego.  
* **Oparte na sesji aplikacji interaktywnych**: usługi sieć szkieletowa jest przydatne w przypadku aplikacji, takich jak gier online lub wiadomości błyskawicznych, wymagają małych opóźnieniach odczyty i zapisy. Sieć szkieletowa usług umożliwia tworzenie tych aplikacji interaktywnych, stanowe bez konieczności tworzenia osobnych sklepie lub w pamięci podręcznej, co jest wymagane dla aplikacji bezstanowych. (Zwiększa to opóźnienie i potencjalnie wprowadza problemy niespójności.).
* **Analiza danych i przepływów pracy**: szybkie odczyty i zapisy sieci szkieletowej usług umożliwiają aplikacjom, które niezawodnie musi przetworzyć zdarzenia lub strumieni danych. Sieć szkieletowa usług umożliwia także aplikacje, które opisują potoków przetwarzania, w którym wyniki muszą być wiarygodne i przeszły do kolejnego etapu przetwarzania bez utraty. Obejmują one systemów transakcyjnych i finansowych, gdzie gwarancje spójności i obliczanie danych są istotne.
* **Zbieranie IoT i przetwarzania danych**: ponieważ sieci szkieletowej usług obsługi dużej skali oraz małe opóźnienia za pośrednictwem jego usługi stanowej, jest idealny dla przetwarzania danych na milionów urządzeń skutkującej znajdują się dane urządzenie i obliczenia.
Zaobserwowano kilku klientów, którzy mają wbudowane systemy IoT przy użyciu usługi Service Fabric w tym [BMW](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/24/service-fabric-customer-profile-bmw-technology-corporation/), [elektrycznego Schneider](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/05/service-fabric-customer-profile-schneider-electric/) i [siatki systemów](https://blogs.msdn.microsoft.com/azureservicefabric/2016/06/20/service-fabric-customer-profile-mesh-systems/).

## <a name="application-design-case-studies"></a>Analizy przypadków projektowania aplikacji
Liczba przypadków przedstawiający sposób sieci szkieletowej usług służy do projektowania aplikacji są publikowane w [blog zespołu usługi sieć szkieletowa](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/) i [mikrousług rozwiązań lokacji](https://azure.microsoft.com/solutions/microservice-applications/).

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>Projektowanie aplikacji składający się z mikrousług bezstanowe i stanowe
Kompilowanie aplikacji za pomocą usługi w chmurze Azure roli proces roboczy jest przykładem bezstanowego usługi. Z kolei mikrousług stanowe Obsługa stanu autorytatywne poza żądania i odpowiedzi. Zapewnia wysoką dostępność i spójności stanu za pośrednictwem prostych interfejsów API, który transakcyjne zagwarantować kopii za pomocą replikacji. Usługa sieć szkieletowa usług stanowych democratize wysokiej dostępności w jednym dla wszystkich typów aplikacji, a nie tylko baz danych i innych magazynów danych. Jest to fizyczne postępu. Aplikacje zostały już przeniesione z wysokiej dostępności do bazy danych NoSQL przy użyciu całkowicie relacyjnych baz danych. Teraz same aplikacje mogą mieć ich stanu "gorących" i danych zarządzanych w nich dla wzrost wydajności dodatkowe bez ograniczania niezawodności, spójność i dostępności.

Podczas tworzenia aplikacji składający się z mikrousług, zazwyczaj mają kombinację aplikacji bezstanowych sieci web (ASP.NET, Node.js, itp.) wywołanie do usług warstwy środkowej bezstanowe i stanowe biznesowych, wszystkich wdrożonych w tej samej sieci szkieletowej usług klastra przy użyciu poleceń wdrażania w sieci szkieletowej usług. Każdy z tych usług jest niezależna w odniesieniu do użycia skalowalności, niezawodności i zasobów, znacznie poprawia elastyczność w zarządzaniu rozwoju i cyklem życia.

Stanowe mikrousług uprościć projektów aplikacji, ponieważ będą oni mogli usunąć potrzebę dodatkowe kolejki i pamięci podręczne tradycyjnie wymagających spełnić wymagań dostępności i opóźnienia czysto bezstanowych aplikacji. Ponieważ usługi stanowej naturalnie wysokiej dostępności i małego opóźnienia, oznacza to, że są mniej części ruchu do zarządzania w aplikacji jako całość. Na poniższych diagramach przedstawiono różnice między projektowaniu aplikacji bezstanowych, który jest obiektem stanowym. Dzięki wykorzystaniu [niezawodne usługi](service-fabric-reliable-services-introduction.md) i [Reliable Actors](service-fabric-reliable-actors-introduction.md) modele programowania usług stanowych uproszczeniu aplikacji uzyskanie wysokiej przepływności i małe opóźnienia.

## <a name="an-application-built-using-stateless-services"></a>Aplikacji utworzonej przy użyciu usług bezstanowych
![Przy użyciu usługi bezstanowej aplikacji][Image1]

## <a name="an-application-built-using-stateful-services"></a>Aplikacji utworzony za pomocą usługi stanowej
![Przy użyciu usługi bezstanowej aplikacji][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki

* Nasłuchiwanie na [analizy przypadków](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=qDJnf86yC_5206218965
)
* Przeczytaj informacje o [analizy przypadków](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/)
* Dowiedz się więcej o [wzorców i scenariusze](service-fabric-patterns-and-scenarios.md)

* Rozpocząć tworzenie usług bezstanowych i stanowych z sieci szkieletowej usług [niezawodne usługi](service-fabric-reliable-services-quick-start.md) i [niezawodnej podmiotów](service-fabric-reliable-actors-get-started.md) modele programowania.
* Zobacz też poniższe tematy:
  * [Podaj informacje o mikrousług](service-fabric-overview-microservices.md)
  * [Definiowanie i zarządzanie nimi stanu usługi](service-fabric-concepts-state.md)
  * [Dostępność usług sieci szkieletowej usług](service-fabric-availability-services.md)
  * [Skaluj usługi sieci szkieletowej usług](service-fabric-concepts-scalability.md)
  * [Partycji usługi sieci szkieletowej usług](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
