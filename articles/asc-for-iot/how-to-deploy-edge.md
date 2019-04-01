---
title: Wdrażanie usługi Azure Security Center dla modułu usługi IoT Edge | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o tym, jak wdrożyć usługę Azure Security Center dla agenta zabezpieczeń IoT w usłudze IoT Edge.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 2a201fe649d52ad9604c7ac6675b26d60e7f2dd1
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58754765"
---
# <a name="deploy-security-module-on-your-iot-edge-device"></a>Wdrażanie zabezpieczeń modułu na urządzeniu usługi IoT Edge

> [!IMPORTANT]
> Centrum zabezpieczeń Azure dla IoT jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Usługa Azure Security Center (ASC) dla IoT **azureiotsecurity** moduł dostarcza rozwiązanie kompleksowych funkcji zabezpieczeń urządzenia usługi IoT Edge.
Moduł zabezpieczeń służy do zbierania, agreguje i analizuje dane dotyczące zabezpieczeń pierwotnych z systemem operacyjnym i kontenera do zaleceń dotyczących zabezpieczeń z możliwością działania i alerty.
Aby dowiedzieć się więcej, zobacz [modułu zabezpieczeń dla usługi IoT Edge](security-edge-architecture.md).

W tym przewodniku dowiesz się, jak wdrożyć modułu zabezpieczeń, na urządzeniu usługi IoT Edge.

## <a name="deploy-security-module"></a>Wdrażanie modułu zabezpieczeń

Wykonaj następujące kroki, aby wdrożyć ASC modułu zabezpieczeń IoT, IoT Edge.

### <a name="prerequisites"></a>Wymagania wstępne

1. Upewnij się, że urządzenie jest [zarejestrowane jako urządzenia usługi IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-register-device-portal).

1. Wymaga usługi ASC dla modułu usługi IoT Edge [AuditD framework](https://linux.die.net/man/8/auditd) zainstalowane na urządzeniu usługi Edge.

   Zainstaluj platformę, uruchamiając następujące polecenie na urządzeniu usługi Edge:
   
   `apt-get install auditd audispd-plugins`

### <a name="deployment-using-azure-portal"></a>Wdrażanie przy użyciu witryny Azure portal

1. Otwórz **Marketplace** w witrynie Azure portal.

1. Wyszukaj **zabezpieczeń usługi azure iot** i kliknij pozycję **zabezpieczeń usługi Azure IoT**.

   ![](media/howto/edge_onboarding_7.png)

1. Kliknij pozycję **Utwórz**.

1. Wybierz swoje **subskrypcji**, **usługi IoT Hub** i **nazwy urządzenia usługi IoT Edge**, następnie kliknij przycisk **Utwórz**.

1. Kliknij przycisk **dalej** dwa razy do **przeglądu wdrożenia**.

1. Upewnij się, że **edgeHub.settings.createOptions** jest skonfigurowane w następujący sposób:

   `"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"`

1. Kliknij przycisk **przesyłania** do ukończenia wdrażania.


## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o opcjach konfiguracji, przejdź do Przewodnik z instrukcjami dotyczącymi konfiguracji modułu. 
> [!div class="nextstepaction"]
> [Konfiguracja modułu jak przewodnik](./how-to-agent-configuration.md)
