---
title: Monitorowanie urządzeń w usłudze Azure IoT Central | Microsoft Docs
description: Jako operator możesz monitorować urządzenia za pomocą aplikacji usługi Azure IoT Central.
author: tbhagwat3
ms.author: tanmayb
ms.date: 10/12/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: f7d8b3bf02fb92f24054172622e431f24bf344d9
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50157483"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices"></a>Samouczek: Korzystanie z usługi Azure IoT Central do monitorowania urządzeń

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

![Reguły Konstruktora aplikacji](media/tutorial-monitor-devices/email.png)

Strona **Urządzenie** dla symulowanego urządzenia **Połączony klimatyzator-1** utworzonego w poprzednim samouczku zostanie otwarta w przeglądarce:

![Urządzenie, które wyzwoliło wiadomość e-mail z powiadomieniem](media/tutorial-monitor-devices/sourcedevice.png)

## <a name="investigate-an-issue"></a>Badanie problemu

Jako operator możesz przejrzeć informacje o urządzeniu na stronach **Miary**, **Ustawienia**, **Właściwości**, **Reguły** i **Pulpit nawigacyjny**. Konstruktor dostosował **pulpit nawigacyjny** pod kątem wyświetlania ważnych informacji o połączonym klimatyzatorze.

Wybierz widok **Pulpit nawigacyjny**, aby wyświetlić informacje o urządzeniu.

![Pulpit nawigacyjny urządzenia](media/tutorial-monitor-devices/initial_screen.png)

Wykres na pulpicie nawigacyjnym pokazuje wykreśloną temperaturę urządzenia. Bieżącą temperaturę docelową urządzenia można także zobaczyć na kafelku **Ustaw temperaturę docelową**. Decydujesz, że temperatura docelowa jest zbyt wysoka.

## <a name="remediate-an-issue"></a>Rozwiązywanie problemu

Aby zmienić temperaturę docelową urządzenia, użyj strony **Ustawienia**:

1. Wybierz pozycję **Ustawienia**. Zmień wartość pola **Ustaw temperaturę** na 75. Wybierz polecenie **Aktualizuj**, aby wysłać nową temperaturę docelowa do urządzenia. Gdy urządzenie potwierdzi zmianę ustawień, stan wartości ustawienia zostanie zmieniony na **zsynchronizowane**:

    ![Aktualizowanie ustawień](media/tutorial-monitor-devices/change_settings.png)

2. Wybierz pozycję **Pulpit nawigacyjny** i zweryfikuj nową wartość ustawienia:

    ![Zaktualizowany pulpit nawigacyjny urządzenia](media/tutorial-monitor-devices/new_settings.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="nextstepaction"]
> * Odbieranie powiadomienia
> * Badanie problemu
> * Rozwiązywanie problemu

Teraz, gdy już wiesz, jak monitorować urządzenie, sugerowanym następnym krokiem jest [dodanie urządzenia](tutorial-add-device.md).