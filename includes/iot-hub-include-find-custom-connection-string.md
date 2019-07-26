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
ms.openlocfilehash: 55dc712d323c43849fa3de23c83e29fc348a0f1e
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403985"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Aby utworzyć zasady dostępu współdzielonego, które przyznają uprawnienia do **odczytu i rejestracji** **usługi** , i Pobierz parametry połączenia dla tych zasad, wykonaj następujące kroki:

1. Otwórz Centrum IoT Hub w [Azure Portal](https://portal.azure.com). Najprostszym sposobem, aby uzyskać dostęp do usługi IoT Hub, jest wybranie opcji **grupy zasobów**, wybranie grupy zasobów, w której znajduje się centrum IoT Hub, a następnie wybranie Centrum IoT Hub z listy zasobów.

2. W okienku po lewej stronie Centrum IoT wybierz pozycję **zasady dostępu**współdzielonego.

3. W górnym menu powyżej listy zasad wybierz pozycję **Dodaj**.

4. W okienku **Dodawanie zasad dostępu** współdzielonego Wprowadź opisową nazwę zasad. na przykład: *serviceAndRegistryRead*. W obszarze **uprawnienia**wybierz pozycję **Rejestr odczyt** i **Usługa łączenie**, a następnie wybierz pozycję **Utwórz**.

    ![Pokaż, jak dodać nowe zasady dostępu współdzielonego](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

5. W okienku **zasady dostępu** współdzielonego wybierz nowe zasady z listy zasad.

6. W obszarze **klucze dostępu**współdzielonego wybierz ikonę kopiowania **parametrów połączenia — klucz podstawowy** i Zapisz wartość.

    ![Sposób pobierania parametrów połączenia](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Aby uzyskać więcej informacji na temat IoT Hub zasad i uprawnień dostępu współdzielonego, zobacz [Kontrola dostępu i uprawnienia](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
