---
title: Połączone fabryki rozwiązania funkcje — Azure | Dokumentacja firmy Microsoft
description: Omówienie funkcji fabryki połączone wstępnie skonfigurowane rozwiązanie.
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2018
ms.author: dobett
ms.openlocfilehash: e0bd023e73088897303fe0f9de47641cd6ea0389
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-azure-iot-suite-connected-factory"></a>Co to jest Azure IoT Suite połączone fabryki?

Fabryka połączenia jest implementacja firmy Microsoft Azure przemysłowych architektura referencyjna IoT platformy, umieszczone na rozwiązanie open source. Można użyć jej jako punkt początkowy dla produktu handlowego. Można wdrożyć wbudowanych wersji rozwiązania fabryki podłączone do Twojej subskrypcji platformy Azure z [pakiet IoT Azure](https://www.azureiotsuite.com/#solutions/types/CF).

![Pulpit nawigacyjny rozwiązania połączonych fabryki](media/iot-suite-connected-factory-features/dashboard.png)

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

[Konfigurowanie reguł alerty oparte na wartościach progowych](iot-suite-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Zabezpieczeń na trasie

- Konfigurowania uprawnień zabezpieczeń dla użytkowników za pomocą kontroli dostępu opartej na rolach (RBAC).
- Szyfrowanie na całej trasie jest zaimplementowana przy użyciu uwierzytelniania OPC UA (przy użyciu certyfikatów X.509), a także tokenów zabezpieczających.

## <a name="customizability"></a>Dostosowywalności

- [Dostosowywanie](iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md) rozwiązanie spełnia określone wymagania biznesowe.
- Rozwiązanie pełnego kodu źródłowego dostępne w witrynie GitHub. Zobacz [połączone fabryki wstępnie skonfigurowane rozwiązanie](https://github.com/Azure/azure-iot-connected-factory) repozytorium.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o rozwiązaniu połączone fabryki wstępnie przeczytaj następujące artykuły:

* [Fabryka połączonych wstępnie skonfigurowane rozwiązanie wskazówki](iot-suite-connected-factory-sample-walkthrough.md)
* [Wdrażanie bramy dla fabryki połączone]( iot-suite-connected-factory-gateway-deployment.md)
