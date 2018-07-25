---
title: Tworzenie i zarządzanie regułami telemetrii w aplikacji usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Reguły telemetrii w usłudze Azure IoT Central umożliwiają monitorowanie urządzeń w czasie zbliżonym do rzeczywistego i automatycznie wywołują akcje, takie jak wysyłanie wiadomości e-mail po wyzwoleniu reguły.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 42516e4dd6a85e0d07d4a8e70e958b2ec6e84aad
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225204"
---
# <a name="create-a-telemetry-rule-and-set-up-an-action-in-your-azure-iot-central-application"></a>Utwórz regułę telemetrii i konfigurowanie akcji w aplikacji usługi Azure IoT Central

Microsoft Azure IoT Central umożliwia zdalne monitorowanie połączonych urządzeń. Reguły usługi Azure IoT Central umożliwiają monitorowanie urządzeń w czasie zbliżonym do rzeczywistego i automatycznie wywołują akcje, takie jak wysyłanie wiadomości e-mail lub wyzwolenie przepływu pracy w programie Microsoft Flow po spełnieniu warunków reguły. Za pomocą kilku kliknięć można określić warunki, które monitorują dane urządzenia i skonfiguruj akcję do wywołania. W tym artykule wyjaśniono reguły telemetrii szczegółowo.

Używa usługi Azure IoT Central [pomiarów telemetrii](howto-set-up-template.md) do przechwytywania danych urządzenia. Każdy typ miary ma kluczowych atrybutów, które definiują pomiaru. Można utworzyć reguły, aby monitorować każdego typu pomiaru urządzenia i generować alerty po wyzwoleniu reguły. Reguła telemetrii uaktywnia danych telemetrycznych z wybranego urządzenia przekracza określoną wartość progową.

## <a name="create-a-telemetry-rule"></a>Utwórz regułę telemetrii

W tej sekcji dowiesz się, jak utworzyć regułę telemetrii. W tym przykładzie użyto urządzenia połączone klimatyzacyjne, który wysyła dane telemetryczne dotyczące temperatury i wilgotności. Reguła monitoruje temperatura zgłoszona przez urządzenia i wysyła wiadomość e-mail, gdy przejdzie ponad 80 stopni.

1. Przejdź do strony szczegółów urządzenia dla urządzenia, którego dodajesz reguły.

1. Jeśli nie utworzono jeszcze żadnych reguł, zostanie wyświetlony następujący ekran:

    ![Jeszcze żadnych reguł](media/howto-create-telemetry-rules/image1.png)

1. Na **reguły** kartę, wybrać **+ Nowa reguła** wyświetlić typy reguł, które można utworzyć.

    ![Typy zasad](media/howto-create-telemetry-rules/image2.png)

1. Wybierz **Telemetrii** Kafelek, aby otworzyć formularz, aby utworzyć regułę.

    ![Reguła telemetrii](media/howto-create-telemetry-rules/image3.png)

1. Wybierz nazwę, która pomaga w identyfikacji reguły, w tym szablonie urządzenia.

1. Aby od razu włączyć zasadę dla wszystkich urządzeń, które są tworzone na podstawie tego szablonu, Przełącz **Włącz regułę**.

### <a name="configure-the-rule-condition"></a>Konfigurowanie warunku reguły

W tej sekcji pokazano, jak dodać warunek do monitorowania danych telemetrycznych dotyczących temperatury.

1. Wybierz **+** obok **warunek**.

1. Wybierz **temperatury** typu telemetrii z listy rozwijanej. Następnie wybierz operator i podaj wartość progową. Można dodać wiele warunków telemetrii. Jeśli określono wiele warunków, wszystkie warunki muszą być spełnione dla tej reguły wyzwolić.

    ![Dodaj warunek Telemetrii](media/howto-create-telemetry-rules/image4.png)

    > [!NOTE]
    > Wybierz co najmniej jedną miarę telemetrii, podczas definiowania warunku reguły telemetrii.

1. Kliknij przycisk **Zapisz** można zapisać reguły. Reguła przechodzi na żywo w ciągu kilku minut i rozpoczyna monitorowanie dane telemetryczne są wysyłane do aplikacji.

### <a name="add-an-action"></a>Dodawanie akcji

W tym przykładzie przedstawiono sposób dodawania akcji do reguły. To pokazuje, jak dodawanie akcji poczty e-mail, ale można również dodać inne akcje:
-  [Microsoft Flow akcji](howto-add-microsoft-flow.md) Konferencję przepływu pracy w Microsoft Flow po wyzwoleniu reguły
- [Akcja elementu Webhook](howto-create-webhooks.md) do powiadamiania innych usług, po wyzwoleniu reguły

> [!NOTE]
> W tej chwili może być skojarzona z jednej reguły tylko 1 akcję.

1. Wybierz **+** obok **akcje**. W tym miejscu zobaczysz listę dostępnych akcji.

    ![Dodawanie akcji](media/howto-create-telemetry-rules/image5.png)

1. Wybierz **E-mail** akcję, wprowadź prawidłowy adres e-mail w **do** pola, a następnie podaj notatkę pojawią się w treści wiadomości e-mail po wyzwoleniu reguły.

    > [!NOTE]
    > Wiadomości e-mail są wysyłane tylko do użytkowników, które zostały dodane do aplikacji i Logowanie zostało wykonane co najmniej raz. Dowiedz się więcej o [Zarządzanie użytkownikami](howto-administer.md) w usłudze Azure IoT Central.

   ![Konfigurowanie akcji](media/howto-create-telemetry-rules/image6.png)

1. Kliknij pozycję **Zapisz**. Reguła przechodzi na żywo w ciągu kilku minut i rozpoczyna monitorowanie dane telemetryczne są wysyłane do aplikacji. Gdy spełnia warunek określony w regule, zasada wyzwala akcji dotyczącej wiadomości e-mail skonfigurowany.

## <a name="parameterize-the-rule"></a>Parametryzacja reguły

Reguły mogą generować niektóre wartości z **właściwości urządzenia** jako parametry. Przy użyciu parametrów jest przydatne w scenariuszach, gdzie telemetria progi różnią się na różnych urządzeniach. Podczas tworzenia reguły, wybierz polecenie Właściwości urządzenia, który określa próg, takie jak **osiągnie maksymalny próg idealne**, zamiast podawać wartości bezwzględnej, takie jak 80 stopni. Zasada jest wykonywana, jest on zgodny danych telemetrycznych z urządzenia z wartością podana we właściwości urządzenia.

Przy użyciu parametrów jest efektywny sposób zmniejszenia liczby reguł do zarządzania na urządzeniu szablonu.

Akcje mogą być również konfigurowane przy użyciu **właściwości urządzenia** jako parametr. Jeśli adres e-mail jest przechowywany jako właściwości urządzenia, a następnie mogą być używane podczas definiowania **do** adresu.

## <a name="delete-a-rule"></a>Usuwanie reguły

Jeśli nie potrzebujesz już regułę, usuń go, otwierając reguły i wybierając pozycję **Usuń**. Usuwanie reguły spowoduje usunięcie jej z szablonu urządzenia i wszystkie skojarzone urządzenia.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Włączanie lub wyłączanie reguły szablonu urządzenia

Przejdź do urządzenia, a następnie wybierz regułę, którą chcesz włączyć lub wyłączyć. Przełączanie **Włącz regułę dla wszystkich urządzeń z tego szablonu** przycisku w regule Włącza lub wyłącza regułę dla wszystkich urządzeń skojarzonych z szablonem urządzenia.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Włącza lub wyłącza regułę dla urządzenia

Przejdź do urządzenia, a następnie wybierz regułę, którą chcesz włączyć lub wyłączyć. Przełącz **Włącz regułę dla tego urządzenia** przycisk, aby włączyć lub wyłączyć regułę dla tego urządzenia.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak edytować zasady w aplikacji usługi Azure IoT Central, Oto zalecane kolejne kroki:

> [!div class="nextstepaction"]
> [Jak dodać akcję Microsoft Flow do reguły](howto-add-microsoft-flow.md)
> [sposobu zarządzania urządzeniami](howto-manage-devices.md)
