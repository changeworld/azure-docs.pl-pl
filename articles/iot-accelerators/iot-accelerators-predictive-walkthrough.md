---
title: Omówienie akceleratora rozwiązania do konserwacji predykcyjnej — Azure | Microsoft Docs
description: Omówienie akceleratora rozwiązań Azure IoT do konserwacji predykcyjnej.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3387996dc0e1953eaafee9c4c61eb8faa865b654
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447538"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>Omówienie akceleratora rozwiązania do konserwacji predykcyjnej

Akcelerator rozwiązania do konserwacji predykcyjnej to kompleksowe rozwiązanie dotyczące scenariusza biznesowego, w którym przewidywany jest moment prawdopodobnego wystąpienia awarii. Ten akcelerator rozwiązania można aktywnie wykorzystać w celu zoptymalizowania konserwacji. Rozwiązanie łączy kluczowe usługi akceleratorów rozwiązań Azure IoT, takich jak usługa IoT Hub i [usługi Azure Machine Learning] [ lnk-machine-learning] obszaru roboczego. Ten obszar roboczy zawiera model oparty na publicznym zestawie przykładowych danych, które umożliwiają prognozowanie pozostałego czasu eksploatacji (RUL, Remaining Useful Life) silnika samolotu. Ponadto w rozwiązaniu w pełni zaimplementowano scenariusz biznesowy IoT jako punkt wyjściowy planowania i wdrażania rozwiązania zgodnego z potrzebami firmy.

Akcelerator rozwiązań konserwacji predykcyjnej [kod jest dostępny w witrynie GitHub](https://github.com/Azure/azure-iot-predictive-maintenance).

## <a name="logical-architecture"></a>Architektura logiczna

Na poniższym diagramie przedstawiono składniki logiczne tego akceleratora rozwiązania:

![Architektura logiczna][img-architecture]

Niebieskie elementy oznaczają usługi platformy Azure zaprowizowane w regionie, w którym wdrożono akcelerator rozwiązania. Lista regionów, w których można wdrożyć akcelerator rozwiązania, znajduje się na [stronie aprowizacji][lnk-azureiotsolutions].

Zielony element oznacza symulowane silników. Więcej informacji na temat tych symulowanych urządzeń można znaleźć w sekcji [Symulowane urządzenia](#simulated-devices).

Szare elementy są składniki z zaimplementowanymi *zarządzania urządzeniami* możliwości. Bieżąca wersja akceleratora rozwiązania do konserwacji predykcyjnej nie umożliwia aprowizowania tych zasobów. Aby dowiedzieć się więcej o zarządzaniu urządzeniami, zapoznaj się [zdalne monitorowanie akcelerator rozwiązań][lnk-remote-monitoring].

## <a name="azure-resources"></a>Zasoby platformy Azure

W portalu Azure przejdź do grupy zasobów z nazwą wybranego rozwiązania, aby wyświetlić aprowizowane zasoby.

![Zasoby akceleratora][img-resource-group]

Po przeprowadzeniu aprowizacji akceleratora rozwiązania otrzymasz wiadomość e-mail z linkiem do obszaru roboczego usługi Machine Learning. Możesz także przejść do obszaru roboczego usługi Machine Learning z [akceleratorów rozwiązań IoT Azure Microsoft] [ lnk-azureiotsolutions] strony. Kafelek jest dostępny na tej stronie, gdy rozwiązanie jest w stanie **Gotowe**.

![Model uczenia maszynowego][img-machine-learning]

## <a name="simulated-devices"></a>Symulowane urządzenia

W akcelerator rozwiązań symulowane urządzenie to silnikowi samolotu. W ramach rozwiązania są aprowizowane dwa silniki (dla jednego samolotu). Każdy silnik emituje cztery rodzaje danych telemetrycznych: Czujnik 9, czujnika 11, czujnika 14 i czujnika 15. czujniki dostarczają dane wymagane przez model usługi Machine Learning do obliczenia pozostałego czasu eksploatacji silnika. Poszczególne symulowane urządzenia wysyłają następujące komunikaty telemetryczne do usługi IoT Hub:

*Liczba cykli*. Cykl jest ukończony lot o długości od 2 do 10 godzin. Podczas lotu dane telemetryczne są przechwytywane co pół godziny.

*Dane telemetryczne*. Istnieją cztery czujniki, służące do rejestrowania silnika. Czujniki te mają ogólne etykiety: czujnik 9, czujnik 11, czujnik 14 i czujnik 15. Te 4 czujniki wysyłają dane telemetryczne, wystarczające do uzyskania przydatnych wyników z modelu pozostałego czasu eksploatacji. Model wykorzystywany w tym akceleratorze rozwiązania został utworzony na podstawie publicznego zestawu danych zawierającego dane z czujników w rzeczywistych silnikach. Aby uzyskać więcej informacji dotyczących tworzenia modelu na podstawie oryginalnego zestawu danych, zobacz [szablon konserwacji predykcyjnej w witrynie Cortana Intelligence Gallery][lnk-cortana-analytics].

Symulowane urządzenia obsługują w rozwiązaniu następujące polecenia wysyłane z centrum IoT:

| Polecenie | Opis |
| --- | --- |
| StartTelemetry |Umożliwia sterowanie stanem symulacji.<br/>Włącza przesyłanie danych telemetrycznych przez urządzenie. |
| StopTelemetry |Umożliwia sterowanie stanem symulacji.<br/>Umożliwia zatrzymanie przesyłania danych telemetrycznych przez urządzenie. |

Usługa IoT Hub udostępnia potwierdzenia poleceń wysyłanych do urządzeń.

## <a name="azure-stream-analytics-job"></a>Zadanie usługi Azure Stream Analytics

**Zadanie: Dane telemetryczne** przetwarza przychodzący strumień danych telemetrycznych urządzenia przy użyciu dwóch instrukcji:

* Pierwsza z nich pobiera wszystkie dane telemetryczne z urządzeń i wysyła je do magazynu obiektów blob. W tym miejscu są wizualizowane w aplikacji sieci web.
* Druga instrukcja oblicza średnie wartości z czujników w ramach przesuwającego się okna czasowego trwającego dwie minuty i wysyła te wartości do **procesora zdarzeń** za pośrednictwem centrum zdarzeń.

## <a name="event-processor"></a>Procesor zdarzeń
**Host procesora zdarzeń** jest uruchamiany w zadaniu sieci Web Azure. **Procesor zdarzeń** przyjmuje średnie wartości z czujników dla ukończonego cyklu. Następnie przekazuje te wartości do trenowanego modelu, który oblicza wartość pozostałego czasu eksploatacji silnika. Interfejs API zapewnia dostęp do modelu w obszarze roboczym usługi Machine Learning, która jest częścią rozwiązania.

## <a name="machine-learning"></a>Usługa Machine Learning
Składnik Machine Learning wykorzystuje model opracowany na podstawie danych zebranych z rzeczywistych silników samolotów. Możesz przejść do obszaru roboczego usługi Machine Learning z poziomu kafelka rozwiązania na [azureiotsolutions.com] [ lnk-azureiotsolutions] strony. Kafelek jest dostępny, gdy rozwiązanie jest w stanie **Gotowe**.

Model usługi Machine Learning jest dostępny jako szablon, który pokazuje, jak pracować z danych telemetrycznych zebranych za pośrednictwem usług akcelerator rozwiązań IoT. Firma Microsoft opracowała [modelu regresji] [ lnk_regression_model] silnika samolotu na podstawie publicznie dostępnych danych<sup>\[1\]</sup>oraz szczegółowe wskazówki na temat używania tego modelu.

Akcelerator rozwiązania Azure IoT do konserwacji predykcyjnej używa modelu regresji utworzonego na podstawie tego szablonu. Model jest wdrażany w Twojej subskrypcji platformy Azure i udostępniane za pośrednictwem automatycznie generowanego interfejsu API. Rozwiązanie to zawiera podzbiór danych testowych 4 (ze 100) aparatów i 4 (z 21) strumieni danych czujników. Te dane wystarczają do uzyskania dokładnego wyniku za pomocą nauczonego modelu.

*\[1\] A. Saxena and K. Goebel (2008). „Turbofan Engine Degradation Simulation Data Set” (Zestaw danych dotyczących symulacji degradacji silnika turbowentylatorowego), repozytorium danych prognostycznych NASA w Ames (https://c3.nasa.gov/dashlink/resources/139/), ośrodek badawczy NASA w Ames, Moffett Field, Kalifornia*

## <a name="next-steps"></a>Kolejne kroki
Po zapoznaniu się z kluczowymi składnikami akceleratora rozwiązania do konserwacji predykcyjnej możesz je dostosować.

Możesz także wypróbować niektóre inne funkcje akceleratorów rozwiązań IoT:

* [Często zadawane pytania dotyczące akceleratorów rozwiązań IoT][lnk-faq]
* [Zabezpieczenia IoT od podstaw][lnk-security-groundup]

[img-architecture]: media/iot-accelerators-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-accelerators-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-accelerators-predictive-walkthrough/machine-learning.png

[lnk-remote-monitoring]: quickstart-predictive-maintenance-deploy.md
[lnk-cortana-analytics]: https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsolutions]: https://www.azureiotsolutions.com/
[lnk-faq]: iot-accelerators-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_regression_model]: https://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
