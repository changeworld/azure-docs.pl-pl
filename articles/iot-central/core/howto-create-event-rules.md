---
title: Tworzenie reguł zdarzeń i zarządzanie nimi w aplikacji usługi Azure IoT Central | Microsoft Docs
description: Reguły zdarzeń IoT Central platformy Azure umożliwiają monitorowanie urządzeń w czasie niemal rzeczywistym oraz automatyczne wywoływanie akcji, takich jak wysyłanie wiadomości e-mail, gdy reguła jest wyzwalana.
author: ankitscribbles
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: f932cb9621e4cfc62e2bb00d11030a7ff03450fc
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954021"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Tworzenie reguły zdarzeń i Konfigurowanie powiadomień w aplikacji IoT Central platformy Azure

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Za pomocą usługi Azure IoT Central można zdalnie monitorować połączone urządzenia. Reguły IoT Central platformy Azure umożliwiają monitorowanie urządzeń w czasie niemal rzeczywistym i automatyczne wywoływanie akcji, takich jak wysyłanie wiadomości e-mail lub wyzwalacza Microsoft Flow. Wystarczy kilka kliknięć, aby zdefiniować warunek monitorowania danych urządzenia i konfigurowania odpowiedniej akcji. W tym artykule wyjaśniono, jak utworzyć reguły monitorowania zdarzeń wysyłanych przez urządzenie.

Urządzenia mogą używać pomiaru zdarzeń do wysyłania ważnych lub informacyjnych zdarzeń urządzeń. Reguła zdarzenia wyzwala, gdy wybrane zdarzenie urządzenia jest zgłaszane przez urządzenie.

## <a name="create-an-event-rule"></a>Tworzenie reguły zdarzeń

Aby utworzyć regułę zdarzenia, szablon urządzenia musi mieć zdefiniowany co najmniej jeden pomiar zdarzenia. W tym przykładzie używane jest urządzenie z systemem chłodzenia w stanie chłodnym, które zgłasza zdarzenie błędu silnika wentylatorowego. Reguła monitoruje zdarzenie zgłoszone przez urządzenie i wysyła wiadomość e-mail za każdym razem, gdy zdarzenie jest zgłaszane.

1. Korzystając ze strony **Szablony urządzeń** , przejdź do szablonu urządzenia, dla którego chcesz dodać regułę.

1. Jeśli nie utworzono jeszcze żadnych reguł, zobaczysz następujący ekran:

    ![Nie ma jeszcze reguł](media/howto-create-event-rules/rules_landing_page1.png)

1. Na karcie **reguły** wybierz pozycję **+ Nowa reguła** , aby wyświetlić typy reguł, które możesz utworzyć.

1. Wybierz kafelek **zdarzenia** , aby utworzyć regułę monitorowania zdarzeń.

    ![Typy reguł](media/howto-create-event-rules/rule_types1.png)

1. Wprowadź nazwę, która pomoże zidentyfikować regułę w tym szablonie urządzenia.

1. Aby natychmiast włączyć regułę dla wszystkich urządzeń utworzonych na podstawie tego szablonu, przełącz **regułę włączania dla wszystkich urządzeń tego szablonu**.

    ![Szczegóły reguły](media/howto-create-event-rules/rule_detail1.png)

    Reguła zostanie automatycznie zastosowana do wszystkich urządzeń objętych szablonem urządzenia.

### <a name="configure-the-rule-conditions"></a>Skonfiguruj warunki reguły

Warunek definiuje kryteria, które są monitorowane przez regułę.

1. Wybierz **+** obok pozycji **warunki** , aby dodać nowy warunek.

1. Wybierz zdarzenie, które chcesz monitorować, z listy rozwijanej miara. W tym przykładzie wybrano zdarzenie **błędu silnika wentylator** .

   ![Warunek](media/howto-create-event-rules/condition_filled_out1.png)

1. Opcjonalnie możesz również ustawić **liczbę** jako **agregację** i podać odpowiedni próg.

   - Bez agregacji reguła wyzwala dla każdego punktu danych zdarzenia, który spełnia warunek. Na przykład, jeśli skonfigurujesz warunek reguły do wyzwalania, gdy wystąpi zdarzenie **błędu silnika wentylator** , reguła jest wyzwalana niemal natychmiast po zaraportowaniu tego zdarzenia przez urządzenie.
   - Jeśli liczba jest używana jako funkcja agregująca, należy podać **wartość progową** i **łączny przedział czasu** , w którym należy oszacować warunek. W tym przypadku liczba zdarzeń jest agregowana i reguła jest wyzwalana tylko wtedy, gdy zagregowana liczba zdarzeń dopasowuje wartość progową.

     Jeśli na przykład chcesz otrzymywać alerty w przypadku więcej niż trzech zdarzeń dotyczących urządzeń w ciągu 5 minut, wybierz zdarzenie i ustaw funkcję agregującą jako "Count", operator jako "większe niż" i "Threshold" jako 3. Ustaw wartość "okres agregacji" jako "5 minut". Reguła jest wyzwalana, gdy urządzenie zostanie wysłane więcej niż trzy zdarzenia w ciągu 5 minut. Częstotliwość oceny reguł jest taka sama jak w **przedziale czasu zagregowanym**, co oznacza, że w tym przykładzie reguła jest szacowana co 5 minut.

     ![Dodaj warunek zdarzenia](media/howto-create-event-rules/aggregate_condition_filled_out1.png)

     >[!NOTE]
     >W **warunku**można dodać więcej niż jeden pomiar zdarzenia. W przypadku określenia wielu warunków wszystkie warunki muszą być spełnione, aby reguła była wyzwalana. Każdy warunek jest przyłączony przez klauzulę "i" niejawnie. W przypadku korzystania z agregacji każdy pomiar musi być zagregowany.

### <a name="configure-actions"></a>Skonfiguruj akcje

W tej sekcji pokazano, jak skonfigurować akcje do wykonania, gdy reguła jest wyzwalana. Akcje są wywoływane, gdy wszystkie warunki określone w regule mają wartość true.

1. Wybierz **+** obok pozycji **Akcje**. W tym miejscu zostanie wyświetlona lista dostępnych akcji.

    ![Dodaj akcję](media/howto-create-event-rules/add_action1.png)

1. Wybierz akcję **e-mail** , wprowadź prawidłowy adres e-mail w polu **do** , a następnie podaj notatkę, która będzie wyświetlana w treści wiadomości e-mail, gdy reguła jest wyzwalana.

    > [!NOTE]
    > Wiadomości e-mail są wysyłane tylko do użytkowników, którzy zostali dodani do aplikacji i zarejestrowali się co najmniej raz. Dowiedz się więcej na temat [zarządzania użytkownikami](howto-administer.md) w usłudze Azure IoT Central.

   ![Konfiguruj akcję](media/howto-create-event-rules/configure_action1.png)

1. Aby zapisać regułę, wybierz pozycję **Zapisz**. Reguła przechodzi na żywo w ciągu kilku minut i rozpocznie monitorowanie zdarzeń wysyłanych do aplikacji. Gdy warunek określony w regule jest zgodny, reguła wyzwala skonfigurowaną akcję poczty e-mail.

Do reguły można dodać inne akcje, takie jak Microsoft Flow i elementy webhook. Można dodać maksymalnie 5 akcji dla każdej reguły.

- [Microsoft Flow akcję](howto-add-microsoft-flow.md) uruchamiania przepływu pracy w Microsoft Flow, gdy reguła jest wyzwalana 
- [Akcja elementu webhook](howto-create-webhooks.md) , która powiadamia inne usługi o wyzwoleniu reguły

## <a name="parameterize-the-rule"></a>Sparametryzuj regułę

Akcje można również skonfigurować za pomocą **Właściwości urządzenia** jako parametru. Jeśli adres e-mail jest przechowywany jako właściwość urządzenia, można go użyć podczas definiowania adresu **do** .

## <a name="delete-a-rule"></a>Usuwanie reguły

Jeśli reguła nie jest już potrzebna, usuń ją, otwierając regułę i wybierając pozycję **Usuń**. Usunięcie reguły spowoduje usunięcie jej z szablonu urządzenia i wszystkich skojarzonych urządzeń.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Włączanie lub wyłączanie reguły dla szablonu urządzenia

Przejdź do urządzenia i wybierz regułę, którą chcesz włączyć lub wyłączyć. Przełącz **regułę włączania dla wszystkich urządzeń tego szablonu** , aby włączyć lub wyłączyć regułę dla wszystkich urządzeń, które są skojarzone z szablonem urządzenia.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Włączanie lub wyłączanie reguły dla urządzenia

Przejdź do urządzenia i wybierz regułę, którą chcesz włączyć lub wyłączyć. Przełącz **regułę włączania dla tego urządzenia** , aby włączyć lub wyłączyć regułę dla tego urządzenia.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak tworzyć reguły w aplikacji IoT Central platformy Azure, Oto kilka następnych kroków:

- [Dodawanie akcji Microsoft Flow w regułach](howto-add-microsoft-flow.md)
- [Dodaj akcję elementu webhook w regułach](howto-create-webhooks.md)
- [Grupuj wiele akcji do uruchomienia z jednej lub kilku reguł](howto-use-action-groups.md)
- [Jak zarządzać urządzeniami](howto-manage-devices.md)
