---
title: Monitorowanie urządzeń w usłudze Azure IoT Central | Microsoft Docs
description: Jako operator możesz monitorować urządzenia za pomocą aplikacji usługi Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: db229a6c6d8fded8dc2434653f34ec0c727cc761
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72958077"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices-preview-features"></a>Szybki Start: korzystanie z usługi Azure IoT Central do monitorowania urządzeń (funkcje w wersji zapoznawczej)

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ten przewodnik Szybki Start przedstawia Cię jako operatora, jak używać aplikacji Microsoft Azure IoT Central do monitorowania urządzeń i zmieniania ustawień.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy wykonać trzy poprzednie Przewodniki Szybki Start [Tworzenie aplikacji IoT Central platformy Azure](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json), [dodać symulowane urządzenie do aplikacji IoT Central](./quick-create-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) i [skonfigurować reguły i akcje dla urządzenia](quick-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="receive-a-notification"></a>Odbieranie powiadomienia

Usługa Azure IoT Central wysyła powiadomienia dotyczące urządzeń jako wiadomości e-mail. Konstruktor dodał regułę wysyłania powiadomienia, gdy temperatura w podłączonym urządzeniu czujnika środowiska przekroczyła wartość progową. Sprawdź wiadomości e-mail wysłane na konto wybrane przez konstruktora do odbierania powiadomień.

Otwórz wiadomość e-mail otrzymaną po zakończeniu [konfigurowania reguł i akcji dla](quick-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) szybkiego startu urządzenia. W wiadomości e-mail wybierz link do urządzenia:

![Wiadomość e-mail z alertem](media/quick-monitor-devices-pnp/email.png)

W przeglądarce zostanie otwarty widok **pulpitu nawigacyjnego** symulowanego urządzenia z czujnikiem środowiska utworzonego w ramach poprzednich przewodników szybki start:

![Urządzenie, które wyzwoliło wiadomość e-mail z powiadomieniem](media/quick-monitor-devices-pnp/dashboard.png)

## <a name="investigate-an-issue"></a>Badanie problemu

Jako operator można wyświetlić informacje o urządzeniu na stronie **Przegląd**, **Właściwości czujnika środowiska**i **polecenia** . Konstruktor dostosował strony właściwości **pulpitu nawigacyjnego** i **czujnika środowiska** w celu wyświetlenia ważnych informacji o podłączonym urządzeniu czujnika środowiska.

Wybierz widok **Przegląd** , aby wyświetlić informacje o urządzeniu.

Wykres na pulpicie nawigacyjnym pokazuje wykreśloną temperaturę urządzenia. Należy określić, że temperatura urządzenia jest zbyt wysoka.

## <a name="remediate-an-issue"></a>Rozwiązywanie problemu

Aby wprowadzić zmianę na urządzeniu, użyj strony **Właściwości czujnika środowiska** .

Wybierz **Właściwości czujnika środowiska**. Zmień **poziom jasności** na 10. Wybierz pozycję **Zapisz** , aby zaktualizować urządzenie. Gdy urządzenie potwierdzi zmianę ustawień, stan właściwości zmieni się na **zsynchronizowany**:

![Aktualizowanie ustawień](media/quick-monitor-devices-pnp/change-settings.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Odbieranie powiadomienia
* Badanie problemu
* Rozwiązywanie problemu

Teraz, gdy teraz wiesz już, jak monitorować urządzenie, sugerowanym następnym krokiem jest:

> [!div class="nextstepaction"]
> [Kompiluj szablon urządzenia i Zarządzaj nim](howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
