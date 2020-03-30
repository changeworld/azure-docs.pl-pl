---
title: Szybki start — konfigurowanie reguł i akcji w usłudze Azure IoT Central
description: Ten przewodnik Szybki start pokazuje, jako konstruktora, jak skonfigurować reguły i akcje oparte na telemetrii w aplikacji Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 4bbf571d6b73a6f43c1c3b1ce261da6963a74183
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77169513"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Szybki start: konfigurowanie reguł i akcji dla urządzenia w usłudze Azure IoT Central

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

W tym przewodniku Szybki start utworzysz regułę, która wysyła wiadomość e-mail, gdy temperatura zgłaszana przez czujnik urządzenia przekracza 90&deg; F.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy wykonać dwa poprzednie przewodniki Szybki start [Tworzenie aplikacji Azure IoT Central](./quick-deploy-iot-central.md) i dodawanie [symulowanego urządzenia do aplikacji IoT Central](./quick-create-pnp-device.md) w celu utworzenia szablonu urządzenia **MXChip IoT DevKit** do pracy.

## <a name="create-a-telemetry-based-rule"></a>Tworzenie reguły opartej na danych telemetrycznych

1. Aby dodać nową regułę opartą na telemetrii do aplikacji, w lewym okienku wybierz pozycję **Reguły**.

1. Aby utworzyć nową regułę, wybierz opcję **+**.

1. Wprowadź **temperaturę środowiska** jako nazwę reguły.

1. W sekcji **Urządzenia docelowe** wybierz **pozycję MXChip IoT DevKit** jako szablon urządzenia. Ta opcja filtruje urządzenia, które reguła ma zastosowanie według typu szablonu urządzenia. Więcej kryteriów filtrowania można dodać, wybierając opcję **+ Filtr**.

1. W sekcji **Warunki** można zdefiniować, co wyzwala regułę. Aby zdefiniować warunek na podstawie danych telemetrycznych temperatury, należy użyć następujących informacji:

    | Pole        | Wartość            |
    | ------------ | ---------------- |
    | Miara  | Temperatura      |
    | Operator     | jest większe niż  |
    | Wartość        | 90               |

    Aby dodać więcej warunków, wybierz **+ Warunek**.

    ![Warunek tworzenia reguły](./media/quick-configure-rules/condition.png)

1. Aby dodać akcję e-mail do uruchomienia po wyzwoleniu reguły, wybierz pozycję **+ Wyślij wiadomość e-mail**.

1. Użyj informacji w poniższej tabeli, aby zdefiniować akcję, a następnie wybierz pozycję **Gotowe:**

    | Ustawienie   | Wartość                                             |
    | --------- | ------------------------------------------------- |
    | Nazwa wyświetlana | Akcja e-mail operatora                          |
    | Do        | Twój adres e-mail                                |
    | Uwagi     | Temperatura środowiska przekroczyła próg. |

    > [!NOTE]
    > Aby użytkownik mógł otrzymywać wiadomości e-mail z powiadomieniem, adres e-mail musi być [identyfikatorem użytkownika w aplikacji](howto-administer.md), a użytkownik musiał zalogować się do aplikacji co najmniej raz.

    ![Utwórz akcję reguły](./media/quick-configure-rules/action.png)

1. Wybierz **pozycję Zapisz**. Reguła znajduje się na stronie **Reguły.**

## <a name="test-the-rule"></a>Testowanie reguły

Wkrótce po zapisaniu reguły stanie się ona aktywna. Gdy warunki zdefiniowane w regule zostaną spełnione, aplikacja wyśle wiadomość na adres e-mail określony w akcji.

> [!NOTE]
> Po zakończeniu testowania wyłącz regułę, aby zatrzymać wysyłanie wiadomości do Twojej skrzynki odbiorczej.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Tworzenie reguły opartej na danych telemetrycznych
* Dodawanie akcji

Aby dowiedzieć się więcej o monitorowaniu urządzeń podłączonych do aplikacji, przejdź do przewodnika Szybki start:

> [!div class="nextstepaction"]
> [Użyj usługi Azure IoT Central, aby monitorować swoje urządzenia.](quick-monitor-devices.md)
