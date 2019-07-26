---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a752427d1e5873f0a27fd231872e3a13b234d9ed
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402344"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Aby uzyskać IoT Hub parametry połączenia dla zasad **registryReadWriteymi** , wykonaj następujące kroki:

1. Otwórz Centrum IoT Hub w [Azure Portal](https://portal.azure.com).  Najprostszym sposobem, aby uzyskać dostęp do usługi IoT Hub, jest wybranie opcji **grupy zasobów**, wybranie grupy zasobów, w której znajduje się centrum IoT Hub, a następnie wybranie Centrum IoT Hub z listy zasobów.

2. W okienku po lewej stronie Centrum IoT wybierz pozycję **zasady dostępu**współdzielonego.

3. Z listy zasad wybierz zasady **registryReadWrite** .

4. W obszarze **klucze dostępu**współdzielonego wybierz ikonę kopiowania **parametrów połączenia — klucz podstawowy** i Zapisz wartość.

    ![Sposób pobierania parametrów połączenia](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-string.png)

Aby uzyskać więcej informacji na temat IoT Hub zasad i uprawnień dostępu współdzielonego, zobacz [Kontrola dostępu i uprawnienia](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
