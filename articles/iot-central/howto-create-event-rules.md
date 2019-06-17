---
title: Tworzenie i zarządzanie nimi reguły zdarzenia w aplikacji usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Zasady zdarzeń w usłudze Azure IoT Central umożliwiają monitorowanie urządzeń w czasie zbliżonym do rzeczywistego i automatycznie wywołują akcje, takie jak wysyłanie wiadomości e-mail po wyzwoleniu reguły.
author: ankitscribbles
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 4754e6b571845d286ef22014f87b86fae2f6633d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053017"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Tworzenie reguły zdarzeń i Konfigurowanie powiadomień w aplikacji usługi Azure IoT Central

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

Usługa Azure IoT Central umożliwia zdalne monitorowanie połączonych urządzeń. Reguły usługi Azure IoT Central umożliwiają monitorowanie urządzeń w czasie zbliżonym do rzeczywistego i automatycznie wykonywanie akcji, takich jak wysłać wiadomość e-mail lub wyzwalacza Microsoft Flow. Za pomocą kilku kliknięć można określić warunek, który chcesz monitorować dane urządzenia i skonfiguruj odpowiednie działanie. W tym artykule opisano sposób tworzenia reguły, aby monitorować zdarzenia wysyłane przez urządzenie.

Urządzenia mogą korzystać pomiaru zdarzenia do wysłania zdarzenia ważne lub informacyjny urządzenia. Zasada zdarzeń uaktywnia zdarzeń wybrane urządzenie jest zgłaszane przez urządzenie.

## <a name="create-an-event-rule"></a>Tworzenie reguły zdarzeń

Aby utworzyć regułę zdarzeń, szablon urządzenia musi mieć co najmniej jedno zdarzenie pomiarów zdefiniowane. W tym przykładzie użyto urządzenia mrożone Automat, która zgłasza zdarzenie błędu motor wentylator. Reguła monitoruje zdarzeń zgłoszonych przez urządzenia i wysyła wiadomość e-mail, gdy zdarzenie jest zgłaszane.

1. Za pomocą **szablonów urządzeń** stronie, przejść do szablonu urządzenia, dla którego dodajesz reguły dla.

1. Jeśli nie utworzono jeszcze żadnych reguł, zostanie wyświetlony następujący ekran:

    ![Jeszcze żadnych reguł](media/howto-create-event-rules/rules_landing_page1.png)

1. Na **reguły** zaznacz **+ Nowa reguła** wyświetlić typy reguł, które można utworzyć.

1. Wybierz **zdarzeń** Kafelek, aby utworzyć zdarzenia reguła monitorowania.

    ![Typy zasad](media/howto-create-event-rules/rule_types1.png)

1. Wprowadź nazwę, która pomaga w identyfikacji reguły, w tym szablonie urządzenia.

1. Aby od razu włączyć zasadę dla wszystkich urządzeń, które są tworzone na podstawie tego szablonu, Przełącz **Włącz regułę dla wszystkich urządzeń z tego szablonu**.

    ![Szczegóły reguły](media/howto-create-event-rules/rule_detail1.png)

    Reguła automatycznie stosuje do wszystkich urządzeń na podstawie szablonu urządzenia.

### <a name="configure-the-rule-conditions"></a>Konfigurowanie warunków reguły

Warunek definiuje kryteria, które są monitorowane przez regułę.

1. Wybierz **+** obok **warunki** można dodać nowego warunku.

1. Wybierz zdarzenie, które chcesz monitorować, z listy rozwijanej miary. W tym przykładzie **błąd Motor wentylator** zdarzeń został wybrany.

   ![Warunek](media/howto-create-event-rules/condition_filled_out1.png)

1. Opcjonalnie możesz również ustawić **liczba** jako **agregacji** i podaj odpowiednie wartości progowej.

   - Bez agregacji, wyzwolenie reguły dla każdego punktu danych zdarzeń, który spełnia warunek. Na przykład, jeśli konfigurujesz reguły warunku Wyzwalaj, gdy **błąd Motor wentylator** wystąpi zdarzenie, a następnie wyzwoleniu reguły niemal natychmiast, gdy urządzenie zgłosi tego zdarzenia.
   - Jeśli liczba jest używana jako funkcji agregującej, a następnie trzeba podać **próg** i **przedział czasu agregacji** za pośrednictwem której warunek musi zostać ocenione. W tym przypadku wartość jest agregowana liczbę zdarzeń i wyzwolenie reguły tylko wtedy, gdy liczba zdarzeń zagregowane dopasowuje wartość progową.

     Na przykład jeśli chcesz alert, gdy istnieje więcej niż trzy zdarzenia urządzenia w ciągu 5 minut, następnie wybierz zdarzenie i zestawu funkcji agregującej jako "liczba", operator jako "większe niż" i "próg" jako 3. Ustaw "Agregacji przedziale czasu" na "5 minut". Reguła jest wyzwalana w przypadku więcej niż trzy zdarzenia są wysyłane przez urządzenie w ciągu 5 minut. Częstotliwość oceny reguły jest taka sama jak **przedział czasu agregacji**, oznacza to, w tym przykładzie reguła jest szacowana co 5 minut.

     ![Dodaj warunek zdarzenia](media/howto-create-event-rules/aggregate_condition_filled_out1.png)

     >[!NOTE]
     >Można dodać więcej niż jednej miary zdarzeń w obszarze **warunek**. Jeśli określono wiele warunków, wszystkie warunki muszą być spełnione dla tej reguły wyzwolić. Każdy warunek pobiera przyłączone niejawnie przez klauzulę "I". Korzystając z agregacji, musi być agregowana co miary.

### <a name="configure-actions"></a>Konfigurowanie akcji

W tej sekcji pokazano, jak skonfigurować akcje do wykonania po wyzwoleniu reguły. Akcje Pobierz wywoływane, gdy wszystkie warunki określone w regule zostaną obliczone na wartość true.

1. Wybierz **+** obok **akcje**. W tym miejscu zobaczysz listę dostępnych akcji.

    ![Dodawanie akcji](media/howto-create-event-rules/add_action1.png)

1. Wybierz **E-mail** akcję, wprowadź prawidłowy adres e-mail w **do** pola, a następnie podaj notatkę pojawią się w treści wiadomości e-mail po wyzwoleniu reguły.

    > [!NOTE]
    > Wiadomości e-mail są wysyłane tylko do użytkowników, które zostały dodane do aplikacji i Logowanie zostało wykonane co najmniej raz. Dowiedz się więcej o [Zarządzanie użytkownikami](howto-administer.md) w usłudze Azure IoT Central.

   ![Konfigurowanie akcji](media/howto-create-event-rules/configure_action1.png)

1. Aby zapisać regułę, wybierz **Zapisz**. Reguła przechodzi na żywo w ciągu kilku minut i rozpoczyna monitorowanie zdarzeń wysyłanych do aplikacji. Gdy spełnia warunek określony w regule, zasada wyzwala akcji dotyczącej wiadomości e-mail skonfigurowany.

Możesz dodać inne akcje reguły, takie jak Microsoft Flow i elementów webhook. Możesz dodać maksymalnie 5 czynności dla każdej reguły.

- [Microsoft Flow akcji](howto-add-microsoft-flow.md) Konferencję przepływu pracy w Microsoft Flow po wyzwoleniu reguły 
- [Akcja elementu Webhook](howto-create-webhooks.md) do powiadamiania innych usług, po wyzwoleniu reguły

## <a name="parameterize-the-rule"></a>Parametryzacja reguły

Akcje mogą być również konfigurowane przy użyciu **właściwości urządzenia** jako parametr. Jeśli adres e-mail jest przechowywany jako właściwości urządzenia, a następnie mogą być używane podczas definiowania **do** adresu.

## <a name="delete-a-rule"></a>Usuwanie reguły

Jeśli nie potrzebujesz już regułę, usuń go, otwierając reguły i wybierając pozycję **Usuń**. Usuwanie reguły spowoduje usunięcie jej z szablonu urządzenia i wszystkie skojarzone urządzenia.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Włączanie lub wyłączanie reguły szablonu urządzenia

Przejdź do urządzenia, a następnie wybierz regułę, którą chcesz włączyć lub wyłączyć. Przełącz **Włącz regułę dla wszystkich urządzeń z tego szablonu** przycisk, aby włączyć lub wyłączyć regułę dla wszystkich urządzeń, które są skojarzone z szablonem urządzenia.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Włącza lub wyłącza regułę dla urządzenia

Przejdź do urządzenia, a następnie wybierz regułę, którą chcesz włączyć lub wyłączyć. Przełącz **Włącz regułę dla tego urządzenia** przycisk, aby włączyć lub wyłączyć regułę dla tego urządzenia.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak utworzyć zasady w aplikacji usługi Azure IoT Central, poniżej przedstawiono niektóre następny krok:

- [Dodawanie akcji Microsoft Flow w regułach](howto-add-microsoft-flow.md)
- [Dodawanie akcji elementu Webhook w regułach](howto-create-webhooks.md)
- [Wiele akcji, aby uruchamiała się z co najmniej jedną regułę grupy](howto-use-action-groups.md)
- [Jak zarządzać urządzeniami](howto-manage-devices.md)
