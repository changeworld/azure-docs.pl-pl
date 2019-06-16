---
title: Usługa Azure zarządzanie urządzeniami IoT za pomocą Eksploratora chmury dla programu Visual Studio | Dokumentacja firmy Microsoft
description: Skorzystaj z Eksploratora chmury dla programu Visual Studio do zarządzania urządzeniami Azure IoT Hub, metod bezpośrednich i opcje zarządzania żądane właściwości bliźniaczej reprezentacji.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: xshi
ms.openlocfilehash: 87a0847f5d42e014f3b2691c96446892176b481b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60399560"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Za pomocą Eksploratora chmury dla programu Visual Studio do zarządzania urządzeniami Azure IoT Hub

![Diagram end-to-end](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

[Eksplorator chmury](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) jest przydatne rozszerzenia Visual Studio, która pozwala na wyświetlanie zasobów platformy Azure, badania ich właściwości i akcje klucza dla deweloperów z poziomu programu Visual Studio. Chodzi o opcje zarządzania, które służą do wykonywania różnych zadań.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opcja zarządzania          | Zadanie                    |
|----------------------------|--------------------------------|
| Metody bezpośrednie             | Należy skonfigurować urządzenie działania, takie jak uruchamianie lub zatrzymywanie wysyłanie wiadomości lub ponowne uruchamianie urządzenia.                                        |
| Bliźniacza reprezentacja urządzenia odczytu           | Pobierz stan zgłoszonego urządzenia. Na przykład urządzenie zgłasza diody LED jest teraz migające.                                    |
| Zaktualizować bliźniaczej reprezentacji urządzenia         | Urządzenia należy umieścić w określonych stanach, takie jak ustawienie DIODĘ zielony lub ustawienie interwału wysyłania danych telemetrycznych do 30 minut.         |
| Komunikaty z chmury do urządzenia   | Wysyłanie powiadomienia do urządzenia. Na przykład "jest bardzo prawdopodobne deszczowa już dziś. Nie zapomnij przenieść parasola."              |

Aby uzyskać bardziej szczegółowe objaśnienia na temat różnic oraz wskazówki na temat korzystania z tych opcji, zobacz [wskazówki dotyczące komunikacji urządzenia do chmury](iot-hub-devguide-d2c-guidance.md) i [wskazówki dotyczące komunikacji chmury do urządzenia](iot-hub-devguide-c2d-guidance.md).

Bliźniacze reprezentacje urządzeń to dokumenty JSON, które przechowują informacje o stanie urządzenia (metadane, konfiguracje i warunki). Usługa IoT Hub utrzymuje bliźniaczą reprezentację urządzenia dla każdego urządzenia, który nawiązuje z nim połączenie. Aby uzyskać więcej informacji dotyczących bliźniaczych reprezentacji urządzeń, zobacz [wprowadzenie do bliźniaków urządzeń](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Omawiane zagadnienia

Dowiesz się, jak używać Eksploratora chmury dla programu Visual Studio z różnymi opcjami zarządzania na komputerze deweloperskim.

## <a name="what-you-do"></a>Co należy zrobić

Uruchom Eksplorator chmury dla programu Visual Studio z różnymi opcjami zarządzania.

## <a name="what-you-need"></a>Co jest potrzebne

- Aktywna subskrypcja platformy Azure
- Usługa Azure IoT Hub w ramach Twojej subskrypcji
- Microsoft Visual Studio 2017 Update 8 lub nowszy
- Składnik Eksploratora chmury z Instalatora programu Visual Studio (wybrane domyślnie obciążenie platformy Azure)

## <a name="update-cloud-explorer-to-latest-version"></a>Eksplorator chmury aktualizację do najnowszej wersji

Składnik programu Cloud Explorer z poziomu Instalatora programu Visual Studio obsługuje tylko monitorowanie komunikatów z urządzenia do chmury i z chmury do urządzeń. Musisz pobrać i zainstalować najnowsze [programu Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) uzyskać dostęp do opcji zarządzania.

## <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się do dostępu do usługi IoT Hub

1. W programie Visual Studio **programu Cloud Explorer** okna, kliknij ikonę Zarządzanie kontami. Możesz otworzyć okno Eksploratora chmury z **widoku** > **programu Cloud Explorer** menu.

    ![Kliknij pozycję Zarządzanie kontami](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)

1. Kliknij przycisk **Zarządzanie kontami** w programie Cloud Explorer.
1. Kliknij przycisk **Dodaj konto...**  w nowym oknie zalogować się do platformy Azure po raz pierwszy.
1. Po zalogowaniu zostaną wyświetlone listy subskrypcji platformy Azure. Wybieranie subskrypcji platformy Azure, aby wyświetlić, a następnie kliknij przycisk **Zastosuj**.
1. Rozwiń **subskrypcji** > **centra IoT Hub** > **Your IoT Hub**, na liście będą wyświetlane w węźle usługi IoT Hub. Kliknij prawym przyciskiem myszy jednego urządzenia, aby uzyskać dostęp do opcji zarządzania.

    ![Opcje zarządzania](media/iot-hub-device-management-visual-studio/management-options.png)

## <a name="direct-methods"></a>Metody bezpośrednie

1. Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz pozycję **wywoływanie metody bezpośredniej urządzenia**.
1. Wprowadź nazwę metody oraz ładunek w polu wejściowym.
1. Wyniki będą wyświetlane w **usługi IoT Hub** okienko danych wyjściowych.

## <a name="read-device-twin"></a>Bliźniacza reprezentacja urządzenia odczytu

1. Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz pozycję **Edytuj bliźniaczą reprezentację urządzenia**.
1. **Azure-iot urządzenie twin.json** plik zostanie otwarty z zawartością bliźniaczej reprezentacji urządzenia.

## <a name="update-device-twin"></a>Zaktualizować bliźniaczej reprezentacji urządzenia

1. Edytujesz z **tagi** lub **properties.desired** pole **azure-iot urządzenie twin.json** pliku.
1. Naciśnij klawisz **Ctrl + S** można zaktualizować bliźniaczej reprezentacji urządzenia.
1. Wyniki będą wyświetlane w **usługi IoT Hub** okienko danych wyjściowych.

## <a name="send-cloud-to-device-messages"></a>Wysyłanie komunikatów z chmury do urządzeń

Aby wysłać wiadomość z usługi IoT Hub do urządzenia, wykonaj następujące kroki:

1. Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz pozycję **wysyłania komunikatu C2D**.
1. Wprowadź komunikat w polu wejściowym.
1. Wyniki będą wyświetlane w **usługi IoT Hub** okienko danych wyjściowych.

## <a name="next-steps"></a>Kolejne kroki

Wyjaśniono sposób za pomocą Eksploratora chmury dla programu Visual Studio z różnymi opcjami zarządzania.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]