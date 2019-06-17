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
ms.openlocfilehash: 6563d6e7c454f44e1a70d725191e56d3f90315c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052603"
---
# <a name="service-fabric-application-scenarios"></a>Scenariusze aplikacji usługi Service Fabric
Usługa Azure Service Fabric oferuje niezawodne i elastyczna platforma, gdzie możesz napisać i uruchomić wiele rodzajów aplikacji i usług biznesowych. Te aplikacje i mikrousług może być bezstanowe lub stanowe i są one równoważenia zasobów dla maszyn wirtualnych, aby zmaksymalizować wydajność. 

Unikatowa Architektura usługi Service Fabric można wykonywać w pobliżu analizy danych w czasie rzeczywistym, obliczenia w pamięci, równoległych transakcji i przetwarzania zdarzeń w aplikacjach. Możesz można łatwo skalować aplikacje w górę lub w dół (naprawdę przychodzący lub wychodzący), w zależności od zmieniających wymagań dotyczących zasobów.

Aby uzyskać wskazówki dotyczące projektowania dotyczących tworzenia aplikacji, przeczytaj [architektury Mikrousług w usłudze Azure Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) i [najlepsze rozwiązania dotyczące projektowania aplikacji przy użyciu usługi Service Fabric](service-fabric-best-practices-applications.md).

Należy rozważyć zastosowanie platformy usługi Service Fabric dla następujących typów aplikacji:

* **Zbieranie danych, przetwarzanie i IoT**: Usługa Service Fabric obsługuje dużej skali i ma małymi opóźnieniami za pośrednictwem jego usług stanowych. Może to pomóc przetwarzania danych na milionach urządzeń, których wspólnie przechowywane są dane urządzenie i obliczeń.

    Klienci, którzy mają wbudowane usług IoT za pomocą usługi Service Fabric to [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [ Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric), i [siatki systemów](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Gry i oparte na sesji aplikacji interaktywnych**: Usługa Service Fabric jest przydatne, jeśli aplikacja wymaga małe opóźnienia odczytu i zapisu, takie jak gier online lub wiadomości błyskawicznych. Usługa Service Fabric umożliwia tworzenie tych aplikacji interaktywnych, stanowa, bez konieczności tworzenia oddzielnego magazynu lub osobnej pamięci podręcznej. Odwiedź stronę [rozwiązań usługi Azure gaming](https://azure.microsoft.com/solutions/gaming/) dla wskazówki dotyczące projektowania w [przy użyciu usługi Service Fabric w ramach usług gier](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf).

    Klienci, którzy mają wbudowane usług gier obejmują [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) i [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). Klienci, którzy utworzone interaktywnych sesji to [Honeywell za pomocą urządzenia Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens).

* **Analiza danych i przetwarzania przepływu pracy**: Aplikacje, które może niezawodnie przetwarzać zdarzenia lub strumienie danych korzyści ze zoptymalizowanego odczyty i zapisy w usłudze Service Fabric. Usługa Service Fabric obsługuje także potoków przetwarzania aplikacji, w którym wyniki musi być niezawodne i przeszły do kolejnego etapu przetwarzania bez utraty. Te potoki obejmują systemy transakcyjne i finansowych, gdzie gwarancje spójności i obliczanie danych są niezbędne.

    Klienci, którzy mają wbudowane usług przepływu pracy firmy obejmują [grupy Zeiss](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform), [rozwiązań biznesowych kworum](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric), i [ogólne Société](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric).

* **Obliczeń na danych**: Usługa Service Fabric umożliwia tworzenie aplikacji stanowych, które wykonują obliczenia intensywnie korzystających z danych. Usługa Service Fabric umożliwia wspólnej przetwarzania (obliczenia) i danych w aplikacjach. 

   Zazwyczaj, gdy aplikacja wymaga dostępu do danych, opóźnienie sieci skojarzonej z warstwą pamięci podręcznej lub magazyn danych zewnętrznych ogranicza czas obliczeń. Stanowe usługi Service Fabric wyeliminować ten czas oczekiwania, włączenie bardziej zoptymalizowane pod kątem operacji odczytu i zapisu. 
   
   Na przykład rozważmy aplikację, która wykonuje niemal wybrane zalecenie w czasie rzeczywistym klienci korzystający z wymogiem czas błądzenia w mniej niż 100 MS. Cechy opóźnienia i wydajność usługi Service Fabric zapewnia interaktywnych doświadczenia z użytkownikiem w porównaniu z modelem standardowej implementacji o konieczności pobrania niezbędnych danych z magazynu zdalnego. System jest bardziej reaktywny, ponieważ obliczenie wybór zalecenie jest wspólnie przechowywane przy użyciu danych i reguł.

    Klienci, którzy mają wbudowane usług obliczeń obejmują [odpowiedzi Solidsoft](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) i [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura).

* **Usługi o wysokiej dostępności**: Usługa Service Fabric udostępnia szybkiego trybu failover, tworząc wiele replik pomocniczych usługi. Jeśli węzeł, proces lub poszczególnych usług ulegnie awarii z powodu sprzętu lub inna awaria, jednej z replik pomocniczych zostanie podwyższony do repliki podstawowej przy minimalnej utracie usługi.

* **Skalowalnych usług**: Poszczególne usługi można podzielić na partycje, pozwalając na stanie, aby być skalowana w poziomie w klastrze. Poszczególne usługi również mogą być tworzone lub usuwane na bieżąco. Skalowanie usług od kilku wystąpień w kilku węzłów do tysięcy wystąpień w wielu węzłach i skalować je w ponownie zgodnie z potrzebami. Usługa Service Fabric umożliwia tworzenie tych usług oraz zarządzanie nimi ich pełne cykle życia.

## <a name="application-design-case-studies"></a>Analizy przypadków projektowania aplikacji
Analizy przypadków, które pokazują, jak Usługa Service Fabric jest używana do projektowania aplikacji są publikowane w [historie](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/) i [Mikrousług na platformie Azure](https://azure.microsoft.com/solutions/microservice-applications/) witryn.

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>Projektowanie aplikacji składających się z mikrousług stanowych i bezstanowych
Tworzenie aplikacji za pomocą usług Azure Cloud Services role procesów roboczych jest przykładem usługi bezstanowej. Z kolei mikrousług stanowych utrzymywać ich autorytatywny stan poza żądaniem i odpowiedzią. Ta funkcja zapewnia wysoką dostępność i spójność stanu za pomocą prostych interfejsów API, zawierające gwarancje transakcyjnych, wspierane przez replikację. 

Usług stanowych w usłudze Service Fabric Zapewnij wysoką dostępność dla wszystkich typów aplikacji, a nie tylko baz danych i inne magazyny danych. To jest naturalnemu postępowi. Aplikacje już zostały przeniesione z wysokiej dostępności do bazy danych NoSQL przy użyciu wyłącznie relacyjnych baz danych. Teraz same aplikacje mogą mieć ich stan "gorącymi" i danych zarządzanych w ramach ich dla dodatkowych wydajność bez obniżania oczekiwanego poziomu niezawodności, spójności i dostępności.

W przypadku tworzenia aplikacji, które składają się z mikrousług, zwykle mają kombinację bezstanowej internetowej aplikacji (np. ASP.NET i Node.js) wywoływania na usługi warstwy środkowej biznesowe stanowe i bezstanowe. Aplikacje i usługi są wdrażane w tym samym klastrze usługi Service Fabric za pomocą polecenia wdrożenia usługi Service Fabric. Każda z tych usług jest niezależne w odniesieniu do użycia skalę, niezawodność i zasobów. Ta niezależność zwiększa elastyczność i elastyczność w rozwoju i zarządzanie cyklem życia.

Mikrousług stanowych uprościć projektów aplikacji, ponieważ one wyeliminować potrzebę dodatkowe kolejki i pamięci podręczne, które mają tradycyjnie wymagane, aby sprostać wymaganiom aplikacji bezstanowych czysto dostępności i opóźnień. Usługi stanowe mają, wysokiej dostępności i małym opóźnieniu, dlatego są mniej szczegółów, aby zarządzać w aplikacji. 

Następujące diagramy przedstawiają różnice między projektowania aplikacji, która jest bezstanowy i jednego stanowych. Dzięki wykorzystaniu [usług Reliable Services](service-fabric-reliable-services-introduction.md) i [elementów Reliable Actors](service-fabric-reliable-actors-introduction.md) modeli programowania, usługi stanowe złożoność aplikacji możesz obniżyć wysokiej przepływności i małego opóźnienia.

Oto przykładowa aplikacja, która korzysta z usług bezstanowych: ![Aplikacja korzystająca z usługi bezstanowej][Image1]

Oto przykładowa aplikacja, która korzysta z usług stanowych: ![Aplikacja korzystająca z usługi bezstanowej][Image2]

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [wzorce i scenariusze](service-fabric-patterns-and-scenarios.md).

* Wprowadzenie do tworzenia usług stanowych i bezstanowych za pomocą usługi Service Fabric [usług Reliable Services](service-fabric-reliable-services-quick-start.md) i [elementów Reliable Actors](service-fabric-reliable-actors-get-started.md) modeli programowania.
* Odwiedź Centrum architektury platformy Azure, aby uzyskać wskazówki na [tworzenia mikrousług na platformie Azure](https://docs.microsoft.com/azure/architecture/microservices/).
* Przejdź do [najważniejsze wskazówki dotyczące aplikacji usługi Service Fabric i klastra](service-fabric-best-practices-overview.md) dla wskazówek dotyczących projektowania aplikacji.

* Zobacz też następujące tematy:
  * [Informacje o mikrousługach](service-fabric-overview-microservices.md)
  * [Definiowanie stanu usługi i zarządzanie](service-fabric-concepts-state.md)
  * [Dostępność usługi Service Fabric](service-fabric-availability-services.md)
  * [Skalowanie usługi Service Fabric](service-fabric-concepts-scalability.md)
  * [Partycji usługi Service Fabric](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
