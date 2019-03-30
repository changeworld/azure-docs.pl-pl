---
title: Scenariusze i projektowanie aplikacji | Dokumentacja firmy Microsoft
description: Omówienie kategorie aplikacji w chmurze w usłudze Service Fabric. W tym artykule omówiono projektowania aplikacji, która używa usługi stanowe i bezstanowe.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 3a8ca6ea-b8e9-4bc3-9e20-262437d2528e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/02/2017
ms.author: atsenthi
ms.openlocfilehash: c9b2f9ac131e71b7c6b37ed85568adc0c3978dc2
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668249"
---
# <a name="service-fabric-application-scenarios"></a>Scenariusze aplikacji usługi Service Fabric
Usługa Azure Service Fabric oferuje niezawodne i elastyczne platformy, która pozwala pisać i uruchamiać wiele rodzajów aplikacji i usług biznesowych. Te aplikacje i mikrousług może być bezstanowe lub stanowe i są one równoważenia zasobów dla maszyn wirtualnych, aby zmaksymalizować wydajność. Unikatowa Architektura usługi Service Fabric można wykonywać w pobliżu analizy danych w czasie rzeczywistym, obliczenia w pamięci, równoległych transakcji i przetwarzania zdarzeń w aplikacjach. Możesz można łatwo skalować aplikacje w górę lub w dół (naprawdę przychodzący lub wychodzący), w zależności od zmieniających wymagań dotyczących zasobów.

Platformy usługi Service Fabric na platformie Azure jest idealny dla następujących kategorii aplikacji:

* **Usługi o wysokiej dostępności**: Usługi Service Fabric udostępniają szybkiego trybu failover, tworząc wiele replik pomocniczych usługi. Jeśli węzeł, proces lub poszczególnych usług ulegnie awarii z powodu sprzętu lub inna awaria, jednej z replik pomocniczych zostanie podwyższony do repliki podstawowej przy minimalnej utracie usługi.
* **Skalowalnych usług**: Poszczególne usługi można podzielić na partycje, pozwalając na stanie, aby być skalowana w poziomie w klastrze. Ponadto poszczególne usługi mogą być tworzone lub usuwane na bieżąco. Usługi mogą szybkie i łatwe skalowanie od kilku wystąpień w kilku węzłów do tysięcy wystąpień w wielu węzłach, a następnie przeskalować w pionie, w zależności od potrzeb zasobów. Usługa Service Fabric umożliwia tworzenie tych usług oraz zarządzanie nimi ich pełne cykle życia.
* **Obliczeń na danych Niestatyczne**: Usługa Service Fabric umożliwia dane kompilacji, we/wy i intensywnie korzystających z aplikacji stanowych obliczeń. Usługa Service Fabric umożliwia kolokacji przetwarzania (obliczenia) i danych w aplikacjach. Zazwyczaj, gdy aplikacja wymaga dostępu do danych, istnieje opóźnienie sieci skojarzonej z warstwą pamięci podręcznej lub magazyn danych zewnętrznych. Za pomocą usługi Service Fabric stanowe tego opóźnienia została pominięta, włączanie wydajniej operacji odczytu i zapisu. Na przykład załóżmy, że masz aplikację, która wykonuje niemal wybrane zalecenie w czasie rzeczywistym klienci korzystający z wymogiem czas błądzenia w mniej niż 100 MS. Cechy opóźnienia i wydajność usługi Service Fabric (w której zlokalizowana obliczeń wybór zalecenie przy użyciu danych i reguły) zawiera interaktywnych doświadczenia użytkownika w porównaniu z modelem standardowej implementacji programu konieczności pobrania niezbędnych danych z magazynu zdalnego.  
* **Oparte na sesji aplikacji interaktywnych**: Usługa Service Fabric jest przydatne, jeśli aplikacje, takie jak gier online lub wiadomości błyskawicznych, wymagają małych opóźnień operacji odczytu i zapisu. Usługa Service Fabric umożliwia tworzenie tych aplikacji interaktywnych, stanowa bez tworzenia oddzielnego magazynu lub osobnej pamięci podręcznej, co jest wymagane dla aplikacji bezstanowych. (Zwiększa to opóźnienie i potencjalnie wprowadza problemy ze spójnością.).
* **Analiza danych i przepływów pracy**: Włącz aplikacje, które może niezawodnie przetwarzać strumienie danych lub zdarzenia, szybkie odczyty i zapisy usługi Service Fabric. Usługa Service Fabric umożliwia także aplikacje, które opisują potoków przetwarzania, w którym wyniki musi być niezawodne i przeszły do kolejnego etapu przetwarzania bez utraty. Obejmują one transakcyjne i finansowych systemów, gdzie gwarancje spójności i obliczanie danych są niezbędne.
* **Gromadzenie, przetwarzanie i Internet rzeczy danych**: Ponieważ usługi Service Fabric obsługuje dużą skalę oraz małe opóźnienia za pośrednictwem jego usług stanowych, to idealne rozwiązanie do przetwarzania danych na milionach urządzeń których znajdują się dane urządzenie i obliczeń.
Zaobserwowaliśmy wielu klientów, którzy mają wbudowane systemy IoT za pomocą usługi Service Fabric w tym [BMW](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/24/service-fabric-customer-profile-bmw-technology-corporation/), [Schneider Electric](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/05/service-fabric-customer-profile-schneider-electric/) i [siatki systemów](https://blogs.msdn.microsoft.com/azureservicefabric/2016/06/20/service-fabric-customer-profile-mesh-systems/).

## <a name="application-design-case-studies"></a>Analizy przypadków projektowania aplikacji
Liczba przypadków, pokazujący, jak Usługa Service Fabric jest używana do projektowania aplikacji są publikowane w [blog zespołu usługi Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/) i [lokacji rozwiązań mikrousług](https://azure.microsoft.com/solutions/microservice-applications/).

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>Projektowanie aplikacji złożonych z mikrousług stanowych i bezstanowych
Tworzenie aplikacji przy użyciu ról procesu roboczego usługi w chmurze platformy Azure jest przykładem usługi bezstanowej. Z kolei mikrousług stanowych utrzymywać ich autorytatywny stan poza żądaniem i odpowiedzią. Zapewnia to wysoką dostępność i spójność stanu za pomocą prostych interfejsów API, zawierające gwarancje transakcyjnych, wspierane przez replikację. Usługi stanowe usługi Service Fabric zdemokratyzuj proces wysoką dostępność, przeniesienie jej do wszystkich typów aplikacji, a nie tylko baz danych i inne magazyny danych. To jest naturalnemu postępowi. Aplikacje już zostały przeniesione z wysokiej dostępności do bazy danych NoSQL przy użyciu wyłącznie relacyjnych baz danych. Teraz same aplikacje mogą mieć ich stan "gorącymi" i danych zarządzanych w ramach ich dla dodatkowych wydajność bez obniżania oczekiwanego poziomu niezawodności, spójności i dostępności.

Podczas kompilowania aplikacji składającej się z mikrousług, zwykle mają kombinację aplikacji bezstanowych sieci web (ASP.NET, Node.js itp.) wywołanie do usług warstwy środkowej firm stanowe i bezstanowe, wszystkie wdrożone do tej samej usługi Service Fabric klaster przy użyciu polecenia usługi Service Fabric wdrożenia. Każda z tych usług jest niezależne w odniesieniu do użycia skalę, niezawodność i zasobów, znacznie poprawia elastyczność w rozwoju i zarządzanie cyklem życia.

Mikrousług stanowych uprościć projektów aplikacji, ponieważ one wyeliminować potrzebę dodatkowe kolejki i pamięci podręczne, które mają tradycyjnie wymagane, aby sprostać wymaganiom aplikacji bezstanowych czysto dostępności i opóźnień. Ponieważ usług stanowych są naturalnie o wysokiej dostępności i małego opóźnienia, oznacza to, że są mniej ruchomych części, aby zarządzać w aplikacji jako całości. Na poniższych diagramach przedstawiono różnice między projektowania aplikacji, która jest bezstanowy i jednego stanowych. Dzięki wykorzystaniu [usług Reliable Services](service-fabric-reliable-services-introduction.md) i [elementów Reliable Actors](service-fabric-reliable-actors-introduction.md) modeli programowania, usługi stanowe złożoność aplikacji możesz obniżyć wysokiej przepływności i małego opóźnienia.

## <a name="an-application-built-using-stateless-services"></a>Aplikacja skompilowana przy użyciu usługi bezstanowej
![Aplikacja korzystająca z usługi bezstanowej][Image1]

## <a name="an-application-built-using-stateful-services"></a>Aplikacja skompilowana przy użyciu usług stanowych
![Aplikacja korzystająca z usługi bezstanowej][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki

* Przeczytaj o [analizy przypadków klientów](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/)
* Dowiedz się więcej o [wzorce i scenariusze](service-fabric-patterns-and-scenarios.md)

* Wprowadzenie do tworzenia usług stanowych i bezstanowych za pomocą usługi Service Fabric [usług reliable services](service-fabric-reliable-services-quick-start.md) i [elementów reliable actors](service-fabric-reliable-actors-get-started.md) modeli programowania.
* Zobacz też następujące tematy:
  * [Informacje o mikrousługach](service-fabric-overview-microservices.md)
  * [Definiowanie stanu usługi i zarządzanie](service-fabric-concepts-state.md)
  * [Dostępność usługi Service Fabric](service-fabric-availability-services.md)
  * [Skalowanie usługi Service Fabric](service-fabric-concepts-scalability.md)
  * [Partycji usługi Service Fabric](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
