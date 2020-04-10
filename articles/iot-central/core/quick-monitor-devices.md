---
title: Szybki start — monitorowanie urządzeń w usłudze Azure IoT Central
description: Jako operator dowiedz się, jak monitorować urządzenia za pomocą aplikacji Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 1dec52bbf1435cd7e363edf111f769d3e2cffb6a
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998920"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>Szybki start: monitorowanie urządzeń za pomocą usługi Azure IoT Central

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

Ten przewodnik Szybki start pokazuje, jako operator, jak używać aplikacji Microsoft Azure IoT Central do monitorowania urządzeń i zmiany ustawień.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy wykonać trzy poprzednie przewodniki Szybki start [Tworzenie aplikacji Azure IoT Central](./quick-deploy-iot-central.md), Dodawanie [symulowanego urządzenia do aplikacji IoT Central](./quick-create-simulated-device.md) oraz [konfigurowanie reguł i akcji dla urządzenia.](quick-configure-rules.md)

## <a name="receive-a-notification"></a>Odbieranie powiadomienia

Usługa Azure IoT Central wysyła powiadomienia dotyczące urządzeń jako wiadomości e-mail. Konstruktor dodał regułę wysyłania powiadomień, gdy temperatura w podłączonym czujniku urządzenia przekroczyła próg. Sprawdź wiadomości e-mail wysłane na konto wybrane przez konstruktora do odbierania powiadomień.

Otwórz wiadomość e-mail otrzymaną na końcu [reguł i akcji Konfigurowanie dla szybkiego startu urządzenia.](quick-configure-rules.md) W wiadomości e-mail wybierz łącze do urządzenia:

![Wiadomość e-mail z alertem](media/quick-monitor-devices/email.png)

Widok **Przegląd** symulowanego urządzenia utworzonego w poprzednich przewodnikach Szybki start otwiera się w przeglądarce:

![Urządzenie, które wyzwoliło wiadomość e-mail z powiadomieniem](media/quick-monitor-devices/dashboard.png)

## <a name="investigate-an-issue"></a>Badanie problemu

Jako operator można wyświetlać informacje o urządzeniu w widokach **Przegląd**, **Informacje**i **Polecenia.** Konstruktor utworzył widok **Zarządzaj urządzeniem,** aby edytować informacje o urządzeniu i ustawić właściwości urządzenia.

Wykres na pulpicie nawigacyjnym pokazuje wykreśloną temperaturę urządzenia. Zdecydujesz, że temperatura urządzenia jest zbyt wysoka.

## <a name="remediate-an-issue"></a>Rozwiązywanie problemu

Aby wprowadzić zmiany na urządzeniu, użyj strony **Zarządzanie urządzeniem.**

Zmień **prędkość wentylatora** na 500, aby schłodzić urządzenie. Wybierz **pozycję Zapisz,** aby zaktualizować urządzenie. Gdy urządzenie potwierdzi zmianę ustawień, stan właściwości zmienia się na **zsynchronizowany:**

![Aktualizowanie ustawień](media/quick-monitor-devices/change-settings.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Odbieranie powiadomienia
* Badanie problemu
* Rozwiązywanie problemu

Teraz, gdy wiesz teraz, aby monitorować swoje urządzenie, sugerowanym następnym krokiem jest:

> [!div class="nextstepaction"]
> [Tworzenie szablonu urządzenia i zarządzanie nim](howto-set-up-template.md).
