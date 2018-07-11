---
title: Konfigurowanie modelu niestandardowego urządzenia — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania modelu niestandardowego urządzenia w symulacji urządzenia akceleratora rozwiązań.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/06/2018
ms.topic: conceptual
ms.openlocfilehash: 61a7c5314df541b4b44a286efe982f4bf93256e3
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967536"
---
# <a name="device-models"></a>Modele urządzeń

Po skonfigurowaniu symulacji, możesz użyć istniejącego modelu urządzenia ze wstępnie zdefiniowanej Ustaw czujników, lub Utwórz model urządzenia niestandardowe Dzięki różnorodnym symulowanych czujników. Niestandardowymi czujnikami pozwalają dokładnie modelowały rzeczywistych urządzeniach.

## <a name="pre-configured-device-models"></a>Modele wstępnie skonfigurowanych urządzeń

Symulacja urządzenia akcelerator rozwiązań udostępnia trzy modele wstępnie skonfigurowane urządzenia: chłodniach windy i ciężarówek.

Modele wstępnie skonfigurowane urządzenia mają wiele czujników przy użyciu zaawansowanych zachowania zdefiniowanego w pliku JavaScript. Nie można skonfigurować te zachowania w internetowym interfejsie użytkownika.

W poniższej tabeli przedstawiono konfiguracje dla każdego modelu wstępnie skonfigurowane urządzenia:

| Model urządzenia  | Czujnik           | Jednostka  |
| ------------- | ---------------- | ----- |
| Chłodnica       | wilgotność         | %     |
|               | pressure         | psig  |
|               | temperature      | F     |
| Krótka prezentacja      | FLOOR            |       |
|               | Wibracje        | mm    |
|               | Temperatura      | F     |
| Truck         | Szerokość geograficzna         |       |
|               | Długość geograficzna        |       |
|               | szybkość            | mil na godzinę   |
|               | cargotemperature | F     |

## <a name="custom-device-models"></a>Modele urządzeń niestandardowych

Modele niestandardowe umożliwiają modelu czujniki, które bardziej dokładnie modelowały własnych urządzeń. Niestandardowe urządzenie może mieć maksymalnie 10 niestandardowymi czujnikami.

Po wybraniu typu modelu urządzenia niestandardowe, Dodaj nowe czujniki, klikając **+ Dodaj czujnika**.

[![Konfigurowanie czujników](./media/iot-accelerators-device-simulation-custom-model/configuresensors-inline.png)](./media/iot-accelerators-device-simulation-custom-model/configuresensors-expanded.png#lightbox)

Niestandardowymi czujnikami mają następujące właściwości:

| Pole     | Opis |
| --------- | ----------- |
| Nazwy czujnika | Przyjazna nazwa dla czujnika, takie jak **temperatury** lub **szybkość**.  |
| Zachowanie  | Zachowania Włącz dane telemetryczne będzie się różnić od jeden komunikat do kolejnego, aby symulować rzeczywiste dane. **Inkrementacja** zwiększa wartość o jeden w każdy komunikat wysyłany zaczynając od wartości minimalnej. Po osiągnięciu maksymalnej wartości, następnie rozpoczyna się za pośrednictwem ponownie na wartość minimalna. **Dekrementacja** zachowuje się w taki sam sposób jak **przyrostu** , ale liczy w dół. **Random** zachowanie generuje losową wartość z zakresu od wartości minimalnej i maksymalnej wartości. |
| Wartość minimalna | Najniższy numer z dopuszczalnego zakresu. |
| Wartość maksymalna | Największa liczba w swój dopuszczalny zakres. |
| Jednostka      | Jednostka miary dla czujnika, takich jak ° F lub mil na godzinę. |

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku wyjaśniono sposób konfigurowania model urządzenia niestandardowe symulacji. Następnie możesz chcieć poznać innych [akceleratorów rozwiązań IoT](about-iot-accelerators.md).