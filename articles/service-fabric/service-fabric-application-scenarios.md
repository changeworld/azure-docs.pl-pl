---
title: Scenariusze aplikacji i projektowanie
description: Omówienie kategorii aplikacji w chmurze w sieci szkieletowej usług. W tym artykule omówiono projekt aplikacji, który używa usług stanowych i bezstanowych.
ms.topic: conceptual
ms.date: 01/08/2020
ms.custom: sfrev
ms.openlocfilehash: 0aeb8ab2923915befdd11f96025687be3b3c4ff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76024743"
---
# <a name="service-fabric-application-scenarios"></a>Scenariusze aplikacji sieci szkieletowej usług

Usługa Azure Service Fabric oferuje niezawodną i elastyczną platformę, na której można pisać i uruchamiać wiele typów aplikacji i usług biznesowych. Te aplikacje i mikrousługi mogą być bezstanowe lub stanowe i są zrównoważone zasobami na maszynach wirtualnych, aby zmaksymalizować wydajność.

Unikatowa architektura sieci szkieletowej usług umożliwia wykonywanie analizy danych w czasie zbliżonym do rzeczywistego, obliczeń w pamięci, transakcji równoległych i przetwarzania zdarzeń w aplikacjach. Aplikacje można łatwo skalować w zależności od zmieniających się wymagań dotyczących zasobów.

Aby uzyskać wskazówki dotyczące projektowania aplikacji, przeczytaj [architekturę mikrousług w sieci szkieletowej usług Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) i [najważniejsze wskazówki dotyczące projektowania aplikacji przy użyciu sieci szkieletowej usług.](service-fabric-best-practices-applications.md)

Należy rozważyć użycie platformy sieci szkieletowej usług dla następujących typów aplikacji:

* **Gromadzenie, przetwarzanie danych i IoT:** Sieci szkieletowej usług obsługuje dużą skalę i ma małe opóźnienia za pośrednictwem swoich usług stanowych. Może pomóc w przetwarzaniu danych na milionach urządzeń, na których dane dla urządzenia i obliczeń są współlokowane.

    Klienci, którzy zbudowali usługi IoT za pomocą service fabric to [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)i [Mesh Systems](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Aplikacje interaktywne oparte na grach i sesjach:** sieci szkieletowej usług jest przydatne, jeśli aplikacja wymaga odczytów i zapisów o małym opóźnieniu, takich jak gry online lub wiadomości błyskawiczne. Sieć szkieletowa usług umożliwia tworzenie tych interaktywnych, stanowych aplikacji bez konieczności tworzenia oddzielnego magazynu lub pamięci podręcznej. Odwiedź [rozwiązania dla gier platformy Azure,](https://azure.microsoft.com/solutions/gaming/) aby uzyskać wskazówki dotyczące projektowania dotyczące korzystania z sieci [szkieletowej usług w usługach gier.](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)

    Klienci, którzy stworzyli usługi gier to [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) i [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). Klienci, którzy zbudowali interaktywne sesje obejmują [Honeywell z Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens).

* **Analiza danych i przetwarzanie przepływu pracy:** Aplikacje, które muszą niezawodnie przetwarzać zdarzenia lub strumienie danych korzystają ze zoptymalizowanych odczytów i zapisów w sieci szkieletowej usług. Usługa Sieci szkieletowej obsługuje również potoki przetwarzania aplikacji, gdzie wyniki muszą być wiarygodne i przekazywane do następnego etapu przetwarzania bez żadnych strat. Potoki te obejmują systemy transakcyjne i finansowe, w których spójność danych i gwarancje obliczeniowe są niezbędne.

    Klienci, którzy stworzyli usługi przepływu pracy biznesowej to [Zeiss Group,](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform) [Quorum Business Solutions](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric)i [Société General](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric).

* **Obliczanie danych:** Sieci szkieletowej usług umożliwia tworzenie aplikacji stanowych, które wykonują intensywne obliczenia danych. Sieci szkieletowej usług umożliwia kolokacji przetwarzania (obliczeń) i danych w aplikacjach. 

   Zwykle, gdy aplikacja wymaga dostępu do danych, opóźnienie sieci skojarzone z zewnętrzną pamięcią podręczną danych lub warstwą magazynu ogranicza czas obliczeń. Usługi stanowej sieci szkieletowej usług eliminują to opóźnienie, umożliwiając bardziej zoptymalizowane odczyty i zapisy.

   Rozważmy na przykład aplikację, która wykonuje niemal w czasie rzeczywistym wybór rekomendacji dla klientów, z wymaganiem czasu podróży w obie strony mniejsze niż 100 milisekund. Charakterystyka opóźnienia i wydajności usług sieci szkieletowej usług zapewniają użytkownikowi responsywne środowisko w porównaniu ze standardowym modelem implementacji konieczności pobierania niezbędnych danych z magazynu zdalnego. System jest bardziej responsywny, ponieważ obliczenia wyboru rekomendacji są współlokowane z danymi i regułami.

    Klienci, którzy zbudowali usługi obliczeniowe to [Solidsoft Reply](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) i [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura).

* **Usługi o wysokiej dostępności:** Usługa Service Fabric zapewnia szybkie przełączenie w błąd przez utworzenie wielu replik usługi pomocniczej. Jeśli węzeł, proces lub pojedyncza usługa ulegnie awarii z powodu sprzętu lub innego błędu, jedna z replik pomocniczych jest promowana do repliki podstawowej z minimalną utratą usługi.

* **Skalowalne usługi:** Poszczególne usługi mogą być podzielone na partycje, co pozwala na skalowanie stanu w poziomie w klastrze. Poszczególne usługi mogą być również tworzone i usuwane na bieżąco. Można skalować w poziomie usług z kilku wystąpień w kilku węzłach do tysięcy wystąpień w wielu węzłach, a następnie skalować je ponownie w razie potrzeby. Sieci szkieletowej usług można użyć do tworzenia tych usług i zarządzania ich pełne cykle życia.

## <a name="application-design-case-studies"></a>Studia przypadków projektowania aplikacji

Studia przypadków, które pokazują, jak sieci szkieletowej usług jest używany do [projektowania](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=2&so=story_publish_date%20desc) aplikacji są publikowane w historie klienta i [mikrousług w](https://azure.microsoft.com/solutions/microservice-applications/) witrynach platformy Azure.

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>Projektowanie aplikacji składających się z mikrousług bezstanowych i stanowych

Tworzenie aplikacji za pomocą ról procesu roboczego usług Azure Cloud Services jest przykładem usługi bezstanowej. Z drugiej strony stanowe mikrousługi zachowują ich stan autorytatywny poza żądanie i jego odpowiedzi. Ta funkcja zapewnia wysoką dostępność i spójność stanu za pośrednictwem prostych interfejsów API, które zapewniają gwarancje transakcyjne wspierane przez replikację.

Usługi stanowe w sieci szkieletowej usług przynoszą wysoką dostępność do wszystkich typów aplikacji, nie tylko baz danych i innych magazynów danych. Jest to naturalny postęp. Aplikacje zostały już przeniesione z korzystania z czysto relacyjnych baz danych dla wysokiej dostępności do baz danych NoSQL. Teraz same aplikacje mogą mieć ich "gorący" stan i dane zarządzane w nich dla dodatkowego wzrostu wydajności bez utraty niezawodności, spójności lub dostępności.

Podczas tworzenia aplikacji, które składają się z mikrousług, zazwyczaj masz kombinację bezstanowych aplikacji sieci web (takich jak ASP.NET i Node.js) wywoływania do bezstanowych i stanowych usług biznesowych warstwy środkowej. Wszystkie aplikacje i usługi są wdrażane w tym samym klastrze sieci szkieletowej usług za pomocą poleceń wdrażania sieci szkieletowej usług. Każda z tych usług jest niezależna pod względem skali, niezawodności i użycia zasobów. Ta niezależność poprawia elastyczność i elastyczność w rozwoju i zarządzaniu cyklem życia.

Mikrousługi stanowe upraszczają projekty aplikacji, ponieważ usuwają potrzebę dodatkowych kolejek i pamięci podręcznych, które tradycyjnie były wymagane do spełnienia wymagań dotyczących dostępności i opóźnień aplikacji czysto bezstanowych. Ponieważ usługi stanowe mają wysoką dostępność i małe opóźnienia, istnieje mniej szczegółów do zarządzania w aplikacji.

Na poniższych diagramach przedstawiono różnice między projektowaniem aplikacji, która jest bezstanowa i jeden, który jest stanowy. Korzystając z modeli programowania [niezawodne usługi](service-fabric-reliable-services-introduction.md) i [niezawodne podmioty,](service-fabric-reliable-actors-introduction.md) usługi stanowe zmniejszają złożoność aplikacji, osiągając jednocześnie wysoką przepustowość i małe opóźnienia.

Oto przykładowa aplikacja, która korzysta z ![usług bezstanowych: aplikacja, która korzysta z usług bezstanowych][Image1]

Oto przykładowa aplikacja, która korzysta z ![usług stanowych: aplikacja, która korzysta z usług stanowych][Image2]

## <a name="next-steps"></a>Następne kroki

* Rozpocznij tworzenie usług bezstanowych i stanowych za pomocą [niezawodnych usług](service-fabric-reliable-services-quick-start.md) sieci szkieletowej usług i modeli programowania [Reliable Actors.](service-fabric-reliable-actors-get-started.md)
* Odwiedź Centrum architektury platformy Azure, aby uzyskać wskazówki dotyczące [tworzenia mikrousług na platformie Azure.](https://docs.microsoft.com/azure/architecture/microservices/)
* Przejdź do [aplikacji sieci szkieletowej usługi Azure i najlepszych rozwiązań klastra,](service-fabric-best-practices-overview.md) aby uzyskać wskazówki dotyczące projektowania aplikacji.

* Zobacz też:
  * [Informacje o mikrousługach](service-fabric-overview-microservices.md)
  * [Definiowanie stanu usługi i zarządzanie nim](service-fabric-concepts-state.md)
  * [Dostępność usług](service-fabric-availability-services.md)
  * [Usługi skalowania](service-fabric-concepts-scalability.md)
  * [Usługi partycji](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.png
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.png
