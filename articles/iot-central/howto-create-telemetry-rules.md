---
title: Tworzenie i zarządzanie regułami telemetrii w aplikacji usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Reguły telemetrii w usłudze Azure IoT Central umożliwiają monitorowanie urządzeń w czasie zbliżonym do rzeczywistego i automatycznie wywołują akcje, takie jak wysyłanie wiadomości e-mail po wyzwoleniu reguły.
author: ankitgupta
ms.author: ankitgup
ms.date: 02/02/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 0fb7cdaa24d139549545c93c920d60936d3c9fc1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60886123"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Utwórz regułę telemetrii i Konfigurowanie powiadomień w aplikacji usługi Azure IoT Central

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

Usługa Azure IoT Central umożliwia zdalne monitorowanie połączonych urządzeń. Reguły usługi Azure IoT Central umożliwiają monitorowanie urządzeń w czasie zbliżonym do rzeczywistego i automatycznie wykonywanie akcji, takich jak wysłać wiadomość e-mail lub wyzwalacza Microsoft Flow. Za pomocą kilku kliknięć można określić warunek, który chcesz monitorować dane urządzenia i skonfiguruj odpowiednie działanie. W tym artykule opisano sposób tworzenia reguły do monitorowania telemetrii wysyłanej przez urządzenie.

Urządzenia mogą korzystać z pomiaru telemetrii, aby wysłać dane liczbowe z urządzenia. Reguła telemetrii uaktywnia danych telemetrycznych z wybranego urządzenia przekracza określoną wartość progową.

## <a name="create-a-telemetry-rule"></a>Utwórz regułę telemetrii

Aby utworzyć regułę telemetrii, szablon urządzenia musi mieć co najmniej jednego pomiaru danych telemetrycznych zdefiniowane. W tym przykładzie użyto urządzenia mrożone Automat, który wysyła dane telemetryczne dotyczące temperatury i wilgotności. Reguła monitoruje temperatura zgłoszona przez urządzenia i wysyła wiadomość e-mail, gdy przejdzie ponad 80 stopni.

1. Za pomocą **szablonów urządzeń** stronie, przejść do szablonu urządzenia, dla którego dodajesz reguły dla.

1. Jeśli nie utworzono jeszcze żadnych reguł, zostanie wyświetlony następujący ekran:

    ![Jeszcze żadnych reguł](media/howto-create-telemetry-rules/Rules_Landing_Page.png)

1. Na **reguły** zaznacz **+ Nowa reguła** wyświetlić typy reguł, które można utworzyć.

1. Wybierz **Telemetrii** można utworzyć regułę do monitorowania danych telemetrycznych z urządzenia.

    ![Typy zasad](media/howto-create-telemetry-rules/Rule_Types.png)

1. Wprowadź nazwę, która pomaga w identyfikacji reguły, w tym szablonie urządzenia.

1. Aby od razu włączyć zasadę dla wszystkich urządzeń, które są tworzone dla tego szablonu, Przełącz **Włącz regułę dla wszystkich urządzeń dla tego szablonu**.

   ![Szczegóły reguły](media/howto-create-telemetry-rules/Rule_Detail.png)

    Reguła automatycznie stosuje do wszystkich urządzeń na podstawie szablonu urządzenia.

### <a name="configure-the-rule-conditions"></a>Konfigurowanie warunków reguły

Warunek definiuje kryteria, które są monitorowane przez regułę.

1. Wybierz **+** obok **warunki** można dodać nowego warunku.

1. Wybierz dane telemetryczne, którą chcesz monitorować z **pomiaru** listy rozwijanej.

1. Następnie wybierz pozycję **agregacji**, **Operator**i podaj **próg** wartość.
   - Agregacja jest opcjonalne. Bez agregacji, wyzwolenie reguły dla każdego punktu danych telemetrii, który spełnia warunek. Na przykład jeśli reguła jest skonfigurowany do wyzwalania, gdy temperatura przekracza 80, a następnie wyzwolenie reguły niemal natychmiast gdy urządzenie zgłosi temperatury > 80.
   - Jeśli funkcja agregująca takie jak średnia, wartość minimalna, maksymalna, liczba jest wybierany następnie użytkownik musi podać **przedział czasu agregacji** za pośrednictwem której warunek musi zostać ocenione. Na przykład jeśli ustawisz okres na "5 minut" i reguła szuka średnia temperatura powyżej 80, reguła jest wyzwalana w przypadku średnia temperatura przekracza 80 dla co najmniej 5 minut. Częstotliwość oceny reguły jest taka sama jak **przedział czasu agregacji**, oznacza to, w tym przykładzie reguła jest szacowana co 5 minut.

     ![Warunek](media/howto-create-telemetry-rules/Aggregate_Condition_Filled_Out.png)

     >[!NOTE]
     >Można dodać więcej niż jednej miary dane telemetryczne w ramach **warunek**. Jeśli określono wiele warunków, wszystkie warunki muszą być spełnione dla tej reguły wyzwolić. Każdy warunek pobiera przyłączone niejawnie przez klauzulę "I". Korzystając z agregacji, musi być agregowana co miary.

### <a name="configure-actions"></a>Konfigurowanie akcji

W tej sekcji pokazano, jak skonfigurować akcje do wykonania po wyzwoleniu reguły. Akcje Pobierz wywoływane, gdy wszystkie warunki określone w regule zostaną obliczone na wartość true.

1. Wybierz **+** obok **akcje**. W tym miejscu zobaczysz listę dostępnych akcji.  

    ![Dodawanie akcji](media/howto-create-telemetry-rules/Add_Action.png)

1. Wybierz **E-mail** akcję, wprowadź prawidłowy adres e-mail w **do** pola, a następnie podaj notatkę pojawią się w treści wiadomości e-mail po wyzwoleniu reguły.

    > [!NOTE]
    > Wiadomości e-mail są wysyłane tylko do użytkowników, które zostały dodane do aplikacji i Logowanie zostało wykonane co najmniej raz. Dowiedz się więcej o [Zarządzanie użytkownikami](howto-administer.md) w usłudze Azure IoT Central.

   ![Konfigurowanie akcji](media/howto-create-telemetry-rules/Configure_Action.png)

1. Aby zapisać regułę, wybierz **Zapisz**. Reguła przechodzi na żywo w ciągu kilku minut i rozpoczyna monitorowanie dane telemetryczne są wysyłane do aplikacji. Gdy zostanie spełniony warunek określony w regule, zasada wyzwala akcji dotyczącej wiadomości e-mail skonfigurowany.

Możesz dodać inne akcje reguły, takie jak Microsoft Flow i elementów webhook. Możesz dodać maksymalnie 5 czynności dla każdej reguły.

- [Microsoft Flow akcji](howto-add-microsoft-flow.md) Konferencję przepływu pracy w Microsoft Flow po wyzwoleniu reguły 
- [Akcja elementu Webhook](howto-create-webhooks.md) do powiadamiania innych usług, po wyzwoleniu reguły

## <a name="parameterize-the-rule"></a>Parametryzacja reguły

Reguły mogą generować niektóre wartości z **właściwości urządzenia** jako parametry. Przy użyciu parametrów jest przydatne w scenariuszach, gdzie telemetria progi różnią się na różnych urządzeniach. Podczas tworzenia reguły, wybierz polecenie Właściwości urządzenia, który określa próg, takie jak **osiągnie maksymalny próg idealne**, zamiast podawać wartości bezwzględnej, takie jak 80 stopni. Zasada jest wykonywana, jest on zgodny danych telemetrycznych z urządzenia z wartością ustawioną we właściwości urządzenia.

Przy użyciu parametrów jest efektywny sposób zmniejszenia liczby reguł do zarządzania na urządzeniu szablonu.

Akcje mogą być również konfigurowane przy użyciu **właściwości urządzenia** jako parametr. Jeśli adres e-mail jest przechowywany jako właściwość, a następnie mogą być używane podczas definiowania **do** adresu.

## <a name="delete-a-rule"></a>Usuwanie reguły

Jeśli nie potrzebujesz już regułę, usuń go, otwierając reguły i wybierając pozycję **Usuń**. Usuwanie reguły spowoduje usunięcie jej z szablonu urządzenia i wszystkie skojarzone urządzenia.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Włączanie lub wyłączanie reguły szablonu urządzenia

Przejdź do urządzenia, a następnie wybierz regułę, którą chcesz włączyć lub wyłączyć. Przełącz **Włącz regułę dla wszystkich urządzeń z tego szablonu** przycisk reguły, aby włączyć lub wyłączyć regułę dla wszystkich urządzeń, które są skojarzone z szablonem urządzenia.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Włącza lub wyłącza regułę dla urządzenia

Przejdź do urządzenia, a następnie wybierz regułę, którą chcesz włączyć lub wyłączyć. Przełącz **Włącz regułę dla tego urządzenia** przycisk, aby włączyć lub wyłączyć regułę dla tego urządzenia.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak utworzyć zasady w aplikacji usługi Azure IoT Central, poniżej przedstawiono niektóre następny krok:

- [Dodawanie akcji Microsoft Flow w regułach](howto-add-microsoft-flow.md)
- [Dodawanie akcji elementu Webhook w regułach](howto-create-webhooks.md)
- [Wiele akcji, aby uruchamiała się z co najmniej jedną regułę grupy](howto-use-action-groups.md)
- [Jak zarządzać urządzeniami](howto-manage-devices.md)
