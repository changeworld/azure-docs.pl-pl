---
title: Zdalne monitorowanie IoT i powiadomień przy użyciu usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Używać usługi Azure Logic Apps na potrzeby IoT temperatury monitorowania w Centrum IoT i automatycznie wysyłać powiadomienia e-mail do skrzynki pocztowej dla wszelkich wykrytych anomalii.
author: robinsh
keywords: powiadomienia dotyczące monitorowania, iot, iot monitorowania temperatury iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/19/2019
ms.author: robinsh
ms.openlocfilehash: 26637468f44e12f7ad66f907e0f6be3d907e578f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126211"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Zdalne monitorowanie IoT i powiadomień za pomocą usługi Azure Logic Apps, łącząc usługę IoT hub i skrzynki pocztowej

![Diagram end-to-end](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Usługa Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) może ułatwić organizowanie przepływów pracy w środowiskach lokalnych i usługi w chmurze, co najmniej jeden przedsiębiorstwa i w różnych protokołów. Aplikacja logiki rozpoczyna się od wyzwalacza, który następuje co najmniej jednej akcji, które sekwencjonowania przy użyciu wbudowanych formantów, takich jak warunki i Iteratory. Ta elastyczność sprawia, że Logic Apps stanowi doskonałe rozwiązanie IoT dla scenariuszy monitorowania, IoT. Na przykład przybycia danych telemetrycznych z urządzeń w punkcie końcowym usługi IoT Hub można zainicjować magazynu danych w usłudze Azure Storage blob, wysyłania alertów e-mail Ostrzegaj anomalii w danych, planowanie odwiedziny technik, jeśli urządzenie zgłasza błąd, przepływów pracy aplikacji logiki , i tak dalej.

## <a name="what-you-learn"></a>Omawiane zagadnienia

Dowiesz się, jak utworzyć aplikację logiki, która łączy Centrum IoT hub i skrzynki pocztowej do monitorowania temperatury i powiadomienia.

Kod klienta, na urządzeniu z ustawia właściwość aplikacji `temperatureAlert`na każdy telemetrii komunikatów wysyła do Centrum IoT hub. Gdy kod klienta wykryje temperatura powyżej 30 C, ustawia tę właściwość na `true`; w przeciwnym razie do nadawania właściwości `false`.

W tym temacie konfigurowania routingu w usłudze IoT hub do wysyłania wiadomości, w którym `temperatureAlert = true` do usługi Service Bus punktu końcowego i możesz skonfigurować aplikację logiki, która wyzwala na komunikaty przychodzące w punkcie końcowym usługi Service Bus oraz wysyła do Ciebie wiadomość e-mail z powiadomieniem.

## <a name="what-you-do"></a>Co należy zrobić

* Tworzenie przestrzeni nazw usługi Service Bus, a następnie dodać kolejki usługi Service Bus do niego.
* Dodaj niestandardowy punkt końcowy i reguły routingu do Centrum IoT hub do przesyłania wiadomości, zawierające alert dotyczący temperatury do kolejki usługi Service Bus.
* Tworzenie, konfigurowanie i przetestować aplikację logiki, aby używać komunikatów z kolejki usługi Service Bus i wysyłanie wiadomości e-mail z powiadomieniem do żądanego adresata.

## <a name="what-you-need"></a>Co jest potrzebne

* Wykonaj [symulatora w trybie online urządzenia Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) samouczka lub jednym z samouczków urządzenia; na przykład [Raspberry Pi w środowisku node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Obejmują one następujące wymagania:

  * Aktywna subskrypcja platformy Azure.
  * Usługi Azure IoT hub w ramach Twojej subskrypcji.
  * Aplikacja kliencka, uruchomione na urządzeniu, która wysyła komunikaty telemetryczne do usługi Azure IoT hub.

## <a name="create-service-bus-namespace-and-queue"></a>Tworzenie przestrzeni nazw usługi Service Bus i kolejki

Utwórz przestrzeń nazw i kolejkę usługi Service Bus. W dalszej części tego tematu można utworzyć regułę routingu w usłudze IoT hub do komunikatów bezpośrednich, które zawierają alert dotyczący temperatury do kolejki usługi Service Bus, w której zostaną pobrane przez aplikację logiki i wyzwalać je, aby wysłać wiadomość e-mail z powiadomieniem.

### <a name="create-a-service-bus-namespace"></a>Tworzenie przestrzeni nazw usługi Service Bus

1. Na [witryny Azure portal](https://portal.azure.com/), wybierz opcję **+ Utwórz zasób** > **integracji** > **usługi Service Bus**.

1. Na **tworzenie przestrzeni nazw** okienku, podaj następujące informacje:

   **Nazwa**: Nazwa przestrzeni nazw magistrali usług. Przestrzeń nazw musi być unikatowa w obrębie platformy Azure.

   **Warstwa cenowa**: Wybierz **podstawowe** z listy rozwijanej. Warstwa podstawowa jest wystarczająca na potrzeby tego samouczka.

   **Grupa zasobów**: Użyj tej samej grupie zasobów, która korzysta z usługi IoT hub.

   **Lokalizacja**: Użyj tej samej lokalizacji, która korzysta z usługi IoT hub.

   ![Utwórz przestrzeń nazw magistrali usług w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Wybierz pozycję **Utwórz**. Poczekaj, aż wdrożenia, które należy wykonać przed przejściem do następnego kroku.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Dodawanie kolejki usługi Service Bus do przestrzeni nazw

1. Otwórz obszar nazw usługi Service Bus. Najprostszym sposobem, aby uzyskać dostęp do przestrzeni nazw usługi Service Bus jest wybranie **grup zasobów** z poziomu okienka zasobów wybierz grupę zasobów, a następnie wybierz przestrzeń nazw usługi Service Bus z listy zasobów.

1. Na **usługi Service Bus Namespace** okienku wybierz **+ kolejka**.

1. Wprowadź nazwę kolejki, a następnie wybierz pozycję **Utwórz**. Po pomyślnym utworzeniu kolejki **Utwórz kolejkę** okienko zostanie zamknięte.

   ![Dodawanie kolejki usługi service bus w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. Po powrocie **usługi Service Bus Namespace** okienku w obszarze **jednostek**, wybierz opcję **kolejek**. Otwórz kolejki usługi Service Bus z listy, a następnie wybierz **zasady dostępu współdzielonego** > **+ Dodaj**.

1. Wprowadź nazwę zasady wyboru **Zarządzaj**, a następnie wybierz pozycję **Utwórz**.

   ![Dodawanie zasad kolejki magistrali usług w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Dodawanie niestandardowego punktu końcowego i reguły routingu do Centrum IoT hub

Dodaj niestandardowy punkt końcowy dla kolejki usługi Service Bus do usługi IoT hub i Utwórz regułę routingu wiadomości do kierowania komunikatów, które zawierają alert dotyczący temperatury do określonego punktu końcowego, gdzie one zostaną pobrane przez aplikację logiki. Reguły routingu korzysta z zapytania routingu, `temperatureAlert = "true"`w celu przekazywania komunikatów na podstawie wartości z `temperatureAlert` aplikacji właściwości ustawione przez kod klienta uruchomionej na urządzeniu. Aby dowiedzieć się więcej, zobacz [komunikatu zapytań routingu na podstawie właściwości wiadomości](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Dodaj niestandardowy punkt końcowy

1. Otwórz Centrum IoT hub. Najprostszym sposobem, aby przejść do Centrum IoT hub jest wybranie **grup zasobów** z poziomu okienka zasobów wybierz grupę zasobów, a następnie wybierz swoje Centrum IoT z listy zasobów.

1. W obszarze **komunikatów**, wybierz opcję **routing komunikatów**. Na **routing komunikatów** okienku wybierz **niestandardowe punkty końcowe** , a następnie wybierz pozycję **+ Dodaj**. Wybierz z listy rozwijanej **kolejki usługi Service bus**.

   ![Dodawanie punktu końcowego do Centrum IoT hub w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. Na **Dodaj punkt końcowy usługi Service bus** okienku, wprowadź następujące informacje:

   **Nazwa punktu końcowego**: Nazwa punktu końcowego.

   **Przestrzeń nazw magistrali usług**: Wybierz utworzoną przestrzeń nazw.

   **Kolejki usługi Service bus**: Wybierz kolejkę, który został utworzony.

   ![Dodawanie punktu końcowego do Centrum IoT hub w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Wybierz pozycję **Utwórz**. Po pomyślnym utworzeniu punktu końcowego, należy przejść do następnego kroku.

### <a name="add-a-routing-rule"></a>Dodawanie reguły rozsyłania

1. Po powrocie **routing komunikatów** okienku wybierz **trasy** , a następnie wybierz pozycję **+ Dodaj**.

1. Na **Dodaj trasę** okienku, wprowadź następujące informacje:

   **Nazwa**: Nazwa reguły routingu.

   **Punkt końcowy**: Wybierz punkt końcowy, który został utworzony.

   **Źródło danych**: Wybierz **komunikaty telemetryczne urządzenia**.

   **Zapytanie dotyczące routingu**: Wprowadź polecenie `temperatureAlert = "true"`.

   ![Dodaj regułę routingu w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Wybierz pozycję **Zapisz**. Możesz zamknąć **routing komunikatów** okienka.

## <a name="create-and-configure-a-logic-app"></a>Tworzenie i konfigurowanie aplikacji logiki

W poprzedniej sekcji konfigurowania usługi IoT hub do przesyłania wiadomości, zawierającego alert dotyczący temperatury do kolejki usługi Service Bus. Teraz należy skonfigurować aplikację logiki w celu monitorowania kolejki usługi Service Bus i wysyłania powiadomień e-mail po każdym dodaniu komunikatu do kolejki.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

1. Wybierz **Utwórz zasób** > **integracji** > **aplikacji logiki**.

1. Wprowadź następujące informacje:

   **Nazwa**: Nazwa aplikacji logiki.

   **Grupa zasobów**: Użyj tej samej grupie zasobów, która korzysta z usługi IoT hub.

   **Lokalizacja**: Użyj tej samej lokalizacji, która korzysta z usługi IoT hub.

   ![Tworzenie aplikacji logiki w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Wybierz pozycję **Utwórz**.

### <a name="configure-the-logic-app-trigger"></a>Konfigurowanie wyzwalacza aplikacji logiki

1. Otwórz aplikację logiki. Najprostszym sposobem, aby uzyskać dostęp do aplikacji logiki jest wybranie **grup zasobów** z poziomu okienka zasobów wybierz grupę zasobów, a następnie wybierz aplikację logiki z listy zasobów. Po wybraniu aplikacji logiki, zostanie otwarty projektant aplikacji logiki.

1. W Projektancie aplikacji logiki, przewiń w dół do **szablony** i wybierz **pusta aplikacja logiki**.

   ![Uruchom przy użyciu pustej aplikacji logiki w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Wybierz **wszystkich** , a następnie wybierz pozycję **usługi Service Bus**.

   ![Wybierz usługi Service Bus, aby rozpocząć tworzenie aplikacji logiki w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. W obszarze **wyzwalaczy**, wybierz opcję **kiedy co najmniej jeden komunikat dociera w kolejce (Automatyczne zakończenie)**.

   ![Wybierz wyzwalacz aplikacji logiki w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Tworzenie połączenia usługi service bus.
   1. Wprowadź nazwę połączenia, a następnie wybierz przestrzeń nazw usługi Service Bus z listy. Zostanie otwarty kolejny ekran.

      ![Tworzenie połączenia usługi service bus dla twojej aplikacji logiki w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Wybierz zasady usługi service bus (RootManageSharedAccessKey). Następnie wybierz pozycję **Utwórz**.

      ![Tworzenie połączenia usługi service bus dla twojej aplikacji logiki w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Na ostatnim ekranie dla **Nazwa kolejki**, wybierz kolejkę, który został utworzony z listy rozwijanej. Wprowadź `175` dla **maksymalna liczba komunikatów**.

      ![Określ liczbę maksymalną komunikatu dla połączenia usługi service bus w aplikacji logiki](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Wybierz **Zapisz** w menu w górnej części Projektant aplikacji logiki, aby zapisać zmiany.

### <a name="configure-the-logic-app-action"></a>Konfigurowanie akcji aplikacji logiki

1. Utwórz połączenie usługi SMTP.

   1. Wybierz pozycję **Nowy krok**. W **wybierz akcję**, wybierz opcję **wszystkich** kartę.

   1. Typ `smtp` w polu wyszukiwania, wybierz **SMTP** w wynikach wyszukiwania, a następnie pozycję **Wyślij wiadomość E-mail**.

      ![Utwórz połączenie SMTP w Twojej aplikacji logiki w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Wprowadzić dane SMTP dla skrzynki pocztowej, a następnie wybierz **Utwórz**.

      ![Wprowadź informacje o połączeniu SMTP w Twojej aplikacji logiki w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Uzyskaj informacje SMTP dla [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en), i [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).

      > [!NOTE]
      > Może być konieczne wyłączenie protokołu SSL w celu nawiązania połączenia. Jeśli jest to możliwe, i chcesz ponownie włączyć protokół SSL, po nawiązaniu połączenia, zobacz krok opcjonalny, na końcu tej sekcji.

   1. Z **dodano nowy parametr** listę rozwijaną **Wyślij wiadomość E-mail** kroku, wybierz pozycję **z**, **do**, **podmiotu**i **treści**. Kliknij lub naciśnij dowolne miejsce na ekranie, aby zamknąć okno zaznaczenia.

      ![Wybierz pola wiadomości e-mail połączenie SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Wprowadź swój adres e-mail dla **z** i **do**, i `High temperature detected` dla **podmiotu** i **treści**. Jeśli **Dodaj zawartość dynamiczną z aplikacji i łączników używanych w tym przepływie** zostanie otwarte okno dialogowe, wybierz **Ukryj** go zamknąć. Nie należy używać zawartości dynamicznej, w tym samouczku.

      ![Pola wiadomości e-mail połączenie SMTP wypełnianie](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Wybierz **Zapisz** można zapisać połączenia SMTP.

1. (Opcjonalnie) Gdyby trzeba było wyłączyć protokół SSL, aby nawiązać połączenie przy użyciu dostawcy poczty e-mail i chcesz ją ponownie włączyć, wykonaj następujące kroki:

   1. Na **aplikacji logiki** okienku w obszarze **narzędzia programistyczne**, wybierz opcję **połączenia interfejsu API**.

   1. Z listy połączenia interfejsu API wybierz połączenie SMTP.

   1. Na **smtp połączenie z interfejsem API** okienku w obszarze **ogólne**, wybierz opcję **połączenia Edytuj interfejsu API**.

   1. Na **Edytowanie połączenia interfejsu API** okienku wybierz **Włącz SSL?**, wprowadź ponownie hasło dla konta e-mail, a wybierz **Zapisz**.

      ![Edytuj połączenie interfejsu API SMTP w aplikacji logiki w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

Twoja aplikacja logiki jest teraz gotowy do przetwarzania temperatury alertów z kolejki usługi Service Bus i wysyłania powiadomień do swojego konta e-mail.

## <a name="test-the-logic-app"></a>Testowanie aplikacji logiki

1. Uruchom aplikację klienta, na urządzeniu.

1. Jeśli używasz urządzenia fizycznego dokładnie Przełącz źródło ciepła niemal czujnik częstości dostępu do momentu temperatura przekroczy 30 C. stopni Jeśli używasz symulatora w trybie online, kod klienta losowo dane wyjściowe komunikaty telemetryczne, które przekraczają 30 C.

1. Należy rozpocząć odbieranie powiadomień e-mail wysłanych przez aplikację logiki.

   > [!NOTE]
   > Dostawcy usługi poczty e-mail może być konieczne Sprawdź tożsamość nadawcy, aby upewnić się, że chodzi o WAS, który umożliwia wysłanie wiadomości e-mail.

## <a name="next-steps"></a>Kolejne kroki

Pomyślnie utworzono aplikację logiki, która łączy Centrum IoT hub i skrzynki pocztowej do monitorowania temperatury i powiadomienia.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
