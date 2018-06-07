---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 28290718d15a893c5d676c887b9f810449075746
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34666014"
---
> [!div class="op_single_selector"]
> * [C w systemie Windows](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [C w systemie Linux](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [Node.js (ogólny)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Node.js na urządzeniu Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [C na urządzeniu Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)

W tym samouczku zaimplementowaniem **Chłodnica** urządzenia, które wysyła następujące dane telemetryczne do monitorowania zdalnego [akcelerator rozwiązań](../articles/iot-accelerators/iot-accelerators-what-are-solution-accelerators.md):

* Temperatura
* Ciśnienie
* Wilgotność

Dla uproszczenia kodu generuje przykładowe wartości telemetrii **Chłodnica**. Można rozszerzyć próbki przez połączenie czujników rzeczywistego urządzenia i wysyła rzeczywistych danych telemetrycznych.

Urządzenie próbki również:

* Wysyła metadane do rozwiązania do opisywania jego możliwości.
* Odpowiada na działania wywoływane z **urządzeń** strony w rozwiązaniu.
* Wyślij odpowiada na zmiany w konfiguracji z **urządzeń** strony w rozwiązaniu.

Do wykonania kroków tego samouczka jest potrzebne aktywne konto platformy Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed przystąpieniem do napisania kodu dla danego urządzenia, wdrażanie Twojego monitorowania zdalnego akcelerator rozwiązań i Dodaj nowe urządzenie fizyczne do rozwiązania.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>Wdrażanie sieci zdalne monitorowanie akcelerator rozwiązań

**Chłodnica** urządzenia, możesz utworzyć w tym samouczku wysyłania danych do wystąpienia [monitorowania zdalnego](../articles/iot-accelerators/iot-accelerators-remote-monitoring-explore.md) akcelerator rozwiązań. Jeśli nie zostało już wstępnie skonfigurowane akcelerator rozwiązań monitorowania zdalnego w konta platformy Azure, zobacz [akcelerator rozwiązań monitorowania zdalnego wdrażania](../articles/iot-accelerators/iot-accelerators-remote-monitoring-deploy.md)

Po zakończeniu procesu wdrażania rozwiązania monitorowania zdalnego, kliknij **uruchamianie** aby otworzyć pulpit nawigacyjny rozwiązania w przeglądarce.

![Pulpit nawigacyjny rozwiązania](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Dodaj urządzenia do monitorowania zdalnego rozwiązania

> [!NOTE]
> Jeśli masz już dodany urządzenia w rozwiązaniu, możesz pominąć ten krok. Parametry połączenia urządzenia wymaga jednak następnego kroku. Można pobrać parametrów połączenia do urządzenia z [portalu Azure](https://portal.azure.com) lub przy użyciu [az iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) narzędzia interfejsu wiersza polecenia.

Urządzenia do nawiązania połączenia akcelerator rozwiązań go zidentyfikowania się Centrum IoT, używając poprawnych poświadczeń. Użytkownik ma możliwość zapisania rozwiązania parametry połączenia urządzenia, które zawiera te poświadczenia podczas dodawania urządzenia. Ciąg połączenia urządzenia możesz uwzględnić w dalszej części tego samouczka aplikacji klienta.

Aby dodać urządzenie do monitorowania zdalnego rozwiązania, wykonaj następujące czynności na **urządzeń** strony w rozwiązaniu:

1. Wybierz **+ nowe urządzenie**, a następnie wybierz pozycję **fizycznych** jako **typu urządzenia**:

    ![Dodaj urządzenia fizycznego](media/iot-suite-selector-connecting/devicesprovision.png)

1. Wprowadź **fizyczna Chłodnica** jako identyfikator urządzenia. Wybierz **klucza symetrycznego** i **automatycznego generowania kluczy** opcje:

    ![Wybierz opcje urządzenia](media/iot-suite-selector-connecting/devicesoptions.png)

1. Wybierz **zastosować**. Następnie zanotuj **identyfikator urządzenia**, **klucza podstawowego**, i **klucza podstawowego połączenia ciąg** wartości:

    ![Pobieranie poświadczeń](media/iot-suite-selector-connecting/credentials.png)

Zostało teraz dodane urządzenie fizyczne do akcelerator rozwiązań monitorowania zdalnego i oznaczane w parametrach połączenia urządzenia. W poniższych sekcjach można zaimplementować aplikacji klienckiej, która używa ciągu połączenia urządzenia do łączenia się rozwiązania.

Aplikacja kliencka implementuje wbudowane **Chłodnica** model urządzenia. Model urządzenia akceleratora rozwiązań określa następujące informacje o urządzeniu:

* Właściwości urządzenia raporty dla rozwiązania. Na przykład **Chłodnica** urządzenie raportuje informacje o jego oprogramowania układowego i lokalizacji.
* Typy danych telemetrycznych urządzenie wysyła do rozwiązania. Na przykład **Chłodnica** temperatury i wilgotności oraz wartości wykorzystania wysłania przez urządzenie.
* Metody można zaplanować z rozwiązania do uruchomienia na urządzeniu. Na przykład **Chłodnica** urządzenia musi implementować **ponowny rozruch**, **FirmwareUpdate**, **EmergencyValveRelease**, i  **IncreasePressure** metody.