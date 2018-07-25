---
title: Tworzenie i zarządzanie nimi reguły zdarzenia w aplikacji usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Zasady zdarzeń w usłudze Azure IoT Central umożliwiają monitorowanie urządzeń w czasie zbliżonym do rzeczywistego i automatycznie wywołują akcje, takie jak wysyłanie wiadomości e-mail po wyzwoleniu reguły.
services: iot-central
author: ankitgupta
ms.author: ankitgup
ms.date: 04/29/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: c5697f6d4ca2c9d9948b7cdd005a6a75bdabb246
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222569"
---
# <a name="create-an-event-rule-and-set-up-an-action-in-your-azure-iot-central-application"></a>Tworzenie reguły zdarzeń i konfigurowanie akcji w aplikacji usługi Azure IoT Central

Microsoft Azure IoT Central umożliwia zdalne monitorowanie połączonych urządzeń. Reguły usługi Azure IoT Central umożliwiają monitorowanie urządzeń w czasie zbliżonym do rzeczywistego i automatycznie wywołują akcje, takie jak wysyłanie wiadomości e-mail lub wyzwolenie przepływu pracy w programie Microsoft Flow po spełnieniu warunków reguły. Za pomocą kilku kliknięć można określić warunek, którego chcesz monitorować dane urządzenia i skonfiguruj akcję do wywołania. W tym artykule wyjaśniono reguły szczegółowo monitorowania zdarzeń.

Używa usługi Azure IoT Central [pomiaru zdarzeń](howto-set-up-template.md) do przechwytywania danych urządzenia. Każdy typ miary ma kluczowych atrybutów, które definiują pomiaru. Można utworzyć reguły, aby monitorować każdego typu pomiaru urządzenia i generować alerty po wyzwoleniu reguły. Zasada zdarzeń uaktywnia zdarzeń wybrane urządzenie jest zgłaszane przez urządzenie.

## <a name="create-an-event-rule"></a>Tworzenie reguły zdarzeń

W tej sekcji pokazano, jak utworzyć regułę zdarzeń. W tym przykładzie użyto urządzenia mrożone Automat zdarzenie błędu motor wentylator raportów. Reguła monitoruje zdarzeń zgłoszonych przez urządzenia i wysyła wiadomość e-mail, gdy zdarzenie jest zgłaszane.

1. Przejdź do strony szczegółów urządzenia dla urządzenia, którego dodajesz reguły.

1. Jeśli nie utworzono jeszcze żadnych reguł, zostanie wyświetlony następujący ekran:

    ![Jeszcze żadnych reguł](media/howto-create-event-rules/image1.png)

1. Na **reguły** kartę, wybrać **+ Nowa reguła** wyświetlić typy reguł, które można utworzyć.

    ![Typy zasad](media/howto-create-event-rules/image2.png)

1. Kliknij pozycję **zdarzeń** aby otworzyć formularz, aby utworzyć regułę.

    ![Zasada zdarzeń](media/howto-create-event-rules/image3.png)

1. Wybierz nazwę, która pomaga w identyfikacji reguły, w tym szablonie urządzenia.

1. Aby od razu włączyć zasadę dla wszystkich urządzeń, które są tworzone na podstawie tego szablonu, Przełącz **Włącz regułę**.

### <a name="configure-the-rule-condition"></a>Konfigurowanie warunku reguły

W tej sekcji pokazano, jak dodać warunek do pomiaru zdarzenia błędów motor wentylator monitorowania.

1. Wybierz **+** obok **warunek**.

1. Wybierz miarę zdarzeń z listy rozwijanej, która ma być monitorowany. W tym przykładzie **błąd Motor wentylator** zdarzeń został wybrany.

1. Opcjonalnie możesz także podać wartość w przypadku, gdy chcesz monitorować określonej wartości zdarzenie zgłaszane przez urządzenie. Na przykład jeśli urządzenie zgłasza te same zdarzenia z kodami błędów inny, następnie podając kod błędu: jako wartość warunku reguły będzie upewnij się, że wyzwolenie reguły tylko wtedy, gdy urządzenie wysyła tej określonej wartości jako ładunek zdarzenia. Pozostawienie to pole puste, oznacza, że reguła wyzwoli zawsze wtedy, gdy urządzenie wysyła zdarzenia, niezależnie od wartości zdarzeń.

    ![Dodaj warunek zdarzenia](media/howto-create-event-rules/image4.png)

    > [!NOTE]
    > Podczas definiowania warunku reguły zdarzenia, należy wybrać co najmniej jedno zdarzenie pomiarów.

1. Kliknij przycisk **Zapisz** można zapisać reguły. Reguła przechodzi na żywo w ciągu kilku minut i rozpoczyna monitorowanie zdarzeń wysyłanych do aplikacji.

### <a name="add-an-action"></a>Dodawanie akcji

Tym przykładzie przedstawiono sposób dodawania akcji do reguły. To pokazuje, jak dodawanie akcji poczty e-mail, ale można również dodać inne akcje:
-  [Microsoft Flow akcji](howto-add-microsoft-flow.md) Konferencję przepływu pracy w Microsoft Flow po wyzwoleniu reguły
- [Akcja elementu Webhook](howto-create-webhooks.md) do powiadamiania innych usług, po wyzwoleniu reguły

> [!NOTE]
> W tej chwili może być skojarzona z jednej reguły tylko 1 akcję.

1. Wybierz **+** obok **akcje**. W tym miejscu zobaczysz listę dostępnych akcji.

    ![Dodawanie akcji](media/howto-create-event-rules/image5.png)

1. Wybierz **E-mail** akcję, wprowadź prawidłowy adres e-mail w **do** pola, a następnie podaj notatkę pojawią się w treści wiadomości e-mail po wyzwoleniu reguły.

    > [!NOTE]
    > Wiadomości e-mail są wysyłane tylko do użytkowników, które zostały dodane do aplikacji i Logowanie zostało wykonane co najmniej raz. Dowiedz się więcej o [Zarządzanie użytkownikami](howto-administer.md) w usłudze Azure IoT Central.

   ![Konfigurowanie akcji](media/howto-create-event-rules/image6.png)

1. Kliknij pozycję **Zapisz**. Reguła przechodzi na żywo w ciągu kilku minut i rozpoczyna monitorowanie zdarzeń wysyłanych do aplikacji. Gdy spełnia warunek określony w regule, zasada wyzwala akcji dotyczącej wiadomości e-mail skonfigurowany.

## <a name="parameterize-the-rule"></a>Parametryzacja reguły

Akcje mogą być również konfigurowane przy użyciu **właściwości urządzenia** jako parametr. Jeśli adres e-mail jest przechowywany jako właściwości urządzenia, a następnie mogą być używane podczas definiowania **do** adresu.

## <a name="delete-a-rule"></a>Usuwanie reguły

Jeśli nie potrzebujesz już regułę, usuń go, otwierając reguły i wybierając pozycję **Usuń**. Usuwanie reguły spowoduje usunięcie jej z szablonu urządzenia i wszystkie skojarzone urządzenia.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Włączanie lub wyłączanie reguły szablonu urządzenia

Przejdź do urządzenia, a następnie wybierz regułę, którą chcesz włączyć lub wyłączyć. Przełączanie **Włącz regułę dla wszystkich urządzeń z tego szablonu** przycisku w regule Włącza lub wyłącza regułę dla wszystkich urządzeń skojarzonych z szablonem urządzenia.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Włącza lub wyłącza regułę dla urządzenia

Przejdź do urządzenia, a następnie wybierz regułę, którą chcesz włączyć lub wyłączyć. Przełącz **Włącz regułę dla tego urządzenia** przycisk, aby włączyć lub wyłączyć regułę dla tego urządzenia.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak utworzyć zasady w aplikacji usługi Azure IoT Central, poniżej przedstawiono sugerowany następnego kroku:

> [!div class="nextstepaction"]
> [Jak dodać akcję Microsoft Flow do reguły](howto-add-microsoft-flow.md)
> [sposobu zarządzania urządzeniami](howto-manage-devices.md).
