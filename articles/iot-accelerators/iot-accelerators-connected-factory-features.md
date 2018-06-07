---
title: Połączone fabryki rozwiązania funkcje — Azure | Dokumentacja firmy Microsoft
description: Omówienie funkcji fabryki połączone wstępnie skonfigurowane rozwiązanie.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: dobett
ms.openlocfilehash: 3478217771418ab31772d6a42a7ed8d8a2e8069a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626500"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Co to jest podłączony IoT fabryki akcelerator rozwiązań?

Fabryka połączenia jest implementacja firmy Microsoft Azure przemysłowych architektura referencyjna IoT platformy, umieszczone na rozwiązanie open source. Można użyć jej jako punkt początkowy dla produktu handlowego. Można wdrożyć wbudowanych wersji rozwiązania fabryki podłączone do Twojej subskrypcji platformy Azure z [akceleratorów rozwiązania Azure IoT](https://www.azureiotsolutions.com/#solutions/types/CF).

![Pulpit nawigacyjny rozwiązania połączonych fabryki](./media/iot-accelerators-connected-factory-features/dashboard.png)

Fabryka połączonych obejmuje następujące funkcje:

## <a name="industrial-device-interoperability"></a>Współdziałanie przemysłowych urządzenia

- Nawiązać przemysłowych zasoby z interfejsem OPC UA.
- Użyj symulowanego liniach (uruchamiania OPC UA serwerów w kontenerach Docker), aby wyświetlić na żywo dane telemetryczne z nich.
- Przeglądaj modelu informacji OPC UA serwerów OPC UA z pulpitu nawigacyjnego chmury.

## <a name="remote-management"></a>Zdalne zarządzanie

- Konfigurowanie zasobów OPC UA z poziomu pulpitu nawigacyjnego chmury (wywołania metody, odczytywania i zapisywania danych).
- Publish and unpublish lub dane telemetryczne z zasobów OPC UA z pulpitu nawigacyjnego chmury.

## <a name="cloud-dashboard"></a>Pulpit nawigacyjny w chmurze

- Wyświetl podgląd danych telemetrycznych bezpośrednio w chmurze pulpitu nawigacyjnego.
- Wyświetlanie trendów w danych telemetrycznych i Utwórz korelacji przy użyciu pulpitu nawigacyjnego Eksplorator Insights serii czasu.
- Zobacz obliczeniowej ogólną wydajność sprzętu (OEE) oraz kluczowych wskaźników wydajności (KPI) z pulpitu nawigacyjnego chmury.
- Hierarchie zasobów przemysłowych widoku w topologii drzewa, a także na mapie interaktywnego.
- Wyświetlanie, potwierdzić i zamknąć alerty z pulpitu nawigacyjnego chmury.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure Insights serii czasu](../time-series-insights/time-series-insights-overview.md) zaprojektowano pod kątem przechowywania, wizualizowanie i badania dużych ilości danych szeregu czasowego. Połączone wykorzystanie fabryki tej usługi.
- Fabryka połączonych integruje się z tym usługa, co pozwala wykonywać analizy głębokie, w czasie rzeczywistym danych urządzenia.

## <a name="rules-and-alerts"></a>Zasady i alerty

[Konfigurowanie reguł alerty oparte na wartościach progowych](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Zabezpieczeń na trasie

- Konfigurowania uprawnień zabezpieczeń dla użytkowników za pomocą kontroli dostępu opartej na rolach (RBAC).
- Szyfrowanie na całej trasie jest zaimplementowana przy użyciu uwierzytelniania OPC UA (przy użyciu certyfikatów X.509), a także tokenów zabezpieczających.

## <a name="customizability"></a>Dostosowywalności

- Dostosowywanie rozwiązanie spełnia określone wymagania biznesowe.
- Rozwiązanie pełnego kodu źródłowego dostępne w witrynie GitHub. Zobacz [połączone fabryki wstępnie skonfigurowane rozwiązanie](https://github.com/Azure/azure-iot-connected-factory) repozytorium.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o rozwiązaniu połączone fabryki wstępnie przeczytaj następujące artykuły:

* [Fabryka połączonych wstępnie skonfigurowane rozwiązanie wskazówki](iot-accelerators-connected-factory-sample-walkthrough.md)
* [Wdrażanie bramy dla połączonej fabryki]( iot-accelerators-connected-factory-gateway-deployment.md)
