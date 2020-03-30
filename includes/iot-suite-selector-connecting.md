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
ms.openlocfilehash: ca4bd3d3b40934323bab8036f3ce72e9281f1de4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183667"
---
> [!div class="op_single_selector"]
> * [C w systemie Windows](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [C w systemie Linux](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [C na urządzeniu Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)
> * [Node.js (ogólny)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Node.js na urządzeniu Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [MXChip IoT DevKit](../articles/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md)

W tym samouczku zaimplementujesz urządzenie **agregatu chłodniczego,** które wysyła następujące dane telemetryczne do [akceleratora rozwiązań](../articles/iot-accelerators/about-iot-accelerators.md)zdalnego monitorowania:

* Temperatura
* Ciśnienie
* Wilgotność

Dla uproszczenia kod generuje przykładowe wartości telemetryczne dla **agregatu chłodniczego**. Próbkę można rozszerzyć, podłączając rzeczywiste czujniki do urządzenia i wysyłając rzeczywistą telemetrię.

Przykładowe urządzenie również:

* Wysyła metadane do rozwiązania, aby opisać jego możliwości.
* Odpowiada na akcje wyzwalane ze strony **Urządzenia** w rozwiązaniu.
* Odpowiada na zmiany konfiguracji wysyłane ze strony **Urządzenia** w rozwiązaniu.

Do wykonania kroków tego samouczka jest potrzebne aktywne konto platformy Azure. Jeśli nie masz konta, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed zapisaniem kodu dla urządzenia należy wdrożyć akcelerator rozwiązań zdalnego monitorowania i dodać nowe rzeczywiste urządzenie do rozwiązania.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>Wdrażanie akceleratora rozwiązań do zdalnego monitorowania

Urządzenie **agregatów chłodniczych** utworzone w tym samouczku wysyła dane do wystąpienia akceleratora rozwiązania [zdalnego monitorowania.](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md) Jeśli akcelerator rozwiązań do zdalnego monitorowania na koncie platformy Azure nie został jeszcze zainicjowany, zobacz [Wdrażanie akceleratora rozwiązań do zdalnego monitorowania](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md)

Po zakończeniu procesu wdrażania rozwiązania zdalnego monitorowania kliknij przycisk **Uruchom,** aby otworzyć pulpit nawigacyjny rozwiązania w przeglądarce.

![Pulpit nawigacyjny rozwiązania](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Dodawanie urządzenia do rozwiązania do zdalnego monitorowania

> [!NOTE]
> Jeśli do rozwiązania dodano już urządzenie, możesz pominąć ten krok. Jednak następny krok wymaga ciągu połączenia urządzenia. Ciąg połączenia urządzenia można pobrać z [witryny Azure portal](https://portal.azure.com) lub za pomocą narzędzia [az iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) CLI.

Aby urządzenie łączy się z akceleratorem rozwiązań, musi identyfikować się z Centrum IoT przy użyciu prawidłowych poświadczeń. Istnieje możliwość zapisania ciągu połączenia urządzenia, który zawiera te poświadczenia podczas dodawania urządzenia do rozwiązania. Ciąg połączenia urządzenia w aplikacji klienckiej należy dołączyć w dalszej części tego samouczka.

Aby dodać urządzenie do rozwiązania do zdalnego monitorowania, wykonaj następujące kroki na stronie **Eksploratora urządzeń** w rozwiązaniu:

1. Wybierz **+ Nowe urządzenie**, a następnie wybierz pozycję **Rzeczywisty** jako **typ urządzenia:**

    ![Dodawanie rzeczywistego urządzenia](media/iot-suite-selector-connecting/devicesprovision.png)

1. Wprowadź **agregat fizyczny** jako identyfikator urządzenia. Wybierz opcje **klucza symetrycznego** i **Automatycznie generuje klucze:**

    ![Wybieranie opcji urządzenia](media/iot-suite-selector-connecting/devicesoptions.png)

1. Wybierz pozycję **Zastosuj**. Następnie zanotuj wartości **klucza podstawowego** **Identyfikator urządzenia,** **Klucz podstawowy**i Parametry połączenia:

    ![Pobieranie poświadczeń](media/iot-suite-selector-connecting/credentials.png)

Teraz dodano prawdziwe urządzenie do akceleratora rozwiązania zdalnego monitorowania i zauważyłeś jego parametry połączenia urządzenia. W poniższych sekcjach zaimplementuj aplikację kliencką, która używa ciągu połączenia urządzenia do łączenia się z rozwiązaniem.

Aplikacja kliencka implementuje wbudowany model urządzenia **chiller.** Model urządzenia akceleratora rozwiązań określa następujące informacje o urządzeniu:

* Właściwości, które urządzenie zgłasza do rozwiązania. Na przykład urządzenie **chiller** raportuje informacje o swoim oprogramowaniu układowym i lokalizacji.
* Typy danych telemetrycznych, które urządzenie wysyła do rozwiązania. Na przykład urządzenie **agregatów chłodniczych** wysyła wartości temperatury, wilgotności i ciśnienia.
* Metody, które można zaplanować z rozwiązania do uruchomienia na urządzeniu. Na przykład urządzenie **agregat chłodniczy** musi implementować **metody Reboot**, **FirmwareUpdate**, **EmergencyValveRelease**i **IncreasePressure.**
