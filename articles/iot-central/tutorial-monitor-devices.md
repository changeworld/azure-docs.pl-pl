---
title: Monitoruj urządzenia na platformie Azure IoT Central | Microsoft Docs
description: Jako operator Użyj aplikacji IoT Central platformy Azure do monitorowania urządzeń.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: f68bc211be3cffb61b3381390ae2eeacaffa4213
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960422"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices"></a>Samouczek: monitorowanie urządzeń za pomocą usługi Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

W tym samouczku pokazano, jak operator, jak używać aplikacji Microsoft Azure IoT Central do monitorowania urządzeń i zmieniania ustawień.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Odbieranie powiadomienia
> * Zbadaj problem
> * Korygowanie problemu

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem, Konstruktor powinien wykonać trzy samouczki dotyczące tworzenia aplikacji IoT Central platformy Azure:

* [Zdefiniuj nowy typ urządzenia](tutorial-define-device-type.md)
* [Konfigurowanie reguł i akcji dla urządzenia](tutorial-configure-rules.md)
* [Dostosuj widoki operatora](tutorial-customize-operator.md)

## <a name="receive-a-notification"></a>Odbieranie powiadomienia

Usługa Azure IoT Central wysyła powiadomienia o urządzeniach jako wiadomości e-mail. Konstruktor dodał regułę w celu wysłania powiadomienia, gdy temperatura w podłączonym urządzeniu warunkowego stanu powietrza przekroczyła wartość progową. Sprawdź wiadomości e-mail wysyłane do konta, które Konstruktor zdecydował się otrzymywać w celu otrzymywania powiadomień.

Otwórz wiadomość e-mail, która została odebrana na końcu samouczka [Konfigurowanie reguł i akcji dla Twojego urządzenia](tutorial-configure-rules.md) . W wiadomości e-mail wybierz link do urządzenia obok pozycji **Nazwa urządzenia** w sekcji **szczegóły** :

![Wiadomość e-mail z powiadomieniem o alercie](media/tutorial-monitor-devices/email.png)

W przeglądarce otwiera się Strona **urządzenia** dla urządzenia symulowanego **Conditioner** , który został utworzony w poprzednich samouczkach:

![Urządzenie, które wyzwoliło wiadomość e-mail z powiadomieniem](media/tutorial-monitor-devices/sourcedevice.png)

## <a name="investigate-an-issue"></a>Zbadaj problem

Jako operator można wyświetlić informacje o urządzeniu na stronach **miary**, **Ustawienia**, **Właściwości**, **reguły**i **pulpit nawigacyjny** . Konstruktor dostosował **pulpit nawigacyjny** do wyświetlania ważnych informacji o urządzeniu z warunkiem związanym ze środowiskiem klimatyzacyjnym.

Wybierz widok **pulpitu nawigacyjnego** , aby wyświetlić informacje o urządzeniu.

![Pulpit nawigacyjny urządzenia](media/tutorial-monitor-devices/initial_screen.png)

Wykres na pulpicie nawigacyjnym pokazuje powierzchnię temperatury urządzenia. Bieżącą temperaturę docelową urządzenia można także zobaczyć na kafelku **Właściwości urządzenia** . Należy określić, że temperatura docelowa jest zbyt wysoka.

## <a name="remediate-an-issue"></a>Korygowanie problemu

Aby zmienić docelową temperaturę urządzenia, użyj strony **ustawień** :

1. Wybierz pozycję **Ustawienia**. Zmień **ustawienie temperatura** na 75. Wybierz pozycję **Aktualizuj** , aby wysłać nową docelową temperaturę do urządzenia. Gdy urządzenie potwierdzi zmianę ustawień, stan ustawienia zmieni się na **Synchronizuj**:

    ![Aktualizuj ustawienia](media/tutorial-monitor-devices/change_settings.png)

2. Wybierz pozycję **pulpit nawigacyjny** i Sprawdź nową wartość ustawienia:

    ![Zaktualizowany pulpit nawigacyjny urządzenia](media/tutorial-monitor-devices/new_settings.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób wykonywania tych instrukcji:

> [!div class="nextstepaction"]
> * Odbieranie powiadomienia
> * Zbadaj problem
> * Korygowanie problemu

Teraz, gdy teraz wiesz już, jak monitorować urządzenie, zalecanym następnym krokiem jest [dodanie urządzenia](tutorial-add-device.md).