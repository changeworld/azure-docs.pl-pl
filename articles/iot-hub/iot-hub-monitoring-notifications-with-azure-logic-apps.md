---
title: Zdalne monitorowanie IoT i powiadomienia przy użyciu usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Korzystanie z aplikacji logiki Azure IoT temperatury monitorowania w Centrum IoT i automatycznie wysyłać powiadomienia e-mail do skrzynki pocztowej do wszelkich wykrytych nieprawidłowości.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: monitorowanie powiadomienia iot iot monitorowania temperatury iot
ms.assetid: 43043067-2e1f-42c9-953d-e2dce8fd86df
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: e54c36d0cfbaedb93db86ad6ce5f99b288b63c9c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Zdalne monitorowanie IoT i powiadomienia przy użyciu usługi Azure Logic Apps łączenia z Centrum IoT i skrzynek pocztowych

![Diagram end-to-end](media/iot-hub-get-started-e2e-diagram/7.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Aplikacje logiki platformy Azure umożliwia automatyzowanie procesów w postaci serii kroków. Aplikacja logiki mogą się łączyć przez różnych usług i protokołów. Rozpoczyna się wyzwalacz takich jak "Po dodaniu konta" i następuje kombinacją akcji, jak "wysyła powiadomienie wypychane". Ta funkcja umożliwia Logic Apps to idealne rozwiązanie IoT dla IoT monitorowania, takich jak pozostaje alert w celu wykrycia nieprawidłowości, między innymi scenariusze użycia.

## <a name="what-you-learn"></a>Omawiane zagadnienia

Sposób do tworzenia aplikacji logiki łączącej centrum IoT i skrzynki pocztowej temperatury monitorowania i powiadomień. Gdy temperatura jest ponad 30 C, aplikacja kliencka oznacza `temperatureAlert = "true"` w komunikacie wysyła do Centrum IoT. Komunikat wyzwala aplikację logiki, aby wysłać wiadomość e-mail z powiadomieniem.

## <a name="what-you-do"></a>Co zrobić

* Utwórz przestrzeń nazw magistrali usług i Dodaj do niej kolejki.
* Dodawanie punktu końcowego i reguły routingu do Centrum IoT.
* Tworzenie, konfigurowanie i testowanie aplikacji logiki.

## <a name="what-you-need"></a>Co jest potrzebne

* Samouczek [skonfigurować Twoje urządzenie](iot-hub-raspberry-pi-kit-node-get-started.md) ukończone, która obejmuje następujące wymagania:
  * Aktywna subskrypcja platformy Azure.
  * Centrum Azure IoT w ramach Twojej subskrypcji.
  * Aplikacja klienta, która wysyła komunikaty do Centrum Azure IoT.

## <a name="create-service-bus-namespace-and-add-a-queue-to-it"></a>Utwórz przestrzeń nazw magistrali usług i Dodaj do niej kolejki

### <a name="create-a-service-bus-namespace"></a>Utwórz przestrzeń nazw magistrali usług

1. Na [portalu Azure](https://portal.azure.com/), kliknij przycisk **Utwórz zasób** > **integracji przedsiębiorstwa** > **usługi Service Bus**.
1. Podaj następujące informacje:

   **Nazwa**: Nazwa usługi service bus.

   **Warstwa cenowa**: kliknij **podstawowe** > **wybierz**. Warstwa podstawowa jest odpowiednia dla tego samouczka.

   **Grupa zasobów**: Użyj tej samej grupie zasobów, która używa Centrum IoT.

   **Lokalizacja**: Użyj tej samej lokalizacji, która używa Centrum IoT.
1. Kliknij przycisk **Utwórz**.

   ![Utwórz przestrzeń nazw magistrali usług w portalu Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1_create-service-bus-namespace-azure-portal.png)

### <a name="add-a-service-bus-queue"></a>Dodaj kolejką usługi service bus

1. Otwórz przestrzeń nazw magistrali usług, a następnie kliknij przycisk **+ kolejki**.
1. Wprowadź nazwę kolejki, a następnie kliknij przycisk **Utwórz**.
1. Otwórz kolejką usługi service bus, a następnie kliknij przycisk **zasady dostępu współużytkowanego** > **+ Dodaj**.
1. Wprowadź nazwę zasady wyboru **Zarządzaj**, a następnie kliknij przycisk **Utwórz**.

   ![Dodaj kolejką usługi service bus w portalu Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2_add-service-bus-queue-azure-portal.png)

## <a name="add-an-endpoint-and-a-routing-rule-to-your-iot-hub"></a>Dodawanie punktu końcowego i reguły routingu do Centrum IoT

### <a name="add-an-endpoint"></a>Dodawanie punktu końcowego

1. Otwórz Centrum IoT, kliknij punkty końcowe > + Dodaj.
1. Wprowadź następujące informacje:

   **Nazwa**: Nazwa punktu końcowego.

   **Typ punktu końcowego**: Wybierz **kolejką usługi Service Bus**.

   **Przestrzeń nazw magistrali usług**: wybierz utworzoną przestrzeń nazw.

   **Kolejki usługi Service Bus**: Wybierz kolejki, został utworzony.
1. Kliknij przycisk **OK**.

   ![Dodawanie punktu końcowego do Centrum IoT w portalu Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3_add-iot-hub-endpoint-azure-portal.png)

### <a name="add-a-routing-rule"></a>Dodaj regułę routingu

1. W Centrum IoT kliknij **tras** > **+ Dodaj**.
1. Wprowadź następujące informacje:

   **Nazwa**: Nazwa reguły routingu.

   **Źródło danych**: Wybierz **DeviceMessages**.

   **Punkt końcowy**: Wybierz punkt końcowy został utworzony.

   **Długość ciągu zapytania**: wprowadź `temperatureAlert = "true"`.
1. Kliknij pozycję **Zapisz**.

   ![Dodaj regułę routingu w portalu Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4_add-routing-rule-azure-portal.png)

## <a name="create-and-configure-a-logic-app"></a>Tworzenie i konfigurowanie aplikacji logiki

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

1. W [portalu Azure](https://portal.azure.com/), kliknij przycisk **Utwórz zasób** > **integracji przedsiębiorstwa** > **aplikacji logiki**.
1. Wprowadź następujące informacje:

   **Nazwa**: Nazwa aplikacji logiki.

   **Grupa zasobów**: Użyj tej samej grupie zasobów, która używa Centrum IoT.

   **Lokalizacja**: Użyj tej samej lokalizacji, która używa Centrum IoT.
1. Kliknij przycisk **Utwórz**.

### <a name="configure-the-logic-app"></a>Konfigurowanie aplikacji logiki

1. Otwórz aplikację logiki, który zostanie otwarty w Projektancie aplikacji logiki.
1. W Projektancie aplikacji logiki, kliknij **pustą aplikację logiki**.

   ![Uruchom z aplikacji logiki pusty w portalu Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5_start-with-blank-logic-app-azure-portal.png)

1. Kliknij przycisk **Service Bus**.

   ![Wybierz usługi Service Bus, aby rozpocząć tworzenie aplikacji logiki w portalu Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6_select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Kliknij przycisk **usługi Service Bus — nadejściu jedną lub więcej wiadomości w kolejce (automatycznie uzupełniać)**.
1. Utwórz połączenia magistrali usługi.
   1. Wprowadź nazwę połączenia.
   1. Kliknij przestrzeń nazw magistrali usług > zasady magistrali usługi > **Utwórz**.

      ![Tworzenie połączenia magistrali usługi aplikacji logiki w portalu Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7_create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Kliknij przycisk **Kontynuuj** po utworzeniu połączenia magistrali usługi.
   1. Wybierz kolejki, do której został utworzony i wprowadź `175` dla **maksymalna liczba komunikatów**

      ![Określ liczba maksymalna komunikatów połączenia usługi service bus w aplikacji logiki](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8_specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)
   1. Kliknij przycisk Zapisz, przycisk, aby zapisać zmiany.

1. Utwórz połączenie usługi SMTP.
   1. Kliknij przycisk **nowy krok** > **Dodaj akcję**.
   1. Typ `SMTP`, kliknij przycisk **SMTP** usługi w wynikach wyszukiwania, a następnie kliknij przycisk **SMTP — Wyślij wiadomość E-mail**.

      ![Tworzenie połączenia protokołu SMTP w Twojej aplikacji logiki w portalu Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9_create-smtp-connection-logic-app-azure-portal.png)

   1. Wprowadź informacje dotyczące SMTP skrzynki pocztowej, a następnie kliknij przycisk **Utwórz**.

      ![Wprowadź informacje o połączeniu SMTP w aplikacji logiki w portalu Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10_enter-smtp-connection-info-logic-app-azure-portal.png)

      Pobierz informacje SMTP dla [Hotmail/Outlook.com](https://support.office.com/en-us/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en), i [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).
   1. Wprowadź adres e-mail dla **z** i **do**, i `High temperature detected` dla **podmiotu** i **treści**.
   1. Kliknij pozycję **Zapisz**.

Aplikację logiki jest w stanie podczas zapisywania.

## <a name="test-the-logic-app"></a>Testowanie aplikacji logiki

1. Uruchom aplikację klienta, wdrożenia na urządzeniu w [ESP8266 Połącz z Centrum IoT Azure](iot-hub-arduino-huzzah-esp8266-get-started.md).
1. Zwiększ temperatury środowiska wokół Sensor tag się powyżej 30 C. Na przykład jasny świecy wokół Twojej Sensor tag.
1. Otrzymasz wiadomość e-mail z powiadomieniem wysyłanych przez aplikację logiki.

   > [!NOTE]
   > Usługodawca poczty e-mail może być konieczne zweryfikować tożsamość nadawcy, aby upewnić się, że użytkownik, który wysyła wiadomości e-mail jest.

## <a name="next-steps"></a>Kolejne kroki

Pomyślnie utworzono aplikację logiki, która łączy Centrum IoT i skrzynki pocztowej temperatury monitorowania i powiadomień.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
