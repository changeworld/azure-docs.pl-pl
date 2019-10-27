---
title: Monitorowanie łączności urządzeń za pomocą Eksploratora IoT Central platformy Azure
description: Monitoruj komunikaty urządzeń i obserwuj zmiany dotyczące sznurka urządzenia za pomocą interfejsu wiersza polecenia IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ec3ccc0a9fa6d1ae5d6193aacd1b068f2d97afe0
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949614"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Monitorowanie łączności urządzeń za pomocą Eksploratora IoT Central platformy Azure

*Ten temat dotyczy konstruktorów i administratorów.*

Użyj interfejsu wiersza polecenia Eksploratora IoT Central, aby wyświetlić komunikaty wysyłane przez urządzenia do IoT Central i obserwować zmiany w IoT Hubch sznurów. Za pomocą tego narzędzia typu "open source" można uzyskać dokładniejsze informacje o stanie łączności urządzeń i zdiagnozować problemy z komunikatami urządzeń, które nie docierają do chmury lub urządzeń, które nie odpowiadają na zmiany sznurka.

[Odwiedź repozytorium IOTC-Explorer w serwisie GitHub.](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Wymagania wstępne

+ Node. js w wersji 8. x lub nowszej — https://nodejs.org
+ Administrator aplikacji musi wygenerować token dostępu, który będzie używany w programie IOTC-Explorer

## <a name="install-iotc-explorer"></a>Instalowanie IOTC-Explorer

Uruchom następujące polecenie w wierszu polecenia, aby zainstalować program:

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> Zazwyczaj należy uruchomić polecenie instalacji z `sudo` w środowiskach, w których są podobne do systemu UNIX.

## <a name="run-iotc-explorer"></a>Uruchamianie IOTC-Explorer

W poniższych sekcjach opisano typowe polecenia i opcje, których można użyć podczas uruchamiania `iotc-explorer`. Aby wyświetlić pełen zestaw poleceń i opcji, Przekaż `--help` do `iotc-explorer` lub dowolnego z jego poleceń.

### <a name="login"></a>Zaloguj się

Przed rozpoczęciem należy mieć uprawnienia administratora aplikacji IoT Central, aby uzyskać token dostępu do użycia. Administrator wykonuje następujące czynności:

1. Przejdź do obszarze **Administracja** , a następnie wybierz pozycję **tokeny dostępu**.
1. Wybierz pozycję **Generuj token**.
    zrzut ekranu strony tokenu dostępu ![](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Wprowadź nazwę tokenu, wybierz pozycję **dalej**, a następnie polecenie **Kopiuj**.
    > [!NOTE]
    > Wartość tokenu jest wyświetlana tylko raz, więc należy ją skopiować przed zamknięciem okna dialogowego. Po zamknięciu okna dialogowego nigdy nie będzie ono wyświetlane ponownie.

    ![Zrzut ekranu okna dialogowego do kopiowania tokenu dostępu](media/howto-use-iotc-explorer/copyaccesstoken.png)

Możesz użyć tokenu, aby zalogować się do interfejsu wiersza polecenia w następujący sposób:

```cmd/sh
iotc-explorer login "<Token value>"
```

Jeśli wolisz nie mieć tokenu utrwalonego w historii powłoki, możesz opuścić token i zamiast tego podać go po wyświetleniu monitu:

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Monitorowanie komunikatów urządzenia

Komunikaty pochodzące z jednego urządzenia lub wszystkich urządzeń w aplikacji można obejrzeć przy użyciu polecenia `monitor-messages`. To polecenie uruchamia obserwatora, który ciągle wyprowadza nowe wiadomości w miarę ich nadejścia:

Aby obejrzeć wszystkie urządzenia w aplikacji, uruchom następujące polecenie:

```cmd/sh
iotc-explorer monitor-messages
```

Dane wyjściowe:

![Monitor — dane wyjściowe polecenia](media/howto-use-iotc-explorer/monitormessages.png)

Aby obejrzeć określone urządzenie, po prostu Dodaj identyfikator urządzenia na końcu polecenia:

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

Możesz również wyprowadzić większą liczbę formatów przyjaznych dla maszyn, dodając `--raw` opcji do polecenia:

```cmd/sh
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Pobierz sznurki urządzenia

Za pomocą polecenia `get-twin` można pobrać zawartość sznurka dla urządzenia IoT Central. Aby to zrobić, uruchom następujące polecenie:

```cmd/sh
iotc-explorer get-twin <your-device-id>
```

Dane wyjściowe:

![dane wyjściowe polecenia Get-bliźniaczy](media/howto-use-iotc-explorer/getdevicetwin.png)

Podobnie jak w przypadku `monitor-messages`można uzyskać więcej przyjaznych dla maszyn danych wyjściowych, przekazując opcję `--raw`:

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak korzystać z Eksploratora IoT Central, sugerowanym następnym krokiem jest zapoznanie się z tematem [Zarządzanie urządzeniami IoT Central](howto-manage-devices.md).
