---
title: Omówienie agenta zabezpieczeń
description: Rozpocznij pracę ze zrozumieniem, konfigurowaniem, wdrażaniem i używaniem usługi Azure Security Center dla agentów usług zabezpieczeń IoT na urządzeniach IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 0d4d21db5185e4564666e526b3edb5ca6d3451e0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310646"
---
# <a name="get-started-with-azure-security-center-for-iot-device-security-agents"></a>Wprowadzenie do usługi Azure Security Center dla agentów zabezpieczeń urządzeń IoT

Usługi Azure Security Center dla agentów zabezpieczeń IoT oferują ulepszone funkcje zabezpieczeń, takie jak monitorowanie połączeń zdalnych, aktywne aplikacje, zdarzenia logowania i najlepsze rozwiązania dotyczące konfiguracji systemu operacyjnego. Przejmij kontrolę nad ochroną przed zagrożeniami i postawą bezpieczeństwa w terenie urządzenia za pomocą jednej usługi.

Architektura referencyjna dla agentów zabezpieczeń systemu Linux i Windows, zarówno w języku C# i C są dostarczane.

Usługa Azure Security Center dla agentów zabezpieczeń IoT obsługuje nieprzetworzoną kolekcję zdarzeń z systemu operacyjnego urządzenia, agregację zdarzeń w celu zmniejszenia kosztów i konfigurację za pośrednictwem bliźniaczej reprezentacji modułu urządzenia. Komunikaty zabezpieczeń są wysyłane za pośrednictwem usługi IoT Hub do usługi Azure Security Center dla usług analitycznych IoT.

Użyj następującego przepływu pracy, aby wdrożyć i przetestować usługę Azure Security Center dla agentów zabezpieczeń IoT:

1. [Włącz usługę Azure Security Center dla usługi IoT w centrum IoT Hub](quickstart-onboard-iot-hub.md)
1. Jeśli centrum IoT Hub nie ma zarejestrowanych urządzeń, [zarejestruj nowe urządzenie](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Utwórz moduł zabezpieczeń azureiotsecurity](quickstart-create-security-twin.md) dla swoich urządzeń.
1. Aby zainstalować agenta na symulowanym urządzeniu platformy Azure zamiast instalować na rzeczywistym urządzeniu, [uruchom nową maszynę wirtualną platformy Azure (VM)](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) w dostępnej strefie.
1. [Wdrażanie usługi Azure Security Center dla agenta zabezpieczeń IoT](how-to-deploy-linux-cs.md) na urządzeniu IoT lub nowej maszynie wirtualnej.
1. Postępuj zgodnie z [instrukcjami, aby trigger_events](https://aka.ms/iot-security-github-trigger-events) uruchomić nieszkodliwą symulację ataku.
1. Sprawdź usługi Azure Security Center dla alertów IoT w odpowiedzi na symulowany atak w poprzednim kroku. Rozpocznij weryfikację pięć minut po uruchomieniu skryptu.
1. Eksploruj [alerty,](concept-security-alerts.md) [rekomendacje](concept-recommendations.md)i [głębokie nurkowanie przy użyciu usługi Log Analytics](how-to-security-data-access.md) przy użyciu usługi IoT Hub.

## <a name="next-steps"></a>Następne kroki

- Konfigurowanie [rozwiązania](quickstart-configure-your-solution.md)
- [Tworzenie modułów zabezpieczeń](quickstart-create-security-twin.md)
- Konfigurowanie [alertów niestandardowych](quickstart-create-custom-alerts.md)
- [Wdrażanie agenta zabezpieczeń](how-to-deploy-agent.md)
