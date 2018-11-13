---
title: Rejestrowanie nowego urządzenia usługi Azure IoT Edge (portal) | Dokumentacja firmy Microsoft
description: Użyj witryny Azure portal, aby zarejestrować nowe urządzenie usługi IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6657203c76bc03a262fbcbd30b5bf74b5be140eb
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51577502"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Rejestrowanie nowego urządzenia usługi Azure IoT Edge w witrynie Azure portal

Zanim użyjesz urządzeń IoT za pomocą usługi Azure IoT Edge, musisz zarejestrować ich przy użyciu usługi IoT hub. Po zarejestrowaniu urządzenia, pojawi się parametry połączenia, który może służyć do konfigurowania urządzenia pod kątem obciążeń krawędzi. 

W tym artykule pokazano, jak zarejestrować nowe urządzenie usługi IoT Edge przy użyciu witryny Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne

* [Usługi IoT hub](../iot-hub/iot-hub-create-through-portal.md) w subskrypcji platformy Azure. 

## <a name="create-a-device"></a>Tworzenie urządzenia

W witrynie Azure portal urządzenia usługi IoT Edge są tworzone i zarządzane oddzielnie od urządzenia, nawiązać połączenie z Centrum IoT, które nie są włączone usługi edge. 

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do Centrum IoT hub. 
2. Wybierz **usługi IoT Edge** z menu.
3. Wybierz **Dodaj urządzenie usługi IoT Edge**. 
4. Podaj identyfikator opisu urządzenia. 
5. Wybierz pozycję **Zapisz**. 

## <a name="view-all-devices"></a>Wyświetl wszystkie urządzenia

Wszystkie włączone krawędzi urządzenia, łączących się z Centrum IoT hub są wyświetlane na **usługi IoT Edge** strony. 

## <a name="retrieve-the-connection-string"></a>Pobieranie parametrów połączenia

Gdy wszystko będzie gotowe skonfigurować urządzenie, należy parametry połączenia, która łączy urządzenie fizyczne za pomocą jej tożsamości w usłudze IoT hub.

1. Z **usługi IoT Edge** strony w portalu, kliknij pozycję identyfikator urządzenia, z listy urządzeń brzegowych. 
2. Skopiuj wartość albo **parametry połączenia (klucz podstawowy)** lub **parametry połączenia (klucz pomocniczy)**. 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [wdrażać moduły do urządzenia przy użyciu witryny Azure portal](how-to-deploy-modules-portal.md)
