---
title: Połączone funkcje rozwiązania fabryki — Azure | Dokumentacja firmy Microsoft
description: Omówienie funkcji połączonej fabryki wstępnie skonfigurowanego rozwiązania.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: dobett
ms.openlocfilehash: af2a2c84f9eb420a7ca9a8bd5909cbf856d29a5e
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309199"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Co to jest akcelerator rozwiązań IoT połączona fabryka?

Połączona fabryka jest implementacja architektury referencyjnej przemysłowego Internetu rzeczy Azure firmy Microsoft na rozwiązanie typu open source w pakiecie. Służy jako punkt wyjścia do komercyjnego produktu. Można wdrożyć wstępnie skompilowanych wersji rozwiązania połączonej fabryki do subskrypcji platformy Azure z [akceleratorów rozwiązań Azure IoT](https://www.azureiotsolutions.com/#solutions/types/CF).

![Pulpit nawigacyjny rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-features/dashboard.png)

Akcelerator rozwiązania połączonej fabryki [kod jest dostępny w witrynie GitHub](https://github.com/Azure/azure-iot-connected-factory).

Połączona fabryka obejmuje następujące funkcje:

## <a name="industrial-device-interoperability"></a>Współdziałanie urządzeń przemysłowych

- Nawiązać zasobów przemysłowych za pomocą interfejsu serwera OPC UA.
- Użyj symulowanych linii produkcyjnych (działają serwery OPC UA w kontenerach platformy Docker), aby wyświetlić na żywo dane telemetryczne z nich.
- Przeglądaj modelu informacji OPC UA serwerów OPC UA z pulpitu nawigacyjnego w chmurze.

## <a name="remote-management"></a>Zdalne zarządzanie

- Konfigurowanie zasobów OPC UA na pulpicie nawigacyjnym chmury (wywołanie metody, Odczyt i zapis danych).
- Publikowanie lub anulowanie publikacji danych telemetrycznych z zasobów OPC UA z pulpitu nawigacyjnego w chmurze.

## <a name="cloud-dashboard"></a>Pulpit nawigacyjny rozwiązania cloud

- Wyświetl podgląd danych telemetrycznych bezpośrednio na pulpicie nawigacyjnym chmury.
- Wyświetlanie trendów w danych telemetrycznych i Utwórz korelacji za pomocą pulpitu nawigacyjnego Eksplorator usługi Time Series Insights.
- Zobacz obliczone ogólnej wydajności sprzętu (OEE) i kluczowe wskaźniki wydajności (KPI) z pulpitu nawigacyjnego w chmurze.
- Widok zasobów przemysłowych hierarchie w topologii drzewa, a także na mapie.
- Wyświetlanie, użytkownik potwierdza i Zamknij alerty z pulpitu nawigacyjnego chmury.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Usługa Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) zaprojektowano pod kątem przechowywania, wizualizacji i wykonywania zapytań dużych ilości danych szeregów czasowych. Połączona fabryka wykorzystuje tej usługi.
- Połączona fabryka integruje się z tym usługa, dzięki czemu można wykonać głębokie, w czasie rzeczywistym analizy danych urządzenia.

## <a name="rules-and-alerts"></a>Reguły i alerty

[Konfigurowanie reguł alertów opartych na próg](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Zabezpieczenia end-to-end

- Konfigurowania uprawnień zabezpieczeń dla użytkowników za pomocą kontroli dostępu opartej na rolach (RBAC).
- End-to-end szyfrowania jest implementowany przy użyciu uwierzytelniania serwera OPC UA (przy użyciu certyfikatów X.509), a także tokenów zabezpieczających.

## <a name="customizability"></a>Dostosowywalności

- Dostosowywanie rozwiązania, aby spełniało określone wymagania biznesowe.
- Pełne rozwiązanie — kod źródłowy dostępny w witrynie GitHub. Zobacz [połączonej fabryki wstępnie skonfigurowanego rozwiązania](https://github.com/Azure/azure-iot-connected-factory) repozytorium.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o rozwiązaniu połączonej fabryki, które są wstępnie skonfigurowane, czytając następujące artykuły:

* [We wstępnie skonfigurowanym Przewodnik po rozwiązaniu połączonej fabryki](iot-accelerators-connected-factory-sample-walkthrough.md)
* [Wdrażanie bramy dla połączonej fabryki]( iot-accelerators-connected-factory-gateway-deployment.md)
