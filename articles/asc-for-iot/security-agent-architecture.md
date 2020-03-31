---
title: Opis architektury agenta zabezpieczeń Usługi Azure Security Center dla usług IoT| Dokumenty firmy Microsoft
description: Poznaj architekturę agenta zabezpieczeń dla agentów używanych w usłudze Azure Security Center dla IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 998aeab197931a75579fc39b28e3a248b85fc57b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596911"
---
# <a name="security-agent-reference-architecture"></a>Architektura referencyjna agenta zabezpieczeń

Usługa Azure Security Center for IoT udostępnia architekturę referencyjną dla agentów zabezpieczeń, które rejestrują, przetwarzają, agregują i wysyłają dane zabezpieczeń za pośrednictwem usługi IoT Hub.

Agenci zabezpieczeń są przeznaczone do pracy w ograniczonym środowisku IoT i są wysoce konfigurowalne pod względem wartości, które zapewniają w porównaniu do zasobów, które zużywają.

Agenci zabezpieczeń obsługują następujące funkcje:

- Zbieranie nieprzetworzonych zdarzeń zabezpieczeń z podstawowego systemu operacyjnego (Linux, Windows). Aby dowiedzieć się więcej o dostępnych modułach zbierających dane zabezpieczeń, zobacz [Usługa Azure Security Center dla konfiguracji agenta IoT](how-to-agent-configuration.md).

- Agreguj nieprzetworzone zdarzenia zabezpieczeń do wiadomości wysyłanych za pośrednictwem usługi IoT Hub.

- Uwierzytelnij się przy użyciu istniejącej tożsamości urządzenia lub dedykowanej tożsamości modułu. Aby dowiedzieć się więcej, zobacz [Metody uwierzytelniania agenta zabezpieczeń.](concept-security-agent-authentication-methods.md)

- Konfigurować zdalnie za pomocą **azureiotsecurity** moduł bliźniaczej reprezentacji. Aby dowiedzieć się więcej, zobacz [Konfigurowanie usługi Azure Security Center dla agenta IoT](how-to-agent-configuration.md).

Usługa Azure Security Center dla agentów zabezpieczeń IoT są opracowywane jako projekty typu open source i są dostępne w usłudze GitHub: 

- [Usługa Azure Security Center dla agenta opartego na języku IoT C](https://github.com/Azure/Azure-IoT-Security-Agent-C) 
- [Usługa Azure Security Center dla agenta opartego na języku IoT C#](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Platformy obsługiwane przez agenta

Usługa Azure Security Center for IoT oferuje różne agentów instalatora dla systemu Windows 32-bitowego i 64-bitowego oraz to samo dla systemu Linux 32-bitowego i 64-bitowego. Upewnij się, że masz odpowiedni instalator agenta dla każdego z twoich urządzeń zgodnie z poniższą tabelą:

| Architektura | Linux | Windows |    Szczegóły|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32bit  | C  | C#  ||
| 64bit  | C# lub C           | C#      | Zalecamy używanie agenta C dla urządzeń z bardziej ograniczonymi lub minimalnymi zasobami urządzenia.|
|

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się o architekturze agenta zabezpieczeń Usługi Azure Security Center dla IoT oraz dostępnych instalatorach.

Aby kontynuować pracę z usługą Azure Security Center dla wdrożenia IoT, należy użyć następujących artykułów:

- Opis [metod uwierzytelniania agenta zabezpieczeń](concept-security-agent-authentication-methods.md)
- Wybieranie i wdrażanie [agenta zabezpieczeń](how-to-deploy-agent.md)
- Przejrzyj wymagania wstępne [usługi](service-prerequisites.md) Azure Security Center dla IoT
- Dowiedz się, jak [włączyć usługę Azure Security Center dla usługi IoT w centrum IoT Hub](quickstart-onboard-iot-hub.md)
- Dowiedz się więcej o usłudze z [często zadawanych pytań dotyczących usługi Azure Security Center for IoT](resources-frequently-asked-questions.md)
