---
title: Rejestrowanie nowego urządzenia w witrynie Azure portal — usługa Azure IoT Edge | Dokumentacja firmy Microsoft
description: Użyj witryny Azure portal, aby zarejestrować nowe urządzenie usługi IoT Edge i pobieranie parametrów połączenia
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 6414f694296ce1f5a8b65ccab30cceaf2172dee7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126326"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Rejestrowanie nowego urządzenia usługi Azure IoT Edge w witrynie Azure portal

Zanim użyjesz urządzeń IoT za pomocą usługi Azure IoT Edge, musisz zarejestrować ich przy użyciu usługi IoT hub. Po zarejestrowaniu urządzenia, pojawi się parametry połączenia, który może służyć do konfigurowania urządzenia pod kątem obciążeń krawędzi.

W tym artykule pokazano, jak zarejestrować nowe urządzenie usługi IoT Edge przy użyciu witryny Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne

[Usługi IoT hub](../iot-hub/iot-hub-create-through-portal.md) w subskrypcji platformy Azure.

## <a name="create-a-device"></a>Tworzenie urządzenia

W witrynie Azure portal urządzenia usługi IoT Edge są tworzone i zarządzane oddzielnie od urządzenia, nawiązać połączenie z Centrum IoT, które nie są włączone usługi edge.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do Centrum IoT hub.
2. Wybierz **usługi IoT Edge** z menu.
3. Wybierz **Dodaj urządzenia usługi IoT Edge**.
4. Podaj identyfikator opisu urządzenia. Użyj ustawień domyślnych, automatycznego wygenerowania kluczy uwierzytelniania i połączenie nowe urządzenie do Centrum.
5. Wybierz pozycję **Zapisz**.

## <a name="view-all-devices"></a>Wyświetl wszystkie urządzenia

Wszystkie włączone krawędzi urządzenia, łączących się z Centrum IoT hub są wyświetlane na **usługi IoT Edge** strony.

## <a name="retrieve-the-connection-string"></a>Pobieranie parametrów połączenia

Gdy wszystko będzie gotowe skonfigurować urządzenie, należy parametry połączenia, która łączy urządzenie fizyczne za pomocą jej tożsamości w usłudze IoT hub.

1. Z **usługi IoT Edge** strony w portalu, kliknij pozycję identyfikator urządzenia, z listy urządzeń brzegowych.
2. Skopiuj wartość albo **parametry połączenia (klucz podstawowy)** lub **parametry połączenia (klucz pomocniczy)**.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [wdrażać moduły do urządzenia przy użyciu witryny Azure portal](how-to-deploy-modules-portal.md)
