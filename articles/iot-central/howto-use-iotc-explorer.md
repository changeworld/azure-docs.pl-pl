---
title: Monitoruj łączność urządzeń przy użyciu usługi Azure IoT Central Explorer
description: Monitorowanie komunikatów przesyłanych z urządzenia i obserwować zmiany bliźniaczej reprezentacji urządzenia przez IoT Central Eksplorator interfejsu wiersza polecenia.
author: viv-liu
ms.author: viviali
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 39359054818b81c0f5f2cdc9413ae2a0c1dc8c65
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2019
ms.locfileid: "57310271"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Monitoruj łączność urządzeń przy użyciu usługi Azure IoT Central Explorer

*Ten temat dotyczy konstruktorów i administratorów.*

Użyj IoT Central Eksplorator interfejsu wiersza polecenia, aby wyświetlić komunikaty urządzenia jest wysyłany do IoT Central i obserwować zmiany w bliźniaczej reprezentacji usługi IoT Hub. Zyskaj lepszy wgląd w stan łączności między urządzeniami i diagnozować problemy komunikaty z urządzenia nie osiągnie chmury lub nie odpowiada na zmiany w bliźniaczej reprezentacji urządzenia, można użyć tego narzędzia typu open source.

## <a name="visit-the-iotc-explorer-repo-in-githubhttpsakamsiotciotcexplorercligithub"></a>[Odwiedź stronę iotc explorer repozytorium w usłudze GitHub](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Wymagania wstępne
+ Środowisko node.js w wersji 8.x lub nowszej - https://nodejs.org
+ Konieczne będzie pomoc administratora aplikacji do wygenerowania tokenu dostępu do użycia w Eksploratorze iotc

## <a name="installing-iotc-explorer"></a>Instalowanie iotc-explorer

Uruchom następujące polecenie z wiersza polecenia do zainstalowania:

```
npm install -g iotc-explorer
```

> [!NOTE]
> Zazwyczaj należy uruchomić polecenie instalacji z `sudo` w środowiskach podobnymi do systemu Unix.

## <a name="running-iotc-explorer"></a>Uruchamianie iotc-explorer

Poniżej przedstawiono niektóre polecenia i typowe opcje, które można uruchomić, korzystając z `iotc-explorer`. Aby wyświetlić pełny zestaw poleceń i opcji, możesz przekazać `--help` do `iotc-explorer` lub dowolny z jej podpoleceń polecenia.

### <a name="login"></a>Login

Zanim można rozpocząć pracę, musisz mieć administrator aplikacji IoT Central w celu uzyskania tokenu dostępu do użycia. Administrator wykonuje następujące czynności:
1. Przejdź do **tokenów dostępuadministracyjnego/**. 
1. Wybierz **Generowanie**.
![Zrzut ekranu strony tokenu dostępu](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Wprowadź nazwę tokenu, wybierz **dalej**, i **skopiuj wartość tokenu**.
    > [!NOTE]
    > Wartość tokenu będą wyświetlane tylko raz, więc należy skopiować przed zamknięciem okna dialogowego. Po zamknięciu okna dialogowego, nigdy nie znajdzie się ona ponownie.

    ![Kopiuj zrzut ekranu okna dialogowego tokenu dostępu](media/howto-use-iotc-explorer/copyaccesstoken.png)

Następnie można użyć tego tokenu, zaloguj się do interfejsu wiersza polecenia, uruchamiając:

```sh
iotc-explorer login "<Token value>"
```

Jeśli czy raczej nie masz token utrwalone w historii powłoki, możesz pozostawić ten token wyjściowy, a zamiast tego podać go po wyświetleniu monitu:

```
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Monitorowanie komunikatów przesyłanych z urządzenia

Możesz obejrzeć komunikatów pochodzących z określonego urządzenia lub wszystkich urządzeń w swojej aplikacji za pomocą `monitor-messages` polecenia. Spowoduje to uruchomienie obserwatora, który jest stale zwróci nowe wiadomości, ponieważ są one dostępne w.

Aby obejrzeć wszystkie urządzenia w aplikacji, uruchom następujące polecenie:

```
iotc-explorer monitor-messages
```
Dane wyjściowe: ![dane wyjściowe polecenia monitorowanie wiadomości](media/howto-use-iotc-explorer/monitormessages.PNG)

Aby obejrzeć określonego urządzenia, Dodaj identyfikator urządzenia w celu polecenia:

```
iotc-explorer monitor-messages <your-device-id>
```

Mogą też istnieć danych wyjściowych na format bardziej przyjaznego dla maszyny, dodając polecenia `--raw` — opcja polecenia:

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Pobierz bliźniaczej reprezentacji urządzenia

Możesz użyć `get-twin` polecenie, aby pobrać zawartość bliźniaczej reprezentacji urządzenia IoT Central. Aby to zrobić, uruchom następujące polecenie:

```
iotc-explorer get-twin <your-device-id>
```

Dane wyjściowe: ![dane wyjściowe polecenia get-twin](media/howto-use-iotc-explorer/getdevicetwin.PNG)

Podobnie jak w przypadku `monitor-messages`, otrzymasz więcej maszyny przyjaznego dla danych wyjściowych, przekazując `--raw` opcji:

```
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy wiesz jak używać Eksploratora centralnej IoT sugerowane następnym krokiem jest Eksplorowanie [zarządzanie urządzeniami IoT Central](howto-manage-devices.md).
