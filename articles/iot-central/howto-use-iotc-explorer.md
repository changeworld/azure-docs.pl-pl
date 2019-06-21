---
title: Monitoruj łączność urządzeń przy użyciu usługi Azure IoT Central Explorer
description: Monitorowanie komunikatów przesyłanych z urządzenia i obserwować zmiany bliźniaczej reprezentacji urządzenia przez IoT Central Eksplorator interfejsu wiersza polecenia.
author: viv-liu
ms.author: viviali
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 4d17f0e5273c7397bd9c6a71d14b7992d8652768
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165865"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Monitoruj łączność urządzeń przy użyciu usługi Azure IoT Central Explorer

*Ten temat dotyczy konstruktorów i administratorów.*

Użyj IoT Central Eksplorator interfejsu wiersza polecenia, aby wyświetlić komunikaty urządzenia jest wysyłany do IoT Central i obserwować zmiany w bliźniaczej reprezentacji usługi IoT Hub. Zyskaj lepszy wgląd w stan łączności między urządzeniami i diagnozować problemy komunikaty z urządzenia nie osiągnie chmury lub nie odpowiada na zmiany w bliźniaczej reprezentacji urządzenia, można użyć tego narzędzia typu open source.

[Odwiedź stronę repozytorium iotc Eksploratora w usłudze GitHub.](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Wymagania wstępne

+ Środowisko node.js w wersji 8.x lub nowszej - https://nodejs.org
+ Administrator aplikacji musi wygenerować token dostępu do użycia w Eksploratorze iotc

## <a name="install-iotc-explorer"></a>Instalowanie Eksploratora iotc

Uruchom następujące polecenie z wiersza polecenia do zainstalowania:

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> Zazwyczaj należy uruchomić polecenie instalacji z `sudo` w środowiskach podobnymi do systemu Unix.

## <a name="run-iotc-explorer"></a>Uruchom Eksploratora iotc

W poniższych sekcjach opisano typowe polecenia i opcje używane po uruchomieniu `iotc-explorer`. Aby wyświetlić pełny zestaw poleceń i opcji, należy przekazać `--help` do `iotc-explorer` lub dowolny z jej podpoleceń polecenia.

### <a name="login"></a>Login

Zanim można rozpocząć pracę, musisz mieć administrator aplikacji IoT Central w celu uzyskania tokenu dostępu do użycia. Administrator wykonuje następujące czynności:

1. Przejdź do **administracji** następnie **tokeny dostępu**.
1. Wybierz **Wygeneruj Token**.
    ![Zrzut ekranu strony tokenu dostępu](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Wprowadź nazwę tokenu, wybierz **dalej**, a następnie **kopiowania**.
    > [!NOTE]
    > Wartość tokenu jest wyświetlane tylko raz, więc należy skopiować przed zamknięciem okna dialogowego. Po zamknięciu okna dialogowego, nigdy nie wyświetleniem ponownie.

    ![Kopiuj zrzut ekranu okna dialogowego tokenu dostępu](media/howto-use-iotc-explorer/copyaccesstoken.png)

Ten token służy do logowania do interfejsu wiersza polecenia w następujący sposób:

```cmd/sh
iotc-explorer login "<Token value>"
```

Jeśli wolisz nie mają tokenu utrwalone w historii powłoki, możesz pozostawić ten token wyjściowy, a zamiast tego podać go po wyświetleniu monitu:

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Monitorowanie komunikatów przesyłanych z urządzenia

Możesz obejrzeć komunikatów pochodzących z określonego urządzenia lub wszystkich urządzeń w swojej aplikacji za pomocą `monitor-messages` polecenia. To polecenie uruchamia obserwatora, który jest stale wysyła nowe wiadomości przychodzące:

Aby obejrzeć wszystkie urządzenia w aplikacji, uruchom następujące polecenie:

```cmd/sh
iotc-explorer monitor-messages
```

Dane wyjściowe:

![dane wyjściowe polecenia monitorowanie wiadomości](media/howto-use-iotc-explorer/monitormessages.png)

Aby obejrzeć określonego urządzenia, Dodaj identyfikator urządzenia w celu polecenia:

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

Można również danych wyjściowych format bardziej przyjaznego dla maszyny, dodając `--raw` — opcja polecenia:

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Pobierz bliźniaczej reprezentacji urządzenia

Możesz użyć `get-twin` polecenie, aby pobrać zawartość bliźniaczej reprezentacji urządzenia IoT Central. Aby to zrobić, uruchom następujące polecenie:

```cmd/sh
iotc-explorer get-twin <your-device-id>
```

Dane wyjściowe:

![dane wyjściowe polecenia GET-twin](media/howto-use-iotc-explorer/getdevicetwin.png)

Podobnie jak w przypadku `monitor-messages`, otrzymasz więcej maszyny przyjaznego dla danych wyjściowych, przekazując `--raw` opcji:

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wyjaśniono sposób za pomocą Eksploratora IoT Central, sugerowane następnym krokiem jest Eksplorowanie [zarządzanie urządzeniami IoT Central](howto-manage-devices.md).
