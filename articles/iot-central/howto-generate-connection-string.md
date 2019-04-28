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
ms.openlocfilehash: f302cbfa7152ae30be434f560c0c39056d40f9f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60885648"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>Generowanie parametrów połączenia urządzenia, aby nawiązać połączenie z aplikacją usługi Azure IoT Central

W tym artykule opisano jak Deweloper urządzeń do generowania parametrów połączenia dla urządzenia, które musi połączyć się z aplikacją IoT Central. W procedurze opisanej w tym artykule dowiesz się, jak szybko łączyć z jednego urządzenia przy użyciu sygnatury dostępu współdzielonego (SAS). Takie podejście jest przydatne podczas testowania urządzeń lub eksperymentowanie z IoT Central. W przypadku alternatywnych metod do użycia w środowisku produkcyjnym, zobacz [łączność urządzeń w usłudze Azure IoT Central](concepts-connectivity.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym artykule potrzebne są:

- Aplikacja usługi Azure IoT Central. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).
- Jest maszyna deweloperska z [Node.js](https://nodejs.org/) wersji 8.0.0 lub nowszej. Możesz uruchomić `node --version` w wierszu polecenia, aby sprawdzić swoją wersję. Oprogramowanie Node.js jest dostępne dla różnych systemów operacyjnych.

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu

W poniższych krokach opisano sposób uzyskać informacje potrzebne do generowania parametrów połączenia sygnatury dostępu Współdzielonego dla urządzenia:

1. W **Device Explorer**, Znajdź prawdziwe urządzenie, aby połączyć się z aplikacją:

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
* [Przygotowanie i Połącz z urządzeniem Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [Przygotowanie i Połącz z urządzeniem Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [Przygotuj i podłącz urządzenie z systemu Windows 10 IoT core (C#)](howto-connect-windowsiotcore.md)
* [Łączenie ogólnego klienta Node.js z aplikacją usługi Azure IoT Central](howto-connect-nodejs.md)