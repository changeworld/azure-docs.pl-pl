---
title: Zarządzanie urządzeniami Usługi Azure IoT w / Visual Studio Cloud Explorer
description: Użyj Cloud Explorer dla programu Visual Studio dla usługi Azure IoT Hub zarządzania, featuring Direct metody i twin żądane opcje zarządzania właściwościami.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 6fe5a45dda6632c56b3c6714827950e25e7d26af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953178"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Korzystanie z Eksploratora chmury dla programu Visual Studio dla zarządzania urządzeniami usługi Azure IoT Hub

![Diagram end-to-end](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) to przydatne rozszerzenie programu Visual Studio, które umożliwia wyświetlanie zasobów platformy Azure, sprawdzanie ich właściwości i wykonywanie kluczowych akcji dewelopera z poziomu programu Visual Studio. Jest wyposażony w opcje zarządzania, których można używać do wykonywania różnych zadań.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opcja zarządzania          | Zadanie                    |
|----------------------------|--------------------------------|
| Metody bezpośrednie             | Ustaw działanie urządzenia, takie jak uruchamianie lub zatrzymywanie wysyłania wiadomości lub ponowne uruchamianie urządzenia.                                        |
| Odczyt urządzenia bliźniaczego           | Pobierz zgłoszony stan urządzenia. Na przykład urządzenie zgłasza, że dioda LED miga teraz.                                    |
| Aktualizacja bliźniaczej reprezentacji urządzenia         | Umieść urządzenie w określonych stanach, takich jak ustawienie diody LED na zielono lub ustawienie interwału wysyłania danych telemetrycznych na 30 minut.         |
| Komunikaty z chmury do urządzenia   | Wysyłanie powiadomień do urządzenia. Na przykład, "Jest bardzo prawdopodobne, że dziś będzie padać. Nie zapomnij zabrać ze sobą parasola."              |

Aby uzyskać bardziej szczegółowe wyjaśnienie różnic i wskazówek dotyczących korzystania z tych opcji, zobacz [Wskazówki dotyczące komunikacji między urządzeniami](iot-hub-devguide-d2c-guidance.md) a chmurą oraz [wskazówki dotyczące komunikacji z chmury do urządzenia](iot-hub-devguide-c2d-guidance.md).

Bliźniacze reprezentacje urządzeń to dokumenty JSON, które przechowują informacje o stanie urządzenia, takie jak metadane, konfiguracje i warunki. Centrum IoT hub utrzymuje bliźniaczej reprezentacji urządzenia dla każdego urządzenia, które łączy się z nim. Aby uzyskać więcej informacji na temat bliźniąt urządzeń, zobacz [Wprowadzenie do bliźniąt urządzeń](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Omawiane zagadnienia

W tym artykule dowiesz się, jak używać Cloud Explorer dla programu Visual Studio z różnymi opcjami zarządzania na komputerze deweloperskim.

## <a name="what-you-do"></a>Co robisz

W tym artykule uruchom Cloud Explorer dla programu Visual Studio z różnymi opcjami zarządzania.

## <a name="what-you-need"></a>Co jest potrzebne

Potrzebne są następujące wymagania wstępne:

- Aktywna subskrypcja platformy Azure.

- Centrum Usługi Azure IoT w ramach subskrypcji.

- Microsoft Visual Studio 2017 Aktualizacja 9 lub nowsza. W tym artykule użyto [programu Visual Studio 2017 lub programu Visual Studio 2019.](https://www.visualstudio.com/vs/)

- Składnik Cloud Explorer z Instalatora programu Visual Studio, który domyślnie wybrał za pomocą usługi Azure Workload.

## <a name="update-cloud-explorer-to-latest-version"></a>Aktualizowanie Eksploratora chmury do najnowszej wersji

Składnik Cloud Explorer z Instalatora programu Visual Studio dla programu Visual Studio 2017 obsługuje tylko monitorowanie komunikatów między urządzeniami i z chmury do urządzenia. Aby korzystać z programu Visual Studio 2017, pobierz i zainstaluj najnowszy [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-hub"></a>Zaloguj się, aby uzyskać dostęp do centrum

1. W programie Visual Studio wybierz pozycję **Wyświetl** > **Eksploratora chmury,** aby otworzyć Eksploratora chmury.

1. Wybierz ikonę Zarządzanie kontem, aby wyświetlić subskrypcje.

    ![Ikona Zarządzanie kontem](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Jeśli zalogujesz się na platformie Azure, pojawią się Twoje konta. Aby zalogować się na platformie Azure po raz pierwszy, wybierz pozycję **Dodaj konto**.

1. Wybierz subskrypcje platformy Azure, których chcesz użyć, i wybierz pozycję **Zastosuj**.

1. Rozwiń subskrypcję, a następnie rozwiń **centrum IoT Hubs**.  W każdym koncentratorze możesz zobaczyć swoje urządzenia dla tego koncentratora. Kliknij prawym przyciskiem myszy jedno urządzenie, aby uzyskać dostęp do opcji zarządzania.

    ![Opcje zarządzania](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>Metody bezpośrednie

Aby użyć metod bezpośrednich, wykonaj następujące czynności:

1. Kliknij prawym przyciskiem myszy urządzenie i wybierz polecenie **Wywołaj metodę bezpośrednią urządzenia**.

1. Wprowadź nazwę metody i ładunek w **obszarze Wywołaj metodę bezpośrednią,** a następnie wybierz przycisk **OK**.

    Wyniki są wyświetlane w **pliku wyjściowym**.

## <a name="update-device-twin"></a>Aktualizacja bliźniaczej reprezentacji urządzenia

Aby edytować bliźniaczątkę urządzenia, wykonaj następujące czynności:

1. Kliknij prawym przyciskiem myszy urządzenie i wybierz polecenie **Edytuj bliźniacze urządzenie**.

   Otworzy się plik **azure-iot-device-twin.json** z zawartością bliźniaczej reprezentacji urządzenia.

1. W pliku **azure-iot-device-twin.json** można wprowadzić zmiany **znaczników** lub pól **właściwości.desired.**

1. Naciśnij **klawisze Ctrl+S,** aby zaktualizować bliźniaczą urządzenie.

   Wyniki są wyświetlane w **pliku wyjściowym**.

## <a name="send-cloud-to-device-messages"></a>Wysyłanie komunikatów z chmury do urządzeń

Aby wysłać wiadomość z Centrum IoT na urządzenie, wykonaj następujące czynności:

1. Kliknij prawym przyciskiem myszy urządzenie i wybierz polecenie **Wyślij wiadomość C2D**.

1. Wprowadź wiadomość w **wiadomości Wyślij C2D** i wybierz **przycisk OK**.

   Wyniki są wyświetlane w **pliku wyjściowym**.

## <a name="next-steps"></a>Następne kroki

Dowiesz się, jak używać Cloud Explorer dla programu Visual Studio z różnymi opcjami zarządzania.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
