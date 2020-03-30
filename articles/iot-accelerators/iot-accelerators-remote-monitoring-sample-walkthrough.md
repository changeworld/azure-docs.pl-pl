---
title: Omówienie akceleratora rozwiązań do zdalnego monitorowania — Platforma Azure | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie niektórych kluczowych elementów rozwiązania zdalnego monitorowania, aby umożliwić zrozumienie, jak to działa.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 33005bc286f7dc2c0ebed74bd9df0309e71346f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73824830"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Omówienie akceleratora rozwiązania do monitorowania zdalnego

[Akcelerator rozwiązań](../iot-accelerators/about-iot-accelerators.md) do zdalnego monitorowania implementuje kompleksowe rozwiązanie do monitorowania wielu komputerów w odległych lokalizacjach. Rozwiązanie to łączy najważniejsze usługi platformy Azure, aby umożliwić ogólną implementację scenariusza biznesowego. Możesz go użyć jako punktu wyjścia dla Twojej własnej implementacji i je [dostosować](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md), aby spełniało Twoje własne określone wymagania biznesowe.

W tym artykule otrzymasz od niektórych kluczowych elementów rozwiązania do zdalnego monitorowania, aby zrozumieć, jak to działa. Ta wiedza ułatwi Ci:

* Rozwiązywanie problemów w rozwiązaniu.
* Planowanie sposobu dostosowywania rozwiązania, aby spełniało Twoje wymagania.
* Projektowanie własnego rozwiązania IoT korzystającego z usług Azure.

Kod akceleratora rozwiązania do zdalnego monitorowania jest dostępny w usłudze GitHub:

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>Architektura logiczna

Na poniższym diagramie przedstawiono składniki logiczne akceleratora rozwiązania zdalnego monitorowania nałożone na [architekturę IoT:](../iot-fundamentals/iot-introduction.md)

![Architektura logiczna](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Dlaczego mikrousług?

Architektura chmury ewoluowała od czasu wydania przez firmę Microsoft pierwszych akceleratorów rozwiązań. [Mikrousługi](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) pojawiły się jako sprawdzona praktyka, aby osiągnąć skalę i elastyczność bez utraty szybkości rozwoju. Kilka usług firmy Microsoft używa tego wzorca architektury wewnętrznie z dużą niezawodnością i skalowalnością. Zaktualizowane akceleratory rozwiązań wcielić te nauki w życie, dzięki czemu można również korzystać z nich.

> [!TIP]
> Aby dowiedzieć się więcej na temat architektury mikrousług, zobacz [Architektura aplikacji .NET](https://www.microsoft.com/net/learn/architecture) i [Mikrousługi: rewolucja w aplikacjach wspierana przez chmurę](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="device-connectivity"></a>Łączność urządzeń

Rozwiązanie zawiera następujące składniki w części łączności urządzenia architektury logicznej:

### <a name="real-devices"></a>Rzeczywiste urządzenia

Do rozwiązania można podłączyć prawdziwe urządzenia. Można zaimplementować zachowanie symulowanych urządzeń przy użyciu zestawów SDK urządzeń IoT platformy Azure.

Można aprowizować rzeczywiste urządzenia z pulpitu nawigacyjnego w portalu rozwiązania.

### <a name="device-simulation-microservice"></a>Mikrousługa symulacji urządzenia

Rozwiązanie obejmuje [mikrousługi symulacji urządzenia,](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation) która umożliwia zarządzanie pulą symulowanych urządzeń z portalu rozwiązania, aby przetestować przepływ end-to-end w rozwiązaniu. Symulowane urządzenia:

* Generowanie danych telemetrycznych między urządzeniami do chmury.
* Odpowiadanie na wywołania metody chmury do urządzenia z usługi IoT Hub.

Mikrousługa zapewnia punkt końcowy RESTful do tworzenia, uruchamiania i zatrzymywania symulacji. Każda symulacja składa się z zestawu urządzeń wirtualnych różnych typów, które wysyłają dane telemetryczne i odpowiadają na wywołania metody.

Można aprowizować symulowane urządzenia z pulpitu nawigacyjnego w portalu rozwiązania.

### <a name="iot-hub"></a>Usługa IoT Hub

[Centrum IoT](../iot-hub/index.yml) posuwania danych telemetrycznych wysyłanych z rzeczywistych i symulowanych urządzeń do chmury. Centrum IoT hub udostępnia dane telemetryczne do usług w wewnętrznej bazy danych rozwiązania IoT do przetwarzania.

Usługa IoT Hub w rozwiązaniu wykonuje ponadto następujące czynności:

* Utrzymuje rejestr tożsamości, który przechowuje identyfikatory i klucze uwierzytelniania wszystkich urządzeń, które mogą łączyć się z portalem.
* Wywołuje metody na urządzeniach w imieniu akceleratora rozwiązań.
* Obsługuje bliźniacze reprezentacje wszystkich zarejestrowanych urządzeń. W bliźniaczej reprezentacji urządzenia są przechowywane wartości właściwości zgłaszanych przez urządzenie. W bliźniaczej reprezentacji urządzenia są także przechowywane żądane właściwości ustawione w portalu rozwiązania, które mają zostać pobrane przez urządzanie podczas nawiązywania następnego połączenia.
* Planuje zadania ustawiania właściwości dla wielu urządzeń lub wywoływania metod na wielu urządzeniach.

## <a name="data-processing-and-analytics"></a>Przetwarzanie danych i analiza

Rozwiązanie obejmuje następujące składniki w części przetwarzania i analizy danych architektury logicznej:

### <a name="iot-hub-manager-microservice"></a>Mikrousługi menedżera usługi IoT Hub

Rozwiązanie obejmuje [mikrousługi menedżera usługi IoT Hub](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) do obsługi interakcji z centrum IoT hub, takich jak:

* Tworzenie urządzeń IoT i zarządzanie nimi.
* Zarządzanie bliźniacze urządzenia.
* Wywoływanie metod na urządzeniach.
* Zarządzanie poświadczeniami IoT.

Ta usługa uruchamia również zapytania Usługi IoT Hub w celu pobrania urządzeń należących do grup zdefiniowanych przez użytkownika.

Mikrousługa zapewnia punkt końcowy RESTful do zarządzania urządzeniami i bliźniaczymi urządzeń, wywoływania metod i uruchamiania zapytań Usługi IoT Hub.

### <a name="device-telemetry-microservice"></a>Mikrousługa telemetrii urządzenia

[Mikrousługa telemetryczna urządzenia](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry) zapewnia punkt końcowy RESTful dla dostępu do odczytu danych telemetrycznych urządzeń przechowywanych w usłudze Time Series Insights. Punkt końcowy RESTful umożliwia również operacje CRUD na regułach i dostęp do odczytu/zapisu dla definicji alarmów z magazynu.

### <a name="storage-adapter-microservice"></a>Mikrousługa karty pamięci masowej

[Mikrousługa karty magazynu](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter) zarządza parami klucz-wartość, abstrakcjonowanie semantyki usługi magazynu i prezentowanie prostego interfejsu do przechowywania danych dowolnego formatu przy użyciu usługi Azure Cosmos DB.

Wartości są zorganizowane w kolekcjach. Można pracować nad poszczególnymi wartościami lub pobrać całe kolekcje. Złożone struktury danych są serializowane przez klientów i zarządzane jako prosty ładunek tekstu.

Usługa zapewnia punkt końcowy RESTful dla operacji CRUD na pary klucza wartości. values

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Wdrożenia akceleratora rozwiązań używają [usługi Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) do przechowywania reguł, alertów, ustawień konfiguracji i wszystkich innych chłodni.

### <a name="azure-stream-analytics-manager-microservice"></a>Mikrousługi usługi Azure Stream Analytics manager

[Mikrousługa usługi Azure Stream Analytics manager](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager) zarządza zadaniami usługi Azure Stream Analytics (ASA), w tym ustawianiem ich konfiguracji, uruchamianiem i zatrzymywaniem ich oraz monitorowaniem ich stanu.

Zadanie ASA jest obsługiwane przez dwa zestawy danych referencyjnych. Jeden zestaw danych definiuje reguły, a jeden definiuje grupy urządzeń. Dane referencyjne reguł są generowane na podstawie informacji zarządzanych przez mikrousługi telemetryczne urządzenia. Mikrousługi menedżera usługi Azure Stream Analytics przekształca reguły telemetryczne w logikę przetwarzania strumienia.

Dane referencyjne grup urządzeń są używane do identyfikowania grupy reguł, które mają być stosowane do przychodzącego komunikatu telemetrycznego. Grupy urządzeń są zarządzane przez mikrousługi konfiguracji i używać usługi Azure IoT Hub bliźniaczej reprezentacji zapytań.

Zadania ASA dostarczają dane telemetryczne z podłączonych urządzeń do usługi Time Series Insights do przechowywania i analizy.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics

[Usługa Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) to aparat przetwarzania zdarzeń, który umożliwia sprawdzanie dużej ilości przesyłania strumieniowego danych z urządzeń.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[Usługa Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/) przechowuje dane telemetryczne z urządzeń podłączonych do akceleratora rozwiązań. Umożliwia również wizualizację i wyszukiwanie danych telemetrycznych urządzenia w interfejsie użytkownika sieci web rozwiązania.

> [!NOTE]
> Usługa Time Series Insights nie jest obecnie dostępna w chmurze platformy Azure w Chinach. Nowe wdrożenia akceleratora rozwiązań do zdalnego monitorowania w chmurze azure chiny używać usługi Cosmos DB dla wszystkich magazynu.

### <a name="configuration-microservice"></a>Mikrousługa konfiguracji

[Mikrousługa konfiguracji](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) zapewnia punkt końcowy RESTful dla operacji CRUD w grupach urządzeń, ustawieniach rozwiązania i ustawieniach użytkownika w akceleratorze rozwiązań. Współpracuje z mikrousługą karty magazynu, aby utrwalić dane konfiguracji.

### <a name="authentication-and-authorization-microservice"></a>Uwierzytelnianie i autoryzacja mikrousług

[Mikrousługa uwierzytelniania i autoryzacji](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) zarządza użytkownikami autoryzowanym do uzyskania dostępu do akceleratora rozwiązań. Zarządzanie użytkownikami można wykonać za pomocą dowolnego dostawcy usług tożsamości obsługującego [openid connect](https://openid.net/connect/).

### <a name="azure-active-directory"></a>Usługa Azure Active Directory

Wdrożenia akceleratora rozwiązań używają [usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) jako dostawcy openid connect. Usługa Azure Active Directory przechowuje informacje o użytkowniku i udostępnia certyfikaty do sprawdzania poprawności podpisów tokenów JWT.

## <a name="presentation"></a>Prezentacja

Rozwiązanie zawiera następujące składniki w części prezentacji architektury logicznej:

Internetowy [interfejs użytkownika jest aplikacją React Javascript](https://github.com/Azure/pcs-remote-monitoring-webui). Zastosowanie:

* Używa tylko Javascript React i działa w całości w przeglądarce.
* Jest stylizowany na CSS.
* Współdziała z mikrousług publicznych stoi za pośrednictwem wywołań AJAX.

Interfejs użytkownika przedstawia wszystkie funkcje akceleratora rozwiązań i współdziała z innymi mikrousługami, takimi jak:

* Mikrousługi uwierzytelniania i autoryzacji w celu ochrony danych użytkownika.
* Mikrousługi menedżera usługi IoT Hub do listy i zarządzania urządzeniami IoT.

Interfejs użytkownika integruje Eksploratora usługi Azure Time Series Insights, aby umożliwić wykonywanie zapytań i analizę telemetrii urządzenia.

Mikrousługa konfiguracji umożliwia interfejs użytkownika do przechowywania i pobierania ustawień konfiguracji.

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz zapoznać się z kodem źródłowym i dokumentacją dewelopera, zacznij od jednego z dwóch repozytoriów GitHub:

* [Akcelerator rozwiązań do zdalnego monitorowania za pomocą usługi Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).
* [Akcelerator rozwiązań do zdalnego monitorowania za pomocą usługi Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Szczegółowe diagramy architektury rozwiązania:
* [Akcelerator rozwiązań dla architektury zdalnego monitorowania](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Aby uzyskać więcej informacji koncepcyjnych dotyczących akceleratora rozwiązań do zdalnego monitorowania, zobacz [Dostosowywanie akceleratora rozwiązań](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).
