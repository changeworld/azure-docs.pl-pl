---
title: Omówienie symulacji urządzeń — Platforma Azure | Dokumenty firmy Microsoft
description: Opis akceleratora rozwiązań device simulation i jego możliwości.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: f58eb05ed582cf18157a76f4d637d72a228f4e96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65464867"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Omówienie akceleratora rozwiązań do symulacji urządzeń

W chmurowym rozwiązaniu IoT urządzenia łączą się z punktem końcowym chmury, aby wysyłać dane telemetryczne, takie jak temperatura, lokalizacja i stan. Rozwiązanie zużywa tę telemetrię, umożliwiając podjęcie działań lub uzyskanie szczegółowych informacji z niego.

Podczas opracowywania rozwiązania IoT eksperymenty i testowanie są istotnymi elementami tego procesu. Symulacja jest ważnym narzędziem w całym tym procesie. Za pomocą symulacji urządzenia można:

* Szybko uruchamiaj prototyp, a następnie iteruj, dostosowując symulowane zachowanie urządzenia w locie. Ten proces pozwala udowodnić pomysł przed zainwestowaniem w kosztowny sprzęt. Urządzenia niestandardowe można tworzyć za pośrednictwem interfejsu użytkownika sieci Web, aby wygenerować urządzenie prototypowe w ciągu kilku sekund.
* Sprawdź poprawność rozwiązania działa zgodnie z oczekiwaniami od urządzenia do rozwiązania, symulując rzeczywiste zachowania urządzeń. Można skryptować złożone zachowania urządzeń przy użyciu języka JavaScript do generowania realistycznych symulowanych danych telemetrycznych.
* Skala testuje rozwiązanie, symulując normalne, szczytowe i wykraczające poza szczytowe warunki obciążenia. Testy skalowania pomagają również w odpowiednim rozmiarze zasobów platformy Azure potrzebnych do uruchomienia rozwiązania.

![Przykładowa symulacja drona](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Za pomocą symulacji urządzenia można definiować modele urządzeń, aby symulować rzeczywiste urządzenia. Ten model zawiera formaty wiadomości, właściwości bliźniaczej reprezentacji i metody. Można również symulować złożone zachowania urządzeń za pomocą języka JavaScript.

Można uruchomić symulacje dla jednego do tysięcy urządzeń łączących się z dowolnym centrum IoT hub. Aby ułatwić testowanie, można opcjonalnie wdrożyć centrum IoT hub wraz z symulacji urządzenia dla środowiska autonomicznego.

Symulacja urządzenia jest bezpłatna. Jednak device simulation wdraża do subskrypcji platformy Azure w chmurze i zużywają zasoby platformy Azure. Jeśli symulacja urządzenia nie spełnia twoich wymagań, [kod źródłowy jest również dostępny w usłudze GitHub,](https://github.com/Azure/device-simulation-dotnet) aby można było go skopiować i zmodyfikować.

## <a name="sample-simulations"></a>Przykładowe symulacje

Podczas wdrażania symulacji urządzenia, można uzyskać kilka przykładowych symulacji i przykładowych urządzeń. Możesz użyć tych przykładów, aby dowiedzieć się, jak korzystać z symulacji urządzenia. Aby rozpocząć, uruchom [przykładową symulację, która symuluje 10 ciężarówek.](quickstart-device-simulation-deploy.md) Można również [utworzyć własną symulację przy użyciu jednego z wielu przykładowych urządzeń.](iot-accelerators-device-simulation-create-simulation.md)

![Konfiguracja symulacji](media/iot-accelerators-device-simulation-overview/samplesimulation1.png)

## <a name="custom-simulated-devices"></a>Niestandardowe symulowane urządzenia

Za pomocą symulacji urządzenia można [tworzyć niestandardowe modele urządzeń](iot-accelerators-device-simulation-create-custom-device.md) do użycia w symulacjach. Na przykład można zdefiniować nowy model urządzenia lodówki, który wysyła dane telemetryczne temperatury i wilgotności. Niestandardowe symulowane urządzenia są idealne do prostych zachowań urządzeń z losowymi, przyrostowymi lub dekrementującymi wartościami telemetrycznymi.

![Tworzenie modelu urządzenia](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Zaawansowane symulowane urządzenia

Jeśli potrzebujesz większej kontroli nad wartościami telemetrii wysyłanym przez urządzenie, można użyć zaawansowanego modelu urządzenia. Zaawansowane modele urządzeń umożliwiają obsługę języka JavaScript manipulowania wysłanymi wartościami telemetrycznymi. Na przykład, można symulować temperaturę wewnętrzną zaparkowanego samochodu w upalny słoneczny dzień - wraz ze wzrostem temperatury zewnętrznej temperatura w wewnętrzna wzrasta wykładniczo.

Zaawansowane modele urządzeń umożliwiają [tworzenie i przesyłanie własnych modeli urządzeń,](iot-accelerators-device-simulation-advanced-device.md) które składają się z pliku definicji urządzenia JSON i odpowiednich plików JavaScript.

Zaawansowane modele urządzeń umożliwiają:

* Określ format wiadomości wysyłany z urządzenia wraz z typami telemetrii.
* Użyj skryptów niestandardowych do generowania wartości telemetrycznych, które utrzymują stan urządzenia w czasie.
* Użyj skryptów niestandardowych, aby określić, jak symulowane urządzenie reaguje na metody.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się o akceleratorze rozwiązań device simulation i jego możliwościach. Aby rozpocząć korzystanie z akceleratora rozwiązań, przejdź do szybkiego startu:

> [!div class="nextstepaction"]
> [wdrażanie i uruchamianie symulacji urządzenia IoT na platformie Azure](quickstart-device-simulation-deploy.md)
