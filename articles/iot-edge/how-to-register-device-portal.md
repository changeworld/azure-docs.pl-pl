---
title: Zarejestruj nowe urządzenie brzegowe IoT Azure (portal) | Dokumentacja firmy Microsoft
description: Użyj portalu Azure, aby zarejestrować nowe urządzenie brzegowe IoT
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b61594469df33e11c23c9cbe0b9542da374fefa3
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036034"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Zarejestrować nowe urządzenie brzegowe IoT Azure w portalu Azure

Przed użyciem urządzenia IoT Azure IoT krawędzi, musisz zarejestrować ich przy użyciu Centrum IoT. Po zarejestrowaniu urządzenia, pojawi się ciąg połączenia, który może służyć do konfigurowania Twojego urządzenia pod kątem obciążeń krawędzi. 

W tym artykule pokazano, jak zarejestrować nowe urządzenie brzegowe IoT przy użyciu portalu Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) w Twojej subskrypcji platformy Azure. 

## <a name="create-a-device"></a>Utwórz urządzenie

W portalu Azure IoT krawędzi urządzenia są tworzone i zarządzane oddzielnie od urządzeń, nawiązać połączenia z Centrum IoT, które nie są włączone krawędzi. 

1. Zaloguj się do [portalu Azure](https://portal.azure.com) i przejdź do Centrum IoT. 
2. Wybierz **krawędzi IoT** z menu.
3. Wybierz **Dodaj urządzenia IoT**. 
4. Podaj identyfikator urządzenia opisowy. 
5. Wybierz pozycję **Zapisz**. 

## <a name="view-all-devices"></a>Wyświetl wszystkie urządzenia

Są wyświetlane wszystkie włączone krawędzi urządzenia, które nawiązać połączenia z Centrum IoT **krawędzi IoT** strony. 

## <a name="retrieve-the-connection-string"></a>Parametry połączenia

Gdy wszystko jest gotowe skonfigurować urządzenie, należy parametry połączenia, która łączy urządzenie fizyczne z swoją tożsamość w Centrum IoT.

1. Z **krawędzi IoT** w portalu kliknij pozycję z listy urządzenia brzegowe identyfikator urządzenia. 
2. Skopiuj wartość jednej **parametry połączenia — klucz podstawowy** lub **parametry połączenia — klucz pomocniczy**. 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [Wdrażanie modułów na urządzenie z portalu Azure](how-to-deploy-modules-portal.md)