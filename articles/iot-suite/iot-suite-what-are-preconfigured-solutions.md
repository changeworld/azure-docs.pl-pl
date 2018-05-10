---
title: Omówienie wstępnie skonfigurowanych rozwiązań pakietu Azure IoT Suite | Microsoft Docs
description: Opis wstępnie skonfigurowanych rozwiązań pakietu Azure IoT Suite, w tym informacje dotyczące ich architektury oraz linki prowadzące do dodatkowych zasobów.
services: ''
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
ms.openlocfilehash: b7b9f61a8fb46d5d591b317049cfd60b723a7e77
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-azure-iot-suite"></a>Co to jest pakiet Azure IoT Suite?

Pakiet Azure IoT Suite jest zestawem *wstępnie skonfigurowanych rozwiązań* który:

* Można wdrożyć w ciągu kilku minut.
* Ułatwia szybkie rozpoczynanie pracy.
* Można dostosować do własnych wymagań.

Wszystkie wstępnie skonfigurowane rozwiązania *Pakietu IoT* zostały zaprojektowane według tych samych reguł i celów.

Poniższe wideo zawiera omówienie wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Meet-the-new-Remote-Monitoring-accelerator-for-Azure-IoT/Player]

## <a name="preconfigured-solutions-overview"></a>Omówienie wstępnie skonfigurowanych rozwiązań

Wstępnie skonfigurowane rozwiązanie to implementacja typu „open source” typowych wzorców rozwiązań IoT, które można wdrożyć na platformie Azure przy użyciu posiadanej subskrypcji. Na każde wstępnie skonfigurowane rozwiązanie składa się niestandardowy kod i usługi platformy Azure umożliwiające zaimplementowanie konkretnego scenariusza (lub scenariuszy) IoT. Dowolny scenariusz można dostosować do własnych wymagań. Scenariusze obejmują:

* Wizualizowanie danych na zaawansowanym pulpicie nawigacyjnym na potrzeby uzyskiwania szczegółowego wglądu w dane i wyświetlania stanu rozwiązania.
* Konfigurowanie reguł i alarmów na podstawie przesyłanych w czasie rzeczywistym danych telemetrycznych z urządzenia IoT.
* Planowanie zadań zarządzania urządzeniami, takich jak aktualizacje oprogramowania i konfiguracja.
* Udostępnianie własnych niestandardowych urządzeń fizycznych lub symulowanych.
* Rozwiązywanie i korygowanie problemów w ramach grup urządzeń IoT.

Każde wstępnie skonfigurowane rozwiązanie stanowi kompletną implementację, która może korzystać z symulowanych lub fizycznych urządzeń do generowania danych telemetrycznych. Wstępnie skonfigurowanych rozwiązań można używać jako akceleratorów rozwiązań do:

* Udostępniania punktu wyjściowego dla własnych rozwiązań IoT.
* Uzyskiwania informacji o typowych wzorcach projektowania i tworzenia rozwiązań IoT.

Obecnie są dostępne trzy wstępnie skonfigurowane rozwiązania:

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
> Aby wdrożyć wstępnie skonfigurowane rozwiązanie i uzyskać więcej informacji o sposobie ich dostosowywania, odwiedź stronę [Pakiet Microsoft Azure IoT Suite](https://www.azureiotsuite.com/).

## <a name="azure-services"></a>Usługi platformy Azure

Podczas wdrażania wstępnie skonfigurowanego rozwiązania proces inicjowania obsługi konfiguruje pewną liczbę usług platformy Azure. W poniższej tabeli przedstawiono usługi używane ze wstępnie skonfigurowanymi rozwiązaniami:

|                      | Zdalne monitorowanie  | Konserwacja zapobiegawcza | Połączona fabryka |
| -------------------- | ------------------ | ---------------------- | ----------------- |
| Usługa IoT Hub              | Yes                | Yes                    | Yes               |
| Event Hubs           |                    | Yes                    |                   |
| Time Series Insights |                    |                        | Yes               |
| Usługi kontenerów   | Yes                |                        |                   |
| Stream Analytics     |                    | Yes                    |                   |
| Web Apps             | Yes                | Yes                    | Yes               |
| Cosmos DB            | Yes                | Yes                    |                    |
| Azure Storage         |                    | Yes                    | Yes               |

> [!NOTE]
> Aby uzyskać więcej informacji na temat zasobów wdrożonych we wstępnie skonfigurowanym rozwiązaniu monitorowania zdalnego, zobacz ten [artykuł](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/blob/master/README.md#basic-vs-standard-deployments) w witrynie GitHub.

* [Azure IoT Hub](../iot-hub/index.md). Umożliwia ona dwukierunkowe przesyłanie komunikatów między urządzeniami a chmurą i działa jako brama chmury i innych kluczowych usług Pakietu IoT. Usługa ta pozwala odbierać komunikaty z urządzeń na dużą skalę i wysyłać polecenia do urządzeń. Usługa ta umożliwia także [zarządzanie urządzeniami](../iot-hub/iot-hub-device-management-overview.md). Na przykład możesz skonfigurować jedno lub większą liczbę urządzeń połączonych z tą usługą, przeprowadzić ich ponowny rozruch lub je zresetować do ustawień fabrycznych.
* [Azure Event Hubs](../event-hubs/index.md). Ta usługa umożliwia wprowadzanie dużej liczby zdarzeń do chmury. Zobacz [Porównanie Centrum IoT Azure i usługi Azure Event Hubs](../iot-hub/iot-hub-compare-event-hubs.md).
* [Azure Time Series Insights](../time-series-insights/index.yml). Wstępnie skonfigurowane rozwiązania używają tej usługi do analizowania i wyświetlania danych telemetrycznych z urządzeń.
* [Azure Container Service](../container-service/index.yml). Ta usługa obsługuje mikrousługi i zarządza nimi we wstępnie skonfigurowanych rozwiązaniach.
* Usługi [Azure Cosmos DB](../cosmos-db/index.yml) i [Azure Storage](../storage/index.yml) obsługują przechowywanie danych.
* [Azure Stream Analytics](../stream-analytics/index.yml). Wstępnie skonfigurowane rozwiązanie do konserwacji predykcyjnej korzysta z tej usługi w celu przetwarzania przychodzących danych telemetrycznych, przeprowadzania agregacji danych i wykrywania zdarzeń. To wstępnie skonfigurowane rozwiązanie używa również analizy strumienia do przetwarzania komunikatów informacyjnych, które zawierają metadane lub odpowiedzi urządzeń na wysyłane polecenia.
* Usługa [Azure Web Apps](../app-service/index.yml) hostuje niestandardowy kod aplikacji we wstępnie skonfigurowanych rozwiązaniach.

Omówienie architektury typowego rozwiązania IoT można znaleźć w artykule [Platforma Azure i Internet rzeczy](iot-suite-what-is-azure-iot.md).

## <a name="whats-new-in-preconfigured-solutions"></a>Co nowego we wstępnie skonfigurowanych rozwiązaniach?

Firma Microsoft aktualizuje wstępnie skonfigurowane rozwiązania do nowej architektury bazującej na mikrousługach. W poniższej tabeli przedstawiono bieżący stan wstępnie skonfigurowanych rozwiązań:

| Wstępnie skonfigurowane rozwiązanie | Architektura  | Języki     |
| ---------------------- | ------------- | ------------- |
| Zdalne monitorowanie      | Mikrousługi | Java i .NET |
| Konserwacja zapobiegawcza | MVC           | .NET          |
| Połączona fabryka      | MVC           | .NET          |

W poniższych sekcjach opisano, jakie nowe elementy wprowadzono we wstępnie skonfigurowanych rozwiązaniach opartych na mikrousługach:

### <a name="microservices"></a>Mikrousługi

Nowa wersja wstępnie skonfigurowanego rozwiązania do zdalnego monitorowania korzysta z architektury mikrousług. To wstępnie skonfigurowane rozwiązanie składa się z wielu mikrousług, takich jak *Menedżer usług IoT Hub* lub *Menedżer magazynowania*. Poszczególne mikrousługi można pobrać zarówno w wersji Java, jak i .NET, wraz z pokrewną dokumentacją dla deweloperów. Aby uzyskać więcej informacji na temat mikrousług, zobacz [Architektura zdalnego monitorowania](iot-suite-remote-monitoring-sample-walkthrough.md).

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

* [Repozytorium GitHub rozwiązania do zdalnego monitorowania w języku .NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Repozytorium GitHub rozwiązania do zdalnego monitorowania w języku Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

Jeśli potrzebujesz implementacji w innym językach, zapytaj o nie na forum [Azure IoT User Voice](https://feedback.azure.com/forums/321918-azure-iot).

### <a name="react-user-interface-framework"></a>Struktura interfejsu użytkownika React

Interfejs użytkownika jest tworzony przy użyciu biblioteki [React](https://facebook.github.io/react/) języka JavaScript. Kod źródłowy jest typu „open source”, można go pobrać i dostosować.

## <a name="next-steps"></a>Następne kroki

Po omówieniu wstępnie skonfigurowanych rozwiązań Pakietu IoT zamieszczono tutaj sugerowane następne czynności, które warto wykonać dla poszczególnych rozwiązań:

* [Eksplorowanie modelu wdrażania przy użyciu usługi Resource Manager rozwiązania do zdalnego monitorowania pakietu Azure IoT Suite](iot-suite-remote-monitoring-explore.md).
* [Omówienie wstępnie skonfigurowanego rozwiązania konserwacji predykcyjnej](iot-suite-predictive-overview.md).
* [Wprowadzenie do wstępnie skonfigurowanego rozwiązania połączonej fabryki](iot-suite-connected-factory-overview.md).

Aby uzyskać więcej informacji na temat architektury rozwiązań IoT, zobacz dokument [Microsoft Azure IoT services: Reference Architecture](http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf) (Usługi Microsoft Azure IoT: architektura referencyjna).
