---
title: Zdalne monitorowanie omówienie akceleratora rozwiązań — Azure | Dokumentacja firmy Microsoft
description: Omówienie akceleratora rozwiązania monitorowania zdalnego.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: af09ea39f373d518d5600e3fa46adc378fd9236d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61442554"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Omówienie akceleratora rozwiązania do monitorowania zdalnego

Zdalne monitorowanie [akcelerator rozwiązań](../iot-accelerators/about-iot-accelerators.md) implementuje — kompleksowe rozwiązanie do monitorowania dla wielu maszyn w lokalizacjach zdalnych. Rozwiązanie to łączy najważniejsze usługi platformy Azure, aby umożliwić ogólną implementację scenariusza biznesowego. Można użyć rozwiązania jako punktu wyjścia dla własnej implementacji i [dostosować](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) go do konkretnych potrzeb biznesowych.

W tym artykule opisano przez niektóre kluczowe elementy rozwiązania monitorowania zdalnego, aby umożliwić Ci zrozumienie sposobu działania. Ta wiedza ułatwi Ci:

* Rozwiązywanie problemów w rozwiązaniu.
* Planowanie sposobu dostosowywania rozwiązania, aby spełniało Twoje wymagania.
* Projektowanie własnego rozwiązania IoT korzystającego z usług Azure.

Kod akceleratora rozwiązania monitorowania zdalnego jest dostępna w witrynie GitHub:

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>Architektura logiczna

Poniższy diagram przedstawia składniki logiczne nałożony na akceleratora rozwiązania monitorowania zdalnego [architektury IoT](../iot-fundamentals/iot-introduction.md):

![Architektura logiczna](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Dlaczego mikrousługi?

Powstała architektury chmury, ponieważ firma Microsoft wydała pierwszą akceleratorów rozwiązań. [Mikrousługi](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) mają została uznana sprawdzone praktyki do osiągnięcia skali i elastyczności bez obniżania oczekiwanego poziomu prędkość opracowywania. Kilka usług firmy Microsoft za pomocą tego wzorca architektury wewnętrznie doskonałą niezawodność i skalowalność wyników. Akceleratory zaktualizowane rozwiązanie umieścić te informacje w praktyce, dzięki czemu można również korzystać z nich.

> [!TIP]
> Aby dowiedzieć się więcej na temat architektury mikrousług, zobacz [Architektura aplikacji .NET](https://www.microsoft.com/net/learn/architecture) i [Mikrousług: Rewolucja w aplikacjach obsługiwana przez chmurę](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="device-connectivity"></a>Łączność urządzeń

Rozwiązanie obejmuje następujące składniki w części łączności urządzeń logiczną architekturę:

### <a name="real-devices"></a>Rzeczywistych urządzeń

Łączenie z rzeczywistych urządzeń, do rozwiązania. Możesz zaimplementować zachowanie symulowanego urządzenia za pomocą zestawów SDK urządzeń Azure IoT.

Możesz aprowizować rzeczywistych urządzeń z poziomu pulpitu nawigacyjnego w portalu rozwiązania.

### <a name="device-simulation-microservice"></a>Mikrousługi symulacji urządzenia

Rozwiązanie obejmuje [mikrousług symulacji urządzenia](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation) , pozwala na zarządzanie pulę symulowane urządzenia z portalu rozwiązania w celu przetestowania przepływu end-to-end w rozwiązaniu. Symulowane urządzenia:

* Generowania danych telemetrycznych z urządzenia do chmury.
* Odpowiadanie na wywołania metody chmury do urządzenia z usługi IoT Hub.

Mikrousługi udostępnia punkt końcowy usługi RESTful umożliwiające tworzenie, uruchamianie i zatrzymywanie symulacji. Każdy symulację składa się z zestawu wirtualnych urządzeń różnych typów, które wysyłają dane telemetryczne i odpowiadanie na wywołania metody.

Możesz aprowizować symulowane urządzenia z poziomu pulpitu nawigacyjnego w portalu rozwiązania.

### <a name="iot-hub"></a>Usługa IoT Hub

[Usługi IoT hub](../iot-hub/index.yml) pozyskuje dane telemetryczne wysyłane z rzeczywistych i symulowanych urządzeń do chmury. Usługa IoT hub udostępnia dane telemetryczne do usług zaplecza rozwiązania IoT dla przetwarzania.

Usługa IoT Hub w rozwiązaniu wykonuje ponadto następujące czynności:

* Obsługuje rejestr tożsamości, w którym są przechowywane identyfikatory i klucze uwierzytelniania wszystkich urządzeń, może łączyć się z portalu.
* Wywołuje metody na Twoich urządzeniach w imieniu akcelerator rozwiązań.
* Obsługuje bliźniacze reprezentacje wszystkich zarejestrowanych urządzeń. W bliźniaczej reprezentacji urządzenia są przechowywane wartości właściwości zgłaszanych przez urządzenie. W bliźniaczej reprezentacji urządzenia są także przechowywane żądane właściwości ustawione w portalu rozwiązania, które mają zostać pobrane przez urządzanie podczas nawiązywania następnego połączenia.
* Planuje zadania ustawiania właściwości dla wielu urządzeń lub wywoływania metod na wielu urządzeniach.

## <a name="data-processing-and-analytics"></a>Przetwarzanie danych i analiza

Rozwiązanie obejmuje następujące składniki przetwarzania danych i analizy część logiczną architekturę:

### <a name="iot-hub-manager-microservice"></a>Mikrousługi Menedżer usłudze IoT Hub

Rozwiązanie obejmuje [mikrousług Menedżer usłudze IoT Hub](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) do obsługi interakcji z Centrum IoT, takich jak:

* Tworzenie i zarządzanie urządzeniami IoT.
* Zarządzanie bliźniaczych reprezentacji urządzeń.
* Wywoływanie metody na urządzeniach.
* Zarządzanie poświadczeniami IoT.

Ta usługa jest również uruchamiane usługi IoT Hub zapytania, aby pobrać urządzeń należących do grup zdefiniowanych przez użytkownika.

Mikrousługi udostępnia punkt końcowy usługi RESTful do zarządzania urządzeniami i bliźniacze reprezentacje urządzeń, wywoływanie metod i uruchamianie zapytań usługi IoT Hub.

### <a name="device-telemetry-microservice"></a>Mikrousługi telemetrii urządzenia

[Mikrousług telemetrii urządzenia](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry) udostępnia punkt końcowy usługi RESTful dostęp do odczytu do danych telemetrycznych z urządzenia, przechowywane w usłudze Time Series Insights. Punktem końcowym RESTful umożliwia również wykonywanie operacji CRUD na zasady oraz odczytu i zapisu, aby uzyskać definicje alarmowego z magazynu.

### <a name="storage-adapter-microservice"></a>Mikrousługi adapter magazynu

[Mikrousług adapter magazynu](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter) zarządza pary klucz wartość, abstrakcyjność semantyki usługi storage i prezentowanie prosty interfejs do przechowywania danych o dowolnym formacie za pomocą usługi Azure Cosmos DB.

Wartości są uporządkowane w kolekcjach. Można pracować na osobne wartości lub pobrania całej kolekcji. Złożone struktury danych są serializowane przez klientów i zarządzane jako ładunek zwykłego tekstu.

Usługa zapewnia punkt końcowy usługi RESTful dla operacji CRUD na parach klucz wartość. Wartości

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Użyj wdrożenia akcelerator rozwiązań [usługi Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) do przechowywania zasad, alertów, ustawienia konfiguracji i wszystkie inne zimnego magazynu.

### <a name="azure-stream-analytics-manager-microservice"></a>Mikrousługi Menedżera usługi Azure Stream Analytics

[Mikrousług Menedżer usłudze Azure Stream Analytics](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager) zarządza zadaniami na platformie Azure Stream Analytics (ASA), wraz z ustawieniem ich konfiguracji, uruchamianie i zatrzymywanie je oraz monitorowanie ich stanu.

Zadanie ASA jest obsługiwana przez dwa zestawy danych referencyjnych. Jeden zestaw danych określa reguły i definiuje jedną grup urządzeń. Dane referencyjne reguły jest generowany na podstawie informacji o zarządzanych przez mikrousług telemetrii urządzenia. Mikrousługi Menedżer usłudze Azure Stream Analytics przekształca dane telemetryczne reguły w logikę przetwarzania strumieni.

Dane referencyjne grup urządzeń jest używany do identyfikowania grupy reguł do zastosowania do wiadomości przychodzących danych telemetrycznych. Grupy urządzeń są zarządzane przez mikrousług konfiguracji i używać zapytań bliźniaczych reprezentacji urządzeń w usłudze Azure IoT Hub.

Zadania usługi ASA dostarczać dane telemetryczne z podłączonych urządzeń do usługi Time Series Insights magazynu i analizy.

### <a name="azure-stream-analytics"></a>Usługa Azure Stream Analytics

[Usługa Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) to aparat przetwarzania zdarzeń, który można badać duże ilości danych przesyłanych strumieniowo z urządzeń.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[Usługa Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/) magazynów danych telemetrycznych z urządzeń połączonych z usługą akcelerator rozwiązań. Umożliwia ona także wizualizowanie i wykonywania zapytań względem danych telemetrycznych z urządzenia w rozwiązaniu internetowym interfejsie użytkownika.

> [!NOTE]
> Usługa Time Series Insights nie jest obecnie dostępna w chmurze Azure (Chiny). Nowych wdrożeń akceleratora zdalne monitorowanie rozwiązań w chmurze Azure (Chiny) używać usługi Cosmos DB do przechowywania wszystkich.

### <a name="configuration-microservice"></a>Mikrousługi konfiguracji

[Mikrousług konfiguracji](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) udostępnia punkt końcowy usługi RESTful dla operacji CRUD na grupy urządzeń, ustawieniach rozwiązania oraz ustawienia użytkownika w akcelerator rozwiązań. Działa z mikrousług adapter magazynu, który można utrwalić danych konfiguracji.

### <a name="authentication-and-authorization-microservice"></a>Uwierzytelnianie i autoryzacja w mikrousługach

[Uwierzytelniania i autoryzacji w mikrousługach](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) zarządza użytkownikom uprawnień do dostępu do akceleratora rozwiązań. Zarządzanie użytkownikami można zrobić za pomocą dowolnego dostawcy usługi tożsamości, która obsługuje [OpenId Connect](https://openid.net/connect/).

### <a name="azure-active-directory"></a>Usługa Azure Active Directory

Użyj wdrożenia akcelerator rozwiązań [usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) jako dostawcy OpenID Connect. Usługa Azure Active Directory są przechowywane informacje o użytkowniku i zapewnia, że certyfikaty do weryfikacji tokenów JWT token sygnatury.

## <a name="presentation"></a>Prezentacja

Rozwiązanie obejmuje następujące składniki w prezentacji części logiczną architekturę:

[Interfejsu użytkownika sieci web jest aplikacją React Javascript](https://github.com/Azure/pcs-remote-monitoring-webui). Aplikacja:

* Używa tylko Javascript React i działa wyłącznie w przeglądarce.
* Jest styl CSS.
* Współdziała z publicznych mikrousług umożliwiający dostęp do Internetu za pośrednictwem wywołania AJAX.

Interfejs użytkownika przedstawia wszystkie funkcje akcelerator rozwiązań i korzysta z innych mikrousług, takich jak:

* Mikrousługi uwierzytelniania i autoryzacji, aby chronić dane użytkownika.
* Mikrousługi Menedżera usługi IoT Hub do listy i zarządzanie urządzeniami IoT.

Interfejs użytkownika jest zintegrowany z Eksploratora usługi Azure Time Series Insights umożliwiające wykonywanie zapytań i analizy danych telemetrycznych z urządzenia.

Mikrousługi konfiguracji umożliwia interfejsu użytkownika do przechowywania i pobierania ustawień konfiguracji.

## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz, zapoznaj się z dokumentacją źródłowych kodu i dla deweloperów, Rozpocznij od jednego z dwóch repozytoriów GitHub:

* [Akcelerator rozwiązań do monitorowania zdalnego za pomocą usługi Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).
* [Akcelerator rozwiązań do zdalnego monitorowania za pomocą usługi Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Szczegółowe diagramów architektury rozwiązania:
* [Akcelerator rozwiązań do monitorowania zdalnego architektury](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Aby uzyskać obszerniejszych informacji koncepcyjnych związanych akceleratora rozwiązania monitorowania zdalnego, zobacz [dostosować akcelerator rozwiązań](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).
