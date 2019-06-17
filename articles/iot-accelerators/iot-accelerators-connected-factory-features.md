---
title: Połączone funkcje rozwiązania fabryki — Azure | Dokumentacja firmy Microsoft
description: Omówienie funkcji połączonej fabryki wstępnie skonfigurowanego rozwiązania.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: 2a11640959a8c7fdd0d238aba92698eb47934969
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080445"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Co to jest akcelerator rozwiązań IoT połączona fabryka?

Połączona fabryka jest implementacja architektury referencyjnej przemysłowego Internetu rzeczy Azure firmy Microsoft na rozwiązanie typu open source w pakiecie. Służy jako punkt wyjścia do komercyjnego produktu. Można wdrożyć wstępnie skompilowanych wersji rozwiązania połączonej fabryki do subskrypcji platformy Azure z [akceleratorów rozwiązań Azure IoT](https://www.azureiotsolutions.com/#solutions/types/CF).

![Pulpit nawigacyjny rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-features/dashboard.png)

Akcelerator rozwiązania połączonej fabryki [kod jest dostępny w witrynie GitHub](https://github.com/Azure/azure-iot-connected-factory).

Połączona fabryka obejmuje następujące funkcje:

## <a name="industrial-device-interoperability"></a>Współdziałanie urządzeń przemysłowych

- Połącz zasoby przemysłowe z interfejsem OPC UA.
- Użyj symulowanych linii produkcyjnych (działają serwery OPC UA w kontenerach platformy Docker), aby wyświetlić na żywo dane telemetryczne z nich.
- Przeglądaj modelu informacji OPC UA serwerów OPC UA z pulpitu nawigacyjnego w chmurze.

## <a name="remote-management"></a>Zdalne zarządzanie

- Konfigurowanie zasobów OPC UA za pomocą pulpitu nawigacyjnego w chmurze (wywoływanie metod, odczytywanie i zapisywanie danych).
- Publikuj dane telemetryczne w zasobach OPC UA i cofaj ich publikację za pomocą pulpitu nawigacyjnego w chmurze.

## <a name="cloud-dashboard"></a>Pulpit nawigacyjny w chmurze

- Wyświetl podgląd danych telemetrycznych bezpośrednio na pulpicie nawigacyjnym chmury.
- Wyświetlaj trendy danych telemetrycznych i twórz korelacje za pomocą pulpitu nawigacyjnego eksploratora usługi Time Series Insights.
- Zobacz obliczone ogólnej wydajności sprzętu (OEE) i kluczowe wskaźniki wydajności (KPI) z pulpitu nawigacyjnego w chmurze.
- Widok zasobów przemysłowych hierarchie w topologii drzewa, a także na mapie.
- Wyświetlanie, użytkownik potwierdza i Zamknij alerty z pulpitu nawigacyjnego chmury.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Usługa Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) zaprojektowano pod kątem przechowywania, wizualizacji i wykonywania zapytań dużych ilości danych szeregów czasowych. Połączona fabryka wykorzystuje tej usługi.
- Połączona fabryka integruje się z tym usługa, dzięki czemu można wykonać głębokie, w czasie rzeczywistym analizy danych urządzenia.

## <a name="rules-and-alerts"></a>Reguły i alerty

[Konfigurowanie reguł alertów opartych na próg](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Kompleksowe zabezpieczenia

- Konfiguruj uprawnienia zabezpieczeń dla użytkowników korzystających z kontroli dostępu opartej na rolach.
- End-to-end szyfrowania jest implementowany przy użyciu uwierzytelniania serwera OPC UA (przy użyciu certyfikatów X.509), a także tokenów zabezpieczających.

## <a name="customizability"></a>Dostosowywalności

- Dostosowywanie rozwiązania, aby spełniało określone wymagania biznesowe.
- Pełne rozwiązanie — kod źródłowy dostępny w witrynie GitHub. Zobacz [połączonej fabryki wstępnie skonfigurowanego rozwiązania](https://github.com/Azure/azure-iot-connected-factory) repozytorium.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat akcelerator rozwiązania połączonej fabryki, zobacz samouczek Szybki Start [spróbuj oparte na chmurze rozwiązanie do zarządzania przemysłowych urządzeń IoT](quickstart-connected-factory-deploy.md).
