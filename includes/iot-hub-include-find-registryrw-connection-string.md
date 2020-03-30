---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883754"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Aby uzyskać parametry połączenia usługi IoT Hub dla zasad **registryReadWrite,** wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com)wybierz pozycję **Grupy zasobów**. Wybierz grupę zasobów, w której znajduje się centrum, a następnie wybierz centrum z listy zasobów.

2. W lewym okienku centrum wybierz pozycję **Zasady dostępu współdzielonego**.

3. Z listy zasad wybierz **registryReadWrite** policy.

4. W obszarze **Udostępnione klawisze dostępu**wybierz ikonę kopiowania **ciągu połączenia — klucz podstawowy** i zapisz wartość.

    ![Sposób pobierania parametrów połączenia](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

Aby uzyskać więcej informacji na temat zasad i uprawnień dostępu współużytkowane centrum IoT Hub, zobacz [Kontrola dostępu i uprawnienia](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
