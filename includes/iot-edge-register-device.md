---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/25/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: bacafdc8f7fd8e206335f3be0a086df1c54f1081
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263617"
---
Utwórz tożsamość urządzenia symulowanego, aby umożliwić mu komunikowanie się z centrum IoT Hub. Ponieważ urządzenia usługi IoT Edge zachowują się inaczej niż typowe urządzenia IoT, a także mogą być inaczej zarządzane, już na samym początku zadeklaruj to urządzenie jako urządzenie usługi IoT Edge. 

1. W witrynie Azure Portal przejdź do centrum IoT Hub.
1. Wybierz pozycję **IoT Edge**, a następnie pozycję **Dodaj urządzenie usługi IoT Edge**.

   ![Dodawanie urządzenia usługi IoT Edge](./media/iot-edge-register-device/add-device.png)

1. Nadaj urządzeniu symulowanemu unikatowy identyfikator urządzenia.
1. Wybierz pozycję **Zapisz**, aby dodać to urządzenie.
1. Wybierz to nowe urządzenie z listy urządzeń.
1. Skopiuj wartość **Parametry połączenia — klucz podstawowy** i zapisz ją. Za pomocą tej wartości skonfigurujesz środowisko uruchomieniowe usługi IoT Edge w następnej sekcji. 

