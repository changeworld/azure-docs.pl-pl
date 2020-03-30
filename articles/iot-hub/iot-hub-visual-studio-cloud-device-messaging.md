---
title: Zarządzanie komunikatami urządzeń usługi Azure IoT Hub za pomocą programu VS Cloud Explorer
description: Dowiedz się, jak używać Cloud Explorer for Visual Studio do monitorowania komunikatów w chmurze i wysyłania wiadomości z chmury do urządzenia w centrum Azure IoT Hub.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: c56bb7030b2ebc12e3afc24e2d8cb29ce2dda0bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74079489"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Wysyłanie i odbieranie wiadomości między urządzeniem a centrum IoT Hub za pomocą Eksploratora chmury dla programu Visual Studio

![Diagram end-to-end](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) to przydatne rozszerzenie programu Visual Studio, które umożliwia wyświetlanie zasobów platformy Azure, sprawdzanie ich właściwości i wykonywanie kluczowych akcji dewelopera z poziomu programu Visual Studio. W tym artykule skupiono się na tym, jak używać Cloud Explorer do wysyłania i odbierania wiadomości między urządzeniem a koncentratorem.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-learn"></a>Omawiane zagadnienia

W tym artykule dowiesz się, jak używać Cloud Explorer dla programu Visual Studio do monitorowania komunikatów z urządzenia do chmury i wysyłania komunikatów z chmury do urządzenia. Komunikaty między urządzeniami do chmury mogą być danymi z czujników zbieranych przez urządzenie, a następnie wysyła do centrum IoT Hub. Komunikaty z chmury do urządzenia mogą być poleceniami wysyłanych przez centrum IoT hub do urządzenia. Na przykład migać diodę LED, która jest podłączona do urządzenia.

## <a name="what-you-do"></a>Co robisz

W tym artykule wykonaj następujące zadania:

- Użyj Cloud Explorer dla programu Visual Studio do monitorowania komunikatów z urządzenia do chmury.

- Użyj Cloud Explorer dla programu Visual Studio do wysyłania komunikatów z chmury do urządzenia.

## <a name="what-you-need"></a>Co jest potrzebne

Potrzebne są następujące wymagania wstępne:

- Aktywna subskrypcja platformy Azure.

- Centrum Usługi Azure IoT w ramach subskrypcji.

- Microsoft Visual Studio 2017 Aktualizacja 9 lub nowsza. W tym artykule użyto [programu Visual Studio 2019](https://www.visualstudio.com/vs/).

- Składnik Cloud Explorer z Instalatora programu Visual Studio, który jest domyślnie wybrany za pomocą usługi Azure Workload.

## <a name="update-cloud-explorer-to-latest-version"></a>Aktualizowanie Eksploratora chmury do najnowszej wersji

Składnik Cloud Explorer z Instalatora programu Visual Studio dla programu Visual Studio 2017 obsługuje tylko monitorowanie komunikatów między urządzeniami i z chmury do urządzenia. Aby korzystać z programu Visual Studio 2017, pobierz i zainstaluj najnowszy [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-hub"></a>Zaloguj się, aby uzyskać dostęp do centrum

Aby uzyskać dostęp do centrum, wykonaj następujące czynności:

1. W programie Visual Studio wybierz pozycję **Wyświetl** > **Eksploratora chmury,** aby otworzyć Eksploratora chmury.

1. Wybierz ikonę Zarządzanie kontem, aby wyświetlić subskrypcje.

    ![Ikona Zarządzanie kontem](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Jeśli zalogujesz się na platformie Azure, pojawią się Twoje konta. Aby zalogować się na platformie Azure po raz pierwszy, wybierz pozycję **Dodaj konto**.

1. Wybierz subskrypcje platformy Azure, których chcesz użyć, i wybierz pozycję **Zastosuj**.

1. Rozwiń subskrypcję, a następnie rozwiń **centrum IoT Hubs**.  W każdym koncentratorze możesz zobaczyć swoje urządzenia dla tego koncentratora.

    ![Lista urządzeń](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Monitorowanie komunikatów między urządzeniami w chmurze

Aby monitorować wiadomości wysyłane z urządzenia do centrum IoT Hub, wykonaj następujące czynności:

1. Kliknij prawym przyciskiem myszy centrum IoT Hub lub urządzenie, a następnie wybierz polecenie **Rozpocznij monitorowanie komunikatu D2C**.

    ![Rozpocznij monitorowanie komunikatu D2C](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. Monitorowane komunikaty są wyświetlane w obszarze **Dane wyjściowe**.

    ![Monitorowanie wyniku komunikatu D2C](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. Aby zatrzymać monitorowanie, kliknij prawym przyciskiem myszy dowolnego centrum IoT Hub lub urządzenie i wybierz **pozycję Zatrzymaj komunikat D2C monitorowania**.

## <a name="send-cloud-to-device-messages"></a>Wysyłanie komunikatów z chmury do urządzeń

Aby wysłać wiadomość z Centrum IoT na urządzenie, wykonaj następujące czynności:

1. Kliknij prawym przyciskiem myszy urządzenie i wybierz polecenie **Wyślij wiadomość C2D**.

1. Wprowadź komunikat w polu wprowadzania.

    ![Wyślij wiadomość C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    Wyniki są wyświetlane w obszarze **Dane wyjściowe**.

    ![Wyślij wynik wiadomości C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>Następne kroki

Dowiesz się, jak monitorować wiadomości między urządzeniami do chmury i wysyłać komunikaty z chmury do urządzenia między urządzeniem IoT a usługą Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]