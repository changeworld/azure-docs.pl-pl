---
title: Generowanie parametrów połączenia urządzenia dla usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Jako deweloper urządzenia, jak czy mogę wygenerować ciąg połączenia dla urządzenia, które wymaga, aby nawiązać połączenie aplikacji IoT Central?
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3a5e8d15d9a705892fe54c50e9b79e6d42af78d9
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426736"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>Generowanie parametrów połączenia urządzenia, aby nawiązać połączenie z aplikacją usługi Azure IoT Central

W tym artykule opisano jak Deweloper urządzeń do generowania parametrów połączenia dla urządzenia, które musi połączyć się z aplikacją IoT Central. W procedurze opisanej w tym artykule pokazano, jak szybko łączenie pojedynczego urządzenia przy użyciu sygnatury dostępu współdzielonego (SAS). Takie podejście jest przydatne podczas testowania urządzeń lub eksperymentowanie z IoT Central. W przypadku alternatywnych metod do użycia w środowisku produkcyjnym, zobacz [łączność urządzeń w usłudze Azure IoT Central](concepts-connectivity.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym artykule potrzebne są:

1. Aplikacja usługi Azure IoT Central. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).
1. Jest maszyna deweloperska z [Node.js](https://nodejs.org/) wersji 8.0.0 lub nowszej. Możesz uruchomić `node --version` w wierszu polecenia, aby sprawdzić swoją wersję. Oprogramowanie Node.js jest dostępne dla różnych systemów operacyjnych.

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu

W poniższych krokach opisano sposób uzyskać informacje potrzebne do generowania parametrów połączenia sygnatury dostępu Współdzielonego dla urządzenia:

1. W **Explorer**, Znajdź prawdziwe urządzenie, aby połączyć się z aplikacją:

    ![Wybierz rzeczywistego urządzenia](media/howto-generate-connection-string/real-devices.png)

1. Na **urządzenia** wybierz opcję **Connect**:

    ![Wybierz Connect](media/howto-generate-connection-string/connect.png)

1. Zanotuj szczegóły połączenia **identyfikator zakresu**, **identyfikator urządzenia**, i **klucza podstawowego urządzenia**, w celu użycia w kolejnych krokach:

    ![Szczegóły połączenia](media/howto-generate-connection-string/device-connect.png)

    Na tej stronie, aby zapisać, można skopiować wartości.

## <a name="generate-the-connection-string"></a>Generowanie parametrów połączenia

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy został wygenerowany ciąg połączenia dla rzeczywistego urządzenia połączyć się z aplikacją usługi Azure IoT Central, Oto zalecane kolejne kroki:

* [Przygotuj i podłącz urządzenie Mxchip (C)](howto-connect-devkit.md)
* [Przygotowywanie i łączenie urządzenia Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [Przygotowywanie i łączenie urządzenia Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [Przygotuj i podłącz urządzenie z systemu Windows 10 IoT core (C#)](howto-connect-windowsiotcore.md)
* [Łączenie z ogólnego klienta Node.js do aplikacji usługi Azure IoT Central](howto-connect-nodejs.md)