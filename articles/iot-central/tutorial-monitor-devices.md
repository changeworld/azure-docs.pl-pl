---
title: Monitorowanie urządzeń w usłudze Azure IoT Central | Microsoft Docs
description: Jako operator możesz monitorować urządzenia za pomocą aplikacji usługi Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: d716eb761ab406b65f10898b29775327a801ac45
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69875490"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices"></a>Samouczek: Korzystanie z usługi Azure IoT Central do monitorowania urządzeń

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

W tym samouczku przedstawiono, jak operator może użyć aplikacji usługi Microsoft Azure IoT Central do monitorowania urządzeń i zmieniania ustawień.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Odbieranie powiadomienia
> * Badanie problemu
> * Rozwiązywanie problemu

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem konstruktor powinien ukończyć trzy samouczki przeznaczone dla konstruktorów w celu utworzenia aplikacji usługi Azure IoT Central:

* [Definiowanie nowego typu urządzenia](tutorial-define-device-type.md)
* [Konfigurowanie reguł i akcji dla urządzenia](tutorial-configure-rules.md)
* [Dostosowywanie widoków operatora](tutorial-customize-operator.md)

## <a name="receive-a-notification"></a>Odbieranie powiadomienia

Usługa Azure IoT Central wysyła powiadomienia dotyczące urządzeń jako wiadomości e-mail. Konstruktor dodał regułę powodującą wysłanie powiadomienia, gdy temperatura połączonego klimatyzatora przekroczy wartość progową. Sprawdź wiadomości e-mail wysłane na konto wybrane przez konstruktora do odbierania powiadomień.

Otwórz wiadomość e-mail odebraną na końcu samouczka [Konfigurowanie reguł i akcji dla urządzenia](tutorial-configure-rules.md). W wiadomości e-mail wybierz pozycję **Kliknij tutaj, aby otworzyć urządzenie**:

![Wiadomość e-mail z alertem](media/tutorial-monitor-devices/email.png)

Strona **Urządzenie** dla symulowanego urządzenia **Połączony klimatyzator-1** utworzonego w poprzednim samouczku zostanie otwarta w przeglądarce:

![Urządzenie, które wyzwoliło wiadomość e-mail z powiadomieniem](media/tutorial-monitor-devices/sourcedevice.png)

## <a name="investigate-an-issue"></a>Badanie problemu

Jako operator możesz przejrzeć informacje o urządzeniu na stronach **Miary**, **Ustawienia**, **Właściwości**, **Reguły** i **Pulpit nawigacyjny**. Konstruktor dostosował **pulpit nawigacyjny** pod kątem wyświetlania ważnych informacji o połączonym klimatyzatorze.

Wybierz widok **Pulpit nawigacyjny**, aby wyświetlić informacje o urządzeniu.

![Pulpit nawigacyjny urządzenia](media/tutorial-monitor-devices/initial_screen.png)

Wykres na pulpicie nawigacyjnym pokazuje wykreśloną temperaturę urządzenia. Bieżącą temperaturę docelową urządzenia można także zobaczyć na kafelku **Właściwości urządzenia** . Decydujesz, że temperatura docelowa jest zbyt wysoka.

## <a name="remediate-an-issue"></a>Rozwiązywanie problemu

Aby zmienić temperaturę docelową urządzenia, użyj strony **Ustawienia**:

1. Wybierz pozycję **Ustawienia**. Zmień wartość pola **Ustaw temperaturę** na 75. Wybierz polecenie **Aktualizuj**, aby wysłać nową temperaturę docelowa do urządzenia. Gdy urządzenie potwierdzi zmianę ustawień, stan ustawienia zostanie zmieniony na **zsynchronizowane**:

    ![Aktualizuj ustawienia](media/tutorial-monitor-devices/change_settings.png)

2. Wybierz pozycję **Pulpit nawigacyjny** i zweryfikuj nową wartość ustawienia:

    ![Zaktualizowany pulpit nawigacyjny urządzenia](media/tutorial-monitor-devices/new_settings.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="nextstepaction"]
> * Odbieranie powiadomienia
> * Badanie problemu
> * Rozwiązywanie problemu

Teraz, gdy już wiesz, jak monitorować urządzenie, sugerowanym następnym krokiem jest [dodanie urządzenia](tutorial-add-device.md).