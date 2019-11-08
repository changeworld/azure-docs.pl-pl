---
title: Funkcje rozwiązania połączonej fabryki — Azure | Microsoft Docs
description: W tym artykule opisano Omówienie funkcji wstępnie skonfigurowanego rozwiązania połączonej fabryki, takie jak pulpit nawigacyjny, reguły i alerty w chmurze.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: c868aa0f1c2449ccf163523c9ded25a31d1d84c4
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820111"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Co to jest Akcelerator rozwiązania do podłączenia fabryki IoT?

Połączona fabryka to implementacja architektury referencyjnej platformy Azure przemysłowego firmy Microsoft, spakowana jako rozwiązanie typu "open source". Można go użyć jako punktu wyjścia dla produktu komercyjnego. Możesz wdrożyć wstępnie utworzoną wersję rozwiązania połączonej fabryki w ramach subskrypcji platformy Azure z [akceleratorów rozwiązań usługi Azure IoT](https://www.azureiotsolutions.com/#solutions/types/CF).

![Pulpit nawigacyjny rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-features/dashboard.png)

Kod akceleratora rozwiązania połączonej fabryki [jest dostępny w serwisie GitHub](https://github.com/Azure/azure-iot-connected-factory).

Połączona fabryka obejmuje następujące funkcje:

## <a name="industrial-device-interoperability"></a>Współdziałanie urządzeń przemysłowych

- Połącz zasoby przemysłowe z interfejsem OPC UA.
- Aby wyświetlić dane telemetryczne na żywo, użyj symulowanych linii produkcyjnych (z uruchomionymi serwerami OPC UA w kontenerach platformy Docker).
- Przeglądaj model informacyjny OPC UA serwerów OPC UA z poziomu pulpitu nawigacyjnego w chmurze.

## <a name="remote-management"></a>Zdalne zarządzanie

- Konfigurowanie zasobów OPC UA za pomocą pulpitu nawigacyjnego w chmurze (wywoływanie metod, odczytywanie i zapisywanie danych).
- Publikuj dane telemetryczne w zasobach OPC UA i cofaj ich publikację za pomocą pulpitu nawigacyjnego w chmurze.

## <a name="cloud-dashboard"></a>Pulpit nawigacyjny w chmurze

- Wyświetl podglądy danych telemetrycznych bezpośrednio na pulpicie nawigacyjnym w chmurze.
- Wyświetlaj trendy danych telemetrycznych i twórz korelacje za pomocą pulpitu nawigacyjnego eksploratora usługi Time Series Insights.
- Zobacz obliczona ogólna wydajność sprzętu (OEE) i kluczowe wskaźniki wydajności (KPI) na pulpicie nawigacyjnym chmury.
- Wyświetl hierarchie zasobów przemysłowych w topologii drzewa, a także na mapie interaktywnej.
- Wyświetlanie, Potwierdzanie i zamykanie alertów z poziomu pulpitu nawigacyjnego w chmurze.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) jest skompilowany do przechowywania, wizualizowania i wykonywania zapytań dotyczących dużych ilości danych szeregów czasowych. Połączona fabryka korzysta z tej usługi.
- Połączona fabryka integruje się z tą usługą, umożliwiając przeprowadzanie dokładnej analizy danych urządzenia w czasie rzeczywistym.

## <a name="rules-and-alerts"></a>Reguły i alerty

[Skonfiguruj reguły oparte na progach dla alertów](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Kompleksowe zabezpieczenia

- Konfiguruj uprawnienia zabezpieczeń dla użytkowników korzystających z kontroli dostępu opartej na rolach.
- Kompleksowe szyfrowanie jest implementowane przy użyciu uwierzytelniania OPC UA (przy użyciu certyfikatów X. 509) oraz tokenów zabezpieczających.

## <a name="customizability"></a>Szerszym

- Dostosuj rozwiązanie pod kątem spełnienia określonych wymagań firmy.
- Pełny kod źródłowy, dostępny w serwisie GitHub. Zobacz repozytorium [wstępnie skonfigurowanego rozwiązania połączonej fabryki](https://github.com/Azure/azure-iot-connected-factory) .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat akceleratora połączonej fabryki, zobacz Przewodnik Szybki Start [dotyczący rozwiązania do zarządzania urządzeniami z systemem IoT w chmurze](quickstart-connected-factory-deploy.md).
