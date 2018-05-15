---
title: Przegląd akceleratorów rozwiązań usługi Azure IoT | Microsoft Docs
description: Opis akceleratorów rozwiązań usługi Azure IoT, w tym informacje dotyczące ich architektury z linkami prowadzącymi do dodatkowych zasobów.
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 59009f37-9ba0-4e17-a189-7ea354a858a2
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 17/01/2018
ms.author: dobett
ms.openlocfilehash: 2dc286dd228b1990e0307476d3623ffc91489f98
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="what-are-the-iot-solution-accelerators"></a>Co to są akceleratory rozwiązań IoT?

_Akceleratory rozwiązań_ usługi Azure IoT to zestaw rozwiązań, które:

* Można wdrożyć w ciągu kilku minut.
* Ułatwiają szybkie rozpoczynanie pracy.
* Można dostosować do własnych wymagań.

Wszystkie akceleratory rozwiązań zostały zaprojektowane według tych samych reguł i z uwzględnieniem tych samych celów.

Poniższe wideo zawiera omówienie akceleratora rozwiązania do zdalnego monitorowania:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Meet-the-new-Remote-Monitoring-accelerator-for-Azure-IoT/Player]

## <a name="solution-accelerators-overview"></a>Przegląd akceleratorów rozwiązań

Akcelerator rozwiązania to implementacja typu open source typowych wzorców rozwiązań IoT, którą można wdrożyć na platformie Azure przy użyciu posiadanej subskrypcji. Każdy akcelerator rozwiązania łączy kod niestandardowy i usługi platformy Azure w celu zaimplementowania konkretnego scenariusza lub scenariuszy IoT. Dowolny scenariusz można dostosować do własnych wymagań. Scenariusze obejmują:

* Wizualizowanie danych na zaawansowanym pulpicie nawigacyjnym na potrzeby uzyskiwania szczegółowego wglądu w dane i wyświetlania stanu rozwiązania.
* Konfigurowanie reguł i alarmów na podstawie przesyłanych w czasie rzeczywistym danych telemetrycznych z urządzenia IoT.
* Planowanie zadań zarządzania urządzeniami, takich jak aktualizacje oprogramowania i konfiguracja.
* Udostępnianie własnych niestandardowych urządzeń fizycznych lub symulowanych.
* Rozwiązywanie i korygowanie problemów w ramach grup urządzeń IoT.

Każdy akcelerator rozwiązania stanowi kompletną implementację, która może korzystać z symulowanych lub fizycznych urządzeń do generowania danych telemetrycznych. Akceleratorów rozwiązań można używać do:

* Udostępniania punktu wyjściowego dla własnych rozwiązań IoT.
* Uzyskiwania informacji o typowych wzorcach projektowania i tworzenia rozwiązań IoT.

Obecnie są dostępne trzy akceleratory rozwiązań:

* [Zdalne monitorowanie](iot-suite-remote-monitoring-explore.md)
* [Konserwacja predykcyjna](iot-suite-predictive-overview.md)
* [Połączona fabryka](iot-suite-connected-factory-overview.md)

W poniższej tabeli przedstawiono odwzorowanie rozwiązań na określone funkcje IoT:

| Rozwiązanie | Wprowadzanie danych | Tożsamość urządzenia | Zarządzanie urządzeniami | Przetwarzanie brzegowe | Sterowanie i kontrola | Reguły i akcje | Analiza predykcyjna |
| ------------------------------------------------------------ | -- | -- | -- | -- | -- | -- | -- |
| [Zdalne monitorowanie](iot-suite-remote-monitoring-explore.md)  |Yes |Yes |Yes |-   |Yes |Yes |-   |
| [Konserwacja predykcyjna](iot-suite-predictive-overview.md)   |Yes |Yes |-   |-   |Yes |Yes |Yes |
| [Połączona fabryka](iot-suite-connected-factory-overview.md) |Yes |- |- |Yes |Yes |Yes |-   |

* *Wprowadzanie danych*: transfer danych do chmury na dużą skalę.
* *Tożsamość urządzenia*: zarządzanie unikatowymi tożsamościami urządzeń i sterowanie dostępem urządzeń do rozwiązania.
* *Zarządzanie urządzeniami*: zarządzanie metadanymi urządzeń i wykonywanie operacji, takich jak ponowne uruchamianie urządzeń i aktualizacje oprogramowania układowego.
* *Sterowanie i kontrola*: aby spowodować wykonanie akcji przez urządzenie, wysyłanie komunikatów z chmury do urządzenia.
* *Reguły i akcje*: w celu wykonania działania względem określonych danych przesyłanych z urządzenia do chmury zaplecze rozwiązania korzysta z ról.
* *Analiza predykcyjna*: zaplecze rozwiązania analizuje dane przesyłane z urządzenia do chmury i przewiduje czas, kiedy konkretne działania powinny zostać wykonane. Na przykład analiza danych telemetrycznych silnika samolotu umożliwia określenie konieczności przeprowadzenia konserwacji silnika.

> [!NOTE]
> Aby wdrożyć akcelerator rozwiązania i uzyskać więcej informacji o sposobie jego dostosowywania, odwiedź stronę [Akceleratory rozwiązań usługi Microsoft Azure IoT](https://www.azureiotsuite.com/).

## <a name="azure-services"></a>Usługi platformy Azure

Podczas wdrażania akceleratora rozwiązania proces aprowizowania konfiguruje pewną liczbę usług platformy Azure. W poniższej tabeli przedstawiono usługi używane w akceleratorach rozwiązań:

|                      | Zdalne monitorowanie  | Konserwacja zapobiegawcza | Połączona fabryka |
| -------------------- | ------------------ | ---------------------- | ----------------- |
| IoT Hub              | Yes                | Yes                    | Yes               |
| Event Hubs           |                    | Yes                    |                   |
| Time Series Insights |                    |                        | Yes               |
| Usługi kontenerów   | Yes                |                        |                   |
| Stream Analytics     |                    | Yes                    |                   |
| Web Apps             | Yes                | Yes                    | Yes               |
| Cosmos DB            | Yes                | Yes                    |                    |
| Azure Storage         |                    | Yes                    | Yes               |

> [!NOTE]
> Aby uzyskać więcej informacji na temat zasobów wdrożonych w ramach akceleratora rozwiązania do zdalnego monitorowania, zobacz ten [artykuł](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/blob/master/README.md#basic-vs-standard-deployments) w witrynie GitHub.

* [Azure IoT Hub](../iot-hub/index.yml). Ta usługa umożliwia dwukierunkowe przesyłanie komunikatów między urządzeniami i chmurą oraz działa jako brama chmury i innych kluczowych usług akceleratora rozwiązania. Usługa ta pozwala odbierać komunikaty z urządzeń na dużą skalę i wysyłać polecenia do urządzeń. Usługa ta umożliwia także [zarządzanie urządzeniami](../iot-hub/iot-hub-device-management-overview.md). Na przykład możesz skonfigurować jedno lub większą liczbę urządzeń połączonych z tą usługą, przeprowadzić ich ponowny rozruch lub je zresetować do ustawień fabrycznych.
* [Azure Event Hubs](../event-hubs/index.md). Ta usługa umożliwia wprowadzanie dużej liczby zdarzeń do chmury. Zobacz [Porównanie Centrum IoT Azure i usługi Azure Event Hubs](../iot-hub/iot-hub-compare-event-hubs.md).
* [Azure Time Series Insights](../time-series-insights/index.yml). Akceleratory rozwiązań używają tej usługi do analizowania i wyświetlania danych telemetrycznych z urządzeń.
* [Azure Container Service](../container-service/index.yml). Ta usługa hostuje mikrousługi i zarządza nimi w akceleratorach rozwiązań.
* Usługi [Azure Cosmos DB](../cosmos-db/index.yml) i [Azure Storage](../storage/index.yml) obsługują przechowywanie danych.
* [Azure Stream Analytics](../stream-analytics/index.yml). Wstępnie skonfigurowane rozwiązanie do konserwacji predykcyjnej korzysta z tej usługi w celu przetwarzania przychodzących danych telemetrycznych, przeprowadzania agregacji danych i wykrywania zdarzeń. To wstępnie skonfigurowane rozwiązanie używa również analizy strumienia do przetwarzania komunikatów informacyjnych, które zawierają metadane lub odpowiedzi urządzeń na wysyłane polecenia.
* Usługa [Azure Web Apps](../app-service/index.yml) hostuje niestandardowy kod aplikacji we wstępnie skonfigurowanych rozwiązaniach.

Omówienie architektury typowego rozwiązania IoT można znaleźć w artykule [Platforma Azure i Internet rzeczy](iot-suite-what-is-azure-iot.md).

## <a name="whats-new-in-solution-accelerators"></a>Co nowego w akceleratorach rozwiązań?

Firma Microsoft aktualizuje akceleratory rozwiązań do nowej architektury opartej na mikrousługach. W poniższej tabeli przedstawiono bieżący stan akceleratorów rozwiązań:

| Akcelerator rozwiązania | Architektura  | Języki     |
| ---------------------- | ------------- | ------------- |
| Zdalne monitorowanie      | Mikrousługi | Java i .NET |
| Konserwacja zapobiegawcza | MVC           | .NET          |
| Połączona fabryka      | MVC           | .NET          |

W poniższych sekcjach opisano nowości wprowadzone w akceleratorach rozwiązań opartych na mikrousługach:

### <a name="microservices"></a>Mikrousługi

Nowa wersja akceleratora rozwiązania do zdalnego monitorowania korzysta z architektury mikrousług. Ten akcelerator rozwiązania składa się z wielu mikrousług, takich jak *menedżer usługi IoT Hub* i *menedżer magazynowania*. Poszczególne mikrousługi można pobrać zarówno w wersji Java, jak i .NET, wraz z pokrewną dokumentacją dla deweloperów. Aby uzyskać więcej informacji na temat mikrousług, zobacz [Architektura zdalnego monitorowania](iot-suite-remote-monitoring-sample-walkthrough.md).

Ta architektura mikrousług jest sprawdzonym wzorcem dla rozwiązań w chmurze, dzięki temu, że:

* Jest skalowalna.
* Umożliwia rozszerzalność.
* Jest łatwa do zrozumienia.
* Umożliwia wymianę poszczególnych usług na ich odpowiedniki.

> [!TIP]
> Aby dowiedzieć się więcej na temat architektury mikrousług, zobacz [Architektura aplikacji .NET](https://www.microsoft.com/net/learn/architecture) i [Mikrousługi: rewolucja w aplikacjach wspierana przez chmurę](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

Podczas wdrażania nowej wersji zdalnego monitorowania należy wybrać jedną z następujących opcji wdrażania:

* **Podstawowa:** tańsza wersja przeznaczona do celów demonstracyjnych i do testowania wdrożenia. Wszystkie mikrousługi są wdrażane na jednej maszynie wirtualnej platformy Azure.
* **Standardowa:** rozwinięte wdrożenie infrastruktury na potrzeby opracowywania wdrożenia produkcyjnego. Mikrousługi są wdrażane na wielu maszynach wirtualnych platformy Azure za pomocą usługi Azure Container Service. Platforma Kubernetes zarządza kontenerami aparatu Docker, w których są hostowane poszczególne mikrousługi.

### <a name="language-choices-java-and-net"></a>Wybór języka: Java i .NET

Implementacje poszczególnych mikrousług są dostępne w językach Java i .NET. Podobnie jak kod platformy .NET, kod źródłowy Java jest typu „open source” i można go dostosować do konkretnych wymagań:

* [Repozytorium GitHub rozwiązania do zdalnego monitorowania dla platformy .NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Repozytorium GitHub rozwiązania do zdalnego monitorowania w języku Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

Jeśli potrzebujesz implementacji w innym językach, zapytaj o nie na forum [Azure IoT User Voice](https://feedback.azure.com/forums/321918-azure-iot).

### <a name="react-user-interface-framework"></a>Struktura interfejsu użytkownika React

Interfejs użytkownika jest tworzony przy użyciu biblioteki [React](https://facebook.github.io/react/) języka JavaScript. Kod źródłowy jest typu „open source”, można go pobrać i dostosować.

## <a name="next-steps"></a>Następne kroki

Po omówieniu akceleratorów rozwiązań IoT zamieszczono tutaj sugerowane kolejne kroki dla poszczególnych akceleratorów rozwiązań:

* [Poznaj rozwiązanie do zdalnego monitorowania](iot-suite-remote-monitoring-explore.md)
* [Omówienie akceleratora rozwiązania do konserwacji zapobiegawczej](iot-suite-predictive-overview.md)
* [Wprowadzenie do akceleratora rozwiązania połączonej fabryki](iot-suite-connected-factory-overview.md)

Aby uzyskać więcej informacji na temat architektury rozwiązań IoT, zobacz dokument [Microsoft Azure IoT services: Reference Architecture](http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf) (Usługi Microsoft Azure IoT: architektura referencyjna).
