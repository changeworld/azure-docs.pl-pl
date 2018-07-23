---
title: Zdalne monitorowanie omówienie akceleratora rozwiązań — Azure | Dokumentacja firmy Microsoft
description: Omówienie akceleratora rozwiązania monitorowania zdalnego.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: dfe584532efeab1dbc0d2928b7afb0a6695a21ee
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39184949"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Omówienie akceleratora rozwiązań do zdalnego monitorowania

Zdalne monitorowanie [akcelerator rozwiązań](../iot-accelerators/about-iot-accelerators.md) implementuje — kompleksowe rozwiązanie do monitorowania dla wielu maszyn w lokalizacjach zdalnych. Rozwiązanie to łączy najważniejsze usługi platformy Azure, aby umożliwić ogólną implementację scenariusza biznesowego. Można użyć rozwiązania jako punktu wyjścia dla własnej implementacji i [dostosować](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) go do konkretnych potrzeb biznesowych.

W tym artykule opisano przez niektóre kluczowe elementy rozwiązania monitorowania zdalnego, aby umożliwić Ci zrozumienie sposobu działania. Ta wiedza ułatwi Ci:

* Rozwiązywanie problemów w rozwiązaniu.
* Planowanie sposobu dostosowywania rozwiązania, aby spełniało Twoje wymagania.
* Projektowanie własnego rozwiązania IoT korzystającego z usług Azure.

## <a name="logical-architecture"></a>Architektura logiczna

Poniższy diagram przedstawia składniki logiczne nałożony na akceleratora rozwiązania monitorowania zdalnego [architektury IoT](../iot-fundamentals/iot-introduction.md):

![Architektura logiczna](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Dlaczego mikrousługi?

Powstała architektury chmury, ponieważ firma Microsoft wydała pierwszą akceleratorów rozwiązań. [Mikrousługi](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) mają została uznana sprawdzone praktyki do osiągnięcia skali i elastyczności bez obniżania oczekiwanego poziomu prędkość opracowywania. Kilka usług firmy Microsoft za pomocą tego wzorca architektury wewnętrznie doskonałą niezawodność i skalowalność wyników. Akceleratory zaktualizowane rozwiązanie umieścić te informacje w praktyce, dzięki czemu można również korzystać z nich.

> [!TIP]
> Aby dowiedzieć się więcej na temat architektury mikrousług, zobacz [Architektura aplikacji .NET](https://www.microsoft.com/net/learn/architecture) i [Mikrousługi: rewolucja w aplikacjach wspierana przez chmurę](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="device-connectivity"></a>Łączność urządzeń

Rozwiązanie obejmuje następujące składniki w części łączności urządzeń logiczną architekturę:

### <a name="simulated-devices"></a>Symulowane urządzenia

Rozwiązanie obejmuje mikrousług, która pozwala na zarządzanie pulą symulowanych urządzeń w celu przetestowania przepływu end-to-end w rozwiązaniu. Symulowane urządzenia:

* Generowania danych telemetrycznych z urządzenia do chmury.
* Odpowiadanie na wywołania metody chmury do urządzenia z usługi IoT Hub.

Mikrousługi udostępnia punkt końcowy usługi RESTful umożliwiające tworzenie, uruchamianie i zatrzymywanie symulacji. Każdy symulację składa się z zestawu wirtualnych urządzeń różnych typów, które wysyłają dane telemetryczne i odpowiadanie na wywołania metody.

Możesz aprowizować symulowane urządzenia z poziomu pulpitu nawigacyjnego w portalu rozwiązania.

### <a name="physical-devices"></a>Urządzenia fizyczne

Fizyczne urządzenia można podłączyć do rozwiązania. Możesz zaimplementować zachowanie symulowanego urządzenia za pomocą zestawów SDK urządzeń Azure IoT.

Można udostępnić fizyczne urządzenia z poziomu pulpitu nawigacyjnego w portalu rozwiązania.

### <a name="iot-hub-and-the-iot-manager-microservice"></a>Mikrousługi Menedżera IoT i usługi IoT Hub

[Usługi IoT hub](../iot-hub/index.yml) pozyskuje dane wysyłane z urządzenia do chmury i udostępnia je zadaniom `telemetry-agent` mikrousług.

Usługa IoT Hub w rozwiązaniu wykonuje ponadto następujące czynności:

* Obsługuje rejestr tożsamości, w którym są przechowywane identyfikatory i klucze uwierzytelniania wszystkich urządzeń, może łączyć się z portalu. Za pomocą rejestru tożsamości możesz włączać i wyłączać urządzenia.
* Wywołuje metody na Twoich urządzeniach w imieniu portalu rozwiązania.
* Obsługuje bliźniacze reprezentacje wszystkich zarejestrowanych urządzeń. W bliźniaczej reprezentacji urządzenia są przechowywane wartości właściwości zgłaszanych przez urządzenie. W bliźniaczej reprezentacji urządzenia są także przechowywane żądane właściwości ustawione w portalu rozwiązania, które mają zostać pobrane przez urządzanie podczas nawiązywania następnego połączenia.
* Planuje zadania ustawiania właściwości dla wielu urządzeń lub wywoływania metod na wielu urządzeniach.

Rozwiązanie obejmuje `iot-manager` mikrousług do obsługi interakcji z Centrum IoT, takich jak:

* Tworzenie i zarządzanie urządzeniami IoT.
* Zarządzanie bliźniaczych reprezentacji urządzeń.
* Wywoływanie metody na urządzeniach.
* Zarządzanie poświadczeniami IoT.

Ta usługa jest również uruchamiane usługi IoT Hub zapytania, aby pobrać urządzeń należących do grup zdefiniowanych przez użytkownika.

Mikrousługi udostępnia punkt końcowy usługi RESTful do zarządzania urządzeniami i bliźniacze reprezentacje urządzeń, wywoływanie metod i uruchamianie zapytań usługi IoT Hub.

## <a name="data-processing-and-analytics"></a>Przetwarzanie danych i analiza

Rozwiązanie obejmuje następujące składniki przetwarzania danych i analizy część logiczną architekturę:

### <a name="device-telemetry"></a>Danych telemetrycznych z urządzenia

Rozwiązanie zawiera dwa mikrousług do obsługi danych telemetrycznych z urządzenia.

[Agenta telemetrii](https://github.com/Azure/telemetry-agent-dotnet) mikrousług:

* Dane telemetryczne są przechowywane w usłudze Azure Cosmos DB.
* Analizuje strumień danych telemetrycznych z urządzeń.
* Generuje alarmy zgodnie ze zdefiniowanymi regułami.

Alarmy są przechowywane w usłudze Azure Cosmos DB.

[Agenta telemetrii](https://github.com/Azure/telemetry-agent-dotnet) mikrousług umożliwia portalu rozwiązania odczytujące dane telemetryczne wysyłane z urządzeń. W portalu rozwiązania zostanie również korzysta z tej usługi, aby:

* Definiowanie reguł monitorowania, takie jak progi wyzwalające alarmów
* Pobranie listy ostatnich alarmy.

Umożliwia zarządzanie telemetrii, reguł i alarmów punktem końcowym RESTful udostępniane przez ten mikrousług.

### <a name="storage"></a>Magazyn

[Adapter magazynu](https://github.com/Azure/pcs-storage-adapter-dotnet) mikrousług jest karta przed Usługa magazynu głównego używany do akceleratora rozwiązań. Zapewnia ona prostych kolekcji i magazyn kluczy i wartości.

Standardowe wdrażanie akceleratora rozwiązań używa usługi Azure Cosmos DB jako jego usługi magazynu głównego.

Baza danych usługi Azure Cosmos DB przechowuje dane w akcelerator rozwiązań. **Adapter magazynu** mikrousług działa jako karty dla innych mikrousług w rozwiązaniu do dostępu do usług magazynu.

## <a name="presentation"></a>Prezentacja

Rozwiązanie obejmuje następujące składniki w prezentacji części logiczną architekturę:

[Interfejsu użytkownika sieci web jest aplikacją React Javascript](https://github.com/Azure/pcs-remote-monitoring-webui). Aplikacja:

* Używa tylko Javascript React i działa wyłącznie w przeglądarce.
* Jest styl CSS.
* Współdziała z publicznych mikrousług umożliwiający dostęp do Internetu za pośrednictwem wywołania AJAX.

Interfejs użytkownika przedstawia wszystkie funkcje akcelerator rozwiązań i współdziała z innymi usługami, takie jak:

* [Uwierzytelniania](https://github.com/Azure/pcs-auth-dotnet) mikrousług, aby chronić dane użytkownika.
* [Iothub-manager](https://github.com/Azure/iothub-manager-dotnet) mikrousług, aby wyświetlić listę i zarządzanie urządzeniami IoT.

[Interfejsu użytkownika konfiguracji](https://github.com/Azure/pcs-config-dotnet) mikrousług zapewnia interfejs użytkownika do przechowywania i pobierania ustawień konfiguracji.

## <a name="next-steps"></a>Kolejne kroki

Chcąc zapoznaj się z dokumentacją źródłowych kodu i dla deweloperów, uruchom za pomocą jednego dwóch głównych repozytoriów GitHub:

* [Akcelerator rozwiązań do monitorowania zdalnego za pomocą usługi Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/).
* [Akcelerator rozwiązań do zdalnego monitorowania za pomocą usługi Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Szczegółowe diagramów architektury rozwiązania:
* [Akcelerator rozwiązań do monitorowania zdalnego architektury](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Aby uzyskać obszerniejszych informacji koncepcyjnych związanych akceleratora rozwiązania monitorowania zdalnego, zobacz [dostosować akcelerator rozwiązań](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).
