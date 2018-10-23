---
title: Tworzenie aplikacji usługi Azure IoT Central | Microsoft Docs
description: Utwórz nową aplikację usługi Azure IoT Central do zarządzania urządzeniami do sprzedaży z funkcją chłodzenia. Wyświetl dane telemetryczne generowane z urządzenia symulowanego.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/15/2018
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 0199efbc1456206c67efb846b4381cae333e3749
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309046"
---
# <a name="create-an-azure-iot-central-application"></a>Tworzenie aplikacji usługi Azure IoT Central

Jako _konstruktor_ użyjesz interfejsu użytkownika usługi Azure IoT Central w celu zdefiniowania aplikacji usługi Microsoft Azure IoT Central. W tym przewodniku Szybki start przedstawiono sposób tworzenia aplikacji usługi Azure IoT Central zawierającej przykładowy _szablon urządzenia_ i symulowane _urządzenia_.

## <a name="create-the-application"></a>Tworzenie aplikacji

Aby ukończyć ten przewodnik Szybki start, musisz utworzyć aplikację usługi Azure IoT Central na podstawie szablonu aplikacji **Przykład Contoso**.

Przejdź na stronę [Menedżer aplikacji](https://aka.ms/iotcentral) usługi Azure IoT Central. Następnie podaj adres e-mail i hasło umożliwiające dostęp do subskrypcji platformy Azure:

![Określanie konta organizacji](media/quick-deploy-iot-central/sign-in.png)

Aby rozpocząć tworzenie nowej aplikacji usługi Azure IoT Central, wybierz pozycję **Nowa aplikacja**:

![Strona Menedżer aplikacji usługi Azure IoT Central](media/quick-deploy-iot-central/iotcentralhome.png)

Aby utworzyć nową aplikację usługi Azure IoT Central:

1. Wybierz plan płatności **Bezpłatna wersja próbna aplikacji**.
1. Wybierz przyjazną nazwę aplikacji, na przykład **Contoso IoT**. Usługa Azure IoT Central wygeneruje unikatowy prefiks adresu URL. Ten prefiks adresu URL możesz zmienić na łatwiejszy do zapamiętania.
1. Wybierz szablon aplikacji **Przykład Contoso**.
1. Następnie wybierz pozycję **Utwórz**.

![Strona Tworzenie aplikacji usługi Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono wstępnie wypełnioną aplikację usługi Azure IoT Central, która zawiera szablon urządzenia **Chłodniczy automat do sprzedaży** oraz symulowane urządzenie. Zobacz [Define a new device template in your application](tutorial-define-device-type.md) (Definiowanie nowego szablonu urządzenia w aplikacji), aby dowiedzieć się więcej (jako konstruktor) na temat definiowania własnych szablonów urządzeń.
