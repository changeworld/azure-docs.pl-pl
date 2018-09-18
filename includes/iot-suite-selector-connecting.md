---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: ed2d52c906bda7060f09769fa689f824026669c5
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45978915"
---
> [!div class="op_single_selector"]
> * [C w systemie Windows](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [C w systemie Linux](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [C na urządzeniu Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)
> * [Node.js (ogólny)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Node.js na urządzeniu Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [Zestawu deweloperskiego IoT Mxchip](../articles/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md)

W ramach tego samouczka, możesz wdrożyć **Chłodnica** urządzenia, które wysyła następujące dane telemetryczne do monitorowania zdalnego [akcelerator rozwiązań](../articles/iot-accelerators/about-iot-accelerators.md):

* Temperatura
* Ciśnienie
* Wilgotność

Dla uproszczenia kod generuje przykładowe wartości danych telemetrycznych dla **Chłodnica**. Można rozszerzenia przykładu przez podłączenie rzeczywistych czujników do urządzenia i wysyłanie rzeczywistych danych telemetrycznych.

Urządzenie przykładowe również:

* Wysyła metadane do rozwiązania do opisania jego możliwości.
* Odpowiada na działania wywoływane z poziomu **urządzeń** strony w rozwiązaniu.
* Odpowiada na zmiany konfiguracji Wyślij z **urządzeń** strony w rozwiązaniu.

Do wykonania kroków tego samouczka jest potrzebne aktywne konto platformy Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed przystąpieniem do napisania jakiegokolwiek kodu dla Twojego urządzenia, wdrożyć Twój akcelerator rozwiązań monitorowania zdalnego i dodać nowe urządzenie fizyczne do rozwiązania.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>Wdrażanie Twój akcelerator rozwiązań zdalnego monitorowania

**Chłodnica** urządzenie, które utworzysz w tym samouczku wysyła dane do wystąpienia [zdalne monitorowanie](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md) akcelerator rozwiązań. Jeśli jeszcze nie zostały już aprowizowane akceleratora rozwiązania monitorowania zdalnego na Twoim koncie platformy Azure, zobacz [wdrażanie akceleratora rozwiązania monitorowania zdalnego](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md)

Po zakończeniu procesu wdrażania rozwiązania do monitorowania zdalnego, kliknij **Uruchom** aby otworzyć pulpit nawigacyjny rozwiązania w przeglądarce.

![Pulpit nawigacyjny rozwiązania](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Dodaj urządzenia z rozwiązaniem monitorowania zdalnego

> [!NOTE]
> Jeśli masz już dodany urządzenia w rozwiązaniu, możesz pominąć ten krok. Następnym krokiem wymaga jednak parametry połączenia urządzenia. Możesz pobrać parametry połączenia urządzenia z [witryny Azure portal](https://portal.azure.com) lub za pomocą [az iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) narzędzie interfejsu wiersza polecenia.

W przypadku urządzeń nawiązać połączenie z akceleratora rozwiązań musi ono zidentyfikować się do usługi IoT Hub za pomocą prawidłowych poświadczeń. Masz możliwość Zapisz parametry połączenia urządzenia, która zawiera te poświadczenia po dodaniu urządzenia rozwiązania. Parametry połączenia urządzenia zawierają się w aplikacji klienckiej w dalszej części tego samouczka.

Aby dodać urządzenie do rozwiązania monitorowania zdalnego, wykonaj następujące czynności na **urządzeń** strony w rozwiązaniu:

1. Wybierz **+ nowe urządzenie**, a następnie wybierz **fizycznych** jako **typu urządzenia**:

    ![Dodaj urządzenie fizyczne](media/iot-suite-selector-connecting/devicesprovision.png)

1. Wprowadź **Chłodnica fizycznych** jako identyfikator urządzenia. Wybierz **klucz symetryczny** i **automatycznie Generuj klucze** opcje:

    ![Wybierz opcje urządzenia](media/iot-suite-selector-connecting/devicesoptions.png)

1. Wybierz **zastosować**. Następnie zanotuj **identyfikator urządzenia**, **klucza podstawowego**, i **klucz podstawowy parametrów połączenia** wartości:

    ![Pobieranie poświadczeń](media/iot-suite-selector-connecting/credentials.png)

Już teraz dodane urządzenia fizycznego do akceleratora rozwiązania monitorowania zdalnego i oznaczane w jego parametry połączenia urządzenia. W poniższych sekcjach można zaimplementować aplikację kliencką, która używa parametrów połączenia urządzenia do łączenia z rozwiązania.

Aplikacja kliencka implementuje wbudowane **Chłodnica** model urządzenia. Model urządzenia akceleratora rozwiązań określa następujące informacje o urządzeniu:

* Właściwości urządzenia raporty do rozwiązania. Na przykład **Chłodnica** urządzenie raportuje informacje o jego oprogramowania układowego i lokalizacji.
* Rodzaje danych telemetrycznych urządzenie wysyła do rozwiązania. Na przykład **Chłodnica** urządzenie wysyła temperaturę, wilgotność i wartości ciśnienia.
* Metody można zaplanować z rozwiązania do uruchamiania na urządzeniu. Na przykład **Chłodnica** urządzenie musi implementować **ponowny rozruch**, **FirmwareUpdate**, **EmergencyValveRelease**, i  **IncreasePressure** metody.