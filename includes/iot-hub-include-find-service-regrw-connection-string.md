---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 04/03/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 1a0502568c1673bcd7f57d3e9bc9c95ed90bbefa
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756906"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

Aby utworzyć zasadę dostępu współdzielonego, która udziela uprawnień do **zapisu usługi** i **rejestru** oraz uzyskać parametry połączenia dla tej zasady, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com)wybierz pozycję **Grupy zasobów**. Wybierz grupę zasobów, w której znajduje się centrum, a następnie wybierz centrum z listy zasobów.

1. W lewym okienku centrum wybierz pozycję **Zasady dostępu współdzielonego**.

1. W górnym menu nad listą zasad wybierz pozycję **Dodaj**.

1. W obszarze **Dodawanie zasad dostępu współdzielonego**wprowadź opisową nazwę zasad, taką jak *serviceAndRegistryReadWrite*. W obszarze **Uprawnienia**wybierz pozycję **Rejestr zapisu** i **połączenia usługi**, a następnie wybierz pozycję **Utwórz**. **(Uprawnienie do odczytu rejestru** jest uwzględniane automatycznie po **wybraniu rejestru zapisu).**

    ![Pokazywale dodawania nowych zasad dostępu współdzielonego](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png)

1. Wybierz nowe zasady z listy zasad.

1. W obszarze **Udostępnione klawisze dostępu**wybierz ikonę kopiowania **ciągu połączenia — klucz podstawowy** i zapisz wartość.

    ![Sposób pobierania parametrów połączenia](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png)

Aby uzyskać więcej informacji na temat zasad i uprawnień dostępu współużytkowane centrum IoT Hub, zobacz [Kontrola dostępu i uprawnienia](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
