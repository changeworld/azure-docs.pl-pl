---
title: Monitorowanie łączności urządzeń za pomocą Eksploratora IoT Central platformy Azure
description: Monitoruj komunikaty urządzeń i obserwuj zmiany dotyczące sznurka urządzenia za pomocą interfejsu wiersza polecenia IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 12/18/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 6af6f01449f2f43e6799ef6d7821b9d71b24e603
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252349"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Monitorowanie łączności urządzeń przy użyciu interfejsu wiersza polecenia platformy Azure

*Ten temat dotyczy konstruktorów i administratorów.*

Użyj rozszerzenia usługi IoT dla interfejsu wiersza polecenia platformy Azure, aby wyświetlić komunikaty wysyłane przez urządzenia do IoT Central i obserwować zmiany w bliźniaczych urządzeniach. Za pomocą tego narzędzia można debugować i obserwować łączność urządzeń oraz diagnozować problemy dotyczące komunikatów urządzeń, które nie docierają do chmury lub urządzeń, które nie odpowiadają na zmiany bliźniaczy.

[Aby uzyskać więcej informacji, przejdź do dokumentacji rozszerzeń interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/central)

## <a name="prerequisites"></a>Wymagania wstępne

+ Zainstalowano interfejs wiersza polecenia platformy Azure w wersji 2.0.7 lub nowszej. Sprawdź wersję interfejsu wiersza polecenia platformy Azure, uruchamiając `az --version`. Dowiedz się, jak zainstalować i zaktualizować z dokumentacji [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
+ Konto służbowe na platformie Azure, które zostało dodane jako użytkownik w aplikacji IoT Central.

## <a name="install-the-iot-central-extension"></a>Zainstaluj rozszerzenie IoT Central

Uruchom następujące polecenie w wierszu polecenia, aby zainstalować program:

```cmd/sh
az extension add --name azure-iot
```

Sprawdź wersję rozszerzenia, uruchamiając:

```cmd/sh
az --version
```

Powinno zostać wyświetlone rozszerzenie Azure-IoT to 0.8.1 lub nowsze. Jeśli tak nie jest, uruchom polecenie:

```cmd/sh
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>Przy użyciu rozszerzenia

W poniższych sekcjach opisano typowe polecenia i opcje, których można użyć podczas uruchamiania `az iot central`. Aby wyświetlić pełen zestaw poleceń i opcji, Przekaż `--help` do `az iot central` lub dowolnego z jego poleceń.

### <a name="login"></a>Login

Zacznij od zalogowania się do interfejsu wiersza polecenia platformy Azure. 

```cmd/sh
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Pobieranie identyfikatora aplikacji aplikacji IoT Central
W obszarze **ustawienia administrowania/aplikacji**Skopiuj **Identyfikator aplikacji**. Zostanie ona użyta w dalszych krokach.

### <a name="monitor-messages"></a>Monitorowanie komunikatów
Monitoruj komunikaty wysyłane do aplikacji IoT Central z urządzeń. Będzie to obejmować wszystkie nagłówki i adnotacje.

```cmd/sh
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Wyświetl właściwości urządzenia
Wyświetl bieżące właściwości urządzenia odczyt i odczyt/zapis dla danego urządzenia.

```cmd/sh
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak korzystać z Eksploratora IoT Central, sugerowanym następnym krokiem jest zapoznanie się z tematem [Zarządzanie urządzeniami IoT Central](howto-manage-devices.md).
