---
title: Funkcje rozwiązania Connected Factory — Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano omówienie funkcji wstępnie skonfigurowanego rozwiązania połączonego systemu fabrycznego, takich jak pulpit nawigacyjny chmury, reguły i alerty.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: c868aa0f1c2449ccf163523c9ded25a31d1d84c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820111"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Co to jest akcelerator rozwiązań Connected Factory IoT?

Connected Factory to implementacja architektury referencyjnej Azure Industrial IoT firmy Microsoft, pakowana jako rozwiązanie typu open source. Można go używać jako punktu wyjścia dla produktu komercyjnego. Wstępnie skomponowana wersja rozwiązania Connected Factory można wdrożyć w ramach subskrypcji platformy Azure za pomocą [akceleratorów rozwiązań Azure IoT.](https://www.azureiotsolutions.com/#solutions/types/CF)

![Pulpit nawigacyjny rozwiązania Połączonej fabryki](./media/iot-accelerators-connected-factory-features/dashboard.png)

Kod akceleratora rozwiązań Connected Factory [jest dostępny w serwisie GitHub.](https://github.com/Azure/azure-iot-connected-factory)

Connected Factory zawiera następujące funkcje:

## <a name="industrial-device-interoperability"></a>Interoperacyjność urządzeń przemysłowych

- Połącz się z zasobami przemysłowymi za pomocą interfejsu OPC UA.
- Użyj symulowanych linii produkcyjnych (z uruchomionymi serwerami OPC UA w kontenerach platformy Docker), aby wyświetlić z nich dane telemetryczne na żywo.
- Przejrzyj model informacji OPC UA serwerów OPC UA z pulpitu nawigacyjnego w chmurze.

## <a name="remote-management"></a>Zdalne zarządzanie

- Skonfiguruj zasoby OPC UA z pulpitu nawigacyjnego w chmurze (metody wywołania, odczytu i zapisu danych).
- Publikowanie i odchowywaj dane telemetryczne z zasobów OPC UA z pulpitu nawigacyjnego w chmurze.

## <a name="cloud-dashboard"></a>Pulpit nawigacyjny chmury

- Wyświetlanie podglądów telemetrii bezpośrednio na pulpicie nawigacyjnym w chmurze.
- Wyświetlanie trendów w danych telemetrycznych i tworzenie korelacji przy użyciu pulpitu nawigacyjnego Eksploratora usługi Time Series Insights.
- Zobacz obliczone ogólne wskaźniki wydajności sprzętu (OEE) i kluczowe wskaźniki wydajności (KPI) z pulpitu nawigacyjnego w chmurze.
- Wyświetlanie hierarchii zasobów przemysłowych w topologii drzewa oraz na interaktywnej mapie.
- Wyświetlanie, potwierdzanie i zamykanie alertów z pulpitu nawigacyjnego w chmurze.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Usługa Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) jest przeznaczona do przechowywania, wizualizacji i wykonywania zapytań o duże ilości danych szeregów czasowych. Connected Factory korzysta z tej usługi.
- Connected Factory integruje się z tą usługą, umożliwiając przeprowadzenie dogłębnej analizy danych urządzenia w czasie rzeczywistym.

## <a name="rules-and-alerts"></a>Zasady i alerty

[Skonfiguruj reguły oparte na progach dla alertów](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Kompleksowe zabezpieczenia

- Konfigurowanie uprawnień zabezpieczeń dla użytkowników korzystających z kontroli dostępu opartej na rolach (RBAC).
- Szyfrowanie end-to-end jest implementowane przy użyciu uwierzytelniania OPC UA (przy użyciu certyfikatów X.509) oraz tokenów zabezpieczających.

## <a name="customizability"></a>Dostosowywania

- Dostosuj rozwiązanie do określonych wymagań biznesowych.
- Pełny kod źródłowy rozwiązania dostępny w usłudze GitHub. Zobacz wstępnie skonfigurowane repozytorium [rozwiązań połączonego z fabryką.](https://github.com/Azure/azure-iot-connected-factory)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o akceleratorze rozwiązań Connected Factory, zobacz Szybki start [Wypróbuj rozwiązanie oparte na chmurze do zarządzania przemysłowymi urządzeniami IoT.](quickstart-connected-factory-deploy.md)
