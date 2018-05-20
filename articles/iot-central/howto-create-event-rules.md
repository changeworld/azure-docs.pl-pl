---
title: Tworzenie i zarządzanie nimi reguły zdarzenia w aplikacji Azure IoT centralnej | Dokumentacja firmy Microsoft
description: Reguły zdarzeń w usłudze Azure IoT centralnego umożliwiają monitorowanie urządzeń w najbliższym czasie rzeczywistym i automatycznie wywołują akcje, takie jak wysyłanie wiadomości e-mail, gdy zasada wyzwala.
services: iot-central
author: ankitgupta
ms.author: ankitgup
ms.date: 04/29/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 932b1906b767ee7676f46ffd7242ad3d478d41c2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Tworzenie reguły zdarzeń i skonfigurować powiadomienia w aplikacji Azure IoT centralnej

Centrum IoT Azure Microsoft umożliwia zdalne monitorowanie podłączonych urządzeń. Azure IoT centralne zasady umożliwiają monitorowanie urządzeń w najbliższym czasie rzeczywistym i automatycznie wywołują akcje, takie jak wysyłanie wiadomości e-mail, gdy zasada wyzwala. Za pomocą kilku kliknięć można określić warunku do monitorowania danych urządzenia i skonfiguruj akcję do wywołania. W tym artykule opisano reguły szczegółowo monitorowania zdarzeń.

Używa Azure IoT centralnego [pomiaru zdarzeń](howto-set-up-template.md) do przechwytywania danych urządzenia. Każdy typ miary ma atrybutów klucza, które definiują pomiaru. Można utworzyć reguły do monitorowania każdego typu pomiaru urządzenia i generować alerty, gdy zasada wyzwala. Reguła zdarzenie jest wyzwalane po zdarzeń wybranego urządzenia został zgłoszony przez urządzenie.

## <a name="create-an-event-rule"></a>Tworzenie reguły zdarzeń

W tej sekcji przedstawiono sposób tworzenia reguły zdarzeń. W tym przykładzie użyto urządzenia chłodniczych Automat zdarzenie błędu motocykla wentylator raportów. Reguła monitoruje zdarzeń zgłoszonych przez urządzenia i wysyła wiadomość e-mail, gdy zdarzenie jest zgłaszane.

1. Przejdź do strony szczegółów urządzenia dla urządzenia, który dodajesz reguły.

1. Jeśli jeszcze nie utworzono żadnych reguł, zostanie wyświetlony następujący ekran:

    ![Jeszcze żadnych reguł](media\howto-create-event-rules\image1.png)

1. Na **reguły** , wybierz pozycję **+ nową regułę** Aby wyświetlić typy reguł można utworzyć.

    ![Typy reguł](media\howto-create-event-rules\image2.png)

1. Polecenie **zdarzeń** aby otworzyć formularz, aby utworzyć regułę.

    ![Reguły zdarzenia](media\howto-create-event-rules\image3.png)

1. Wybierz nazwę, która pomaga w identyfikacji reguły w tym szablonie urządzenia.

1. Aby natychmiast reguły dla wszystkich urządzeń, które są tworzone na podstawie tego szablonu, Przełącz **Włącz regułę**.

### <a name="configure-the-rule-condition"></a>Konfigurowanie warunku reguły

W tej sekcji przedstawiono sposób Dodaj warunek do monitorowania pomiaru zdarzenia błędów motocykla wentylator.

1. Wybierz **+** obok **warunku**.

1. Wybierz miary zdarzenia z listy rozwijanej, która ma być monitorowany. W tym przykładzie **błąd motocykla wentylator** zdarzeń został wybrany.

1. Opcjonalnie możesz także podać wartość w przypadku, gdy chcesz monitorować określonej wartości zdarzenie zgłaszane przez urządzenie. Na przykład jeśli urządzenie zgłasza zdarzenie z kodów błędów różnych podają kod błędu jako wartość warunek reguły sprawdzi, czy wyzwalaczy reguły tylko wtedy, gdy urządzenie wysyła tej określonej wartości jako ładunek zdarzenia. Pozostawienie pustego tym oznacza, że reguła wyzwoli zawsze, gdy urządzenie wysyła zdarzenia niezależnie od wartości zdarzeń.

    ![Dodaj warunek zdarzenia](media\howto-create-event-rules\image4.png)

    > [!NOTE]
    > Należy wybrać co najmniej jedno zdarzenie pomiaru podczas definiowania warunek reguły zdarzeń.

### <a name="configure-the-action"></a>Konfigurowanie akcji

W tej sekcji przedstawiono sposób określania, jakie reguły wykonuje, gdy warunek zgodny przez dodanie akcji.

1. Wybierz **+** obok **akcje**. Tutaj możesz wyświetlić listę dostępnych akcji. W publicznej wersji zapoznawczej **E-mail** jest jedyną akcją, obsługiwane.

    ![Dodaj akcję](media\howto-create-event-rules\image5.png)

1. Wybierz **E-mail** akcji, wprowadź prawidłowy adres e-mail w **do** pola i podaj Uwaga do występować w treści wiadomości e-mail, gdy zasada wyzwala.

    > [!NOTE]
    > Wiadomości e-mail będą wysyłane tylko do użytkowników, które zostały dodane do aplikacji i zalogowali się co najmniej raz. Dowiedz się więcej o [Zarządzanie użytkownikami](howto-administer.md) w Azure IoT centralnej.

   ![Konfigurowanie akcji](media\howto-create-event-rules\image6.png)

1. Aby zapisać regułę, wybierz **zapisać**. Reguła przechodzi na żywo za kilka minut i rozpoczyna monitorowanie zdarzeń są wysyłane do aplikacji. Gdy spełnia warunek określony w regule, zasada wyzwala akcji skonfigurowanych poczty e-mail.

## <a name="parameterize-the-rule"></a>Parametryzacja reguły

Akcje można również skonfigurować za pomocą **właściwości urządzenia** jako parametr. Jeśli adres e-mail jest przechowywany jako właściwość urządzenia, a następnie mogą być używane podczas definiowania **do** adres.

## <a name="delete-a-rule"></a>Usuwanie reguły

Jeśli reguła nie jest już potrzebny, usuń go, otwierając reguły i wybierając pozycję **usunąć**. Usuwanie reguły spowoduje usunięcie jej z szablonu urządzenia i wszystkie skojarzone urządzenia.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Włącza lub wyłącza regułę dla szablonu urządzenia

Przejdź do urządzenia, a następnie wybierz regułę, którą chcesz włączyć lub wyłączyć. Przełączanie **Włącz reguły dla wszystkich urządzeń z tego szablonu** przycisk w regule Włącza lub wyłącza reguły dla wszystkich urządzeń skojarzonych z szablonem urządzenia.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Włącz lub wyłącz reguły dla urządzenia

Przejdź do urządzenia, a następnie wybierz regułę, którą chcesz włączyć lub wyłączyć. Przełącz **Włącz regułę dla tego urządzenia** przycisk, aby włączyć funkcję lub wyłączyć regułę dla tego urządzenia.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposobu tworzenia reguły w aplikacji Azure IoT centralnej, Oto sugerowane następnego kroku:

> [!div class="nextstepaction"]
> [Jak zarządzać urządzeniami](howto-manage-devices.md).