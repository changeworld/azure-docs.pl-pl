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
ms.date: 4/24/2019
ms.author: atsenthi
ms.openlocfilehash: f7ad295eb30d257cd195ae02d5a5b1d85de76bda
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228503"
---
# <a name="service-fabric-application-scenarios"></a>Scenariusze aplikacji usługi Service Fabric
Usługa Azure Service Fabric oferuje niezawodne i elastyczne platformy, która pozwala pisać i uruchamiać wiele rodzajów aplikacji i usług biznesowych. Te aplikacje i mikrousług może być bezstanowe lub stanowe i są one równoważenia zasobów dla maszyn wirtualnych, aby zmaksymalizować wydajność. Unikatowa Architektura usługi Service Fabric można wykonywać w pobliżu analizy danych w czasie rzeczywistym, obliczenia w pamięci, równoległych transakcji i przetwarzania zdarzeń w aplikacjach. Możesz można łatwo skalować aplikacje w górę lub w dół (naprawdę przychodzący lub wychodzący), w zależności od zmieniających wymagań dotyczących zasobów.

Aby uzyskać wskazówki dotyczące projektowania dotyczących tworzenia aplikacji, przeczytaj [architektury mikrousług w usłudze Azure Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) i [najlepsze rozwiązania dotyczące projektowania aplikacji przy użyciu usługi Service Fabric](service-fabric-best-practices-applications.md)

Platforma Service Fabric jest idealny dla następujących typów aplikacji:

* **Zbieranie danych, przetwarzanie i IoT**: Ponieważ usługi Service Fabric obsługuje dużą skalę oraz małe opóźnienia za pośrednictwem jego usług stanowych, to idealne rozwiązanie do przetwarzania danych na milionach urządzeń gdzie wspólnie przechowywane są dane urządzenie i obliczeń.

    Klienci, którzy mają wbudowane usług IoT za pomocą usługi Service Fabric to [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [ Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric), i [siatki systemów](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Gry i oparte na sesji aplikacji interaktywnych**: Usługa Service Fabric jest idealnym rozwiązaniem, jeśli aplikacja wymaga małych opóźnień operacji odczytu i zapisu, takie jak gier online lub wiadomości błyskawicznych. Usługa Service Fabric umożliwia tworzenie tych aplikacji interaktywnych, stanowa, bez konieczności tworzenia oddzielnego magazynu lub osobnej pamięci podręcznej. Odwiedź stronę [rozwiązań usługi Azure gaming](https://azure.microsoft.com/solutions/gaming/) dla wskazówki dotyczące projektowania w [przy użyciu usługi Service Fabric w ramach usług gier](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)

    Klienci, którzy mają wbudowane usług gier obejmują [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) i [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). Klienci, którzy utworzone interaktywnych sesji to [Honeywell za pomocą urządzenia Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)

* **Analiza danych i przetwarzania przepływu pracy**: Aplikacje, które muszą niezawodnie przetwarzać strumienie danych lub zdarzenia również korzystać ze zoptymalizowanego odczyty i zapisy w usłudze Service Fabric. Ponadto Usługa Service Fabric obsługuje potoki przetwarzania aplikacji, w którym wyniki musi być niezawodne i przeszły do kolejnego etapu przetwarzania bez utraty. Obejmują one transakcyjne i finansowych systemów, gdzie gwarancje spójności i obliczanie danych są niezbędne.

    Klienci, którzy mają wbudowane usług przepływu pracy firmy obejmują [grupy Zeiss](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform) i [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)

* **Obliczeń na danych**: Usługa Service Fabric umożliwia tworzenie aplikacji stanowych intensywnie korzystających z obliczeń danych. Usługa Service Fabric umożliwia wspólnej lokalizacji danych i przetwarzania (obliczenia) w aplikacjach. Zazwyczaj, gdy aplikacja wymaga dostępu do danych, opóźnienie sieci skojarzonej z warstwą pamięci podręcznej lub magazyn danych zewnętrznych ogranicza czas obliczeń. Za pomocą usługi Service Fabric stanowe wyeliminowania tego opóźnienia, włączenie bardziej zoptymalizowane pod kątem operacji odczytu i zapisu. Na przykład rozważmy aplikację, która wykonuje niemal wybrane zalecenie w czasie rzeczywistym klienci korzystający z wymogiem czas błądzenia w mniej niż 100 MS. Cechy opóźnienia i wydajność usługi Service Fabric (gdzie obliczeń wybór zalecenie jest wspólnie przechowywane przy użyciu danych i reguły) zawiera interaktywnych doświadczenia użytkownika w porównaniu z modelem standardowej implementacji programu konieczności pobrania niezbędnych danych z magazynu zdalnego.

    Klienci, którzy mają wbudowane usług obliczeń obejmują [odpowiedzi Solidsoft](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) i [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)

* **Usługi o wysokiej dostępności**: Usługa Service Fabric udostępnia szybkiego trybu failover, tworząc wiele replik pomocniczych usługi. Jeśli węzeł, proces lub poszczególnych usług ulegnie awarii z powodu sprzętu lub inna awaria, jednej z replik pomocniczych zostanie podwyższony do repliki podstawowej przy minimalnej utracie usługi.

* **Skalowalnych usług**: Poszczególne usługi można podzielić na partycje, pozwalając na stanie, aby być skalowana w poziomie w klastrze. Ponadto poszczególne usługi mogą być tworzone lub usuwane na bieżąco. Usługi można szybko i łatwo być skalowana w poziomie z kilku wystąpień w węzłach kilku do tysięcy wystąpień w wielu węzłach, a następnie przeskalować w pionie, w zależności od potrzeb zasobów. Usługa Service Fabric umożliwia tworzenie tych usług oraz zarządzanie nimi ich pełne cykle życia.

## <a name="application-design-case-studies"></a>Analizy przypadków projektowania aplikacji
Liczba przypadków, pokazujący, jak Usługa Service Fabric jest używana do projektowania aplikacji są publikowane w [historie](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/) i [lokacji rozwiązań mikrousług](https://azure.microsoft.com/solutions/microservice-applications/).

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>Projektowanie aplikacji złożonych z mikrousług stanowych i bezstanowych
Tworzenie aplikacji przy użyciu ról procesu roboczego usługi w chmurze platformy Azure jest przykładem usługi bezstanowej. Z kolei mikrousług stanowych utrzymywać ich autorytatywny stan poza żądaniem i odpowiedzią. Ta funkcja zapewnia wysoką dostępność i spójność stanu za pomocą prostych interfejsów API, zawierające gwarancje transakcyjnych, wspierane przez replikację. Usługi stanowe usługi Service Fabric zdemokratyzuj proces wysoką dostępność, przeniesienie jej do wszystkich typów aplikacji, a nie tylko baz danych i inne magazyny danych. To jest naturalnemu postępowi. Aplikacje już zostały przeniesione z wysokiej dostępności do bazy danych NoSQL przy użyciu wyłącznie relacyjnych baz danych. Teraz same aplikacje mogą mieć ich stan "gorącymi" i danych zarządzanych w ramach ich dla dodatkowych wydajność bez obniżania oczekiwanego poziomu niezawodności, spójności i dostępności.

Podczas kompilowania aplikacji składającej się z mikrousług, zwykle mają kombinację aplikacji bezstanowych sieci web (ASP.NET, Node.js itp.) wywołanie do usług warstwy środkowej firm stanowe i bezstanowe, wszystkie wdrożone do tej samej usługi Service Fabric klaster przy użyciu polecenia usługi Service Fabric wdrożenia. Każda z tych usług jest niezależne w odniesieniu do skalowania, niezawodność i użycia zasobów, znacznie poprawia elastyczność i elastyczność w rozwoju i zarządzanie cyklem życia.

Mikrousług stanowych uprościć projektów aplikacji, ponieważ one wyeliminować potrzebę dodatkowe kolejki i pamięci podręczne, które mają tradycyjnie wymagane, aby sprostać wymaganiom aplikacji bezstanowych czysto dostępności i opóźnień. Ponieważ usług stanowych mieć naturalnie wysokiej dostępności i małym opóźnieniu, istnieją mniej ruchomych części, aby zarządzać w aplikacji jako całości. Na poniższych diagramach przedstawiono różnice między projektowania aplikacji, która jest bezstanowy i jednego stanowych. Dzięki wykorzystaniu [usług Reliable Services](service-fabric-reliable-services-introduction.md) i [elementów Reliable Actors](service-fabric-reliable-actors-introduction.md) modeli programowania, usługi stanowe złożoność aplikacji możesz obniżyć wysokiej przepływności i małego opóźnienia.

## <a name="an-application-built-using-stateless-services"></a>Aplikacja skompilowana przy użyciu usługi bezstanowej
![Aplikacja korzystająca z usługi bezstanowej][Image1]

## <a name="an-application-built-using-stateful-services"></a>Aplikacja skompilowana przy użyciu usług stanowych
![Aplikacja korzystająca z usługi bezstanowej][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki

* Przeczytaj o [analizy przypadków klientów](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc)
* Dowiedz się więcej o [wzorce i scenariusze](service-fabric-patterns-and-scenarios.md)

* Wprowadzenie do tworzenia usług stanowych i bezstanowych za pomocą usługi Service Fabric [usług reliable services](service-fabric-reliable-services-quick-start.md) i [elementów reliable actors](service-fabric-reliable-actors-get-started.md) modeli programowania.
* Odwiedź Centrum architektury platformy Azure, aby uzyskać wskazówki na [tworzenie mikrousług na platformie Azure](https://docs.microsoft.com/azure/architecture/microservices/)
* Przejdź do [najważniejsze wskazówki dotyczące aplikacji usługi Service Fabric i klastra](service-fabric-best-practices-overview.md) dla wskazówek dotyczących projektowania aplikacji.

* Zobacz też następujące tematy:
  * [Informacje o mikrousługach](service-fabric-overview-microservices.md)
  * [Definiowanie stanu usługi i zarządzanie](service-fabric-concepts-state.md)
  * [Dostępność usługi Service Fabric](service-fabric-availability-services.md)
  * [Skalowanie usługi Service Fabric](service-fabric-concepts-scalability.md)
  * [Partycji usługi Service Fabric](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
