---
title: Opis modułu zabezpieczeń Usługi Azure Security Center dla usługi IoT dla usługi IoT Edge| Dokumenty firmy Microsoft
description: Poznaj architekturę i możliwości modułu zabezpieczeń Usługi Azure Security Center dla IoT dla usługi IoT Edge.
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
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 148c68234a937efde554ef00a6014cdc1a350f34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315890"
---
# <a name="azure-iot-edge-security-module"></a>Moduł zabezpieczeń usługi Azure IoT Edge

[Usługa Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) zapewnia zaawansowane możliwości zarządzania przepływami pracy biznesowej i ich wykonywania na obrzeżach.
Kluczowa część, którą gra usługa IoT Edge w środowiskach IoT, czyni ją szczególnie atrakcyjną dla złośliwych aktorów.

Moduł zabezpieczeń Usługi Azure Security Center for IoT zapewnia kompleksowe rozwiązanie zabezpieczające dla urządzeń usługi IoT Edge.
Moduł Usługi Azure Security Center for IoT zbiera, agreguje i analizuje surowe dane zabezpieczeń z systemu operacyjnego i systemu kontenerów na użyteczne zalecenia dotyczące zabezpieczeń i alerty.

Podobnie jak w przypadku usługi Azure Security Center dla agentów zabezpieczeń IoT dla urządzeń IoT, moduł Usługi Azure Security Center dla usługi IoT Edge można bardzo dostosować za pośrednictwem bliźniaczej reprezentacji modułu.
Zobacz [Konfigurowanie agenta,](how-to-agent-configuration.md) aby dowiedzieć się więcej.

Moduł zabezpieczeń Usługi Azure Security Center dla IoT dla usługi IoT Edge oferuje następujące funkcje:

- Zbiera nieprzetworzone zdarzenia zabezpieczeń z podstawowego systemu operacyjnego (Linux) i systemów kontenera usługi IoT Edge.
  
  Zobacz [Usługa Azure Security Center dla konfiguracji agenta IoT,](how-to-agent-configuration.md) aby dowiedzieć się więcej o dostępnych modułach zbierających dane zabezpieczeń.

- Analiza manifestów wdrażania usługi IoT Edge.

- Agreguje nieprzetworzone zdarzenia zabezpieczeń do wiadomości wysyłanych za pośrednictwem [usługi IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub).

- Usuń konfigurację za pomocą bliźniaczej reprezentacji modułu zabezpieczeń.

  Aby dowiedzieć się [więcej, zobacz Konfigurowanie usługi Azure Security Center dla agenta IoT.](how-to-agent-configuration.md)

Moduł zabezpieczeń Usługi Azure Security Center dla IoT dla usługi IoT Edge działa w trybie uprzywilejowanym w usłudze IoT Edge.
Tryb uprzywilejowany jest wymagany, aby umożliwić modułowi monitorowanie systemu operacyjnego i innych modułów usługi IoT Edge.

## <a name="module-supported-platforms"></a>Platformy obsługiwane przez moduł

Moduł zabezpieczeń Usługi Azure Security Center dla IoT dla usługi IoT Edge jest obecnie dostępny tylko dla systemu Linux. 

## <a name="next-steps"></a>Następne kroki

W tym artykule poznaliście architekturę i możliwości modułu zabezpieczeń Usługi Azure Security Center dla usługi IoT dla usługi IoT Edge.

Aby kontynuować pracę z usługą Azure Security Center dla wdrożenia IoT, należy użyć następujących artykułów:

- Wdrażanie [modułu zabezpieczeń dla ioT Edge](how-to-deploy-edge.md)
- Dowiedz się, jak [skonfigurować moduł zabezpieczeń](how-to-agent-configuration.md)
- Przejrzyj [wymagania wstępne](service-prerequisites.md) usługi IoT usługi Azure Security Center
- Dowiedz się, jak [włączyć usługę Azure Security Center dla usługi IoT w centrum IoT Hub](quickstart-onboard-iot-hub.md)
- Dowiedz się więcej o usłudze z [często zadawanych pytań dotyczących usługi Azure Security Center for IoT](resources-frequently-asked-questions.md)