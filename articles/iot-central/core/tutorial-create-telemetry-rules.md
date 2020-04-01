---
title: Samouczek — tworzenie reguł i zarządzanie nimi w aplikacji Azure IoT Central
description: W tym samouczku pokazano, jak reguły usługi Azure IoT Central umożliwiają monitorowanie urządzeń w czasie zbliżonym do rzeczywistego i automatyczne wywoływanie akcji, takich jak wysyłanie wiadomości e-mail, gdy reguła wyzwala.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f61a41fa89c7006341db928472f6b20d272bc550
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77167410"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Samouczek: Tworzenie reguły i konfigurowanie powiadomień w aplikacji Azure IoT Central

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

Za pomocą usługi Azure IoT Central można zdalnie monitorować podłączone urządzenia. Reguły usługi Azure IoT Central umożliwiają monitorowanie urządzeń w czasie zbliżonym do rzeczywistego i automatyczne wywoływanie akcji, takich jak wysyłanie wiadomości e-mail. Za pomocą kilku kliknięć można zdefiniować warunek monitorowania danych telemetrycznych z urządzeń i skonfigurować odpowiednią akcję. W tym artykule wyjaśniono, jak utworzyć reguły do monitorowania danych telemetrycznych wysyłanych przez urządzenie.

Urządzenia używają danych telemetrycznych do wysyłania danych liczbowych z urządzenia. Reguła wyzwala, gdy wybrana telemetria urządzenia przekroczy określony próg.

W tym samouczku utworzysz regułę wysyłania wiadomości e-mail, gdy temperatura&deg; w symulowanym urządzeniu czujnika środowiskowego przekracza 70 F.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie reguły
> * Dodawanie akcji e-mail

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy ukończyć [tworzenie aplikacji Azure IoT Central](./quick-deploy-iot-central.md) i dodać symulowane urządzenie do szybkiego startu aplikacji [IoT Central,](./quick-create-pnp-device.md) aby utworzyć szablon urządzenia **MXChip IoT DevKit** do pracy.

## <a name="create-a-rule"></a>Tworzenie reguły

Aby utworzyć regułę telemetrii, szablon urządzenia musi mieć zdefiniowany co najmniej jeden pomiar telemetryczny. W tym samouczku użyto czujnika środowiskowego, które wysyła dane telemetryczne temperatury i wilgotności. Dodano ten szablon urządzenia i utworzono symulowane urządzenie w przewodniku Szybki start [aplikacji Dodaj symulowane do aplikacji IoT Central.](./quick-create-pnp-device.md) Reguła monitoruje temperaturę zgłaszaną przez urządzenie i wysyła wiadomość e-mail, gdy przekracza ona 70 stopni.

1. W lewym okienku wybierz pozycję **Reguły**.

1. Jeśli nie utworzono jeszcze żadnych reguł, zostanie wyświetlony następujący ekran:

    ![Jeszcze nie ma reguł](media/tutorial-create-telemetry-rules/rules-landing-page1.png)

1. Wybierz, **+** aby dodać nową regułę.

1. Wprowadź nazwę _Monitor temperatury,_ aby zidentyfikować regułę, i naciśnij klawisz Enter.

1. Wybierz szablon urządzenia **MXChip IoT DevKit.** Domyślnie reguła jest automatycznie stosowana do wszystkich urządzeń skojarzonych z szablonem urządzenia. Aby filtrować podzbiór urządzeń, wybierz **+ Filtruj** i użyj właściwości urządzenia, aby zidentyfikować urządzenia. Aby wyłączyć regułę, przełącz przycisk **Włączone/Wyłączone** w nagłówku reguły:

    ![Filtry i włączanie](media/tutorial-create-telemetry-rules/device-filters.png)

### <a name="configure-the-rule-conditions"></a>Konfigurowanie warunków reguły

Warunki określają kryteria, które monitoruje reguła. W tym samouczku można skonfigurować regułę do ognia,&deg; gdy temperatura przekracza 70 F.

1. Wybierz **pozycję Temperatura** w menu rozwijanym **Telemetria.**

1. Następnie wybierz opcję **Jest większa niż** **operator** i wprowadź _70_ jako **wartość**.

    ![Warunek](media/tutorial-create-telemetry-rules/condition-filled-out1.png)

1. Opcjonalnie można ustawić **agregację czasu**. Po wybraniu agregacji czasu należy również wybrać typ agregacji, taki jak średnia lub suma z listy rozwijanej agregacji.

    * Bez agregacji reguła wyzwala dla każdego punktu danych telemetrii, który spełnia warunek. Na przykład jeśli reguła jest skonfigurowana do wyzwalania, gdy temperatura przekracza 70, reguła wyzwala się niemal natychmiast, gdy urządzenie zgłasza temperaturę > 70.
    * W przypadku agregacji reguła wyzwala, jeśli łączna wartość punktów danych telemetrycznych w oknie czasu spełnia warunek. Na przykład jeśli reguła jest skonfigurowana do wyzwalania, gdy temperatura jest powyżej 70, agregacja czasu jest ustawiona na 10 minut, a typ agregacji jest średni, reguła wyzwala, gdy urządzenie zgłasza średnią temperaturę > 70, obliczoną w przedziale 10 minut.

     ![Warunek agregacji](media/tutorial-create-telemetry-rules/aggregate-condition-filled-out1.png)

Do reguły można dodać wiele warunków, wybierając **opcję + Warunek**. Po określeniu wielu warunków wszystkie warunki muszą być spełnione, aby reguła wyzwoliła. Każdy warunek jest połączony `AND` przez klauzulę niejawną. Jeśli używasz agregacji czasu z wieloma warunkami, wszystkie wartości telemetryczne muszą być agregowane.

### <a name="configure-actions"></a>Konfigurowanie akcji

Po zdefiniowaniu warunku należy skonfigurować akcje, które należy podjąć po uruchomieniu reguły. Akcje są wywoływane, gdy wszystkie warunki określone w regule oceniają wartość true.

1. Wybierz **pozycję + Wyślij wiadomość e-mail** w sekcji **Akcje.**

1. Wprowadź _ostrzeżenie temperatury_ jako nazwę wyświetlaną akcji, twój adres e-mail w polu **Do** i _powinieneś sprawdzić urządzenie!_ jako notatkę, która pojawi się w treści wiadomości e-mail.

    > [!NOTE]
    > Wiadomości e-mail są wysyłane tylko do użytkowników, którzy zostali dodani do aplikacji i zalogowali się co najmniej raz. Dowiedz się więcej o [zarządzaniu użytkownikami](howto-administer.md) w usłudze Azure IoT Central.

   ![Konfigurowanie akcji](media/tutorial-create-telemetry-rules/configure-action1.png)

1. Aby zapisać akcję, wybierz pozycję **Gotowe**. Do reguły można dodać wiele akcji.

1. Aby zapisać regułę, wybierz pozycję **Zapisz**. Reguła zostanie wyciągnięta w ciągu kilku minut i rozpocznie monitorowanie danych telemetrycznych wysyłanych do aplikacji. Po spełnieniu warunku określonego w regule reguła wyzwala skonfigurowane działanie wiadomości e-mail.

Po pewnym czasie otrzymasz wiadomość e-mail po uruchomieniu reguły:

![Przykładowa wiadomość e-mail](media/tutorial-create-telemetry-rules/email.png)

## <a name="delete-a-rule"></a>Usuwanie reguły

Jeśli reguła nie jest już potrzebna, usuń ją, otwierając ją i wybierając polecenie **Usuń**.

## <a name="enable-or-disable-a-rule"></a>Włączanie lub wyłączanie reguły

Wybierz regułę, którą chcesz włączyć lub wyłączyć. Przełącz przycisk **Włącz** lub **Wyłącz** w regule, aby włączyć lub wyłączyć regułę dla wszystkich urządzeń, które mają zakres w regule.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Włączanie lub wyłączanie reguły dla urządzenia

Wybierz regułę, którą chcesz włączyć lub wyłączyć. Dodaj filtr w sekcji **Zakresy,** aby uwzględnić lub wykluczyć określone urządzenie w szablonie urządzenia.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Tworzenie reguły opartej na danych telemetrycznych
* Dodawanie akcji

Teraz, po zdefiniowanie reguły opartej na progu, sugerowany następny krok to dowiedzenie się, jak:

> [!div class="nextstepaction"]
> [Skonfiguruj ciągły eksport danych](./howto-export-data.md).
