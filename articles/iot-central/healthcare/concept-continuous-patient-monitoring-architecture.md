---
title: Architektura ciągłego monitorowania pacjentów w usłudze Azure IoT Central | Dokumenty firmy Microsoft
description: Dowiedz się więcej o architekturze rozwiązania do ciągłego monitorowania pacjentów.
author: philmea
ms.author: philmea
ms.date: 10/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 92eb4157abb55b7056952d1fb064c7c7d7500335
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77021700"
---
# <a name="continuous-patient-monitoring-architecture"></a>Architektura ciągłego monitorowania pacjenta



Rozwiązania do ciągłego monitorowania pacjentów można sbudować przy użyciu dostarczonego szablonu aplikacji i przy użyciu architektury opisanej poniżej jako wskazówki.

>[!div class="mx-imgBorder"] 
>![Architektura CPM](media/cpm-architecture.png)

1. Urządzenia medyczne komunikujące się za pomocą Bluetooth Low Energy (BLE)
1. Brama telefonii komórkowej odbierająca dane BLE i wysyłająca do IoT Central
1. Ciągły eksport danych o kondycji pacjenta do interfejsu API platformy Azure dla FHIR&reg;
1. Uczenie maszynowe oparte na interoperacyjnych danych
1. Pulpit nawigacyjny zespołu opieki zbudowany na danych FHIR

## <a name="details"></a>Szczegóły
W tej sekcji opisano każdą część diagramu architektury bardziej szczegółowo.

### <a name="ble-medical-devices"></a>Wyroby medyczne BLE
Wiele urządzeń do noszenia medycznych używanych w przestrzeni IoT w służbie zdrowia to urządzenia Bluetooth Low Energy. Nie są w stanie mówić bezpośrednio do chmury i będą musieli przejść przez bramę. Ta architektura sugeruje użycie aplikacji na telefon komórkowy jako tej bramy.

### <a name="mobile-phone-gateway"></a>Brama telefonii komórkowej
Podstawową funkcją aplikacji telefonu komórkowego jest łonowanie danych BLE z urządzeń medycznych i przekazywanie ich do usługi Azure IoT Central. Ponadto aplikacja może pomóc w prowadzeniu pacjentów przez konfigurację urządzenia i przepływ aprowizacji i pomóc im zobaczyć widok ich osobistych danych zdrowotnych. Inne rozwiązania mogą używać bramy tabletu lub bramy statycznej, jeśli wewnątrz sali szpitalowej, aby osiągnąć ten sam przepływ komunikacji.

### <a name="export-to-azure-api-for-fhirreg"></a>Eksportowanie do interfejsu API platformy Azure dla FHIR&reg;
Usługa Azure IoT Central jest zgodna&reg; ze standardem HIPAA i certyfikatEM HITRUST, ale możesz również wysłać dane dotyczące kondycji pacjenta do interfejsu API platformy Azure dla FHIR. [Interfejs API platformy Azure dla FHIR](../../healthcare-apis/overview.md) to w pełni zarządzany, zgodny ze standardami, zgodny interfejs API dla danych o kondycji klinicznej, który umożliwia tworzenie nowych systemów zaangażowania danych o kondycji. Umożliwia szybką wymianę danych za pośrednictwem interfejsów API FHIR, wspieranych przez zarządzaną usługę Platform-as-a Service (PaaS) oferującą w chmurze. Korzystając z funkcji ciągłego eksportowania danych usługi IoT Central, można wysyłać dane do interfejsu API platformy Azure dla FHIR.

### <a name="machine-learning"></a>Uczenie maszynowe
Po agregacji danych i przetłumaczeniu ich na format FHIR można tworzyć modele uczenia maszynowego, które mogą wzbogacić szczegółowe informacje i umożliwić inteligentniejsze podejmowanie decyzji przez zespół opieki. Istnieją różne rodzaje usług, które mogą służyć do tworzenia, uczenia i wdrażania modeli uczenia maszynowego. Więcej informacji na temat korzystania z oferty uczenia maszynowego platformy Azure można znaleźć w naszej [dokumentacji uczenia maszynowego.](../../machine-learning/index.yml)

### <a name="provider-dashboard"></a>Pulpit nawigacyjny dostawcy
Dane znajdujące się w interfejsie API platformy Azure dla FHIR mogą służyć do tworzenia pulpitu nawigacyjnego szczegółowych informacji o pacjencie lub mogą być bezpośrednio zintegrowane z EMR, aby pomóc zespołom opieki w wizualizacji stanu pacjenta. Zespoły opieki mogą korzystać z tej deski rozdzielczej, aby dbać o pacjentów potrzebujących pomocy i wykrywać wczesne oznaki pogorszenia. Aby dowiedzieć się, jak utworzyć pulpit nawigacyjny dostawcy usługi Power BI w czasie rzeczywistym, postępuj zgodnie z naszym [przewodnikiem.](howto-health-data-triage.md)

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się, jak wdrożyć szablon aplikacji do ciągłego monitorowania pacjentów](tutorial-continuous-patient-monitoring.md)