---
title: Omówienie usługi Azure Digital Twins | Microsoft Docs
description: Dowiedz się więcej o usłudze Azure Digital Twins, rozwiązaniu IoT platformy Azure do analizy przestrzennej.
author: julieseto
ms.author: jseto
ms.date: 10/10/2018
ms.topic: overview
ms.service: digital-twins
services: digital-twins
manager: bertvanhoof
ms.custom: mvc
ms.openlocfilehash: 85b67683730c8352051b4d6b48f813576b11615b
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2018
ms.locfileid: "49322849"
---
# <a name="overview-of-azure-digital-twins"></a>Omówienie usługi Azure Digital Twins

Azure Digital Twins to usługa IoT platformy Azure, która umożliwia tworzenie kompleksowych modeli środowiska fizycznego. Pośród jej wielu funkcji warto wyróżnić możliwość tworzenia wykresów analizy przestrzennej do modelowania relacji i interakcji między osobami, obszarami i urządzeniami.

Usługa Azure Digital Twins pozwala na wykonywanie zapytań o dane z obszaru fizycznego, zamiast z wielu różnorodnych czujników. Ta usługa pomaga w konstruowaniu środowisk wielokrotnego użytku o wysokim stopniu skalowalności, rozpoznających przestrzeń, które łączą dane przesyłane strumieniowo z cyfrowego i fizycznego świata. Niezależnie od tego, czy określasz potrzeby konserwacyjne dla fabryki, analizujesz wymagania energetyczne w czasie rzeczywistym dla sieci elektrycznej, czy optymalizujesz użycie dostępnej przestrzeni w biurze, Twoje aplikacje są ulepszane i rozszerzane dzięki tym jednoznacznie istotnym funkcjom kontekstowym.

Usługę Azure Digital Twins można stosować do obsługi środowisk wszystkich typów, na przykład do magazynów, biur, szkół, szpitali, banków, stadionów, fabryk, parkingów, parków, sieci inteligentnych i miast. Warto rozważyć scenariusze takie jak śledzenie dziennych temperatur w kilku województwach, monitorowanie tras dronów, identyfikowanie autonomicznych pojazdów, analizowanie poziomów zajętości budynku lub znajdowanie najczęściej odwiedzanej kasy w sklepie. Niezależnie od tego, jaki jest Twój rzeczywisty scenariusz biznesowy, odpowiadające mu wystąpienie cyfrowe może zostać aprowizowane za pośrednictwem usługi Azure Digital Twins.

W poniższym klipie wideo bardziej szczegółowo omówiono usługę Digital Twins:

> [!VIDEO https://www.youtube.com/embed/TvN_NxpgyzQ]

## <a name="key-capabilities"></a>Najważniejsze możliwości

Kluczowe funkcje usługi Azure Digital Twins są następujące:

### <a name="spatial-intelligence-graph"></a>Wykres analizy przestrzennej

[*Wykres analizy przestrzennej*](./concepts-objectmodel-spatialgraph.md) lub inaczej *wykres przestrzenny* jest wirtualną reprezentacją środowiska fizycznego, która umożliwia modelowanie relacji między osobami, miejscami i urządzeniami.

Wyobraź sobie inteligentną aplikację narzędziową, która może obejmować kilka mierników użycia energii elektrycznej rozmieszczonych na pewnym obszarze. Aby precyzyjnie monitorować i przewidywać użycie energii elektrycznej i rozliczenia, firma inteligentnych usług komunalnych musi modelować każde urządzenie i czujnik za pomocą kontekstu dotyczącego lokalizacji i klienta, który będzie rozliczany. Wykres analizy przestrzennej umożliwia modelowanie złożonych relacji tego rodzaju.

### <a name="digital-twin-object-models"></a>Cyfrowe modele obiektów bliźniaczych

[Cyfrowe modele obiektów bliźniaczych](./concepts-objectmodel-spatialgraph.md) to wstępnie zdefiniowane protokoły urządzeń i schematy danych dostosowane do wymagań specyficznych dla danego obszaru, które przyspieszają i upraszczają tworzenie rozwiązań.

Na przykład aplikacja dotycząca zajętości pomieszczeń może korzystać z wstępnie zdefiniowanych typów obszarów, takich jak kampus, budynek, piętro, pokój itd.

### <a name="multiple-and-nested-tenants"></a>Wiele dzierżaw i dzierżawy zagnieżdżone

Możesz tworzyć rozwiązania, które są bezpiecznie skalowane i które mogą być używane w wielu dzierżawach. Możesz również tworzyć wiele poddzierżaw, z których można korzystać i do których można uzyskiwać dostęp w izolowany i bezpieczny sposób.

Na przykład aplikację wykorzystania przestrzeni można skonfigurować tak, aby izolowała dane dzierżawy od danych innych dzierżaw w jednym budynku, lub aby łączyła dane jednej dzierżawy z wieloma budynkami.

### <a name="advanced-compute-capabilities"></a>Zaawansowane możliwości obliczeniowe

Zaawansowane możliwości obliczeniowe, nazywane [funkcjami zdefiniowanymi przez użytkownika](./concepts-user-defined-functions.md), umożliwiają definiowanie i uruchamianie niestandardowych funkcji względem przychodzących [danych urządzenia](./concepts-device-ingress.md) w celu wysyłania sygnałów do wstępnie zdefiniowanych punktów końcowych. Ta możliwość usprawnia dostosowywanie i automatyzację zadań urządzenia.

Na przykład inteligentna aplikacja rolnicza mogłaby zawierać funkcję zdefiniowaną przez użytkownika do oceniania odczytów czujnika wilgotności gleby oraz prognozy pogody w celu wysyłania sygnałów dotyczących potrzeb nawodnienia.

### <a name="built-in-access-control"></a>Wbudowana kontrola dostępu

Funkcje zarządzania dostępem i tożsamościami, takie jak [kontrola dostępu oparta na rolach](./security-role-based-access-control.md) i [usługa Azure Active Directory](./security-authenticating-apis.md), umożliwiają bezpieczne kontrolowanie dostępu osób i urządzeń.

Na przykład aplikację do zarządzania obiektami można skonfigurować tak, aby umożliwić osobom zajmującym pomieszczenie ustawianie temperatury w określonym zakresie, a menedżerom obiektów ustawianie temperatury w dowolnym pomieszczeniu na dowolną wartość.

### <a name="ecosystem"></a>Ekosystem

Wystąpienie usługi Azure Digital Twins można połączyć z wieloma zaawansowanymi usługami platformy Azure, takimi jak: Azure Stream Analytics, sztuczna inteligencja, usługi magazynowe, Azure Maps, Microsoft Mixed Reality, Dynamics 365 lub Office 365.

Na przykład inteligentna aplikacja dla biurowca mogłaby używać usługi Azure Digital Twins do reprezentowania zespołów i urządzeń znajdujących się na wielu piętrach. Podczas gdy urządzenia przesyłałyby strumieniowo dane na żywo do aprowizowanego wystąpienia usługi Digital Twins, usługa Azure Stream Analytics mogłaby przetwarzać te dane, aby dostarczać kluczowe szczegółowe informacje umożliwiające podjęcie działań. Następnie dane te mogłyby być przechowywane w usłudze Azure Storage i konwertowane na format pliku możliwy do udostępnienia w celu dystrybucji w całej organizacji przy użyciu usługi Office 365.

## <a name="solutions-that-benefit-from-azure-digital-twins"></a>Rozwiązania, które korzystają z zalet usługi Azure Digital Twins

Usługa Azure Digital Twins przydaje się do reprezentowania świata fizycznego i jego wielu relacji, ponieważ upraszcza modelowanie IoT, przetwarzanie danych, obsługę zdarzeń i śledzenie urządzeń. Warto rozważyć chociaż kilka z poniższych scenariuszy obejmujących różnorodne branże, które skorzystałyby na jej zastosowaniu:

* Pokazywanie firmie zajmującej się zarządzaniem nieruchomościami poziomów zajętości pomieszczeń w czasie w celu zgromadzenia wniosków dotyczących najlepszych sposobów konfiguracji budynku biurowego.
* Wyzwalanie biletów zleceń służbowych dla aplikacji mobilnej, która kieruje wzywaniem ochrony, planowaniem pracy dozorcy i wszelkimi innymi usługami w centrum handlowym lub w obiekcie sportowym.
* Pokazywanie osobie pracującej w danym budynku, które pomieszczenia w tym budynku są zajęte w czasie rzeczywistym. Następnie ułatwienie tej osobie zarezerwowania przestrzeni roboczych odpowiadających jej potrzebom.
* Śledzenie, gdzie znajdują się zasoby w przestrzeni.
* Optymalizowanie ładowania pojazdów elektrycznych przez modelowanie preferencji użytkowników i ograniczeń sieci elektrycznej.

## <a name="azure-digital-twins-in-the-context-of-other-iot-services"></a>Usługa Azure Digital Twins w kontekście innych usług IoT

Usługa Azure Digital Twins używa usługi Azure IoT Hub w celu łączenia urządzeń IoT i czujników, które na bieżąco aktualizują dane ze świata fizycznego. Na poniższym diagramie pokazano, jak usługa Azure Digital Twins odnosi się do innych usług Azure IoT:

![Usługa Azure Digital Twins to usługa oparta na usłudze Azure IoT Hub](./media/overview/azure-digital-twins-in-iot-ecosystem.png)

Aby uzyskać bardziej szczegółowy opis pozostałej części ekosystemu IoT, zobacz [Technologie i rozwiązania Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-services-and-technologies).

## <a name="next-steps"></a>Następne kroki

Przejdź do krótkiego pokazu możliwości usługi Azure Digital Twins:

> [!div class="nextstepaction"]
> [Szybki start: wyszukiwanie dostępnych pomieszczeń przy użyciu usługi Azure Digital Twins](./quickstart-view-occupancy-dotnet.md)

Zagłęb się w szczegóły aplikacji do zarządzania obiektami, korzystającej z usługi Azure Digital Twins:

> [!div class="nextstepaction"]
> [Samouczek: wdrażanie usługi Azure Digital Twins i konfigurowanie wykresu przestrzennego](./tutorial-facilities-setup.md)

Zapoznaj się z kluczowymi pojęciami usługi Azure Digital Twins:

> [!div class="nextstepaction"]
> [Czym są modele obiektów i wykresy analizy przestrzennej w usłudze Digital Twins](./concepts-objectmodel-spatialgraph.md)
