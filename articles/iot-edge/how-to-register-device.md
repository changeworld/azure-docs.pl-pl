---
title: Zarejestruj nowe urządzenie usługi Azure IoT Edge | Dokumenty firmy Microsoft
description: Użyj rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure, aby zarejestrować nowe urządzenie usługi IoT Edge i pobrać ciąg połączenia
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6fb776b4c1ff537401a23eb272526b3043fdb1e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235725"
---
# <a name="register-an-azure-iot-edge-device"></a>Rejestrowanie urządzenia usługi Azure IoT Edge

Aby można było używać urządzeń IoT z usługą Azure IoT Edge, należy zarejestrować je w centrum IoT hub. Po zarejestrowaniu urządzenia można pobrać parametry połączenia, aby skonfigurować urządzenie dla obciążeń usługi IoT Edge.

Możesz zarejestrować się przy użyciu jednego z następujących narzędzi:

* [Zarejestruj urządzenie w witrynie Azure portal,](#register-in-the-azure-portal) jeśli wolisz graficzny interfejs użytkownika do tworzenia, wyświetlania i zarządzania zasobami platformy Azure.
* [Zarejestruj urządzenie za pomocą programu Visual Studio Code,](#register-with-visual-studio-code) jeśli wolisz zarządzać zasobami Usługi Azure IoT w tym samym miejscu, w którym opracujesz rozwiązania IoT.
* [Zarejestruj urządzenie w narzędziu polecenia platformy Azure CLI,](#register-with-the-azure-cli) jeśli wolisz narzędzia wiersza polecenia do zarządzania zasobami platformy Azure lub zamierzasz zautomatyzować zadania.

## <a name="register-in-the-azure-portal"></a>Zarejestruj się w witrynie Azure portal

Wszystkie zadania rejestracji można wykonywać w witrynie Azure portal.

### <a name="prerequisites-for-the-azure-portal"></a>Wymagania wstępne dla witryny Azure portal

Bezpłatne lub standardowe [centrum IoT w](../iot-hub/iot-hub-create-through-portal.md) subskrypcji platformy Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Tworzenie urządzenia usługi IoT Edge w witrynie Azure portal

W centrum IoT Hub w witrynie Azure portal urządzenia usługi IoT Edge są tworzone i zarządzane oddzielnie od urządzeń IOT, które nie są włączone.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do centrum IoT Hub.
2. W lewym okienku wybierz z menu pozycję **IoT Edge.**
3. Wybierz **pozycję Dodaj urządzenie IoT Edge**.
4. Podaj opisowy identyfikator urządzenia. Użyj ustawień domyślnych, aby automatycznie generować klucze uwierzytelniania i podłączyć nowe urządzenie do koncentratora.
5. Wybierz **pozycję Zapisz**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Wyświetlanie urządzeń usługi IoT Edge w witrynie Azure portal

Wszystkie urządzenia z obsługą krawędzi, które łączą się z centrum IoT hub są wymienione na stronie **Usługi IoT Edge.**

![Wyświetlanie wszystkich urządzeń Usługi IoT Edge w centrum IoT](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Pobieranie ciągu połączenia w witrynie Azure portal

Gdy będziesz gotowy do skonfigurowania urządzenia, potrzebujesz ciągu połączenia, który łączy urządzenie fizyczne z jego tożsamością w centrum IoT hub.

1. Na stronie **IoT Edge** w portalu kliknij identyfikator urządzenia z listy urządzeń IoT Edge.
2. Skopiuj wartość **podstawowego ciągu połączenia** lub **pomocniczego ciągu połączenia**.

## <a name="register-with-visual-studio-code"></a>Zarejestruj się za pomocą kodu programu Visual Studio

Istnieje wiele sposobów, aby wykonać większość operacji w programie VS Code. W tym artykule użyto Eksploratora, ale można również użyć palety poleceń, aby uruchomić kroki.

### <a name="prerequisites-for-visual-studio-code"></a>Wymagania wstępne dotyczące kodu programu Visual Studio

* [Centrum IoT w](../iot-hub/iot-hub-create-through-portal.md) subskrypcji platformy Azure
* [Kod programu Visual Studio](https://code.visualstudio.com/)
* [Narzędzia IoT platformy Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) dla kodu programu Visual Studio

### <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się, aby uzyskać dostęp do centrum IoT

Rozszerzenia IoT usługi Azure dla programu Visual Studio Code umożliwiają wykonywanie operacji za pomocą centrum IoT Hub. Aby te operacje działały, musisz zalogować się do konta platformy Azure i wybrać centrum IoT Hub.

1. W programie Visual Studio Code otwórz widok **Eksploratora.**
1. U dołu Eksploratora rozwiń sekcję **Centrum Usługi Azure IoT.**

   ![Rozwiń sekcję Usługi Azure IoT Hub](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Kliknij **...** w nagłówku sekcji **Usługi Azure IoT Hub.** Jeśli nie widzisz wielokropka, kliknij lub najedź kursorem na nagłówek.
1. Wybierz **pozycję Wybierz Centrum IoT**.
1. Jeśli nie jesteś zalogowany do konta platformy Azure, postępuj zgodnie z instrukcjami, aby to zrobić.
1. Wybierz swoją subskrypcję platformy Azure.
1. Wybierz centrum IoT.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Tworzenie urządzenia IoT Edge z kodem programu Visual Studio

1. W Eksploratorze kodu usługi VS rozwiń sekcję **Urządzenia usługi Azure IoT Hub.**
1. Kliknij **...** w nagłówku sekcji **Urządzenia usługi Azure IoT Hub.** Jeśli nie widzisz wielokropka, kliknij lub najedź kursorem na nagłówek.
1. Wybierz **pozycję Utwórz urządzenie brzegowe IoT**.
1. W otworzynym polu tekstowym nadaj urządzeniu identyfikator.

Na ekranie wyjściowym zobaczysz wynik polecenia. Informacje o urządzeniu są drukowane, w tym **identyfikator urządzenia,** który podałeś, oraz **połączenieString,** którego można użyć do podłączenia urządzenia fizycznego do centrum IoT Hub.

Na ekranie wyjściowym zobaczysz wynik polecenia. Informacje o urządzeniu są drukowane, w tym **identyfikator urządzenia,** który podałeś, oraz **połączenieString,** którego można użyć do podłączenia urządzenia fizycznego do centrum IoT Hub.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Wyświetlanie urządzeń IoT Edge za pomocą kodu programu Visual Studio

Wszystkie urządzenia, które łączą się z centrum IoT są wymienione w sekcji **Centrum Azure IoT w** Eksploratorze kodu programu Visual Studio. Urządzenia IoT Edge można odróżnić od urządzeń innych niż Edge z inną ikoną oraz faktu, że **moduły $edgeAgent** i **$edgeHub** są wdrażane na każdym urządzeniu IoT Edge.

![Wyświetlanie wszystkich urządzeń Usługi IoT Edge w centrum IoT](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Pobieranie ciągu połączenia z programem Visual Studio Code

Gdy będziesz gotowy do skonfigurowania urządzenia, potrzebujesz ciągu połączenia, który łączy urządzenie fizyczne z jego tożsamością w centrum IoT hub.

1. Kliknij prawym przyciskiem myszy identyfikator urządzenia w sekcji **Centrum Usługi Azure IoT.**
1. Wybierz **pozycję Kopiuj ciąg połączenia urządzenia**.

   Ciąg połączenia zostanie skopiowany do schowka.

Można również wybrać **pobierz informacje o urządzeniu** z menu po kliknięciu prawym przyciskiem myszy, aby wyświetlić wszystkie informacje o urządzeniu, w tym parametry połączenia, w oknie wyjściowym.

## <a name="register-with-the-azure-cli"></a>Zarejestruj się w usłudze Azure CLI

[Narzędzie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) to narzędzie wiersza polecenia platformy open source do zarządzania zasobami platformy Azure, takimi jak usługa IoT Edge. Umożliwia zarządzanie zasobami usługi Azure IoT Hub, wystąpieniami usługi inicjowania obsługi administracyjnej urządzeń i połączonych koncentratorów po wyjęciu z pudełka. Rozszerzenie IoT wzbogaca interfejs wiersza polecenia platformy Azure o funkcje, takie jak zarządzanie urządzeniami i pełne możliwości usługi IoT Edge.

### <a name="prerequisites-for-the-azure-cli"></a>Wymagania wstępne dotyczące interfejsu wiersza polecenia platformy Azure

* [Centrum IoT w](../iot-hub/iot-hub-create-using-cli.md) subskrypcji platformy Azure.
* [Narzędzie CLI platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) w twoim środowisku. Co najmniej wersja interfejsu wiersza polecenia platformy Azure musi mieć wartość 2.0.70 lub nowszy. Użyj polecenia `az --version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack.
* [Rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Tworzenie urządzenia usługi IoT Edge za pomocą interfejsu wiersza polecenia platformy Azure

Użyj polecenia [tworzenia tożsamości urządzenia az iot hub,](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) aby utworzyć nową tożsamość urządzenia w centrum IoT hub. Przykład:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

To polecenie zawiera trzy parametry:

* **device-id**: Podaj opisową nazwę unikatową dla twojego centrum IoT.
* **nazwa koncentratora:** podaj nazwę centrum IoT Hub.
* **edge-enabled:** Ten parametr deklaruje, że urządzenie jest do użytku z IoT Edge.

   ![az iot hub device-identity tworzenie danych wyjściowych](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Wyświetlanie urządzeń usługi IoT Edge za pomocą interfejsu wiersza polecenia platformy Azure

Użyj polecenia [az iot hub device-identity,](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) aby wyświetlić wszystkie urządzenia w centrum IoT hub. Przykład:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Każde urządzenie zarejestrowane jako urządzenie Usługi IoT Edge będzie miało ustawioną **właściwość.iotEdge** **ustawioną**na true .

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Pobieranie ciągu połączenia za pomocą interfejsu wiersza polecenia platformy Azure

Gdy będziesz gotowy do skonfigurowania urządzenia, potrzebujesz ciągu połączenia, który łączy urządzenie fizyczne z jego tożsamością w centrum IoT hub. Użyj polecenia [az iot device-identity show-connection-string,](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) aby zwrócić parametry połączenia dla jednego urządzenia:

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

W wartości `device-id` parametru jest rozróżniana wielkość liter. Nie kopiuj cudzysłowów wokół ciągu połączenia.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz tożsamość urządzenia zarejestrowaną w centrum IoT hub, możesz przystąpić do zainstalowania środowiska wykonawczego usługi IoT Edge na swoich urządzeniach. Zainstaluj środowisko wykonawcze zgodnie z systemem operacyjnym urządzenia:

* [Instalowanie usługi Azure IoT Edge w systemie Windows](how-to-install-iot-edge-windows.md)
* [Instalowanie środowiska wykonawczego usługi Azure IoT Edge w systemie Linux](how-to-install-iot-edge-linux.md)
