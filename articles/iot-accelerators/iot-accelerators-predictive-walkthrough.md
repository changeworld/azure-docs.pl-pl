---
title: Omówienie akceleratora rozwiązania do konserwacji predykcyjnej — Azure | Microsoft Docs
description: Omówienie akceleratora rozwiązań Azure IoT do konserwacji predykcyjnej.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: dobett
ms.openlocfilehash: 822e02d42be8ce516901190af4be579d13ac841d
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888326"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>Omówienie akceleratora rozwiązania do konserwacji predykcyjnej

Akcelerator rozwiązania do konserwacji predykcyjnej to kompleksowe rozwiązanie dotyczące scenariusza biznesowego, w którym przewidywany jest moment prawdopodobnego wystąpienia awarii. Ten akcelerator rozwiązania można aktywnie wykorzystać w celu zoptymalizowania konserwacji. To rozwiązanie łączy kluczowe usługi akceleratorów rozwiązań Azure IoT, takie jak usługa IoT Hub, analiza strumienia oraz obszar roboczy usługi [Azure Machine Learning][lnk-machine-learning]. Ten obszar roboczy zawiera model oparty na publicznym zestawie przykładowych danych, które umożliwiają prognozowanie pozostałego czasu eksploatacji (RUL, Remaining Useful Life) silnika samolotu. Ponadto w rozwiązaniu w pełni zaimplementowano scenariusz biznesowy IoT jako punkt wyjściowy planowania i wdrażania rozwiązania zgodnego z potrzebami firmy.

## <a name="logical-architecture"></a>Architektura logiczna

Na poniższym diagramie przedstawiono składniki logiczne tego akceleratora rozwiązania:

![][img-architecture]

Niebieskie elementy oznaczają usługi platformy Azure zaprowizowane w regionie, w którym wdrożono akcelerator rozwiązania. Lista regionów, w których można wdrożyć akcelerator rozwiązania, znajduje się na [stronie aprowizacji][lnk-azureiotsuite].

Zielony element oznacza symulowane urządzenie, które odpowiada silnikowi samolotu. Więcej informacji na temat tych symulowanych urządzeń można znaleźć w sekcji [Symulowane urządzenia](#simulated-devices).

Szare elementy oznaczają składniki z zaimplementowanymi funkcjami *zarządzania urządzeniami*. Bieżąca wersja akceleratora rozwiązania do konserwacji predykcyjnej nie umożliwia aprowizowania tych zasobów. Aby dowiedzieć się więcej o zarządzaniu urządzeniami, zapoznaj się [zdalne monitorowanie akcelerator rozwiązań][lnk-remote-monitoring].

## <a name="azure-resources"></a>Zasoby platformy Azure

W portalu Azure przejdź do grupy zasobów z nazwą wybranego rozwiązania, aby wyświetlić aprowizowane zasoby.

![Zasoby akceleratora][img-resource-group]

Po przeprowadzeniu aprowizacji akceleratora rozwiązania otrzymasz wiadomość e-mail z linkiem do obszaru roboczego usługi Machine Learning. Możesz także przejść do obszaru roboczego usługi Machine Learning z [akceleratorów rozwiązań IoT Azure Microsoft] [ lnk-azureiotsuite] stronę swojego aprowizowanego rozwiązania. Kafelek jest dostępny na tej stronie, gdy rozwiązanie jest w stanie **Gotowe**.

![Model uczenia maszynowego][img-machine-learning]

## <a name="simulated-devices"></a>Symulowane urządzenia

W tym akceleratorze rozwiązania symulowane urządzenie odpowiada silnikowi samolotu. W ramach rozwiązania są aprowizowane dwa silniki (dla jednego samolotu). Każdy silnik emituje cztery rodzaje danych telemetrycznych: z czujnika 9, czujnika 11, czujnika 14 i czujnika 15. Czujniki dostarczają dane wymagane przez model usługi Machine Learning do obliczenia pozostałego czasu eksploatacji silnika. Poszczególne symulowane urządzenia wysyłają następujące komunikaty telemetryczne do usługi IoT Hub:

*Liczba cykli*. Cykl reprezentuje ukończony lot o długości od 2 do 10 godzin. Podczas lotu dane telemetryczne są przechwytywane co pół godziny.

*Dane telemetryczne*. Są cztery czujniki, które reprezentują parametry silnika. Czujniki te mają ogólne etykiety: czujnik 9, czujnik 11, czujnik 14 i czujnik 15. Te 4 czujniki reprezentują dane telemetryczne, które są wystarczające do uzyskania przydatnych wyników z modelu pozostałego czasu eksploatacji silnika. Model wykorzystywany w tym akceleratorze rozwiązania został utworzony na podstawie publicznego zestawu danych zawierającego dane z czujników w rzeczywistych silnikach. Aby uzyskać więcej informacji dotyczących tworzenia modelu na podstawie oryginalnego zestawu danych, zobacz [szablon konserwacji predykcyjnej w witrynie Cortana Intelligence Gallery][lnk-cortana-analytics].

Symulowane urządzenia obsługują w rozwiązaniu następujące polecenia wysyłane z centrum IoT:

| Polecenie | Opis |
| --- | --- |
| StartTelemetry |Umożliwia sterowanie stanem symulacji.<br/>Włącza przesyłanie danych telemetrycznych przez urządzenie. |
| StopTelemetry |Umożliwia sterowanie stanem symulacji.<br/>Umożliwia zatrzymanie przesyłania danych telemetrycznych przez urządzenie. |

Usługa IoT Hub udostępnia potwierdzenia poleceń wysyłanych do urządzeń.

## <a name="azure-stream-analytics-job"></a>Zadanie usługi Azure Stream Analytics

**Zadanie Telemetria** przetwarza strumień danych telemetrycznych przychodzących z urządzeń przy użyciu dwóch instrukcji:

* Pierwsza z nich pobiera wszystkie dane telemetryczne z urządzeń i wysyła je do magazynu obiektów blob. Stamtąd trafiają one do aplikacji internetowej w celu wizualizacji.
* Druga instrukcja oblicza średnie wartości z czujników w ramach przesuwającego się okna czasowego trwającego dwie minuty i wysyła te wartości do **procesora zdarzeń** za pośrednictwem centrum zdarzeń.

## <a name="event-processor"></a>Procesor zdarzeń
**Host procesora zdarzeń** jest uruchamiany w zadaniu sieci Web Azure. **Procesor zdarzeń** przyjmuje średnie wartości z czujników dla ukończonego cyklu. Następnie przekazuje te wartości do interfejsu API, który dostarcza je nauczonemu modelowi w celu obliczenia pozostałego czasu eksploatacji silnika. Interfejs API jest udostępniany przez obszar roboczy usługi Machine Learning, który jest aprowizowany w ramach rozwiązania.

## <a name="machine-learning"></a>Usługa Machine Learning
Składnik Machine Learning wykorzystuje model opracowany na podstawie danych zebranych z rzeczywistych silników samolotów. Do obszaru roboczego usługi Machine Learning możesz przejść z poziomu kafelka rozwiązania na stronie [azureiotsuite.com][lnk-azureiotsuite]. Kafelek jest dostępny, gdy rozwiązanie jest w stanie **Gotowe**.

Model usługi Azure Machine Learning jest dostępny jako szablon na demonstrację działania funkcji z urządzeń danych telemetrycznych zebranych za pośrednictwem usług akceleratorów rozwiązań IoT. Firma Microsoft opracowała [modelu regresji] [ lnk_regression_model] silnika samolotu na podstawie publicznie dostępnych danych<sup>\[1\]</sup>oraz szczegółowe wskazówki na temat używania tego modelu.

Akcelerator rozwiązania Azure IoT do konserwacji predykcyjnej używa modelu regresji utworzonego na podstawie tego szablonu. Model jest wdrożony w ramach subskrypcji platformy Azure i dostępny za pośrednictwem automatycznie generowanego interfejsu API. Rozwiązanie to zawiera podzbiór danych testowych odpowiadających 4 ze 100 silników oraz strumieniom danych z 4 z 21 czujników. Te dane wystarczają do uzyskania dokładnego wyniku za pomocą nauczonego modelu.

*\[1\] A. Saxena and K. Goebel (2008). „Turbofan Engine Degradation Simulation Data Set” (Zestaw danych dotyczących symulacji degradacji silnika turbowentylatorowego), repozytorium danych prognostycznych NASA w Ames (https://c3.nasa.gov/dashlink/resources/139/), ośrodek badawczy NASA w Ames, Moffett Field, Kalifornia*

## <a name="next-steps"></a>Kolejne kroki
Po zapoznaniu się z kluczowymi składnikami akceleratora rozwiązania do konserwacji predykcyjnej możesz je dostosować.

Możesz także wypróbować niektóre inne funkcje i możliwości akceleratorów rozwiązań IoT:

* [Często zadawane pytania dotyczące akceleratorów rozwiązań IoT][lnk-faq]
* [Zabezpieczenia IoT od podstaw][lnk-security-groundup]

[img-architecture]: media/iot-accelerators-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-accelerators-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-accelerators-predictive-walkthrough/machine-learning.png

[lnk-remote-monitoring]: quickstart-predictive-maintenance-deploy.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsolutions.com/
[lnk-faq]: iot-accelerators-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
