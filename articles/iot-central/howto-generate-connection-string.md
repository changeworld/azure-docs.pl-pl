---
title: Generowanie parametrów połączenia urządzenia dla platformy Azure IoT Central | Microsoft Docs
description: Jako deweloper urządzenia jak wygenerować parametry połączenia dla urządzenia, które musi nawiązać połączenie z aplikacją IoT Central?
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 97c0332656b75c3c8d0cddecb41c7a15ac2f218c
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019775"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>Wygeneruj parametry połączenia urządzenia, aby połączyć się z aplikacją usługi Azure IoT Central

W tym artykule opisano sposób, w jaki deweloper urządzenia generuje parametry połączenia dla urządzenia, które musi nawiązać połączenie z aplikacją IoT Central. Procedura opisana w tym artykule pokazuje, jak szybko podłączyć pojedyncze urządzenie przy użyciu sygnatury dostępu współdzielonego (SAS). Takie podejście jest przydatne w przypadku eksperymentowania z IoT Central lub testowaniem urządzeń. Aby zapoznać się z alternatywnymi metodami do użycia w środowisku produkcyjnym, zobacz [łączność urządzenia w usłudze Azure IoT Central](concepts-connectivity.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym artykule potrzebne są:

- Aplikacja IoT Central platformy Azure. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).
- Komputer deweloperski z zainstalowanym środowiskiem [Node. js](https://nodejs.org/) w wersji 8.0.0 lub nowszej. Aby sprawdzić swoją `node --version` wersję, można uruchomić polecenie w wierszu polecenia. Oprogramowanie Node.js jest dostępne dla różnych systemów operacyjnych.

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu

W poniższych krokach opisano, jak uzyskać informacje potrzebne do wygenerowania parametrów połączenia sygnatury dostępu współdzielonego dla urządzenia:

1. W **Device Explorer**Znajdź rzeczywiste urządzenie, które chcesz połączyć z aplikacją:

    ![Wybierz rzeczywiste urządzenie](media/howto-generate-connection-string/real-devices.png)

1. Na stronie **urządzenie** wybierz pozycję **Połącz**:

    ![Wybierz pozycję Połącz](media/howto-generate-connection-string/connect.png)

1. Zanotuj szczegóły połączenia, **Identyfikator zakresu**, **Identyfikator urządzenia**i **klucz podstawowy urządzenia**, aby użyć ich w następujących krokach:

    ![Szczegóły połączenia](media/howto-generate-connection-string/device-connect.png)

    Możesz skopiować wartości z tej strony do zapisania.

## <a name="generate-the-connection-string"></a>Generuj parametry połączenia

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wygenerowałeś parametry połączenia dla rzeczywistego urządzenia, aby połączyć się z aplikacją IoT Central platformy Azure, poniżej przedstawiono sugerowane następne kroki:

* [Przygotowywanie i łączenie urządzenia DevKit (C)](howto-connect-devkit.md)
* [Przygotowywanie i łączenie Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [Przygotuj i Połącz Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [Przygotowanie i podłączenie urządzenia z systemem Windows 10 IoT CoreC#()](howto-connect-windowsiotcore.md)
* [Łączenie ogólnego klienta Node.js z aplikacją usługi Azure IoT Central](howto-connect-nodejs.md)