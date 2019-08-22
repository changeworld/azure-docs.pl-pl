---
title: Monitorowanie urządzeń w usłudze Azure IoT Central | Microsoft Docs
description: Jako operator możesz monitorować urządzenia za pomocą aplikacji usługi Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: cf50fd80bdbce5895bd1da39700d1c6e4cdcc230
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878117"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices-preview-features"></a>Samouczek: Monitorowanie urządzeń za pomocą usługi Azure IoT Central (funkcje w wersji zapoznawczej)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

W tym samouczku przedstawiono, jak operator może użyć aplikacji usługi Microsoft Azure IoT Central do monitorowania urządzeń i zmieniania ustawień.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Odbieranie powiadomienia
> * Badanie problemu
> * Rozwiązywanie problemu

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem konstruktor powinien ukończyć trzy samouczki przeznaczone dla konstruktorów w celu utworzenia aplikacji usługi Azure IoT Central:

* [Definiowanie nowego typu urządzenia](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [Dodawanie urządzenia](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [Konfigurowanie reguł i akcji dla urządzenia](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

## <a name="receive-a-notification"></a>Odbieranie powiadomienia

Usługa Azure IoT Central wysyła powiadomienia dotyczące urządzeń jako wiadomości e-mail. Konstruktor dodał regułę wysyłania powiadomienia, gdy temperatura w podłączonym urządzeniu czujnika środowiska przekroczyła wartość progową. Sprawdź wiadomości e-mail wysłane na konto wybrane przez konstruktora do odbierania powiadomień.

Otwórz wiadomość e-mail odebraną na końcu samouczka [Konfigurowanie reguł i akcji dla urządzenia](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json). W wiadomości e-mail wybierz link do urządzenia:

![Wiadomość e-mail z alertem](media/tutorial-monitor-devices-pnp/email.png)

W przeglądarce zostanie otwarty widok **pulpitu nawigacyjnego** symulowanego urządzenia z czujnikiem środowiska utworzonego w poprzednich samouczkach:

![Urządzenie, które wyzwoliło wiadomość e-mail z powiadomieniem](media/tutorial-monitor-devices-pnp/dashboard.png)

## <a name="investigate-an-issue"></a>Badanie problemu

Jako operator można wyświetlić informacje o urządzeniu na **pulpicie nawigacyjnym**, **właściwościach czujnika środowiska**i stronach **poleceń** . Konstruktor dostosował strony właściwości **pulpitu nawigacyjnego** i **czujnika środowiska** w celu wyświetlenia ważnych informacji o podłączonym urządzeniu czujnika środowiska.

Wybierz widok **Pulpit nawigacyjny**, aby wyświetlić informacje o urządzeniu.

Wykres na pulpicie nawigacyjnym pokazuje wykreśloną temperaturę urządzenia. Bieżącą temperaturę docelową urządzenia można także zobaczyć na kafelku **Właściwości urządzenia** . Decydujesz, że temperatura docelowa jest zbyt wysoka.

## <a name="remediate-an-issue"></a>Rozwiązywanie problemu

Aby wprowadzić zmianę na urządzeniu, użyj strony **Właściwości czujnika środowiska** :

1. Wybierz **Właściwości czujnika środowiska**. Zmień **poziom jasności** na 10. Wybierz pozycję **Zapisz** , aby zaktualizować urządzenie. Gdy urządzenie potwierdzi zmianę ustawień, stan właściwości zmieni się na **zsynchronizowany**:

    ![Aktualizuj ustawienia](media/tutorial-monitor-devices-pnp/change-settings.png)

2. Wybierz pozycję **pulpit nawigacyjny** i Sprawdź nową wartość właściwości:

    ![Zaktualizowany pulpit nawigacyjny urządzenia](media/tutorial-monitor-devices-pnp/new-settings.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Odbieranie powiadomienia
* Badanie problemu
* Rozwiązywanie problemu

Teraz, gdy teraz wiesz już, jak monitorować urządzenie, sugerowanym następnym krokiem jest:

> [!div class="nextstepaction"]
> [Konfigurowanie reguł i akcji dla urządzenia](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).