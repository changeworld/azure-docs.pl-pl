---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/16/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 55cdd864d73ce084d994c64233e79d5a58b17def
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "69558442"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

Aby uzyskać parametry połączenia usługi IoT Hub dla zasad **usługi,** wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com)wybierz pozycję **Grupy zasobów**. Wybierz grupę zasobów, w której znajduje się centrum, a następnie wybierz centrum z listy zasobów.

1. W lewym okienku centrum IoT wybierz pozycję **Zasady dostępu współużytkowego**.

1. Z listy zasad wybierz zasady **usługi.**

1. W obszarze **Udostępnione klawisze dostępu**wybierz ikonę kopiowania **ciągu połączenia — klucz podstawowy** i zapisz wartość.

    ![Sposób pobierania parametrów połączenia](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

Aby uzyskać więcej informacji na temat zasad i uprawnień dostępu współużytkowane centrum IoT Hub, zobacz [Kontrola dostępu i uprawnienia](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
