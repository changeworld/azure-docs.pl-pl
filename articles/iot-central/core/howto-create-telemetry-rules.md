---
title: Używanie reguł telemetrii w aplikacji IoT Central platformy Azure | Microsoft Docs
description: Reguły telemetrii IoT Central platformy Azure umożliwiają monitorowanie urządzeń w czasie niemal rzeczywistym oraz automatyczne wywoływanie akcji, takich jak wysyłanie wiadomości e-mail, gdy reguła jest wyzwalana.
author: ankitgupta
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 0b24c064424b00fa9acb96b03c0a3c5ca69f67f2
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264398"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Tworzenie reguły telemetrii i Konfigurowanie powiadomień w aplikacji IoT Central platformy Azure

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Za pomocą usługi Azure IoT Central można zdalnie monitorować połączone urządzenia. Reguły IoT Central platformy Azure umożliwiają monitorowanie urządzeń w czasie niemal rzeczywistym i automatyczne wywoływanie akcji, takich jak wysyłanie wiadomości e-mail lub wyzwalacza Microsoft Flow. Wystarczy kilka kliknięć, aby zdefiniować warunek monitorowania danych urządzenia i konfigurowania odpowiedniej akcji. W tym artykule wyjaśniono, jak utworzyć reguły monitorowania danych telemetrycznych wysyłanych przez urządzenie.

Aby wysyłać dane liczbowe z urządzenia, urządzenia mogą używać pomiarów telemetrii. Reguła telemetrii wyzwala, gdy wybrane dane telemetryczne urządzenia przecinają określony próg.

## <a name="create-a-telemetry-rule"></a>Tworzenie reguły telemetrii

Aby utworzyć regułę telemetrii, szablon urządzenia musi mieć zdefiniowany co najmniej jeden pomiar telemetrii. W tym przykładzie jest używane urządzenie maszyny z systemem chłodzenia w systemie, które wysyła dane telemetryczne temperatury i wilgotności. Reguła monitoruje temperaturę zgłoszoną przez urządzenie i wysyła wiadomość e-mail, gdy znajdzie się ona powyżej 70&deg; F.

1. Korzystając ze strony **Szablony urządzeń** , przejdź do szablonu urządzenia, dla którego chcesz dodać regułę.

1. Jeśli nie utworzono jeszcze żadnych reguł, zobaczysz następujący ekran:

    ![Jeszcze nie ma reguł](media/howto-create-telemetry-rules/rules_landing_page1.png)

1. Na karcie **reguły** wybierz pozycję **+ Nowa reguła** , aby wyświetlić typy reguł, które możesz utworzyć.

1. Wybierz pozycję **telemetrię** , aby utworzyć regułę służącą do monitorowania danych telemetrycznych urządzenia.

    ![Typy reguł](media/howto-create-telemetry-rules/rule_types1.png)

1. Wprowadź nazwę, która pomoże zidentyfikować regułę w tym szablonie urządzenia.

1. Aby natychmiast włączyć regułę dla wszystkich urządzeń utworzonych dla tego szablonu, przełącz **regułę włączania dla wszystkich urządzeń tego szablonu**.

   ![Szczegóły reguły](media/howto-create-telemetry-rules/rule_detail1.png)

    Reguła zostanie automatycznie zastosowana do wszystkich urządzeń objętych szablonem urządzenia.

### <a name="configure-the-rule-conditions"></a>Skonfiguruj warunki reguły

Warunek definiuje kryteria, które są monitorowane przez regułę.

1. Wybierz **+** obok pozycji **warunki** , aby dodać nowy warunek.

1. Wybierz dane telemetryczne, które chcesz monitorować, z listy rozwijanej **miara** .

1. Następnie wybierz **agregację**, **operator**i podaj wartość **progową** .
   - Agregacja jest opcjonalna. Bez agregacji reguła wyzwala dla każdego punktu danych telemetrii, który spełnia warunek. Jeśli na przykład zasada jest skonfigurowana do wyzwalania, gdy temperatura jest większa niż 70&deg; F, reguła jest wyzwalana niemal natychmiast, gdy urządzenie zgłosi temperaturę > 70.
   - Jeśli funkcja agregująca, taka jak Average, min, Max, jest wybierana, użytkownik musi podać **zagregowany przedział czasu** , w którym należy oszacować warunek. Na przykład jeśli ustawisz okres jako "5 minut", a Twoja reguła szuka średniej temperatury powyżej 70, reguła jest wyzwalana, gdy średnia temperatura jest powyżej 70&deg; F przez co najmniej 5 minut. Częstotliwość oceny reguł jest taka sama jak w **przedziale czasu zagregowanym**, co oznacza, że w tym przykładzie reguła jest szacowana co 5 minut.

     ![Warunek](media/howto-create-telemetry-rules/aggregate_condition_filled_out1.png)

     >[!NOTE]
     >W **warunku**można dodać więcej niż jedną miarę telemetrię. W przypadku określenia wielu warunków wszystkie warunki muszą być spełnione, aby reguła była wyzwalana. Każdy warunek jest przyłączony przez klauzulę "i" niejawnie. W przypadku korzystania z agregacji każdy pomiar musi być zagregowany.

### <a name="configure-actions"></a>Skonfiguruj akcje

W tej sekcji pokazano, jak skonfigurować akcje do wykonania, gdy reguła jest wyzwalana. Akcje są wywoływane, gdy wszystkie warunki określone w regule mają wartość true.

1. Wybierz **+** obok pozycji **Akcje**. W tym miejscu zostanie wyświetlona lista dostępnych akcji.  

    ![Dodaj akcję](media/howto-create-telemetry-rules/add_action1.png)

1. Wybierz akcję **e-mail** , wprowadź prawidłowy adres e-mail w polu **do** , a następnie podaj notatkę, która będzie wyświetlana w treści wiadomości e-mail, gdy reguła jest wyzwalana.

    > [!NOTE]
    > Wiadomości e-mail są wysyłane tylko do użytkowników, którzy zostali dodani do aplikacji i zarejestrowali się co najmniej raz. Dowiedz się więcej na temat [zarządzania użytkownikami](howto-administer.md) w usłudze Azure IoT Central.

   ![Konfiguruj akcję](media/howto-create-telemetry-rules/configure_action1.png)

1. Aby zapisać regułę, wybierz pozycję **Zapisz**. Reguła przechodzi na żywo w ciągu kilku minut i zacznie monitorować dane telemetryczne wysyłane do aplikacji. Gdy warunek określony w regule zostanie spełniony, reguła wyzwala skonfigurowaną akcję poczty e-mail.

Do reguły można dodać inne akcje, takie jak Microsoft Flow i elementy webhook. Można dodać maksymalnie 5 akcji dla każdej reguły.

- [Microsoft Flow akcję](howto-add-microsoft-flow.md) uruchamiania przepływu pracy w Microsoft Flow, gdy reguła jest wyzwalana 
- [Akcja elementu webhook](howto-create-webhooks.md) , która powiadamia inne usługi o wyzwoleniu reguły

## <a name="parameterize-the-rule"></a>Sparametryzuj regułę

Reguły mogą wyprowadzać pewne geograficznej z **Właściwości urządzenia** jako parametry. Używanie parametrów jest przydatne w scenariuszach, w których progi telemetrii różnią się w zależności od różnych urządzeń. Podczas tworzenia reguły wybierz właściwość urządzenia, która określa próg, taki jak **Maksymalny idealny próg**, zamiast podać wartość bezwzględną, na przykład 70&deg; F. Gdy reguła jest wykonywana, dopasowuje dane telemetryczne urządzenia z wartością ustawioną we właściwości urządzenia.

Używanie parametrów jest efektywnym sposobem na zmniejszenie liczby reguł do zarządzania dla każdego szablonu urządzenia.

Akcje można również skonfigurować za pomocą **Właściwości urządzenia** jako parametru. Jeśli adres e-mail jest przechowywany jako właściwość, można go użyć podczas definiowania adresu **do** .

## <a name="delete-a-rule"></a>Usuwanie reguły

Jeśli reguła nie jest już potrzebna, usuń ją, otwierając regułę i wybierając pozycję **Usuń**. Usunięcie reguły spowoduje usunięcie jej z szablonu urządzenia i wszystkich skojarzonych urządzeń.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Włączanie lub wyłączanie reguły dla szablonu urządzenia

Przejdź do urządzenia i wybierz regułę, którą chcesz włączyć lub wyłączyć. Przełącz **regułę włączania dla wszystkich urządzeń tego szablonu** w regule, aby włączyć lub wyłączyć regułę dla wszystkich urządzeń, które są skojarzone z szablonem urządzenia.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Włączanie lub wyłączanie reguły dla urządzenia

Przejdź do urządzenia i wybierz regułę, którą chcesz włączyć lub wyłączyć. Przełącz **regułę włączania dla tego urządzenia** , aby włączyć lub wyłączyć regułę dla tego urządzenia.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak tworzyć reguły w aplikacji IoT Central platformy Azure, Oto kilka następnych kroków:

- [Dodawanie akcji Microsoft Flow w regułach](howto-add-microsoft-flow.md)
- [Dodaj akcję elementu webhook w regułach](howto-create-webhooks.md)
- [Grupuj wiele akcji do uruchomienia z jednej lub kilku reguł](howto-use-action-groups.md)
- [Jak zarządzać urządzeniami](howto-manage-devices.md)
