---
title: Przegląd symulacji urządzenia — Azure | Dokumentacja firmy Microsoft
description: Opis akcelerator rozwiązań symulacji urządzenia i jego możliwości.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: f58eb05ed582cf18157a76f4d637d72a228f4e96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65464867"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Omówienie akceleratora rozwiązań do symulacji urządzeń

Rozwiązania IoT opartych na chmurze urządzenia łączą się z punktu końcowego w chmurze do wysyłania danych telemetrycznych, takich jak temperatury, lokalizacja i stan. Rozwiązanie używa tych danych telemetrycznych, dzięki któremu można wykonać akcje lub dziedziczyć po nim szczegółowych informacji.

Podczas opracowywania rozwiązania IoT eksperymentowania i testowanie są podstawowe części procesu. Symulacja jest ważnym narzędziem w trakcie tego procesu. Za pomocą symulacji urządzenia możesz wykonywać następujące czynności:

* Szybkie rozpoczęcie prototyp, działanie i wejść, dostosowując symulowane urządzenie zachowanie na bieżąco. Ten proces umożliwia okazać się koncepcja przed inwestowania w sprzęt kosztowne. Możesz utworzyć niestandardowe urządzeniami za pośrednictwem internetowego interfejsu użytkownika, aby wygenerować urządzenia prototypu w ciągu kilku sekund.
* Sprawdź, czy urządzenie i rozwiązanie działają zgodnie z oczekiwaniami, symulując zachowania rzeczywistego urządzenia. Można tworzyć skrypty dla zachowania złożonych urządzenia, które można wygenerować realistyczne symulowanych danych telemetrycznych przy użyciu języka JavaScript.
* Skala przetestowanie rozwiązania przez symulowanie szczytu normalne oraz wiele innych warunkach obciążenia szczytowego. Testy skalowania również pomóc wielkości zasobów platformy Azure potrzebnych do uruchomienia rozwiązania.

![Symulacja drona próbki](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Za pomocą symulacji urządzenia można zdefiniować modele urządzeń, aby symulować rzeczywiste urządzenia. Ten model zawiera formaty wiadomości, bliźniacza reprezentacja właściwości i metody. Można także symulować urządzenie złożonych zachowań za pomocą języka JavaScript.

Możesz uruchomić symulacje, jednego do tysięcy urządzeń nawiązujących połączenie z dowolnej usługi IoT hub. Aby ułatwić testowanie, opcjonalnie można wdrożyć Centrum IoT, wraz z symulacji urządzenia środowisku autonomicznym.

Symulacja urządzenia jest bezpłatne. Jednak symulacji urządzenia wdraża do subskrypcji platformy Azure w chmurze i używanie zasobów platformy Azure. Jeśli symulacji urządzenia nie spełniają Twoich wymagań, [kod źródłowy jest również dostępna w witrynie GitHub](https://github.com/Azure/device-simulation-dotnet) skopiować i zmodyfikować.

## <a name="sample-simulations"></a>Symulacje próbki

Podczas wdrażania symulacji urządzenia, możesz korzystać z niektórych przykładowej symulacji i przykładowe urządzenia. Te przykłady można użyć, aby dowiedzieć się, jak używać symulacji urządzenia. Aby rozpocząć pracę, należy uruchomić [przykładowy symulacji, która symuluje sieć 10 ciężarówek](quickstart-device-simulation-deploy.md). Możesz również [własne symulację przy użyciu jednego z wielu urządzeń próbki, pod warunkiem](iot-accelerators-device-simulation-create-simulation.md).

![Konfiguracja symulacji](media/iot-accelerators-device-simulation-overview/samplesimulation1.png)

## <a name="custom-simulated-devices"></a>Niestandardowe symulowanych urządzeń

Można użyć do symulacji urządzenia [tworzyć modele niestandardowe](iot-accelerators-device-simulation-create-custom-device.md) do użycia w swoje symulacje. Na przykład można zdefiniować nowego modelu urządzenia lodówki, który wysyła dane telemetryczne dotyczące temperatury i wilgotności. Niestandardowe symulowane urządzenia są idealne dla zachowania proste urządzenia za pomocą losowych, zwiększanie lub zmniejszanie wartości telemetryczne.

![Tworzenie modelu urządzenia](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Zaawansowany symulowane urządzenia

Jeśli potrzebujesz większej kontroli nad wartości telemetryczne, które urządzenie wysyła, można użyć modelu urządzenia zaawansowane. Modele urządzeń zaawansowane Włącz obsługę języka JavaScript do manipulowania wartością wysłane dane telemetryczne. Na przykład można symulować posługiwanie się nimi temperatury samochodu zaparkowane w warstwie gorąca słoneczny dzień — jako wzrost temperatury zewnętrznej, posługiwanie się nimi temperatury zwiększa się wykładniczo.

Modele zaawansowanych urządzeń umożliwiają [utworzenia i przekazania własnych modeli urządzeń](iot-accelerators-device-simulation-advanced-device.md) składających się z definicji urządzenia JSON plików i odpowiadające im pliki JavaScript.

Modele zaawansowanych urządzeń pozwalają na:

* Określ format wiadomości wysyłanych z urządzenia oraz typy telemetrii.
* Użyj niestandardowego skryptu, aby wygenerować wartości telemetryczne, które zarządzania stanem urządzenia wraz z upływem czasu.
* Użyj niestandardowych skryptów, aby określić, jak symulowane urządzenie odpowiada na metody.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule omówiono akcelerator rozwiązań symulacji urządzenia i jego możliwości. Aby rozpocząć pracę, przy użyciu akcelerator rozwiązań, przejdź do przewodnika Szybki Start:

> [!div class="nextstepaction"]
> [Wdrażanie i uruchamianie symulacji urządzenia IoT na platformie Azure](quickstart-device-simulation-deploy.md)
