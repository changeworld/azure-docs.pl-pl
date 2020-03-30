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
ms.openlocfilehash: 6f43bbcd83861f7d39de2aa89bbe035c2ff5b809
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050454"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Aby utworzyć zasadę dostępu współdzielonego, która udziela uprawnień **do połączenia usługi** i **odczytu rejestru** i uzyskać parametry połączenia dla tej zasady, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com)wybierz pozycję **Grupy zasobów**. Wybierz grupę zasobów, w której znajduje się centrum, a następnie wybierz centrum z listy zasobów.

1. W lewym okienku centrum wybierz pozycję **Zasady dostępu współdzielonego**.

1. W górnym menu nad listą zasad wybierz pozycję **Dodaj**.

1. W obszarze **Dodawanie zasad dostępu współdzielonego**wprowadź opisową nazwę zasad, taką jak *serviceAndRegistryRead*. W obszarze **Uprawnienia**wybierz pozycję **Odczyt rejestru** i **połączenie usługi**, a następnie wybierz pozycję **Utwórz**.

    ![Pokazywale dodawania nowych zasad dostępu współdzielonego](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. Wybierz nowe zasady z listy zasad.

1. W obszarze **Udostępnione klawisze dostępu**wybierz ikonę kopiowania **ciągu połączenia — klucz podstawowy** i zapisz wartość.

    ![Sposób pobierania parametrów połączenia](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Aby uzyskać więcej informacji na temat zasad i uprawnień dostępu współużytkowane centrum IoT Hub, zobacz [Kontrola dostępu i uprawnienia](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
